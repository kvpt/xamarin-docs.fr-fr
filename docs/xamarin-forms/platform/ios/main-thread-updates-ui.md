---
title: Mises à jour du contrôle de thread principal sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui permet d’effectuer le contrôle de disposition et de rendu des mises à jour sur le thread principal.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 404e84e9da1df8a44fa176a17f2407e314ae7968
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368312"
---
# <a name="main-thread-control-updates-on-ios"></a>Mises à jour du contrôle de thread principal sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS permet l’exécution des mises à jour de la disposition du contrôle et du rendu sur le thread principal, au lieu d’être exécuté sur un thread d’arrière-plan. Elle doit être rarement nécessaire, mais dans certains cas peut empêcher des blocages. Son consommé en XAML en affectant `Application.HandleControlUpdatesOnMainThread` à la propriété pouvant être liée la valeur `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

La `Application.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Application.SetHandleControlUpdatesOnMainThread` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si la disposition des contrôles et les mises à jour de rendu sont effectuées sur le thread principal, au lieu d’être exécutées sur un thread d’arrière-plan. En outre, la `Application.GetHandleControlUpdatesOnMainThread` méthode peut être utilisée pour retourner si la disposition du contrôle et les mises à jour du rendu sont exécutées sur le thread principal.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)