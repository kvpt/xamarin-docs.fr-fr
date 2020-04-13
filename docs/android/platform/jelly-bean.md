---
title: Caractéristiques de Jelly Bean
description: 'Ce document fournira un aperçu de haut niveau des nouvelles fonctionnalités pour les développeurs qui ont été introduits dans Android 4.1. Ces fonctionnalités comprennent : notifications améliorées, mises à jour d’Android Beam pour partager de grands fichiers, mises à jour multimédia, découverte de réseau peer-to-peer, animations, nouvelles autorisations.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a638ccf7810c737faaeded7fcc98fcf657c85288
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027211"
---
# <a name="jelly-bean-features"></a>Caractéristiques de Jelly Bean

_Ce document fournira un aperçu de haut niveau des nouvelles fonctionnalités pour les développeurs qui ont été introduits dans Android 4.1. Ces fonctionnalités comprennent : notifications améliorées, mises à jour d’Android Beam pour partager de grands fichiers, mises à jour multimédia, découverte de réseau peer-to-peer, animations, nouvelles autorisations._

## <a name="overview"></a>Vue d’ensemble

Android 4.1 (API Level 16), également connu sous le nom "Jelly Bean", a été publié le 9 Juillet 2012. Cet article fournira une introduction de haut niveau à certaines des nouvelles fonctionnalités dans Android 4.1 pour les développeurs utilisant Xamarin.Android. Certaines de ces nouvelles fonctionnalités introduites sont des améliorations aux animations pour le lancement d’une activité, de nouveaux sons pour une caméra, et un meilleur soutien pour la navigation de pile d’applications. Il est maintenant possible de couper et coller avec des intentions.

La stabilité des applications Android est améliorée avec la capacité d’isoler la dépendance à l’égard des fournisseurs de contenu instables. Les services peuvent également être isolés de sorte qu’ils ne soient accessibles que par l’activité qui les a lancés.

Un support a été ajouté pour la découverte de services réseau à l’aide de services basés sur Bonjour, UPnP ou multicastS DNS. Il est maintenant possible pour les notifications plus riches qui ont formaté du texte, des boutons d’action et de grandes images.

Enfin, plusieurs nouvelles autorisations ont été ajoutées dans Android 4.1.

## <a name="requirements"></a>Spécifications

Pour développer des applications Xamarin.Android en utilisant Jelly Bean nécessite Xamarin.Android 4.2.6 ou plus et Android 4.1 (niveau API 16) être installé via le gestionnaire Android SDK comme indiqué dans la capture d’écran suivante:

