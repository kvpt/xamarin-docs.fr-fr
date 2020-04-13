---
title: Xamarin.Forms TabbedPage
description: Le TabbedPage de Xamarin.Forms se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails. Cet article montre comment utiliser un TabbedPage pour parcourir une collection de pages.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 986045a4be352da0e439de87fdc70e2958b48d36
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305086"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.Forms TabbedPage

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Le Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se compose d’une liste d’onglets et d’une zone de détail plus grande, avec chaque onglet de chargement du contenu dans la zone de détail. Les captures d’écran suivantes montrent un `TabbedPage` sur iOS et Android:

[![Capture d’écran d’un TabbedPage contenant trois onglets, sur iOS et Android](tabbed-page-images/tabbedpage-today.png "TabbedPage avec trois onglets")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage avec trois onglets")

Sur iOS, la liste des onglets apparaît au bas de l’écran et la zone de détails apparaît au-dessus. Chaque onglet se compose d’un titre et d’une icône, qui doit être un fichier PNG avec un canal alpha. Dans l’orientation du portrait, les icônes de barre d’onglet apparaissent au-dessus des titres d’onglet. Dans l’orientation paysagère, les icônes et les titres apparaissent côte à côte. En outre, une barre d’onglet régulière ou compacte peut être affichée, selon l’appareil et l’orientation. S’il y a plus de cinq onglets, un onglet **Plus** s’affiche et permet d’accéder aux onglets supplémentaires. Pour plus d’informations sur les exigences de l’icône, voir [Tab Bar Icon Size](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) sur developer.apple.com.

> [!TIP]
> Le `TabbedRenderer` pour iOS a `GetIcon` une méthode primordiale qui peut être utilisé pour charger des icônes d’onglet à partir d’une source spécifiée. Cette substitution permet d’utiliser des images SVG en tant qu’icônes sur un `TabbedPage`. Vous pouvez aussi fournir des versions sélectionnées et désélectionnées d’une icône.

Sur Android, la liste des onglets apparaît en haut de l’écran, et la zone de détail est ci-dessous. Chaque onglet se compose d’un titre et d’une icône, qui doit être un fichier PNG avec un canal alpha. Cependant, les onglets peuvent être déplacés vers le bas de l’écran avec une plate-forme spécifique. S’il y a plus de cinq onglets, et que la liste d’onglets se trouve au bas de l’écran, un onglet *Plus* apparaîtra qui peut être utilisé pour accéder aux onglets supplémentaires. Pour plus d’informations sur les exigences de l’icône, voir [Onglets](https://material.io/components/tabs/#) sur material.io et [prendre en charge différentes densités de pixels](https://developer.android.com/training/multiscreen/screendensities) sur developer.android.com. Pour plus d’informations sur le déplacement des onglets vers le bas de l’écran, voir [Réglage TabbedPage Toolbar Placement et couleur](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> Le `TabbedPageRenderer` pour Android AppCompat a `GetIconDrawable` une méthode primordiale qui peut être `Drawable`utilisé pour charger des icônes onglet à partir d’une coutume . Cette substitution rend possible l’utilisation d’images SVG comme icônes sur un `TabbedPage`, et fonctionne à la fois avec les barres d’onglets supérieures et inférieures. En guise d’alternative, vous pouvez utiliser la méthode `SetTabIcon` substituable pour charger des icônes d’onglets à partir d’un `Drawable` personnalisé pour les barres d’onglets supérieures.

Sur la plate-forme Windows Universelle (UWP), la liste des onglets apparaît en haut de l’écran, et la zone de détails est ci-dessous. Chaque onglet se compose d’un titre. Cependant, des icônes peuvent être ajoutées à chaque onglet avec une plate-forme spécifique. Pour plus d’informations, voir [Icônes TabbedPage sur Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Créer un TabbedPage

Deux approches peuvent être [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)utilisées pour créer un :

- Peuplez [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) le avec une [`Page`](xref:Xamarin.Forms.Page) collection d’objets [`ContentPage`](xref:Xamarin.Forms.ContentPage) pour enfants, comme une collection d’objets. Pour plus d’informations, voir [Populate a TabbedPage avec une collection de pages](#populate-a-tabbedpage-with-a-page-collection).
- Affecter une collection à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) et affecter un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) afin de retourner des pages pour les objets dans la collection. Pour plus d’informations, voir [Populate a TabbedPage avec un modèle](#populate-a-tabbedpage-with-a-template).

Avec les deux [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) approches, l’affichera chaque page au fur et à mesure que l’utilisateur sélectionnera chaque onglet.

> [!IMPORTANT]
> Il est recommandé [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) qu’un devrait [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) [`ContentPage`](xref:Xamarin.Forms.ContentPage) être peuplé avec et les instances seulement. Cela vous aidera à garantir une expérience utilisateur cohérente sur toutes les plateformes.

En outre, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) définit les propriétés suivantes :

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), de [`Color`](xref:Xamarin.Forms.Color)type , la couleur de fond de la barre d’onglet.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), de [`Color`](xref:Xamarin.Forms.Color)type , la couleur du texte sur la barre d’onglet.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), de [`Color`](xref:Xamarin.Forms.Color)type , la couleur de l’onglet quand il est sélectionné.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), de [`Color`](xref:Xamarin.Forms.Color)type , la couleur de l’onglet quand il n’est pas sélectionné.

