---
title: Installation et l’utilisation de watchOS dans Xamarin
description: Ce document décrit comment installer et utiliser watchOS avec Xamarin. Il aborde l’installation, watchOS project structure, comment utiliser le concepteur iOS, l’intégration de Xcode et fournit des conseils de dépannage.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/05/2017
ms.openlocfilehash: b4d827715870d54de3116067a3e24e229ecda530
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065624"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Installation et l’utilisation de watchOS dans Xamarin

watchOS 4 nécessite macOS Sierra (10.12) avec Xcode 9.

watchOS 1 requis à l’origine OS X Yosemite (10.10) avec Xcode 7.

> [!WARNING]
> [mises à jour watchOS 1 ne seront pas acceptés après le 1er avril 2018](https://developer.apple.com/news/?id=11162017a). Mises à jour ultérieures doivent utiliser watchOS 2 SDK ou une version ultérieure ; génération avec la watchOS 4 SDK est recommandé.

## <a name="project-structure"></a>Structure de projet

Une application watch se compose de trois projets :

- **Projet d’application Xamarin.iOS iPhone** -il s’agit d’un projet iPhone normal, il peut être un des modèles de Xamarin.iOS. L’application Watch et son extension est fournies à l’intérieur de ce projet principal.

- **Projet d’extension Watch** : contient le code (par exemple, les classes de contrôleur) pour l’application Watch.

- **Projet d’application Watch** : contient le fichier Storyboard de l’interface utilisateur avec toutes les ressources d’interface utilisateur de l’application Watch.

Le [exemple espion Kit Catalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) solution ressemble à ceci dans Xamarin.Studio :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](installation-images/catalog-solution.png "La solution dans Visual Studio")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "La solution dans Visual Studio")

-----

Téléchargez et exécutez le [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) exemple pour commencer.
Vous trouverez des écrans à partir de l’exemple sur le [contrôles](~/ios/watchos/user-interface/index.md) page.


## <a name="creating-a-new-project"></a>Création d'un projet

Vous ne pouvez pas créer une « espion Solution »... au lieu de cela, vous pouvez ajouter une application Watch à une application iOS existante. Suivez ces étapes pour créer une application watch :

