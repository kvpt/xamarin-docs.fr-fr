---
title: Test, optimisation et déploiement d’applications Xamarin.Android
description: Cette section comporte des guides qui expliquent comment tester une application, optimiser ses performances, la préparer pour la mise en production, la signer avec un certificat et la publier sur un Store d'applications.
ms.prod: xamarin
ms.assetid: 568C0B85-EFF3-AF6F-5605-95055193D367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: bec2bef92433320925c4179535c8371b69b9bf22
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996550"
---
# <a name="deployment-and-testing-of-xamarinandroid-apps"></a>Déploiement et test des applications Xamarin. Android

Cette section comporte des guides qui expliquent comment tester une application, optimiser ses performances, la préparer pour la mise en production, la signer avec un certificat et la publier sur un Store d'applications.

## <a name="application-package-sizes"></a>[Tailles de packages d'application](app-package-size.md)

Cet article examine les éléments constitutifs des packages d’application Xamarin.Android, ainsi que les stratégies associées permettant de les déployer avec efficacité au cours des phases de débogage et de mise en production.

## <a name="apply-changes"></a>[Appliquer les modifications](apply-changes.md)

Ce guide couvre la fonctionnalité appliquer les modifications qui vous permet de transmettre des modifications de ressources à votre application en cours d’exécution sans redémarrer votre application.

## <a name="building-apps"></a>[Création d’applications](building-apps/index.md)

Cette section décrit le fonctionnement du processus de génération et explique comment générer des APK propres à une interface ABI.

## <a name="command-line-emulator"></a>[Émulateur en ligne de commande](command-line-emulator.md)

Cet article aborde brièvement le démarrage de l’émulateur en ligne de commande.

## <a name="debugging"></a>[Débogage](~/android/deploy-test/debugging/index.md)

Les guides de cette section vous aident à déboguer votre application à l’aide d’émulateurs Android, d’appareils Android réels et du journal de débogage.

## <a name="setting-the-debuggable-attribute"></a>[Configuration de l’attribut Debuggable](~/android/deploy-test/debuggable-attribute.md)

Cet article explique comment définir l’attribut Debuggable afin que des outils tels que `adb` puissent communiquer avec la Machine virtuelle Java.

## <a name="environment"></a>[Environment](environment.md)

Cet article décrit l’environnement d’exécution de Xamarin.Android et les propriétés système Android qui influencent l’exécution du programme.

## <a name="gdb"></a>[GDB](gdb.md)

Cet article explique comment utiliser `gdb` pour déboguer une application Xamarin.Android.

## <a name="installing-a-system-app"></a>[Installer une application système](install-system-app.md)

Ce guide explique comment installer une application Xamarin.Android en tant qu’application système sur un appareil Android ou au sein d’une ROM personnalisée.

## <a name="linking-on-android"></a>[Liaison sur Android](linker.md)

Cet article aborde le processus de liaison utilisé par Xamarin.Android pour réduire la taille finale d’une application. Il décrit les différents niveaux de liaison possibles, et aide à résoudre les problèmes et à limiter les erreurs potentiellement causées par l’éditeur de liens.

## <a name="xamarinandroid-performance"></a>[Performances de Xamarin.Android](~/android/deploy-test/performance.md)

De nombreuses techniques permettent d’accroître les performances des applications générées avec Xamarin.Android. Ensemble, ces techniques peuvent considérablement réduire la charge de travail d’un processeur, de même que la quantité de mémoire consommée par une application.

## <a name="profiling-android-apps"></a>[Profilage des applications Android](~/android/deploy-test/profiling.md)

Ce guide explique comment utiliser les outils de profilage pour examiner les performances et l’utilisation de la mémoire d’une application Android.

## <a name="preparing-an-application-for-release"></a>[Préparation d’une application pour la mise en production](~/android/deploy-test/release-prep/index.md)

Une fois qu’une application a été codée et testée, il est nécessaire de préparer un paquet pour la distribution. La première tâche de préparation de ce paquet consiste à générer l’application pour sa mise en production, ce qui implique principalement de définir quelques attributs d’application.

## <a name="signing-the-android-application-package"></a>[Signature du package d’application Android](~/android/deploy-test/signing/index.md)

Découvrez comment créer une identité de signature Android, créer un nouveau certificat de signature pour les applications Android et signer l’application avec le certificat de signature. Cette rubrique explique aussi comment exporter l’application sur disque pour une distribution *ad-hoc*. Le fichier APK ainsi créé peut être chargé de façon indépendante (sideloaded) dans les appareils Android sans passer par un App Store.

## <a name="publishing-an-application"></a>[Publication d’une application](~/android/deploy-test/publishing/index.md)

Cette série d’articles décrit les étapes à suivre pour distribuer publiquement une application créée avec Xamarin.Android. La distribution peut passer par des canaux tels que l’e-mail, un serveur web privé, Google Play ou Amazon Appstore pour Android.
