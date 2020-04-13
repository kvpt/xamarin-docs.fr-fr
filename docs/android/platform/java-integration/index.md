---
title: Intégration Java avec Xamarin.Android
description: L’écosystème de Java comprend une collection diversifiée et immense de composants. Beaucoup de ces composants peuvent être utilisés pour réduire le temps qu’il faut pour développer une application Android. Ce document présentera et fournira un aperçu de haut niveau de certaines des façons dont les développeurs peuvent utiliser ces composants Java existants pour améliorer leur expérience de développement d’applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: ecaa02e036c74074b4fa922ea079355b72ff02e2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020101"
---
# <a name="java-integration-with-xamarinandroid"></a>Intégration Java avec Xamarin.Android

_L’écosystème de Java comprend une collection diversifiée et immense de composants. Beaucoup de ces composants peuvent être utilisés pour réduire le temps qu’il faut pour développer une application Android. Ce document présentera et fournira un aperçu de haut niveau de certaines des façons dont les développeurs peuvent utiliser ces composants Java existants pour améliorer leur expérience de développement d’applications Xamarin.Android._

## <a name="overview"></a>Vue d’ensemble

Compte tenu de l’étendue de l’écosystème Java, il est très probable que toute fonctionnalité donnée requise pour une application Xamarin.Android a déjà été codée à Java. Pour cette raison, il est attrayant d’essayer de réutiliser ces bibliothèques existantes lors de la création d’une application Xamarin.Android.

Il existe trois façons possibles de réutiliser les bibliothèques Java dans une application Xamarin.Android: 

- **Créer une bibliothèque** &ndash; java Bindings Avec cette technique, un projet Xamarin.Android est utilisé pour créer des emballages C ' autour des types Java. Une application Xamarin.Android peut alors faire référence aux emballages Cmd créés par ce projet, puis utiliser le `.jar` fichier. 

- **Interface native java** &ndash; L’interface *native java* *Interface* (JNI) est un cadre qui permet au code non-Java (comme le C ou le C) d’appeler ou d’être appelé par le code Java en cours d’exécution à l’intérieur d’un JVM. 

- **Port le Code** &ndash; Cette méthode consiste à prendre le code source Java, puis à le convertir en C. Cela peut être fait manuellement, ou en utilisant un outil automatisé tel que Sharpen. 

Au cœur des deux premières techniques se trouve *l’interface indigène Java* (JNI). JNI est un cadre qui permet aux applications non écrites à Java d’interagir avec le code Java en cours d’exécution dans une machine virtuelle Java. Xamarin.Android utilise JNI pour créer *des liaisons* pour le code C. 

La première technique est une approche plus automatisée et déclarative pour lier les bibliothèques Java. Il s’agit d’utiliser soit Visual Studio pour Mac ou un &ndash; type de projet Visual Studio qui est fourni par Xamarin.Android la bibliothèque Java Bindings. Pour créer avec succès ces fixations, une bibliothèque de liaisons Java peut encore nécessiter quelques modifications manuelles, mais pas autant qu’une approche JNI pure. Voir [Binding a Java Library](~/android/platform/binding-java-library/index.md) pour plus d’informations sur les bibliothèques Java Binding. 

La deuxième technique, utilisant JNI, fonctionne à un niveau beaucoup plus bas, mais peut fournir un contrôle plus fin et l’accès aux méthodes Java qui ne seraient normalement pas accessibles par une bibliothèque de liaison Java. 

La troisième technique est radicalement différente des deux précédentes : le portage du code de Java à C. Le portage du code d’une langue à l’autre peut être un processus très laborieux, mais il est possible de réduire cet effort à l’aide d’un outil appelé *Sharpen*. Sharpen est un outil open source qui est un convertisseur Java-to-C. 

## <a name="summary"></a>Récapitulatif

Ce document a fourni un aperçu de haut niveau de certaines des différentes façons dont les bibliothèques de Java peuvent être réutilisées dans une application Xamarin.Android. Il a introduit les concepts de fixations et d’emballages callables gérés, et discuté des options pour le portage du code Java à C . 

## <a name="related-links"></a>Liens connexes

- [Architecture](~/android/internals/architecture.md)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
- [Travailler avec JNI](~/android/platform/java-integration/working-with-jni.md)
- [Améliorer la netteté](https://github.com/slluis/sharpen)
- [Java Native Interface](https://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
