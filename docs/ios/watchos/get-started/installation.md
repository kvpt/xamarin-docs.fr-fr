---
title: Installation et utilisation de Watchos dans Xamarin
description: Ce document décrit comment installer et utiliser Watchos avec Xamarin. Il traite de l’installation, de la structure du projet Watchos, de l’utilisation du concepteur iOS, de l’intégration de Xcode et fournit des conseils de dépannage.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: 4cc321f44238a7b738e40c02656b42f1eda1155a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938760"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Installation et utilisation de Watchos dans Xamarin

Watchos 4 requiert macOS Sierra (10,12) avec Xcode 9.

Watchos 1 nécessitait à l’origine OS X Yosemite (10,10) avec Xcode 7.

> [!WARNING]
> les [mises à jour Watchos 1 ne seront pas acceptées après le 1er avril 2018](https://developer.apple.com/news/?id=11162017a). Les futures mises à jour doivent utiliser Watchos 2 SDK ou une version ultérieure. la création avec le kit de développement logiciel Watchos 4 est recommandée.

## <a name="project-structure"></a>Structure du projet

Une application Watch se compose de trois projets :

- **Projet d’application iPhone Xamarin. iOS** -il s’agit d’un projet iPhone normal, il peut s’agir de l’un des modèles Xamarin. iOS. L’application Watch et son extension sont regroupées dans ce projet principal.

- **Projet d’extension Watch** : contient le code (par exemple, les classes de contrôleur) pour l’application Watch.

- **Projet d’application Watch** : contient le fichier Storyboard de l’interface utilisateur avec toutes les ressources d’interface utilisateur de l’application Watch.

L' [exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) de solution du catalogue du kit de surveillance ressemble à ceci dans Xamarin. Studio :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![La solution dans Visual Studio](installation-images/catalog-solution.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![La solution dans Visual Studio](installation-images/catalog-solution-vs.png)

-----

Téléchargez et exécutez l’exemple [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) pour commencer.
Les écrans de l’exemple se trouvent dans la page [contrôles](~/ios/watchos/user-interface/index.md) .

## <a name="creating-a-new-project"></a>Création d'un projet

Vous ne pouvez pas créer une nouvelle solution « regarder une solution »... au lieu de cela, vous pouvez ajouter une application espion à une application iOS existante. Pour créer une application Watch, procédez comme suit :

1. Si vous n’avez pas de projet existant, choisissez d’abord **fichier > nouvelle solution** et créez une application iOS (par exemple, une **application de vue unique**) :

    [![Choisir un fichier > nouvelle solution et créer une application iOS](installation-images/cycle8-2-sml.png)](installation-images/cycle8-2.png#lightbox)

2. Une fois l’application iOS créée (ou si vous envisagez d’utiliser votre application iOS existante), cliquez avec le bouton droit sur la solution et choisissez **ajouter > ajouter un nouveau projet.**.. Dans la fenêtre **nouveau projet** , sélectionnez **watchos > application > application WatchKit**:

    [![Sélectionner l’application > Watchos > application WatchKit](installation-images/cycle8-6-sml.png)](installation-images/cycle8-6.png#lightbox)

3. L’écran suivant vous permet de choisir le projet d’application iOS qui doit inclure l’application espion :

    [![Choisir le projet d’application iOS qui doit inclure l’application Watch](installation-images/cycle8-7-sml.png)](installation-images/cycle8-7.png#lightbox)

4. Enfin, choisissez l’emplacement d’enregistrement du projet (et éventuellement activez le contrôle de code source) :

    [![Choisir l’emplacement d’enregistrement du projet](installation-images/cycle8-8-sml.png)](installation-images/cycle8-8.png#lightbox)

5. Visual Studio pour Mac configure automatiquement les [références de projet et les paramètres **info. plist** ](~/ios/watchos/get-started/project-references.md) .

## <a name="creating-the-watch-user-interface"></a>Création de l’interface utilisateur Watch

<a name="designer"></a>

### <a name="using-the-xamarin-ios-designer"></a>Utilisation du concepteur iOS Xamarin

Double-cliquez sur **interface. Storyboard** de l’application Watch pour la modifier à l’aide du concepteur iOS. Vous pouvez faire glisser les contrôleurs d’interface et les contrôles d’interface utilisateur dans le Storyboard à partir de la **boîte à outils** et les configurer à l’aide du panneau **Propriétés** :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![La table de montage séquentiel dans le concepteur](installation-images/iosdesigner-sml.png)](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La table de montage séquentiel dans le concepteur](installation-images/iosdesigner-sml-vs.png)](installation-images/iosdesigner-vs.png#lightbox)

-----

Vous devez attribuer à chaque nouveau contrôleur d’interface une **classe** en le sélectionnant et en entrant le nom dans le panneau **Propriétés** (cela créera automatiquement les fichiers code-behind C# requis) :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Donnez à chaque nouveau contrôleur d’interface une classe](installation-images/iosdesigner-classname.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Donnez à chaque nouveau contrôleur d’interface une classe](installation-images/iosdesigner-classname-vs.png)

-----

Créez SEGUES en **appuyant sur Ctrl + glisser-déplacer** d’un contrôle Button, table ou Interface Controller sur un autre contrôleur d’interface.

### <a name="using-xcode-on-the-mac"></a>Utilisation de XCode sur le Mac

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Vous pouvez continuer à utiliser Xcode pour créer votre interface utilisateur en cliquant avec le bouton droit sur le fichier interface. Storyboard et en sélectionnant **Ouvrir avec > Xcode Interface Builder**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Les utilisateurs de Visual Studio peuvent également utiliser Xcode pour créer leur interface utilisateur en basculant pour utiliser directement l’hôte de build Mac.
Ouvrez votre solution dans Visual Studio pour Mac, cliquez avec le bouton droit sur le fichier interface. Storyboard et sélectionnez **Ouvrir avec > Interface Builder Xcode**:

-----

![Ouvrez l’interface. Storyboard dans Xcode Interface Builder](installation-images/openwith-xcode.png)

Si vous utilisez Xcode, vous devez suivre les mêmes étapes pour les applications de surveillance que pour les [storyboards d’application iOS](~/ios/user-interface/storyboards/index.md) normales (telles que la création de prises et d’actions par **Ctrl + glisser** dans le fichier d’en-tête **. h** ).

Lorsque vous enregistrez la table de montage séquentiel dans Xcode Interface Builder, elle ajoute automatiquement les prises et les actions que vous créez aux fichiers C# **. Designer.cs** dans le projet d’extension Watch.

### <a name="adding-additional-screens-in-xcode"></a>Ajout d’écrans supplémentaires dans Xcode

Lorsque vous ajoutez des écrans supplémentaires (au-delà de ce qui est inclus dans le modèle par défaut) à votre table de montage séquentiel à l’aide de Xcode Interface Builder **vous devez ajouter manuellement les fichiers de code C#** pour chaque nouveau contrôleur d’interface.

Reportez-vous aux [instructions avancées sur l’ajout de nouveaux contrôleurs d’interface à une table de montage séquentiel](~/ios/watchos/troubleshooting.md#add).

*Le concepteur iOS Xamarin effectue cette opération automatiquement, aucune étape manuelle n’est requise.*

## <a name="building"></a>Génération

Un projet qui comprend une application Watch est généré comme d’autres projets iOS. Le processus de génération génère une application iPhone (. app) qui contient une extension Watch (. APPEX), qui contient à son tour l’application espion sans code (. app).

## <a name="launching"></a>Lancement

Vous pouvez lancer des applications de surveillance dans le simulateur à l’aide de Visual Studio pour Mac ou Visual Studio (démarre sur l’hôte de build Mac).

Il existe deux modes de lancement d’une application WatchKit :

- mode d’application normal (valeur par défaut)
- [Notifications](~/ios/watchos/platform/notifications.md) (qui requiert une charge utile de notification de test au format JSON).

### <a name="xcode-8-support"></a>Support Xcode 8

Une fois que Xcode 8 (ou version ultérieure) est installé, les simulateurs Apple Watch sont distincts des simulateurs iOS (contrairement à [Xcode 6](#xcode6), où ils apparaissent sous la forme d’un *affichage externe*).
Lorsque vous sélectionnez le projet d’application Watch et que vous en faites le projet de démarrage, la liste simulateur affiche les *simulateurs iOS* à choisir (comme indiqué ci-dessous).

[![Sélection du type de simulateur](installation-images/xs-xcode8-watchos3-sml.png)](installation-images/xs-xcode8-watchos3.png#lightbox)

Lorsque vous démarrez le débogage, *deux* simulateurs doivent démarrer : le simulateur iOS *et* le simulateur de Apple Watch. Utilisez la **Commande + Maj + H** pour accéder au menu espion et à la facette de l’horloge. et utilisez le menu **matériel** pour définir la **pression force Touch**. Le défilement sur le pavé tactile ou la souris simule à l’aide de la Digital Crown.

#### <a name="troubleshooting"></a>Dépannage

L’erreur suivante s’affiche dans la **sortie** de l’application si vous essayez de lancer un simulateur qui n’a pas d’espion jumelé :

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Reportez-vous aux [forums d’Apple](https://forums.developer.apple.com/thread/7783) pour obtenir des instructions sur la configuration des simulateurs, si les valeurs par défaut ne fonctionnent pas.

<a name="xcode6"></a>

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 et Watchos 1

Vous devez faire en sorte que l' *extension Watch projette* le **projet de démarrage** avant d’exécuter ou de déboguer l’application. Vous ne pouvez pas « démarrer » l’application Watch elle-même, et si vous choisissez l’application iOS, elle démarrera normalement dans le simulateur iOS.

Par défaut, une application espion démarre en mode d' **application** normal (pas d’aperçu ou en mode notifications) à partir des commandes d' **exécution** ou de **débogage** de Visual Studio pour Mac.

Quand vous utilisez Xcode 6, seuls les iPhone 5, iPhone 5 S, iPhone 6 et iPhone 6 plus peuvent activer l’affichage externe pour **Apple Watch-38mm** ou **Apple Watch-Watch 42mm** où les applications Watch seront affichées.

> [!NOTE]
> N’oubliez pas que l’écran espion n’apparaît pas automatiquement dans le simulateur iOS lors de l’utilisation de Xcode 6.
> Utilisez le menu **matériel > externe affiche** pour afficher l’écran espion.

<a name="custommodes"></a>

## <a name="launching-notification-mode"></a>Lancement du mode de notification

Pour plus d’informations sur la gestion des notifications dans le code, consultez la [page notifications](~/ios/watchos/platform/notifications.md) .

Visual Studio pour Mac pouvez démarrer l’application Watch avec des _modes de démarrage_ de notification pour les notifications :

Cliquez avec le bouton droit sur le projet d’application Watch et choisissez **exécuter avec > configuration personnalisée...**:

[![Exécution d’une configuration personnalisée](installation-images/runwith-customparams-sml.png)](installation-images/runwith-customparams.png#lightbox)

La fenêtre **paramètres personnalisés** s’ouvre, dans laquelle vous pouvez sélectionner **notification** (et fournir une charge utile JSON), puis appuyer sur **exécuter** pour démarrer l’application espion dans le simulateur :

[![Définition de la notification et de la charge utile](installation-images/runwith-execargs-sml.png)](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>Débogage

Le débogage est pris en charge dans Visual Studio pour Mac et Visual Studio.
N’oubliez pas de fournir un fichier de notification JSON lors du débogage en mode notifications. Cette capture d’écran montre un point d’arrêt de débogage atteint dans une application Watch :

![Cette capture d’écran montre un point d’arrêt de débogage atteint dans une application Watch](installation-images/debug-sml.png)

Après avoir suivi les instructions de lancement, votre application Watch s’exécutera sur le **simulateur iOS (Watch)**.
Pour le mode de notification, vous pouvez sélectionner **Déboguer > ouvrir le journal système** (**cmd +/**) et l’utiliser `Console.WriteLine` dans votre code.

### <a name="debugging-lifecycle-event-handlers"></a>Débogage des gestionnaires d’événements de cycle de vie

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

Les fichiers de modèle Watchos (tels que `InterfaceController` ,, `ExtensionDelegate` `NotificationController` et `ComplicationController` ) sont fournis avec leurs méthodes de cycle de vie requises déjà implémentées. Ajoutez `Console.WriteLine` des appels et lisez la sortie de l' **application** pour mieux comprendre le cycle de vie de l’événement.

## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Vidéo de la première application Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Conseils WatchKit d’Apple](https://developer.apple.com/watchkit/tips/)