[![Sélection d’Android 4.1 dans le gestionnaire Android SDK](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>What's New

### <a name="animations"></a>Animations

Les activités peuvent être lancées en utilisant des `ActivityOptions` animations de zoom ou des animations personnalisées en utilisant la classe. Les nouvelles méthodes suivantes sont fournies pour soutenir ces animations :

- `MakeScaleUpAnimation`Cela créera une animation qui éridira une fenêtre d’activité à partir d’une position de départ et de la taille sur l’écran.
- `MakeThumbnailScaleUpAnimation`Cela créera une animation qui s’érifitera à partir d’une image miniature à partir d’une position spécifiée sur l’écran.
- `MakeCustomAnimation`Cela crée une animation à partir de ressources dans l’application. Il y a une animation pour quand l’activité s’ouvre et une autre pour quand l’activité s’arrête.

La `TimeAnimator` nouvelle classe `TimeAnimator.ITimeListener` fournit une interface qui peut notifier une application chaque fois qu’un cadre change dans une animation. Par exemple, considérez `TimeAnimator.ITimeListener`la mise en œuvre suivante de :

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

Et maintenant, pour utiliser la `TimeAnimator` classe, une instance de est créé, et l’auditeur est fixé:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Comme `TimeAnimator` l’instance est en `ITimeAnimator.ITimeListener`cours d’exécution, il invoquera , qui enregistrera ensuite la durée de l’animateur a été en cours d’exécution et combien de temps il a été depuis la dernière fois que la méthode a été invoquée.

### <a name="application-stack-navigation"></a>Application Stack Navigation

Android 4.1 améliore sur la navigation pile d’application qui a été introduit dans Android 3.0. En `ParentName` spécifiant `ActivityAttribute`la propriété de la , Android peut ouvrir l’activité parent appropriée lorsque l’utilisateur appuie `ParentName` sur le bouton [Up](https://developer.android.com/design/patterns/navigation.html#up-vs-back) sur la barre d’action - Android instantanément l’activité spécifiée par la propriété. Cela permet aux applications de préserver la hiérarchie des activités qui font une tâche donnée.

Pour la plupart `ParentName` des applications de réglage de l’activité est suffisamment d’informations pour Android pour fournir le comportement correct pour naviguer dans la pile d’applications; Android va synthétiser la pile arrière nécessaire en créant une série d’intentions pour chaque activité parente. Cependant, parce qu’il s’agit d’une pile d’application artificielle, chaque activité synthétique n’aura pas l’état sauvé qu’une activité naturelle aurait. Pour fournir un état sauvé à une activité `OnPrepareNavigationUpTaskStack` parente synthétique, une activité peut remplacer la méthode. Cette méthode `TaskStackBuilder` reçoit une instance qui aura une collection d’objets d’intention qu’Android utilisera pour créer la pile arrière. L’activité peut modifier ces intentions de sorte que, au fur et à mesure que l’activité synthétique est créée, elle recevra les informations d’état appropriées.

Pour des scénarios plus complexes, il existe de nouvelles méthodes sur la classe d’activité qui peuvent être utilisées pour gérer le comportement de la navigation Up et construire la pile arrière:

- `OnNavigateUp`En prépondant cette méthode, il est possible d’effectuer une action personnalisée lorsque le bouton **Up** est appuyé.
- `NavigateUpTo`L’appel de cette méthode entraînera la navigation de l’application de l’activité actuelle à l’activité spécifiée par une intention donnée.
- `ParentActivityIntent`Ceci est utilisé pour obtenir une intention qui lancera l’activité parente de l’activité actuelle.
- `ShouldUpRecreateTask`Cette méthode est utilisée pour interroger si la pile arrière synthétique doit être créée pour naviguer jusqu’à une activité parente. Retourne `true` si la pile synthétique doit être créée. 
- `FinishAffinity`L’appel de cette méthode permettra de terminer l’activité actuelle et toutes les activités en dessous dans la tâche actuelle qui ont la même affinité tâche.
- `OnCreateNavigateUpTaskStack`Cette méthode est remplacée lorsqu’il est nécessaire d’avoir un contrôle complet sur la façon dont la pile synthétique est créée.

### <a name="camera"></a>Caméra

Il ya une `Camera.IAutoFocusMoveCallback`nouvelle interface, qui peut être utilisé pour détecter quand la mise au point automatique a commencé ou cessé de se déplacer. Un exemple de cette nouvelle interface peut être vu dans l’extrait suivant:

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

La nouvelle `MediaActionSound` classe fournit un ensemble d’API pour produire des sons pour les différentes actions médiatiques. Il ya plusieurs actions qui peuvent se produire avec `Android.Media.MediaActionSoundType`une caméra, ceux-ci sont définis par l’enum:

- `MediaActionSoundType.FocusComplete`Ce son qui est joué lors de la mise au point est terminé.
- `MediaActionSoundType.ShutterClick`Ce son sera joué lorsqu’une image fixe est prise.
- `MediaActionSoundType.StartVideoRecording`Ce son est utilisé indiquent le début de l’enregistrement vidéo.
- `MediaActionSoundType.StopVideoRecording`Ce son sera joué pour indiquer la fin de l’enregistrement vidéo.

Un exemple de la `MediaActionSound` façon d’utiliser la classe peut être vu dans l’extrait suivant:

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```

### <a name="connectivity"></a>Connectivité

#### <a name="android-beam"></a>Android Beam

Android Beam est une technologie basée sur NFC qui permet à deux appareils Android de communiquer entre eux. Android 4.1 fournit un meilleur support pour le transfert de fichiers volumineux. Lors de l’utilisation de la nouvelle méthode `NfcAdapter.SetBeamPushUris()` Android va passer d’autres mécanismes de transport (tels que Bluetooth) pour atteindre une vitesse de transfert rapide.

#### <a name="network-services-discovery"></a>Découverte des services réseau

Android 4.1 contient de nouvelles API pour la découverte de services multicast basée sur DNS.
Cela permet à une application de détecter et de connecter par Wi-Fi à d’autres appareils tels que les imprimantes, les caméras et les appareils multimédias. Ces nouvelles API sont `Android.Net.Nsd` dans le paquet.

Pour créer un service qui peut être `NsdServiceInfo` consommé par d’autres services, la classe est utilisée pour créer un objet qui définira les propriétés d’un service. Cet objet est `NsdManager.RegisterService()` ensuite fourni avec `NsdManager.ResolveListener`une mise en œuvre de . Les mises `NsdManager.ResolveListener` en œuvre sont utilisées pour aviser un enregistrement réussi et pour non enregistrer le service.

Découvrir les services sur le `Nsd.DiscoveryListener` réseau, `NsdManager.discoverServices()`et la mise en œuvre de passa par .

#### <a name="network-usage"></a>Utilisation du réseau

Une nouvelle `ConnectivityManager.IsActiveNetworkMetered` méthode, permet à un appareil de vérifier s’il est connecté à un réseau mesuré. Cette méthode peut être utilisée pour aider à gérer l’utilisation des données en informant avec précision les utilisateurs qu’il pourrait y avoir des frais coûteux pour les opérations de données.

#### <a name="wifi-direct-service-discovery"></a>Découverte du service direct WiFi

La `WifiP2pManager` classe a été introduite dans Android 4.0 pour prendre en charge *zeroconf*. Zeroconf (réseau de configuration zéro) est un ensemble de techniques qui permettent aux appareils (ordinateurs, imprimantes, téléphones) de se connecter automatiquement aux réseaux, avec l’intervention d’opérateurs de réseaux humains ou de serveurs de configuration spéciaux.

Dans Jelly Bean, `WifiP2pManager` peut découvrir les appareils à proximité en utilisant *Bonjour* ou *Upnp*. Bonjour est la mise en œuvre de zeroconf par Apple. Upnp est un ensemble de protocoles de réseautage qui prend également en charge zeroconf. Les méthodes suivantes `WiFiP2pManager` ont ajouté à la pour soutenir la découverte du service Wi-Fi:

- `AddLocalService()`Cette méthode est utilisée annonce une application comme un service sur Wi-Fi pour la découverte par les pairs.
- `AddServiceRequest(`) Cette méthode consiste à envoyer une demande de découverte de service dans le cadre. Il est utilisé pour initialiser la découverte du service Wi-Fi.
- `SetDnsSdResponseListeners()`Cette méthode est utilisée pour enregistrer les rappels à invoquer lors de la réception d’une réponse aux demandes de découverte de Bonjour.
- `SetUpnpServiceResponseListener()`Cette méthode est utilisée pour enregistrer les rappels à invoquer lors de la réception d’une réponse aux demandes de découverte Upnp.

### <a name="content-providers"></a>Fournisseurs de contenu

La `ContentResolver` classe a reçu `AcquireUnstableContentProvider`une nouvelle méthode, . Cette méthode permet à une application d’acquérir un fournisseur de contenu « instable ». Normalement, lorsqu’une application acquiert un fournisseur de contenu, et que le fournisseur de contenu se bloque, l’application aussi. Avec cet appel de méthode, une application ne se bloque pas si le fournisseur de contenu se bloque. Au `Android.OS.DeadObjectionException` lieu de cela, sera jeté des appels sur le fournisseur de contenu pour informer une application que le fournisseur de contenu a disparu. Un fournisseur de contenu « instable » est utile lorsqu’il interagit avec les fournisseurs de contenu d’autres applications - il est moins probable que le code buggy d’une autre application affectera une autre application.

### <a name="copy-and-paste-with-intents"></a>Copie et pâte avec des intentions

La `Intent` classe peut `ClipData` maintenant avoir un `Intent.ClipData` objet qui lui est associé via la propriété. Cette méthode permet de transmettre des données supplémentaires du presse-papiers avec l’intention. Une instance `ClipData` de peut `ClipData.Item`contenir un ou plusieurs . `ClipData.Item`sont des éléments des types suivants :

- **Texte** - Il s’agit de n’importe quelle chaîne de texte, soit HTML ou toute chaîne dont le format est pris en charge par les travées intégrées de style Android.
- **Intention** - `Intent` Tout objet.
- **Uri** - Cela peut être n’importe quel URI, comme un signet HTTP ou l’URI à un fournisseur de contenu.

### <a name="isolated-services"></a>Services isolés

Un service isolé est un service qui fonctionne dans le cadre de son propre processus spécial et qui n’a pas d’autorisations. La seule communication avec le service est lors du démarrage du service et de la liaison à elle via l’API service. Il est possible de déclarer un service `IsolatedProcess="true"` aussi `ServiceAttribute` isolé en plaçant la propriété dans celui qui orne une classe de service.

### <a name="media"></a>Médias

La `Android.Media.MediaCodec` nouvelle classe fournit une API aux codecs des médias de bas niveau. Les applications peuvent interroger le système pour savoir quels codecs de bas niveau sont disponibles sur l’appareil.

Les `Android.Media.Audiofx.AudioEffect` nouvelles sous-classes ont été ajoutées pour prendre en charge le pré-traitement audio supplémentaire sur l’audio capturé :

- `Android.Media.Audiofx.AcousticEchoCanceler`Cette classe est utilisée pour le pré-traitement audio pour supprimer le signal d’une partie à distance d’un signal audio capturé. Par exemple, supprimer l’écho d’une application de communication vocale.
- `Android.Media.Audiofx.AutomaticGainControl`Cette classe est utilisée pour normaliser le signal capturé en stimulant ou en abaissant un signal d’entrée de sorte que le signal de sortie est constant.
- `Android.Media.Audiofx.NoiseSuppressor`Cette classe éliminera le bruit de fond du signal capturé.

Tous les appareils ne supporteront pas ces effets. La `AudioEffect.IsAvailable` méthode doit être appelée par une application pour voir si l’effet audio en question est pris en charge sur l’appareil exécutant l’application.

La `MediaPlayer` classe prend désormais en `SetNextMediaPlayer()` charge la lecture sans écart avec la méthode. Cette nouvelle méthode spécifie le prochain MediaPlayer à démarrer lorsque le lecteur multimédia actuel termine sa lecture.

Les nouvelles classes suivantes fournissent des mécanismes standard et l’interface utilisateur pour choisir où les médias seront joués :

- `MediaRouter`Cette classe permet aux applications de contrôler le routage des canaux multimédias d’un appareil à des haut-parleurs externes ou d’autres appareils.
- `MediaRouterActionProvider`et `MediaRouteButton` ces classes aident à fournir une interface utilisateur cohérente pour la sélection et la lecture des médias.

### <a name="notifications"></a>Notifications

Android 4.1 permet aux applications plus de flexibilité et de contrôle avec l’affichage des notifications. Les applications peuvent désormais afficher des notifications plus grandes et meilleures pour les utilisateurs. Une nouvelle `NotificationBuilder.SetStyle()` méthode, permet de définir l’un des trois nouveaux styles sur les notifications :

- `Notification.BigPictureStyle`Il s’agit d’une classe d’aide qui générera des notifications qui auront une image en eux. L’image suivante montre un exemple de notification avec une grande image :

 [![Exemple de capture d’écran d’une notification BigImageStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`Il s’agit d’une classe d’aide qui générera des notifications qui auront plusieurs lignes de texte, comme l’e-mail. Un exemple de ce nouveau style de notification peut être vu dans la capture d’écran suivante:

 [![Exemple de capture d’écran d’une notification BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`Il s’agit d’une classe d’aide qui générera des notifications qui contiennent une liste de chaînes, telles que des extraits d’un message électronique, comme indiqué dans cette capture d’écran:

 [![Exemple de capture d’écran d’une notification Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

Il est possible d’ajouter jusqu’à deux boutons d’action au bas d’un message de notification lorsque la notification utilise le style normal ou plus grand.
Un exemple de ceci peut être vu dans la capture d’écran suivante, où les boutons d’action sont visibles au bas de la notification :

 [![Exemple de capture d’écran des boutons d’action affichés ci-dessous un message de notification](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La `Notification` classe a reçu de nouvelles constantes qui permettent à un développeur de spécifier l’un des cinq niveaux prioritaires pour une notification. Ceux-ci peuvent être réglés sur une notification à l’aide de la `Priority` propriété.

### <a name="permissions"></a>Autorisations

Les nouvelles autorisations suivantes ont été ajoutées :

- `READ_EXTERNAL_STORAGE`- L’application nécessite l’accès à l’accès à un stockage externe uniquement. Actuellement, toutes les applications ont lu l’accès par défaut, mais les versions futures d’Android nécessiteront des applications explicitement demander l’accès lu.
- `READ_USER_DICTIONARY`- Permet un accès de lecture au dictionnaire de mots de l’utilisateur.
- `READ_CALL_LOG`- Permet à une demande d’obtenir des informations sur les appels entrants et sortants en lisant le journal d’appel.
- `WRITE_CALL_LOG`- Permet à une application d’écrire au journal d’appel sur le téléphone.
- `WRITE_USER_DICTIONARY`- Permet à une application d’écrire sur le dictionnaire de mots de l’utilisateur.

Un changement important `READ_EXTERNAL_STORAGE` à noter - actuellement cette autorisation est automatiquement accordée par Android. Les versions futures d’Android nécessiteront une demande pour demander cette autorisation avant d’obtenir la permission.

## <a name="summary"></a>Récapitulatif

Cet article a introduit quelques-uns des nouveaux API qui sont disponibles dans Android 4.1 (niveau API 16). Il a mis en évidence certains des changements pour les animations et l’animation du lancement d’une activité, et a introduit les nouvelles API pour la découverte réseau d’autres appareils en utilisant des protocoles tels que Bonjour ou UPnP. D’autres modifications à l’API ont également été mises en évidence, comme la capacité de couper et coller des données par des intentions, la possibilité d’utiliser des services isolés ou des fournisseurs de contenu « instables ».

Cet article a ensuite introduit les mises à jour des notifications, et discuté de certaines des nouvelles autorisations qui ont été introduites avec Android 4.1

## <a name="related-links"></a>Liens connexes

- [Exemple d’animation temporelle (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [Android 4.1 API](https://developer.android.com/about/versions/android-4.1.html)
- [Tâches et piles de dos](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navigation avec dos et haut](https://developer.android.com/design/patterns/navigation.html)
