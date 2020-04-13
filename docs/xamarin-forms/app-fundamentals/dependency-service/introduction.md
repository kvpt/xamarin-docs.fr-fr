---
title: Présentation de Xamarin.Forms DependencyService
description: Cet article explique comment utiliser la classe DependencyService de Xamarin.Forms pour appeler des fonctionnalités natives d’une plateforme.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: b27b4b0c3c5662c6cc1c2c151dd9ebe1523da3a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "71198521"
---
# <a name="xamarinforms-dependencyservice-introduction"></a>Présentation de Xamarin.Forms DependencyService

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

La [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe est un localisateur de service qui permet aux applications Xamarin.Forms d’invoquer la fonctionnalité de la plate-forme native à partir de code partagé.

Le processus d’utilisation de la [`DependencyService`](xref:Xamarin.Forms.DependencyService) fonctionnalité de la plate-forme native pour invoquer est de :

1. Créer une interface pour les fonctionnalités natives de la plateforme dans du code partagé. Pour plus d’informations, consultez [Créer une interface](#create-an-interface).
1. Implémenter l’interface dans les projets de plateforme nécessaires. Pour plus d’informations, consultez [Implémenter l’interface sur chaque plateforme](#implement-the-interface-on-each-platform).
1. Enregistrez les implémentations de la plate-forme avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService). Ceci permet à Xamarin.Forms de localiser les implémentations de plateforme lors de l’exécution. Pour plus d’informations, consultez [Inscrire les implémentations de plateforme](#register-the-platform-implementations).
1. Résoudre les implémentations de plateforme à partir du code partagé et les appeler. Pour plus d’informations, consultez [Résoudre les implémentations de plateforme](#resolve-the-platform-implementations).

Le diagramme suivant montre comment les fonctionnalités natives d’une plateforme sont appelées dans une application Xamarin.Forms :

![Aperçu de l’emplacement du service à l’aide de la classe Xamarin.Forms DependencyService](introduction-images/dependency-service.png "Emplacement du service DependencyService")

## <a name="create-an-interface"></a>Créer une interface

La première étape pour pouvoir appeler des fonctionnalités natives d’une plateforme depuis du code partagé est de créer une interface qui définit l’API pour interagir avec ces fonctionnalités. Cette interface doit être placée dans votre projet de code partagé.

L’exemple suivant montre une interface pour une API qui peut être utilisée pour récupérer l’orientation d’un appareil :

```csharp
public interface IDeviceOrientationService
{
    DeviceOrientation GetOrientation();
}
```

## <a name="implement-the-interface-on-each-platform"></a>Implémenter l’interface sur chaque plateforme

Une fois que vous avez créé l’interface qui définit l’API permettant d’interagir avec les fonctionnalités natives d’une plateforme, vous devez implémenter cette interface dans chaque projet de plateforme.

### <a name="ios"></a>iOS

L’exemple de code suivant montre l’implémentation de l’interface `IDeviceOrientationService` sur iOS :

```csharp
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            UIInterfaceOrientation orientation = UIApplication.SharedApplication.StatusBarOrientation;

            bool isPortrait = orientation == UIInterfaceOrientation.Portrait ||
                orientation == UIInterfaceOrientation.PortraitUpsideDown;
            return isPortrait ? DeviceOrientation.Portrait : DeviceOrientation.Landscape;
        }
    }
}
```

### <a name="android"></a>Android

L’exemple de code suivant montre l’implémentation de l’interface `IDeviceOrientationService` sur Android :

```csharp
namespace DependencyServiceDemos.Droid
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            SurfaceOrientation orientation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = orientation == SurfaceOrientation.Rotation90 ||
                orientation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

L’exemple de code suivant montre l’implémentation de `IDeviceOrientationService` sur la plateforme Windows universelle (UWP) :

```csharp
namespace DependencyServiceDemos.UWP
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ApplicationViewOrientation orientation = ApplicationView.GetForCurrentView().Orientation;
            return orientation == ApplicationViewOrientation.Landscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

## <a name="register-the-platform-implementations"></a>Inscrire les implémentations de plateforme

Après la mise en œuvre de l’interface dans [`DependencyService`](xref:Xamarin.Forms.DependencyService)chaque projet de plate-forme, les implémentations de la plate-forme doivent être enregistrées auprès de la , afin que Xamarin.Forms peut les localiser au moment de l’exécution. Ceci est généralement [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)effectué avec le , ce qui indique que le type spécifié fournit une implémentation de l’interface.

L’exemple suivant [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) montre l’utilisation de `IDeviceOrientationService` l’enregistrement de la mise en œuvre iOS de l’interface:

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DependencyServiceDemos.iOS.DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

Dans cet exemple, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) les `DeviceOrientationService` registres avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService). De même, les `IDeviceOrientationService` implémentations de l’interface sur d’autres plates-formes doivent être enregistrées auprès de la [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

Pour plus d’informations sur l’enregistrement des implémentations de la plate-forme avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService), voir [Xamarin.Forms DependencyService Enregistrement et résolution](registration-and-resolution.md).

## <a name="resolve-the-platform-implementations"></a>Résoudre les implémentations d’une plateforme

Suite à l’enregistrement [`DependencyService`](xref:Xamarin.Forms.DependencyService)des implémentations de la plate-forme avec le , les implémentations doivent être résolues avant d’être invoquées. Ceci est généralement effectué dans [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) le code partagé en utilisant la méthode.

Le code suivant montre un [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) exemple d’appel de la méthode pour résoudre l’interface, `IDeviceOrientationService` puis d’invoquer sa `GetOrientation` méthode:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

Pour plus d’informations sur la [`DependencyService`](xref:Xamarin.Forms.DependencyService)résolution des implémentations de la plate-forme avec le , voir [Xamarin.Forms DependencyService Enregistrement et résolution](registration-and-resolution.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de DependencyService (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Inscription et résolution de Xamarin.Forms DependencyService](registration-and-resolution.md)
