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
ms.sourcegitcommit: 266e75fa6893d3732e4e2c0c8e79c62be2804468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820967"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Ajout d’un module de reconnaissance des mouvements panoramiques

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_Le mouvement panoramique est utilisé pour détecter le mouvement des doigts sur l’écran et l’appliquer au contenu. Il est implémenté avec la classe `PanGestureRecognizer`. Un scénario courant du mouvement panoramique est l’exécution d’un panoramique horizontal et vertical d’une image pour que tout le contenu de l’image soit visible quand il est affiché dans une fenêtre d’affichage plus petite que les dimensions de l’image. Cette action est accomplie en déplaçant l’image dans la fenêtre d’affichage, elle est illustrée dans cet article._

Pour qu’un élément d’interface utilisateur puisse être déplacé avec un mouvement panoramique, créez une instance [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer), traitez l’événement [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) et ajoutez le nouveau module de reconnaissance de geste à la collection [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) sur l’élément d’interface utilisateur. L'exemple de code suivant montre un `PanGestureRecognizer` attaché à un élément [`Image`](xref:Xamarin.Forms.Image) :

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

Cette classe peut être encapsulée autour d’un élément d’interface utilisateur pour que le geste effectue un panoramique de l’élément d’interface utilisateur encapsulé. L’exemple de code XAML suivant montre l’encapsulage `PanContainer` d’un élément [`Image`](xref:Xamarin.Forms.Image) :

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

L’exemple de code suivant montre comment `PanContainer` encapsule un élément [`Image`](xref:Xamarin.Forms.Image) dans une page C# :

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

Dans les deux exemples, les propriétés [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) sont définies sur les valeurs de largeur et de hauteur de l’image affichée.

Quand l’élément [`Image`](xref:Xamarin.Forms.Image) reçoit un mouvement panoramique, l’image affichée fait l’objet d’un panoramique. Le panoramique est exécuté par la méthode `PanContainer.OnPanUpdated`, ce qui est illustré dans l’exemple de code suivant :

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

Cette méthode met à jour le contenu visible de l’élément d’interface utilisateur encapsulé, en fonction du mouvement panoramique de l’utilisateur. Pour cela, vous devez utiliser les valeurs des propriétés [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) et [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) de l’instance [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) pour calculer la direction et la distance du panoramique. Les propriétés `App.ScreenWidth` et `App.ScreenHeight` fournissent la hauteur et la largeur de la fenêtre d’affichage, et sont définies sur les valeurs de largeur et hauteur de l’écran de l’appareil par les projets respectifs propres à la plateforme. L’élément utilisateur encapsulé fait ensuite l’objet d’un panoramique en définissant ses propriétés [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) sur les valeurs calculées.

Quand vous effectuez un panoramique d’un élément qui n’occupe pas tout l’écran, la hauteur et la largeur de la fenêtre d’affichage peuvent être obtenues à partir des propriétés [`Height`](xref:Xamarin.Forms.VisualElement.Height) et [`Width`](xref:Xamarin.Forms.VisualElement.Width) de l’élément.

> [!NOTE]
> L’affichage d’images haute résolution peut augmenter considérablement l’empreinte mémoire d’une application. Par conséquent, elles doivent être créées seulement si elles sont nécessaires et doivent être libérées dès que l’application n’en a plus besoin. Pour plus d’informations, consultez [Optimiser les ressources d’images](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources).

## <a name="related-links"></a>Liens associés

- [PanGesture (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
