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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "66835264"
---
# <a name="xamarinforms-navigation"></a>Navigation Xamarin.Forms

_Xamarin.Forms fournit un certain nombre d’expériences de navigation de page différentes, selon le type De page utilisé._

![](images/page-types.png "Xamarin.Forms Page Types")

Alternativement, les applications Shell Xamarin.Forms utilisent une expérience de navigation basée sur URI qui n’applique pas de hiérarchie de navigation définie. Pour plus d’informations, consultez [Navigation Shell Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigation"></a>[Navigation hiérarchique](hierarchical.md)

La [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe offre une expérience de navigation hiérarchique où l’utilisateur est capable de naviguer à travers les pages, en avant et en arrière, comme vous le souhaitez. La classe met en œuvre la navigation comme une [`Page`](xref:Xamarin.Forms.Page) dernière pile d’objets (LIFO).

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

Le Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se compose d’une liste d’onglets et d’une zone de détail plus grande, avec chaque onglet de chargement du contenu dans la zone de détail.

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

Le Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est une page que les utilisateurs peuvent glisser d’un côté à l’autre pour naviguer à travers les pages de contenu, comme une galerie.

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

Le Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) est une page qui gère deux pages d’informations connexes - une page maîtresse qui présente des éléments, et une page de détails qui présente des détails sur les éléments sur la page principale.

## <a name="modal-pages"></a>[Pages modales](modal.md)

Xamarin.Forms prend aussi en charge les pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée.
