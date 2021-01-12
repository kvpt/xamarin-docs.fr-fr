---
title: Xamarin.Forms Hiérarchie des classes de contrôles
description: Les développeurs doivent être familiarisés avec la hiérarchie de types utilisée pour créer l’interface utilisateur d’une Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed3fc6d7aab48886f0c6166390b0ff224f66da60
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115181"
---
# <a name="no-locxamarinforms-controls-class-hierarchy"></a>Xamarin.Forms Hiérarchie des classes de contrôles

Xamarin.Forms est constitué de centaines de types, sur plusieurs espaces de noms. Les développeurs doivent être plus familiarisés avec la hiérarchie de types utilisée pour créer l’interface utilisateur d’une Xamarin.Forms application, qui réside dans l' `Xamarin.Forms` espace de noms.

Ces types peuvent être divisés en pages, dispositions, vues et cellules. Une Xamarin.Forms page occupe généralement la totalité de l’écran, et tous les types de pages dérivent de la [`Page`](xref:Xamarin.Forms.Page) classe. Les pages contiennent généralement une disposition, et tous les types de disposition dérivent de la [`Layout`](xref:Xamarin.Forms.Layout) classe. Une disposition contient généralement des vues et éventuellement d’autres dispositions, et tous les types d’affichages dérivent finalement de la [`View`](xref:Xamarin.Forms.View) classe. Enfin, les cellules sont des contrôles spécialisés qui sont utilisés dans les données d’affichage des [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) contrôles et. Les pages, les dispositions, les vues et les cellules sont finalement dérivés de la [`Element`](xref:Xamarin.Forms.Element) classe.

Le diagramme de classes suivant illustre la hiérarchie des types généralement utilisés pour créer une interface utilisateur dans Xamarin.Forms :

[![::: No-Loc (Xamarin. Forms) ::: contrôles diagramme de classes](class-hierarchy-images/class-diagram.png "::: No-Loc (Xamarin. Forms) ::: contrôles diagramme de classes")](class-hierarchy-images/class-diagram-large.png#lightbox "::: No-Loc (Xamarin. Forms) ::: contrôles diagramme de classes")

Toutefois, Notez que le diagramme n’affiche qu’un seul type d’interpréteur de commandes.

> [!NOTE]
> Une version haute résolution du diagramme de classes peut être téléchargée à partir d' [ici](class-hierarchy-images/class-diagram-high-resolution.png).

## <a name="related-links"></a>Liens connexes

- [Xamarin.Forms Référence des contrôles](~/xamarin-forms/user-interface/controls/index.md)
