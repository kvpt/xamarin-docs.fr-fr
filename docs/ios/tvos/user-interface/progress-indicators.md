---
title: Utilisation des indicateurs de progression tvOS dans Xamarin
description: Ce document explique comment utiliser les indicateurs de progression dans une application tvOS créée avec Xamarin. Il traite des barres de progression et des indicateurs d’activité.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 202ce8d674a39b06fd1b07460dff4bf573062592
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70291404"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>Utilisation des indicateurs de progression tvOS dans Xamarin

_Cet article traite de la conception et de l’utilisation des indicateurs de progression dans une application Xamarin. tvOS._

Il peut arriver que votre application Xamarin. tvOS doive charger un nouveau contenu ou effectuer une opération de traitement longue. Dans ce cas, vous devez présenter un indicateur d’activité ou une barre de progression pour informer l’utilisateur que l’application est toujours en cours d’exécution et lui donner une indication sur la durée de l’exécution de la tâche.

![Exemples d’indicateurs de progression](progress-indicators-images/intro01.png "Exemples d’indicateurs de progression")

## <a name="about-activity-indicators"></a>À propos des indicateurs d’activité

Un indicateur d’activité présente comme une roue dentée rotative et est utilisé pour représenter une tâche d’une longueur indéterminée. L’indicateur est présenté lorsque la tâche démarre et disparaît lorsque la tâche est terminée.

Apple propose les suggestions suivantes pour l’utilisation des indicateurs d’activité :

- Dans la mesure du **possible, utilisez des barres de progression** à la place, car un indicateur d’activité ne donne à l’utilisateur aucun commentaire sur la durée d’exécution du processus, toujours utiliser une barre de progression si la longueur est connue (par exemple, le nombre d’octets à télécharger dans un fichier).
- **Garder l’indicateur animé** : les utilisateurs associent un indicateur d’activité stationnaire à une application bloquée. vous devez donc toujours animer l’indicateur pendant qu’il est affiché.
- **Décrire la tâche en cours de traitement** : l’affichage de l’indicateur d’activité seul n’est pas suffisant ; l’utilisateur doit être informé du processus sur lequel il est en attente. Incluez une étiquette significative (généralement une phrase unique et complète) qui définit clairement la tâche.

## <a name="about-progress-bars"></a>À propos des barres de progression

Une barre de progression présente comme une ligne qui remplit la couleur pour indiquer l’État et la durée d’une tâche longue. Les barres de progression doivent toujours être utilisées lorsque la longueur des tâches est connue ou peut être calculée.

Apple propose les suggestions suivantes pour l’utilisation des barres de progression :

- **Signaler avec précision la progression** -les barres de progression doivent toujours présenter une représentation précise du temps nécessaire à l’exécution d’une tâche. Ne vous contentez pas de rendre l’application visible.
- **À utiliser pour les durées bien définies** -les barres de progression ne montrent pas seulement qu’une longue tâche est effectuée, mais donnent à l’utilisateur et indique la quantité de tâche terminée et une estimation du temps restant.

## <a name="progress-indicators-and-storyboards"></a>Indicateurs de progression et storyboards

Le moyen le plus simple d’utiliser un indicateur de progression dans une application Xamarin. tvOS consiste à l’ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur le fichier **main. Storyboard** et ouvrez-le pour le modifier.

2. Faites glisser un **indicateur d’activité** de la **boîte à outils** et déposez-le sur la vue : 

    ![Indicateur d’activité](progress-indicators-images/activity01.png "Indicateur d’activité")

3. Dans l’onglet **widget** du **panneau Propriétés**, vous pouvez ajuster plusieurs propriétés de l’indicateur d’activité, telles que son **style**, son **comportement**et son **nom**: 

    ![Onglet de widget pour un indicateur d’activité](progress-indicators-images/activity02.png "Onglet de widget pour un indicateur d’activité")
    
    Le **nom** détermine le nom de la propriété qui représente l’indicateur d’activité C# dans le code.

4. Faites glisser une **vue de progression** de la **boîte à outils** et déposez-la sur la vue : 

    ![Vue de la progression](progress-indicators-images/activity03.png "Vue de la progression")

5. Dans l’onglet **widget** de l' **Explorateur de propriétés**, vous pouvez ajuster plusieurs propriétés de la vue de la progression, telles que son **style**, sa **progression** (pourcentage de réalisation) et son **nom**: 

    ![Onglet de widget pour une vue de la progression](progress-indicators-images/activity04.png "Onglet de widget pour une vue de la progression")
    
    Le **nom** détermine le nom de la propriété qui représente la vue de la C# progression dans le code.

6. Enregistrez les modifications apportées.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans la **Explorateur de solutions**, double-cliquez sur le fichier **main. Storyboard** et ouvrez-le pour le modifier.

2. Faites glisser un **indicateur d’activité** de la **boîte à outils** et déposez-le sur la vue : 

    ![Indicateur d’activité](progress-indicators-images/activity01-vs.png
    "Indicateur d’activité")

3. Dans l’onglet **widget** de l' **Explorateur de propriétés**, vous pouvez ajuster plusieurs propriétés de l’indicateur d’activité, telles que son **style**, son **comportement**et son **nom**: 

    ![Onglet de widget pour un indicateur d’activité](progress-indicators-images/activity02-vs.png "Onglet de widget pour un indicateur d’activité")

    Le **nom** détermine le nom de la propriété qui représente l’indicateur d’activité C# dans le code.

4. Faites glisser une **vue de progression** de la **boîte à outils** et déposez-la sur la vue : 

   ![Vue de la progression](progress-indicators-images/activity03-vs.png "Vue de la progression")

5. Dans l’onglet **widget** de l' **Explorateur de propriétés**, vous pouvez ajuster plusieurs propriétés de la vue de la progression, telles que son **style**, sa **progression** (pourcentage de réalisation) et son **nom**: 

    ![Onglet de widget pour une vue de la progression](progress-indicators-images/activity04-vs.png "Onglet de widget pour une vue de la progression")
    
    Le **nom** détermine le nom de la propriété qui représente la vue de la C# progression dans le code.

6. Enregistrez les modifications apportées.

-----

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Utilisation des indicateurs d’activité

Comme indiqué ci-dessus, les indicateurs d’activité doivent s’afficher lorsque votre application exécute un processus long de longueur indéterminée.

À tout moment, vous pouvez voir si un indicateur d’activité est en train d’être animé en vérifiant sa propriété `IsAnimating`. Si la propriété `HidesWhenStopped` est `true`, l’indicateur d’activité est automatiquement masqué lorsque son animation est arrêtée.

Vous pouvez utiliser le code suivant pour démarrer l’animation : 

```csharp
ActivityIndicator.StartAnimating();
```

La commande suivante permet d’arrêter l’animation :

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Ces extraits de code supposent que le **nom** de l’indicateur d’activité a été défini sur **ActivityIndicator** dans l’onglet **widget** du concepteur iOS.

## <a name="working-with-progress-bars"></a>Utilisation des barres de progression

Là encore, une barre de progression doit être utilisée chaque fois que votre application exécute une tâche longue d’une durée connue. 

La propriété `Progress` permet de définir la valeur de la tâche qui est terminée de 0% à 100% (0,0 à 1,0). Utilisez la propriété `ProgressTintColor` pour définir la couleur de la barre quantité terminée et la propriété `TrackTintColor` pour définir la couleur d’arrière-plan (quantité incomplète).

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation des indicateurs de progression dans une application Xamarin. tvOS.

## <a name="related-links"></a>Liens connexes

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
