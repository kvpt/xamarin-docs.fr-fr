---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparaison des tâches courantes
description: Ce document compare comment effectuer diverses tâches courantes sur WPF et Xamarin. Forms. Il examine les boutons, les minuteurs, les tailles de police, l’ouverture d’un URI et l’affichage d’une feuille d’action.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: fecd8ed774adbacf69e3b2db514a4698e71711d8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290329"
---
# <a name="common-tasks-comparison"></a>Comparaison des tâches courantes

| Tâche | WPF | Xamarin.Forms |
|--- |--- |--- |
|Afficher un message à l’écran avec des boutons|`MessageBox`|`Page.DisplayAlert`|
|Créer un minuteur|Classe `DispatcherTimer`|`Device.StartTimer`méthode statique|
|Obtenir une taille de police par défaut|`SystemFonts`classe statique|`Device.GetNamedSize`méthode statique|
|Ouvrir un URI/une URL|`Process.Start`|`Device.OpenUri`|
|Afficher une feuille d’action (liste de boutons)|N/A|`Page.DisplayActionSheet`|
