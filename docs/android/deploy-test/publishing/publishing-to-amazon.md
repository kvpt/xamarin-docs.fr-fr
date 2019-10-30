---
title: Publication dans l’Amazon Appstore
ms.prod: xamarin
ms.assetid: A3E9EAC7-2968-8891-CDF2-B73FC0013EC9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 288ff00b35c369581e50b8e8777f85b7b6119590
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021302"
---
# <a name="publishing-to-the-amazon-app-store"></a>Publication dans l’Amazon Appstore

Le programme de distribution des applications mobiles d’Amazon permet aux développeurs d’applications mobiles de publier leurs applications sur Amazon. Cette section présente brièvement l’Amazon Appstore pour Android. 

[![Écran Amazon Appstore](publishing-to-amazon-images/amazon-app-store.png)](publishing-to-amazon-images/amazon-app-store.png#lightbox)

Amazon ne limite pas la taille des APK. Toutefois, si un APK est supérieur à 30 Mo, il est distribué par FTP et non via le Portail de distribution des applications mobiles de l'Amazon Appstore.

## <a name="submitting-apps-binary-info"></a>Envoi d’applications : informations binaires

Le processus d’envoi d’une application sur l’Amazon Appstore est similaire à celui de l’envoi d’une application sur Google Play. Les applications distribuées par Amazon requièrent les ressources suivantes : 

- **Icône** &ndash;   Fichier .png de 114 x 114 pixels avec un arrière-plan transparent. Cette icône est obligatoire.
- **Miniature** &ndash;   Version plus grande de l’icône ci-dessus, de 512 x 512 pixels avec un arrière-plan transparent. Cette icône est également obligatoire.
- **Captures d’écran** &ndash;   Amazon nécessite entre 3 (minimum) et 10 (maximum) captures d’écran. Les captures d’écran doivent être de 1024 x 600 pixels ou de 800 x 480 pixels (largeur x hauteur). Les formats .png et .jpg sont acceptés.
- **Image promotionnelle** &ndash;   Pour que l’application soit mise en avant dans des présentations promotionnelles comme la page d’accueil, une image promotionnelle peut éventuellement être envoyée. Elle doit être fournie dans un fichier .png ou .jpg de 1 024 x500 pixels (largeur x hauteur), en mode paysage. Elle ne doit pas contenir d’animation.
- Des mises à jour de cinq vidéos peuvent être fournies.

## <a name="approval-process"></a>Processus d’approbation

Une fois soumise, une application passe par un processus d’approbation.
Amazon examine votre application afin de s’assurer qu’elle fonctionne comme indiqué dans la description du produit, qu’elle ne met pas en péril les données des clients et qu’elle ne dégrade pas le fonctionnement de l’appareil. Une fois le processus d’approbation terminé, Amazon envoie une notification et distribue l’application.
