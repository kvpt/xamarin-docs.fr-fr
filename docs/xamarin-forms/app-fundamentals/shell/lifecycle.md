---
title: Cycle de vie Shell Xamarin.Forms
description: Les applications Shell respectent le cycle de vie Xamarin.Forms. Un événement Appearing est déclenché lorsqu’une page est sur le point d'apparaître sur l’écran, tandis qu’un événement Disappearing est déclenché lorsqu’une page est sur le point de disparaître de l’écran.
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
ms.openlocfilehash: 2ed51763b5866c15e91d88a6a1a58c7285fb5973
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749766"
---
# <a name="xamarinforms-shell-lifecycle"></a>Cycle de vie Shell Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Les applications Shell respectent le cycle de vie Xamarin.Forms. Un événement `Appearing` est déclenché lorsqu’une page est sur le point d'apparaître sur l’écran, tandis qu’un événement `Disappearing` est déclenché lorsqu’une page est sur le point de disparaître de l’écran. Ces événements sont propagés aux pages et peuvent être gérés en remplaçant les méthodes [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ou [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) sur la page.

> [!NOTE]
> Dans une application Shell, les événements `Appearing` et `Disappearing` sont déclenchés à partir du code multiplateforme, avant que le code de plateforme rende une page visible ou supprime une page de l’écran.

Pour plus d’informations sur le cycle de vie des applications Xamarin.Forms, consultez la page [Cycle de vie des applications Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navigation hiérarchique

Dans une application Shell, l’envoi (push) d’une page sur la pile de navigation entraîne que l’objet `ShellContent` actuellement visible, et le contenu de sa page, déclenchent l’événement `Disappearing`. De même, l’affichage de la dernière page depuis la pile de navigation entraîne que l’objet `ShellContent` nouvellement visible, et le contenu de sa page, déclenchent l’événement `Appearing`.

Pour plus d’informations sur la navigation hiérarchique, consultez [Navigation hiérarchique Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navigation modale

Dans une application Shell, l’envoi d’une page modale sur la pile de navigation modale entraîne que tous les objets Shell visibles déclenchent l’événement `Disappearing`. De même, l’affichage de la dernière page modale depuis la pile de navigation modale entraîne que tous les objets Shell visibles déclenchent l’événement `Appearing`.

Pour plus d’informations sur la navigation modale, consultez [Pages modales Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Cycle de vie des applications Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Pages modales Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md)
