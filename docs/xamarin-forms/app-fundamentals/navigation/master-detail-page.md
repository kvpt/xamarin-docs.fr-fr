---
title: Xamarin.FormsPage maître-détail
description: 'Le Xamarin.Forms MasterDetailPage est une page qui gère deux pages d’informations associées : une page maître qui présente des éléments et une page de détails qui présente des détails sur les éléments de la page maître. Cet article explique comment utiliser MasterDetailPage et comment naviguer entre ses pages d’informations.'
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 89b1dedcb6219a32332641550018405abd4b6870
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137811"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.FormsPage maître-détail

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_Le Xamarin.Forms MasterDetailPage est une page qui gère deux pages d’informations associées : une page maître qui présente des éléments et une page de détails qui présente des détails sur les éléments de la page maître. Cet article explique comment utiliser un MasterDetailPage et naviguer entre ses pages d’informations._

## <a name="overview"></a>Vue d'ensemble

Une page maître affiche généralement une liste d’éléments, comme indiqué dans les captures d’écran suivantes :

[![](master-detail-page-images/masterpage-components.png "Master Page Components")](master-detail-page-images/masterpage-components-large.png#lightbox "Master Page Components")

L’emplacement de la liste d’éléments est identique sur chaque plateforme. La sélection de l’un des éléments permet d’accéder à la page de détail correspondante. De plus, la page maître comporte également une barre de navigation qui contient un bouton permettant d’accéder à la page de détail active :

- Sur iOS, la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail. De plus, vous pouvez accéder à la page de détail active en faisant glisser la page maître vers la gauche.
- Sur Android, la barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton qui permettent d’accéder à la page de détail. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android. De plus, vous pouvez accéder à la page de détail active en faisant glisser la page maître vers la gauche, en appuyant sur la page de détail située à l’extrême droite de l’écran, puis en appuyant sur le bouton *Précédent* au bas de l’écran.
- Sur UWP (plateforme Windows universelle), la barre de navigation est présente en haut de la page et comporte un bouton qui permet d’accéder à la page de détail.

Une page de détail affiche les données qui correspondent à l’élément sélectionné dans la page maître. Les principaux composants de la page de détail sont illustrés dans les captures d’écran suivantes :

![](master-detail-page-images/detailpage-components.png "Detail Page Components")

La page de détail contient une barre de navigation dont le contenu dépend de la plateforme :

- Sur iOS, la barre de navigation est présente en haut de la page et affiche un titre, et possède un bouton qui retourne à la page maître, à condition que l’instance de page de détails soit incluse dans un wrapper dans l' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance de. De plus, vous pouvez retourner à la page maître en faisant glisser la page de détail vers la droite.
- Sur Android, une barre de navigation est présente en haut de la page. Elle affiche un titre et une icône ainsi qu’un bouton qui permet de retourner à la page maître. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android.
- Sur UWP, la barre de navigation est présente en haut de la page. Elle affiche un titre ainsi qu’un bouton qui permet de retourner à la page maître.

### <a name="navigation-behavior"></a>Comportement de la navigation

Le comportement de l’expérience de navigation entre les pages maîtres et les pages de détail dépend de la plateforme :

- Sur iOS, la page de détail *glisse* vers la droite tandis que la page maître glisse à partir de la gauche. La partie gauche de la page de détail est toujours visible.
- Sur Android, les pages de détail et les pages maîtres se *superposent*.
- Sur UWP, la page maître glisse à gauche de la page de détails, à condition que la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriété ait la valeur `Popover` . Pour plus d’informations, consultez [Contrôle du comportement d’affichage de la page de détail](#Controlling_the_Detail_Page_Display_Behavior).

Un comportement similaire se produit en mode paysage, à la différence que la page maître sur iOS et Android a une largeur similaire à celle de la page maître en mode portrait. Ainsi, une plus grande partie de la page de détail est visible.

Pour plus d’informations sur le contrôle du comportement de la navigation, consultez [Contrôle du comportement d’affichage de la page de détail](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Création d’un MasterDetailPage

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)Contient [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) les propriétés et qui sont toutes deux de type [`Page`](xref:Xamarin.Forms.Page) , qui sont utilisées pour obtenir et définir les pages maître et de détails, respectivement.

> [!IMPORTANT]
> Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) est conçu pour être une page racine et son utilisation comme page enfant dans d’autres types de page peut entraîner un comportement inattendu et incohérent. En outre, il est recommandé que la page maître d’un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) doit toujours être une [`ContentPage`](xref:Xamarin.Forms.ContentPage) instance et que la page de détails ne soit remplie qu’avec [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) les [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instances, et `ContentPage` . Cela vous aidera à garantir une expérience utilisateur cohérente sur toutes les plateformes.

L’exemple de code XAML suivant montre un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) qui définit [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) les [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) Propriétés et :

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

