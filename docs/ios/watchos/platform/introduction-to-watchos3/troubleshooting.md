---
title: Dépannage de Watchos 3
description: Ce document fournit plusieurs conseils de dépannage utiles lors de l’utilisation de Watchos 3 dans Xamarin. Les conseils se rapportent aux activités, Apple Pay, actualisation en arrière-plan, NSURLConnection, confidentialité et bien plus encore.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 093ac4a3242866413042de0b650433d4369ad35f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028255"
---
# <a name="watchos-3-troubleshooting"></a>Dépannage de Watchos 3

_Cet article fournit plusieurs conseils de dépannage pour l’utilisation de Watchos 3 dans Xamarin Apple Watch apps._

Cette page répertorie certains problèmes connus qui peuvent se produire lors de l’utilisation de Watchos 3 avec Xamarin et de la solution à ces problèmes.

## <a name="activities"></a>Activités

Pour que le partage d’activité fonctionne correctement, toutes les Apple Watch associées doivent exécuter Watchos 3.

Problèmes connus :

- La réponse à une notification de partage d’activité échoue parfois.
- La réponse à une notification de partage d’activité avec un message peut échouer.
- Le texte contextuel au-dessus d’un message de notification de partage d’activité est incorrect.

## <a name="apple-pay"></a>Apple Pay

Problèmes connus :

- Si une date d’expiration ou un code en PV incorrect est entré pour un nouveau service de paiement dans Apple Pay, lorsque vous appuyez sur **suivant** , le processus en cours d’exécution se bloquera.
- Les achats dans l’application Apple Pay nécessitant un numéro de code confidentiel peuvent se bloquer.

## <a name="auto-mac-unlock"></a>Déverrouillage auto Mac

En utilisant Watchos 3 bêta 2 (ou version ultérieure) et macOS Sierra bêta 2 (ou version ultérieure), si l’authentification à deux facteurs est activée sur le compte iCloud de l’utilisateur, elle peut utiliser son Apple Watch pour déverrouiller automatiquement son Mac.

## <a name="background-refresh"></a>Actualisation en arrière-plan

La violation des ressources système entraîne un blocage de l’application Watchos 3 avec les codes d’exception suivants :

- **0xc51bad01** : l’application a consommé trop de temps processeur.
- **0xc51bad02** : l’application a consommé trop de temps.
- **0xc51bad03** : l’application n’a pas suffisamment d’exécution pour terminer la tâche en cours.

## <a name="clock"></a>Clock

Les complications des applications de Apple Watch récemment installées peuvent apparaître comme vides. Redémarrez Apple Watch pour résoudre ce problème.

## <a name="connectivity"></a>Connectivité

Problèmes connus :

- Watchos ne demande pas à l’utilisateur l’autorisation d’accès pour les données utilisateur protégées sur le Apple Watch. Accordez l’accès à l’application iPhone avant d’utiliser les données de l’application Watch.
- Le Apple Watch pouvez entrer un État dans lequel toutes les transmissions WatchConnectivity échouent, redémarrez le Apple Watch à corriger.

## <a name="notifications"></a>Notifications

Si une pièce jointe de média est trop grande, elle est présentée sur l’iPhone de l’utilisateur, mais pas sur son Apple Watch.

## <a name="nsurlconnection"></a>NSURLConnection

Toute connexion `NSURLConnection` utilisant des protocoles TLS anciens échouera. Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est maintenant désactivé par défaut. En outre, l’API de transport sécurisé ne prend plus en charge SSLv3 et il est recommandé que l’application cesse d’utiliser le chiffrement SHA-1 et 3DES le plus rapidement possible.

À partir de Watchos 3, la sécurité des connexions SSL/TLS est strictement appliquée par Apple. Les services et applications affectés doivent mettre à jour les serveurs Web pour utiliser les versions de protocole TLS les plus récentes.

## <a name="nsurlsession"></a>Passer

À partir de Watchos 3, la propriété `HTTPBodyStream` de la classe `NSMutableURLRequest` doit être définie sur un flux non ouvert, car `NSURLConnection` et `NSURLSession` appliquent désormais strictement cette exigence.

## <a name="privacy"></a>Confidentialité

Problèmes connus :

Lorsque vous travaillez avec des URL `https://` `NSURLSession` et `NSURLConnection` ne prennent plus en charge les suites de chiffrement RC4 pendant la négociation TLS. L’un des codes d’erreur suivants peut être généré :

- **-1200 ou-98** -pour les erreurs de `NSURLErrorSecurityConnectionFailed` et SecureTransport.
- **-1200 [3 :-9824]** -échec du chargement http.
- **-1200** - `NSURLConnection` s’est terminé avec une erreur.

À partir de Watchos 3, la sécurité des connexions SSL/TLS est strictement appliquée par Apple. Les services et applications affectés doivent mettre à jour les serveurs Web pour utiliser les versions de protocole TLS les plus récentes. Pour plus d’informations, consultez [NSURLConnection](#nsurlconnection) ci-dessus.

## <a name="snapshots"></a>Snapshots

Les applications WatchKit qui n’ont pas adopté le nouvel `HandelBackgroundTask` API ne recevront plus de mises à jour périodiques dans Watchos 3. 

## <a name="watchkit"></a>WatchKit

Les scènes SpriteKit et SceneKit sont suspendues quand une application entre l’arrière-plan dans l’ancrage Watchos.

## <a name="related-links"></a>Liens associés

- [Exemples watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Nouveautés de Watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
