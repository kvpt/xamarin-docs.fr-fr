---
title: Inspection des applications Live
description: Ce document explique comment utiliser le Xamarin Inspector pour inspecter des applications. Il aborde également les limitations de l’outil Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: 2ccf6966e85eddaa10b5651e1b0b48dec9203b28
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772203"
---
# <a name="inspecting-live-applications"></a>Inspection des applications Live

L’inspection des applications en direct est disponible pour les entreprises.

1. Ouvrez un [projet d’application pris en charge](~/tools/inspector/install.md#supported-platforms) dans Visual Studio pour Mac ou Visual Studio.
1. Exécutez votre application en mode débogage.
1. Cliquez sur le bouton **inspecter** dans la barre d’outils de l’IDE (dans Visual Studio, l’élément de menu **inspecter l’application actuelle...** est également disponible dans le menu **Outils** ou **Déboguer** ).

[![](inspect-images/mac-heres-the-button.png "Cliquez sur le bouton inspecter dans la barre d’outils de l’IDE.")](inspect-images/mac-heres-the-button.png#lightbox)

Une nouvelle fenêtre de Xamarin Inspector client s’ouvre, avec une nouvelle invite de réplication.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Une nouvelle fenêtre de Xamarin Inspector client s’ouvre, avec une nouvelle invite de réplication")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Une fois cette fenêtre affichée, vous disposez d' C# une invite interactive que vous pouvez utiliser pour exécuter C# et évaluer des instructions et des expressions. Ce qui en fait un unique est que le code est évalué dans le contexte du processus cible. Dans ce cas, nous affichons le code qui s’exécute sur l’application iOS affichée.

Toutes les modifications apportées à l’état de l’application se produisent en réalité sur le processus cible. vous pouvez C# donc utiliser pour modifier l’application en direct, ou vous pouvez inspecter l’état de l’application en temps réel.

Par exemple, sur iOS, nous pourrions souhaiter trouver notre classe déléguée UIApplication, qui est notre principal pilote (où nous stockons un grand nombre de l’état de l’application) :

```csharp
var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
del.Database.GetAllCustomers ()
...
del.Database.AddCustomer (...)
```

(Notez que chaque envoi se produit dans un éditeur multiligne. `Shift + Enter`crée une nouvelle ligne et `Cmd + Enter` (`Ctrl + Enter` sur Windows) soumet le code pour évaluation. `Enter`envoie automatiquement lorsqu’il est sécurisé.)

Un moyen plus pratique d’accéder aux éléments visuels de votre application consiste à utiliser le bouton inspecter. Une fois que vous avez appuyé sur cette option, vous pouvez sélectionner un élément d’interface utilisateur en cliquant sur votre application. La variable `selectedView` est assignée pour pointer vers l’élément réel de l’écran. Dans la capture d’écran ci-dessus, vous pouvez voir comment nous `selectedView.BarTintColor` avons accédé `UISearchBar` , puis modifié sur le que nous avions sélectionné.

L’arborescence d’éléments visuels dynamique est également très utile. Il représente l’instantané actuel de votre hiérarchie d’affichage. Vous pouvez sélectionner les lignes à `selectedView` définir dans le REPL et afficher les valeurs de propriété de la vue. Sur Mac, vous pouvez interagir avec une visualisation éclatée 3D des vues superposées. Sur Windows, vous pouvez modifier visuellement les valeurs de propriété d’un affichage.

## <a name="known-limitations"></a>Limites connues

- La sélection de la vue est uniquement prise en charge sur votre écran principal.
- La modification de la grille des propriétés n’est pas disponible pour Mac, et sur Windows est limité à quelques types de données. Utilisez REPL pour une modification plus puissante.
- Tant que le complément ou l’extension Inspector est installé et activé dans votre IDE, nous injectons du code dans votre application chaque fois qu’il démarre en mode débogage. Si vous constatez un comportement étrange dans votre application, veuillez essayer de désactiver ou de désinstaller le complément ou l’extension Inspector, de redémarrer l’IDE et de revérifier. Nous vous invitons à nous signaler les [bogues](~/tools/inspector/install.md#reporting-bugs) .
- Si l’inspection d’un élément de l’interface utilisateur entraîne une modification de tout de même, faites-le [nous savoir](~/tools/inspector/install.md#reporting-bugs), car cela peut indiquer un bogue.