Toutes ces propriétés [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) sont sauvegardées par des objets, ce qui signifie qu’elles peuvent être stylées, et les propriétés peuvent être la cible de liaisons de données.

> [!WARNING]
> Dans [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)un [`Page`](xref:Xamarin.Forms.Page) , chaque objet `TabbedPage` est créé lorsque l’est construit. Cela peut conduire à une mauvaise `TabbedPage` expérience utilisateur, en particulier si la page racine de l’application. Cependant, Xamarin.Forms Shell permet de créer des pages accessibles via une barre d’onglets à la demande, en réponse à la navigation. Pour plus d’informations, voir [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Peuplez un TabbedPage avec une collection Page

A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être peuplé d’une collection d’objets pour enfants, [`Page`](xref:Xamarin.Forms.Page) comme une collection d’objets. [`ContentPage`](xref:Xamarin.Forms.ContentPage) Ceci est réalisé `Page` en ajoutant [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) les objets à la collection. Ceci est accompli dans XAML comme suit :

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
> La [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) propriété [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) de la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) classe, dont `ContentProperty` provient-on, est la de `MultiPage<T>`. Par conséquent, dans XAML, il n’est pas nécessaire d’attribuer explicitement les [`Page`](xref:Xamarin.Forms.Page) objets à la `Children` propriété.

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

Dans cet exemple, le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) est [`Page`](xref:Xamarin.Forms.ContentPage) peuplé de deux objets. Le premier enfant [`ContentPage`](xref:Xamarin.Forms.ContentPage) est un objet, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et `ContentPage` le deuxième enfant est un objet contenant.

Les captures d’écran suivantes montrent un [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

[![Capture d’écran d’un TabbedPage contenant trois onglets, sur iOS et Android](tabbed-page-images/tabbedpage-today.png "TabbedPage avec trois onglets")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage avec trois onglets")

La sélection d’un autre onglet affiche l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui représente l’onglet :

[![Capture d’écran d’un TabbedPage contenant des onglets, sur iOS et Android](tabbed-page-images/tabbedpage-week.png "TabbedPage avec des onglets")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage avec des onglets")

Sur l’onglet [`ContentPage`](xref:Xamarin.Forms.ContentPage) **Annexe,** l’objet est enveloppé dans un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) objet.

> [!WARNING]
> Alors [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qu’un peut [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)être placé dans un `TabbedPage` , `NavigationPage`il n’est pas recommandé de placer un dans un . En effet, sur iOS, un `UITabBarController` agit toujours en tant que wrapper pour le `UINavigationController`. Pour plus d’informations, consultez [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfaces de contrôleurs de vue combinés) dans la bibliothèque du développeur iOS.

## <a name="navigate-within-a-tab"></a>Naviguez dans un onglet

La navigation peut être effectuée dans [`ContentPage`](xref:Xamarin.Forms.ContentPage) un onglet, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) à condition que l’objet soit enveloppé dans un objet. Ceci est accompli en [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) invoquant [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) la méthode [`ContentPage`](xref:Xamarin.Forms.ContentPage) sur la propriété de l’objet :

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

La page à naviguer est spécifiée [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) comme argument à la méthode. Dans cet exemple, la `UpcomingAppointmentsPage` page est poussée sur la pile de navigation, où elle devient la page active :

[![Capture d’écran de la navigation dans un onglet, sur iOS et Android](tabbed-page-images/tabbedpage-upcoming.png "Navigation TabbedPage dans un onglet")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navigation TabbedPage dans un onglet")

Pour plus d’informations [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) sur la navigation d’exécution en utilisant la classe, voir [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Peuplez un TabbedPage avec un modèle

A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être peuplé de pages en assignant [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) une collection de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) données [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) à la propriété, [`Page`](xref:Xamarin.Forms.Page) et en assignant un à la propriété qui modèle les données comme des objets. Ceci est accompli dans XAML comme suit :

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

Dans cet exemple, chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) onglet se [`Image`](xref:Xamarin.Forms.Image) [`Label`](xref:Xamarin.Forms.Label) compose d’un objet qui utilise et des objets pour afficher des données pour l’onglet :

[![Capture d’écran d’un TabbedPage modélé, sur iOS et Android](tabbed-page-images/tabbedpage-template.png "Templated TabbedPage")](tabbed-page-images/tabbedpage-template-large.png#lightbox "Templated TabbedPage")

La sélection d’un autre onglet affiche l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui représente l’onglet.

## <a name="related-links"></a>Liens connexes

- [TabbedPageWithNavigationPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Types de pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPage API](xref:Xamarin.Forms.TabbedPage)
