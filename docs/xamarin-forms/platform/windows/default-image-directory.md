---
title: Répertoire d’images par défaut sur Windows
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le propre à la plateforme Windows qui définit le répertoire du projet à partir duquel les ressources d’images seront chargées.
ms.prod: xamarin
ms.assetid: 537A032B-74DD-4D43-864E-7D7113286D0D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 52197b980726936f4368ef1e4507ea671c9e70b1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291642"
---
# <a name="default-image-directory-on-windows"></a>Répertoire d’images par défaut sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme définit le répertoire du projet à partir duquel les ressources d’images seront chargées. Il est consommé en XAML en affectant à la `Application.ImageDirectory` un `string` qui représente le répertoire du projet qui contient les éléments multimédias de l’image :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

La méthode `Application.On<Windows>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. La méthode `Application.SetImageDirectory`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , est utilisée pour spécifier le répertoire du projet à partir duquel les images seront chargées. En outre, la méthode `GetImageDirectory` peut être utilisée pour retourner un `string` qui représente le répertoire du projet qui contient les ressources de l’image d’application.

Le résultat est que toutes les images utilisées dans une application sont chargées à partir du répertoire de projet spécifié.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
