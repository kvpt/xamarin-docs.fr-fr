---
title: Noyau ML 2 dans Xamarin. iOS
description: Ce document décrit les mises à jour de Core ML disponibles dans le cadre d’iOS 12. En particulier, il examine les améliorations des performances associées à la nouvelle API de prédiction par lot.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/15/2018
ms.openlocfilehash: 13eecdfe3ded3a0fd68594527f6c5bc8ca3a6c66
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434242"
---
# <a name="core-ml-2-in-xamarinios"></a>Noyau ML 2 dans Xamarin. iOS

Core ML est une technologie Machine Learning disponible sur iOS, macOS, tvOS et Watchos. Elle permet aux applications d’effectuer des prédictions basées sur des modèles de Machine Learning.

Dans iOS 12, Core ML comprend une API de traitement par lots. Cette API améliore l’efficacité de l’utilisation de Core ML et permet d’améliorer les performances dans les scénarios où un modèle est utilisé pour effectuer une séquence de prédictions.

## <a name="sample-app-marshabitatcoremltimer"></a>Exemple d’application : MarsHabitatCoreMLTimer

Pour illustrer des prédictions par lot avec Core ML, jetez un coup d’œil sur l’exemple d’application [MarsHabitatCoreMLTimer](/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer) . Cet exemple utilise un modèle noyau ML formé pour prédire le coût de la construction d’un habitat sur mars, sur la base de diverses entrées : le nombre de panneaux solaires, le nombre de serres et le nombre de acres.

Les extraits de code de ce document proviennent de cet exemple.

## <a name="generate-sample-data"></a>Générer un exemple de données

Dans `ViewController` , les appels de méthode de l’exemple d’application `ViewDidLoad` `LoadMLModel` , qui chargent le modèle ml de noyau inclus :

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

L’exemple d’application crée ensuite 100 000 `MarsHabitatPricerInput` objets à utiliser comme entrée pour les prédictions de noyaux de base-ml. Chaque échantillon généré a une valeur aléatoire définie pour le nombre de panneaux solaires, le nombre de serres et le nombre de acres :

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

En appuyant sur l’un des trois boutons de l’application, vous exécutez deux séquences de prédictions : une à l’aide d’une `for` boucle et une autre à l’aide de la nouvelle méthode de traitement par lots `GetPredictions` introduite dans IOS 12 :

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>for (boucle)

La `for` version en boucle du naïvement de test itère au sein du nombre spécifié d’entrées, en appelant [`GetPrediction`](xref:CoreML.MLModel.GetPrediction*) pour chaque et en ignorant le résultat. La méthode indique le temps nécessaire pour effectuer les prédictions :

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>GetPredictions (nouvelle API batch)

La version de lot du test crée un `MLArrayBatchProvider` objet à partir du tableau d’entrée (puisqu’il s’agit d’un paramètre d’entrée requis pour la `GetPredictions` méthode), crée un [`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
objet qui empêche les calculs de prédiction d’être limités au processeur, et utilise l' `GetPredictions` API pour extraire les prédictions, en ignorant le résultat :

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>Résultats

Sur le simulateur et l’appareil, `GetPredictions` se termine plus rapidement que les prédictions noyau en boucle.

## <a name="related-links"></a>Liens connexes

- [Exemple d’application – MarsHabitatCoreMLTimer](/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)
- [Nouveautés de Core ML, partie 1 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Nouveautés de Core ML, partie 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Présentation de Core ML dans Xamarin. iOS](../introduction-to-ios11/coreml.md)
- [Noyau ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Utilisation des modèles ML de noyau](https://developer.apple.com/machine-learning/build-run-models/)