---
title: Page maître/détail Xamarin.Forms
description: 'MasterDetailPage de Xamarin.Forms est une page qui gère deux pages d’informations liées : une page maître présentant des éléments et une page de détail présentant les détails relatifs aux éléments de la page maître. Cet article explique comment utiliser MasterDetailPage et comment naviguer entre ses pages d’informations.'
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 702ec35bca051f7255c5c9d67d2dc68d4f89ca52
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68645958"
---
# <a name="xamarinforms-master-detail-page"></a>Page maître/détail Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_Le Xamarin.Forms MasterDetailPage est une page qui gère deux pages d’information connexes - une page maîtresse qui présente des éléments, et une page de détails qui présente des détails sur les éléments sur la page principale. Cet article explique comment utiliser un MasterDetailPage et naviguer entre ses pages d’information._

## <a name="overview"></a>Vue d’ensemble

Une page maître affiche généralement une liste d’éléments, comme indiqué dans les captures d’écran suivantes :

[![](master-detail-page-images/masterpage-components.png "Master Page Components")](master-detail-page-images/masterpage-components-large.png#lightbox "Master Page Components")

L’emplacement de la liste d’éléments est identique sur chaque plateforme. La sélection de l’un des éléments permet d’accéder à la page de détail correspondante. De plus, la page maître comporte également une barre de navigation qui contient un bouton permettant d’accéder à la page de détail active :

- Sur iOS, la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail. De plus, vous pouvez accéder à la page de détail active en faisant glisser la page maître vers la gauche.
- Sur Android, la barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton qui permettent d’accéder à la page de détail. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android. De plus, vous pouvez accéder à la page de détail active en faisant glisser la page maître vers la gauche, en appuyant sur la page de détail située à l’extrême droite de l’écran, puis en appuyant sur le bouton *Précédent* au bas de l’écran.
- Sur UWP (plateforme Windows universelle), la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail.

Une page de détail affiche les données qui correspondent à l’élément sélectionné dans la page maître. Les principaux composants de la page de détail sont illustrés dans les captures d’écran suivantes :

![](master-detail-page-images/detailpage-components.png "Detail Page Components")

La page de détail contient une barre de navigation dont le contenu dépend de la plateforme :

- Sur iOS, la barre de navigation est présente en haut de la page et affiche un titre, et dispose [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) d’un bouton qui retourne à la page principale, à condition que l’instance de la page de détail est enveloppé dans l’instance. De plus, vous pouvez retourner à la page maître en faisant glisser la page de détail vers la droite.
- Sur Android, une barre de navigation est présente en haut de la page. Elle affiche un titre et une icône ainsi qu’un bouton qui permet de retourner à la page maître. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android.
- Sur UWP, la barre de navigation est présente en haut de la page. Elle affiche un titre ainsi qu’un bouton qui permet de retourner à la page maître.

### <a name="navigation-behavior"></a>Comportement de la navigation

Le comportement de l’expérience de navigation entre les pages maîtres et les pages de détail dépend de la plateforme :

- Sur iOS, la page de détail *glisse* vers la droite tandis que la page maître glisse à partir de la gauche. La partie gauche de la page de détail est toujours visible.
- Sur Android, les pages de détail et les pages maîtres se *superposent*.
- Sur UWP, la page principale glisse à partir de la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) gauche sur `Popover`une partie de la page de détail, à condition que la propriété est définie à . Pour plus d’informations, consultez [Contrôle du comportement d’affichage de la page de détail](#Controlling_the_Detail_Page_Display_Behavior).

Un comportement similaire se produit en mode paysage, à la différence que la page maître sur iOS et Android a une largeur similaire à celle de la page maître en mode portrait. Ainsi, une plus grande partie de la page de détail est visible.

Pour plus d’informations sur le contrôle du comportement de la navigation, consultez [Contrôle du comportement d’affichage de la page de détail](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Création d’un MasterDetailPage

A [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) contient [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) et les propriétés qui sont à la fois de type [`Page`](xref:Xamarin.Forms.Page), qui sont utilisés pour obtenir et définir le maître et les pages de détail respectivement.

> [!IMPORTANT]
> A [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) est conçu pour être une page racine, et l’utiliser comme une page d’enfant dans d’autres types de page pourrait entraîner un comportement inattendu et incohérent. En outre, il est recommandé que [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) la page [`ContentPage`](xref:Xamarin.Forms.ContentPage) principale d’un devrait toujours être un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)exemple, `ContentPage` et que la page de détail ne doit être peuplée avec , , et les instances. Cela vous aidera à garantir une expérience utilisateur cohérente sur toutes les plateformes.

L’exemple de code XAML suivant montre un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) qui définit le et [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) les propriétés:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

L’exemple de code [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) suivant montre l’équivalent créé dans C :

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

