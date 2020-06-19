---
title: Chargement du code XAML au moment de l’exécution dansXamarin.Forms
description: XAML peut être chargé et analysé au moment de l’exécution avec les méthodes d’extension LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d750aa84a48ad4c8015a619d819134cefc63c3d9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139371"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Chargement du code XAML au moment de l’exécution dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

L' [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) espace de noms comprend deux [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthodes d’extension qui peuvent être utilisées pour charger et analyser le XAML au moment de l’exécution.

## <a name="background"></a>Arrière-plan

Quand une Xamarin.Forms classe XAML est construite, la [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthode est appelée indirectement. Cela est dû au fait que le fichier code-behind pour une classe XAML appelle la `InitializeComponent` méthode à partir de son constructeur :

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Lorsque Visual Studio génère un projet contenant un fichier XAML, il analyse le fichier XAML pour générer un fichier de code C# (par exemple, **MainPage.Xaml.g.cs**) qui contient la définition de la `InitializeComponent` méthode :

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

La `InitializeComponent` méthode appelle la [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthode pour extraire le fichier XAML (ou son fichier binaire compilé) de la bibliothèque de .NET standard. Après l’extraction, elle initialise tous les objets définis dans le fichier XAML, les connecte tous ensemble dans les relations parent-enfant, attache les gestionnaires d’événements définis dans le code aux événements définis dans le fichier XAML et définit l’arborescence résultante d’objets en tant que contenu de la page.

## <a name="loading-xaml-at-runtime"></a>Chargement du code XAML au moment de l’exécution

Les [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthodes sont `public` et, par conséquent, peuvent être appelées à partir d' Xamarin.Forms applications pour charger et analyser le XAML au moment de l’exécution. Cela permet des scénarios tels qu’une application qui télécharge du code XAML à partir d’un service Web, crée la vue requise à partir du XAML et l’affiche dans l’application.

> [!WARNING]
> Le chargement du code XAML au moment de l’exécution a un coût de performance significatif et doit généralement être évité.

L’exemple de code suivant montre une utilisation simple :

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

Dans cet exemple, une [`Button`](xref:Xamarin.Forms.Button) instance est créée, avec sa [`Text`](xref:Xamarin.Forms.Button.Text) valeur de propriété définie à partir du XAML défini dans le `string` . `Button`Est ensuite ajouté à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui a été défini dans le XAML pour la page.

> [!NOTE]
> Les [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthodes d’extension permettent de spécifier un argument de type générique. Toutefois, il est rarement nécessaire de spécifier l’argument de type, car il sera déduit du type de l’instance sur laquelle il est utilisé.

La [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthode peut être utilisée pour augmenter n’importe quel XAML, avec l’exemple suivant qui montre un, [`ContentPage`](xref:Xamarin.Forms.ContentPage) puis y accéder :

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Accéder aux éléments

Le chargement du code XAML au moment de l’exécution avec la [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthode n’autorise pas l’accès fortement typé aux éléments XAML qui ont des noms d’objets d’exécution spécifiés (à l’aide de `x:Name` ). Toutefois, ces éléments XAML peuvent être récupérés à l’aide de la [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) méthode, puis accessibles en fonction des besoins :

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

Dans cet exemple, le code XAML pour un [`ContentPage`](xref:Xamarin.Forms.ContentPage) est gonflé. Ce code XAML comprend un [`Label`](xref:Xamarin.Forms.Label) nommé `monkeyName` , qui est récupéré à l’aide de la [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) méthode, avant que sa [`Text`](xref:Xamarin.Forms.Label.Text) propriété ne soit définie.

## <a name="related-links"></a>Liens connexes

- [LoadRuntimeXAML (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
