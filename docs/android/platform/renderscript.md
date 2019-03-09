---
title: Introduction à Renderscript
description: Ce guide présente Renderscript et explique comment utiliser l’intrinsèque Renderscript API dans une application Xamarin.Android ce niveau cibles API 17 ou version ultérieure.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 8364310d23739c05ff97ea8aa8fa4c56f89ea40c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670725"
---
# <a name="an-introduction-to-renderscript"></a>Introduction à Renderscript

_Ce guide présente Renderscript et explique comment utiliser l’intrinsèque Renderscript API dans une application Xamarin.Android ce niveau cibles API 17 ou version ultérieure._

## <a name="overview"></a>Vue d'ensemble

RenderScript est une infrastructure de programmation créée par Google pour améliorer les performances des applications Android qui nécessitent d’importantes ressources de calculs. Il s’agit d’un bas niveau, hautes performances API basée sur [C99](https://en.wikipedia.org/wiki/C99). S’agissant d’un API qui s’exécutera sur les processeurs, GPU ou DSP de bas niveau, Renderscript convient bien pour les applications Android qui devra peut-être effectuer les opérations suivantes :

* Graphiques
* Traitement d’image
* Chiffrement
* Traitement des signaux
* Routines mathématiques

RenderScript utilisera `clang` et de la compilation des scripts pour les codes d’octets LLVM est fourni dans le fichier APK. Quand l’application est exécutée pour la première fois, le code d’octet LLVM sera compilé en code machine pour les processeurs sur l’appareil. Cette architecture permet à une application Android exploiter les avantages du code machine sans les développeurs eux-mêmes devoir l’écrire pour chaque processeur sur l’appareil eux-mêmes.

Il existe deux composants pour une routine Renderscript :

1. **Le runtime Renderscript** &ndash; il s’agit d’API natives qui sont responsables de l’exécution de la Renderscript. Cela inclut tout Renderscripts écrit pour l’application.

2. **Des Wrappers managés à partir du Framework Android** &ndash; gérés des classes qui permettent à une application Android contrôler et interagir avec le runtime de Renderscript et les scripts. En plus des classes d’infrastructure fournie pour le contrôle de l’exécution de Renderscript, la chaîne d’outils Android examine le code source Renderscript et générer des classes de wrapper managé pour une utilisation par l’application Android.

Le diagramme suivant illustre comment ces composants sont liés :

![Diagramme illustrant la façon dont le Framework Android interagit avec le Renderscript Runtime](renderscript-images/renderscript-01.png)

Il existe trois concepts importants pour l’utilisation de Renderscripts dans une application Android :

1. **Un contexte** &ndash; API fournie par le Kit Android SDK qui alloue des ressources à Renderscript et autorise l’application Android à transmettre et recevoir des données à partir de la Renderscript géré.

2. **Un _noyau de calcul_**  &ndash; également connu sous le _noyau de racine_ ou _noyau_, cette une routine qui effectue le travail. Le noyau est très similaire à une fonction C ; Il est une routine de parallélisme qui exécutera toutes les données dans la mémoire allouée.

3. **Mémoire allouée** &ndash; données sont passées vers et à partir d’un noyau via un  _[Allocation](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Un noyau peut avoir une entrée ou de sortie d’Allocation.

Le [Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) espace de noms contient les classes permettant d’interagir avec le runtime Renderscript. En particulier, le [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) classe gère le cycle de vie et les ressources du moteur Renderscript. L’application Android doit initialiser une ou plusieurs [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
Objets. Une Allocation est une API managée qui est responsable de l’allocation et l’accès à la mémoire est partagée entre l’application Android et le runtime Renderscript. En règle générale, une Allocation est créée pour l’entrée, et éventuellement un autre d’Allocation est créée pour contenir la sortie du noyau. Le moteur d’exécution Renderscript et les classes de wrapper managé associé seront gérer l’accès à la mémoire détenue par les Allocations, il n’est pas nécessaire pour un développeur d’application Android pour effectuer tout travail supplémentaire.

Une Allocation contient un ou plusieurs [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Les éléments sont un type spécialisé qui décrivent les données de chaque Allocation.
Les types d’élément de la sortie de l’Allocation doit correspondre les types de l’élément d’entrée. Lors de l’exécution, un Renderscript itérer sur chaque élément dans l’Allocation d’entrée en parallèle et écrire les résultats vers la sortie d’Allocation. Il existe deux types d’éléments :

- **type simple** &ndash; sur le plan conceptuel, il est identique à un type de données C, `float` ou un `char`.

- **type complexe** &ndash; ce type est similaire à un C `struct`.

Le moteur Renderscript effectue une vérification à l’exécution pour vous assurer que les éléments dans chaque Allocation sont compatibles avec ce qui est requis par le noyau. Si le type de données des éléments de l’Allocation ne correspondre pas le type de données que le noyau attend, une exception sera levée.

Tous les noyaux Renderscript seront encapsulés par un type qui est un descendant de la [`Android.Renderscripts.Script`](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)
. Le `Script` classe est utilisée pour définir les paramètres d’un Renderscript, définissez approprié `Allocations`, et exécuter le Renderscript. Il existe deux `Script` sous-classes dans le Kit Android SDK :


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Certaines des tâches de Renderscript plus courantes sont fournies dans le Kit Android SDK et sont accessibles par une sous-classe de la [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) classe. Il est inutile pour un développeur d’étapes supplémentaires pour utiliser ces scripts dans leur application, car elles sont déjà fournies.

- **`ScriptC_XXXXX`** &ndash; Également appelé _scripts utilisateur_, il s’agit des scripts qui sont écrites par les développeurs et empaquetés dans le fichier APK. Au moment de la compilation, la chaîne d’outils Android génère des classes wrapper managées permettant d’exécuter les scripts à utiliser dans l’application Android.
  Le nom de ces classes générées est le nom du fichier Renderscript, avec le préfixe `ScriptC_`. Écriture et en incorporant des scripts de l’utilisateur ne sont pas officiellement pris en charge par Xamarin.Android dépasse le cadre de ce guide.

Ces deux types, le `StringIntrinsic` est pris en charge par Xamarin.Android. Ce guide explique comment utiliser des scripts intrinsèques dans une application Xamarin.Android.

## <a name="requirements"></a>Spécifications

Ce guide concerne les applications Xamarin.Android ce niveau d’API cible 17 ou version ultérieure. L’utilisation de _scripts utilisateur_ n’est pas couverte dans ce guide.

Le [bibliothèque de prise en charge de Xamarin.Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports intrinsèques de l’API Renderscript pour les applications qui ciblent des versions antérieures d’Android SDK. Ajout de ce package à un projet Xamarin.Android doit autoriser les applications qui ciblent des versions antérieures du kit SDK Android d’exploiter les scripts intrinsèques.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>À l’aide de Renderscripts intrinsèque dans Xamarin.Android

Les scripts intrinsèques sont un excellent moyen pour effectuer des tâches de calcul intensifs avec un minimum de code supplémentaire. Ils ont été main réglé pour offrir des performances optimales sur une section volumineuse d’appareils.
Il n’est pas rare pour un script intrinsèque s’exécute 10 fois plus rapide que le code managé et 2-3 fois de x après qu’une implémentation personnalisée de C. La plupart des scénarios de traitement par défaut sont couverts par les scripts intrinsèques. Cette liste des scripts intrinsèques décrit les scripts en cours dans Xamarin.Android :

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; convertit RVB à RVBA à l’aide d’une table de recherche 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh performances Renderscript APIs à [BLAS](http://www.netlib.org/blas/). BLAS (Basic sous-programmes d’algèbre linéaire) sont des routines qui fournissent les blocs de construction standard pour effectuer des opérations de matrice et de vecteur de base. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; fusionne deux Allocations ensemble.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; plus flou gaussien une Allocation.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; s’applique une matrice de couleurs pour une Allocation (c'est-à-dire modification couleurs, régler la nuance).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; s’applique une matrice de couleurs de 3 x 3 pour une Allocation.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; s’applique une matrice de couleurs de 5 x 5 pour une Allocation.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; un filtre d’histogramme intrinsèque.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; s’applique une table de recherche par canal vers une mémoire tampon.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; Script pour effectuer le redimensionnement d’une allocation 2D.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; convertit une mémoire tampon YUV RVB.

Veuillez consulter la documentation API pour plus d’informations sur chacun des scripts intrinsèques.

Les étapes de base pour l’utilisation de Renderscript dans une application Android sont décrites ci-après.

**Créer un contexte Renderscript** &ndash; le [`Renderscript`](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)
classe est un wrapper managé autour du contexte Renderscript et détermine l’initialisation, la gestion des ressources et nettoyer. L’objet Renderscript est créé à l’aide de la `RenderScript.Create` méthode de fabrique, qui prend un contexte Android (par exemple, une activité) en tant que paramètre. La ligne de code suivante montre comment initialiser le contexte Renderscript :

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Créer des Allocations** &ndash; selon le script intrinsèque, il peut être nécessaire créer un ou deux `Allocation`s. Le [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
classe possède plusieurs méthodes de fabrique pour faciliter l’instanciation d’une allocation pour un type intrinsèque. Par exemple, l’extrait de code suivant montre comment créer d’Allocation pour les Bitmaps.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Souvent, il sera nécessaire créer un `Allocation` pour contenir les données de sortie d’un script. Cet extrait de code suivant montre comment utiliser le `Allocation.CreateTyped` helper pour instancier une seconde `Allocation` que type similaire à celui d’origine :

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Instanciez le wrapper de Script** &ndash; chacune des classes wrapper intrinsèque de script doit avoir des méthodes d’assistance (généralement appelé `Create`) pour instancier un objet de wrapper pour ce script. L’extrait de code suivant est un exemple montrant comment instancier un `ScriptIntrinsicBlur` flou d’objet. Le `Element.U8_4` méthode d’assistance crée un élément qui décrit un type de données est de 4 champs de valeurs d’entier non signé 8 bits, recevoir les données d’un `Bitmap` objet :

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Affecter Allocation(s), définir les paramètres et exécuter le Script** &ndash; le `Script` classe fournit un `ForEach` méthode pour exécuter le Renderscript. Cette méthode effectue une itération sur chaque `Element` dans le `Allocation` contenant les données d’entrée. Dans certains cas, il peut être nécessaire de fournir un `Allocation` qui contient la sortie.
`ForEach` remplace le contenu de la sortie de l’Allocation. Pour exercer avec les extraits de code des étapes précédentes, cet exemple montre comment affecter une Allocation d’entrée, définissez un paramètre et puis enfin exécuter le script (copier les résultats vers la sortie d’Allocation) :

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Vous pouvez souhaiter extraire le [flou une Image avec Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) Recipe (Recette), il est un exemple complet montrant comment utiliser un script intrinsèque dans Xamarin.Android.

## <a name="summary"></a>Récapitulatif

Ce guide a présenté Renderscript et comment l’utiliser dans une application Xamarin.Android. Il est brièvement décrite Renderscript What ' s et son fonctionnement dans une application Android. Il décrit certains des composants clés dans Renderscript et la différence entre _scripts utilisateur_ et _intrinsèques scripts_. Enfin, ce guide décrit les étapes décrites dans l’aide d’un script intrinsèque dans une application Xamarin.Android.



## <a name="related-links"></a>Liens associés

- [Espace de noms Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [Une Image avec Renderscript de flou](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutoriel : Prise en main Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
