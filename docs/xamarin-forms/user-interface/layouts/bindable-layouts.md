---
title: Dispositions pouvant être liées dans Xamarin. Forms
description: Les dispositions pouvant être liées permettent aux classes de disposition de générer leur contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de chaque élément avec un DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68647905"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Dispositions pouvant être liées dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Les dispositions pouvant être liées activent toute classe de disposition qui dérive de la classe [`Layout<T>`](xref:Xamarin.Forms.Layout`1) pour générer son contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de chaque élément avec une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Les dispositions pouvant être liées sont fournies par la classe `BindableLayout`, qui expose les propriétés jointes suivantes :

- `ItemsSource` : spécifie la collection d’éléments de `IEnumerable` à afficher par la disposition.
- `ItemTemplate` : spécifie le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à appliquer à chaque élément de la collection d’éléments affichés par la disposition.
- `ItemTemplateSelector` : spécifie le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.

Ces propriétés peuvent être attachées aux classes [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)et [`StackLayout`](xref:Xamarin.Forms.StackLayout) , qui dérivent toutes de la classe [1](xref:Xamarin.Forms.Layout`1) .

> [!NOTE]
> La propriété `ItemTemplate` est prioritaire lorsque les propriétés `ItemTemplate` et `ItemTemplateSelector` sont définies.

La classe `Layout<T>` expose une collection [`Children`](xref:Xamarin.Forms.Layout`1.Children) , à laquelle les éléments enfants d’une disposition sont ajoutés. Lorsque la propriété `BinableLayout.ItemsSource` est définie sur une collection d’éléments et attachée à une classe dérivée d' [`Layout<T>`](xref:Xamarin.Forms.Layout`1), chaque élément de la collection est ajouté à la collection `Layout<T>.Children` pour être affiché par la disposition. La classe dérivée de `Layout<T>` met alors à jour ses vues enfants lorsque la collection sous-jacente est modifiée. Pour plus d’informations sur le cycle de disposition Xamarin. Forms, consultez [création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md).

Les dispositions pouvant être liées doivent être utilisées uniquement lorsque la collection d’éléments à afficher est petite, et que le défilement et la sélection ne sont pas requis. Si le défilement peut être fourni en encapsulant une disposition pouvant être liée dans un [`ScrollView`](xref:Xamarin.Forms.ScrollView), cela n’est pas recommandé car les dispositions pouvant être liées ne possèdent pas de virtualisation de l’interface utilisateur. Lorsque le défilement est requis, vous devez utiliser une vue avec défilement qui comprend la virtualisation de l’interface utilisateur, par exemple [`ListView`](xref:Xamarin.Forms.ListView) ou [`CollectionView`](xref:Xamarin.Forms.CollectionView). Si vous ne respectez pas cette recommandation, vous risquez de provoquer des problèmes de performances.

> [!IMPORTANT]
>Bien qu’il soit techniquement possible d’attacher une disposition pouvant être liée à une classe de disposition dérivée de la classe [`Layout<T>`](xref:Xamarin.Forms.Layout`1) , il n’est pas toujours pratique de le faire, en particulier pour les classes [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`Grid`](xref:Xamarin.Forms.Grid)et [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Par exemple, considérez le scénario dans lequel vous souhaitez afficher une collection de données dans un [`Grid`](xref:Xamarin.Forms.Grid) à l’aide d’une disposition pouvant être liée, où chaque élément de la collection est un objet contenant plusieurs propriétés. Chaque ligne du `Grid` doit afficher un objet de la collection, chaque colonne du `Grid` affichant l’une des propriétés de l’objet. Étant donné que la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour la disposition pouvant être liée ne peut contenir qu’un seul objet, il est nécessaire que cet objet soit une classe de disposition contenant plusieurs vues qui affichent chacune une des propriétés de l’objet dans une colonne `Grid` spécifique. Bien que ce scénario puisse être réalisé avec des dispositions pouvant être liées, il en résulte un `Grid` parent contenant un `Grid` enfant pour chaque élément de la collection liée, ce qui constitue une utilisation très inefficace et problématique de la disposition `Grid`.

## <a name="populating-a-bindable-layout-with-data"></a>Remplissage d’une disposition pouvant être liée à l’aide de données

Une disposition pouvant être liée est remplie avec des données en affectant à sa propriété `ItemsSource` la valeur d’une collection qui implémente `IEnumerable` et en l’attachant à une classe dérivée de [`Layout<T>`](xref:Xamarin.Forms.Layout`1):

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Le code C# équivalent est :

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Lorsque la `BindableLayout.ItemsSource` propriété jointe est définie sur une disposition, mais que la propriété jointe `BindableLayout.ItemTemplate` n’est pas définie, chaque élément de la collection `IEnumerable` est affiché par un [`Label`](xref:Xamarin.Forms.Label) créé par la classe `BindableLayout`.

## <a name="defining-item-appearance"></a>Définition de l’apparence des éléments

L’apparence de chaque élément dans la disposition pouvant être liée peut être définie en affectant à la `BindableLayout.ItemTemplate` propriété jointe un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

Le code C# équivalent est :

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

Dans cet exemple, chaque élément de la collection `TopFollowers` est affiché par une vue `CircleImage` définie dans la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![Disposition pouvant être liée avec un DataTemplate](bindable-layouts-images/top-followers.png "Disposition pouvant être liée avec un modèle de données")

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Choix de l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément dans la disposition pouvant être liée peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant à la `BindableLayout.ItemTemplateSelector` propriété jointe un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

Le code C# équivalent est :

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

Le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) utilisé dans l’exemple d’application est illustré dans l’exemple suivant :

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

La classe `TechItemTemplateSelector` définit les propriétés de `DefaultTemplate` et de `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui sont définies sur des modèles de données différents. La méthode `OnSelectTemplate` retourne le `XamarinFormsTemplate`, qui affiche un élément en rouge foncé avec un cœur à côté de celui-ci, lorsque l’élément est égal à « Xamarin. Forms ». Lorsque l’élément n’est pas égal à « Xamarin. Forms », la méthode `OnSelectTemplate` retourne l' `DefaultTemplate`, qui affiche un élément à l’aide de la couleur par défaut d’un [`Label`](xref:Xamarin.Forms.Label):

![Disposition pouvant être liée avec un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Disposition pouvant être liée avec un sélecteur de modèle de données")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [création d’un Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Liens connexes

- [Démonstration de la mise en page pouvant être liée (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modèles de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Création d’un Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
