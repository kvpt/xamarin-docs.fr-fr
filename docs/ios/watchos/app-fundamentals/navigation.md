---
title: Utilisation de watchOS Navigation dans Xamarin
description: Ce document décrit comment travailler avec la navigation dans une application watchOS. Il aborde les interfaces modales, la navigation hiérarchique et interfaces basées sur la page.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 264bdcc9a7a0d73ebe3385246ee6dfae3d9a850f
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740995"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Utilisation de watchOS Navigation dans Xamarin

L’option navigation le plus simple disponible sur l’observation est une simple [contextuelle modale](#modal) qui apparaît au-dessus de la scène actuelle.

Pour les applications de scène multi espion il sont deux paradigmes de navigation disponibles :

- [Navigation hiérarchique](#Hierarchical_Navigation)
- [Interfaces basées sur la page](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfaces modales

Utilisez le `PresentController` méthode pour ouvrir un contrôleur d’interface de façon modale. Le contrôleur d’interface doit déjà être défini dans le **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Contrôleurs présentées de façon modale utilisent tout l’écran (couvrant la scène précédente). Par défaut, le titre est défini **Annuler** et en appuyant dessus fait disparaître le contrôleur.

Pour fermer par programmation le contrôleur présentées de façon modale, appelez `DismissController`.

```csharp
DismissController();
```

Écrans modales peuvent être une scène unique ou utiliser une disposition basée sur des pages.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navigation hiérarchique

Présente les scènes comme une pile qui peut être navigué via, similaire à la façon dont `UINavigationController` fonctionne sur iOS. Arrière-plan peut être placé sur la pile de navigation et dépilés (par programmation ou par la sélection de l’utilisateur).

![](navigation-images/hierarchy-1.png "En arrière-plan peut être placé sur la pile de navigation") ![](navigation-images/hierarchy-2.png "en arrière-plan peut être dépilée navigation")

Comme avec iOS, un balayage de bord de gauche navigue vers le contrôleur parent dans une pile de navigation hiérarchique.

Les deux le [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) et [WatchTables](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) exemples incluent la navigation hiérarchique.

### <a name="pushing-and-popping-in-code"></a>Poussant et en dépilant dans le Code

Regardez Kit ne nécessite pas un ailleurs « contrôleur de navigation » doit être créé comme le fait d’iOS - simplement transmettre un contrôleur à l’aide de la `PushController` (méthode) et une pile de navigation seront créés automatiquement.

```csharp
PushController("secondPageController","some context info");
```

Écran de la surveillance inclura un **retour** bouton dans le coin supérieur gauche, mais vous pouvez supprimer par programme une scène à partir de la pile de navigation avec `PopController`.

```csharp
PopController();
```

Comme avec iOS, il est également possible de revenir à la racine de la pile de navigation avec `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>À l’aide de Segues

Enchaînements peuvent être créés entre les scènes dans la table de montage séquentiel pour définir la navigation hiérarchique. Pour obtenir le contexte de la scène cible, les appels de système d’exploitation `GetContextForSegue` pour initialiser le nouveau contrôleur d’interface.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```
<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>Interfaces basées sur la page

Interfaces basées sur la page balayez vers la gauche à droite, similaire à la façon dont `UIPageViewController` fonctionne sur iOS. Points d’indicateur sont affichés dans la partie inférieure de l’écran pour afficher la page actuellement affichée.

![](navigation-images/paged-1.png "Exemple de première page") ![](navigation-images/paged-2.png "deuxième exemple de page") ![](navigation-images/paged-5.png "cinquième page d’exemples")


Pour rendre une interface basée sur la page de l’interface utilisateur principale pour votre application Apple watch, utilisez `ReloadRootControllers` avec un tableau de contrôleurs d’interface et les contextes :

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

Vous pouvez également présenter un contrôleur de page qui n’est pas la racine à l’aide de `PresentController` parmi les autres scènes dans une application.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (exemple)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
