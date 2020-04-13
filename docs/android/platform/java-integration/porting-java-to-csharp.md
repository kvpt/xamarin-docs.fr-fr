---
title: Porting Java à C pour Xamarin.Android
description: Une troisième option pour l’utilisation de Java dans une application Xamarin.Android est de porter le code source Java à C.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027191"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Porting Java à C pour Xamarin.Android

Cette approche peut intéresser les organisations qui :

- **Les piles de technologie de commutation de Java à C.**
- **Doit maintenir un C et une version Java du même produit.**
- **Souhaitez avoir une version .NET d’une bibliothèque Java populaire.**

Il y a deux façons de porter le code Java à C. La première façon est de porter le code manuellement. Cela implique des développeurs qualifiés qui comprennent à la fois .NET et Java et sont familiers avec les idiomes appropriés pour chaque langue. Cette approche est la plus logique pour de petites quantités de code, ou pour les organisations qui souhaitent s’éloigner complètement de Java à C.

La deuxième méthodologie de portage est d’essayer d’automatiser le processus en utilisant un convertisseur de code, tel que [Sharpen](https://github.com/mono/sharpen). [Sharpen](https://github.com/mono/sharpen) est un convertisseur open source de Versant qui a été utilisé à l’origine pour le port du code pour *db4o* de Java à C . db4o est une base de données orientée objet que Versant a développé à Java, puis porté à .NET. L’utilisation d’un convertisseur de code peut avoir un sens pour les projets qui doivent exister dans les deux langues et qui nécessitent une certaine parité entre les deux.

Un exemple de quand un outil automatisé de conversion de code est logique peut être vu dans le projet [ngit.](https://github.com/mono/ngit)
Ngit est un port du projet Java [jgit](https://eclipse.org/).
Jgit lui-même est une implémentation Java du système de gestion de code source [Git.](https://git-scm.com/) Pour générer du code Cmd à partir de Java, les programmeurs ngit utilisent un système automatisé personnalisé pour extraire le code Java de jgit, appliquer quelques correctifs pour accueillir le processus de conversion, puis exécuter Sharpen, qui génère le code C. Cela permet au projet ngit de bénéficier du travail continu et continu qui se fait sur jgit.

Il ya souvent une quantité non triviale de travail impliqué dans bootstrapping un outil automatisé de conversion de code, et cela peut s’avérer être un obstacle à l’utilisation. Dans de nombreux cas, il peut être plus simple et plus facile de port Java à C à la main.

## <a name="related-links"></a>Liens connexes

- [Outil de conversion Sharpen](https://github.com/mono/sharpen)
