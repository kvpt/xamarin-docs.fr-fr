---
title: Xamarin. Mac-résolution des problèmes macOS Sierra
description: Ce document fournit plusieurs conseils de dépannage pour l’utilisation de macOS Sierra dans les applications Xamarin. Mac. Les conseils se rapportent à l’App Store Mac, Apple Pay, la compatibilité binaire, CFNetwork, CloudKit et bien plus encore.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: a4e7f7169e4c7ec0ec2947e17b1434179f47488f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017037"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin. Mac-résolution des problèmes macOS Sierra

_Cet article fournit plusieurs conseils pour la résolution des problèmes liés à l’utilisation de macOS Sierra dans les applications Xamarin. Mac._

Les sections suivantes répertorient certains problèmes connus qui peuvent se produire lors de l’utilisation de macOS Sierra avec Xamarin. Mac et la solution à ces problèmes :

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilité binaire](#Binary-Compatibility)
- [Protocole HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Image principale](#CoreImage)
- [Notifications](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problèmes connus :

- Lors du test des achats dans l’application dans l’environnement du bac à sable (sandbox), la boîte de dialogue d’authentification peut apparaître deux fois.
- Lorsque vous testez des achats dans l’application avec du contenu hébergé dans l’environnement sandbox, la boîte de dialogue de mot de passe s’affiche chaque fois que l’application est mise au premier plan jusqu’à ce que le téléchargement du contenu se termine.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Si une date d’expiration ou un code de sécurité incorrect (CW) est entré lors de l’ajout d’une nouvelle carte de paiement à Apple Pay, le processus de configuration de la carte est terminé.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilité binaire

Problèmes connus :

- L’appel de `NSObject.ValueForKey` est une clé de `null` entraîne une exception.
- Les `NSURLSession` et `NSURLConnection` plus de suites de chiffrement RC4 pendant la négociation TLS pour les URL de `http://`.
- Les applications peuvent se bloquer si elles modifient la géométrie d’un SuperView dans les méthodes `ViewWillLayoutSubviews` ou `LayoutSubviews`.
- Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est maintenant désactivé par défaut. En outre, l’API de transport sécurisé ne prend plus en charge SSLv3 et il est recommandé que l’application cesse d’utiliser le chiffrement SHA-1 et 3DES le plus rapidement possible.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocole HTTP CFNetwork

La propriété `HTTPBodyStream` de la classe `NSMutableURLRequest` doit être définie sur un flux non ouvert, car `NSURLConnection` et `NSURLSession` appliquent désormais strictement cette exigence.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Les opérations de longue durée renverront un _« vous n’êtes pas autorisé à enregistrer le fichier »._ Erreurs.

<a name="CoreImage" />

## <a name="core-image"></a>Image principale

L’API `CIImageProcessor` prend désormais en charge un nombre arbitraire d’images d’entrée. `CIImageProcessor` API qui était incluse dans macOS Sierra bêta 1 sera supprimée.

<a name="Notifications" />

## <a name="notifications"></a>Notifications

Lorsque vous utilisez des extensions de contenu de notification, les contrôleurs d’affichage ne sont pas correctement libérés et peuvent provoquer un blocage lorsque les limites de mémoire d’extension sont atteintes.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Après une opération de remise, la propriété `UserInfo` d’un objet `NSUserActivity` peut être vide. Appelez explicitement `BecomeCurrent` objet `NSUserActivity` en tant que solution de contournement actuelle.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation requiert une URL sécurisée (`https://`) pour travailler à la fois sur iOS 10 et macOS Sierra afin d’empêcher toute utilisation malveillante des données de localisation.

## <a name="related-links"></a>Liens associés

- [Exemples Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Nouveautés de macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
