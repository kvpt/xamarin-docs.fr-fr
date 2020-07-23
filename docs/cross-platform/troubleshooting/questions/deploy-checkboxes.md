---
title: Déployer des cases à cocher désactivées dans le Gestionnaire de Configuration
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: 92fe421aee6b5add84a188468856172bf8c56d15
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939280"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Déployer des cases à cocher désactivées dans le Gestionnaire de Configuration

Depuis la Xamarin 3,5, les projets Xamarin. iOS sont déployés automatiquement chaque fois que vous appuyez sur le bouton de barre d’outils de **démarrage** ou sélectionnez l’élément de menu **déboguer > démarrer le débogage** . Vous devrez toujours définir le projet d’application Xamarin. iOS souhaité comme projet de **démarrage** avant d’exécuter l’une de ces commandes.

Pour cette raison, les cases à cocher **déployer** sont intentionnellement désactivées dans Visual Studio Configuration Manager pour les projets Xamarin. iOS :

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager showing the 'Deploy' checkbox disabled for a Xamarin.iOS project in Xamarin 3.5")

Cette modification élimine une erreur qui peut s’afficher dans les versions antérieures de Xamarin (version 3,3 et versions antérieures) lorsque le projet d’application Xamarin. iOS n’a pas été défini sur Deploy :

![Boîte de dialogue d’erreur : le projet iPhoneApp1 doit être déployé avant de pouvoir être démarré. Vérifiez que le projet est sélectionné pour être déployé dans le Configuration Manager de la solution.](deploy-checkboxes-images/error.png)
