---
title: Événements du cycle de vie d’une page sur Android
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme Android qui désactive les événements de page discontinues et apparaissant sur la suspension et la reprise de l’application, respectivement.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 1745f137f2eeb04c0894c57bb0e45e5c43be7d0b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649935"
---
# <a name="page-lifecycle-events-on-android"></a>Événements du cycle de vie d’une page sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour désactiver [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) les [`Appearing`](xref:Xamarin.Forms.Page.Appearing) événements de page et de mise en pause de l’application, respectivement, pour les applications qui utilisent AppCompat. En outre, il inclut la possibilité de contrôler si le clavier logiciel s’affiche à la reprise, si elle était affichée sur pause, à condition que le mode de fonctionnement du clavier de manière réversible est activée [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

> [!NOTE]
> Notez que ces événements sont activés par défaut pour conserver le comportement existant pour les applications qui s’appuient sur les événements. La désactivation de ces événements rend le cycle des événements AppCompat de correspondre le cycle des événements pré-AppCompat.

Cette plateforme spécifique peut être consommée dans XAML en définissant le [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty), [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty), et [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) à despropriétésjointes`boolean` valeurs :

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

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

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

Le `Application.Current.On<Android>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Android. Le [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espace de noms, est utilisé pour activer ou désactiver le déclenchement de l’événement le [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) les événements de page, lorsque l’application passe à l’arrière-plan. Le [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) méthode est utilisée pour activer ou désactiver le déclenchement de l’événement le [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) événements de page, lorsque l’application reprend à partir de l’arrière-plan. Le [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) sert de méthode contrôle si le clavier logiciel s’affiche à la reprise, si elle était affichée sur pause, fournies que le mode de fonctionnement du clavier de manière réversible a la valeur [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

Le résultat est que le [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) et [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) les événements de page ne sont pas être déclenchés à la suspension de l’application et reprendre respectivement, et que si le clavier logiciel a été affiché lorsque l’application a été suspendu, il est également affichée lorsque l’application reprend :

[![](page-lifecycle-events-images/keyboard-on-resume.png "Cycle de vie des événements spécifiques à la plateforme")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "cycle de vie des événements spécifiques à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
