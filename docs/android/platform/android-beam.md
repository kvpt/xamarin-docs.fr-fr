---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 0c4f7303d3620dcc2c829d732fe7a5f97f0e3883
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643757"
---
# <a name="android-beam"></a>Android Beam

Android Beam est une technologie NFC (Near Field communication) introduite dans Android 4,0 qui permet aux applications de partager des informations sur NFC lorsqu’elles sont proches.

[![Diagramme illustrant deux appareils dans des informations de partage de proximité proches](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Le faisceau Android fonctionne en envoyant des messages sur NFC lorsque deux appareils sont à portée. Les appareils sur les 4cm peuvent partager des données à l’aide d’Android Beam. Une activité sur un appareil crée un message et spécifie une ou plusieurs activités qui peuvent gérer le push. Lorsque l’activité spécifiée se trouve au premier plan et que les périphériques sont à portée, Android Beam envoie le message au deuxième appareil. Sur l’appareil de réception, une intention est appelée et contient les données du message.

Android prend en charge deux méthodes de configuration des messages avec Android Beam:

-   `SetNdefPushMessage`-Avant d’initier le faisceau Android, une application peut appeler SetNdefPushMessage pour spécifier un NdefMessage pour effectuer un push sur NFC et l’activité qui l’exécute. Ce mécanisme est le mieux utilisé lorsqu’un message ne change pas lorsqu’une application est en cours d’utilisation.

-   `SetNdefPushMessageCallback`-Quand le faisceau Android est initialisé, une application peut gérer un rappel pour créer un NdefMessage. Ce mécanisme permet de différer la création du message jusqu’à ce que les appareils soient à portée. Il prend en charge les scénarios dans lesquels le message peut varier en fonction de ce qui se passe dans l’application.


Dans les deux cas, pour envoyer des données avec Android Beam, une application `NdefMessage`envoie un, en empaquetant les données dans plusieurs. `NdefRecords` Jetons un coup d’œil aux points clés qui doivent être traités avant de pouvoir déclencher le faisceau Android. Tout d’abord, nous allons utiliser le style de rappel de `NdefMessage`la création d’un.


## <a name="creating-a-message"></a>Création d’un message

Nous pouvons inscrire des rappels avec `NfcAdapter` un dans la méthode `OnCreate` de l’activité. Par exemple, en supposant `NfcAdapter` qu' `mNfcAdapter` un nommé est déclaré en tant que variable de classe dans l’activité, nous pouvons écrire le code suivant pour créer le rappel qui construira le message:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

L’activité, qui implémente `NfcAdapter.ICreateNdefMessageCallback`, est passée à la `SetNdefPushMessageCallback` méthode ci-dessus. Lorsque le faisceau Android est initialisé, le système appelle `CreateNdefMessage`, à partir duquel l’activité peut construire `NdefMessage` un comme indiqué ci-dessous:

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```


## <a name="receiving-a-message"></a>Réception d’un message

Du côté de la réception, le système appelle une intention avec l' `ActionNdefDiscovered` action, à partir de laquelle nous pouvons extraire le NdefMessage comme suit:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Pour obtenir un exemple de code complet qui utilise le faisceau Android, illustré dans la capture d’écran ci-dessous, consultez la [démonstration de faisceau Android](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) dans la Galerie d’exemples.

[![Exemples de captures d’écran de la démonstration de faisceau Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Liens associés

- [Démo de faisceau Android (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
- [Présentation du sandwich glacé](http://www.android.com/about/ice-cream-sandwich/)
- [Plateforme Android 4,0](https://developer.android.com/sdk/android-4.0.html)
