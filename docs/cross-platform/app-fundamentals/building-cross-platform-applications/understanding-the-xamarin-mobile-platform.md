---
title: 'Partie 1 : comprendre la plate-forme mobile Xamarin'
description: Ce document décrit la plateforme Xamarin à un niveau élevé, en examinant le processus de compilation, l’accès au kit de développement logiciel (SDK) de plateforme, le partage de code, la création de l’interface utilisateur, les concepteurs visuels et bien plus encore.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: e10e9f5330de3226fb0f08051ab135ea58900fe7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016861"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Partie 1 : comprendre la plate-forme mobile Xamarin

La plateforme Xamarin est constituée d’un certain nombre d’éléments qui vous permettent de développer des applications pour iOS et Android :

- langage : vous permet d’utiliser une syntaxe familière et des fonctionnalités sophistiquées comme les génériques, LINQ et la bibliothèque de tâches parallèles. **C#**
- Le **.NET Framework mono** : fournit une implémentation multiplateforme des fonctionnalités étendues du .NET Framework de Microsoft.
- **Compilateur** : en fonction de la plateforme, produit une application native (par exemple, iOS) ou une application et un Runtime .NET intégrés (par exemple, Android). Le compilateur effectue également de nombreuses optimisations pour le déploiement mobile, telles que la liaison de code non utilisé.
- **Outils IDE** : Visual Studio sur Mac et Windows vous permet de créer, générer et déployer des projets Xamarin.

En outre, étant donné que le langage C# sous-jacent est le .NET Framework, les projets peuvent être structurés pour partager du code qui peut également être déployé sur Windows Phone.

## <a name="under-the-hood"></a>En coulisses

Bien que Xamarin vous permette d’écrire C#des applications dans et de partager le même code sur plusieurs plateformes, l’implémentation réelle sur chaque système est très différente.

## <a name="compilation"></a>Compilation

La C# source fait de son sens dans une application native de manière très différente sur chaque plateforme :

- **iOS** : C# est compilé dans le langage assembleur ARM (out-of-Time). Le .NET Framework est inclus, avec les classes inutilisées supprimées lors de la liaison pour réduire la taille de l’application. Apple n’autorise pas la génération de code d’exécution sur iOS, donc certaines fonctionnalités de langage ne sont pas disponibles (consultez [limitations de Xamarin. iOS](~/ios/internals/limitations.md) ).
- **Android** : C# est compilé en il et empaqueté avec MonoVM + JIT’ing. Les classes inutilisées dans le Framework sont supprimées lors de la liaison. L’application s’exécute côte à côte avec Java/ART (Runtime Android) et interagit avec les types natifs via JNI (consultez [limitations de Xamarin. Android](~/android/internals/limitations.md) ).
- **Windows** : C# est compilé en il et exécuté par le runtime intégré, et ne requiert pas d’outils Xamarin. La conception d’applications Windows après l’aide de Xamarin simplifie la réutilisation du code sur iOS et Android.
  Notez que le plateforme Windows universelle a également une option **.net Native** qui se comporte de la même façon que la compilation AOA de Xamarin. iOS.

La documentation de l’éditeur de liens pour [Xamarin. iOS](~/ios/deploy-test/linker.md) et [Xamarin. Android](~/android/deploy-test/linker.md) fournit plus d’informations sur cette partie du processus de compilation.

La « compilation » du Runtime (génération de code dynamique avec `System.Reflection.Emit`) doit être évitée.

Le noyau d’Apple empêche la génération de code dynamique sur les appareils iOS. par conséquent, l’émission de code à la volée ne fonctionnera pas dans Xamarin. iOS. De même, les fonctionnalités Dynamic Language Runtime ne peuvent pas être utilisées avec les outils Xamarin.

Certaines fonctionnalités de réflexion fonctionnent (par exemple, Unitouch. la boîte de dialogue l’utilise pour l’API de réflexion), mais pas la génération de code.

## <a name="platform-sdk-access"></a>Accès au kit de développement Platform SDK

