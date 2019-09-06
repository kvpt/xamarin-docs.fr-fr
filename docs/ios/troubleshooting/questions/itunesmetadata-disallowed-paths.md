---
title: 'Pourquoi ma soumission d’application échoue avec : Chemins d’accès non autorisés (iTunesMetadata. plist) disponibles à l’adresse... ?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: ad663c47520826cc549011c9e5fc7cbb078d6214
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291031"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>Pourquoi ma soumission d’application échoue avec : Chemins d’accès non autorisés (iTunesMetadata. plist) disponibles à l’adresse... ?

> ERREURS ERREUR ITMS-90047 : « Chemins d’accès non autorisés («iTunesMetadata. plist ») disponibles à l’adresse suivante : Payload/iPhoneApp1.app"

Cette erreur est le résultat d’une modification apportée au processus de vérification App Store d’Apple pour empêcher les utilisateurs d’atteindre des problèmes tels que le [bogue 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Cette erreur spécifique n’est _pas_ liée à la version particulière de Xamarin que vous avez installée. par conséquent, la rétrogradation n’est _pas_ utile.

Consultez la discussion du forum [ici](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) pour obtenir des informations de contournement et les dernières mises à jour pour ce problème.
