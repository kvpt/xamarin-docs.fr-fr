---
title: Utilisation des tables et des cellules dans Xamarin. iOS
description: Ce document contient des liens vers divers guides qui décrivent comment afficher des données avec le contrôle UITableView dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/06/2016
ms.openlocfilehash: 0dd3830b4903658d0b014eceaad9c57859963c04
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528630"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Utilisation des tables et des cellules dans Xamarin. iOS

Cette section présente les classes utilisées pour créer et afficher des tables, puis fournit des exemples de leur utilisation dans Xamarin. iOS. Il aborde l’utilisation de l’apparence par défaut pour les tables, la personnalisation de la disposition, l’implémentation de la modification et l’utilisation du concepteur iOS Xamarin pour concevoir une table visuellement. Parfois, l’affichage est évidemment une liste de lignes (par exemple, l’application musique) et, dans d’autres cas, il est difficile de reconnaître le contrôle de table (par exemple, modification dans l’application contacts ou une conversation dans l’application messages).

Pour ceux qui travaillent sur des applications multiplateformes avec Xamarin. Android, le contrôle UITableView est semblable à la classe ListView dans Android (et la classe UITableViewSource est semblable aux classes d’adaptateur Android).

Ces articles présentent un aperçu complet de l’utilisation des tables, notamment:

- **Parties de table** : présentation et explication des éléments visuels du `UITableView` contrôle. 
- **Affichage des données dans les tables** : l’illustration de la création et du remplissage d’une table, l’utilisation de différents styles de table et de cellule et l’évitement des problèmes de mémoire en recyclant des objets Cell. 
- **Utilisation avancée** : génération de cellules personnalisées et utilisation des fonctionnalités de modification de la classe UITableView. 
- **Création visuelle d’une table** : à l’aide de l’Xamarin Designer pour IOS pour créer une interface pilotée par table avec une table de montage séquentiel. 

## <a name="contents"></a>Sommaire

 [Fonctionnalités des &amp; composants de la table](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Remplissage d’un tableau avec des données](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personnalisation de l’apparence d’un tableau](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Modification](~/ios/user-interface/controls/tables/editing.md)
 
 [Actions de ligne](~/ios/user-interface/controls/tables/row-action.md)

 [Création de tables dans une table de montage séquentiel](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Redimensionnement automatique de la hauteur de ligne](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Liens associés

- [WorkingWithTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
- [Tables dans les storyboards (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Introduction aux storyboards](~/ios/user-interface/storyboards/index.md)
- [Storyboard a TableView Recipe](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Présentation de la boîte de dialogue de monopression.](~/ios/user-interface/monotouch.dialog/index.md)
- [Exemple TableEditing sur GitHub](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Exemple TableParts sur GitHub](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Exemple TableAndCellStyles sur GitHub](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Référence de la classe UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Référence de la classe UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
