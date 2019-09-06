---
title: 'Erreur lors de l’envoi à l’App Store : « Bundle non valide-options non autorisées à être incorporées dans bitcode sont détectées dans la soumission »'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 84244e0c4c24a8ca6ac71a79de963bedf5c1ee68
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292532"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Erreur lors de l’envoi à l’App Store : « Bundle non valide-options non autorisées à être incorporées dans bitcode sont détectées dans la soumission »

les applications Watchos et tvOS _requièrent_ bitcode quand elles sont envoyées à l’App Store. Lors de la création et de la soumission d’applications Watchos et tvOS à l’aide de Xcode 8,3 ou version antérieure, l’erreur suivante peut se produire (par notification par courrier électronique) lors d’une tentative de chargement vers l’App Store :

>Bundle non valide-l’application ne peut pas être traitée, car les options qui ne sont pas autorisées à être incorporées dans bitcode sont détectées dans la soumission. Il est probable que vous ne générez pas l’application avec le chaîne d’outils fourni dans Xcode.

La solution à ce problème consiste à créer les applications avec Xcode 9 et la dernière version de Xamarin. iOS.
