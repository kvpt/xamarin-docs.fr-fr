---
title: Dépannage des applications tvOS 10 générées avec Xamarin
description: Cet article fournit plusieurs conseils de dépannage pour l’utilisation de tvOS 10 dans les applications Xamarin. Il décrit les problèmes liés à l’App Store, la compatibilité binaire, CFNetwork HttpProtocol, CloudKit, Core image, NSUserActivity et UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 54a4bf2a6f575a55ce9dde8ec87c93e0d56acf9c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657410"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Dépannage des applications tvOS 10 générées avec Xamarin

Les sections suivantes répertorient certains problèmes connus qui peuvent se produire lors de l’utilisation de tvOS 10 avec Xamarin et la solution à ces problèmes:

- [App Store](#App-Store)
- [Compatibilité binaire](#Binary-Compatibility)
- [Protocole HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Image principale](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problèmes connus :

- Lors du test des achats dans l’application dans l’environnement du bac à sable (sandbox), la boîte de dialogue d’authentification peut apparaître deux fois.
- Lorsque vous testez des achats dans l’application avec du contenu hébergé dans l’environnement sandbox, la boîte de dialogue de mot de passe s’affiche chaque fois que l’application est mise au premier plan jusqu’à ce que le téléchargement du contenu se termine.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilité binaire

Problèmes connus :

- L' `NSObject.ValueForKey` appel de `null` la clé entraîne une exception.
- Le fait de référencer une police `UIFont.WithName` par son nom lors de l’appel de provoque un blocage.
- Et ne sont plus des suites de chiffrement RC4 pendant la négociation `http://` TLS pour les URL. `NSURLConnection` `NSURLSession`
- Les applications peuvent se bloquer si elles modifient la géométrie d’un SuperView `LayoutSubviews` dans les `ViewWillLayoutSubviews` méthodes ou.
- Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est maintenant désactivé par défaut. En outre, l’API de transport sécurisé ne prend plus en charge SSLv3 et il est recommandé que l’application cesse d’utiliser le chiffrement SHA-1 et 3DES le plus rapidement possible.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocole HTTP CFNetwork

La `HTTPBodyStream` propriété de la `NSMutableURLRequest` classe doit être définie sur un flux non ouvert, car `NSURLConnection` et `NSURLSession` s’appliquent maintenant strictement à cette exigence.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Les opérations de longue durée renverront un _«vous n’êtes pas autorisé à enregistrer le fichier»._ Erreurs.

<a name="CoreImage" />

## <a name="core-image"></a>Image principale

L' `CIImageProcessor` API prend maintenant en charge un nombre arbitraire d’images d’entrée. `CIImageProcessor`L’API qui était incluse dans tvOS 10 bêta 1 sera supprimée.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Après une opération de remise, `UserInfo` la propriété d' `NSUserActivity` un objet peut être vide. Appelez `BecomeCurrent` explicitement NSUserActivity’Object en tant que solution de contournement actuelle.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problèmes connus :

- Les modifications apportées à `UINavigationBar`l' `UITabBar` arrière `UIToolBar` -plan de ou peuvent entraîner une passe de disposition pour résoudre la nouvelle apparence. Toute tentative de modification de ces apparences à `LayoutSubviews`l’intérieur `WillLayoutSubviews` d' `DidUpdateSubviews` un événement, `UpdateConstraints`ou peut entraîner une boucle de disposition infinie.
- Dans tvOS 10, l’appel `RemoveGestureRecognizer` de la méthode `UIView` d’un objet annule explicitement tout module de reconnaissance de mouvement en cours.
- Les contrôleurs de vue présentés peuvent désormais affecter l’apparence de la barre d’État.
- tvOS 10 oblige le développeur à appeler `base.AwakeFromNib` lors de la `UIViewController` sous-classe et de `AwakeFromNib` la substitution de la méthode.
- Les applications avec `UIView` des sous-classes personnalisées `LayoutSubviews` qui remplacent et modifient `base.LayoutSubviews` la disposition avant d’appeler peuvent déclencher une boucle de disposition infinie dans tvOS 10.
- Les ressources d’images spécifiques à une direction ou retournées ne sont pas `UIButton` retournées lorsqu’elles sont assignées aux objets.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Nouveautés de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
