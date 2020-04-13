---
title: Distribution OpenJDK mobile de Microsoft
description: Guide pas à pas pour configurer et dépanner la distribution OpenJDK de Microsoft pour le développement mobile.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: a24edbc10d529878092b474df7f186d14049d5e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "60955086"
---
# <a name="microsofts-mobile-openjdk-distribution"></a>Distribution OpenJDK mobile de Microsoft

_Ce guide décrit les étapes pour passer à une distribution interne d’OpenJDK. Cette distribution est destinée au développement mobile._

## <a name="overview"></a>Vue d’ensemble

À partir de Visual Studio 15.9 et Visual Studio pour Mac 7.7, Visual Studio Tools pour Xamarin est passé du kit JDK d’Oracle à une **version légère d’OpenJDK, destinée uniquement au développement Android**. Il s’agit d’une migration obligatoire parce qu’Oracle met fin au support de la distribution commerciale de JDK 8 en 2019 et que JDK 8 est une dépendance requise pour tout développement Android.

Cette migration présente les avantages suivants :

- Vous aurez toujours une version d’OpenJDK qui fonctionne pour le développement Android.

- Le téléchargement du kit JDK 9 ou ultérieur d’Oracle est sans incidence sur l’expérience de développement.

- Elle réduit l’encombrement et la taille du téléchargement.

- Il n’y a plus de problème avec les programmes d’installation et les serveurs tiers.

Si vous souhaitez passer plus tôt à l’expérience améliorée, vous pouvez tester les builds de la distribution OpenJDK mobile de Microsoft sur Windows et Mac. Le processus d’installation est décrit ci-dessous. Vous pourrez revenir au kit JDK Oracle à tout moment.

## <a name="download"></a>Téléchargement

La distribution d’OpenJDK mobile est installée automatiquement si vous sélectionnez les packages du kit Android SDK dans le programme d’installation de Visual Studio sur Windows.

Sur Mac, OpenJDK mobile est installé pour vous dans le cadre de la charge de travail Android des nouvelles installations. Pour les utilisateurs de Visual Studio pour Mac, vous devez l’installer dans le cadre de la mise à jour. L’IDE vous invite à passer au nouveau JDK et l’applique au prochain redémarrage.

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez des problèmes avec le programme d’installation sur Mac ou Windows, vous pouvez effectuer les étapes suivantes d’une installation manuelle :

Vérifiez qu’OpenJDK est installé sur l’ordinateur à l’emplacement approprié :

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.x**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.x**

Faites pointer l’environnement IDE sur le nouveau kit JDK :

- **Mac** &ndash; Cliquez sur **Outils > Gestionnaire des kits SDK > Emplacements** et, dans **Emplacement du kit SDK Java (JDK)**, indiquez le chemin d’accès complet de l’installation d’OpenJDK. Dans l’exemple suivant, ce chemin est défini sur **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**, mais votre version est peut-être plus récente.

![Définir le chemin du JDK pour la distribution OpenJDK mobile de Microsoft sur Mac](openjdk-images/vsm.png)

- **Windows** &ndash; Cliquez sur **Outils > Options > Xamarin > Paramètres Android** et, dans **Emplacement du kit de développement Java**, indiquez le chemin d’accès complet de l’installation d’OpenJDK. Dans l’exemple suivant, ce chemin est défini sur **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**, mais votre version est peut-être plus récente :

![Définir le chemin du JDK pour la distribution OpenJDK mobile de Microsoft sur Windows](openjdk-images/vs.png)

## <a name="known-issues"></a>Problèmes connus

### <a name="package-openjdkv1regkeyversion18025chipx64-failed-to-install"></a>L’installation du package 'OpenJDKV1.RegKey,version=1.8.0.25,chip=x64' échoue

Le problème peut se produire dans certains environnements d’entreprise. OpenJDK est déjà sur l’ordinateur : suivez les [étapes de dépannage ci-dessus](#troubleshooting) pour pointer votre IDE sur l’emplacement approprié. Vous pouvez suivre l’état des problèmes [ici](https://developercommunity.visualstudio.com/content/problem/382549/packageidopenjdkv1regkeypackageactioninstallreturn.html).

## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez appris à configurer votre IDE pour utiliser la distribution OpenJDK mobile de Microsoft et à résoudre les problèmes si vous en rencontrez.
