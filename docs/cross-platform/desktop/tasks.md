---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparaison des tâches courantes
description: Ce document compare comment effectuer diverses tâches courantes sur WPF et Xamarin. Forms. Il examine les boutons, les minuteurs, les tailles de police, l’ouverture d’un URI et l’affichage d’une feuille d’action.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: d1f1430d8044f94c17a19d747334b5ed8a1441cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016424"
---
# <a name="common-tasks-comparison"></a>Comparaison des tâches courantes

| Tâche | WPF | Xamarin.Forms |
|--- |--- |--- |
|Afficher un message à l’écran avec des boutons|`MessageBox`|`Page.DisplayAlert`|
|Créer un minuteur|Classe `DispatcherTimer`|méthode statique `Device.StartTimer`|
|Obtenir une taille de police par défaut|classe statique `SystemFonts`|méthode statique `Device.GetNamedSize`|
|Ouvrir un URI/une URL|`Process.Start`|`Device.OpenUri`|
|Afficher une feuille d’action (liste de boutons)|N/A|`Page.DisplayActionSheet`|