1. Si vous n’avez pas un projet existant, vous devez tout d’abord choisir **fichier > Nouvelle Solution** et créer une application iOS (par exemple, un **application avec affichage unique**) :

    [![](installation-images/cycle8-2-sml.png "Choisissez Fichier > Nouvelle Solution et créer une application iOS")](installation-images/cycle8-2.png#lightbox)

2. Une fois l’application iOS est créée (ou si vous envisagez d’utiliser votre application iOS existante), avec le bouton droit sur la solution et choisissez **Ajouter > Ajouter un nouveau projet...** . Dans le **nouveau projet** fenêtre Sélectionnez **watchOS > application > application WatchKit**:

    [![](installation-images/cycle8-6-sml.png "Sélectionnez watchOS > application > application WatchKit")](installation-images/cycle8-6.png#lightbox)

3. L’écran suivant vous permet de choisir le projet d’application iOS doit inclure l’application Apple watch :

    [![](installation-images/cycle8-7-sml.png "Choisissez le projet d’application iOS doit inclure l’application Apple watch")](installation-images/cycle8-7.png#lightbox)

4. Enfin, choisissez l’emplacement pour enregistrer le projet (et éventuellement activée de contrôle de code source) :

    [![](installation-images/cycle8-8-sml.png "Choisissez l’emplacement pour enregistrer le projet")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio pour Mac configure automatiquement [références de projet et **Info.plist** paramètres](~/ios/watchos/get-started/project-references.md) pour vous.

## <a name="creating-the-watch-user-interface"></a>Création de l’Interface utilisateur de surveillance

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>À l’aide du Concepteur de Xamarin iOS

Double-cliquez sur l’application watch **Interface.storyboard** à modifier à l’aide du concepteur iOS. Vous pouvez faire glisser contrôleurs d’interface et des contrôles d’interface utilisateur du plan conceptuel à partir la **boîte à outils** et les configurer à l’aide de la **propriétés** panneau :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "La table de montage séquentiel dans le Concepteur")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "La table de montage séquentiel dans le Concepteur")](installation-images/iosdesigner-vs.png#lightbox)

-----

Vous devez donner à chaque nouveau contrôleur d’interface une **classe** en la sélectionnant et en entrant le nom dans la **propriétés** pad (Cela créera requis C# automatiquement les fichiers code-behind) :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Donnez à chaque nouveau contrôleur d’interface à une classe")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Donnez à chaque nouveau contrôleur d’interface à une classe")

-----

Créer des enchaînements par **Ctrl + glisser** à partir d’un contrôleur de bouton, d’une table ou d’interface sur un autre contrôleur d’interface.


### <a name="using-xcode-on-the-mac"></a>À l’aide de Xcode sur le Mac

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Vous pouvez continuer à utiliser Xcode pour créer votre interface utilisateur en effectuant un clic droit sur le fichier Interface.storyboard et en sélectionnant **ouvrir avec > Xcode Interface Builder**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Utilisateurs de Visual Studio peuvent également utiliser Xcode pour générer leur interface utilisateur par basculement pour utiliser l’hôte de Build Mac directement.
Ouvrez votre solution dans Visual Studio pour Mac avec le bouton droit sur le fichier Interface.storyboard et sélectionnez **ouvrir avec > Xcode Interface Builder**:

-----

![](installation-images/openwith-xcode.png "Ouvrez le Interface.storyboard dans Xcode Interface Builder")

Si à l’aide de Xcode, puis vous devez suivre les mêmes étapes pour les applications cadran que pour normal [iOS application storyboards](~/ios/user-interface/storyboards/index.md) (telles que la création des outlets et actions par **Ctrl + glisser** dans le **.h**fichier d’en-tête).

Lorsque vous enregistrez la table de montage dans Xcode Interface Builder, il ajoute automatiquement les prises de courant et les actions que vous créez pour le C# **. designer.cs** fichiers dans le projet d’extension watch.


### <a name="adding-additional-screens-in-xcode"></a>Ajout d’écrans supplémentaires dans Xcode

Lorsque vous ajoutez des écrans supplémentaires (au-delà de ce qui est inclus dans le modèle par défaut) à votre table de montage séquentiel à l’aide de Xcode Interface Builder **vous devez ajouter manuellement le C# fichiers de code** pour chaque nouveau contrôleur d’interface.

Reportez-vous à la [avancé des instructions sur l’ajout de nouveaux contrôleurs de l’interface de montage](~/ios/watchos/troubleshooting.md#add).

*Le Concepteur de Xamarin iOS fait automatiquement, aucune étape manuelle est nécessaire.*


## <a name="building"></a>Génération

Un projet qui inclut une application watch généré, comme d’autres projets iOS. Le processus de génération entraîne une application iPhone (.app) qui contient une extension watch (.appex), qui à son tour contient l’application sans code espion (.app).


## <a name="launching"></a>Lancement

Vous pouvez lancer des applications cadran dans le simulateur à l’aide de Visual Studio pour Mac ou Visual Studio (démarre sur l’hôte de Build Mac).

Il existe deux modes pour le lancement d’une application WatchKit :

- mode d’application normal (valeur par défaut), et
- [Notifications](~/ios/watchos/platform/notifications.md) (ce qui nécessite une charge utile de notification test au format JSON).

### <a name="xcode-8-support"></a>Prise en charge de Xcode 8

Une fois que Xcode 8 (ou version ultérieure) est installé, les simulateurs Apple Watch sont séparés à partir d’iOS simulateurs (contrairement à [Xcode 6](#xcode6), où elles apparaissaient comme un *affichage externe*).
Lorsque vous sélectionnez le projet d’application Watch et rendre le projet de démarrage, la liste des simulateurs affichera *iOS simulateurs* sélectionnables (comme indiqué ci-dessous).

[![](installation-images/xs-xcode8-watchos3-sml.png "Sélection du type de simulateur")](installation-images/xs-xcode8-watchos3.png#lightbox)

Lorsque vous démarrez le débogage, *deux* simulateurs doit démarrer - le simulateur iOS *et* le simulateur d’Apple Watch. Utilisez **commande + MAJ + H** pour accéder à la face de menu et d’horloge espion ; et utilisez le **matériel** menu pour définir le **Force la pression tactile**. Le défilement sur le pavé tactile ou une souris simule à l’aide de la couronne numérique.

#### <a name="troubleshooting"></a>Résolution des problèmes

L’erreur suivante s’affiche dans le **sortie de l’Application** si vous essayez de lancer un simulateur qui n’a pas un espion jumelé :

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Reportez-vous à [les forums d’Apple](https://forums.developer.apple.com/thread/7783) pour obtenir des instructions sur la configuration les simulateurs, si les valeurs par défaut ne fonctionnent pas.


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 et watchOS 1

Vous devez apporter la *projet d’extension watch* le **projet de démarrage** avant l’exécution ou de débogage de l’application. Vous ne pouvez pas « start » l’application watch elle-même, et si vous choisissez l’application iOS il démarre normalement dans le simulateur iOS.

Par défaut, une application watch démarre en mode normal **application** mode (mode Aperçu pas ou Notifications) à partir de Visual Studio pour Mac **exécuter** ou **déboguer** commandes.

Lorsque vous utilisez Xcode 6, uniquement l’iPhone 5, 5 s iPhone, iPhone 6 et iPhone 6 Plus peut activer l’affichage externe pour soit **Apple Watch - 38mm** ou **Apple Watch - 42mm** où les applications espion sera affiché.

> [!NOTE]
> N’oubliez pas que l’écran espion n’apparaît pas automatiquement dans le simulateur iOS lors de l’utilisation de Xcode 6.
> Utilisez le **matériel > affiche externe** menu pour afficher l’écran de surveillance.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Lancement du Mode de Notification

Reportez-vous à la [page Notifications](~/ios/watchos/platform/notifications.md) pour plus d’informations comment gérer les notifications dans le code.


Visual Studio pour Mac peut démarrer l’application watch avec une notification _modes de démarrage_ pour les notifications :



Avec le bouton droit sur le projet d’application watch et choisissez **exécuter avec > Configuration personnalisée...** :


[![](installation-images/runwith-customparams-sml.png "Une Configuration personnalisée en cours d’exécution")](installation-images/runwith-customparams.png#lightbox)


Cette opération ouvre le **personnalisé paramètres** fenêtre dans laquelle vous pouvez sélectionner **Notification** (et fournir une charge utile JSON), puis appuyez sur **exécuter** pour démarrer l’application de surveillance dans le simulateur :


[![](installation-images/runwith-execargs-sml.png "Définition de la Notification et la charge utile")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>Débogage

Le débogage est pris en charge dans Visual Studio pour Mac et Visual Studio.
N’oubliez pas de fournir un fichier JSON de notification lors du débogage en mode de notifications. Cette capture d’écran montre un point d’arrêt de débogage en cours d’accès dans une application watch :

![](installation-images/debug-sml.png "Cette capture d’écran montre un point d’arrêt de débogage en cours d’accès dans une application watch")

Après avoir suivi les instructions de lancement vous finirez avec votre application watch en cours d’exécution le **iOS Simulator (Watch)** .
Pour le mode de notification, vous pouvez sélectionner **Déboguer > Ouvrir le journal système** (**CMD + /** ) et utiliser `Console.WriteLine` dans votre code.

### <a name="debugging-lifecycle-event-handlers"></a>Débogage des gestionnaires d’événements de cycle de vie

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

Les fichiers de modèle watchOS (tel que `InterfaceController`, `ExtensionDelegate`, `NotificationController`, et `ComplicationController`) sont fournis avec leurs méthodes de cycle de vie requis déjà implémentés. Ajouter `Console.WriteLine` appels et lire le **sortie de l’Application** pour mieux comprendre le cycle de vie d’événement.



## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Première vidéo de l’application Apple Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Conseils de WatchKit d’Apple](https://developer.apple.com/watchkit/tips/)
