---
title: Xamarin.Forms Chemin de liaison
description: Cet article explique comment utiliser Xamarin.Forms des liaisons de données pour accéder aux sous-propriétés et aux membres de collection avec la propriété Path de la classe Binding.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 154219f58c22005de0a0a2171aeedd04ec9f9ff9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557957"
---
# <a name="no-locxamarinforms-binding-path"></a>Xamarin.Forms Chemin de liaison

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Dans tous les exemples de liaison de données précédents, la [`Path`](xref:Xamarin.Forms.Binding.Path) propriété de la `Binding` classe (ou [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) de la propriété de l' `Binding` extension de balisage) a été définie sur une seule propriété. Il est en fait possible de définir `Path` sur une *sous-propriété* (une propriété d’une propriété) ou un membre d’une collection.

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
             xmlns:globe="clr-namespace:System.Globalization;assembly=netstandard"
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

La source est définie sur la propriété `CultureInfo.CurrentCulture` statique, qui est un objet de type `CultureInfo`. Cette classe définit une propriété nommée `DateTimeFormat` de type [`DateTimeFormatInfo`](xref:System.Globalization.DateTimeFormatInfo) qui contient une `DayNames` collection. L’index sélectionne le quatrième élément.

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

Consultez [Transmission des arguments de constructeur](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments) pour plus d’informations sur la spécification des arguments de constructeur en XAML.

Enfin, le dernier exemple est similaire au second, à ceci près qu’il fait référence à l’un des enfants de `StackLayout` :

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Cet enfant est un objet `Label`, qui a une propriété `Text` de type `String`, qui a une propriété `Length`. Le premier objet `Label` signale le `TimeSpan` défini dans `TimePicker`, de sorte que lorsque ce texte est modifié, le dernier objet `Label` change également.

Voici le programme en cours d’exécution :

[![Path Variations](binding-path-images/pathvariations-small.png "Path Variations (Variations de chemin)")](binding-path-images/pathvariations-large.png#lightbox "Path Variations (Variations de chemin)") (Variations de chemin)

## <a name="debugging-complex-paths"></a>Débogage de chemins complexes

Les définitions de chemins complexes peuvent être difficiles à construire : vous devez connaître le type de chaque sous-propriété ou le type des éléments dans la collection pour ajouter correctement la sous-propriété suivante, mais les types eux-mêmes n’apparaissent pas dans le chemin. Une bonne technique consiste à générer le chemin de façon incrémentielle et d’examiner les résultats intermédiaires. Pour ce dernier exemple, vous pouvez commencer sans aucune définition `Path` :

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

Le type de la propriété `Content` est désormais révélé comme `Xamarin.Forms.StackLayout`. Ajoutez la `Children` propriété à `Path` et le type est `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]` , qui est une classe interne à Xamarin.Forms , mais qui est évidemment un type de collection. Ajoutez un index à ceci et le type est `Xamarin.Forms.Label`. Continuez de cette façon.

Comme Xamarin.Forms traite le chemin de liaison, il installe un `PropertyChanged` Gestionnaire sur tout objet du chemin d’accès qui implémente l' `INotifyPropertyChanged` interface. Par exemple, la liaison finale réagit à une modification dans le premier objet `Label`, car la propriété `Text` change.

Si une propriété dans le chemin de liaison n’implémente pas `INotifyPropertyChanged`, toutes les modifications apportées à cette propriété sont ignorées. Certaines modifications pourraient invalider entièrement le chemin de liaison et vous devriez utiliser cette technique uniquement lorsque la chaîne des propriétés et les sous-propriétés ne deviennent jamais non valides.

## <a name="related-links"></a>Liens associés

- [Démonstrations de liaison de données (exemple)](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur la liaison de données à partir de Xamarin.Forms Book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)