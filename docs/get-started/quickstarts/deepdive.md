---
title: Xamarin.FormsPrésentation approfondie des Démarrages rapides
description: Cet article examine les notions de base du développement d’applications à l’aide de Xamarin.Forms . Les rubriques couvertes comprenaient l’anatomie d’une Xamarin.Forms application, l’architecture et les notions de base de l’application, ainsi que l’interface utilisateur.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d1670506fa78b39825368fc91d2452a44ddf8f03
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939917"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.FormsPrésentation approfondie des Démarrages rapides

Dans le Guide de [ Xamarin.Forms démarrage rapide](~/get-started/index.yml), l’application notes a été générée. Cet article passe en revue ce qui a été conçu pour comprendre les principes de base du fonctionnement des Xamarin.Forms applications.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduction à Visual Studio

Visual Studio organise le code en *solutions* et *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application Notes se compose d’une solution qui contient quatre projets, comme le montre la capture d’écran suivante :

![Explorateur de solutions Visual Studio](deepdive-images/vs/solution.png)

Les projets sont :

- Remarques - Ce projet est le projet de bibliothèque .NET Standard qui contient l’ensemble du code partagé et de l’IU partagée.
- Notes.Android - Ce projet contient du code spécifique à Android et constitue le point d’entrée pour l’application Android.
- Notes.iOS - Ce projet contient du code spécifique à iOS et constitue le point d’entrée pour l’application iOS.
- Notes.UWP - Ce projet contient du code spécifique à la plateforme UWP (plateforme Windows universelle) et constitue le point d’entrée pour l’application UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomie d’une Xamarin.Forms application

La capture d’écran suivante montre le contenu du projet de bibliothèque .NET Standard Notes dans Visual Studio :

![Contenu du projet .NET Standard Phoneword](deepdive-images/vs/net-standard-project.png)

Le projet a un nœud **dépendances** qui contient les nœuds **NuGet** et **SDK** :

- **NuGet** &ndash; les Xamarin.Forms packages NuGet et SQLite-net-PCL qui ont été ajoutés au projet.
- **SDK** &ndash; Métapaquet `NETStandard.Library` qui référence l’ensemble complet des paquets NuGet définissant .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduction à Visual Studio pour Mac

Tout comme Visual Studio, [Visual Studio pour Mac](/visualstudio/mac/) organise le code en *solutions* et en *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application, une bibliothèque de prise en charge, une application de test, etc. L’application Notes se compose d’une solution qui contient trois projets, comme le montre la capture d’écran suivante :

![Volet Solution Visual Studio pour Mac](deepdive-images/vsmac/solution.png)

Les projets sont :

- Remarques - Ce projet est le projet de bibliothèque .NET Standard qui contient l’ensemble du code partagé et de l’IU partagée.
- Notes.Android - Ce projet contient du code spécifique à Android et constitue le point d’entrée pour les applications Android.
- Notes.iOS - Ce projet contient du code spécifique à iOS et constitue le point d’entrée pour les applications iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomie d’une Xamarin.Forms application

La capture d’écran suivante montre le contenu du projet de bibliothèque .NET Standard Notes dans Visual Studio pour Mac :

![Contenu du projet de bibliothèque .NET Standard Phoneword](deepdive-images/vsmac/net-standard-project.png)

Le projet a un nœud **dépendances** qui contient les nœuds **NuGet** et **SDK** :

- **NuGet** &ndash; les Xamarin.Forms packages NuGet et SQLite-net-PCL qui ont été ajoutés au projet.
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

Une Xamarin.Forms application est structurée de la même façon qu’une application multiplateforme traditionnelle. En général, le code partagé est placé dans une bibliothèque .NET Standard et les applications spécifiques à la plateforme consomment le code partagé. Le diagramme suivant présente une vue d’ensemble de cette relation pour l’application Notes :

::: zone pivot="windows"

![Architecture de Notes](deepdive-images/vs/architecture.png)

