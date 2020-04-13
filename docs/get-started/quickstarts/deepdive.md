---
title: Xamarin.Forms - Démarrage rapide en immersion
description: Cet article examine les notions de base du développement d’applications avec Xamarin.Forms. Les sujets abordés étaient notamment la structure d’une application Xamarin.Forms, les concepts fondamentaux d’une architecture et d’une application, et l’interface utilisateur.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: c0e8ec27898cc842d485967e525c2936d7a0f56d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131048"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.Forms - Démarrage rapide en immersion

Dans [Xamarin.Forms - Démarrage rapide](~/get-started/index.yml), l’application Notes a été générée. Cet article passe en revue les éléments générés afin d’acquérir les notions de base du fonctionnement des applications Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduction à Visual Studio

Visual Studio organise le code en *Solutions* et *Projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application Notes se compose d’une solution qui contient quatre projets, comme le montre la capture d’écran suivante :

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

Les projets sont :

- Remarques - Ce projet est le projet de bibliothèque .NET Standard qui contient l’ensemble du code partagé et de l’IU partagée.
- Notes.Android - Ce projet contient du code spécifique à Android et constitue le point d’entrée pour l’application Android.
- Notes.iOS - Ce projet contient du code spécifique à iOS et constitue le point d’entrée pour l’application iOS.
- Notes.UWP - Ce projet contient du code spécifique à la plateforme UWP (plateforme Windows universelle) et constitue le point d’entrée pour l’application UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Structure d’une application Xamarin.Forms

La capture d’écran suivante montre le contenu du projet de bibliothèque .NET Standard Notes dans Visual Studio :

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

Le projet a un nœud **de dépendance qui** contient des nœuds **NuGet** et **SDK:**

- **NuGet** &ndash; Paquets NuGet Xamarin.Forms et sqlite-net-pcl ajoutés au projet.
- **SDK** &ndash; Métapaquet `NETStandard.Library` qui référence l’ensemble complet des paquets NuGet définissant .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduction à Visual Studio pour Mac

Tout comme Visual Studio, [Visual Studio pour Mac](/visualstudio/mac/) organise le code en *solutions* et en *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application Notes se compose d’une solution qui contient trois projets, comme le montre la capture d’écran suivante :

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

Les projets sont :

- Remarques - Ce projet est le projet de bibliothèque .NET Standard qui contient l’ensemble du code partagé et de l’IU partagée.
- Notes.Android - Ce projet contient du code spécifique à Android et constitue le point d’entrée pour les applications Android.
- Notes.iOS - Ce projet contient du code spécifique à iOS et constitue le point d’entrée pour les applications iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Structure d’une application Xamarin.Forms

La capture d’écran suivante montre le contenu du projet de bibliothèque .NET Standard Notes dans Visual Studio pour Mac :

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

Le projet a un nœud **de dépendance qui** contient des nœuds **NuGet** et **SDK:**

- **NuGet** &ndash; Paquets NuGet Xamarin.Forms et sqlite-net-pcl ajoutés au projet.
- **SDK** &ndash; Métapaquet `NETStandard.Library` qui référence l’ensemble complet des paquets NuGet définissant .NET Standard.

::: zone-end

Le projet comprend également un certain nombre de fichiers :

- **Data\NoteDatabase.cs** - Cette classe contient du code qui permet de créer la base de données, de lire les données existantes, d’en écrire de nouvelles ou d’en supprimer.
- **Models\Note.cs** - Cette classe définit un modèle `Note` dont les instances stockent les données relatives à chaque note de l’application.
- **App.xaml** : balisage XAML pour la classe `App`, qui définit un dictionnaire de ressources pour l’application.
- **App.xaml.cs** : code-behind pour la classe `App`, qui est chargé de l’instanciation de la première page affichée par l’application sur chaque plateforme et de la gestion des événements du cycle de vie de l’application.
- **AssemblyInfo.cs** - Ce fichier contient un attribut d’application sur le projet, qui est appliqué au niveau de l’assembly.
- **NotesPage.xaml** - Balisage XAML pour la classe `NotesPage`, qui définit l’IU de la page affichée au lancement de l’application.
- **NotesPage.xaml.cs** - Code-behind de la classe `NotesPage`, qui contient la logique métier exécutée quand l’utilisateur interagit avec la page.
- **NoteEntryPage.xaml** - Balisage XAML pour la classe `NoteEntryPage`, qui définit l’IU de la page affichée quand l’utilisateur entre une note.
- **NoteEntryPage.xaml.cs** - Code-behind de la classe `NoteEntryPage`, qui contient la logique métier exécutée quand l’utilisateur interagit avec la page.

