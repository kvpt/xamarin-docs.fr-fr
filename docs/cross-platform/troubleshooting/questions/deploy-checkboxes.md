---
title: Déployer des cases à cocher désactivées dans le Gestionnaire de Configuration
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: edf471f1d9a2ee4adc11f09e0c7b7ad3cf6f78f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014252"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Déployer des cases à cocher désactivées dans le Gestionnaire de Configuration

Depuis la Xamarin 3,5, les projets Xamarin. iOS sont déployés automatiquement chaque fois que vous appuyez sur le bouton de barre d’outils de **démarrage** ou sélectionnez l’élément de menu **déboguer > Démarrer le débogage** . Vous devrez toujours définir le projet d’application Xamarin. iOS souhaité comme projet de **démarrage** avant d’exécuter l’une de ces commandes.

Pour cette raison, les cases à cocher **déployer** sont intentionnellement désactivées dans Visual Studio Configuration Manager pour les projets Xamarin. iOS :

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager showing the 'Deploy' checkbox disabled for a Xamarin.iOS project in Xamarin 3.5")

Cette modification élimine une erreur qui peut s’afficher dans les versions antérieures de Xamarin (version 3,3 et versions antérieures) lorsque le projet d’application Xamarin. iOS n’a pas été défini sur Deploy :

![](deploy-checkboxes-images/error.png "Error dialog: The project iPhoneApp1 needs to be deployed before it can be started. Verify the project is selected to be deployed in the Solution Configuration Manager.")
