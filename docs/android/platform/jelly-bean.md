---
title: Fonctionnalités de la gelée
description: 'Ce document fournit un aperçu général des nouvelles fonctionnalités pour les développeurs qui ont été introduits dans Android 4,1. Parmi ces fonctionnalités, citons les suivantes : notifications améliorées, mises à jour d’Android Beam pour partager des fichiers volumineux, mises à jour des éléments multimédias, découverte du réseau pair à pair, animations et nouvelles autorisations.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 55969993a4cb3755f5a26e681ae21bf993307a0a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456884"
---
# <a name="jelly-bean-features"></a>Fonctionnalités de la gelée

_Ce document fournit un aperçu général des nouvelles fonctionnalités pour les développeurs qui ont été introduits dans Android 4,1. Parmi ces fonctionnalités, citons les suivantes : notifications améliorées, mises à jour d’Android Beam pour partager des fichiers volumineux, mises à jour des éléments multimédias, découverte du réseau pair à pair, animations et nouvelles autorisations._

## <a name="overview"></a>Vue d’ensemble

Android 4,1 (API de niveau 16), également connu sous le nom de « composant Bean gelé », a été publié le 9 juillet 2012. Cet article fournit une présentation générale de certaines des nouvelles fonctionnalités d’Android 4,1 pour les développeurs qui utilisent Xamarin. Android. Certaines de ces nouvelles fonctionnalités introduites sont des améliorations apportées aux animations pour lancer une activité, de nouveaux sons pour un appareil photo et une meilleure prise en charge de la navigation dans la pile d’applications. Il est désormais possible de couper et coller avec des intentions.

La stabilité des applications Android est améliorée grâce à la possibilité d’isoler la dépendance vis-à-vis des fournisseurs de contenu instables. Les services peuvent également être isolés afin qu’ils soient accessibles uniquement par l’activité qui les a démarrés.

Une prise en charge a été ajoutée pour la découverte de service réseau à l’aide de services basés sur Bonjour, UPnP ou DNS de multidiffusion. Il est désormais possible d’obtenir des notifications plus riches qui contiennent du texte mis en forme, des boutons d’action et des images volumineuses.

Enfin, plusieurs nouvelles autorisations ont été ajoutées dans Android 4,1.

## <a name="requirements"></a>Configuration requise

Pour développer des applications Xamarin. Android à l’aide de gelée, vous devez installer Xamarin. Android 4.2.6 ou version ultérieure et Android 4,1 (niveau d’API 16) via le gestionnaire de Android SDK, comme illustré dans la capture d’écran suivante :

