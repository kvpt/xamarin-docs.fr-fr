---
title: Xamarin.Mac - macOS Sierra résolution des problèmes
description: Ce document fournit plusieurs conseils de dépannage pour travailler avec macOS Sierra dans les applications Xamarin.Mac. Conseils concernent le Store d’applications Mac, Apple Pay, compatibilité binaire, CFNetwork, CloudKit et bien plus encore.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 322acff3279d0513266c7d9883726cac726334f7
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830552"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac - macOS Sierra résolution des problèmes

_Cet article fournit plusieurs conseils de dépannage pour travailler avec macOS Sierra dans les applications Xamarin.Mac._

Les sections suivantes répertorient certains problèmes connus qui peuvent se produire lorsque vous utilisez macOS Sierra avec Xamarin.mac et la solution à ces problèmes :

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilité binaire](#Binary-Compatibility)
- [Protocole HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Image de base](#CoreImage)
- [Notifications](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problèmes connus :

- Lorsque vous testez les achats dans l’application dans l’environnement de bac à sable, la boîte de dialogue d’authentification peut-être apparaître deux fois.
- Lorsque vous testez les achats dans l’application avec le contenu hébergé dans l’environnement de bac à sable, la boîte de dialogue de mot de passe s’affiche chaque fois que l’application est proposée au premier plan jusqu'à ce que le téléchargement du contenu se termine.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Si un code sécurité ou de la date d’expiration incorrecte (PA) est entré lors de l’ajout d’une nouvelle carte de paiement pour Apple Pay, la processus de configuration de carte va être interrompue.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilité binaire

Problèmes connus :

- Appel `NSObject.ValueForKey` sera un `null` clé entraîne une exception.
- Les deux `NSURLSession` et `NSURLConnection` n’est plus les suites de chiffrement RC4 pendant la négociation TLS pour `http://` URL.
- Applications peuvent se bloquer lorsqu’ils modifient la géométrie d’un superview dans un le `ViewWillLayoutSubviews` ou `LayoutSubviews` méthodes.
- Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est désormais désactivé par défaut. En outre, l’API de Transport sécurisé n’est plus prend en charge que les SSLv3 et il est recommandé que l’application s’arrêter à l’aide d’un chiffrement SHA-1 et 3DES dès que possible.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocole HTTP CFNetwork

Le `HTTPBodyStream` propriété de la `NSMutableURLRequest` classe doit être définie sur un flux non ouvert depuis `NSURLConnection` et `NSURLSession` applique désormais strictement cette exigence.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Opérations de longue retournera un _« Vous n’êtes pas autorisé à enregistrer le fichier. »_ Erreur.

<a name="CoreImage" />

## <a name="core-image"></a>Image de base

Le `CIImageProcessor` API prend désormais en charge un nombre arbitraire d’image d’entrée. `CIImageProcessor` API qui a été inclus dans macOS version bêta 1 de Sierra sera supprimée.

<a name="Notifications" />

## <a name="notifications"></a>Notifications

Lorsque vous travaillez avec des Extensions de contenu de Notification, contrôleurs d’affichage n’ont pas été libérés correctement et peut entraîner un incident lorsque les limites de mémoire d’Extension sont atteintes.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Après une opération de transfert, le `UserInfo` propriété d’un `NSUserActivity` objet peut être vide. Appeler explicitement `BecomeCurrent` `NSUserActivity` objet sous la forme d’une solution de contournement actuelle.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation nécessite un sécurisé (`https://`) URL pour travailler sur iOS 10 et macOS Sierra pour empêcher toute utilisation malveillante des données de localisation.







## <a name="related-links"></a>Liens associés

- [Exemples Mac](https://developer.xamarin.com/samples/mac/)
- [Quelles sont les nouveautés dans OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
