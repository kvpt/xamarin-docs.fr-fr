---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8132be3e709e6cfe519934ce6c16ae16ac7054c6
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454219"
---
# <a name="android-beam"></a>Android Beam

Android Beam est une technologie NFC (Near Field communication) introduite dans Android 4,0 qui permet aux applications de partager des informations sur NFC lorsqu’elles sont proches.

[![Diagramme illustrant deux appareils dans des informations de partage de proximité proches](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Le faisceau Android fonctionne en envoyant des messages sur NFC lorsque deux appareils sont à portée. Les appareils sur les 4cm peuvent partager des données à l’aide d’Android Beam. Une activité sur un appareil crée un message et spécifie une ou plusieurs activités qui peuvent gérer le push. Lorsque l’activité spécifiée se trouve au premier plan et que les périphériques sont à portée, Android Beam envoie le message au deuxième appareil. Sur l’appareil de réception, une intention est appelée et contient les données du message.

Android prend en charge deux méthodes de configuration des messages avec Android Beam :

- `SetNdefPushMessage` -Avant d’initier le faisceau Android, une application peut appeler SetNdefPushMessage pour spécifier un NdefMessage pour effectuer un push sur NFC et l’activité qui l’exécute. Ce mécanisme est le mieux utilisé lorsqu’un message ne change pas lorsqu’une application est en cours d’utilisation.

- `SetNdefPushMessageCallback` -Quand le faisceau Android est initialisé, une application peut gérer un rappel pour créer un NdefMessage. Ce mécanisme permet de différer la création du message jusqu’à ce que les appareils soient à portée. Il prend en charge les scénarios dans lesquels le message peut varier en fonction de ce qui se passe dans l’application.

Dans les deux cas, pour envoyer des données avec Android Beam, une application envoie un `NdefMessage` , en empaquetant les données dans plusieurs `NdefRecords` . Jetons un coup d’œil aux points clés qui doivent être traités avant de pouvoir déclencher le faisceau Android. Tout d’abord, nous allons utiliser le style de rappel de la création d’un `NdefMessage` .

## <a name="creating-a-message"></a>Création d’un message

Nous pouvons inscrire des rappels avec un `NfcAdapter` dans la méthode de l’activité `OnCreate` . Par exemple, en supposant `NfcAdapter` qu’un nommé `mNfcAdapter` est déclaré en tant que variable de classe dans l’activité, nous pouvons écrire le code suivant pour créer le rappel qui construira le message :

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

L’activité, qui implémente `NfcAdapter.ICreateNdefMessageCallback` , est passée à la `SetNdefPushMessageCallback` méthode ci-dessus. Lorsque le faisceau Android est initialisé, le système appelle `CreateNdefMessage` , à partir duquel l’activité peut construire un `NdefMessage` comme indiqué ci-dessous :

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

Du côté de la réception, le système appelle une intention avec l' `ActionNdefDiscovered` action, à partir de laquelle nous pouvons extraire le NdefMessage comme suit :

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Pour obtenir un exemple de code complet qui utilise le faisceau Android, illustré dans la capture d’écran ci-dessous, consultez la [démonstration de faisceau Android](/samples/xamarin/monodroid-samples/androidbeamdemo) dans la Galerie d’exemples.

[![Exemples de captures d’écran de la démonstration de faisceau Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Liens associés

- [Démo de faisceau Android (exemple)](/samples/xamarin/monodroid-samples/androidbeamdemo)