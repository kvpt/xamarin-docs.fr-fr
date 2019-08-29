---
title: Préparation d’une application pour la mise en production
ms.prod: xamarin
ms.assetid: 9C8145B3-FCF1-4649-8C6A-49672DDA4159
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/21/2018
ms.openlocfilehash: 3560c58c6fbb9920cfaf9c3830cd442bca443571
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119632"
---
# <a name="preparing-an-application-for-release"></a>Préparation d’une application pour la mise en production

Une fois qu’une application a été codée et testée, il est nécessaire de préparer un paquet pour la distribution. La première tâche de préparation de ce package consiste à générer l’application à mettre en production, ce qui implique principalement de définir différents attributs d’application.

Effectuez les étapes suivantes pour générer l’application à mettre en production :

- **[Spécifier l’icône de l’application](#Specify_the_Application_Icon)**  &ndash; Une icône d’application doit être spécifiée pour chaque application Xamarin.Android. Bien que non nécessaire techniquement, certaines marketplaces, comme Google Play, en exigent une.

- **[Version de l’application](#Versioning)**  &ndash; Cette étape initialise et met à jour les informations de gestion de versions. Ces informations sont importantes pour les futures mises à jour de l’application et permettent aux utilisateurs de savoir quelle version de l’application est installée.

- **[Réduire l’APK](#shrink_apk)**  &ndash; La taille de l’APK final peut être considérablement réduite en utilisant l’éditeur de liens Xamarin.Android sur le code managé et ProGuard sur le bytecode Java.

- **[Protéger l’application](#protect_app)** &ndash; Empêchez les utilisateurs ou les attaquants de déboguer, falsifier ou rétroconcevoir l’application en désactivant le débogage, en obfusquant le code managé, en ajoutant du code anti-violation et anti-débogage et en utilisant la compilation native.

- **[Définir les propriétés de création de package ](#Set_Packaging_Properties)** &ndash; Les propriétés de création de package contrôlent la création du paquet d’application Android (APK). Cette étape optimise l’APK, protège ses ressources et divise si nécessaire le paquet en modules.

- **[Compiler](#Compile)** &ndash; Cette étape compile le code et les ressources pour vérifier que l’application est générée en mode Mise en production.

- **[Archiver pour publication](#archive)** &ndash; Cette étape génère l’application et la place dans une archive en vue de sa signature et de sa publication.

Chacune de ces étapes est décrite ci-dessous plus en détail.

<a name="Specify_the_Application_Icon" />

## <a name="specify-the-application-icon"></a>Spécifier l’icône de l'application

Il est fortement recommandé de spécifier une icône d’application pour chaque application Xamarin.Android. Certaines places de marché d’applications n’autorisent pas la publication d’une application Android qui n’en aurait pas. La propriété `Icon` de l’attribut `Application` est utilisée pour spécifier l’icône d’application d’un projet Xamarin.Android.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dans Visual Studio 2017 et ultérieur, spécifiez l’icône de l’application via la section **Manifeste Android** des **Propriétés** du projet, comme illustré dans la capture d’écran suivante :

[![Définir l’icône de l’application](images/vs/01-application-icon-sml.png)](images/vs/01-application-icon.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, il est également possible de spécifier l’icône de l’application via la section **Manifeste Android** des **Options du projet**, comme illustré dans la capture d’écran suivante :

[![Définir l’icône de l’application](images/xs/01-application-icon-sml.png)](images/xs/01-application-icon.png#lightbox)

-----

Dans ces exemples, `@drawable/icon` fait référence à un fichier d’icône qui se trouve dans **Resources/drawable/icon.png** (notez que l’extension **.png** n’est pas incluse dans le nom de ressource). Cet attribut peut être également déclaré dans le fichier **Properties\AssemblyInfo.cs**, comme illustré dans cet exemple d’extrait de code :

```csharp
[assembly: Application(Icon = "@drawable/icon")]
```

Normalement, `using Android.App` est déclaré au niveau de la partie supérieure de **AssemblyInfo.cs** (l’espace de noms de l’attribut `Application` est `Android.App`) ; toutefois, vous devrez peut-être ajouter cette instruction `using` si elle n’est pas déjà présente.

<a name="Versioning" />

## <a name="version-the-application"></a>Définir la version de l'application

La gestion de versions est un élément important de la maintenance et de la distribution des applications Android. Sans gestion de versions, il est difficile de déterminer si une application doit être mise à jour ou comment elle doit l’être. Pour faciliter la gestion de versions, Android reconnaît deux types différents d’informations : 

- **Numéro de version** &ndash; Valeur entière (utilisée en interne par Android et l’application) qui représente la version de l’application. Cette valeur est initialement définie sur 1 dans la plupart des applications et elle est ensuite incrémentée avec chaque nouvelle build. Elle n’a aucun lien avec l’attribut de nom de version (voir plus bas). Les applications et les services de publication ne doivent pas montrer cette valeur aux utilisateurs. Cette valeur est stockée dans le fichier **AndroidManifest.xml** sous la forme `android:versionCode`. 

- **Nom de la version** &ndash; Chaîne qui est utilisée uniquement pour communiquer à l’utilisateur la version de l’application (tel qu’elle est installée sur un appareil spécifique). Le nom de la version est destiné à être affiché aux utilisateurs ou dans Google Play. Cette chaîne n’est pas utilisée en interne par Android. Le nom de la version peut être toute valeur de chaîne qui permet à un utilisateur d’identifier la build qui est installée sur son appareil. Cette valeur est stockée dans le fichier **AndroidManifest.xml** sous la forme `android:versionName`. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dans Visual Studio, ces valeurs peuvent être définies dans la section **Manifeste Android** des **Propriétés** du projet, comme illustré dans la capture d’écran suivante :

[![Définir le numéro de version](images/vs/02-versioning-sml.png)](images/vs/02-versioning.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Ces valeurs peuvent être définies dans la section **Générer > Application Android** des **Options du projet**, comme illustré dans la capture d’écran suivante :

[![Définir le numéro de version](images/xs/02-versioning-sml.png)](images/xs/02-versioning.png#lightbox)

-----

<a name="shrink_apk" />

## <a name="shrink-the-apk"></a>Réduire l’APK

La taille des APK Xamarin.Android peut être réduite par une combinaison de l’éditeur de liens Xamarin.Android, qui supprime le code *managé* inutile, et l’outil *ProGuard* du kit Android SDK, qui supprime le *bytecode Java* non utilisé. Le processus de génération commence par utiliser l’éditeur de liens Xamarin.Android pour optimiser l’application au niveau du code managé (C#), puis il utilise ProGuard (s’il est activé) pour optimiser l’APK au niveau du bytecode Java.


### <a name="configure-the-linker"></a>Configurer l'éditeur de liens

Le mode Mise en production désactive le runtime partagé et active la liaison afin que l’application ne fournisse que les parties de Xamarin.Android requises lors de l’exécution. L’*éditeur de liens* dans Xamarin.Android utilise l’analyse statique pour déterminer quels assemblys, types et membres de type sont utilisés ou référencés par une application Xamarin.Android. L’éditeur de liens ignore ensuite tous les assemblys, types et membres qui ne sont pas utilisés (ou référencés). Cela peut entraîner une réduction considérable de la taille du paquet. Prenons l’exemple d’[HelloWorld](~/android/deploy-test/linker.md) dont la taille finale de l’APK est réduite de 83 % : 

- Configuration : Aucune&ndash; Xamarin.Android 4.2.5 Taille = 17,4 Mo.

- Configuration : Assemblys de SDK uniquement &ndash; Xamarin.Android 4.2.5 Taille = 3,0 Mo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Définissez les options de l’éditeur de liens via la section **Options Android** des **Propriétés** du projet :

[![Options de l’éditeur de liens](images/vs/03-linking-sml.png)](images/vs/03-linking.png#lightbox)

Le menu déroulant **Édition des liens** propose les options suivantes pour contrôler l’éditeur de liens :

- **Aucun** &ndash; Cette option désactive l’éditeur de liens ; aucune liaison ne sera effectuée.

- **Assemblys de SDK uniquement** &ndash; Avec cette option, seuls les assemblys [requis par Xamarin.Android](~/cross-platform/internals/available-assemblies.md) seront liés. 
    Les autres assemblys ne seront pas liés.

- **Assemblys de SDK et assemblys d'utilisateur** &ndash; Avec cette option, tous les assemblys qui sont requis par l’application seront liés, pas seulement ceux qui sont requis par Xamarin.Android.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Définissez les options de l’éditeur de liens via l’onglet **Éditeur de liens** de la section **Build Android** des **Options du projet**, comme illustré dans la capture d’écran suivante :

[![Options de l’éditeur de liens](images/xs/03-linking-sml.png)](images/xs/03-linking.png#lightbox)

Les options de contrôle de l’éditeur de liens sont les suivantes :

- **Ne pas lier** &ndash; Cette option désactive l’éditeur de liens ; aucune liaison ne sera effectuée.

- **Lier les assemblys du SDK uniquement** &ndash; Avec cette option, seuls les assemblys [requis par Xamarin.Android](~/cross-platform/internals/available-assemblies.md) seront liés. Les autres assemblys ne seront pas liés.

- **Lier tous les assemblys** &ndash; Avec cette option, tous les assemblys qui sont requis par l’application seront liés, pas seulement ceux qui sont requis par Xamarin.Android.

-----

La liaison peut avoir des effets secondaires inattendus. Il est donc important de tester à nouveau une application en mode Mise en production sur un appareil physique.

### <a name="proguard"></a>ProGuard

*ProGuard* est un outil du kit Android SDK qui lie et obfusque le code Java. ProGuard est généralement utilisé pour créer des applications plus petites en réduisant l’encombrement des grandes bibliothèques (comme Services Google Play) qui se trouvent dans votre APK. ProGuard supprime le bytecode Java non utilisé, ce qui réduit la taille de l’application. Par exemple, l’utilisation de ProGuard sur de petites applications Xamarin.Android permet généralement de réduire la taille d’environ 24 % &ndash; sur des applications plus grandes avec de multiples dépendances de bibliothèques, la réduction de la taille est encore plus importante. 

ProGuard n’est pas une alternative à l’éditeur de liens Xamarin.Android. L’éditeur de liens Xamarin.Android lie le code *managé*, tandis que ProGuard lie le bytecode Java. Le processus de génération commence par utiliser l’éditeur de liens Xamarin.Android pour optimiser le code managé (C#) de l’application, puis il utilise ProGuard (s’il est activé) pour optimiser l’APK au niveau du bytecode Java. 

Si **Activer ProGuard** est activé, Xamarin.Android exécute l’outil ProGuard sur l’APK qui en résulte. Un fichier de configuration ProGuard est généré et utilisé par ProGuard au moment de la génération. Xamarin.Android prend également en charge les actions de génération *ProguardConfiguration* personnalisées. Vous pouvez ajouter un fichier de configuration ProGuard personnalisé à votre projet, cliquer dessus avec le bouton droit et le sélectionner comme action de génération comme illustré dans cet exemple : 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Action de génération Proguard](images/vs/05-proguard-build-action-sml.png)](images/vs/05-proguard-build-action.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Action de génération Proguard](images/xs/05-proguard-build-action-sml.png)](images/xs/05-proguard-build-action.png#lightbox)

-----

ProGuard est désactivé par défaut. L’option **Activer ProGuard** est disponible uniquement lorsque le projet est en mode **Mise en production**. Toutes les actions de génération ProGuard sont ignorées sauf si **Activer ProGuard** est activé. La configuration ProGuard de Xamarin.Android n’obfusque pas l’APK et il n’est pas possible d’activer l’obfuscation, même avec des fichiers de configuration personnalisés. Si vous souhaitez utiliser l’obfuscation, consultez [Protection des applications avec Dotfuscator](~/android/deploy-test/release-prep/index.md#dotfuscator). 

Pour plus d’informations sur l’utilisation de l’outil ProGuard, consultez [ProGuard](~/android/deploy-test/release-prep/proguard.md).

<a name="protect_app" />

## <a name="protect-the-application"></a>Protéger l'application

<a name="Disable_Debugging" />

### <a name="disable-debugging"></a>Désactiver le débogage

Au cours du développement d’une application Android, le débogage est réalisé à l’aide du protocole *JDWP* (Java Debug Wire Protocol). Il s’agit d’une technologie qui permet à des outils tels qu’**adb** de communiquer avec une machine virtuelle Java pour le débogage. JDWP est activé par défaut pour les builds Debug d’une application Xamarin.Android. JDWP est important pendant le développement, mais peut poser un problème de sécurité pour les applications mises en production. 

> [!IMPORTANT]
> Désactivez toujours l’état de débogage dans une application mise en production. À défaut, il sera possible (avec JDWP) d’obtenir un accès total au processus Java et d’exécuter du code arbitraire dans le contexte de l’application.

Le manifeste Android contient l’attribut `android:debuggable`, qui contrôle si l’application peut ou non être déboguée. Il est considéré comme une bonne pratique de définir l’attribut `android:debuggable` sur `false`. Pour ce faire, le plus simple consiste à ajouter une instruction de compilation conditionnelle dans **AssemblyInfo.cs** : 

```csharp
#if DEBUG
[assembly: Application(Debuggable=true)]
#else
[assembly: Application(Debuggable=false)]
#endif
```

Notez que les builds Debug définissent automatiquement certaines autorisations pour faciliter le débogage (comme **Internet** et **ReadExternalStorage**). Toutefois, les builds Debug n’utilisent que les autorisations que vous configurez explicitement. Si vous découvrez qu’en basculant vers la build de mise en production votre application perd une autorisation qui était disponible dans la build Debug, vérifiez que vous avez explicitement activé cette autorisation dans la liste **Autorisations nécessaires**, comme décrit dans [Autorisations](~/android/app-fundamentals/permissions.md). 

<a name="dotfuscator" id="dotfuscator" />

### <a name="application-protection-with-dotfuscator"></a>Protection des applications avec Dotfuscator

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Même lorsque le [débogage est désactivé](#Disable_Debugging), les pirates peuvent recréer le package d’une application, en ajoutant ou supprimant des options de configuration ou des autorisations. Ils peuvent alors rétroconcevoir, déboguer ou falsifier l’application.
[Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) peut être utilisé pour obfusquer le code managé et injecter du code de détection de l’état de sécurité à l’exécution dans une application Xamarin.Android au moment de la génération pour détecter et répondre si l’application s’exécute sur un appareil rooté.

Dotfuscator CE est fourni avec Visual Studio 2017.
Pour utiliser Dotfuscator, cliquez sur **Outils > PreEmptive Protection - Dotfuscator**.

Pour configurer Dotfuscator CE, consultez [Utilisation de Dotfuscator Community Edition avec Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator).
Une fois configuré, Dotfuscator CE protégera automatiquement chaque build créée.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Même lorsque le [débogage est désactivé](#Disable_Debugging), les pirates peuvent recréer le package d’une application, en ajoutant ou supprimant des options de configuration ou des autorisations. Ils peuvent alors rétroconcevoir, déboguer ou falsifier l’application.
Même s’il ne prend pas en charge Visual Studio pour Mac, vous pouvez utiliser [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) avec Visual Studio pour obfusquer le code managé et injecter du code de détection de l’état de sécurité à l’exécution dans une application Xamarin.Android au moment de la génération pour détecter et répondre si l’application s’exécute sur un appareil rooté.

Pour configurer Dotfuscator CE, consultez [Utilisation de Dotfuscator Community Edition avec Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator).
Une fois configuré, Dotfuscator CE protégera automatiquement chaque build créée.

-----

<a name="bundle" />

### <a name="bundle-assemblies-into-native-code"></a>Regrouper les assemblys dans du code natif

Lorsque cette option est activée, les assemblys sont regroupés dans une bibliothèque partagée native. Cette option sécurise le code. Elle protège les assemblys managés en les incorporant dans des fichiers binaires natifs.

Cette option requiert une licence Entreprise et est disponible uniquement lorsque l’option **Utiliser Fast Deployment** est désactivée. **Regrouper les assemblys dans le code natif** est désactivé par défaut.

Notez que l’option **Regrouper les assemblys dans le code natif** ne signifie *pas* que les assemblys sont compilés en code natif. Il n’est pas possible d’utiliser [**Compilation AOT**](#aot) pour compiler des assemblys en code natif (à ce stade il s’agit d’une fonctionnalité expérimentale qui n’est pas utilisée en production).

<a name="aot" />

### <a name="aot-compilation"></a>Compilation AOT

L’option **Compilation AOT** (sur la page [Propriétés de création de package](#Set_Packaging_Properties)) active la compilation Ahead-of-Time (AOT) des assemblys. Lorsque cette option est activée, la charge de démarrage JIT (Just-In-Time) est réduite en précompilant les assemblys avant l’exécution. Le code natif qui en résulte est dans l’APK avec les assemblys non compilés. Cela réduit le temps de démarrage de l’application, mais au détriment de la taille de l’APK qui est légèrement plus grande.

L’option **Compilation AOT** requiert une licence Entreprise ou supérieure. **Compilation AOT** est disponible uniquement lorsque le projet est configuré pour le mode Mise en production et elle est désactivée par défaut. Pour plus d’informations sur la compilation AOT, consultez [AOT](https://www.mono-project.com/docs/advanced/aot/).

#### <a name="llvm-optimizing-compiler"></a>Compilateur d'optimisation LLVM

Le _compilateur d’optimisation LLVM_ crée du code compilé plus petit et plus rapide et convertit en code natif les assembys compilés en AOT, mais au détriment de la vitesse de génération qui est plus lente. Le compilateur LLVM est désactivé par défaut. Pour utiliser le compilateur LLVM, l’option **Compilation AOT** doit être activée au préalable (sur la page [Propriétés de création de packages](#Set_Packaging_Properties)).


> [!NOTE]
> L’option **Compilateur d’optimisation LLVM** requiert une licence Entreprise.  

<a name="Set_Packaging_Properties" />

## <a name="set-packaging-properties"></a>Définir les propriétés de création de package

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Les propriétés de création de package peuvent être définies dans la section **Options Android** des **Propriétés** du projet, comme illustré dans la capture d’écran suivante :

[![Propriétés de l’empaquetage](images/vs/04-packaging-sml.png)](images/vs/04-packaging.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Les propriétés de création de package peuvent être définies dans les **Options du projet**, comme illustré dans la capture d’écran suivante :

[![Propriétés de l’empaquetage](images/xs/04-packaging-sml.png)](images/xs/04-packaging.png#lightbox)

-----

Nombre de ces propriétés, comme **Utiliser le runtime partagé** et **Utiliser Fast Deployment**, sont prévues pour le mode Debug. Toutefois, lorsque l’application est configurée pour le mode Mise en production, d’autres paramètres permettent de déterminer la manière dont l’application est [optimisée en termes de taille et de vitesse d’exécution](#shrink_apk), [la manière dont elle est protégée contre la falsification](#protect_app) et la manière dont elle peut être ajoutée à un package pour prendre en charge différentes architectures et restrictions de taille.

### <a name="specify-supported-architectures"></a>Spécifier les architectures prises en charge

Lors de la préparation d’une application Xamarin.Android pour sa mise en production, il est nécessaire de spécifier les architectures de processeur qui sont prises en charge. Un même APK peut contenir du code machine permettant la prise en charge de plusieurs architectures différentes. Pour plus d’informations sur la prise en charge de plusieurs architectures de processeur, consultez [Architectures de processeur](~/android/app-fundamentals/cpu-architectures.md).

### <a name="generate-one-package-apk-per-selected-abi"></a>Générer un package (.APK) par ABI sélectionnée

Lorsque cette option est activée, un APK est créé pour chacune des ABI prises en charge (sélectionnées sous l’onglet **Avancé**, comme décrit dans [Architectures de processeur](~/android/app-fundamentals/cpu-architectures.md)), plutôt qu’un seul grand APK pour toutes les ABI prises en charge. Cette option est disponible uniquement quand le projet est configuré pour le mode Mise en production et elle est désactivée par défaut.

### <a name="multi-dex"></a>Multi-Dex

Lorsque l’option **Activer Multi-Dex** est activée, les outils du kit Android SDK sont utilisés pour contourner la limite de 65 000 méthodes du format de fichier **.dex**. La limite de 65 000 méthodes repose sur le nombre de méthodes Java _référencées_ par une application (y compris les méthodes se trouvant dans les bibliothèques dont dépend l’application) &ndash; elle ne repose pas sur le nombre de méthodes qui sont _écrites dans le code source_. Si une application ne définit que quelques méthodes alors qu’elle en utilise beaucoup (ou de grandes bibliothèques), la limite de 65 000 peut être dépassée.

Il est possible qu’une application n’utilise pas toutes les méthodes de toutes les bibliothèques référencées, et donc qu’un outil tel que ProGuard (voir ci-dessus) puisse supprimer du code les méthodes non utilisées. La bonne pratique consiste à activer **Activer Multi-Dex** seulement si cela est absolument nécessaire, autrement dit si l’application référence toujours plus de 65 000 méthodes Java même après avoir utilisé ProGuard.

Pour plus d’informations sur Multi-Dex, consultez [Configurer les applications avec plus de 64K méthodes](https://developer.android.com/tools/building/multidex.html).

<a name="Compile" />

## <a name="compile"></a>Compile

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Une fois toutes les étapes ci-dessus terminées, l’application est prête à être compilée. Sélectionnez **Générer > Regénérer la solution** pour vérifier que l’application est correctement générée en mode Mise en production. Notez que cette étape ne produit pas encore un APK.

La création de package et la signature sont abordées plus en détail dans la section [Signature du paquet d’application](~/android/deploy-test/signing/index.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Une fois que toutes les étapes ci-dessus sont terminées, compilez l’application (sélectionnez **Générer > Générer tout**) pour vérifier qu’elle est correctement générée en mode Mise en production. Notez que cette étape ne produit pas encore un APK.

-----

<a name="archive" />

## <a name="archive-for-publishing"></a>Archiver pour publication

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour commencer le processus de publication, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis sélectionnez l’élément de menu contextuel **Archiver...**  :

[![Archiver l’application](images/vs/07-archive-for-publishing-sml.png)](images/vs/07-archive-for-publishing.png#lightbox)

**Archiver...**  lance le **Gestionnaire d’archives** et commence le processus d’archivage du bundle d’applications, comme illustré dans cette capture d’écran :

[![Gestionnaire d’archives](images/vs/08-archive-manager-sml.png)](images/vs/08-archive-manager.png#lightbox)

Vous pouvez également créer une archive en cliquant avec le bouton droit sur la solution dans l’**Explorateur de solutions**, puis en sélectionnant **Archiver tout...** , ce qui génère la solution et archive tous les projets Xamarin pouvant générer une archive :

[![Archiver tout](images/vs/09-archive-all-sml.png)](images/vs/09-archive-all.png#lightbox)

**Archiver** et **Archiver tout** lancent automatiquement le **Gestionnaire d’archives**. Pour lancer directement le **Gestionnaire d’archives**, cliquez sur l’élément de menu **Outils > Gestionnaire d’archives...**  :

[![Lancer le Gestionnaire d’archives](images/vs/10-launch-archive-manager-sml.png)](images/vs/10-launch-archive-manager.png#lightbox)

Les archives de la solution sont accessibles à tout moment en cliquant avec le bouton droit sur le nœud **Solution** et en sélectionnant **Afficher les archives** :

[![Afficher les archives](images/vs/11-view-archives-sml.png)](images/vs/11-view-archives.png#lightbox)

### <a name="the-archive-manager"></a>Gestionnaire d'archives

Le **Gestionnaire d’archives** est composé d’un volet **Liste des solutions**, d’un volet **Liste des archives** et d’un **Panneau des détails** :

[![Volets du Gestionnaire d’archives](images/vs/12-archive-manager-detail-sml.png)](images/vs/12-archive-manager-detail.png#lightbox)

Le volet **Liste des solutions** affiche toutes les solutions ayant au moins un projet archivé. **Liste des solutions** comprend les sections suivantes :

- **Solution actuelle** &ndash; Affiche la solution actuelle. Notez que cette zone peut être vide s’il n’existe aucune archive pour la solution actuelle.
- **Toutes les archives** &ndash; Affiche toutes les solutions ayant une archive.
- Zone de texte **Rechercher** (en haut) &ndash; Filtre les solutions répertoriées dans la liste **Toutes les archives** en fonction de la chaîne de recherche entrée.

Le volet **Liste des archives** affiche la liste de toutes les archives de la solution sélectionnée. **Liste des archives** comprend les sections suivantes :

- **Nom de la solution sélectionnée** &ndash; Affiche le nom de la solution sélectionnée dans le volet **Liste des solutions**. Toutes les informations affichées dans le volet **Liste des archives** font référence à la solution sélectionnée.
- **Filtre Plateformes** &ndash; Ce champ permet de filtrer les archives par type de plateforme (par exemple, iOS ou Android).
- **Éléments d’archive** &ndash; Liste des archives de la solution sélectionnée. Chaque élément de cette liste inclut le nom du projet, la date de création et la plateforme. Cette liste peut également afficher des informations supplémentaires telles que la progression de l’archivage ou de la publication d’un élément.

Le **Panneau des détails** affiche des informations supplémentaires sur chaque archive. Il permet également à l’utilisateur de démarrer le workflow de distribution ou d’ouvrir le dossier dans lequel la distribution a été créée. La section **Commentaires de build** permet d’inclure des commentaires de build dans l’archive.

### <a name="distribution"></a>Distribution

Lorsqu’une version archivée de l’application est prête à être publiée, sélectionnez l’archive dans le **Gestionnaire d’archives** et cliquez sur le bouton **Distribuer...**  :

[![Bouton Distribuer](images/vs/13-distribute-sml.png)](images/vs/13-distribute.png#lightbox)

La boîte de dialogue **Canal de distribution** affiche des informations sur l’application, indique la progression du workflow de distribution et propose un choix de canaux de distribution. Lors de sa première exécution, deux choix sont présentés :

[![Sélectionner un canal de distribution](images/vs/14-distribution-channel-sml.png)](images/vs/14-distribution-channel.png#lightbox)

Il est possible de choisir l’un des canaux de distribution suivants :

- **Ad-Hoc** &ndash; Enregistre un APK signé sur disque qui peut être chargé de façon indépendante (sideloaded) sur des appareils Android. Poursuivez avec [Signature du paquet d’application](~/android/deploy-test/signing/index.md) pour apprendre à créer une identité de signature Android, à créer un certificat de signature pour les applications Android et à publier une version _ad hoc_ de l’application sur disque. Il s’agit d’un bon moyen de créer un APK de test.

- **Google Play** &ndash; Publie un APK signé sur Google Play. Poursuivez avec [Publication sur Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) pour apprendre comment signer et publier un APK sur Google Play Store.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour commencer le processus de publication, sélectionnez **Générer > Archiver pour publication** :

[![Archiver pour publication](images/xs/07-archive-for-publishing-sml.png)](images/xs/07-archive-for-publishing.png#lightbox)

**Archiver pour publication** génère le projet et le regroupe dans un fichier d’archive. L’option de menu **Archiver tout** archive tous les projets archivables de la solution. Ces deux options ouvrent automatiquement le **Gestionnaire d’archives** à l’issue des opérations de génération et de regroupement :

[![Vue Archive](images/xs/08-archives-view-sml.png)](images/xs/08-archives-view.png#lightbox)

Dans cet exemple, le **Gestionnaire d’archives** répertorie une seule application archivée, **MyApp**. Notez que le champ de commentaire permet d’enregistrer un bref commentaire avec l’archive. Pour publier une version archivée d’une application Xamarin.Android, sélectionnez l’application dans le **Gestionnaire d’archives**, puis cliquez sur **Signe et distribuer...** , comme illustré ci-dessus. La boîte de dialogue **Signer et distribuer** qui s’affiche propose deux options :

[![Signer et distribuer](images/xs/09-sign-and-distribute-sml.png)](images/xs/09-sign-and-distribute.png#lightbox)

Vous pouvez y sélectionner le canal de distribution :

- **Ad-Hoc** &ndash; Enregistre un APK signé sur disque de manière à pouvoir en charger une version test sur des appareils Android. Poursuivez avec [Signature du paquet d’application](~/android/deploy-test/signing/index.md) pour apprendre à créer une identité de signature Android, à créer un certificat de signature pour les applications Android et à publier une version &ldquo;ad hoc&rdquo; de l’application sur disque. Il s’agit d’un bon moyen de créer un APK de test.


- **Google Play** &ndash; Publie un APK signé sur Google Play.
    Poursuivez avec [Publication sur Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) pour apprendre comment signer et publier un APK sur Google Play Store.

-----

## <a name="related-links"></a>Liens associés

- [Appareils multicœurs et Xamarin.Android](~/android/deploy-test/multicore-devices.md)
- [Architectures de processeur](~/android/app-fundamentals/cpu-architectures.md)
- [AOT](https://www.mono-project.com/docs/advanced/aot/)
- [Réduire le code et les ressources](https://developer.android.com/tools/help/proguard.html)
- [Configurer des applications avec plus de 64 000 méthodes](https://developer.android.com/tools/building/multidex.html)
