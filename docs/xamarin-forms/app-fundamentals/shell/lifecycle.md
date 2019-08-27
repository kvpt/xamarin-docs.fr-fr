---
title: Cycle de vie Shell Xamarin.Forms
description: Les applications Shell respectent le cycle de vie Xamarin.Forms. Un événement Appearing est déclenché lorsqu’une page est sur le point d'apparaître sur l’écran, tandis qu’un événement Disappearing est déclenché lorsqu’une page est sur le point de disparaître de l’écran.
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
ms.openlocfilehash: c008cc29d2ceae073459766597040af653329d71
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69893955"
---
# <a name="xamarinforms-shell-lifecycle"></a>Cycle de vie Shell Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

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

- [Xaminals (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Cycle de vie des applications Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Pages modales Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md)
