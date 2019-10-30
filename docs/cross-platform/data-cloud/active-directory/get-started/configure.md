---
title: Étape 2. Configurer l’accès au service pour une application mobile
description: Ce document explique comment fournir à une application Xamarin l’accès à une application Azure sécurisée par Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: eeac7b0b70b2f11304a374de7522f28d4bcad6c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016666"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Étape 2. Configurer l’accès au service pour une application mobile

Chaque fois qu’une ressource, par exemple une application Web, un service Web, etc. doit être sécurisée par Azure Active Directory, elle doit être inscrite. Toutes les applications ou services sécurisés peuvent être affichés sous l’onglet **applications** . Ici, vous pouvez sélectionner l’application à laquelle vous devez accéder à partir de l’application mobile et y accéder.

1. Sous l’onglet **configurer** , recherchez la section **autorisations pour d’autres applications** :

   ![](configure-images/2.1-configure.png "On the Configure tab, locate permissions to other applications section")

2. Cliquez sur le bouton **Ajouter une application** . Dans l’écran suivant, vous devez voir la liste de toutes les applications sécurisées par Azure Active Directory. Sélectionnez les applications qui doivent être accessibles à partir de l’application mobile.

   ![](configure-images/2.2-add-application.png "Select the applications that needs to be accessed from the mobile application")

3. Après avoir sélectionné l’application, sélectionnez de nouveau l’application nouvellement ajoutée dans la section **autorisations pour d’autres applications** et accordez les droits appropriés.

   ![](configure-images/2.3-permissions.png "After selecting the application, once again select the newly-added application in permissions to other   applications section and give appropriate rights")

4. Enfin, **Enregistrez** la configuration. Ces services doivent maintenant être disponibles dans les applications mobiles !

## <a name="related-links"></a>Liens associés

- [Exemple Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
