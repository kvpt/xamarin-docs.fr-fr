---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Utilisez les bibliothèques C/CMD avec Xamarin
description: Visual Studio pour Mac peut être utilisé pour construire et intégrer le code C/CM multiplateforme dans les applications mobiles pour Android et iOS, à l’aide de Xamarin et de C. Cet article explique comment configurer et déboiffer un projet CMD dans une application Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 42a59570d727657b2f3c23bd9d1f37e1205717d0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73842820"
---
# <a name="use-cc-libraries-with-xamarin"></a>Utilisez les bibliothèques C/CMD avec Xamarin

## <a name="overview"></a>Vue d’ensemble

Xamarin permet aux développeurs de créer des applications mobiles natives multiplateformes avec Visual Studio. En général, les liaisons C sont utilisées pour exposer les composants existants de la plate-forme aux développeurs. Cependant, il ya des moments où les applications Xamarin doivent travailler avec les bases de code existantes. Parfois, les équipes n’ont tout simplement pas le temps, le budget ou les ressources pour porter une base de code importante, bien testée et hautement optimisée à C.

[Le CMD visuel pour le développement mobile multiplateforme](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permet de construire le code C/C et CMD dans le cadre de la même solution, offrant de nombreux avantages, y compris une expérience de débogage unifiée. Microsoft a utilisé C/C et Xamarin de cette façon pour fournir des applications telles que [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) et [Pix Camera](https://www.microsoft.com/microsoftpix).

Toutefois, dans certains cas, il y a un désir (ou une exigence) de maintenir en place des outils et des processus C/CM existants et de garder le code de la bibliothèque découplé de l’application, en traitant la bibliothèque comme si elle était semblable à celle d’un tiers. Dans ces situations, le défi n’est pas seulement d’exposer les membres concernés à la C, mais aussi de gérer la bibliothèque comme une dépendance. Et, bien sûr, l’automatisation autant de ce processus que possible.  

Ce post décrit une approche de haut niveau pour ce scénario et marche à travers un exemple simple.

## <a name="background"></a>Arrière-plan

Le C/CM est considéré comme un langage multiplateforme, mais il faut faire grand soin de s’assurer que le code source est effectivement multiplateforme, en utilisant uniquement C/CMmd pris en charge par tous les compilateurs cibles et contenant peu ou pas de code conditionnel ou spécifique à un compilateur.

En fin de compte, le code doit compiler et fonctionner avec succès sur toutes les plates-formes cibles, ce qui se résume à la communnalité à travers les plates-formes (et les compilateurs) ciblés. Les problèmes peuvent encore découler de différences mineures entre les compilateurs et de sorte que des tests approfondis (de préférence automatisés) sur chaque plate-forme cible deviennent de plus en plus importants.  

## <a name="high-level-approach"></a>Approche de haut niveau

L’illustration ci-dessous représente l’approche en quatre étapes utilisée pour transformer le code source C/CMD en une bibliothèque multiplateforme Xamarin qui est partagée via NuGet, puis consommée dans une application Xamarin.Forms.

![Approche de haut niveau pour l’utilisation de C/C avec Xamarin](images/cpp-steps.jpg)

Les 4 étapes sont les :

1. Compiler le code source C/CMD dans des bibliothèques autochtones spécifiques à la plate-forme.
2. Envelopper les bibliothèques autochtones avec une solution Visual Studio.
3. Emballage et poussant un paquet NuGet pour l’emballage .NET.
4. Consommer le paquet NuGet à partir d’une application Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Étape 1 : Compilation du code source C/CMMD dans des bibliothèques autochtones spécifiques à la plate-forme

L’objectif de cette étape est de créer des bibliothèques indigènes qui peuvent être appelées par l’emballage C. Cela peut ou non être pertinent en fonction de votre situation. Les nombreux outils et processus qui peuvent être mis en œuvre dans ce scénario commun sont au-delà de la portée de cet article. Les principales considérations sont de maintenir la base de code C/CMD en phase avec n’importe quel code d’emballage natif, suffisamment de tests unitaires et automatisation de construction. 

Les bibliothèques de la visite ont été créées à l’aide de Visual Studio Code avec un script coquillage d’accompagnement. Une version étendue de ce passage à pied peut être trouvée dans le [référentiel mobile CAT GitHub](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) qui traite de cette partie de l’échantillon plus en profondeur. Les bibliothèques autochtones sont traitées comme une dépendance de tiers en l’espèce, mais cette étape est illustrée pour son contexte.

Par souci de simplicité, la procédure pas à pas ne vise qu’un sous-ensemble d’architectures. Pour iOS, il utilise l’utilitaire lipo pour créer un binaire de graisse unique à partir des binaires individuels spécifiques à l’architecture. Android utilisera des binaires dynamiques avec une extension .so et iOS utilisera un binaire de graisse statique avec une extension .a. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Étape 2 : Envelopper les bibliothèques autochtones avec une solution Visual Studio

L’étape suivante est d’envelopper les bibliothèques autochtones afin qu’elles soient facilement utilisées à partir de .NET. Cela se fait avec une solution Visual Studio avec quatre projets. Un projet partagé contient le code commun. Les projets ciblant chacun de Xamarin.Android, Xamarin.iOS et .NET Standard permettent à la bibliothèque d’être référencée d’une manière agnostique plate-forme.

L’emballage utilise «[l’appât et le truc](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)de commutateur », décrit par Paul Betts. Ce n’est pas la seule façon, mais il est facile de référencer la bibliothèque et il évite la nécessité de gérer explicitement les implémentations spécifiques à la plate-forme au sein de l’application consommatrice elle-même. L’astuce consiste essentiellement à s’assurer que les cibles (.NET Standard, Android, iOS) partagent le même namespace, le nom d’assemblage et la structure de classe. Étant donné que NuGet préférera toujours une bibliothèque spécifique à la plate-forme, la version .NET Standard n’est jamais utilisée au moment de l’exécution.

La plupart des travaux de cette étape se concentreront sur l’utilisation de P/Invoke pour appeler les méthodes de bibliothèque indigènes et la gestion des références aux objets sous-jacents. L’objectif est d’exposer la fonctionnalité de la bibliothèque au consommateur tout en abstrait toute complexité. Les développeurs de Xamarin.Forms n’ont pas besoin d’avoir des connaissances de travail sur le fonctionnement interne de la bibliothèque non gestion. Il devrait se sentir comme ils utilisent n’importe quelle autre bibliothèque gérée C .

En fin de compte, la sortie de cette étape est un ensemble de bibliothèques .NET, une par cible, avec un document nuspec qui contient les informations nécessaires pour construire le paquet dans la prochaine étape.

**Étape 3 : Emballage et poussant un paquet NuGet pour l’emballage .NET**

La troisième étape consiste à créer un paquet NuGet à l’aide des artefacts de construction de l’étape précédente. Le résultat de cette étape est un paquet NuGet qui peut être consommé à partir d’une application Xamarin. La procédure pas à pas utilise un répertoire local pour servir d’aliment NuGet. En production, cette étape devrait publier un package à un flux NuGet public ou privé et devrait être entièrement automatisée.

**Étape 4 : Consommer le paquet NuGet à partir d’une application Xamarin.Forms**

La dernière étape consiste à référencer et à utiliser le paquet NuGet à partir d’une application Xamarin.Forms. Cela nécessite de configurer le flux NuGet dans Visual Studio pour utiliser le flux défini dans l’étape précédente.

Une fois que le flux est configuré, le paquet doit être référencé à partir de chaque projet dans l’application multiplateforme Xamarin.Forms. «L’appât-et-switch trick» fournit des interfaces identiques, de sorte que la fonctionnalité de la bibliothèque native peut être appelé à l’aide de code défini en un seul emplacement.

Le référentiel de code source contient une [liste de lecture supplémentaire](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up) qui comprend des articles sur la façon de mettre en place un flux NuGet privé sur Azure DevOps et comment pousser le paquet à ce flux. Tout en exigeant un peu plus de temps d’installation qu’un répertoire local, ce type d’alimentation est meilleur dans un environnement de développement d’équipe.

## <a name="walk-through"></a>Procédure pas à pas

Les étapes fournies sont spécifiques à **Visual Studio pour Mac**, mais la structure fonctionne dans Visual Studio **2017** ainsi.

### <a name="prerequisites"></a>Prérequis

Afin de suivre, le développeur aura besoin:

- [Ligne de commandement NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Studio visuel* *pour Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Un [**compte de développeur Apple**](https://developer.apple.com/) actif est nécessaire afin de déployer des applications sur un iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Création des bibliothèques autochtones (étape 1)

La fonctionnalité de la bibliothèque native est basée sur l’exemple de [Walkthrough: Creating and Using a Static Library (CMD)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

Cette procédure pas à pas saute la première étape, la construction des bibliothèques autochtones, puisque la bibliothèque est fournie comme une dépendance de tiers dans ce scénario. Les bibliothèques autochtones présélectionnées sont incluses à côté du [code de l’échantillon](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) ou peuvent être [téléchargées](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) directement.

### <a name="working-with-the-native-library"></a>Travailler avec la bibliothèque autochtone

L’exemple original *de MathFuncsLib* inclut une seule classe appelée `MyMathFuncs` avec la définition suivante :

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

Une classe supplémentaire définit les fonctions d’emballage qui permettent à un consommateur `MyMathFuncs` .NET de créer, de disposer et d’interagir avec la classe autochtone sous-jacente.

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

Ce sont ces fonctions d’emballage qui sont utilisées sur le côté [Xamarin.](https://visualstudio.microsoft.com/xamarin/)

## <a name="wrapping-the-native-library-stage-2"></a>Envelopper la bibliothèque autochtone (étape 2)

Cette étape nécessite les [bibliothèques précalculées décrites](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) dans la [section précédente](#creating-the-native-libraries-stage-1).

### <a name="creating-the-visual-studio-solution"></a>Création de la solution Visual Studio

1. Dans **Visual Studio for Mac**, cliquez sur New **Project** (à partir de la page de *bienvenue*) ou **nouvelle solution** (à partir du menu *Fichier).*
2. De la fenêtre **New Project,** choisissez **Le projet partagé** (à partir de l’intérieur de *multiplateforme > Bibliothèque)* et cliquez ensuite **.**
3. Mettre à jour les champs suivants, puis cliquez sur **Créer**:

    - **Nom du projet :** MathFuncs.Partagé  
    - **Nom de la solution:** MathFuncs MathFuncs  
    - **Lieu:** Utilisez l’emplacement d’enregistrement par défaut (ou choisissez une alternative)   
    - **Créer un projet dans le répertoire de la solution :** Réglez ceci à vérifier
4. De **Solution Explorer**, double clic sur le projet **MathFuncs.Shared** et naviguer vers **les paramètres principaux**.
5. Supprimer **. Partagé** à partir de **l’espace nom par défaut** de sorte qu’il est réglé à **MathFuncs** seulement, puis cliquez **sur OK**.
6. Ouvrez **MyClass.cs** (créé par le modèle), puis renommer à la fois la classe et le nom de fichier à **MyMathFuncsWrapper** et changer l’espace de nom en **MathFuncs**.
7. **CONTROL - CLIQUEZ** sur la solution **MathFuncs**, puis choisissez **Ajouter un nouveau projet...** à partir du menu **Ajouter.**
8. De la fenêtre **New Project,** choisissez **.NET Standard Library** (à partir de l’intérieur de *Multiplateforme > Library*) et cliquez ensuite . **Next**
9. Choisissez **.NET Standard 2.0** et cliquez **ensuite**.
10. Mettre à jour les champs suivants, puis cliquez sur **Créer**:

    - **Nom du projet :** MathFuncs.Standard  
    - **Lieu:** Utilisez le même emplacement d’enregistrement que le projet partagé   

11. De **Solution Explorer**, double clic sur le projet **MathFuncs.Standard.**
12. Naviguez vers **les paramètres principaux,** puis mettez à jour **Nomspace par défaut** à **MathFuncs**.
13. Naviguez vers les paramètres **de sortie,** puis mettez à jour **le nom de l’Assemblée** à **MathFuncs**.
14. Naviguez vers les paramètres **Compilateur,** modifiez la **Configuration** pour **libérer,** définissant **les informations Debug** en **symboles Seulement** puis cliquez sur **OK**.
15. Supprimer **Class1.cs/Getting Commencé du** projet (si l’un d’eux a été inclus dans le modèle).
16. **CONTROL - CLIQUEZ** sur le dossier **Dépendance/Références** du projet, puis choisissez **Edit References**.
17. Sélectionnez **MathFuncs.Shared** à partir de l’onglet **Projets,** puis cliquez **sur OK**.
18. Répétez les étapes 7-17 (ignorant l’étape 9) en utilisant les configurations suivantes :

    | **NOM DU PROJET**  | **NOM DU MODÈLE**   | **NOUVEAU MENU DE PROJET**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Bibliothèque de classes       | Bibliothèque Android >      |
    | MathFuncs.iOS (en)     | Bibliothèque contraignante     | Bibliothèque de > iOS          |

19. De **Solution Explorer**, double clic sur le projet **MathFuncs.Android,** puis naviguez vers les paramètres **Compilateur.**

20. Avec la **configuration** définie à **Debug**, modifier **Définir les symboles** pour inclure **Android;**.

21. Modifier la **configuration** pour **libérer**, puis modifier **Définir les symboles** pour inclure également **Android;**.

22. Répétez les étapes 19-20 pour **MathFuncs.iOS**, l’édition **Define Symbols** pour inclure **iOS;** au lieu d’Android; dans les deux cas. **Android;**

23. Construisez la solution dans la configuration **de version** **(CONTROL - COMMAND - B**) et validez que les trois assemblages de sortie (Android, iOS, .NET Standard) (dans les dossiers de bac de projet respectifs) partagent le même nom **MathFuncs.dll**.

À ce stade, la solution devrait avoir trois cibles, une chacune pour Android, iOS et .NET Standard, et un projet partagé qui est référencé par chacune des trois cibles. Ceux-ci doivent être configurés pour utiliser le même namespace par défaut et les assemblages de sortie du même nom. Ceci est nécessaire pour l’approche « appât et commutateur » mentionnée précédemment.

### <a name="adding-the-native-libraries"></a>Ajout des bibliothèques autochtones

Le processus d’ajout des bibliothèques indigènes à la solution d’emballage varie légèrement entre Android et iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Références natives pour MathFuncs.Android

1. **CONTROL - CLIQUEZ** sur le projet **MathFuncs.Android,** puis choisissez **New Folder** à partir du menu **Ajouter** en le nommant **libs**.

2. Pour chaque **ABI** (Application Binary Interface), **CONTROL - CLIQUEZ** sur le dossier **libs,** puis choisissez **New Folder** dans le menu **Ajouter,** le nommant d’après cet **ABI**respectif . Dans ce cas :

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Pour un aperçu plus détaillé, consultez le sujet [Architectures et processeurs](https://developer.android.com/ndk/guides/arch) du guide des [développeurs NDK](https://developer.android.com/ndk/guides/), en particulier la section sur l’adresse du [code natif dans les paquets d’applications](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Vérifier la structure du dossier :  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Ajoutez les bibliothèques correspondantes **.so** à chacun des dossiers **ABI** en fonction de la cartographie suivante :

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/bras  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Pour ajouter des fichiers, **CONTROL ET CLIQUEZ** sur le dossier représentant l’ABI respectif, puis choisissez Ajouter des **fichiers...** à partir du menu **Ajouter.** **ABI** Choisissez la bibliothèque appropriée (à partir de **l’annuaire PrecompiledLibs)** puis cliquez sur **Open,** puis cliquez sur **OK** laissant l’option par défaut pour *copier le fichier à l’annuaire*.

5. Pour chacun des fichiers **.so,** **CONTROL - CLIQUEZ** puis choisir l’option **EmbeddedNativeLibrary** à partir du menu **Build Action.**

Maintenant, le dossier **libs** doit apparaître comme suit:

```folders
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86
        - libMathFuncs.so
    - x86_64
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>Références natives pour MathFuncs.iOS

1. **CONTROL - CLIQUEZ** sur le projet **MathFuncs.iOS,** puis choisissez **Ajouter la référence native** dans le menu **Ajouter.** 
2. Choisissez la bibliothèque **libMathFuncs.a** (à partir de libs/ios sous **l’annuaire PrecompiledLibs)** puis cliquez sur **Open** 
3. **CONTROL - CLIQUEZ** sur le fichier **libMathFuncs** (dans le dossier **Références Autochtones,** puis choisissez l’option **Propriétés** dans le menu  
4. Configurer les propriétés **de référence native** afin qu’elles soient vérifiées (montrant une icône de tique) dans le pad de **propriétés** :

    - Charge de force
    - C
    - Lien intelligent

    > [!NOTE]
    > L’utilisation d’un type de projet de bibliothèque contraignante ainsi qu’une [référence native](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) intègre la bibliothèque statique et lui permet d’être automatiquement reliée à l’application Xamarin.iOS qui la fait référence (même lorsqu’elle est incluse via un forfait NuGet).

5. Ouvrez **ApiDefinition.cs**, en supprimant le code commenté modèle `MathFuncs` (ne laissant que l’espace de nom), puis effectuez la même étape pour **Structs.cs** 

    > [!NOTE]
    > Un projet de bibliothèque contraignante nécessite ces fichiers (avec les actions de construction *ObjCBindingApi Etpi Etfinition* et *ObjCBindingCoreSource)* afin de construire. Cependant, nous allons écrire le code, pour appeler notre bibliothèque native, en dehors de ces fichiers d’une manière qui peut être partagée entre les deux cibles de bibliothèque Android et iOS en utilisant P/Invoke standard.

### <a name="writing-the-managed-library-code"></a>Rédaction du code de la bibliothèque géré

Maintenant, écrivez le code C pour appeler la bibliothèque autochtone. L’objectif est de cacher toute complexité sous-jacente. Le consommateur ne devrait pas avoir besoin d’une connaissance pratique des internes de la bibliothèque autochtone ou des concepts P/Invoke.  

#### <a name="creating-a-safehandle"></a>Création d’un SafeHandle

1. **CONTROL - CLIQUEZ** sur le projet **MathFuncs.Shared,** puis choisissez **Add File...** dans le menu **Ajouter.** 
2. Choisissez **la classe vide** de la fenêtre du nouveau **fichier,** nommez-la **MyMathFuncsSafeHandle,** puis cliquez sur **New**
3. Implémenter la classe **MyMathFuncsSafeHandle** :

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Un [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) est la meilleure façon de travailler avec des ressources non gérées dans le code géré. Cela résume beaucoup de code de la plaque de chaudière liés à la finalisation critique et le cycle de vie de l’objet. Le propriétaire de cette poignée peut ensuite le traiter comme n’importe quelle autre ressource gérée et n’aura pas à mettre en œuvre le [modèle jetable](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)complet . 

#### <a name="creating-the-internal-wrapper-class"></a>Création de la classe d’emballage interne

1. Ouvrez **MyMathFuncsWrapper.cs,** en le changeant en une classe statique interne

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. Dans le même fichier, ajoutez la déclaration conditionnelle suivante à la classe :

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Cela définit la valeur constante **DllName** basée sur la construction de la bibliothèque pour **Android** ou **iOS**. Il s’agit d’aborder les différentes conventions de nommage utilisées par chaque plate-forme respective, mais aussi le type de bibliothèque utilisée dans ce cas. Android utilise une bibliothèque dynamique et s’attend donc à un nom de fichier, y compris l’extension. Pour iOS, '*__Internal'* est nécessaire puisque nous utilisons une bibliothèque statique.

3. Ajoutez une référence à **System.Runtime.InteropServices** en haut du fichier **MyMathFuncsWrapper.cs**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Ajoutez les méthodes d’emballage pour gérer la création et l’élimination de la classe **MyMathFuncs** :

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Nous passons dans notre **DllName** constante à **l’attribut DllImport** avec **l’EntryPoint** qui indique explicitement le temps d’exécution .NET le nom de la fonction à appeler dans cette bibliothèque. Techniquement, nous n’avons pas besoin de fournir la valeur **EntryPoint** si nos noms de méthode gérée étaient identiques à ceux non gérés. Si l’on n’est pas fourni, le nom de la méthode gérée serait utilisé comme **le Point d’entrée** à la place. Cependant, il est préférable d’être explicite.  

5. Ajoutez les méthodes d’emballage pour nous permettre de travailler avec la classe **MyMathFuncs** à l’aide du code suivant :

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > Nous utilisons des types simples pour les paramètres de cet exemple. Étant donné que le rassemblement est un peu sage dans ce cas, il ne nécessite aucun travail supplémentaire de notre part. Notez également l’utilisation de la classe **MyMathFuncsSafeHandle** au lieu de la norme **IntPtr**. **L’IntPtr** est automatiquement cartographié sur le **SafeHandle** dans le cadre du processus de rassemblement.

6. Vérifiez que la classe **MyMathFuncsWrapper** finie apparaît ci-dessous :

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Compléter la classe MyMathFuncsSafeHandle

1. Ouvrez la classe **MyMathFuncsSafeHandle,** naviguez vers le commentaire **TODO** titulaire dans la méthode **ReleaseHandle** :

    ```csharp
    // TODO: Release the handle here
    ```

1. Remplacer la ligne **TODO** :

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Rédaction de la classe MyMathFuncs

Maintenant que l’emballage est complet, créez une classe MyMathFuncs qui gérera la référence à l’objet myMathFuncs non géré.  

1. **CONTROL - CLIQUEZ** sur le projet **MathFuncs.Shared,** puis choisissez **Add File...** dans le menu **Ajouter.** 
2. Choisissez **la classe vide** de la fenêtre du nouveau **fichier,** nommez-la **MyMathFuncs,** puis cliquez sur **New**
3. Ajoutez les membres suivants à la classe **MyMathFuncs** :

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implémentez le constructeur pour la classe afin qu’il crée et stocke une poignée à l’objet **natif MyMathFuncs** lorsque la classe est instantanée:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implémenter l’interface **IDisposable** à l’aide du code suivant :

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. Implémentez les méthodes **MyMathFuncs** en utilisant la classe **MyMathFuncsWrapper** pour effectuer le vrai travail sous le capot en passant dans le pointeur que nous avons stocké à l’objet sous-jacent non gestion. Le code doit être le suivant :

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>Création du nuspec

Afin d’avoir la bibliothèque emballée et distribuée via NuGet, la solution a besoin d’un fichier **nuspec.** Ceci identifiera lequel des assemblages résultants sera inclus pour chaque plate-forme prise en charge.

1. **CONTROL - CLIQUEZ** sur la solution **MathFuncs**, puis choisissez **Add Solution Folder** à partir du menu **Ajouter** en le nommant **SolutionItems**.
2. **CONTROL - CLIQUEZ** sur le dossier **SolutionItems,** puis choisissez **New File...** dans le menu **Ajouter.**
3. Choisissez **vide XML Fichier** de la fenêtre Du nouveau **fichier,** nomciez-le **MathFuncs.nuspec,** puis cliquez sur **Nouveau**.
4. Mettre à jour **MathFuncs.nuspec** avec les métadonnées de base du paquet à afficher au consommateur **NuGet.** Par exemple :

    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    > Voir le document [de référence nuspec](https://docs.microsoft.com/nuget/reference/nuspec) pour plus de détails sur le schéma utilisé pour ce manifeste.

5. Ajoutez `<files>` un élément en `<package>` tant qu’enfant de l’élément (juste en dessous), `<metadata>`identifiant chaque fichier avec un élément distinct `<file>` :

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Lorsqu’un paquet est installé dans un projet, et lorsqu’il y a plusieurs assemblages spécifiés par le même nom, NuGet choisira efficacement l’assemblage qui est le plus spécifique à la plate-forme donnée.

6. Ajoutez `<file>` les éléments pour les assemblages **Android:**

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Ajoutez `<file>` les éléments pour les assemblages **iOS** :

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Ajouter `<file>` les éléments pour les assemblages **netstandard2.0** :

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Vérifier le manifeste **nuspec:**

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>

        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > Ce fichier spécifie les trajectoires de sortie d’assemblage d’une version **de version,** alors assurez-vous de construire la solution en utilisant cette configuration.

À ce stade, la solution contient 3 assemblages .NET et un manifeste **nuspec** de soutien.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuer l’emballage .NET avec NuGet

L’étape suivante consiste à emballer et distribuer le paquet NuGet afin qu’il puisse être facilement consommé par l’application et géré comme une dépendance. L’emballage et la consommation pourraient tous se faire dans une seule solution, mais la distribution de la bibliothèque via Des aides NuGet dans le découplage et nous permet de gérer ces bases de code indépendamment.

### <a name="preparing-a-local-packages-directory"></a>Préparation d’un répertoire de forfaits locaux

La forme la plus simple de l’alimentation NuGet est un répertoire local:

1. Dans **Finder**, naviguez vers un répertoire pratique. Par exemple, **/Utilisateurs**.
2. Choisissez **New Folder** dans le menu **Fichier,** fournissant un nom significatif tel que **local-nuget-feed**.

### <a name="creating-the-package"></a>Création du package

1. Définissez la **configuration de construction** pour **libérer,** et exécutez une version à l’aide **de COMMAND B**.
2. Ouvrez **le terminal** et changez l’annuaire du dossier contenant le fichier **nuspec.**
3. Dans **Terminal**, exécuter la commande **de pack de nuget** spécifiant le fichier **nuspec,** la **version** (par exemple, 1.0.0), et le **OutputDirectory** en utilisant le dossier créé dans [l’étape précédente,](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)c’est-à-dire, **local-nuget-feed**. Par exemple :

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confirmez** que **MathFuncs.1.0.0.nupkg** a été créé dans le répertoire **local-nuget-feed.**

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[FACULTATIF] Utilisation d’un flux NuGet privé avec Azure DevOps

Une technique plus robuste est décrite dans [Get started with NuGet packages in Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), qui montre comment créer un flux privé et pousser le paquet (généré dans l’étape précédente) à ce flux.

Il est idéal d’avoir ce flux de travail entièrement automatisé, par exemple en utilisant [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Pour plus d’informations, voir [Démarrer avec Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Consommer l’emballage .NET d’une application Xamarin.Forms

Pour compléter la procédure pas à pas, créez une application **Xamarin.Forms** pour consommer le paquet qui vient d’être publié sur l’alimentation **nuGet** locale.

### <a name="creating-the-xamarinforms-project"></a>Création du projet **Xamarin.Forms**

1. Ouvrez une nouvelle instance de **Visual Studio pour Mac**. Cela peut être fait à partir de **Terminal**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. Dans **Visual Studio for Mac**, cliquez sur New **Project** (à partir de la page de *bienvenue*) ou **nouvelle solution** (à partir du menu *Fichier).*
3. De la fenêtre **New Project,** choisissez **Blank Forms App** (à partir de l’intérieur de *Multiplateforme > App*) et cliquez ensuite . **Next**
4. Mettre à jour les champs suivants, puis cliquez sur **Suivant**:

    - **Nom de l’application:** MathFuncsApp.
    - **Identifiant de l’organisation :** Utilisez un espace de nom inversé, par exemple, _com your_org._
    - **Plates-formes cibles :** Utilisez la valeur par défaut (cibles Android et iOS).
    - **Code partagé :** Définissez ceci à .NET Standard (une solution de « bibliothèque partagée » est possible, mais au-delà de la portée de cette procédure pas à pas).

5. Mettre à jour les champs suivants, puis cliquez sur **Créer**:

    - **Nom du projet :** MathFuncsApp.
    - **Nom de la solution:** MathFuncsApp.  
    - **Lieu:** Utilisez l’emplacement d’enregistrement par défaut (ou choisissez une alternative).

6. Dans **Solution Explorer**, CONTROL - **CLIQUEZ** sur la cible (**MathFuncsApp.Android** ou **MathFuncs.iOS**) pour les tests initiaux, puis choisissez **Set As Startup Project**.
7. Choisissez **l’appareil** préféré ou **l’émulateur de**/**simulateur.** 
8. Exécutez la solution (**COMMAND - RETURN**) pour valider que le projet **Xamarin.Forms** modélique construit et fonctionne bien. 

    > [!NOTE]
    > **iOS** (en particulier le simulateur) a tendance à avoir le temps de construction/déploiement le plus rapide.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Ajout de l’alimentation NuGet locale à la configuration NuGet

1. Dans **Visual Studio**, choisissez **Préférences** (à partir du menu **Visual Studio).**
2. Choisissez des **sources** sous la section **NuGet,** puis cliquez sur **Ajouter**.
3. Mettez à jour les champs suivants, puis cliquez sur **Ajouter Source**:

    - **Nom:** Fournir un nom significatif, par exemple, Local-Packages.  
    - **Lieu:** Spécifier le dossier **local-nuget-feed** créé dans [l’étape précédente](#preparing-a-local-packages-directory).

    > [!NOTE]
    > Dans ce cas, il n’est pas nécessaire de spécifier un **nom d’utilisateur** et **mot de passe**. 

4. Cliquez sur **OK**.

### <a name="referencing-the-package"></a>Référencement du package

Répétez les étapes suivantes pour chaque projet (**MathFuncsApp**, **MathFuncsApp.Android**, et **MathFuncsApp.iOS**).

1. **CONTROL - CLIQUEZ** sur le projet, puis choisissez **Des forfaits Ajouter NuGet...** à partir du menu **Ajouter.**
2. Recherche de **MathFuncs**. 
3. Vérifier la **version** de l’emballage est **1.0.0** et les autres détails apparaissent comme prévu tels que le titre et la **description**, c’est-à-dire, *MathFuncs* et *Sample C 'Wrapper Library*. **Title** 
4. Sélectionnez le forfait **MathFuncs,** puis cliquez sur **Ajouter le paquet**.

### <a name="using-the-library-functions"></a>Utilisation des fonctions de la bibliothèque

Maintenant, avec une référence au paquet **MathFuncs** dans chacun des projets, les fonctions sont disponibles pour le code C.

1. Open **MainPage.xaml.cs** de l’intérieur du projet **MathFuncsApp** commun **Xamarin.Forms**(référencé par **MathFuncsApp.Android** et **MathFuncsApp.iOS**).
2. Ajouter **l’utilisation des** instructions pour **System.Diagnostics** et **MathFuncs** en haut du fichier:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Déclarez une `MyMathFuncs` instance de la `MainPage` classe au sommet de la classe :

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Remplacer les `OnAppearing` `OnDisappearing` méthodes et `ContentPage` les méthodes de la classe de base :

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. Mettre `OnAppearing` à jour la `myMathFuncs` méthode d’initialisation de la variable déclarée précédemment :

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Mettre `OnDisappearing` à jour `Dispose` la `myMathFuncs`méthode pour appeler la méthode sur :

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implémentez une méthode privée appelée **TestMathFuncs** comme suit :

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. Enfin, `TestMathFuncs` appelez à la `OnAppearing` fin de la méthode :

    ```csharp
    TestMathFuncs();
    ```

9. Exécutez l’application sur chaque plate-forme cible et validez la sortie dans le bloc **de sortie d’application** apparaît comme suit :

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Si vous rencontrez un '*DLLNotFoundException*' lors d’un test sur Android, ou une erreur de construction sur iOS, assurez-vous de vérifier que l’architecture CPU de l’appareil / émulateur / simulateur que vous utilisez est compatible avec le sous-ensemble que nous avons choisi de prendre en charge. 

## <a name="summary"></a>Récapitulatif

Cet article expliquait comment créer une application Xamarin.Forms qui utilise les bibliothèques autochtones par le biais d’un emballage .NET commun distribué via un package NuGet. L’exemple fourni dans cette procédure pas à pas est intentionnellement très simpliste pour démontrer plus facilement l’approche. Une application réelle devra faire face à des complexités telles que le traitement des exceptions, les rappels, le regroupement de types plus complexes et les liens avec d’autres bibliothèques de dépendance. Un facteur clé est le processus par lequel l’évolution du code C est coordonnée et synchronisée avec l’emballage et les applications client. Ce processus peut varier selon que l’une ou l’autre de ces préoccupations relèvent d’une seule équipe. Quoi qu’il en soit, l’automatisation est un réel avantage. Voici quelques ressources fournissant une lecture plus approfondie autour de certains des concepts clés ainsi que les téléchargements pertinents. 

### <a name="downloads"></a>Téléchargements

- [Outils NuGet Command Line (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Exemples

- [Développement mobile multiplateforme Hyperlapse avec C](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C et Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Port d’échantillon de Mono San Angeles](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>Pour aller plus loin

[Articles relatifs au contenu de ce post](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)