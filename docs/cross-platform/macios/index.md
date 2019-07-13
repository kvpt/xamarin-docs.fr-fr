---
title: Plateforme Apple (iOS et Mac)
description: 'Ce document décrit les différentes rubriques relatives au développement Xamarin.iOS et Xamarin.Mac : code de partage, l’API unifiée, liaison Objective-C bibliothèques, des références natives, les types natifs et bien plus encore.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: c30d70d8a36c0e5a9b9ff6ddc74710dec4fb86a4
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864400"
---
# <a name="apple-platform-ios-and-mac"></a>Plateforme Apple (iOS et Mac)

## <a name="code-sharing"></a>Partage de code

Pour les éléments de votre code qui n’ont à aucun élément d’interface utilisateur la meilleure façon de partager le code entre iOS et Mac est toujours l’utilisation de [bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md).

Pour le code qui doit effectuer certaines tâches d’interface utilisateur et encore, que vous souhaitez partager, vous devez utiliser [les projets partagés](~/cross-platform/app-fundamentals/shared-projects.md) qui vous permettent de placer du code à partager dans un seul projet et l’avez compilé avec Mac et iOS lorsque référencé.

## <a name="unified-apiunifiedindexmd"></a>[API unifiée](unified/index.md)

L’API unifiée pour les projets iOS et Mac utilise les mêmes espaces de noms pour les infrastructures afin que le même fichier de code peut être utilisé sur les deux plateformes, transparente de partage de code. Il permet également pour les versions 32 et 64 bits. L’API unifiée est la valeur par défaut du modèle depuis début 2015 et est recommandée pour tous les nouveaux projets - *uniquement* projets d’API unifiée peuvent être soumis à l’App Store.

### <a name="classic-apis"></a>API classique

> [!NOTE]
> **Désapprobation de profil classique :** Lors de l’ajout de nouvelles plateformes dans Xamarin.iOS, nous avons commencé à déconseiller progressivement des fonctionnalités à partir du profil classique (monotouch.dll). Par exemple, l’option non-NRC (nouvelle-ref-count) a été supprimée. NRC a toujours été activé pour unifiée de toutes les applications (c'est-à-dire non NRC n’a jamais été une option) et n’a aucun problème connu. Les versions futures supprimera la possibilité d’utiliser Boehm en tant que le garbage collector. C’était également une option jamais disponible pour les applications unifiées. La suppression complète de prise en charge classique est planifiée pour l’automne 2016 avec la version de Xamarin.iOS 10.0.

Les d’origine (non-unifiée) Xamarin.iOS et Xamarin.Mac APIs apportées partage de code plus difficile, car les infrastructures natives avaient soit `MonoTouch.` ou `MonoMac.` préfixes d’espace de noms.  Nous vous avons fourni certains espaces de noms vide qui permet aux développeurs de partager du code en ajoutant `using` instructions qui font référence les espaces de noms MonoMac et MonoTouch sur le même fichier, mais cela a été quelques inconvénients. L’API classique doit uniquement continuer à être utilisé dans les applications héritées qui sont distribuées en interne (la mise à niveau vers l’API unifiée est recommandée).


### <a name="updating-from-classic-to-the-unified-api"></a>La mise à jour d’un déploiement classique à l’API unifiée

Il existe des instructions détaillées pour la mise à jour n’importe quelle application du modèle Classic vers l’API unifiée.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Liaison de bibliothèques Objective-C](binding/index.md)

Xamarin vous permet d’importer des bibliothèques natives dans vos applications avec des liaisons. Cette section explique :

- fonctionnement des liaisons,
- comment créer manuellement un projet de liaison qui vous permet de placer du code de Objective-C dans Xamarin, et
- comment utiliser notre **objectif Sharpie** outil pour aider à automatiser le processus.

## <a name="native-referencesnative-referencesmd"></a>[Références natives](native-references.md)

## <a name="macios-native-typesnativetypesmd"></a>[Types natifs Mac/iOS](nativetypes.md)

Pour prendre en charge le code de bit 32 et 64 en toute transparence à partir de C# et F#, nous avons introduit de nouveaux types de données.   Apprenez-en plus ici.

## <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Création d’applications de bit 32 et 64](32-and-64/index.md)

Ce que vous devez savoir pour prendre en charge pour les applications 32 et 64 bits.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Utilisation de types natifs dans des applications multiplateformes](native-types-cross-platform.md)

Cet article traite de l’utilisation de la nouvelle iOS types natifs des API unifiée (`nint`, `nuint`, `nfloat`) dans une application multiplateforme où le code est partagé avec des appareils non-iOS comme Android ou les systèmes d’exploitation de Windows Phone.
Il fournit un aperçu de quand les types natifs doivent être utilisées et offre plusieurs solutions possibles pour les cas où le nouveau type doit être utilisé avec code multiplateforme.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Pile HttpClient et sélecteur d’implémentation de SSL/TLS](http-stack.md)

Le nouveau sélecteur de pile HttpClient détermine quelle implémentation de HttpClient à utiliser dans votre application Xamarin.iOS, Xamarin.tvOS et Xamarin.Mac. Vous pouvez maintenant passer à une implémentation qui utilise d’iOS, de tvOS ou transports natifs à la place du système d’exploitation (`NSUrlSession` ou `CFNetwork` selon le système d’exploitation).

SSL (Secure Socket Layer) et son successeur, TLS (Transport Layer Security), prennent en charge pour HTTP et d’autres connexions réseau via `System.Net.Security.SslStream`. La nouvelle option de build de mise en œuvre de SSL/TLS bascule entre la pile TLS de Mono et celle alimentée par la pile TLS d’Apple présente dans Mac et iOS.
