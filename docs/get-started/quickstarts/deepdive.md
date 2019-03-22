---
title: Immersion dans Xamarin.Forms-démarrage rapide
description: Cet article examine les notions de base du développement d’applications avec Xamarin.Forms. Les sujets abordés étaient notamment la structure d’une application Xamarin.Forms, les concepts fondamentaux d’une architecture et d’une application, et l’interface utilisateur.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: 8674ef47867acf3bca4d05fd6628a58e2f9ad90e
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329362"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Immersion dans Xamarin.Forms-démarrage rapide

Dans le [Xamarin.Forms-démarrage rapide](~/get-started/index.yml), l’application de notes de publication a été générée. Cet article passe en revue les éléments générés afin d’acquérir les notions de base du fonctionnement des applications Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduction à Visual Studio

Visual Studio organise le code en *solutions* et en *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application de notes de publication se compose d’une solution qui contient quatre projets, comme illustré dans la capture d’écran suivante :

![](deepdive-images/vs/solution.png "Explorateur de solutions Visual Studio")

Les projets sont :

- Remarques : ce projet est le projet de bibliothèque .NET Standard qui conserve l’ensemble du code partagé et de l’interface utilisateur partagée.
- Notes.Android : ce projet conserve le code spécifique à Android et constitue le point d’entrée pour l’application Android.
- Notes.iOS : ce projet conserve le code spécifique à iOS et constitue le point d’entrée pour l’application iOS.
- Notes.UWP : ce projet conserve le code spécifique de Universal Windows Platform (UWP) et est le point d’entrée pour l’application UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomie d’une application Xamarin.Forms

La capture d’écran suivante illustre le contenu du projet de bibliothèque Notes .NET Standard dans Visual Studio :

![](deepdive-images/vs/net-standard-project.png "Contenu du projet .NET Standard Phoneword ")

Le projet a un nœud **Dépendances** qui contient les nœuds **NuGet** et **SDK** :

- **NuGet** &ndash; Xamarin.Forms les packages NuGet sqlite-net-pcl qui ont été ajoutés au projet.
- **SDK** &ndash; Métapaquet `NETStandard.Library` qui référence l’ensemble complet de paquets NuGet définissant .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduction à Visual Studio pour Mac

Tout comme Visual Studio, [Visual Studio pour Mac](/visualstudio/mac/) organise le code en *solutions* et en *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application de notes de publication se compose d’une solution contenant trois projets, comme illustré dans la capture d’écran suivante :

![](deepdive-images/vsmac/solution.png "Volet Solution Visual Studio pour Mac")

Les projets sont :

- Remarques : ce projet est le projet de bibliothèque .NET Standard qui conserve l’ensemble du code partagé et de l’interface utilisateur partagée.
- Notes.Android : ce projet conserve le code spécifique à Android et constitue le point d’entrée pour les applications Android.
- Notes.iOS : ce projet conserve le code spécifique à iOS et constitue le point d’entrée pour les applications iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomie d’une application Xamarin.Forms

La capture d’écran suivante illustre le contenu du projet de bibliothèque Notes .NET Standard dans Visual Studio pour Mac :

![](deepdive-images/vsmac/net-standard-project.png "Contenu du projet de bibliothèque .NET Standard Phoneword ")

Le projet a un nœud **Dépendances** qui contient les nœuds **NuGet** et **SDK** :

- **NuGet** &ndash; Xamarin.Forms les packages NuGet sqlite-net-pcl qui ont été ajoutés au projet.
- **SDK** &ndash; Métapaquet `NETStandard.Library` qui référence l’ensemble complet de paquets NuGet définissant .NET Standard.

::: zone-end

Le projet comprend également un certain nombre de fichiers :

- **Data\NoteDatabase.cs** – cette classe contient le code pour créer la base de données, lire les données à partir de celui-ci, écrire des données et supprimer des données à partir de celui-ci.
- **Models\Note.cs** – cette classe définit un `Note` modèle dont les instances stockent les données relatives à chaque note dans l’application.
- **App.xaml** : balisage XAML pour la classe `App`, qui définit un dictionnaire de ressources pour l’application.
- **App.xaml.cs** : code-behind pour la classe `App`, qui est chargé de l’instanciation de la première page affichée par l’application sur chaque plateforme et de la gestion des événements du cycle de vie de l’application.
- **NotesPage.xaml** – balisage le XAML pour la `NotesPage` classe, qui définit l’interface utilisateur pour la page affichée lorsque l’application est lancée.
- **NotesPage.xaml.cs** – le code-behind pour le `NotesPage` (classe), qui contient la logique métier qui est exécutée lorsque l’utilisateur interagit avec la page.
- **NoteEntryPage.xaml** – balisage le XAML pour la `NoteEntryPage` classe, qui définit l’interface utilisateur pour la page affichée lorsque l’utilisateur entre une note.
- **NoteEntryPage.xaml.cs** – le code-behind pour le `NoteEntryPage` (classe), qui contient la logique métier qui est exécutée lorsque l’utilisateur interagit avec la page.

