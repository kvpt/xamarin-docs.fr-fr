---
title: Xamarin. Forms dans les projets natifs Xamarin
description: Cet article explique comment utiliser les pages dérivées de ContentPage qui sont directement ajoutées aux projets natifs Xamarin et comment naviguer entre eux.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 0c84b844455b8a792b8cbe2f4dac97097e5ebd97
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "69621059"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin. Forms dans les projets natifs Xamarin

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

En règle générale, une application Xamarin. Forms comprend une ou plusieurs pages qui dérivent de [`ContentPage`](xref:Xamarin.Forms.ContentPage), et ces pages sont partagées par toutes les plateformes d’un projet de bibliothèque .NET standard ou d’un projet partagé. Toutefois, les formulaires natifs permettent l’ajout des pages dérivées de `ContentPage` directement aux applications natives Xamarin. iOS, Xamarin. Android et UWP. Comparé au fait que le projet natif consomme `ContentPage` pages dérivées d’un projet de bibliothèque .NET Standard ou d’un projet partagé, l’avantage d’ajouter des pages directement aux projets natifs est que les pages peuvent être étendues avec des vues natives. Les vues natives peuvent ensuite être nommées en XAML avec `x:Name` et référencées à partir du code-behind. Pour plus d’informations sur les vues natives, consultez [affichages natifs](~/xamarin-forms/platform/native-views/index.md).

Le processus permettant de consommer une page dérivée d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage). Forms Xamarin dans un projet natif est le suivant :

1. Ajoutez le package NuGet Xamarin. Forms au projet natif.
1. Ajoutez la page dérivée de [`ContentPage`](xref:Xamarin.Forms.ContentPage), ainsi que toutes les dépendances, au projet natif.
1. Appelez la méthode `Forms.Init`.
1. Construisez une instance de la page dérivée de [`ContentPage`](xref:Xamarin.Forms.ContentPage)et convertissez-la en type natif approprié à l’aide de l’une des méthodes d’extension suivantes : `CreateViewController` pour iOS, `CreateSupportFragment` pour Android ou `CreateFrameworkElement` pour UWP.
1. Accédez à la représentation de type natif de la page dérivée de [`ContentPage`](xref:Xamarin.Forms.ContentPage)à l’aide de l’API de navigation native.

Xamarin. Forms doit être initialisé en appelant la méthode `Forms.Init` avant qu’un projet natif puisse construire une page dérivée de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Le choix du moment le plus approprié dépend du moment où il est le plus pratique dans le workflow de votre application : il peut être effectué au démarrage de l’application ou juste avant la construction de la page dérivée de `ContentPage`. Dans cet article, et les exemples d’applications qui l’accompagnent, la méthode `Forms.Init` est appelée au démarrage de l’application.

> [!NOTE]
> L’exemple de solution d’application **NativeForms** ne contient pas de projets Xamarin. Forms. Au lieu de cela, il se compose d’un projet Xamarin. iOS, d’un projet Xamarin. Android et d’un projet UWP. Chaque projet est un projet natif qui utilise des formulaires natifs pour consommer des pages dérivées de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Toutefois, il n’y a aucune raison pour laquelle les projets natifs ne pouvaient pas consommer des pages dérivées d' `ContentPage` à partir d’un projet de bibliothèque .NET Standard ou d’un projet partagé.

Lorsque vous utilisez des formulaires natifs, les fonctionnalités Xamarin. Forms, telles que [`DependencyService`](xref:Xamarin.Forms.DependencyService), [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)et le moteur de liaison de données, fonctionnent toujours. Toutefois, la navigation entre les pages doit être effectuée à l’aide de l’API de navigation native.

## <a name="ios"></a>iOS

