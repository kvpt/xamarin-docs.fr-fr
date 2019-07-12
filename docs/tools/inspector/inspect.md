---
title: Inspection des applications Live
description: Ce document décrit comment utiliser l’inspecteur de Xamarin pour inspecter les applications. Il aborde également les limitations de l’outil Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2def0a01bdd28af5eefb76afc19a0e49fd1df355
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831554"
---
# <a name="inspecting-live-applications"></a>Inspection des applications Live

Inspection de l’application en direct est disponible pour les clients d’entreprise.

1. Ouvrir une [pris en charge du projet d’application](~/tools/inspector/install.md#supported-platforms) dans Visual Studio pour Mac ou Visual Studio.
1. Exécutez votre application en mode débogage.
1. Cliquez sur le **inspecter** bouton dans la barre d’outils de l’IDE (dans Visual Studio, le **application actuelle inspecter...**  élément de menu est également disponible à partir de la **outils** ou **déboguer** menu).

[![](inspect-images/mac-heres-the-button.png "Cliquez sur ce bouton dans la barre d’outils IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Une nouvelle fenêtre du client Xamarin Inspector s’ouvre, avec une nouvelle invite de commandes REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Une nouvelle fenêtre du client Xamarin Inspector s’ouvre, avec une nouvelle invite de commandes REPL")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Une fois cette fenêtre apparaît, vous avez un interactive C# invite que vous pouvez utiliser pour exécuter et évaluer C# instructions et expressions. Ce qui complique uniques, c’est que le code est évalué dans le contexte du processus cible. Dans ce cas, nous expliquons le code en cours d’exécution par rapport à l’application iOS affichée.

Les modifications que vous apportez à l’état de l’application sont produisent réellement sur le processus cible, vous pouvez donc utiliser C# pour modifier l’application en direct, ou vous pouvez inspecter l’état de l’application en direct.

Par exemple, sur iOS, nous pourrions trouver notre classe de délégué UIApplication, qui est notre pilote principal (où nous stockent une grande quantité de l’état de l’application) :

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

(Notez que chaque envoi a lieu dans un éditeur multiligne. `Shift + Enter` Crée une nouvelle ligne, et `Cmd + Enter` (`Ctrl + Enter` sur Windows) envoie le code pour l’évaluation. `Enter` envoie automatiquement lorsqu’il est plus sûr.)

Un moyen plus pratique d’obtenir les éléments visuels de votre application est à l’aide du bouton « Inspecter ». Une fois que vous appuyez sur cela, vous pouvez sélectionner un élément d’interface utilisateur en cliquant sur votre application. La variable `selectedView` sera être affectée pour pointer vers l’élément réel dans l’écran. Dans la capture d’écran ci-dessus, vous pouvez voir comment nous accessibles et modifiées puis `selectedView.BarTintColor` sur la `UISearchBar` nous avions sélectionné.

L’arborescence d’éléments visuels en direct est également très utile. Il représente l’instantané actuel de votre hiérarchie d’affichage. Vous pouvez sélectionner les lignes à définir `selectedView` dans la boucle REPL et pour afficher les valeurs de propriété de la vue. Sur Mac, vous pouvez interagir avec une visualisation éclatée 3D des vues en couches. Sur Windows, vous pouvez modifier les valeurs de propriété de l’affichage visuellement.

## <a name="known-limitations"></a>Limites connues

- Sélection de la vue est uniquement pris en charge sur votre écran d’affichage principal.
- Modification de grille de propriété n’est pas disponible pour Mac et sur Windows est limitée à quelques types de données. Utilisez la boucle REPL de modification plus puissant.
- Tant que la complément/extension Inspector est installée et activée dans votre IDE, nous allons injecter du code dans votre application chaque fois qu’il démarre en mode débogage. Si vous remarquez un comportement étrange dans votre application, veuillez, essayez de désactiver ou désinstaller l’inspecteur addin/extension, le redémarrage de l’IDE et en revérifiant. Et [signaler des bogues](~/tools/inspector/install.md#reporting-bugs) pour nous faire savoir !
- Si l’inspection d’un élément d’interface utilisateur provoque la quand même modifier, veuillez [faites-nous savoir](~/tools/inspector/install.md#reporting-bugs), comme cela peut indiquer un bogue.

