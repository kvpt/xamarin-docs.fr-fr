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
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68647905"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Dispositions pouvant être liées dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Les dispositions pouvant être liées activent toute classe de disposition qui dérive de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe pour générer son contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de chaque élément avec un. [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) Les dispositions pouvant être liées sont fournies par `BindableLayout` la classe, qui expose les propriétés jointes suivantes :

- `ItemsSource`: spécifie la collection `IEnumerable` d’éléments à afficher par la disposition.
- `ItemTemplate`: spécifie [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) le à appliquer à chaque élément de la collection d’éléments affichés par la disposition.
- `ItemTemplateSelector`– spécifie [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) le qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.

Ces propriétés peuvent être attachées aux [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classes [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), et [`StackLayout`](xref:Xamarin.Forms.StackLayout) , qui dérivent toutes de [`Layout<T>`](xref:Xamarin.Forms.Layout`1) la classe.

> [!NOTE]
> La `ItemTemplate` propriété est prioritaire lorsque les `ItemTemplate` propriétés et `ItemTemplateSelector` sont définies.

La `Layout<T>` classe expose une [`Children`](xref:Xamarin.Forms.Layout`1.Children) collection à laquelle les éléments enfants d’une disposition sont ajoutés. Lorsque la `BinableLayout.ItemsSource` propriété est définie sur une collection d’éléments et attachée à [`Layout<T>`](xref:Xamarin.Forms.Layout`1)une classe dérivée de, chaque élément de la collection est ajouté `Layout<T>.Children` à la collection pour être affiché par la disposition. La `Layout<T>`classe dérivée de met alors à jour ses vues enfants lorsque la collection sous-jacente est modifiée. Pour plus d’informations sur le cycle de disposition Xamarin. Forms, consultez [création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md).

Les dispositions pouvant être liées doivent être utilisées uniquement lorsque la collection d’éléments à afficher est petite, et que le défilement et la sélection ne sont pas requis. Si le défilement peut être fourni en encapsulant une disposition pouvant être liée [`ScrollView`](xref:Xamarin.Forms.ScrollView)dans un, cela n’est pas recommandé car les dispositions pouvant être liées ne possèdent pas de virtualisation de l’interface utilisateur. Lorsque le défilement est requis, vous devez utiliser une vue avec défilement qui comprend la virtualisation de [`ListView`](xref:Xamarin.Forms.ListView) l' [`CollectionView`](xref:Xamarin.Forms.CollectionView)interface utilisateur, telle que ou. Si vous ne respectez pas cette recommandation, vous risquez de provoquer des problèmes de performances.

> [!IMPORTANT]
>Bien qu’il soit techniquement possible d’attacher une disposition pouvant être liée à une classe de disposition dérivée [`Layout<T>`](xref:Xamarin.Forms.Layout`1) de la classe, il n’est pas toujours pratique de le faire, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)en particulier pour [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) les classes, [`Grid`](xref:Xamarin.Forms.Grid)et. Par exemple, considérez le scénario qui souhaite afficher une collection de données dans un [`Grid`](xref:Xamarin.Forms.Grid) à l’aide d’une disposition pouvant être liée, où chaque élément de la collection est un objet contenant plusieurs propriétés. Chaque ligne `Grid` du doit afficher un objet de la collection, chaque colonne de l' `Grid` affichage de l’une des propriétés de l’objet. Étant donné [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que le pour la disposition pouvant être liée ne peut contenir qu’un seul objet, il est nécessaire que cet objet soit une classe de disposition contenant plusieurs vues qui affichent chacune une des propriétés de `Grid` l’objet dans une colonne spécifique. Bien que ce scénario puisse être réalisé avec des dispositions pouvant être liées, il en résulte `Grid` un parent contenant `Grid` un enfant pour chaque élément de la collection liée, qui est une utilisation très inefficace et problématique `Grid` de la disposition.

## <a name="populating-a-bindable-layout-with-data"></a>Remplissage d’une disposition pouvant être liée à l’aide de données

Une disposition pouvant être liée est remplie avec des données en `ItemsSource` affectant à sa propriété une collection `IEnumerable`qui implémente, et en l' [`Layout<T>`](xref:Xamarin.Forms.Layout`1)attachant à une classe dérivée de :

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Le code C# équivalent est :

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quand la `BindableLayout.ItemsSource` propriété jointe est définie sur une disposition, mais `BindableLayout.ItemTemplate` que la propriété jointe n’est pas définie `IEnumerable` , chaque élément de la collection [`Label`](xref:Xamarin.Forms.Label) est affiché par un créé `BindableLayout` par la classe.

## <a name="defining-item-appearance"></a>Définition de l’apparence des éléments

L’apparence de chaque élément dans la disposition pouvant être liée peut être définie en affectant `BindableLayout.ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)à la propriété jointe :

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

Dans cet exemple, chaque élément de la `TopFollowers` collection sera affiché par une `CircleImage` vue définie dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![Disposition pouvant être liée avec un DataTemplate](bindable-layouts-images/top-followers.png "Disposition pouvant être liée avec un modèle de données")

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Choix de l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément dans la disposition pouvant être liée peut être choisie au moment de l’exécution, en fonction de la valeur `BindableLayout.ItemTemplateSelector` de l’élément, [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)en affectant à la propriété jointe :

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

La `TechItemTemplateSelector` classe définit `DefaultTemplate` et `XamarinFormsTemplate` [lespropriétésquisontdéfiniessurdesmodèles`DataTemplate`](xref:Xamarin.Forms.DataTemplate) de données différents. La `OnSelectTemplate` méthode retourne le `XamarinFormsTemplate`, qui affiche un élément en rouge foncé avec un cœur à côté de celui-ci, lorsque l’élément est égal à « Xamarin. Forms ». Lorsque l’élément n’est pas égal à « Xamarin. Forms `OnSelectTemplate` », la `DefaultTemplate`méthode retourne le, qui affiche un élément à l’aide [`Label`](xref:Xamarin.Forms.Label)de la couleur par défaut d’un :

![Disposition pouvant être liée avec un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Disposition pouvant être liée avec un sélecteur de modèle de données")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [création d’un Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Liens connexes

- [Démonstration de la mise en page pouvant être liée (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modèles de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Création d’un Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