Sur iOS, la `FinishedLaunching` remplacement dans la classe `AppDelegate` est généralement l’endroit où effectuer des tâches liées au démarrage de l’application. Elle est appelée après le lancement de l’application, et est généralement remplacée pour configurer la fenêtre principale et le contrôleur d’affichage. L’exemple de code suivant montre la classe `AppDelegate` dans l’exemple d’application :

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;
    UIWindow _window;
    AppNavigationController _navigation;

    public static string FolderPath { get; private set; }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
        UIViewController mainPage = new NotesPage().CreateViewController();
        mainPage.Title = "Notes";

        _navigation = new AppNavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    // ...
}
```

La méthode `FinishedLaunching` effectue les tâches suivantes :

- Xamarin. Forms est initialisé en appelant la méthode `Forms.Init`.
- Une référence à la classe `AppDelegate` est stockée dans le champ `Instance` `static`. Il s’agit de fournir un mécanisme permettant à d’autres classes d’appeler des méthodes définies dans la classe `AppDelegate`.
- Le `UIWindow`, qui est le conteneur principal pour les vues dans les applications iOS natives, est créé.
- La propriété `FolderPath` est initialisée sur le chemin d’accès de l’appareil où les données de note seront stockées.
- La classe `NotesPage`, qui est une page dérivée du [`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin. Forms définie en XAML, est construite et convertie en `UIViewController` à l’aide de la méthode d’extension `CreateViewController`.
- La propriété `Title` du `UIViewController` est définie, qui sera affichée sur la `UINavigationBar`.
- Un `AppNavigationController` est créé pour la gestion de la navigation hiérarchique. Il s’agit d’une classe de contrôleur de navigation personnalisée, qui dérive de `UINavigationController`. L’objet `AppNavigationController` gère une pile de contrôleurs d’affichage, et le `UIViewController` passé dans le constructeur sera présenté initialement lors du chargement de l' `AppNavigationController`.
- L’objet `AppNavigationController` est défini en tant que `UIViewController` de niveau supérieur pour le `UIWindow` et le `UIWindow` est défini en tant que fenêtre clé pour l’application et est rendu visible.

Une fois la méthode `FinishedLaunching` exécutée, l’interface utilisateur définie dans la classe Xamarin. Forms `NotesPage` s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. iOS qui utilise une interface utilisateur définie en XAML](native-forms-images/ios-notespage.png "Application Xamarin. iOS avec une interface utilisateur XAML")](native-forms-images/ios-notespage-large.png#lightbox "Application Xamarin. iOS avec une interface utilisateur XAML")

L’interaction avec l’interface utilisateur, par exemple en appuyant sur le [`Button`](xref:Xamarin.Forms.Button) **+** , entraîne l’exécution du gestionnaire d’événements suivant dans le `NotesPage` qui s’exécute :

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le champ `static` `AppDelegate.Instance` permet d’appeler la méthode `AppDelegate.NavigateToNoteEntryPage`, qui est illustrée dans l’exemple de code suivant :

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    var noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(noteEntryPage, true);
}
```

La méthode `NavigateToNoteEntryPage` convertit la page dérivée de la [`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin. Forms en un `UIViewController` avec la méthode d’extension `CreateViewController` et définit la propriété `Title` de l' `UIViewController`. Le `UIViewController` est ensuite envoyé sur `AppNavigationController` par la méthode `PushViewController`. Par conséquent, l’interface utilisateur définie dans la classe Xamarin. Forms `NoteEntryPage` s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. iOS qui utilise une interface utilisateur définie en XAML](native-forms-images/ios-noteentrypage.png "Application Xamarin. iOS avec une interface utilisateur XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "Application Xamarin. iOS avec une interface utilisateur XAML")

Lorsque le `NoteEntryPage` s’affiche, l’arrière-plan de navigation affiche le `UIViewController` de la classe `NoteEntryPage` à partir du `AppNavigationController`, en retournant l’utilisateur à la `UIViewController` de la classe `NotesPage`. Toutefois, le décodeur d’une `UIViewController` à partir de la pile de navigation Native iOS ne supprime pas automatiquement les `UIViewController` et les `Page` objet attachés. Par conséquent, la classe `AppNavigationController` substitue la méthode `PopViewController` pour supprimer les contrôleurs d’affichage en vue de la navigation vers l’arrière :

