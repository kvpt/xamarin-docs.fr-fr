---
title: Pages modales Xamarin.Forms
description: Xamarin.Forms assure la prise en charge des pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée. Cet article explique comment accéder à des pages modales.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 7a4c67f067b73873c3d1de4499abda2703217ddf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760826"
---
# <a name="xamarinforms-modal-pages"></a>Pages modales Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms prend en charge les pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée. Cet article explique comment accéder à des pages modales._

Cet article traite des sujets suivants :

- [Navigation](#Performing_Navigation) - Envoi (push) des pages vers la pile modale, dépilation des pages de la pile modale, désactivation du bouton Précédent et animation des transitions de page.
- [Passage de données durant la navigation](#Passing_Data_when_Navigating) - Passage de données via un constructeur de page et via `BindingContext`.

## <a name="overview"></a>Présentation

Une page modale peut correspondre à l’un des types [Page](~/xamarin-forms/user-interface/controls/pages.md) pris en charge par Xamarin.Forms. Pour afficher une page modale, l’application l’envoie vers la pile modale, où elle devient la page active, comme indiqué dans le diagramme suivant :

![](modal-images/pushing.png "Envoi (push) d’une page dans la pile modale")

Pour retourner à la page précédente, l’application dépile la page actuelle de la pile modale. La nouvelle page la plus haute de la pile devient la page active, comme indiqué dans le diagramme suivant :

![](modal-images/popping.png "Dépilation d’une page de la pile modale")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Navigation

Les méthodes de navigation modale sont exposées par la propriété [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) sur n’importe quel type dérivé [`Page`](xref:Xamarin.Forms.Page). Ces méthodes permettent d’[envoyer (push) des pages modales](#Pushing_Pages_to_the_Modal_Stack) vers la pile modale et de [dépiler des pages modales](#Popping_Pages_from_the_Modal_Stack) de la pile modale.

La propriété [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) expose également une propriété [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) à partir de laquelle les pages modales de la pile modale peuvent être obtenues. Toutefois, il n’existe aucun concept de manipulation de pile modale ni d’ajout à la page racine dans la navigation modale. En effet, ces opérations ne sont pas universellement prises en charge sur les plateformes sous-jacentes.

> [!NOTE]
> Aucune instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) n’est requise pour la navigation entre les pages modales.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Envoi de pages vers la pile modale

Pour accéder à `ModalPage`, il est nécessaire d’appeler la méthode [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) sur la propriété [`Navigation` ](xref:Xamarin.Forms.NavigableElement.Navigation) de la page active, comme indiqué dans l’exemple de code suivant :

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Ainsi, l’instance de `ModalPage` est envoyée vers la pile modale, où elle devient la page active, à condition qu’un élément ait été sélectionné dans [`ListView`](xref:Xamarin.Forms.ListView) sur l’instance de `MainPage`. L’instance de `ModalPage` est présentée dans les captures d’écran suivantes :

![](modal-images/modalpage.png "Exemple de page modale")

Quand [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) est appelé, les événements suivants se produisent :

- Pour la page qui appelle `PushModalAsync`, la substitution de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) est appelée, à condition que la plateforme sous-jacente ne soit pas de type Android.
- Pour la page cible de la navigation, la substitution de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) est appelée.
- La tâche `PushAsync` est effectuée.

Toutefois, l’ordre précis dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [Chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre Xamarin.Forms de Charles Petzold.

> [!NOTE]
> Les appels des substitutions [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) et [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ne peuvent pas être considérés comme des indications garanties de la navigation entre les pages. Par exemple, sur iOS, la substitution de `OnDisappearing` est appelée dans la page active quand l’application se termine.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Dépilation de pages de la pile modale

Vous pouvez dépiler la page active de la pile modale en appuyant sur le bouton *Précédent* de l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran.

Pour revenir par programmation à la page d’origine, l’instance de `ModalPage` doit appeler la méthode [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), comme illustré dans l’exemple de code suivant :

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Cela provoque la suppression de l’instance de `ModalPage` de la pile modale. Ainsi, la nouvelle page située tout en haut de la pile devient la page active. Quand [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) est appelé, les événements suivants se produisent :

- Pour la page qui appelle `PopModalAsync`, la substitution de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) est appelée.
- Pour la page retournée, la substitution de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) est appelée, à condition que la plateforme sous-jacente ne soit pas de type Android.
- La tâche `PopModalAsync` est retournée.

Toutefois, l’ordre précis dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [Chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre Xamarin.Forms de Charles Petzold.

### <a name="disabling-the-back-button"></a>Désactivation du bouton Précédent

Sur Android, l’utilisateur peut toujours retourner à la page précédente en appuyant sur le bouton *Précédent* standard de l’appareil. Si la page modale impose à l’utilisateur d’exécuter une tâche autonome avant de quitter la page, l’application doit désactiver le bouton *Précédent*. Pour ce faire, substituez la méthode [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) dans la page modale. Pour plus d’informations, consultez le [Chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre Xamarin.Forms de Charles Petzold.

### <a name="animating-page-transitions"></a>Animation de transitions de page

La propriété [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de chaque page fournit également des méthodes d’envoi et de dépilation substituées qui incluent un paramètre `boolean` contrôlant l’affichage d’une animation de page durant la navigation, comme indiqué dans l’exemple de code suivant :

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

Si vous affectez au paramètre `boolean` la valeur `false`, l’animation de transition de page est désactivée. En revanche, si vous affectez au paramètre la valeur `true` l’animation de transition de page est activée, à condition qu’elle soit prise en charge par la plateforme sous-jacente. Toutefois, les méthodes d’envoi et de dépilation qui n’ont pas ce paramètre activent l’animation par défaut.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passage des données durant la navigation

Il est parfois nécessaire qu’une page passe des données à une autre page durant la navigation. Il existe deux techniques pour y parvenir : passer les données via un constructeur de page et définir le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la nouvelle page en fonction des données. Chacune de ces techniques va être abordée à tour de rôle.

### <a name="passing-data-through-a-page-constructor"></a>Passage des données via un constructeur de page

La technique la plus simple pour passer des données à une autre page durant la navigation consiste à utiliser un paramètre de constructeur de page, comme indiqué dans l’exemple de code suivant :

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Ce code crée une instance de `MainPage` en passant la date et l’heure actuelles au format ISO8601.

L’instance de `MainPage` reçoit les données via un paramètre de constructeur, comme indiqué dans l’exemple de code suivant :

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Les données sont ensuite affichées dans la page via la définition de la propriété [`Label.Text`](xref:Xamarin.Forms.Label.Text).

### <a name="passing-data-through-a-bindingcontext"></a>Passage des données via un BindingContext

Il existe une autre approche pour passer des données à une autre page durant la navigation. Elle consiste à affecter les données au [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la nouvelle page, comme indiqué dans l’exemple de code suivant :

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Ce code affecte au [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de l’instance`DetailPage` l’instance de `Contact`, puis accède à `DetailPage`.

`DetailPage` utilise ensuite la liaison de données pour afficher les données de l’instance de `Contact`, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

L’exemple de code suivant montre comment effectuer la liaison de données en C# :

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
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
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

Les données sont ensuite affichées dans la page par une série de contrôles [`Label`](xref:Xamarin.Forms.Label).

Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Récapitulatif

Cet article a montré comment accéder aux pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée.

## <a name="related-links"></a>Liens associés

- [Navigation de page](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)
- [PassingData (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
