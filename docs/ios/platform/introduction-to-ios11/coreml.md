---
title: Présentation de CoreML dans Xamarin. iOS
description: Ce document décrit CoreML, qui permet d’Machine Learning sur iOS. Ce document explique comment prendre en main CoreML et comment l’utiliser avec l’infrastructure de vision.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: c2092cd9e7beb233c9478869ebff91d85b5b30c0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649602"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Présentation de CoreML dans Xamarin. iOS

CoreML apporte des Machine Learning à iOS: les applications peuvent tirer parti des modèles de Machine Learning formés pour effectuer toutes sortes de tâches, de la résolution des problèmes à la reconnaissance d’images.

Cette introduction aborde les sujets suivants:

- [Prise en main avec CoreML](#coreml)
- [Utilisation de CoreML avec l’infrastructure de vision](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Prise en main avec CoreML

Ces étapes décrivent comment ajouter CoreML à un projet iOS. Pour obtenir un exemple concret, reportez-vous à l’exemple de tarif de l' [habitat mars](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) .

![Capture d’écran de l’exemple de prédiction de prix d’habitat mars](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. Ajouter le modèle CoreML au projet

Ajoutez un modèle CoreML (un fichier avec l’extension **. mlmodel** ) au répertoire Resources du projet. 

Dans les propriétés du fichier de modèle, son **action de génération** est définie sur **CoreMLModel**. Cela signifie qu’elle sera compilée dans un fichier **. mlmodelc** lors de la génération de l’application.

### <a name="2-load-the-model"></a>2. Charger le modèle

Chargez le modèle à `MLModel.Create` l’aide de la méthode statique:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Définir les paramètres

Les paramètres de modèle sont passés et sortants à l’aide d’une `IMLFeatureProvider`classe de conteneur qui implémente.

Les classes de fournisseur de fonctionnalités se comportent `MLFeatureValue`comme un dictionnaire de chaînes et de s, où chaque valeur de fonctionnalité peut être une chaîne ou un nombre simple, un tableau ou des données, ou une mémoire tampon de pixels contenant une image.

Le code d’un fournisseur de fonctionnalités à valeur unique est illustré ci-dessous:

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

À l’aide de classes comme celle-ci, les paramètres d’entrée peuvent être fournis de façon compréhensible par CoreML. Les noms des fonctionnalités (comme `myParam` dans l’exemple de code) doivent correspondre à ce que le modèle attend.

### <a name="4-run-the-model"></a>4. Exécuter le modèle

L’utilisation du modèle requiert que le fournisseur de fonctionnalités soit instancié et que les paramètres soient définis, `GetPrediction` puis que la méthode soit appelée:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Extraire les résultats

Le résultat `outFeatures` de prédiction est également une instance `IMLFeatureProvider`de; les valeurs de sortie sont `GetFeatureValue` accessibles à l’aide de avec le nom de chaque `theResult`paramètre de sortie (tel que), comme dans cet exemple:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Utilisation de CoreML avec l’infrastructure de vision

CoreML peut également être utilisé conjointement avec l’infrastructure de vision pour effectuer des opérations sur une image, telles que la reconnaissance des formes, l’identification d’un objet et d’autres tâches.

Les étapes ci-dessous décrivent l’utilisation conjointe de CoreML et de vision dans l' [exemple CoreMLVision](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision). L’exemple combine la [reconnaissance](~/ios/platform/introduction-to-ios11/vision.md#rectangles) des rectangles de vision Framework avec le modèle _MNINSTClassifier_ CoreML pour identifier un chiffre manuscrit dans une photographie.

![Reconnaissance d’image du numéro 3](coreml-images/vision3.png) ![Reconnaissance d’image du numéro 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Créer un modèle de vision CoreML

Le modèle CoreML _MNISTClassifier_ est chargé, puis encapsulé dans un `VNCoreMLModel` qui rend le modèle disponible pour les tâches de vision. Ce code crée également deux demandes de vision: tout d’abord pour rechercher des rectangles dans une image, puis pour traiter un rectangle avec le modèle CoreML:

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

La classe doit toujours implémenter les `HandleRectangles` méthodes `HandleClassification` et pour les demandes de vision, présentées aux étapes 3 et 4 ci-dessous.

### <a name="2-start-the-vision-processing"></a>2. Démarrer le traitement de la vision

Le code suivant démarre le traitement de la requête. Dans l’exemple **CoreMLVision** , ce code s’exécute après que l’utilisateur a sélectionné une image:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Ce gestionnaire transmet `ciImage` à l’infrastructure `VNDetectRectanglesRequest` de vision créée à l’étape 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gérer les résultats du traitement de la vision

Une fois la détection de rectangle terminée, elle exécute la `HandleRectangles` méthode, qui rogne l’image pour extraire le premier rectangle, convertit l’image rectangle en nuances de gris et la transmet au modèle CoreML pour la classification.

Le `request` paramètre passé à cette méthode contient les détails de la demande de vision et, à `GetResults<VNRectangleObservation>()` l’aide de la méthode, elle retourne une liste de rectangles trouvés dans l’image. Le premier rectangle `observations[0]` est extrait et transmis au modèle CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

A été initialisé à l’étape 1 pour utiliser la `HandleClassification` méthode définie à l’étape suivante. `ClassificationRequest`

### <a name="4-handle-the-coreml"></a>4. Gérer le CoreML

Le `request` paramètre passé à cette méthode contient les détails de la requête CoreML et, à l' `GetResults<VNClassificationObservation>()` aide de la méthode, elle retourne une liste de résultats possibles triés par confiance (confiance la plus élevée en premier):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>Exemples

Il existe trois exemples CoreML à essayer:

* L' [exemple mars habitat Price Prediction](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) contient des entrées et sorties numériques simples.

* L' [exemple vision & CoreML](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision) accepte un paramètre image et utilise l’infrastructure de vision pour identifier les zones carrées de l’image, qui sont passées à un modèle CoreML qui reconnaît des chiffres uniques.

* Enfin, l' [exemple de reconnaissance d’image CoreML](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition) utilise CoreML pour identifier les fonctionnalités d’une photo. Par défaut, il utilise le modèle **SqueezeNet** plus petit (5 Mo), mais il a été écrit pour vous permettre de télécharger et d’intégrer le modèle **VGG16** (553MB). Pour plus d’informations, consultez le [fichier Lisez-moi de l’exemple](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Liens associés

- [Machine Learning (Apple)](https://developer.apple.com/machine-learning/)
- [Exemple CoreML (habitat mars) (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)
- [CoreML et vision (reconnaissance numérique) (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)
- [Reconnaissance d’images CoreML (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition)
- [CoreML avec Azure Custom Vision (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlazuremodel)
- [Présentation de CoreML (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/703/)
