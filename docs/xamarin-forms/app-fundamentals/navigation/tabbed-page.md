---
title: Page à onglets Xamarin.Forms
description: Le TabbedPage de Xamarin.Forms se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails. Cet article montre comment utiliser un TabbedPage pour parcourir une collection de pages.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f8f2fc489c10d404b5864ea13c438e3e5508eabe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771358"
---
# <a name="xamarinforms-tabbed-page"></a>Page à onglets Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

_Le TabbedPage de Xamarin.Forms se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails. Cet article montre comment utiliser un TabbedPage pour parcourir une collection de pages._

## <a name="overview"></a>Présentation

Les captures d’écran suivantes montrent un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) sur chaque plateforme :

![](tabbed-page-images/tab1.png "Exemple de TabbedPage")

Les captures d’écran suivantes montrent le format de l’onglet sur chaque plateforme :

![](tabbed-page-images/tabbedpage-components.png "Composants d’onglets TabbedPage")

La disposition d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), et ses onglets, dépend de la plateforme :

- Sur iOS, la liste des onglets apparaît au bas de l’écran et la zone de détails apparaît au-dessus. Chaque onglet a également une image d’icône qui doit être au format PNG 30x30 avec une transparence pour la résolution normale, 60x60 pour la haute résolution et 90x90 pour la résolution iPhone 6 Plus. S’il y a plus de cinq onglets, un onglet *Plus* s’affiche et permet d’accéder aux onglets supplémentaires. Pour plus d’informations sur le chargement des images dans une application Xamarin.Forms, consultez [Utilisation des images](~/xamarin-forms/user-interface/images.md). Pour plus d’informations sur les exigences liées aux icônes, consultez [Création d’applications avec onglets](~/ios/user-interface/controls/creating-tabbed-applications.md).

  > [!NOTE]
  > Notez que le `TabbedRenderer` pour iOS a une méthode `GetIcon` substituable que vous pouvez utiliser pour charger des icônes d’onglets à partir d’une source spécifiée. Cette substitution permet d’utiliser des images SVG en tant qu’icônes sur un `TabbedPage`. Vous pouvez aussi fournir des versions sélectionnées et désélectionnées d’une icône.

- Sur Android, la liste des onglets apparaît en haut de l’écran par défaut, et la zone de détails est au-dessous. Toutefois, la liste d’onglets peut être déplacée vers le bas de l’écran à l’aide de paramètres propres à la plateforme. Pour plus d’informations, consultez [Définition de l’emplacement et de la couleur de la barre d’outils TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

  > [!NOTE]
  > Notez que quand vous utilisez AppCompat sur Android, chaque onglet affiche également une icône. De plus, le `TabbedPageRenderer` pour Android AppCompat a une méthode `GetIconDrawable` substituable que vous pouvez utiliser pour charger des icônes d’onglets à partir d’un `Drawable` personnalisé. Cette substitution rend possible l’utilisation d’images SVG comme icônes sur un `TabbedPage`, et fonctionne à la fois avec les barres d’onglets supérieures et inférieures. En guise d’alternative, vous pouvez utiliser la méthode `SetTabIcon` substituable pour charger des icônes d’onglets à partir d’un `Drawable` personnalisé pour les barres d’onglets supérieures.

- Sur les facteurs de forme de tablette Windows, les onglets ne sont pas toujours visibles et les utilisateurs doivent balayer vers le bas (ou cliquer avec le bouton droit, s’ils utilisent une souris) pour afficher les onglets dans un `TabbedPage` (comme illustré ci-dessous).

    ![](tabbed-page-images/windows-tabs.png "Onglets TabbedPage sur Windows")

## <a name="creating-a-tabbedpage"></a>Création d’un TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) définit les propriétés suivantes :

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor) de type [`Color`](xref:Xamarin.Forms.Color) la couleur d’arrière-plan de la barre d’onglets.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor) de type [`Color`](xref:Xamarin.Forms.Color) la couleur du texte de la barre d’onglets.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) de type [`Color`](xref:Xamarin.Forms.Color), la couleur de l’onglet lorsqu’il est sélectionné.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) de type [`Color`](xref:Xamarin.Forms.Color), la couleur de l’onglet lorsqu’il n’est pas sélectionné.

Toutes ces propriétés s’appuient sur des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être personnalisées et être des cibles de liaisons de données.

