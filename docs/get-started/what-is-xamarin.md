---
titre : « qu’est-ce que Xamarin ? »
Description : « cet article présente la plateforme Xamarin et les bibliothèques associées ».
ms. Prod : xamarin ms. AssetID : 33C83E13-F3E5-17B4-6512-207F3D3C5AB6 ms. Custom : Video Author : profexorgeek ms. Author : jusjohns ms. Date : 05/28/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="what-is-xamarin"></a>Qu’est-ce que Xamarin ?

[![Captures d’écran de l’exemple d’application Xamarin dans iOS et Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin est une plateforme open source qui permet de générer des applications modernes et performantes pour iOS, Android et Windows avec .NET. Xamarin est une couche d’abstraction qui gère la communication entre le code partagé et le code de plateforme sous-jacent. Xamarin s’exécute dans un environnement managé qui offre des commodités telles que l’allocation de mémoire et le garbage collection.

Xamarin permet aux développeurs de partager en moyenne 90 % de leur application entre les plateformes. Ce modèle permet aux développeurs d’écrire toute leur logique métier dans un seul langage (ou de réutiliser du code d’application existant) tout en obtenant des performances, une apparence et une convivialité natives sur chaque plateforme.

Les applications Xamarin peuvent être écrites sur PC ou Mac et être compilées dans des paquets d’application natifs, par exemple un fichier **.apk** sur Android ou un fichier **.ipa** sur iOS.

> [!NOTE]
> La compilation et le déploiement d’applications pour iOS nécessitent une machine macOS. Pour plus d’informations sur les exigences relatives au développement, consultez [Configuration requise](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-xamarin-is-for"></a>À qui s’adresse Xamarin

Xamarin s’adresse aux développeurs qui ont les objectifs suivants :

- Partager le code, les tests et la logique métier entre les plateformes.
- Écrire des applications multiplateformes en C# avec Visual Studio

## <a name="how-xamarin-works"></a>Fonctionnement de Xamarin

![Diagramme de l’architecture de Xamarin](what-is-xamarin-images/xamarin-architecture.png)

Le diagramme montre l’architecture globale d’une application Xamarin multiplateforme. Xamarin vous permet de créer une IU native sur chaque plateforme et d’écrire une logique métier en C#, qui est partagée entre les plateformes. Dans la plupart des cas, 80 % du code d’application peut être partagé à l’aide de Xamarin.

Xamarin repose sur .NET, qui gère automatiquement les tâches telles que l’allocation de mémoire, les garbage collection et l’interopérabilité avec les plateformes sous-jacentes.

Pour plus d’informations sur l’architecture spécifique à la plateforme, consultez [Xamarin.Android](#xamarinandroid) et [Xamarin.iOS](#xamarinios).

### <a name="added-features"></a>Fonctionnalités ajoutées

Xamarin combine les fonctionnalités des plateformes natives et ajoute un certain nombre de fonctionnalités, notamment :

1. **Liaison complète pour les kits SDK sous-jacents** - Xamarin contient des liaisons pour la quasi-totalité des kits SDK de plateforme sous-jacents, à la fois pour iOS et Android. En outre, ces liaisons sont fortement typées, ce qui signifie qu’il est facile de les parcourir et de les utiliser. En outre, elles fournissent un contrôle de type robuste au moment de la compilation et pendant le développement. Les liaisons fortement typées entraînent moins d’erreurs d’exécution et produisent des applications de meilleure qualité.
1. **Objective-C, Java, C et C++ Interop** - Xamarin fournit des fonctionnalités qui permettent d’appeler directement les bibliothèques Objective-C, Java, C et C++. Ainsi, vous pouvez utiliser du code tiers dans un large éventail de scénarios. Cette fonctionnalité vous permet d’utiliser les bibliothèques iOS et Android existantes écrites en Objective-C, Java ou C/C++. De plus, Xamarin propose des projets de liaison qui vous permettent de lier des bibliothèques Objective-C et Java natives à l’aide d’une syntaxe déclarative.
1. **Constructions de langage moderne** - Les applications Xamarin sont écrites en C#. Ce langage moderne inclut des améliorations significatives par rapport à Objective-C et Java par exemple les fonctionnalités de langage dynamique, les constructions fonctionnelles telles que les lambdas, LINQ, la programmation parallèle, les génériques et bien d’autres choses encore.
1. **Bibliothèque de classes de base robuste** - Les applications Xamarin utilisent la BCL (bibliothèque de classes de base) .NET. Il s’agit d’une vaste collection de classes qui comprennent des fonctionnalités complètes, rationalisées et puissantes, telles que la prise en charge du langage XML, des bases de données, de la sérialisation, des E/S, des chaînes, des réseaux, et bien d’autres choses encore. Vous pouvez compiler du code C# existant pour l’utiliser dans une application. Ainsi, vous avez accès à des milliers de bibliothèques qui offrent des fonctionnalités supplémentaires dont ne dispose pas la BCL.
1. **IDE (environnement de développement intégré) moderne** - Xamarin utilise Visual Studio, un IDE moderne qui inclut des fonctionnalités telles que l’autocomplétion du code, un système de gestion de projets et de solutions sophistiqué, une bibliothèque complète de modèles de projet, un contrôle de code source intégré, et bien d’autres choses encore.
1. **Prise en charge multiplateforme mobile** - Xamarin offre une prise en charge multiplateforme sophistiquée pour les trois principales plateformes : iOS, Android et Windows. Les applications peuvent être écrites pour partager jusqu’à 90% de leur code et Xamarin.Essentials offrent une API unifiée pour accéder aux ressources communes sur les trois plateformes. En utilisant du code partagé, les développeurs mobiles peuvent réduire considérablement les coûts de développement et les délais de commercialisation.

### <a name="xamarinandroid"></a>Xamarin.Android

[![Diagramme d’architecture Xamarin. Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Les applications Xamarin.Android sont compilées du C# en **IL (langage intermédiaire)**, lequel fait ensuite l’objet d’une compilation **JIT (juste-à-temps)** en assembly natif au moment du lancement de l’application. Les applications Xamarin.Android s’exécutent dans l’environnement d’exécution Mono, côte à côte avec la machine virtuelle ART (Android Runtime). Xamarin fournit des liaisons .NET aux espaces de noms Android.* et Java.*. L’environnement d’exécution Mono fait appel à ces espaces de noms via les wrappers **MCW (Managed Callable Wrappers)**, et fournit les wrappers **ACW (Android Callable Wrappers)** à la machine virtuelle ART, ce qui permet aux deux environnements d’appeler du code.

Pour plus d’informations, consultez [Architecture de Xamarin.Android](~/android/internals/architecture.md).

### <a name="xamarinios"></a>Xamarin.iOS

[![Diagramme d’architecture Xamarin. iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Les applications Xamarin.iOS sont entièrement compilées **AOT (Ahead Of Time)** du C# en code d’assembly ARM natif. Xamarin utilise des **sélecteurs** pour exposer Objective-C au C# managé et des **registres** pour exposer le code C# managé à Objective-C. Les sélecteurs et les registres sont collectivement appelés des « liaisons », et permettent à Objective-C et C# de communiquer.

Pour plus d’informations, consultez [Architecture de Xamarin.iOS](~/ios/internals/architecture.md).

### Xamarin.Essentials

Xamarin.Essentialsest une bibliothèque qui fournit des API multiplateforme pour les fonctionnalités d’appareils natifs. Comme Xamarin lui-même, Xamarin.Essentials est une abstraction qui simplifie le processus d’accès aux fonctionnalités natives. Voici quelques exemples de fonctionnalités fournies par Xamarin.Essentials :

- Informations sur l’appareil
- Système de fichiers
- Accéléromètre
- Numéroteur téléphonique
- Synthèse vocale
- Verrouillage d’écran

Pour plus d’informations, consultez [Xamarin.Essentials](~/essentials/index.md).

### Xamarin.Forms

Xamarin.Formsest une infrastructure d’interface utilisateur Open source. Xamarin.Formspermet aux développeurs de générer des applications Xamarin. iOS, Xamarin. Android et Windows à partir d’un code base partagé unique. Xamarin.Formspermet aux développeurs de créer des interfaces utilisateur en XAML avec code-behind en C#. Ces interfaces utilisateur sont affichées en tant que contrôles natifs performants sur chaque plateforme. Voici quelques exemples de fonctionnalités fournies par Xamarin.Forms :

- Langage d’interface utilisateur XAML
- Liaison de données
- Mouvements
- Effets
- Styles

Pour plus d’informations, consultez [Xamarin.Forms](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Prendre en main

Les guides suivants vont vous aider à générer votre première application à l’aide de Xamarin :

- [Prise en main deXamarin.Forms](~/xamarin-forms/index.yml)
- [Bien démarrer avec Xamarin.Android](~/android/index.yml)
- [Bien démarrer avec Xamarin.iOS](~/ios/index.yml)
- [Bien démarrer avec Xamarin.Mac](~/mac/index.yml)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
