---
title: Paramètres de nommage avec Javadoc
description: Cet article explique comment récupérer les noms de paramètres dans un projet de liaison Java en utilisant Javadoc généré par le projet Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027664"
---
# <a name="naming-parameters-with-javadoc"></a>Paramètres de nommage avec Javadoc

_Cet article explique comment récupérer les noms de paramètres dans un projet de liaison Java en utilisant Javadoc généré par le projet Java._

## <a name="overview"></a>Vue d’ensemble

Lors de la liaison d’une bibliothèque Java existante, certaines métadonnées sur l’API lié est perdue. En particulier les noms des paramètres aux méthodes. Les noms de `p0` `p1`paramètres apparaîtront comme, , etc. C’est parce `.class` que les fichiers Java ne conservent pas les noms de paramètres qui ont été utilisés dans le code source Java. 

Un projet de liaison Xamarin.Android Java peut fournir les noms de paramètres s’il a accès au HTML Javadoc à partir de la bibliothèque d’origine. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Intégration de Javadoc HTML dans un projet de liaison Java

L’intégration du HTML Javadoc dans un projet de liaison Java est un processus manuel composé des étapes suivantes : 

1. Téléchargez le Javadoc pour la bibliothèque
2. Modifier `.csproj` le fichier `<JavaDocPaths>` et ajouter une propriété :
3. Nettoyer et reconstruire le projet

Une fois cela fait, les noms de paramètres Java d’origine doivent être présents dans les API liées par un projet de liaison Java. 

> [!NOTE]
> Il ya beaucoup de variance dans la sortie JavaDoc. Lla. La chaîne d’outils de liaison JAR ne prend pas en charge chaque permutation possible et, par conséquent, certains paramètres peuvent ne pas être correctement nommés.

## <a name="summary"></a>Récapitulatif

Cet article couvrait l’utilisation de Javadoc dans un projet de liaison Java pour fournir des noms de paramètres de sens pour les API liées. 
