---
title : « Xamarin.Forms in Xamarin Native Projects » Description : « cet article explique comment utiliser les pages dérivées de ContentPage qui sont directement ajoutées aux projets natifs Xamarin et comment naviguer entre eux. »
ms. Prod : xamarin ms. AssetID : f343fc21-dfb1-4364-A332-9da6705d36bc ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 08/19/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin.Formsdans les projets natifs Xamarin

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

En règle générale, une Xamarin.Forms application comprend une ou plusieurs pages qui dérivent de [`ContentPage`](xref:Xamarin.Forms.ContentPage) , et ces pages sont partagées par toutes les plateformes d’un projet de bibliothèque .NET standard ou d’un projet partagé. Toutefois, les formulaires natifs permettent `ContentPage` d’ajouter des pages dérivées directement aux applications natives Xamarin. iOS, Xamarin. Android et UWP. Par rapport au fait que le projet natif consomme des `ContentPage` pages dérivées d’une .NET standard projet de bibliothèque ou d’un projet partagé, l’avantage d’ajouter des pages directement aux projets natifs est que les pages peuvent être étendues avec des vues natives. Les vues natives peuvent ensuite être nommées en XAML avec `x:Name` et référencées à partir du code-behind. Pour plus d’informations sur les vues natives, consultez [affichages natifs](~/xamarin-forms/platform/native-views/index.md).

Le processus permettant de consommer une Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée d’un projet natif est le suivant :

