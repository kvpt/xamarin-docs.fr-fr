---
title: Résolution des problèmes de tvOS 10 applications développées avec Xamarin
description: Cet article fournit plusieurs conseils de dépannage pour travailler avec tvOS 10 dans les applications Xamarin. Il décrit les problèmes liés à l’App Store, compatibilité binaire, CFNetwork HttpProtocol, CloudKit, Image Core, NSUserActivity et UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 97cfe7220f8b351ec30a9f2c7a7347d318378fbc
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830489"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Résolution des problèmes de tvOS 10 applications développées avec Xamarin

Les sections suivantes répertorient certains problèmes connus qui peuvent se produire lors de l’utilisation de tvOS 10 avec Xamarin et de la solution à ces problèmes :

- [App Store](#App-Store)
- [Compatibilité binaire](#Binary-Compatibility)
- [Protocole HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Image de base](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problèmes connus :

- Lorsque vous testez les achats dans l’application dans l’environnement de bac à sable, la boîte de dialogue d’authentification peut-être apparaître deux fois.
- Lorsque vous testez les achats dans l’application avec le contenu hébergé dans l’environnement de bac à sable, la boîte de dialogue de mot de passe s’affiche chaque fois que l’application est proposée au premier plan jusqu'à ce que le téléchargement du contenu se termine.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilité binaire

Problèmes connus :

- Appel `NSObject.ValueForKey` sera un `null` clé entraîne une exception.
- Référencement d’une police par nom lors de l’appel `UIFont.WithName` entraîne un blocage.
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

Le `CIImageProcessor` API prend désormais en charge un nombre arbitraire d’image d’entrée. `CIImageProcessor` API qui a été inclus dans la version bêta 1 de tvOS 10 sera supprimée.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Après une opération de transfert, le `UserInfo` propriété d’un `NSUserActivity` objet peut être vide. Appeler explicitement `BecomeCurrent` NSUserActivity' objet sous la forme d’une solution de contournement actuelle.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problèmes connus :

- Modifie l’apparence de l’arrière-plan du `UINavigationBar`, `UITabBar` ou `UIToolBar` peut entraîner une passe de disposition pour résoudre la nouvelle apparence. Tente de modifier ces apparences à l’intérieur d’un `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` ou `DidUpdateSubviews` événement peut entraîner une boucle infinie de disposition.
- Dans l’appel de tvOS 10, le `RemoveGestureRecognizer` méthode d’un `UIView` objet annule explicitement n’importe quel module de reconnaissance de mouvement en cours d’exécution.
- Contrôleurs d’affichage présenté peut affecter maintenant l’apparence de la barre d’état.
- tvOS 10 nécessite le développeur d’appeler `base.AwakeFromNib` lors du sous-classement `UIViewController` et en remplaçant le `AwakeFromNib` (méthode).
- Applications avec personnalisé `UIView` sous-classes qui substituent `LayoutSubviews` et simplement la mise en page avant d’appeler `base.LayoutSubviews` peut déclencher une boucle infinie de disposition dans tvOS 10.
- Ressources d’images spécifiques à la direction ou PLIABLE ne sont aucun retournement lorsque affectés à `UIButton` objets.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Quelles sont les nouveautés dans tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
