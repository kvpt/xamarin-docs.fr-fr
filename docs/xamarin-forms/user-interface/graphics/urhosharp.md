---
title: Utilisation de UrhoSharp dansXamarin.Forms
description: Cet article explique comment UrhoSharp peut être utilisé pour ajouter des graphiques 3D à une Xamarin.Forms application en vue d’une visualisation avancée.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c0eed1a451d62025562ac5fff4f12be96f0bf53
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137655"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Utilisation de UrhoSharp dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>Qu’est-ce que UrhoSharp ?

[UrhoSharp](~/graphics-games/urhosharp/index.md) est un moteur 3D puissant pour les développeurs Xamarin et .net. La [Présentation](~/graphics-games/urhosharp/introduction.md) explique plus en détail la bibliothèque UrhoSharp et [ces notes](~/graphics-games/urhosharp/using.md) décrivent comment programmer des scènes et des actions.

UrhoSharp peut être utilisé pour restituer des graphiques dans des Xamarin.Forms applications.
Cet [exemple](https://github.com/xamarin/urho-samples/tree/master/FormsSample) illustre l’utilisation de UrhoSharp pour construire un graphique 3D interactif :

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D Interactive Chart on iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D Interactive Chart on Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Ajout des packages NuGet UrhoSharp

Avant d’utiliser UrhoSharp, les développeurs doivent ajouter le package NuGet UrhoSharp à leur solution. Ce guide part du principe qu’il s’agit d’un Xamarin.Forms projet avec un projet de bibliothèque iOS, Android et .NET standard. Tout le code est écrit dans le projet de bibliothèque .NET Standard ; Toutefois, le NuGet UrhoSharp doit également être ajouté aux projets iOS et Android.

Le package NuGet UrhoSharp. Forms contient tous les objets nécessaires pour créer des objets UrhoSharp. Le package NuGet UrhoSharp. Forms comprend la `UrhoSurface` classe, qui est utilisée pour héberger des UrhoSharp dans Xamarin.Forms .
Pour commencer, cliquez avec le bouton droit sur le dossier **packages** dans le projet de bibliothèque .NET standard et sélectionnez **Ajouter des packages...**. Entrez le terme de recherche **UrhoSharp. Forms**, sélectionnez **UrhoSharp pour Xamarin.Forms **, puis cliquez sur Ajouter un **package**.

[![](urhosharp-images/add-package-sml.png "Add Packages Dialog")](urhosharp-images/add-package.png#lightbox "Add Packages Dialog")

Le package NuGet UrhoSharp. Forms sera ajouté au projet :

![](urhosharp-images/packages.png "Packages Folder")

Répétez les étapes ci-dessus pour les projets spécifiques à une plateforme (par exemple, iOS et Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Procédure pas à pas : ajout de UrhoSharp à une Xamarin.Forms application

Ces étapes décrivent le code dans l' Xamarin.Forms exemple UrhoSharp :

1. [Créé une page de formulaires Xamarin](#1)
2. [Ajouter UrhoSurface](#2)
3. [Créer une application Urho](#3)
4. [Ajoutez la classe Charts à UrhoSurface](#4)
5. [Interaction avec UrhoSharp](#5)

Notez que l’exemple utilise des fonctionnalités C# 6 et peut ne pas se compiler sur les versions antérieures de Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. créer une page de formulaires Xamarin

Le code ci-dessous montre une Xamarin.Forms page `UrhoPage` avant l’ajout d’un code lié à Urho :

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2. Ajoutez le UrhoSurface

UrhoSharp peut être hébergé dans un `ContentPage` comme d’autres Xamarin.Forms contrôles.
L’extrait de code ci-dessous montre un `UrhoSurface` ajouté à la Xamarin.Forms page :

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3. créer une application Urho

Reportez-vous à la `Charts` classe pour l’implémentation du graphique 3D Urho utilisé dans cet exemple. La structure du code de base est illustrée ci-dessous. Notez que la classe implémente, `Urho.Application` qui est différente de la `Xamarin.Forms.Application` classe implémentée dans **app.cs**.

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

La [documentation UrhoSharp](~/graphics-games/urhosharp/index.md) contient des informations supplémentaires sur la façon de créer des scènes et des actions 3D.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Ajoutez la classe Charts à UrhoSurface

Utilisez la `UrhoSurface.Show<T>` méthode générique pour ajouter l’application Urho à la Xamarin.Forms page. L’extrait de code ci-dessous montre le code supplémentaire requis pour créer la `Charts` classe :

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

Remarque : la `Show<T>` méthode est asynchrone et doit être appelée avec le `await` mot clé.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. interaction avec UrhoSharp

L’exemple permet de sélectionner et de modifier les barres du graphique. La `Charts` classe expose les `Bars` et `SelectedBar` pour activer cette interaction.

Chaque « bar » a un gestionnaire d’événements de sélection ajouté après le rendu de la `Charts` classe, en effectuant une itération sur la collection exposée `Bars` :

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

Le gestionnaire d’événements utilise la valeur du Xamarin.Forms `Slider` contrôle pour ajuster la hauteur de la barre donnée :

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

Enfin, associez les deux `Slider` contrôles afin que lorsque leur valeur change le canevas UrhoSharp soit affecté. Le premier curseur fait pivoter l’image de graphique 3D et le deuxième curseur ajuste la hauteur de la barre sélectionnée.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Les animations en [haut de la page](#what-is-urhosharp) affichent l’exemple en cours d’exécution.

## <a name="summary"></a>Résumé

Cette page montre comment UrhoSharp peut être utilisé pour ajouter une visualisation de données 3D à Xamarin.Forms . Pour plus [UrhoSharp documentation](~/graphics-games/urhosharp/index.md) d’informations sur la création de scènes Urho qui peuvent être incluses dans des Xamarin.Forms applications à l’aide de la méthode illustrée ci-dessus, consultez la documentation UrhoSharp.

## <a name="related-links"></a>Liens connexes

- [Exemple de graphiques (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
