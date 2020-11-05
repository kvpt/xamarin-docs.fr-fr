---
title: Xamarin.Forms Présentation de DependencyService
description: Cet article explique comment utiliser la Xamarin.Forms classe DependencyService pour appeler des fonctionnalités de plateforme natives.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1234584dda50746e45d121eb03a3c6abafd8473a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371506"
---
# <a name="no-locxamarinforms-dependencyservice-introduction"></a>Xamarin.Forms Présentation de DependencyService

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/dependencyservice/)

La [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe est un localisateur de service qui permet Xamarin.Forms aux applications d’appeler des fonctionnalités de plateforme natives à partir de code partagé.

Le processus d’utilisation de [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour appeler la fonctionnalité de plateforme Native consiste à :

1. Créer une interface pour les fonctionnalités natives de la plateforme dans du code partagé. Pour plus d’informations, consultez [Créer une interface](#create-an-interface).
1. Implémenter l’interface dans les projets de plateforme nécessaires. Pour plus d’informations, consultez [Implémenter l’interface sur chaque plateforme](#implement-the-interface-on-each-platform).
1. Inscrivez les implémentations de plateforme avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Cela permet Xamarin.Forms à de localiser les implémentations de plateforme au moment de l’exécution. Pour plus d’informations, consultez [Inscrire les implémentations de plateforme](#register-the-platform-implementations).
1. Résoudre les implémentations de plateforme à partir du code partagé et les appeler. Pour plus d’informations, consultez [Résoudre les implémentations de plateforme](#resolve-the-platform-implementations).

Le diagramme suivant montre comment les fonctionnalités de plateforme natives sont appelées dans une Xamarin.Forms application :

![Vue d’ensemble de l’emplacement du service à l’aide de la classe ::: No-Loc (Xamarin. Forms) ::: DependencyService](introduction-images/dependency-service.png "Emplacement du service DependencyService")

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

Après l’implémentation de l’interface dans chaque projet de plateforme, les implémentations de plateforme doivent être inscrites auprès du [`DependencyService`](xref:Xamarin.Forms.DependencyService) , afin que Xamarin.Forms puisse les localiser au moment de l’exécution. Cette opération est généralement effectuée avec [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) , qui indique que le type spécifié fournit une implémentation de l’interface.

L’exemple suivant illustre l’utilisation de [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) pour inscrire l’implémentation iOS de l' `IDeviceOrientationService` interface :

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

Dans cet exemple, le [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) s’inscrit `DeviceOrientationService` auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService) . De même, les implémentations de l' `IDeviceOrientationService` interface sur d’autres plateformes doivent être inscrites auprès du [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) .

Pour plus d’informations sur l’inscription des implémentations de plateforme avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService) , consultez [ Xamarin.Forms inscription et résolution DependencyService](registration-and-resolution.md).

## <a name="resolve-the-platform-implementations"></a>Résoudre les implémentations d’une plateforme

Après l’inscription des implémentations de plateforme avec [`DependencyService`](xref:Xamarin.Forms.DependencyService) , les implémentations doivent être résolues avant d’être appelées. Cela est généralement effectué en code partagé à l’aide de la [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode.

Le code suivant illustre un exemple d’appel de la [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode pour résoudre l’interface, puis l’appel de la `IDeviceOrientationService` `GetOrientation` méthode :

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

Pour plus d’informations sur la résolution des implémentations de plateforme avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService) , consultez [ Xamarin.Forms inscription et résolution DependencyService](registration-and-resolution.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de DependencyService (exemple)](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.Forms Inscription et résolution DependencyService](registration-and-resolution.md)