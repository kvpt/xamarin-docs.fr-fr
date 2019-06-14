---
title: Vérification de l’état de la batterie
description: Cet article explique comment utiliser la classe DependencyService de Xamarin.Forms pour accéder de façon native aux informations d’état de la batterie sur chaque plateforme.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 2c9fae211e6a88944c94ca265409865b3252a10a
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740931"
---
# <a name="checking-battery-status"></a>Vérification de l’état de la batterie

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/)

Cet article vous montre comment créer une application qui vérifie l’état de la batterie. Cet article est basé sur le plug-in de batterie conçu par James Montemagno. Pour plus d’informations, consultez le [dépôt GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Étant donné que Xamarin.Forms n’inclut pas de fonctionnalité permettant de vérifier l’état actuel de la batterie, cette application doit utiliser [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour accéder aux API natives.  Cet article décrit les étapes suivantes nécessaires pour utiliser `DependencyService` :

- **[Création de l’interface](#Creating_the_Interface)** &ndash; Découvrez comment créer l’interface dans le code partagé.
- **[Implémentation iOS ](#iOS_Implementation)** &ndash; Apprenez à implémenter l’interface dans le code natif pour iOS.
- **[Implémentation Android ](#Android_Implementation)** &ndash; Apprenez à implémenter l’interface dans le code natif pour Android.
- **[Implémentation UWP](#UWPImplementation)** &ndash; Apprenez à implémenter l’interface dans le code natif pour la plateforme Windows universelle (UWP).
- **[Implémentation dans le code partagé](#Implementing_in_Shared_Code)** &ndash; Apprenez à utiliser `DependencyService` pour appeler l’implémentation native à partir du code partagé.

Une fois terminée, l’application qui utilise `DependencyService` aura la structure suivante :

![](battery-info-images/battery-diagram.png "Structure de l’application avec DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Création de l'interface utilisateur

Tout d’abord, créez une interface dans le code partagé qui expose la fonctionnalité que vous souhaitez implémenter. Une application qui vérifie l’état de la batterie a besoin de ces informations : le pourcentage de batterie restante, si l’appareil est ou non en charge, et le mode d’alimentation de l’appareil :

```csharp
namespace DependencyServiceSample
{
  public enum BatteryStatus
  {
    Charging,
    Discharging,
    Full,
    NotCharging,
    Unknown
  }

  public enum PowerSource
  {
    Battery,
    Ac,
    Usb,
    Wireless,
    Other
  }

  public interface IBattery
  {
    int RemainingChargePercent { get; }
    BatteryStatus Status { get; }
    PowerSource PowerSource { get; }
  }
}
```

Le codage par rapport à cette interface dans le code partagé permettra à l’application Xamarin.Forms d’accéder aux API de gestion de l’alimentation sur chaque plateforme.

> [!NOTE]
> Les classes implémentant l’interface doivent avoir un constructeur sans paramètre pour fonctionner avec `DependencyService`. Les constructeurs ne peuvent pas être définis par des interfaces.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implémentation iOS

L’interface `IBattery` doit être implémentée dans le projet d’application de chaque plateforme. L’implémentation iOS utilise les API [`UIDevice`](xref:UIKit.UIDevice) natives pour accéder aux informations d’état de la batterie. Notez que la classe suivante a un constructeur sans paramètre pour permettre à `DependencyService` de créer des instances :

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;

namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery
  {
    public BatteryImplementation()
    {
      UIDevice.CurrentDevice.BatteryMonitoringEnabled = true;
    }

    public int RemainingChargePercent
    {
      get
      {
        return (int)(UIDevice.CurrentDevice.BatteryLevel * 100F);
      }
    }

    public BatteryStatus Status
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return BatteryStatus.Charging;
          case UIDeviceBatteryState.Full:
            return BatteryStatus.Full;
          case UIDeviceBatteryState.Unplugged:
            return BatteryStatus.Discharging;
          default:
            return BatteryStatus.Unknown;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Full:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Unplugged:
            return PowerSource.Battery;
          default:
            return PowerSource.Other;
        }
      }
    }
  }
}
```

Enfin, ajoutez cet attribut `[assembly]` au-dessus de la classe (et en dehors des espaces de noms qui ont été définis), y compris toutes les instructions `using` requises :

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;//necessary for registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery {
    ...
```

Cet attribut inscrit la classe en tant qu’implémentation de l’interface `IBattery`, ce qui signifie que `DependencyService.Get<IBattery>` peut être utilisé dans le code partagé pour créer une instance de celle-ci :

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implémentation Android

L’implémentation Android utilise l’API [`Android.OS.BatteryManager`](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/). Cette implémentation est plus complexe que la version iOS, car elle nécessite de vérifier qu’il ne manque pas certaines autorisations d’accès à la batterie :

```csharp
using System;
using Android;
using Android.Content;
using Android.App;
using Android.OS;
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid;

namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery
  {
    private BatteryBroadcastReceiver batteryReceiver;
    public BatteryImplementation() { }

    public int RemainingChargePercent
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              var level = battery.GetIntExtra(BatteryManager.ExtraLevel, -1);
              var scale = battery.GetIntExtra(BatteryManager.ExtraScale, -1);

              return (int)Math.Floor(level * 100D / scale);
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }

      }
    }

    public DependencyServiceSample.BatteryStatus Status
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;
              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);
              if (isCharging)
                return DependencyServiceSample.BatteryStatus.Charging;

              switch(status)
              {
                case (int)BatteryStatus.Charging:
                  return DependencyServiceSample.BatteryStatus.Charging;
                case (int)BatteryStatus.Discharging:
                  return DependencyServiceSample.BatteryStatus.Discharging;
                case (int)BatteryStatus.Full:
                  return DependencyServiceSample.BatteryStatus.Full;
                case (int)BatteryStatus.NotCharging:
                  return DependencyServiceSample.BatteryStatus.NotCharging;
                default:
                  return DependencyServiceSample.BatteryStatus.Unknown;
              }
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;

              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);

              if (!isCharging)
                return DependencyServiceSample.PowerSource.Battery;
              else if (usbCharge)
                return DependencyServiceSample.PowerSource.Usb;
              else if (acCharge)
                return DependencyServiceSample.PowerSource.Ac;
              else if (wirelessCharge)
                return DependencyServiceSample.PowerSource.Wireless;
              else
                return DependencyServiceSample.PowerSource.Other;
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }
  }
}
```

Ajoutez cet attribut `[assembly]` au-dessus de la classe (et en dehors des espaces de noms qui ont été définis), y compris toutes les instructions `using` requises :

```csharp
...
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery {
    ...
```

Cet attribut inscrit la classe en tant qu’implémentation de l’interface `IBattery`, ce qui signifie que `DependencyService.Get<IBattery>` peut être utilisé dans le code partagé pour créer une instance de celle-ci.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implémentation sur la plateforme Windows universelle

L’implémentation UWP utilise les API `Windows.Devices.Power` pour obtenir les informations d’état de la batterie :

```csharp
using DependencyServiceSample.UWP;
using Xamarin.Forms;

[assembly: Dependency(typeof(BatteryImplementation))]
namespace DependencyServiceSample.UWP
{
    public class BatteryImplementation : IBattery
    {
        private BatteryStatus status = BatteryStatus.Unknown;
        Windows.Devices.Power.Battery battery;

        public BatteryImplementation()
        {
        }

        private Windows.Devices.Power.Battery DefaultBattery
        {
            get
            {
                return battery ?? (battery = Windows.Devices.Power.Battery.AggregateBattery);
            }
        }

        public int RemainingChargePercent
        {
            get
            {
                var finalReport = DefaultBattery.GetReport();
                var finalPercent = -1;

                if (finalReport.RemainingCapacityInMilliwattHours.HasValue && finalReport.FullChargeCapacityInMilliwattHours.HasValue)
                {
                    finalPercent = (int)((finalReport.RemainingCapacityInMilliwattHours.Value /
                        (double)finalReport.FullChargeCapacityInMilliwattHours.Value) * 100);
                }
                return finalPercent;
            }
        }

        public BatteryStatus Status
        {
            get
            {
                var report = DefaultBattery.GetReport();
                var percentage = RemainingChargePercent;

                if (percentage >= 1.0)
                {
                    status = BatteryStatus.Full;
                }
                else if (percentage < 0)
                {
                    status = BatteryStatus.Unknown;
                }
                else
                {
                    switch (report.Status)
                    {
                        case Windows.System.Power.BatteryStatus.Charging:
                            status = BatteryStatus.Charging;
                            break;
                        case Windows.System.Power.BatteryStatus.Discharging:
                            status = BatteryStatus.Discharging;
                            break;
                        case Windows.System.Power.BatteryStatus.Idle:
                            status = BatteryStatus.NotCharging;
                            break;
                        case Windows.System.Power.BatteryStatus.NotPresent:
                            status = BatteryStatus.Unknown;
                            break;
                    }
                }
                return status;
            }
        }

        public PowerSource PowerSource
        {
            get
            {
                if (status == BatteryStatus.Full || status == BatteryStatus.Charging)
                {
                    return PowerSource.Ac;
                }
                return PowerSource.Battery;
            }
        }
    }
}
```

L’attribut `[assembly]` au-dessus de la déclaration de l’espace de noms inscrit la classe en tant qu’implémentation de l’interface `IBattery`, ce qui signifie que `DependencyService.Get<IBattery>` peut être utilisé dans le code partagé pour créer une instance de celle-ci.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implémentation dans le code partagé

Maintenant que l’interface a été implémentée pour chaque plateforme, l’application partagée peut être écrite pour l’utiliser. L’application se compose d’une page avec un bouton. Quand l’utilisateur appuie sur ce bouton, le texte du bouton est mis à jour avec l’état actuel de la batterie. `DependencyService` est utilisé pour obtenir une instance de l’interface `IBattery`. Au moment de l’exécution, cette instance constitue l’implémentation spécifique de la plateforme qui a un accès total au SDK natif.

```csharp
public MainPage ()
{
    var button = new Button {
        Text = "Click for battery info",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    button.Clicked += (sender, e) => {
        var bat = DependencyService.Get<IBattery>();

        switch (bat.PowerSource){
          case PowerSource.Battery:
            button.Text = "Battery - ";
            break;
          case PowerSource.Ac:
            button.Text = "AC - ";
            break;
          case PowerSource.Usb:
            button.Text = "USB - ";
            break;
          case PowerSource.Wireless:
            button.Text = "Wireless - ";
            break;
          case PowerSource.Other:
          default:
            button.Text = "Other - ";
            break;
        }
        switch (bat.Status){
          case BatteryStatus.Charging:
            button.Text += "Charging";
            break;
          case BatteryStatus.Discharging:
            button.Text += "Discharging";
            break;
          case BatteryStatus.NotCharging:
            button.Text += "Not Charging";
            break;
          case BatteryStatus.Full:
            button.Text += "Full";
            break;
          case BatteryStatus.Unknown:
          default:
            button.Text += "Unknown";
            break;
        }
    };
    Content = button;
}
```

Quand un clic sur ce bouton est enregistré dans cette application exécutée sur iOS, Android ou UWP, le texte du bouton est mis à jour pour refléter l’état actuel de la batterie de l’appareil.

![](battery-info-images/battery.png "Exemple de l’état de la batterie")


## <a name="related-links"></a>Liens associés

- [DependencyService (exemple)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/)
- [Utilisation de DependencyService (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Exemples Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
