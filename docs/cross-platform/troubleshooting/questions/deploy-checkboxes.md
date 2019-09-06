---
title: Déployer des cases à cocher désactivées dans le Gestionnaire de Configuration
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 82ff1a684ffad75a301f0db6b0f8e3116be6746d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285067"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Déployer des cases à cocher désactivées dans le Gestionnaire de Configuration

Depuis la Xamarin 3,5, les projets Xamarin. iOS sont déployés automatiquement chaque fois que vous appuyez sur le bouton de barre d’outils de **démarrage** ou sélectionnez l’élément de menu **déboguer > Démarrer le débogage** . Vous devrez toujours définir le projet d’application Xamarin. iOS souhaité comme projet de **démarrage** avant d’exécuter l’une de ces commandes.

Pour cette raison, les cases à cocher **déployer** sont intentionnellement désactivées dans Visual Studio Configuration Manager pour les projets Xamarin. iOS :

![](deploy-checkboxes-images/configuration.png "Configuration Manager Visual Studio présentant la case à cocher « déployer » désactivée pour un projet Xamarin. iOS dans Xamarin 3,5")

Cette modification élimine une erreur qui peut s’afficher dans les versions antérieures de Xamarin (version 3,3 et versions antérieures) lorsque le projet d’application Xamarin. iOS n’a pas été défini sur Deploy :

![](deploy-checkboxes-images/error.png "Boîte de dialogue d’erreur : Le projet iPhoneApp1 doit être déployé avant de pouvoir être démarré. Vérifiez que le projet est sélectionné pour être déployé dans le Configuration Manager de la solution.")
