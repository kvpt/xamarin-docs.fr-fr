---
title: 'Partie 4 : Utilisation de plusieurs plateformes'
description: Ce document explique comment gérer la divergence des applications en fonction de la plateforme ou de la fonctionnalité. Il aborde la taille de l’écran, les métaphores de navigation, les fonctions tactiles et les gestes, les notifications push et les paradigmes d’interface tels que les listes et les onglets.
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: c8b4dcbfbf65bc4059125404b0d20ed35fa31f29
ms.sourcegitcommit: ce4670de51e24116a944c778ee64585bd0aae0e1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088927"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Partie 4 : Utilisation de plusieurs plateformes

## <a name="handling-platform-divergence-amp-features"></a>Gestion des fonctionnalités de divergence de plateforme &amp;

La divergence n’est pas simplement un problème « multiplateforme ». les appareils sur la plateforme « identique » ont des fonctionnalités différentes (notamment la grande variété de périphériques Android disponibles). La taille d’écran la plus évidente et la plus simple, mais les autres attributs d’appareil peuvent varier et obliger une application à vérifier certaines fonctionnalités et à se comporter différemment en fonction de leur présence (ou absence).

Cela signifie que toutes les applications doivent faire face à une dégradation normale des fonctionnalités, ou encore présenter un ensemble de fonctionnalités non attrayant, le plus petit dénominateur commun. L’intégration profonde de Xamarin avec les kits de développement logiciel (SDK) natifs de chaque plateforme permet aux applications de tirer parti des fonctionnalités spécifiques à la plateforme. il est donc judicieux de concevoir des applications qui utilisent ces fonctionnalités.

Pour obtenir une vue d’ensemble de la façon dont les plateformes diffèrent, consultez la documentation sur les fonctionnalités de la plateforme.

## <a name="examples-of-platform-divergence"></a>Exemples de divergence de plateforme

### <a name="fundamental-elements-that-exist-across-platforms"></a>Éléments fondamentaux qui existent entre les plateformes

Il existe certaines caractéristiques des applications mobiles universelles.
Il s’agit de concepts de niveau supérieur qui sont généralement vrais pour tous les appareils et qui peuvent donc constituer la base de la conception de votre application :

- Sélection des fonctionnalités par le biais d’onglets ou de menus
- Listes de données et défilement
- Vues de données uniques
- Modification des vues uniques des données
- Naviguer vers l’arrière

Lors de la conception de votre circuit d’écran de haut niveau, vous pouvez baser une expérience utilisateur commune sur ces concepts.

### <a name="platform-specific-attributes"></a>Attributs spécifiques à la plateforme

En plus des éléments de base qui existent sur toutes les plateformes, vous devez répondre aux principales différences de plateforme dans votre conception. Vous devrez peut-être prendre en compte (et écrire du code spécifiquement pour gérer) ces différences :

- **Tailles d’écran** : certaines plateformes (comme iOS et versions antérieures Windows Phone) ont des tailles d’écran standardisées relativement simples à cibler. Les appareils Android possèdent un grand nombre de dimensions d’écran, qui nécessitent davantage d’efforts pour prendre en charge dans votre application.
- **Métaphores de navigation** : différentes plateformes (par exemple, bouton « Back » du matériel, contrôle de l’interface utilisateur Panorama) et dans les plateformes (Android 2 et 4, iPhone vs iPad).
- **Claviers** : certains appareils Android ont des claviers physiques, tandis que d’autres ne disposent que d’un clavier logiciel. Le code qui détecte quand un clavier conditionnel masque une partie de l’écran doit être sensible à ces différences.
- **Toucher et gestes** : la prise en charge du système d’exploitation pour la reconnaissance des mouvements varie, en particulier dans les versions antérieures de chaque système d’exploitation. Les versions antérieures d’Android offrent une prise en charge très limitée des opérations tactiles, ce qui signifie que la prise en charge des appareils plus anciens peut nécessiter du code séparé
- **Notifications push** : il existe différentes fonctionnalités/implémentations sur chaque plateforme (par exemple, Vignettes dynamiques sur Windows).

### <a name="device-specific-features"></a>Fonctionnalités spécifiques à l’appareil

