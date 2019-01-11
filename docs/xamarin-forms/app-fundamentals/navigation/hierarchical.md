---
title: Navigation hiérarchique
description: Cet article montre comment utiliser la classe NavigationPage pour effectuer la navigation dans une pile de pages de type LIFO (dernier entré, premier sorti).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: f69acd60d7a80607528e4a39ee6a8bfbc19711f5
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207971"
---
# <a name="hierarchical-navigation"></a>Navigation hiérarchique

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)

_La classe NavigationPage propose une expérience de navigation hiérarchique où l’utilisateur est en mesure de parcourir les pages, vers l’avant et vers l’arrière, comme il le souhaite. La classe implémente la navigation sous forme de pile LIFO (dernier entré, premier sorti) d’objets Pages. Cet article montre comment utiliser la classe NavigationPage pour effectuer la navigation dans une pile de pages._

Pour passer d’une page à une autre, une application envoie une nouvelle page dans la pile de navigation, où elle devient la page active, comme illustré dans le diagramme suivant :

![](hierarchical-images/pushing.png "Envoi d’une page vers la pile de navigation")

Pour revenir à la page précédente, l’application dépile la page actuelle, et la nouvelle page tout en haut devient la page active, comme illustré dans le diagramme suivant :

![](hierarchical-images/popping.png "Retrait d’une page de la pile de navigation")

Les méthodes de navigation sont exposées par la propriété [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) sur n’importe quel type dérivé [`Page`](xref:Xamarin.Forms.Page). Ces méthodes permettent d’envoyer des pages dans la pile de navigation, de retirer des pages de la pile de navigation, et d’effectuer des manipulations de la pile.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Navigation

Dans la navigation hiérarchique, la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) est utilisée pour parcourir une pile d’objets [`ContentPage`](xref:Xamarin.Forms.ContentPage). Les captures d’écran suivantes montrent les principaux composants de `NavigationPage` sur chaque plateforme :

![](hierarchical-images/navigationpage-components.png "Composants de NavigationPage")

La disposition d’une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) dépend de la plateforme :

- Sur iOS, une barre de navigation est présente en haut de la page et affiche un titre, avec un bouton *Précédent* permettant de revenir à la page précédente.
- Sur Android, une barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton *Précédent* permettant de revenir à la page précédente. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android.
- Sur la plateforme Windows universelle, une barre de navigation en haut de la page affiche un titre.

Sur toutes les plateformes, la valeur de la propriété [`Page.Title`](xref:Xamarin.Forms.Page.Title) est affichée en tant que titre de la page.

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

L’instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage) `Page1Xaml` est ainsi envoyée dans la pile de navigation, où elle devient la page active et la page racine de l’application. Ceci est illustré dans les captures d’écran suivantes :

![](hierarchical-images/mainpage.png "Page racine de la pile de navigation")

> [!NOTE]
> La propriété [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) d’une instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) permet d’accéder à la première page de la pile de navigation.

### <a name="pushing-pages-to-the-navigation-stack"></a>Envoi de pages dans la pile de navigation

Pour accéder à `Page2Xaml`, vous devez appeler la méthode [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) sur la propriété [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) de la page active, comme illustré dans l’exemple de code suivant :

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

L’instance de `Page2Xaml` est ainsi envoyée dans la pile de navigation, où elle devient la page active. Ceci est illustré dans les captures d’écran suivantes :

![](hierarchical-images/secondpage.png "Page envoyée dans la pile de navigation")

Quand la méthode [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) est appelée, les événements suivants se produisent :

