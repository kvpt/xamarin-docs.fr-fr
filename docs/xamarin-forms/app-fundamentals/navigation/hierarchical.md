---
title: Navigation hiérarchique
description: Cet article montre comment utiliser la classe NavigationPage pour effectuer la navigation dans une pile de pages de type LIFO (dernier entré, premier sorti).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
ms.openlocfilehash: 984f54698ccdee54c0b8670a50cb1f4432327977
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517276"
---
# <a name="hierarchical-navigation"></a>Navigation hiérarchique

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_La classe NavigationPage fournit une expérience de navigation hiérarchique dans laquelle l’utilisateur peut naviguer dans les pages, vers l’avant et vers l’arrière, selon les besoins. La classe implémente la navigation sous la forme d’une pile d’objets page LIFO (dernier entré, premier sorti). Cet article montre comment utiliser la classe NavigationPage pour effectuer la navigation dans une pile de pages._

Pour passer d’une page à une autre, une application envoie une nouvelle page dans la pile de navigation, où elle devient la page active, comme illustré dans le diagramme suivant :

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

Pour revenir à la page précédente, l’application dépile la page actuelle, et la nouvelle page tout en haut devient la page active, comme illustré dans le diagramme suivant :

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

Les méthodes de navigation sont exposées [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) par la propriété [`Page`](xref:Xamarin.Forms.Page) sur tous les types dérivés. Ces méthodes permettent d’envoyer des pages dans la pile de navigation, de retirer des pages de la pile de navigation, et d’effectuer des manipulations de la pile.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Navigation

Dans la navigation hiérarchique, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe est utilisée pour naviguer dans une pile d' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets. Les captures d’écran suivantes montrent les principaux composants de `NavigationPage` sur chaque plateforme :

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

La disposition d’un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) dépend de la plateforme :

- Sur iOS, une barre de navigation est présente en haut de la page et affiche un titre, avec un bouton *Précédent* permettant de revenir à la page précédente.
- Sur Android, une barre de navigation est présente en haut de la page et affiche un titre, une icône et un bouton *Précédent* permettant de revenir à la page précédente. L’icône est définie dans l’attribut `[Activity]` qui décore la classe `MainActivity` dans le projet propre à la plateforme Android.
- Sur la plateforme Windows universelle, une barre de navigation en haut de la page affiche un titre.

Sur toutes les plateformes, la valeur de [`Page.Title`](xref:Xamarin.Forms.Page.Title) la propriété sera affichée comme titre de page. En outre, la `IconColor` propriété peut être définie sur un [`Color`](xref:Xamarin.Forms.Color) qui est appliqué à l’icône dans la barre de navigation.

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

