---
title: Partie 1 – Présentation de la plateforme Mobile Xamarin
description: Ce document décrit la plateforme Xamarin à un niveau élevé, en examinant le processus de compilation, accès SDK de plateforme, partage de code, création de l’interface utilisateur, les concepteurs visuels et bien plus encore.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c7c0f582ac4a7dc8571fbc607dba9b0ad97d49e1
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674848"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Partie 1 – Présentation de la plateforme Mobile Xamarin

La plateforme Xamarin comprend un certain nombre d’éléments qui vous permettent de développer des applications pour iOS et Android :

-   **C#langage** – vous permet d’utiliser une syntaxe familière et des fonctionnalités sophistiquées telles que les génériques, LINQ et la bibliothèque parallèle de tâches.
-   **Mono .NET framework** – fournit une implémentation multiplateforme de fonctionnalités étendues dans Microsoft .NET framework.
-   **Compilateur** : selon la plateforme, génère une application native (par ex. iOS) ou une application .NET intégrée et le runtime (par exemple). Android). Le compilateur effectue également le nombre d’optimisations pour le déploiement mobile tels que la liaison du code non utilisé absent.
-   **Outils de l’IDE** – Visual Studio sur Mac et Windows vous permet de créer, générer et déployer des projets Xamarin.

En outre, étant donné que le langage sous-jacent est C# avec le .NET framework, les projets peuvent être structurés pour partager du code qui peut également être déployée sur Windows Phone.

## <a name="under-the-hood"></a>Sous le capot

Bien que Xamarin vous permet d’écrire des applications C#et partager le même code entre plusieurs plateformes, l’implémentation réelle sur chaque système est très différente.

## <a name="compilation"></a>Compilation

Le C# source fait dans une application native de façons très différentes sur chaque plateforme :

-   **iOS** – C# est ahead-of-time (AOT) compilée en langage d’assembly ARM. Le .NET framework est inclus, et les classes inutilisées en cours sont supprimés lors de la liaison pour réduire la taille de l’application. Apple n’autorise pas la génération de code de runtime sur iOS, certaines fonctionnalités de langage sont donc pas disponibles (voir [Xamarin.iOS Limitations](~/ios/internals/limitations.md) ).
-   **Android** – C# est compilée en langage intermédiaire et empaquetées avec MonoVM + JIT'ing. Les classes inutilisées dans le framework sont supprimés lors de la liaison. L’application s’exécute côté à côte avec Java/ART (runtime Android) et interagit avec les types natifs via JNI (consultez [Xamarin.Android Limitations](~/android/internals/limitations.md) ).
-   **Windows** – C# est compilé en langage intermédiaire et exécuté par le runtime intégré et ne nécessite pas d’outils Xamarin. Conception d’applications de Windows les conseils suivants Xamarin, plus facile de réutiliser le code sur iOS et Android.
  Notez que la plateforme Windows universelle a également un **.NET Native** option qui se comporte comme la compilation AOT des Xamarin.iOS.


La documentation de l’éditeur de liens pour [Xamarin.iOS](~/ios/deploy-test/linker.md) et [Xamarin.Android](~/android/deploy-test/linker.md) fournit plus d’informations sur cette partie du processus de compilation.

Runtime 'compilation' – générer du code dynamiquement avec `System.Reflection.Emit` – doit être évitée.

Noyau de d’Apple empêche la génération de code dynamique sur les appareils iOS, par conséquent émission de code à la volée ne fonctionnera pas dans Xamarin.iOS. De même, les fonctionnalités du Dynamic Language Runtime ne peut pas être utilisées avec les outils Xamarin.

Certaines fonctionnalités de réflexion ne fonctionnent pas (par exemple). MonoTouch.Dialog l’utilise pour l’API de réflexion), mais pas la génération de code.

## <a name="platform-sdk-access"></a>Accès SDK de plateforme

Xamarin rend les fonctionnalités fournies par le Kit de développement spécifiques à la plateforme facilement accessible avec classique C# syntaxe :

