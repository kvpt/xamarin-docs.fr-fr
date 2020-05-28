---
title: Xamarin.FormsCycle de vie du shell
description: Les applications de Shell respectent le Xamarin.Forms cycle de vie et un événement apparaît lorsqu’une page est sur le point d’apparaître sur l’écran, et un événement de disparition est déclenché quand une page est sur le point de disparaître de l’écran.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3a7a46187d861098b61f638a3fb460d890b081dd
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138721"
---
# <a name="xamarinforms-shell-lifecycle"></a>Xamarin.FormsCycle de vie du shell

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Les applications de Shell respectent le Xamarin.Forms cycle de vie et un `Appearing` événement est déclenché lorsqu’une page est sur le point d’apparaître sur l’écran, et un `Disappearing` événement est déclenché lorsqu’une page est sur le point de disparaître de l’écran. Ces événements sont propagés aux pages et peuvent être gérés en remplaçant les [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) méthodes ou sur la page.

> [!NOTE]
> Dans une application Shell, les événements `Appearing` et `Disappearing` sont déclenchés à partir du code multiplateforme, avant que le code de plateforme rende une page visible ou supprime une page de l’écran.

Pour plus d’informations sur le Xamarin.Forms cycle de vie des applications, consultez [ Xamarin.Forms cycle de vie des applications](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navigation hiérarchique

Dans une application Shell, l’envoi (push) d’une page sur la pile de navigation entraîne que l’objet `ShellContent` actuellement visible, et le contenu de sa page, déclenchent l’événement `Disappearing`. De même, l’affichage de la dernière page depuis la pile de navigation entraîne que l’objet `ShellContent` nouvellement visible, et le contenu de sa page, déclenchent l’événement `Appearing`.

Pour plus d’informations sur la navigation hiérarchique, consultez [ Xamarin.Forms navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navigation modale

Dans une application Shell, l’envoi d’une page modale sur la pile de navigation modale entraîne que tous les objets Shell visibles déclenchent l’événement `Disappearing`. De même, l’affichage de la dernière page modale depuis la pile de navigation modale entraîne que tous les objets Shell visibles déclenchent l’événement `Appearing`.

Pour plus d’informations sur la navigation modale, consultez [ Xamarin.Forms pages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsCycle de vie des applications](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Xamarin.FormsPages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
