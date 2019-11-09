---
title: Xamarin. Forms TabbedPage
description: Le TabbedPage de Xamarin.Forms se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails. Cet article montre comment utiliser un TabbedPage pour parcourir une collection de pages.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 22c5b5b6479ce65c2e6b69f6ad5a98fd11ae47d7
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842913"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin. Forms TabbedPage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) de Xamarin.Forms se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails. Les captures d’écran suivantes montrent un `TabbedPage` sur iOS et Android :

[![Capture d’écran d’un TabbedPage contenant trois onglets, sur iOS et Android](tabbed-page-images/tabbedpage-today.png "TabbedPage avec trois onglets")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage avec trois onglets")

Sur iOS, la liste des onglets apparaît au bas de l’écran et la zone de détails apparaît au-dessus. Chaque onglet est constitué d’un titre et d’une icône, qui doit être un fichier PNG avec un canal alpha. En orientation portrait, les icônes de la barre d’onglets apparaissent au-dessus des titres des onglets. Dans l’orientation paysage, les icônes et les titres s’affichent côte à côte. En outre, une barre d’onglets standard ou compacte peut s’afficher, en fonction de l’appareil et de l’orientation. S’il y a plus de cinq onglets, un onglet **Plus** s’affiche et permet d’accéder aux onglets supplémentaires. Pour plus d’informations sur les conditions requises pour les icônes, consultez taille de l' [icône de barre d’onglets](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) sur Developer.Apple.com.

> [!TIP]
> La `TabbedRenderer` pour iOS a une méthode substituable `GetIcon` qui peut être utilisée pour charger les icônes de tabulation à partir d’une source spécifiée. Cette substitution permet d’utiliser des images SVG en tant qu’icônes sur un `TabbedPage`. Vous pouvez aussi fournir des versions sélectionnées et désélectionnées d’une icône.

Sur Android, la liste des onglets apparaît en haut de l’écran et la zone de détails est affichée en dessous. Chaque onglet est constitué d’un titre et d’une icône, qui doit être un fichier PNG avec un canal alpha. Toutefois, les onglets peuvent être déplacés vers le bas de l’écran avec un spécifique à la plateforme. Pour plus d’informations sur les exigences relatives aux icônes, consultez les [onglets](https://material.io/components/tabs/#) sur Material.IO et la [prise en charge de différentes densités de pixels](https://developer.android.com/training/multiscreen/screendensities) sur Developer.Android.com. Pour plus d’informations sur le déplacement des onglets vers le bas de l’écran, consultez Définition de la couleur et de l’emplacement de la [barre d’outils TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> La `TabbedPageRenderer` pour Android AppCompat a une méthode de `GetIconDrawable` substituable qui peut être utilisée pour charger des icônes de tabulation à partir d’un `Drawable`personnalisé. Cette substitution rend possible l’utilisation d’images SVG comme icônes sur un `TabbedPage`, et fonctionne à la fois avec les barres d’onglets supérieures et inférieures. En guise d’alternative, vous pouvez utiliser la méthode `SetTabIcon` substituable pour charger des icônes d’onglets à partir d’un `Drawable` personnalisé pour les barres d’onglets supérieures.

Sur la plateforme Windows universelle (UWP), la liste des onglets s’affiche en haut de l’écran et la zone de détails est indiquée ci-dessous. Chaque onglet est constitué d’un titre. Toutefois, les icônes peuvent être ajoutées à chaque onglet avec un spécifique à la plateforme. Pour plus d’informations, consultez [TabbedPage icons on Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Créer un TabbedPage

Deux approches peuvent être adoptées pour créer un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

- Remplissez le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) avec une collection d’objets [`Page`](xref:Xamarin.Forms.Page) enfants, tels qu’une collection d’objets [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Pour plus d’informations, consultez [remplir un TabbedPage avec une collection de pages](#populate-a-tabbedpage-with-a-page-collection).
- Assignez une collection à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) et assignez un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) pour retourner des pages pour les objets de la collection. Pour plus d’informations, consultez [remplir un TabbedPage avec un modèle](#populate-a-tabbedpage-with-a-template).

Avec les deux approches, le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) affiche chaque page quand l’utilisateur sélectionne chaque onglet.

> [!IMPORTANT]
> Nous vous recommandons de remplir un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) uniquement avec des instances de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Cela permet de garantir une expérience utilisateur cohérente sur toutes les plateformes.

En outre, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) définit les propriétés suivantes :

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), de type [`Color`](xref:Xamarin.Forms.Color), la couleur d’arrière-plan de la barre d’onglets.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), de type [`Color`](xref:Xamarin.Forms.Color), la couleur du texte dans la barre d’onglets.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), de type [`Color`](xref:Xamarin.Forms.Color), la couleur de l’onglet lorsqu’il est sélectionné.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), de type [`Color`](xref:Xamarin.Forms.Color), la couleur de l’onglet lorsqu’il est désélectionné.

