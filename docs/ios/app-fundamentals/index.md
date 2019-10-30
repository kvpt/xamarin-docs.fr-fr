---
title: Notions de base de l’application Xamarin. iOS
description: Ce document contient des liens vers divers guides qui décrivent les concepts fondamentaux du développement Xamarin. iOS, tels que la sécurité du transport de l’application, l’arrière-plan, les événements et le Threading.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/21/2017
ms.openlocfilehash: 0ccdde29183645b93831b7261909714f9baf3fa4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010026"
---
# <a name="xamarinios-application-fundamentals"></a>Notions de base de l’application Xamarin. iOS

Cette section fournit un guide sur les tâches ou concepts les plus courants que les développeurs doivent connaître lors du développement d’applications Xamarin. iOS (anciennement monotactiles).

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Accessibilité](~/ios/app-fundamentals/accessibility.md)

Ce document décrit les différents outils et API qui peuvent être utilisés pour créer des applications qui sont accessibles à autant d’utilisateurs que possible.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Sécurité de transport de l’application](~/ios/app-fundamentals/ats.md)

Cet article présente les modifications de sécurité apportées par la sécurité du transport d’application sur une application iOS 9 et ce que cela signifie pour vos projets Xamarin. iOS, elle couvre les options de configuration de l’ATS et elle explique comment refuser l’ATS, si nécessaire. Dans la mesure où ATS est activé par défaut, toutes les connexions Internet non sécurisées lèvent une exception dans les applications iOS 9 (à moins que vous ne les ayez explicitement autorisées).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)

Le traitement en arrière-plan ou l’arrière-plan est le processus qui permet aux applications d’effectuer des tâches en arrière-plan pendant qu’une autre application s’exécute au premier plan. Ce guide constitue une introduction au traitement en arrière-plan dans iOS.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Création d’applications iOS dans du code](~/ios/app-fundamentals/ios-code-only.md)

Cet article explique comment créer des applications iOS entièrement dans du code à l’aide de Visual Studio et Visual Studio pour Mac. Il montre comment démarrer à partir d’un modèle de projet vide pour créer un écran d’application dans un contrôleur en créant une hiérarchie de vues à partir de UIKit. Ensuite, il explique comment créer des vues personnalisées qui peuvent être chargées dans un contrôleur.

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[Regroupement d’exceptions](~/ios/platform/exception-marshaling.md)

Décrit comment objective-C et les exceptions managées sont marshalés entre des frames natifs et managés.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Événements, protocoles et délégués](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Cet article présente les principales technologies iOS utilisées pour recevoir des rappels et remplir les contrôles d’interface utilisateur avec des données. Ces technologies sont les événements, les protocoles et les délégués. Cet article explique ce que sont les éléments et comment ils sont utilisés à C#partir de. Il montre comment Xamarin. iOS utilise les contrôles iOS pour exposer des événements .NET familiers, ainsi que la façon dont Xamarin. iOS prend en charge les concepts objective-C, tels que les protocoles et les délégués ( C# les délégués objective-c ne doivent pas être confondus avec les délégués). Cet article fournit également des exemples qui montrent comment les protocoles sont utilisés à la fois comme base pour les délégués objective-C et dans les scénarios non délégués.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Utilisation du système de fichiers](~/ios/app-fundamentals/file-system.md)

Xamarin. iOS peut utiliser les mêmes classes System.IO pour travailler avec des fichiers et répertoires dans iOS que vous utiliseriez dans n’importe quelle application .NET. Toutefois, en dépit des classes et méthodes familières, iOS implémente certaines restrictions sur les fichiers qui peuvent être créés ou accessibles et fournit également des fonctionnalités spéciales pour certains répertoires. Cet article décrit ces restrictions et fonctionnalités et montre comment fonctionne l’accès aux fichiers dans une application Xamarin. iOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Utilisation des images](~/ios/app-fundamentals/images-icons/index.md)

Cet article explique comment utiliser des images dans Xamarin. iOS, les deux images de prise en charge des applications (telles que les icônes, le chargement d’images, etc.) et les images dans les applications (telles que les images appliquées aux contrôles). Elle explique également comment utiliser Visual Studio pour Mac pour incorporer des images et comment interagir avec les images à partir du code.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localisation](~/ios/app-fundamentals/localization/index.md)

Ce guide couvre l’ajout d’encodages à une application Xamarin. iOS pour prendre en charge l’internationalisation.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Utilisation des listes de propriétés](~/ios/app-fundamentals/index.md)

Ce document présente l’éditeur de liste de propriétés (. plist) graphique et avancé de Visual Studio pour Mac pour l’utilisation d’info. plist et de conversions. plist. Il illustre la définition des icônes et des images de lancement pour l’application iOS, et montre comment spécifier des fonctionnalités d’application (droits) à partir de Visual Studio pour Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Utilisation de la sécurité et de la confidentialité](~/ios/app-fundamentals/security-privacy.md)

Apple a apporté plusieurs améliorations à la sécurité et à la confidentialité dans iOS 10 (et versions ultérieures), ce qui permet au développeur d’améliorer la sécurité de ses applications et de garantir la confidentialité de l’utilisateur final. Cet article aborde l’implémentation de ces fonctionnalités dans une application Xamarin. iOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Thread](~/ios/app-fundamentals/threading.md)

Cet article décrit le Threading dans une application Xamarin. iOS et discute d’un peu plus sur le pool de threads .NET, les applications réactives et les garbage collection.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Touch](~/ios/app-fundamentals/touch/index.md)

Les écrans tactiles sur la plupart des appareils actuels permettent aux utilisateurs d’interagir rapidement et efficacement avec les appareils de manière naturelle et intuitive. Cette interaction n’est pas limitée uniquement à la détection tactile simple : il est également possible d’utiliser des mouvements. Par exemple, le geste de pincement de zoom est un exemple très courant : en pinceant une partie de l’écran avec deux doigts, l’utilisateur peut effectuer un zoom avant ou arrière. Ce guide examine les entrées tactiles et les gestes dans iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Utilisation des valeurs par défaut de l’utilisateur](~/ios/app-fundamentals/user-defaults.md)

La classe `NSUserDefaults` offre aux applications et extensions iOS un moyen d’interagir par programmation avec le système par défaut à l’ensemble du système. En utilisant le système par défaut, l’utilisateur peut configurer le comportement ou le style d’une application pour qu’elle réponde à ses préférences (en fonction de la conception de l’application). Par exemple, pour présenter des données dans les mesures métriques vs impériales ou sélectionner un thème d’interface utilisateur donné.
