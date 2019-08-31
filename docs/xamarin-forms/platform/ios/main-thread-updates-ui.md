---
title: Mises à jour du contrôle de thread principal sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui permet d’effectuer le contrôle de disposition et de rendu des mises à jour sur le thread principal.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: d55ef4a97d5d4f320bf152ba05c86aff82eb2f1e
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200115"
---
# <a name="main-thread-control-updates-on-ios"></a>Mises à jour du contrôle de thread principal sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS permet l’exécution des mises à jour de la disposition du contrôle et du rendu sur le thread principal, au lieu d’être exécuté sur un thread d’arrière-plan. Il doit être rarement nécessaire, mais dans certains cas peut empêcher les blocages. Son consommées dans XAML en définissant le `Application.HandleControlUpdatesOnMainThread` propriété pouvant être liée `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

Le `Application.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `Application.SetHandleControlUpdatesOnMainThread` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisé pour contrôler si la disposition des contrôles et le rendu des mises à jour sont effectuées sur le thread principal, au lieu d’en cours d’exécution sur un thread d’arrière-plan. En outre, le `Application.GetHandleControlUpdatesOnMainThread` méthode peut être utilisée pour retourner si la disposition des contrôles et le rendu des mises à jour sont effectuées sur le thread principal.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
