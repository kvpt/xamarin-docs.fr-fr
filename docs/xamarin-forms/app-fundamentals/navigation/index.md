---
title: Navigation Xamarin.Forms
description: Ce guide explique comment naviguer dans les applications Xamarin.Forms. Xamarin.Forms propose plusieurs expériences différentes de navigation dans les pages, selon le type de page utilisé.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 682e3bd0ac4cdd651203496dd28586db2cef3165
ms.sourcegitcommit: c2bba24233624c2ec0e9ee9827310ca022212a2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66835264"
---
# <a name="xamarinforms-navigation"></a>Navigation Xamarin.Forms

_Xamarin.Forms propose plusieurs expériences différentes de navigation dans les pages, selon le type de page utilisé._

![](images/page-types.png "Types de Page Xamarin.Forms")

Alternativement, les applications Shell Xamarin.Forms utilisent une expérience de navigation basée sur URI qui n’applique pas de hiérarchie de navigation définie. Pour plus d’informations, consultez [Navigation Shell Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navigation hiérarchique](hierarchical.md)

La classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) propose une expérience de navigation hiérarchique où l’utilisateur est en mesure de parcourir les pages, vers l’avant et vers l’arrière, comme il le souhaite. La classe implémente la navigation comme une pile d’objets [`Page`](xref:Xamarin.Forms.Page) LIFO (dernier entré, premier sorti).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) de Xamarin.Forms se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) de Xamarin.Forms est une page que les utilisateurs peuvent balayer pour parcourir les pages de contenu, comme une galerie.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Le [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) de Xamarin.Forms est une page qui gère deux pages d’informations liées (une page maître qui présente les éléments et une page de détails qui présente les détails des éléments de la page maître).

## <a name="modal-pagesmodalmd"></a>[Pages modales](modal.md)

Xamarin.Forms prend aussi en charge les pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée.
