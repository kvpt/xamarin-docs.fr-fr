---
title: Étape 2. Configurer l’accès au service pour une application mobile
description: Ce document explique comment fournir à une application Xamarin l’accès à une application Azure sécurisée par Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 1f0cdec005dc210600977d5c8f5606cff6570989
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290011"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Étape 2. Configurer l’accès au service pour une application mobile

Chaque fois qu’une ressource, par exemple une application Web, un service Web, etc. doit être sécurisée par Azure Active Directory, elle doit être inscrite. Toutes les applications ou services sécurisés peuvent être affichés sous l’onglet **applications** . Ici, vous pouvez sélectionner l’application à laquelle vous devez accéder à partir de l’application mobile et y accéder.

1. Sous l’onglet **configurer** , recherchez la section **autorisations pour d’autres applications** :

   ![](configure-images/2.1-configure.png "Sous l’onglet configurer, localisez la section autorisations pour d’autres applications.")

2. Cliquez sur le bouton **Ajouter une application** . Dans l’écran suivant, vous devez voir la liste de toutes les applications sécurisées par Azure Active Directory. Sélectionnez les applications qui doivent être accessibles à partir de l’application mobile.

   ![](configure-images/2.2-add-application.png "Sélectionner les applications qui doivent être accessibles à partir de l’application mobile")

3. Après avoir sélectionné l’application, sélectionnez de nouveau l’application nouvellement ajoutée dans la section **autorisations pour d’autres applications** et accordez les droits appropriés.

   ![](configure-images/2.3-permissions.png "Après avoir sélectionné l’application, sélectionnez de nouveau l’application nouvellement ajoutée dans la section autorisations pour d’autres applications et accordez les droits appropriés.")

4. Enfin, **Enregistrez** la configuration. Ces services doivent maintenant être disponibles dans les applications mobiles !



## <a name="related-links"></a>Liens associés

- [Exemple Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
