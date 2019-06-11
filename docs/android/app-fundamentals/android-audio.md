---
title: Android Audio
description: Le système d’exploitation Android fournit une prise en charge complète multimédias, englobant ainsi l’audio et vidéo. Ce guide se concentre sur audio dans Android et couvre la lecture et l’enregistrement audio à l’aide du lecteur audio intégré et les classes de l’enregistreur, ainsi que l’API de bas niveau audio. Il aborde également l’utilisation des événements Audio de diffusion par d’autres applications, afin que les développeurs peuvent créer des applications conformes.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: f34a76879c2a38ec8253d8f3dd3230f03d9af32e
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827324"
---
# <a name="android-audio"></a>Android Audio

_Le système d’exploitation Android fournit une prise en charge complète multimédias, englobant ainsi l’audio et vidéo. Ce guide se concentre sur audio dans Android et couvre la lecture et l’enregistrement audio à l’aide du lecteur audio intégré et les classes de l’enregistreur, ainsi que l’API de bas niveau audio. Il aborde également l’utilisation des événements Audio de diffusion par d’autres applications, afin que les développeurs peuvent créer des applications conformes._


## <a name="overview"></a>Vue d'ensemble

Les appareils mobiles modernes ont adopté des fonctionnalités qui anciennement aurait nécessité dédié d’éléments d’un équipement &ndash; caméras, des lecteurs de musique et des enregistreurs vidéo. Pour cette raison, les infrastructures multimédias sont devenus une fonctionnalité de première classe dans l’API mobile.

Android fournit la prise en charge complète pour multimédia. Cet article examine l’utilisation de l’audio dans Android et couvre les rubriques suivantes

1.  **Lecture Audio avec MediaPlayer** &ndash; à l’aide intégrée `MediaPlayer` classe pour la lecture audio, y compris les fichiers audio locaux et des fichiers audio en continu avec le `AudioTrack` classe.

2.  **Enregistrement Audio** &ndash; à l’aide intégrée `MediaRecorder` classe à enregistrer l’audio.

3.  **Utilisation des Notifications Audio** &ndash; à l’aide de notifications audio pour créer des applications se comportant bien répondent correctement aux événements (tels que les appels téléphoniques entrants) par la suspension ou l’annulation de leurs sorties audio.

4.  **Utilisation de bas niveau Audio** &ndash; lecture audio à l’aide de la `AudioTrack` classe en écrivant directement dans les mémoires tampons. L’enregistrement audio à l’aide de la `AudioRecord` classe et en lisant directement à partir de mémoires tampons.


## <a name="requirements"></a>Configuration requise

Ce guide nécessite Android 2.0 (API de niveau 5) ou une version ultérieure. Veuillez noter que le débogage audio sur Android doit être effectué sur un appareil.

Il est nécessaire des demande le `RECORD_AUDIO` autorisations dans **AndroidManifest.XML**:

