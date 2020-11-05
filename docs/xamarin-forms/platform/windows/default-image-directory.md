---
title: Répertoire d’images par défaut sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le propre à la plateforme Windows qui définit le répertoire du projet à partir duquel les ressources d’images seront chargées.
ms.prod: xamarin
ms.assetid: 537A032B-74DD-4D43-864E-7D7113286D0D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4dd8655d4cd84f7d4214801e55677706eb67efa7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374678"
---
# <a name="default-image-directory-on-windows"></a>Répertoire d’images par défaut sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme définit le répertoire du projet à partir duquel les ressources d’images seront chargées. Il est consommé en XAML en définissant `Application.ImageDirectory` sur un `string` qui représente le répertoire du projet qui contient les ressources de l’image :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

La `Application.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. La `Application.SetImageDirectory` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisée pour spécifier le répertoire du projet à partir duquel les images seront chargées. En outre, la `GetImageDirectory` méthode peut être utilisée pour retourner un `string` qui représente le répertoire du projet qui contient les ressources de l’image d’application.

Le résultat est que toutes les images utilisées dans une application sont chargées à partir du répertoire de projet spécifié.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)