Déterminez ce que les fonctionnalités minimales requises pour l’application doivent être ; ou lorsque vous décidez des fonctionnalités supplémentaires à tirer parti de sur chaque plateforme. Du code est requis pour détecter les fonctionnalités et désactiver les fonctionnalités ou proposer des alternatives (par exemple, une alternative à la géolocalisation pourrait consister à permettre à l’utilisateur de taper un emplacement ou d’en choisir un dans une carte.

- **Caméra** : les fonctionnalités diffèrent sur les appareils : certains appareils n’ont pas d’appareil photo, d’autres ont des caméras à l’avant et à l’arrière. Certains appareils photo sont en charge de l’enregistrement vidéo.
- **Géo-location & Maps** : la prise en charge du GPS ou de l’emplacement Wi-Fi n’est pas présente sur tous les appareils. Les applications doivent également répondre aux différents niveaux de précision pris en charge par chaque méthode.
- **Accéléromètre, gyroscope et Compass** : ces fonctionnalités se trouvent souvent dans une sélection de périphériques sur chaque plateforme, de sorte que les applications doivent presque toujours fournir une solution de secours lorsque le matériel n’est pas pris en charge.
- **Twitter et Facebook** , uniquement « intégrés » sur IOS5 et iOS6, respectivement. Sur les versions antérieures et les autres plateformes, vous devrez fournir vos propres fonctions d’authentification et l’interface directement avec chaque API de services.
- **NFC (Near Field communications)** : uniquement sur (certains) téléphones Android (au moment de l’écriture).

## <a name="dealing-with-platform-divergence"></a>Gestion de la divergence de plateforme

Il existe deux approches différentes pour la prise en charge de plusieurs plateformes à partir du même code-base, chacune ayant son propre ensemble d’avantages et de inconvénients.

- L' **abstraction de plateforme** – modèle de façade métier fournit un accès unifié sur les plateformes et soustrait les implémentations de plateformes particulières dans une API unifiée unique.
- **Implémentation divergente** : appel de fonctionnalités de plateforme spécifiques via des implémentations divergentes à l’aide d’outils d’architecture tels que les interfaces et l’héritage ou la compilation conditionnelle.

## <a name="platform-abstraction"></a>Abstraction de plateforme

### <a name="class-abstraction"></a>Abstraction de classe

En utilisant les interfaces ou les classes de base définies dans le code partagé et implémentées ou étendues dans des projets spécifiques à la plateforme. L’écriture et l’extension de code partagé avec des abstractions de classe est particulièrement adaptée aux bibliothèques de classes portables, car elles ont un sous-ensemble limité de l’infrastructure disponible et ne peuvent pas contenir de directives de compilateur pour prendre en charge des branches de code spécifiques à la plateforme.

#### <a name="interfaces"></a>Interfaces

L’utilisation d’interfaces vous permet d’implémenter des classes spécifiques à la plateforme qui peuvent toujours être passées dans vos bibliothèques partagées pour tirer parti du code commun.

L’interface est définie dans le code partagé et transmise dans la bibliothèque partagée en tant que paramètre ou propriété.

Les applications spécifiques à la plateforme peuvent ensuite implémenter l’interface tout en tirant parti du code partagé pour le « traiter ».

 **Avantages**

L’implémentation peut contenir du code spécifique à la plateforme et même référencer des bibliothèques externes spécifiques à la plateforme.

 **Inconvénients**

Avoir à créer et à passer des implémentations dans le code partagé. Si l’interface est utilisée de manière profonde dans le code partagé, elle finit par passer par plusieurs paramètres de méthode ou par la suite dans la chaîne d’appel. Si le code partagé utilise un grand nombre d’interfaces différentes, celles-ci doivent toutes être créées et définies dans le code partagé quelque part.

#### <a name="inheritance"></a>Héritage

Le code partagé peut implémenter des classes abstraites ou virtuelles qui peuvent être étendues dans un ou plusieurs projets spécifiques à la plateforme. Cela est similaire à l’utilisation d’interfaces, mais avec un certain comportement déjà implémenté. Il existe différents points de vue pour déterminer si les interfaces ou l’héritage sont un meilleur choix de C# conception : en particulier, étant donné que autorise uniquement l’héritage unique, il peut dicter la façon dont vos API peuvent être conçues à l’avenir. Utilisez l’héritage avec précaution.

Les avantages et les inconvénients des interfaces s’appliquent également à l’héritage, avec l’avantage supplémentaire que la classe de base peut contenir du code d’implémentation (peut-être une implémentation indépendante de la plateforme entière qui peut éventuellement être étendue).

## <a name="xamarinforms"></a>Xamarin.Forms

Consultez la documentation de [Xamarin. Forms](~/get-started/index.yml) .

### <a name="other-cross-platform-libraries"></a>Autres bibliothèques multiplateformes

Ces bibliothèques proposent également des fonctionnalités multiplateforme pour C# les développeurs :

- [**Xamarin. Essentials**](~/essentials/index.md) : API multiplateforme pour les fonctionnalités courantes.
- [**SkiaSharp**](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) : graphiques 2D multiplateforme.

## <a name="conditional-compilation"></a>Compilation conditionnelle

Dans certains cas, votre code partagé doit toujours fonctionner différemment sur chaque plateforme, en accédant éventuellement à des classes ou des fonctionnalités qui se comportent différemment. La compilation conditionnelle fonctionne mieux avec les projets d’actifs partagés, où le même fichier source est référencé dans plusieurs projets qui ont des symboles différents définis.

Les projets Xamarin définissent toujours `__MOBILE__` ce qui est vrai pour les projets d’application iOS et Android (Notez le double-trait de soulignement avant et après correction de ces symboles).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

#### <a name="ios"></a>iOS

Xamarin. iOS définit `__IOS__` que vous pouvez utiliser pour détecter les appareils iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

Il y a également des symboles spécifiques aux espions et aux téléviseurs :

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

Le code qui doit être compilé uniquement dans les applications Xamarin. Android peut utiliser les éléments suivants :

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Chaque version d’API définit également une nouvelle directive de compilateur, de sorte que le code comme celui-ci vous permet d’ajouter des fonctionnalités si des API plus récentes sont ciblées. Chaque niveau d’API comprend tous les symboles de niveau « inférieur ». Cette fonctionnalité n’est pas vraiment utile pour la prise en charge de plusieurs plateformes. en général, le symbole de `__ANDROID__` est suffisant.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

Xamarin. Mac définit `__MACOS__` que vous pouvez utiliser pour compiler uniquement pour macOS :

```csharp
#if __MACOS__
// macOS-specific code
#endif
```

#### <a name="universal-windows-platform-uwp"></a>Plateforme Windows universelle (UWP)

Utilisez `WINDOWS_UWP`. Il n’y a aucun trait de soulignement entourant la chaîne comme les symboles de la plateforme Xamarin.

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>Utilisation de la compilation conditionnelle

Un exemple simple d’étude de cas de compilation conditionnelle consiste à définir l’emplacement du fichier de base de données SQLite. Les trois plateformes ont des exigences légèrement différentes pour spécifier l’emplacement du fichier :

- **iOS** : Apple préfère placer les données non-utilisateur dans un emplacement spécifique (le répertoire de bibliothèque), mais il n’existe aucune constante système pour ce répertoire. Du code spécifique à la plateforme est requis pour générer le chemin d’accès correct.
- **Android** : le chemin d’accès système retourné par `Environment.SpecialFolder.Personal` est un emplacement acceptable pour stocker le fichier de base de données.
- **Windows Phone** : le mécanisme de stockage isolé ne permet pas de spécifier un chemin d’accès complet, juste un chemin d’accès relatif et un nom de fichier.
- **Plateforme Windows universelle** : utilise des API `Windows.Storage`.

Le code suivant utilise la compilation conditionnelle pour s’assurer que la `DatabaseFilePath` est correcte pour chaque plateforme :

```csharp
public static string DatabaseFilePath
{
    get
    {
        var filename = "TodoDatabase.db3";
#if SILVERLIGHT
        // Windows Phone 8
        var path = filename;
#else

#if __ANDROID__
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine(libraryPath, filename);
#endif
        return path;
    }
}
```

Le résultat est une classe qui peut être générée et utilisée sur toutes les plateformes, en plaçant le fichier de base de données SQLite dans un emplacement différent sur chaque plateforme.
