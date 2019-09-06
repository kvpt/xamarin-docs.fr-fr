---
title: Parties et fonctionnalités de table dans Xamarin. iOS
description: Ce document décrit les différents éléments d’un UITableView dans iOS. Il traite des en-têtes de section, des cellules, des pieds de page de section, de l’index et du mode de modification.
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: edc5ec51499116fc2fabeb03327b32a689dc0c49
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279326"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Parties et fonctionnalités de table dans Xamarin. iOS

Un UITableView peut avoir un style « groupé » ou « clair », et se compose des éléments suivants :

- [En-tête de section](#Section_Header)
- [Cellules](#Cells) (ou lignes, si vous préférez)
- [Pied de page de la section](#Section_Footer)
- [Index](#Index)
- [Mode édition](#Edit_Features) (comprend « balayer pour supprimer » et faire glisser les poignées pour modifier l’ordre des lignes) 

Ces captures d’écran montrent comment les lignes, les en-têtes, les pieds de page, les contrôles d’édition et l’index sont affichés.

 [![](table-parts-and-functionality-images/image1a.png "Ces captures d’écran montrent comment les lignes, les en-têtes, les pieds de page, les contrôles d’édition et l’index sont affichés.")](table-parts-and-functionality-images/image1a.png#lightbox)

Ces éléments sont décrits plus en détail ci-dessous :

<a name="Section_Header" />

## <a name="section-header"></a>En-tête de section

Les cellules peuvent éventuellement être regroupées en sections, étiquetées avec un en-tête personnalisé et/ou étiquetées avec un pied de page. L’en-tête peut être défini avec une valeur de chaîne ou une vue personnalisée peut être fournie pour autoriser une disposition ou un style différent.

<a name="Cells" />

## <a name="cells"></a>Cellules

Les cellules sont l’élément d’interface utilisateur principal d’une table. En cas d’implémentation correcte, les cellules sont réutilisées pour l’efficacité de la mémoire. Il existe quatre styles de cellule intégrés, et vous pouvez créer vos propres cellules personnalisées (dans le code ou dans le concepteur lorsque vous utilisez des storyboards).

<a name="Section_Footer"/>

## <a name="section-footer"></a>Pied de page de la section

Le pied de page de la section facultative peut être défini avec une valeur de chaîne, ou une vue personnalisée peut être fournie pour autoriser une disposition ou un style différent. Les en-têtes et pieds de page de section peuvent être définis indépendamment.

<a name="Index" />

## <a name="index"></a>Index

L’index apparaît sous la forme d’une bande de caractères vers le bas du bord droit de la table.
Le fait de toucher ou de faire glisser sur l’index accélère le défilement jusqu’à cette partie de la table. Un index est facultatif, mais il est recommandé de parcourir les longues listes. Un index n’est généralement pas utilisé avec le style groupé.

<a name="Edit_Features" />

## <a name="editing-mode"></a>Mode édition

Deux fonctions d’édition différentes sont disponibles :

- Balayez pour supprimer des cellules individuelles.
- Entrée en mode édition pour afficher les boutons supprimer sur chaque ligne 
- Entrée en mode édition pour révéler les descripteurs de réorganisation. 
- Insertion de nouvelles cellules (avec animation).

Le reste de ce document montre comment implémenter toutes ces fonctionnalités UITableView avec Xamarin. iOS.


## <a name="classes-overview"></a>Vue d’ensemble des classes

Les classes principales utilisées pour afficher les vues de table sont indiquées ici :

[![](table-parts-and-functionality-images/classdiagram.png "Les classes principales utilisées pour afficher les vues de table sont présentées ici")](table-parts-and-functionality-images/classdiagram.png#lightbox)

L’objectif de chaque classe est décrit ci-dessous :

- **UITableView** : vue qui contient une collection de cellules à l’intérieur d’un conteneur de défilement. La vue table utilise généralement l’écran entier dans une application iPhone, mais peut exister dans le cadre d’une vue plus large sur l’iPad (ou apparaître dans un menu Segue). 
- **UITableViewCell** : vue qui représente une seule cellule (ou ligne) dans une vue de table. Il existe quatre types de cellules intégrés et il est possible de créer des cellules personnalisées dans C# ou avec le concepteur iOS. 
- **UITableViewSource** – Xamarin. iOS : classe abstraite exclusive qui fournit toutes les méthodes requises pour afficher une table, notamment le nombre de lignes, retournant un affichage de cellule pour chaque ligne, gérer la sélection de lignes et de nombreuses autres fonctionnalités facultatives. Vous *devez* la sous-classer pour obtenir un UITableView opérationnel. 
- **NSIndexPath** – contient des propriétés de ligne et de section qui identifient de façon unique la position d’une cellule dans une table. 
- **UITableViewController** : UIViewController prêt à l’emploi qui a un UITableView codé en dur comme vue et accessible via la propriété TableView. 
- **UIViewController** : si la table n’occupe pas la totalité de l’écran, vous pouvez ajouter un UITableView à n’importe quel UIViewController avec son ensemble de frames correctement défini. 

UITableViewSource remplace les deux classes suivantes, qui sont toujours disponibles dans Xamarin. iOS, mais qui ne sont normalement pas nécessaires :

- **UITableViewDataSource** : protocole objective-C qui est modélisé dans Xamarin. IOS en tant que classe abstraite. Doit être sous-classé pour fournir une table avec une vue pour chaque cellule, ainsi que des informations sur les en-têtes, les pieds de page et le nombre de lignes et de sections dans le tableau. 
- **UITableViewDelegate** : protocole objective-C qui est modélisé dans Xamarin. IOS en tant que classe. Gère la sélection, la modification des fonctionnalités et d’autres fonctionnalités de table facultatives. 

Dans ce document, les exemples utilisent tous UITableViewSource et ignorent ces deux classes. Ils sont mentionnés ici, car tous les exemples objective-C trouvés dans la documentation d’Apple les référencent. il est donc utile de comprendre ce qu’ils font (et que vous pouvez utiliser UITableViewSource à la place de Xamarin. iOS).

## <a name="related-links"></a>Liens associés

- [WorkingWithTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
