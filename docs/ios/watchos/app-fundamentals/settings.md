---
title: Utilisation de paramètres dans Xamarin de watchOS
description: Ce document décrit comment utiliser des paramètres de watchOS dans Xamarin. Il aborde des paramètres ajout à une solution d’application espion, à l’aide de ces paramètres dans l’application et l’application Apple Watch sur l’iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a8fe2c2765676db52c23fd7c475f218f14697caf
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675230"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Utilisation de paramètres dans Xamarin de watchOS

Les applications Apple Watch peuvent utiliser les mêmes fonctionnalités de paramètres comme des applications iOS : l’interface utilisateur de paramètres s’affiche dans le **Apple Watch** application iPhone, mais les valeurs sont accessibles dans votre application iPhone et également l’extension watch.

![](settings-images/intro.png "Les applications Apple Watch peuvent utiliser les mêmes fonctionnalités de paramètres comme des applications iOS")

Les paramètres seront stockées dans un emplacement de fichier partagé qui est accessible à l’application iOS et l’extension d’application watch, défini par un **App Group**. Vous devez [configurer un groupe de l’application](~/ios/watchos/app-fundamentals/app-groups.md) avant d’ajouter les paramètres en suivant les instructions ci-dessous.

## <a name="add-settings-in-a-watch-solution"></a>Ajouter des paramètres dans une Solution de surveillance

Dans le **application iPhone** dans votre solution (*pas* l’extension ou une application watch) :

1. Avec le bouton droit **Ajouter > nouveau fichier...**  et choisissez **Settings.bundle** (vous ne pouvez pas modifier le nom dans la **nouveau fichier** boîte de dialogue) :

   [![](settings-images/settings-add-sml.png "Ajouter un nouveau groupe de paramètres")](settings-images/settings-add.png#lightbox)

2. Remplacez le nom par **paramètres-Watch.bundle** (sélectionnez et tapez **commande + R** renommer) :

   ![](settings-images/settings-rename.png "Renommer le groupe")

3. Ajoutez une nouvelle clé `ApplicationGroupContainerIdentifier` à la **Root.plist** avec la valeur définie pour le groupe d’application que vous avez configuré, (par ex. `group.com.xamarin.WatchSettings` dans l’exemple) :

   [![](settings-images/settings-appgroup-sml.png "Ajouter une clé ApplicationGroupContainerIdentifier le fichier root.plist")](settings-images/settings-appgroup.png#lightbox)

4. Modifier le **Settings-Watch.bundle/Root.plist** pour contenir les options que vous souhaitez utiliser - le fichier de modèle contient un groupe.
  champ de texte, bouton bascule et curseur par défaut (ce qui vous pouvez supprimer et remplacer par vos propres paramètres) :

  [![](settings-images/rootplist-sml.png "Modifier le Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Utiliser les paramètres dans l’application Apple Watch

Pour accéder aux valeurs sélectionnées par l’utilisateur, créer un `NSUserDefaults` à l’aide de l’app group et en spécifiant l’instance `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Application d’Apple Watch

[![](settings-images/settings-app-sml.png "La nouvelle application Apple Watch sur l’iPhone")](settings-images/settings-app.png#lightbox)

Les utilisateurs interagissent avec les paramètres via le nouveau **Apple Watch** application sur leur iPhone. Cette application permet à l’utilisateur à afficher/masquer les applications sur l’espion et modifier les paramètres exposés à l’aide de la **paramètres-Watch.bundle**.

![](settings-images/applewatch-1.png "Exemple de paramètres d’application") ![](settings-images/applewatch-2.png "exemple de paramètres d’application")



## <a name="related-links"></a>Liens associés

- [Document de paramètres d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
