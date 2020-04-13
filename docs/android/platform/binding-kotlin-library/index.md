---
title: Bind Bibliothèques Android Kotlin
description: Ce document décrit comment créer des liaisons Cmd au code Kotlin, permettant de consommer des bibliothèques indigènes dans une application Xamarin.Android.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292661"
---
# <a name="bind-android-kotlin-libraries"></a>Bind Bibliothèques Android Kotlin

La plate-forme Android, ainsi que ses langues et son outillage natifs, est en constante évolution et il ya beaucoup de bibliothèques tierces qui ont été développés en utilisant les dernières offres. Maximiser la réutilisation du code et des composants est l’un des principaux objectifs du développement interplateforme. La capacité de réutiliser les composants construits avec Kotlin est devenu de plus en plus important pour les développeurs Xamarin que leur popularité parmi les développeurs continue de croître. Vous connaissez peut-être déjà le processus de liaison des bibliothèques [Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) régulières. Une documentation supplémentaire est maintenant disponible décrivant le processus de [liaison d’une bibliothèque Kotlin](walkthrough.md), de sorte qu’ils sont consommables par une application Xamarin de la même manière. Le but de ce document est de décrire une approche de haut niveau pour créer une liaison Kotlin pour Xamarin.

## <a name="high-level-approach"></a>Approche de haut niveau

Avec Xamarin, vous pouvez lier n’importe quelle bibliothèque autochtone tierce à consommer par une application Xamarin. Kotlin est la nouvelle langue et la création de reliure pour les bibliothèques construites avec cette langue nécessite quelques étapes supplémentaires et l’outillage. Cette approche comporte les quatre étapes suivantes :

1. Construire la bibliothèque autochtone
1. Préparer les métadonnées Xamarin, qui permettent à l’outillage Xamarin de générer des classes de C
1. Construire une bibliothèque de liaison Xamarin à l’aide de la bibliothèque indigène et des métadonnées
1. Consommer la bibliothèque de liaison Xamarin dans une application Xamarin

Les sections suivantes décrivent ces étapes avec des détails supplémentaires.

### <a name="build-the-native-library"></a>Construire la bibliothèque autochtone

La première étape consiste à obtenir une bibliothèque Kotlin native (paquet AAR, qui est une archive Android). Vous pouvez soit le demander directement auprès d’un fournisseur, soit le construire vous-même.

### <a name="prepare-the-xamarin-metadata"></a>Préparer les métadonnées Xamarin

La deuxième étape consiste à préparer le fichier de transformation des métadonnées, qui sera utilisé par les outils Xamarin pour générer les classes C respectives. Dans le meilleur des cas, ce fichier pourrait être vide lorsque toutes les classes sont découvertes et générées par les outils Xamarin. Dans certains cas, la transformation des métadonnées doit être appliquée pour générer un code CMD correct et/ou souhaité. Dans de nombreux cas, un démonteur AAR, tel que [Java Decompiler (JD)](http://java-decompiler.github.io/), doit être utilisé pour identifier les dépendances cachées et les classes indésirables que vous souhaitez exclure de la liste finale des classes C à générer. Les métadonnées finales devraient représenter l’interface publique avec laquelle l’application Xamarin.Android de référence interagira avec.

### <a name="build-a-xamarinandroid-binding-library"></a>Construire une bibliothèque de liaison Xamarin.Android

La troisième étape consiste à créer un projet spécial - une bibliothèque de liaison Xamarin.Android. Il comprend les bibliothèques Kotlin comme références autochtones et la transformation des métadonnées définies dans l’étape précédente. Au moment de la rédaction, un projet distinct de bibliothèque de liaison Android est nécessaire pour chaque paquet AAR référencé. La Bibliothèque de liaison doit ajouter le paquet [Xamarin.Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) afin de soutenir la bibliothèque standard de Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Consommer la bibliothèque de liaison Xamarin

La quatrième et dernière étape consiste à faire référence à la bibliothèque de liaison dans une application Xamarin.Android. L’ajout d’une référence à la bibliothèque de liaison Xamarin.Android permet à votre application Xamarin d’utiliser les classes Kotlin exposées à partir de cet emballage.

## <a name="walkthrough"></a>Procédure pas à pas

L’approche ci-dessus décrit les étapes de haut niveau nécessaires pour créer une liaison Kotlin pour Xamarin. Il y a beaucoup d’étapes de niveau inférieur impliquées et d’autres détails à prendre en considération lors de la préparation de ces liaisons dans la pratique, y compris l’adaptation aux changements dans les outils et les langues autochtones. L’objectif est de vous aider à mieux comprendre ce concept et les étapes de haut niveau de ce processus. Pour un guide détaillé étape par étape, consultez la documentation sur la liaison [Xamarin Kotlin.](walkthrough.md)

## <a name="related-links"></a>Liens connexes

- [Android Studio](https://developer.android.com/studio)
- [Gradle Installation](https://gradle.org/install/)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Décompiler Java](http://java-decompiler.github.io/)
- [Bibliothèque BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Bibliothèque Java contraignante](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [Xpath](https://www.w3.org/TR/xpath/)
- [Java Binding Metadata](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Exemple de dépôt de projet](https://github.com/xamcat/xamarin-binding-kotlin-framework)
