---
title: Lier des bibliothèques Android Kotlin
description: Ce document explique comment créer des liaisons C# vers du code Kotlin, ce qui permet de consommer des bibliothèques natives dans une application Xamarin. Android.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 4fcb36f76ff2714a7a783b716abfe44de51b2855
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454141"
---
# <a name="bind-android-kotlin-libraries"></a>Lier des bibliothèques Android Kotlin

> [!IMPORTANT]
> Nous étudions actuellement l’utilisation de la liaison personnalisée sur la plateforme Xamarin. Veuillez suivre [**ce questionnaire**](https://www.surveymonkey.com/r/KKBHNLT) pour informer les futurs efforts de développement.

La plateforme Android, ainsi que ses langages et outils natifs, évoluent constamment et il existe de nombreuses bibliothèques tierces qui ont été développées à l’aide des dernières offres. L’optimisation du code et de la réutilisation des composants est l’un des objectifs clés du développement multiplateforme. La possibilité de réutiliser des composants créés avec Kotlin est devenue de plus en plus importante pour les développeurs Xamarin, car leur popularité entre les développeurs continue de croître. Vous êtes peut-être déjà familiarisé avec le processus de liaison de bibliothèques [java](../binding-java-library/index.md) normales. Une documentation supplémentaire est désormais disponible décrivant le processus de [liaison d’une bibliothèque Kotlin](walkthrough.md), afin qu’ils soient consommables par une application Xamarin de la même manière. L’objectif de ce document est de décrire une approche de haut niveau pour créer une liaison Kotlin pour Xamarin.

## <a name="high-level-approach"></a>Approche de haut niveau

Avec Xamarin, vous pouvez lier toutes les bibliothèques natives tierces pour qu’elles soient consommables par une application Xamarin. Kotlin est le nouveau langage et la création d’une liaison pour les bibliothèques générées avec ce langage nécessite des étapes et des outils supplémentaires. Cette approche implique les quatre étapes suivantes :

1. Générer la bibliothèque Native
1. Préparer les métadonnées Xamarin, ce qui permet aux outils Xamarin de générer des classes C#
1. Créer une bibliothèque de liaisons Xamarin à l’aide de la bibliothèque native et des métadonnées
1. Utilisation de la bibliothèque de liaisons Xamarin dans une application Xamarin

Les sections suivantes décrivent ces étapes avec des détails supplémentaires.

### <a name="build-the-native-library"></a>Générer la bibliothèque Native

La première étape consiste à obtenir une bibliothèque Kotlin Native (package AAR, qui est une archive Android). Vous pouvez la demander directement auprès d’un fournisseur ou la créer vous-même.

### <a name="prepare-the-xamarin-metadata"></a>Préparer les métadonnées Xamarin

La deuxième étape consiste à préparer le fichier de transformation des métadonnées, qui sera utilisé par les outils Xamarin pour générer les classes C# respectives. Dans le meilleur des cas, ce fichier peut être vide, où toutes les classes sont découvertes et générées par les outils Xamarin. Dans certains cas, la transformation de métadonnées doit être appliquée pour générer le code C# correct et/ou souhaité. Dans de nombreux cas, un désassembleur AAR, tel que [java décompilateur (JD)](http://java-decompiler.github.io/), doit être utilisé pour identifier les dépendances masquées et les classes indésirables que vous souhaitez exclure de la liste finale des classes C# à générer. Les métadonnées finales doivent représenter l’interface publique dans laquelle l’application de référencement Xamarin. Android interagit avec.

### <a name="build-a-xamarinandroid-binding-library"></a>Créer une bibliothèque de liaisons Xamarin. Android

La troisième étape consiste à créer une bibliothèque de liaisons Xamarin. Android spéciale. Il comprend les bibliothèques Kotlin comme références natives et la transformation de métadonnées définie à l’étape précédente. Au moment de l’écriture, un projet de bibliothèque de liaison Android distinct est requis pour chaque package AAR référencé. La bibliothèque de liaisons doit ajouter le package [Xamarin. Kotlin. stdlib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) afin de prendre en charge la bibliothèque standard Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Utiliser la bibliothèque de liaisons Xamarin

La quatrième et dernière étape consiste à faire référence à la bibliothèque de liaisons dans une application Xamarin. Android. L’ajout d’une référence à la bibliothèque de liaisons Xamarin. Android permet à votre application Xamarin d’utiliser les classes Kotlin exposées à partir de ce package.

## <a name="walkthrough"></a>Procédure pas à pas

L’approche ci-dessus présente les étapes de haut niveau requises pour créer une liaison Kotlin pour Xamarin. Il y a de nombreuses étapes de niveau inférieur impliquées et d’autres informations à prendre en compte lors de la préparation de ces liaisons dans la pratique, y compris l’adaptation aux modifications des outils et des langages natifs. L’objectif est de vous aider à mieux comprendre ce concept et les étapes principales impliquées dans ce processus. Pour obtenir un guide pas à pas détaillé, reportez-vous à la documentation relative à la [liaison Xamarin Kotlin](walkthrough.md) .

## <a name="related-links"></a>Liens connexes

- [Android Studio](https://developer.android.com/studio)
- [Installation de Gradle](https://gradle.org/install/)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Décompilateur Java](http://java-decompiler.github.io/)
- [Bibliothèque BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Liaison de la bibliothèque Java](../binding-java-library/index.md)
- [XPath](https://www.w3.org/TR/xpath/)
- [Métadonnées de liaison Java](../binding-java-library/customizing-bindings/java-bindings-metadata.md)
- [Xamarin. Kotlin. StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Exemple de référentiel de projet](https://github.com/xamcat/xamarin-binding-kotlin-framework)