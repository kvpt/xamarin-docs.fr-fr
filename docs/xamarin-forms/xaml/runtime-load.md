---
title: Chargement du code XAML au moment de l’exécution dans Xamarin. Forms
description: XAML peut être chargé et analysé au moment de l’exécution avec les méthodes d’extension LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: 71f510cd37d4bed2a5a6077ed63f748ce9894725
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "78293063"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Chargement du code XAML au moment de l’exécution dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

L’espace de noms [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) comprend deux méthodes d’extension [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) qui peuvent être utilisées pour charger et analyser le XAML au moment de l’exécution.

## <a name="background"></a>Arrière-plan

Quand une classe XAML Xamarin. Forms est construite, la méthode [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) est appelée indirectement. Cela est dû au fait que le fichier code-behind pour une classe XAML appelle la méthode `InitializeComponent` à partir de son constructeur :

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Lorsque Visual Studio génère un projet contenant un fichier XAML, il analyse le fichier XAML pour générer un C# fichier de code (par exemple, **MainPage.Xaml.g.cs**) qui contient la définition de la méthode `InitializeComponent` :

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

La méthode `InitializeComponent` appelle la méthode [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) pour extraire le fichier XAML (ou son fichier binaire compilé) de la bibliothèque de .NET standard. Après l’extraction, elle initialise tous les objets définis dans le fichier XAML, les connecte tous ensemble dans les relations parent-enfant, attache les gestionnaires d’événements définis dans le code aux événements définis dans le fichier XAML et définit l’arborescence d’objets résultante comme contenu du pagination.

## <a name="loading-xaml-at-runtime"></a>Chargement du code XAML au moment de l’exécution

Les méthodes de [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) sont `public`et peuvent donc être appelées à partir d’applications Xamarin. Forms pour charger et analyser le code XAML au moment de l’exécution. Cela permet des scénarios tels qu’une application qui télécharge du code XAML à partir d’un service Web, crée la vue requise à partir du XAML et l’affiche dans l’application.

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

Dans cet exemple, une instance de [`Button`](xref:Xamarin.Forms.Button) est créée, avec sa valeur de propriété [`Text`](xref:Xamarin.Forms.Button.Text) définie à partir du XAML défini dans le `string`. Le `Button` est ensuite ajouté à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui a été défini dans le XAML pour la page.

> [!NOTE]
> Les méthodes d’extension [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) permettent de spécifier un argument de type générique. Toutefois, il est rarement nécessaire de spécifier l’argument de type, car il sera déduit du type de l’instance sur laquelle il est utilisé.

La méthode [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) peut être utilisée pour augmenter n’importe quel XAML, avec l’exemple suivant, qui montre comment gonfler une [`ContentPage`](xref:Xamarin.Forms.ContentPage) , puis naviguer vers celle-ci :

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Accéder aux éléments

Le chargement du code XAML au moment de l’exécution avec la méthode [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) n’autorise pas l’accès fortement typé aux éléments XAML qui ont des noms d’objets d’exécution spécifiés (à l’aide de `x:Name`). Toutefois, ces éléments XAML peuvent être récupérés à l’aide de la méthode [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) , puis accessibles en fonction des besoins :

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

Dans cet exemple, le code XAML d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) est gonflé. Ce code XAML comprend un [`Label`](xref:Xamarin.Forms.Label) nommé `monkeyName`, qui est récupéré à l’aide de la méthode [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) , avant que sa propriété [`Text`](xref:Xamarin.Forms.Label.Text) soit définie.

## <a name="related-links"></a>Liens connexes

- [LoadRuntimeXAML (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