Xamarin rend les fonctionnalités fournies par le kit de développement logiciel (SDK) spécifiques C# à la plateforme facilement accessibles avec une syntaxe familière :

- **iOS** : Xamarin. iOS expose les frameworks du kit de développement logiciel (SDK) d’Apple cocoatouch C#en tant qu’espaces de noms auxquels vous pouvez faire référence. Par exemple, l’infrastructure UIKit qui contient tous les contrôles d’interface utilisateur peut être incluse avec une instruction `using UIKit;` simple.
- **Android** : Xamarin. Android expose les Android SDK de Google en tant qu’espaces de noms. vous pouvez donc référencer n’importe quelle partie du kit de développement logiciel (SDK) pris en charge à l’aide d’une instruction using, telle que `using Android.Views;` pour accéder aux contrôles de l’interface utilisateur.
- **Windows** : les applications Windows sont créées à l’aide de Visual Studio sur Windows. Les types de projets incluent Windows Forms, WPF, WinRT et le plateforme Windows universelle (UWP).

## <a name="seamless-integration-for-developers"></a>Intégration transparente pour les développeurs

L’avantage de Xamarin est que malgré les différences qui existent dans le sens, Xamarin. iOS et Xamarin. Android (couplés aux kits de développement logiciel (SDK) de C# Microsoft Windows) offrent une expérience transparente pour l’écriture de code qui peut être réutilisé sur les trois plateformes.

La logique métier, l’utilisation de la base de données, l’accès réseau et d’autres fonctions courantes peuvent être écrites une seule fois et réutilisées sur chaque plateforme, ce qui fournit une base pour les interfaces utilisateur spécifiques à la plateforme qui s’affichent et s’exécutent en tant qu’applications natives.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilité de l’environnement de développement intégré (IDE)

Le développement Xamarin peut être effectué dans Visual Studio sur Mac ou Windows. L’IDE que vous choisissez sera déterminé par les plateformes que vous souhaitez cibler.

Étant donné que les applications Windows peuvent uniquement être développées sur Windows, la génération pour iOS, Android _et_ Windows nécessite Visual Studio pour Windows. Toutefois, il est possible de partager des projets et des fichiers entre des ordinateurs Windows et Mac. par conséquent, les applications iOS et Android peuvent être générées sur un Mac et le code partagé peut être ajouté ultérieurement à un projet Windows.

Les exigences de développement pour chaque plateforme sont présentées plus en détail dans le guide des [exigences](~/cross-platform/get-started/requirements.md) .

### <a name="ios"></a>iOS

Le développement d’applications iOS requiert un ordinateur Mac exécutant macOS. Vous pouvez également utiliser Visual Studio pour écrire et déployer des applications iOS avec Xamarin dans Visual Studio. Toutefois, un Mac est toujours nécessaire à des fins de création et de gestion des licences.

L’IDE Xcode d’Apple doit être installé pour fournir le compilateur et le simulateur à des fins de test. Vous pouvez tester [gratuitement](~/ios/get-started/installation/device-provisioning/free-provisioning.md)vos propres appareils, mais pour créer des applications pour la distribution (par exemple, l’App Store) vous devez rejoindre le programme de développement d’Apple ($99 USD par an). Chaque fois que vous envoyez ou mettez à jour une application, celle-ci doit être révisée et approuvée par Apple avant d’être mise à la disposition des clients pour le téléchargement.

Le code est écrit avec l’IDE de Visual Studio et les dispositions d’écran peuvent être créées par programmation ou modifiées avec le concepteur iOS de Xamarin dans l’un ou l’autre IDE.

Pour obtenir des instructions détaillées sur la configuration, reportez-vous au [Guide d’installation de Xamarin. iOS](~/ios/get-started/installation/index.md) .

### <a name="android"></a>Android

Le développement d’applications Android nécessite l’installation des kits de développement logiciel (SDK) Java et Android. Ils fournissent le compilateur, l’émulateur et d’autres outils requis pour la génération, le déploiement et le test. Java, les outils Android SDK de Google et Xamarin peuvent tous être installés et exécutés sur Windows et macOS. Les configurations suivantes sont recommandées :

- Windows 10 avec Visual Studio 2019
- macOS Mojave (10.11 +) avec Visual Studio 2019 pour Mac

Xamarin fournit un programme d’installation unifié qui configurera votre système avec les outils requis Java, Android et Xamarin (y compris un concepteur visuel pour les dispositions d’écran). Pour obtenir des instructions détaillées, reportez-vous au [Guide d’installation de Xamarin. Android](~/android/get-started/installation/index.md) .

Vous pouvez créer et tester des applications sur un appareil réel sans aucune licence de Google, mais pour distribuer votre application via un magasin (par exemple, Google Play, Amazon ou Barnes &amp; noble), un tarif d’inscription peut être payable à l’opérateur. Google Play publie votre application instantanément, tandis que les autres magasins ont un processus d’approbation similaire à celui d’Apple.

### <a name="windows"></a>Windows

Les applications Windows (WinForms, WPF ou UWP) sont créées avec Visual Studio. Ils n’utilisent pas directement Xamarin. Toutefois, C# le code peut être partagé entre Windows, iOS et Android.
Visitez le [Centre](https://developer.microsoft.com/) de développement de Microsoft pour en savoir plus sur les outils requis pour le développement Windows.

## <a name="creating-the-user-interface-ui"></a>Création de l'interface utilisateur

L’un des principaux avantages de l’utilisation de Xamarin est que l’interface utilisateur de l’application utilise des contrôles natifs sur chaque plateforme, créant des applications qui ne se distinguent pas d’une application écrite en Objective-C ou en Java (pour iOS et Android, respectivement).

Lorsque vous créez des écrans dans votre application, vous pouvez disposer les contrôles dans le code ou créer des écrans complets à l’aide des outils de conception disponibles pour chaque plateforme.

### <a name="create-controls-programmatically"></a>Créer des contrôles par programmation

Chaque plateforme autorise l’ajout de contrôles d’interface utilisateur à un écran à l’aide de code. Cela peut être très long, car il peut être difficile de visualiser la conception finie lors du codage en dur des coordonnées de pixel pour les positions et les tailles de contrôle.

La création de contrôles par programmation offre toutefois des avantages, en particulier sur iOS pour créer des vues qui se redimensionnent ou s’affichent différemment sur les tailles d’écran iPhone et iPad.

### <a name="visual-designer"></a>Concepteur visuel

Chaque plateforme a une méthode différente pour la disposition visuelle des écrans :

- **iOS** : le concepteur iOS d’Xamarin facilite la création d’affichages à l’aide des fonctionnalités de glisser-déplacer et des champs de propriété. Collectivement, ces vues composent une table de montage séquentiel et sont accessibles dans le **. Fichier de table de montage séquentiel** inclus dans votre projet.
- **Android** – Xamarin fournit un concepteur d’interface utilisateur par glisser-déplacer Android pour Visual Studio. Les dispositions d’écran Android sont enregistrées sous **. Fichiers AXML** lors de l’utilisation d’outils Xamarin.
- **Windows** – Microsoft fournit un concepteur d’interface utilisateur glisser-déplacer dans Visual Studio et Blend. Les dispositions d’écran sont stockées sous la forme. Fichiers XAML.

Ces captures d’écran montrent les concepteurs d’écran visuel disponibles sur chaque plateforme :

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "These screenshots show the visual screen designers available on each platform")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

Dans tous les cas, les éléments que vous créez visuellement peuvent être référencés dans votre code.

### <a name="user-interface-considerations"></a>Considérations relatives à l’interface utilisateur

L’un des principaux avantages de l’utilisation de Xamarin pour créer des applications multiplateformes est qu’ils peuvent tirer parti des kits d’outils d’interface utilisateur natifs pour présenter une interface familière à l’utilisateur. L’interface utilisateur s’exécute aussi rapidement que toute autre application native.

Certaines métaphores de l’interface utilisateur fonctionnent sur plusieurs plateformes (par exemple, les trois plateformes utilisent un contrôle de liste de défilement similaire), mais pour que votre application puisse « ressentir » le droit, l’interface utilisateur doit tirer parti des éléments d’interface utilisateur spécifiques à la plateforme, le cas échéant. Voici des exemples de métaphores d’interface utilisateur spécifiques à la plateforme :

- **iOS** : navigation hiérarchique avec bouton retour arrière, onglets en bas de l’écran.
- **Android** – matériel/système-bouton retour logiciel, menu Action, onglets en haut de l’écran.
- **Windows** : les applications Windows peuvent s’exécuter sur des ordinateurs de bureau, des tablettes (tels que Microsoft surface) et des téléphones. Les appareils Windows 10 peuvent avoir un bouton de retour matériel et des vignettes dynamiques, par exemple.

Nous vous recommandons de lire les instructions de conception relatives aux plateformes que vous ciblez :

- **iOS** : [instructions relatives à l’interface humaine d’Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [instructions de l’interface utilisateur de Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [instructions de conception de l’expérience utilisateur pour Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>Réutilisation de la bibliothèque et du code

La plateforme Xamarin permet la réutilisation de code C# existant sur toutes les plateformes, ainsi que l’intégration de bibliothèques écrites en mode natif pour chaque plateforme.

### <a name="c-source-and-libraries"></a>C#Sources et bibliothèques

Étant donné que les C# produits Xamarin utilisent et le .NET Framework, un grand nombre de codes source existants (à la fois les projets open source et internes) peuvent être réutilisés dans des projets Xamarin. iOS ou Xamarin. Android. Souvent, la source peut simplement être ajoutée à une solution Xamarin et elle fonctionne immédiatement. Si une fonctionnalité .NET Framework non prise en charge a été utilisée, des ajustements peuvent être nécessaires.

Les exemples C# de sources pouvant être utilisées dans Xamarin. iOS ou Xamarin. Android incluent : SQLite-net, NEWTONSOFT. JSON et sharpziplib.

### <a name="objective-c-bindings--binding-projects"></a>Liaisons objective-C + projets de liaison

Xamarin fournit un outil appelé *btouch* qui permet de créer des liaisons qui permettent d’utiliser des bibliothèques objective-C dans des projets Xamarin. iOS. Pour plus d’informations sur la façon de procéder, consultez la documentation sur les [types objective-C de liaison](~/cross-platform/macios/binding/binding-types-reference.md) .

Voici des exemples de bibliothèques objective-C qui peuvent être utilisées dans Xamarin. iOS : analyse de code-barres RedLaser, intégration de Google Analytics et PayPal. Les liaisons Xamarin. iOS Open source sont disponibles sur [GitHub](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>Liaisons. jar + projets de liaison

Xamarin prend en charge l’utilisation des bibliothèques Java existantes dans Xamarin. Android. Pour plus d’informations sur l’utilisation d’un, consultez la [documentation relative à la liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md) . Fichier JAR à partir de Xamarin. Android.

Les liaisons Open source Xamarin. Android sont disponibles sur [GitHub](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C par le biais de PInvoke

La technologie d’appel de code non managé (P/Invoke) permetC#au code managé () d’appeler des méthodes dans les bibliothèques natives et de prendre en charge les bibliothèques natives pour rappeler le code managé.

Par exemple, la bibliothèque [SQLite-net](https://github.com/praeclarum/sqlite-net) utilise des instructions de ce type :

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Cela est lié à l’implémentation SQLite du langage C natif dans iOS et Android.
Les développeurs habitués à une API C existante peuvent construire un ensemble C# de classes à mapper à l’API native et utiliser le code de plate-forme existant. Il existe une documentation sur la [liaison de bibliothèques natives](~/ios/platform/native-interop.md) dans Xamarin. iOS. des principes similaires s’appliquent à Xamarin. Android.

### <a name="c-via-cppsharp"></a>C++Via CppSharp

Miguel explique CXXI (désormais appelé [CppSharp](https://github.com/mono/CppSharp)) sur son [blog](https://tirania.org/blog/archive/2011/Dec-19.html). Une alternative à la liaison directe C++ à une bibliothèque consiste à créer un wrapper C et à le lier par le biais de P/Invoke.
