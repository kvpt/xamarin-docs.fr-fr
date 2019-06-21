---
title: Introduction à iOS 12
description: Ce document fournit une description générale de certaines API iOS 12 pour la version préliminaire de quels Xamarin version fournit des liaisons c#.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/08/2018
ms.openlocfilehash: 5ac19571bc1f1163539a48ea2689c743445d8047
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268865"
---
# <a name="introduction-to-ios-12"></a>Introduction à iOS 12

Ce document fournit une description générale de certaines API iOS 12 pour la version préliminaire de quels Xamarin version fournit des liaisons c#.

Pour commencer à créer des applications iOS 12 avec Xamarin, consultez le [guide Mise en route](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit est l’infrastructure de réalité augmentée inclus avec iOS. ARKit 2 permet à plusieurs utilisateurs d’interagir avec eux dans une scène de réalité augmentée, rend possible conserver des objets dans l’espace et de retourner leur ultérieurement, reconnaissance d’images 2D et fournit la reconnaissance de l’objet suivi et 3D. iOS 12 fournit également de rechercher AR rapide, un moyen pour restituer usdz modèles AR dans vos applications.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Raccourcis de Siri](siri-shortcuts.md)

Raccourcis de Siri permettent aux développeurs d’intégrer plus étroitement leurs applications avec Siri. Avec les raccourcis de Siri, les utilisateurs peuvent utiliser les commandes vocales d’ouvrir du contenu ou de lancer des tâches en arrière-plan, ou peut être lancée ces mêmes tâches via les raccourcis qui suggère de Siri sur l’écran de verrouillage.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 réduit la taille de l’application par le biais de quantification de modèle et de modèles flexibles, améliore les performances des applications avec une nouvelle API de prédiction de lot et utilise des modèles personnalisés pour prendre en charge des progrès dans l’apprentissage.

## <a name="notification-improvementsnotificationsindexmd"></a>[Amélioration de la notification](notifications/index.md)

Dans iOS 12, notifications groupées rendent possible pour envoyer des notifications utilisateur présents dans l’application ou des regroupements liés aux threads. Texte du résumé fournit davantage d’informations sur un groupe de notification.

Extensions de contenu de notification dans iOS 12 permettent des interfaces utilisateur personnalisées et des boutons d’action dynamique.

## <a name="natural-language-frameworknatural-languagemd"></a>[Framework de langage naturel](natural-language.md)

L’infrastructure de langage naturel permet aux applications d’effectuer différents types d’analyse linguistique. Par exemple, il peut identifier les parties du discours et déterminer la langue représentée par un bloc de texte.

## <a name="vision-frameworkiosplatformintroduction-to-ios11visionmd"></a>[Framework de vision](~/ios/platform/introduction-to-ios11/vision.md)

L’infrastructure de Vision inclut un détecteur de visage améliorée qui peut détecter les visages dans différentes orientations. En outre, demander une révision sélectionnables révision algorithme de framework spécifique Vision.

## <a name="photo-and-video-apis"></a>Photos et des API vidéo

Dans iOS 12, la segmentation de portrait API retourne un cache d’effets portrait – un masque linéaire qui délimite le premier plan à partir de l’arrière-plan d’une image en mode portrait et est utile lors de la création des divers effets de l’image. iOS 12 rend également est possible d’utiliser des données de profondeur de la caméra TrueDepth pour les effets de vidéo en temps réel.

## <a name="passwords"></a>Mots de passe

iOS 12 rend plus facile pour les utilisateurs et aux développeurs de travailler avec les mots de passe :

- Le remplissage automatique de mot de passe et les mots de passe forts automatique permettent de générer, stocker et utiliser des mots de passe forts dans les applications iOS lors de l’inscription et la journalisation dans une application automatiquement.
- Le remplissage automatique du Code de sécurité rend possible d’utiliser des codes de l’authentification basée sur SMS sans coupe manuelle et de collage ou de mémorisation des commandes.
- Le `ASWebAuthenticationSession` classe rationalise le processus de l’utilisation des services d’authentification fédérée.
- Extensions de fournisseur d’informations d’identification de remplissage automatique rendent possible pour les applications de mot de passe par des tiers fournir le nom d’utilisateur et mots de passe aux champs de connexion.

## <a name="healthkit-updates"></a>Mises à jour HealthKit

iOS 11.3 introduit [médicaux](https://www.apple.com/healthcare/health-records/), ce qui permet aux utilisateurs de télécharger leur intégrité enregistrer des informations à partir de différents établissements de soins de santé et l’afficher sur leurs appareils iOS. iOS 12 ajoute des API qui permettent à des applications tierces à accéder en toute sécurité ces données.

## <a name="imessage-app-presentation-contexts"></a>iMessage contextes de présentation d’application

Dans iOS 12, iMessage applications prennent en charge les contextes de présentation, qui autorise les applications à exécuter comme application iMessage normal ou dans le contexte d’une photo ou d’effet vidéo.

## <a name="network-framework"></a>Infrastructure de réseau

Infrastructure de réseau, le réseau de la pile sous-jacente la `URLSession` API couramment utilisées dans les applications iOS, est maintenant disponible comme infrastructure autonome, rendant ainsi plus facile de travailler avec TCP, UDP, TLS, IPv4/IPv6 et bien plus encore.

## <a name="carplay"></a>CarPlay

Dans iOS 12, les applications tierces peuvent offrir maps et obtenir des instructions étape par étape navigation dans CarPlay à l’aide de la nouvelle infrastructure CarPlay.

## <a name="deprecations"></a>Dépréciations

Avec iOS 12, Apple a déconseillé :

- OpenGL ES, [encouragent les développeurs](https://developer.apple.com/ios/whats-new/) à adopter complète.
- [`UIWebView`](xref:UIKit.UIWebView), [en faveur de `WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).
