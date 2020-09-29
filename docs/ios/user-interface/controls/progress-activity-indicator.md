---
title: Indicateurs de progression et d’activité dans Xamarin. iOS
description: Ce document explique comment utiliser les indicateurs de progression et d’activité dans Xamarin. iOS. Il décrit comment les utiliser par programme et avec un Storyboard.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: a2e8e6d1fdf3f51f015dccf12a6a3b455ceff741
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432749"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Indicateurs de progression et d’activité dans Xamarin. iOS

Il est probable que votre application doit exécuter des tâches de longue durée, telles que le chargement ou le traitement des données, et que ce délai peut entraîner un retard dans la mise à jour de votre interface utilisateur. Pendant ce temps, vous devez toujours utiliser un indicateur de progression pour rassurer l’utilisateur que le système est occupé à travailler. Cela donne au contrôle utilisateur que l’application travaille sur sa demande, qu’il n’attend pas son entrée et peut fournir un moyen de détailler exactement le temps qu’il doit attendre.

iOS offre deux méthodes principales pour fournir cette indication de progression dans votre application : les indicateurs d’activité (y compris un indicateur d’activité _réseau_ spécifique) et les barres de progression.

## <a name="activity-indicator"></a>Indicateur d’activité

Les indicateurs d’activité doivent être affichés lorsque votre application exécute un processus long, mais que vous ne connaissez pas la durée exacte requise par la tâche.

Apple propose les suggestions suivantes pour l’utilisation des indicateurs d’activité :

- Dans la mesure du **possible, utilisez des barres de progression** à la place, car un indicateur d’activité ne donne à l’utilisateur aucun commentaire sur la durée d’exécution du processus, toujours utiliser une barre de progression si la longueur est connue (par exemple, le nombre d’octets à télécharger dans un fichier).
- **Garder l’indicateur animé** : les utilisateurs associent un indicateur d’activité stationnaire à une application bloquée. vous devez donc toujours faire en sorte que l’indicateur soit animé pendant qu’il est affiché.
- **Décrire la tâche en cours de traitement** : simplement l’affichage de l’indicateur d’activité seul n’est pas suffisant, l’utilisateur doit être informé du processus sur lequel il est en attente. Incluez une étiquette significative (généralement une phrase unique et complète) qui définit clairement la tâche.

### <a name="implementing-an-activity-indicator"></a>Implémentation d’un indicateur d’activité

Un indicateur d’activité est implémenté par le biais de la [`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView) classe pour indiquer qu’un `UIActivity` a eu lieu.

### <a name="activity-indicators-and-storyboards"></a>Indicateurs d’activité et storyboards

Si vous utilisez le concepteur iOS pour créer votre interface utilisateur, l’indicateur d’activité peut être ajouté à votre disposition à partir de la boîte à outils. Les propriétés suivantes peuvent être ajustées à partir de la Panneau Propriétés :

![Panneau Propriétés](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Gestion du comportement des indicateurs d’activité

Utilisez les `StartAnimating()` `StopAnimating()` méthodes et pour démarrer et arrêter l’animation des indicateurs d’activité.

Affectez à la propriété la valeur `HidesWhenStopped` `true` pour que l’indicateur d’activité disparaisse après l' `StopAnimating()` appel de. La valeur par `true` défaut est. À tout moment, vous pouvez voir si l’indicateur d’activité exécute son animation en vérifiant la `IsAnimating` propriété. 

### <a name="managing-activity-indicator-appearances"></a>Gestion des apparences des indicateurs d’activité

L' `UIActivityIndicatorViewStyle` énumération peut être passée en tant que paramètre lors de l’instanciation de l’indicateur d’activité. Vous pouvez l’utiliser pour définir le style visuel sur `Gray` , `White` ou `WhiteLarge` , par exemple :

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

Vous pouvez substituer la couleur fournie par `UIActivityIndicatorViewStyle`  en définissant la `Color` propriété.

## <a name="progress-bar"></a>ProgressBar

Une barre de progression présente comme une ligne qui remplit la couleur pour indiquer l’État et la durée d’une tâche longue. Les barres de progression doivent toujours être utilisées lorsque la longueur des tâches est connue ou peut être calculée.

Apple propose les suggestions suivantes pour l’utilisation des barres de progression :

- **Signalez avec précision la progression** -les barres de progression doivent toujours être une représentation exacte du temps nécessaire à l’exécution d’une tâche. Ne vous contentez pas de rendre l’application visible.
- **Utiliser pour les durées bien définies** : la barre de progression ne doit pas seulement montrer qu’une longue tâche a lieu, mais fournir à l’utilisateur et indiquer la quantité de tâche terminée et une estimation du temps restant.

### <a name="implementing-an-progress-bar"></a>Implémentation d’une barre de progression

Une barre de progression est créée en instanciant un [`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>Barres de progression et storyboards

Vous pouvez également ajouter une barre de progression à votre interface utilisateur lors de l’utilisation du concepteur iOS. Recherchez vue de la **progression** dans la **boîte à outils** et faites-la glisser vers votre vue.

Les propriétés suivantes peuvent être ajustées dans le panneau Propriétés :

![Panneau Propriétés](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>Gestion du comportement de la barre de progression

La progression de la barre peut être définie initialement à l’aide de la `Progress` propriété :

```csharp
ProgressBar.Progress = 0f;
```

La progression peut être ajustée à l’aide de la `SetProgress` méthode et en passant une déclaration booléenne si vous souhaitez que la modification soit animée.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Pour plus d’informations sur l’utilisation de la barre de progression, reportez-vous à la recette de la [progression des rapports](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) et à l' [exemple UICatalog tvOS](/samples/xamarin/ios-samples/tvos-uicatalog).

### <a name="managing-progress-bar-appearance"></a>Gestion de l’apparence de la barre de progression

À l’instar d’un indicateur d’activité, l' `UIProgressViewStyle` énumération peut être passée en tant que paramètre lors de l’instanciation de la barre de progression.

L’image de progression et de suivi et la couleur de teinte peuvent être ajustées à l’aide des propriétés suivantes :

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```