- La substitution [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) de la page qui appelle `PushAsync` est appelée.
- La substitution [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de la page cible de la navigation est appelée.
- La tâche `PushAsync` est terminée.

Toutefois, l’ordre exact dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

> [!NOTE]
> Les appels des substitutions [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) et [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ne peuvent pas être considérés comme des indications garanties de la navigation entre les pages. Par exemple, sur iOS, la substitution `OnDisappearing` est appelée sur la page active quand l’application se ferme.

### <a name="popping-pages-from-the-navigation-stack"></a>Retrait de pages de la pile de navigation

La page active peut être retirée de la pile de navigation en appuyant sur le bouton *Précédent* sur l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran.

Pour revenir par programmation à la page d’origine, l’instance de `Page2Xaml` doit appeler la méthode [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), comme illustré dans l’exemple de code suivant :

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

L’instance de `Page2Xaml` est ainsi retirée de la pile de navigation, et la nouvelle page tout en haut devient la page active. Quand la méthode [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) est appelée, les événements suivants se produisent :

- La substitution [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) de la page qui appelle `PopAsync` est appelée.
- La substitution [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de la page cible de la navigation est appelée.
- La tâche `PopAsync` est terminée.

Toutefois, l’ordre exact dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

En plus des méthodes [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) et [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), la propriété [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) de chaque page fournit également une méthode [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync), illustrée dans l’exemple de code suivant :

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Cette méthode retire de la pile de navigation toutes les pages sauf la [`Page`](xref:Xamarin.Forms.Page) racine, ce qui fait de la page racine de l’application la page active.

### <a name="animating-page-transitions"></a>Animation des transitions de page

La propriété [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) de chaque page fournit également des méthodes d’envoi et de dépilation substituées qui incluent un paramètre `boolean` contrôlant l’affichage d’une animation de page durant la navigation, comme indiqué dans l’exemple de code suivant :

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

Il est parfois nécessaire pour une page de transmettre des données à une autre page lors de la navigation. Il existe pour cela deux techniques : transmettre les données par le biais d’un constructeur de page, et définir les données comme [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la nouvelle page. Nous allons maintenant examiner chacune de ces techniques.

### <a name="passing-data-through-a-page-constructor"></a>Passage des données via un constructeur de page

La technique la plus simple pour transmettre des données à une autre page lors de la navigation est de faire appel à un paramètre de constructeur de page, comme illustré dans l’exemple de code suivant :

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Ce code crée une instance de `MainPage` et transmet la date et l’heure actuelles au format ISO8601, encapsulées dans une instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

L’instance de `MainPage` reçoit les données par le biais d’un paramètre de constructeur, comme illustré dans l’exemple de code suivant :

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Les données sont ensuite affichées dans la page en définissant la propriété [`Label.Text`](xref:Xamarin.Forms.Label.Text), comme indiqué dans les captures d’écran suivantes :

![](hierarchical-images/passing-data-constructor.png "Données transmises par le biais d’un constructeur de page")

### <a name="passing-data-through-a-bindingcontext"></a>Transmission de données par le biais d’un BindingContext

Il existe une autre approche pour passer des données à une autre page durant la navigation. Elle consiste à affecter les données au [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la nouvelle page, comme indiqué dans l’exemple de code suivant :

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

Ce code affecte au [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de l’instance`SecondPage` l’instance de `Contact`, puis accède à `SecondPage`.

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

L’exemple de code suivant montre comment la liaison de données peut être effectuée en C# :

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

Les données sont ensuite affichées dans la page par une série de contrôles [`Label`](xref:Xamarin.Forms.Label), comme indiqué dans les captures d’écran suivantes :

![](hierarchical-images/passing-data-bindingcontext.png "Données transmises par le biais d’un BindingContext")

Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulation de la pile de navigation

La propriété [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) expose une propriété [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) à partir de laquelle les pages dans la pile de navigation peuvent être obtenues. Tandis que Xamarin.Forms gère l’accès à la pile de navigation, la propriété `Navigation` fournit les méthodes [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) et [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) pour manipuler la pile en insérant ou en supprimant des pages.

La méthode [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) insère une page spécifiée dans la pile de navigation avant une page existante spécifiée, comme indiqué dans le diagramme suivant :

![](hierarchical-images/insert-page-before.png "Insertion d’une page dans la pile de navigation")

La méthode [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) supprime la page spécifiée de la pile de navigation, comme illustré dans le diagramme suivant :

![](hierarchical-images/remove-page.png "Retrait d’une page de la pile de navigation")

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

À condition que les informations d’identification de l’utilisateur soient correctes, l’instance de `MainPage` est insérée dans la pile de navigation avant la page active. La méthode [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) supprime ensuite la page active de la pile de navigation, et l’instance de `MainPage` devient la page active.

## <a name="displaying-views-in-the-navigation-bar"></a>Affichage des vues dans la barre de navigation

Toute [`View`](xref:Xamarin.Forms.View) Xamarin.Forms peut être affichée dans la barre de navigation d’une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Pour cela, vous devez définir la propriété jointe [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) sur une `View`. Cette propriété jointe peut être définie sur n’importe quelle [`Page`](xref:Xamarin.Forms.Page), et quand la `Page` envoyée vers sur une `NavigationPage`, la `NavigationPage` respecte la valeur de la propriété.

L’exemple suivant, tiré de l’[exemple Title View](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/), montre comment définir la propriété jointe [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) à partir de XAML :

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

Il en résulte l’affichage d’un [`Slider`](xref:Xamarin.Forms.Slider) dans la barre de navigation dans la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) :

[![Curseur TitleView](hierarchical-images/titleview-small.png "Curseur TitleView")](hierarchical-images/titleview-large.png#lightbox "Curseur TitleView")

> [!IMPORTANT]
> De nombreuses vues n’apparaissent dans la barre de navigation que si la taille de la vue est spécifiée avec les propriétés [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest). En guise d’alternative, vous pouvez encapsuler la vue dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) avec des valeurs appropriées affectées aux propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Notez que, étant donné que la classe [`Layout`](xref:Xamarin.Forms.Layout) dérive de la classe [`View`](xref:Xamarin.Forms.View), la propriété jointe [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) peut être configurée de façon à afficher une classe de disposition qui contient plusieurs vues. Sur iOS et la plateforme universelle Windows (UWP), la hauteur de la barre de navigation ne peut pas être changée ; ainsi, un découpage se produira si la taille de la vue affichée dans la barre de navigation est supérieure à la taille par défaut de la barre de navigation. En revanche, sur Android, la hauteur de la barre de navigation peut être changée en affectant un `double` représentant la nouvelle hauteur comme valeur de la propriété pouvant être liée [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty). Pour plus d’informations, consultez [Définition de la hauteur de la barre de navigation sur une NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md).

En guise d’alternative, une barre de navigation étendue peut être suggérée en plaçant une partie du contenu dans la barre de navigation et une autre partie dans une vue en haut du contenu de page, à laquelle vous affectez une couleur correspondant à celle de la barre de navigation. En outre, sur iOS la ligne de séparation et l’ombre qui se trouve au bas de la barre de navigation peuvent être supprimées en affectant la valeur `true` à la propriété pouvant être liée [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty). Pour plus d’informations, consultez [Masquage du séparateur de barre de navigation sur une NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md).

> [!NOTE]
> Les propriétés [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty), [`Title`](xref:Xamarin.Forms.Page.Title), [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) et [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) peuvent toutes définir des valeurs qui occupent de l’espace sur la barre de navigation. Bien que la taille de la barre de navigation varie en fonction de la plateforme et de la taille de l’écran, la définition de toutes ces propriétés provoquera des conflits en raison de l’espace limité disponible. Au lieu d’essayer d’utiliser une combinaison de ces propriétés, vous obtiendrez peut-être plus facilement la conception de barre de navigation souhaitée en définissant uniquement la propriété `TitleView`.

### <a name="limitations"></a>Limitations

Vous devez prendre en compte un certain nombre de limitations lors de l’affichage d’une [`View`](xref:Xamarin.Forms.View) dans la barre de navigation d’une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) :

- Sur iOS, les vues placées dans la barre de navigation d’une `NavigationPage` apparaissent dans une position différente selon que les grands titres sont activés ou non. Pour plus d’informations sur l’activation des grands titres, consultez [Affichage de grands titres](~/xamarin-forms/platform/ios/page-large-title.md).
- Sur Android, vous ne pouvez placer des vues dans la barre de navigation d’une `NavigationPage` que dans les applications qui utilisent app-compat.
- Nous vous déconseillons de placer des vues volumineuses et complexes, telles que [`ListView`](xref:Xamarin.Forms.ListView) et [`TableView`](xref:Xamarin.Forms.TableView), dans la barre de navigation d’une `NavigationPage`.

## <a name="related-links"></a>Liens associés

- [Navigation entre les pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (exemple)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (exemple)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (exemple)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [TitleView (exemple)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/)
- [How to Create a Sign In Screen Flow in Xamarin.Forms (Guide pratique pour créer un flux d’écran de connexion dans Xamarin.Forms) (vidéo Xamarin University) - Exemple](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [How to Create a Sign In Screen Flow in Xamarin.Forms (Guide pratique pour créer un flux d’écran de connexion dans Xamarin.Forms) (vidéo Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
