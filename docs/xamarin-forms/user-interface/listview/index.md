---
title: ListView de Xamarin.Forms
description: Ce guide présente le ListView Xamarin.Forms, qui peut être utilisé pour présenter des données dans les listes de belles et interactives.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: 4aae2a206f07ea6caa7fc0d7530fb9fec05ce5f0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648426"
---
# <a name="xamarinforms-listview"></a>ListView de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)est une vue pour la présentation de listes de données, en particulier les longues listes nécessitant un défilement.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)est une vue pour la présentation de listes de données à l’aide de différentes spécifications de disposition. Il vise à fournir une alternative plus flexible et plus performante à [`ListView`](xref:Xamarin.Forms.ListView). Pour plus d’informations, consultez [Xamarin. Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Cas d’usage

Assurez-vous que ListView est le contrôle approprié pour vos besoins. ListView peut être utilisé dans n’importe quelle situation où vous affichez les listes permettant le défilement de données. ListView prend en charge la liaison de données et des actions contextuelles.

ListView ne doit pas être confondu avec [TableView](~/xamarin-forms/user-interface/tableview.md). Le contrôle TableView constitue une meilleure option chaque fois que vous avez une liste non liées à des options ou des données. Par exemple, l’application de paramètres iOS qui a un ensemble d’options principalement prédéfini, est mieux adaptée à utiliser TableView que ListView.

Notez qu’un ListView est préférable également adapté aux données homogènes &ndash; , autrement dit, toutes les données doivent être du même type. Il s’agit, car le seul type de cellule peut être utilisé pour chaque ligne dans la liste. TableViews peut prendre en charge plusieurs types de cellules, afin qu’ils soient une meilleure option lorsque vous avez besoin de combiner des vues.

## <a name="components"></a>Composants
ListView comportant des composants disponibles pour tester la fonctionnalité native de chaque plateforme. Chacun de ces composants est décrit ci-dessous :

- **[En-têtes et pieds de page](customizing-list-appearance.md#Headers_and_Footers)**  &ndash; texte ou une vue à afficher au début et à la fin d’une liste, séparer les données de la liste. En-têtes et pieds de page peuvent être liés à une source de données indépendamment à partir de la source de données de la ListView.
- **[Groupes](customizing-list-appearance.md#Grouping)**  &ndash; les données dans un ListView peuvent être regroupées pour faciliter la navigation. Les groupes sont généralement liés aux données :

![](images/grouping-depth.png "ListView avec des données regroupées")

- **[Cellules](customizing-cell-appearance.md)**  &ndash; les données dans un ListView sont présentées dans les cellules. Chaque cellule correspond à une ligne de données. Les cellules intégrées à choisir à partir de, ou vous pouvez définir votre propre cellule personnalisée. Cellules intégrées et personnalisées peuvent être utilisé/définis dans XAML ou du code.
  - **[Intégrés](customizing-cell-appearance.md#Built_in_Cells)**  &ndash; intégrées dans des cellules, en particulier TextCell et ImageCell, peut être améliorer sensiblement les performances, car elles correspondent aux contrôles natifs sur chaque plateforme.
       - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash; affiche une chaîne de texte, éventuellement avec le texte de détail. Texte de détail est restitué sous la forme d’une deuxième ligne dans une police plus petite avec une couleur d’accentuation.
       - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash; affiche une image avec du texte. Apparaît sous la forme d’un TextCell avec une image sur la gauche.
  - **[Cellules personnalisés](customizing-cell-appearance.md#customcells)**  &ndash; les cellules personnalisées sont idéales lorsque vous avez besoin présenter des données complexes. Par exemple, une vue personnalisée peut servir à présenter une liste de chansons, y compris l’album et artiste :

![](images/image-cell-default.png "ListView avec ImageCells")

Pour en savoir plus sur la personnalisation des cellules dans un ListView, consultez [personnalisation une apparence de cellule ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Fonctionnalité
ListView prend en charge un nombre de styles d’interaction, y compris :

- **[Extraire pour actualiser](interactivity.md#Pull_to_Refresh)**  &ndash; ListView prend en charge l’actualisation de l’extraction sur chaque plateforme.
- **[Actions contextuelles](interactivity.md#Context_Actions)**  &ndash; ListView prend en charge agir en conséquence sur des éléments individuels dans une liste. Par exemple, vous pouvez implémenter le passage à l’action sur iOS, ou appuyez longuement sur les actions sur Android.
- **[Sélection](interactivity.md#selectiontaps)**  &ndash; vous pouvez écouter les sélections et désélections non contiguës action à effectuer lors de l’appui sur une ligne.

![](images/context-default.png "ListView avec des Actions contextuelles")

Pour en savoir plus sur les fonctionnalités d’interactivité de ListView, consultez [Actions & interactivité avec ListView](interactivity.md).

## <a name="related-links"></a>Liens associés

- [Utilisation avec ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Liaison bidirectionnelle (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Intégrées dans les cellules (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Cellules personnalisés (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Regroupement (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Affichage de convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interactivité de ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
