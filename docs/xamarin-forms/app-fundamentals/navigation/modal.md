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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760826"
---
# <a name="xamarinforms-modal-pages"></a>Pages modales Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms fournit un support pour les pages modales. Une page modale encourage les utilisateurs à accomplir une tâche autonome qui ne peut pas être naviguée loin de jusqu’à ce que la tâche soit terminée ou annulée. Cet article montre comment naviguer vers les pages modales._

Cet article aborde les thèmes suivants :

- [Navigation](#Performing_Navigation) - Envoi (push) des pages vers la pile modale, dépilation des pages de la pile modale, désactivation du bouton Précédent et animation des transitions de page.
- [Passage de données durant la navigation](#Passing_Data_when_Navigating) - Passage de données via un constructeur de page et via `BindingContext`.

## <a name="overview"></a>Vue d’ensemble

Une page modale peut correspondre à l’un des types [Page](~/xamarin-forms/user-interface/controls/pages.md) pris en charge par Xamarin.Forms. Pour afficher une page modale, l’application l’envoie vers la pile modale, où elle devient la page active, comme indiqué dans le diagramme suivant :

![](modal-images/pushing.png "Pushing a Page to the Modal Stack")

Pour retourner à la page précédente, l’application dépile la page actuelle de la pile modale. La nouvelle page la plus haute de la pile devient la page active, comme indiqué dans le diagramme suivant :

![](modal-images/popping.png "Popping a Page from the Modal Stack")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Navigation

Les méthodes de navigation [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) modales [`Page`](xref:Xamarin.Forms.Page) sont exposées par la propriété sur tous les types dérivés. Ces méthodes permettent d’[envoyer (push) des pages modales](#Pushing_Pages_to_the_Modal_Stack) vers la pile modale et de [dépiler des pages modales](#Popping_Pages_from_the_Modal_Stack) de la pile modale.

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété expose [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) également une propriété à partir de laquelle les pages modales de la pile modale peuvent être obtenues. Toutefois, il n’existe aucun concept de manipulation de pile modale ni d’ajout à la page racine dans la navigation modale. En effet, ces opérations ne sont pas universellement prises en charge sur les plateformes sous-jacentes.

> [!NOTE]
> Un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) cas n’est pas nécessaire pour effectuer la navigation de page modale.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Envoi de pages vers la pile modale

Pour naviguer `ModalPage` vers le, il [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) est [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) nécessaire d’invoquer la méthode sur la propriété de la page actuelle, comme démontré dans l’exemple de code suivant:

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

Cela provoque `ModalPage` l’instance d’être poussé sur la pile modale, où il [`ListView`](xref:Xamarin.Forms.ListView) devient `MainPage` la page active, à condition qu’un élément a été sélectionné dans l’instance. L’instance de `ModalPage` est présentée dans les captures d’écran suivantes :

![](modal-images/modalpage.png "Modal Page Example")

Lorsqu’elle [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) est invoquée, les événements suivants se produisent :

- L’appel `PushModalAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) page a son remplacement invoqué, à condition que la plate-forme sous-jacente n’est pas Android.
- La page en cours [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de navigation a son remplacement invoqué.
- La tâche `PushAsync` est terminée.

Toutefois, l’ordre précis dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, voir [le chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre Xamarin.Forms de Charles Petzold.

> [!NOTE]
> Les appels [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) vers les dérogations et les remplacements ne peuvent pas être traités comme des indications garanties de navigation de page. Par exemple, sur iOS, la substitution `OnDisappearing` est appelée sur la page active quand l’application se ferme.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Dépilation de pages de la pile modale

Vous pouvez dépiler la page active de la pile modale en appuyant sur le bouton *Précédent* de l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran.

Pour revenir sur le plan programmatique à la page originale, l’instance `ModalPage` doit invoquer la [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) méthode, comme le démontre l’exemple de code suivant :

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Cela provoque la suppression de l’instance de `ModalPage` de la pile modale. Ainsi, la nouvelle page située tout en haut de la pile devient la page active. Lorsqu’elle [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) est invoquée, les événements suivants se produisent :

- L’appel `PopModalAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) page a son remplacement invoqué.
- La page étant retournée [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) à a son remplacement invoqué, à condition que la plate-forme sous-jacente n’est pas Android.
- La tâche `PopModalAsync` est terminée.

Toutefois, l’ordre précis dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, voir [le chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre Xamarin.Forms de Charles Petzold.

### <a name="disabling-the-back-button"></a>Désactivation du bouton Précédent

Sur Android, l’utilisateur peut toujours retourner à la page précédente en appuyant sur le bouton *Précédent* standard de l’appareil. Si la page modale impose à l’utilisateur d’exécuter une tâche autonome avant de quitter la page, l’application doit désactiver le bouton *Précédent*. Cela peut être accompli en [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) l’emportent sur la méthode sur la page modale. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

### <a name="animating-page-transitions"></a>Animation des transitions de page

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de chaque page fournit également des méthodes `boolean` de poussée et de pop qui incluent un paramètre qui contrôle s’il y a à afficher une animation de page pendant la navigation, comme indiqué dans l’exemple de code suivant :

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

Il est parfois nécessaire pour une page de transmettre des données à une autre page lors de la navigation. Deux techniques pour y parvenir sont en passant des données à travers [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) un constructeur de pages, et en définissant la nouvelle page aux données. Nous allons maintenant examiner chacune de ces techniques.

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

Les données sont ensuite affichées [`Label.Text`](xref:Xamarin.Forms.Label.Text) sur la page en définissant la propriété.

### <a name="passing-data-through-a-bindingcontext"></a>Passage des données via un BindingContext

Une approche alternative pour transmettre des données à une autre [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) page pendant la navigation est de définir la nouvelle page aux données, comme indiqué dans l’exemple de code suivant :

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

Ce code [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) définit `DetailPage` l’instance `Contact` à l’instance, `DetailPage`puis navigue vers le .

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

Les données sont ensuite affichées sur [`Label`](xref:Xamarin.Forms.Label) la page par une série de contrôles.

Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Récapitulatif

Cet article a montré comment accéder aux pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée.

## <a name="related-links"></a>Liens connexes

- [Page Navigation](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)
- [PassingData (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
