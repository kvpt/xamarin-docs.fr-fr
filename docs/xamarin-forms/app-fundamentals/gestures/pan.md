---
title: Ajout d’un module de reconnaissance des mouvements panoramiques
description: Cet article explique comment utiliser un mouvement panoramique pour faire un panoramique horizontal et vertical d’une image afin que tout le contenu de l’image soit visible quand il est affiché dans une fenêtre d’affichage plus petite que les dimensions de l’image.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 73e312a1af56091a7e579d3fcbcea810ee0efb1e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68820967"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Ajout d’un module de reconnaissance des mouvements panoramiques

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_Le geste de casserole est employé pour détecter le mouvement des doigts autour de `PanGestureRecognizer` l’écran et appliquer ce mouvement au contenu, et est implémenté avec la classe. Un scénario commun pour le geste de pan est de panoramique horizontalement et verticalement une image, de sorte que tout le contenu de l’image peut être visualisé quand il est affiché dans un viewport plus petit que les dimensions d’image. Ceci est accompli en déplaçant l’image dans le viewport, et est démontré dans cet article._

Pour rendre un élément d’interface utilisateur évoluable avec le geste de pan, créez une [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) instance, gérez l’événement [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) et ajoutez le nouveau gestateur à la [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) collection sur l’élément d’interface utilisateur. L’exemple de `PanGestureRecognizer` code suivant [`Image`](xref:Xamarin.Forms.Image) montre un élément attaché à un élément :

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Cette opération est également possible en XAML, comme le montre l’exemple de code suivant :

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Le code pour le gestionnaire d'événements `OnPanUpdated` est ensuite ajouté au fichier code-behind :

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

## <a name="creating-a-pan-container"></a>Création d’un conteneur panoramique

Cette section contient une classe d’assistance généralisée qui effectue un panoramique de forme libre, généralement adapté à la navigation dans les images ou les cartes. Le traitement du mouvement panoramique pour effectuer cette opération nécessite quelques calculs pour transformer l’interface utilisateur. Ces calculs sont utilisés pour effectuer un panoramique uniquement dans les limites de l’élément d’interface utilisateur encapsulé. L’exemple de code suivant illustre la classe `PanContainer` :

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

Cette classe peut être encapsulée autour d’un élément d’interface utilisateur pour que le geste effectue un panoramique de l’élément d’interface utilisateur encapsulé. L’exemple de code XAML suivant montre l’emballage `PanContainer` d’un [`Image`](xref:Xamarin.Forms.Image) élément :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

L’exemple de code `PanContainer` suivant [`Image`](xref:Xamarin.Forms.Image) montre comment l’enveloppe un élément dans une page C :

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

Dans les deux [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) exemples, les propriétés et les propriétés sont définies à la largeur et aux valeurs de hauteur de l’image affichée.

Lorsque [`Image`](xref:Xamarin.Forms.Image) l’élément reçoit un geste pan, l’image affichée sera en panne. Le panoramique est exécuté par la méthode `PanContainer.OnPanUpdated`, ce qui est illustré dans l’exemple de code suivant :

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

Cette méthode met à jour le contenu visible de l’élément d’interface utilisateur encapsulé, en fonction du mouvement panoramique de l’utilisateur. Ceci est réalisé en utilisant [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) les valeurs [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) de l’instance et les propriétés de l’instance pour calculer la direction et la distance de la casserole. Les propriétés `App.ScreenWidth` et `App.ScreenHeight` fournissent la hauteur et la largeur de la fenêtre d’affichage, et sont définies sur les valeurs de largeur et hauteur de l’écran de l’appareil par les projets respectifs propres à la plateforme. L’élément utilisateur enveloppé est ensuite [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) en panne en définissant son et ses propriétés aux valeurs calculées.

Lorsque le contenu panoramique dans un élément qui n’occupe pas le plein écran, la hauteur [`Height`](xref:Xamarin.Forms.VisualElement.Height) [`Width`](xref:Xamarin.Forms.VisualElement.Width) et la largeur du port de vue peuvent être obtenues à partir de l’élément et les propriétés.

> [!NOTE]
> L’affichage d’images haute résolution peut augmenter considérablement l’empreinte mémoire d’une application. Par conséquent, elles doivent être créées seulement si elles sont nécessaires et doivent être libérées dès que l’application n’en a plus besoin. Pour plus d’informations, consultez [Optimiser les ressources d’images](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources).

## <a name="related-links"></a>Liens connexes

- [PanGesture (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