L’exemple de code suivant montre l’équivalent [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) créé en C# :

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

La [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriété est définie sur une [`ContentPage`](xref:Xamarin.Forms.ContentPage) instance. La [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriété est définie sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qui contient une `ContentPage` instance.

### <a name="creating-the-master-page"></a>Création de la page maître

L’exemple de code XAML suivant illustre la déclaration de l' `MasterPage` objet, qui est référencé par le biais de la [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriété :

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

La page se compose d’un [`ListView`](xref:Xamarin.Forms.ListView) qui est rempli avec les données en XAML en affectant [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) à sa propriété un tableau d' `MasterPageItem` instances. Chaque `MasterPageItem` définit les propriétés `Title`, `IconSource` et `TargetType`.

Une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est assignée à la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriété pour afficher chacune `MasterPageItem` . Le `DataTemplate` contient un [`ViewCell`](xref:Xamarin.Forms.ViewCell) qui se compose d’un [`Image`](xref:Xamarin.Forms.Image) et d’un [`Label`](xref:Xamarin.Forms.Label) . Le [`Image`](xref:Xamarin.Forms.Image) affiche la `IconSource` valeur de la propriété et [`Label`](xref:Xamarin.Forms.Label) affiche la `Title` valeur de la propriété pour chaque `MasterPageItem` .

Les [`Title`](xref:Xamarin.Forms.Page.Title) Propriétés et de la page sont [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) définies. L’icône s’affiche dans la page de détail, à condition que celle-ci comporte une barre de titre. Vous devez activer cette option sur iOS en encapsulant l’instance de page de détails dans une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance.

> [!NOTE]
> La [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriété doit être définie pour la page [`Title`](xref:Xamarin.Forms.Page.Title) , sinon une exception se produit.

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

L' `MasterPage` instance contient une `ListView` propriété qui expose son [`ListView`](xref:Xamarin.Forms.ListView) instance afin que l' `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) instance puisse inscrire un gestionnaire d’événements pour gérer l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement. Cela permet `MainPage` à l’instance de définir la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriété sur la page qui représente l' `ListView` élément sélectionné. L’exemple de code suivant illustre le gestionnaire d’événements :

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

- Elle récupère le [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) de l' [`ListView`](xref:Xamarin.Forms.ListView) instance de et à condition que ce ne soit pas le cas `null` , définit la page de détails sur une nouvelle instance du type de page stocké dans la `TargetType` propriété de `MasterPageItem` . Le type de page est encapsulé dans une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance pour garantir que l’icône référencée via la [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) propriété sur le `MasterPage` s’affiche sur la page de détails dans iOS.
- L’élément sélectionné dans la [`ListView`](xref:Xamarin.Forms.ListView) est défini sur `null` pour s’assurer qu’aucun des `ListView` éléments ne sera sélectionné la prochaine fois que le `MasterPage` sera présenté.
- La page de détails est présentée à l’utilisateur en affectant à la propriété la valeur [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) `false` . Cette propriété détermine si la page maître ou la page de détail est présentée. Elle doit avoir la valeur `true` pour afficher la page maître et la valeur `false` pour afficher la page de détail.

Les captures d’écran suivantes montrent la page de détail `ContactPage`, qui s’affiche une fois qu’elle a été sélectionnée dans la page maître :

![](master-detail-page-images/detailpage.png "Detail Page Example")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Contrôle du comportement d’affichage de la page de détail

La manière dont [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gère les pages maître et détails varie selon que l’application s’exécute sur un téléphone ou une tablette, l’orientation de l’appareil et la valeur de la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriété. Cette propriété détermine le mode d’affichage de la page de détail. Valeurs possibles :

- **Default** - Les pages sont affichées à l’aide de la plateforme par défaut.
- **Popover** - La page de détail recouvre complètement ou partiellement la page maître.
- **Split** - La page maître est affichée à gauche, et la page de détail à droite.
- **SplitOnLandscape** - Un écran partagé est utilisé quand l’appareil est en orientation paysage.
- **SplitOnPortrait** - Un écran partagé est utilisé quand l’appareil est en orientation portrait.

L’exemple de code XAML suivant montre comment définir la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriété sur un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

L’exemple de code suivant montre l’équivalent [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) créé en C# :

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

Toutefois, la valeur de la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriété affecte uniquement les applications qui s’exécutent sur les tablettes ou le bureau. Les applications qui s’exécutent sur des téléphones ont toujours le comportement *Popover*.

## <a name="summary"></a>Résumé

Cet article a montré comment utiliser un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) et naviguer entre ses pages d’informations. Xamarin.Forms `MasterDetailPage` Est une page qui gère deux pages d’informations associées : une page maître qui présente des éléments et une page de détails qui présente des détails sur les éléments de la page maître.

## <a name="related-links"></a>Liens connexes

- [Types de pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
