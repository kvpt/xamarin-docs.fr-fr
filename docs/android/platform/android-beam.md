---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: aab121ed5f811baf38eed48cf891ccdf076eaf44
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292891"
---
# <a name="android-beam"></a>Android Beam

Android Beam est une technologie de communication en champ proche (NFC) introduite dans Android 4.0 qui permet aux applications de partager des informations sur NFC lorsqu’elles sont à proximité.

[![Diagramme illustrant deux appareils à proximité partageant des informations](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android Beam fonctionne en poussant des messages sur NFC lorsque deux appareils sont à portée. Les appareils à environ 4 cm les uns des autres peuvent partager des données à l’aide d’Android Beam. Une activité sur un appareil crée un message et spécifie une activité (ou activités) qui peut gérer le pousser. Lorsque l’activité spécifiée est au premier plan et que les appareils sont à portée, Android Beam poussera le message vers le deuxième appareil. Sur l’appareil de réception, une intention est invoquée contenant les données du message.

Android prend en charge deux façons de définir des messages avec Android Beam:

- `SetNdefPushMessage`- Avant l’lancement d’Android Beam, une application peut appeler SetNdefPushMessage pour spécifier un NdefMessage pour pousser NFC, et l’activité qui la pousse. Ce mécanisme est mieux utilisé lorsqu’un message ne change pas pendant qu’une application est utilisée.

- `SetNdefPushMessageCallback`- Lorsque Android Beam est lancé, une application peut gérer un rappel pour créer un NdefMessage. Ce mécanisme permet de retarder la création de messages jusqu’à ce que les périphériques soient à portée. Il prend en charge les scénarios où le message peut varier en fonction de ce qui se passe dans l’application.

Dans les deux cas, pour envoyer des `NdefMessage`données avec Android `NdefRecords`Beam, une application envoie un , l’emballage des données dans plusieurs . Jetons un coup d’oeil aux points clés qui doivent être abordés avant que nous puissions déclencher Android Beam. Tout d’abord, nous allons travailler avec `NdefMessage`le style de rappel de la création d’un .

## <a name="creating-a-message"></a>Création d’un message

Nous pouvons enregistrer les `NfcAdapter` rappels avec `OnCreate` une méthode de l’activité. Par exemple, `NfcAdapter` en `mNfcAdapter` supposant qu’un nom soit déclaré variable de classe dans l’activité, nous pouvons écrire le code suivant pour créer le rappel qui construira le message :

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

L’activité, qui `NfcAdapter.ICreateNdefMessageCallback`met en `SetNdefPushMessageCallback` œuvre, est transmise à la méthode ci-dessus. Lorsque Android Beam est lancé, `CreateNdefMessage`le système appelle, `NdefMessage` à partir de laquelle l’activité peut construire un comme indiqué ci-dessous:

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

## <a name="receiving-a-message"></a>Recevoir un message

Du côté de la réception, le `ActionNdefDiscovered` système invoque une intention avec l’action, à partir de laquelle nous pouvons extraire le NdefMessage comme suit:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Pour un exemple de code complet qui utilise Android Beam, montré en cours d’exécution dans la capture d’écran ci-dessous, voir la [démo Android Beam](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) dans la galerie d’échantillons.

[![Exemple de captures d’écran de la démo Android Beam](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [Démo Android Beam (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
