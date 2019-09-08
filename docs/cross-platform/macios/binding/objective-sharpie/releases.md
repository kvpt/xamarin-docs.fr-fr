---
title: Historique des versions objectivie
description: Ce document décrit l’historique des versions d’objective Sharp, l’outil utilisé pour automatiser la création C# de liaisons au code Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: fa50ae16b69436936f0a7a8a5cf0aeaa54dfedfb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765673"
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
* [Persistance des options d’envoi de données de télémétrie](https://twitter.com/Symbiatch/status/760373403878559744) de `sharpie pod bind` à `sharpie bind`.

## <a name="32-june-14-2016"></a>3,2 (14 juin, 2016)

[Télécharger v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Prise en charge de Xcode 8 bêta 1, iOS 10, macOS 10,12, tvOS 10 et Watchos 3.

## <a name="31-may-31-2016"></a>3,1 (31 mai, 2016)

[Télécharger v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Prise en charge de CocoaPods 1,0
* Amélioration de la fiabilité de la liaison CocoaPods en `.framework` générant d’abord une liaison native, puis en liant
* Copier CocoaPods `.framework` et la définition de liaison `Binding` dans un répertoire pour faciliter l’intégration avec les projets de liaison Xamarin. iOS et Xamarin. Mac

## <a name="30-october-5-2015"></a>3,0 (5 octobre 2015)

[Télécharger v 3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Prise en charge des nouvelles fonctionnalités de langage Objective-C, notamment les génériques légers et la possibilité de valeur null, comme introduit dans Xcode 7
* Prise en charge des derniers kits de développement logiciel (SDK) iOS et Mac.
* Prise en charge de l’analyse et du développement de projets Xcode ! Vous pouvez maintenant passer un projet XCode complet à la finesse objective et il fera de son mieux pour déterminer la bonne chose à faire (par exemple `sharpie bind Project.xcodeproj -sdk ios`,).
* Prise en charge de [CocoaPods](https://cocoapods.org) ! Vous pouvez désormais configurer, générer et lier des CocoaPods directement à partir de la finesse objective `sharpie pod init ios AFNetworking && sharpie pod bind`(par exemple,).
* Lors de la liaison d’infrastructures, les modules Clang sont activés si le Framework les prend en charge, ce qui aboutit à l’analyse la plus correcte d’une infrastructure, car la `module.modulemap`structure de l’infrastructure est définie par son.
* Pour les projets Xcode qui génèrent un produit Framework, analysez ce produit au lieu des cibles de produit intermédiaires en tant que cibles non-Framework dans un projet XCode peut encore avoir des ambiguïtés qui ne peuvent pas être résolues automatiquement.

## <a name="216-march-17-2015"></a>2.1.6 (17 mars, 2015)

[Télécharger v 2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Liaison d’expression d’opérateur binaire fixe : la partie gauche de l’expression n’a pas été correctement remplacée par la droite (par exemple `1 << 0` , a été lié de manière incorrecte en tant que `0 << 1`). Merci à Adam Kemp de noter cela !
* Correction d’un problème `NSInteger` avec `NSUInteger` et lié en `int` tant `uint` que et `nint` à `nuint` la place de et sur i386 ; est maintenant transmis à Clang pour que l' `objc/NSObjCRuntime.h` analyse fonctionne comme prévu sur i386. `-DNS_BUILD_32_LIKE_64`
* L’architecture par défaut des kits de développement logiciel `-sdk macosx10.10`(SDK) Mac OS X (par exemple,) `-arch` est maintenant x86_64 au lieu de i386. vous pouvez donc l’omettre, sauf si vous souhaitez remplacer la valeur par défaut.

## <a name="210-march-15-2015"></a>2.1.0 (15 mars, 2015)

[Télécharger v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [BXC # 27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Vérifiez `using ObjCRuntime;` que est produit `ArgumentSemantic` lorsque est utilisé.
* [BXC # 27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Vérifiez `using System.Runtime.InteropServices;` que est produit `DllImport` lorsque est utilisé.
* [BXC # 27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): Par `DllImport` défaut, pour charger `__Internal`les symboles à partir de.
* [BXC # 27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): Ignore les déclarations de conteneur objective-C déclarées avant.
* [BXC # 27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): Liez les types de protocole avec une seule qualification comme interfaces`id<Foo>` concrètes ( `Foundation.NSObject<Foo>`comme `Foo` au lieu de).
* [BXC # 28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): Liez `UInt32`, `Int32` `u` `Int32`et les littéraux comme pour supprimer les suffixes et `uL` /ou lorsque les valeurs peuvent être contenues en toute sécurité dans. `Int64` `UInt64`
* [BXC # 28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): Corriger le mappage de noms enum lorsque le nom natif d' `k` origine commence par un préfixe.
* `sizeof`Les expressions C dont le type d’argument n’est C# pas mappé à un type primitif sont évaluées dans Clang et liées en tant que littéral C#d’entier pour éviter la génération d’une valeur non valide.
* Corriger la syntaxe objective-C pour les propriétés dont le type est un bloc (le code Objective-C apparaît dans les commentaires ci-dessus les déclarations liées).
* Liez les types dépassés comme leur type`int[]` d’origine ( `int*` s’atténue à au cours de l’analyse sémantique dans Clang, mais liez- `int[]` le en tant qu’écriture comme écrit à la place).

Merci beaucoup à Dave Dunkin pour signaler un grand nombre des bogues résolus dans cette version de point.

## <a name="200-march-9-2015"></a>2.0.0 9 mars, 2015

[Télécharger v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

La version objective Sharpy 2,0 est une version majeure qui intègre un pilote et un analyseur Clang améliorés, ainsi qu’un nouveau moteur de liaison basé sur NRefactory. Ces composants améliorés fournissent des liaisons _bien_ meilleures, en particulier autour de la liaison de type. De nombreuses autres améliorations ont été apportées, qui sont internes à un grand nombre d’objectifs, qui générera de nombreuses fonctionnalités visibles par l’utilisateur dans les futures versions.

L’objectif Sharpy 2,0 est basé sur Clang 3.6.1.

### <a name="type-binding-improvements"></a>Améliorations de la liaison de type

* Les blocs objective-C sont désormais pris en charge. Cela comprend les blocs anonymes/inline et les `typedef`blocs nommés via. Les blocs anonymes sont liés `System.Action` en `System.Func` tant que délégués ou, tandis que les blocs nommés `delegate` sont liés en tant que types portant un nom fort.

* Il existe une méthode heuristique de dénomination améliorée pour les énumérations anonymes qui sont immédiatement `typedef` précédées d’une résolution en un type `long` intégral `int`intégré tel que ou.

* Les pointeurs C sont maintenant liés C# `unsafe` comme pointeurs au `System.IntPtr`lieu de. Cela donne plus de clarté à la liaison lorsque vous souhaitez peut-être transformer les paramètres du `out` pointeur `ref` en paramètres ou. Il n’est pas possible de toujours déterminer si un paramètre doit `out` être `ref`ou. par conséquent, le pointeur est conservé dans la liaison pour permettre un audit plus facile.

* Une exception à la liaison de pointeur ci-dessus se produit lorsqu’un pointeur à 2 rangs vers un objet objective-C est rencontré comme paramètre. Dans ces cas-là, la Convention est prédominante et le paramètre est `out` lié en tant `NSError **error` que `out NSError error`(par exemple, →).

### <a name="verify-attribute"></a>Vérifier l’attribut

Vous constaterez souvent que les liaisons produites par la finesse objective seront désormais annotées avec `[Verify]` l’attribut. Ces attributs indiquent que vous devez _vérifier_ que la netteté objective a effectué la bonne chose en comparant la liaison avec la déclaration d’origine c/objective-c (qui sera fournie dans un commentaire au-dessus de la déclaration liée).

La vérification est _recommandée_ pour toutes les déclarations liées, mais elle est très probablement _requise_ pour les déclarations `[Verify]` annotées avec l’attribut. En effet, dans de nombreux cas, il n’y a pas assez de métadonnées dans le code source natif d’origine pour déduire comment créer au mieux une liaison. Vous devrez peut-être référencer la documentation ou des commentaires de code dans les fichiers d’en-tête pour prendre la meilleure décision de liaison.

Pour plus d’informations, consultez la documentation sur les [attributs](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

### <a name="other-notable-improvements"></a>Autres améliorations notables

* `using`les instructions sont désormais générées en fonction des types liés. Par exemple, si un `NSURL` a été lié, `using Foundation;` une instruction est également générée.

* `struct`les `union` déclarations et sont maintenant liées, à l' `[FieldOffset]` aide de la astuce pour les unions.

* Les valeurs enum avec des initialiseurs d’expression constantes sont désormais correctement liées ; l’expression complète est traduite C#en.

* Les méthodes et les blocs variadiques sont maintenant liés.

* Les frameworks sont désormais pris en `-framework` charge via l’option. Pour plus d’informations, consultez la documentation sur la [liaison de frameworks natifs](~/cross-platform/macios/binding/objective-sharpie/index.md) .

* Le code source objective-C sera maintenant détecté automatiquement, ce qui devrait éliminer la nécessité de `-ObjC` passer `-xobjective-c` ou de Clang manuellement.

* L’utilisation du module`@import`Clang () est maintenant détectée automatiquement, ce qui évite d’avoir `-fmodules` à passer à Clang manuellement pour les bibliothèques qui utilisent la nouvelle prise en charge de module dans Clang.

* La API unifiée Xamarin est désormais la cible de liaison par défaut ; Utilisez l' `-classic` option pour cibler le API classique 32 bits uniquement.

### <a name="notable-bug-fixes"></a>Correctifs de bogues notables

* Corriger `instancetype` la liaison quand elle est utilisée dans une catégorie objective-C
* Nom complet des catégories objective-C
* Préfixez les protocoles objective-C `INSCopying` avec `I` ( `NSCopying`par exemple, au lieu de)

## <a name="1135-december-21-2014"></a>1.1.35: 21 décembre 2014

[Télécharger v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correctifs de bogues mineurs.

## <a name="111-december-15-2014"></a>1.1.1 Le 15 décembre 2014

[Télécharger v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 était la première version majeure après 1,5 ans d’utilisation et de développement internes à Xamarin, à la suite de la préversion initiale de la finesse objective en avril 2013. Cette version est la première à être considérée comme stable et utilisable pour une grande variété de bibliothèques natives, avec un nouveau backend Clang.
