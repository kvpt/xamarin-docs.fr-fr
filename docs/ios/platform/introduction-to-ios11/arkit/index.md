---
title: Présentation de ARKit dans Xamarin. iOS
description: Ce document décrit la réalité augmentée dans iOS 11 avec ARKit. Il explique comment ajouter un modèle 3D à une application, configurer l’affichage, implémenter un délégué de session, positionner le modèle 3D dans le monde et suspendre la session de réalité augmentée.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 47c092215afef4aa6964a39f7dcb5b685d98a4fc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655736"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Présentation de ARKit dans Xamarin. iOS

_Réalité augmentée pour iOS 11_

ARKit permet un large éventail d’applications et de jeux de réalité augmentés. Cette section couvre les rubriques suivantes :

- [Prise en main avec ARKit](#gettingstarted)
- [Utilisation de ARKit avec UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Prise en main avec ARKit

Pour prendre en main la réalité augmentée, les instructions suivantes vous guident dans une application simple: le positionnement d’un modèle 3D et la mise en place de ARKit pour maintenir le modèle en place avec sa fonctionnalité de suivi.

![Image de caméra flottante du modèle 3D jet](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Ajouter un modèle 3D

Les ressources doivent être ajoutées au projet avec l’action de génération **SceneKitAsset** .

![SceneKit des ressources dans un projet](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Configurer la vue

Dans la méthode du contrôleur `ViewDidLoad` d’affichage, chargez la ressource de scène `Scene` et définissez la propriété sur la vue:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. Implémentez éventuellement un délégué de session

Bien que cela ne soit pas obligatoire pour les cas simples, l’implémentation d’un délégué de session peut être utile pour déboguer l’état de la session ARKit (et dans les applications réelles, en fournissant des commentaires à l’utilisateur). Créez un délégué simple en utilisant le code ci-dessous:

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

Assignez le délégué dans dans `ViewDidLoad` la méthode:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Positionner le modèle 3D dans le monde

Dans `ViewWillAppear`, le code suivant établit une session ARKit et définit la position du modèle 3D dans l’espace relatif à l’appareil photo de l’appareil:

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

Chaque fois que l’application est exécutée ou reprise, le modèle 3D est positionné devant l’appareil photo. Une fois le modèle positionné, déplacez l’appareil photo et regardez en ARKit pour maintenir le modèle positionné.

### <a name="5-pause-the-augmented-reality-session"></a>5. Suspendre la session de réalité augmentée

Il est recommandé de suspendre la session ARKit quand le contrôleur d’affichage n’est pas visible (dans `ViewWillDisappear` la méthode:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Récapitulatif

Le code ci-dessus génère une application ARKit simple. Des exemples plus complexes s’attendent à ce que le contrôleur d’affichage hébergeant la `IARSCNViewDelegate`session de réalité augmentée à implémenter et que des méthodes supplémentaires soient implémentées.

ARKit fournit un grand nombre de fonctionnalités plus sophistiquées, telles que le suivi des surfaces et l’interaction avec l’utilisateur. Pour obtenir un exemple de combinaison de suivi ARKit avec UrhoSharp, consultez la [démonstration UrhoSharp](urhosharp.md) .


## <a name="related-links"></a>Liens associés

- [Réalité augmentée (Apple)](https://developer.apple.com/arkit/)
- [Utilisation de ARKit avec UrhoSharp](urhosharp.md)
- [Exemple simple de ARKit (jet)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit placement d’objets (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [Présentation de la réalité augmentée par ARKit pour iOS (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/602/)