Pour plus d’informations sur la structure d’une application Xamarin.iOS, consultez [Structure d’une application Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Pour plus d’informations sur la structure d’une application Xamarin.Android, consultez [Structure d’une application Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Architecture et application des principes de base

Une application Xamarin.Forms est structurée de la même façon qu’une application multiplateforme classique. En général, le code partagé est placé dans une bibliothèque .NET Standard et les applications spécifiques à la plateforme consomment le code partagé. Le diagramme suivant montre une vue d’ensemble de cette relation pour l’application de notes de publication :

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Architecture de notes")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Architecture de notes")

::: zone-end

Pour optimiser la réutilisation du code de démarrage, les applications Xamarin.Forms ont une seule classe nommée `App` qui est chargée de l’instanciation de la première page affichée par l’application sur chaque plateforme, comme indiqué dans l’exemple de code suivant :

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

Ce code définit le `MainPage` propriété de la `App` classe à une [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instance dont le contenu est un `NotesPage` instance. En outre, l’attribut [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) active le compilateur XAML afin que le code XAML soit compilé directement en langage intermédiaire. Pour plus d’informations, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launching-the-application-on-each-platform"></a>Lancement de l’application sur chaque plateforme

### <a name="ios"></a>iOS

Pour lancer la page Xamarin.Forms initiale dans iOS, le projet Notes.iOS définit le `AppDelegate` classe qui hérite de la `FormsApplicationDelegate` classe :

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

La valeur de remplacement `FinishedLaunching` initialise le framework Xamarin.Forms en appelant la méthode `Init`. Cela entraîne le chargement de l’implémentation spécifique à iOS de Xamarin.Forms dans l’application avant que le contrôleur d’affichage racine ne soit défini par l’appel à la méthode `LoadApplication`.

### <a name="android"></a>Android

Pour lancer la page Xamarin.Forms initiale dans Android, le projet Notes.Android inclut du code qui crée un `Activity` avec la `MainLauncher` attribut, avec l’activité héritant de la `FormsAppCompatActivity` classe :

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

La valeur de remplacement `OnCreate` initialise le framework Xamarin.Forms en appelant la méthode `Init`. Cela entraîne le chargement de l’implémentation spécifique à Android de Xamarin.Forms dans l’application avant que l’application Xamarin.Forms ne soit chargée.

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Dans les applications de plateforme Windows universelle (UWP), la méthode `Init` qui initialise le framework Xamarin.Forms est appelée à partir de la classe `App` :

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Cela entraîne le chargement de l’implémentation spécifique à UWP de Xamarin.Forms dans l’application. La page Xamarin.Forms initiale est lancée par le `MainPage` classe :

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

L’application Xamarin.Forms est chargée avec la méthode `LoadApplication`.

> [!NOTE]
> Les applications universelles Windows Platform peuvent être générées avec Xamarin.Forms, mais uniquement à l’aide de Visual Studio sur Windows.

::: zone-end

## <a name="user-interface"></a>Interface utilisateur

Il existe quatre principaux groupes de contrôles utilisées pour créer l’interface utilisateur d’une application Xamarin.Forms :

1. **Pages** : les pages Xamarin.Forms représentent les écrans de l’application mobile multiplateforme. L’application de notes de publication utilise le [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) classe pour afficher des écrans uniques. Pour plus d’informations sur les pages, consultez [Pages Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md).
1. **Vues** : les vues Xamarin.Forms sont les contrôles affichés sur l’interface utilisateur, tels que les étiquettes, les boutons et les zones d’entrée de texte. L’application de notes de publication terminée utilise le [ `ListView` ](xref:Xamarin.Forms.ListView), [ `Editor` ](xref:Xamarin.Forms.Editor), et [ `Button` ](xref:Xamarin.Forms.Button) vues. Pour plus d’informations sur les vues, consultez [Vues Xamarin.Forms](~/xamarin-forms/user-interface/controls/views.md).
1. **Dispositions** : les dispositions Xamarin.Forms sont des conteneurs utilisés pour composer des vues dans des structures logiques. L’application de notes de publication utilise le [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) classe pour organiser des vues dans une pile verticale et le [ `Grid` ](xref:Xamarin.Forms.Grid) classe pour réorganiser les boutons horizontalement. Pour plus d’informations sur les dispositions, consultez [Dispositions Xamarin.Forms](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Cellules** : les cellules Xamarin.Forms sont des éléments spécialisés utilisés pour des éléments dans une liste et décrivent comment chaque élément dans une liste doit être dessiné. L’application de notes de publication utilise le [ `TextCell` ](xref:Xamarin.Forms.TextCell) pour afficher les deux éléments pour chaque ligne dans la liste. Pour plus d’informations sur les cellules, consultez [Cellules Xamarin.Forms](~/xamarin-forms/user-interface/controls/cells.md).

Lors de l’exécution, chaque contrôle est mappé à son équivalent natif, ce qui sera affiché.

### <a name="layout"></a>Mise en page

L’application de notes de publication utilise le [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) pour simplifier le développement de l’application multi-plateforme en disposant automatiquement les vues à l’écran, quel que soit la taille d’écran. Les éléments enfants sont placés les uns après les autres, horizontalement ou verticalement dans l’ordre dans lequel ils ont été ajoutés. La quantité d’espace utilisée par la classe `StackLayout` dépend de la définition des propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), mais `StackLayout` essaie par défaut d’utiliser la totalité de l’écran.

Le code XAML suivant montre un exemple d’utilisation une [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) à disposition le `NoteEntryPage`:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

Par défaut le [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) suppose une orientation verticale. Toutefois, il peut être modifié par une orientation horizontale en définissant le [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propriété le [ `StackOrientation.Horizontal` ](xref:Xamarin.Forms.StackOrientation.Horizontal) membre d’énumération.

> [!NOTE]
> La taille des vues peut être définie via la `HeightRequest` et `WidthRequest` propriétés.

Pour plus d’informations sur la classe [`StackLayout`](xref:Xamarin.Forms.StackLayout), consultez [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Réponse aux interactions de l’utilisateur

Un objet défini en XAML peut déclencher un événement qui est géré dans le fichier code-behind. Le code suivant montre l’exemple le `OnSaveButtonClicked` méthode dans le code-behind pour le `NoteEntryPage` (classe), qui est exécutée en réponse à la [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) événement déclenché sur le *enregistrer* bouton .

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

Le `OnSaveButtonClicked` méthode enregistre la note dans la base de données et revient à la page précédente.

> [!NOTE]
> Le fichier code-behind d’une classe XAML peut accéder à un objet défini en XAML en utilisant le nom qui lui est affecté avec l’attribut `x:Name`. La valeur affectée à cet attribut suit les mêmes règles que les variables C# : elle doit commencer par une lettre ou un trait de soulignement et ne contenir aucun espace incorporé.

Le câblage de l’enregistrement bouton à la `OnSaveButtonClicked` méthode se produit dans le balisage XAML pour la `NoteEntryPage` classe :

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listes

Le [ `ListView` ](xref:Xamarin.Forms.ListView) est chargée d’afficher une collection d’éléments verticalement dans une liste. Chaque élément dans le `ListView` se trouvera dans une cellule unique.

Le code suivant montre l’exemple le [ `ListView` ](xref:Xamarin.Forms.ListView) à partir de la `NotesPage`:

```xaml
<ListView x:Name="listView"
          Margin="{StaticResource PageMargin}"
          ItemSelected="OnListViewItemSelected">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Text}"
                      Detail="{Binding Date}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

La disposition de chaque ligne dans le [ `ListView` ](xref:Xamarin.Forms.ListView) est défini dans le [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) élément et utilise des données de liaison pour afficher toutes les notes sont récupérées par l’application. Le [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété est définie sur la source de données dans `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Ce code remplit la [ `ListView` ](xref:Xamarin.Forms.ListView) avec des notes stockées dans la base de données.

Quand une ligne est sélectionnée dans le [ `ListView` ](xref:Xamarin.Forms.ListView), le [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) événement est déclenché. Un gestionnaire d’événements, nommé `OnListViewItemSelected`, est exécutée lorsque l’événement est déclenché :

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

Le [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) événement peut accéder à l’objet qui a été associé à la cellule via la [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriété.

Pour plus d’informations sur la [ `ListView` ](xref:Xamarin.Forms.ListView) de classe, consultez [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigation

Xamarin.Forms propose plusieurs expériences de navigation entre les pages différentes, selon le type de [`Page`](xref:Xamarin.Forms.Page) utilisé. Pour [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) navigation d’instances peut être hiérarchique, ou non. Pour plus d’informations sur la navigation modale, consultez [Pages modales Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Les classes [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) et [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) fournissent d’autres expériences de navigation. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md).

Dans le volet de navigation hiérarchique, le [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe est utilisée pour naviguer dans une pile de [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) objets, ascendante et descendante, comme vous le souhaitez. La classe implémente la navigation comme une pile d’objets [`Page`](xref:Xamarin.Forms.Page) LIFO (dernier entré, premier sorti). Pour passer d’une page à une autre, une application envoie une nouvelle page dans la pile de navigation, où elle devient la page active. Pour revenir à la page précédente, l’application dépile la page actuelle, et la nouvelle page tout en haut devient la page active.

La classe `NavigationPage` ajoute également une barre de navigation en haut de la page qui affiche un titre et un bouton **Précédent** approprié à la plateforme qui permet de revenir à la page précédente.

La première page ajoutée à une pile de navigation est appelée le *racine* page de l’application et l’exemple de code suivant montre comment cela fonctionne dans l’application de notes de publication :

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Toutes les instances de [`ContentPage`](xref:Xamarin.Forms.ContentPage) ont une propriété [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) qui expose des méthodes pour modifier la pile de pages. Ces méthodes ne doivent être appelées que si l’application inclut une classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Pour accéder à `NoteEntryPage`, il est nécessaire d’appeler la méthode [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) comme illustré dans l’exemple de code ci-dessous :

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

Cela entraîne le `NoteEntryPage` objet à être envoyée à la pile de navigation, où elle devient la page active.

La page active peut être retirée de la pile de navigation en appuyant sur le bouton *Précédent* sur l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran. Pour retourner par programmation à la page d’origine, l’objet `NoteEntryPage` doit appeler la méthode [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), comme illustré dans l’exemple de code ci-dessous :

```csharp
await Navigation.PopAsync();
```

Pour plus d’informations sur la navigation hiérarchique, consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Liaison de données

La liaison de données permet de simplifier la façon dont une application Xamarin.Forms affiche ses données et interagit avec elles. Elle établit une connexion entre l’interface utilisateur et l’application sous-jacente. La classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) contient une grande partie de l’infrastructure pour prendre en charge la liaison de données.

La liaison de données connecte deux objets, appelés la *source* et la *cible*. L’objet *source* fournit les données. L’objet *cible* consomme (et affiche souvent) les données de l’objet source. Par exemple, un [ `Editor` ](xref:Xamarin.Forms.Editor) (*cible* objet) lie couramment sa [ `Text` ](xref:Xamarin.Forms.Editor.Text) propriété publique `string` propriété dans un *source* objet. Le diagramme suivant illustre la relation de liaison :

![](deepdive-images/data-binding.png "Liaison de données")

Le principal avantage de la liaison de données est que vous n’avez plus à vous soucier de la synchronisation des données entre les vues et la source de données. Les modifications apportées à l’objet *source* sont automatiquement envoyées à l’objet *cible* dans les coulisses par le framework de liaison, et les modifications apportées à l’objet cible peuvent éventuellement être renvoyées à l’objet *source*.

L’établissement de données de liaison est un processus en deux étapes :

- La propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de l’objet *cible* doit être définie sur la *source*.
- Une liaison doit être établie entre la *cible* et la *source*. En XAML, cette opération est effectuée en utilisant l’extension de balisage [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

Dans l’application de notes de publication, la cible de liaison est le [ `Editor` ](xref:Xamarin.Forms.Editor) qui affiche une note, tandis que le `Note` instance définie en tant que le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` est la liaison source.

Le `BindingContext` de la `NoteEntryPage` est définie lors de la navigation de page, comme indiqué dans l’exemple de code suivant :

```csharp
async void OnNoteAddedClicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new NoteEntryPage
    {
        BindingContext = new Note()
    });
}

async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        await Navigation.PushAsync(new NoteEntryPage
        {
            BindingContext = e.SelectedItem as Note
        });
    }
}
```

Dans le `OnNoteAddedClicked` (méthode), qui est exécutée lors de l’ajout d’une note à l’application, le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` est défini sur un nouveau `Note` instance. Dans le `OnListViewItemSelected` (méthode), qui est exécutée lorsqu’une note existante est sélectionnée dans le [ `ListView` ](xref:Xamarin.Forms.ListView), le `BindingContext` de la `NoteEntryPage` est définie sur le texte sélectionné `Note` instance, ce qui est accessible via le [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriété.

> [!IMPORTANT]
> Alors que la propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de chaque objet *cible* peut être définie individuellement, cela n’est pas nécessaire. `BindingContext` est une propriété spéciale qui est héritée par tous ses enfants. Par conséquent, lorsque le `BindingContext` sur le [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) est définie sur une `Note` de l’instance, tous les enfants de la `ContentPage` ont les mêmes `BindingContext`et vous pouvez lier aux propriétés publiques de la `Note`objet.

Le [ `Editor` ](xref:Xamarin.Forms.Editor) dans `NoteEntryPage` lie ensuite à la `Text` propriété de la `Note` objet :

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Une liaison entre la propriété [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) et la propriété `Text` de l’objet *source* est établie. Modifications apportées dans le `Editor` seront propagées automatiquement à la `Note` objet. De même, si des modifications sont apportées à la `Note.Text` propriété, le moteur de liaison Xamarin.Forms met également à jour le contenu de la `Editor`. Il s’agit là d’une *liaison bidirectionnelle*.

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Styles

Les applications Xamarin.Forms contiennent souvent plusieurs éléments visuels qui ont une apparence identique. Définition de l’apparence de chaque élément visuel peut être répétitive et sujet aux erreurs. Au lieu de cela, vous peuvent créer les styles qui définissent l’apparence, puis appliquée pour les éléments visuels requis.

Le [ `Style` ](xref:Xamarin.Forms.Style) classe regroupe une collection de valeurs de propriété dans un objet qui peut ensuite être appliqué à plusieurs instances de l’élément visuel. Les styles sont stockés dans un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), soit au niveau de l’application, le niveau de la page ou le niveau de la vue. Choix de l’emplacement définir un `Style` impacts où il peut être utilisé :

- [`Style`](xref:Xamarin.Forms.Style) les instances définies au niveau de l’application peuvent être appliquées dans toute l’application.
- [`Style`](xref:Xamarin.Forms.Style) les instances définies au niveau de la page peuvent être appliquées à la page et à ses enfants.
- [`Style`](xref:Xamarin.Forms.Style) instances définies au niveau de la vue peuvent être appliquées à la vue et à ses enfants.

> [!IMPORTANT]
> Tous les styles qui sont utilisés dans l’application sont stockés dans le dictionnaire de ressources de l’application pour éviter la duplication. Toutefois, XAML est spécifique à une page ne doit pas être inclus dans le dictionnaire de ressources de l’application, comme les ressources sont alors analysées au démarrage de l’application au lieu de lorsque vous invitent à une page.

Chaque [ `Style` ](xref:Xamarin.Forms.Style) instance contient une collection d’un ou plusieurs [ `Setter` ](xref:Xamarin.Forms.Setter) objets, avec chacun `Setter` ayant un [ `Property` ](xref:Xamarin.Forms.Setter.Property) et un [`Value`](xref:Xamarin.Forms.Setter.Value). Le `Property` est le nom de la propriété peut être liée de l’élément auquel le style est appliqué, et le `Value` est la valeur qui est appliquée à la propriété. L’exemple de code suivant montre un style à partir de `NoteEntryPage`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    <ContentPage.Resources>
        <!-- Implicit styles -->
        <Style TargetType="{x:Type Editor}">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

Ce style est appliqué aux [ `Editor` ](xref:Xamarin.Forms.Editor) instances sur la page.

Lorsque vous créez un [ `Style` ](xref:Xamarin.Forms.Style), le [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriété est toujours requise.

> [!NOTE]
> Appliquer un style d’une application Xamarin.Forms s’effectue généralement à l’aide de styles XAML. Toutefois, Xamarin.Forms prend également en charge le style des éléments visuels à l’aide de feuilles de Style en cascade (CSS). Pour plus d’informations, consultez [applications Xamarin.Forms de styles à l’aide de feuilles de Style en cascade (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Pour plus d’informations sur les styles XAML, consultez [styles des applications de Xamarin.Forms à l’aide de Styles de XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>En fournissant les styles spécifiques à la plateforme

Le `OnPlatform` autorisent les extensions de balisage vous permettent de personnaliser l’apparence de l’interface utilisateur sur une base par plateforme :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

Cela [ `Style` ](xref:Xamarin.Forms.Style) définit différents [ `Color` ](xref:Xamarin.Forms.Color) valeurs pour le [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) et [ `BarTextColor` ](xref:Xamarin.Forms.NavigationPage.BarTextColor) propriétés de [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), selon la plateforme utilisée.

Pour plus d’informations sur les extensions de balisage XAML, consultez [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Pour plus d’informations sur la `OnPlatform` extension de balisage, consultez [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Test et déploiement

Visual Studio pour Mac et Visual Studio proposent tous deux de nombreuses options pour tester et déployer les applications. Le débogage d’applications est une partie courante du cycle de vie de développement des applications et permet de diagnostiquer les problèmes de code. Pour plus d’informations, consultez [Définir un point d’arrêt](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Exécuter du code pas à pas](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) et [Informations de sortie dans la fenêtre de journal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Les simulateurs constituent un endroit judicieux pour commencer à déployer et tester une application, et comportent des fonctionnalités utiles pour le test des applications. Toutefois, les utilisateurs ne consommant pas l’application finale dans un simulateur, les applications doivent être testées tôt et souvent sur des appareils réels. Pour plus d’informations sur le provisionnement des appareils iOS, consultez [Provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md). Pour plus d’informations sur le provisionnement des appareils Android, consultez [Configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Étapes suivantes

Cette présentation approfondie a examiné les principes fondamentaux du développement d’applications à l’aide de Xamarin.Forms. Les étapes suggérées ci-dessous incluent la découverte des fonctionnalités suivantes :

- Il existe quatre principaux groupes de contrôles permettant de créer l’interface utilisateur d’une application Xamarin.Forms. Pour plus d’informations, consultez [Informations de référence sur les contrôles](~/xamarin-forms/user-interface/controls/index.md).
- La liaison de données est une technique qui permet de lier les propriétés de deux objets afin que les changements d’une propriété soient automatiquement répercutés sur l’autre propriété. Pour plus d’informations, consultez [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms propose plusieurs expériences différentes de navigation dans les pages, selon le type de page utilisé. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Les styles contribuent à réduire le balisage répétitif et permettent de modifier plus facilement l’apparence des applications. Pour plus d’informations, consultez [Applications de style Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- Les extensions de balisage XAML étendent la puissance et la flexibilité du langage XAML en permettant de définir des attributs d’éléments à partir de sources autres que des chaînes de texte littéral. Pour plus d’informations, consultez [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Les modèles de données permettent de définir la présentation des données sur les affichages pris en charge. Pour plus d’informations, consultez [Modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Chaque page, disposition et affichage est restitué différemment sur chaque plateforme avec une classe `Renderer` qui crée ensuite un contrôle natif, le dispose à l’écran et ajoute le comportement spécifié dans le code partagé. Les développeurs peuvent implémenter leurs propres classes `Renderer` pour personnaliser l’apparence et/ou le comportement d’un contrôle. Pour plus d’informations, consultez [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Les effets permettent également de personnaliser les contrôles natifs sur chaque plateforme. Les effets sont créés dans des projets spécifiques à la plateforme en sous-classant la classe [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2), et sont utilisés en les attachant à un contrôle Xamarin.Forms approprié. Pour plus d’informations, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).
- Le code partagé permet d’accéder aux fonctionnalités natives par le biais de la classe [`DependencyService`](xref:Xamarin.Forms.DependencyService). Pour plus d’informations, consultez [Accès aux fonctionnalités natives avec DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Vous pouvez également consulter [_Creating Mobile Apps with Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md), un livre très intéressant écrit par Charles Petzold, pour découvrir Xamarin.Forms plus en détail. Le livre est disponible au format PDF ou dans une variété de formats électroniques.

## <a name="related-links"></a>Liens connexes

- [eXtensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.md)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Informations de référence sur les contrôles](~/xamarin-forms/user-interface/controls/index.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Exemples Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Exemples pour bien démarrer](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/)
- [Informations de référence sur les API Xamarin.Forms](xref:Xamarin.Forms)
- [Autoformation gratuite (vidéo)](https://university.xamarin.com/self-guided/)