-   **iOS** – Xamarin.iOS expose les infrastructures de CocoaTouch SDK d’Apple sous la forme d’espaces de noms que vous pouvez référencer depuis C#. Par exemple, le framework UIKit qui contient tous les contrôles d’interface utilisateur peut être inclus avec un simple `using UIKit;` instruction.
-   **Android** – Xamarin.Android expose SDK Android de Google en tant qu’espaces de noms, donc vous pouvez référencer n’importe quelle partie du Kit de développement pris en charge avec un à l’aide de l’instruction, telles que `using Android.Views;` pour accéder aux contrôles d’interface utilisateur.
-   **Windows** – les applications Windows sont créées à l’aide de Visual Studio sur Windows. Types de projets incluent des Windows Forms, WPF, WinRT et la plateforme universelle Windows (UWP).

## <a name="seamless-integration-for-developers"></a>Intégration transparente pour les développeurs

L’avantage de Xamarin est que malgré les différences sous le capot, Xamarin.iOS et Xamarin.Android (couplées avec les kits de développement logiciel de Microsoft Windows) offrent une expérience transparente pour l’écriture C# code qui peut être réutilisé sur toutes les trois plateformes.

Logique métier, l’utilisation de base de données, accès au réseau et autres fonctions courantes peuvent écrites qu’une seule fois et réutilisées sur chaque plateforme, en fournissant une fondation pour les interfaces utilisateur spécifiques à la plateforme dont l’apparence et l’exécuter en tant qu’applications natives.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilité de l’environnement (IDE) de développement intégré

Développement de Xamarin peut être effectué dans Visual Studio sur Mac ou Windows. L’IDE que vous choisissez sera déterminée par les plateformes que vous souhaitez cibler.

Étant donné que les applications Windows peuvent uniquement être développées sur Windows, de génération pour iOS, Android, _et_ Windows requiert Visual Studio pour Windows. Toutefois, il est possible de partager des projets et des fichiers entre des ordinateurs Windows et Mac pour les applications iOS et Android peuvent reposer sur un Mac et le code partagé peut être ajouté ultérieurement à un projet Windows.

Les exigences de développement pour chaque plateforme sont abordées plus en détail dans le [exigence](~/cross-platform/get-started/requirements.md) guide.

### <a name="ios"></a>iOS

Développement d’applications iOS nécessite un ordinateur Mac, macOS en cours d’exécution. Vous pouvez également utiliser Visual Studio pour écrire et de déployer des applications iOS avec Xamarin dans Visual Studio. Toutefois, un Mac est toujours nécessaire pour la génération et à des fins de gestion des licences.

Xcode IDE d’Apple doit être installé pour fournir le compilateur et le simulateur pour le test. Vous pouvez tester sur vos propres appareils [gratuitement](~/ios/get-started/installation/device-provisioning/free-provisioning.md), mais pour créer des applications pour la distribution (par exemple). l’App Store), vous devez joindre le programme Developer d’Apple (99 USD par an). Chaque fois que vous envoyez ou mettre à jour une application, il doit être révisé et approuvé par Apple avant d’être rendu disponible en téléchargement.

Code est écrit avec l’IDE Visual Studio et dispositions d’écran peuvent être générées par programme ou modifiées à l’aide iOS de Xamarin concepteur dans l’IDE.

Reportez-vous à la [Guide d’Installation de Xamarin.iOS](~/ios/get-started/installation/index.md) pour obtenir des instructions détaillées sur la configuration d’un projet.

### <a name="android"></a>Android

Développement d’applications Android nécessite le Java et les kits Android SDK doit être installé. Ils fournissent le compilateur, émulateur et autres outils requis pour la création, déploiement et test. Outils Java, de Google Android SDK et de Xamarin peuvent tous être installés et exécutés sur Windows et macOS. Les configurations suivantes sont recommandées :

- Windows 10 avec Visual Studio 2019
- macOS Mojave (10.11 +) avec Visual Studio 2019 pour Mac

