---
title: Valeurs de repli pour les liaisons Xamarin.Forms
description: Cet article explique comment renforcer des liaisons en définissant des valeurs de repli qui seront utilisées si une liaison échoue.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 67fd8070ae36bdc1a90b8a33b25f13369d8d995d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650209"
---
# <a name="xamarinforms-binding-fallbacks"></a>Valeurs de repli pour les liaisons Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Une liaison de données échoue parfois car la source de la liaison ne peut pas être résolue ou parce que la liaison réussit mais retourne une valeur `null`. De tels scénarios peuvent être gérés avec des convertisseurs de valeurs ou du code supplémentaire, mais il est possible de renforcer les liaisons de données en définissant des valeurs de repli à utiliser si le processus de liaison échoue. Pour cela, vous pouvez définir les propriétés [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) et [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) dans une expression de liaison. Comme ces propriétés résident dans la classe [`BindingBase`](xref:Xamarin.Forms.BindingBase), elles peuvent être utilisées avec des liaisons, des liaisons compilées et l’extension de balisage `Binding`.

> [!NOTE]
> L’utilisation des propriétés [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) et [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) dans une expression de liaison est facultative.

## <a name="defining-a-fallback-value"></a>Définition d’une valeur de repli

La propriété [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) permet de définir une valeur de repli à utiliser si la *source* de la liaison ne peut pas être résolue. Un scénario courant pour définir cette propriété est lors d’une liaison à des propriétés de source qui n’existent peut-être pas sur tous les objets d’une collection liée de types hétérogènes.

La page **MonkeyDetail** illustre la définition de la propriété [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) :

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

La liaison sur l’objet [`Label`](xref:Xamarin.Forms.Label) définit une valeur [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) qui sera définie sur la cible si la source de la liaison ne peut pas être résolue. Par conséquent, la valeur définie par la propriété `FallbackValue` s’affiche si la propriété `Population` n’existe pas sur l’objet lié. Notez qu’ici la valeur de propriété `FallbackValue` est délimitée par des guillemets simples (apostrophes).

Au lieu de définir des valeurs de propriété [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) incluses, il est recommandé de les définir en tant que ressources dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). L’avantage de cette approche est que ces valeurs sont définies une seule fois dans un emplacement unique et sont plus facilement localisables. Les ressources peuvent ensuite être récupérées à l’aide de l’extension de balisage `StaticResource` :

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Il n’est pas possible de définir la propriété `FallbackValue` avec une expression de liaison.

Voici le programme en cours d’exécution :

![Liaison FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "Liaison FallbackValue")

Lorsque la propriété `FallbackValue` n’est pas définie dans une expression de liaison et que le chemin de liaison ou une partie de ce chemin n’est pas résolu(e), [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) est défini sur la cible. Toutefois, lorsque la propriété `FallbackValue` est définie et que le chemin de liaison ou une partie de ce chemin n’est pas résolu(e), la valeur de la propriété `FallbackValue` est définie sur la cible. Par conséquent, dans la page **MonkeyDetail**, le [`Label`](xref:Xamarin.Forms.Label) affiche « Population size unknown » (Taille de la population inconnue), car l’objet lié n’a pas de propriété `Population`.

> [!IMPORTANT]
> Un convertisseur de valeurs défini n’est pas exécuté dans une expression de liaison lorsque la propriété [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) est définie.

## <a name="defining-a-null-replacement-value"></a>Définition d’une valeur de remplacement de null

La propriété [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) permet de définir une valeur de remplacement à utiliser si la *source* de la liaison est résolue mais que la valeur est `null`. Un scénario courant pour définir cette propriété est lors d’une liaison à des propriétés de source qui peuvent être `null` dans une collection liée.

La page **Monkeys** illustre la définition de la propriété [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) :

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Les liaisons sur [`Image`](xref:Xamarin.Forms.Image) et [`Label`](xref:Xamarin.Forms.Label) définissent toutes les deux des valeurs [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) qui seront appliquées si le chemin de liaison retourne `null`. Par conséquent, les valeurs définies par les propriétés `TargetNullValue` seront affichées pour tous les objets dans la collection où les propriétés `ImageUrl` et `Location` ne sont pas définies. Notez qu’ici les valeurs de propriété `TargetNullValue` sont délimitées par des guillemets simples (apostrophes).

Au lieu de définir des valeurs de propriété [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) incluses, il est recommandé de les définir en tant que ressources dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). L’avantage de cette approche est que ces valeurs sont définies une seule fois dans un emplacement unique et sont plus facilement localisables. Les ressources peuvent ensuite être récupérées à l’aide de l’extension de balisage `StaticResource` :

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Il n’est pas possible de définir la propriété `TargetNullValue` avec une expression de liaison.

Voici le programme en cours d’exécution :

[![Liaison de TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "Liaison de TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "Liaison de TargetNullValue")

Lorsque la propriété `TargetNullValue` n’est pas définie dans une expression de liaison, une valeur source `null` est convertie si un convertisseur de valeurs est défini, formatée si un `StringFormat` est défini, et le résultat est ensuite défini sur la cible. Toutefois, lorsque la propriété `TargetNullValue` est définie, une valeur source `null` est convertie si un convertisseur de valeurs est défini, et si elle est encore `null` après la conversion, la valeur de la propriété `TargetNullValue` est définie sur la cible.

> [!IMPORTANT]
> Le formatage de chaîne n’est pas appliqué dans une expression de liaison lorsque la propriété `TargetNullValue` est définie.

## <a name="related-links"></a>Liens associés

- [Démos des liaisons de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
