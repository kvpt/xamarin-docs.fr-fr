---
title: Présentation de RenderScript
description: Ce guide présente RenderScript et explique comment utiliser l’API RenderScript intrinsèque dans une application Xamarin. Android qui cible le niveau d’API 17 ou une version ultérieure.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019794"
---
# <a name="an-introduction-to-renderscript"></a>Présentation de RenderScript

_Ce guide présente RenderScript et explique comment utiliser l’API RenderScript intrinsèque dans une application Xamarin. Android qui cible le niveau d’API 17 ou une version ultérieure._

## <a name="overview"></a>Overview

RenderScript est une infrastructure de programmation créée par Google dans le but d’améliorer les performances des applications Android qui nécessitent des ressources de calcul étendues. Il s’agit d’une API de niveau faible et hautes performances basée sur [C99](https://en.wikipedia.org/wiki/C99). Étant donné qu’il s’agit d’une API de bas niveau qui s’exécute sur des UC, des GPU ou des DSP, RenderScript est bien adapté aux applications Android qui peuvent avoir besoin d’effectuer les opérations suivantes :

- Graphiques
- Traitement des images
- Chiffrement
- Traitement des signaux
- Routines mathématiques

RenderScript utilisera `clang` et compilera les scripts dans le code LLVM octet qui est regroupé dans le APK. Lorsque l’application est exécutée pour la première fois, le code d’octet LLVM est compilé en code machine pour les processeurs sur l’appareil. Cette architecture permet à une application Android d’exploiter les avantages du code machine sans que les développeurs aient eux-mêmes à les écrire pour chaque processeur de l’appareil.

Il existe deux composants à une routine RenderScript :

1. **Le runtime Renderscript** &ndash; il s’agit des API natives qui sont chargées d’exécuter le RenderScript. Cela comprend tous les Renderscripts écrits pour l’application.

2. **Les wrappers managés de l’infrastructure android &ndash; les** classes managées qui permettent à une application Android de contrôler et d’interagir avec le runtime et les scripts RenderScript. En plus des classes fournies par le Framework pour contrôler le runtime RenderScript, Android chaîne d’outils examine le code source RenderScript et génère des classes wrapper gérées pour une utilisation par l’application Android.

Le diagramme suivant illustre la relation entre ces composants :

![Diagramme illustrant la façon dont l’infrastructure Android interagit avec le runtime RenderScript](renderscript-images/renderscript-01.png)

Il existe trois concepts importants pour l’utilisation de Renderscripts dans une application Android :

1. **Contexte** &ndash; une API gérée fournie par le Android SDK qui alloue des ressources à RenderScript et permet à l’application Android de transmettre et de recevoir des données à partir du RenderScript.

2. **Un _noyau de calcul_**  &ndash; également appelé noyau _ou noyau_ _racine_ . il s’agit d’une routine qui effectue le travail. Le noyau est très similaire à une fonction C. Il s’agit d’une routine parallèles qui est exécutée sur toutes les données de la mémoire allouée.

3. La **mémoire Allouée** &ndash; données sont transmises vers et à partir d’un noyau via une _[allocation](xref:Android.Renderscripts.Allocation)_ . Un noyau peut avoir une seule entrée et/ou une allocation de sortie.

L’espace de noms [Android. Renderscripts](xref:Android.Renderscripts) contient les classes permettant d’interagir avec le runtime RenderScript. En particulier, la classe [`Renderscript`](xref:Android.Renderscripts.RenderScript) gère le cycle de vie et les ressources du moteur RenderScript. L’application Android doit initialiser un ou plusieurs [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
. Une allocation est une API managée chargée de l’allocation et de l’accès à la mémoire partagée entre l’application Android et le runtime RenderScript. En règle générale, une allocation est créée pour l’entrée, et éventuellement une autre allocation est créée pour contenir la sortie du noyau. Le moteur d’exécution RenderScript et les classes wrapper managées associées gèrent l’accès à la mémoire détenue par les allocations. il n’est donc pas nécessaire qu’un développeur d’applications Android effectue des tâches supplémentaires.

Une allocation contient un ou plusieurs [Android. Renderscripts. Elements](xref:Android.Renderscripts.Element).
Les éléments sont un type spécialisé qui décrivent les données dans chaque allocation.
Les types d’éléments de l’allocation de sortie doivent correspondre aux types de l’élément d’entrée. Lors de l’exécution, un RenderScript effectue une itération sur chaque élément de l’allocation d’entrée en parallèle et écrit les résultats dans l’allocation de sortie. Il existe deux types d’éléments :

- le **type simple** &ndash; conceptuellement est le même qu’un type de données C, `float` ou un `char`.

- Le **type complexe** &ndash; ce type est similaire à un `struct`C.

Le moteur RenderScript effectue une vérification de l’exécution pour s’assurer que les éléments de chaque allocation sont compatibles avec ce qui est requis par le noyau. Si le type de données des éléments de l’allocation ne correspond pas au type de données attendu par le noyau, une exception est levée.

Tous les noyaux RenderScript seront encapsulés par un type qui est un descendant de l' [`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
. La classe `Script` est utilisée pour définir les paramètres d’un RenderScript, définir le `Allocations`approprié et exécuter RenderScript. Il existe deux sous-classes `Script` dans le Android SDK :

- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; certaines tâches RenderScript les plus courantes sont regroupées dans la Android SDK et sont accessibles par une sous-classe de la classe [ScriptIntrinsic](xref:Android.Renderscripts.ScriptIntrinsic) . Il n’est pas nécessaire pour un développeur de prendre des mesures supplémentaires pour utiliser ces scripts dans leur application, car ils sont déjà fournis.

- **`ScriptC_XXXXX`** &ndash; également appelées _scripts utilisateur_, il s’agit de scripts écrits par les développeurs et empaquetés dans apk. Au moment de la compilation, le chaîne d’outils Android génère des classes wrapper managées qui permettent d’utiliser les scripts dans l’application Android.
  Le nom de ces classes générées est le nom du fichier RenderScript, préfixé avec `ScriptC_`. L’écriture et l’incorporation de scripts utilisateur n’est pas officiellement prise en charge par Xamarin. Android et dépasse le cadre de ce guide.

Parmi ces deux types, seul le `StringIntrinsic` est pris en charge par Xamarin. Android. Ce guide explique comment utiliser des scripts intrinsèques dans une application Xamarin. Android.

## <a name="requirements"></a>Configuration requise

Ce guide concerne les applications Xamarin. Android qui ciblent le niveau d’API 17 ou une version ultérieure. L’utilisation de _scripts utilisateur_ n’est pas abordée dans ce guide.

La [bibliothèque de prise en charge Xamarin. Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) reporte les API RenderScript intrinsèques pour les applications qui ciblent des versions antérieures du Android SDK. L’ajout de ce package à un projet Xamarin. Android doit permettre aux applications qui ciblent des versions antérieures du Android SDK de tirer parti des scripts intrinsèques.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Utilisation de Renderscripts intrinsèques dans Xamarin. Android

Les scripts intrinsèques sont un excellent moyen d’effectuer des tâches de calcul IntensifLes avec une quantité minimale de code supplémentaire. Ils ont été réglés pour offrir des performances optimales sur une grande section transversale des appareils.
Il n’est pas rare qu’un script intrinsèque s’exécute 10 fois plus rapidement que le code managé et 2 fois trois fois plus qu’une implémentation C personnalisée. La plupart des scénarios de traitement classiques sont couverts par les scripts intrinsèques. Cette liste de scripts intrinsèques décrit les scripts actuels dans Xamarin. Android :

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; convertit le format RVB en RVBA à l’aide d’une table de recherche 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh les API RenderScript de performances à [Blas](http://www.netlib.org/blas/). Les BLAS (sous-programmes algébrique linéaires de base) sont des routines qui fournissent des blocs de construction standard pour effectuer des opérations de base de vecteur et de matrice. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; fusionne deux allocations ensemble.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; applique un flou gaussien à une allocation.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; applique une matrice de couleurs à une allocation (c’est-à-dire modifier les couleurs, ajuster la teinte).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; applique une matrice de couleurs 3x3 à une allocation.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; applique une matrice de couleurs 5x5 à une allocation.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; un filtre d’histogramme intrinsèque.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; applique une table de recherche par canal à une mémoire tampon.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; script pour effectuer le redimensionnement d’une allocation 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; convertit une mémoire tampon YUV en RVB.

Pour plus d’informations sur chacun des scripts intrinsèques, consultez la documentation de l’API.

Les étapes de base pour l’utilisation de RenderScript dans une application Android sont décrites ci-après.

**Créez un contexte Renderscript** &ndash; le [`Renderscript`](xref:Android.Renderscripts.RenderScript)
la classe est un wrapper géré autour du contexte RenderScript et contrôle l’initialisation, la gestion des ressources et le nettoyage. L’objet RenderScript est créé à l’aide de la méthode de fabrique `RenderScript.Create`, qui prend un contexte Android (tel qu’une activité) en tant que paramètre. La ligne de code suivante montre comment initialiser le contexte RenderScript :

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Créer des allocations** &ndash; en fonction du script intrinsèque, il peut être nécessaire de créer un ou deux `Allocation`s. [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
la classe a plusieurs méthodes de fabrique pour faciliter l’instanciation d’une allocation pour un intrinsèque. À titre d’exemple, l’extrait de code suivant montre comment créer une allocation pour les bitmaps.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Il est souvent nécessaire de créer un `Allocation` pour stocker les données de sortie d’un script. L’extrait de code suivant montre comment utiliser le programme d’assistance `Allocation.CreateTyped` pour instancier un deuxième `Allocation` qui est du même type que l’original :

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Instanciez le wrapper de script** &ndash; chacune des classes wrapper de script intrinsèques doit avoir des méthodes d’assistance (généralement appelées `Create`) pour instancier un objet wrapper pour ce script. L’extrait de code suivant est un exemple d’instanciation d’un objet `ScriptIntrinsicBlur` Blur. La méthode d’assistance `Element.U8_4` crée un élément qui décrit un type de données qui est 4 champs de valeurs entières non signées 8 bits, permettant de conserver les données d’un objet `Bitmap` :

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Affecter des allocations, définir des paramètres & exécuter le Script** &ndash; la classe `Script` fournit une méthode `ForEach` pour exécuter réellement le RenderScript. Cette méthode effectue une itération sur chaque `Element` du `Allocation` contenant les données d’entrée. Dans certains cas, il peut être nécessaire de fournir un `Allocation` qui contient la sortie.
`ForEach` remplacera le contenu de l’allocation de sortie. Pour continuer avec les extraits de code des étapes précédentes, cet exemple montre comment attribuer une allocation d’entrée, définir un paramètre, puis exécuter le script (en copiant les résultats dans l’allocation de sortie) :

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Vous souhaiterez peut-être consulter la recette [d’une image avec RenderScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) , c’est un exemple complet d’utilisation d’un script intrinsèque dans Xamarin. Android.

## <a name="summary"></a>Résumé

Ce guide a introduit RenderScript et comment l’utiliser dans une application Xamarin. Android. Il a brièvement abordé les RenderScript et la façon dont il fonctionne dans une application Android. Il a décrit certains des composants clés de RenderScript et la différence entre les _scripts utilisateur_ et les _scripts intrinsèques_. Enfin, ce guide a présenté les étapes de l’utilisation d’un script intrinsèque dans une application Xamarin. Android.

## <a name="related-links"></a>Liens connexes

- [Espace de noms Android. Renderscripts](xref:Android.Renderscripts)
- [Brouiller une image avec RenderScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Didacticiel : Prise en main avec RenderScript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
