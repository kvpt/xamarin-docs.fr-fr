---
title: Cycle de vie des applications Xamarin.Forms
description: Cet article explique comment répondre au cycle de vie des applications, notamment concernant les méthodes de cycle de vie, les événements de notification de page et les événements de navigation modale.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 41e8d073982bf7963b3a77a939bf28e52e86feaa
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "67675176"
---
# <a name="xamarinforms-app-lifecycle"></a>Cycle de vie des applications Xamarin.Forms

La [`Application`](xref:Xamarin.Forms.Application) classe de base fournit les caractéristiques suivantes :

- [Méthodes](#Lifecycle_Methods) `OnStart`de `OnSleep`cycle `OnResume`de vie , , et .
- [Événements de navigation de](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)page , [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
- [Événements](#modal) `ModalPushing`de navigation `ModalPushed` `ModalPopping`modal `ModalPopped`, , , et .

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Méthodes de cycle de vie

La [`Application`](xref:Xamarin.Forms.Application) classe contient trois méthodes virtuelles qui peuvent être remplacées pour répondre aux changements du cycle de vie :

- `OnStart` : appelé au démarrage de l’application.
- `OnSleep` : appelé chaque fois que l’application passe à l’arrière-plan.
- `OnResume` : appelé lors de la reprise de l’application, après son envoi en arrière-plan.

> [!NOTE]
> Il n’existe *aucune* méthode pour arrêter l’application. Dans des circonstances normales (c’est-à-dire, en dehors d’un plantage), l’arrêt de l’application est effectué à partir de l’état *OnSleep*, sans autre notification de votre code.

Pour savoir à quel moment ces méthodes sont appelées, implémentez un appel `WriteLine` dans chacune d’elle (comme indiqué ci-dessous) et testez-les sur chaque plateforme.

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

> [!IMPORTANT]
> Sur Android, la méthode `OnStart` sera appelée sur la rotation tout comme quand l’application démarre pour la première fois, si l’activité principale n’a pas de `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` dans l’attribut `[Activity()]`.

<a name="page" />

## <a name="page-notification-events"></a>Événements de notification de page

Il y a [`Application`](xref:Xamarin.Forms.Application) deux événements sur la classe qui fournissent la notification des pages apparaissant et disparaissant :

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)- surélevé lorsqu’une page est sur le point d’apparaître à l’écran.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)- surélevé lorsqu’une page est sur le point de disparaître de l’écran.

Ces événements peuvent être utilisés lorsque vous souhaitez effectuer le suivi des pages qui s’affichent à l’écran.

> [!NOTE]
> Les [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) événements et les [`Page`](xref:Xamarin.Forms.Page) événements sont [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) soulevés à partir de la classe de base immédiatement après le et [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) les événements, respectivement.

<a name="modal" />

## <a name="modal-navigation-events"></a>Événements de navigation modale

Il ya quatre [`Application`](xref:Xamarin.Forms.Application) événements sur la classe, chacun avec leurs propres arguments événementiel, qui vous permettent de répondre aux pages modales étant montré et rejeté:

- `ModalPushing` : déclenché lors de l’envoi (push) modal d’une page.
- `ModalPushed` : déclenché une fois l’envoi (push) modal d’une page effectué.
- `ModalPopping` : déclenché lorsqu’une page est dépilée de façon modale.
- `ModalPopped` : déclenché une fois qu’une page a été dépilée de façon modale.

> [!NOTE]
> Les arguments d’événement `ModalPopping` de type `ModalPoppingEventArgs` contiennent une propriété `Cancel`. Lorsque `Cancel` est défini sur `true`, la fenêtre contextuelle modale est annulée.
