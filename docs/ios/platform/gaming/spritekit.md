---
title: SpriteKit dans Xamarin. iOS
description: Ce document décrit SpriteKit, l’infrastructure graphique 2D d’Apple qui s’intègre à SceneKit, intègre la physique et l’animation, inclut la prise en charge de l’éclairage et de l’ombrage, et bien plus encore. SpriteKit peut être utilisé pour créer des jeux 2D.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: dfda8b1ec3e7cfbdec3fe313d305d78422487f08
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289312"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit dans Xamarin. iOS

SpriteKit, l’infrastructure graphique 2D d’Apple, présente de nouvelles fonctionnalités intéressantes dans iOS 8 et OS X Yosemite. Il s’agit notamment de l’intégration avec SceneKit, la prise en charge des nuanceurs, l’éclairage, les ombres, les contraintes, la génération normale de cartes et les améliorations physiques. En particulier, les nouvelles fonctionnalités physiques rendent très facile l’ajout d’effets réalistes à un jeu.

## <a name="physics-bodies"></a>Corps physiques

SpriteKit comprend une API physique de corps rigide et 2D. Chaque sprite est associé à un corps physique`SKPhysicsBody`() qui définit les propriétés physiques telles que la masse et la friction, ainsi que la géométrie du corps dans le monde physique.

## <a name="creating-a-physics-body-from-a-texture"></a>Création d’un corps physique à partir d’une texture
SpriteKit prend désormais en charge la dérivation du corps physique d’un sprite à partir de sa texture. Il est ainsi plus facile d’implémenter des collisions qui semblent plus naturelles.

Par exemple, Notez dans le cas suivant comment la banane et le singe entrent en conflit presque à la surface de chaque image :
 
![](spritekit-images/image13.png "La banane et le singe sont en conflit presque à la surface de chaque image")

SpriteKit permet de créer un corps physique de ce type possible avec une seule ligne de code. Appelez `SKPhysicsBody.Create` simplement avec la texture et la taille : Sprite. PhysicsBody = SKPhysicsBody. Create (Sprite. Texture, Sprite. Taille);

## <a name="alpha-threshold"></a>Seuil alpha

En plus de définir simplement la `PhysicsBody` propriété directement dans la géométrie dérivée de la texture, les applications peuvent définir et le seuil alpha pour contrôler la façon dont la géométrie est dérivée. 

Le seuil alpha définit la valeur alpha minimale qu’un pixel doit avoir pour être inclus dans le corps physique résultant. Par exemple, le code suivant produit un corps physique légèrement différent :

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

L’effet de la modification du seuil alpha comme celui-ci ajuste la collision précédente, de telle sorte que le singe passe en collision avec la banane :

![](spritekit-images/image14.png "Le singe passe en collision avec la banane")
 
## <a name="physics-fields"></a>Champs de physique

La nouvelle prise en charge du champ physique constitue un autre excellent ajout à SpriteKit. Elles vous permettent d’ajouter des éléments tels que des champs Vortex, des champs de gravité radiaux et des champs Spring pour n’en nommer que quelques-uns.

Les champs physiques sont créés à l’aide de la classe SKFieldNode, qui est ajoutée à une scène `SKNode`comme n’importe quelle autre. Il existe plusieurs méthodes de fabrique sur `SKFieldNode` pour créer différents champs de physique. Vous pouvez créer un champ Spring en appelant `SKFieldNode.CreateSpringField()`, un champ de gravité radiale en appelant `SKFieldNode.CreateRadialGravityField()`, et ainsi de suite.

`SKFieldNode`possède également des propriétés pour contrôler les attributs de champ tels que l’intensité de champ, la région de champ et l’atténuation des forces de champ.

## <a name="spring-field"></a>Champ Spring

Par exemple, le code suivant crée un champ Spring et l’ajoute à la scène :

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

Vous pouvez ensuite ajouter des sprites et définir leurs `PhysicsBody` propriétés afin que le champ physique affecte les sprites, comme le montre le code suivant lorsque l’utilisateur touche l’écran :

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

Cela amène les bananes à osciller comme un ressort autour du nœud Field :

![](spritekit-images/image15.png "Les bananes oscillent comme un ressort autour du nœud de champ")
 
## <a name="radial-gravity-field"></a>Champ gravité radiale

L’ajout d’un autre champ est similaire. Par exemple, le code suivant crée un champ de gravité radiale :

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Cela génère un champ de force différent, où les bananes sont extraites de façon radiale à propos du champ :

![](spritekit-images/image16.png "Les bananes sont extraites de façon radiale autour du champ")
