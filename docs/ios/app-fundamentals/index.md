---
title: Principes de base application Xamarin.iOS
description: Ce document contient des liens aux guides qui décrivent les concepts fondamentaux du développement Xamarin.iOS, telles que la sécurité de transport d’application, backgrounding, événements et le threading.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/21/2017
ms.openlocfilehash: 120bbfe0d5fa91e632fc56ee05431f5555653360
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268591"
---
# <a name="xamarinios-application-fundamentals"></a>Principes de base application Xamarin.iOS

Cette section fournit un guide sur certains des plus courants des tâches de choses ou concepts que les développeurs doivent connaître lors du développement d’applications Xamarin.iOS (anciennement MonoTouch).

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Accessibilité](~/ios/app-fundamentals/accessibility.md)

Ce document décrit les différentes API et outils qui peuvent être utilisés pour aider à créer des applications qui sont accessibles aux utilisateurs autant que possible.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Sécurité de transport de l’application](~/ios/app-fundamentals/ats.md)

Cet article vous présente les modifications de sécurité qui applique la sécurité de Transport d’application sur une application iOS 9 et ce que cela signifie pour vos projets Xamarin.iOS, il traitera les options de configuration ATS et il nous allons étudier comment adhérer de ATS, si nécessaire. ATS étant activé par défaut, toutes les connexions internet non sécurisée lèvent une exception dans les applications iOS 9 (sauf si vous avez explicitement autorisé).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)

Arrière-plan de traitement ou backgrounding consiste à laisser les applications à effectuer des tâches en arrière-plan pendant qu’une autre application est en cours d’exécution au premier plan. Ce guide constitue une introduction à iOS de traitement en arrière-plan.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Création d’applications iOS dans le code](~/ios/app-fundamentals/ios-code-only.md)

Cet article examine comment créer des applications iOS entièrement dans le code à l’aide de Visual Studio et Visual Studio pour Mac. Il montre comment démarrer à partir d’un modèle de projet vide pour créer un écran de l’application dans un contrôleur en créant une hiérarchie d’affichages à partir de UIKit. Ensuite, il explique comment créer des vues personnalisées qui peuvent être chargées dans un contrôleur.

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[Regroupement d’exceptions](~/ios/platform/exception-marshaling.md)

Décrit comment Objective-C et les exceptions managées sont marshalées entre les frames natifs et managés.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Événements, protocoles et délégués](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Cet article présente les technologies clés iOS utilisées pour recevoir des rappels et pour remplir des contrôles d’interface utilisateur avec des données. Ces technologies sont les événements, protocoles et délégués. Cet article explique ce que chacune d’elles est et comment chacun est utilisé à partir de c#. Il montre comment Xamarin.iOS utilise les contrôles d’iOS pour exposer des événements de .NET familières, ainsi que la manière dont Xamarin.iOS prend en charge les concepts de Objective-C, tels que les protocoles et les délégués (délégués d’Objective-C ne devraient pas être confondus avec les délégués c#). Cet article fournit également des exemples qui indiquent comment protocoles sont utilisés à la fois comme base pour les délégués d’Objective-C et dans les scénarios non délégué.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Utilisation avec le système de fichiers](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS peut utiliser les mêmes classes System.IO pour travailler avec des fichiers et des répertoires dans iOS que vous utiliseriez dans n’importe quelle application .NET. Toutefois, malgré les classes familiers et les méthodes, iOS implémente certaines restrictions sur les fichiers qui peuvent être créées ou accédées et fournit des fonctionnalités spéciales pour certains répertoires. Cet article décrit ces restrictions et les fonctionnalités et illustre le fonctionnement de l’accès aux fichiers dans une application Xamarin.iOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Utilisation d’images](~/ios/app-fundamentals/images-icons/index.md)

Cet article examine comment utiliser des images dans Xamarin.iOS, des images de prise en charge des applications (telles que des icônes, le chargement des images, etc.) et images dans les applications (telles que des images appliqués aux contrôles). Il couvre également l’utilisation de Visual Studio pour Mac pour incorporer des images, ainsi que comment interagir avec des images à partir du code.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localisation](~/ios/app-fundamentals/localization/index.md)

Ce guide couvre l’ajout des encodages à une application Xamarin.iOS pour prendre en charge de l’internationalisation.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Utilisation des listes de propriétés](~/ios/app-fundamentals/index.md)

Ce document présente Visual Studio pour l’éditeur de liste (.plist) Mac propriété graphique et avancées pour travailler avec Info.plist et Entitlements.plist. Il illustre la définition d’icônes et le lancement d’images pour application iOS et montre les fonctionnalités d’application en spécifiant (droits) à partir de Visual Studio pour Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Utilisation de la sécurité et confidentialité](~/ios/app-fundamentals/security-privacy.md)

Apple a apporté plusieurs améliorations à la sécurité et confidentialité dans iOS 10 (et supérieur) qui aideront les développeurs à améliorer la sécurité de leurs applications et garantir la confidentialité de l’utilisateur final. Cet article décrit l’implémentation de ces fonctionnalités dans une application Xamarin.iOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Thread](~/ios/app-fundamentals/threading.md)

Cet article présente le threading dans une application Xamarin.iOS et communique avec un peu sur le pool de threads .NET, des applications réactives et le garbage collection.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Entrées tactiles](~/ios/app-fundamentals/touch/index.md)

Les écrans tactiles sur la plupart des appareils d’aujourd'hui permettent aux utilisateurs rapidement et efficacement interagir avec les appareils de façon naturelle et intuitive. Cette interaction n’est pas limitée uniquement à la détection de tactile : il est possible d’utiliser des gestes également. Par exemple, le geste de zoom par pincement est un exemple très courant de cette solution par pincement une partie de l’écran avec deux doigts, que l’utilisateur peut effectuer un zoom avant ou arrière. Ce guide examine touchers et gestes dans iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Utilisation des valeurs par défaut de l’utilisateur](~/ios/app-fundamentals/user-defaults.md)

Le `NSUserDefaults` classe offre un moyen pour iOS, applications et des Extensions pour interagir par programmation avec le système par défaut de l’échelle du système. En utilisant le système par défaut, l’utilisateur peut configurer de comportement d’une application ou appliquer un style pour répondre à leurs préférences (basés sur la conception de l’application). Par exemple, pour présenter les données de métrique vs Imperial mesures ou sélectionnez un thème de l’interface utilisateur donné.
