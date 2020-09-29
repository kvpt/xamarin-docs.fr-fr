---
title: Dépannage des applications tvOS 10 générées avec Xamarin
description: Cet article fournit plusieurs conseils de dépannage pour l’utilisation de tvOS 10 dans les applications Xamarin. Il décrit les problèmes liés à l’App Store, la compatibilité binaire, CFNetwork HttpProtocol, CloudKit, Core image, NSUserActivity et UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 65a76c0196b79a17f935f59902c8e6d2f9f25933
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434913"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Dépannage des applications tvOS 10 générées avec Xamarin

Les sections suivantes répertorient certains problèmes connus qui peuvent se produire lors de l’utilisation de tvOS 10 avec Xamarin et la solution à ces problèmes :

- [App Store](#App-Store)
- [Compatibilité binaire](#Binary-Compatibility)
- [Protocole HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Image principale](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

Problèmes connus :

- Lors du test des achats dans l’application dans l’environnement du bac à sable (sandbox), la boîte de dialogue d’authentification peut apparaître deux fois.
- Lorsque vous testez des achats dans l’application avec du contenu hébergé dans l’environnement sandbox, la boîte de dialogue de mot de passe s’affiche chaque fois que l’application est mise au premier plan jusqu’à ce que le téléchargement du contenu se termine.

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>Compatibilité binaire

Problèmes connus :

- `NSObject.ValueForKey`L’appel de la `null` clé entraîne une exception.
- Le fait de référencer une police par son nom lors de l’appel `UIFont.WithName` de provoque un blocage.
- `NSURLSession`Et `NSURLConnection` ne sont plus des suites de chiffrement RC4 pendant la négociation TLS pour les `http://` URL.
- Les applications peuvent se bloquer si elles modifient la géométrie d’un SuperView dans les `ViewWillLayoutSubviews` `LayoutSubviews` méthodes ou.
- Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est maintenant désactivé par défaut. En outre, l’API de transport sécurisé ne prend plus en charge SSLv3 et il est recommandé que l’application cesse d’utiliser le chiffrement SHA-1 et 3DES le plus rapidement possible.

<a name="CFNetwork-HTTP-Protocol"></a>

## <a name="cfnetwork-http-protocol"></a>Protocole HTTP CFNetwork

La `HTTPBodyStream` propriété de la `NSMutableURLRequest` classe doit être définie sur un flux non ouvert, car `NSURLConnection` et `NSURLSession` s’appliquent maintenant strictement à cette exigence.

<a name="CloudKit"></a>

## <a name="cloudkit"></a>CloudKit

Les opérations de longue durée renverront un _« vous n’êtes pas autorisé à enregistrer le fichier »._ erreur.

<a name="CoreImage"></a>

## <a name="core-image"></a>Image principale

L' `CIImageProcessor` API prend maintenant en charge un nombre arbitraire d’images d’entrée. `CIImageProcessor` L’API qui était incluse dans tvOS 10 bêta 1 sera supprimée.

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

Après une opération de remise, la `UserInfo` propriété d’un `NSUserActivity` objet peut être vide. Appelez explicitement `BecomeCurrent` NSUserActivity’Object en tant que solution de contournement actuelle.

<a name="UIKit"></a>

## <a name="uikit"></a>UIKit

Problèmes connus :

- Les modifications apportées à l’arrière-plan de `UINavigationBar` `UITabBar` ou `UIToolBar` peuvent entraîner une passe de disposition pour résoudre la nouvelle apparence. Toute tentative de modification de ces apparences à l’intérieur d’un `LayoutSubviews` `UpdateConstraints` `WillLayoutSubviews` événement, ou `DidUpdateSubviews` peut entraîner une boucle de disposition infinie.
- Dans tvOS 10, l’appel `RemoveGestureRecognizer` de la méthode d’un `UIView` objet annule explicitement tout module de reconnaissance de mouvement en cours.
- Les contrôleurs de vue présentés peuvent désormais affecter l’apparence de la barre d’État.
- tvOS 10 oblige le développeur à appeler `base.AwakeFromNib` lors de la sous-classe `UIViewController` et de la substitution de la `AwakeFromNib` méthode.
- Les applications avec des sous-classes personnalisées `UIView` qui remplacent et modifient `LayoutSubviews` la disposition avant d’appeler `base.LayoutSubviews` peuvent déclencher une boucle de disposition infinie dans tvOS 10.
- Les ressources d’images spécifiques à une direction ou retournées ne sont pas retournées lorsqu’elles sont assignées aux `UIButton` objets.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [Nouveautés de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)