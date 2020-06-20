---
title: Dispositions pouvant être liées dansXamarin.Forms
description: Les dispositions pouvant être liées permettent aux classes de disposition de générer leur contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de chaque élément avec un DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9d0497c0c0593b54f69bac84307976c4050e9c95
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138240"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Dispositions pouvant être liées dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Les dispositions pouvant être liées activent toute classe de disposition qui dérive de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe pour générer son contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de chaque élément avec un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Les dispositions pouvant être liées sont fournies par la `BindableLayout` classe, qui expose les propriétés jointes suivantes :

- `ItemsSource`: spécifie la collection d' `IEnumerable` éléments à afficher par la disposition.
- `ItemTemplate`: spécifie le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à appliquer à chaque élément de la collection d’éléments affichés par la disposition.
- `ItemTemplateSelector`– Spécifie le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.

> [!NOTE]
> La `ItemTemplate` propriété est prioritaire lorsque les `ItemTemplate` Propriétés et `ItemTemplateSelector` sont définies.

En outre, la `BindableLayout` classe expose les propriétés pouvant être liées suivantes :

- `EmptyView`: spécifie la `string` vue ou qui s’affiche lorsque la `ItemsSource` propriété est ou `null` lorsque la collection spécifiée par la `ItemsSource` propriété est ou est `null` vide. La valeur par défaut est `null`.
- `EmptyViewTemplate`– Spécifie le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui sera affiché lorsque la `ItemsSource` propriété est `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. La valeur par défaut est `null`.

> [!NOTE]
> La `EmptyViewTemplate` propriété est prioritaire lorsque les `EmptyView` Propriétés et `EmptyViewTemplate` sont définies.

Toutes ces propriétés peuvent être attachées aux [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) classes, [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) , [`Grid`](xref:Xamarin.Forms.Grid) , [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) et [`StackLayout`](xref:Xamarin.Forms.StackLayout) , qui dérivent toutes de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe.

La `Layout<T>` classe expose une [`Children`](xref:Xamarin.Forms.Layout`1.Children) collection à laquelle les éléments enfants d’une disposition sont ajoutés. Lorsque la `BinableLayout.ItemsSource` propriété est définie sur une collection d’éléments et attachée à une [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe dérivée de, chaque élément de la collection est ajouté à la `Layout<T>.Children` collection pour être affiché par la disposition. La `Layout<T>` classe dérivée de met alors à jour ses vues enfants lorsque la collection sous-jacente est modifiée. Pour plus d’informations sur le Xamarin.Forms cycle de disposition, consultez [création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md).

Les dispositions pouvant être liées doivent être utilisées uniquement lorsque la collection d’éléments à afficher est petite, et que le défilement et la sélection ne sont pas requis. Si le défilement peut être fourni en encapsulant une disposition pouvant être liée dans un [`ScrollView`](xref:Xamarin.Forms.ScrollView) , cela n’est pas recommandé car les dispositions pouvant être liées ne possèdent pas de virtualisation de l’interface utilisateur. Lorsque le défilement est requis, vous devez utiliser une vue avec défilement qui comprend la virtualisation de l’interface utilisateur, telle que [`ListView`](xref:Xamarin.Forms.ListView) ou [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Si vous ne respectez pas cette recommandation, vous risquez de provoquer des problèmes de performances.

> [!IMPORTANT]
> Bien qu’il soit techniquement possible d’attacher une disposition pouvant être liée à une classe de disposition dérivée de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe, il n’est pas toujours pratique de le faire, en particulier pour les [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) [`Grid`](xref:Xamarin.Forms.Grid) classes, et [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Par exemple, considérez le scénario qui souhaite afficher une collection de données dans un [`Grid`](xref:Xamarin.Forms.Grid) à l’aide d’une disposition pouvant être liée, où chaque élément de la collection est un objet contenant plusieurs propriétés. Chaque ligne du `Grid` doit afficher un objet de la collection, chaque colonne de l' `Grid` affichage de l’une des propriétés de l’objet. Étant donné que le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour la disposition pouvant être liée ne peut contenir qu’un seul objet, il est nécessaire que cet objet soit une classe de disposition contenant plusieurs vues qui affichent chacune une des propriétés de l’objet dans une `Grid` colonne spécifique. Bien que ce scénario puisse être réalisé avec des dispositions pouvant être liées, il en résulte un parent `Grid` contenant un enfant `Grid` pour chaque élément de la collection liée, qui est une utilisation très inefficace et problématique de la `Grid` disposition.

## <a name="populate-a-bindable-layout-with-data"></a>Remplir une disposition pouvant être liée avec des données

Une disposition pouvant être liée est remplie avec des données en affectant à sa `ItemsSource` propriété une collection qui implémente `IEnumerable` , et en l’attachant à une [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe dérivée de :

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Le code C# équivalent est :

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quand la `BindableLayout.ItemsSource` propriété jointe est définie sur une disposition, mais `BindableLayout.ItemTemplate` que la propriété jointe n’est pas définie, chaque élément de la `IEnumerable` collection est affiché par un [`Label`](xref:Xamarin.Forms.Label) créé par la `BindableLayout` classe.

## <a name="define-item-appearance"></a>Définir l’apparence des éléments

L’apparence de chaque élément dans la disposition pouvant être liée peut être définie en affectant `BindableLayout.ItemTemplate` à la propriété jointe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

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

Dans cet exemple, chaque élément de la `TopFollowers` collection sera affiché par une `CircleImage` vue définie dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

![Disposition pouvant être liée avec un DataTemplate](bindable-layouts-images/top-followers.png "Disposition pouvant être liée avec un modèle de données")

Pour plus d’informations sur les modèles de données, consultez [ Xamarin.Forms modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément dans la disposition pouvant être liée peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant `BindableLayout.ItemTemplateSelector` à la propriété jointe [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

La `TechItemTemplateSelector` classe définit `DefaultTemplate` et les `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriétés qui sont définies sur des modèles de données différents. La `OnSelectTemplate` méthode retourne le `XamarinFormsTemplate` , qui affiche un élément en rouge foncé avec un cœur à côté de celui-ci, lorsque l’élément est égal à « Xamarin.Forms ». Lorsque l’élément n’est pas égal à « Xamarin.Forms », la `OnSelectTemplate` méthode retourne le `DefaultTemplate` , qui affiche un élément à l’aide de la couleur par défaut d’un [`Label`](xref:Xamarin.Forms.Label) :

![Disposition pouvant être liée avec un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Disposition pouvant être liée avec un sélecteur de modèle de données")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [création d’un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Afficher une chaîne lorsque les données ne sont pas disponibles

La `EmptyView` propriété peut être définie sur une chaîne, qui sera affichée par un [`Label`](xref:Xamarin.Forms.Label) lorsque la `ItemsSource` propriété a la valeur `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. Le code XAML suivant illustre un exemple de ce scénario :

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

Le résultat est que, lorsque la collection liée aux données est `null` , la chaîne définie en tant que `EmptyView` valeur de propriété est affichée :

[![Capture d’écran d’une vue de chaîne de disposition pouvant être liée, sur iOS et Android](bindable-layouts-images/emptyview-string.png "Vue vide de chaîne de disposition pouvant être liée")](bindable-layouts-images/emptyview-string-large.png#lightbox "Vue vide de chaîne de disposition pouvant être liée")

## <a name="display-views-when-data-is-unavailable"></a>Afficher les affichages quand les données ne sont pas disponibles

La `EmptyView` propriété peut être définie sur une vue, qui sera affichée lorsque la `ItemsSource` propriété a la valeur `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. Il peut s’agir d’une vue unique ou d’une vue contenant plusieurs vues enfants. L’exemple de code XAML suivant montre `EmptyView` que la propriété est définie sur une vue qui contient plusieurs vues enfants :

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyView>
        <StackLayout>
            <Label Text="None."
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
            <Label Text="Try harder and return later?"
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
        </StackLayout>
    </BindableLayout.EmptyView>
    ...
</StackLayout>
```

Le résultat est que, lorsque la collection liée aux données est `null` , le [`StackLayout`](xref:Xamarin.Forms.StackLayout) et ses vues enfants sont affichés.

[![Capture d’écran d’une disposition pouvant être liée, d’un affichage vide avec plusieurs vues, sur iOS et Android](bindable-layouts-images/emptyview-views.png "Vue vide de la disposition pouvant être liée")](bindable-layouts-images/emptyview-views-large.png#lightbox "Vue vide de la disposition pouvant être liée")

De même, `EmptyViewTemplate` peut être défini sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui est affiché lorsque la `ItemsSource` propriété a la valeur `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. Le `DataTemplate` peut contenir une vue unique ou une vue qui contient plusieurs vues enfants. En outre, l' `BindingContext` du `EmptyViewTemplate` sera hérité de l' `BindingContext` du `BindableLayout` . L’exemple de code XAML suivant montre `EmptyViewTemplate` que la propriété a pour valeur un `DataTemplate` qui contient une vue unique :

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyViewTemplate>
        <DataTemplate>
            <Label Text="{Binding Source={x:Reference usernameLabel}, Path=Text, StringFormat='{0} has no achievements.'}" />
        </DataTemplate>
    </BindableLayout.EmptyViewTemplate>
    ...
</StackLayout>
```

Le résultat est que, lorsque la collection liée aux données est `null` , le [`Label`](xref:Xamarin.Forms.Label) dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est affiché :

[![Capture d’écran d’une disposition pouvant être liée, modèle de vue vide, sur iOS et Android](bindable-layouts-images/emptyviewtemplate.png "Modèle de vue de disposition pouvant être lié")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "Modèle de vue de disposition pouvant être lié")

> [!NOTE]
> La `EmptyViewTemplate` propriété ne peut pas être définie via un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) .

## <a name="choose-an-emptyview-at-runtime"></a>Choisir un EmptyView au moment de l’exécution

Les vues qui s’affichent sous la forme d’un `EmptyView` lorsque les données ne sont pas disponibles, peuvent être définies en tant qu' [`ContentView`](xref:Xamarin.Forms.ContentView) objets dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . La `EmptyView` propriété peut ensuite être définie sur un spécifique `ContentView` , en fonction de la logique métier, au moment de l’exécution. Le code XAML suivant illustre un exemple de ce scénario :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...    
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No achievements."
                       FontSize="14" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="None."
                       FontAttributes="Italic"
                       FontSize="14" />
                <Label Text="Try harder and return later?"
                       FontAttributes="Italic"
                       FontSize="14" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <Switch Toggled="OnEmptyViewSwitchToggled" />

        <StackLayout x:Name="stackLayout"
                     BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
            ...
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Le code XAML définit deux [`ContentView`](xref:Xamarin.Forms.ContentView) objets au niveau de la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , l' [`Switch`](xref:Xamarin.Forms.Switch) objet contrôlant l’objet qui sera `ContentView` défini comme `EmptyView` valeur de propriété. Quand `Switch` est basculé, le `OnEmptyViewSwitchToggled` Gestionnaire d’événements exécute la `ToggleEmptyView` méthode :

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

La `ToggleEmptyView` méthode affecte `EmptyView` à la propriété de l' `stackLayout` objet l’un des deux [`ContentView`](xref:Xamarin.Forms.ContentView) objets stockés dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , en fonction de la valeur de la [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriété. Ensuite, lorsque la collection liée aux données est `null` , l' `ContentView` objet défini comme `EmptyView` propriété s’affiche :

[![Capture d’écran du choix d’affichage vide au moment de l’exécution, sur iOS et Android](bindable-layouts-images/emptyview-runtime.png "Disposition pouvant être liée vue vide affichage du Runtime")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "Disposition pouvant être liée vue vide affichage du Runtime")

## <a name="related-links"></a>Liens connexes

- [Démonstration de la mise en page pouvant être liée (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.FormsModèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Création d’un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