Toutes ces propriétés s’appuient sur des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être personnalisées et être des cibles de liaisons de données.

> [!WARNING]
> Dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), chaque objet [`Page`](xref:Xamarin.Forms.Page) est créé lors de la construction de l' `TabbedPage`. Cela peut entraîner une mauvaise expérience utilisateur, en particulier si la `TabbedPage` est la page racine de l’application. Toutefois, le shell Xamarin. Forms permet de créer des pages accessibles par le biais d’une barre d’onglets à la demande, en réponse à la navigation. Pour plus d’informations, consultez [Interpréteur de commandes Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Remplir un TabbedPage avec une collection de pages

Une [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être remplie avec une collection d’objets [`Page`](xref:Xamarin.Forms.Page) enfants, tels qu’une collection d’objets [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Pour ce faire, vous devez ajouter les objets `Page` à la collection [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) . Cela s’effectue en XAML comme suit :

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" IconImageSource="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

> [!NOTE]
> La propriété [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) de la classe [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) , à partir de laquelle dérive [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , est la `ContentProperty` de `MultiPage<T>`. Par conséquent, dans XAML, il n’est pas nécessaire d’assigner explicitement les objets [`Page`](xref:Xamarin.Forms.Page) à la propriété `Children`.

Le code C# équivalent est :

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    NavigationPage navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

Dans cet exemple, le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) est rempli avec deux objets [`Page`](xref:Xamarin.Forms.ContentPage) . Le premier enfant est un objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) , et le deuxième enfant est un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) contenant un objet `ContentPage`.

Les captures d’écran suivantes montrent un objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

[![Capture d’écran d’un TabbedPage contenant trois onglets, sur iOS et Android](tabbed-page-images/tabbedpage-today.png "TabbedPage avec trois onglets")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage avec trois onglets")

En sélectionnant un autre onglet, vous affichez l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui représente l’onglet :

[![Capture d’écran d’un TabbedPage contenant des onglets, sur iOS et Android](tabbed-page-images/tabbedpage-week.png "TabbedPage avec onglets")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage avec onglets")

Sous l’onglet **calendrier** , l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) est encapsulé dans un objet [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) .

> [!WARNING]
> Bien qu’un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) puisse être placé dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), il n’est pas recommandé de placer un `TabbedPage` dans un `NavigationPage`. En effet, sur iOS, un `UITabBarController` agit toujours en tant que wrapper pour le `UINavigationController`. Pour plus d’informations, consultez [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfaces de contrôleurs de vue combinés) dans la bibliothèque du développeur iOS.

## <a name="navigate-within-a-tab"></a>Naviguer dans un onglet

La navigation peut être effectuée dans un onglet, à condition que l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) soit encapsulé dans un objet [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Pour ce faire, appelez la méthode [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) sur la propriété [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

La page cible de la navigation est spécifiée comme argument de la méthode [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) . Dans cet exemple, la page `UpcomingAppointmentsPage` fait l’objet d’un push dans la pile de navigation, où elle devient la page active :

[![Capture d’écran de navigation au sein d’un onglet, sur iOS et Android](tabbed-page-images/tabbedpage-upcoming.png "Navigation TabbedPage dans un onglet")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navigation TabbedPage dans un onglet")

Pour plus d’informations sur l’exécution de la navigation à l’aide de la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Remplir un TabbedPage à l’aide d’un modèle

Une [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être remplie avec des pages en assignant une collection de données à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) , et en assignant un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) qui modèle les données en tant qu’objets [`Page`](xref:Xamarin.Forms.Page) . Cela s’effectue en XAML comme suit :

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage"
            ItemsSource="{x:Static local:MonkeyDataModel.All}">            
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" IconImageSource="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

Le code C# équivalent est :

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() =>
    {
      var nameLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage
      {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children =
          {
            nameLabel,
            image,
            new StackLayout
            {
              Padding = new Thickness (50, 10),
              Children =
              {
                new StackLayout
                {
                  Orientation = StackOrientation.Horizontal,
                  Children =
                  {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                // ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

Dans cet exemple, chaque onglet se compose d’un objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui utilise des objets [`Image`](xref:Xamarin.Forms.Image) et [`Label`](xref:Xamarin.Forms.Label) pour afficher les données de l’onglet :

[![Capture d’écran d’un TabbedPage basé sur un modèle, sur iOS et Android](tabbed-page-images/tabbedpage-template.png "TabbedPage basé sur un modèle")](tabbed-page-images/tabbedpage-template-large.png#lightbox "TabbedPage basé sur un modèle")

En sélectionnant un autre onglet, vous affichez l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui représente l’onglet.

## <a name="related-links"></a>Liens connexes

- [TabbedPageWithNavigationPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Variétés de page](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [API TabbedPage](xref:Xamarin.Forms.TabbedPage)
