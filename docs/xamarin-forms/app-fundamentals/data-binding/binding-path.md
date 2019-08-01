---
title: Liaison de données Xamarin.Forms
description: Cet article explique comment utiliser les liaisons de données Xamarin.Forms pour accéder aux sous-propriétés et aux membres de la collection avec la propriété Path de la classe Binding.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b9658aa0a4222fb9d257299ea3c6fd69016abdb4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646747"
---
# <a name="xamarinforms-binding-path"></a>Liaison de données Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Dans tous les exemples précédents de liaison de données, la propriété [`Path`](xref:Xamarin.Forms.Binding.Path) de la classe `Binding` (ou la propriété [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) de l’extension de balisage `Binding`) a été définie sur une propriété unique. Il est en fait possible de définir `Path` sur une *sous-propriété* (une propriété d’une propriété) ou un membre d’une collection.

Par exemple, supposons que votre page contient un `TimePicker` :

```xaml
<TimePicker x:Name="timePicker">
```

La propriété `Time` de `TimePicker` est de type `TimeSpan`, mais vous voulez peut-être créer une liaison de données qui référence la propriété `TotalSeconds` de cette valeur `TimeSpan`. Voici la liaison de données :

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

La propriété `Time` est de type `TimeSpan`, qui a une propriété `TotalSeconds`. Les propriétés `Time` et `TotalSeconds` sont simplement connectées par un point. Les éléments figurant dans la chaîne `Path` référencent toujours les propriétés et non pas les types de ces propriétés.

Cet exemple et plusieurs autres sont illustrés dans la page **Path Variations** (Variations de chemin d’accès) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=mscorlib"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

Dans le deuxième objet `Label`, la source de la liaison est la page elle-même. La propriété `Content` est de type `StackLayout`, qui a une propriété `Children` de type `IList<View>`, qui a une propriété `Count` qui indique le nombre d’enfants.

## <a name="paths-with-indexers"></a>Chemins d’accès avec indexeurs

La liaison figurant dans le troisième objet `Label` dans les pages **Path Variations** référence la classe [`CultureInfo`](xref:System.Globalization.CultureInfo) dans l’espace de noms `System.Globalization` :

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

La source est définie sur la propriété `CultureInfo.CurrentCulture` statique, qui est un objet de type `CultureInfo`. Cette classe définit une propriété nommée `DateTimeFormat` de type [`DateTimeFormatInfo`](xref:System.Globalization.DateTimeFormatInfo) qui contient une collection `DayNames`. L’index sélectionne le quatrième élément.

Le quatrième objet `Label` fait quelque chose de similaire mais pour la culture associée à la France. La propriété `Source` de la liaison est définie sur l’objet `CultureInfo` avec un constructeur :

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

Consultez [Transmission des arguments de constructeur](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) pour plus d’informations sur la spécification des arguments de constructeur en XAML.

Enfin, le dernier exemple est similaire au second, à ceci près qu’il fait référence à l’un des enfants de `StackLayout` :

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Cet enfant est un objet `Label`, qui a une propriété `Text` de type `String`, qui a une propriété `Length`. Le premier objet `Label` signale le `TimeSpan` défini dans `TimePicker`, de sorte que lorsque ce texte est modifié, le dernier objet `Label` change également.

Voici le programme en cours d’exécution :

[![Path Variations](binding-path-images/pathvariations-small.png "Path Variations")](binding-path-images/pathvariations-large.png#lightbox "Path Variations")

## <a name="debugging-complex-paths"></a>Débogage de chemins complexes

Les définitions de chemins complexes peuvent être difficiles à construire : Vous devez connaître le type de chaque sous-propriété ou le type des éléments dans la collection pour ajouter correctement la sous-propriété suivante, mais les types eux-mêmes n’apparaissent pas dans le chemin. Une bonne technique consiste à générer le chemin de façon incrémentielle et d’examiner les résultats intermédiaires. Pour ce dernier exemple, vous pouvez commencer sans aucune définition `Path` :

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Ceci affiche le type de la source de la liaison, ou `DataBindingDemos.PathVariationsPage`. Vous savez que `PathVariationsPage` dérive de `ContentPage` et possède donc une propriété `Content` :

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

Le type de la propriété `Content` est désormais révélé comme `Xamarin.Forms.StackLayout`. Ajoutez la propriété `Children` à `Path` et le type est `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, qui est une classe interne à Xamarin.Forms, mais de toute évidence un type de collection. Ajoutez un index à ceci et le type est `Xamarin.Forms.Label`. Continuez de cette façon.

Lorsque Xamarin.Forms traite le chemin de liaison, il installe un gestionnaire `PropertyChanged` sur n’importe quel objet dans le chemin qui implémente l’interface `INotifyPropertyChanged`. Par exemple, la liaison finale réagit à une modification dans le premier objet `Label`, car la propriété `Text` change.

Si une propriété dans le chemin de liaison n’implémente pas `INotifyPropertyChanged`, toutes les modifications apportées à cette propriété sont ignorées. Certaines modifications pourraient invalider entièrement le chemin de liaison et vous devriez utiliser cette technique uniquement lorsque la chaîne des propriétés et les sous-propriétés ne deviennent jamais non valides.



## <a name="related-links"></a>Liens associés

- [Démos des liaisons de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur les liaisons de données dans la documentation de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
