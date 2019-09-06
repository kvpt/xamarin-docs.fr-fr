---
title: Pourquoi les Jenkins ne sont-ils pas pris en charge par Microsoft ?
description: Ce document décrit, à un niveau élevé, l’interaction entre Xamarin et le système Jenkins CI. Il aborde également quelques problèmes courants qui se posent lors de l’utilisation de Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 4f09f4ca97dcf50891aa0a0415e47d474297c411
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282763"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>Pourquoi les Jenkins ne sont-ils pas pris en charge par Microsoft ?

## <a name="jenkins-support-explanation"></a>Explication du support Jenkins

Jenkins est une suite d’intégration open source. en raison de ce nombre de problèmes directement provoqués par le Jenkins *lui-même* doit être classé comme un problème sur lequel vous avez obtenu le code ; tels que le [Jenkins principal référentiel](https://github.com/jenkinsci/jenkins)ou référentiel pour [Jenkins. app](https://github.com/stisti/jenkins-app).

L’exception à cette question concerne les problèmes qui peuvent être isolés pour des bogues particuliers dans les outils de Xamarin. Si vous pensez que c’est le cas, vous pouvez vérifier vos [options de support](~/cross-platform/troubleshooting/support-options.md), même si le problème peut être lié à l’aide de l’équipe de support Xamarin.

## <a name="setup-jenkins-with-xamarin"></a>Configurer Jenkins avec Xamarin

Comme indiqué ci-dessus, les problèmes Jenkins ne sont pas pris en charge directement par notre équipe. le guide [using Jenkins with Xamarin](~/tools/ci/jenkins-walkthrough.md) peut être utilisé pour configurer un serveur Jenkins ci intégré à Xamarin. 

## <a name="fixes-for-common-issues"></a>Correctifs pour les problèmes courants

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins ne parvient pas à trouver le Android SDK

Le message d’erreur de ce problème se présente comme suit :

> erreur XA5205 : Le répertoire Android SDK est introuvable. Veuillez définir via/p : AndroidSdkDirectory

Les options de configuration de l’emplacement du kit de développement logiciel (SDK) peuvent varier en fonction du plug-in Android Jenkins que vous utilisez. pour savoir comment définir cela, consultez le Guide du plug-in. Par exemple, le [plug-in émulateur Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) recherche automatiquement le kit de développement logiciel (SDK), mais s’il ne peut pas le trouver ; l’emplacement peut également être défini via la page de configuration du système Jenkins pour ce plug-in. 


## <a name="deprecated-errors"></a>Erreurs dépréciées

> [!IMPORTANT]
> Ce problème a été résolu dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins signale une licence Xamarin non valide
Les messages d’erreur relatifs à ce problème sont généralement des éléments comme

> Erreur XA9008 : La génération à partir de la ligne de commande nécessite une licence d’entreprise

ou Gestionnaire de configuration

> Erreur : L’édition Starter de Xamarin. iOS ne prend pas en charge la création en dehors de Xamarin Studio 

La cause la plus courante de ce scénario est l’utilisation de Jenkins en se connectant avec un compte d’utilisateur qui n’est pas associé à votre licence Xamarin. Le moyen le plus simple de résoudre ce problèmes consiste à installer Jenkins en tant qu’application directement via le compte d’utilisateur. Ce processus et quelques considérations supplémentaires sont décrits ici :[https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
