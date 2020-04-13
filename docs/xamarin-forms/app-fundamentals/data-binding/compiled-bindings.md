---
title: Liaisons compilées Xamarin.Forms
description: Cet article explique comment utiliser des liaisons compilées pour améliorer les performances des liaisons de données dans les applications Xamarin.Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
ms.openlocfilehash: 531d9719eb4bf5c23001ebe4260254e13f9989eb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697154"
---
# <a name="xamarinforms-compiled-bindings"></a>Liaisons compilées Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_Les liaisons compilées sont résolues plus rapidement que les liaisons classiques. Elles améliorent ainsi les performances des liaisons de données dans les applications Xamarin.Forms._

Les liaisons de données présentent deux problèmes principaux :

1. Aucune validation des expressions de liaison n’a lieu au moment de la compilation. À la place, les liaisons sont résolues au moment de l’exécution. Par conséquent, les liaisons non valides ne sont pas détectées avant l’exécution, lorsque l’application ne se comporte pas comme prévu ou que des messages d’erreur apparaissent.
1. Elles ne sont pas rentables. Les liaisons sont résolues au moment de l’exécution via l’inspection (réflexion) des objets à usage général et la surcharge de cette opération varie d’une plateforme à l’autre.

Les liaisons compilées améliorent les performances de liaison de données dans les applications Xamarin.Forms en résolvant les expressions de liaison au moment de la compilation plutôt qu’au moment de l’exécution. En outre, cette validation des expressions de liaison au moment de la compilation favorise une meilleure expérience de dépannage pour les développeurs, car les liaisons non valides sont signalées comme erreurs de build.

Le processus permettant d’utiliser des liaisons compilées est le suivant :

1. Activez la compilation XAML. Pour plus d’informations sur la compilation XAML, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).
1. Définissez `x:DataType` un [`VisualElement`](xref:Xamarin.Forms.VisualElement) attribut sur un au `VisualElement` type de l’objet à qui les enfants et ses enfants se lient.

> [!NOTE]
> Il est recommandé de `x:DataType` définir l’attribut au même [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) niveau dans la hiérarchie de vue que l’est défini. Toutefois, cet attribut peut être redéfini à n’importe quel endroit dans une hiérarchie de vue.

Pour utiliser les fixations compilées, l’attribut `x:DataType` doit être réglé `x:Type` sur une chaîne littérale, ou un type utilisant l’extension de balisage. Au moment de la compilation XAML, toutes les expressions de liaison non valides seront signalées comme erreurs de build. Toutefois, le compilateur XAML signalera uniquement une erreur de build pour la première expression de liaison non valide qu’il rencontrera. Toute expression contraignante valide `VisualElement` qui est définie sur les enfants [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) ou ses enfants sera compilée, que ce soit dans XAML ou en code. La compilation d’une expression de liaison génère du code compilé qui obtiendra une valeur d’une propriété de la *source* et la définira sur la propriété de la *cible* spécifiée dans le balisage. En outre, en fonction de l’expression de liaison, le code généré peut observer des modifications dans la valeur de la propriété *source* et actualiser la propriété *cible*, et il peut renvoyer (push) des modifications à partir de la *cible* vers la *source*.

> [!IMPORTANT]
> Les fixations compilées sont actuellement désactivées pour toutes les expressions contraignantes qui définissent la [`Source`](xref:Xamarin.Forms.Binding.Source) propriété. Cela tient au fait que la propriété `Source` est toujours définie à l’aide de l’extension de balisage `x:Reference`, qui ne peut pas être résolue au moment de la compilation.

## <a name="use-compiled-bindings"></a>Utiliser des liaisons compilées