![Section autorisations de manifeste Android avec enregistrement requise\_AUDIO est activé](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Lecture Audio avec la classe de MediaPlayer

Le plus simple pour lire un fichier audio dans Android est avec intégrés [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) classe.
`MediaPlayer` pouvez lire des fichiers locaux ou distants en passant le chemin d’accès de fichier. Toutefois, `MediaPlayer` est très sensible état et de l’appel de l’une de ses méthodes dans un état incorrect provoquera une exception levée. Il est important d’interagir avec `MediaPlayer` dans l’ordre décrit ci-dessous pour éviter les erreurs.



### <a name="initializing-and-playing"></a>L’initialisation et lecture

Lecture audio avec `MediaPlayer` nécessite la séquence suivante :

1. Instanciez un nouvel [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) objet.

1. Configurer le fichier à lire par le biais de la [SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/) (méthode).

1. Appelez le [préparation](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/) méthode pour initialiser le lecteur.

1. Appelez le [Démarrer](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/) méthode pour démarrer la lecture audio.


L’exemple de code ci-dessous illustre cette utilisation :

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


### <a name="suspending-and-resuming-playback"></a>Suspension et reprise de la lecture

La lecture peut être suspendue en appelant le [Pause](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) méthode :

```csharp
player.Pause();
```

Pour reprendre la lecture en pause, appelez le [Démarrer](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) (méthode).
Cela va reprendre à partir de l’emplacement en pause dans la lecture :

```csharp
player.Start();
```

Appel de la [arrêter](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) méthode sur le lecteur termine une lecture en cours :

```csharp
player.Stop();
```

Lorsque le lecteur n’est plus nécessaire, les ressources doivent être libérées en appelant le [version](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) méthode :

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>À l’aide de la classe MediaRecorder pour enregistrer l’Audio

La conséquence `MediaPlayer` pour l’enregistrement audio dans Android est le [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) classe. Comme le `MediaPlayer`, il est dépendant de l’état et passe par plusieurs États pour arriver au stade où il peut démarrer l’enregistrement. Pour enregistrer le contenu audio, le `RECORD_AUDIO` autorisation doit être définie. Pour obtenir des instructions sur la définition d’application les autorisations consultez [utilisation AndroidManifest.xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>L’initialisation et d’enregistrement

Enregistrement audio avec la `MediaRecorder` implique les étapes suivantes :

1. Instanciez un nouvel [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) objet.

2. Spécifier quel périphérique matériel à utiliser pour capturer l’entrée audio via le [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) (méthode).

3. Définir le format audio du fichier de sortie en utilisant le [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) (méthode). Pour obtenir la liste de types audio pris en charge, consultez [Android pris en charge des Formats Media](https://developer.android.com/guide/appendix/media-formats.html).

4. Appelez le [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) méthode pour définir le type d’encodage de l’audio.

5. Appelez le [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) méthode pour spécifier le nom du fichier de sortie écrite dans les données audio.

6. Appelez le [préparation](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) méthode pour initialiser l’enregistreur.

7. Appelez le [Démarrer](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) méthode pour commencer l’enregistrement.


L’exemple de code suivant illustre cette séquence :

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

Pour arrêter l’enregistrement, appelez le `Stop` méthode sur le `MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Nettoyage

Une fois le `MediaRecorder` a été arrêté, appelez le [réinitialiser](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) méthode mets sauvegarder dans son état d’inactivité :

```csharp
recorder.Reset();
```

Lorsque le `MediaRecorder` est devenu inutile, ses ressources doivent être libérées en appelant le [version](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) (méthode) :

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Gestion des Notifications Audio



### <a name="the-audiomanager-class"></a>La classe AudioManager

Le [AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) classe fournit l’accès à des notifications audio qui permettent aux applications de savoir quand les événements audio se produisent. Ce service permet également d’accéder à d’autres fonctionnalités audio, telles que contrôle de mode de volume et de la sonnerie. Le `AudioManager` permet à une application gérer les notifications audio pour contrôler la lecture audio.



### <a name="managing-audio-focus"></a>Gestion du Focus Audio

Les ressources audio de l’appareil (le lecteur intégré et enregistreur) sont partagées par toutes les applications en cours d’exécution.

Conceptuellement, cela revient à des applications sur un ordinateur de bureau où une seule application a le focus clavier : après avoir sélectionné une des applications en cours d’exécution par un clic de la souris, l’entrée au clavier va uniquement à cette application.

Focus audio est une idée similaire et empêche plusieurs applications de lire ou de l’enregistrement audio en même temps. Il est plus complexe que le focus clavier, car il est volontaire &ndash; l’application peut ignorer le fait que sa ne pas actuellement le focus audio et lire quel que soit &ndash; et parce qu’il existe différents types de focus audio qui peut être demandé. Par exemple, si le demandeur est prévu uniquement pour la lecture audio pour une durée très courte, il peut demander le focus temporaire.

Focus audio peut être accordé immédiatement, ou initialement refusé et accordé ultérieurement. Par exemple, si un application demandes audio le focus lors d’un appel téléphonique, celles-ci seront refusées, mais le focus peut également bénéficier d’une fois que l’appel est terminé. Dans ce cas, un écouteur est inscrit afin de réagir en conséquence si le focus audio n’est pas possible. Demande de focus audio est utilisé pour déterminer si elle est OK pour lire ou enregistrer l’audio.

Pour plus d’informations sur le focus audio, consultez [la gestion de Focus Audio](https://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>L’inscription du rappel au focus Audio

L’inscription du `FocusChangeListener` rappel à partir de la `IOnAudioChangeListener` est un élément important d’obtenir et de libérer le focus audio. Il s’agit, car l’octroi de focus audio peut être différée jusqu'à une date ultérieure. Par exemple, une application peut demander à écouter de la musique lorsqu’il est un appel téléphonique en cours. Focus audio ne pas être accordé jusqu'à la fin de l’appel téléphonique.

Pour cette raison, l’objet de rappel est passé en tant que paramètre dans le `GetAudioFocus` méthode de la `AudioManager`, et il est cet appel qui inscrit le rappel. Si le focus audio est initialement refusé et accordé ultérieurement, l’application est informée en appelant `OnAudioFocusChange` sur le rappel. La même méthode est utilisée pour indiquer que le focus audio est en cours révoquée à l’application.

Lorsque l’application a terminé d’utiliser les ressources audio, il appelle le `AbandonFocus` méthode de la `AudioManager`et le transmet à nouveau dans le rappel. Cela annule l’inscription du rappel et libère les ressources audio, afin que les autres applications peuvent obtenir le focus audio.



#### <a name="requesting-audio-focus"></a>Demande de Focus Audio

Les étapes requises pour demander les ressources audio de l’appareil sont les suivantes :

1.  Obtenir un handle vers la `AudioManager` service système.

2.  Créez une instance de la classe de rappel.

3.  Demander les ressources audio de l’appareil en appelant le `RequestAudioFocus` méthode sur le `AudioManager` . Les paramètres sont l’objet de rappel, le type de flux (musique, appel vocal, anneau, etc.) et le type de l’accès demandé à droite (les ressources audio peuvent être demandées momentanément ou pendant une durée indéterminée, par exemple).

4.  Si la demande est accordée, le `playMusic` méthode est appelée immédiatement, et l’audio commence à lire.

5.  Si la demande est refusée, aucune action supplémentaire n’est effectuée. Dans ce cas, l’audio tiendra uniquement si la demande n’est accordée à une date ultérieure.


L’exemple de code ci-dessous illustre ces étapes :

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


#### <a name="releasing-audio-focus"></a>Libérer le Focus Audio

Lorsque la lecture de la piste est terminée, le `AbandonFocus` méthode sur `AudioManager` est appelé. Cela permet à une autre application obtenir les ressources audio de l’appareil. Autres applications recevront une notification de ce changement de focus audio si elles ont inscrit leurs propres écouteurs.


## <a name="low-level-audio-api"></a>API Audio de niveau faible

L’API de bas niveau audio fournissent un plus grand contrôle sur la lecture et l’enregistrement car elles interagissent directement avec les mémoires tampons au lieu d’utiliser l’URI de fichiers audio. Il existe certains scénarios où cette approche est préférable. Ces scénarios incluent :

1.  Lors de la lecture à partir de fichiers audio chiffrés.

2.  Lors de la lecture d’une succession de courts clips.

3.  Flux audio.


### <a name="audiotrack-class"></a>Classe de AudioTrack

Le [AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) classe utilise l’API de bas niveau audio pour l’enregistrement et est l’équivalent de bas niveau de la `MediaPlayer` classe.


#### <a name="initializing-and-playing"></a>L’initialisation et lecture

Pour lire le fichier audio, une nouvelle instance de `AudioTrack` doit être instanciée. La liste d’arguments passé dans le [constructeur](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) spécifie comment lire l’échantillon audio contenue dans la mémoire tampon. Les arguments sont :

1.  Stream type &ndash; vocale, sonnerie, musique, système ou alarme.

2.  Fréquence &ndash; le taux d’échantillonnage exprimé en Hz.

3.  Configuration de canal &ndash; Mono ou stéréo.

4.  Format audio &ndash; 8 bits ou codage 16 bits.

5.  Taille de mémoire tampon &ndash; en octets.

6.  Mode mémoire tampon &ndash; statique ou de diffusion en continu.


Après la construction, le [lire](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) méthode de `AudioTrack` est appelé, pour configurer de manière à commencer la lecture. Écriture de la mémoire tampon audio à la `AudioTrack` démarre la lecture :

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


#### <a name="pausing-and-stopping-the-playback"></a>Suspension et l’arrêt de la lecture

Appelez le [suspendre](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/) méthode pour suspendre la lecture :

```csharp
audioTrack.Pause();
```

Appel de la [arrêter](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) méthode se termine la lecture définitivement :

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Nettoyage

Lorsque le `AudioTrack` est devenu inutile, ses ressources doivent être libérées en appelant [version](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>La classe AudioRecord

Le [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) classe est l’équivalent de `AudioTrack` sur le côté de l’enregistrement. Comme `AudioTrack`, il utilise des mémoires tampons directement, à la place de fichiers et d’URI. Il requiert que le `RECORD_AUDIO` jeu d’autorisations dans le manifeste.


#### <a name="initializing-and-recording"></a>L’initialisation et d’enregistrement

La première étape consiste à construire une nouvelle [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) objet. La liste d’arguments passé dans le [constructeur](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) fournit toutes les informations requises pour l’enregistrement. Contrairement à dans `AudioTrack`, où les arguments sont en grande partie des énumérations, les arguments équivalents dans `AudioRecord` sont des entiers. Elles incluent notamment :

1.  Source de d’entrée audio matériel tels que microphone.

2.  Stream type &ndash; vocale, sonnerie, musique, système ou alarme.

3.  Fréquence &ndash; le taux d’échantillonnage exprimé en Hz.

4.  Configuration de canal &ndash; Mono ou stéréo.

5.  Format audio &ndash; 8 bits ou codage 16 bits.

6.  Octets de taille de mémoire tampon


Une fois le `AudioRecord` est construit, ses [StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) méthode est appelée. Il est maintenant prêt à commencer l’enregistrement. Le `AudioRecord` en permanence lit la mémoire tampon audio d’entrée et écrit des entrées dans un fichier audio.

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

Appel de la [arrêter](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) méthode met fin à l’enregistrement :

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Nettoyage

Lorsque le `AudioRecord` objet n’est plus nécessaire, appeler ses [version](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) méthode libère toutes les ressources associées :

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Récapitulatif

Le système d’exploitation Android fournit un framework puissant pour lecture, l’enregistrement et la gestion des données audio. Cet article a abordé comment lire et enregistrer l’audio à l’aide de la vue `MediaPlayer` et `MediaRecorder` classes. Ensuite, il a expliqué comment utiliser des notifications audio pour partager les ressources audio de l’appareil entre les différentes applications. Enfin, il traitées comment pour la lecture et l’enregistrement audio à l’aide de l’API de bas niveau, lequel interagir directement avec les mémoires tampons.


## <a name="related-links"></a>Liens associés

- [Utilisation avec Audio (exemple)](https://developer.xamarin.com/samples/monodroid/Example_WorkingWithAudio/)
- [Media Player](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Enregistreur de support](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Gestionnaire audio](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Piste audio](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Enregistreur audio](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