1. Ajoutez le Xamarin.Forms package NuGet au projet natif.
1. Ajoutez la [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de, ainsi que toutes les dépendances, au projet natif.
1. Appelez la méthode `Forms.Init` .
1. Construisez une instance de la [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de et convertissez-la en type natif approprié à l’aide de l’une des méthodes d’extension suivantes : `CreateViewController` pour iOS, `CreateSupportFragment` pour Android ou `CreateFrameworkElement` pour UWP.
1. Accédez à la représentation de type natif de la [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de à l’aide de l’API de navigation native.

Xamarin.Formsdoit être initialisé en appelant la `Forms.Init` méthode avant qu’un projet natif puisse construire une [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de. Le choix du moment le plus approprié dépend du moment où il est le plus pratique dans le workflow de votre application : il peut être effectué au démarrage de l’application ou juste avant la construction de la `ContentPage` page dérivée de. Dans cet article, et les exemples d’applications qui l’accompagnent, la `Forms.Init` méthode est appelée au démarrage de l’application.

> [!NOTE]
> L’exemple de solution d’application **NativeForms** ne contient aucun Xamarin.Forms projet. Au lieu de cela, il se compose d’un projet Xamarin. iOS, d’un projet Xamarin. Android et d’un projet UWP. Chaque projet est un projet natif qui utilise des formulaires natifs pour consommer [`ContentPage`](xref:Xamarin.Forms.ContentPage) des pages dérivées de. Toutefois, il n’y a aucune raison pour laquelle les projets natifs n’ont pas pu consommer les `ContentPage` pages dérivées d’une .NET standard projet de bibliothèque ou d’un projet partagé.

Lorsque vous utilisez des formulaires natifs, Xamarin.Forms les fonctionnalités telles que [`DependencyService`](xref:Xamarin.Forms.DependencyService) , [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) et le moteur de liaison de données fonctionnent toujours. Toutefois, la navigation entre les pages doit être effectuée à l’aide de l’API de navigation native.

## <a name="ios"></a>iOS

Sur iOS, le `FinishedLaunching` remplacement dans la `AppDelegate` classe est généralement l’endroit où effectuer des tâches liées au démarrage de l’application. Elle est appelée après le lancement de l’application, et est généralement remplacée pour configurer la fenêtre principale et le contrôleur d’affichage. L’exemple de code suivant montre la `AppDelegate` classe dans l’exemple d’application :

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

La méthode `FinishedLaunching` effectue les tâches suivantes :

- Xamarin.Formsest initialisé en appelant la `Forms.Init` méthode.
- Une référence à la `AppDelegate` classe est stockée dans le `static` `Instance` champ. Cela permet de fournir un mécanisme permettant à d’autres classes d’appeler des méthodes définies dans la `AppDelegate` classe.
- `UIWindow`, Qui est le conteneur principal pour les vues dans les applications iOS natives, est créé.
- La `FolderPath` propriété est initialisée sur un chemin d’accès sur l’appareil où les données de note seront stockées.
- La `NotesPage` classe, qui est une Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée en XAML, est construite et convertie en objet `UIViewController` à l’aide de la `CreateViewController` méthode d’extension.
- La `Title` propriété du `UIViewController` est définie, qui sera affichée sur `UINavigationBar` .
- Un `AppNavigationController` est créé pour gérer la navigation hiérarchique. Il s’agit d’une classe de contrôleur de navigation personnalisée, qui dérive de `UINavigationController` . L' `AppNavigationController` objet gère une pile de contrôleurs d’affichage, et le `UIViewController` passé dans le constructeur sera présenté initialement lorsque `AppNavigationController` est chargé.
- L' `AppNavigationController` objet est défini en tant que niveau supérieur `UIViewController` pour `UIWindow` , et `UIWindow` est défini comme fenêtre clé pour l’application et est rendu visible.

Une fois la `FinishedLaunching` méthode exécutée, l’interface utilisateur définie dans la Xamarin.Forms `NotesPage` classe s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. iOS qui utilise une interface utilisateur définie en XAML](native-forms-images/ios-notespage.png "Application Xamarin. iOS avec une interface utilisateur XAML")](native-forms-images/ios-notespage-large.png#lightbox "Application Xamarin. iOS avec une interface utilisateur XAML")

L’interaction avec l’interface utilisateur, par exemple en appuyant sur **+** [`Button`](xref:Xamarin.Forms.Button) , entraîne l’exécution du gestionnaire d’événements suivant dans l' `NotesPage` exécution du code-behind :

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le `static` `AppDelegate.Instance` champ permet `AppDelegate.NavigateToNoteEntryPage` à la méthode d’être appelée, ce qui est illustré dans l’exemple de code suivant :

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

La `NavigateToNoteEntryPage` méthode convertit la Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de en une `UIViewController` avec la `CreateViewController` méthode d’extension et définit la `Title` propriété de `UIViewController` . Le `UIViewController` fait ensuite l’objet `AppNavigationController` d’un push par la `PushViewController` méthode. Par conséquent, l’interface utilisateur définie dans la Xamarin.Forms `NoteEntryPage` classe est affichée, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. iOS qui utilise une interface utilisateur définie en XAML](native-forms-images/ios-noteentrypage.png "Application Xamarin. iOS avec une interface utilisateur XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "Application Xamarin. iOS avec une interface utilisateur XAML")

Lorsque le `NoteEntryPage` est affiché, la navigation arrière dépilera `UIViewController` pour la `NoteEntryPage` classe de `AppNavigationController` , en retournant l’utilisateur au `UIViewController` pour la `NotesPage` classe. Toutefois, le fait de décoder un `UIViewController` à partir de la pile de navigation Native iOS ne supprime pas automatiquement l' `UIViewController` objet joint et `Page` . Par conséquent, la `AppNavigationController` classe substitue la `PopViewController` méthode pour supprimer les contrôleurs d’affichage à la navigation vers l’arrière :

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

La `PopViewController` substitution appelle la `Dispose` méthode sur l' `UIViewController` objet qui a été dépilée à partir de la pile de navigation Native iOS. Dans le cas contraire, l' `UIViewController` objet attaché et `Page` est orphelin.

> [!IMPORTANT]
> Les objets orphelins ne peuvent pas être récupérés par le garbage collector, ce qui entraîne une fuite de mémoire.

## <a name="android"></a>Android

Sur Android, le `OnCreate` remplacement dans la `MainActivity` classe est généralement l’endroit où effectuer des tâches liées au démarrage de l’application. L’exemple de code suivant montre la `MainActivity` classe dans l’exemple d’application :

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

La méthode `OnCreate` effectue les tâches suivantes :

- Xamarin.Formsest initialisé en appelant la `Forms.Init` méthode.
- Une référence à la `MainActivity` classe est stockée dans le `static` `Instance` champ. Cela permet de fournir un mécanisme permettant à d’autres classes d’appeler des méthodes définies dans la `MainActivity` classe.
- Le `Activity` contenu est défini à partir d’une ressource de disposition. Dans l’exemple d’application, la disposition est composée d’un `LinearLayout` qui contient un `Toolbar` et `FrameLayout` d’un pour agir en tant que conteneur de fragments.
- `Toolbar`Est récupéré et défini comme barre d’action pour le `Activity` , et le titre de barre d’action est défini.
- La `FolderPath` propriété est initialisée sur un chemin d’accès sur l’appareil où les données de note seront stockées.
- La `NotesPage` classe, qui est une Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée en XAML, est construite et convertie en objet `Fragment` à l’aide de la `CreateSupportFragment` méthode d’extension.
- La `SupportFragmentManager` classe crée et valide une transaction qui remplace l' `FrameLayout` instance par le `Fragment` de la `NotesPage` classe.

Pour plus d’informations sur les fragments, consultez [fragments](~/android/platform/fragments/index.md).

Une fois la `OnCreate` méthode exécutée, l’interface utilisateur définie dans la Xamarin.Forms `NotesPage` classe s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. Android qui utilise une interface utilisateur définie en XAML](native-forms-images/android-notespage.png "Application Xamarin. Android avec une interface utilisateur XAML")](native-forms-images/android-notespage-large.png#lightbox "Application Xamarin. Android avec une interface utilisateur XAML")

L’interaction avec l’interface utilisateur, par exemple en appuyant sur **+** [`Button`](xref:Xamarin.Forms.Button) , entraîne l’exécution du gestionnaire d’événements suivant dans l' `NotesPage` exécution du code-behind :

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le `static` `MainActivity.Instance` champ permet `MainActivity.NavigateToNoteEntryPage` à la méthode d’être appelée, ce qui est illustré dans l’exemple de code suivant :

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

La `NavigateToNoteEntryPage` méthode convertit la Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de en une `Fragment` avec la `CreateSupportFragment` méthode d’extension et ajoute `Fragment` à la pile de retour de fragment. Par conséquent, l’interface utilisateur définie dans le Xamarin.Forms `NoteEntryPage` sera affichée, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application Xamarin. Android qui utilise une interface utilisateur définie en XAML](native-forms-images/android-noteentrypage.png "Application Xamarin. Android avec une interface utilisateur XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "Application Xamarin. Android avec une interface utilisateur XAML")

Lorsque le `NoteEntryPage` est affiché, le fait d’appuyer sur la flèche précédent permet de `Fragment` dépiler pour le de la `NoteEntryPage` pile de fragments Back, en retournant l’utilisateur au `Fragment` pour la `NotesPage` classe.

### <a name="enable-back-navigation-support"></a>Activer la prise en charge de la navigation vers l’arrière

La `SupportFragmentManager` classe a un `BackStackChanged` événement qui se déclenche chaque fois que le contenu de la pile de retour de fragment est modifié. La `OnCreate` méthode de la `MainActivity` classe contient un gestionnaire d’événements anonymes pour cet événement :

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Ce gestionnaire d’événements affiche un bouton précédent sur la barre d’action, à condition qu’il y ait une ou plusieurs `Fragment` instances sur la pile de retour de fragment. La réponse en appuyant sur le bouton précédent est gérée par le `OnOptionsItemSelected` remplacement :

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

Le `OnOptionsItemSelected` remplacement est appelé chaque fois qu’un élément du menu options est sélectionné. Cette implémentation dépile le fragment actuel de la pile de fragments en arrière, à condition que le bouton précédent ait été sélectionné et qu’il y ait une ou plusieurs `Fragment` instances sur la pile de retour de fragment.

### <a name="multiple-activities"></a>Plusieurs activités

Lorsqu’une application est composée de plusieurs activités, [`ContentPage`](xref:Xamarin.Forms.ContentPage) les pages dérivées de peuvent être incorporées dans chacune des activités. Dans ce scénario, la `Forms.Init` méthode doit être appelée uniquement dans la `OnCreate` substitution de la première `Activity` qui incorpore un Xamarin.Forms `ContentPage` . Toutefois, cela a l’impact suivant :

- La valeur de `Xamarin.Forms.Color.Accent` sera extraite de la `Activity` qui a appelé la `Forms.Init` méthode.
- La valeur de `Xamarin.Forms.Application.Current` sera associée au qui a `Activity` appelé la `Forms.Init` méthode.

### <a name="choose-a-file"></a>Choisir un fichier

Lors de l’incorporation d’une [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de qui utilise un [`WebView`](xref:Xamarin.Forms.WebView) qui doit prendre en charge un bouton « Choose file » html, le `Activity` doit substituer la `OnActivityResult` méthode :

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Sur UWP, la `App` classe native est généralement l’endroit où effectuer des tâches liées au démarrage de l’application. Xamarin.Formsest généralement initialisée, dans Xamarin.Forms les applications UWP, dans la `OnLaunched` substitution de la `App` classe native, pour passer l' `LaunchActivatedEventArgs` argument à la `Forms.Init` méthode. Pour cette raison, les applications UWP natives qui consomment une Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de peuvent facilement appeler la `Forms.Init` méthode à partir de la `App.OnLaunched` méthode.

Par défaut, la `App` classe Native lance la `MainPage` classe en tant que première page de l’application. L’exemple de code suivant montre la `MainPage` classe dans l’exemple d’application :

```csharp
public sealed partial class MainPage : Page
{
    NotesPage notesPage;
    NoteEntryPage noteEntryPage;
    
    public static MainPage Instance;
    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        notesPage = new Notes.UWP.Views.NotesPage();
        this.Content = notesPage.CreateFrameworkElement();
        // ...        
    } 
    // ...
}
```

Le `MainPage` constructeur effectue les tâches suivantes :

- La mise en cache est activée pour la page, de sorte qu’une nouvelle `MainPage` n’est pas construite quand un utilisateur accède à la page.
- Une référence à la `MainPage` classe est stockée dans le `static` `Instance` champ. Cela permet de fournir un mécanisme permettant à d’autres classes d’appeler des méthodes définies dans la `MainPage` classe.
- La `FolderPath` propriété est initialisée sur un chemin d’accès sur l’appareil où les données de note seront stockées.
- La `NotesPage` classe, qui est une Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée en XAML, est construite et convertie en objet `FrameworkElement` à l’aide de la `CreateFrameworkElement` méthode d’extension, puis définie en tant que contenu de la `MainPage` classe.

Une fois le `MainPage` constructeur exécuté, l’interface utilisateur définie dans la Xamarin.Forms `NotesPage` classe s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application UWP qui utilise une interface utilisateur définie avec Xamarin.Forms XAML](native-forms-images/uwp-notespage.png "Application UWP avec un [ ! Opérationnel. NO-LOC (Xamarin. Forms)] IU XAML")](native-forms-images/uwp-notespage-large.png#lightbox "Application UWP avec un [ ! Opérationnel. NO-LOC (Xamarin. Forms)] IU XAML")

L’interaction avec l’interface utilisateur, par exemple en appuyant sur **+** [`Button`](xref:Xamarin.Forms.Button) , entraîne l’exécution du gestionnaire d’événements suivant dans l' `NotesPage` exécution du code-behind :

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

Le `static` `MainPage.Instance` champ permet `MainPage.NavigateToNoteEntryPage` à la méthode d’être appelée, ce qui est illustré dans l’exemple de code suivant :

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    noteEntryPage = new Notes.UWP.Views.NoteEntryPage
    {
        BindingContext = note
    };
    this.Frame.Navigate(noteEntryPage);
}
```

La navigation dans UWP est généralement effectuée à l’aide de la `Frame.Navigate` méthode, qui prend un `Page` argument. Xamarin.Formsdéfinit une `Frame.Navigate` méthode d’extension qui prend une [`ContentPage`](xref:Xamarin.Forms.ContentPage) instance de page dérivée de. Par conséquent, lorsque la `NavigateToNoteEntryPage` méthode s’exécute, l’interface utilisateur définie dans le Xamarin.Forms `NoteEntryPage` s’affiche, comme illustré dans la capture d’écran suivante :

[![Capture d’écran d’une application UWP qui utilise une interface utilisateur définie avec Xamarin.Forms XAML](native-forms-images/uwp-noteentrypage.png "Application UWP avec un [ ! Opérationnel. NO-LOC (Xamarin. Forms)] IU XAML")](native-forms-images/uwp-noteentrypage-large.png#lightbox "Application UWP avec un [ ! Opérationnel. NO-LOC (Xamarin. Forms)] IU XAML")

Lorsque le `NoteEntryPage` s’affiche, appuyez sur la flèche précédent pour obtenir le de la `FrameworkElement` `NoteEntryPage` pile de retour dans l’application, en retournant l’utilisateur pour la `FrameworkElement` `NotesPage` classe.

### <a name="enable-page-resizing-support"></a>Activer la prise en charge du redimensionnement de page

Lorsque la fenêtre d’application UWP est redimensionnée, le Xamarin.Forms contenu doit également être redimensionné. Pour ce faire, vous devez inscrire un gestionnaire d’événements pour l' `Loaded` événement, dans le `MainPage` constructeur :

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

L' `Loaded` événement se déclenche lorsque la page est disposée, rendue et prête pour l’interaction, et exécute la `OnMainPageLoaded` méthode en réponse :

```csharp
void OnMainPageLoaded(object sender, RoutedEventArgs e)
{
    this.Frame.SizeChanged += (o, args) =>
    {
        if (noteEntryPage != null)
            noteEntryPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
        else
            notesPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
    };
}
```

La `OnMainPageLoaded` méthode inscrit un gestionnaire d’événements anonymes pour l' `Frame.SizeChanged` événement, qui est déclenché lorsque les `ActualHeight` Propriétés ou `ActualWidth` changent sur `Frame` . En réponse, le Xamarin.Forms contenu de la page active est redimensionné en appelant la `Layout` méthode.

### <a name="enable-back-navigation-support"></a>Activer la prise en charge de la navigation vers l’arrière

Sur UWP, les applications doivent activer la navigation vers l’arrière pour tous les boutons de retour matériel et logiciel, sur différents facteurs de forme de périphérique. Pour ce faire, vous devez inscrire un gestionnaire d’événements pour l' `BackRequested` événement, qui peut être effectué dans le `MainPage` constructeur :

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

Lorsque l’application est lancée, la `GetForCurrentView` méthode récupère l' `SystemNavigationManager` objet associé à la vue actuelle, puis inscrit un gestionnaire d’événements pour l' `BackRequested` événement. L’application reçoit uniquement cet événement s’il s’agit de l’application de premier plan et, en réponse, appelle le `OnBackRequested` Gestionnaire d’événements :

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
        noteEntryPage = null;
    }
}
```

Le `OnBackRequested` Gestionnaire d’événements appelle la `GoBack` méthode sur le frame racine de l’application et affecte `BackRequestedEventArgs.Handled` à la propriété la valeur `true` pour marquer l’événement comme étant géré. Si vous ne Marquez pas l’événement comme géré, vous risquez d’ignorer l’événement.

L’application choisit s’il faut afficher un bouton précédent sur la barre de titre. Pour ce faire, affectez `AppViewBackButtonVisibility` à la propriété l’une des `AppViewBackButtonVisibility` valeurs d’énumération, dans la `App` classe :

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Le `OnNavigated` Gestionnaire d’événements, qui est exécuté en réponse au `Navigated` déclenchement de l’événement, met à jour la visibilité du bouton précédent de la barre de titre lorsque la navigation entre les pages se produit. Cela permet de s’assurer que le bouton précédent de la barre de titre est visible si la pile de retour dans l’application n’est pas vide ou est supprimée de la barre de titre si la pile de retour dans l’application est vide.

Pour plus d’informations sur la prise en charge de la navigation arrière sur UWP, consultez [historique de navigation et navigation vers l’arrière pour les applications UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Liens connexes

- [NativeForms (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Vues natives](~/xamarin-forms/platform/native-views/index.md)