```csharp
public class AppNavigationController : UINavigationController
{
    //...
    public override UIViewController PopViewController(bool animated)
    {
        UIViewController topView = TopViewController;
        if (topView != null)
        {
            // Dispose of ViewController on back navigation.
            topView.Dispose();
        }
        return base.PopViewController(animated);
    }
}
```

La `PopViewController` substitution appelle la méthode `Dispose` sur l’objet `UIViewController` qui a été dépilée à partir de la pile de navigation Native iOS. Si vous n’effectuez pas cette opération, le `UIViewController` et l’objet `Page` attachés sont orphelins.

> [!IMPORTANT]
> Les objets orphelins ne peuvent pas être récupérés par le garbage collector, ce qui entraîne une fuite de mémoire.

## <a name="android"></a>Android

Sur Android, la `OnCreate` remplacement dans la classe `MainActivity` est généralement l’endroit où effectuer des tâches liées au démarrage de l’application. L’exemple de code suivant montre la classe `MainActivity` dans l’exemple d’application :

```csharp
public class MainActivity : AppCompatActivity
{
    public static string FolderPath { get; private set; }

    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        Android.Support.V4.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

La méthode `OnCreate` effectue les tâches suivantes :

- Xamarin. Forms est initialisé en appelant la méthode `Forms.Init`.
- Une référence à la classe `MainActivity` est stockée dans le champ `Instance` `static`. Il s’agit de fournir un mécanisme permettant à d’autres classes d’appeler des méthodes définies dans la classe `MainActivity`.
- Le contenu `Activity` est défini à partir d’une ressource de disposition. Dans l’exemple d’application, la disposition se compose d’un `LinearLayout` qui contient un `Toolbar`, et d’un `FrameLayout` qui agit comme un conteneur de fragments.
- La `Toolbar` est récupérée et définie comme barre d’action du `Activity` et le titre de la barre d’action est défini.
- La propriété `FolderPath` est initialisée sur le chemin d’accès de l’appareil où les données de note seront stockées.
- La classe `NotesPage`, qui est une page dérivée du [`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin. Forms définie en XAML, est construite et convertie en `Fragment` à l’aide de la méthode d’extension `CreateSupportFragment`.
- La classe `SupportFragmentManager` crée et valide une transaction qui remplace l’instance `FrameLayout` par le `Fragment` pour la classe `NotesPage`.

Pour plus d’informations sur les fragments, consultez [fragments](~/android/platform/fragments/index.md).