Cela entraîne le `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage) push de l’instance dans la pile de navigation, où elle devient la page active et la page racine de l’application. Ceci est illustré dans les captures d’écran suivantes :

![](hierarchical-images/mainpage.png "Root Page of Navigation Stack")

> [!NOTE]
> La [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) propriété d’une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance de permet d’accéder à la première page de la pile de navigation.

### <a name="pushing-pages-to-the-navigation-stack"></a>Envoi de pages dans la pile de navigation

Pour naviguer vers `Page2Xaml`, il est nécessaire d’appeler la [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) méthode sur la [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de la page actuelle, comme illustré dans l’exemple de code suivant :

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

L’instance de `Page2Xaml` est ainsi envoyée dans la pile de navigation, où elle devient la page active. Ceci est illustré dans les captures d’écran suivantes :

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

Lorsque la [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) méthode est appelée, les événements suivants se produisent :

- La page appelante `PushAsync` a [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) sa substitution appelée.
- La page cible de la navigation a sa [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substitution appelée.
- La tâche `PushAsync` est terminée.

Toutefois, l’ordre exact dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre Xamarin. Forms de Charles Petzold.

> [!NOTE]
> Les appels aux [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) substitutions et [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ne peuvent pas être traités comme des indications garanties de la navigation entre les pages. Par exemple, sur iOS, la substitution `OnDisappearing` est appelée sur la page active quand l’application se ferme.

### <a name="popping-pages-from-the-navigation-stack"></a>Retrait de pages de la pile de navigation

La page active peut être retirée de la pile de navigation en appuyant sur le bouton *Précédent* sur l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran.

Pour revenir par programmation à la page d’origine, l' `Page2Xaml` instance doit appeler la [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) méthode, comme illustré dans l’exemple de code suivant :

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

L’instance de `Page2Xaml` est ainsi retirée de la pile de navigation, et la nouvelle page tout en haut devient la page active. Lorsque la [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) méthode est appelée, les événements suivants se produisent :

- La page appelante `PopAsync` a [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) sa substitution appelée.
- La page retournée a sa [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substitution appelée.
- La tâche `PopAsync` est terminée.

Toutefois, l’ordre exact dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

Ainsi que [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) les méthodes [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) et, la [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de chaque page fournit également une [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) méthode, qui est illustrée dans l’exemple de code suivant :

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Cette méthode dépile tout sauf la [`Page`](xref:Xamarin.Forms.Page) racine de la pile de navigation, rendant ainsi la page racine de l’application active.

### <a name="animating-page-transitions"></a>Animation des transitions de page

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de chaque page fournit également des méthodes push et pop substituées qui incluent `boolean` un paramètre qui contrôle s’il faut afficher une animation de page pendant la navigation, comme illustré dans l’exemple de code suivant :

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

Il est parfois nécessaire pour une page de transmettre des données à une autre page lors de la navigation. Pour ce faire, deux techniques permettent de passer des données via un constructeur de page et de définir les données [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la nouvelle page. Nous allons maintenant examiner chacune de ces techniques.

### <a name="passing-data-through-a-page-constructor"></a>Passage des données via un constructeur de page

La technique la plus simple pour passer des données à une autre page durant la navigation consiste à utiliser un paramètre de constructeur de page, comme indiqué dans l’exemple de code suivant :

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Ce code crée une `MainPage` instance, en passant la date et l’heure actuelles au format ISO8601, qui est encapsulé dans [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) une instance.

L’instance de `MainPage` reçoit les données via un paramètre de constructeur, comme indiqué dans l’exemple de code suivant :

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Les données sont ensuite affichées sur la page en définissant [`Label.Text`](xref:Xamarin.Forms.Label.Text) la propriété, comme illustré dans les captures d’écran suivantes :

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>Passage des données via un BindingContext

Une autre approche pour passer des données à une autre page pendant la navigation consiste à définir la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) nouvelle page sur les données, comme illustré dans l’exemple de code suivant :

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

[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) Ce code affecte `SecondPage` l' `Contact` instance à l’instance, puis accède à. `SecondPage`

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

Les données sont ensuite affichées sur la page par une série de [`Label`](xref:Xamarin.Forms.Label) contrôles, comme illustré dans les captures d’écran suivantes :

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulation de la pile de navigation

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété expose une [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) propriété à partir de laquelle les pages de la pile de navigation peuvent être obtenues. Alors que Xamarin. Forms conserve l’accès à la pile `Navigation` de navigation, [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) la [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) propriété fournit les méthodes et pour manipuler la pile en insérant des pages ou en les supprimant.

La [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) méthode insère une page spécifiée dans la pile de navigation avant une page spécifiée existante, comme indiqué dans le diagramme suivant :

![](hierarchical-images/insert-page-before.png "Inserting a Page in the Navigation Stack")

La [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) méthode supprime la page spécifiée de la pile de navigation, comme indiqué dans le diagramme suivant :

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

À condition que les informations d’identification de l’utilisateur soient correctes, l’instance de `MainPage` est insérée dans la pile de navigation avant la page active. La [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) méthode supprime ensuite la page actuelle de la pile de navigation, l' `MainPage` instance devenant la page active.

## <a name="displaying-views-in-the-navigation-bar"></a>Affichage des vues dans la barre de navigation

N’importe quel Xamarin [`View`](xref:Xamarin.Forms.View) . Forms peut être affiché dans la barre [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)de navigation d’un. Pour ce faire, affectez [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) la valeur à la `View`propriété jointe. Cette propriété jointe peut être définie sur [`Page`](xref:Xamarin.Forms.Page)any et, lorsque `Page` le fait l’objet `NavigationPage`d’un `NavigationPage` push sur un, le respecte la valeur de la propriété.

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

Cela entraîne l’affichage [`Slider`](xref:Xamarin.Forms.Slider) d’un dans la barre de navigation sur [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)le :

[![TitleView de curseur](hierarchical-images/titleview-small.png "TitleView de curseur")](hierarchical-images/titleview-large.png#lightbox "TitleView de curseur")

> [!IMPORTANT]
> De nombreux affichages ne s’affichent pas dans la barre de navigation, sauf si la [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) taille [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de la vue est spécifiée avec les propriétés et. La vue peut également être incluse dans un wrapper dans [`StackLayout`](xref:Xamarin.Forms.StackLayout) un avec [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) les [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propriétés et définies sur les valeurs appropriées.

Notez que, étant donné [`Layout`](xref:Xamarin.Forms.Layout) que la classe dérive [`View`](xref:Xamarin.Forms.View) de la classe [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) , la propriété jointe peut être définie pour afficher une classe de disposition qui contient plusieurs vues. Sur iOS et la plateforme universelle Windows (UWP), la hauteur de la barre de navigation ne peut pas être changée ; ainsi, un découpage se produira si la taille de la vue affichée dans la barre de navigation est supérieure à la taille par défaut de la barre de navigation. Toutefois, sur Android, la hauteur de la barre de navigation peut être modifiée en affectant à la [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propriété pouvant `double` être liée une valeur représentant la nouvelle hauteur. Pour plus d’informations, consultez [Définition de la hauteur de la barre de navigation sur une NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md).

En guise d’alternative, une barre de navigation étendue peut être suggérée en plaçant une partie du contenu dans la barre de navigation et une autre partie dans une vue en haut du contenu de page, à laquelle vous affectez une couleur correspondant à celle de la barre de navigation. En outre, sur iOS, la ligne de séparation et l’ombre qui se trouvent au bas de la barre de navigation peuvent être [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) supprimées en affectant à `true`la propriété pouvant être liée la valeur. Pour plus d’informations, consultez [Masquage du séparateur de barre de navigation sur une NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md).

> [!NOTE]
> Les [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty)propriétés [`Title`](xref:Xamarin.Forms.Page.Title), [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty), et [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) peuvent toutes définir des valeurs qui occupent de l’espace dans la barre de navigation. Bien que la taille de la barre de navigation varie en fonction de la plateforme et de la taille de l’écran, la définition de toutes ces propriétés provoquera des conflits en raison de l’espace limité disponible. Au lieu d’essayer d’utiliser une combinaison de ces propriétés, vous obtiendrez peut-être plus facilement la conception de barre de navigation souhaitée en définissant uniquement la propriété `TitleView`.

### <a name="limitations"></a>Limites

Il existe un certain nombre de limitations à connaître lorsque vous affichez [`View`](xref:Xamarin.Forms.View) un dans la barre de navigation [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)d’un :

- Sur iOS, les vues placées dans la barre de navigation d’une `NavigationPage` apparaissent dans une position différente selon que les grands titres sont activés ou non. Pour plus d’informations sur l’activation des grands titres, consultez [Affichage de grands titres](~/xamarin-forms/platform/ios/page-large-title.md).
- Sur Android, vous ne pouvez placer des vues dans la barre de navigation d’une `NavigationPage` que dans les applications qui utilisent app-compat.
- Il n’est pas recommandé de placer des vues volumineuses et complexes [`ListView`](xref:Xamarin.Forms.ListView) , [`TableView`](xref:Xamarin.Forms.TableView)telles que et, dans la barre `NavigationPage`de navigation d’un.

## <a name="related-links"></a>Liens connexes

- [Navigation entre les pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)
- [PassingData (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
- [LoginFlow (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [TitleView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)
- [Vidéo sur la création d’un flux d’écran de connexion dans Xamarin.Forms](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