La page **Sélectionneur de couleur compilée** démontre l’utilisation de liaisons compilées entre les vues Xamarin.Forms et les propriétés viewmodel :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) racine instantanément `HslColorViewModel` la propriété et `Color` initialise la propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dans les étiquettes d’élément de propriété pour la propriété. Cette `StackLayout` racine définit `x:DataType` également l’attribut comme le type de viewmodel, indiquant que toutes les expressions contraignantes dans la hiérarchie de vue de racine `StackLayout` seront compilées. Ceci peut être vérifié en changeant n’importe laquelle des expressions contraignantes pour se lier à une propriété de viewmodel inexistante, qui entraînera une erreur de construction. Bien que cet `x:DataType` exemple donne l’attribut à une chaîne littérale, `x:Type` il peut également être réglé à un type avec l’extension de balisage. Pour plus d’informations sur l’extension de `x:Type` balisage, voir [x:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

> [!IMPORTANT]
> L’attribut `x:DataType` peut être redéfini à tout emplacement dans une hiérarchie d’affichage.

Les [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) éléments et [`Slider`](xref:Xamarin.Forms.Slider) les points de [`StackLayout`](xref:Xamarin.Forms.StackLayout)vue du . Ces vues sont toutes des cibles contraignantes qui référencent les propriétés sources dans le viewmodel. Pour [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) la propriété, [`Label.Text`](xref:Xamarin.Forms.Label.Text) et la propriété, `OneWay` les liaisons de données sont - les propriétés dans la vue sont définies à partir des propriétés dans le viewmodel. Cependant, [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) la propriété `TwoWay` utilise une liaison. Cela permet `Slider` à chacun d’être défini à partir du viewmodel, `Slider`et aussi pour le viewmodel d’être défini à partir de chacun .

Lorsque l’application est [`BoxView`](xref:Xamarin.Forms.BoxView)exécutée pour la première fois, les [`Label`](xref:Xamarin.Forms.Label) éléments et [`Slider`](xref:Xamarin.Forms.Slider) les éléments sont tous définis à partir du modèle de vue en fonction de l’ensemble de propriété initiale `Color` lorsque le modèle de vue a été instantané. Ceci est illustré dans les captures d’écran suivantes :

[![Sélecteur de couleurs compilé](compiled-bindings-images/compiledcolorselector-small.png "Sélecteur de couleurs compilé")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Sélecteur de couleurs compilé")

Au fur et à mesure [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) que les curseurs sont manipulés, les éléments et les éléments sont mis à jour en conséquence.

Pour plus d’informations sur ce sélecteur de couleur, consultez [ViewModels et notifications de modification de propriété](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Utiliser des fixations compilées dans un DataTemplate

Les liaisons [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) dans un sont interprétées dans le contexte de l’objet modélé. Par conséquent, lorsque vous utilisez des liaisons compilées dans un `DataTemplate`, le `DataTemplate` doit déclarer le type de son objet de données à l’aide de l’attribut `x:DataType`.

La page **Compiled Color List** (Liste de couleurs compilée) illustre l’utilisation des liaisons compilées dans un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

La [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) propriété est située `NamedColor.All` sur la propriété statique. La `NamedColor` classe utilise la réflexion .NET pour énumérer [`Color`](xref:Xamarin.Forms.Color) tous les champs publics statiques de la structure, `All` et pour les stocker avec leurs noms dans une collection accessible à partir de la propriété statique. Par conséquent, l’objet `ListView` est rempli avec toutes les instances de `NamedColor`. Pour chaque élément figurant dans `ListView`, le contexte de liaison de l’élément est défini sur un objet `NamedColor`. Les [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) éléments et [`ViewCell`](xref:Xamarin.Forms.ViewCell) les `NamedColor` éléments dans le sont liés aux propriétés.

Notez [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que `x:DataType` l’attribut définit `NamedColor` comme le type, indiquant `DataTemplate` que toutes les expressions contraignantes dans la hiérarchie de vue seront compilées. Cela peut être vérifié en modifiant les expressions de liaison à lier à une propriété de `NamedColor` inexistante, ce qui entraînera une erreur de build.  Bien que cet `x:DataType` exemple donne l’attribut à une chaîne littérale, `x:Type` il peut également être réglé à un type avec l’extension de balisage. Pour plus d’informations sur l’extension de `x:Type` balisage, voir [x:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

Lorsque l’application est [`ListView`](xref:Xamarin.Forms.ListView) exécutée pour `NamedColor` la première fois, la demande est remplie d’instances. Lorsqu’un élément `ListView` dans le [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) est sélectionné, la propriété est `ListView`définie à la couleur de l’élément sélectionné dans le :

[![Liste des couleurs compilées](compiled-bindings-images/compiledcolorlist-small.png "Liste de couleurs compilée]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Liste des couleurs compilées")

Sélection d’autres [`ListView`](xref:Xamarin.Forms.BoxView) éléments dans les [`BoxView`](xref:Xamarin.Forms.BoxView)mises à jour de la couleur de la .

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combinez les fixations compilées avec les fixations classiques

Les expressions de liaison sont compilées uniquement pour la hiérarchie d’affichage sur laquelle l’attribut `x:DataType` est défini. À l’inverse, toutes les vues dans une hiérarchie sur laquelle l’attribut `x:DataType` n’est pas défini utiliseront des liaisons classiques. Par conséquent, il est possible de combiner des liaisons compilées et classiques sur une page. Par exemple, dans la section [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) précédente, les vues [`BoxView`](xref:Xamarin.Forms.BoxView) dans l’utilisation ont compilé [`ListView`](xref:Xamarin.Forms.ListView) des fixations, tandis que le qui est réglé sur la couleur sélectionnée dans le n’est pas.

La structuration prudente des attributs `x:DataType` permet ainsi d’obtenir une page utilisant des liaisons compilées et classiques. Vous pouvez également redéfinir l’attribut `x:DataType` à tout moment dans une hiérarchie d’affichage sur `null` à l’aide de l’extension de balisage `x:Null`. Cela indique que toutes les expressions de liaison figurant dans la hiérarchie d’affichage utiliseront des liaisons classiques. La page *Mixed Bindings* (Liaisons mixtes) illustre cette approche :

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) racine `x:DataType` définit l’attribut pour être le `HslColorViewModel` type, `StackLayout` indiquant que toute expression contraignante dans la hiérarchie de vue de racine sera compilée. Toutefois, le `StackLayout` interne redéfinit l’attribut `x:DataType` sur `null` avec l’expression de balisage `x:Null`. Par conséquent, les expressions de liaison figurant dans le `StackLayout` interne utilisent des liaisons classiques. Seul [`BoxView`](xref:Xamarin.Forms.BoxView)le , `StackLayout` dans la hiérarchie de la vue root, utilise des fixations compilées.

Pour plus d'informations sur l’expression de balisage `x:Null`, consultez [Extension de balisage x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Performances

Les liaisons compilées améliorent de façon variable les performances de liaison de données. Les tests unitaires révèlent que :

- Une liaison compilée qui utilise la notification de modification de propriété (c'est-à-dire une liaison `OneWay`, `OneWayToSource` ou `TwoWay`) est résolue environ 8 fois plus vite qu’une liaison classique.
- Une liaison compilée qui n’utilise pas la notification de modification de propriété (c'est-à-dire une liaison `OneTime`) est résolue environ 20 fois plus vite qu’une liaison classique.
- Définir [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) la fixation sur une liaison compilée qui `OneWay`utilise `OneWayToSource`la `TwoWay` notification de changement de propriété `BindingContext` (c.-à-d. un , , ou la liaison) est environ 5 fois plus rapide que la mise sur une liaison classique.
- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) fixation sur une liaison compilée qui n’utilise pas `OneTime` la notification de changement de `BindingContext` propriété (c.-à-d. une liaison) est environ 7 fois plus rapide que le réglage de la fixation sur une liaison classique.

Ces différences de performances peuvent être accrues sur les appareils mobiles, selon la plateforme utilisée, la version du système d’exploitation utilisé et l’appareil sur lequel l’application s’exécute.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
