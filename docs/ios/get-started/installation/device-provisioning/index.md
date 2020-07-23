---
title: Provisionnement d’appareil pour Xamarin.iOS
description: Ce document décrit comment provisionner un appareil afin de pouvoir l’utiliser pour tester une application. Il explique également comment configurer une application pour qu’elle puisse utiliser des fonctionnalités telles que les notifications Push.
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/06/2018
ms.openlocfilehash: dea08895b59626da97c1ca3d97464cfc80f42b88
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928703"
---
# <a name="device-provisioning-for-xamarinios"></a>Provisionnement d’appareil pour Xamarin.iOS

Pendant le développement d’une application Xamarin.iOS, il est essentiel de la tester en la déployant sur un appareil physique, en plus du simulateur. Des bogues propres aux appareils et des problèmes de performance peuvent surgir lors de l’exécution sur un appareil, en raison des limites matérielles comme la mémoire ou la connectivité réseau. Pour effectuer des tests sur un appareil physique, celui-ci doit être *provisionné* et Apple doit être informé que l’appareil va servir à des tests.

Les sections mises en surbrillance dans l’image ci-dessous indiquent les étapes exigées pour préparer le provisionnement iOS :

[![Les sections en surbrillance de cette image montrent les étapes requises pour configurer l’approvisionnement iOS](images/provisioningdiagram.png)](images/provisioningdiagram.png#lightbox)

L’étape suivante consiste à distribuer l’application. Pour plus d’informations sur le déploiement, consultez les guides de [distribution d’applications](~/ios/deploy-test/app-distribution/index.md).

Avant de déployer l’application sur un appareil, vous devez souscrire un abonnement actif à un programme pour développeurs Apple *ou* utiliser le [provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md). Apple propose deux options de programme :

- **Programme pour développeurs Apple** : que vous soyez un particulier ou représentiez une organisation, le programme pour développeurs (Apple Developer Program) vous permet de développer, tester et distribuer des applications.
- **Programme d’entreprise pour développeurs Apple** : le programme d’entreprise (Apple Developer Enterprise Program) est plus adapté aux organisations qui souhaitent développer et distribuer des applications en interne uniquement. Les membres du programme d’entreprise n’ont pas accès à iTunes Connect et les applications créées ne peuvent pas être publiées sur l’App Store.

Pour vous inscrire à l’un de ces programmes, accédez au [portail des développeurs Apple](https://developer.apple.com/programs/enroll/). Notez que pour vous inscrire en tant que développeur Apple, vous avez besoin d’un [ID Apple](https://appleid.apple.com/). Ce guide a été créé en partant du principe que vous **êtes** membre d’un programme pour développeurs Apple.

Par ailleurs, Apple a introduit le [provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md) dans Xcode 7, qui permet d’exécuter une application unique sur un seul appareil *sans* être membre d’un programme pour développeurs Apple. Il existe plusieurs restrictions avec un tel provisionnement, comme expliqué [ici](~/ios/get-started/installation/device-provisioning/free-provisioning.md#limitations).

Toute application qui s’exécute sur un appareil doit inclure un jeu de métadonnées (ou *empreinte numérique*), qui contient des informations sur l’application et le développeur. Apple utilise cette empreinte numérique pour vérifier que l’application n’a pas été modifiée pendant le déploiement ou l’exécution sur l’appareil de l’utilisateur. Dans ce but, Apple exige que les développeurs d’applications enregistrent leur ID Apple en tant que développeur. Pour configurer un ID Apple, ils doivent demander un certificat et inscrire l’appareil sur lequel l’application sera déployée.

Lors du déploiement d’une application sur un appareil, un profil de provisionnement est également installé sur l’appareil iOS. Ce profil de provisionnement permet de vérifier les informations avec lesquelles l’application a été signée au moment de la génération. Il est signé par chiffrement par Apple. Ensemble, les vérifications du profil de provisionnement et de l’empreinte numérique déterminent si une application peut être déployée sur un appareil en déterminant :

- **Qui** (Certificats : L’application a-t-elle été signée avec une clé privée, qui a une clé publique correspondante dans le profil de provisionnement ? Le certificat associe également le développeur à une équipe de développement.)
- **Quoi** (ID d’application individuel : L’identificateur de bundle défini dans le fichier Info.plist correspond-il à l’ID d’application figurant dans le profil de provisionnement ?)
- **Où** (Appareils : L’appareil figure-t-il dans le profil de provisionnement ?)

Ces étapes permettent de vérifier que tout ce qui est créé ou utilisé pendant le processus de développement, notamment les applications et les appareils, peut être associé à un compte de développeur Apple.

## <a name="provisioning-your-device"></a>Provisionnement de votre appareil

Il existe deux façons de provisionner votre appareil iOS :

- **Automatiquement (recommandé)** : Sélectionnez le schéma **Provisionnement automatique** dans votre projet pour que Visual Studio crée et gère automatiquement vos identités de signature, ID d’application et profils de provisionnement. Pour plus d’informations sur la façon de gérer automatiquement le provisionnement, consultez le guide de [provisionnement automatique](automatic-provisioning.md). Il s’agit de la méthode recommandée pour provisionner un appareil iOS.

- **Manuellement** : Il est possible de créer et gérer des identités de signature, ID d’application et profils de provisionnement par le biais du portail des développeurs Apple, comme décrit dans le guide de [provisionnement manuel](manual-provisioning.md). Ces artefacts peuvent être gérés comme décrit dans le guide de [gestion des comptes Apple](~/cross-platform/macios/apple-account-management.md).

## <a name="provisioning-for-application-services"></a>Provisionnement des services d’application

Apple propose une sélection de services d’application spéciaux, également appelés fonctionnalités, que vous pouvez activer pour une application Xamarin.iOS. Ces services d’application doivent être configurés à la fois sur le portail de provisionnement iOS quand l’**ID d’application** est créé et dans le fichier **Entitlements.plist** qui fait partie du projet d’application Xamarin.iOS. Pour plus d’informations sur l’ajout de services d’application à votre application, reportez-vous au guide de [présentation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md) et au guide d’[utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md).

- Créez un ID d’application avec les services d’application requis.
- Créez un [profil de provisionnement](#provisioning-your-device) qui contient cet ID d’application.
- Définir des droits dans le projet Xamarin.iOS

## <a name="related-links"></a>Liens connexes

- [Provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribution d’applications](~/ios/deploy-test/app-distribution/index.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
- [Apple - Guide de distribution d’applications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