Une fois la méthode `OnCreate` exécutée, l’interface utilisateur définie dans la classe Xamarin. Forms `NotesPage` s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. Android qui utilise une interface utilisateur définie en XAML](native-forms-images/android-notespage.png "Application Xamarin. Android avec une interface utilisateur XAML")](native-forms-images/android-notespage-large.png#lightbox "Application Xamarin. Android avec une interface utilisateur XAML")

L’interaction avec l’interface utilisateur, par exemple en appuyant sur le [`Button`](xref:Xamarin.Forms.Button) **+** , entraîne l’exécution du gestionnaire d’événements suivant dans le `NotesPage` qui s’exécute :

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le champ `static` `MainActivity.Instance` permet d’appeler la méthode `MainActivity.NavigateToNoteEntryPage`, qui est illustrée dans l’exemple de code suivant :

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    Android.Support.V4.App.Fragment noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryPage)
        .Commit();
}
```

La méthode `NavigateToNoteEntryPage` convertit la page dérivée de la [`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin. Forms en `Fragment` à l’aide de la méthode d’extension `CreateSupportFragment`, et ajoute la `Fragment` à la pile de fragments précédente. Par conséquent, l’interface utilisateur définie dans Xamarin. Forms `NoteEntryPage` s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. Android qui utilise une interface utilisateur définie en XAML](native-forms-images/android-noteentrypage.png "Application Xamarin. Android avec une interface utilisateur XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "Application Xamarin. Android avec une interface utilisateur XAML")

Lorsque le `NoteEntryPage` s’affiche, le fait de cliquer sur la flèche précédent permet de dépiler les `Fragment` de la `NoteEntryPage` à partir de la pile de fragments en arrière, en retournant l’utilisateur à la `Fragment` pour la classe `NotesPage`.

### <a name="enable-back-navigation-support"></a>Activer la prise en charge de la navigation vers l’arrière

La classe `SupportFragmentManager` a un événement `BackStackChanged` qui se déclenche chaque fois que le contenu de la pile de fragments précédente change. La méthode `OnCreate` de la classe `MainActivity` contient un gestionnaire d’événements anonymes pour cet événement :

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Ce gestionnaire d’événements affiche un bouton précédent sur la barre d’action, à condition qu’il y ait une ou plusieurs instances `Fragment` sur la pile de retour de fragment. La réponse en appuyant sur le bouton précédent est gérée par le `OnOptionsItemSelected` remplacement :

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && SupportFragmentManager.BackStackEntryCount > 0)
    {
        SupportFragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

Le `OnOptionsItemSelected` remplacement est appelé chaque fois qu’un élément du menu options est sélectionné. Cette implémentation dépile le fragment actuel de la pile de fragments en arrière, à condition que le bouton précédent ait été sélectionné et qu’il y ait une ou plusieurs instances `Fragment` sur la pile de fragments en arrière.

### <a name="multiple-activities"></a>Plusieurs activités

Lorsqu’une application est composée de plusieurs activités, les pages dérivées d' [`ContentPage`](xref:Xamarin.Forms.ContentPage)peuvent être incorporées dans chacune des activités. Dans ce scénario, la méthode `Forms.Init` n’a besoin d’être appelée que dans le `OnCreate` substitution de la première `Activity` qui incorpore un `ContentPage` Xamarin. Forms. Toutefois, cela a l’impact suivant :

- La valeur de `Xamarin.Forms.Color.Accent` est extraite du `Activity` qui a appelé la méthode `Forms.Init`.
- La valeur de `Xamarin.Forms.Application.Current` sera associée à la `Activity` qui a appelé la méthode `Forms.Init`.

### <a name="choose-a-file"></a>Choisir un fichier

Lors de l’incorporation d’une page dérivée d' [`ContentPage`](xref:Xamarin.Forms.ContentPage)qui utilise un [`WebView`](xref:Xamarin.Forms.WebView) qui doit prendre en charge un bouton « Choose file » html, le `Activity` doit substituer la méthode `OnActivityResult` :

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Sur UWP, la classe de `App` native est généralement l’endroit où effectuer des tâches liées au démarrage de l’application. Xamarin. Forms est généralement initialisé, dans les applications UWP Xamarin. Forms, dans la `OnLaunched` substitution de la classe `App` native, pour passer l’argument `LaunchActivatedEventArgs` à la méthode `Forms.Init`. Pour cette raison, les applications UWP natives qui consomment une page dérivée de [`ContentPage`](xref:Xamarin.Forms.ContentPage) Xamarin.Forms peuvent facilement appeler la méthode `Forms.Init` à partir de la méthode `App.OnLaunched`.

Par défaut, la classe de `App` Native lance la classe `MainPage` en tant que première page de l’application. L’exemple de code suivant montre la classe `MainPage` dans l’exemple d’application :

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        this.Content = new Notes.UWP.Views.NotesPage().CreateFrameworkElement();
    }
    ...
}
```

Le constructeur `MainPage` effectue les tâches suivantes :

- La mise en cache est activée pour la page, de sorte qu’une nouvelle `MainPage` n’est pas construite quand un utilisateur accède à la page.
- Une référence à la classe `MainPage` est stockée dans le champ `Instance` `static`. Il s’agit de fournir un mécanisme permettant à d’autres classes d’appeler des méthodes définies dans la classe `MainPage`.
- La propriété `FolderPath` est initialisée sur le chemin d’accès de l’appareil où les données de note seront stockées.
- La classe `NotesPage`, qui est une page dérivée du [`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin. Forms définie en XAML, est construite et convertie en `FrameworkElement` à l’aide de la méthode d’extension `CreateFrameworkElement`, puis définie en tant que contenu de la classe `MainPage`.

