---
title: 'Partie 1 : création d’un MonoGame multiplateforme'
description: Cette procédure pas à pas montre comment créer un nouveau projet pour iOS et Android à l’aide de MonoGame. Le résultat est un Visual Studio pour la solution Mac avec un projet de code partagé multiplateformes ainsi que d’un projet pour chaque plateforme. Ce projet affiche un écran bleu vide lors de l’exécution.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c8ef426c742f875e26fc0fcf88a9468e1618e30f
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832528"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Partie 1 : création d’un MonoGame multiplateforme

_Cette procédure pas à pas montre comment créer un nouveau projet pour iOS et Android à l’aide de MonoGame. Le résultat est un Visual Studio pour la solution Mac avec un projet de code partagé multiplateformes ainsi que d’un projet pour chaque plateforme. Ce projet affiche un écran bleu vide lors de l’exécution._

MonoGame permet de développer des jeux multiplateformes avec une grande partie de la réutilisation de code. Cette procédure pas à pas se concentrera sur la configuration d’une solution qui contient des projets pour iOS et Android, ainsi que d’un projet de code partagé pour le code inter-plateformes.

Lorsque nous aurons terminé, nous ont un projet qui a la structure appropriée pour effectuer la logique de jeu de mise à jour et de jeux logique de dessin à 30 images par seconde. Il peut être utilisé en tant que le projet de base pour n’importe quel projet MonoGame. Notre projet ressemblera à ceci lors de l’exécution :

![Écran bleu vide](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>Ajout de MonoGame à Visual Studio pour Mac

MonoGame peut être ajouté comme un complément à Visual Studio pour Mac. Sur Mac, sélectionnez **Visual Studio pour Mac** > **Add-in Manager...**  . Sur Windows, sélectionnez ** Outils ** > **Add-in Manager...**  . Sélectionnez le **galerie** onglet, développez le **le développement de jeux** catégorie et sélectionnez **MonoGame Addin**, puis cliquez sur **installer**:

![Visual Studio pour la galerie d’extensions Mac en sélectionnant MonoGame](part1-images/image2.png)

> [!IMPORTANT]
> Si le **le développement de jeux** section n’apparaît pas dans le Gestionnaire de compléments, vous pouvez manuellement télécharger et installer la dernière version à partir d’ici : http://www.monogame.net/downloads/. Vous devrez peut-être redémarrer Visual Studio pour Mac pour les modèles s’affichent.

Une fois installé, les modèles de MonoGame apparaît dans Visual Studio pour Mac, comme nous le verrons dans la section suivante.

## <a name="creating-a-new-solution"></a>Création d’une solution

Dans Visual Studio pour, sélectionnez Mac **fichier > Nouvelle Solution**. Dans le **nouveau projet** boîte de dialogue, cliquez sur **divers**, faites défiler vers le **général** section, sélectionnez le ** application Universal MonoGame Mobile ** option, puis cliquez sur Suivant.

![Nouvelle boîte de dialogue projet Création d’une application MonoGame](part1-images/image3.png)

Nommez le projet WalkingGame, puis cliquez sur Créer :

![Nouvelle boîte de dialogue de projet un nom et un emplacement de prélèvement](part1-images/image4.png)

Désormais, notre projet s’exécute comme n’importe quel autre iOS ou Android projet. Le projet doit s’exécuter à afficher un arrière-plan bleu vif bleu :

![Arrière-plan de l’application bleue vide](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Correction des erreurs de compilation Android

La version actuelle des modèles de MonoGame inclut quelques erreurs de syntaxe dans l’Android `Activity1.cs` fichier. Pour résoudre ces problèmes, remplacez le `OnCreate` fonction par le code suivant :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas vu comment créer un projet de MonoGame multiplateforme à l’aide de Visual Studio pour Mac. Le résultat de ce est un écran bleu vide. Ce projet peut être utilisé comme point de départ pour iOS et Android jeu.

## <a name="related-links"></a>Liens associés

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentation de l’API de MonoGame](http://www.monogame.net/documentation/?page=main)
