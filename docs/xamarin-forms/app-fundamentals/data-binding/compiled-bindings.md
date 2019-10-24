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
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697154"
---
# <a name="xamarinforms-compiled-bindings"></a>Liaisons compilées Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_Les liaisons compilées sont résolues plus rapidement que les liaisons classiques. Elles améliorent ainsi les performances des liaisons de données dans les applications Xamarin.Forms._

Les liaisons de données présentent deux problèmes principaux :

1. Aucune validation des expressions de liaison n’a lieu au moment de la compilation. À la place, les liaisons sont résolues au moment de l’exécution. Par conséquent, les liaisons non valides ne sont pas détectées avant l’exécution, lorsque l’application ne se comporte pas comme prévu ou que des messages d’erreur apparaissent.
1. Elles ne sont pas rentables. Les liaisons sont résolues au moment de l’exécution via l’inspection (réflexion) des objets à usage général et la surcharge de cette opération varie d’une plateforme à l’autre.

Les liaisons compilées améliorent les performances de liaison de données dans les applications Xamarin.Forms en résolvant les expressions de liaison au moment de la compilation plutôt qu’au moment de l’exécution. En outre, cette validation des expressions de liaison au moment de la compilation favorise une meilleure expérience de dépannage pour les développeurs, car les liaisons non valides sont signalées comme erreurs de build.

Le processus permettant d’utiliser des liaisons compilées est le suivant :

1. Activez la compilation XAML. Pour plus d’informations sur la compilation XAML, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).
1. Définissez un attribut `x:DataType` sur un objet [`VisualElement`](xref:Xamarin.Forms.VisualElement) correspondant au type de l’objet auquel `VisualElement` et ses enfants seront liés.

> [!NOTE]
> Il est recommandé de définir l’attribut `x:DataType` dans la hiérarchie d’affichage au niveau où [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) est défini. Toutefois, cet attribut peut être redéfini à n’importe quel emplacement d’une hiérarchie d’affichage.

Pour utiliser des liaisons compilées, l’attribut `x:DataType` doit avoir pour valeur un littéral de chaîne ou un type utilisant l’extension de balisage `x:Type`. Au moment de la compilation XAML, toutes les expressions de liaison non valides seront signalées comme erreurs de build. Toutefois, le compilateur XAML signalera uniquement une erreur de build pour la première expression de liaison non valide qu’il rencontrera. Toutes les expressions de liaison valides qui sont définies sur l’objet `VisualElement` ou ses enfants seront compilées, que [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) soit défini en XAML ou dans le code. La compilation d’une expression de liaison génère du code compilé qui obtiendra une valeur d’une propriété de la *source* et la définira sur la propriété de la *cible* spécifiée dans le balisage. En outre, en fonction de l’expression de liaison, le code généré peut observer des modifications dans la valeur de la propriété *source* et actualiser la propriété *cible*, et il peut renvoyer (push) des modifications à partir de la *cible* vers la *source*.

> [!IMPORTANT]
> Les liaisons compilées sont actuellement désactivées pour toutes les expressions de liaison qui définissent la propriété [`Source`](xref:Xamarin.Forms.Binding.Source). Cela tient au fait que la propriété `Source` est toujours définie à l’aide de l’extension de balisage `x:Reference`, qui ne peut pas être résolue au moment de la compilation.

## <a name="use-compiled-bindings"></a>Utiliser des liaisons compilées

La page du **Sélecteur de couleurs compilées** montre l’utilisation de liaisons compilées entre les vues Xamarin. Forms et les propriétés ViewModel :

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

