---
title: Xamarin.Forms dans les projets Xamarin Native
description: Cet article explique comment utiliser les pages ContentPage dérivées qui sont ajoutés directement à Xamarin les projets natifs et comment naviguer entre eux.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/03/2019
ms.openlocfilehash: dad68f5daaf3a4c85f130ee219472b867163fd71
ms.sourcegitcommit: 9a2a21974d35353c3765eb683ef2fd7161c1d94a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68329993"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms dans les projets Xamarin Native

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)

En règle générale, une application Xamarin.Forms inclut une ou plusieurs pages qui dérivent [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), et ces pages sont partagées par toutes les plateformes dans un projet partagé ou un projet de bibliothèque .NET Standard. Toutefois, les formulaires natifs permet `ContentPage`-dérivée des pages à ajouter directement à des applications Xamarin.iOS, Xamarin.Android et UWP natives. Par rapport à avoir le projet natif consommer `ContentPage`-pages dérivées à partir d’un projet de bibliothèque .NET Standard ou d’un projet partagé, l’avantage de l’ajout de pages directement vers les projets natifs est que les pages peuvent être étendues avec les vues natives. Vues natives peuvent ensuite être définis dans XAML avec `x:Name` et référencés à partir du code-behind. Pour plus d’informations sur les vues natives, consultez [vues natives](~/xamarin-forms/platform/native-views/index.md).

Le processus permettant d’utiliser un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-page dérivée dans un projet natif est comme suit :

