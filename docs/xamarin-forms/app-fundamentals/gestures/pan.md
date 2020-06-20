---
title: Ajout d’un module de reconnaissance des mouvements panoramiques
description: Cet article explique comment utiliser un mouvement panoramique pour faire un panoramique horizontal et vertical d’une image afin que tout le contenu de l’image soit visible quand il est affiché dans une fenêtre d’affichage plus petite que les dimensions de l’image.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 53122991811c06360e8d015a753096cb35c1cca0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137629"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Ajout d’un module de reconnaissance des mouvements panoramiques

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_Le mouvement de panoramique est utilisé pour détecter le mouvement des doigts sur l’écran et appliquer ce mouvement au contenu, et est implémenté avec la `PanGestureRecognizer` classe. Un scénario courant pour le mouvement panoramique consiste à faire pivoter horizontalement et verticalement une image, afin que tout le contenu de l’image puisse être affiché lorsqu’il est affiché dans une fenêtre d’affichage plus petite que les dimensions de l’image. Pour ce faire, vous devez déplacer l’image dans la fenêtre d’affichage et elle est illustrée dans cet article._

Pour rendre un élément d’interface utilisateur déplaçable avec le mouvement de panoramique, créez une [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) instance, gérez l' [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) événement, puis ajoutez le nouveau module de reconnaissance de mouvement à la [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) collection sur l’élément d’interface utilisateur. L’exemple de code suivant montre un `PanGestureRecognizer` attaché à un [`Image`](xref:Xamarin.Forms.Image) élément :

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

Cette classe peut être encapsulée autour d’un élément d’interface utilisateur pour que le geste effectue un panoramique de l’élément d’interface utilisateur encapsulé. L’exemple de code XAML suivant illustre l' `PanContainer` encapsulation d’un [`Image`](xref:Xamarin.Forms.Image) élément :

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

L’exemple de code suivant montre comment le `PanContainer` encapsule un [`Image`](xref:Xamarin.Forms.Image) élément dans une page C# :

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

Dans les deux exemples, [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) les [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Propriétés et sont définies sur les valeurs de largeur et de hauteur de l’image affichée.

Lorsque l' [`Image`](xref:Xamarin.Forms.Image) élément reçoit un mouvement de panoramique, l’image affichée sera panoramique. Le panoramique est exécuté par la méthode `PanContainer.OnPanUpdated`, ce qui est illustré dans l’exemple de code suivant :

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

Cette méthode met à jour le contenu visible de l’élément d’interface utilisateur encapsulé, en fonction du mouvement panoramique de l’utilisateur. Pour ce faire, utilisez les valeurs des [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) Propriétés et [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) de l' [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) instance pour calculer la direction et la distance du panoramique. Les propriétés `App.ScreenWidth` et `App.ScreenHeight` fournissent la hauteur et la largeur de la fenêtre d’affichage, et sont définies sur les valeurs de largeur et hauteur de l’écran de l’appareil par les projets respectifs propres à la plateforme. L’élément utilisateur encapsulé est ensuite panoramique en définissant [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) ses [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriétés et sur les valeurs calculées.

Lorsque vous panoramiqueez du contenu dans un élément qui n’occupe pas le plein écran, la hauteur et la largeur de la fenêtre d’affichage peuvent être obtenues à partir des propriétés et de l’élément [`Height`](xref:Xamarin.Forms.VisualElement.Height) [`Width`](xref:Xamarin.Forms.VisualElement.Width) .

> [!NOTE]
> L’affichage d’images haute résolution peut augmenter considérablement l’empreinte mémoire d’une application. Par conséquent, elles doivent être créées seulement si elles sont nécessaires et doivent être libérées dès que l’application n’en a plus besoin. Pour plus d’informations, consultez [Optimiser les ressources d’images](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources).

## <a name="related-links"></a>Liens connexes

- [PanGesture (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
