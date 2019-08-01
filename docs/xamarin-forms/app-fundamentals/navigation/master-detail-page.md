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
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645958"
---
# <a name="xamarinforms-master-detail-page"></a>Page maître/détail Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_MasterDetailPage de Xamarin.Forms est une page qui gère deux pages d’informations liées : une page maître présentant des éléments et une page de détail présentant les détails relatifs aux éléments de la page maître. Cet article explique comment utiliser MasterDetailPage et comment naviguer entre ses pages d’informations._

## <a name="overview"></a>Vue d'ensemble

Une page maître affiche généralement une liste d’éléments, comme indiqué dans les captures d’écran suivantes :

[![](master-detail-page-images/masterpage-components.png "Composants de page maître")](master-detail-page-images/masterpage-components-large.png#lightbox "Composants de page maître")

L’emplacement de la liste d’éléments est identique sur chaque plateforme. La sélection de l’un des éléments permet d’accéder à la page de détail correspondante. De plus, la page maître comporte également une barre de navigation qui contient un bouton permettant d’accéder à la page de détail active :

- Sur iOS, la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail. De plus, vous pouvez accéder à la page de détail active en faisant glisser la page maître vers la gauche.
- Sur Android, la barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton qui permettent d’accéder à la page de détail. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet spécifique à la plateforme Android. De plus, vous pouvez accéder à la page de détail active en faisant glisser la page maître vers la gauche, en appuyant sur la page de détail située à l’extrême droite de l’écran, puis en appuyant sur le bouton *Précédent* au bas de l’écran.
- Sur UWP (plateforme Windows universelle), la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail.

Une page de détail affiche les données qui correspondent à l’élément sélectionné dans la page maître. Les principaux composants de la page de détail sont illustrés dans les captures d’écran suivantes :

![](master-detail-page-images/detailpage-components.png "Composants de la page de détail")

La page de détail contient une barre de navigation dont le contenu dépend de la plateforme :

- Sur iOS, la barre de navigation est présente en haut de la page et affiche un titre. Elle comporte un bouton qui permet de retourner à la page maître, à condition que l’instance de la page de détail soit wrappée dans l’instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). De plus, vous pouvez retourner à la page maître en faisant glisser la page de détail vers la droite.
- Sur Android, une barre de navigation est présente en haut de la page. Elle affiche un titre et une icône ainsi qu’un bouton qui permet de retourner à la page maître. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet spécifique à la plateforme Android.
- Sur UWP, la barre de navigation est présente en haut de la page. Elle affiche un titre ainsi qu’un bouton qui permet de retourner à la page maître.

### <a name="navigation-behavior"></a>Comportement de la navigation

Le comportement de l’expérience de navigation entre les pages maîtres et les pages de détail dépend de la plateforme :

- Sur iOS, la page de détail *glisse* vers la droite tandis que la page maître glisse à partir de la gauche. La partie gauche de la page de détail est toujours visible.
- Sur Android, les pages de détail et les pages maîtres se *superposent*.
- Sur UWP, la page maître glisse à partir de la gauche vers la page de détails, la recouvrant en partie, à condition que la propriété [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) soit définie sur `Popover`. Pour plus d’informations, consultez [Contrôle du comportement d’affichage de la page de détail](#Controlling_the_Detail_Page_Display_Behavior).

Un comportement similaire se produit en mode paysage, à la différence que la page maître sur iOS et Android a une largeur similaire à celle de la page maître en mode portrait. Ainsi, une plus grande partie de la page de détail est visible.

Pour plus d’informations sur le contrôle du comportement de la navigation, consultez [Contrôle du comportement d’affichage de la page de détail](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Création d’un MasterDetailPage

Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) contient les propriétés [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) et [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) qui sont de type [`Page`](xref:Xamarin.Forms.Page). Elles permettent d’obtenir et de définir la page maître et la page de détail, respectivement.

> [!IMPORTANT]
> Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) est conçu pour être une page racine. Son utilisation en tant que page enfant dans d’autres types de page peut entraîner un comportement inattendu et incohérent. De plus, il est recommandé que la page maître d’un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) soit toujours une instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage), et que la page de détail contienne uniquement les instances de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et `ContentPage`. Cela permet de garantir une expérience utilisateur cohérente sur toutes les plateformes.

L’exemple de code XAML suivant montre un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) qui définit les propriétés [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) et [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) :

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

L’exemple de code suivant montre le [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) équivalent créé en C# :

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

La propriété [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) a la valeur d’une instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage). La propriété [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) a la valeur d’un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) contenant une instance de `ContentPage`.

### <a name="creating-the-master-page"></a>Création de la page maître