Xamarin fournit un programme d’installation unifiée qui permettra de configurer votre système avec le conditions préalable Java, les outils de Xamarin et Android (y compris un concepteur visuel pour les dispositions d’écran). Reportez-vous à la [Guide d’Installation de Xamarin.Android](~/android/get-started/installation/index.md) pour obtenir des instructions détaillées.

Vous pouvez générer et tester des applications sur un appareil réel sans aucune licence à partir de Google, toutefois pour distribuer votre application via un magasin (tels que Google Play, Amazon ou Barnes &amp; Noble) un frais d’inscription peuvent être dus à l’opérateur. Google Play publier votre application instantanément, tandis que les autres magasins ont un processus d’approbation similaire d’Apple.

### <a name="windows"></a>Windows

Les applications Windows (WinForms, WPF ou UWP) sont créées avec Visual Studio. Elles n’utilisent pas directement de Xamarin. Toutefois, C# code peut être partagé entre Windows, iOS et Android.
Visitez Microsoft [centre de développement](https://developer.microsoft.com/) pour en savoir plus sur les outils nécessaires pour le développement de Windows.

## <a name="creating-the-user-interface-ui"></a>Création de l'interface utilisateur

Des principaux avantages de l’utilisation de Xamarin sont que l’interface utilisateur utilise les contrôles natifs sur chaque plateforme, créer des applications qui ne se distinguent pas à partir d’une application écrite en Objective-C ou Java (pour iOS et Android respectivement).

Lors de la création d’écrans dans votre application, vous pouvez disposer les contrôles dans le code ou créer des écrans complètes à l’aide des outils de conception disponibles pour chaque plateforme.

### <a name="create-controls-programmatically"></a>Créer des contrôles par programmation

Chaque plateforme permet à des contrôles d’interface à ajouter à un écran à l’aide de code utilisateur. Ceci peut être très longue car il peut être difficile de visualiser la conception lorsque coordonne les pixels de coder en dur pour les tailles et positions de contrôle.

Création par programmation de contrôles a avantages Cependant, en particulier sur iOS pour la création de vues qui redimensionnent ou de s’afficher différemment sur les tailles d’écran iPhone et iPad.

### <a name="visual-designer"></a>Concepteur visuel

Chaque plateforme a une méthode différente pour la disposition visuelle des écrans :

- **iOS** – iOS de Xamarin concepteur facilite la création des vues à l’aide des champs de fonctionnalités et de la propriété de glisser-déplacer. Collectivement ces vues constituent un Storyboard et sont accessibles dans le **. Table de montage séquentiel** fichier qui est inclus dans votre projet.
- **Android** – Xamarin fournit un concepteur d’interface utilisateur glisser-déplacer Android pour Visual Studio. Dispositions d’écran Android sont enregistrées en tant que **. AXML** fichiers lors de l’utilisation des outils Xamarin.
- **Windows** – Microsoft fournit un concepteur d’interface utilisateur glisser-déplacer dans Visual Studio et Blend. Les dispositions d’écran sont stockées en tant que. Fichiers XAML.

Ces captures d’écran montrent les concepteurs visual écran disponibles sur chaque plateforme :

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "Ces captures d’écran affichent les concepteurs visual écran disponibles sur chaque plateforme")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

Dans tous les cas, les éléments que vous créez visuellement peuvent être référencées dans votre code.

### <a name="user-interface-considerations"></a>Considérations sur l’Interface utilisateur

Des principaux avantages de l’utilisation de Xamarin pour créer des applications inter-plateformes sont qu’ils peuvent tirer parti des boîtes à outils de l’interface utilisateur native pour présenter une interface familière à l’utilisateur. L’interface utilisateur sera également effectuer aussi rapidement que toute autre application native.

Des métaphores d’interface utilisateur fonctionnent sur plusieurs plateformes (par exemple, les trois plateformes utilisent un contrôle de liste de défilement similaire) mais afin que votre application se sentir droit l’interface utilisateur doit tirer parti d’utilisateur spécifiques à la plateforme des éléments d’interface lorsque cela est approprié. Voici quelques exemples des métaphores d’interface utilisateur spécifiques à la plateforme :

