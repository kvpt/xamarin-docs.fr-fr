---
title: Utilisation de la navigation Watchos dans Xamarin
description: Ce document explique comment utiliser la navigation dans une application Watchos. Il traite des interfaces modales, de la navigation hiérarchique et des interfaces basées sur des pages.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: a44d68426ff03ba0b6ab41f57e339caebce62c39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436712"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Utilisation de la navigation Watchos dans Xamarin

L’option de navigation la plus simple disponible sur la montre est un simple [message modal](#modal) qui apparaît en haut de la scène actuelle.

Pour les applications Watch à plusieurs scènes, deux paradigmes de navigation sont disponibles :

- [Navigation hiérarchique](#Hierarchical_Navigation)
- [Interfaces basées sur des pages](#Page-Based_Interfaces)

<a name="modal"></a>

## <a name="modal-interfaces"></a>Interfaces modales

Utilisez la `PresentController` méthode pour ouvrir un contrôleur d’interface de façon modale. Le contrôleur d’interface doit déjà être défini dans l' **interface. Storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Les contrôleurs présentés de façon modale utilisent la totalité de l’écran (qui couvre la scène précédente). Par défaut, le titre est défini sur Annuler et son appui sur le contrôleur est **abandonné** .

Pour fermer par programmation le contrôleur présenté de façon modale, appelez `DismissController` .

```csharp
DismissController();
```

Les écrans modaux peuvent être une seule scène ou utiliser une disposition basée sur une page.

<a name="Hierarchical_Navigation"></a>

## <a name="hierarchical-navigation"></a>Navigation hiérarchique

Présente des scènes comme une pile qui peut être remontée, comme dans le cas de l' `UINavigationController` utilisation d’iOS. Les scènes peuvent faire l’objet d’un push dans la pile de navigation et dépilés (par programmation ou par sélection de l’utilisateur).

![Les scènes peuvent faire l’objet d’un push dans la pile de navigation](navigation-images/hierarchy-1.png) ![Les scènes peuvent être dépilées de la pile de navigation](navigation-images/hierarchy-2.png)

Comme avec iOS, un balayage de bord gauche revient au contrôleur parent dans une pile de navigation hiérarchique.

Les exemples [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) et [WatchTables](/samples/xamarin/ios-samples/watchos-watchtables) incluent la navigation hiérarchique.

### <a name="pushing-and-popping-in-code"></a>Push et dépilé dans le code

Le kit Watch ne nécessite pas la création d’un « contrôleur de navigation » trop arching comme iOS. il effectue simplement un push d’un contrôleur à l’aide de la `PushController` méthode et une pile de navigation est créée automatiquement.

```csharp
PushController("secondPageController","some context info");
```

L’écran de l’espion inclut un bouton **précédent** dans l’angle supérieur gauche, mais vous pouvez également supprimer par programmation une scène de la pile de navigation à l’aide de `PopController` .

```csharp
PopController();
```

Comme avec iOS, il est également possible de revenir à la racine de la pile de navigation à l’aide de `PopToRootController` .

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Utilisation de SEGUES

SEGUES peut être créé entre des scènes dans le Storyboard pour définir la navigation hiérarchique. Pour obtenir le contexte de la scène cible, le système d’exploitation appelle `GetContextForSegue` pour initialiser le nouveau contrôleur d’interface.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

<a name="Page-Based_Interfaces"></a>

## <a name="page-based-interfaces"></a>Interfaces basées sur des pages

Les interfaces basées sur des pages sont balayées de gauche à droite, comme dans le cas de l' `UIPageViewController` utilisation d’iOS. Les points d’indicateur s’affichent le long du bord inférieur de l’écran pour indiquer la page actuellement affichée.

![Exemple de première page](navigation-images/paged-1.png) ![Exemple de deuxième page](navigation-images/paged-2.png) ![Exemple de cinquième page](navigation-images/paged-5.png)

Pour faire d’une interface basée sur une page l’interface utilisateur principale de votre application Watch, utilisez `ReloadRootControllers` avec un tableau de contextes et de contrôleurs d’interface :

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

Vous pouvez également présenter un contrôleur basé sur une page qui n’est pas la racine à l’aide `PresentController` de l’une des autres scènes d’une application.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```

## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (exemple)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)