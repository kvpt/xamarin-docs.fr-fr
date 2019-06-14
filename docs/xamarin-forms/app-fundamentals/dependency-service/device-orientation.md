---
title: Vérification de l’orientation de l’appareil
description: Cet article explique comment utiliser la classe DependencyService de Xamarin.Forms pour accéder à l’orientation de l’appareil à partir du code partagé.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: d8763c6fe8e330181c836bc8d10923ea676a07c1
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740944"
---
# <a name="checking-device-orientation"></a>Vérification de l’orientation de l’appareil

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

Cet article vous montre comment utiliser [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour vérifier l’orientation de l’appareil dans le code partagé à l’aide des API natives sur chaque plateforme. Cette procédure pas à pas est basée sur le plug-in `DeviceOrientation` existant conçu par Ali Özgür. Pour plus d’informations, consultez le [dépôt GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation).

- **[Création de l’interface](#Creating_the_Interface)** &ndash; Découvrez comment créer l’interface dans le code partagé.
- **[Implémentation iOS ](#iOS_Implementation)** &ndash; Apprenez à implémenter l’interface dans le code natif pour iOS.
- **[Implémentation Android ](#Android_Implementation)** &ndash; Apprenez à implémenter l’interface dans le code natif pour Android.
- **[Implémentation UWP](#WindowsImplementation)** &ndash; Apprenez à implémenter l’interface dans le code natif pour la plateforme Windows universelle (UWP).
- **[Implémentation dans le code partagé](#Implementing_in_Shared_Code)** &ndash; Apprenez à utiliser `DependencyService` pour appeler l’implémentation native à partir du code partagé.

L’application qui utilise `DependencyService` aura la structure suivante :

![](device-orientation-images/orientation-diagram.png "Structure de l’application avec DependencyService")

> [!NOTE]
> Il est possible de vérifier si l’appareil est en mode portrait ou paysage dans le code partagé, comme cela est expliqué dans [Orientation de l’appareil](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation). La méthode décrite dans cet article utilise des fonctionnalités natives pour obtenir plus d’informations sur l’orientation, notamment si l’appareil est à l’envers.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Création de l'interface utilisateur

Tout d’abord, créez une interface dans le code partagé qui expose la fonctionnalité que vous souhaitez implémenter. Dans cet exemple, l’interface contient une seule méthode :

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

Le codage par rapport à cette interface dans le code partagé permettra à l’application Xamarin.Forms d’accéder aux API d’orientation de l’appareil sur chaque plateforme.

> [!NOTE]
> Les classes implémentant l’interface doivent avoir un constructeur sans paramètre pour fonctionner avec `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implémentation iOS

L’interface doit être implémentée dans le projet d’application de chaque plateforme. Notez que la classe a un constructeur sans paramètre pour permettre à `DependencyService` de créer des instances :

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

Enfin, ajoutez cet attribut `[assembly]` au-dessus de la classe (et en dehors des espaces de noms qui ont été définis), y compris toutes les instructions `using` requises :

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Cet attribut inscrit la classe en tant qu’implémentation de l’interface `IDeviceOrientation`, ce qui signifie que `DependencyService.Get<IDeviceOrientation>` peut être utilisé dans le code partagé pour créer une instance de celle-ci.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implémentation Android

Le code suivant implémente `IDeviceOrientation` sur Android :

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

Ajoutez cet attribut `[assembly]` au-dessus de la classe (et en dehors des espaces de noms qui ont été définis), y compris toutes les instructions `using` requises :

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Cet attribut inscrit la classe en tant qu’implémentation de l’interface `IDeviceOrientaiton`, ce qui signifie que `DependencyService.Get<IDeviceOrientation>` peut être utilisé dans le code partagé pour créer une instance de celle-ci.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implémentation sur la plateforme Windows universelle

Le code suivant implémente l’interface `IDeviceOrientation` sur la plateforme Windows universelle :

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

Ajoutez l’attribut `[assembly]` au-dessus de la classe (et en dehors des espaces de noms qui ont été définis), y compris toutes les instructions `using` requises :

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Cet attribut inscrit la classe en tant qu’implémentation de l’interface `DeviceOrientationImplementation`, ce qui signifie que `DependencyService.Get<IDeviceOrientation>` peut être utilisé dans le code partagé pour créer une instance de celle-ci.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implémentation dans le code partagé

Nous pouvons maintenant écrire et tester le code partagé qui accède à l’interface `IDeviceOrientation`. Cette page simple comporte un bouton qui met à jour le texte qu’il affiche selon l’orientation de l’appareil. `DependencyService` est utilisé pour obtenir une instance de l’interface `IDeviceOrientation` &ndash; au moment de l’exécution, cette instance constitue l’implémentation spécifique de la plateforme qui a un accès total au SDK natif :

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

Quand un clic sur ce bouton est enregistré dans cette application exécutée sur iOS, Android ou une plateforme Windows, le texte du bouton est mis à jour en fonction de l’orientation de l’appareil.

![](device-orientation-images/orientation.png "Exemple d’orientation de l’appareil")


## <a name="related-links"></a>Liens associés

- [Utilisation de DependencyService (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyService (exemple)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/)
- [Exemples Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
