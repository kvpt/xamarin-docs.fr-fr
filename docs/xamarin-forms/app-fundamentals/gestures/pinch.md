---
title: Ajouter un module de reconnaissance de mouvement de pincement
description: Cet article explique comment utiliser le geste de pincement pour effectuer un zoom interactif d’une image à l’emplacement du pincement.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a3405e3bff1b500bcf0f7fab6bea1a888f2d4604
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918280"
---
# <a name="add-a-pinch-gesture-recognizer"></a>Ajouter un module de reconnaissance de mouvement de pincement

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

_Le mouvement de pincement est utilisé pour effectuer un zoom interactif et est implémenté avec la classe PinchGestureRecognizer. Un scénario courant pour le geste de pincement consiste à effectuer un zoom interactif d’une image à l’emplacement de pincement. Pour ce faire, il faut mettre à l’échelle le contenu de la fenêtre d’affichage et est illustré dans cet article._

Pour rendre un élément d’interface utilisateur zoomable avec le geste de pincement, créez une [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) instance, gérez l' [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) événement et ajoutez le nouveau module de reconnaissance de mouvement à la [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) collection sur l’élément d’interface utilisateur. L’exemple de code suivant montre un `PinchGestureRecognizer` attaché à un [`Image`](xref:Xamarin.Forms.Image) élément :

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Cette opération est également possible en XAML, comme le montre l’exemple de code suivant :

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Le code pour le gestionnaire d'événements `OnPinchUpdated` est ensuite ajouté au fichier code-behind :

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Création d’un conteneur PinchToZoom

Le traitement du pincement pour effectuer cette opération nécessite quelques calculs pour transformer l’interface utilisateur. Cette section contient une classe d’assistance généralisée pour effectuer les calculs, qui peuvent être utilisés pour zoomer de manière interactive sur n’importe quel élément d’interface utilisateur. L’exemple de code suivant illustre la classe `PinchToZoomContainer` :

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

Cette classe peut être encapsulée autour d’un élément d’interface utilisateur pour que le pincement effectue un zoom sur l’élément d’interface utilisateur encapsulé. L’exemple de code XAML suivant illustre l' `PinchToZoomContainer` encapsulation d’un [`Image`](xref:Xamarin.Forms.Image) élément :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

L’exemple de code suivant montre comment le `PinchToZoomContainer` encapsule un [`Image`](xref:Xamarin.Forms.Image) élément dans une page C# :

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

Lorsque l' [`Image`](xref:Xamarin.Forms.Image) élément reçoit un mouvement de pincement, l’image affichée est zoomée ou sortante. Le zoom est effectué par la `PinchZoomContainer.OnPinchUpdated` méthode, qui est illustrée dans l’exemple de code suivant :

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

Cette méthode met à jour le niveau de zoom de l’élément d’interface utilisateur encapsulé, en fonction du geste de pincement de l’utilisateur. Pour ce faire, utilisez les valeurs des [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale) Propriétés, [`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) et [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) de l' [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) instance pour calculer le facteur d’échelle à appliquer à l’origine du mouvement de pincement. L’élément utilisateur encapsulé est ensuite agrandi à l’origine du geste de pincement en affectant [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) aux propriétés, et les valeurs calculées.

## <a name="related-links"></a>Liens associés

- [PinchGesture (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
