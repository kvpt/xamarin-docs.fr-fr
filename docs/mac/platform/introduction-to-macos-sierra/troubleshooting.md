---
title: Xamarin. Mac-résolution des problèmes macOS Sierra
description: Ce document fournit plusieurs conseils de dépannage pour l’utilisation de macOS Sierra dans les applications Xamarin. Mac. Les conseils se rapportent à l’App Store Mac, Apple Pay, la compatibilité binaire, CFNetwork, CloudKit et bien plus encore.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 09/22/2016
ms.openlocfilehash: 51276a7682599c6480c637fac385992feaf06e49
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278893"
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

- L' `NSObject.ValueForKey` appel de `null` la clé entraîne une exception.
- Et ne sont plus des suites de chiffrement RC4 pendant la négociation `http://` TLS pour les URL. `NSURLConnection` `NSURLSession`
- Les applications peuvent se bloquer si elles modifient la géométrie d’un SuperView `LayoutSubviews` dans les `ViewWillLayoutSubviews` méthodes ou.
- Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est maintenant désactivé par défaut. En outre, l’API de transport sécurisé ne prend plus en charge SSLv3 et il est recommandé que l’application cesse d’utiliser le chiffrement SHA-1 et 3DES le plus rapidement possible.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocole HTTP CFNetwork

La `HTTPBodyStream` propriété de la `NSMutableURLRequest` classe doit être définie sur un flux non ouvert, car `NSURLConnection` et `NSURLSession` s’appliquent maintenant strictement à cette exigence.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Les opérations de longue durée renverront un _« vous n’êtes pas autorisé à enregistrer le fichier »._ Erreurs.

<a name="CoreImage" />

## <a name="core-image"></a>Image principale

L' `CIImageProcessor` API prend maintenant en charge un nombre arbitraire d’images d’entrée. `CIImageProcessor`L’API qui a été incluse dans macOS Sierra bêta 1 sera supprimée.

<a name="Notifications" />

## <a name="notifications"></a>Notifications

Lorsque vous utilisez des extensions de contenu de notification, les contrôleurs d’affichage ne sont pas correctement libérés et peuvent provoquer un blocage lorsque les limites de mémoire d’extension sont atteintes.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Après une opération de remise, `UserInfo` la propriété d' `NSUserActivity` un objet peut être vide. `BecomeCurrent` Appelez`NSUserActivity` explicitement Object en tant que solution de contournement actuelle.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation requiert l’utilisation d'`https://`une URL sécurisée () sur iOS 10 et MacOS Sierra pour empêcher l’utilisation malveillante des données de localisation.

## <a name="related-links"></a>Liens associés

- [Exemples Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Nouveautés de macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
