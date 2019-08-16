---
title: Portage de Java vers C# pour Xamarin. Android
description: Une troisième option pour l’utilisation de Java dans une application Xamarin. Android est le portage du code source C#Java vers.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: c6627f585326848c5221729ca94071b00651c59e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511176"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Portage de Java vers C# pour Xamarin. Android

Cette approche peut présenter un intérêt pour les organisations qui:

- **Échangez des piles de technologies de Java C#à.**
- **Doit conserver un C# et une version Java du même produit.**
- **Vous souhaitez disposer d’une version .NET d’une bibliothèque Java populaire.**

Il existe deux façons de porter du code Java C#vers. La première consiste à déplacer le code manuellement. Cela implique des développeurs expérimentés qui comprennent .NET et Java et connaissent les idiomes appropriés pour chaque langue. Cette approche est la plus appropriée pour les petites quantités de code, ou pour les organisations qui souhaitent passer de Java à C#.

La deuxième méthode de Portage consiste à essayer et à automatiser le processus à l’aide d’un convertisseur de code, tel qu’une [netteté](https://github.com/mono/sharpen). La [netteté](https://github.com/mono/sharpen) est un convertisseur Open source de versant qui a été utilisé à l’origine pour porter le code C#pour *db4o* de Java vers. db4o est une base de données orientée objet qui versant développée en Java, puis déplacée vers .NET. L’utilisation d’un convertisseur de code peut s’avérer utile pour les projets qui doivent exister dans les deux langages et qui nécessitent une certaine parité entre les deux.

Un exemple de cas où un outil de conversion de code automatisé est pertinent peut être consulté dans le projet [NGIT](https://github.com/mono/ngit) .
NGIT est un port du projet Java [jgit](http://eclipse.org/).
Jgit lui-même est une implémentation Java du système de gestion du code source [git](http://git-scm.com/) . Pour générer C# du code à partir de Java, les programmeurs NGIT utilisent un système automatisé personnalisé pour extraire le code Java à partir de jgit, appliquer certains correctifs pour prendre en charge le processus de conversion C# , puis exécuter Sharp, ce qui génère le code. Cela permet au projet NGIT de tirer parti du travail continu en cours qui est effectué sur jgit.

Il y a souvent une quantité de travail non négligeable impliquée dans l’amorçage d’un outil de conversion de code automatisé, ce qui peut s’avérer un obstacle à l’utilisation. Dans de nombreux cas, il peut être plus simple et plus facile de porter C# Java à la main.

## <a name="related-links"></a>Liens connexes

- [Outil de conversion de la netteté](https://github.com/mono/sharpen)
