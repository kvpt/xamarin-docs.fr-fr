---
title: Framework de vision dans Xamarin. iOS
description: Ce document explique comment utiliser le Framework iOS 11 vision dans Xamarin. iOS. Plus précisément, il aborde la détection des rectangles et la détection des visages.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/31/2017
ms.openlocfilehash: b58e7b1fffed3253d9765401d52f16b751db134d
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304750"
---
# <a name="vision-framework-in-xamarinios"></a>Framework de vision dans Xamarin. iOS

L’infrastructure de vision ajoute un certain nombre de nouvelles fonctionnalités de traitement d’image à iOS 11, notamment :

- [Détection de rectangles](#rectangles)
- [Détection de visage](#faces)
- Analyse des images Machine Learning (décrite dans [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Détection de codes-barres
- Analyse de l’alignement de l’image
- Détection de texte
- Détection de l’horizon
- Suivi des & de détection d’objets

![Photographie avec trois rectangles détectés](vision-images/found-rectangles-tiny.png) ![Photographie avec deux visages détectés](vision-images/xamarin-home-faces-tiny.png)

La détection des rectangles et les Détection de visage sont décrits plus en détail ci-dessous.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Détection de rectangles

L' [exemple VisionRects](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles) montre comment traiter une image et dessiner les rectangles détectés dessus.

### <a name="1-initialize-the-vision-request"></a>1. initialiser la demande de vision

Dans `ViewDidLoad`, créez un `VNDetectRectanglesRequest` qui fait référence à la méthode `HandleRectangles` qui sera appelée à la fin de chaque requête :

La propriété `MaximumObservations` doit également être définie. dans le cas contraire, sa valeur par défaut est 1 et un seul résultat est retourné.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. démarrer le traitement de la vision

Le code suivant démarre le traitement de la requête. Dans l’exemple **VisionRects** , ce code s’exécute après que l’utilisateur a sélectionné une image :

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Ce gestionnaire passe le `ciImage` à l’infrastructure de vision `VNDetectRectanglesRequest` qui a été créé à l’étape 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. gérer les résultats du traitement de la vision

Une fois la détection de rectangle terminée, l’infrastructure exécute la méthode `HandleRectangles`, un résumé qui est affiché ci-dessous :

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. afficher les résultats

La méthode `OverlayRectangles` dans l’exemple **VisionRectangles** comporte trois fonctions :

- Rendu de l’image source,
- Dessin d’un rectangle pour indiquer l’emplacement où chacun a été détecté, et
- Ajout d’une étiquette de texte pour chaque rectangle à l’aide de CoreGraphics.

Affichez la source de l' [exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles) pour la méthode coregraphics exacte.

![Photographie avec trois rectangles détectés](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. traitement supplémentaire

La détection de rectangle est souvent simplement la première étape d’une chaîne d’opérations, comme avec [cet exemple CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), où les rectangles sont passés à un modèle CoreML pour analyser des chiffres manuscrits.

<a name="faces" />

## <a name="face-detection"></a>Détection de visage

L' [exemple VisionFaces](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces) fonctionne de la même façon que l’exemple **VisionRectangles** , à l’aide d’une classe de demande de vision différente.

### <a name="1-initialize-the-vision-request"></a>1. initialiser la demande de vision

Dans `ViewDidLoad`, créez un `VNDetectFaceRectanglesRequest` qui fait référence à la méthode `HandleRectangles` qui sera appelée à la fin de chaque demande.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. démarrer le traitement de la vision

Le code suivant démarre le traitement de la requête. Dans l’exemple **VisionFaces** , ce code s’exécute après que l’utilisateur a sélectionné une image :

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Ce gestionnaire passe le `ciImage` à l’infrastructure de vision `VNDetectFaceRectanglesRequest` qui a été créé à l’étape 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. gérer les résultats du traitement de la vision

Une fois la détection de visage terminée, le gestionnaire exécute la méthode `HandleRectangles` qui effectue la gestion des erreurs et affiche les limites des visages détectés, puis appelle la `OverlayRectangles` pour dessiner des rectangles englobants sur l’image d’origine :

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. afficher les résultats

La méthode `OverlayRectangles` dans l’exemple **VisionFaces** comporte trois fonctions :

- Rendu de l’image source,
- Dessin d’un rectangle pour chaque visage détecté, et
- Ajout d’une étiquette de texte pour chaque visage à l’aide de CoreGraphics.

Affichez la source de l' [exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces) pour la méthode coregraphics exacte.

![Photographie avec deux visages détectés](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. traitement supplémentaire

L’infrastructure de vision comprend des fonctionnalités supplémentaires pour détecter les caractéristiques du visage, telles que les yeux et la bouche. Utilisez le type de `VNDetectFaceLandmarksRequest`, qui renverra `VNFaceObservation` résultats comme à l’étape 3 ci-dessus, mais avec des données de `VNFaceLandmark` supplémentaires.

## <a name="related-links"></a>Liens connexes

- [Rectangles de vision (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles)
- [Visages de vision (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces)
- [Avance dans l’image principale-filtres, métal, vision, etc. (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/510/)