La racine [`StackLayout`](xref:Xamarin.Forms.StackLayout) instancie `HslColorViewModel` et initialise la propriété `Color` dans les balises d’élément de propriété pour la propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Cette `StackLayout` racine définit également l’attribut `x:DataType` comme type ViewModel, ce qui indique que toutes les expressions de liaison dans la hiérarchie d’affichage `StackLayout` racine seront compilées. Cela peut être vérifié en modifiant l’une des expressions de liaison à lier à une propriété ViewModel inexistante, ce qui provoque une erreur de génération. Bien que cet exemple affecte un littéral de chaîne à l’attribut `x:DataType`, il peut également être défini sur un type avec l’extension de balisage `x:Type`. Pour plus d’informations sur l’extension de balisage `x:Type`, consultez [X :type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

> [!IMPORTANT]
> L’attribut `x:DataType` peut être redéfini à tout emplacement dans une hiérarchie d’affichage.

Les éléments [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) et les vues [`Slider`](xref:Xamarin.Forms.Slider) héritent du contexte de liaison de [`StackLayout`](xref:Xamarin.Forms.StackLayout). Ces vues sont toutes des cibles de liaison qui référencent des propriétés de source dans le ViewModel. Pour la propriété [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) et la propriété [`Label.Text`](xref:Xamarin.Forms.Label.Text) , les liaisons de données sont `OneWay` : les propriétés de la vue sont définies à partir des propriétés dans le ViewModel. Toutefois, la propriété [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) utilise une liaison `TwoWay`. Cela permet de définir chaque `Slider` à partir du ViewModel, et également de définir le ViewModel à partir de chaque `Slider`.

Lorsque l’application est exécutée pour la première fois, les éléments [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) et [`Slider`](xref:Xamarin.Forms.Slider) sont tous définis à partir du ViewModel en fonction de la propriété initiale du `Color` définie lors de l’instanciation du ViewModel. Ceci est illustré dans les captures d’écran suivantes :

[![Sélecteur de couleurs compilées](compiled-bindings-images/compiledcolorselector-small.png "Sélecteur de couleurs compilées")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Sélecteur de couleurs compilées")

Lorsque les curseurs sont manipulés, les éléments [`BoxView`](xref:Xamarin.Forms.BoxView) et [`Label`](xref:Xamarin.Forms.Label) sont mis à jour en conséquence.

Pour plus d’informations sur ce sélecteur de couleur, consultez [ViewModels et notifications de modification de propriété](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Utiliser des liaisons compilées dans un DataTemplate

Les liaisons figurant dans un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) sont interprétées dans le contexte de l’objet basé sur le modèle. Par conséquent, lorsque vous utilisez des liaisons compilées dans un `DataTemplate`, le `DataTemplate` doit déclarer le type de son objet de données à l’aide de l’attribut `x:DataType`.

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

La propriété [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) est définie sur la propriété `NamedColor.All` statique. La classe `NamedColor` utilise la réflexion .NET pour énumérer tous les champs publics statiques dans la structure [`Color`](xref:Xamarin.Forms.Color) et pour les stocker avec leurs noms dans une collection accessible à partir de la propriété `All` statique. Par conséquent, l’objet `ListView` est rempli avec toutes les instances de `NamedColor`. Pour chaque élément figurant dans `ListView`, le contexte de liaison de l’élément est défini sur un objet `NamedColor`. Les éléments [`BoxView`](xref:Xamarin.Forms.BoxView) et [`Label`](xref:Xamarin.Forms.Label) dans l’objet [`ViewCell`](xref:Xamarin.Forms.ViewCell) sont liés aux propriétés de `NamedColor`.

Notez que le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définit l’attribut `x:DataType` en tant que type de `NamedColor`, ce qui indique que toutes les expressions de liaison figurant dans la hiérarchie d’affichage `DataTemplate` seront compilées. Cela peut être vérifié en modifiant les expressions de liaison à lier à une propriété de `NamedColor` inexistante, ce qui entraînera une erreur de build.  Bien que cet exemple affecte un littéral de chaîne à l’attribut `x:DataType`, il peut également être défini sur un type avec l’extension de balisage `x:Type`. Pour plus d’informations sur l’extension de balisage `x:Type`, consultez [X :type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

À la première exécution de l’application, [`ListView`](xref:Xamarin.Forms.ListView) est rempli avec les instances de `NamedColor`. Lorsqu’un élément dans `ListView` est sélectionné, la propriété [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) est définie sur la couleur de l’élément sélectionné dans `ListView` :

[![Liste des couleurs compilées](compiled-bindings-images/compiledcolorlist-small.png "Liste de couleurs compilée]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

La sélection d’autres éléments dans l’objet [`ListView`](xref:Xamarin.Forms.BoxView) met à jour la couleur de l’objet [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combiner des liaisons compilées avec des liaisons classiques

Les expressions de liaison sont compilées uniquement pour la hiérarchie d’affichage sur laquelle l’attribut `x:DataType` est défini. À l’inverse, toutes les vues dans une hiérarchie sur laquelle l’attribut `x:DataType` n’est pas défini utiliseront des liaisons classiques. Par conséquent, il est possible de combiner des liaisons compilées et classiques sur une page. Par exemple, dans la section précédente, les vues figurant dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) utilisent des liaisons compilées, alors que ce n’est pas le cas du [`BoxView`](xref:Xamarin.Forms.BoxView) qui est défini sur la couleur sélectionnée dans l’objet [`ListView`](xref:Xamarin.Forms.ListView).

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

Le [`StackLayout`](xref:Xamarin.Forms.StackLayout) racine définit l’attribut `x:DataType` en tant que type `HslColorViewModel`, ce qui indique que toutes les expressions de liaison figurant dans la hiérarchie d’affichage `StackLayout` racine seront compilées. Toutefois, le `StackLayout` interne redéfinit l’attribut `x:DataType` sur `null` avec l’expression de balisage `x:Null`. Par conséquent, les expressions de liaison figurant dans le `StackLayout` interne utilisent des liaisons classiques. Seul l’objet [`BoxView`](xref:Xamarin.Forms.BoxView), au sein de la hiérarchie d’affichage `StackLayout` racine, utilise des liaisons compilées.

Pour plus d'informations sur l’expression de balisage `x:Null`, consultez [Extension de balisage x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Performances

Les liaisons compilées améliorent de façon variable les performances de liaison de données. Les tests unitaires révèlent que :

- Une liaison compilée qui utilise la notification de modification de propriété (c'est-à-dire une liaison `OneWay`, `OneWayToSource` ou `TwoWay`) est résolue environ 8 fois plus vite qu’une liaison classique.
- Une liaison compilée qui n’utilise pas la notification de modification de propriété (c'est-à-dire une liaison `OneTime`) est résolue environ 20 fois plus vite qu’une liaison classique.
- La définition du [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) sur une liaison compilée qui utilise la notification de modification de propriété (c'est-à-dire une liaison `OneWay`, `OneWayToSource` ou `TwoWay`) est environ 5 fois plus rapide que la définition du `BindingContext` sur une liaison classique.
- La définition du [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) sur une liaison compilée qui n’utilise pas la notification de modification de propriété (c'est-à-dire une liaison `OneTime`) est environ 7 fois plus rapide que la définition du `BindingContext` sur une liaison classique.

Ces différences de performances peuvent être accrues sur les appareils mobiles, selon la plateforme utilisée, la version du système d’exploitation utilisé et l’appareil sur lequel l’application s’exécute.

## <a name="related-links"></a>Liens connexes

- [Démos des liaisons de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
