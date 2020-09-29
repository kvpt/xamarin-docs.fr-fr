---
title: Animation principale dans Xamarin. iOS
description: Cet article examine l’infrastructure d’animation principale, qui montre comment elle permet des animations fluides et hautes performances dans UIKit, et comment l’utiliser directement pour un contrôle d’animation de niveau inférieur.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d25d48421ad9b05925c1fa373ddba600ad3bac2e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431223"
---
# <a name="core-animation-in-xamarinios"></a>Animation principale dans Xamarin. iOS

_Cet article examine l’infrastructure d’animation principale, qui montre comment elle permet des animations fluides et hautes performances dans UIKit, et comment l’utiliser directement pour un contrôle d’animation de niveau inférieur._

iOS comprend une [*animation principale*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) pour assurer la prise en charge de l’animation pour les vues dans votre application.
Toutes les animations ultra-lisses dans iOS, telles que le défilement des tables et le balayage entre les différentes vues, fonctionnent aussi bien qu’elles s’appuient sur l’animation principale en interne.

L’animation principale et les infrastructures graphiques principales peuvent fonctionner ensemble pour créer de superbes graphiques 2D animés. En fait, l’animation principale peut même transformer des graphiques 2D en espace 3D, créant ainsi des expériences cinématographiques étonnantes. Toutefois, pour créer des graphiques 3D véritables, vous devez utiliser des éléments tels que OpenGL ES, ou pour que les jeux se transforment en API comme monojeu, bien que la 3D dépasse le cadre de cet article.

<a name="Using_Core_Animation"></a>

## <a name="core-animation"></a>Animation de base

iOS utilise l’infrastructure d’animation principale pour créer des effets d’animation tels que la transition entre des affichages, des menus déroulants et des effets de défilement pour n’en nommer que quelques-uns. Il existe deux façons d’utiliser l’animation :