- **iOS** – navigation hiérarchique avec le bouton précédent réversible, onglets au bas de l’écran.
- **Android** : matériel/logiciel système nouveau bouton, menu action, onglets en haut de l’écran.
- **Windows** – applications de Windows peuvent s’exécuter sur les ordinateurs de bureau, les tablettes (par exemple, Microsoft Surface) et les téléphones portables. Appareils Windows 10 peuvent avoir bouton Précédent et des vignettes dynamiques, par exemple.

Il est recommandé de lire les instructions de conception pertinentes pour les plateformes que vous ciblez :

- **iOS** – [Human Interface Guidelines pour d’Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [indications de l’Interface utilisateur de Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [User Experience Design Guidelines for Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>Bibliothèque et réutiliser le Code

La plateforme Xamarin permet la réutilisation d’existant C# code toutes les plateformes, ainsi que l’intégration de bibliothèques écrites en mode natif pour chaque plateforme.

### <a name="c-source-and-libraries"></a>C#Source et bibliothèques

Étant donné que les produits Xamarin utilisent C# et le .NET framework, un grand nombre de code source existant (à la fois ouvrent la source et des projets en interne) peut être réutilisé dans les projets Xamarin.iOS ou Xamarin.Android. Souvent la source peut simplement être ajoutée à une solution Xamarin et il fonctionnera immédiatement. Si une fonctionnalité du framework .NET non pris en charge a été utilisée, quelques ajustements peuvent être nécessaire.

Exemples de C# source qui peut être utilisée dans Xamarin.iOS ou Xamarin.Android incluent : SQLite-NET, NewtonSoft.JSON et SharpZipLib.

### <a name="objective-c-bindings--binding-projects"></a>Liaisons objective-C + des projets de liaison

Xamarin fournit un outil appelé *btouch* que vous aide à créer des liaisons qui permettent de bibliothèques Objective-C à utiliser dans les projets Xamarin.iOS. Reportez-vous à la [documentation de la liaison des Types Objective-C](~/cross-platform/macios/binding/binding-types-reference.md) pour plus d’informations sur la façon de procéder.

Voici quelques exemples de bibliothèques Objective-C qui peuvent être utilisées dans Xamarin.iOS : Code-barres RedLaser analyse, intégration de Google Analytique et de PayPal. Liaisons de Xamarin.iOS Open source sont disponibles sur [github](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>les liaisons .jar + des projets de liaison

Xamarin prend en charge à l’aide de bibliothèques Java existantes dans Xamarin.Android. Reportez-vous à la [liaison une documentation de bibliothèque Java](~/android/platform/binding-java-library/index.md) pour plus d’informations sur l’utilisation d’un. Fichier JAR à partir de Xamarin.Android.

Liaisons de Xamarin.Android Open source sont disponibles sur [github](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C par le biais de PInvoke

La technologie « Platform Invoke » (P/Invoke) permet au code managé (C#) pour appeler des méthodes dans les bibliothèques natives ainsi que la prise en charge des bibliothèques natives effectuer un rappel dans du code managé.

Par exemple, le [SQLite-NET](https://github.com/praeclarum/sqlite-net) bibliothèque utilise des instructions telles que :

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Cette opération lie à l’implémentation de langage C SQLite native dans iOS et Android.
Les développeurs familiarisés avec une API C existante peuvent construire un ensemble de C# classes mapper à l’API native et utiliser le code existant de la plateforme. Il existe une documentation pour [liaison de bibliothèques natives](~/ios/platform/native-interop.md) dans Xamarin.iOS, les mêmes principes s’appliquent à Xamarin.Android.

### <a name="c-via-cppsharp"></a>C++ via CppSharp

Miguel explique CXXI (maintenant appelé [CppSharp](https://github.com/mono/CppSharp)) sur son [blog](https://tirania.org/blog/archive/2011/Dec-19.html). Une liaison directement à une bibliothèque C++ consiste à créer un wrapper de C et lier à celle via P/Invoke.