Pour plus d’informations sur la structure d’une application Xamarin.iOS, consultez [Structure d’une application Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Pour plus d’informations sur la structure d’une application Xamarin.Android, consultez [Structure d’une application Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Concepts fondamentaux de l’architecture et de l’application

Une application Xamarin.Forms est structurée de la même façon qu’une application multiplateforme classique. En général, le code partagé est placé dans une bibliothèque .NET Standard et les applications spécifiques à la plateforme consomment le code partagé. Le diagramme suivant présente une vue d’ensemble de cette relation pour l’application Notes :

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

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

Ce code `MainPage` définit la `App` propriété [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) de la classe `NotesPage` à une instance dont le contenu est une instance.

De plus, le fichier **AssemblyInfo.cs** contient un seul attribut d’application, qui est appliqué au niveau de l’assembly :

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

L’attribut [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) tourne sur le compilateur XAML, de sorte que XAML est compilé directement dans la langue intermédiaire. Pour plus d’informations, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launching-the-application-on-each-platform"></a>Lancement de l’application sur chaque plateforme

### <a name="ios"></a>iOS

Pour lancer la page Xamarin.Forms initiale dans iOS, le projet Notes.iOS définit la classe `AppDelegate` qui hérite de la classe `FormsApplicationDelegate` :

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

Pour lancer la page Xamarin.Forms initiale dans Android, le projet Notes.Android inclut du code qui crée un `Activity` avec l’attribut `MainLauncher`, l’activité héritant de la classe `FormsAppCompatActivity` :

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

Cela entraîne le chargement de l’implémentation spécifique à UWP de Xamarin.Forms dans l’application. La page initiale de Xamarin.Forms est lancée par la classe `MainPage` :

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
> Vous pouvez générer les applications UWP (plateforme Windows universelle) avec Xamarin.Forms, mais uniquement à l’aide de Visual Studio sur Windows.

::: zone-end

## <a name="user-interface"></a>Interface utilisateur

Il existe quatre groupes de contrôles principaux permettant de créer l’interface utilisateur d’une application Xamarin.Forms :

1. **Pages** : les pages Xamarin.Forms représentent les écrans de l’application mobile multiplateforme. L’application Notes [`ContentPage`](xref:Xamarin.Forms.ContentPage) utilise la classe pour afficher des écrans simples. Pour plus d’informations sur les pages, consultez [Pages Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md).
1. **Vues** : les vues Xamarin.Forms sont les contrôles affichés sur l’interface utilisateur, tels que les étiquettes, les boutons et les zones d’entrée de texte. L’application Notes [`ListView`](xref:Xamarin.Forms.ListView)finie utilise le , [`Editor`](xref:Xamarin.Forms.Editor), et [`Button`](xref:Xamarin.Forms.Button) les vues. Pour plus d’informations sur les vues, consultez [Vues Xamarin.Forms](~/xamarin-forms/user-interface/controls/views.md).
1. **Dispositions** : les dispositions Xamarin.Forms sont des conteneurs utilisés pour composer des vues dans des structures logiques. L’application Notes [`StackLayout`](xref:Xamarin.Forms.StackLayout) utilise la classe pour organiser [`Grid`](xref:Xamarin.Forms.Grid) des vues dans une pile verticale, et la classe pour organiser des boutons horizontalement. Pour plus d’informations sur les dispositions, consultez [Dispositions Xamarin.Forms](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Cellules** : les cellules Xamarin.Forms sont des éléments spécialisés utilisés pour des éléments dans une liste et décrivent comment chaque élément dans une liste doit être dessiné. L’application Notes [`TextCell`](xref:Xamarin.Forms.TextCell) utilise l’affichage de deux éléments pour chaque rangée dans la liste. Pour plus d’informations sur les cellules, consultez [Cellules Xamarin.Forms](~/xamarin-forms/user-interface/controls/cells.md).

Lors de l’exécution, chaque contrôle est mappé à son équivalent natif, ce qui sera affiché.

### <a name="layout"></a>Mise en page

L’application Notes [`StackLayout`](xref:Xamarin.Forms.StackLayout) utilise le pour simplifier le développement d’applications multiplateforme en arrangeant automatiquement les vues sur l’écran indépendamment de la taille de l’écran. Les éléments enfants sont placés les uns après les autres, horizontalement ou verticalement dans l’ordre dans lequel ils ont été ajoutés. La quantité `StackLayout` d’espace que [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) l’utilisation dépendra de la façon dont les propriétés et [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) les propriétés sont définies, mais par défaut, la `StackLayout` volonté d’utiliser l’écran entier.

Le code XAML suivant montre [`StackLayout`](xref:Xamarin.Forms.StackLayout) un exemple `NoteEntryPage`d’utilisation d’un pour mettre en page le :

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

Par défaut, l’hypothèse [`StackLayout`](xref:Xamarin.Forms.StackLayout) d’une orientation verticale. Cependant, il peut être changé en [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) une orientation [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) horizontale en fixant la propriété au membre de recensement.

> [!NOTE]
> Vous pouvez définir la taille des vues à l’aide des propriétés `HeightRequest` et `WidthRequest`.

Pour plus d’informations sur la [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe, voir [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Réponse aux interactions de l’utilisateur

Un objet défini en XAML peut déclencher un événement qui est géré dans le fichier code-behind. L’exemple de `OnSaveButtonClicked` code suivant montre la `NoteEntryPage` méthode dans le code-derrière [`Clicked`](xref:Xamarin.Forms.Button.Clicked) pour la classe, qui est exécuté en réponse à l’événement de tir sur le bouton *Enregistrer.*

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

La méthode `OnSaveButtonClicked` enregistre la note dans la base de données, puis revient à la page précédente.

> [!NOTE]
> Le fichier code-behind d’une classe XAML peut accéder à un objet défini en XAML en utilisant le nom qui lui est affecté avec l’attribut `x:Name`. La valeur affectée à cet attribut suit les mêmes règles que les variables C# : elle doit commencer par une lettre ou un trait de soulignement et ne contenir aucun espace incorporé.

L’association du bouton d’enregistrement à la méthode `OnSaveButtonClicked` se produit dans le balisage XAML pour la classe `NoteEntryPage` :

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listes

Le [`ListView`](xref:Xamarin.Forms.ListView) est responsable de l’affichage d’une collection d’articles verticalement dans une liste. Chaque élément de `ListView` est contenu dans une seule cellule.

L’exemple de [`ListView`](xref:Xamarin.Forms.ListView) code `NotesPage`suivant montre le :

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

La disposition de chaque [`ListView`](xref:Xamarin.Forms.ListView) ligne de [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) l’élément est définie dans l’élément, et utilise la liaison de données pour afficher toutes les notes qui sont récupérées par l’application. La [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété est définie à `NotesPage.xaml.cs`la source de données, dans :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Ce code remplit [`ListView`](xref:Xamarin.Forms.ListView) les notes stockées dans la base de données.

Quand une rangée est [`ListView`](xref:Xamarin.Forms.ListView)sélectionnée [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) dans le , l’événement s’allume. Un gestionnaire d’événements, nommé `OnListViewItemSelected`, s’exécute quand l’événement se déclenche :

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

L’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) peut accéder à l’objet [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) qui a été associé à la cellule à travers la propriété.

Pour plus d’informations sur la [`ListView`](xref:Xamarin.Forms.ListView) classe, voir [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigation

Xamarin.Forms fournit un certain nombre d’expériences [`Page`](xref:Xamarin.Forms.Page) de navigation de page différentes, selon le type utilisé. Par [`ContentPage`](xref:Xamarin.Forms.ContentPage) exemple, la navigation peut être hiérarchique ou modale. Pour plus d’informations sur la navigation modale, consultez [Pages modales Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Les [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) cours [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) et les classes offrent d’autres expériences de navigation. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md).

Dans la navigation hiérarchique, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) est utilisée pour naviguer à travers une pile d’objets, vers l’avant et vers l’arrière, comme vous le souhaitez. La classe met en œuvre la navigation comme une [`Page`](xref:Xamarin.Forms.Page) dernière pile d’objets (LIFO). Pour passer d’une page à une autre, une application envoie une nouvelle page dans la pile de navigation, où elle devient la page active. Pour revenir à la page précédente, l’application dépile la page actuelle, et la nouvelle page tout en haut devient la page active.

La classe `NavigationPage` ajoute également une barre de navigation en haut de la page qui affiche un titre et un bouton **Précédent** approprié à la plateforme qui permet de revenir à la page précédente.

La première page ajoutée à une pile de navigation est appelée la page *racine* de l’application. L’exemple de code suivant illustre le déroulement de l’opération dans l’application Notes :

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Tous [`ContentPage`](xref:Xamarin.Forms.ContentPage) les [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) cas ont une propriété qui expose des méthodes pour modifier la pile de page. Ces méthodes ne doivent être invoquées [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)que si la demande comprend un . Pour naviguer `NoteEntryPage`vers le , il [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) est nécessaire d’invoquer la méthode comme démontré dans l’exemple de code ci-dessous:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

Ainsi, le nouvel ​​objet `NoteEntryPage` est envoyé (push) vers la pile de navigation, où il devient la page active.

La page active peut être retirée de la pile de navigation en appuyant sur le bouton *Précédent* sur l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran. Pour revenir de façon programmatique à `NoteEntryPage` la page [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) originale, l’objet doit invoquer la méthode, comme le démontre l’exemple du code ci-dessous :

```csharp
await Navigation.PopAsync();
```

Pour plus d’informations sur la navigation hiérarchique, consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Liaison de données

La liaison de données permet de simplifier la façon dont une application Xamarin.Forms affiche ses données et interagit avec elles. Elle établit une connexion entre l’interface utilisateur et l’application sous-jacente. La [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe contient une grande partie de l’infrastructure pour soutenir la liaison des données.

La liaison de données connecte deux objets, appelés la *source* et la *cible*. L’objet *source* fournit les données. L’objet *cible* consomme (et affiche souvent) les données de l’objet source. Par exemple, [`Editor`](xref:Xamarin.Forms.Editor) un (objet*cible)* lie généralement sa [`Text`](xref:Xamarin.Forms.InputView.Text) propriété à un bien public `string` dans un objet *source.* Le diagramme suivant illustre la relation de liaison :

![](deepdive-images/data-binding.png "Data Binding")

Le principal avantage de la liaison de données est que vous n’avez plus à vous soucier de la synchronisation des données entre les vues et la source de données. Les modifications apportées à l’objet *source* sont automatiquement envoyées à l’objet *cible* dans les coulisses par le framework de liaison, et les modifications apportées à l’objet cible peuvent éventuellement être renvoyées à l’objet *source*.

L’établissement de la liaison de données est un processus en deux étapes :

- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété de l’objet *cible* doit être réglée à la *source*.
- Une liaison doit être établie entre la *cible* et la *source*. Dans XAML, cela est [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) réalisé en utilisant l’extension de balisage.

Dans l’application Notes, l’objectif de liaison [`Editor`](xref:Xamarin.Forms.Editor) `Note` est celui [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) qui `NoteEntryPage` affiche une note, tandis que l’instance définie comme la source de liaison est la source de liaison.

Le `BindingContext` de `NoteEntryPage` est défini pendant la navigation dans la page, comme illustré dans l’exemple de code suivant :

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

Dans `OnNoteAddedClicked` la méthode, qui est exécutée lorsqu’une nouvelle [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `NoteEntryPage` note est ajoutée `Note` à l’application, la de est définie à une nouvelle instance. Dans `OnListViewItemSelected` la méthode, qui est exécutée lorsqu’une `BindingContext` note `NoteEntryPage` existante est sélectionnée `Note` dans le [`ListView`](xref:Xamarin.Forms.ListView), le [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) de celui-ci est réglé à l’instance sélectionnée, qui est accessible par la propriété.

> [!IMPORTANT]
> Bien [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) que la propriété de chaque objet *cible* puisse être réglée individuellement, ce n’est pas nécessaire. `BindingContext` est une propriété spéciale qui est héritée par tous ses enfants. Par conséquent, `BindingContext` lorsque [`ContentPage`](xref:Xamarin.Forms.ContentPage) le sur `Note` le est réglé à `ContentPage` une `BindingContext`instance, tous les enfants `Note` de l’ont le même , et peut se lier aux propriétés publiques de l’objet.

L’in [`Editor`](xref:Xamarin.Forms.Editor) `NoteEntryPage` se lie `Text` alors `Note` à la propriété de l’objet :

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Une liaison [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) entre le `Text` bien et la propriété de l’objet *source* est établie. Les changements apportés dans `Editor` sont automatiquement propagés à l’objet `Note`. De même, si des changements sont apportés à la propriété `Note.Text`, le moteur de liaison Xamarin.Forms met également à jour le contenu de `Editor`. Il s’agit là d’une *liaison bidirectionnelle*.

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Style

Les applications Xamarin.Forms contiennent souvent plusieurs éléments visuels qui ont une apparence identique. La définition de l’apparence de chaque élément visuel peut être répétitive et sujette aux erreurs. À la place, vous pouvez créer des styles qui définissent l’apparence, puis les appliquer aux éléments visuels nécessaires.

La [`Style`](xref:Xamarin.Forms.Style) classe regroupe une collection de valeurs de propriété en un seul objet qui peut ensuite être appliqué à plusieurs instances d’éléments visuels. Les styles sont [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)stockés dans un , soit au niveau de l’application, le niveau de page, ou le niveau de vue. Le fait de choisir où définir `Style` impacte l’emplacement où il peut être utilisé :

- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de l’application peuvent être appliquées tout au long de la demande.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de la page peuvent être appliquées à la page et à ses enfants.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de vue peuvent être appliquées à la vue et à ses enfants.

> [!IMPORTANT]
> Tous les styles utilisés dans l’application sont stockés dans le dictionnaire de ressources de l’application pour éviter la duplication. Toutefois, le code XAML qui est spécifique à une page ne doit pas être inclus dans le dictionnaire de ressources de l’application, car les ressources sont alors analysées au démarrage de l’application et non lorsqu’une page le demande.

Chaque [`Style`](xref:Xamarin.Forms.Style) instance contient une collection [`Setter`](xref:Xamarin.Forms.Setter) d’un `Setter` ou [`Property`](xref:Xamarin.Forms.Setter.Property) plusieurs [`Value`](xref:Xamarin.Forms.Setter.Value)objets, chacun ayant un et un . `Property` est le nom de la propriété pouvant être liée de l’élément auquel le style est appliqué, et `Value` est la valeur qui est appliquée à la propriété. L’exemple de code suivant illustre un style de `NoteEntryPage` :

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

Ce style est [`Editor`](xref:Xamarin.Forms.Editor) appliqué à toutes les instances de la page.

Lors de [`Style`](xref:Xamarin.Forms.Style)la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) création d’un , la propriété est toujours nécessaire.

> [!NOTE]
> L’application de styles dans une application Xamarin.Forms est généralement obtenue à l’aide de styles XAML. Toutefois, Xamarin.Forms prend également en charge l’application de styles à des éléments visuels à l’aide du CSS (feuilles de style en cascade). Pour plus d’informations, consultez [Style des applications Xamarin.Forms avec des feuilles de style en cascade (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Pour plus d’informations sur les styles XAML, consultez [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>Fourniture de styles spécifiques à la plateforme

Les extensions de balisage `OnPlatform` vous permettent de personnaliser l’apparence de l’IU pour chaque plateforme :

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

Cela [`Style`](xref:Xamarin.Forms.Style) définit [`Color`](xref:Xamarin.Forms.Color) des [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) valeurs [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) différentes [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)pour le et les propriétés de , en fonction de la plate-forme utilisée.

Pour plus d’informations sur les extensions de balisage XAML, consultez [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Pour plus d’informations sur l’extension de balisage `OnPlatform`, consultez [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Tests et déploiement

Visual Studio pour Mac et Visual Studio proposent tous deux de nombreuses options pour tester et déployer les applications. Le débogage d’applications est une partie courante du cycle de vie de développement des applications et permet de diagnostiquer les problèmes de code. Pour plus d’informations, consultez [Définir un point d’arrêt](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Exécuter du code pas à pas](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) et [Informations de sortie dans la fenêtre de journal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Les simulateurs constituent un endroit judicieux pour commencer à déployer et tester une application, et comportent des fonctionnalités utiles pour le test des applications. Toutefois, les utilisateurs ne consommant pas l’application finale dans un simulateur, les applications doivent être testées tôt et souvent sur des appareils réels. Pour plus d’informations sur le provisionnement des appareils iOS, consultez [Provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md). Pour plus d’informations sur le provisionnement des appareils Android, consultez [Configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Étapes suivantes

Cette immersion a permis d’examiner les notions de base du développement d’applications à l’aide de Xamarin.Forms. Les étapes suggérées ci-dessous incluent la découverte des fonctionnalités suivantes :

- Il existe quatre principaux groupes de contrôles permettant de créer l’interface utilisateur d’une application Xamarin.Forms. Pour plus d’informations, consultez [Informations de référence sur les contrôles](~/xamarin-forms/user-interface/controls/index.md).
- La liaison de données est une technique qui permet de lier les propriétés de deux objets afin que les changements d’une propriété soient automatiquement répercutés sur l’autre propriété. Pour plus d’informations, consultez [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms propose plusieurs expériences différentes de navigation dans les pages, selon le type de page utilisé. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Les styles contribuent à réduire le balisage répétitif et permettent de modifier plus facilement l’apparence des applications. Pour plus d’informations, consultez [Applications de style Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- Les extensions de balisage XAML étendent la puissance et la flexibilité du langage XAML en permettant de définir des attributs d’éléments à partir de sources autres que des chaînes de texte littéral. Pour plus d’informations, consultez [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Les modèles de données permettent de définir la présentation des données sur les affichages pris en charge. Pour plus d’informations, consultez [Modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Chaque page, disposition et affichage est restitué différemment sur chaque plateforme avec une classe `Renderer` qui crée ensuite un contrôle natif, le dispose à l’écran et ajoute le comportement spécifié dans le code partagé. Les développeurs peuvent implémenter leurs propres classes `Renderer` pour personnaliser l’apparence et/ou le comportement d’un contrôle. Pour plus d’informations, consultez [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Les effets permettent également de personnaliser les contrôles natifs sur chaque plateforme. Les effets sont créés dans des [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) projets spécifiques à la plate-forme en sous-classant la classe, et sont consommés en les attachant à un contrôle approprié Xamarin.Forms. Pour plus d’informations, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).
- Le code partagé peut accéder [`DependencyService`](xref:Xamarin.Forms.DependencyService) aux fonctionnalités natives par l’intermédiaire de la classe. Pour plus d’informations, consultez [Accès aux fonctionnalités natives avec DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Alternativement, [_Créer des applications mobiles avec Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md), un livre de Charles Petzold, est un bon endroit pour en savoir plus sur Xamarin.Forms. Le livre est disponible au format PDF ou dans une variété de formats électroniques.

## <a name="related-links"></a>Liens connexes

- [XAML (eXtensible Application Markup Language)](~/xamarin-forms/xaml/index.yml)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Référence des contrôles](~/xamarin-forms/user-interface/controls/index.md)
- [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Obtenir des échantillons de démarrage](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Informations de référence sur les API Xamarin.Forms](xref:Xamarin.Forms)
- [Autoformation gratuite (vidéo)](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
