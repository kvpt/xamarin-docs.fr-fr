---
title: Références natives projets iOS, Mac et liaisons
description: Les références natives vous permettent d’incorporer un Framework natif dans un projet Xamarin. iOS, Xamarin. Mac ou de liaison.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: e2f874446b48726afc2218e5cdcac9b8736e1681
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930440"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Références natives dans les projets iOS, Mac et Bindings

_Les références natives vous permettent d’incorporer un Framework natif dans un projet ou un projet de liaison Xamarin. iOS ou Xamarin. Mac._

Depuis iOS 8,0, il était possible de créer une infrastructure intégrée pour partager du code entre les extensions d’application et l’application principale dans Xcode. À l’aide de la fonctionnalité de référence native, il sera possible d’utiliser ces frameworks intégrés (créés avec Xcode) dans Xamarin. iOS.

> [!IMPORTANT]
> Il n’est pas possible de créer des frameworks incorporés à partir de tout type de projet Xamarin. iOS ou Xamarin. Mac, les références natives autorisent uniquement la consommation des infrastructures natives (objective-C) existantes.

<a name="Terminology"></a>

## <a name="terminology"></a>Terminologie

Dans iOS 8 (et versions ultérieures), les **infrastructures incorporées** peuvent être des frameworks incorporés statiquement et liés de manière dynamique. Pour les distribuer correctement, vous devez les intégrer dans des infrastructures « Fat » qui incluaient toutes leurs _tranches_ pour chaque architecture d’appareil que vous souhaitez prendre en charge avec votre application.

<a name="Static-vs-Dynamic-Frameworks"></a>

### <a name="static-vs-dynamic-frameworks"></a>Comparaison entre les infrastructures statique et dynamique

Les **infrastructures statiques** sont liées au moment de la compilation, où les **infrastructures dynamiques** sont liées au moment de l’exécution et peuvent être modifiées sans réédition de liens. Si vous avez utilisé un Framework tiers antérieur à iOS 8, vous utilisiez un **Framework statique** qui a été compilé dans votre application. Pour plus d’informations, consultez la documentation sur la programmation de la [bibliothèque dynamique](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) d’Apple.

<a name="Embedded-vs-System-Frameworks"></a>

### <a name="embedded-vs-system-frameworks"></a>Comparaison entre les frameworks intégrés et les infrastructures système

Les **frameworks incorporés** sont inclus dans votre offre groupée d’applications et sont uniquement accessibles à votre application spécifique via son bac à sable (sandbox). Les **infrastructures système** sont stockées au niveau du système d’exploitation et sont disponibles pour toutes les applications sur l’appareil. Actuellement, seul Apple a la possibilité de créer des frameworks au niveau du système d’exploitation.

<a name="Thin-vs-Fat-Frameworks"></a>

### <a name="thin-vs-fat-frameworks"></a>Frameworks étroits et FAT

Les **frameworks légers** contiennent uniquement le code compilé pour une architecture système spécifique où les **infrastructures Fat** contiennent du code pour plusieurs architectures. Chaque code base spécifique à l’architecture compilé dans une infrastructure est désigné sous le terme de « _tranche_». Ainsi, par exemple, si nous avions une infrastructure qui a été compilée pour les deux architectures du simulateur iOS (i386 et X86_64), elle contiendrait deux tranches.

Si vous avez essayé de distribuer cet exemple d’infrastructure avec votre application, il s’exécuterait correctement sur le simulateur, mais échouera sur l’appareil, car l’infrastructure ne contient pas de tranches spécifiques au code pour un appareil iOS. Pour garantir qu’une infrastructure fonctionnera dans toutes les instances, elle doit également inclure des secteurs spécifiques à l’appareil, tels que arm64, ARMv7 et armv7s.

<a name="Working-with-Embedded-Frameworks"></a>

## <a name="working-with-embedded-frameworks"></a>Utilisation des frameworks intégrés

Vous devez effectuer deux étapes pour utiliser des frameworks intégrés dans une application Xamarin. iOS ou Xamarin. Mac : création d’une infrastructure FAT et incorporation de l’infrastructure.

<a name="Overview"></a>

### <a name="creating-a-fat-framework"></a>Création d’une infrastructure FAT

Comme indiqué ci-dessus, pour pouvoir consommer un Framework incorporé dans votre application, il doit s’agir d’une infrastructure Fat qui comprend toutes les tranches d’architectures système pour les périphériques sur lesquels votre application s’exécutera.

Lorsque l’infrastructure et l’application consommatrice se trouvent dans le même projet Xcode, il ne s’agit pas d’un problème car Xcode générera à la fois l’infrastructure et l’application à l’aide des mêmes paramètres de Build. Étant donné que les applications Xamarin ne peuvent pas créer d’infrastructures incorporées, cette technique ne peut pas être utilisée.

Pour résoudre ce problème, l' `lipo` outil en ligne de commande peut être utilisé pour fusionner deux ou plusieurs frameworks dans une infrastructure Fat contenant toutes les tranches nécessaires. Pour plus d’informations sur l’utilisation de la `lipo` commande, consultez notre documentation sur les [bibliothèques natives de liaison](~/ios/platform/native-interop.md) .

<a name="Embedding-a-Framework"></a>

### <a name="embedding-a-framework"></a>Incorporation d’une infrastructure

L’étape suivante est requise pour incorporer une infrastructure dans un projet Xamarin. iOS ou Xamarin. Mac à l’aide de références natives :

1. Créez ou ouvrez un projet Xamarin. iOS, Xamarin. Mac ou de liaison existant.
2. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter**  >  **Ajouter une référence Native**: 

    [![Dans le Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet et sélectionnez Ajouter une référence native.](native-references-images/ref01.png)](native-references-images/ref01.png#lightbox)
3. Dans la boîte de dialogue **ouvrir** , sélectionnez le nom de l’infrastructure Native que vous souhaitez incorporer, puis cliquez sur le bouton **ouvrir** : 

    [![Sélectionnez le nom de l’infrastructure native à incorporer, puis cliquez sur le bouton Ouvrir.](native-references-images/ref02.png)](native-references-images/ref02.png#lightbox)
4. L’infrastructure sera ajoutée à l’arborescence du projet : 

    [![L’infrastructure sera ajoutée à l’arborescence des projets](native-references-images/ref03.png)](native-references-images/ref03.png#lightbox)

Lorsque le projet est compilé, le Framework natif est incorporé dans l’offre groupée de l’application.

<a name="App-Extensions-and-Embedded-Frameworks"></a>

## <a name="app-extensions-and-embedded-frameworks"></a>Extensions d’application et frameworks intégrés

En interne, Xamarin. iOS peut tirer parti de cette fonctionnalité pour établir une liaison avec le runtime mono comme Framework (lorsque la cible de déploiement est >= iOS 8,0), réduisant ainsi considérablement la taille de l’application pour les applications avec des extensions (puisque le runtime mono n’est inclus qu’une seule fois pour l’ensemble de l’application, et non une fois pour l’application conteneur).

Les extensions sont liées au runtime mono comme une infrastructure, car toutes les extensions requièrent iOS 8,0.

Applications qui n’ont pas d’extensions et d’applications qui ciblent iOS 

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Cet article a décrit en détail l’intégration d’une infrastructure native dans une application Xamarin. iOS ou Xamarin. Mac.
