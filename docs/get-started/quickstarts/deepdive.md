---
title: Présentation approfondie des Démarrages rapides Xamarin. Forms
description: Cet article examine les notions de base du développement d’applications avec Xamarin.Forms. Les sujets abordés étaient notamment la structure d’une application Xamarin.Forms, les concepts fondamentaux d’une architecture et d’une application, et l’interface utilisateur.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: c0e772e0207d2ccc9a94aefd9655dc1bb6776bba
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653484"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Présentation approfondie des Démarrages rapides Xamarin. Forms

Dans le [démarrage rapide Xamarin. Forms](~/get-started/index.yml), l’application notes a été générée. Cet article passe en revue les éléments générés afin d’acquérir les notions de base du fonctionnement des applications Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduction à Visual Studio

Visual Studio organise le code en *solutions* et en *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application notes se compose d’une solution contenant quatre projets, comme illustré dans la capture d’écran suivante:

![](deepdive-images/vs/solution.png "Explorateur de solutions Visual Studio")

Les projets sont :

- Notes: ce projet est le projet de bibliothèque de .NET Standard qui contient l’ensemble du code partagé et de l’interface utilisateur partagée.
- Notes. Android: ce projet contient le code spécifique à Android et constitue le point d’entrée de l’application Android.
- Notes. iOS: ce projet contient le code spécifique à iOS et constitue le point d’entrée de l’application iOS.
- Notes. UWP: ce projet contient du code spécifique plateforme Windows universelle (UWP) et constitue le point d’entrée de l’application UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomie d’une application Xamarin. Forms

La capture d’écran suivante montre le contenu du projet de bibliothèque notes .NET Standard dans Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Contenu du projet .NET Standard Phoneword ")

Le projet a un nœud **Dépendances** qui contient les nœuds **NuGet** et **SDK** :

- **NuGet** &ndash; les packages NuGet Xamarin. Forms et SQLite-net-PCL qui ont été ajoutés au projet.
- **SDK** &ndash; Métapaquet `NETStandard.Library` qui référence l’ensemble complet de paquets NuGet définissant .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduction à Visual Studio pour Mac

Tout comme Visual Studio, [Visual Studio pour Mac](/visualstudio/mac/) organise le code en *solutions* et en *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application notes se compose d’une solution contenant trois projets, comme illustré dans la capture d’écran suivante:

![](deepdive-images/vsmac/solution.png "Volet Solution Visual Studio pour Mac")

Les projets sont :

- Notes: ce projet est le projet de bibliothèque de .NET Standard qui contient l’ensemble du code partagé et de l’interface utilisateur partagée.
- Notes. Android: ce projet contient du code spécifique à Android et constitue le point d’entrée pour les applications Android.
- Notes. iOS: ce projet contient du code spécifique à iOS et constitue le point d’entrée pour les applications iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomie d’une application Xamarin. Forms

La capture d’écran suivante montre le contenu du projet de bibliothèque notes .NET Standard dans Visual Studio pour Mac:

![](deepdive-images/vsmac/net-standard-project.png "Contenu du projet de bibliothèque .NET Standard Phoneword ")

Le projet a un nœud **Dépendances** qui contient les nœuds **NuGet** et **SDK** :

- **NuGet** &ndash; les packages NuGet Xamarin. Forms et SQLite-net-PCL qui ont été ajoutés au projet.
- **SDK** &ndash; Métapaquet `NETStandard.Library` qui référence l’ensemble complet de paquets NuGet définissant .NET Standard.

::: zone-end

Le projet comprend également un certain nombre de fichiers :

- **Data\NoteDatabase.cs** : cette classe contient le code permettant de créer la base de données, de lire des données, d’y écrire des données et de supprimer des données de celle-ci.
- **Models\Note.cs** : cette classe définit un `Note` modèle dont les instances stockent les données relatives à chaque note dans l’application.
- **App.xaml** : balisage XAML pour la classe `App`, qui définit un dictionnaire de ressources pour l’application.
- **App.xaml.cs** : code-behind pour la classe `App`, qui est chargé de l’instanciation de la première page affichée par l’application sur chaque plateforme et de la gestion des événements du cycle de vie de l’application.
- **AssemblyInfo.cs** : ce fichier contient un attribut d’application sur le projet, qui est appliqué au niveau de l’assembly.
- **NotesPage. Xaml** : balisage XAML pour la `NotesPage` classe, qui définit l’interface utilisateur pour la page affichée au lancement de l’application.
- **NotesPage.Xaml.cs** : code-behind pour la `NotesPage` classe, qui contient la logique métier exécutée lorsque l’utilisateur interagit avec la page.
- **NoteEntryPage. Xaml** : balisage XAML pour la `NoteEntryPage` classe, qui définit l’interface utilisateur pour la page affichée lorsque l’utilisateur entre une note.
- **NoteEntryPage.Xaml.cs** : code-behind pour la `NoteEntryPage` classe, qui contient la logique métier exécutée lorsque l’utilisateur interagit avec la page.

