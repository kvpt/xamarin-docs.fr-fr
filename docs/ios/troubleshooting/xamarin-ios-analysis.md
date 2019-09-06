---
title: Règles d’analyse Xamarin. iOS
description: Ce document décrit un ensemble de règles d’analyse qui vérifient les paramètres de projet Xamarin. iOS pour déterminer si plus ou moins de paramètres optimisés sont disponibles.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 211bb94b595a6b4e2f4f8c05ab6a90ba200d44e5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292045"
---
# <a name="xamarinios-analysis-rules"></a>Règles d’analyse Xamarin. iOS

Xamarin. iOS Analysis est un ensemble de règles qui vérifient les paramètres de votre projet pour vous aider à déterminer si des paramètres optimisés/plus optimisés sont disponibles.

Exécutez les règles d’analyse le plus souvent possible pour trouver des améliorations possibles dès le début et économisez le temps de développement.

Pour exécuter les règles, dans le menu de Visual Studio pour Mac, sélectionnez **projet > exécuter l’analyse du code**.

> [!NOTE]
> L’analyse Xamarin. iOS s’exécute uniquement sur la configuration actuellement sélectionnée. Nous vous recommandons vivement d’exécuter l’outil pour les configurations Debug **et** Release.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problème :** L’éditeur de liens est désactivé sur l’appareil pour le mode débogage.
- **Résoudre** Vous devez essayer d’exécuter votre code avec l’éditeur de liens pour éviter les surprises.
Pour le configurer, accédez à projet > iOS Build > comportement de l’éditeur de liens.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problème :** Les builds d’application qui initialisent l’agent de Test Cloud sont rejetées par Apple quand elles sont soumises, car elles utilisent l’API privée.
- **Résoudre** Ajoutez ou corrigez les #if et les définitions nécessaires dans le code.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problème :** La configuration de débogage qui utilise des clés de signature de développeur ne doit pas générer de loi, car elle n’est nécessaire que pour la distribution, qui utilise désormais l’Assistant Publication.
- **Résoudre** Désactivez la génération de la configuration de la loi dans les options de projet pour la configuration Debug.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problème :** L’architecture prise en charge pour «Release | l’appareil n’est pas compatible 64 bits, ARM64 manquant. Il s’agit d’un problème car Apple n’accepte pas 32 bits uniquement pour les applications iOS dans AppStore.
- **Résoudre** Double-cliquez sur votre projet iOS, accédez à Build > iOS Build et modifiez les architectures prises en charge pour qu’elles disposent de ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problème :** Le fait de ne pas utiliser l’option float32 (--AOT-options =-O = float32) entraîne un coût de performance de lourdeur, en particulier sur mobile, où la mathématique double précision est sensiblement plus lente. Notez que .NET utilise la double précision en interne, même pour le type float, donc l’activation de cette option affecte la précision et, éventuellement, la compatibilité.
- **Résoudre** Double-cliquez sur votre projet iOS, accédez à Build > iOS Build et décochez la case « effectuer toutes les opérations float 32 bits sous la forme d’une virgule flottante de 64 bits ».

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problème :** Nous vous recommandons d’utiliser le gestionnaire HttpClient natif au lieu de l’un d’eux pour obtenir de meilleures performances, une plus petite taille d’exécutable et pour prendre en charge facilement les normes les plus récentes.
- **Résoudre** Double-cliquez sur votre projet iOS, accédez à Build > iOS Build et remplacez l’implémentation de HttpClient par passer (iOS 7 +) ou CFNetwork pour prendre en charge la version précédente d’iOS 7.

<a name="XIA0007" />

## <a name="xia0007-usellvmrule"></a>XIA0007: UseLLVMRule

- **Problème :** Pour la configuration de la version | iPhone, nous vous recommandons d’activer le compilateur LLVM qui génère du code plus rapide à exécuter au détriment du temps de génération.
- **Résoudre** Double-cliquez sur votre projet iOS, accédez à Build > iOS Build et pour Release | iPhone, activez l’option du compilateur d’optimisation LLVM.
