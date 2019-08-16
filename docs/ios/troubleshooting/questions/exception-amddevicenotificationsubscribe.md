---
title: Erreur System.Exception AMDeviceNotificationSubscribe retournée ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d834c06c5fa5ee55e5e3b91bd16b5b4d326c42ee
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528159"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>Erreur System.Exception AMDeviceNotificationSubscribe retournée ...

> [!IMPORTANT]
> Ce problème a été résolu dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.


## <a name="fix"></a>Corriger

1. Arrêtez le `usbmuxd` processus afin que le système le redémarre:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2. Si cela ne résout pas le problème, redémarrez le Mac.

## <a name="error-message"></a>Message d’erreur

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

Ce message peut s’afficher dans une boîte de dialogue d’erreur lorsque vous démarrez pour la `mtbserver.log` première fois Visual Studio pour Mac ou dans le fichier de l’application hôte de build Xamarin. iOS (**Xamarin. iOS hôte de build > Afficher le journal**de l’hôte de Build).

Notez qu’il s’agit d’un problème rare. Si Visual Studio rencontre des problèmes de connexion à l’hôte de build Mac, d’autres erreurs risquent de se produire dans le `mtbserver.log` fichier.

### <a name="errors-in-systemlog"></a>Erreurs dans System. log

Dans certains cas, les deux messages d’erreur suivants peuvent également apparaître à `/var/log/system.log`plusieurs reprises dans:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Informations supplémentaires

L’une des hypothèses à la cause première de l’erreur est que les services système OS X chargés de la création de rapports sur les informations de l’appareil et du simulateur iOS peuvent, dans de rares cas, entrer dans un état inattendu. Xamarin ne peut pas interagir correctement avec les services système dans cet État. Le redémarrage de l’ordinateur redémarre les services système et résout le problème.

En fonction des erreurs `system.log` qui s’affichent, ce problème peut être lié à Bonjour (`mDNSResponder`). La modification entre différents réseaux Wi-Fi semble augmenter les risques d’atteinte au problème.

## <a name="references"></a>Références

* [Bogue 11789-MonoTouch. MobileDevice. MobileDeviceException: AMDeviceNotificationSubscribe retourné: 0xe8000063 [résolu NoResponse]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
