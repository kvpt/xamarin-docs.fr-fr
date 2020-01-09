---
title: Hiérarchie des classes des contrôles Xamarin. Forms
description: Les développeurs doivent être familiarisés avec la hiérarchie de types utilisée pour créer l’interface utilisateur d’une application Xamarin. Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
ms.openlocfilehash: 7c55ed3082a031352a8eefb8ef579060a9dd6404
ms.sourcegitcommit: 4899cf4aaa73aa56c48f7ee2339c0af8112e1feb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717294"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Hiérarchie des classes des contrôles Xamarin. Forms

Xamarin. Forms est constitué de centaines de types, sur plusieurs espaces de noms. Les développeurs doivent être plus familiarisés avec la hiérarchie de types utilisée pour créer l’interface utilisateur d’une application Xamarin. Forms, qui réside dans l’espace de noms `Xamarin.Forms`.

Ces types peuvent être divisés en pages, dispositions, vues et cellules. Une page Xamarin. Forms occupe généralement la totalité de l’écran, et tous les types de pages dérivent de la classe [`Page`](xref:Xamarin.Forms.Page) . Les pages contiennent généralement une disposition, et tous les types de disposition dérivent de la classe [`Layout`](xref:Xamarin.Forms.Layout) . Une disposition contient généralement des vues et éventuellement d’autres dispositions, et tous les types d’affichages dérivent finalement de la classe [`View`](xref:Xamarin.Forms.View) . Enfin, les cellules sont des contrôles spécialisés qui sont utilisés dans l’affichage des données dans les contrôles [`TableView`](xref:Xamarin.Forms.TableView) et [`ListView`](xref:Xamarin.Forms.ListView) . Les pages, les dispositions, les vues et les cellules sont toutes dérivées de la classe [`Element`](xref:Xamarin.Forms.Element) .

Le diagramme de classes suivant illustre la hiérarchie des types généralement utilisés pour créer une interface utilisateur dans Xamarin. Forms :

[![Diagramme de classes des contrôles Xamarin. Forms](class-hierarchy-images/class-diagram.png "Diagramme de classes des contrôles Xamarin. Forms")](class-hierarchy-images/class-diagram-large.png#lightbox "Diagramme de classes des contrôles Xamarin. Forms")

> [!NOTE]
> Une version haute résolution du diagramme de classes peut être téléchargée à partir d' [ici](class-hierarchy-images/class-diagram-high-resolution.png). Toutefois, Notez que le diagramme n’affiche qu’un seul type d’interpréteur de commandes.

## <a name="related-links"></a>Liens connexes

- [Référence des contrôles Xamarin. Forms](~/xamarin-forms/user-interface/controls/index.md)
