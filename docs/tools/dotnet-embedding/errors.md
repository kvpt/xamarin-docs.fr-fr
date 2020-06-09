---
title: Erreurs d’incorporation .NET
description: Ce document décrit les erreurs générées par l’incorporation .NET. Les erreurs sont répertoriées par code et reçoivent une description pour faciliter la résolution des problèmes.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 9d3dff0de912455a49e9f7a66aae2640a99db746
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573909"
---
# <a name="net-embedding-errors"></a>Erreurs d’incorporation .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx : messages d’erreur de liaison

Par exemple, paramètres, environnement

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000"></a>

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000 : erreur inattendue. Veuillez remplir un rapport de bogue àhttps://github.com/mono/Embeddinator-4000/issues

Une condition d’erreur inattendue s’est produite. Signalez [un problème](https://github.com/mono/Embeddinator-4000/issues) avec autant d’informations que possible, notamment :

* Journaux de génération complets avec un niveau de détail maximal
* Un cas de test minimal qui reproduit l’erreur
* Toutes les informations sur la version

Pour obtenir des informations de version exactes, la méthode la plus simple consiste à utiliser le menu **Xamarin Studio** , **à propos de Xamarin Studio** élément, afficher le bouton **Détails** , puis copier/coller les informations de version (vous pouvez utiliser le bouton **copier les informations** ).

<a name="EM0001"></a>

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001 : impossible de créer le répertoire de sortie`X`

Le nom de répertoire spécifié par `-o=DIR` n’existe pas et n’a pas pu être créé. Il peut s’agir d’un nom non valide pour le système de fichiers.

<a name="EM0002"></a>

### <a name="em0002-option-x-is-not-supported"></a>EM0002 : l’option `X` n’est pas prise en charge

L’outil ne prend pas en charge l’option `X` . Il est possible qu’une autre version de l’outil la prenne en charge ou que l' `X` option ne s’applique pas dans cet environnement.

<a name="EM0003"></a>

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003 : la plateforme `X` n’est pas valide.

L’outil ne prend pas en charge la plateforme `X` . Il est possible qu’une autre version de l’outil la prenne en charge ou que la `X` plateforme ne s’applique pas dans cet environnement.

<a name="EM0004"></a>

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004 : la cible `X` n’est pas valide.

L’outil ne prend pas en charge la cible `X` . Il est possible qu’une autre version de l’outil la prenne en charge ou que la `X` cible ne s’applique pas dans cet environnement.

<a name="EM0005"></a>

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005 : la cible de compilation `X` n’est pas valide.

L’outil ne prend pas en charge la cible de compilation `X` . Il est possible qu’une autre version de l’outil la prenne en charge ou que la `X` cible de compilation ne s’applique pas dans cet environnement.

<a name="EM0006"></a>

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006 : impossible de trouver l’emplacement de Xcode.

L’outil n’a pas pu trouver l’emplacement Xcode actuellement sélectionné à l’aide de la `xcode-select -p` commande. Vérifiez que vous pouvez exécuter cette commande avec succès et qu’elle retourne l’emplacement Xcode correct.

<a name="EM0007"></a>

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007 : impossible d’obtenir la version du kit de développement logiciel (SDK) pour' {SDK} '.

L’outil n’a pas pu récupérer la version du SDK à l’aide de la `xcrun --show-sdk-version --sdk {sdk}` commande. Vérifiez que vous pouvez exécuter cette commande avec succès et qu’elle retourne la version du kit de développement logiciel (SDK).

<a name="EM0008"></a>

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008 : l’architecture « {Arch} » n’est pas valide pour « {Platform} ». Les architectures valides pour « {Platform} » sont : « {architectures} ».

L’architecture dans le message d’erreur n’est pas valide pour la plateforme ciblée. Vérifiez qu’une architecture valide est passée pour l' `--abi` option.

<a name="EM0009"></a>

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009 : la fonctionnalité `X` n’est pas implémentée actuellement par le générateur

Il s’agit d’un problème connu que nous envisageons de corriger dans une version ultérieure du générateur. Les contributions sont les bienvenues.

<a name="EM0010"></a>

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010 : impossible de fusionner les frameworks « {simulatorFramework} » et « {deviceFramework} », car le fichier « {file} » existe dans les deux.

L’outil n’a pas pu fusionner les frameworks mentionnés dans le message d’erreur, car il existe un fichier commun entre eux.

Cela peut indiquer un bogue dans l’incorporation .NET. Veuillez signaler un rapport de bogue dans à l' [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) aide d’un cas de test.

<a name="EM0011"></a>

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011 : l’assembly `X` n’existe pas.

L’outil n’a pas pu trouver l’assembly `X` spécifié dans les arguments.

<a name="EM0012"></a>

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012 : le nom de l’assembly `X` n’est pas unique

Plusieurs assemblys fournis ont le même nom interne et il n’est pas possible de les distinguer au moment de l’exécution.

La cause la plus probable est qu’un assembly est spécifié plusieurs fois dans les arguments de ligne de commande. Toutefois, un assembly renommé conserve son nom d’origine et plusieurs copies ne peuvent pas coexister.

<a name="EM0013"></a>

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013 : impossible de trouver l’assembly’X', référencé par’Y'.

L’outil n’a pas pu trouver l’assembly’X', référencé par l’assembly’Y'. Assurez-vous que tous les assemblys référencés se trouvent dans le même répertoire que l’assembly à lier.

<a name="EM0014"></a>

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014 : {Product} introuvable ({Product} {min_version} est requis).

La dépendance mentionnée dans le message d’erreur est introuvable sur le système.

<a name="EM0015"></a>

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015 : impossible de trouver une version valide de {Product} ({version} trouvée, mais au moins {min_version} est requis).

La dépendance mentionnée dans le message d’erreur a été trouvée sur le système, mais elle est trop ancienne. Effectuez une mise à jour vers une version plus récente.

<a name="EM0016"></a>

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016 : impossible de créer le lien symbolique' {file} '-> ' {target} ' : erreur {Number}

Impossible de créer le lien symbolique mentionné dans le message d’erreur.

<a name="EM0026"></a>

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 n’a pas pu analyser l’argument de ligne de commande’A' : *

La syntaxe fournie pour l’option de ligne de commande `A` n’a pas pu être analysée par l’outil. Consultez la documentation pour obtenir la syntaxe correcte.

<a name="EM0099"></a>

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099 : erreur interne *. Veuillez signaler un rapport de bogues avec un cas de test ( https://github.com/mono/Embeddinator-4000/issues) .

Ce message d’erreur est signalé en cas d’échec d’une vérification de cohérence interne dans l’incorporation .NET.

Cela indique un bogue dans l’incorporation .NET. Veuillez signaler un rapport de bogue dans à l' [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) aide d’un cas de test.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx : traitement du code

<a name="EM1010"></a>

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010 : le type `T` n’est pas généré, car `X` n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant que le type `T` sera ignoré (autrement dit, rien ne sera généré), car il utilise `X` , une fonctionnalité qui n’est pas prise en charge.

Remarque : les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1011"></a>

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011 : `T` le type n’est pas généré, car il n’a pas de code de marshaling avec un équivalent natif.

Il s’agit d’un **Avertissement** indiquant que le type `T` sera ignoré (autrement dit, rien ne sera généré), car il expose un élément du .NET Framework qui nécessite un marshaling supplémentaire.

Remarque : cela peut être pris en charge, avec certaines limitations, dans une future version de l’outil.

<a name="EM1020"></a>

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020 : le constructeur `C` n’est pas généré, car le type de paramètre `T` n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant que le constructeur `C` sera ignoré (autrement dit, rien ne sera généré), car un paramètre de type `T` n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1021"></a>

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021 : `C` le constructeur a des valeurs par défaut pour lesquelles aucun wrapper n’est généré.

Il s’agit d’un **Avertissement** indiquant que les paramètres par défaut du constructeur ne `C` génèrent pas de code supplémentaire. La cause la plus courante est qu’une méthode existante a déjà la même signature. Par exemple, dans .NET, il est possible d’avoir :

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

Dans ces cas-là, seuls deux `init` sélecteurs générés seront créés, à la fois pour l’appel en mono, mais aucun wrapper n’existe pour ce dernier.

<a name="EM1030"></a>

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030 : la méthode `M` n’est pas générée car le type de retour `T` n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant que la méthode `M` sera ignorée (autrement dit, rien ne sera généré), car son type de retour `T` n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1031"></a>

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031 : la méthode `M` n’est pas générée car le type de paramètre `T` n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant que la méthode `M` sera ignorée (autrement dit, rien ne sera généré), car un paramètre de type `T` n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1032"></a>

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032 : `M` la méthode a des valeurs par défaut pour lesquelles aucun wrapper n’est généré.

Il s’agit d’un **Avertissement** indiquant que les paramètres par défaut de la méthode ne `M` génèrent pas de code supplémentaire. La cause la plus courante est qu’une méthode existante a déjà la même signature. Par exemple, dans .NET, il est possible d’avoir :

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

Dans ces cas-là, seuls deux `Increment` sélecteurs générés seront créés, à la fois pour l’appel en mono, mais aucun wrapper n’existe pour ce dernier.

<a name="EM1033"></a>

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033 : `M` la méthode n’est pas générée, car une autre méthode expose l’opérateur avec un nom convivial.

Il s’agit d’un **Avertissement** indiquant que la méthode `M` n’est pas générée, car une autre méthode expose l’opérateur avec un nom convivial. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034"></a>

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034 : la méthode `M` d’extension n’est pas générée dans une catégorie, car elle ne peut pas être créée sur le type primitif `T` . Une méthode statique normale a été générée.

Il s’agit d’un **Avertissement** indiquant qu’une méthode d’extension sur un type primivite (par exemple `System.Int32` ,) a été trouvée. En Objective-C, il n’est pas possible de créer des catégories sur un type primitif. Au lieu de cela, le générateur produira une méthode statique normale.

<a name="EM1040"></a>

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040 : la propriété `P` n’est pas générée car le type de paramètre `T` n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant que la propriété `P` sera ignorée (autrement dit, rien ne sera généré), car le type exposé `T` n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1041"></a>

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041 : les propriétés indexées sur `T` ne sont pas générées, car plusieurs propriétés indexées ne sont pas prises en charge.

Il s’agit d’un **Avertissement** indiquant que les propriétés indexées sur `T` seront ignorées (c’est-à-dire que rien ne sera généré) parce que plusieurs propriétés indexées ne sont pas prises en charge.

<a name="EM1050"></a>

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050 : le champ `F` n’est pas généré, car le type de champ `T` n’est pas pris en charge.

Il s’agit d’un **Avertissement** indiquant que le champ `F` sera ignoré (autrement dit, rien ne sera généré), car le type exposé `T` n’est pas pris en charge.

Il doit y avoir un avertissement antérieur indiquant pourquoi le type `T` n’est pas pris en charge.

Remarque : les fonctionnalités prises en charge vont évoluer avec les nouvelles versions de l’outil.

<a name="EM1051"></a>

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051 : `E` l’élément est généré à `F` la place, car son nom est en conflit avec un sélecteur objective-c important.

Il s’agit d’un **Avertissement** indiquant que l’élément est `E` généré comme `F` parce que son nom est en conflit avec un sélecteur objective-c important.

Les sélecteurs sur le [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) ont une signification importante en objective-c et doivent être remplacés avec précaution.

Remarque : la liste des sélecteurs réservés évoluera avec les nouvelles versions de l’outil.

<a name="EM1052"></a>

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052 : l’élément `E` n’est pas généré, son nom est en conflit avec d’autres éléments de la même classe.

Il s’agit d’un **Avertissement** indiquant que `E` l’élément n’est pas généré, car son nom est en conflit avec d’autres éléments de la même classe.

<a name="EM1053"></a>

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053 : la cible `E` n’est pas prise en charge pour Xamarin. iOS et Xamarin. Mac. Seule l' `framework` option est considérée comme prise en charge et doit être utilisée.

Il s’agit d’un **Avertissement** indiquant que la cible `E` est considérée comme non prise en charge pour les cas d’usage Xamarin. iOS et Xamarin. Mac. 

La consommation de bibliothèques d’incorporation .NET statiques ou dynamiques peut nécessiter des étapes ou des ajustements de travail supplémentaires et doit être évitée dans la plupart des cas d’usage.

Supprimez votre `--target` paramètre ou Pass à la `--target=framework` place.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx : génération de code

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
