---
title: Lier des bibliothèques iOS SWIFT
description: Ce document décrit comment créer C# des liaisons au code SWIFT, ce qui permet de consommer des bibliothèques natives et des CocoaPods dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 890EFCCA-A2A2-4561-88EA-30DE3041F61D
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 9a683f31016a9db4271e3909e421f27ef83c2080
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292734"
---
# <a name="bind-ios-swift-libraries"></a>Lier des bibliothèques iOS SWIFT

La plateforme iOS, ainsi que ses langages et outils natifs, évoluent constamment et il existe de nombreuses bibliothèques tierces qui ont été développées à l’aide des dernières offres. L’optimisation du code et de la réutilisation des composants est l’un des objectifs clés du développement multiplateforme. La possibilité de réutiliser des composants créés avec SWIFT est devenue de plus en plus importante pour les développeurs Xamarin, car leur popularité entre les développeurs continue de croître. Vous êtes peut-être déjà familiarisé avec le processus de liaison de bibliothèques [objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough) régulières. Une documentation supplémentaire est désormais disponible décrivant le processus de [liaison d’un Framework SWIFT](walkthrough.md), afin qu’ils soient consommables par une application Xamarin de la même manière. L’objectif de ce document est de décrire une approche de haut niveau pour créer une liaison SWIFT pour Xamarin.

## <a name="high-level-approach"></a>Approche de haut niveau

Avec Xamarin, vous pouvez lier toutes les bibliothèques natives tierces pour qu’elles soient consommables par une application Xamarin. SWIFT est le nouveau langage et la création d’une liaison pour les bibliothèques créées avec ce langage nécessite des étapes et des outils supplémentaires. Cette approche implique les quatre étapes suivantes :

1. Génération de la bibliothèque Native
1. Préparation des métadonnées Xamarin, ce qui permet aux outils Xamarin C# de générer des classes
1. Génération d’une bibliothèque de liaisons Xamarin à l’aide de la bibliothèque native et des métadonnées
1. Utilisation de la bibliothèque de liaisons Xamarin dans une application Xamarin

Les sections suivantes décrivent ces étapes avec des détails supplémentaires.

### <a name="build-the-native-library"></a>Générer la bibliothèque Native

La première étape consiste à avoir un Framework SWIFT natif prêt avec l’en-tête objective-C créé. Ce fichier est un en-tête généré automatiquement qui expose les classes, méthodes et champs SWIFT souhaités, ce qui les rend accessibles à C# la fois en Objective-C et au final par le biais d’une bibliothèque de liaisons Xamarin. Ce fichier se trouve dans le Framework sous le chemin d’accès suivant : **\<frameworkname >. Framework/headers/\<FrameworkName >-SWIFT. h**. Si l’interface exposée contient tous les membres requis, vous pouvez passer à l’étape suivante. Dans le cas contraire, d’autres étapes sont requises pour exposer ces membres. L’approche varie selon que vous avez accès au code source SWIFT Framework :

- Si vous avez accès au code, vous pouvez décorer le ou les membres SWIFT requis avec l’attribut `@objc` et appliquer quelques règles supplémentaires pour permettre aux outils de génération Xcode de savoir que ces membres doivent être exposés au monde objective-C et à l’en-tête.
- Si vous n’avez pas accès au code source, vous devez créer un Framework SWIFT proxy, qui encapsule l’infrastructure SWIFT d’origine et définit l’interface publique requise par votre application à l’aide de l’attribut `@objc`.

### <a name="prepare-the-xamarin-metadata"></a>Préparer les métadonnées Xamarin

La deuxième étape consiste à préparer les interfaces de définition d’API, qui sont utilisées par un projet C# de liaison pour générer des classes. Ces définitions peuvent être créées manuellement ou automatiquement par l’outil [objectivité objective](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) et le fichier d’en-tête **\<FrameworkName >-SWIFT. h** généré automatiquement ci-dessus. Une fois les métadonnées générées, elles doivent être vérifiées et validées manuellement.

### <a name="build-the-xamarinios-binding-library"></a>Générer la bibliothèque de liaisons Xamarin. iOS

La troisième étape consiste à créer une bibliothèque de liaisons Project-Xamarin. iOS spéciale. Elle fait référence aux frameworks et aux métadonnées préparées à l’étape précédente, ainsi que toutes les dépendances supplémentaires sur lesquelles repose l’infrastructure respective. Il gère également la liaison des frameworks natifs référencés avec l’application Xamarin. iOS consommatrice.

### <a name="consume-the-xamarin-binding-library"></a>Utiliser la bibliothèque de liaisons Xamarin

La quatrième et dernière étape consiste à faire référence à la bibliothèque de liaisons dans une application Xamarin. iOS. Il suffit d’activer l’utilisation de la bibliothèque native dans les applications Xamarin. iOS ciblant iOS 12,2 et versions ultérieures. Pour les applications ciblant une version antérieure, certaines étapes supplémentaires sont requises :

- Ajoutez des dépendances dylib SWIFT pour la prise en charge du Runtime. Depuis iOS 12,2 et Swift 5,1, le langage est devenu ABI (application binary interface) stable et compatible. C’est pourquoi toute application ciblant une version iOS inférieure doit inclure des dépendances dylibs SWIFT utilisées par l’infrastructure. Utilisez le [package NuGet SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) pour inclure automatiquement les dépendances dylib requises dans le package d’application résultant.
- Ajoutez le dossier **SwiftSupport** avec le dylibs signé, qui est validé par le AppStore pendant le processus de téléchargement. Le package doit être signé et distribué à AppStore Connect à l’aide des outils Xcode. dans le cas contraire, il sera automatiquement rejeté.

## <a name="walkthrough"></a>Procédure pas à pas

L’approche ci-dessus présente les étapes de haut niveau nécessaires à la création d’une liaison SWIFT pour Xamarin. Il y a de nombreuses étapes de niveau inférieur impliquées et d’autres informations à prendre en compte lors de la préparation de ces liaisons dans la pratique, y compris l’adaptation aux modifications des outils et des langages natifs. L’objectif est de vous aider à mieux comprendre ce concept et les étapes principales impliquées dans ce processus. Pour obtenir un guide pas à pas détaillé, reportez-vous à la documentation relative à la [liaison SWIFT Xamarin](walkthrough.md) .

## <a name="related-links"></a>Liens connexes

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Vérification des métadonnées de la netteté](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [Liaison de l’infrastructure objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [Kit de développement logiciel (SDK) Gigya iOS (Framework SWIFT)](https://developers.gigya.com/display/GD/Swift+SDK)
- [SWIFT 5,1-stabilité ABI](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Automatisation Xamarin UITest](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [Configuration de Xamarin. iOS UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter Test Cloud](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Exemple de référentiel de projet](https://github.com/xamcat/xamarin-binding-swift-framework)
