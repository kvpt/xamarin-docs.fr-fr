---
title: Xamarin.Forms FlyoutPage
description: 'Le Xamarin.Forms FlyoutPage est une page qui gère deux pages d’informations associées : une page de menu volant qui présente des éléments et une page de détails qui présente des détails sur les éléments de la page du menu volant. Cet article explique comment utiliser un FlyoutPage et naviguer entre ses pages d’informations.'
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ea0be2136b354ef7a613904799481079bcae52ad
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115234"
---
# <a name="no-locxamarinforms-flyoutpage"></a>Xamarin.Forms FlyoutPage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)

Une page de menu volant affiche généralement une liste d’éléments, comme illustré dans les captures d’écran suivantes :

[![Composants de la page de menu volant](flyoutpage-images/flyoutpage-components.png)](flyoutpage-images/flyoutpage-components-large.png#lightbox "Composants de la page de menu volant")

L’emplacement de la liste d’éléments est identique sur chaque plateforme. La sélection de l’un des éléments permet d’accéder à la page de détail correspondante. En outre, la page de menu volant comporte également une barre de navigation qui contient un bouton qui peut être utilisé pour accéder à la page de détails active :

- Sur iOS, la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail. De plus, vous pouvez accéder à la page de détail active en faisant glisser la page maître vers la gauche.
- Sur Android, la barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton qui permettent d’accéder à la page de détail. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android. En outre, vous pouvez accéder à la page de détails active en passant la page de menu volant à gauche, en appuyant sur la page de détails à l’extrême droite de l’écran, puis en appuyant sur le bouton *précédent* situé en bas de l’écran.
- Sur UWP (plateforme Windows universelle), la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail.

Une page de détails affiche les données correspondant à l’élément sélectionné dans la page de menu volant et les principaux composants de la page de détails sont présentés dans les captures d’écran suivantes :

![Composants de la page de détail](flyoutpage-images/detailpage-components.png)

La page de détail contient une barre de navigation dont le contenu dépend de la plateforme :

- Sur iOS, la barre de navigation est présente en haut de la page et affiche un titre, et comporte un bouton qui retourne à la page de menu volant, à condition que l’instance de page de détails soit incluse dans un wrapper dans l' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance de. En outre, la page de menu volant peut être retournée en passant la page de détails à droite.
- Sur Android, une barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton qui retourne à la page de menu volant. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android.
- Sur UWP, la barre de navigation est présente en haut de la page et affiche un titre et un bouton qui retourne à la page de menu volant.

## <a name="navigation-behavior"></a>Comportement de navigation

Le comportement de l’expérience de navigation entre le menu volant et les pages de détails dépend de la plateforme :

- Sur iOS, la *page de détails s’affiche à droite* , tandis que la page de menu volant glisse à gauche, et la partie gauche de la page de détails est toujours visible.
- Sur Android, les pages de détails et de menu volant sont *superposées* les unes aux autres.
- Sur UWP, la page du menu volant glisse à gauche de la page de détails, à condition que la [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) propriété ait la valeur `Popover` .

Un comportement similaire sera observé en mode paysage, sauf que la page de menu volant sur iOS et Android a une largeur similaire à celle de la page de menu volant en mode portrait, si bien plus la page de détails sera visible.

Pour plus d’informations sur le contrôle du comportement de navigation, consultez [contrôler le comportement de mise en page des détails](#control-the-detail-page-layout-behavior).

## <a name="create-a-flyoutpage"></a>Créer un FlyoutPage

[`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage)Contient [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) les propriétés et qui sont toutes deux de type [`Page`](xref:Xamarin.Forms.Page) , qui sont utilisées pour obtenir et définir les pages de menu volant et détails respectivement.

> [!IMPORTANT]
> Un [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) est conçu pour être une page racine et son utilisation comme page enfant dans d’autres types de page peut entraîner un comportement inattendu et incohérent. En outre, il est recommandé que la page de menu volant d’un `FlyoutPage` doit toujours être une [`ContentPage`](xref:Xamarin.Forms.ContentPage) instance et que la page de détails ne soit remplie qu’avec les [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instances, et `ContentPage` . Cela vous aidera à garantir une expérience utilisateur cohérente sur toutes les plateformes.

L’exemple de code XAML suivant montre un [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) qui définit [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) les [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) Propriétés et :

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:FlyoutPageNavigation;assembly=FlyoutPageNavigation"
            x:Class="FlyoutPageNavigation.MainPage">
    <FlyoutPage.Flyout>
        <local:FlyoutMenuPage x:Name="flyoutPage" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </FlyoutPage.Detail>
</FlyoutPage>
```

L’exemple de code suivant montre l’équivalent [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) créé en C# :

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        flyoutPage = new FlyoutMenuPageCS();
        Flyout = flyoutPage;
        Detail = new NavigationPage(new ContactsPageCS());
        ...
    }
    ...
}    
```

La [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) propriété est définie sur une [`ContentPage`](xref:Xamarin.Forms.ContentPage) instance. La [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) propriété est définie sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qui contient une `ContentPage` instance.

### <a name="create-the-flyout-page"></a>Créer la page de menu volant

L’exemple de code XAML suivant illustre la déclaration de l' `FlyoutMenuPage` objet, qui est référencé par le biais de la [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) propriété :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:FlyoutPageNavigation"
             x:Class="FlyoutPageNavigation.FlyoutMenuPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:FlyoutPageItem}">
                    <local:FlyoutPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:FlyoutPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:FlyoutPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

La page se compose d’un [`ListView`](xref:Xamarin.Forms.ListView) qui est rempli avec les données en XAML en affectant [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) à sa propriété un tableau d' `FlyoutPageItem` objets. Chaque `FlyoutPageItem` définit les propriétés `Title`, `IconSource` et `TargetType`.

Une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est assignée à la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriété pour afficher chacune `FlyoutPageItem` . Le `DataTemplate` contient un [`ViewCell`](xref:Xamarin.Forms.ViewCell) qui se compose d’un [`Image`](xref:Xamarin.Forms.Image) et d’un [`Label`](xref:Xamarin.Forms.Label) . Le [`Image`](xref:Xamarin.Forms.Image) affiche la `IconSource` valeur de la propriété et [`Label`](xref:Xamarin.Forms.Label) affiche la `Title` valeur de la propriété pour chaque `FlyoutPageItem` .

Les [`Title`](xref:Xamarin.Forms.Page.Title) Propriétés et de la page sont [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) définies. L’icône s’affiche dans la page de détail, à condition que celle-ci comporte une barre de titre. Vous devez activer cette option sur iOS en encapsulant l’instance de page de détails dans une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance.

> [!NOTE]
> La [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) propriété doit être définie pour la page [`Title`](xref:Xamarin.Forms.Page.Title) , sinon une exception se produit.

L’exemple de code suivant montre la page équivalente créée en C# :

```csharp
public class FlyoutMenuPageCS : ContentPage
{
    ListView listView;
    public ListView ListView { get { return listView; } }

    public FlyoutMenuPageCS()
    {
        var flyoutPageItems = new List<FlyoutPageItem>();
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Contacts",
            IconSource = "contacts.png",
            TargetType = typeof(ContactsPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "TodoList",
            IconSource = "todo.png",
            TargetType = typeof(TodoListPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Reminders",
            IconSource = "reminders.png",
            TargetType = typeof(ReminderPageCS)
        });

        listView = new ListView
        {
            ItemsSource = flyoutPageItems,
            ItemTemplate = new DataTemplate(() =>
            {
                var grid = new Grid { Padding = new Thickness(5, 10) };
                grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
                grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

                var image = new Image();
                image.SetBinding(Image.SourceProperty, "IconSource");
                var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
                label.SetBinding(Label.TextProperty, "Title");

                grid.Children.Add(image);
                grid.Children.Add(label, 1, 0);

                return new ViewCell { View = grid };
            }),
            SeparatorVisibility = SeparatorVisibility.None
        };

        IconImageSource = "hamburger.png";
        Title = "Personal Organiser";
        Padding = new Thickness(0, 40, 0, 0);
        Content = new StackLayout
        {
            Children = { listView }
        };
    }
}
```

Les captures d’écran suivantes montrent la page de menu volant sur chaque plateforme :

![Exemple de page maître](flyoutpage-images/flyoutpage.png)

### <a name="create-and-display-the-detail-page"></a>Créer et afficher la page de détails

L' `FlyoutMenuPage` instance contient une `ListView` propriété qui expose son [`ListView`](xref:Xamarin.Forms.ListView) instance afin que l' `MainPage` [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) instance puisse inscrire un gestionnaire d’événements pour gérer l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement. Cela permet `MainPage` à l’instance de définir la [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) propriété sur la page qui représente l' `ListView` élément sélectionné. L’exemple de code suivant illustre le gestionnaire d’événements :

```csharp
public partial class MainPage : FlyoutPage
{
    public MainPage()
    {
        ...
        flyoutPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as FlyoutPageItem;
        if (item != null)
        {
            Detail = new NavigationPage((Page)Activator.CreateInstance(item.TargetType));
            flyoutPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

La méthode `OnItemSelected` effectue les actions suivantes :

- Elle récupère le [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) de l' [`ListView`](xref:Xamarin.Forms.ListView) instance de et à condition que ce ne soit pas le cas `null` , définit la page de détails sur une nouvelle instance du type de page stocké dans la `TargetType` propriété de `FlyoutPageItem` . Le type de page est encapsulé dans une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance pour garantir que l’icône référencée via la [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) propriété sur le `FlyoutMenuPage` s’affiche sur la page de détails dans iOS.
- L’élément sélectionné dans la [`ListView`](xref:Xamarin.Forms.ListView) est défini sur `null` pour s’assurer qu’aucun des `ListView` éléments ne sera sélectionné la prochaine fois que le `FlyoutMenuPage` sera présenté.
- La page de détails est présentée à l’utilisateur en affectant à la propriété la valeur [`FlyoutPage.IsPresented`](xref:Xamarin.Forms.FlyoutPage.IsPresented) `false` . Cette propriété contrôle si le menu volant ou la page de détails est présenté. Elle doit avoir la valeur `true` pour afficher la page de menu volant et pour `false` afficher la page de détails.

Les captures d’écran suivantes montrent la `ContactPage` page de détails, qui s’affiche après qu’elle a été sélectionnée sur la page du menu volant :

![Exemple de page de détail](flyoutpage-images/detailpage.png)

### <a name="control-the-detail-page-layout-behavior"></a>Contrôler le comportement de la disposition de la page de détails

La manière dont [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) gère le menu volant et les pages de détails varie selon que l’application s’exécute sur un téléphone ou une tablette, l’orientation de l’appareil et la valeur de la [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) propriété. Cette propriété détermine le mode d’affichage de la page de détail. Valeurs possibles :

- `Default` : Les pages sont affichées à l’aide de la plateforme par défaut.
- `Popover` : La page de détails couvre ou couvre partiellement la page du menu volant.
- `Split` : La page de menu volant s’affiche à gauche et la page de détails est à droite.
- `SplitOnLandscape` : Un écran fractionné est utilisé lorsque l’appareil est en orientation paysage.
- `SplitOnPortrait` : Un écran fractionné est utilisé lorsque l’appareil est en orientation portrait.

L’exemple de code XAML suivant montre comment définir la [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) propriété sur un [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) :

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            x:Class="FlyoutPageNavigation.MainPage"
            FlyoutLayoutBehavior="Popover">
  ...
</FlyoutPage>
```

L’exemple de code suivant montre l’équivalent [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) créé en C# :

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        ...
        FlyoutLayoutBehavior = FlyoutLayoutBehavior.Popover;
    }
}
```

> [!IMPORTANT]
> La valeur de la [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) propriété affecte uniquement les applications qui s’exécutent sur les tablettes ou le bureau. Les applications qui s’exécutent sur des téléphones ont toujours le `Popover` comportement.

## <a name="related-links"></a>Liens connexes

- [Types de pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [FlyoutPage (exemple)](/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)
- [API FlyoutPage](xref:Xamarin.Forms.FlyoutPage)