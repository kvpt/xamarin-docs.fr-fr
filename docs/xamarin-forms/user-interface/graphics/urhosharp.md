---
title: Utilisation de UrhoSharp dans Xamarin.Forms
description: Cet article explique comment les UrhoSharp peut être utilisé pour ajouter des graphiques 3D à une application Xamarin.Forms pour la visualisation avancée.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: 60c647a9a09ebffa5a9d50c799c09cf0dbf2e4ac
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728007"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Utilisation de UrhoSharp dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>Qu’est UrhoSharp ?

[UrhoSharp](~/graphics-games/urhosharp/index.md) est un moteur 3D puissant pour les développeurs Xamarin et .net. La [Présentation](~/graphics-games/urhosharp/introduction.md) explique plus en détail la bibliothèque UrhoSharp et [ces notes](~/graphics-games/urhosharp/using.md) décrivent comment programmer des scènes et des actions.

UrhoSharp peut être utilisé pour restituer des graphiques dans les applications Xamarin.Forms.
Cet [exemple](https://github.com/xamarin/urho-samples/tree/master/FormsSample) illustre l’utilisation de UrhoSharp pour construire un graphique 3D interactif :

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D Interactive Chart on iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D Interactive Chart on Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Ajout des packages NuGet UrhoSharp

Avant d’utiliser UrhoSharp, les développeurs doivent ajouter le package NuGet UrhoSharp à leur solution. Ce guide suppose un projet Xamarin.Forms avec un iOS, Android et .NET Standard projet de bibliothèque. Tout le code est écrit dans le projet de bibliothèque .NET Standard ; Toutefois, le NuGet UrhoSharp doit également être ajouté aux projets iOS et Android.

Le package NuGet UrhoSharp. Forms contient tous les objets nécessaires pour créer des objets UrhoSharp. Le package NuGet UrhoSharp. Forms comprend la classe `UrhoSurface`, qui est utilisée pour héberger des UrhoSharp dans Xamarin. Forms.
Pour commencer, cliquez avec le bouton droit sur le dossier **packages** dans le projet de bibliothèque .NET standard et sélectionnez **Ajouter des packages...** . Entrez le terme de recherche **UrhoSharp. Forms**, sélectionnez **UrhoSharp pour Xamarin. Forms**, puis cliquez sur **Ajouter un package**.

[![](urhosharp-images/add-package-sml.png "Add Packages Dialog")](urhosharp-images/add-package.png#lightbox "Add Packages Dialog")

Le package NuGet de UrhoSharp.Forms sera ajouté au projet :

![](urhosharp-images/packages.png "Packages Folder")

Répétez les étapes ci-dessus pour les projets spécifiques à la plateforme (par exemple, iOS et Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Procédure pas à pas : Ajout de UrhoSharp à une application Xamarin.Forms

Ces étapes décrivent le code dans l’exemple de Xamarin.Forms UrhoSharp :

1. [Créé une page de formulaires Xamarin](#1)
2. [Ajouter UrhoSurface](#2)
3. [Créer une application Urho](#3)
4. [Ajoutez la classe Charts à UrhoSurface](#4)
5. [Interaction avec UrhoSharp](#5)

Notez que l’exemple utilise C# 6 fonctionnalités et ne peut pas compiler sur les versions antérieures de Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. créer une page de formulaires Xamarin

Le code ci-dessous montre une page Xamarin. Forms `UrhoPage` avant l’ajout d’un code lié à Urho :

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

UrhoSharp peut être hébergé dans un `ContentPage` comme d’autres contrôles Xamarin. Forms.
L’extrait de code ci-dessous montre une `UrhoSurface` ajoutée à la page Xamarin. Forms :

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

Reportez-vous à la classe `Charts` pour l’implémentation du graphique 3D Urho utilisé dans cet exemple. La structure du code de base est illustrée ci-dessous. Notez que la classe implémente `Urho.Application` qui est différente de la classe `Xamarin.Forms.Application` qui est implémentée dans **app.cs**.

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

Utilisez la méthode générique `UrhoSurface.Show<T>` pour ajouter l’application Urho à la page Xamarin. Forms. L’extrait de code ci-dessous montre le code supplémentaire requis pour créer la classe `Charts` :

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

Remarque : la méthode `Show<T>` est asynchrone et doit être appelée avec le mot clé `await`.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. interaction avec UrhoSharp

L’exemple permet à barres du graphique sélectionné et modifiés. La classe `Charts` expose les `Bars` et `SelectedBar` pour activer cette interaction.

Chaque « bar » a un gestionnaire d’événements de sélection ajouté après le rendu de la classe `Charts`, en effectuant une itération sur la collection de `Bars` exposée :

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

Le gestionnaire d’événements utilise la valeur du contrôle Xamarin. Forms `Slider` pour ajuster la hauteur de la barre donnée :

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

Enfin, associez les deux contrôles `Slider` afin que, lorsque leur valeur change, le canevas UrhoSharp soit affecté. Le premier curseur fait pivoter l’image de graphique 3D, et le deuxième curseur ajuste la hauteur de la barre sélectionnée.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Les animations en [haut de la page](#what-is-urhosharp) affichent l’exemple en cours d’exécution.

## <a name="summary"></a>Résumé

Cette page illustre l’utilisation des UrhoSharp pour ajouter la visualisation des données 3D à Xamarin.Forms. Pour plus d’informations sur la création de scènes Urho qui peuvent être incluses dans des applications Xamarin. Forms à l’aide de la méthode décrite ci-dessus, consultez la [documentation UrhoSharp](~/graphics-games/urhosharp/index.md) .

## <a name="related-links"></a>Liens connexes

- [Exemple de graphiquesC# (6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
