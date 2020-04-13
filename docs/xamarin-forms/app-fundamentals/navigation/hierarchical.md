---
title: Navigation hiérarchique
description: Cet article montre comment utiliser la classe NavigationPage pour effectuer la navigation dans une pile de pages de type LIFO (dernier entré, premier sorti).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 11ad1fb18d1263eb77ef037350a3633510934c42
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305114"
---
# <a name="hierarchical-navigation"></a>Navigation hiérarchique

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_La classe NavigationPage offre une expérience de navigation hiérarchique où l’utilisateur est capable de naviguer à travers les pages, les avants et vers l’arrière, comme vous le souhaitez. La classe implémente la navigation comme une dernière pile d’objets de page(LIFO). Cet article montre comment utiliser la classe NavigationPage pour effectuer la navigation dans une pile de pages._

Pour passer d’une page à une autre, une application envoie une nouvelle page dans la pile de navigation, où elle devient la page active, comme illustré dans le diagramme suivant :

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

Pour revenir à la page précédente, l’application dépile la page actuelle, et la nouvelle page tout en haut devient la page active, comme illustré dans le diagramme suivant :

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

Les méthodes de [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) navigation sont [`Page`](xref:Xamarin.Forms.Page) exposées par la propriété sur tous les types dérivés. Ces méthodes permettent d’envoyer des pages dans la pile de navigation, de retirer des pages de la pile de navigation, et d’effectuer des manipulations de la pile.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Navigation

Dans la navigation hiérarchique, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) est utilisée pour naviguer à travers une pile d’objets. Les captures d’écran suivantes montrent les principaux composants de `NavigationPage` sur chaque plateforme :

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

La mise [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) en page d’un dépend de la plate-forme:

- Sur iOS, une barre de navigation est présente en haut de la page et affiche un titre, avec un bouton *Précédent* permettant de revenir à la page précédente.
- Sur Android, une barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton *Précédent* permettant de revenir à la page précédente. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android.
- Sur la plateforme Windows universelle, une barre de navigation en haut de la page affiche un titre.

Sur toutes les plates-formes, la valeur de la [`Page.Title`](xref:Xamarin.Forms.Page.Title) propriété sera affichée comme titre de page.

> [!NOTE]
> Nous vous recommandons de remplir une `NavigationPage` uniquement avec des instances de `ContentPage`.

### <a name="creating-the-root-page"></a>Création de la page racine

La première page ajoutée à une pile de navigation est appelée la page *racine* de l’application et l’exemple de code suivant illustre le déroulement de l’opération :

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