Deux approches peuvent être adoptées pour créer un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

- [Remplir](#Populating_a_TabbedPage_with_a_Page_Collection) le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) avec une collection d’objets enfants [`Page`](xref:Xamarin.Forms.Page), telle qu’une collection d’instances de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Affecter](#Populating_a_TabbedPage_with_a_Template) une collection à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) et affecter un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) afin de retourner des pages pour les objets dans la collection.

Avec les deux approches, le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) affiche chaque page quand l’utilisateur sélectionne chaque onglet.

> [!NOTE]
> Nous vous recommandons de remplir un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) uniquement avec des instances de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Cela permet de garantir une expérience utilisateur cohérente sur toutes les plateformes.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Remplissage d’un TabbedPage avec une collection de pages

L’exemple de code XAML suivant montre un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) construit par son remplissage avec une collection d’objets [`Page`](xref:Xamarin.Forms.Page) enfants :

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

L’exemple de code suivant montre le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) équivalent créé en C# :

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

Le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) est rempli avec deux objets [`Page`](xref:Xamarin.Forms.Page) enfants. Le premier enfant est une instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage), tandis que le deuxième onglet est un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) contenant une instance de `ContentPage`.

> [!NOTE]
> Le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ne prend pas en charge la virtualisation de l’interface utilisateur. Par conséquent, les performances peuvent être affectées si le `TabbedPage` contient trop d’éléments enfants.

Les captures d’écran suivantes montrent l’instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage) `TodayPage`, qui est affichée sous l’onglet *Today* :

![](tabbed-page-images/today-page.png "ContentPage dans un TabbedPage")

La sélection de l’onglet *Schedule* affiche l’instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage) `SchedulePage`, qui est encapsulée dans une instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), et est affichée dans le capture d’écran suivante :

![](tabbed-page-images/schedule-page.png "NavigationPage dans un TabbedPage")

Pour plus d’informations sur la disposition d’un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), consultez [Exécution de la navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Bien qu’il soit acceptable de placer un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), nous vous déconseillons de placer un `TabbedPage` dans un `NavigationPage`. En effet, sur iOS, un `UITabBarController` agit toujours en tant que wrapper pour le `UINavigationController`. Pour plus d’informations, consultez [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfaces de contrôleurs de vue combinés) dans la bibliothèque du développeur iOS.

#### <a name="navigation-inside-a-tab"></a>Navigation à l’intérieur d’un onglet

La navigation peut être effectuée à partir du deuxième onglet en appelant la méthode [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) sur la propriété [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de l’instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage), comme illustré dans l’exemple de code suivant :

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

L’instance de `UpcomingAppointmentsPage` est ainsi envoyée dans la pile de navigation, où elle devient la page active. Ceci est illustré dans les captures d’écran suivantes :

![](tabbed-page-images/navigationpage.png "Navigation à l’intérieur d’un onglet")

Pour plus d’informations sur l’exécution de la navigation à l’aide de la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Remplissage d’un TabbedPage avec un modèle

L’exemple de code XAML suivant montre un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) construit en assignant un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) afin de retourner des pages pour les objets de la collection :

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
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

Le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) est rempli avec des données en définissant la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) dans le constructeur pour le fichier code-behind :

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

L’exemple de code suivant montre le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) équivalent créé en C# :

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
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

Chaque onglet affiche un [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui utilise une série d’instances de [`StackLayout`](xref:Xamarin.Forms.StackLayout) et de [`Label`](xref:Xamarin.Forms.Label) pour afficher les données de l’onglet. Les captures d’écran suivantes affichent le contenu de l’onglet *Tamarin* :

![](tabbed-page-images/tab3.png "Remplissage d’un TabbedPage avec un modèle")

La sélection d’un autre onglet affiche alors le contenu de cet onglet.

> [!NOTE]
> Le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ne prend pas en charge la virtualisation de l’interface utilisateur. Par conséquent, les performances peuvent être affectées si le `TabbedPage` contient trop d’éléments enfants.

Pour plus d’informations sur [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), consultez le [chapitre 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez vu comment utiliser un TabbedPage pour parcourir une collection de pages. Le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) de Xamarin.Forms se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails.

## <a name="related-links"></a>Liens associés

- [Types de pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
