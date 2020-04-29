---
title: Dispositions pouvant être liées dans Xamarin. Forms
description: Les dispositions pouvant être liées permettent aux classes de disposition de générer leur contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de chaque élément avec un DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2020
ms.openlocfilehash: d084d910786c24a4b0333ecbc3e893cfe144d404
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517203"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Dispositions pouvant être liées dans Xamarin. Forms

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Les dispositions pouvant être liées activent toute classe de disposition qui dérive de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe pour générer son contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)chaque élément avec un. Les dispositions pouvant être liées sont fournies par `BindableLayout` la classe, qui expose les propriétés jointes suivantes :

- `ItemsSource`: spécifie la collection `IEnumerable` d’éléments à afficher par la disposition.
- `ItemTemplate`: spécifie [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) le à appliquer à chaque élément de la collection d’éléments affichés par la disposition.
- `ItemTemplateSelector`– spécifie [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) le qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.

> [!NOTE]
> La `ItemTemplate` propriété est prioritaire lorsque les `ItemTemplate` propriétés et `ItemTemplateSelector` sont définies.

En outre, la `BindableLayout` classe expose les propriétés pouvant être liées suivantes :

- `EmptyView`: spécifie `string` la vue ou qui s’affiche lorsque la `ItemsSource` propriété est `null`ou lorsque la collection spécifiée par la `ItemsSource` propriété est ou `null` est vide. La valeur par défaut est `null`.
- `EmptyViewTemplate`– spécifie [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) le qui sera affiché lorsque la `ItemsSource` propriété est `null`, ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. La valeur par défaut est `null`.

> [!NOTE]
> La `EmptyViewTemplate` propriété est prioritaire lorsque les `EmptyView` propriétés et `EmptyViewTemplate` sont définies.

Toutes ces propriétés peuvent être attachées aux classes [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid) [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), et [`StackLayout`](xref:Xamarin.Forms.StackLayout) , qui dérivent toutes de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe.

La `Layout<T>` classe expose une [`Children`](xref:Xamarin.Forms.Layout`1.Children) collection à laquelle les éléments enfants d’une disposition sont ajoutés. Lorsque la `BinableLayout.ItemsSource` propriété est définie sur une collection d’éléments et attachée à [`Layout<T>`](xref:Xamarin.Forms.Layout`1)une classe dérivée de, chaque élément de la collection est ajouté `Layout<T>.Children` à la collection pour être affiché par la disposition. La `Layout<T>`classe dérivée de met alors à jour ses vues enfants lorsque la collection sous-jacente est modifiée. Pour plus d’informations sur le cycle de disposition Xamarin. Forms, consultez [création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md).

Les dispositions pouvant être liées doivent être utilisées uniquement lorsque la collection d’éléments à afficher est petite, et que le défilement et la sélection ne sont pas requis. Si le défilement peut être fourni en encapsulant une disposition pouvant être liée [`ScrollView`](xref:Xamarin.Forms.ScrollView)dans un, cela n’est pas recommandé car les dispositions pouvant être liées ne possèdent pas de virtualisation de l’interface utilisateur. Lorsque le défilement est requis, vous devez utiliser une vue avec défilement qui comprend la virtualisation de [`ListView`](xref:Xamarin.Forms.ListView) l' [`CollectionView`](xref:Xamarin.Forms.CollectionView)interface utilisateur, telle que ou. Si vous ne respectez pas cette recommandation, vous risquez de provoquer des problèmes de performances.

> [!IMPORTANT]
> Bien qu’il soit techniquement possible d’attacher une disposition pouvant être liée à une classe de disposition dérivée [`Layout<T>`](xref:Xamarin.Forms.Layout`1) de la classe, il n’est pas toujours pratique de le faire, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)en [`Grid`](xref:Xamarin.Forms.Grid)particulier pour [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) les classes, et. Par exemple, considérez le scénario qui souhaite afficher une collection de données dans un [`Grid`](xref:Xamarin.Forms.Grid) à l’aide d’une disposition pouvant être liée, où chaque élément de la collection est un objet contenant plusieurs propriétés. Chaque ligne du `Grid` doit afficher un objet de la collection, chaque colonne de l' `Grid` affichage de l’une des propriétés de l’objet. Étant donné [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que le pour la disposition pouvant être liée ne peut contenir qu’un seul objet, il est nécessaire que cet objet soit une classe de disposition contenant plusieurs vues qui affichent chacune une des propriétés de `Grid` l’objet dans une colonne spécifique. Bien que ce scénario puisse être réalisé avec des dispositions pouvant être liées, il en résulte `Grid` un parent contenant `Grid` un enfant pour chaque élément de la collection liée, qui est une utilisation très inefficace et problématique `Grid` de la disposition.

## <a name="populate-a-bindable-layout-with-data"></a>Remplir une disposition pouvant être liée avec des données

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

## <a name="define-item-appearance"></a>Définir l’apparence des éléments

L’apparence de chaque élément dans la disposition pouvant être liée peut être définie en affectant à la `BindableLayout.ItemTemplate` propriété [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)jointe :

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

Dans cet exemple, chaque élément de la `TopFollowers` collection sera affiché par une `CircleImage` vue définie dans le : [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)

![Disposition pouvant être liée avec un DataTemplate](bindable-layouts-images/top-followers.png "Disposition pouvant être liée avec un modèle de données")

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

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

La `TechItemTemplateSelector` classe définit `DefaultTemplate` et `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) les propriétés qui sont définies sur des modèles de données différents. La `OnSelectTemplate` méthode retourne le `XamarinFormsTemplate`, qui affiche un élément en rouge foncé avec un cœur à côté de celui-ci, lorsque l’élément est égal à « Xamarin. Forms ». Lorsque l’élément n’est pas égal à « Xamarin. Forms `OnSelectTemplate` », la `DefaultTemplate`méthode retourne le, qui affiche un élément à l’aide [`Label`](xref:Xamarin.Forms.Label)de la couleur par défaut d’un :

