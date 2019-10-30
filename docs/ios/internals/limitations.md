---
title: Limitations de Xamarin. iOS
description: Ce document décrit les limitations de Xamarin. iOS, la discussion sur les génériques, les sous-classes génériques de NSObjects, les appels P/Invoke dans les objets génériques, et bien plus encore.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/09/2018
ms.openlocfilehash: 91513936a0223af0e4220154d0fe65ee0a599a4f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022306"
---
# <a name="limitations-of-xamarinios"></a>Limitations de Xamarin. iOS

Étant donné que les applications qui utilisent Xamarin. iOS sont compilées en code statique, il n’est pas possible d’utiliser des fonctionnalités qui requièrent la génération de code au moment de l’exécution.

Il s’agit des limitations de Xamarin. iOS comparées à l’ordinateur de bureau mono :

 <a name="Limited_Generics_Support" />

## <a name="limited-generics-support"></a>Prise en charge des génériques limités

Contrairement aux mono/. NET classiques, le code sur l’iPhone est compilé statiquement à l’avance au lieu d’être compilé à la demande par un compilateur JIT.

La technologie [AOA complète](https://www.mono-project.com/docs/advanced/aot/#full-aot) de mono présente quelques limitations en ce qui concerne les génériques, ce qui est dû au fait que toutes les instanciations génériques possibles ne peuvent pas être déterminées en amont au moment de la compilation. Ce n’est pas un problème pour les runtimes .NET ou mono ordinaires, car le code est toujours compilé au moment de l’exécution à l’aide du compilateur juste-à-temps. Mais cela pose un défi pour un compilateur statique comme Xamarin. iOS.

Parmi les problèmes courants rencontrés par les développeurs, citons :

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />

### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Les sous-classes génériques de NSObjects sont limitées

Xamarin. iOS offre actuellement une prise en charge limitée pour la création de sous-classes génériques de la classe NSObject, telles que l’absence de prise en charge des méthodes génériques. À partir de la 7.2.1, l’utilisation de sous-classes génériques de NSObjects est possible, comme celle-ci :

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Alors que les sous-classes génériques de NSObjects sont possibles, il existe quelques limitations. Pour plus d’informations, consultez les [sous-classes génériques du document NSObject](~/ios/internals/api-design/nsobject-generics.md) .

 <a name="No_Dynamic_Code_Generation" />

## <a name="no-dynamic-code-generation"></a>Aucune génération de code dynamique

Étant donné que le noyau iOS empêche une application de générer du code dynamiquement, Xamarin. iOS ne prend en charge aucune forme de génération de code dynamique. Elles incluent notamment les suivantes :

- System. Reflection. Emit n’est pas disponible.
- Aucune prise en charge de System. Runtime. Remoting.
- Aucune prise en charge de la création dynamique de types (sans type. GetType ("MyType' 1")), bien que la recherche de types existants (type. GetType ("System. String") par exemple, fonctionne parfaitement).
- Les rappels inverses doivent être inscrits auprès du Runtime au moment de la compilation.

 <a name="System.Reflection.Emit" />

### <a name="systemreflectionemit"></a>System.Reflection.Emit

L’absence de System. Reflection. L' **émission** signifie qu’aucun code dépendant de la génération du code d’exécution ne fonctionnera. Cela comprend des éléments tels que :

- Le Dynamic Language Runtime.
- Tous les langages reposant sur le Dynamic Language Runtime.
- La valeur TransparentProxy de communication à distance ou tout autre qui obligerait le runtime à générer du code dynamiquement.

  > [!IMPORTANT]
  > Ne confondez pas la **réflexion. Emit** avec **réflexion**. La réflexion. Emit concerne la génération dynamique du code et l’compilées et la compilation du code en code natif. En raison des limitations relatives à iOS (aucune compilation JIT), cela n’est pas pris en charge.

Mais la totalité de l’API de réflexion, y compris type. GetType (« someClass »), la liste des méthodes, la liste des propriétés, la récupération des attributs et des valeurs fonctionne parfaitement.

### <a name="using-delegates-to-call-native-functions"></a>Utilisation de délégués pour appeler des fonctions natives

Pour appeler une fonction native par le C# biais d’un délégué, la déclaration du délégué doit être décorée avec l’un des attributs suivants :

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (par défaut, car il est multiplateforme et compatible avec .NET standard 1.1 +)
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

L’échec de la fourniture de l’un de ces attributs entraînera une erreur d’exécution, par exemple :

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```

 <a name="Reverse_Callbacks" />

### <a name="reverse-callbacks"></a>Rappels inverses

En mono standard, il est possible de C# passer des instances de délégué au code non managé à la place d’un pointeur de fonction. Le Runtime transforme généralement ces pointeurs fonction en un petit thunk qui permet au code non managé de rappeler le code managé.

Dans mono, ces ponts sont implémentés par le compilateur juste-à-temps. Lorsque vous utilisez le compilateur à l’avance requis par l’iPhone, il existe deux limitations importantes à ce stade :

- Vous devez marquer toutes vos méthodes de rappel avec [MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)
- Les méthodes doivent être des méthodes statiques, il n’existe aucune prise en charge des méthodes d’instance.

<a name="No_Remoting" />

## <a name="no-remoting"></a>Aucune communication à distance

La pile de communication à distance n’est pas disponible sur Xamarin. iOS.

 <a name="Runtime_Disabled_Features" />

## <a name="runtime-disabled-features"></a>Fonctionnalités désactivées du Runtime

Les fonctionnalités suivantes ont été désactivées dans le runtime iOS de mono :

- Profiler
- Réflexion. Emit
- Fonctionnalité Reflection. Emit. Save
- Liaisons COM
- Le moteur JIT
- Vérificateur de métadonnées (dans la mesure où il n’y a pas de JIT)

 <a name=".NET_API_Limitations" />

## <a name="net-api-limitations"></a>Limitations de l’API .NET

L’API .NET exposée est un sous-ensemble de l’infrastructure complète, car tous les éléments ne sont pas disponibles dans iOS. Consultez le Forum aux questions pour obtenir la [liste des assemblys actuellement pris en charge](~/cross-platform/internals/available-assemblies.md).

En particulier, le profil d’API utilisé par Xamarin. iOS n’inclut pas System. Configuration. il n’est donc pas possible d’utiliser des fichiers XML externes pour configurer le comportement du Runtime.
