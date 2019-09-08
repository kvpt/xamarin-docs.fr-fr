---
title: Attribution de noms aux paramètres avec Javadoc
description: Cet article explique comment récupérer des noms de paramètres dans un projet de liaison Java à l’aide de Javadoc généré à partir du projet Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: fa1fb0656384455322a2d0a3562fc0ee3ca52397
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757602"
---
# <a name="naming-parameters-with-javadoc"></a>Attribution de noms aux paramètres avec Javadoc

_Cet article explique comment récupérer des noms de paramètres dans un projet de liaison Java à l’aide de Javadoc généré à partir du projet Java._

## <a name="overview"></a>Présentation

Lors de la liaison d’une bibliothèque Java existante, certaines métadonnées relatives à l’API liée sont perdues. En particulier, les noms des paramètres des méthodes. Les noms de paramètres s' `p0`affichent sous la forme, `p1`, etc. Cela est dû au fait `.class` que les fichiers Java ne conservent pas les noms de paramètres utilisés dans le code source Java. 

Un projet de liaison Java Xamarin. Android peut fournir les noms de paramètre s’il a accès au code HTML Javadoc à partir de la bibliothèque d’origine. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Intégration du code HTML Javadoc dans un projet de liaison Java

L’intégration du code HTML Javadoc dans un projet de liaison Java est un processus manuel constitué des étapes suivantes : 

1. Télécharger javadoc pour la bibliothèque
2. Modifiez le `.csproj` fichier et ajoutez une `<JavaDocPaths>` propriété :
3. Nettoyer et régénérer le projet

Une fois cette opération effectuée, les noms de paramètres Java d’origine doivent être présents dans les API liées par un projet de liaison Java. 

> [!NOTE]
> Il y a beaucoup de variance dans la sortie JavaDoc. La. La liaison JAR chaîne d’outils ne prend pas en charge chaque permutation possible et, par conséquent, certains paramètres peuvent ne pas être correctement nommés.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment utiliser Javadoc dans un projet de liaison Java pour fournir des noms de paramètres significatifs pour les API liées. 