![Disposition pouvant être liée avec un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Disposition pouvant être liée avec un sélecteur de modèle de données")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [création d’un Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Afficher une chaîne lorsque les données ne sont pas disponibles

La `EmptyView` propriété peut être définie sur une chaîne, qui [`Label`](xref:Xamarin.Forms.Label) sera affichée par un lorsque la propriété `ItemsSource` a `null`la valeur, ou lorsque la collection spécifiée par `ItemsSource` la propriété `null` est ou est vide. Le code XAML suivant illustre un exemple de ce scénario :

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

Le résultat est que, lorsque la collection liée aux `null`données est, la chaîne définie `EmptyView` en tant que valeur de propriété est affichée :

[![Capture d’écran d’une vue de chaîne de disposition pouvant être liée, sur iOS et Android](bindable-layouts-images/emptyview-string.png "Vue vide de chaîne de disposition pouvant être liée")](bindable-layouts-images/emptyview-string-large.png#lightbox "Vue vide de chaîne de disposition pouvant être liée")

## <a name="display-views-when-data-is-unavailable"></a>Afficher les affichages quand les données ne sont pas disponibles

La `EmptyView` propriété peut être définie sur une vue, qui sera affichée lorsque la propriété `ItemsSource` a `null`la valeur, ou lorsque la collection spécifiée par `ItemsSource` la propriété `null` est ou est vide. Il peut s’agir d’une vue unique ou d’une vue contenant plusieurs vues enfants. L’exemple de code XAML suivant `EmptyView` montre que la propriété est définie sur une vue qui contient plusieurs vues enfants :

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

Le résultat est que, lorsque la collection liée aux `null`données est [`StackLayout`](xref:Xamarin.Forms.StackLayout) , le et ses vues enfants sont affichés.

[![Capture d’écran d’une disposition pouvant être liée, d’un affichage vide avec plusieurs vues, sur iOS et Android](bindable-layouts-images/emptyview-views.png "Vue vide de la disposition pouvant être liée")](bindable-layouts-images/emptyview-views-large.png#lightbox "Vue vide de la disposition pouvant être liée")

De même, `EmptyViewTemplate` peut être défini sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui est affiché lorsque la propriété a `ItemsSource` `null`la valeur, ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. Le `DataTemplate` peut contenir une vue unique ou une vue qui contient plusieurs vues enfants. En outre, l `BindingContext` 'du `EmptyViewTemplate` sera hérité de l' `BindingContext` du `BindableLayout`. L’exemple de code XAML suivant `EmptyViewTemplate` montre que la propriété `DataTemplate` a pour valeur un qui contient une vue unique :

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

Le résultat est que, lorsque la collection liée aux `null`données est [`Label`](xref:Xamarin.Forms.Label) , le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) dans est affiché :

[![Capture d’écran d’une disposition pouvant être liée, modèle de vue vide, sur iOS et Android](bindable-layouts-images/emptyviewtemplate.png "Modèle de vue de disposition pouvant être lié")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "Modèle de vue de disposition pouvant être lié")

> [!NOTE]
> La `EmptyViewTemplate` propriété ne peut pas être définie [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)via un.

## <a name="choose-an-emptyview-at-runtime"></a>Choisir un EmptyView au moment de l’exécution

Les vues qui s’affichent sous `EmptyView` la forme d’un lorsque les données ne sont pas [`ContentView`](xref:Xamarin.Forms.ContentView) disponibles, peuvent [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)être définies en tant qu’objets dans un. La `EmptyView` propriété peut ensuite être définie sur un spécifique `ContentView`, en fonction de la logique métier, au moment de l’exécution. Le code XAML suivant illustre un exemple de ce scénario :

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

Le code XAML définit [`ContentView`](xref:Xamarin.Forms.ContentView) deux objets au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)niveau de la page, l' [`Switch`](xref:Xamarin.Forms.Switch) objet contrôlant `ContentView` l’objet qui sera défini comme `EmptyView` valeur de propriété. Quand `Switch` est basculé, le `OnEmptyViewSwitchToggled` gestionnaire d’événements exécute la `ToggleEmptyView` méthode :

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

La `ToggleEmptyView` méthode affecte à `EmptyView` la propriété de `stackLayout` l’objet l’un des deux [`ContentView`](xref:Xamarin.Forms.ContentView) objets stockés dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), en fonction de la valeur de [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) la propriété. Ensuite, lorsque la collection liée aux données `null`est, `ContentView` l’objet défini comme `EmptyView` propriété s’affiche :

[![Capture d’écran du choix d’affichage vide au moment de l’exécution, sur iOS et Android](bindable-layouts-images/emptyview-runtime.png "Disposition pouvant être liée vue vide affichage du Runtime")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "Disposition pouvant être liée vue vide affichage du Runtime")

## <a name="related-links"></a>Liens connexes

- [Démonstration de la mise en page pouvant être liée (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Création d’un DataTemplateSelector Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