1. Ajoutez le package Xamarin.Forms NuGet au projet natif.
1. Ajouter le [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page et toutes ses éventuelles dépendances au projet natif.
1. Appelez la méthode `Forms.Init`.
1. Construire une instance de la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-page dérivée et la convertir en type natif approprié à l’aide d’une des méthodes d’extension suivantes : `CreateViewController` pour iOS, `CreateSupportFragment` pour Android, ou `CreateFrameworkElement` pour UWP.
1. Accédez à la représentation sous forme de type natif de la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page à l’aide de l’API native de la navigation.

Xamarin.Forms doit être initialisé en appelant le `Forms.Init` méthode avant un projet natif peut construire un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-page dérivée. Quand cela principalement le choix dépend lorsqu’il est plus pratique dans votre flux d’application : il peut être effectué au démarrage de l’application, ou juste avant le `ContentPage`-page dérivée est construit. Dans cet article et les exemples d’applications qui accompagne cet article, le `Forms.Init` méthode est appelée au démarrage de l’application.

> [!NOTE]
> Le **NativeForms** solution d’application ne contient pas tous les projets Xamarin.Forms. Au lieu de cela, il se compose d’un projet Xamarin.iOS, un projet Xamarin.Android et un projet UWP. Chaque projet est un projet natif qui utilise des formulaires natifs pour consommer [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de pages. Toutefois, il n’existe aucune raison pourquoi ne pouvaient pas consommer les projets natifs `ContentPage`-pages dérivé d’un projet de bibliothèque .NET Standard ou d’un projet partagé.

Lors de l’utilisation des formulaires natifs, Xamarin.Forms fonctionnalités telles que [ `DependencyService` ](xref:Xamarin.Forms.DependencyService), [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)et le moteur de liaison de données, tout le travail fixe. Toutefois, la navigation entre les pages doit être effectuées à l’aide de l’API native de la navigation.

## <a name="ios"></a>iOS

Sur iOS, le `FinishedLaunching` remplacer dans la `AppDelegate` classe est généralement l’endroit où effectuer l’application des tâches de démarrage. Elle est appelée une fois que l’application a été lancée et est généralement substituée pour configurer la fenêtre principale et afficher le contrôleur. Le code suivant montre l’exemple le `AppDelegate` classe dans l’exemple d’application :

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static string FolderPath { get; private set; }

    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;
    UIViewController _noteEntryPage;

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

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

Le `FinishedLaunching` méthode effectue les tâches suivantes :

- Xamarin.Forms est initialisé en appelant le `Forms.Init` (méthode).
- Une référence à la `AppDelegate` classe est stockée dans le `static` `Instance` champ. Il s’agit de fournir un mécanisme pour les autres classes d’appeler les méthodes définies dans le `AppDelegate` classe.
- Le `UIWindow`, qui est le conteneur principal pour les vues dans des applications iOS natives, est créé.
- La `FolderPath` propriété est initialisée sur un chemin d’accès sur l’appareil où les données de note seront stockées.
- Le `NotesPage` (classe), qui est un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page défini dans XAML, qui est construit et converti en un `UIViewController` à l’aide de la `CreateViewController` méthode d’extension.
- Le `Title` propriété de la `UIViewController` est défini, ce qui s’affichera sur le `UINavigationBar`.
- Un `UINavigationController` est créé pour la gestion de la navigation hiérarchique. Le `UINavigationController` classe gère une pile de contrôleurs d’affichage et le `UIViewController` passé dans le constructeur s’affiche initialement lorsque le `UINavigationController` est chargé.
- Le `UINavigationController` instance est définie en tant que le niveau supérieur `UIViewController` pour le `UIWindow`et le `UIWindow` est défini en tant que la fenêtre de clé pour l’application et est rendu visible.

Une fois le `FinishedLaunching` méthode est exécutée, l’interface utilisateur définie dans le Xamarin.Forms `NotesPage` s’affichera classe, comme indiqué dans la capture d’écran suivante :

[ ![Capture d’écran d’une application Xamarin. IOS qui utilise une interface utilisateur définie dans l'](native-forms-images/ios-notespage.png "application XAML Xamarin. iOS avec une interface utilisateur XAML") ] (native-forms-images/ios-notespage-large.png#lightbox "Application Xamarin. iOS avec une interface utilisateur XAML")

L’interaction avec l’interface utilisateur, par exemple en appuyant **+** sur [`Button`](xref:Xamarin.Forms.Button), entraîne l’exécution du gestionnaire d’événements suivant `NotesPage` dans l’exécution du code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le `static` `AppDelegate.Instance` champ permet la `AppDelegate.NavigateToNoteEntryPage` méthode à appeler, ce qui est illustré dans l’exemple de code suivant :

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    _noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    _noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(_noteEntryPage, true);
}
```

Le `NavigateToNoteEntryPage` méthode convertit le Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page pour un `UIViewController` avec le `CreateViewController` méthode d’extension et définit le `Title` propriété de la `UIViewController`. Le `UIViewController` est ensuite transmis vers `UINavigationController` par le `PushViewController` (méthode). Par conséquent, l’interface utilisateur définie dans le Xamarin.Forms `NoteEntryPage` s’affichera classe, comme indiqué dans la capture d’écran suivante :

[ ![Capture d’écran d’une application Xamarin. IOS qui utilise une interface utilisateur définie dans l'](native-forms-images/ios-noteentrypage.png "application XAML Xamarin. iOS avec une interface utilisateur XAML") ] (native-forms-images/ios-noteentrypage-large.png#lightbox "Application Xamarin. iOS avec une interface utilisateur XAML")

Lorsque le `NoteEntryPage` s’affiche, en appuyant sur l’arrière flèche s’affiche le `UIViewController` pour le `NoteEntryPage` classe à partir de la `UINavigationController`, retour de l’utilisateur à la `UIViewController` pour la `NotesPage` classe.

> [!IMPORTANT]
> Le dépilament d’un `UIViewController` à partir de la pile de navigation Native iOS ne `UIViewController`supprime pas automatiquement les. Il `UIViewController` incombe au développeur de s’assurer que tout ce qui n’est plus nécessaire a sa `Dispose` méthode appelée, sinon le et le `Page` `UIViewController` attaché sont orphelins et ne seront pas collectés par le garbage collector. entraînant une fuite de mémoire.

## <a name="android"></a>Android

Sur Android, le `OnCreate` remplacer dans la `MainActivity` classe est généralement l’endroit où effectuer l’application des tâches de démarrage. Le code suivant montre l’exemple le `MainActivity` classe dans l’exemple d’application :

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

Le `OnCreate` méthode effectue les tâches suivantes :

- Xamarin.Forms est initialisé en appelant le `Forms.Init` (méthode).
- Une référence à la `MainActivity` classe est stockée dans le `static` `Instance` champ. Il s’agit de fournir un mécanisme pour les autres classes d’appeler les méthodes définies dans le `MainActivity` classe.
- Le `Activity` contenu est défini à partir d’une ressource mise en page. Dans l’exemple d’application, la disposition se compose d’un `LinearLayout` qui contient un `Toolbar`et un `FrameLayout` d’agir comme un conteneur de fragment.
- Le `Toolbar` est récupéré et défini en tant que la barre d’action pour le `Activity`, et le titre de barre d’action est défini.
- La `FolderPath` propriété est initialisée sur un chemin d’accès sur l’appareil où les données de note seront stockées.
- Le `NotesPage` (classe), qui est un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page défini dans XAML, qui est construit et converti en un `Fragment` à l’aide de la `CreateSupportFragment` méthode d’extension.
- Le `SupportFragmentManager` classe crée et valide une transaction qui remplace le `FrameLayout` instance avec le `Fragment` pour la `NotesPage` classe.

Pour plus d’informations sur les Fragments, consultez [Fragments](~/android/platform/fragments/index.md).

Une fois le `OnCreate` méthode est exécutée, l’interface utilisateur définie dans le Xamarin.Forms `NotesPage` s’affichera classe, comme indiqué dans la capture d’écran suivante :

[ ![Capture d’écran d’une application Xamarin. Android qui utilise une interface utilisateur définie dans l'](native-forms-images/android-notespage.png "application XAML Xamarin. Android avec une interface utilisateur XAML") ] (native-forms-images/android-notespage-large.png#lightbox "Application Xamarin. Android avec une interface utilisateur XAML")

L’interaction avec l’interface utilisateur, par exemple en appuyant **+** sur [`Button`](xref:Xamarin.Forms.Button), entraîne l’exécution du gestionnaire d’événements suivant `NotesPage` dans l’exécution du code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le `static` `MainActivity.Instance` champ permet la `MainActivity.NavigateToNoteEntryyPage` méthode à appeler, ce qui est illustré dans l’exemple de code suivant :

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

Le `NavigateToNoteEntryPage` méthode convertit le Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page pour un `Fragment` avec la `CreateSupportFragment` méthode d’extension et ajoute le `Fragment` au fragment de la pile back. Par conséquent, l’interface utilisateur définie dans le Xamarin.Forms `NoteEntryPage` s’affichera, comme illustré dans la capture d’écran suivante :

[ ![Capture d’écran d’une application Xamarin. Android qui utilise une interface utilisateur définie dans l'](native-forms-images/android-noteentrypage.png "application XAML Xamarin. Android avec une interface utilisateur XAML") ] (native-forms-images/android-noteentrypage-large.png#lightbox "Application Xamarin. Android avec une interface utilisateur XAML")

Lorsque le `NoteEntryPage` s’affiche, en appuyant sur l’arrière flèche s’affiche le `Fragment` pour le `NoteEntryPage` à partir de la pile de retour de fragment, retour de l’utilisateur à la `Fragment` pour la `NotesPage` classe.

### <a name="enable-back-navigation-support"></a>Activer la prise en charge de la navigation vers l’arrière

Le `SupportFragmentManager` classe a un `BackStackChanged` événement qui se déclenche chaque fois que le contenu de la pile de retour de fragment est modifié. Le `OnCreate` méthode dans la `MainActivity` classe contient un gestionnaire d’événements anonyme pour cet événement :

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Ce gestionnaire d’événements affiche un bouton précédent sur la barre d’action à condition qu’il se trouve un ou plusieurs `Fragment` instances sur le fragment de pile back. La réponse à en appuyant sur le bouton précédent est gérée par le `OnOptionsItemSelected` remplacer :

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

Le `OnOptionsItemSelected` substitution est appelée chaque fois qu’un élément dans le menu options est sélectionné. Cette implémentation dépile le fragment en cours à partir de la pile de retour de fragment, autant que le bouton précédent a été sélectionné et il existe un ou plusieurs `Fragment` instances sur le fragment de pile back.

### <a name="multiple-activities"></a>Plusieurs activités

Lorsqu’une application est composée de plusieurs activités, [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pages dérivées peuvent être incorporées dans chacune des activités. Dans ce scénario, le `Forms.Init` méthode doive être appelée uniquement dans le `OnCreate` la substitution de la première `Activity` qui incorpore un Xamarin.Forms `ContentPage`. Toutefois, cela a l’impact suivant :

- La valeur de `Xamarin.Forms.Color.Accent` seront prises dans le `Activity` qui a appelé le `Forms.Init` (méthode).
- La valeur de `Xamarin.Forms.Application.Current` sera associé le `Activity` qui a appelé le `Forms.Init` (méthode).

### <a name="choose-a-file"></a>Choisir un fichier

Lors de l’incorporation un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page qui utilise un [ `WebView` ](xref:Xamarin.Forms.WebView) qui a besoin pour prendre en charge HTML « Choisir un fichier » bouton, le `Activity` substituez le `OnActivityResult` méthode :

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Sur UWP, natif `App` classe est généralement l’endroit où effectuer l’application des tâches de démarrage. Xamarin.Forms est généralement initialisé, dans les applications UWP de Xamarin.Forms, dans le `OnLaunched` substituer dans natif `App` (classe), pour transmettre le `LaunchActivatedEventArgs` l’argument de la `Forms.Init` (méthode). Pour cette raison, les applications UWP natives qui consomment un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-page dérivée peut appeler plus facilement le `Forms.Init` méthode à partir de la `App.OnLaunched` (méthode).

Par défaut, natif `App` classe lance le `MainPage` classe en tant que la première page de l’application. Le code suivant montre l’exemple le `MainPage` classe dans l’exemple d’application :

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

Le `MainPage` constructeur effectue les tâches suivantes :

- La mise en cache est activée pour la page, afin qu’un nouveau `MainPage` n’est pas construite lorsqu’un utilisateur accède à la page.
- Une référence à la `MainPage` classe est stockée dans le `static` `Instance` champ. Il s’agit de fournir un mécanisme pour les autres classes d’appeler les méthodes définies dans le `MainPage` classe.
- La `FolderPath` propriété est initialisée sur un chemin d’accès sur l’appareil où les données de note seront stockées.
- Le `NotesPage` (classe), qui est un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée de page défini dans XAML, qui est construit et converti en un `FrameworkElement` à l’aide de la `CreateFrameworkElement` méthode d’extension et définissez en tant que le contenu de la `MainPage` classe.

Une fois le `MainPage` constructeur a exécuté, l’interface utilisateur définie dans le Xamarin.Forms `NotesPage` s’affichera classe, comme indiqué dans la capture d’écran suivante :

[ ![Capture d’écran d’une application UWP qui utilise une interface utilisateur définie avec l’application UWP XAML Xamarin. Forms](native-forms-images/uwp-notespage.png "avec une interface utilisateur XAML Xamarin. Forms") ] (native-forms-images/uwp-notespage-large.png#lightbox "Application UWP avec une interface utilisateur XAML Xamarin. Forms")

L’interaction avec l’interface utilisateur, par exemple en appuyant **+** sur [`Button`](xref:Xamarin.Forms.Button), entraîne l’exécution du gestionnaire d’événements suivant `NotesPage` dans l’exécution du code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le `static` `MainPage.Instance` champ permet la `MainPage.NavigateToNoteEntryPage` méthode à appeler, ce qui est illustré dans l’exemple de code suivant :

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    this.Frame.Navigate(new NoteEntryPage
    {
        BindingContext = note
    });
}
```

Navigation dans UWP est généralement effectuée avec la `Frame.Navigate` (méthode), qui prend un `Page` argument. Xamarin.Forms définit un `Frame.Navigate` méthode d’extension qui prend un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-instance de page dérivée. Par conséquent, lorsque le `NavigateToNoteEntryPage` méthode s’exécute, l’interface utilisateur définie dans le Xamarin.Forms `NoteEntryPage` s’affichera, comme illustré dans la capture d’écran suivante :

[ ![Capture d’écran d’une application UWP qui utilise une interface utilisateur définie avec l’application UWP XAML Xamarin. Forms](native-forms-images/uwp-noteentrypage.png "avec une interface utilisateur XAML Xamarin. Forms") ] (native-forms-images/uwp-noteentrypage-large.png#lightbox "Application UWP avec une interface utilisateur XAML Xamarin. Forms")

Lorsque le `NoteEntryPage` s’affiche, en appuyant sur l’arrière flèche s’affiche le `FrameworkElement` pour le `NoteEntryPage` à partir de la pile de retour dans l’application, retour de l’utilisateur à la `FrameworkElement` pour la `NotesPage` classe.

### <a name="enable-back-navigation-support"></a>Activer la prise en charge de la navigation vers l’arrière

Sur UWP, applications doivent activer la navigation arrière pour tous les matériels et logiciels serveurs boutons, sur les facteurs de forme de périphérique différent. Cela est possible en inscrivant un gestionnaire d’événements pour le `BackRequested` événement, qui peut être effectuée dans le `OnLaunched` méthode dans natif `App` classe :

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

Lorsque l’application est lancée, le `GetForCurrentView` méthode récupère le `SystemNavigationManager` objet associé à la vue actuelle, puis enregistre un gestionnaire d’événements pour le `BackRequested` événement. L’application ne reçoit que cet événement s’il est l’application de premier plan et en réponse, appelle le `OnBackRequested` Gestionnaire d’événements :

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

Le `OnBackRequested` Gestionnaire d’événements appelle la `GoBack` méthode sur le frame de la racine de l’application et les jeux le `BackRequestedEventArgs.Handled` propriété `true` pour marquer l’événement comme géré. Si vous ne Marquez pas l’événement comme géré, vous risquez d’ignorer l’événement.

L’application choisit s’il faut afficher un bouton précédent sur la barre de titre. Cela s’effectue en définissant le `AppViewBackButtonVisibility` propriété de la `AppViewBackButtonVisibility` valeurs d’énumération :

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Le `OnNavigated` Gestionnaire d’événements, qui est exécutée en réponse à la `Navigated` déclenchement des événements, des mises à jour la visibilité du titre de la barre de bouton précédent lors de la navigation entre les pages se produit. Cela garantit que le bouton précédent de barre de titre est visible si la pile de retour dans l’application n’est pas vide, ou supprimé de la barre de titre, si la pile de retour dans l’application est vide.

Pour plus d’informations sur la prise en charge de la navigation arrière sur UWP, consultez [l’historique de Navigation et vers l’arrière navigation pour les applications UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Liens connexes

- [NativeForms (exemple)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Vues natives](~/xamarin-forms/platform/native-views/index.md)
