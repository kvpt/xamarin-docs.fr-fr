---
title: Hiérarchie des classes des contrôles Xamarin. Forms
description: Les développeurs doivent être familiarisés avec la hiérarchie de types utilisée pour créer l’interface utilisateur d’une application Xamarin. Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2019
ms.openlocfilehash: f08146d4439ff1fc22edea71ab1cbb337f64c037
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68984391"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Hiérarchie des classes des contrôles Xamarin. Forms

Xamarin. Forms est constitué de centaines de types, sur plusieurs espaces de noms. Les développeurs doivent se familiariser avec la hiérarchie des types utilisée pour créer l’interface utilisateur d’une application Xamarin. Forms, qui réside dans `Xamarin.Forms` l’espace de noms.

Ces types peuvent être divisés en pages, dispositions, vues et cellules. Une page Xamarin. Forms occupe généralement la totalité de l’écran, et tous les types de [`Page`](xref:Xamarin.Forms.Page) pages dérivent de la classe. Les pages contiennent généralement une disposition, et tous les types de disposition dérivent de la [`Layout`](xref:Xamarin.Forms.Layout) classe. Une disposition contient généralement des vues et éventuellement d’autres dispositions, et tous les types d’affichages [`View`](xref:Xamarin.Forms.View) dérivent de la classe. Enfin, les cellules sont des contrôles spécialisés qui sont utilisés dans les [`TableView`](xref:Xamarin.Forms.TableView) données d’affichage des contrôles et. [`ListView`](xref:Xamarin.Forms.ListView) Les pages, les dispositions, les vues et les cellules sont finalement dérivés [`Element`](xref:Xamarin.Forms.Element) de la classe.

Le diagramme de classes suivant illustre la hiérarchie des types généralement utilisés pour créer une interface utilisateur dans Xamarin. Forms:

Diagramme de classes des contrôles [(class-hierarchy-images/class-diagram.png "Xamarin. Forms") de la ![classe Xamarin. Forms]] (class-hierarchy-images/class-diagram-large.png#lightbox "Diagramme de classes des contrôles Xamarin. Forms")

> [!NOTE]
> Une version haute résolution du diagramme de classes peut être téléchargée à partir d' [ici](class-hierarchy-images/class-diagram-high-resolution.png). Toutefois, Notez que le diagramme n’affiche pas les types `CarouselView` et `CollectionView` pour le moment. Celles-ci sont ajoutées une fois que les contrôles ne sont plus en préversion. En outre, le diagramme n’affiche qu’un seul type d’interpréteur de commandes.

## <a name="related-links"></a>Liens connexes

- [Référence des contrôles Xamarin. Forms](~/xamarin-forms/user-interface/controls/index.md)
