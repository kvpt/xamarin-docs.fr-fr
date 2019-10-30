---
title: 'Pourquoi l’envoi de mon application échoue-t-il avec : chemins d’accès non autorisés (iTunesMetadata. plist) disponibles à l’adresse... ?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0255c918f7d6e984c9af2b396d9a2a00286286e4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030992"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>Pourquoi l’envoi de mon application échoue-t-il avec : chemins d’accès non autorisés (iTunesMetadata. plist) disponibles à l’adresse... ?

> ERREUR : erreur ITMS-90047 : « chemins d’accès non autorisés («iTunesMetadata. plist ») trouvés dans : charge utile/iPhoneApp1. app»

Cette erreur est le résultat d’une modification apportée au processus de vérification App Store d’Apple pour empêcher les utilisateurs d’atteindre des problèmes tels que le [bogue 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Cette erreur spécifique n’est _pas_ liée à la version particulière de Xamarin que vous avez installée. par conséquent, la rétrogradation n’est _pas_ utile.

Consultez la discussion du forum [ici](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) pour obtenir des informations de contournement et les dernières mises à jour pour ce problème.