::: zone-end
::: zone pivot="macos"

![Architecture de Notes](deepdive-images/vsmac/architecture.png)

::: zone-end

Pour optimiser la réutilisation du code de démarrage, les Xamarin.Forms applications ont une seule classe nommée `App` qui est chargée d’instancier la première page qui sera affichée par l’application sur chaque plateforme, comme illustré dans l’exemple de code suivant :

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

Ce code affecte `MainPage` à la propriété de la `App` classe une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instance dont le contenu est une `NotesPage` instance.

De plus, le fichier **AssemblyInfo.cs** contient un seul attribut d’application, qui est appliqué au niveau de l’assembly :

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

L' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attribut Active le compilateur XAML, afin que le XAML soit compilé directement en langage intermédiaire. Pour plus d’informations, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launching-the-application-on-each-platform"></a>Lancement de l’application sur chaque plateforme

### <a name="ios"></a>iOS

Pour lancer la Xamarin.Forms page initiale dans iOS, le projet notes. iOS définit la `AppDelegate` classe qui hérite de la `FormsApplicationDelegate` classe :

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

La `FinishedLaunching` substitution Initialise le Xamarin.Forms Framework en appelant la `Init` méthode. Cela entraîne le chargement de l’implémentation spécifique à iOS Xamarin.Forms dans l’application avant que le contrôleur d’affichage racine soit défini par l’appel à la `LoadApplication` méthode.

### <a name="android"></a>Android

Pour lancer la Xamarin.Forms page initiale dans Android, le projet notes. Android comprend du code qui crée un `Activity` avec l' `MainLauncher` attribut, avec l’activité qui hérite de la `FormsAppCompatActivity` classe :

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

La `OnCreate` substitution Initialise le Xamarin.Forms Framework en appelant la `Init` méthode. Cela entraîne le chargement de l’implémentation spécifique à Android Xamarin.Forms dans l’application avant le chargement de l' Xamarin.Forms application.

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Dans les applications plateforme Windows universelle (UWP), la `Init` méthode qui initialise l' Xamarin.Forms infrastructure est appelée à partir de la `App` classe :

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Cela entraîne le chargement de l’implémentation spécifique Xamarin.Forms à UWP dans l’application. La Xamarin.Forms page initiale est lancée par la `MainPage` classe :

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

L' Xamarin.Forms application est chargée avec la `LoadApplication` méthode.

> [!NOTE]
> Plateforme Windows universelle applications peuvent être générées avec Xamarin.Forms , mais uniquement à l’aide de Visual Studio sur Windows.

::: zone-end

## <a name="user-interface"></a>Interface utilisateur

Quatre groupes de contrôles principaux sont utilisés pour créer l’interface utilisateur d’une Xamarin.Forms application :

