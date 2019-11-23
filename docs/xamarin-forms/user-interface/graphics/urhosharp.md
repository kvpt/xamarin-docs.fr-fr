---
title: Utilisation de UrhoSharp dans Xamarin.Forms
description: Cet article explique comment les UrhoSharp peut être utilisé pour ajouter des graphiques 3D à une application Xamarin.Forms pour la visualisation avancée.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: 7045bd4d3343d0c11c6cd52fa02cdc005175b8a7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772934"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Utilisation de UrhoSharp dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>Qu’est UrhoSharp ?

[UrhoSharp](~/graphics-games/urhosharp/index.md) est un puissant moteur 3D pour les développeurs Xamarin et .NET. Le [introduction](~/graphics-games/urhosharp/introduction.md) explique en détail la bibliothèque UrhoSharp, et [ces notes](~/graphics-games/urhosharp/using.md) expliquent comment programmer des scènes et actions.

UrhoSharp peut être utilisé pour restituer des graphiques dans les applications Xamarin.Forms.
Cela [exemple](https://github.com/xamarin/urho-samples/tree/master/FormsSample) montre comment UrhoSharp peut être utilisé pour construire un graphique 3D interactif :

![](urhosharp-images/ios-animation.gif "Graphique interactif en 3D de UrhoSharp sur iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp un graphique interactif 3D sur Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Ajouter les packages Nuget de UrhoSharp

Avant d’utiliser UrhoSharp, les développeurs doivent ajouter le package Nuget de UrhoSharp à leur solution. Ce guide suppose un projet Xamarin.Forms avec un iOS, Android et .NET Standard projet de bibliothèque. Tout le code sera écrit dans le projet de bibliothèque .NET Standard. mais le package UrhoSharp Nuget doivent être ajouté pour les projets iOS et Android trop.

Le package Nuget de UrhoSharp.Forms contient tous les objets nécessaires pour créer des objets de UrhoSharp. Le package nuget UrhoSharp.Forms inclut la `UrhoSurface` classe, qui est utilisé pour héberger UrhoSharp dans Xamarin.Forms.
Pour commencer, cliquez avec le bouton droit sur le dossier **packages** dans le projet de bibliothèque .NET standard et sélectionnez **Ajouter des packages...** . Entrez le terme de recherche **UrhoSharp. Forms**, sélectionnez **UrhoSharp pour Xamarin. Forms**, puis cliquez sur **Ajouter un package**.

[![](urhosharp-images/add-package-sml.png "Ajouter la boîte de dialogue Packages")](urhosharp-images/add-package.png#lightbox "Packages la boîte de dialogue Ajouter")

Le package NuGet de UrhoSharp.Forms sera ajouté au projet :

![](urhosharp-images/packages.png "Dossier packages")

Répétez les étapes ci-dessus pour les projets spécifiques à la plateforme (par exemple, iOS et Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Procédure pas à pas : Ajout de UrhoSharp à une application Xamarin.Forms

Ces étapes décrivent le code dans l’exemple de Xamarin.Forms UrhoSharp :

1. [Créer une Page de formulaires Xamarin](#1)
2. [Ajouter le UrhoSurface](#2)
3. [Créer une Application Urho](#3)
4. [Ajoutez la classe de graphiques à l’UrhoSurface](#4)
5. [Interaction avec UrhoSharp](#5)

Notez que l’exemple utilise C# 6 fonctionnalités et ne peut pas compiler sur les versions antérieures de Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. créer une page de formulaires Xamarin

Le code suivant montre une page de Xamarin.Forms `UrhoPage` avant tout code lié à Urho a été ajouté :

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

UrhoSharp peut être hébergé dans un `ContentPage` comme d’autres contrôles Xamarin.Forms.
L’extrait de code ci-dessous montre un `UrhoSurface` ajouté à la page Xamarin.Forms :

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

Reportez-vous à la `Charts` classe pour l’implémentation de graphismes 3D Urho utilisés dans cet exemple. La structure du code de base est illustré ci-dessous - Notez que la classe implémente `Urho.Application` qui est différent pour le `Xamarin.Forms.Application` classe qui est implémenté dans **App.cs**.

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

Le [UrhoSharp documentation](~/graphics-games/urhosharp/index.md) contient plus d’informations sur la façon de créer des scènes 3D et des actions.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Ajoutez la classe Charts à UrhoSurface

Utilisez le `UrhoSurface.Show<T>` une méthode générique pour ajouter l’application Urho à la page Xamarin.Forms. L’extrait de code ci-dessous montre le code supplémentaire requis pour créer la `Charts` classe :

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

Remarque : le `Show<T>` méthode est asynchrone et doit être appelée avec le `await` mot clé.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. interaction avec UrhoSharp

L’exemple permet à barres du graphique sélectionné et modifiés. Le `Charts` classe expose le `Bars` et `SelectedBar` pour permettre cette interaction.

Chaque « barre » a un gestionnaire d’événements de sélection ajouté après le `Charts` classe a été restituée en effectuant une itération sur exposés `Bars` collection :

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

Le Gestionnaire d’événements utilise la valeur de la Xamarin.Forms `Slider` contrôle pour ajuster la hauteur de la barre de donnée :

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

Enfin, associer les deux `Slider` contrôle afin que lorsque leur valeur change la zone de dessin UrhoSharp soit affectée. Le premier curseur fait pivoter l’image de graphique 3D, et le deuxième curseur ajuste la hauteur de la barre sélectionnée.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Les animations à le [supérieur de la page](#what-is-urhosharp) afficher l’exécution de l’exemple.

## <a name="summary"></a>Résumé

Cette page illustre l’utilisation des UrhoSharp pour ajouter la visualisation des données 3D à Xamarin.Forms. Lire le [UrhoSharp documentation](~/graphics-games/urhosharp/index.md) pour plus d’informations sur la création des scènes Urho qui peuvent être inclus dans les applications Xamarin.Forms à l’aide de la méthode illustrée ci-dessus.

## <a name="related-links"></a>Liens connexes

- [Exemple de graphiques (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
