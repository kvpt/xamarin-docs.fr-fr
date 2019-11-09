---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Utiliser C/C++ Libraries avec Xamarin
description: Visual Studio pour Mac peut être utilisé pour créer et intégrer du code multiplateforme CC++ /code dans les applications mobiles pour Android et iOS, à C#l’aide de Xamarin et. Cet article explique comment installer et déboguer un C++ projet dans une application Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 42a59570d727657b2f3c23bd9d1f37e1205717d0
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842820"
---
# <a name="use-cc-libraries-with-xamarin"></a>Utiliser C/C++ Libraries avec Xamarin

## <a name="overview"></a>Vue d'ensemble

Xamarin permet aux développeurs de créer des applications mobiles natives multiplateformes avec Visual Studio. En général C# , les liaisons sont utilisées pour exposer des composants de plateforme existants aux développeurs. Toutefois, il existe des cas où les applications Xamarin doivent fonctionner avec les codes base existants. Parfois, les équipes n’ont pas le temps, le budget ou les ressources nécessaires pour porter une base de code volumineuse, bien testée et C#hautement optimisée à.

[Visual C++ pour le développement mobile multiplateforme](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permet de générer le codeC++ C C# /et dans le cadre de la même solution, en offrant de nombreux avantages, notamment une expérience de débogage unifiée. Microsoft a utilisé C/C++ et Xamarin de cette façon pour fournir des applications telles que l' [appareil photo](https://www.microsoft.com/microsoftpix) [mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) et pix.

Toutefois, dans certains cas, il y a un désir (ou une exigence) de conserverC++ les processus C/Tools et les processus existants et de conserver le code de bibliothèque dissocié de l’application, en traitant la bibliothèque comme si elle était similaire à un composant tiers. Dans ce cas, le défi consiste à exposer les membres pertinents à, mais C# à gérer la bibliothèque en tant que dépendance. Et, bien sûr, en automatisant autant que possible le processus.  

Ce billet décrit une approche de haut niveau pour ce scénario et vous guide dans un exemple simple.

## <a name="background"></a>Présentation

C/C++ est considéré comme un langage multiplateforme, mais il est important de veiller à ce que le code source soit effectivement multiplateforme, en utilisant uniquement C/C++ pris en charge par tous les compilateurs cibles et contenant peu ou pas de code spécifique à la plateforme ou au compilateur.

Au final, le code doit se compiler et s’exécuter correctement sur toutes les plateformes cibles. par conséquent, l’uniformité entre les plateformes (et les compilateurs) est réduite. Les problèmes peuvent toujours survenir suite à des différences mineures entre les compilateurs, de sorte que les tests rigoureux (de préférence automatisés) sur chaque plateforme cible deviennent de plus en plus importants.  

## <a name="high-level-approach"></a>Approche de haut niveau

L’illustration ci-dessous représente l’approche à quatre étapes utilisée pour transformerC++ le code C/source en une bibliothèque Xamarin multiplateforme partagée via NuGet, puis utilisée dans une application Xamarin. Forms.

![Approche de haut niveau pour l’utilisation deC++ C/with Xamarin](images/cpp-steps.jpg)

Les 4 étapes sont les suivantes :

1. Compilation du code CC++ /source en bibliothèques natives spécifiques à la plateforme.
2. Encapsulation des bibliothèques natives avec une solution Visual Studio.
3. Empaquetage et push d’un package NuGet pour le wrapper .NET.
4. Consommation du package NuGet à partir d’une application Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Étape 1 : compilation du code CC++ /source en bibliothèques natives spécifiques à la plateforme

L’objectif de cette étape est de créer des bibliothèques natives qui peuvent être appelées par le C# wrapper. Cela peut être utile en fonction de votre situation. Les nombreux outils et processus qu’il est possible d’introduire dans ce scénario courant n’entrent pas dans le cadre de cet article. Les principales considérations sont le maintien deC++ la synchronisation de la base de code C/code avec tout code wrapper natif, un test unitaire suffisant et l’automatisation de la génération. 

Les bibliothèques de la procédure pas à pas ont été créées à l’aide de Visual Studio Code avec un script d’interpréteur de commandes associé. Une version étendue de cette procédure pas à pas est disponible dans le [référentiel GITHUB Cat mobile](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) qui traite de cette partie de l’exemple de façon plus approfondie. Dans ce cas, les bibliothèques natives sont traitées comme une dépendance tierce. Toutefois, cette étape est illustrée pour le contexte.

Par souci de simplicité, la procédure pas à pas cible uniquement un sous-ensemble d’architectures. Pour iOS, il utilise l’utilitaire LiPo pour créer un seul binaire Fat à partir des fichiers binaires propres à l’architecture individuels. Android utilise des binaires dynamiques avec une extension. so et iOS utilise un binaire Fat statique avec une extension. a. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Étape 2 : encapsulation des bibliothèques natives avec une solution Visual Studio

L’étape suivante consiste à encapsuler les bibliothèques natives afin qu’elles soient facilement utilisées à partir de .NET. Pour ce faire, vous disposez d’une solution Visual Studio avec quatre projets. Un projet partagé contient le code commun. Les projets ciblant chacun des Xamarin. Android, Xamarin. iOS et .NET Standard permettent de référencer la bibliothèque de manière indépendante de la plateforme.

Le wrapper utilise «[The appât and Switch pli](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)», décrit par Paul Betts. Ce n’est pas le seul moyen, mais cela facilite la référence à la bibliothèque et évite d’avoir à gérer explicitement les implémentations spécifiques à la plateforme au sein de l’application consommatrice elle-même. L’astuce consiste essentiellement à s’assurer que les cibles (.NET Standard, Android, iOS) partagent le même espace de noms, le même nom d’assembly et la même structure de classe. Dans la mesure où NuGet préfèrera toujours une bibliothèque spécifique à la plateforme, la version de .NET Standard n’est jamais utilisée au moment de l’exécution.

La majeure partie du travail de cette étape se concentrera sur l’utilisation de P/Invoke pour appeler les méthodes de la bibliothèque native et la gestion des références aux objets sous-jacents. L’objectif est d’exposer les fonctionnalités de la bibliothèque au consommateur tout en éliminant toute complexité. Les développeurs Xamarin. Forms n’ont pas besoin de connaître le fonctionnement interne de la bibliothèque non managée. Elle doit être similaire à celle d’une autre bibliothèque C# gérée.

Finalement, la sortie de cette étape est un ensemble de bibliothèques .NET, une par cible, ainsi qu’un document NuSpec qui contient les informations requises pour générer le package à l’étape suivante.

**Étape 3 : empaquetage et push d’un package NuGet pour le wrapper .NET**

La troisième étape consiste à créer un package NuGet à l’aide des artefacts de build de l’étape précédente. Le résultat de cette étape est un package NuGet qui peut être consommé à partir d’une application Xamarin. La procédure pas à pas utilise un répertoire local pour servir de flux NuGet. En production, cette étape doit publier un package sur un flux NuGet public ou privé et doit être entièrement automatisée.

**Étape 4 : utilisation du package NuGet à partir d’une application Xamarin. Forms**

La dernière étape consiste à référencer et à utiliser le package NuGet à partir d’une application Xamarin. Forms. Pour cela, vous devez configurer le flux NuGet dans Visual Studio afin d’utiliser le flux défini à l’étape précédente.

Une fois le flux configuré, le package doit être référencé à partir de chaque projet dans l’application Xamarin. Forms multiplateforme. « The appât-and-Switch pli » fournit des interfaces identiques. la fonctionnalité de la bibliothèque Native peut donc être appelée à l’aide du code défini dans un emplacement unique.

Le référentiel de code source contient une [liste de lectures supplémentaires](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up) qui incluent des articles sur la configuration d’un flux NuGet privé sur Azure DevOps et la transmission du package vers ce flux. Si vous avez besoin d’un peu plus de temps de configuration qu’un répertoire local, ce type de flux est mieux adapté à un environnement de développement en équipe.

## <a name="walk-through"></a>Procédure pas à pas

Les étapes fournies sont spécifiques à **Visual Studio pour Mac**, mais la structure fonctionne également dans **Visual Studio 2017** .

### <a name="prerequisites"></a>Configuration requise

Pour suivre la procédure, le développeur a besoin des éléments suivants :

- [Ligne de commande NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Visual Studio* *pour Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Un [**compte de développeur Apple**](https://developer.apple.com/) actif est requis pour déployer des applications sur un iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Création des bibliothèques natives (étape 1)

La fonctionnalité de la bibliothèque native est basée sur l’exemple de [procédure pas à pas : création etC++utilisation d’une bibliothèque statique ()](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

Cette procédure pas à pas ignore la première étape, en générant les bibliothèques natives, car la bibliothèque est fournie en tant que dépendance tierce dans ce scénario. Les bibliothèques natives précompilées sont incluses avec l' [exemple de code](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) ou peuvent être [téléchargées](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) directement.

### <a name="working-with-the-native-library"></a>Utilisation de la bibliothèque Native

L’exemple de code *MathFuncsLib* d’origine comprend une classe unique appelée `MyMathFuncs` avec la définition suivante :

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

Une classe supplémentaire définit des fonctions wrapper qui permettent à un consommateur .NET de créer, de supprimer et d’interagir avec la classe de `MyMathFuncs` Native sous-jacente.

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

Il s’agira des fonctions wrapper utilisées côté [Xamarin](https://visualstudio.microsoft.com/xamarin/) .

## <a name="wrapping-the-native-library-stage-2"></a>Encapsulation de la bibliothèque native (étape 2)

Cette étape nécessite les [bibliothèques précompilées](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) décrites dans la [section précédente](#creating-the-native-libraries-stage-1).

### <a name="creating-the-visual-studio-solution"></a>Création de la solution Visual Studio

1. Dans **Visual Studio pour Mac**, cliquez sur **nouveau projet** (dans la *page Bienvenue*) ou **nouvelle solution** (à partir du menu *fichier* ).
2. Dans la fenêtre **nouveau projet** , choisissez **projet partagé** (dans la *bibliothèque multiplateforme >* ), puis cliquez sur **suivant**.
3. Mettez à jour les champs suivants, puis cliquez sur **créer**:

    - **Nom du projet :** MathFuncs. Shared  
    - **Nom de la solution :** MathFuncs  
    - **Emplacement :** Utiliser l’emplacement d’enregistrement par défaut (ou choisir une alternative)   
    - **Créez un projet dans le répertoire de la solution :** Activer la case à cocher
4. Dans **Explorateur de solutions**, double-cliquez sur le projet **MathFuncs. Shared** et accédez à **principaux paramètres**.
5. Supprimez **. Partagé** à partir de l' **espace de noms par défaut** afin qu’il soit défini sur **MathFuncs** uniquement, puis cliquez sur **OK**.
6. Ouvrez **MyClass.cs** (créé par le modèle), puis renommez la classe et le nom de fichier en **MyMathFuncsWrapper** et remplacez l’espace de noms par **MathFuncs**.
7. **Ctrl + clic** sur la solution **MathFuncs**, puis choisissez **Ajouter un nouveau projet...** dans le menu **Ajouter** .
8. Dans la fenêtre **nouveau projet** , choisissez **bibliothèque de .NET standard** (dans la *bibliothèque multiplateforme >* ), puis cliquez sur **suivant**.
9. Choisissez **.NET Standard 2,0** , puis cliquez sur **suivant**.
10. Mettez à jour les champs suivants, puis cliquez sur **créer**:

    - **Nom du projet :** MathFuncs. standard  
    - **Emplacement :** Utiliser le même emplacement d’enregistrement que le projet partagé   

11. Dans **Explorateur de solutions**, double-cliquez sur le projet **MathFuncs. standard** .
12. Accédez à **principaux paramètres**, puis mettez à jour **espace de noms par défaut** sur **MathFuncs**.
13. Accédez aux paramètres de **sortie** , puis mettez à jour le nom de l' **assembly** sur **MathFuncs**.
14. Accédez aux paramètres **du compilateur** , modifiez la **configuration** en **Release**, en définissant les **informations de débogage** sur **symboles uniquement** , puis cliquez sur **OK**.
15. Suppression de **Class1.cs/Getting démarrée** à partir du projet (si l’un de ces éléments a été inclus dans le modèle).
16. **Ctrl + clic** dans le dossier **dépendances/références** du projet, puis choisissez **modifier les références**.
17. Sélectionnez **MathFuncs. Shared** sous l’onglet **projets** , puis cliquez sur **OK**.
18. Répétez les étapes 7-17 (en ignorant l’étape 9) à l’aide des configurations suivantes :

    | **NOM DU PROJET**  | **NOM DU MODÈLE**   | **MENU NOUVEAU PROJET**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs. Android | Bibliothèque de classes       | Bibliothèque de > Android      |
    | MathFuncs. iOS     | Bibliothèque de liaisons     | Bibliothèque de > iOS          |

19. Dans **Explorateur de solutions**, double-cliquez sur le projet **MathFuncs. Android** , puis accédez aux paramètres **du compilateur** .

20. Avec la **configuration** définie à **Déboguer**, modifiez **définir les symboles** pour inclure **Android ;** .

21. Modifiez la **configuration** en **Release**, puis modifiez **définir les symboles** pour inclure également **Android ;** .

22. Répétez les étapes 19-20 pour **MathFuncs. iOS**, en modifiant **définir les symboles** pour inclure **iOS ;** au lieu d' **Android ;** dans les deux cas.

23. Générez la solution dans la configuration **Release** (**Ctrl + Commande + B**) et vérifiez que les trois assemblys de sortie (Android, iOS, .NET standard) (dans les dossiers bin de projet respectifs) partagent le même nom **MathFuncs. dll**.

À ce niveau, la solution doit avoir trois cibles, une pour Android, iOS et .NET Standard, et un projet partagé qui est référencé par chacune des trois cibles. Ils doivent être configurés pour utiliser le même espace de noms par défaut et les mêmes assemblys de sortie portant le même nom. Cela est nécessaire pour l’approche « appât et commutateur » mentionnée précédemment.

### <a name="adding-the-native-libraries"></a>Ajout des bibliothèques natives

Le processus d’ajout des bibliothèques natives à la solution Wrapper varie légèrement entre Android et iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Références natives pour MathFuncs. Android

1. **Ctrl + clic** sur le projet **MathFuncs. Android** , puis choisissez **nouveau dossier** dans le menu **Ajouter** attribution de **noms.**

2. Pour chaque **Abi** (interface binaire d’application), appuyez sur **Ctrl + clic** sur le dossier **libs** , puis choisissez **nouveau dossier** dans le menu **Ajouter** , en le nommant après cet **Abi**. Dans ce cas :

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Pour obtenir une vue d’ensemble plus détaillée, consultez la rubrique [architectures et processeurs](https://developer.android.com/ndk/guides/arch) dans le [Guide du développeur NDK](https://developer.android.com/ndk/guides/), en particulier la section relative à l’adressage du [code natif dans les packages d’application](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Vérifiez la structure de dossiers :  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Ajoutez les bibliothèques **. so** associées à chacun des dossiers **Abi** en fonction du mappage suivant :

    **arm64-V8A :** libs/Android/arm64

    **ARMEABI-V7A :** libs/Android/ARM  

    **x86 :** libs/Android/x86

    **x86_64 :** libs/Android/x86_64

    > [!NOTE]
    > Pour ajouter des fichiers, appuyez sur **Ctrl et cliquez** sur le dossier représentant l' **Abi**respectif, puis choisissez **Ajouter des fichiers...** dans le menu **Ajouter** . Choisissez la bibliothèque appropriée (à partir du répertoire **PrecompiledLibs** ), cliquez sur **ouvrir** , puis sur **OK** en laissant l’option par défaut pour *copier le fichier dans le répertoire*.

5. Pour chacun des fichiers **. so** , **Ctrl + clic** , puis choisissez l’option **EmbeddedNativeLibrary** dans le menu **action de génération** .

Le dossier **libs** doit maintenant s’afficher comme suit :

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

#### <a name="native-references-for-mathfuncsios"></a>Références natives pour MathFuncs. iOS

1. **Ctrl + clic** sur le projet **MathFuncs. iOS** , puis choisissez **Ajouter une référence Native** dans le menu **Ajouter** . 
2. Choisissez la bibliothèque **libMathFuncs. a** (à partir de libs/iOS sous le répertoire **PrecompiledLibs** ), puis cliquez sur **ouvrir** . 
3. **Ctrl + clic** sur le fichier **libMathFuncs** (dans le dossier **références natives** , puis choisissez l’option **Propriétés** dans le menu  
4. Configurez les propriétés de la **référence Native** afin qu’elles soient activées (avec une icône représentant une coche) dans le panneau **Propriétés** :

    - Forcer le chargement
    - NonC++
    - Liaison intelligente

    > [!NOTE]
    > L’utilisation d’un type de projet de bibliothèque de liaison avec une [référence Native](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) incorpore la bibliothèque statique et lui permet d’être liée automatiquement à l’application Xamarin. IOS qui la référence (même si elle est incluse via un package NuGet).

5. Ouvrez **ApiDefinition.cs**, en supprimant le code commenté par un modèle (en laissant uniquement l’espace de noms `MathFuncs`), puis effectuez la même étape pour **structs.cs** 

    > [!NOTE]
    > Un projet de bibliothèque de liaisons requiert ces fichiers (avec les actions de génération *ObjCBindingApiDefinition* et *ObjCBindingCoreSource* ) afin de générer. Toutefois, nous allons écrire le code, pour appeler notre bibliothèque native, en dehors de ces fichiers d’une manière qui peut être partagée entre les cibles de bibliothèque Android et iOS à l’aide de P/Invoke standard.

### <a name="writing-the-managed-library-code"></a>Écriture du code de la bibliothèque managée

À présent, écrivez C# le code pour appeler la bibliothèque native. L’objectif est de masquer toute complexité sous-jacente. Le consommateur ne doit pas avoir de connaissances pratiques sur les éléments internes de la bibliothèque native ou sur les concepts P/Invoke.  

#### <a name="creating-a-safehandle"></a>Création d’un SafeHandle

1. **Ctrl + clic** sur le projet **MathFuncs. Shared** , puis choisissez **Ajouter un fichier...** dans le menu **Ajouter** . 
2. Choisissez **classe vide** dans la fenêtre **nouveau fichier** , nommez-la **MyMathFuncsSafeHandle** , puis cliquez sur **nouveau**
3. Implémentez la classe **MyMathFuncsSafeHandle** :

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
    > Un [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) est la meilleure façon de travailler avec des ressources non managées dans du code managé. Cela élimine un grand nombre de code réutilisable lié à la finalisation critique et au cycle de vie des objets. Le propriétaire de ce descripteur peut ensuite le traiter comme n’importe quelle autre ressource gérée et n’a pas besoin d’implémenter le [modèle entièrement jetable](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose). 

#### <a name="creating-the-internal-wrapper-class"></a>Création de la classe wrapper interne

1. Ouvrez **MyMathFuncsWrapper.cs**, en le remplaçant par une classe statique interne

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. Dans le même fichier, ajoutez l’instruction conditionnelle suivante à la classe :

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Cela définit la valeur de constante **DllName** selon que la bibliothèque est en cours de génération pour **Android** ou **iOS**. Cela permet de traiter les différentes conventions d’affectation de noms utilisées par chaque plateforme respective, mais également le type de bibliothèque utilisé dans ce cas. Android utilise une bibliothèque dynamique et attend un nom de fichier, y compris l’extension. Pour iOS, ' *__Internal*'est nécessaire, car nous utilisons une bibliothèque statique.

3. Ajoutez une référence à **System. Runtime. InteropServices** en haut du fichier **MyMathFuncsWrapper.cs**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Ajoutez les méthodes wrapper pour gérer la création et la suppression de la classe **MyMathFuncs** :

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Nous transmettons notre constante **DllName** à l’attribut **DllImport** avec le point d' **entrée** qui indique explicitement au Runtime .net le nom de la fonction à appeler dans cette bibliothèque. Techniquement, nous n’avons pas besoin de fournir la valeur **entryPoint** si nos noms de méthode managée étaient identiques à ceux non managés. Si aucun n’est fourni, le nom de la méthode managée est utilisé à la place en tant que **point d’entrée** . Toutefois, il est préférable d’être explicite.  

5. Ajoutez les méthodes wrapper pour nous permettre d’utiliser la classe **MyMathFuncs** à l’aide du code suivant :

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
    > Nous utilisons des types simples pour les paramètres de cet exemple. Dans la mesure où le marshaling est une copie au niveau du bit dans ce cas, il ne nécessite aucun travail supplémentaire de notre part. Notez également l’utilisation de la classe **MyMathFuncsSafeHandle** au lieu du **IntPtr**standard. **IntPtr** est mappé automatiquement au **SafeHandle** dans le cadre du processus de marshaling.

6. Vérifiez que la classe **MyMathFuncsWrapper** terminée s’affiche comme ci-dessous :

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

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Exécution de la classe MyMathFuncsSafeHandle

1. Ouvrez la classe **MyMathFuncsSafeHandle** , accédez à l’espace réservé **TODO** commentaire dans la méthode **ReleaseHandle** :

    ```csharp
    // TODO: Release the handle here
    ```

1. Remplacez la ligne **TODO** :

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Écriture de la classe MyMathFuncs

Maintenant que le wrapper est terminé, créez une classe MyMathFuncs qui gérera la référence à l’objet C++ MyMathFuncs non managé.  

1. **Ctrl + clic** sur le projet **MathFuncs. Shared** , puis choisissez **Ajouter un fichier...** dans le menu **Ajouter** . 
2. Choisissez **classe vide** dans la fenêtre **nouveau fichier** , nommez-la **MyMathFuncs** , puis cliquez sur **nouveau**
3. Ajoutez les membres suivants à la classe **MyMathFuncs** :

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implémentez le constructeur pour la classe afin qu’il crée et stocke un handle vers l’objet **MyMathFuncs** natif lorsque la classe est instanciée :

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implémentez l’interface **IDisposable** à l’aide du code suivant :

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

6. Implémentez les méthodes **MyMathFuncs** à l’aide de la classe **MyMathFuncsWrapper** pour effectuer le travail réel dans le capot en passant le pointeur que nous avons stocké à l’objet non managé sous-jacent. Le code doit se présenter comme suit :

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

#### <a name="creating-the-nuspec"></a>Création du NuSpec

Pour que la bibliothèque soit empaquetée et distribuée via NuGet, la solution a besoin d’un fichier **NuSpec** . Cela permet d’identifier les assemblys qui seront inclus pour chaque plateforme prise en charge.

1. **Ctrl + clic** sur la solution **MathFuncs**, puis choisissez **Ajouter un dossier de solution** dans le menu **Ajouter** attribution de nom **SolutionItems**.
2. **Ctrl + clic** sur le dossier **SolutionItems** , puis choisissez **nouveau fichier...** dans le menu **Ajouter** .
3. Choisissez **fichier XML vide** dans la fenêtre **nouveau fichier** , nommez-le **MathFuncs. NuSpec** , puis cliquez sur **nouveau**.
4. Mettez à jour **MathFuncs. NuSpec** avec les métadonnées de package de base à afficher au consommateur **NuGet** . Exemple :

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
    > Consultez le document de [référence NuSpec](https://docs.microsoft.com/nuget/reference/nuspec) pour plus d’informations sur le schéma utilisé pour ce manifeste.

5. Ajoutez un élément `<files>` en tant qu’enfant de l’élément `<package>` (juste en dessous `<metadata>`), en identifiant chaque fichier avec un élément `<file>` distinct :

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Lorsqu’un package est installé dans un projet et que plusieurs assemblys sont spécifiés par le même nom, NuGet choisit en fait l’assembly qui est le plus spécifique à la plateforme donnée.

6. Ajoutez les éléments `<file>` pour les assemblys **Android** :

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Ajoutez les éléments `<file>` pour les assemblys **iOS** :

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Ajoutez les éléments `<file>` pour les assemblys **netstandard 2.0** :

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Vérifiez le manifeste **NuSpec** :

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
    > Ce fichier spécifie les chemins de sortie de l’assembly à partir d’une version **Release** . par conséquent, veillez à générer la solution à l’aide de cette configuration.

À ce stade, la solution contient 3 assemblys .NET et un manifeste **NuSpec** de prise en charge.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribution du wrapper .NET avec NuGet

L’étape suivante consiste à empaqueter et distribuer le package NuGet afin qu’il puisse être facilement consommé par l’application et géré comme une dépendance. L’encapsulation et la consommation peuvent toutes être effectuées au sein d’une solution unique, mais la distribution de la bibliothèque par le biais d’outils NuGet facilite le découplage et nous permet de gérer ces codes de base indépendamment.

### <a name="preparing-a-local-packages-directory"></a>Préparation d’un répertoire de packages locaux

La forme la plus simple du flux NuGet est un répertoire local :

1. Dans **Finder**, accédez à un répertoire pratique. Par exemple, **/Users**.
2. Choisissez **nouveau dossier** dans le menu **fichier** , en fournissant un nom explicite, par exemple **local-NuGet-Feed**.

### <a name="creating-the-package"></a>Création du package

1. Définissez la **configuration de build** sur **Release**, puis exécutez une génération à l’aide de la **commande + B**.
2. Ouvrez le **Terminal** et accédez au répertoire contenant le fichier **NuSpec** .
3. Dans **Terminal**, exécutez la commande du **Pack NuGet** en spécifiant le fichier **NuSpec** , la **version** (par exemple, 1.0.0) et le **outputDirectory** à l’aide du dossier créé à l' [étape précédente](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed), autrement dit, **local-NuGet-Feed**. Exemple :

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Vérifiez** que **MathFuncs. 1.0.0. nupkg** a été créé dans le répertoire **local-NuGet-Feed** .

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>FACULTATIF Utilisation d’un flux NuGet privé avec Azure DevOps

Une technique plus robuste est décrite dans [prise en main des packages NuGet dans Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), qui montre comment créer un flux privé et transmettre le package (généré à l’étape précédente) à ce flux.

Il est idéal de faire en sorte que ce flux de travail soit entièrement automatisé, par exemple à l’aide de [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Pour plus d’informations, consultez la page [prise en main de Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Utilisation du wrapper .NET à partir d’une application Xamarin. Forms

Pour effectuer la procédure pas à pas, créez une application **Xamarin. Forms** pour utiliser le package qui vient d’être publié dans le flux **NuGet** local.

### <a name="creating-the-xamarinforms-project"></a>Création du projet **Xamarin. Forms**

1. Ouvrez une nouvelle instance de **Visual Studio pour Mac**. Pour ce faire, vous pouvez utiliser le **Terminal**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. Dans **Visual Studio pour Mac**, cliquez sur **nouveau projet** (dans la *page Bienvenue*) ou **nouvelle solution** (à partir du menu *fichier* ).
3. Dans la fenêtre **nouveau projet** , choisissez **application Formulaires vide** (dans l' *application > multiplateformes*), puis cliquez sur **suivant**.
4. Mettez à jour les champs suivants, puis cliquez sur **suivant**:

    - **Nom de l’application :** MathFuncsApp.
    - **Identificateur de l’Organisation :** Utilisez un espace de noms inversé, par exemple _com. {your_org}_ .
    - **Plateformes cibles :** Utilisez la valeur par défaut (les cibles Android et iOS).
    - **Code partagé :** Définissez cette valeur sur .NET Standard (une solution « bibliothèque partagée » est possible, mais au-delà de la portée de cette procédure pas à pas).

5. Mettez à jour les champs suivants, puis cliquez sur **créer**:

    - **Nom du projet :** MathFuncsApp.
    - **Nom de la solution :** MathFuncsApp.  
    - **Emplacement :** Utilisez l’emplacement d’enregistrement par défaut (ou choisissez une alternative).

6. Dans **Explorateur de solutions**, appuyez sur **Ctrl + clic** sur la cible (**MathFuncsApp. Android** ou **MathFuncs. iOS**) pour le test initial, puis choisissez **définir comme projet de démarrage**.
7. Choisissez l' **appareil** ou le **simulateur**/**émulateur**par défaut. 
8. Exécutez la solution (**commande + retour**) pour confirmer que le projet **Xamarin. Forms** basé sur un modèle est généré et s’exécute. 

    > [!NOTE]
    > **iOS** (le simulateur) a tendance à avoir le temps de build/déploiement le plus rapide.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Ajout du flux NuGet local à la configuration NuGet

1. Dans **Visual Studio**, choisissez **Préférences** (dans le menu **Visual Studio** ).
2. Choisissez **sources** dans la section **NuGet** , puis cliquez sur **Ajouter**.
3. Mettez à jour les champs suivants, puis cliquez sur **Ajouter une source**:

    - **Nom :** Fournissez un nom explicite, par exemple, local-packages.  
    - **Emplacement :** Spécifiez le dossier **local-NuGet-Feed** créé à l' [étape précédente](#preparing-a-local-packages-directory).

    > [!NOTE]
    > Dans ce cas, il est inutile de spécifier un **nom d’utilisateur** et un **mot de passe**. 

4. Cliquez sur **OK**.

### <a name="referencing-the-package"></a>Référencement du package

Répétez les étapes suivantes pour chaque projet (**MathFuncsApp**, **MathFuncsApp. Android**et **MathFuncsApp. iOS**).

1. **Ctrl + clic** sur le projet, puis choisissez **Ajouter des packages NuGet...** dans le menu **Ajouter** .
2. Recherchez **MathFuncs**. 
3. Vérifiez que la **version** du package est **1.0.0** et que les autres détails s’affichent comme prévu, par exemple le **titre** et la **Description**, autrement dit, *MathFuncs* et *Sample C++ wrapper library*. 
4. Sélectionnez le package **MathFuncs** , puis cliquez sur **Ajouter un package**.

### <a name="using-the-library-functions"></a>Utilisation des fonctions de bibliothèque

Désormais, avec une référence au package **MathFuncs** dans chacun des projets, les fonctions sont disponibles pour le C# code.

1. Ouvrez **MainPage.Xaml.cs** dans le projet **MathFuncsApp** common **Xamarin. Forms**(référencé par **MathFuncsApp. Android** et **MathFuncsApp. iOS**).
2. Ajoutez des instructions **using** pour **System. Diagnostics** et **MathFuncs** en haut du fichier :

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Déclarez une instance de la classe `MyMathFuncs` en haut de la classe `MainPage` :

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Substituez les méthodes `OnAppearing` et `OnDisappearing` à partir de la classe de base `ContentPage` :

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

5. Mettez à jour la méthode `OnAppearing` pour initialiser la variable `myMathFuncs` déclarée précédemment :

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Mettez à jour la méthode `OnDisappearing` pour appeler la méthode `Dispose` sur `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implémentez une méthode privée appelée **TestMathFuncs** comme suit :

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

8. Enfin, appelez `TestMathFuncs` à la fin de la méthode `OnAppearing` :

    ```csharp
    TestMathFuncs();
    ```

9. Exécutez l’application sur chaque plateforme cible et validez la sortie dans le bloc de sortie de l' **application** , comme suit :

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Si vous rencontrez un «*DLLNotFoundException*» lors des tests sur Android ou une erreur de génération sur iOS, veillez à vérifier que l’architecture de l’UC de l’appareil/de l’émulateur/simulateur que vous utilisez est compatible avec le sous-ensemble que nous avons choisi de prendre en charge. 

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment créer une application Xamarin. Forms qui utilise des bibliothèques natives via un wrapper .NET commun distribué via un package NuGet. L’exemple fourni dans cette procédure pas à pas est intentionnellement très simpliste pour démontrer plus facilement l’approche. Une application réelle devra gérer des complexités telles que la gestion des exceptions, les rappels, le marshaling de types plus complexes et la liaison avec d’autres bibliothèques de dépendances. Un point important à prendre en compte est le processus par lequel C++ l’évolution du code est coordonnée et synchronisée avec les applications du wrapper et du client. Ce processus peut varier selon que l’une ou l’autre de ces préoccupations est la responsabilité d’une seule équipe. Dans les deux cas, l’automatisation est un véritable avantage. Voici quelques ressources qui fournissent des informations supplémentaires sur certains concepts clés, ainsi que les téléchargements correspondants. 

### <a name="downloads"></a>Téléchargements

- [Outils de ligne de commande NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Exemples

- [Développement mobile inter-plateformes avecC++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft pix (C++ et Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Port d’exemple San Angeles mono](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>informations supplémentaires

[Articles relatifs au contenu de cette publication](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)