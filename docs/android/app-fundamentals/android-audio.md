---
title: Audio Android
description: Le système d’exploitation Android offre une prise en charge complète du multimédia, qui englobe à la fois l’audio et la vidéo. Ce guide se concentre sur l’audio dans Android et traite de la lecture et de l’enregistrement audio à l’aide des classes de lecteur et d’enregistreur audio intégrées, ainsi que de l’API audio de bas niveau. Il traite également de l’utilisation des événements audio diffusés par d’autres applications, afin que les développeurs puissent créer des applications bien connues.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 960b4eb058209547c65a3b438bed541c3ade257c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521256"
---
# <a name="android-audio"></a>Audio Android

_Le système d’exploitation Android offre une prise en charge complète du multimédia, qui englobe à la fois l’audio et la vidéo. Ce guide se concentre sur l’audio dans Android et traite de la lecture et de l’enregistrement audio à l’aide des classes de lecteur et d’enregistreur audio intégrées, ainsi que de l’API audio de bas niveau. Il traite également de l’utilisation des événements audio diffusés par d’autres applications, afin que les développeurs puissent créer des applications bien connues._


## <a name="overview"></a>Présentation

Les appareils mobiles modernes ont adopté des fonctionnalités qui, auparavant, auraient requis des &ndash; appareils de caméras, de lecteurs musicaux et d’enregistreurs vidéo dédiés. Pour cette raison, les frameworks multimédias sont devenus une fonctionnalité de première classe dans les API mobiles.

Android offre une prise en charge complète des éléments multimédias. Cet article examine l’utilisation de l’audio dans Android et aborde les sujets suivants:

1. **Lecture audio avec MediaPlayer** Utilisation de la `MediaPlayer` classe intégrée pour lire l’audio, y compris les fichiers audio locaux et les fichiers audio diffusés avec la `AudioTrack` classe. &ndash;

2. **Enregistrement audio** Utilisation de la `MediaRecorder` classe intégrée pour enregistrer l’audio. &ndash;

3. **Utilisation des notifications audio** &ndash; Utilisation de notifications audio pour créer des applications bien connues qui répondent correctement aux événements (par exemple, les appels téléphoniques entrants) en interrompant ou en annulant leurs sorties audio.

4. **Utilisation de l’audio de bas niveau** Lecture audio à l' `AudioTrack` aide de la classe en écrivant directement dans des mémoires tampons. &ndash; Enregistrement audio à l' `AudioRecord` aide de la classe et lecture directe à partir de mémoires tampons.


## <a name="requirements"></a>Configuration requise

Ce guide nécessite Android 2,0 (niveau d’API 5) ou une version ultérieure. Notez que le débogage audio sur Android doit être effectué sur un appareil.

Il est nécessaire de demander les `RECORD_AUDIO` autorisations dans **fichier AndroidManifest. xml**:

