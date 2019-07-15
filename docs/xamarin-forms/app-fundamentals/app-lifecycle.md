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
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675176"
---
# <a name="xamarinforms-app-lifecycle"></a>Cycle de vie des applications Xamarin.Forms

La classe de base [`Application`](xref:Xamarin.Forms.Application) offre les fonctionnalités suivantes :

- [Méthodes de cycle de vie](#Lifecycle_Methods) `OnStart`, `OnSleep` et `OnResume`.
- [Événements de navigation de page](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing), [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
- [Événements de navigation modale](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping` et `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Méthodes de cycle de vie

La classe [`Application`](xref:Xamarin.Forms.Application) contient trois méthodes virtuelles qui peuvent être substituées pour répondre aux changements liés au cycle de vie :

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

Dans la classe [`Application`](xref:Xamarin.Forms.Application), deux événements fournissent une notification d’apparition et de disparition de pages :

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) : déclenché lorsqu’une page est sur le point de s’afficher à l’écran.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) : déclenché lorsqu’une page est sur le point de disparaître de l’écran.

Ces événements peuvent être utilisés lorsque vous souhaitez effectuer le suivi des pages qui s’affichent à l’écran.

> [!NOTE]
> Les événements [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) et [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) sont déclenchés à partir de la classe de base [`Page`](xref:Xamarin.Forms.Page) immédiatement après les événements [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) et [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing), respectivement.

<a name="modal" />

## <a name="modal-navigation-events"></a>Événements de navigation modale

La classe [`Application`](xref:Xamarin.Forms.Application) comprend quatre événements qui ont chacun leurs propres arguments d’événement, et qui vous permettent de répondre aux pages modales affichées et ignorées :

- `ModalPushing` : déclenché lors de l’envoi (push) modal d’une page.
- `ModalPushed` : déclenché une fois l’envoi (push) modal d’une page effectué.
- `ModalPopping` : déclenché lorsqu’une page est dépilée de façon modale.
- `ModalPopped` : déclenché une fois qu’une page a été dépilée de façon modale.

> [!NOTE]
> Les arguments d’événement `ModalPopping` de type `ModalPoppingEventArgs` contiennent une propriété `Cancel`. Lorsque `Cancel` est défini sur `true`, la fenêtre contextuelle modale est annulée.
