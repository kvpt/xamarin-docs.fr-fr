---
title: Frameworks intégrés dans Xamarin. iOS
description: Ce document décrit comment partager du code avec des infrastructures incorporées dans une application Xamarin. iOS. Pour ce faire, vous pouvez utiliser l’outil mTouch ou les références natives.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: ba3be4fea9999698c5a81faf5b07bec99fb1aa46
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753241"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Frameworks intégrés dans Xamarin. iOS

_Ce document décrit comment les développeurs d’applications peuvent incorporer des frameworks d’utilisateur dans leurs applications._

Avec iOS 8,0 Apple, il était possible de créer une infrastructure intégrée pour partager du code entre les extensions d’application et l’application principale dans Xcode.

Xamarin. iOS 9,0 ajoute la prise en charge de l’utilisation de ces frameworks intégrés (créés avec Xcode) dans les applications Xamarin. iOS. *Il n’est **pas** possible de créer des frameworks incorporés à partir de tout type de projet Xamarin. iOS, qui consomme uniquement des frameworks natifs (objective-C) existants.*

Il existe deux façons de consommer des frameworks dans Xamarin. iOS :

- Transmettez l’infrastructure à l’outil mTouch en ajoutant le code suivant aux arguments mTouch supplémentaires dans les options de **génération iOS** du projet :

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Cette valeur doit être définie pour chaque configuration de projet.

- Ajouter des références natives à partir du menu contextuel

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Cliquez avec le bouton droit sur le projet et accédez à ajouter des références natives

![](embedded-frameworks-images/xam-native-refs.png "Sélectionnez Ajouter des références natives dans Visual Studio pour Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Cliquez avec le bouton droit sur le projet et accédez à ajouter des références natives

![](embedded-frameworks-images/vs-native-refs.png "Sélectionnez Ajouter des références natives dans Visual Studio")

-----

  Cela fonctionne pour toutes les configurations.

Dans les versions ultérieures de Visual Studio pour Mac et des outils Xamarin pour Visual Studio, il sera possible de consommer des frameworks à partir de l’IDE (sans modifier les fichiers projet manuellement).

Quelques exemples de projets se trouvent sur [GitHub](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limites

- Les frameworks incorporés sont uniquement pris en charge dans les projets [unifiés](~/cross-platform/macios/unified/index.md) .
- Les frameworks incorporés sont uniquement pris en charge dans les projets avec une cible de déploiement d’au moins iOS 8,0.
- Si une extension requiert un Framework incorporé, l’application conteneur doit également avoir une référence à l’infrastructure, sinon l’infrastructure ne sera pas incluse dans l’offre groupée d’applications.

## <a name="the-mono-runtime"></a>Runtime mono

En interne, Xamarin. iOS tire parti de cette fonctionnalité pour établir une liaison avec le runtime mono en tant qu’infrastructure, au lieu de lier le runtime mono de manière statique dans chaque extension et l’application de conteneur.

Cela s’effectue automatiquement si l’application conteneur est une application unifiée, qu’elle contient des extensions et que le déploiement cible est iOS 8,0 ou une version ultérieure.

Les applications sans extensions continuent de lien avec le runtime mono de manière statique, car il y a une pénalité de taille pour l’utilisation d’une infrastructure si une seule application y fait référence.

Ce comportement peut être remplacé par le développeur de l’application, en ajoutant le code suivant comme argument mTouch supplémentaire dans les options de génération iOS du projet :

- `--mono:static`: Liens avec le runtime mono statiquement.
- `--mono:framework`: Liens avec le runtime mono en tant qu’infrastructure.

Un scénario de liaison avec le runtime mono en tant qu’infrastructure même pour les applications sans extensions consiste à réduire la taille de l’exécutable, afin de surmonter les restrictions de taille qu’Apple applique sur le fichier exécutable. Pour référence, le runtime mono ajoute approximativement 1.7 Mo par architecture (à partir de Xamarin. iOS 8,12, mais son variation entre les versions, et même entre les applications). L’infrastructure mono ajoute approximativement 2,3 Mo par architecture, ce qui signifie que pour une application à architecture unique sans extensions, le fait que le lien de l’application avec le runtime mono en tant qu’infrastructure réduit la taille de l’exécutable d’environ 1.7 Mo, mais ajoute un Framework ~ 2.3 Mo, résultant dans un aucune d’application ~ 0,6 Mo plus grand.
