---
title: SceneKit dans Xamarin. iOS
description: Ce document décrit SceneKit, une API graphique de scène 3D qui simplifie l’utilisation des graphiques 3D en éliminant les complexités de OpenGL.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 5279effa83a8784f6d475188e67a535f7b5e1262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032502"
---
# <a name="scenekit-in-xamarinios"></a>SceneKit dans Xamarin. iOS

SceneKit est une API graphique de scène 3D qui simplifie l’utilisation de graphiques 3D. Elle a été introduite pour la première fois dans le système d’exploitation X 10,8 et est désormais disponible dans iOS 8. Avec SceneKit, la création de visualisations 3D immersives et de jeux informels 3D ne nécessite pas d’expertise dans OpenGL. En s’appuyant sur les concepts courants du graphique de scène, SceneKit résume les complexités de OpenGL et OpenGL ES, ce qui facilite l’ajout de contenu 3D à une application. Toutefois, si vous êtes un expert OpenGL, SceneKit prend également en charge la liaison directe avec OpenGL. Il comprend également de nombreuses fonctionnalités qui complètent les graphiques 3D, telles que la physique, et s’intègrent parfaitement avec plusieurs autres frameworks Apple, tels que l’animation principale, l’image principale et le sprite kit.

SceneKit est très facile à utiliser. Il s’agit d’une API déclarative qui prend en charge le rendu. Vous configurez simplement une scène, y ajoutez des propriétés, et SceneKit gère le rendu de la scène.

Pour utiliser SceneKit, vous créez un graphique de scène à l’aide de la classe `SCNScene`. Une scène contient une hiérarchie de nœuds, représentés par des instances de `SCNNode`, qui définissent des emplacements dans l’espace 3D. Chaque nœud possède des propriétés telles que la géométrie, l’éclairage et les matériaux qui affectent son apparence, comme illustré dans la figure suivante :

![](scenekit-images/image7.png "The SceneKit hierarchy")

## <a name="create-a-scene"></a>Créer une scène

Pour qu’une scène apparaisse sur l’écran, vous l’ajoutez à une `SCNView` en l’affectant à la propriété de scène de la vue. En outre, si vous apportez des modifications à la scène, `SCNView` se met à jour pour afficher les modifications.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Les scènes peuvent être remplies à partir de fichiers exportés via un outil de modélisation 3D ou par programmation à partir de primitives géométriques. Par exemple, voici comment créer une sphère et l’ajouter à la scène :

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Ajouter de la lumière

À ce stade, la sphère n’affiche rien, car il n’y a pas de lumière dans la scène. L’attachement de `SCNLight` instances à des nœuds crée des lumières dans SceneKit. Il existe plusieurs types de lumières, allant des différentes formes d’éclairage directionnel à l’éclairage ambiant. Par exemple, le code suivant crée une lumière omnidirectionnelle sur le côté de la sphère :

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

L’éclairage omnidirectionnel produit une réflexion diffuse qui se traduit par un éclairage même, à l’instar d’une lampe torche. La création d’une lumière ambiante est similaire, même si elle n’a pas de sens, car elle se distingue de manière égale dans toutes les directions. Imaginez qu’il s’agit d’un éclairage de humeur :)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Une fois les lumières en place, la sphère est maintenant visible dans la scène.

![](scenekit-images/image8.png "The sphere is visible in the scene when lit")

## <a name="adding-a-camera"></a>Ajout d’une caméra

L’ajout d’une caméra (SCNCamera) à la scène modifie le point de vue. Le modèle d’ajout de l’appareil photo est similaire. Créez l’appareil photo, attachez-le à un nœud et ajoutez le nœud à la scène.

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

Comme vous pouvez le voir dans le code ci-dessus, vous pouvez créer des objets SceneKit à l’aide de constructeurs ou de la méthode Create Factory. La première permet d' C# utiliser la syntaxe de l’initialiseur, mais celle qui doit être utilisée est principalement une question de préférence.

Une fois l’appareil photo en place, la totalité de la sphère est visible par l’utilisateur :

![](scenekit-images/image9.png "The entire sphere is visible to the user")

Vous pouvez également ajouter des lumières supplémentaires à la scène. Voici à quoi elle ressemble avec quelques lumières plus omnidirectionnelle :

![](scenekit-images/image10.png "The sphere with a few more omnidirectional lights")

En outre, en définissant `sceneView.AllowsCameraControl = true`, l’utilisateur peut modifier le point de vue avec un mouvement tactile.

### <a name="materials"></a>Destinés

Les matériaux sont créés avec la classe SCNMaterial. Par exemple, pour ajouter une image sur la surface de la sphère, définissez l’image sur le contenu *diffus* du matériau.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Cela couche l’image sur le nœud comme indiqué ci-dessous :

![](scenekit-images/image11.png "Layering the image onto the sphere")

Un matériau peut également être configuré pour répondre à d’autres types d’éclairage. Par exemple, l’objet peut être rendu brillant et son contenu spéculaire est défini pour afficher la réflexion spéculaire, ce qui se traduit par un spot lumineux sur la surface, comme indiqué ci-dessous :

![](scenekit-images/image12.png "The object made shiny with specular reflection, resulting in a bright spot on the surface")

Les matériaux sont très flexibles, ce qui vous permet d’obtenir beaucoup de code très peu volumineux. Par exemple, au lieu de définir l’image sur le contenu diffus, définissez-la sur le contenu réfléchissant à la place.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Le singe semble maintenant asseoir visuellement au sein de la sphère, indépendamment du point de vue.

### <a name="animation"></a>Animation

SceneKit est conçu pour fonctionner correctement avec l’animation. Vous pouvez créer des animations implicites ou explicites, et même afficher une scène à partir d’une arborescence de couche d’animation principale. Lors de la création d’une animation implicite, SceneKit fournit sa propre classe de transition, `SCNTransaction`.

Voici un exemple qui fait pivoter la sphère :

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

Vous pouvez cependant animer bien plus que la rotation. De nombreuses propriétés de SceneKit peuvent être animées. Par exemple, le code suivant anime la `Shininess` du matériau pour augmenter la réflexion spéculaire.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit est très simple à utiliser. Il offre une multitude de fonctionnalités supplémentaires, notamment les contraintes, la physique, les actions déclaratives, le texte 3D, la profondeur de prise en charge des champs, l’intégration du kit de sprite et l’intégration d’images principales pour n’en nommer que quelques-uns.
