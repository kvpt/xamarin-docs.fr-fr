---
title: Intégration de Java à Xamarin. Android
description: L’écosystème Java comprend une collection diversifiée et immense de composants. Un grand nombre de ces composants peuvent être utilisés pour réduire le temps nécessaire au développement d’une application Android. Ce document présente et fournit une vue d’ensemble de la façon dont les développeurs peuvent utiliser ces composants Java existants pour améliorer leur expérience de développement d’applications Xamarin. Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: 22c3217eec1b2e531ad4534fc1cb35a701a06e34
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524084"
---
# <a name="java-integration-with-xamarinandroid"></a>Intégration de Java à Xamarin. Android

_L’écosystème Java comprend une collection diversifiée et immense de composants. Un grand nombre de ces composants peuvent être utilisés pour réduire le temps nécessaire au développement d’une application Android. Ce document présente et fournit une vue d’ensemble de la façon dont les développeurs peuvent utiliser ces composants Java existants pour améliorer leur expérience de développement d’applications Xamarin. Android._

## <a name="overview"></a>Présentation

Compte tenu de l’étendue de l’écosystème Java, il est très probable que toutes les fonctionnalités requises pour une application Xamarin. Android aient déjà été codées en Java. Pour cette raison, il est attrayant d’essayer et de réutiliser ces bibliothèques existantes lors de la création d’une application Xamarin. Android.

Il existe trois façons de réutiliser des bibliothèques Java dans une application Xamarin. Android: 

- **Créer une bibliothèque de liaisons Java** Avec cette technique, un projet Xamarin. Android est utilisé pour créer C# des wrappers autour des types Java. &ndash; Une application Xamarin. Android peut ensuite référencer C# les wrappers créés par ce projet, puis utiliser le `.jar` fichier. 

- **Interface native Java** C++ C# JNI (Java Native Interface) est une infrastructure qui permet à du code non-Java (tel que ou) d’appeler ou d’être appelé par du code Java exécuté dans une machine virtuelle Java. &ndash; 

- **Portage du code** Cette méthode implique de créer le code source Java, puis de le convertir en C# &ndash; Cette opération peut être effectuée manuellement ou à l’aide d’un outil automatisé tel que la netteté. 

L' *interface native Java* (JNI) est au cœur des deux premières techniques. JNI est un Framework qui permet aux applications qui ne sont pas écrites en Java d’interagir avec le code Java s’exécutant dans un Machine virtuelle Java. Xamarin. Android utilise JNI pour créer des *liaisons* pour C# le code. 

La première technique est une approche plus automatisée et déclarative de la liaison de bibliothèques Java. Il implique l’utilisation de Visual Studio pour Mac ou d’un type de projet Visual Studio fourni par Xamarin. &ndash; Android dans la bibliothèque de liaisons Java. Pour créer correctement ces liaisons, une bibliothèque de liaisons Java peut toujours nécessiter des modifications manuelles, mais pas autant qu’une approche JNI pure. Pour plus d’informations sur les bibliothèques de liaisons Java, consultez [liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md) . 

La deuxième technique, qui utilise JNI, fonctionne à un niveau nettement inférieur, mais peut offrir un contrôle et un accès plus précis aux méthodes Java qui ne seraient normalement pas accessibles par le biais d’une bibliothèque de liaisons Java. 

La troisième technique est radicalement différente des deux précédentes: Portage du code de Java vers C#. Le portage du code d’un langage à un autre peut être un processus très fastidieux, mais il est possible de réduire ce travail à l’aide d’un outil appelé *net*. La netteté est un outil open source qui est un Java-C# to-Converter. 



## <a name="summary"></a>Récapitulatif

Ce document a fourni une vue d’ensemble des différentes façons dont les bibliothèques de Java peuvent être réutilisées dans une application Xamarin. Android. Il a introduit les concepts des liaisons et des wrappers pouvant être appelés et a abordé les options de Portage du code C#Java vers. 


## <a name="related-links"></a>Liens associés

- [Architecture](~/android/internals/architecture.md)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
- [Utilisation de JNI](~/android/platform/java-integration/working-with-jni.md)
- [Améliorer la netteté](https://github.com/slluis/sharpen)
- [Interface native Java](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