[![Sélection d’Android 4,1 dans le gestionnaire de Android SDK](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>What's New

### <a name="animations"></a>Animations

Les activités peuvent être lancées à l’aide d’animations de zoom ou d’animations personnalisées à l’aide de la `ActivityOptions` classe. Les nouvelles méthodes suivantes sont fournies pour prendre en charge ces animations :

- `MakeScaleUpAnimation` : Cette opération crée une animation qui met à l’échelle une fenêtre d’activité à partir d’une position de départ et d’une taille à l’écran.
- `MakeThumbnailScaleUpAnimation` : Crée une animation qui se met à l’échelle à partir d’une image miniature de la position spécifiée à l’écran.
- `MakeCustomAnimation` : Crée une animation à partir des ressources de l’application. Il existe une animation pour le moment où l’activité s’ouvre et une autre pour lorsque l’activité s’arrête.

La nouvelle `TimeAnimator` classe fournit une interface `TimeAnimator.ITimeListener` qui peut notifier une application chaque fois qu’un frame change dans une animation. Par exemple, considérez l’implémentation suivante de `TimeAnimator.ITimeListener` :

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

Et maintenant, pour utiliser la classe, une instance de `TimeAnimator` est créée et l’écouteur est défini :

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Lorsque l' `TimeAnimator` instance est en cours d’exécution, elle appelle `ITimeAnimator.ITimeListener` , qui consigne ensuite la durée d’exécution de l’animateur et le temps écoulé depuis la dernière fois que la méthode a été appelée.

### <a name="application-stack-navigation"></a>Navigation dans la pile d’applications

Android 4,1 améliore la navigation dans la pile d’applications qui a été introduite dans Android 3,0. En spécifiant la `ParentName` propriété du `ActivityAttribute` , Android peut ouvrir l’activité parente appropriée lorsque l’utilisateur appuie sur le [bouton haut](https://developer.android.com/design/patterns/navigation.html#up-vs-back) de la barre d’action. Android instancie l’activité spécifiée par la `ParentName` propriété. Cela permet aux applications de conserver la hiérarchie des activités qui effectuent une tâche donnée.

Pour la plupart des applications `ParentName` , le paramètre sur l’activité est suffisamment d’informations pour que Android fournisse le comportement correct pour naviguer dans la pile d’applications. Android synthétise la pile de retour nécessaire en créant une série d’intentions pour chaque activité parent. Toutefois, étant donné qu’il s’agit d’une pile d’applications artificielles, chaque activité synthétique n’aura pas l’état enregistré qu’une activité naturelle aurait. Pour fournir l’état enregistré à une activité parente synthétique, une activité peut substituer la `OnPrepareNavigationUpTaskStack` méthode. Cette méthode reçoit une `TaskStackBuilder` instance qui aura une collection d’objets d’intention qu’Android utilisera pour créer la pile de retour. L’activité peut modifier ces intentions afin que, lors de la création de l’activité synthétique, les informations d’État appropriées soient reçues.

Pour les scénarios plus complexes, il existe de nouvelles méthodes sur la classe Activity qui peuvent être utilisées pour gérer le comportement de la navigation vers le haut et construire la pile Back :

- `OnNavigateUp` : En substituant cette méthode, il est possible d’exécuter une action personnalisée quand vous appuyez sur le bouton **haut** .
- `NavigateUpTo` : L’appel de cette méthode permet à l’application de naviguer de l’activité actuelle à l’activité spécifiée par une intention donnée.
- `ParentActivityIntent` : Permet d’obtenir une intention qui lance l’activité parente de l’activité en cours.
- `ShouldUpRecreateTask` : Cette méthode est utilisée pour interroger si la pile de retour synthétique doit être créée pour accéder à une activité parente. Retourne  `true` si la pile synthétique doit être créée. 
- `FinishAffinity` : L’appel de cette méthode termine l’activité en cours et toutes les activités situées au-dessous de celle-ci dans la tâche actuelle qui ont la même affinité de tâche.
- `OnCreateNavigateUpTaskStack` : Cette méthode est substituée lorsqu’il est nécessaire de disposer d’un contrôle total sur la façon dont la pile synthétique est créée.

### <a name="camera"></a>Appareil photo

Il existe une nouvelle interface, `Camera.IAutoFocusMoveCallback` , qui peut être utilisée pour détecter quand le focus automatique a démarré ou a cessé de se déplacer. Vous pouvez voir un exemple de cette nouvelle interface dans l’extrait de code suivant :

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

La nouvelle classe `MediaActionSound` fournit un ensemble d’API pour produire des sons pour les différentes actions de média. Il existe plusieurs actions qui peuvent se produire avec une caméra, celles-ci sont définies par l’énumération `Android.Media.MediaActionSoundType` :

- `MediaActionSoundType.FocusComplete` : Ce son qui est lu lorsque le focus est terminé.
- `MediaActionSoundType.ShutterClick` : Ce son est lu quand une image continue est prise.
- `MediaActionSoundType.StartVideoRecording` : Ce son est utilisé pour indiquer le début de l’enregistrement vidéo.
- `MediaActionSoundType.StopVideoRecording` : Ce son est lu pour indiquer la fin de l’enregistrement vidéo.

Vous pouvez voir un exemple d’utilisation de la `MediaActionSound` classe dans l’extrait de code suivant :

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

Android Beam est une technologie NFC qui permet à deux appareils Android de communiquer entre eux. Android 4,1 offre une meilleure prise en charge du transfert de fichiers volumineux. Lorsque vous utilisez la nouvelle méthode `NfcAdapter.SetBeamPushUris()` , Android bascule entre les mécanismes de transport alternatifs (tels que Bluetooth) pour obtenir une vitesse de transfert rapide.

#### <a name="network-services-discovery"></a>Découverte de Network Services

Android 4,1 contient de nouvelles API pour la détection des services basés sur DNS de multidiffusion.
Cela permet à une application de détecter et de se connecter via le Wi-Fi à d’autres appareils, tels que des imprimantes, des appareils photo et des appareils multimédias. Ces nouvelles API se trouvent dans le `Android.Net.Nsd` Package.

Pour créer un service qui peut être consommé par d’autres services, la `NsdServiceInfo` classe est utilisée pour créer un objet qui définit les propriétés d’un service. Cet objet est ensuite fourni `NsdManager.RegisterService()` avec une implémentation de `NsdManager.ResolveListener` . Les implémentations de `NsdManager.ResolveListener` sont utilisées pour notifier une inscription réussie et pour annuler l’inscription du service.

Pour découvrir les services sur le réseau et l’implémentation de `Nsd.DiscoveryListener` passé à `NsdManager.discoverServices()` .

#### <a name="network-usage"></a>Utilisation du réseau

Une nouvelle méthode `ConnectivityManager.IsActiveNetworkMetered` permet à un appareil de vérifier s’il est connecté à un réseau contrôlé. Cette méthode peut être utilisée pour faciliter la gestion de l’utilisation des données en informant les utilisateurs qu’il peut y avoir des frais coûteux pour les opérations de données.

#### <a name="wifi-direct-service-discovery"></a>Détection du service Wi-Fi direct

La `WifiP2pManager` classe a été introduite dans Android 4,0 pour prendre en charge *Zeroconf*. Zeroconf (mise en réseau de configuration nulle) est un ensemble de techniques qui permet aux appareils (ordinateurs, imprimantes, téléphones) de se connecter automatiquement aux réseaux, avec l’intervention d’opérateurs de réseau humain ou de serveurs de configuration spéciaux.

Dans une haricot de gelée, `WifiP2pManager` peut détecter des appareils à proximité à l’aide de *Bonjour* ou de *UPnP*. Bonjour est l’implémentation d’Apple de Zeroconf. UPnP est un ensemble de protocoles réseau qui prend également en charge Zeroconf. Les méthodes suivantes ont été ajoutées à `WiFiP2pManager` pour prendre en charge la découverte de service Wi-Fi :

- `AddLocalService()` : Cette méthode est utilisée pour annoncer une application en tant que service sur le Wi-Fi pour la découverte par des pairs.
- `AddServiceRequest(` ) : Cette méthode permet d’envoyer une demande de découverte de service à l’infrastructure. Elle est utilisée pour initialiser la détection du service Wi-Fi.
- `SetDnsSdResponseListeners()` : Cette méthode est utilisée pour enregistrer les rappels à appeler lors de la réception d’une réponse aux demandes de découverte à partir de Bonjour.
- `SetUpnpServiceResponseListener()` : Cette méthode est utilisée pour enregistrer les rappels à appeler lors de la réception d’une réponse aux requêtes de découverte UPnP.

### <a name="content-providers"></a>Fournisseurs de contenu

La `ContentResolver` classe a reçu une nouvelle méthode, `AcquireUnstableContentProvider` . Cette méthode permet à une application d’obtenir un fournisseur de contenu « instable ». Normalement, lorsqu’une application acquiert un fournisseur de contenu, et que ce fournisseur de contenu se bloque, l’application est donc. Avec cet appel de méthode, une application ne se bloquera pas en cas de blocage du fournisseur de contenu. Au lieu `Android.OS.DeadObjectionException` de cela, sera levée à partir des appels sur le fournisseur de contenu pour informer une application que le fournisseur de contenu a disparu. Un fournisseur de contenu « instable » est utile lors de l’interaction avec les fournisseurs de contenu à partir d’autres applications : il est moins probable que le code de bogue d’une autre application affecte une autre application.

### <a name="copy-and-paste-with-intents"></a>Copier et coller avec les intentions

`Intent`Un objet est maintenant associé à la classe `ClipData` via la `Intent.ClipData` propriété. Cette méthode permet de transmettre des données supplémentaires du presse-papiers avec l’intention. Une instance de `ClipData` peut contenir un ou plusieurs `ClipData.Item` . `ClipData.Item`les sont des éléments des types suivants :

- **Text** : il s’agit d’une chaîne de texte, html ou toute chaîne dont le format est pris en charge par les étendues de style Android intégrées.
- **Intent** : tout  `Intent` objet.
- **URI** : il peut s’agir de n’importe quel URI, tel qu’un signet http ou l’URI d’un fournisseur de contenu.

### <a name="isolated-services"></a>Services isolés

Un service isolé est un service qui s’exécute sous son propre processus spécial et ne dispose d’aucune autorisation propre. La seule communication avec le service est lors du démarrage du service et de sa liaison via l’API de service. Il est possible de déclarer un service comme isolé en définissant la propriété `IsolatedProcess="true"` dans `ServiceAttribute` qui orne une classe de service.

### <a name="media"></a>Média

La nouvelle `Android.Media.MediaCodec` classe fournit une API aux codecs de média de bas niveau. Les applications peuvent interroger le système pour connaître les codecs de bas niveau disponibles sur l’appareil.

Les nouvelles `Android.Media.Audiofx.AudioEffect` sous-classes ont été ajoutées pour prendre en charge un prétraitement audio supplémentaire sur le son capturé :

- `Android.Media.Audiofx.AcousticEchoCanceler` : Cette classe est utilisée pour le pré-traitement audio pour supprimer le signal d’un tiers distant d’un signal audio capturé. Par exemple, la suppression de l’écho d’une application de communication vocale.
- `Android.Media.Audiofx.AutomaticGainControl` : Cette classe est utilisée pour normaliser le signal capturé en amplifiant ou en abaissant un signal d’entrée afin que le signal de sortie soit constant.
- `Android.Media.Audiofx.NoiseSuppressor` : Cette classe supprime le bruit de fond du signal capturé.

Tous les appareils ne prennent pas en charge ces effets. La méthode `AudioEffect.IsAvailable` doit être appelée par une application pour voir si l’effet audio en question est pris en charge sur l’appareil exécutant l’application.

La `MediaPlayer` classe prend désormais en charge la lecture ininterrompue avec la `SetNextMediaPlayer()` méthode. Cette nouvelle méthode spécifie le prochain MediaPlayer à démarrer lorsque le lecteur multimédia actuel termine sa lecture.

Les nouvelles classes suivantes fournissent des mécanismes standard et une interface utilisateur pour sélectionner l’emplacement de lecture des médias :

- `MediaRouter` : Cette classe permet aux applications de contrôler le routage des canaux multimédias d’un appareil vers des haut-parleurs externes ou d’autres appareils.
- `MediaRouterActionProvider` et  `MediaRouteButton` : ces classes aident à fournir une interface utilisateur cohérente pour la sélection et la diffusion de médias.

### <a name="notifications"></a>Notifications

Android 4,1 offre aux applications plus de souplesse et de contrôle sur l’affichage des notifications. Les applications peuvent désormais afficher des notifications plus volumineuses et plus performantes pour les utilisateurs. Une nouvelle méthode `NotificationBuilder.SetStyle()` permet de définir l’un des trois nouveaux styles sur les notifications :

- `Notification.BigPictureStyle` : Il s’agit d’une classe d’assistance qui génèrera des notifications qui comporteront une image. L’illustration suivante montre un exemple de notification avec une image Big :

 [![Exemple de capture d’écran d’une notification BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle` : Il s’agit d’une classe d’assistance qui génèrera des notifications qui auront plusieurs lignes de texte, telles que le courrier électronique. Vous pouvez voir un exemple de ce nouveau style de notification dans la capture d’écran suivante :

 [![Exemple de capture d’écran d’une notification BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle` : Il s’agit d’une classe d’assistance qui génère des notifications contenant une liste de chaînes, telles que des extraits de code à partir d’un message électronique, comme illustré dans cette capture d’écran :

 [![Exemple de capture d’écran d’une notification. InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

Il est possible d’ajouter jusqu’à deux boutons d’action en bas d’un message de notification lorsque la notification utilise le style normal ou plus grand.
Vous pouvez voir un exemple dans la capture d’écran suivante, où les boutons d’action sont visibles au bas de la notification :

 [![Exemple de capture d’écran des boutons d’action affichés sous un message de notification](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La `Notification` classe a reçu de nouvelles constantes qui permettent à un développeur de spécifier l’un des cinq niveaux de priorité pour une notification. Elles peuvent être définies sur une notification à l’aide de la `Priority` propriété.

### <a name="permissions"></a>Autorisations

Les nouvelles autorisations suivantes ont été ajoutées :

- `READ_EXTERNAL_STORAGE` -L’application nécessite un accès en lecture seule au stockage externe. Actuellement, toutes les applications ont un accès en lecture par défaut, mais les versions ultérieures d’Android requièrent des applications qui demandent explicitement un accès en lecture.
- `READ_USER_DICTIONARY` -Autorise un accès en lecture au dictionnaire Word de l’utilisateur.
- `READ_CALL_LOG` -Permet à une application d’obtenir des informations sur les appels entrants et sortants en lisant le journal des appels.
- `WRITE_CALL_LOG` -Permet à une application d’écrire dans le journal des appels sur le téléphone.
- `WRITE_USER_DICTIONARY` -Permet à une application d’écrire dans le dictionnaire Word de l’utilisateur.

Une modification importante à noter `READ_EXTERNAL_STORAGE` : actuellement, cette autorisation est accordée automatiquement par Android. Les versions ultérieures d’Android requièrent qu’une application demande cette autorisation avant de lui accorder l’autorisation.

## <a name="summary"></a>Résumé

Cet article a présenté quelques-unes des nouvelles API disponibles dans Android 4,1 (niveau d’API 16). Il a mis en évidence certaines des modifications apportées aux animations et à animer le lancement d’une activité, et a introduit les nouvelles API pour la découverte du réseau d’autres appareils à l’aide de protocoles tels que Bonjour ou UPnP. D’autres modifications apportées à l’API ont également été mises en surbrillance, telles que la possibilité de couper et coller des données via des intentions, la possibilité d’utiliser des services isolés ou des fournisseurs de contenu « instables ».

Cet article s’est ensuite présenté à l’introduction des mises à jour des notifications et a abordé certaines des nouvelles autorisations introduites avec Android 4,1

## <a name="related-links"></a>Liens associés

- [Exemple d’animation temporelle (exemple)](/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [API Android 4,1](https://developer.android.com/about/versions/android-4.1.html)
- [Tâches et piles de arrière-plan](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navigation avec l’arrière-plan](https://developer.android.com/design/patterns/navigation.html)