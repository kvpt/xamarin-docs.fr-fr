---
title: Chargement du code XAML au moment de l’exécution dans Xamarin. Forms
description: XAML peut être chargé et analysé au moment de l’exécution avec les méthodes d’extension LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: e253d2ba949a94637d7773fdc50b479679fd3f41
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657254"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Chargement du code XAML au moment de l’exécution dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

L' [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) espace de noms [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) comprend deux méthodes d’extension qui peuvent être utilisées pour charger et analyser le XAML au moment de l’exécution.

## <a name="background"></a>Présentation

Quand une classe XAML Xamarin. Forms est construite, [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) la méthode est appelée indirectement. Cela est dû au fait que le fichier code-behind pour une classe `InitializeComponent` XAML appelle la méthode à partir de son constructeur:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Lorsque Visual Studio génère un projet contenant un fichier XAML, il analyse le fichier XAML pour générer un C# fichier de code (par exemple, **MainPage.Xaml.g.cs**) qui contient la définition de la `InitializeComponent` méthode:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

La `InitializeComponent` méthode appelle la [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthode pour extraire le fichier XAML (ou son fichier binaire compilé) de la bibliothèque de .NET standard. Après l’extraction, elle initialise tous les objets définis dans le fichier XAML, les connecte tous ensemble dans les relations parent-enfant, attache les gestionnaires d’événements définis dans le code aux événements définis dans le fichier XAML et définit l’arborescence d’objets résultante comme contenu du pagination.

## <a name="loading-xaml-at-runtime"></a>Chargement du code XAML au moment de l’exécution

Les [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthodes sont `public`, et par conséquent, peuvent être appelées à partir d’applications Xamarin. Forms pour charger et analyser le code XAML au moment de l’exécution. Cela permet des scénarios tels qu’une application qui télécharge du code XAML à partir d’un service Web, crée la vue requise à partir du XAML et l’affiche dans l’application.

> [!WARNING]
> Le chargement du code XAML au moment de l’exécution a un coût de performance significatif et doit généralement être évité.

L’exemple de code suivant montre une utilisation simple:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

Dans cet exemple, une [`Button`](xref:Xamarin.Forms.Button) instance est créée, avec [`Text`](xref:Xamarin.Forms.Button.Text) sa valeur de propriété définie à partir du XAML défini dans le `string`. Est ensuite ajouté à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui a été défini dans le XAML pour la page. `Button`

> [!NOTE]
> Les [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthodes d’extension permettent de spécifier un argument de type générique. Toutefois, il est rarement nécessaire de spécifier l’argument de type, car il sera déduit du type de l’instance sur laquelle il est utilisé.

La [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) méthode peut être utilisée pour augmenter n’importe quel XAML, avec l’exemple suivant qui montre [`ContentPage`](xref:Xamarin.Forms.ContentPage) un, puis y accéder:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Accéder aux éléments

Le chargement du code XAML au [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) moment de l’exécution avec la méthode n’autorise pas l’accès fortement typé aux éléments XAML qui ont des `x:Name`noms d’objets d’exécution spécifiés (à l’aide de). Toutefois, ces éléments XAML peuvent être récupérés à [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) l’aide de la méthode, puis accessibles en fonction des besoins:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

Dans cet exemple, le code XAML pour [`ContentPage`](xref:Xamarin.Forms.ContentPage) un est gonflé. Ce code XAML comprend [`Label`](xref:Xamarin.Forms.Label) un `monkeyName`nommé, qui est récupéré à [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) l’aide de la méthode [`Text`](xref:Xamarin.Forms.Label.Text) , avant que sa propriété ne soit définie.

## <a name="related-links"></a>Liens connexes

- [LoadRuntimeXAML (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