L’exemple de code XAML suivant illustre la déclaration de l’objet `MasterPage`, qui est référencé via la propriété [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) :

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

La page se compose d’un [`ListView`](xref:Xamarin.Forms.ListView) rempli de données XAML grâce à l’affectation à la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) d’un tableau d’instances de `MasterPageItem`. Chaque `MasterPageItem` définit les propriétés `Title`, `IconSource` et `TargetType`.

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est affecté à la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) pour afficher chaque `MasterPageItem`. `DataTemplate` contient un [`ViewCell`](xref:Xamarin.Forms.ViewCell) qui comprend [`Image`](xref:Xamarin.Forms.Image) et [`Label`](xref:Xamarin.Forms.Label). [`Image`](xref:Xamarin.Forms.Image) affiche la valeur de propriété `IconSource`, et [`Label`](xref:Xamarin.Forms.Label) affiche la valeur de propriété `Title`, pour chaque `MasterPageItem`.

La page a ses propriétés [`Title`](xref:Xamarin.Forms.Page.Title) et [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) définies. L’icône s’affiche dans la page de détail, à condition que celle-ci comporte une barre de titre. Vous devez l’activer sur iOS en wrappant l’instance de la page de détail dans une instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

> [!NOTE]
> La page [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) doit avoir sa propriété [`Title`](xref:Xamarin.Forms.Page.Title) définie, sinon une exception se produit.

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

![](master-detail-page-images/masterpage.png "Exemple de page maître")

### <a name="creating-and-displaying-the-detail-page"></a>Création et affichage de la page de détail

L’instance de `MasterPage` contient une propriété `ListView` qui expose son instance de [`ListView`](xref:Xamarin.Forms.ListView) pour que l’instance de `MainPage`[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) puisse inscrire un gestionnaire d’événements permettant de gérer l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected). Cela permet à l’instance de `MainPage` de définir la propriété [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) en fonction de la page qui représente l’élément `ListView` sélectionné. L’exemple de code suivant illustre le gestionnaire d’événements :

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

- Elle récupère le [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) de l’instance de [`ListView`](xref:Xamarin.Forms.ListView) et, du moment que sa valeur n’est pas une valeur `null`, elle affecte à la page de détail une nouvelle instance du type de page stocké dans la propriété `TargetType` de `MasterPageItem`. Le type de page est wrappé dans une instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) pour garantir que l’icône référencée via la propriété [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) sur `MasterPage` soit affichée dans la page de détail sur iOS.
- L’élément sélectionné dans [`ListView`](xref:Xamarin.Forms.ListView) a la valeur `null`, ce qui permet de garantir qu’aucun des éléments `ListView` n’est sélectionné à la prochaine présentation de `MasterPage`.
- La page de détail est présentée à l’utilisateur quand vous affectez à la propriété [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) la valeur `false`. Cette propriété détermine si la page maître ou la page de détail est présentée. Elle doit avoir la valeur `true` pour afficher la page maître et la valeur `false` pour afficher la page de détail.

Les captures d’écran suivantes montrent la page de détail `ContactPage`, qui s’affiche une fois qu’elle a été sélectionnée dans la page maître :

![](master-detail-page-images/detailpage.png "Exemple de page de détail")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Contrôle du comportement d’affichage de la page de détail

La manière dont [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gère les pages maîtres et de détail dépend de l’application qui s’exécute sur un téléphone ou une tablette, de l’orientation de l’appareil et de la valeur de la propriété [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior). Cette propriété détermine le mode d’affichage de la page de détail. Valeurs possibles :

- **Default** - Les pages sont affichées à l’aide de la plateforme par défaut.
- **Popover** - La page de détail recouvre complètement ou partiellement la page maître.
- **Split** - La page maître est affichée à gauche, et la page de détail à droite.
- **SplitOnLandscape** - Un écran partagé est utilisé quand l’appareil est en orientation paysage.
- **SplitOnPortrait** - Un écran partagé est utilisé quand l’appareil est en orientation portrait.

L’exemple de code XAML suivant montre comment définir la propriété [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) sur [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

L’exemple de code suivant montre le [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) équivalent créé en C# :

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

Toutefois, la valeur de la propriété [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) affecte uniquement les applications qui s’exécutent sur des tablettes ou des postes de travail. Les applications qui s’exécutent sur des téléphones ont toujours le comportement *Popover*.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) et comment naviguer entre ses pages d’informations. `MasterDetailPage` de Xamarin.Forms est une page qui gère deux pages d’informations liées : une page maître présentant des éléments et une page de détail présentant les détails relatifs aux éléments de la page maître.

## <a name="related-links"></a>Liens associés

- [Variétés de page](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