Cela provoque `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage) l’instance d’être poussé sur la pile de navigation, où il devient la page active et la page racine de l’application. Ceci est illustré dans les captures d’écran suivantes :

![](hierarchical-images/mainpage.png "Root Page of Navigation Stack")

> [!NOTE]
> La [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) propriété [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) d’une instance donne accès à la première page de la pile de navigation.

### <a name="pushing-pages-to-the-navigation-stack"></a>Envoi de pages dans la pile de navigation

Pour naviguer `Page2Xaml`vers , il [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) est nécessaire [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) d’invoquer la méthode sur la propriété de la page actuelle, comme le démontre l’exemple de code suivant:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

L’instance de `Page2Xaml` est ainsi envoyée dans la pile de navigation, où elle devient la page active. Ceci est illustré dans les captures d’écran suivantes :

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

Lorsque [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) la méthode est invoquée, les événements suivants se produisent :

- L’appel `PushAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) page a son remplacement invoqué.
- La page en cours [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de navigation a son remplacement invoqué.
- La tâche `PushAsync` est terminée.

Toutefois, l’ordre exact dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, voir [le chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre Xamarin.Forms de Charles Petzold.

> [!NOTE]
> Les appels [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) vers les dérogations et les remplacements ne peuvent pas être traités comme des indications garanties de navigation de page. Par exemple, sur iOS, la substitution `OnDisappearing` est appelée sur la page active quand l’application se ferme.

### <a name="popping-pages-from-the-navigation-stack"></a>Retrait de pages de la pile de navigation

La page active peut être retirée de la pile de navigation en appuyant sur le bouton *Précédent* sur l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran.

Pour revenir sur le plan programmatique à la page originale, l’instance `Page2Xaml` doit invoquer la [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) méthode, comme le démontre l’exemple de code suivant :

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

L’instance de `Page2Xaml` est ainsi retirée de la pile de navigation, et la nouvelle page tout en haut devient la page active. Lorsque [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) la méthode est invoquée, les événements suivants se produisent :

- L’appel `PopAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) page a son remplacement invoqué.
- La page à laquelle [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) il est retourné a invoqué son remplacement.
- La tâche `PopAsync` est terminée.

Toutefois, l’ordre exact dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

En plus [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) des méthodes [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) et des méthodes, [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) la propriété de chaque page fournit également une méthode, qui est indiquée dans l’exemple de code suivant :

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Cette méthode apparaît tout [`Page`](xref:Xamarin.Forms.Page) sauf la racine de la pile de navigation, ce qui rend la page racine de l’application la page active.

### <a name="animating-page-transitions"></a>Animation des transitions de page

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de chaque page fournit également des méthodes `boolean` de poussée et de pop qui incluent un paramètre qui contrôle s’il y a à afficher une animation de page pendant la navigation, comme indiqué dans l’exemple de code suivant :

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

Si vous affectez au paramètre `boolean` la valeur `false`, l’animation de transition de page est désactivée. En revanche, si vous affectez au paramètre la valeur `true` l’animation de transition de page est activée, à condition qu’elle soit prise en charge par la plateforme sous-jacente. Toutefois, les méthodes d’envoi et de dépilation qui n’ont pas ce paramètre activent l’animation par défaut.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passage des données durant la navigation

Il est parfois nécessaire pour une page de transmettre des données à une autre page lors de la navigation. Deux techniques pour y parvenir sont de transmettre des données à un [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) constructeur de pages, et en définissant la nouvelle page aux données. Nous allons maintenant examiner chacune de ces techniques.

### <a name="passing-data-through-a-page-constructor"></a>Passage des données via un constructeur de page

La technique la plus simple pour passer des données à une autre page durant la navigation consiste à utiliser un paramètre de constructeur de page, comme indiqué dans l’exemple de code suivant :

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Ce code `MainPage` crée une instance, en passant dans la date et l’heure [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) actuelles dans le format ISO8601, qui est enveloppé dans une instance.

L’instance de `MainPage` reçoit les données via un paramètre de constructeur, comme indiqué dans l’exemple de code suivant :

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Les données sont ensuite affichées [`Label.Text`](xref:Xamarin.Forms.Label.Text) sur la page en définissant la propriété, comme le montrent les captures d’écran suivantes :

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>Passage des données via un BindingContext

Une approche alternative pour transmettre des données à une autre [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) page pendant la navigation est de définir la nouvelle page aux données, comme indiqué dans l’exemple de code suivant :

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

Ce code [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) définit `SecondPage` l’instance `Contact` à l’instance, `SecondPage`puis navigue vers le .

`SecondPage` utilise ensuite la liaison de données pour afficher les données de l’instance de `Contact`, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

L’exemple de code suivant montre comment effectuer la liaison de données en C# :

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

Les données sont ensuite affichées sur [`Label`](xref:Xamarin.Forms.Label) la page par une série de contrôles, comme le montrent les captures d’écran suivantes :

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulation de la pile de navigation

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété expose [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) une propriété à partir de laquelle les pages de la pile de navigation peuvent être obtenues. Tandis que Xamarin.Forms maintient l’accès `Navigation` à [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) la [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) pile de navigation, la propriété fournit le et les méthodes pour manipuler la pile en insérant des pages ou en les supprimant.

La [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) méthode insère une page spécifiée dans la pile de navigation avant une page spécifiée existante, comme indiqué dans le diagramme suivant :

![](hierarchical-images/insert-page-before.png "Inserting a Page in the Navigation Stack")

La [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) méthode supprime la page spécifiée de la pile de navigation, comme indiqué dans le diagramme suivant :

![](hierarchical-images/remove-page.png "Removing a Page from the Navigation Stack")

Ces méthodes permettent de disposer d’une expérience de navigation personnalisée, par exemple remplacer une page de connexion par une nouvelle page, suite à une connexion réussie. L’exemple de code suivant illustre ce scénario :

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

À condition que les informations d’identification de l’utilisateur soient correctes, l’instance de `MainPage` est insérée dans la pile de navigation avant la page active. La [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) méthode supprime ensuite la page actuelle de `MainPage` la pile de navigation, l’instance devenant la page active.

## <a name="displaying-views-in-the-navigation-bar"></a>Affichage des vues dans la barre de navigation

N’importe quel [`View`](xref:Xamarin.Forms.View) Xamarin.Forms peut être [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)affiché dans la barre de navigation d’un . Ceci est accompli [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) en fixant `View`la propriété attachée à un . Cette propriété attachée peut [`Page`](xref:Xamarin.Forms.Page)être fixée `Page` sur n’importe quel, et quand le est poussé sur un `NavigationPage`, le `NavigationPage` respectera la valeur de la propriété.

L’exemple suivant, tiré de l’[exemple Title View](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview), montre comment définir la propriété jointe [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) à partir de XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

Voici le code C# équivalent :

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

Il en [`Slider`](xref:Xamarin.Forms.Slider) résulte un affichage dans [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)la barre de navigation sur le :

[![Titre De SliderView](hierarchical-images/titleview-small.png "Titre De SliderView")](hierarchical-images/titleview-large.png#lightbox "Titre De SliderView")

> [!IMPORTANT]
> De nombreuses vues n’apparaîtront pas dans la barre de [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) navigation à moins que la taille de la vue ne soit spécifiée avec les propriétés et les propriétés. Alternativement, la vue peut [`StackLayout`](xref:Xamarin.Forms.StackLayout) être [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) enveloppée dans un avec le et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) les propriétés définies à des valeurs appropriées.

Notez que, [`Layout`](xref:Xamarin.Forms.Layout) parce que [`View`](xref:Xamarin.Forms.View) la classe [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) dérive de la classe, la propriété ci-jointe peut être définie pour afficher une classe de mise en page qui contient plusieurs vues. Sur iOS et la plateforme universelle Windows (UWP), la hauteur de la barre de navigation ne peut pas être changée ; ainsi, un découpage se produira si la taille de la vue affichée dans la barre de navigation est supérieure à la taille par défaut de la barre de navigation. Cependant, sur Android, la hauteur de la barre [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) de navigation `double` peut être changée en définissant la propriété liant à une représentant la nouvelle hauteur. Pour plus d’informations, consultez [Définition de la hauteur de la barre de navigation sur une NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md).

En guise d’alternative, une barre de navigation étendue peut être suggérée en plaçant une partie du contenu dans la barre de navigation et une autre partie dans une vue en haut du contenu de page, à laquelle vous affectez une couleur correspondant à celle de la barre de navigation. En outre, sur iOS la ligne de séparateur et l’ombre qui [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) est au `true`bas de la barre de navigation peut être enlevé en définissant la propriété liant à . Pour plus d’informations, consultez [Masquage du séparateur de barre de navigation sur une NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md).

> [!NOTE]
> Les [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty) [`Title`](xref:Xamarin.Forms.Page.Title), [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty), [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) et les propriétés peuvent tous définir les valeurs qui occupent l’espace sur la barre de navigation. Bien que la taille de la barre de navigation varie en fonction de la plateforme et de la taille de l’écran, la définition de toutes ces propriétés provoquera des conflits en raison de l’espace limité disponible. Au lieu d’essayer d’utiliser une combinaison de ces propriétés, vous obtiendrez peut-être plus facilement la conception de barre de navigation souhaitée en définissant uniquement la propriété `TitleView`.

### <a name="limitations"></a>Limites

Il y a un certain nombre de [`View`](xref:Xamarin.Forms.View) limites à [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)connaître lors de l’affichage d’un dans la barre de navigation d’un :

- Sur iOS, les vues placées dans la barre de navigation d’une `NavigationPage` apparaissent dans une position différente selon que les grands titres sont activés ou non. Pour plus d’informations sur l’activation des grands titres, consultez [Affichage de grands titres](~/xamarin-forms/platform/ios/page-large-title.md).
- Sur Android, vous ne pouvez placer des vues dans la barre de navigation d’une `NavigationPage` que dans les applications qui utilisent app-compat.
- Il n’est pas recommandé de placer [`ListView`](xref:Xamarin.Forms.ListView) des [`TableView`](xref:Xamarin.Forms.TableView)vues grandes et `NavigationPage`complexes, telles que et , dans la barre de navigation d’un .

## <a name="related-links"></a>Liens connexes

- [Page Navigation](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)
- [PassingData (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
- [LoginFlow (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [TitleView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)
- [Vidéo sur la création d’un flux d’écran de connexion dans Xamarin.Forms](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
