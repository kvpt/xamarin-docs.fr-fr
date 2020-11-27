---
title: Fonctionnement des niveaux d’API Android
description: Xamarin. Android possède plusieurs paramètres de niveau d’API Android qui déterminent la compatibilité de votre application avec plusieurs versions d’Android. Ce guide explique ce que signifient ces paramètres, comment les configurer et l’effet qu’ils ont sur votre application au moment de l’exécution.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: bb41a6e4ea477ed0a7932df441678e53a304aa35
ms.sourcegitcommit: 8fa0cb9ccbc107d697aa5b9113a4e5d1e75d6eb9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303096"
---
# <a name="understanding-android-api-levels"></a>Présentation des niveaux d’API Android

_Xamarin. Android possède plusieurs paramètres de niveau d’API Android qui déterminent la compatibilité de votre application avec plusieurs versions d’Android. Ce guide explique ce que signifient ces paramètres, comment les configurer et l’effet qu’ils ont sur votre application au moment de l’exécution._

## <a name="quick-start"></a>Démarrage rapide

Xamarin. Android expose trois paramètres de projet de niveau d’API Android :

- [Framework cible](#framework) &ndash; Spécifie l’infrastructure à utiliser dans la génération de votre application. Ce niveau d’API est utilisé au moment de la *compilation* par Xamarin. Android.

- Version d’Android [minimale](#minimum) &ndash; Spécifie la version Android la plus ancienne que votre application doit prendre en charge. Ce niveau d’API est utilisé au moment de l' *exécution* par Android.

- Version d’Android [cible](#target) &ndash; Spécifie la version d’Android sur laquelle votre application est destinée à s’exécuter. Ce niveau d’API est utilisé au moment de l' *exécution* par Android.

Avant de pouvoir configurer un niveau d’API pour votre projet, vous devez installer les composants de plateforme du kit de développement logiciel (SDK) pour ce niveau d’API. Pour plus d’informations sur le téléchargement et l’installation de Android SDK composants, consultez [Android SDK le programme d’installation](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> Depuis le 2020 août, la console Google Play nécessite que les nouvelles applications ciblent le niveau d’API 29 (Android 10,0) ou une version ultérieure.
Les applications existantes sont requises pour cibler le niveau d’API 29 ou supérieur à partir du 2020 novembre. Pour plus d’informations, consultez [exigences de niveau d’API cible pour la console de lecture](https://support.google.com/googleplay/android-developer/answer/9859152#targetsdk) dans « créer et configurer votre application » dans la documentation de la console Play.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Normalement, les trois niveaux d’API Xamarin. Android sont définis sur la même valeur. Sur la page **application** , définissez **compiler à l’aide d’Android version (Target Framework)** sur la dernière version de l’API stable (ou au minimum, sur la version Android qui possède toutes les fonctionnalités dont vous avez besoin).
Dans la capture d’écran suivante, la version cible de .NET Framework est définie sur **Android 7,1 (niveau d’API 25-nougat)**:

[![Valeurs par défaut de la version du Framework cible à compiler avec la version Android](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

Sur la page **manifeste Android** , définissez la version minimale d’Android pour **utiliser compiler à l’aide de la version du kit de développement logiciel (SDK)** et définissez la version Android cible sur la même valeur que la version cible du .NET Framework (dans la capture d’écran suivante, l’infrastructure Android cible est définie sur **Android 7,1 (nougat)**) :

[![Versions minimale et cible d’Android définies sur la version cible du .NET Framework](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Si vous souhaitez assurer la compatibilité descendante avec une version antérieure d’Android, définissez la **version minimale d’Android sur** la version la plus ancienne d’Android que votre application doit prendre en charge. (Notez que le niveau d’API 14 est le niveau d’API minimal requis pour [Google Play services et la prise en charge de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) L’exemple de configuration suivant prend en charge les versions d’Android de l’API de niveau 14 jusqu’au niveau d’API 25 :

[![Compiler à l’aide du niveau d’API 25 nougat, version Android minimale définie au niveau d’API 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Normalement, les trois niveaux d’API Xamarin. Android sont définis sur la même valeur. Définissez la version **cible du .NET Framework** sur la dernière version de l’API stable (ou au minimum, sur la version Android qui dispose de toutes les fonctionnalités dont vous avez besoin). Pour définir la version **cible de .NET Framework**, accédez à **générer > général** dans les **Options du projet**. Dans la capture d’écran suivante, la version cible de .NET Framework est configurée pour **utiliser la dernière plateforme installée (8,0)**:

[![Utilisation par défaut du Framework cible pour utiliser la dernière plateforme installée](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

Les paramètres de version d’Android minimale et cible se trouvent sous **générer > application Android** dans les **Options du projet**. Définissez la version minimale d’Android sur **automatique-utiliser la version du Framework cible** et définissez la version Android cible sur la même valeur que la version cible de .NET Framework. Dans la capture d’écran suivante, l’infrastructure Android cible a la valeur **android 8,0 (API de niveau 26)** pour correspondre au paramètre Framework cible ci-dessus :

[![Définition des niveaux de la cible et de l’infrastructure dans les options du projet](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Si vous souhaitez assurer la compatibilité descendante avec une version antérieure d’Android, remplacez la **version Android minimale** par la version la plus ancienne d’Android que votre application doit prendre en charge. Notez que le niveau d’API 14 est le niveau d’API minimal requis pour [Google Play services et la prise en charge de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Par exemple, la configuration suivante prend en charge les versions d’Android aussi tôt que le niveau d’API 14 :

[![Versions minimale et cible définies sur automatique-utiliser la version du Framework cible](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----

Si votre application prend en charge plusieurs versions d’Android, votre code doit inclure des vérifications à l’exécution pour s’assurer que votre application fonctionne avec le paramètre de version Android minimal (consultez vérifications à l' [exécution pour les versions Android](#runtimechecks) ci-dessous pour plus de détails). Si vous utilisez ou créez une bibliothèque, consultez les [bibliothèques et les niveaux d’API](#libraries) ci-dessous pour connaître les meilleures pratiques en matière de configuration des paramètres de niveau d’API pour les bibliothèques.

## <a name="android-versions-and-api-levels"></a>Versions d’Android et niveaux d’API

À mesure que la plateforme Android évolue et que de nouvelles versions d’Android sont publiées, chaque version d’Android reçoit un identificateur entier unique, appelé *niveau d’API*. Par conséquent, chaque version d’Android correspond à un seul niveau d’API Android. Étant donné que les utilisateurs installent des applications sur des versions plus anciennes, ainsi que les versions les plus récentes d’Android, les applications Android réelles doivent être conçues pour fonctionner avec plusieurs niveaux d’API Android.

### <a name="android-versions"></a>Versions d’Android

Chaque version d’Android passe par plusieurs noms :

- La version d’Android, par exemple **android 9,0**
- Nom de code (ou dessert), tel que _Pie_
- Un niveau d’API correspondant, tel que le **niveau d’API 28**

Un nom de code Android peut correspondre à plusieurs versions et à des niveaux d’API (comme indiqué dans le tableau ci-dessous), mais chaque version d’Android correspond à un seul niveau d’API.

En outre, Xamarin. Android définit les *codes de version de build* qui correspondent aux niveaux d’API Android actuellement connus. Le tableau suivant peut vous aider à traduire entre le niveau d’API, la version Android, le nom de code et le code de version de build Xamarin. Android (les codes de version de build sont définis dans l' `Android.OS` espace de noms) :

[!include[](~/android/includes/api-levels.md)]

Comme le montre ce tableau, les nouvelles versions d’Android sont publiées fréquemment &ndash; plus d’une version par an. Par conséquent, l’univers des appareils Android qui peuvent exécuter votre application comprend une large gamme de versions Android plus anciennes et plus récentes. Comment pouvez-vous garantir que votre application s’exécutera de façon cohérente et fiable sur de nombreuses versions différentes d’Android ? Les niveaux d’API d’Android peuvent vous aider à gérer ce problème.

### <a name="android-api-levels"></a>Niveaux d’API Android

Chaque appareil Android s’exécute à *un* niveau d’API exactement &ndash; . ce niveau d’API est garanti comme étant unique par version de la plateforme Android. Le niveau d’API identifie avec précision la version de l’ensemble d’API que votre application peut appeler ; Il identifie la combinaison d’éléments de manifeste, d’autorisations, etc. que vous codez en tant que développeur. Le système Android de niveaux d’API permet à Android de déterminer si une application est compatible avec une image système Android avant l’installation de l’application sur un appareil.

Quand une application est générée, elle contient les informations de niveau d’API suivantes :

- Niveau d’API *cible* d’Android sur lequel l’application est conçue pour s’exécuter.

- Niveau d’API Android *minimal* qu’un appareil Android doit avoir pour exécuter votre application. 

Ces paramètres sont utilisés pour s’assurer que les fonctionnalités nécessaires à l’exécution correcte de l’application sont disponibles sur l’appareil Android au moment de l’installation. Si ce n’est pas le cas, l’application ne peut pas s’exécuter sur cet appareil. Par exemple, si le niveau d’API d’un appareil Android est inférieur au niveau d’API minimal que vous spécifiez pour votre application, l’appareil Android empêchera l’utilisateur d’installer votre application.

## <a name="project-api-level-settings"></a>Paramètres au niveau de l’API du projet

Les sections suivantes expliquent comment utiliser le gestionnaire du kit de développement logiciel (SDK) pour préparer votre environnement de développement pour les niveaux d’API que vous souhaitez cibler, suivis d’explications détaillées sur la configuration de la version *cible de .NET Framework*, de la *version Android minimale* et des paramètres de la *version cible d’Android* dans Xamarin. Android.

### <a name="android-sdk-platforms"></a>Plates-formes Android SDK

Avant de pouvoir sélectionner un niveau d’API cible ou minimal dans Xamarin. Android, vous devez installer la version de la plateforme Android SDK qui correspond à ce niveau d’API. La plage des choix disponibles pour la version cible de .NET Framework, la version minimale d’Android et la version cible d’Android est limitée à la plage des versions de Android SDK que vous avez installées. Vous pouvez utiliser le gestionnaire du kit de développement logiciel (SDK) pour vérifier que les versions de Android SDK requises sont installées et vous pouvez l’utiliser pour ajouter les nouveaux niveaux d’API dont vous avez besoin pour votre application. Si vous n’êtes pas familiarisé avec l’installation des niveaux d’API, consultez [Android SDK configuration](~/android/get-started/installation/android-sdk.md).

<a name="framework"></a>

### <a name="target-framework"></a>Framework cible

Le *Framework cible* (également appelé `compileSdkVersion` ) est la version de Framework Android spécifique (niveau d’API) pour laquelle votre application est compilée au moment de la génération. Ce paramètre spécifie les API que votre application *s’attend* à utiliser lors de son exécution, mais elle n’a aucun effet sur les API qui sont réellement disponibles pour votre application lors de son installation. Par conséquent, la modification du paramètre Framework cible ne change pas le comportement d’exécution.

La version cible du .NET Framework identifie les versions de bibliothèque auxquelles votre application est liée &ndash; . ce paramètre détermine les API que vous pouvez utiliser dans votre application. Par exemple, si vous souhaitez utiliser la méthode [NotificationBuilder. SetCategory](xref:Android.App.Notification.Builder.SetCategory*) introduite dans Android 5,0 Lollipop, vous devez définir la version cible de .NET Framework sur l' **API de niveau 21 (lollipop)** ou version ultérieure. Si vous définissez la version cible de .NET Framework de votre projet sur un niveau d’API tel que le **niveau d’API 19 (KitKat)** et que vous essayez d’appeler la `SetCategory` méthode dans votre code, vous obtiendrez une erreur de compilation.

Nous vous recommandons de toujours compiler avec la *dernière* version de Framework cible disponible. Cela vous fournit des messages d’avertissement utiles pour toutes les API déconseillées qui peuvent être appelées par votre code. L’utilisation de la version la plus récente de Framework cible est particulièrement importante lorsque vous utilisez les dernières versions de la bibliothèque de prise en charge &ndash; . chaque bibliothèque s’attend à ce que votre application soit compilée au niveau d’API minimal de la bibliothèque de prise en charge ou supérieure.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour accéder au paramètre Framework cible dans Visual Studio, ouvrez les propriétés du projet dans **Explorateur de solutions** puis sélectionnez la page **application** :

[![Page application des propriétés du projet](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Définissez la version cible du .NET Framework en sélectionnant un niveau d’API dans le menu déroulant sous **compiler à l’aide de la version Android** , comme indiqué ci-dessus.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour accéder au paramètre Framework cible dans Visual Studio pour Mac, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **options**. la boîte de dialogue **Options du projet** s’ouvre. Dans cette boîte de dialogue, accédez à **Build > général** comme indiqué ici :

[![Section général de la génération de la page d’options du projet](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Définissez la version cible du .NET Framework en sélectionnant un niveau d’API dans le menu déroulant à droite de **Target Framework** , comme indiqué ci-dessus.

-----

<a name="minimum"></a>

### <a name="minimum-android-version"></a>Version d’Android minimale

La *version minimale d’Android* (également appelée `minSdkVersion` ) est la version la plus ancienne du système d’exploitation Android (c’est-à-dire, le niveau d’API le plus bas) qui peut installer et exécuter votre application. Par défaut, une application ne peut être installée que sur les appareils qui correspondent au paramètre du Framework cible ou à une version ultérieure. Si le paramètre de version Android minimale est *inférieur* au paramètre Framework cible, votre application peut également s’exécuter sur des versions antérieures d’Android. Par exemple, si vous définissez la version cible de .NET Framework sur **android 7,1 (nougat)** et que vous définissez la version Android minimale sur **Android 4.0.3 (Ice Ice sandwich)**, votre application peut être installée sur n’importe quelle plateforme du niveau d’API 15 au niveau d’API 25, inclus.

Bien que votre application puisse être correctement générée et installée sur cette plage de plateformes, cela ne garantit pas qu’elle sera *exécutée* avec succès sur toutes ces plateformes. Par exemple, si votre application est installée sur **android 5,0 (lollipop)** et que votre code appelle une API disponible uniquement dans **Android 7,1 (nougat)** et les versions ultérieures, votre application obtiendra une erreur d’exécution et peut-être se bloquer. Par conséquent, votre code doit vérifier &ndash; au moment de &ndash; l’exécution qu’il appelle uniquement les API qui sont prises en charge par l’appareil Android sur lequel il s’exécute. En d’autres termes, votre code doit inclure des vérifications à l’exécution explicites pour s’assurer que votre application utilise des API plus récentes uniquement sur les appareils qui sont suffisamment récents pour les prendre en charge.
[Vérifications au moment de l’exécution pour les versions Android](#runtimechecks), plus loin dans ce guide, explique comment ajouter ces vérifications du runtime à votre code.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour accéder au paramètre de version Android minimal dans Visual Studio, ouvrez les propriétés du projet dans **Explorateur de solutions** puis sélectionnez la page du **manifeste Android** . Dans le menu déroulant sous **version minimale d’Android** , vous pouvez sélectionner la version Android minimale pour votre application :

[![Option d’Android à Target minimale définie à compiler à l’aide de la version du SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Si vous sélectionnez **utiliser compiler à l’aide de la version du kit de développement logiciel (SDK**), la version minimale d’Android sera identique à celle du paramètre Framework cible.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour accéder à la version minimale d’Android dans Visual Studio pour Mac, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **options**. la boîte de dialogue **Options du projet** s’ouvre. Accédez à **Build > application Android**.
Dans le menu déroulant à droite de la **version minimale d’Android**, vous pouvez définir la version Android minimale de votre application :

[![Version d’Android minimale définie sur automatique-utiliser la version du Framework cible](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Si vous sélectionnez **utiliser automatiquement la &ndash; version du Framework cible**, la version minimale d’Android sera identique à celle du paramètre Framework cible.

-----

<a name="target"></a>

### <a name="target-android-version"></a>Version d’Android cible

La *version d’Android cible* (également appelée `targetSdkVersion` ) est le niveau d’API de l’appareil Android sur lequel l’application s’attend à s’exécuter. Android utilise ce paramètre pour déterminer s’il faut activer les comportements de compatibilité &ndash; . cela garantit que votre application continue de fonctionner comme prévu. Android utilise le paramètre de version cible Android de votre application pour déterminer les modifications de comportement qui peuvent être appliquées à votre application sans la rompre (c’est ainsi que Android fournit une compatibilité ascendante).

La version cible de .NET Framework et la version d’Android cible, tout en ayant des noms très similaires, n’ont pas la même signification. Le paramètre Framework cible communique les informations au niveau de l’API cible à Xamarin. Android pour une utilisation au moment de la *compilation*, tandis que la version Android cible communique les informations au niveau de l’API cible à Android pour une utilisation au moment de l' *exécution* (lorsque l’application est installée et en cours d’exécution sur un appareil).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour accéder à ce paramètre dans Visual Studio, ouvrez les propriétés du projet dans **Explorateur de solutions** puis sélectionnez la page du **manifeste Android** . Dans le menu déroulant de la **version cible d’Android** , vous pouvez sélectionner la version d’Android cible pour votre application :

[![Version d’Android cible définie à compiler à l’aide de la version du SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

Nous vous recommandons de définir explicitement la version Android cible sur la dernière version d’Android que vous utilisez pour tester votre application. Dans l’idéal, il doit être défini avec la dernière version de Android SDK, &ndash; ce qui vous permet d’utiliser les nouvelles API avant de procéder aux changements de comportement. Pour la plupart des développeurs *, nous vous* déconseillons de définir la version d’Android cible pour **utiliser compiler à l’aide** de la version du SDK.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour accéder à ce paramètre dans Visual Studio pour Mac, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **options**. la boîte de dialogue **Options du projet** s’ouvre. Accédez à **Build > application Android**. Dans le menu déroulant à droite de la **version cible d’Android**, vous pouvez définir la version d’Android cible pour votre application :

[![Version d’Android cible définie sur automatique-utiliser la version du Framework cible](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

Nous vous recommandons de définir explicitement la version Android cible sur la dernière version d’Android que vous utilisez pour tester votre application. Dans l’idéal, il doit être défini avec la dernière version disponible du Android SDK &ndash; cela vous permet d’utiliser les nouvelles API avant d’effectuer les changements de comportement. Pour la plupart des développeurs, nous vous déconseillons de définir la version Android cible sur **automatique-utiliser la version du Framework cible**.

-----

En général, la version cible d’Android doit être limitée par la version d’Android minimale et la version cible de .NET Framework. Plus précisément :

**Version d’Android minimale <= cible version d’Android <= Framework cible**

Pour plus d’informations sur les niveaux du kit de développement logiciel (SDK), consultez la documentation du kit de développement [logiciel (SDK)](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) Android.

<a name="runtimechecks"></a>

## <a name="runtime-checks-for-android-versions"></a>Vérifications à l’exécution pour les versions Android

Au fur et à mesure de la publication de chaque nouvelle version d’Android, l’API Framework est mise à jour pour fournir des fonctionnalités nouvelles ou de remplacement. À quelques exceptions près, les fonctionnalités d’API des versions antérieures d’Android sont reportées dans les versions plus récentes d’Android sans modification. Par conséquent, si votre application s’exécute sur un niveau d’API Android particulier, elle est généralement en mesure de s’exécuter sur un niveau d’API Android ultérieur sans modification. Mais que se passe-t-il si vous souhaitez également exécuter votre application sur des versions antérieures d’Android ?

Si vous sélectionnez une version Android minimale *inférieure à celle* du paramètre Framework cible, certaines API peuvent ne pas être disponibles pour votre application au moment de l’exécution. Toutefois, votre application peut toujours s’exécuter sur un appareil antérieur, mais avec des fonctionnalités réduites. Pour chaque API qui n’est pas disponible sur les plateformes Android correspondant à votre paramètre de version Android minimale, votre code doit vérifier explicitement la valeur de la `Android.OS.Build.VERSION.SdkInt` propriété pour déterminer le niveau d’API de la plateforme sur laquelle l’application s’exécute. Si le niveau d’API est *inférieur* à la version Android minimale qui prend en charge l’API que vous souhaitez appeler, votre code doit trouver un moyen de fonctionner correctement sans effectuer cet appel d’API.

Supposons, par exemple, que nous souhaitons utiliser la méthode [NotificationBuilder. SetCategory](xref:Android.App.Notification.Builder.SetCategory*) pour classer une notification lors de l’exécution sur **Android 5,0 Lollipop** (et versions ultérieures), mais nous voulons toujours que notre application s’exécute sur les versions antérieures d’Android, par exemple **Android 4,1 geléy Bean** (où `SetCategory` n’est pas disponible). En vous référant à la table de version Android au début de ce guide, nous voyons que le code de version de build pour **Android 5,0 Lollipop** est `Android.OS.BuildVersionCodes.Lollipop` . Pour prendre en charge les versions antérieures d’Android où `SetCategory` n’est pas disponible, notre code peut détecter le niveau d’API au moment de l’exécution et appeler de manière conditionnelle `SetCategory` uniquement lorsque le niveau d’API est supérieur ou égal au code de la version de build Lollipop :

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

Dans cet exemple, le Framework cible de notre application est défini sur **android 5,0 (niveau d’API 21)** et sa version Android minimale est définie sur **Android 4,1 (niveau d’API 16)**. Étant donné que `SetCategory` est disponible au niveau de l’API `Android.OS.BuildVersionCodes.Lollipop` et versions ultérieures, cet exemple de code appellera `SetCategory` uniquement lorsqu’il est disponible. &ndash; il ne tente *pas* d’appeler `SetCategory` lorsque le niveau d’API est 16, 17, 18, 19 ou 20. La fonctionnalité est réduite sur ces versions d’Android antérieures uniquement dans la mesure où les notifications ne sont pas triées correctement (car elles ne sont pas classées par type), mais les notifications sont toujours publiées pour alerter l’utilisateur. Notre application fonctionne toujours, mais ses fonctionnalités sont légèrement amoindries.

En général, la vérification de la version de build permet à votre code de décider au moment de l’exécution de passer d’une méthode à l’autre. Par exemple :

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

Il n’existe aucune règle rapide et simple expliquant comment réduire ou modifier les fonctionnalités de votre application lorsqu’elle s’exécute sur des versions Android plus anciennes qui ne disposent pas d’une ou de plusieurs API. Dans certains cas (par exemple, dans l' `SetCategory` exemple ci-dessus), il suffit d’omettre l’appel d’API lorsqu’il n’est pas disponible. Toutefois, dans d’autres cas, vous devrez peut-être implémenter des fonctionnalités alternatives pour lorsque `Android.OS.Build.VERSION.SdkInt` est détecté comme étant inférieur au niveau d’API dont votre application a besoin pour présenter son expérience optimale.

<a name="libraries"></a>

## <a name="api-levels-and-libraries"></a>Bibliothèques et niveaux d’API

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Lorsque vous créez un projet de bibliothèque Xamarin. Android (par exemple, une bibliothèque de classes ou une bibliothèque de liaisons), vous pouvez configurer uniquement la version cible du .NET Framework. les paramètres de version &ndash; Android minimale et d’Android cible ne sont pas disponibles. Cela est dû au fait qu’il n’existe pas de page de **manifeste Android** :

[![Seule l’option compiler à l’aide de la version Android est disponible](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Lorsque vous créez un projet de bibliothèque Xamarin. Android, aucune page d' **application Android** ne vous permet de configurer la version d’Android minimale et la version cible d’Android &ndash; . les paramètres de version Android minimale et de version Android cible ne sont pas disponibles.
Cela est dû au fait qu’il n’y a aucune page de **génération > d’application Android** :

[![Page général de génération sans options de version minimale et cible](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

La version minimale d’Android et la cible des paramètres de version Android ne sont pas disponibles, car la bibliothèque résultante n’est pas une application autonome &ndash; . la bibliothèque peut être exécutée sur n’importe quelle version d’Android, en fonction de l’application avec laquelle elle est empaquetée. Vous pouvez spécifier la façon dont la bibliothèque doit être *compilée*, mais vous ne pouvez pas prédire le niveau d’API de plateforme sur lequel la bibliothèque sera exécutée. En tenant compte de cela, les meilleures pratiques suivantes doivent être observées lors de l’utilisation ou de la création de bibliothèques :

- **Lors de l’utilisation d’une bibliothèque Android** &ndash; Si vous consommez une bibliothèque Android dans votre application, veillez à définir le paramètre Framework cible de votre application sur un niveau d’API *au moins aussi élevé que* le paramètre du Framework cible de la bibliothèque.

- **Lors de la création d’une bibliothèque Android** &ndash; Si vous créez une bibliothèque Android pour une utilisation par d’autres applications, veillez à définir le paramètre Framework cible sur le niveau d’API minimal requis pour la compilation.

Ces meilleures pratiques sont recommandées afin d’éviter la situation dans laquelle une bibliothèque tente d’appeler une API qui n’est pas disponible au moment de l’exécution (ce qui peut provoquer le blocage de l’application). Si vous êtes développeur de bibliothèque, vous devez vous efforcer de limiter l’utilisation des appels d’API à un sous-ensemble bien établi de la surface d’exposition totale de l’API. Cela vous permet de vous assurer que votre bibliothèque peut être utilisée en toute sécurité dans une plus large gamme de versions Android.

## <a name="summary"></a>Résumé

Ce guide a expliqué comment les niveaux d’API Android sont utilisés pour gérer la compatibilité des applications entre les différentes versions d’Android. Il a fourni des étapes détaillées pour la configuration de l' *infrastructure cible* Xamarin. Android, de la *version d’Android minimale* et des paramètres de projet de *version Android cible* . Il vous a fourni des instructions sur l’utilisation du gestionnaire de Android SDK pour installer des packages de SDK. il contient des exemples d’écriture de code pour gérer différents niveaux d’API au moment de l’exécution et explique comment gérer les niveaux d’API lors de la création ou de l’utilisation de bibliothèques Android. Elle a également fourni une liste complète qui lie les niveaux d’API aux numéros de version Android (tels que Android 4,4), les noms de version Android (tels que KitKat) et les codes de version de build Xamarin. Android.

## <a name="related-links"></a>Liens associés

- [Configuration du kit Android SDK](~/android/get-started/installation/android-sdk.md)
- [Modifications des outils de l’interface CLI SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Sélection de votre versions compilesdkversion, minSdkVersion et targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [Qu’est-ce que le niveau d’API ?](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Nom de codes, balises et numéros de build](https://source.android.com/source/build-numbers)
