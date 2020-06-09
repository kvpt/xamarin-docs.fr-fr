---
titre : « Xamarin.Forms pages modales » Description : « Xamarin.Forms fournit la prise en charge des pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée. Cet article montre comment naviguer vers des pages modales.
ms. Prod : xamarin ms. AssetID : 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 12/01/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-modal-pages"></a>Xamarin.FormsPages modales

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin. Forms prend en charge les pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome qui ne peut pas être quittée jusqu’à ce que la tâche soit terminée ou annulée. Cet article montre comment naviguer vers des pages modales._

Cet article aborde les thèmes suivants :

- [Navigation](#performing-navigation) - Envoi (push) des pages vers la pile modale, dépilation des pages de la pile modale, désactivation du bouton Précédent et animation des transitions de page.
- [Passage de données durant la navigation](#passing-data-when-navigating) - Passage de données via un constructeur de page et via `BindingContext`.

## <a name="overview"></a>Vue d’ensemble

Une page modale peut être n’importe quel type de [page](~/xamarin-forms/user-interface/controls/pages.md) pris en charge par Xamarin.Forms . Pour afficher une page modale, l’application l’envoie vers la pile modale, où elle devient la page active, comme indiqué dans le diagramme suivant :

![](modal-images/pushing.png "Pushing a Page to the Modal Stack")

Pour retourner à la page précédente, l’application dépile la page actuelle de la pile modale. La nouvelle page la plus haute de la pile devient la page active, comme indiqué dans le diagramme suivant :

![](modal-images/popping.png "Popping a Page from the Modal Stack")

## <a name="performing-navigation"></a>Navigation

Les méthodes de navigation modales sont exposées par la [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété sur tous les [`Page`](xref:Xamarin.Forms.Page) types dérivés. Ces méthodes permettent d’[envoyer (push) des pages modales](#pushing-pages-to-the-modal-stack) vers la pile modale et de [dépiler des pages modales](#popping-pages-from-the-modal-stack) de la pile modale.

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété expose également une [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) propriété à partir de laquelle les pages modales de la pile modale peuvent être obtenues. Toutefois, il n’existe aucun concept de manipulation de pile modale ni d’ajout à la page racine dans la navigation modale. En effet, ces opérations ne sont pas universellement prises en charge sur les plateformes sous-jacentes.

> [!NOTE]
> Une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance n’est pas requise pour effectuer une navigation de page modale.

### <a name="pushing-pages-to-the-modal-stack"></a>Envoi de pages vers la pile modale

Pour naviguer jusqu’au `ModalPage` , il est nécessaire d’appeler la [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) méthode sur la [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de la page actuelle, comme illustré dans l’exemple de code suivant :

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

Cela entraîne le `ModalPage` push de l’instance sur la pile modale, où elle devient la page active, à condition qu’un élément ait été sélectionné dans le [`ListView`](xref:Xamarin.Forms.ListView) sur l' `MainPage` instance. L’instance de `ModalPage` est présentée dans les captures d’écran suivantes :

![](modal-images/modalpage.png "Modal Page Example")

Lorsque [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) est appelé, les événements suivants se produisent :

- La page appelante `PushModalAsync` a sa [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) substitution appelée, à condition que la plateforme sous-jacente ne soit pas Android.
- La page cible de la navigation a sa [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substitution appelée.
- La tâche `PushAsync` est terminée.

Toutefois, l’ordre précis dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre de Charles Petzold Xamarin.Forms .

> [!NOTE]
> Les appels aux [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substitutions et ne peuvent pas être traités comme des indications garanties de la navigation entre les pages. Par exemple, sur iOS, la substitution `OnDisappearing` est appelée sur la page active quand l’application se ferme.

### <a name="popping-pages-from-the-modal-stack"></a>Dépilation de pages de la pile modale

Vous pouvez dépiler la page active de la pile modale en appuyant sur le bouton *Précédent* de l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran.

Pour revenir par programmation à la page d’origine, l' `ModalPage` instance doit appeler la [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) méthode, comme illustré dans l’exemple de code suivant :

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Cela provoque la suppression de l’instance de `ModalPage` de la pile modale. Ainsi, la nouvelle page située tout en haut de la pile devient la page active. Lorsque [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) est appelé, les événements suivants se produisent :

- La page appelante `PopModalAsync` a sa [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) substitution appelée.
- La page retournée a sa valeur [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) override appelée, à condition que la plateforme sous-jacente ne soit pas Android.
- La tâche `PopModalAsync` est terminée.

Toutefois, l’ordre précis dans lequel ces événements se produisent dépend de la plateforme. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre de Charles Petzold Xamarin.Forms .

### <a name="disabling-the-back-button"></a>Désactivation du bouton Précédent

Sur Android, l’utilisateur peut toujours retourner à la page précédente en appuyant sur le bouton *Précédent* standard de l’appareil. Si la page modale impose à l’utilisateur d’exécuter une tâche autonome avant de quitter la page, l’application doit désactiver le bouton *Précédent*. Pour ce faire, vous pouvez substituer la [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) méthode sur la page modale. Pour plus d’informations, consultez le [chapitre 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) du livre de Charles Petzold Xamarin.Forms .

### <a name="animating-page-transitions"></a>Animation des transitions de page

La [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de chaque page fournit également des méthodes push et pop substituées qui incluent un `boolean` paramètre qui contrôle s’il faut afficher une animation de page pendant la navigation, comme illustré dans l’exemple de code suivant :

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

## <a name="passing-data-when-navigating"></a>Passage des données durant la navigation

Il est parfois nécessaire pour une page de transmettre des données à une autre page lors de la navigation. Pour ce faire, deux techniques consiste à passer des données via un constructeur de page et à définir les données de la nouvelle page [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Nous allons maintenant examiner chacune de ces techniques.

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

Les données sont ensuite affichées sur la page en définissant la [`Label.Text`](xref:Xamarin.Forms.Label.Text) propriété.

### <a name="passing-data-through-a-bindingcontext"></a>Passage des données via un BindingContext

Une autre approche pour passer des données à une autre page pendant la navigation consiste à définir la nouvelle page [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) sur les données, comme illustré dans l’exemple de code suivant :

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

Ce code affecte l' [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `DetailPage` instance à l’instance `Contact` , puis accède à `DetailPage` .

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

Les données sont ensuite affichées sur la page par une série de [`Label`](xref:Xamarin.Forms.Label) contrôles.

Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Résumé

Cet article a montré comment accéder aux pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée.

## <a name="related-links"></a>Liens connexes

- [Navigation entre les pages](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)
- [PassingData (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
