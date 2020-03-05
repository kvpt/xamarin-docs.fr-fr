---
title: Historique des versions objectivie
description: Ce document décrit l’historique des versions d’objective Sharp, l’outil utilisé pour automatiser la création C# de liaisons au code Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: f60be3f7dc14749f5cd58d5228c17fa85282cd78
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291629"
---
# <a name="objective-sharpie-release-history"></a>Historique des versions objectivie

## <a name="34-october-11-2017"></a>3,4 (11 octobre 2017)

[Télécharger v 3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Prise en charge de Xcode 9 : iOS 11, macOS 10,13, tvOS 11 et Watchos 4
* Les problèmes liés à SIMD et tgmath doivent maintenant être résolus
* La télémétrie a été entièrement supprimée

## <a name="33-august-3-2016"></a>3,3 (3 août 2016)

[Télécharger v 3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Prise en charge de Xcode 8 bêta 4, iOS 10, macOS 10,12, tvOS 10 et Watchos 3.
* Mise à jour vers la dernière version de Clang Master (2016-08-02)
* [Conserver les options d’envoi de télémétrie](https://twitter.com/Symbiatch/status/760373403878559744) de `sharpie pod bind` à `sharpie bind`.

## <a name="32-june-14-2016"></a>3,2 (14 juin, 2016)

[Télécharger v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Prise en charge de Xcode 8 bêta 1, iOS 10, macOS 10,12, tvOS 10 et Watchos 3.

## <a name="31-may-31-2016"></a>3,1 (31 mai, 2016)

[Télécharger v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Prise en charge de CocoaPods 1,0
* Amélioration de la fiabilité de la liaison CocoaPods en générant d’abord un `.framework` natif, puis en liant
* Copier les `.framework` CocoaPods et la définition de liaison dans un répertoire `Binding` pour faciliter l’intégration avec les projets de liaison Xamarin. iOS et Xamarin. Mac

## <a name="30-october-5-2015"></a>3,0 (5 octobre 2015)

[Télécharger v 3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Prise en charge des nouvelles fonctionnalités de langage Objective-C, notamment les génériques légers et la possibilité de valeur null, comme introduit dans Xcode 7
* Prise en charge des derniers kits de développement logiciel (SDK) iOS et Mac.
* Prise en charge de l’analyse et du développement de projets Xcode ! Vous pouvez maintenant passer un projet XCode complet à la finesse objective et il fera de son mieux pour déterminer la bonne chose à faire (par exemple, `sharpie bind Project.xcodeproj -sdk ios`).
* Prise en charge de [CocoaPods](https://cocoapods.org) ! Vous pouvez désormais configurer, créer et lier des CocoaPods directement à partir de la finesse objective (par exemple, `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Lors de la liaison d’infrastructures, les modules Clang sont activés si le Framework les prend en charge, ce qui aboutit à l’analyse la plus correcte d’une infrastructure, étant donné que la structure de l’infrastructure est définie par son `module.modulemap`.
* Pour les projets Xcode qui génèrent un produit Framework, analysez ce produit au lieu des cibles de produit intermédiaires en tant que cibles non-Framework dans un projet XCode peut encore avoir des ambiguïtés qui ne peuvent pas être résolues automatiquement.

## <a name="216-march-17-2015"></a>2.1.6 (17 mars, 2015)

* Liaison d’expression d’opérateur binaire fixe : la partie gauche de l’expression n’a pas été correctement remplacée par la droite (par exemple, `1 << 0` n’a pas été correctement liée en tant que `0 << 1`). Merci à Adam Kemp de noter cela !
* Correction d’un problème avec `NSInteger` et `NSUInteger` lié en tant que `int` et `uint` au lieu de `nint` et `nuint` sur i386 ; `-DNS_BUILD_32_LIKE_64` est maintenant transmis à Clang pour que l’analyse `objc/NSObjCRuntime.h` fonctionner comme prévu sur i386.
* L’architecture par défaut des kits de développement logiciel (SDK) Mac OS X (par exemple, `-sdk macosx10.10`) est désormais x86_64 à la place de i386. par conséquent, `-arch` peut être omis, sauf si vous souhaitez remplacer la valeur par défaut.

## <a name="210-march-15-2015"></a>2.1.0 (15 mars, 2015)

[Télécharger v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [BXC # 27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Assurez-vous que `using ObjCRuntime;` est produit lors de l’utilisation de `ArgumentSemantic`.
* [BXC # 27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Assurez-vous que `using System.Runtime.InteropServices;` est produit lors de l’utilisation de `DllImport`.
* [BXC # 27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): `DllImport` par défaut pour charger les symboles à partir de `__Internal`.
* [BXC # 27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): ignore les déclarations de conteneur objective-C déclarées en avant.
* [BXC # 27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): liez les types de protocole avec une seule qualification comme interfaces concrètes (`id<Foo>` en tant que `Foo` au lieu de `Foundation.NSObject<Foo>`).
* [BXC # 28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): liez `UInt32`, `UInt64`et `Int64` littéraux comme `Int32` pour supprimer les suffixes `u` et/ou `uL` lorsque les valeurs peuvent être contenues en toute sécurité dans `Int32`.
* [BXC # 28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): corriger le mappage de noms enum lorsque le nom natif d’origine commence par un préfixe `k`.
* les expressions `sizeof` C dont le type d’argument n’est C# pas mappé à un type primitif sont évaluées dans Clang et liées en tant que littéral C#d’entier pour éviter la génération d’une valeur non valide.
* Corriger la syntaxe objective-C pour les propriétés dont le type est un bloc (le code Objective-C apparaît dans les commentaires ci-dessus les déclarations liées).
* Liez les types dépassés comme leur type d’origine (`int[]` distants de `int*` lors de l’analyse sémantique dans Clang, mais liez-le en tant que `int[]` à la place de l’écriture d’origine).

Merci beaucoup à Dave Dunkin pour signaler un grand nombre des bogues résolus dans cette version de point.

## <a name="200-march-9-2015"></a>2.0.0:9 mars 2015

[Télécharger v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

La version objective Sharpy 2,0 est une version majeure qui intègre un pilote et un analyseur Clang améliorés, ainsi qu’un nouveau moteur de liaison basé sur NRefactory. Ces composants améliorés fournissent des liaisons _bien_ meilleures, en particulier autour de la liaison de type. De nombreuses autres améliorations ont été apportées, qui sont internes à un grand nombre d’objectifs, qui générera de nombreuses fonctionnalités visibles par l’utilisateur dans les futures versions.

L’objectif Sharpy 2,0 est basé sur Clang 3.6.1.

### <a name="type-binding-improvements"></a>Améliorations de la liaison de type

* Les blocs objective-C sont désormais pris en charge. Cela comprend les blocs anonymes/inline et les blocs nommés via `typedef`. Les blocs anonymes sont liés en tant que `System.Action` ou `System.Func` délégués, tandis que les blocs nommés sont liés en tant que types de `delegate` portant un nom fort.

* Il existe une méthode heuristique de dénomination améliorée pour les énumérations anonymes qui sont immédiatement précédées d’un `typedef` la résolution en un type intégral intégré tel que `long` ou `int`.

* Les pointeurs C sont maintenant liés C# en tant que pointeurs de `unsafe` au lieu de `System.IntPtr`. Cela donne plus de clarté à la liaison lorsque vous souhaitez transformer les paramètres du pointeur en `out` ou `ref` paramètres. Il n’est pas possible de toujours déterminer si un paramètre doit être `out` ou `ref`. par conséquent, le pointeur est conservé dans la liaison pour permettre un audit plus facile.

* Une exception à la liaison de pointeur ci-dessus se produit lorsqu’un pointeur à 2 rangs vers un objet objective-C est rencontré comme paramètre. Dans ces cas-là, la Convention est prédominante et le paramètre est lié en tant que `out` (par exemple, `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Vérifier l’attribut

Vous constaterez souvent que les liaisons produites par la finesse objective seront désormais annotées avec l’attribut `[Verify]`. Ces attributs indiquent que vous devez _vérifier_ que la netteté objective a effectué la bonne chose en comparant la liaison avec la déclaration d’origine c/objective-c (qui sera fournie dans un commentaire au-dessus de la déclaration liée).

La vérification est _recommandée_ pour toutes les déclarations liées, mais elle est très probablement _requise_ pour les déclarations annotées avec l’attribut `[Verify]`. En effet, dans de nombreux cas, il n’y a pas assez de métadonnées dans le code source natif d’origine pour déduire comment créer au mieux une liaison. Vous devrez peut-être référencer la documentation ou des commentaires de code dans les fichiers d’en-tête pour prendre la meilleure décision de liaison.

Pour plus d’informations, consultez la documentation sur les [attributs](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

### <a name="other-notable-improvements"></a>Autres améliorations notables

* les instructions `using` sont désormais générées en fonction des types liés. Par exemple, si un `NSURL` a été lié, une instruction `using Foundation;` sera également générée.

* les déclarations `struct` et `union` sont désormais liées, à l’aide de la astuce `[FieldOffset]` pour les unions.

* Les valeurs enum avec des initialiseurs d’expression constantes sont désormais correctement liées ; l’expression complète est traduite C#en.

* Les méthodes et les blocs variadiques sont maintenant liés.

* Les frameworks sont désormais pris en charge via l’option `-framework`. Pour plus d’informations, consultez la documentation sur la [liaison de frameworks natifs](~/cross-platform/macios/binding/objective-sharpie/index.md) .

* Le code source objective-C sera maintenant détecté automatiquement, ce qui devrait éliminer la nécessité de passer `-ObjC` ou `-xobjective-c` manuellement à Clang.

* L’utilisation du module Clang (`@import`) est maintenant détectée automatiquement, ce qui évite de devoir transmettre `-fmodules` manuellement à Clang pour les bibliothèques qui utilisent la nouvelle prise en charge de module dans Clang.

* La API unifiée Xamarin est désormais la cible de liaison par défaut ; Utilisez l’option `-classic` pour cibler le API classique 32 bits uniquement.

### <a name="notable-bug-fixes"></a>Correctifs de bogues notables

* Corriger `instancetype` liaison quand elle est utilisée dans une catégorie objective-C
* Nom complet des catégories objective-C
* Préfixez les protocoles objective-C avec `I` (par exemple, `INSCopying` au lieu de `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35:21 décembre 2014

[Télécharger v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correctifs de bogues mineurs.

## <a name="111-december-15-2014"></a>1.1.1:15 décembre 2014

[Télécharger v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 était la première version majeure après 1,5 ans d’utilisation et de développement internes à Xamarin, à la suite de la préversion initiale de la finesse objective en avril 2013. Cette version est la première à être considérée comme stable et utilisable pour une grande variété de bibliothèques natives, avec un nouveau backend Clang.
