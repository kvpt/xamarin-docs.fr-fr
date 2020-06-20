---
title: Xamarin.FormsCycle de vie des applications
description: Cet article explique comment répondre au cycle de vie des applications, notamment concernant les méthodes de cycle de vie, les événements de notification de page et les événements de navigation modale.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a67d0c3adb54332bf30879a5b6f1d086581f0ec
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573337"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.FormsCycle de vie des applications

La [`Application`](xref:Xamarin.Forms.Application) classe de base fournit les fonctionnalités suivantes :

- [Méthodes de cycle](#lifecycle-methods) `OnStart` de vie, `OnSleep` et `OnResume` .
- [Événements de navigation entre les pages](#page-navigation-events) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) , [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) .
- [Événements de navigation modaux](#modal-navigation-events) `ModalPushing` ,, `ModalPushed` `ModalPopping` et `ModalPopped` .

## <a name="lifecycle-methods"></a>Méthodes de cycle de vie

La [`Application`](xref:Xamarin.Forms.Application) classe contient trois méthodes virtuelles qui peuvent être substituées pour répondre aux modifications du cycle de vie :

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

## <a name="page-navigation-events"></a>Événements de navigation entre les pages

Il existe deux événements sur la [`Application`](xref:Xamarin.Forms.Application) classe qui fournissent la notification des pages qui apparaissent et disparaissent :

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)-déclenché quand une page est sur le présent visible à l’écran.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)-déclenché quand une page est sur le côté de disparaître de l’écran.

Ces événements peuvent être utilisés lorsque vous souhaitez effectuer le suivi des pages qui s’affichent à l’écran.

> [!NOTE]
> Les [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) événements et sont déclenchés à partir de la [`Page`](xref:Xamarin.Forms.Page) classe de base immédiatement après les [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) événements et, respectivement.

## <a name="modal-navigation-events"></a>Événements de navigation modale

Il y a quatre événements sur la [`Application`](xref:Xamarin.Forms.Application) classe, chacun avec ses propres arguments d’événement, qui vous permettent de répondre aux pages modales qui sont affichées et ignorées :

- `ModalPushing` : déclenché lors de l’envoi (push) modal d’une page.
- `ModalPushed` : déclenché une fois l’envoi (push) modal d’une page effectué.
- `ModalPopping` : déclenché lorsqu’une page est dépilée de façon modale.
- `ModalPopped` : déclenché une fois qu’une page a été dépilée de façon modale.

> [!NOTE]
> Les arguments d’événement `ModalPopping` de type `ModalPoppingEventArgs` contiennent une propriété `Cancel`. Lorsque `Cancel` est défini sur `true`, la fenêtre contextuelle modale est annulée.