- [Via UIKit](#Using_UIKit_Animation), qui comprend des animations basées sur les vues, ainsi que des transitions animées entre les contrôleurs.
- Les [animations de base](#Using_Core_Animation), qui effectuent des couches directement, ce qui permet un contrôle plus précis.

<a name="Using_UIKit_Animation"></a>

## <a name="using-uikit-animation"></a>Utilisation de l’animation UIKit

UIKit fournit plusieurs fonctionnalités qui facilitent l’ajout d’une animation à une application. Bien qu’elle utilise l’animation de base en interne, elle l’extrait pour vous permettre de travailler uniquement avec les vues et les contrôleurs.

Cette section traite des fonctionnalités d’animation UIKit, notamment :

- Transitions entre contrôleurs
- Transitions entre les vues
- Afficher l’animation des propriétés

### <a name="view-controller-transitions"></a>Transitions du contrôleur d’affichage

 `UIViewController` fournit la prise en charge intégrée pour la transition entre les contrôleurs d’affichage via la `PresentViewController` méthode. Lors de l’utilisation de `PresentViewController` , la transition vers le deuxième contrôleur peut éventuellement être animée.

Par exemple, considérez une application avec deux contrôleurs, où le contact d’un bouton dans le premier contrôleur appelle `PresentViewController` pour afficher un deuxième contrôleur. Pour contrôler l’animation de transition utilisée pour afficher le deuxième contrôleur, il vous suffit de définir sa [`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle) propriété comme indiqué ci-dessous :

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

Dans ce cas `PartialCurl` , une animation est utilisée, bien que plusieurs autres soient disponibles, notamment :

- `CoverVertical` : Diapositives à partir du bas de l’écran
- `CrossDissolve` : L’ancienne vue disparaît en fondu & la nouvelle vue apparaît en fondu
- `FlipHorizontal` -Retournement horizontal de droite à gauche. En cas de disparition, la transition s’inverse de gauche à droite.

Pour animer la transition, passez `true` comme deuxième argument à `PresentViewController` :

```csharp
PresentViewController (vc2, true, null);
```

La capture d’écran suivante montre à quoi ressemble la transition pour le `PartialCurl` cas :

 ![Cette capture d’écran montre la transition PartialCurl](core-animation-images/06-view-transitions.png)

### <a name="view-transitions"></a>Afficher les transitions

En plus des transitions entre les contrôleurs, UIKit prend également en charge l’animation des transitions entre les vues pour échanger une vue pour une autre.

Par exemple, imaginons que vous disposiez d’un contrôleur avec `UIImageView` , où le taraudage de l’image devrait afficher une seconde `UIImageView` . Pour animer la SuperVue de la vue image afin de passer à la deuxième vue image, il suffit d’appeler `UIView.Transition` , en lui transmettant le `toView` et `fromView` comme indiqué ci-dessous :

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` prend également un `duration` paramètre qui contrôle la durée d’exécution de l’animation, ainsi que [`options`](xref:UIKit.UIViewAnimationOptions) pour spécifier des éléments tels que l’animation à utiliser et la fonction d’accélération. En outre, vous pouvez spécifier un gestionnaire d’achèvement qui sera appelé à la fin de l’animation.

La capture d’écran ci-dessous montre la transition animée entre les vues d’image lorsque `TransitionFlipFromTop` est utilisé :

 ![Cette capture d’écran montre la transition animée entre les vues d’image quand TransitionFlipFromTop est utilisé](core-animation-images/07-animated-transition.png)

### <a name="view-property-animations"></a>Afficher les animations de propriétés

UIKit prend en charge l’animation gratuite d’une variété de propriétés sur la `UIView` classe, notamment :

- Frame
- Bounds
- Center
- Alpha
- Transformer
- Couleur

Ces animations se produisent implicitement en spécifiant des modifications de propriétés dans un `NSAction` délégué passé à la `UIView.Animate` méthode statique. Par exemple, le code suivant anime le point central d’un `UIImageView` :

```csharp
pt = imgView.Center;

UIView.Animate (
  duration: 2, 
  delay: 0, 
  options: UIViewAnimationOptions.CurveEaseInOut | 
    UIViewAnimationOptions.Autoreverse,
  animation: () => {
    imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
      - imgView.Frame.Width / 2, pt.Y);},
  completion: () => {
    imgView.Center = pt; }
);
```

Cela entraîne l’animation d’une image en arrière-plan dans la partie supérieure de l’écran, comme indiqué ci-dessous :

 ![Une image qui s’affiche en arrière et en sens horizontalement en haut de l’écran comme sortie](core-animation-images/08-animate-center.png)

Comme avec la `Transition` méthode, `Animate` permet de définir la durée, ainsi que la fonction d’accélération. Cet exemple utilisait également l' `UIViewAnimationOptions.Autoreverse` option, ce qui a pour effet que l’animation s’anime de la valeur à la valeur initiale. Toutefois, le code rétablit également `Center` sa valeur initiale dans un gestionnaire d’achèvement. Tandis qu’une animation interpole les valeurs de propriété dans le temps, la valeur de modèle réelle de la propriété est toujours la valeur finale qui a été définie. Dans cet exemple, la valeur est un point près du côté droit de la SuperView. Sans définir `Center` sur le point initial, où l’animation se termine en raison de la `Autoreverse` définition, l’image s’aligne sur le côté droit une fois l’animation terminée, comme indiqué ci-dessous :

 ![Sans définir le centre sur le point initial, l’image est réalignée sur le côté droit une fois l’animation terminée.](core-animation-images/09-animation-complete.png)

## <a name="using-core-animation"></a>Utilisation de l’animation principale

 `UIView` les animations permettent un grand nombre de fonctionnalités et doivent être utilisées si possible en raison de la facilité d’implémentation. Comme mentionné précédemment, les animations UIView utilisent l’infrastructure d’animation principale. Toutefois, certaines choses ne peuvent pas être effectuées à l’aide d' `UIView` animations, telles que l’animation de propriétés supplémentaires qui ne peuvent pas être animées avec une vue ou l’interpolation sur un tracé non linéaire. Dans ce cas, si vous avez besoin d’un contrôle plus fin, l’animation principale peut également être utilisée directement.

### <a name="layers"></a>Calques

Lorsque vous travaillez avec l’animation de base, l’animation se produit via des *couches*, qui sont de type `CALayer` . Une couche est conceptuellement similaire à une vue dans la mesure où il existe une hiérarchie de couche, à l’instar d’une hiérarchie d’affichage. En fait, les vues de couche arrière, avec la vue qui ajoute la prise en charge de l’interaction de l’utilisateur. Vous pouvez accéder à la couche de n’importe quelle vue via la propriété de la vue `Layer` . En fait, le contexte utilisé dans `Draw` la méthode de `UIView` est effectivement créé à partir de la couche. En interne, la couche de stockage `UIView` a a son délégué défini sur la vue elle-même, ce qui est appelé par `Draw` . Ainsi, lorsque vous dessinez dans un `UIView` , vous dessinez en fait sur sa couche.

Les animations de couche peuvent être implicites ou explicites. Les animations implicites sont déclaratives. Il suffit de déclarer les propriétés de couche qui doivent changer et l’animation fonctionne. Les animations explicites à l’inverse sont créées à l’aide d’une classe d’animation qui est ajoutée à une couche. Les animations explicites permettent un contrôle supplémentaire sur la façon dont une animation est créée. Les sections suivantes abordent en détail les animations implicites et explicites.

### <a name="implicit-animations"></a>Animations implicites

L’une des façons d’animer les propriétés d’une couche est d’utiliser une animation implicite. `UIView` les animations créent des animations implicites. Toutefois, vous pouvez également créer des animations implicites directement sur une couche.

Par exemple, le code suivant définit une couche `Contents` à partir d’une image, définit une largeur et une couleur de bordure, puis ajoute la couche en tant que sous-couche de la couche de la vue :

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  layer = new CALayer ();
  layer.Bounds = new CGRect (0, 0, 50, 50);
  layer.Position = new CGPoint (50, 50);
  layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
  layer.ContentsGravity = CALayer.GravityResize;
  layer.BorderWidth = 1.5f;
  layer.BorderColor = UIColor.Green.CGColor;

  View.Layer.AddSublayer (layer);
}
```

Pour ajouter une animation implicite pour la couche, encapsulez simplement les modifications de propriété dans un `CATransaction` . Cela permet d’animer des propriétés qui ne seraient pas animables avec une animation de vue, telle que `BorderWidth` et `BorderColor` comme indiqué ci-dessous :

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);

  CATransaction.Begin ();
  CATransaction.AnimationDuration = 10;
  layer.Position = new CGPoint (50, 400);
  layer.BorderWidth = 5.0f;
  layer.BorderColor = UIColor.Red.CGColor;
  CATransaction.Commit ();
}
```

Ce code anime également le de la couche `Position` , qui est l’emplacement du point d’ancrage de la couche mesuré à partir du coin supérieur gauche des coordonnées de la supercouche. Le point d’ancrage d’une couche est un point normalisé dans le système de coordonnées de la couche.

L’illustration suivante montre la position et le point d’ancrage :

 ![Cette illustration montre la position et le point d’ancrage](core-animation-images/10-postion-anchorpt.png)

Lorsque l’exemple est exécuté, le `Position` `BorderWidth` et l' `BorderColor` animent, comme indiqué dans les captures d’écran suivantes :

 ![Lorsque l’exemple est exécuté, l’animation position, BorderWidth et BorderColor comme indiqué](core-animation-images/11-implicit-animation.png)

### <a name="explicit-animations"></a>Animations explicites

Outre les animations implicites, l’animation principale comprend une variété de classes qui héritent de `CAAnimation` qui vous permettent d’encapsuler des animations qui sont ensuite explicitement ajoutées à une couche. Ils permettent un contrôle plus précis sur les animations, telles que la modification de la valeur de début d’une animation, le regroupement d’animations et la spécification d’images clés pour autoriser les chemins non linéaires.

Le code suivant montre un exemple d’animation explicite à l’aide `CAKeyframeAnimation` d’un pour la couche indiquée précédemment (dans la section animation implicite) :

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);
  
  // get the initial value to start the animation from
  CGPoint fromPt = layer.Position;
  
  /* set the position to coincide with the final animation value
  to prevent it from snapping back to the starting position
  after the animation completes*/
  layer.Position = new CGPoint (200, 300);
  
  // create a path for the animation to follow
  CGPath path = new CGPath ();
  path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
  
  // create a keyframe animation for the position using the path
  CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
  animPosition.Path = path;
  animPosition.Duration = 2;
  
  // add the animation to the layer.
  /* the "position" key is used to overwrite the implicit animation created
  when the layer positino is set above*/
  layer.AddAnimation (animPosition, "position");
}
```

Ce code modifie le `Position` de la couche en créant un tracé qui est ensuite utilisé pour définir une animation d’image clé. Notez que la couche `Position` est définie sur la valeur finale du `Position` de l’animation. Sans cela, la couche retourne brusquement à son `Position` avant l’animation, car l’animation modifie uniquement la valeur de présentation et non la valeur de modèle réelle. En définissant la valeur de modèle sur la valeur finale de l’animation, la couche reste en place à la fin de l’animation.

Les captures d’écran suivantes montrent la couche contenant l’image qui anime le chemin spécifié :

 ![Cette capture d’écran montre la couche contenant l’image qui s’anime dans le chemin spécifié](core-animation-images/12-explicit-animation.png)

## <a name="summary"></a>Résumé

Dans cet article, nous avons examiné les fonctionnalités d’animation fournies par le biais des frameworks d' *animation centraux* . Nous avons examiné l’animation principale, en illustrant la façon dont elle alimente les animations dans UIKit et comment elle peut être utilisée directement pour le contrôle d’animation de niveau inférieur.

## <a name="related-links"></a>Liens associés

- [Exemple d’animation principale](/samples/xamarin/ios-samples/graphicsandanimation)
- [Graphismes de base](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Procédure pas à pas graphiques et animation](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animation de base](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)