Pour plus d’informations sur la structure d’une application Xamarin.iOS, consultez [Structure d’une application Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Pour plus d’informations sur la structure d’une application Xamarin.Android, consultez [Structure d’une application Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Notions de base de l’architecture et des applications

Une application Xamarin.Forms est structurée de la même façon qu’une application multiplateforme classique. En général, le code partagé est placé dans une bibliothèque .NET Standard et les applications spécifiques à la plateforme consomment le code partagé. Le diagramme suivant présente une vue d’ensemble de cette relation pour l’application Notes:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Architecture des notes")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Architecture des notes")

::: zone-end

Pour optimiser la réutilisation du code de démarrage, les applications Xamarin.Forms ont une seule classe nommée `App` qui est chargée de l’instanciation de la première page affichée par l’application sur chaque plateforme, comme indiqué dans l’exemple de code suivant :

```csharp
using Xamarin.Forms;

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

Ce code affecte à `MainPage` la propriété de `App` la classe une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance dont le contenu est `NotesPage` une instance.

En outre, le fichier **AssemblyInfo.cs** contient un seul attribut d’application, qui est appliqué au niveau de l’assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

L' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attribut Active le compilateur XAML, afin que le XAML soit compilé directement en langage intermédiaire. Pour plus d’informations, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launching-the-application-on-each-platform"></a>Lancement de l’application sur chaque plateforme

### <a name="ios"></a>iOS

Pour lancer la page Xamarin. Forms initiale dans iOS, le projet notes. iOS définit `AppDelegate` la classe qui hérite de `FormsApplicationDelegate` la classe:

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

Pour lancer la page Xamarin. Forms initiale dans Android, le projet notes. Android comprend du code qui `Activity` crée un `MainLauncher` avec l’attribut, avec l’activité qui hérite de la `FormsAppCompatActivity` classe:

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

Cela entraîne le chargement de l’implémentation spécifique à UWP de Xamarin.Forms dans l’application. La page Xamarin. Forms initiale est lancée `MainPage` par la classe:

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
> Plateforme Windows universelle applications peuvent être générées avec Xamarin. Forms, mais uniquement à l’aide de Visual Studio sur Windows.

::: zone-end

## <a name="user-interface"></a>Interface utilisateur

Quatre groupes de contrôles principaux sont utilisés pour créer l’interface utilisateur d’une application Xamarin. Forms:

1. **Pages** : les pages Xamarin.Forms représentent les écrans de l’application mobile multiplateforme. L’application notes utilise la [`ContentPage`](xref:Xamarin.Forms.ContentPage) classe pour afficher des écrans uniques. Pour plus d’informations sur les pages, consultez [Pages Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md).
1. **Vues** : les vues Xamarin.Forms sont les contrôles affichés sur l’interface utilisateur, tels que les étiquettes, les boutons et les zones d’entrée de texte. L’application notes terminée utilise les [`ListView`](xref:Xamarin.Forms.ListView)vues [`Editor`](xref:Xamarin.Forms.Editor), et [`Button`](xref:Xamarin.Forms.Button) . Pour plus d’informations sur les vues, consultez [Vues Xamarin.Forms](~/xamarin-forms/user-interface/controls/views.md).
1. **Dispositions** : les dispositions Xamarin.Forms sont des conteneurs utilisés pour composer des vues dans des structures logiques. L’application notes utilise la [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe pour réorganiser les vues dans une pile verticale, [`Grid`](xref:Xamarin.Forms.Grid) et la classe pour réorganiser les boutons horizontalement. Pour plus d’informations sur les dispositions, consultez [Dispositions Xamarin.Forms](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Cellules** : les cellules Xamarin.Forms sont des éléments spécialisés utilisés pour des éléments dans une liste et décrivent comment chaque élément dans une liste doit être dessiné. L’application notes utilise le [`TextCell`](xref:Xamarin.Forms.TextCell) pour afficher deux éléments pour chaque ligne de la liste. Pour plus d’informations sur les cellules, consultez [Cellules Xamarin.Forms](~/xamarin-forms/user-interface/controls/cells.md).

Lors de l’exécution, chaque contrôle est mappé à son équivalent natif, ce qui sera affiché.

### <a name="layout"></a>Mise en page

L’application notes utilise le [`StackLayout`](xref:Xamarin.Forms.StackLayout) pour simplifier le développement d’applications interplateformes en organisant automatiquement des vues sur l’écran, quelle que soit la taille de l’écran. Les éléments enfants sont placés les uns après les autres, horizontalement ou verticalement dans l’ordre dans lequel ils ont été ajoutés. La quantité d’espace utilisée par la classe `StackLayout` dépend de la définition des propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), mais `StackLayout` essaie par défaut d’utiliser la totalité de l’écran.

Le code XAML suivant illustre un exemple d’utilisation d' [`StackLayout`](xref:Xamarin.Forms.StackLayout) un pour mettre `NoteEntryPage`en page le:

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

Par défaut, [`StackLayout`](xref:Xamarin.Forms.StackLayout) le suppose une orientation verticale. Toutefois, il peut être remplacé par une orientation horizontale en affectant [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) à la propriété [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) le membre de l’énumération.

> [!NOTE]
> La taille des vues peut être définie à l' `HeightRequest` aide `WidthRequest` des propriétés et.

Pour plus d’informations sur la classe [`StackLayout`](xref:Xamarin.Forms.StackLayout), consultez [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Réponse aux interactions de l’utilisateur

Un objet défini en XAML peut déclencher un événement qui est géré dans le fichier code-behind. L’exemple de code suivant montre `OnSaveButtonClicked` la méthode dans le code-behind pour `NoteEntryPage` la classe, qui est exécutée en réponse [`Clicked`](xref:Xamarin.Forms.Button.Clicked) à l’événement déclenché sur le bouton *Enregistrer* .

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

La `OnSaveButtonClicked` méthode enregistre la note dans la base de données et revient à la page précédente.

> [!NOTE]
> Le fichier code-behind d’une classe XAML peut accéder à un objet défini en XAML en utilisant le nom qui lui est affecté avec l’attribut `x:Name`. La valeur affectée à cet attribut suit les mêmes règles que les variables C# : elle doit commencer par une lettre ou un trait de soulignement et ne contenir aucun espace incorporé.

La connexion du bouton d’enregistrement à la `OnSaveButtonClicked` méthode se produit dans le balisage XAML `NoteEntryPage` pour la classe:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listes

[`ListView`](xref:Xamarin.Forms.ListView) Est chargé d’afficher une collection d’éléments verticalement dans une liste. Chaque élément dans le `ListView` sera contenu dans une cellule unique.

L’exemple de code suivant montre [`ListView`](xref:Xamarin.Forms.ListView) le à `NotesPage`partir du:

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

La disposition de chaque ligne [`ListView`](xref:Xamarin.Forms.ListView) du est définie dans l’élément et utilise la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) liaison de données pour afficher toutes les notes récupérées par l’application. La [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété est définie sur la source de données, `NotesPage.xaml.cs`dans:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Ce code remplit la [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes stockées dans la base de données.

Lorsqu’une ligne est sélectionnée dans le [`ListView`](xref:Xamarin.Forms.ListView), l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement se déclenche. Un gestionnaire d’événements, `OnListViewItemSelected`nommé, est exécuté lorsque l’événement se déclenche:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

L' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement peut accéder à l’objet qui a été associé à la cellule [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) par le biais de la propriété.

Pour plus d’informations sur [`ListView`](xref:Xamarin.Forms.ListView) la classe, consultez [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigation

Xamarin.Forms propose plusieurs expériences de navigation entre les pages différentes, selon le type de [`Page`](xref:Xamarin.Forms.Page) utilisé. Pour [`ContentPage`](xref:Xamarin.Forms.ContentPage) les instances, la navigation peut être hiérarchique ou modale. Pour plus d’informations sur la navigation modale, consultez [pages modales Xamarin. Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Les classes [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) et [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) fournissent d’autres expériences de navigation. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md).

Dans la navigation hiérarchique, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe est utilisée pour naviguer dans une pile d' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets, vers l’avant et vers l’arrière, selon les besoins. La classe implémente la navigation comme une pile d’objets [`Page`](xref:Xamarin.Forms.Page) LIFO (dernier entré, premier sorti). Pour passer d’une page à une autre, une application envoie une nouvelle page dans la pile de navigation, où elle devient la page active. Pour revenir à la page précédente, l’application dépile la page actuelle, et la nouvelle page tout en haut devient la page active.

La classe `NavigationPage` ajoute également une barre de navigation en haut de la page qui affiche un titre et un bouton **Précédent** approprié à la plateforme qui permet de revenir à la page précédente.

La première page ajoutée à une pile de navigation est appelée page *racine* de l’application, et l’exemple de code suivant montre comment cela est accompli dans l’application Notes:

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

Cela amène le nouvel `NoteEntryPage` objet à faire l’objet d’un push dans la pile de navigation, où il devient la page active.

La page active peut être retirée de la pile de navigation en appuyant sur le bouton *Précédent* sur l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran. Pour retourner par programmation à la page d’origine, l’objet `NoteEntryPage` doit appeler la méthode [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), comme illustré dans l’exemple de code ci-dessous :

```csharp
await Navigation.PopAsync();
```

Pour plus d’informations sur la navigation hiérarchique, consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Liaison de données

La liaison de données permet de simplifier la façon dont une application Xamarin.Forms affiche ses données et interagit avec elles. Elle établit une connexion entre l’interface utilisateur et l’application sous-jacente. La classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) contient une grande partie de l’infrastructure pour prendre en charge la liaison de données.

La liaison de données connecte deux objets, appelés la *source* et la *cible*. L’objet *source* fournit les données. L’objet *cible* consomme (et affiche souvent) les données de l’objet source. Par exemple, un [`Editor`](xref:Xamarin.Forms.Editor) (objet*cible* ) lie généralement sa [`Text`](xref:Xamarin.Forms.Editor.Text) propriété à une propriété publique `string` dans un objet *source* . Le diagramme suivant illustre la relation de liaison :

![](deepdive-images/data-binding.png "Liaison de données")

Le principal avantage de la liaison de données est que vous n’avez plus à vous soucier de la synchronisation des données entre les vues et la source de données. Les modifications apportées à l’objet *source* sont automatiquement envoyées à l’objet *cible* dans les coulisses par le framework de liaison, et les modifications apportées à l’objet cible peuvent éventuellement être renvoyées à l’objet *source*.

L’établissement de la liaison de données est un processus en deux étapes:

- La propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de l’objet *cible* doit être définie sur la *source*.
- Une liaison doit être établie entre la *cible* et la *source*. En XAML, cette opération est effectuée en utilisant l’extension de balisage [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

Dans l’application notes, [`Editor`](xref:Xamarin.Forms.Editor) la cible [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de liaison est le qui affiche une note, tandis que l' `Note` instance définie `NoteEntryPage` en tant que de est la source de liaison.

`BindingContext` Le`NoteEntryPage` de est défini pendant la navigation entre les pages, comme indiqué dans l’exemple de code suivant:

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

Dans la `OnNoteAddedClicked` méthode, qui est exécutée lorsqu’une nouvelle note est ajoutée à l’application, [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) la `NoteEntryPage` valeur de est définie sur `Note` une nouvelle instance. Dans la `OnListViewItemSelected` méthode, qui est exécutée quand une note existante est sélectionnée dans [`ListView`](xref:Xamarin.Forms.ListView)le, `BindingContext` le du `NoteEntryPage` est défini sur l’instance `Note` sélectionnée, accessible via la [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriété.

> [!IMPORTANT]
> Alors que la propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de chaque objet *cible* peut être définie individuellement, cela n’est pas nécessaire. `BindingContext` est une propriété spéciale qui est héritée par tous ses enfants. Par conséquent, lorsque `BindingContext` le [`ContentPage`](xref:Xamarin.Forms.ContentPage) sur est défini sur une `Note` instance de, tous les enfants de l' `ContentPage` objet ont le `BindingContext`même et peuvent effectuer une liaison aux propriétés publiques `Note` de l’objet.

Le [`Editor`](xref:Xamarin.Forms.Editor) `Text` `Note` dans `NoteEntryPage` crée alors une liaison avec la propriété de l’objet:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Une liaison entre la propriété [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) et la propriété `Text` de l’objet *source* est établie. Les modifications apportées dans le `Editor` sont automatiquement propagées à l' `Note` objet. De même, si des modifications sont apportées à la `Note.Text` propriété, le moteur `Editor`de liaison Xamarin. Forms met également à jour le contenu du. Il s’agit là d’une *liaison bidirectionnelle*.

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Styles

Les applications Xamarin. Forms contiennent souvent plusieurs éléments visuels qui ont une apparence identique. La définition de l’apparence de chaque élément visuel peut être répétitive et sujette aux erreurs. Au lieu de cela, vous pouvez créer des styles qui définissent l’apparence, puis les appliquer aux éléments visuels requis.

La [`Style`](xref:Xamarin.Forms.Style) classe regroupe une collection de valeurs de propriété dans un objet qui peut ensuite être appliqué à plusieurs instances d’éléments visuels. Les styles sont stockés dans [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)un, au niveau de l’application, au niveau de la page ou au niveau de la vue. Choix de l’emplacement de `Style` définition d’un impact à utiliser:

- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de l’application peuvent être appliquées dans l’ensemble de l’application.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de la page peuvent être appliquées à la page et à ses enfants.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de la vue peuvent être appliquées à la vue et à ses enfants.

> [!IMPORTANT]
> Tout style utilisé dans l’application est stocké dans le dictionnaire de ressources de l’application pour éviter la duplication. Toutefois, le code XAML qui est spécifique à une page ne doit pas être inclus dans le dictionnaire de ressources de l’application, car les ressources sont ensuite analysées au démarrage de l’application et non pas lorsqu’une page l’exige.

Chaque [`Style`](xref:Xamarin.Forms.Style) instance contient une collection d’un ou de [`Setter`](xref:Xamarin.Forms.Setter) plusieurs objets, chacun `Setter` ayant un [`Property`](xref:Xamarin.Forms.Setter.Property) et un [`Value`](xref:Xamarin.Forms.Setter.Value). Est le nom de la propriété pouvant être liée de l’élément auquel le style est appliqué, `Value` et est la valeur qui est appliquée à la propriété. `Property` L’exemple de code suivant montre un style `NoteEntryPage`à partir de:

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

Ce style est appliqué à toutes [`Editor`](xref:Xamarin.Forms.Editor) les instances de la page.

Lors de la [`Style`](xref:Xamarin.Forms.Style)création d' [`TargetType`](xref:Xamarin.Forms.Style.TargetType) un, la propriété est toujours obligatoire.

> [!NOTE]
> Le style d’une application Xamarin. Forms s’effectue généralement à l’aide de styles XAML. Toutefois, Xamarin. Forms prend également en charge le style des éléments visuels à l’aide d’feuilles de style en cascade (CSS). Pour plus d’informations, consultez stylisation des [applications Xamarin. Forms à l’aide d’feuilles de style en cascade (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Pour plus d’informations sur les styles XAML, consultez [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>Fournir des styles spécifiques à la plateforme

Les `OnPlatform` extensions de balisage vous permettent de personnaliser l’apparence de l’interface utilisateur pour chaque plateforme:

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

Cela [`Style`](xref:Xamarin.Forms.Style) permet de [`Color`](xref:Xamarin.Forms.Color) définir des valeurs [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) différentes [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) pour les [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)propriétés et de, en fonction de la plateforme utilisée.

Pour plus d’informations sur les extensions de balisage XAML, consultez [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Pour plus d’informations `OnPlatform` sur l’extension de balisage, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Test et déploiement

Visual Studio pour Mac et Visual Studio proposent tous deux de nombreuses options pour tester et déployer les applications. Le débogage d’applications est une partie courante du cycle de vie de développement des applications et permet de diagnostiquer les problèmes de code. Pour plus d’informations, consultez [Définir un point d’arrêt](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Exécuter du code pas à pas](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) et [Informations de sortie dans la fenêtre de journal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Les simulateurs constituent un endroit judicieux pour commencer à déployer et tester une application, et comportent des fonctionnalités utiles pour le test des applications. Toutefois, les utilisateurs ne consommant pas l’application finale dans un simulateur, les applications doivent être testées tôt et souvent sur des appareils réels. Pour plus d’informations sur le provisionnement des appareils iOS, consultez [Provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md). Pour plus d’informations sur le provisionnement des appareils Android, consultez [Configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Étapes suivantes

Cette présentation approfondie a examiné les notions de base du développement d’applications à l’aide de Xamarin. Forms. Les étapes suggérées ci-dessous incluent la découverte des fonctionnalités suivantes :

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

- [XAML (eXtensible Application Markup Language)](~/xamarin-forms/xaml/index.md)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Informations de référence sur les contrôles](~/xamarin-forms/user-interface/controls/index.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Exemples pour bien démarrer](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Informations de référence sur les API Xamarin.Forms](xref:Xamarin.Forms)
- [Autoformation gratuite (vidéo)](https://university.xamarin.com/self-guided/)
