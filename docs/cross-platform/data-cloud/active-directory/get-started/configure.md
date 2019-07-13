---
title: Étape 2. Configurer l’accès au Service d’Application Mobile
description: Ce document décrit comment fournir une application Xamarin avec l’accès à une application Azure sécurisée par Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e0015316b7be3462982ee0959862250c0c27dc74
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864657"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Étape 2. Configurer l’accès au Service d’Application Mobile

Chaque fois que les ressources, par exemple, l’application web, les service web, etc. doit être sécurisé par Azure Active Directory, il doit être enregistré. Toutes les applications sécurisées ou les services peuvent être affichés sous **Applications** onglet. Ici, vous pouvez sélectionner l’application qui doit être accessible à partir de l’application mobile et lui donner accès.

1. Sur le **configurer** onglet, recherchez **autorisations pour d’autres applications** section :

   ![](configure-images/2.1-configure.png "Sous l’onglet Configurer, recherchez les autorisations à la section d’autres applications")

2. Cliquez sur **ajouter application** bouton. Dans l’écran suivant contextuelle, vous devez voir la liste de toutes les applications qui sont sécurisés par Azure Active Directory. Sélectionnez les applications qui ont besoin d’être accessibles à partir de l’application mobile.

   ![](configure-images/2.2-add-application.png "Sélectionnez les applications qui ont besoin d’être accessibles à partir de l’application mobile")

3. Après avoir sélectionné l’application, une fois encore sélectionner l’application qui vient d’être ajouté dans **autorisations pour d’autres applications** section et donner des droits appropriés.

   ![](configure-images/2.3-permissions.png "Après avoir sélectionné l’application, une fois encore sélectionner l’application qui vient d’être ajouté dans les autorisations pour la section d’autres applications et donner des droits appropriés")

4. Enfin, **enregistrer** la configuration. Ces services doivent maintenant être disponibles dans les applications mobiles !



## <a name="related-links"></a>Liens associés

- [Exemple de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