La [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriété est [`ContentPage`](xref:Xamarin.Forms.ContentPage) réglée à un cas. La [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriété est [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) réglée `ContentPage` à un contenant une instance.

### <a name="creating-the-master-page"></a>Création de la page maître

L’exemple de code XAML `MasterPage` suivant montre la déclaration [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) de l’objet, qui est référencée par la propriété :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
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

La page se [`ListView`](xref:Xamarin.Forms.ListView) compose d’un qui est peuplé de [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) données dans `MasterPageItem` XAML en définissant sa propriété à un éventail d’instances. Chaque `MasterPageItem` définit les propriétés `Title`, `IconSource` et `TargetType`.

A [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est attribué [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) à la `MasterPageItem`propriété, pour afficher chacun . Le `DataTemplate` contient [`ViewCell`](xref:Xamarin.Forms.ViewCell) un qui [`Image`](xref:Xamarin.Forms.Image) se [`Label`](xref:Xamarin.Forms.Label)compose d’un et un . La [`Image`](xref:Xamarin.Forms.Image) valeur `IconSource` de la [`Label`](xref:Xamarin.Forms.Label) propriété `Title` affiche, et `MasterPageItem`les affiches la valeur de la propriété, pour chacun .

La page [`Title`](xref:Xamarin.Forms.Page.Title) a [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) son et les propriétés définies. L’icône s’affiche dans la page de détail, à condition que celle-ci comporte une barre de titre. Cela doit être activé sur iOS en [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) enveloppant l’instance de page de détail dans un cas.

> [!NOTE]
> La [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) page doit [`Title`](xref:Xamarin.Forms.Page.Title) avoir son ensemble de propriété, ou une exception se produira.

L’exemple de code suivant montre la page équivalente créée en C# :

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
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
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

Les captures d’écran suivantes montrent la page maître sur chaque plateforme :

![](master-detail-page-images/masterpage.png "Master Page Example")

### <a name="creating-and-displaying-the-detail-page"></a>Création et affichage de la page de détail

L’instance `MasterPage` `ListView` contient une propriété [`ListView`](xref:Xamarin.Forms.ListView) qui expose `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) son instance afin que l’instance puisse enregistrer un gestionnaire d’événements pour gérer l’événement. [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) Cela permet `MainPage` à l’instance de définir [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) la `ListView` propriété à la page qui représente l’élément sélectionné. L’exemple de code suivant illustre le gestionnaire d’événements :

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

La méthode `OnItemSelected` effectue les actions suivantes :

- Il récupère [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) [`ListView`](xref:Xamarin.Forms.ListView) l’instance, et à condition `null`qu’il ne soit pas , définit la `TargetType` page de `MasterPageItem`détail à une nouvelle instance du type de page stockée dans la propriété de la . Le type de page [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) est enveloppé dans une instance [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) pour `MasterPage` s’assurer que l’icône référencée à travers la propriété sur la page de détail dans iOS.
- L’élément sélectionné [`ListView`](xref:Xamarin.Forms.ListView) dans `null` le est défini `ListView` pour s’assurer qu’aucun des éléments ne sera sélectionné la prochaine fois que l’élément `MasterPage` sera présenté.
- La page de détail est présentée [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) à `false`l’utilisateur en définissant la propriété à . Cette propriété détermine si la page maître ou la page de détail est présentée. Elle doit avoir la valeur `true` pour afficher la page maître et la valeur `false` pour afficher la page de détail.

Les captures d’écran suivantes montrent la page de détail `ContactPage`, qui s’affiche une fois qu’elle a été sélectionnée dans la page maître :

![](master-detail-page-images/detailpage.png "Detail Page Example")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Contrôle du comportement d’affichage de la page de détail

La [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) façon dont le maître gère les pages de maître et de détail dépend de la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) question de savoir si l’application fonctionne sur un téléphone ou une tablette, l’orientation de l’appareil et la valeur de la propriété. Cette propriété détermine le mode d’affichage de la page de détail. Valeurs possibles :

- **Default** - Les pages sont affichées à l’aide de la plateforme par défaut.
- **Popover** - La page de détail recouvre complètement ou partiellement la page maître.
- **Split** - La page maître est affichée à gauche, et la page de détail à droite.
- **SplitOnLandscape** - Un écran partagé est utilisé quand l’appareil est en orientation paysage.
- **SplitOnPortrait** - Un écran partagé est utilisé quand l’appareil est en orientation portrait.

L’exemple de code XAML suivant [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) montre [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)comment définir la propriété sur un :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

L’exemple de code [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) suivant montre l’équivalent créé dans C :

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

Cependant, la valeur [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) de la propriété n’affecte que les applications fonctionnant sur tablettes ou sur le bureau. Les applications qui s’exécutent sur des téléphones ont toujours le comportement *Popover*.

## <a name="summary"></a>Récapitulatif

Cet article a démontré [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) comment utiliser un et naviguer entre ses pages d’information. `MasterDetailPage` de Xamarin.Forms est une page qui gère deux pages d’informations liées : une page maître présentant des éléments et une page de détail présentant les détails relatifs aux éléments de la page maître.

## <a name="related-links"></a>Liens connexes

- [Types de pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
