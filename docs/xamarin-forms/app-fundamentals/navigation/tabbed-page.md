---
title: Xamarin.FormsTabbedPage
description: Le Xamarin.Forms TabbedPage se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails. Cet article montre comment utiliser un TabbedPage pour parcourir une collection de pages.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38389867ba52e63d8310e3b59d7838f58e8cf488
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137512"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.FormsTabbedPage

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Le Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails. Les captures d’écran suivantes montrent une `TabbedPage` sur iOS et Android :

[![Capture d’écran d’un TabbedPage contenant trois onglets, sur iOS et Android](tabbed-page-images/tabbedpage-today.png "TabbedPage avec trois onglets")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage avec trois onglets")

Sur iOS, la liste des onglets apparaît au bas de l’écran et la zone de détails apparaît au-dessus. Chaque onglet est constitué d’un titre et d’une icône, qui doit être un fichier PNG avec un canal alpha. En orientation portrait, les icônes de la barre d’onglets apparaissent au-dessus des titres des onglets. Dans l’orientation paysage, les icônes et les titres s’affichent côte à côte. En outre, une barre d’onglets standard ou compacte peut s’afficher, en fonction de l’appareil et de l’orientation. S’il y a plus de cinq onglets, un onglet **Plus** s’affiche et permet d’accéder aux onglets supplémentaires. Pour plus d’informations sur les conditions requises pour les icônes, consultez taille de l' [icône de barre d’onglets](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) sur Developer.Apple.com.

> [!TIP]
> Le `TabbedRenderer` pour iOS a une méthode substituable `GetIcon` qui peut être utilisée pour charger les icônes de tabulation à partir d’une source spécifiée. Cette substitution permet d’utiliser des images SVG en tant qu’icônes sur un `TabbedPage`. Vous pouvez aussi fournir des versions sélectionnées et désélectionnées d’une icône.

Sur Android, la liste des onglets apparaît en haut de l’écran et la zone de détails est affichée en dessous. Chaque onglet est constitué d’un titre et d’une icône, qui doit être un fichier PNG avec un canal alpha. Toutefois, les onglets peuvent être déplacés vers le bas de l’écran avec un spécifique à la plateforme. S’il y a plus de cinq onglets et que la liste d’onglets se trouve au bas de l’écran, un onglet *plus* s’affiche et peut être utilisé pour accéder aux onglets supplémentaires. Pour plus d’informations sur les exigences relatives aux icônes, consultez les [onglets](https://material.io/components/tabs/#) sur Material.IO et la [prise en charge de différentes densités de pixels](https://developer.android.com/training/multiscreen/screendensities) sur Developer.Android.com. Pour plus d’informations sur le déplacement des onglets vers le bas de l’écran, consultez Définition de la couleur et de l’emplacement de la [barre d’outils TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> Le `TabbedPageRenderer` pour Android AppCompat a une méthode substituable `GetIconDrawable` qui peut être utilisée pour charger des icônes d’onglet à partir d’un personnalisé `Drawable` . Cette substitution rend possible l’utilisation d’images SVG comme icônes sur un `TabbedPage`, et fonctionne à la fois avec les barres d’onglets supérieures et inférieures. En guise d’alternative, vous pouvez utiliser la méthode `SetTabIcon` substituable pour charger des icônes d’onglets à partir d’un `Drawable` personnalisé pour les barres d’onglets supérieures.

Sur la plateforme Windows universelle (UWP), la liste des onglets s’affiche en haut de l’écran et la zone de détails est indiquée ci-dessous. Chaque onglet est constitué d’un titre. Toutefois, les icônes peuvent être ajoutées à chaque onglet avec un spécifique à la plateforme. Pour plus d’informations, consultez [TabbedPage icons on Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Créer un TabbedPage

Vous pouvez utiliser deux approches pour créer un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

- Remplir [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) avec une collection d’objets enfants [`Page`](xref:Xamarin.Forms.Page) , tels qu’une collection d' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets. Pour plus d’informations, consultez [remplir un TabbedPage avec une collection de pages](#populate-a-tabbedpage-with-a-page-collection).
- Affecter une collection à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) et affecter un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) afin de retourner des pages pour les objets dans la collection. Pour plus d’informations, consultez [remplir un TabbedPage avec un modèle](#populate-a-tabbedpage-with-a-template).

Avec les deux approches, le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) affiche chaque page à mesure que l’utilisateur sélectionne chaque onglet.

> [!IMPORTANT]
> Il est recommandé de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) remplir [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) uniquement les [`ContentPage`](xref:Xamarin.Forms.ContentPage) instances et. Cela vous aidera à garantir une expérience utilisateur cohérente sur toutes les plateformes.

