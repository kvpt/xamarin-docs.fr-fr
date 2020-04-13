---
title: Une introduction au Renderscript
description: Ce guide introduit Renderscript et explique comment utiliser l’API intrinsèque Renderscript dans une application Xamarin.Android qui cible le niveau API 17 ou plus.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019794"
---
# <a name="an-introduction-to-renderscript"></a>Une introduction au Renderscript

_Ce guide introduit Renderscript et explique comment utiliser l’API intrinsèque Renderscript dans une application Xamarin.Android qui cible le niveau API 17 ou plus._

## <a name="overview"></a>Vue d’ensemble

Renderscript est un cadre de programmation créé par Google dans le but d’améliorer les performances des applications Android qui nécessitent des ressources informatiques étendues. Il s’agit d’un API de bas niveau et de haute performance basé sur [C99](https://en.wikipedia.org/wiki/C99). Étant donné qu’il s’agit d’une API de bas niveau qui fonctionnera sur les processeurs, les GPU ou les DSP, Renderscript est bien adapté aux applications Android qui pourraient avoir besoin d’effectuer l’un des éléments suivants :

- Graphiques
- Traitement d’image
- Chiffrement
- Traitement des signaux
- Routines mathématiques

Renderscript utilisera `clang` et compilera les scripts au code byte LLVM qui est regroupé dans l’APK. Lorsque l’application est exécutée pour la première fois, le code byte LLVM sera compilé dans le code machine pour les processeurs de l’appareil. Cette architecture permet à une application Android d’exploiter les avantages du code machine sans que les développeurs eux-mêmes aient à l’écrire pour chaque processeur sur l’appareil eux-mêmes.

Il y a deux éléments à une routine De Renderscript :

1. Le &ndash; **temps d’exécution Renderscript** C’est l’API indigène qui sont responsables de l’exécution du Renderscript. Cela comprend tous les Renderscripts écrits pour l’application.

2. **Emballages gérés à partir des** &ndash; classes Android Framework Managed qui permettent à une application Android de contrôler et d’interagir avec l’heure d’exécution Et les scripts De Renderscript. En plus du cadre fourni des classes pour contrôler l’exécution Renderscript, la chaîne d’outils Android examinera le code source Renderscript et générera des classes d’emballage gérés pour une utilisation par l’application Android.

Le diagramme suivant illustre comment ces composants se rapportent :

![Diagramme illustrant comment le cadre Android interagit avec le Renderscript Runtime](renderscript-images/renderscript-01.png)

Il existe trois concepts importants pour l’utilisation de Renderscripts dans une application Android :

1. **Un contexte** &ndash; A a géré l’API fourni par le SDK Android qui alloue des ressources à Renderscript et permet à l’application Android de passer et de recevoir des données du Renderscript.

2. **Un _noyau de calcul_ ** &ndash; Également connu sous le nom de noyau de _racine_ ou _de noyau,_ c’est une routine qui fait le travail. Le noyau est très similaire à une fonction C; il s’agit d’une routine parallèlementable qui sera exécutée sur toutes les données de mémoire allouée .

3. **Les** &ndash; données de mémoire allouées sont transmises à et à partir d’un noyau par le biais _[d’une allocation](xref:Android.Renderscripts.Allocation)_. Un noyau peut avoir une entrée et/ou une allocation de sortie.

L’espace de nom [Android.Renderscripts](xref:Android.Renderscripts) contient les classes pour interagir avec le temps d’exécution Renderscript. En particulier, [`Renderscript`](xref:Android.Renderscripts.RenderScript) la classe gérera le cycle de vie et les ressources du moteur Renderscript. L’application Android doit parasétiser un ou plusieurs[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
. Une allocation est une API gérée qui est responsable de l’attribution et de l’accès à la mémoire qui est partagée entre l’application Android et l’heure d’exécution Renderscript. Typiquement, une allocation est créée pour l’entrée, et optionnellement une autre allocation est créée pour tenir la sortie du noyau. Le moteur De temps d’exécution Renderscript et les classes d’emballage gérées associées géreront l’accès à la mémoire détenue par les Allocations, il n’est pas nécessaire qu’un développeur d’applications Android fasse un travail supplémentaire.

Une allocation contiendra un ou plusieurs [Android.Renderscripts.Elements](xref:Android.Renderscripts.Element).
Les éléments sont un type spécialisé qui décrit les données dans chaque allocation.
Les types d’élément de l’allocation de sortie doivent correspondre aux types de l’élément d’entrée. Lors de l’exécution, un Renderscript itérera sur chaque élément dans l’allocation d’entrée en parallèle, et écrirea les résultats à l’allocation de sortie. Il existe deux types d’éléments :

- **type** &ndash; simple Conceptuellement c’est le `float` même `char`qu’un type de données C, ou un .

- **type** &ndash; complexe Ce type est `struct`similaire à un C .

Le moteur Renderscript effectuera une vérification du temps d’exécution pour s’assurer que les éléments de chaque allocation sont compatibles avec ce qui est requis par le noyau. Si le type de données des éléments de l’allocation ne correspond pas au type de données que le noyau attend, une exception sera lancée.

Tous les grains De Renderscript seront enveloppés par un type qui est un descendant de la[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
. La `Script` classe est utilisée pour définir les paramètres `Allocations`d’un Renderscript, définir le approprié, et exécuter le Renderscript. Il ya `Script` deux sous-classes dans l’Android SDK:

- **`Android.Renderscripts.ScriptIntrinsic`**&ndash; Certaines des tâches Renderscript les plus courantes sont regroupées dans l’Android SDK et sont accessibles par une sous-classe de la classe [ScriptIntrinsic.](xref:Android.Renderscripts.ScriptIntrinsic) Il n’est pas nécessaire pour un développeur de prendre des mesures supplémentaires pour utiliser ces scripts dans leur application car ils sont déjà fournis.

- **`ScriptC_XXXXX`**&ndash; Aussi connu sous le nom _de scripts d’utilisateur_, ce sont des scripts qui sont écrits par des développeurs et emballés dans l’APK. Au moment de la compilation, la chaîne d’outils Android générera des classes d’emballage gérés qui permettront d’utiliser les scripts dans l’application Android.
  Le nom de ces classes générées est le nom `ScriptC_`du fichier Renderscript, préfixé avec . L’écriture et l’intégration de scripts d’utilisateurs ne sont pas officiellement pris en charge par Xamarin.Android et au-delà de la portée de ce guide.

De ces deux types, seul le `StringIntrinsic` est pris en charge par Xamarin.Android. Ce guide discutera de la façon d’utiliser des scripts intrinsèques dans une application Xamarin.Android.

## <a name="requirements"></a>Spécifications

Ce guide est pour les applications Xamarin.Android qui ciblent le niveau API 17 ou plus. L’utilisation de _scripts utilisateur_ n’est pas couverte dans ce guide.

La [Xamarin.Android V8 Support Library](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) soutient les API instrinsic Renderscript pour les applications qui ciblent les anciennes versions de l’Android SDK. L’ajout de ce package à un projet Xamarin.Android devrait permettre aux applications qui ciblent les anciennes versions de l’Android SDK de tirer parti des scripts intrinsèques.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Utilisation de rendus intrinsèques dans Xamarin.Android

Les scripts intrinsèques sont un excellent moyen d’effectuer des tâches informatiques intensives avec un minimum de code supplémentaire. Ils ont été accordés à la main pour offrir des performances optimales sur une grande section transversale d’appareils.
Il n’est pas rare qu’un script intrinsèque s’exécute 10 fois plus vite que le code géré et 2-3x fois après qu’une implémentation C personnalisée. Bon nombre des scénarios de traitement typiques sont couverts par les scripts intrinsèques. Cette liste des scripts intrinsèques décrit les scripts actuels dans Xamarin.Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; convertit RGB en RGBA à l’aide d’une table de recherche 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Fournit la performance Renderscript APIs à [BLAS](http://www.netlib.org/blas/). Les BLAS (sous-programmes d’algèbre linéaire de base) sont des routines qui fournissent des éléments de base standard pour effectuer des opérations de base de vecteur et de matrice. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; mélange deux allocations ensemble.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; applique un flou gaussien à une allocation.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; applique une matrice de couleurs à une allocation (c.-à-d. changer de couleur, ajuster la teinte).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; Applique une matrice de couleur 3x3 à une allocation.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; Applique une matrice de couleur 5x5 à une allocation.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; Un filtre histogramme intrinsèque.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; Applique une table de recherche par canal à un tampon.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; Script pour effectuer la resize d’une allocation 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; convertit un tampon YUV en RGB.

Veuillez consulter la documentation de l’API pour plus de détails sur chacun des scripts intrinsèques.

Les étapes de base pour l’utilisation de Renderscript dans une application Android sont décrites ensuite.

**Créer un contexte** &ndash; Renderscript Le[`Renderscript`](xref:Android.Renderscripts.RenderScript)
classe est un emballage géré autour du contexte Renderscript et contrôlera l’initialisation, la gestion des ressources et le nettoyage. L’objet Renderscript est `RenderScript.Create` créé en utilisant la méthode d’usine, qui prend un contexte Android (comme une activité) comme paramètre. La ligne de code suivante montre comment initialiser le contexte Renderscript :

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Créer des allocations** &ndash; Selon le script intrinsèque, il `Allocation`peut être nécessaire de créer un ou deux s. Lla[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
classe a plusieurs méthodes d’usine pour aider à instantané une allocation pour un intrinsèque. À titre d’exemple, l’extrait de code suivant montre comment créer Allocation pour Bitmaps.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Souvent, il sera nécessaire `Allocation` de créer un pour tenir les données de sortie d’un script. Cet extrait suivant montre comment utiliser `Allocation.CreateTyped` l’aide pour instantané `Allocation` une seconde que le même type que l’original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Instantané de l’emballage script** &ndash; Chacun des classes intrinsèques d’emballage `Create`de script devrait avoir des méthodes d’aide (généralement appelées ) pour instantanéiser un objet d’emballage pour ce script. L’extrait de code suivant est un exemple de `ScriptIntrinsicBlur` la façon d’instantanér un objet flou. La `Element.U8_4` méthode d’aide créera un élément qui décrit un type de données qui est de 4 champs de valeurs `Bitmap` integer 8 bits et non signées, approprié pour tenir les données d’un objet :

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Assigner l’allocation(s), paramètres définis, & Script d’exécution** &ndash; La `Script` classe fournit une `ForEach` méthode pour exécuter réellement le Renderscript. Cette méthode s’adérera sur chacune `Element` des `Allocation` données de conservation de l’entrée. Dans certains cas, il peut `Allocation` être nécessaire de fournir un qui détient la sortie.
`ForEach`remplacera le contenu de l’allocation de sortie. Pour poursuivre les extraits de code des étapes précédentes, cet exemple montre comment attribuer une allocation d’entrée, définir un paramètre, puis enfin exécuter le script (copier les résultats à l’allocation de sortie):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Vous pouvez consulter le [Blur an Image avec Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) recette, c’est un exemple complet de la façon d’utiliser un script intrinsèque dans Xamarin.Android.

## <a name="summary"></a>Récapitulatif

Ce guide a introduit Renderscript et comment l’utiliser dans une application Xamarin.Android. Il a brièvement discuté de ce que Renderscript est et comment il fonctionne dans une application Android. Il a décrit certains des composants clés de Renderscript et la différence entre les _scripts d’utilisateur_ et _les scripts instrinsiques_. Enfin, ce guide a discuté des étapes dans l’utilisation d’un script intrinsèque dans une application Xamarin.Android.

## <a name="related-links"></a>Liens connexes

- [Android.Renderscripts namespace](xref:Android.Renderscripts)
- [Flou une image avec Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Démarrer avec Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
