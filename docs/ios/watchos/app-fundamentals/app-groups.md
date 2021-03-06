---
title: Utilisation des groupes d’applications Watchos dans Xamarin
description: Ce document décrit les groupes d’applications et leur utilisation dans une application Watchos. Il explique comment configurer un groupe d’applications, les exigences de configuration, les considérations relatives à l’approvisionnement et le déploiement de.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: ee3897f14c460149e840fcea8b3fb533beeab935
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931623"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Utilisation des groupes d’applications Watchos dans Xamarin

Un App Group est un groupe qui permet à différentes applications (ou à une application et ses extensions) d’accéder à un emplacement partagé du stockage de fichiers. Vous pouvez utiliser des App Groups pour les données suivantes, par exemple :

- [Paramètres](~/ios/watchos/app-fundamentals/settings.md)de Apple Watch.
- [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)partagé.
- [Fichiers](~/ios/watchos/app-fundamentals/parent-app.md#files)partagés.

## <a name="configure-an-app-group"></a>Configurer un groupe d’applications

L’emplacement partagé est configuré à l’aide d’un [groupe d’applications](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), qui est configuré dans la section **certificats, identificateurs & profils** du [Centre de développement iOS](https://developer.apple.com/devcenter/ios/). Cette valeur doit également être référencée dans les **droits. plist**de chaque projet.

### <a name="provisioning"></a>Provisionnement

Le groupe d’applications aura un identificateur, qui est généralement votre ID d’offre groupée avec un `group.` préfixe. Par exemple, nous pourrions utiliser l’ID d’offre groupée `com.xamarin.WatchSettings` et le groupe d’applications `group.com.xamarin.WatchSettings` .

[![Utilisez l’ID de Bundle com. xamarin. WatchSettings et le groupe d’applications. com. xamarin. WatchSettings](app-groups-images/app-group-sml.png)](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Vous pouvez également configurer le profil de provisionnement, **activer des groupes d’applications** dans les **droits. plist** et entrer l’ID que vous avez choisi :

[![Configurez les plist et entrez l’ID](app-groups-images/entitlements-sml.png)](app-groups-images/entitlements.png#lightbox)

### <a name="deployment"></a>Déploiement

Veillez à configurer correctement le groupe d’applications dans votre approvisionnement de [déploiement](~/ios/watchos/deploy-test/index.md#App_Groups) .

Pour plus d’informations, consultez la documentation sur les [fonctionnalités du groupe d’applications](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="related-links"></a>Liens associés

- [Partager des données avec votre application conteneur](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Doc du groupe d’applications Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
