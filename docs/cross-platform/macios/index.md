---
title: Plateforme Apple (iOS et Mac)
description: 'Ce document décrit les différentes rubriques relatives au développement Xamarin. iOS et Xamarin. Mac : le partage de code, le API unifiée, la liaison de bibliothèques objective-C, les références natives, les types natifs, et bien plus encore.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 76e883ba42ed898188a646a8a43cf1e3f123a7eb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015601"
---
# <a name="apple-platform-ios-and-mac"></a>Plateforme Apple (iOS et Mac)

## <a name="code-sharing"></a>Partage de code

Pour les éléments de votre code qui n’ont pas d’éléments d’interface utilisateur, la meilleure façon de partager du code entre iOS et Mac est toujours l’utilisation de [bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md).

Pour le code qui doit faire du travail de l’interface utilisateur, mais que vous souhaitez partager, vous devez utiliser des [projets partagés](~/cross-platform/app-fundamentals/shared-projects.md) qui vous permettent de placer le code à partager dans un projet unique et de le compiler avec Mac et iOS lorsqu’il est référencé.

## <a name="unified-apiunifiedindexmd"></a>[API unifiée](unified/index.md)

La API unifiée pour les projets iOS et Mac utilise les mêmes espaces de noms pour les frameworks afin que le même fichier de code puisse être utilisé sur les deux plateformes, pour un partage de code transparent. Il permet également de générer des versions 32 et 64 bits. La API unifiée a été la valeur par défaut du modèle depuis le 2015, et est recommandée pour tous les nouveaux projets. *seuls* les projets API unifiée peuvent être envoyés à l’App Store.

### <a name="classic-apis"></a>API classiques

> [!NOTE]
> **Désapprobation du profil classique :** À mesure que de nouvelles plates-formes sont ajoutées dans Xamarin. iOS, nous commençons à déprécier progressivement les fonctionnalités du profil classique (MonoTouch. dll). Par exemple, l’option non NRC (New-Ref-Count) a été supprimée. NRC a toujours été activé pour toutes les applications unifiées (par exemple, non-NRC n’a jamais été une option) et n’a pas de problèmes connus. Dans les versions ultérieures, vous supprimerez l’option d’utilisation de Boehm comme garbage collector. Il s’agissait également d’une option jamais disponible pour les applications unifiées. La suppression complète de la prise en charge classique est prévue pour l’automne 2016 avec la version de Xamarin. iOS 10,0.

Les API Xamarin. iOS et Xamarin. Mac d’origine (non unifiées) ont rendu le partage de code plus difficile, car les frameworks natifs avaient soit des préfixes d’espace de noms `MonoTouch.`, soit `MonoMac.`.  Nous avons fourni des espaces de noms vides qui permettent aux développeurs de partager du code en ajoutant des instructions `using` qui font référence à la fois à des espaces de noms MonoMac et monotoucher sur le même fichier, mais cela était un peu insupportable. Le API classique doit continuer à être utilisé uniquement dans les applications héritées qui sont distribuées en interne (la mise à niveau vers le API unifiée est recommandée).

### <a name="updating-from-classic-to-the-unified-api"></a>Mise à jour de Classic vers le API unifiée

Des instructions détaillées sont disponibles pour la mise à jour de toutes les applications du Classic au API unifiée.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Liaison de bibliothèques Objective-C](binding/index.md)

Xamarin vous permet de placer des bibliothèques natives dans vos applications avec des liaisons. Cette section explique les éléments suivants :

- fonctionnement des liaisons,
- Comment créer manuellement un projet de liaison qui vous permet de placer du code Objective-C dans Xamarin, et
- Comment utiliser notre outil **sharpiste** pour faciliter l’automatisation du processus.

## <a name="native-referencesnative-referencesmd"></a>[Références natives](native-references.md)

## <a name="macios-native-typesnativetypesmd"></a>[Types natifs Mac/iOS](nativetypes.md)

Pour prendre en charge le code 32 et 64 bits de C# manière F#transparente à partir de et, nous introduisons de nouveaux types de données.   En savoir plus à leur sujet ici.

## <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Génération d’applications 32 et 64 bits](32-and-64/index.md)

Ce que vous devez savoir pour prendre en charge les applications 32 et 64 bits.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Utilisation de types natifs dans des applications multiplateformes](native-types-cross-platform.md)

Cet article aborde l’utilisation des nouveaux types natifs iOS API unifiée (`nint`, `nuint`, `nfloat`) dans une application multiplateforme dans laquelle le code est partagé avec des appareils non-iOS tels que des systèmes d’exploitation Android ou Windows Phone.
Il fournit des informations sur le moment où les types natifs doivent être utilisés et fournit plusieurs solutions possibles pour les cas où le nouveau type doit être utilisé avec du code multiplateforme.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Pile HttpClient et sélecteur d’implémentation de SSL/TLS](http-stack.md)

Le nouveau sélecteur de pile HttpClient contrôle l’implémentation HttpClient à utiliser dans vos applications Xamarin. iOS, Xamarin. tvOS et Xamarin. Mac. Vous pouvez maintenant basculer vers une implémentation qui utilise les transports natifs d’iOS, de tvOS ou de OS X (`NSUrlSession` ou `CFNetwork` selon le système d’exploitation).

SSL (Secure Socket Layer) et son successeur, TLS (Transport Layer Security), assurent la prise en charge de HTTP et d’autres connexions réseau via `System.Net.Security.SslStream`. La nouvelle option de génération d’implémentation SSL/TLS bascule entre la pile TLS de l’mono et l’autre alimentée par la pile TLS d’Apple présente dans Mac et iOS.