![Section autorisations requises du manifeste Android avec enregistrement\_audio activé](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Lecture audio avec la classe MediaPlayer

La façon la plus simple de lire des données audio dans Android est d’utiliser la classe [MediaPlayer](xref:Android.Media.MediaPlayer) intégrée.
`MediaPlayer`peut lire des fichiers locaux ou distants en passant le chemin d’accès au fichier. Toutefois, `MediaPlayer` est très sensible à l’État et l’appel de l’une de ses méthodes dans un état incorrect entraîne la levée d’une exception. Il est important d’interagir avec `MediaPlayer` dans l’ordre décrit ci-dessous pour éviter les erreurs.



### <a name="initializing-and-playing"></a>Initialisation et exécution

La lecture audio `MediaPlayer` avec requiert la séquence suivante:

1. Instanciez un nouvel objet [MediaPlayer](xref:Android.Media.MediaPlayer) .

1. Configurez le fichier à lire à l’aide de la méthode [SetDataSource](xref:Android.Media.MediaPlayer.SetDataSource*) .

1. Appelez la méthode [prepare](xref:Android.Media.MediaPlayer.Prepare) pour initialiser le lecteur.

1. Appelez la méthode [Start](xref:Android.Media.MediaPlayer.Start) pour démarrer la lecture audio.


L’exemple de code ci-dessous illustre cette utilisation:

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>Interruption et reprise de la lecture

La lecture peut être interrompue en appelant la méthode [Pause](xref:Android.Media.MediaPlayer.Pause) :

```csharp
player.Pause();
```

Pour reprendre la lecture suspendue, appelez la méthode [Start](xref:Android.Media.MediaPlayer.Start) .
Cette opération reprend à partir de l’emplacement en pause dans la lecture:

```csharp
player.Start();
```

L’appel de la méthode [Stop](xref:Android.Media.MediaPlayer.Stop) sur le joueur met fin à une lecture en cours:

```csharp
player.Stop();
```

Lorsque le lecteur n’est plus nécessaire, les ressources doivent être libérées en appelant la méthode [Release](xref:Android.Media.MediaPlayer.Release) :

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Utilisation de la classe MediaRecorder pour enregistrer l’audio

La classe [MediaRecorder](xref:Android.Media.MediaRecorder) pour `MediaPlayer` l’enregistrement audio dans Android est la classe. Comme le `MediaPlayer`, il est sensible à l’État et passe par plusieurs États pour atteindre le point où il peut commencer à enregistrer. Pour pouvoir enregistrer l’audio, l' `RECORD_AUDIO` autorisation doit être définie. Pour obtenir des instructions sur la façon de définir des autorisations d’application, consultez [utilisation de fichier AndroidManifest. xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Initialisation et enregistrement

L’enregistrement de l' `MediaRecorder` audio avec le requiert les étapes suivantes:

1. Instanciez un nouvel objet [MediaRecorder](xref:Android.Media.MediaRecorder) .

2. Spécifiez le périphérique matériel à utiliser pour capturer l’entrée audio via la méthode [SetAudioSource](xref:Android.Media.MediaRecorder.SetAudioSource*) .

3. Définissez le format audio du fichier de sortie à l’aide de la méthode [SetOutputFormat](xref:Android.Media.MediaRecorder.SetOutputFormat*) . Pour obtenir la liste des types audio pris en charge, consultez [formats multimédias pris en charge par Android](https://developer.android.com/guide/appendix/media-formats.html).

4. Appelez la méthode [SetAudioEncoder](xref:Android.Media.MediaRecorder.SetAudioEncoder*) pour définir le type d’encodage audio.

5. Appelez la méthode [SetOutputFile](xref:Android.Media.MediaRecorder.SetOutputFile*) pour spécifier le nom du fichier de sortie dans lequel les données audio sont écrites.

6. Appelez la méthode [prepare](xref:Android.Media.MediaRecorder.Prepare) pour initialiser l’enregistreur.

7. Appelez la méthode [Start](xref:Android.Media.MediaRecorder.Start) pour démarrer l’enregistrement.


L’exemple de code suivant illustre cette séquence:

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>Arrêt de l’enregistrement

Pour arrêter l’enregistrement, appelez la `Stop` méthode sur le `MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Nettoyage en trop

Une fois que `MediaRecorder` a été arrêté, appelez la méthode de [réinitialisation](xref:Android.Media.MediaRecorder.Reset) pour rétablir l’état d’inactivité :

```csharp
recorder.Reset();
```

Lorsque n’est plus nécessaire, ses ressources doivent être libérées en appelant la méthode [Release:](xref:Android.Media.MediaRecorder.Release) `MediaRecorder`

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Gestion des notifications audio



### <a name="the-audiomanager-class"></a>La classe AudioManager

La classe [AudioManager](xref:Android.Media.AudioManager) fournit l’accès aux notifications audio qui permettent aux applications de savoir quand des événements audio se produisent. Ce service permet également d’accéder à d’autres fonctionnalités audio, telles que le contrôle du volume et du mode de sonnerie. Permet `AudioManager` à une application de gérer les notifications audio pour contrôler la lecture audio.



### <a name="managing-audio-focus"></a>Gestion du focus audio

Les ressources audio de l’appareil (lecteur et enregistreur intégrés) sont partagées par toutes les applications en cours d’exécution.

D’un point de vue conceptuel, cela est similaire aux applications sur un ordinateur de bureau où une seule application a le focus clavier: après avoir sélectionné l’une des applications en cours d’exécution en cliquant dessus, l’entrée du clavier est insérée uniquement pour cette application.

Le focus audio est une idée similaire et empêche la lecture ou l’enregistrement audio de plusieurs applications en même temps. Il est plus compliqué que le focus clavier, car il &ndash; est volontaire que l’application peut ignorer ce fait qu’elle ne possède pas actuellement le focus &ndash; audio et qu’elle est exécutée, qu’il y ait différents types de focus audio pouvant être devant. Par exemple, si le demandeur est supposé uniquement lire de l’audio pendant un très bref laps de temps, il peut demander un focus temporaire.

Le focus audio peut être accordé immédiatement, ou initialement refusé et accordé ultérieurement. Par exemple, si une application demande le focus audio pendant un appel téléphonique, elle est refusée, mais le focus peut être accordé une fois que l’appel téléphonique est terminé. Dans ce cas, un écouteur est inscrit pour répondre en conséquence si le focus audio est retiré. Le fait de demander le focus audio est utilisé pour déterminer s’il est correct de lire ou d’enregistrer l’audio.

Pour plus d’informations sur le focus audio, consultez [gestion du focus audio](https://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Inscription du rappel pour le focus audio

L’inscription `FocusChangeListener` du rappel à `IOnAudioChangeListener` partir de est une partie importante de l’obtention et de la libération du focus audio. Cela est dû au fait que l’octroi du focus audio peut être différé jusqu’à une date ultérieure. Par exemple, une application peut demander à écouter de la musique pendant un appel téléphonique en cours. Le focus audio n’est pas accordé tant que l’appel téléphonique n’est pas terminé.

Pour cette raison, l’objet `GetAudioFocus` `AudioManager`de rappel est passé en tant que paramètre dans la méthode de, et c’est cet appel qui inscrit le rappel. Si le focus audio est initialement refusé, mais accordé ultérieurement, l’application est informée `OnAudioFocusChange` en appelant sur le rappel. La même méthode est utilisée pour indiquer à l’application que le focus audio est retiré.

Lorsque l’application a terminé `AbandonFocus` `AudioManager`d’utiliser les ressources audio, elle appelle la méthode du et transmet à nouveau le rappel. Cela annule l’inscription du rappel et libère les ressources audio, afin que d’autres applications puissent obtenir le focus audio.



#### <a name="requesting-audio-focus"></a>Demande du focus audio

Les étapes requises pour demander les ressources audio de l’appareil sont les suivantes:

1. Obtenez un handle pour le `AudioManager` service système.

2. Créez une instance de la classe de rappel.

3. Demandez les ressources audio de l’appareil en appelant la `RequestAudioFocus` méthode sur le `AudioManager` . Les paramètres sont l’objet de rappel, le type de flux (musique, appel vocal, sonnerie, etc.) et le type de droit d’accès demandé (les ressources audio peuvent être demandées momentanément ou pendant une période indéterminée, par exemple).

4. Si la demande est accordée, la `playMusic` méthode est appelée immédiatement et le contenu audio commence à être lu.

5. Si la demande est refusée, aucune action supplémentaire n’est effectuée. Dans ce cas, l’audio est lu uniquement si la demande est accordée ultérieurement.


L’exemple de code ci-dessous illustre ces étapes:

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>Libération du focus audio

Lorsque la lecture de la piste est terminée, la `AbandonFocus` méthode sur `AudioManager` est appelée. Cela permet à une autre application d’obtenir les ressources audio de l’appareil. D’autres applications recevront une notification de ce changement de focus audio s’ils ont inscrit leurs propres écouteurs.


## <a name="low-level-audio-api"></a>API audio de bas niveau

Les API audio de bas niveau offrent un contrôle accru sur la lecture et l’enregistrement audio, car elles interagissent directement avec les mémoires tampons au lieu d’utiliser des URI de fichier. Dans certains cas, cette approche est préférable. Ces scénarios sont les suivants:

1. Lors de la lecture à partir de fichiers audio chiffrés.

2. Lors de la diffusion d’une succession de petits clips.

3. Streaming audio.


### <a name="audiotrack-class"></a>AudioTrack, classe

La classe [AudioTrack](xref:Android.Media.AudioTrack) utilise les API audio de bas niveau pour l’enregistrement et est l’équivalent de bas niveau de la `MediaPlayer` classe.


#### <a name="initializing-and-playing"></a>Initialisation et exécution

Pour lire de l’audio, une nouvelle `AudioTrack` instance de doit être instanciée. La liste d’arguments passée dans le [constructeur](xref:Android.Media.AudioTrack) spécifie comment lire l’exemple audio contenu dans la mémoire tampon. Les arguments sont les suivants:

1. Type &ndash; de flux voix, sonnerie, musique, système ou alarme.

2. Fréquence &ndash; à laquelle le taux d’échantillonnage est exprimé en Hz.

3. Configuration &ndash; de canal mono ou stéréo.

4. Format &ndash; audio 8 bits ou 16 bits.

5. Taille &ndash; de la mémoire tampon en octets.

6. Streaming en &ndash; mode de mémoire tampon ou statique.


Après la construction, la méthode [Play](xref:Android.Media.AudioTrack.Play) de `AudioTrack` est appelée afin de la configurer pour démarrer la lecture. L’écriture de la mémoire tampon `AudioTrack` audio sur le démarre la lecture:

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>Interruption et arrêt de la lecture

Appelez la méthode [Pause](xref:Android.Media.AudioTrack.Pause) pour suspendre la lecture:

```csharp
audioTrack.Pause();
```

L’appel de la méthode [Stop](xref:Android.Media.AudioTrack.Stop) met fin à la lecture de manière permanente:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Nettoyage

Quand le `AudioTrack` n’est plus nécessaire, ses ressources doivent être libérées par l’appel de [Release](xref:Android.Media.AudioTrack.Release):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>La classe AudioRecord

La classe [AudioRecord](xref:Android.Media.AudioRecord) est l’équivalent du `AudioTrack` côté enregistrement. À `AudioTrack`l’instar de, il utilise des mémoires tampons directement, à la place des fichiers et des URI. L' `RECORD_AUDIO` autorisation doit être définie dans le manifeste.


#### <a name="initializing-and-recording"></a>Initialisation et enregistrement

La première étape consiste à construire un nouvel objet [AudioRecord](xref:Android.Media.AudioRecord) . La liste d’arguments passée au [constructeur](xref:Android.Media.AudioRecord) fournit toutes les informations requises pour l’enregistrement. Contrairement à `AudioTrack`, où les arguments sont en grande partie des énumérations, les `AudioRecord` arguments équivalents dans sont des entiers. Elles incluent notamment :

1. Source d’entrée audio matérielle telle que microphone.

2. Type &ndash; de flux voix, sonnerie, musique, système ou alarme.

3. Fréquence &ndash; à laquelle le taux d’échantillonnage est exprimé en Hz.

4. Configuration &ndash; de canal mono ou stéréo.

5. Format &ndash; audio 8 bits ou 16 bits.

6. Taille de la mémoire tampon-en octets


Une fois `AudioRecord` le créé, sa méthode [StartRecording](xref:Android.Media.AudioRecord.StartRecording) est appelée. Il est maintenant prêt à commencer l’enregistrement. Le `AudioRecord` lit en continu la mémoire tampon audio pour l’entrée et écrit cette entrée dans un fichier audio.

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>Arrêt de l’enregistrement

L’appel de la méthode [Stop](xref:Android.Media.AudioRecord.Stop) met fin à l’enregistrement:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Nettoyage

Lorsque l' `AudioRecord` objet n’est plus nécessaire, l’appel de sa méthode [Release](xref:Android.Media.AudioRecord.Release) libère toutes les ressources qui lui sont associées:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Récapitulatif

Le système d’exploitation Android fournit une infrastructure puissante pour la lecture, l’enregistrement et la gestion de l’audio. Cet article a décrit comment lire et enregistrer des données audio à l’aide `MediaPlayer` des `MediaRecorder` classes de haut niveau et. Ensuite, il a exploré l’utilisation des notifications audio pour partager les ressources audio de l’appareil entre différentes applications. Enfin, il a abordé la lecture et l’enregistrement audio à l’aide des API de bas niveau, qui sont directement intégrées aux mémoires tampons.


## <a name="related-links"></a>Liens associés

- [Utilisation de l’audio (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/example-workingwithaudio)
- [Lecteur multimédia](xref:Android.Media.MediaPlayer)
- [Enregistreur multimédia](xref:Android.Media.MediaRecorder)
- [Gestionnaire audio](xref:Android.Media.AudioManager)
- [Piste audio](xref:Android.Media.AudioTrack)
- [Enregistreur audio](xref:Android.Media.AudioRecord)