En outre, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) définit les propriétés suivantes :

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), de type [`Color`](xref:Xamarin.Forms.Color) , la couleur d’arrière-plan de la barre d’onglets.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), de type [`Color`](xref:Xamarin.Forms.Color) , la couleur du texte dans la barre d’onglets.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), de type [`Color`](xref:Xamarin.Forms.Color) , la couleur de l’onglet lorsqu’il est sélectionné.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), de type [`Color`](xref:Xamarin.Forms.Color) , la couleur de l’onglet lorsqu’il est désélectionné.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être stylisées et que les propriétés peuvent être les cibles des liaisons de données.

> [!WARNING]
> Dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , chaque [`Page`](xref:Xamarin.Forms.Page) objet est créé lors de la `TabbedPage` construction de. Cela peut entraîner une mauvaise expérience utilisateur, en particulier si `TabbedPage` est la page racine de l’application. Toutefois, Xamarin.Forms l’interpréteur de commandes permet de créer des pages accessibles par le biais d’une barre d’onglets à la demande, en réponse à la navigation. Pour plus d’informations, consultez [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Remplir un TabbedPage avec une collection de pages

Un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être rempli avec une collection d' [`Page`](xref:Xamarin.Forms.Page) objets enfants, tels qu’une collection d' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets. Pour ce faire, il suffit d’ajouter les `Page` objets à la [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) collection. Cela s’effectue en XAML comme suit :

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
> La [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) propriété de la [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) classe, à partir de laquelle [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) dérive, est le `ContentProperty` de `MultiPage<T>` . Par conséquent, dans XAML, il n’est pas nécessaire d’assigner explicitement les [`Page`](xref:Xamarin.Forms.Page) objets à la `Children` propriété.

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

Dans cet exemple, le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) est rempli avec deux [`Page`](xref:Xamarin.Forms.ContentPage) objets. Le premier enfant est un [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet, et le deuxième enfant est un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qui contient un `ContentPage` objet.

Les captures d’écran suivantes montrent un [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

[![Capture d’écran d’un TabbedPage contenant trois onglets, sur iOS et Android](tabbed-page-images/tabbedpage-today.png "TabbedPage avec trois onglets")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage avec trois onglets")

Si vous sélectionnez un autre onglet [`ContentPage`](xref:Xamarin.Forms.ContentPage) , l’objet qui représente l’onglet s’affiche :

[![Capture d’écran d’un TabbedPage contenant des onglets, sur iOS et Android](tabbed-page-images/tabbedpage-week.png "TabbedPage avec onglets")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage avec onglets")

Sous l’onglet **calendrier** , l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet est encapsulé dans un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) objet.

> [!WARNING]
> Alors qu’un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) peut être placé dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , il n’est pas recommandé de placer un `TabbedPage` dans un `NavigationPage` . En effet, sur iOS, un `UITabBarController` agit toujours en tant que wrapper pour le `UINavigationController`. Pour plus d’informations, consultez [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfaces de contrôleurs de vue combinés) dans la bibliothèque du développeur iOS.

## <a name="navigate-within-a-tab"></a>Naviguer dans un onglet

La navigation peut être effectuée dans un onglet, à condition que l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet soit encapsulé dans un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) objet. Pour ce faire, appelez la [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) méthode sur la [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet :

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

La page cible de la navigation est spécifiée comme argument de la [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) méthode. Dans cet exemple, la `UpcomingAppointmentsPage` page fait l’objet d’un push dans la pile de navigation, où elle devient la page active :

[![Capture d’écran de navigation au sein d’un onglet, sur iOS et Android](tabbed-page-images/tabbedpage-upcoming.png "Navigation TabbedPage dans un onglet")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navigation TabbedPage dans un onglet")

Pour plus d’informations sur l’exécution de la navigation à l’aide de la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe, consultez [navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Remplir un TabbedPage à l’aide d’un modèle

Une [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être remplie avec des pages en assignant une collection de données à la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété et en assignant un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété qui modèle les données sous forme d' [`Page`](xref:Xamarin.Forms.Page) objets. Cela s’effectue en XAML comme suit :

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

Dans cet exemple, chaque onglet se compose d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet qui utilise les [`Image`](xref:Xamarin.Forms.Image) [`Label`](xref:Xamarin.Forms.Label) objets et pour afficher les données de l’onglet :

[![Capture d’écran d’un TabbedPage basé sur un modèle, sur iOS et Android](tabbed-page-images/tabbedpage-template.png "TabbedPage basé sur un modèle")](tabbed-page-images/tabbedpage-template-large.png#lightbox "TabbedPage basé sur un modèle")

En sélectionnant un autre onglet, vous affichez l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet qui représente l’onglet.

## <a name="related-links"></a>Liens connexes

- [TabbedPageWithNavigationPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Types de pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [API TabbedPage](xref:Xamarin.Forms.TabbedPage)