Une fois le constructeur `MainPage` exécuté, l’interface utilisateur définie dans la classe Xamarin. Forms `NotesPage` s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application UWP qui utilise une interface utilisateur définie avec le XAML Xamarin. Forms](native-forms-images/uwp-notespage.png "Application UWP avec une interface utilisateur XAML Xamarin. Forms")](native-forms-images/uwp-notespage-large.png#lightbox "Application UWP avec une interface utilisateur XAML Xamarin. Forms")

L’interaction avec l’interface utilisateur, par exemple en appuyant sur le [`Button`](xref:Xamarin.Forms.Button) **+** , entraîne l’exécution du gestionnaire d’événements suivant dans le `NotesPage` qui s’exécute :

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le champ `static` `MainPage.Instance` permet d’appeler la méthode `MainPage.NavigateToNoteEntryPage`, qui est illustrée dans l’exemple de code suivant :

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    this.Frame.Navigate(new NoteEntryPage
    {
        BindingContext = note
    });
}
```

La navigation dans UWP est généralement effectuée à l’aide de la méthode `Frame.Navigate`, qui accepte un argument `Page`. Xamarin. Forms définit une méthode d’extension `Frame.Navigate` qui prend une instance de page dérivée de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Par conséquent, lorsque la méthode `NavigateToNoteEntryPage` s’exécute, l’interface utilisateur définie dans le `NoteEntryPage` Xamarin. Forms s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application UWP qui utilise une interface utilisateur définie avec le XAML Xamarin. Forms](native-forms-images/uwp-noteentrypage.png "Application UWP avec une interface utilisateur XAML Xamarin. Forms")](native-forms-images/uwp-noteentrypage-large.png#lightbox "Application UWP avec une interface utilisateur XAML Xamarin. Forms")

Lorsque le `NoteEntryPage` s’affiche, le fait de cliquer sur la flèche précédent permet de dépiler les `FrameworkElement` de la `NoteEntryPage` à partir de la pile de retour dans l’application, en retournant l’utilisateur à la `FrameworkElement` pour la classe `NotesPage`.

### <a name="enable-back-navigation-support"></a>Activer la prise en charge de la navigation vers l’arrière

Sur UWP, les applications doivent activer la navigation vers l’arrière pour tous les boutons de retour matériel et logiciel, sur différents facteurs de forme de périphérique. Pour ce faire, vous devez inscrire un gestionnaire d’événements pour l’événement `BackRequested`, qui peut être effectué dans la méthode `OnLaunched` de la classe `App` Native :

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

Lorsque l’application est lancée, la méthode `GetForCurrentView` récupère l’objet `SystemNavigationManager` associé à la vue actuelle, puis inscrit un gestionnaire d’événements pour l’événement `BackRequested`. L’application reçoit uniquement cet événement s’il s’agit de l’application de premier plan et, en réponse, appelle le gestionnaire d’événements `OnBackRequested` :

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

Le gestionnaire d’événements `OnBackRequested` appelle la méthode `GoBack` sur le frame racine de l’application et définit la propriété `BackRequestedEventArgs.Handled` sur `true` pour marquer l’événement comme étant géré. Si vous ne Marquez pas l’événement comme géré, vous risquez d’ignorer l’événement.

L’application choisit s’il faut afficher un bouton précédent sur la barre de titre. Pour ce faire, affectez à la propriété `AppViewBackButtonVisibility` l’une des valeurs d’énumération `AppViewBackButtonVisibility` :

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Le gestionnaire d’événements `OnNavigated`, qui est exécuté en réponse au déclenchement de l’événement `Navigated`, met à jour la visibilité du bouton précédent de la barre de titre lorsque la navigation entre les pages se produit. Cela permet de s’assurer que le bouton précédent de la barre de titre est visible si la pile de retour dans l’application n’est pas vide ou est supprimée de la barre de titre si la pile de retour dans l’application est vide.

Pour plus d’informations sur la prise en charge de la navigation arrière sur UWP, consultez [historique de navigation et navigation vers l’arrière pour les applications UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Liens connexes

- [NativeForms (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Vues natives](~/xamarin-forms/platform/native-views/index.md)