1. **Pages** : les Xamarin.Forms pages représentent des écrans d’application mobile multiplateforme. L’application notes utilise la [`ContentPage`](xref:Xamarin.Forms.ContentPage) classe pour afficher des écrans uniques. Pour plus d’informations sur les pages, consultez [ Xamarin.Forms pages](~/xamarin-forms/user-interface/controls/pages.md).
1. **Vues** : Xamarin.Forms les vues sont les contrôles affichés dans l’interface utilisateur, tels que les étiquettes, les boutons et les zones de saisie de texte. L’application notes terminée utilise les [`ListView`](xref:Xamarin.Forms.ListView) [`Editor`](xref:Xamarin.Forms.Editor) vues, et [`Button`](xref:Xamarin.Forms.Button) . Pour plus d’informations sur les affichages, consultez [ Xamarin.Forms vues](~/xamarin-forms/user-interface/controls/views.md).
1. **Dispositions** : Xamarin.Forms les dispositions sont des conteneurs utilisés pour composer des vues en structures logiques. L’application notes utilise la [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe pour réorganiser les vues dans une pile verticale, et la [`Grid`](xref:Xamarin.Forms.Grid) classe pour réorganiser les boutons horizontalement. Pour plus d’informations sur les dispositions, consultez [ Xamarin.Forms dispositions](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Cellules** : Xamarin.Forms les cellules sont des éléments spécialisés utilisés pour les éléments d’une liste et décrivent comment chaque élément d’une liste doit être dessiné. L’application notes utilise le [`TextCell`](xref:Xamarin.Forms.TextCell) pour afficher deux éléments pour chaque ligne de la liste. Pour plus d’informations sur les cellules, consultez [ Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md).

Lors de l’exécution, chaque contrôle est mappé à son équivalent natif, ce qui sera affiché.

### <a name="layout"></a>Mise en page

L’application notes utilise le [`StackLayout`](xref:Xamarin.Forms.StackLayout) pour simplifier le développement d’applications interplateformes en organisant automatiquement des vues sur l’écran, quelle que soit la taille de l’écran. Les éléments enfants sont placés les uns après les autres, horizontalement ou verticalement dans l’ordre dans lequel ils ont été ajoutés. La quantité d’espace que le `StackLayout` doit utiliser dépend de la façon dont les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriétés et sont définies, mais par défaut, le `StackLayout` essaie d’utiliser la totalité de l’écran.

Le code XAML suivant illustre un exemple d’utilisation d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) pour mettre en page le `NoteEntryPage` :

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

Par défaut, le [`StackLayout`](xref:Xamarin.Forms.StackLayout) suppose une orientation verticale. Toutefois, il peut être remplacé par une orientation horizontale en affectant [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) à la propriété le membre de l' [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) énumération.

> [!NOTE]
> Vous pouvez définir la taille des vues à l’aide des propriétés `HeightRequest` et `WidthRequest`.

Pour plus d’informations sur la [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe, consultez [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md).

### <a name="responding-to-user-interaction"></a>Réponse aux interactions de l’utilisateur

Un objet défini en XAML peut déclencher un événement qui est géré dans le fichier code-behind. L’exemple de code suivant montre la `OnSaveButtonClicked` méthode dans le code-behind pour la `NoteEntryPage` classe, qui est exécutée en réponse à l' [`Clicked`](xref:Xamarin.Forms.Button.Clicked) événement déclenché sur le bouton *Enregistrer* .

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

[`ListView`](xref:Xamarin.Forms.ListView)Est chargé d’afficher une collection d’éléments verticalement dans une liste. Chaque élément de `ListView` est contenu dans une seule cellule.

L’exemple de code suivant montre le [`ListView`](xref:Xamarin.Forms.ListView) à partir du `NotesPage` :

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

La disposition de chaque ligne du [`ListView`](xref:Xamarin.Forms.ListView) est définie dans l' [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) élément et utilise la liaison de données pour afficher toutes les notes récupérées par l’application. La [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété est définie sur la source de données, dans `NotesPage.xaml.cs` :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Ce code remplit la [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes stockées dans la base de données.

Lorsqu’une ligne est sélectionnée dans le [`ListView`](xref:Xamarin.Forms.ListView) , l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement se déclenche. Un gestionnaire d’événements, nommé `OnListViewItemSelected`, s’exécute quand l’événement se déclenche :

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

L' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement peut accéder à l’objet qui a été associé à la cellule par le biais de la [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriété.

Pour plus d’informations sur la [`ListView`](xref:Xamarin.Forms.ListView) classe, consultez [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigation

Xamarin.Formsfournit un certain nombre d’expériences de navigation entre les pages différentes, selon le [`Page`](xref:Xamarin.Forms.Page) type utilisé. Pour les [`ContentPage`](xref:Xamarin.Forms.ContentPage) instances, la navigation peut être hiérarchique ou modale. Pour plus d’informations sur la navigation modale, consultez [ Xamarin.Forms pages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Les [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) classes, et [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) fournissent d’autres expériences de navigation. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md).

Dans la navigation hiérarchique, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe est utilisée pour naviguer dans une pile d' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets, vers l’avant et vers l’arrière, selon les besoins. La classe implémente la navigation sous la forme d’une pile d’objets LIFO (dernier entré, premier sorti) [`Page`](xref:Xamarin.Forms.Page) . Pour passer d’une page à une autre, une application envoie une nouvelle page dans la pile de navigation, où elle devient la page active. Pour revenir à la page précédente, l’application dépile la page actuelle, et la nouvelle page tout en haut devient la page active.

La classe `NavigationPage` ajoute également une barre de navigation en haut de la page qui affiche un titre et un bouton **Précédent** approprié à la plateforme qui permet de revenir à la page précédente.

La première page ajoutée à une pile de navigation est appelée la page *racine* de l’application. L’exemple de code suivant illustre le déroulement de l’opération dans l’application Notes :

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Toutes les [`ContentPage`](xref:Xamarin.Forms.ContentPage) instances ont une [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété qui expose des méthodes pour modifier la pile de pages. Ces méthodes ne doivent être appelées que si l’application comprend un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Pour naviguer jusqu’au `NoteEntryPage` , il est nécessaire d’appeler [ `PushAsync` ] (XREF : Xamarin.Forms . NavigationPage. PushAsync ( Xamarin.Forms . Page)), comme illustré dans l’exemple de code ci-dessous :

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

Ainsi, le nouvel ​​objet `NoteEntryPage` est envoyé (push) vers la pile de navigation, où il devient la page active.

La page active peut être retirée de la pile de navigation en appuyant sur le bouton *Précédent* sur l’appareil, qu’il s’agisse d’un bouton physique sur l’appareil ou d’un bouton à l’écran. Pour revenir par programmation à la page d’origine, l' `NoteEntryPage` objet doit appeler la [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) méthode, comme illustré dans l’exemple de code ci-dessous :

```csharp
await Navigation.PopAsync();
```

Pour plus d’informations sur la navigation hiérarchique, consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Liaison de données

La liaison de données permet de simplifier la façon dont une Xamarin.Forms application s’affiche et interagit avec ses données. Elle établit une connexion entre l’interface utilisateur et l’application sous-jacente. La [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe contient une grande partie de l’infrastructure pour prendre en charge la liaison de données.

La liaison de données connecte deux objets, appelés la *source* et la *cible*. L’objet *source* fournit les données. L’objet *cible* consomme (et affiche souvent) les données de l’objet source. Par exemple, un [`Editor`](xref:Xamarin.Forms.Editor) (objet*cible* ) lie généralement sa [`Text`](xref:Xamarin.Forms.InputView.Text) propriété à une propriété publique `string` dans un objet *source* . Le diagramme suivant illustre la relation de liaison :

![Liaison de données](deepdive-images/data-binding.png)

Le principal avantage de la liaison de données est que vous n’avez plus à vous soucier de la synchronisation des données entre les vues et la source de données. Les modifications apportées à l’objet *source* sont automatiquement envoyées à l’objet *cible* dans les coulisses par le framework de liaison, et les modifications apportées à l’objet cible peuvent éventuellement être renvoyées à l’objet *source*.

L’établissement de la liaison de données est un processus en deux étapes :

- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété de l’objet *cible* doit être définie sur la *source*.
- Une liaison doit être établie entre la *cible* et la *source*. En XAML, cela est effectué à l’aide de l' [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) extension de balisage.

Dans l’application notes, la cible de liaison est le [`Editor`](xref:Xamarin.Forms.Editor) qui affiche une note, tandis que l' `Note` instance définie en tant que [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` est la source de liaison.

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

Dans la `OnNoteAddedClicked` méthode, qui est exécutée lorsqu’une nouvelle note est ajoutée à l’application, [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) la `NoteEntryPage` valeur de est définie sur une nouvelle `Note` instance. Dans la `OnListViewItemSelected` méthode, qui est exécutée quand une note existante est sélectionnée dans le [`ListView`](xref:Xamarin.Forms.ListView) , le `BindingContext` du `NoteEntryPage` est défini sur l' `Note` instance sélectionnée, accessible via la [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriété.

> [!IMPORTANT]
> La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété de chaque objet *cible* peut être définie individuellement, ce qui n’est pas nécessaire. `BindingContext` est une propriété spéciale qui est héritée par tous ses enfants. Par conséquent, lorsque le `BindingContext` sur [`ContentPage`](xref:Xamarin.Forms.ContentPage) est défini sur une `Note` instance de, tous les enfants de l' `ContentPage` objet ont le même `BindingContext` et peuvent effectuer une liaison aux propriétés publiques de l' `Note` objet.

Le [`Editor`](xref:Xamarin.Forms.Editor) dans `NoteEntryPage` crée alors une liaison avec la `Text` propriété de l' `Note` objet :

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Une liaison entre la [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) propriété et la `Text` propriété de l’objet *source* est établie. Les changements apportés dans `Editor` sont automatiquement propagés à l’objet `Note`. De même, si des modifications sont apportées à la `Note.Text` propriété, le Xamarin.Forms moteur de liaison mettra également à jour le contenu du `Editor` . Il s’agit là d’une *liaison bidirectionnelle*.

Pour plus d’informations sur la liaison de données, consultez [ Xamarin.Forms liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Styles

Xamarin.Formsles applications contiennent souvent plusieurs éléments visuels qui ont une apparence identique. La définition de l’apparence de chaque élément visuel peut être répétitive et sujette aux erreurs. À la place, vous pouvez créer des styles qui définissent l’apparence, puis les appliquer aux éléments visuels nécessaires.

La [`Style`](xref:Xamarin.Forms.Style) classe regroupe une collection de valeurs de propriété dans un objet qui peut ensuite être appliqué à plusieurs instances d’éléments visuels. Les styles sont stockés dans un, au niveau de l’application, au niveau de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) la page ou au niveau de la vue. Le fait de choisir où définir `Style` impacte l’emplacement où il peut être utilisé :

- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de l’application peuvent être appliquées dans l’ensemble de l’application.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de la page peuvent être appliquées à la page et à ses enfants.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de la vue peuvent être appliquées à la vue et à ses enfants.

> [!IMPORTANT]
> Tous les styles utilisés dans l’application sont stockés dans le dictionnaire de ressources de l’application pour éviter la duplication. Toutefois, le code XAML qui est spécifique à une page ne doit pas être inclus dans le dictionnaire de ressources de l’application, car les ressources sont alors analysées au démarrage de l’application et non lorsqu’une page le demande.

Chaque [`Style`](xref:Xamarin.Forms.Style) instance contient une collection d’un ou de plusieurs [`Setter`](xref:Xamarin.Forms.Setter) objets, chacun `Setter` ayant un [`Property`](xref:Xamarin.Forms.Setter.Property) et un [`Value`](xref:Xamarin.Forms.Setter.Value) . `Property` est le nom de la propriété pouvant être liée de l’élément auquel le style est appliqué, et `Value` est la valeur qui est appliquée à la propriété. L’exemple de code suivant illustre un style de `NoteEntryPage` :

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

Ce style est appliqué à toutes les [`Editor`](xref:Xamarin.Forms.Editor) instances de la page.

Lors de la création d’un [`Style`](xref:Xamarin.Forms.Style) , la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propriété est toujours obligatoire.

> [!NOTE]
> Le style d’une Xamarin.Forms application est généralement effectué à l’aide de styles XAML. Toutefois, Xamarin.Forms prend également en charge le style des éléments visuels à l’aide d’feuilles de style en cascade (CSS). Pour plus d’informations, consultez [stylisation d' Xamarin.Forms applications à l’aide de feuilles de style en cascade (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Pour plus d’informations sur les styles XAML, consultez [stylisation d' Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

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

Cela [`Style`](xref:Xamarin.Forms.Style) permet de définir des [`Color`](xref:Xamarin.Forms.Color) valeurs différentes pour les [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) Propriétés et de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , en fonction de la plateforme utilisée.

Pour plus d’informations sur les extensions de balisage XAML, consultez [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Pour plus d’informations sur l’extension de balisage `OnPlatform`, consultez [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Tests et déploiement

Visual Studio pour Mac et Visual Studio proposent tous deux de nombreuses options pour tester et déployer les applications. Le débogage d’applications est une partie courante du cycle de vie de développement des applications et permet de diagnostiquer les problèmes de code. Pour plus d’informations, consultez [Définir un point d’arrêt](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Exécuter du code pas à pas](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) et [Informations de sortie dans la fenêtre de journal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Les simulateurs constituent un endroit judicieux pour commencer à déployer et tester une application, et comportent des fonctionnalités utiles pour le test des applications. Toutefois, les utilisateurs ne consommant pas l’application finale dans un simulateur, les applications doivent être testées tôt et souvent sur des appareils réels. Pour plus d’informations sur le provisionnement des appareils iOS, consultez [Provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md). Pour plus d’informations sur le provisionnement des appareils Android, consultez [Configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Étapes suivantes

Cette présentation approfondie a examiné les notions de base du développement d’applications à l’aide de Xamarin.Forms . Les étapes suggérées ci-dessous incluent la découverte des fonctionnalités suivantes :

- Quatre groupes de contrôles principaux sont utilisés pour créer l’interface utilisateur d’une Xamarin.Forms application. Pour plus d’informations, consultez [Informations de référence sur les contrôles](~/xamarin-forms/user-interface/controls/index.md).
- La liaison de données est une technique qui permet de lier les propriétés de deux objets afin que les changements d’une propriété soient automatiquement répercutés sur l’autre propriété. Pour plus d’informations, consultez [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Formsfournit un certain nombre d’expériences de navigation entre les pages différentes, en fonction du type de page utilisé. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Les styles contribuent à réduire le balisage répétitif et permettent de modifier plus facilement l’apparence des applications. Pour plus d’informations, consultez application d’un [style aux Xamarin.Forms applications](~/xamarin-forms/user-interface/styles/index.md).
- Les extensions de balisage XAML étendent la puissance et la flexibilité du langage XAML en permettant de définir des attributs d’éléments à partir de sources autres que des chaînes de texte littéral. Pour plus d’informations, consultez [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Les modèles de données permettent de définir la présentation des données sur les affichages pris en charge. Pour plus d’informations, consultez [Modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Chaque page, disposition et affichage est restitué différemment sur chaque plateforme avec une classe `Renderer` qui crée ensuite un contrôle natif, le dispose à l’écran et ajoute le comportement spécifié dans le code partagé. Les développeurs peuvent implémenter leurs propres classes `Renderer` pour personnaliser l’apparence et/ou le comportement d’un contrôle. Pour plus d’informations, consultez [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Les effets permettent également de personnaliser les contrôles natifs sur chaque plateforme. Les effets sont créés dans des projets spécifiques à la plateforme en sous-classant la [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) classe et sont consommés en les joignant à un Xamarin.Forms contrôle approprié. Pour plus d’informations, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).
- Le code partagé peut accéder aux fonctionnalités natives par le biais de la [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe. Pour plus d’informations, consultez [Accès aux fonctionnalités natives avec DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Vous pouvez également [_créer des Mobile Apps Xamarin.Forms avec _](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md), un livre de Charles Petzold, pour en savoir plus sur Xamarin.Forms . Le livre est disponible au format PDF ou dans une variété de formats électroniques.

## <a name="related-links"></a>Liens connexes

- [eXtensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.yml)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Référence des contrôles](~/xamarin-forms/user-interface/controls/index.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsExtraits](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Exemples de Prise en main](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Xamarin.FormsRéférence d’API](xref:Xamarin.Forms)
- [Autoformation gratuite (vidéo)](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
