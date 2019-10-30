---
title: Utilisation des paramètres Watchos dans Xamarin
description: Ce document explique comment utiliser les paramètres Watchos dans Xamarin. Il aborde l’ajout de paramètres à une solution d’application Watch, l’utilisation de ces paramètres dans l’application et l’application Apple Watch sur l’iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1a59979b164c5200a96343caa1a44e05992763d0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028384"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Utilisation des paramètres Watchos dans Xamarin

Les applications Apple Watch peuvent utiliser les mêmes fonctionnalités de paramètres que les applications iOS : l’interface utilisateur des paramètres s’affiche dans l’application **Apple Watch** iPhone, mais les valeurs sont accessibles à la fois dans votre application iPhone et également dans l’extension Watch.

![](settings-images/intro.png "Apple Watch apps can use the same Settings functionality as iOS apps")

Les paramètres sont stockés dans un emplacement de fichier partagé qui est accessible à la fois à l’application iOS et à l’extension de l’application espion, définie par un **groupe d’applications**. Vous devez [configurer un groupe d’applications avant d'](~/ios/watchos/app-fundamentals/app-groups.md) ajouter les paramètres à l’aide des instructions ci-dessous.

## <a name="add-settings-in-a-watch-solution"></a>Ajouter des paramètres dans une solution Watch

Dans l' **application iPhone** de votre solution (*pas* l’application ou l’extension Watch) :

1. Cliquez avec le bouton droit sur **ajouter > nouveau fichier...** , puis choisissez **paramètres. Bundle** (vous ne pouvez pas modifier le nom dans la boîte de dialogue **nouveau fichier** ) :

   [![](settings-images/settings-add-sml.png "Add a new Settings Bundle")](settings-images/settings-add.png#lightbox)

2. Remplacez le nom par **Settings-Watch. Bundle** (sélectionnez et tapez la **commande + R** pour renommer) :

   ![](settings-images/settings-rename.png "Rename the bundle")

3. Ajoutez une nouvelle clé `ApplicationGroupContainerIdentifier` au fichier **root. plist** avec la valeur définie sur le groupe d’applications que vous avez configuré, (par exemple, `group.com.xamarin.WatchSettings` dans l’exemple) :

   [![](settings-images/settings-appgroup-sml.png "Add a ApplicationGroupContainerIdentifier key to the Root.plist")](settings-images/settings-appgroup.png#lightbox)

4. Modifiez **Settings-Watch. Bundle/root. plist** pour qu’il contienne les options que vous souhaitez utiliser : le fichier de modèle contient un groupe.
  TextField, basculer le commutateur et le curseur par défaut (que vous pouvez supprimer et remplacer par vos propres paramètres) :

  [![](settings-images/rootplist-sml.png "Edit the Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)

## <a name="use-settings-in-the-watch-app"></a>Utiliser les paramètres de l’application Watch

Pour accéder aux valeurs sélectionnées par l’utilisateur, créez une instance de `NSUserDefaults` à l’aide du groupe d’applications et spécifiez `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Application Apple Watch

[![](settings-images/settings-app-sml.png "The new Apple Watch app on the iPhone")](settings-images/settings-app.png#lightbox)

Les utilisateurs interagissent avec les paramètres via la nouvelle application **Apple Watch** sur leur iPhone. Cette application permet à l’utilisateur d’afficher ou de masquer des applications sur la montre, ainsi que de modifier les paramètres exposés à l’aide du **paramètre-Watch. Bundle**.

![](settings-images/applewatch-1.png "Exemple de paramètres d’application") ![](settings-images/applewatch-2.png "Exemple de paramètres d’application")

## <a name="related-links"></a>Liens associés

- [Document des paramètres d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
