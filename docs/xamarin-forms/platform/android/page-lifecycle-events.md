---
title: Événements du cycle de vie d’une page sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui désactive les événements de page discontinues et apparaissant sur la suspension et la reprise de l’application, respectivement.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76724ff17613fcebe35cb68518a1c932eee8aad7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128724"
---
# <a name="page-lifecycle-events-on-android"></a>Événements du cycle de vie d’une page sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour désactiver [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) les [`Appearing`](xref:Xamarin.Forms.Page.Appearing) événements de page et de mise en pause de l’application, respectivement, pour les applications qui utilisent AppCompat. En outre, il offre la possibilité de contrôler si le clavier logiciel est affiché à la reprise, s’il a été affiché lors de la pause, à condition que le mode d’opération du clavier logiciel soit défini sur [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

> [!NOTE]
> Notez que ces événements sont activés par défaut pour conserver le comportement existant pour les applications qui reposent sur les événements. La désactivation de ces événements fait que le cycle d’événement AppCompat correspond au cycle d’événement de pré-AppCompat.

Ce spécifique à la plateforme peut être consommé en XAML en affectant [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty) [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty) [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) aux propriétés, et jointes les `boolean` valeurs suivantes :

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

La `Application.Current.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `Application.SendDisappearingEventOnPause` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. application. SendDisappearingEventOnPause ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espace de noms, est utilisé pour activer ou désactiver le déclenchement de l' [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) événement de page, lorsque l’application entre l’arrière-plan. [ `Application.SendAppearingEventOnResume` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. application. SendAppearingEventOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)) est utilisé pour activer ou désactiver le déclenchement de l' [`Appearing`](xref:Xamarin.Forms.Page.Appearing) événement de page lorsque l’application reprend à partir de l’arrière-plan. [ `Application.ShouldPreserveKeyboardOnResume` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. application. ShouldPreserveKeyboardOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)) est utilisé pour contrôler si le clavier logiciel est affiché à la reprise, s’il a été affiché lors de la pause, à condition que le mode d’opération du clavier logiciel soit défini sur [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

Le résultat est que les [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) événements de page et ne seront pas déclenchés à la pause et à la reprise de l’application, et que si le clavier logiciel était affiché lorsque l’application a été suspendue, elle sera également affichée au moment de la reprise de l’application :

[![](page-lifecycle-events-images/keyboard-on-resume.png "Lifecycle Events Platform-Specific")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "Lifecycle Events Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
