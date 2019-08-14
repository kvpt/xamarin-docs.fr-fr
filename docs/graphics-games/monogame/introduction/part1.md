---
title: 'Partie 1: création d’un monojeu multiplateforme'
description: Cette procédure pas à pas montre comment créer un projet pour iOS et Android à l’aide d’un monojeu. Le résultat est une solution Visual Studio pour Mac avec un projet de code partagé multiplateforme, ainsi qu’un projet pour chaque plate-forme. Ce projet affiche un écran bleu vide lorsqu’il est exécuté.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d72c428bb4b8c88365180c5c3c50b107eed2b21d
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68978447"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Partie 1: création d’un monojeu multiplateforme

_Cette procédure pas à pas montre comment créer un projet pour iOS et Android à l’aide d’un monojeu. Le résultat est une solution Visual Studio pour Mac avec un projet de code partagé multiplateforme, ainsi qu’un projet pour chaque plate-forme. Ce projet affiche un écran bleu vide lorsqu’il est exécuté._

Le monojeu permet le développement de jeux multiplateformes avec une grande partie de la réutilisation du code. Cette procédure pas à pas se concentre sur la configuration d’une solution qui contient des projets pour iOS et Android, ainsi que sur un projet de code partagé pour du code multiplateforme.

Une fois l’opération terminée, le projet dispose de la structure appropriée pour l’exécution de la logique de mise à jour de jeu et la logique de dessin de jeu à 30 images par seconde. Il peut être utilisé comme projet de base pour tous les projets monojeu. Le projet doit ressembler à ceci lorsqu’il est exécuté:

![Écran bleu vide](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio"></a>Ajout de polygame à Visual Studio

> [!IMPORTANT]
> Le monojeu n’est pas installé par défaut dans Visual Studio 2019 ou Visual Studio pour Mac.
>
> Vous devez télécharger et installer manuellement la version la plus http://www.monogame.net/downloads/ récente à partir de, puis exécuter le programme d’installation. Vous devrez peut-être redémarrer Visual Studio pour que les modèles s’affichent.
>
> La section **développement de jeux** doit alors apparaître dans le **Gestionnaire de compléments**.

Pour activer le complément monojeu pour Visual Studio pour Mac, sélectionnez **Visual Studio pour Mac** > **Gestionnaire de compléments...** . Pour Visual Studio 2019 sur Windows, sélectionnez **Outils** > **Gestionnaire de compléments...** . Sélectionnez l’onglet **Galerie** , développez la catégorie **développement de jeu** et sélectionnez complément monojeu, puis cliquez sur **installer...** :

![Galerie d’extensions de Visual Studio pour Mac sélection d’un monojeu](part1-images/image2.png)

Une fois l’installation terminée, les modèles de monojeu s’affichent dans Visual Studio pour Mac, comme nous le verrons dans la section suivante.

## <a name="creating-a-new-solution"></a>Création d’une nouvelle solution

Dans Visual Studio pour Mac sélectionnez **fichier > nouvelle solution**. Dans la boîte de dialogue **nouveau projet** , cliquez sur **divers**, faites défiler la section **général** , sélectionnez l’option **application mobile monojeu universelle** , puis cliquez sur suivant.

![Boîte de dialogue Nouveau projet création d’une application monojeu](part1-images/image3.png)

Nommez le projet WalkingGame, puis cliquez sur créer:

![Boîte de dialogue Nouveau projet qui sélectionne un nom et un emplacement](part1-images/image4.png)

À présent, notre projet s’exécutera comme n’importe quel autre projet iOS ou Android. Le projet doit s’exécuter en affichant un arrière-plan bleu bleuet:

![Arrière-plan d’application bleu vide](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Correction des erreurs de compilation Android

La version actuelle des modèles de monojeu comprend quelques erreurs de syntaxe dans le `Activity1.cs` fichier Android. Pour résoudre ces problèmes, remplacez la `OnCreate` fonction par le code suivant:

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

Cette procédure pas à pas a abordé la création d’un projet monojeu multiplateforme à l’aide de Visual Studio pour Mac. Le résultat de cet écran est un écran bleu vide. Ce projet peut être utilisé comme point de départ pour n’importe quel jeu iOS et Android.

## <a name="related-links"></a>Liens associés

- [Android NuGet monojeu](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS de monojeu NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentation sur les API monojeu](http://www.monogame.net/documentation/?page=main)
