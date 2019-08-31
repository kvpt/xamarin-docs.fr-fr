---
title: Erreurs d’incorporation .NET
description: Ce document décrit les erreurs générées par l’incorporation .NET. Les erreurs sont répertoriées par code et reçoivent une description pour faciliter la résolution des problèmes.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: lobrien
ms.author: laobri
ms.date: 04/11/2018
ms.openlocfilehash: e5f998105202a4143c94748efad5faae0212acba
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198778"
---
# <a name="net-embedding-errors"></a>Erreurs d’incorporation .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: Liaison de messages d’erreur

Par exemple, paramètres, environnement

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Erreur inattendue-veuillez remplir un rapport de bogue à https://github.com/mono/Embeddinator-4000/issues

Une condition d’erreur inattendue s’est produite. Signalez [un problème](https://github.com/mono/Embeddinator-4000/issues) avec autant d’informations que possible, notamment:

* Journaux de génération complets, avec un niveau de détail maximal
* Un cas de test minimal qui reproduit l’erreur
* Toutes les informations sur les versions

Pour obtenir des informations de version exactes, la méthode la plus simple consiste à utiliser le menu **Xamarin Studio** , **à propos de Xamarin Studio** élément, afficher le bouton **Détails** et copier/coller la version informations (vous pouvez utiliser le bouton **copier les informations** ).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: Impossible de créer le répertoire de sortie`X`

Le nom de répertoire spécifié `-o=DIR` par n’existe pas et n’a pas pu être créé. Il peut s’agir d’un nom non valide pour le système de fichiers.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: L' `X` option n’est pas prise en charge

L’outil ne prend pas en charge `X`l’option. Il est possible qu’une autre version de l’outil la prenne en charge ou qu’elle ne s’applique pas dans cet environnement.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: La plateforme `X` n’est pas valide.

L’outil ne prend pas en charge `X`la plateforme. Il est possible qu’une autre version de l’outil la prenne en charge ou qu’elle ne s’applique pas dans cet environnement.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: La cible `X` n’est pas valide.

L’outil ne prend pas en charge `X`la cible. Il est possible qu’une autre version de l’outil la prenne en charge ou qu’elle ne s’applique pas dans cet environnement.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: La cible `X` de compilation n’est pas valide.

L’outil ne prend pas en charge la `X`cible de compilation. Il est possible qu’une autre version de l’outil la prenne en charge ou qu’elle ne s’applique pas dans cet environnement.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: Impossible de trouver l’emplacement de Xcode.

L’outil n’a pas pu trouver l’emplacement Xcode actuellement sélectionné `xcode-select -p` à l’aide de la commande. Vérifiez que cette commande fonctionne correctement et retourne l’emplacement Xcode correct.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: Impossible d’obtenir la version du kit de développement logiciel (SDK) pour «{Sdk}».

L’outil n’a pas pu récupérer la version du `xcrun --show-sdk-version --sdk {sdk}` SDK à l’aide de la commande. Vérifiez que cette commande a été exécutée correctement et retourne la version du kit de développement logiciel (SDK).

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: L’architecture «{Arch}» n’est pas valide pour {Platform}. Les architectures valides pour {Platform} sont: «{architectures}».

L’architecture dans le message d’erreur n’est pas valide pour la plateforme ciblée. Vérifiez que l’option--Abi reçoit une architecture valide.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: La fonctionnalité `X` n’est pas implémentée actuellement par le générateur

Il s’agit d’un problème connu que nous envisageons de corriger dans une version ultérieure du générateur. Les contributions sont les bienvenues.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: Impossible de fusionner les frameworks «{simulatorFramework}» et «{deviceFramework}», car le fichier «{file}» existe dans les deux.

L’outil n’a pas pu fusionner les frameworks mentionnés dans le message d’erreur, car il existe un fichier commun entre eux.

Cela peut indiquer un bogue dans l’incorporation .NET. Veuillez signaler un rapport de bogue dans [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) à l’aide d’un cas de test.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: L’assembly `X` n’existe pas.

L’outil n’a pas pu trouver `X` l’assembly spécifié dans les arguments.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: Le nom `X` de l’assembly n’est pas unique

Plusieurs assemblys fournis ont le même nom interne et il n’est pas possible de les distinguer au moment de l’exécution.

La cause la plus probable est qu’un assembly est spécifié plusieurs fois sur les arguments de ligne de commande. Toutefois, un assembly renommé conserve toujours son nom d’origine et plusieurs copies ne peuvent pas coexister.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: Impossible de trouver l’assembly’X', référencé par’Y'.

L’outil n’a pas pu trouver l’assembly’X', référencé par l’assembly’Y'. Assurez-vous que tous les assemblys référencés se trouvent dans le même répertoire que l’assembly à lier.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014: {Product} introuvable ({Product} {min_version} requis).

La dépendance mentionnée dans le message d’erreur est introuvable sur le système.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015: Impossible de trouver une version valide de {Product} ({version} trouvée, mais au moins {min_version} est requis).

La dépendance mentionnée dans le message d’erreur a été détectée sur le système, mais elle est trop ancienne. Effectuez une mise à jour vers une version plus récente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: Impossible de créer le lien symbolique' {file} '-> ' {target} ': erreur {Number}

Impossible de créer le lien symbolique mentionné dans le message d’erreur.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 n’a pas pu analyser l’argument de ligne de commande’A': *

La syntaxe fournie pour l’option `A` de ligne de commande n’a pas pu être analysée par l’outil. Elle est probablement incorrecte. consultez la documentation ou l’aide pour obtenir la syntaxe correcte.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: Erreur interne *. Veuillez signaler un rapport de bogues avec un cas https://github.com/mono/Embeddinator-4000/issues) de test (.

Ce message d’erreur est signalé en cas d’échec d’une vérification de cohérence interne dans l’incorporation .NET.

Cela indique un bogue dans l’incorporation .NET. Veuillez signaler un rapport de bogue dans [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) à l’aide d’un cas de test.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: Traitement du code

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: Le `T` type n’est pas `X` généré, car n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant `T` que le type sera ignoré (autrement dit, rien ne sera généré) `X`, car il utilise, une fonctionnalité qui n’est pas prise en charge.

Remarque : Les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: Le `T` type n’est pas généré, car il n’a pas de code de marshaling avec un équivalent natif.

Il s’agit d’un **Avertissement** indiquant `T` que le type sera ignoré (autrement dit, rien ne sera généré), car il expose un élément du .NET Framework qui nécessite un marshaling supplémentaire.

Remarque : Cela peut être pris en charge, avec certaines limitations, dans une future version de l’outil.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: Le `C` constructeur n’est pas généré, car `T` le type de paramètre n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant `C` que le constructeur sera ignoré (autrement dit, rien ne sera généré), car `T` un paramètre de type n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : Les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: Le `C` constructeur a des valeurs par défaut pour lesquelles aucun wrapper n’est généré.

Il s’agit d’un **Avertissement** indiquant que les paramètres `C` par défaut du constructeur ne génèrent pas de code supplémentaire. La cause la plus courante est qu’une méthode existante a déjà la même signature. Par exemple, dans .net, il est possible d’avoir:

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

Dans ce cas, seuls deux `init` sélecteurs générés sont créés, à la fois, l’appel à mono, mais aucun wrapper pour le plus tard n’existe.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: La `M` méthode n’est pas générée car `T` le type de retour n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant `M` que la méthode sera ignorée (autrement dit, rien ne sera généré), `T` car son type de retour n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : Les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: La `M` méthode n’est pas générée car le `T` type de paramètre n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant `M` que la méthode sera ignorée (autrement dit, rien ne sera généré), `T` car un paramètre de type n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : Les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: La `M` méthode a des valeurs par défaut pour lesquelles aucun wrapper n’est généré.

Il s’agit d’un **Avertissement** indiquant que les paramètres `M` par défaut de la méthode ne génèrent pas de code supplémentaire. La cause la plus courante est qu’une méthode existante a déjà la même signature. Par exemple, dans .net, il est possible d’avoir:

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

Dans ce cas, seuls deux `increment` sélecteurs générés sont créés, à la fois, l’appel à mono, mais aucun wrapper pour le plus tard n’existe.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: La `M` méthode n’est pas générée, car une autre méthode expose l’opérateur avec un nom convivial.

Il s’agit d’un **Avertissement** indiquant `M` que la méthode n’est pas générée, car une autre méthode expose l’opérateur avec un nom convivial. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: La méthode `M` d’extension n’est pas générée dans une catégorie, car elle ne peut `T`pas être créée sur le type primitif. Une méthode statique normale a été générée.

Il s’agit d’un **Avertissement** indiquant qu’une méthode d’extension sur un type `System.Int32`primivite (par exemple,) a été trouvée. En Objective-C, il n’est pas possible de créer des catégories sur le type primitif. Au lieu de cela, le générateur produira une méthode statique normale.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: La `P` propriété n’est pas générée car le `T` type de paramètre n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant `P` que la propriété sera ignorée (autrement dit, rien ne sera généré) `T` , car le type exposé n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : Les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: Les propriétés indexées `T` sur ne sont pas générées, car plusieurs propriétés indexées ne sont pas prises en charge.

Il s’agit d’un **Avertissement** indiquant que les propriétés `T` indexées sur seront ignorées (c’est-à-dire que rien ne sera généré) parce que plusieurs propriétés indexées ne sont pas prises en charge.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: Le `F` champ n’est pas généré, car `T` le type de champ n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant `F` que le champ sera ignoré (autrement dit, rien ne sera généré), `T` car le type exposé n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : Les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: L' `E` élément est généré à `F` la place comme parce que son nom est en conflit avec un sélecteur objective-c important.

Il s’agit d’un **Avertissement** indiquant `E` que l’élément sera généré `F` à la place, car son nom est en conflit avec un sélecteur objective-c important.

Les sélecteurs sur le [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) ont une signification importante en objective-c et doivent être remplacés avec précaution.

Remarque : La liste des sélecteurs réservés évoluera avec les nouvelles versions de l’outil.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: L' `E` élément n’est pas généré, son nom est en conflit avec d’autres éléments de la même classe.

Il s’agit d’un avertissement `E` indiquant que l’élément n’est pas généré, car son nom est en conflit avec d’autres éléments de la même classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: La `E` cible n’est pas prise en charge pour Xamarin. iOS et Xamarin. Mac. Seule l' `framework` option est considérée comme prise en charge et doit être utilisée.

Il s’agit d’un avertissement `E` indiquant que la cible est considérée comme non prise en charge pour les cas d’usage Xamarin. iOS et Xamarin. Mac. 

La consommation de bibliothèques d’incorporation .NET statiques ou dynamiques peut nécessiter des étapes de travail supplémentaires ou des ajustements, et doit être évitée dans la plupart des cas d’usage.

Supprimez votre `--target` paramètre ou Pass `--target=framework` à la place.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Génération de code

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
