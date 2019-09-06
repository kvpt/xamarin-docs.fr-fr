---
title: Introduction à iOS 12
description: Ce document fournit une description de haut niveau de certaines API iOS 12 pour lesquelles la version préliminaire de Xamarin C# fournit des liaisons.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/08/2018
ms.openlocfilehash: b3f3db32e87d83ea4e076d439df3342e5ca2ed50
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284637"
---
# <a name="introduction-to-ios-12"></a>Introduction à iOS 12

Ce document fournit une description de haut niveau de certaines API iOS 12 pour lesquelles la version préliminaire de Xamarin C# fournit des liaisons.

Pour commencer à créer des applications iOS 12 avec Xamarin, consultez le [Guide de mise](get-started.md) en route

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit est l’infrastructure de réalité augmentée incluse avec iOS. ARKit 2 permet à plusieurs utilisateurs d’interagir entre eux dans une scène de réalité augmentée, permet de conserver des objets dans l’espace et de les retourner ultérieurement, et fournit la reconnaissance d’image 2D et le suivi et la reconnaissance d’objets 3D. iOS 12 fournit également un aperçu rapide de AR, un moyen de rendre les modèles usdz AR dans vos applications.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Siri les raccourcis](siri-shortcuts.md)

Les raccourcis Siri permettent aux développeurs d’intégrer plus profondément leurs applications avec Siri. Avec les raccourcis Siri, les utilisateurs peuvent utiliser des commandes vocales pour ouvrir du contenu ou lancer des tâches en arrière-plan, ou ils peuvent lancer ces mêmes tâches via des raccourcis que Siri suggère sur l’écran de verrouillage.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 réduit la taille des applications par le biais de la quantification de modèle et des modèles flexibles, améliore les performances des applications avec une nouvelle API de prédiction par lot et utilise des modèles personnalisés pour prendre en charge les avancées dans Machine Learning.

## <a name="notification-improvementsnotificationsindexmd"></a>[Améliorations des notifications](notifications/index.md)

Dans iOS 12, les notifications groupées permettent de présenter des notifications à l’utilisateur dans des regroupements liés à des applications ou des threads. Le texte résumé fournit des informations supplémentaires sur un groupe de notification.

Les extensions de contenu de notification dans iOS 12 autorisent les interfaces utilisateur personnalisées et les boutons d’action dynamiques.

## <a name="natural-language-frameworknatural-languagemd"></a>[Infrastructure en langage naturel](natural-language.md)

L’infrastructure de langage naturel permet aux applications d’effectuer différents types d’analyse de langage. Par exemple, il peut identifier des parties de la parole et déterminer la langue représentée par un bloc de texte.

## <a name="vision-frameworkiosplatformintroduction-to-ios11visionmd"></a>[Framework de vision](~/ios/platform/introduction-to-ios11/vision.md)

L’infrastructure de vision comprend un détecteur de visage amélioré qui peut détecter les visages dans différentes orientations. En outre, les révisions de la demande peuvent sélectionner une révision spécifique de l’algorithme de vision.

## <a name="photo-and-video-apis"></a>API photo et vidéo

Dans iOS 12, l’API de segmentation portrait retourne un cache d’effets de portrait : un masque linéaire qui délimite le premier plan de l’arrière-plan d’une image portrait et est utile pour créer différents effets d’image. iOS 12 permet également d’utiliser des données de profondeur de l’appareil photo TrueDepth pour des effets vidéo en temps réel.

## <a name="passwords"></a>Mots de passe

iOS 12 permet aux utilisateurs et aux développeurs de travailler plus facilement avec les mots de passe :

- Le remplissage automatique de mot de passe et les mots de passe forts automatiques permettent de générer, de stocker et d’utiliser automatiquement des mots de passe forts dans les applications iOS lors de l’inscription et de la connexion à une application.
- Grâce à la fonction de remplissage automatique du code de sécurité, il est possible d’utiliser des codes d’authentification SMS sans couper-coller ni Mémorisation manuelle.
- La `ASWebAuthenticationSession` classe simplifie le processus d’utilisation des services d’authentification fédérée.
- Les extensions de fournisseur d’informations d’identification de remplissage automatique permettent aux applications de mots de passe tierces de fournir un nom d’utilisateur et des mots de passe aux champs de connexion.

## <a name="healthkit-updates"></a>Mises à jour HealthKit

iOS 11,3 a introduit des [enregistrements d’intégrité](https://www.apple.com/healthcare/health-records/), qui permettent aux utilisateurs de télécharger leurs informations d’enregistrement d’intégrité à partir de divers établissements de santé et de les afficher sur leurs appareils iOS. iOS 12 ajoute des API qui permettent aux applications tierces d’accéder en toute sécurité à ces données.

## <a name="imessage-app-presentation-contexts"></a>contextes de présentation d’application iMessage

Dans iOS 12, les applications iMessage prennent en charge les contextes de présentation, ce qui permet aux applications de s’exécuter comme une application iMessage normale ou dans le contexte d’un effet photo ou vidéo.

## <a name="network-framework"></a>Infrastructure réseau

Network Framework, la pile réseau sous- `URLSession` jacente aux API couramment utilisées dans les applications iOS, est désormais disponible en tant qu’infrastructure autonome, ce qui facilite l’utilisation de TCP, UDP, TLS, IPv4/IPv6 et bien plus encore.

## <a name="carplay"></a>CarPlay

Dans iOS 12, les applications tierces peuvent fournir des cartes et activer les instructions de navigation dans CarPlay à l’aide de la nouvelle infrastructure CarPlay.

## <a name="deprecations"></a>Dépréciations

Avec iOS 12, Apple est dépréciée :

- OpenGL ES, [encourageant les développeurs](https://developer.apple.com/ios/whats-new/) à adopter des métaux.
- [`UIWebView`](xref:UIKit.UIWebView), [en faveur de `WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).
