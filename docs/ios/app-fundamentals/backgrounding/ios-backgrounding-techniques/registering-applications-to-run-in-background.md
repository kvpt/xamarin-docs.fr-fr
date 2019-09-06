---
title: Inscription des applications Xamarin. iOS pour une exécution en arrière-plan
description: Ce document explique comment inscrire une application Xamarin. iOS pour qu’elle s’exécute en arrière-plan. Il aborde les applications audio, les applications VoIP, les accessoires externes et Bluetooth, et bien plus encore.
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 044ae7e4a77abb77e766df58b9cda5938ce8fe7b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290614"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>Inscription des applications Xamarin. iOS pour une exécution en arrière-plan

L’inscription de tâches individuelles pour les privilèges d’arrière-plan fonctionne pour certaines applications, mais que se passe-t-il si une application est régulièrement appelée pour exécuter des tâches importantes et longues, telles que l’obtention de directions pour l’utilisateur via GPS ? Les applications telles que celles-ci doivent plutôt être inscrites en tant qu’applications requises en arrière-plan connues.

L’inscription d’une application signale à iOS que l’application doit disposer de privilèges spéciaux nécessaires pour effectuer des tâches en arrière-plan.

## <a name="application-registration-categories"></a>Catégories d’inscription d’application

Les applications inscrites peuvent appartenir à plusieurs catégories :

- Les lecteurs **audio** -musique et autres applications qui fonctionnent avec du contenu audio peuvent être enregistrés pour continuer à jouer du son, même lorsque l’application n’est plus au premier plan. Si une application de cette catégorie tente d’effectuer autre chose que de lire un fichier audio ou de télécharger en arrière-plan, iOS l’arrête.
- Les **applications VoIP (** voix sur IP) bénéficient des mêmes privilèges que ceux octroyés aux applications audio pour maintenir le traitement audio en arrière-plan. Ils sont également autorisés à répondre en fonction des besoins des services VoIP qui les alimentent pour maintenir les connexions actives.
- **Accessoires externes et Bluetooth** -réservés aux applications qui doivent communiquer avec des appareils Bluetooth et d’autres accessoires matériels externes, l’inscription dans ces catégories permet à l’application de rester connectée au matériel.
- **Newsstand** : une application Newsstand peut continuer à synchroniser le contenu en arrière-plan.
- **Emplacement** : les applications qui utilisent des données GPS ou d’emplacement réseau peuvent envoyer et recevoir des mises à jour d’emplacement en arrière-plan.
- **Fetch (iOS 7 +)** : une application inscrite pour les privilèges d’extraction en arrière-plan peut vérifier un nouveau contenu par un fournisseur à intervalles réguliers, en présentant à l’utilisateur un contenu mis à jour lorsqu’il retourne à l’application.
- **Notifications distantes (iOS 7 +)** : les applications peuvent s’inscrire pour recevoir des notifications d’un fournisseur et utiliser la notification pour lancer une mise à jour avant que l’utilisateur n’ouvre l’application. Les notifications peuvent se présenter sous la forme de notifications push, ou choisir de réveiller l’application en mode silencieux.


Les applications peuvent être inscrites en définissant la propriété **modes d’arrière-plan requis** dans le fichier *info. plist*de l’application. Une application peut s’inscrire dans autant de catégories que nécessaire :

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Définition des modes d’arrière-plan")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Pour obtenir un guide pas à pas d’inscription d’une application pour les mises à jour de l’emplacement en arrière-plan, consultez la procédure pas à pas relative à l' [emplacement en arrière-plan](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>L’application n’est pas exécutée dans la propriété Background

Une autre propriété qui peut être définie dans *info. plist* est l' *application ne s’exécute pas en arrière-plan*, ou `UIApplicationExitsOnSuspend` propriété :

 [![](registering-applications-to-run-in-background-images/plist.png "Désactivation de l’exécution en arrière-plan")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Cela revient exactement à définir le paramètre d’actualisation de l’application en arrière-plan sur désactivé dans iOS 7 +, sauf qu’il ne peut être modifié que côté développeur et est disponible pour iOS 4 et versions ultérieures. L’application est suspendue immédiatement après avoir entré l’arrière-plan et ne peut pas effectuer de traitement.

Utilisez cette propriété si votre application n’est pas conçue pour gérer le traitement en arrière-plan, car elle permet d’éviter un comportement inattendu.

## <a name="background-fetch-and-remote-notifications"></a>Récupération en arrière-plan et notifications à distance

L’extraction en arrière-plan et les notifications distantes sont des catégories d’inscription spéciales introduites dans iOS 7. Ces catégories permettent aux applications de recevoir le nouveau contenu d’un fournisseur et de les mettre à jour en arrière-plan. La section suivante explore les notifications d’extraction et les notifications distantes de manière plus détaillée, et introduit également la sensibilisation à l’emplacement comme moyen de mise à jour d’une application en arrière-plan sur iOS 6.
