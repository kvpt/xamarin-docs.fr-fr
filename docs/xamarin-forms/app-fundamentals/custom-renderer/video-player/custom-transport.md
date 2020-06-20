---
title: Contrôles de transport vidéo personnalisés
description: Cet article explique comment implémenter des contrôles de transport personnalisés dans une application de lecteur vidéo à l’aide de Xamarin.Forms .
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c72f68f327b6c0df6959d37ed4bf390e3bd7f31d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135003"
---
# <a name="custom-video-transport-controls"></a>Contrôles de transport vidéo personnalisés

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Les contrôles de transport d’un lecteur vidéo incluent les boutons qui effectuent les fonctions **Lecture**, **Pause** et **Arrêt**. Ces boutons sont habituellement identifiés par des icônes familières plutôt que du texte, et les fonctions **Lecture** et **Pause** sont généralement combinées en un seul bouton.

Par défaut, `VideoPlayer` affiche des contrôles de transport pris en charge par chaque plateforme. Quand vous affectez `false` à la propriété `AreTransportControlsEnabled`, ces contrôles sont supprimés. Vous pouvez ensuite contrôler `VideoPlayer` par programmation ou fournir vos propres contrôles de transport.

## <a name="the-play-pause-and-stop-methods"></a>Méthodes Play, Pause et Stop

La classe `VideoPlayer` définit trois méthodes nommées `Play`, `Pause` et `Stop` qui sont implémentées par le déclenchement d’événements :

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

Les gestionnaires d’événements pour ces événements sont définis par la classe `VideoPlayerRenderer` dans chaque plateforme, comme indiqué ci-dessous :

### <a name="ios-transport-implementations"></a>Implémentations de transport iOS

La version iOS de `VideoPlayerRenderer` utilise la méthode `OnElementChanged` afin de définir des gestionnaires pour ces trois événements quand la propriété `NewElement` n’est pas `null` et détache les gestionnaires d’événements quand `OldElement` n’est pas `null` :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

Les gestionnaires d’événements sont implémentés en appelant des méthodes sur l’objet `AVPlayer`. Comme il n’existe aucune méthode `Stop` pour `AVPlayer`, elle est simulée en suspendant la vidéo et en déplaçant la position au début.

### <a name="android-transport-implementations"></a>Implémentations de transport Android

L’implémentation Android est similaire à l’implémentation iOS. Les gestionnaires pour les trois fonctions sont définis quand `NewElement` n’est pas `null` et détachés quand `OldElement` n’est pas `null` :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

Les trois fonctions appellent des méthodes définies par `VideoView`.

### <a name="uwp-transport-implementations"></a>Implémentations de transport UWP

L’implémentation UWP des trois fonctions de transport est très similaire aux implémentations iOS et Android :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>État du lecteur vidéo

L’implémentation des fonctions **Lecture**, **Pause** et **Arrêt** n’est pas suffisante pour prendre en charge les contrôles de transport. Les commandes **Lecture** et **Pause** sont souvent implémentées avec le même bouton qui change d’apparence pour indiquer si la vidéo est actuellement en cours de lecture ou en pause. En outre, le bouton ne doit même pas être activé si la vidéo n’a pas encore été chargée.

Ces exigences impliquent que le lecteur vidéo doit proposer un état indiquant s’il est en cours de lecture ou en pause, ou s’il n’est pas encore prêt à lire une vidéo. (Chaque plateforme prend également en charge des propriétés qui indiquent si la vidéo peut être mise en pause ou déplacée vers une nouvelle position, mais ces propriétés étant applicables au streaming vidéo plutôt qu’aux fichiers vidéo, elles ne sont pas prises en charge dans le `VideoPlayer` décrit ici.)

Le projet **VideoPlayerDemos** inclut une énumération `VideoStatus` avec trois membres :

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

La classe `VideoPlayer` définit une propriété pouvant être liée en lecture seule nommée `Status` de type `VideoStatus`. Cette propriété est définie en lecture seule, car elle ne doit être définie qu’à partir du renderer de plateforme :

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

En règle générale, une propriété pouvant être liée en lecture seule dispose d’un accesseur `set` privé sur la propriété `Status` pour lui permettre d’être définie à partir de la classe. Pour un dérivé `View` pris en charge par les renderers, toutefois, la propriété doit être définie à l’extérieur de la classe, mais uniquement par le renderer de plateforme.

Pour cette raison, une autre propriété est définie avec le nom `IVideoPlayerController.Status`. Il s’agit d’une implémentation d’interface explicite qui est rendue possible par l’interface `IVideoPlayerController` implémentée par la classe `VideoPlayer` :

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

Cela est similaire à la façon dont le [`WebView`](xref:Xamarin.Forms.WebView) contrôle utilise l' [`IWebViewController`](xref:Xamarin.Forms.IWebViewController) interface pour implémenter les `CanGoBack` `CanGoForward` Propriétés et. (Pour plus d’informations, consultez le code source de [`WebView`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) et de ses convertisseurs.)

Il est ainsi possible pour une classe externe à `VideoPlayer` de définir la propriété `Status` en référençant l’interface `IVideoPlayerController`. (Vous verrez bientôt le code.) La propriété peut également être définie à partir d’autres classes, mais il est peu probable qu’elle soit définie par inadvertance. Plus important encore, la propriété `Status` ne peut pas être définie via une liaison de données.

Pour aider les renderers à assurer la mise à jour de cette propriété `Status`, la classe `VideoPlayer` définit un événement `UpdateStatus` qui est déclenché tous les dixièmes de seconde :

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>Paramètre d’état iOS

Le `VideoPlayerRenderer` IOS définit un gestionnaire pour l’événement `UpdateStatus` (et détache ce gestionnaire quand l’élément `VideoPlayer` sous-jacent est absent) et utilise le gestionnaire pour définir la propriété `Status` :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

Vous `AVPlayer` devez accéder à deux propriétés : la [`Status`](xref:AVFoundation.AVPlayer.Status*) propriété de type `AVPlayerStatus` et la [`TimeControlStatus`](xref:AVFoundation.AVPlayer.TimeControlStatus*) propriété de type `AVPlayerTimeControlStatus` . Notez que la propriété `Element` (qui correspond au `VideoPlayer`) doit être castée en `IVideoPlayerController` pour définir la propriété `Status`.

### <a name="the-android-status-setting"></a>Paramètre d’état Android

La [`IsPlaying`](xref:Android.Widget.VideoView.IsPlaying) propriété de l’Android `VideoView` est une valeur booléenne qui indique uniquement si la vidéo est en mode de diffusion ou en pause. Pour déterminer si le `VideoView` ne peut ni lire ni suspendre la vidéo pour l’instant, l’événement `Prepared` de `VideoView` doit être géré. Ces deux gestionnaires sont définis dans la méthode `OnElementChanged` et détachés pendant le remplacement de `Dispose` :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

Le gestionnaire `UpdateStatus` utilise le champ `isPrepared` (défini dans le gestionnaire `Prepared`) et la propriété `IsPlaying` pour définir la propriété `Status` :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>Paramètre d’état UWP

Le `VideoPlayerRenderer` UWP utilise l’événement `UpdateStatus`, mais il n’en a pas besoin pour définir la propriété `Status`. `MediaElement`Définit un [`CurrentStateChanged`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentStateChanged) événement qui permet au convertisseur d’être notifié lorsque la [`CurrentState`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentState*) propriété a été modifiée. La propriété est détachée dans le remplacement de `Dispose` :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

La `CurrentState` propriété est de type [`MediaElementState`](/uwp/api/windows.ui.xaml.media.mediaelementstate) et est facilement mappée à `VideoStatus` :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>Boutons de lecture, de pause et d’arrêt

L’utilisation de caractères Unicode pour les images **Lecture**, **Pause** et **Arrêt** symboliques est problématique. La section [Divers technique](https://unicode-table.com/en/blocks/miscellaneous-technical/) de la convention Unicode Standard définit trois symboles apparemment appropriés à cet effet. Ces règles sont les suivantes :

- 0x23F5 (triangle noir moyen pointant vers la droite) ou &#x23F5; pour **Lecture**
- 0x23F8 (double barre verticale) ou &#x23F8; pour **Pause**
- 0x23F9 (carré noir) ou &#x23F9; pour **Arrêt**

Quelle que soit la façon dont ces symboles s’affichent dans votre navigateur (et les différents navigateurs les gèrent de différentes façons), ils ne sont pas affichés de manière cohérente sur les plateformes prises en charge par Xamarin.Forms . Sur les appareils iOS et UWP, les caractères **Pause** et **Arrêt** ont une apparence graphique, avec un arrière-plan 3D bleu et un premier plan blanc. Ce n’est pas le cas sur Android, où le symbole est simplement bleu. Toutefois, le point de code 0x23F5 pour **Lecture** n’a pas la même apparence sur UWP et il n’est même pas pris en charge sur iOS et Android.

Pour cette raison, le point de code 0x23F5 ne peut pas être utilisé pour **Lecture**. Une bonne alternative est :

- 0x25B6 (triangle noir pointant vers la droite) ou &#x25B6; pour **Lecture**

Ce symbole est pris en charge par chaque plateforme, à ceci près qu’il s’agit d’un triangle noir ordinaire qui ne ressemble pas à l’apparence 3D de **Pause** ni d’**Arrêt**. Vous avez la possibilité de compléter le point de code 0x25B6 par un code variante :

- 0x25B6 suivi de 0xFE0F (variante 16) ou &#x25B6;&#xFE0F; pour **Lecture**

C’est ce qui est utilisé dans la balise ci-dessous. Sur iOS, ce code donne au symbole **Lecture** la même apparence 3D que les boutons **Pause** et **Arrêt**, mais la variante ne fonctionne pas sur Android et UWP.

La page **Transport personnalisé** affecte à la propriété **AreTransportControlsEnabled** la valeur **false** et inclut un élément `ActivityIndicator` affiché lors du chargement de la vidéo ainsi que deux boutons. Des objets `DataTrigger` servent à activer et désactiver l’élément `ActivityIndicator` et les boutons ainsi qu’à basculer le premier bouton entre **Lecture** et **Pause** :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

Les déclencheurs de données sont décrits en détail dans l’article [Déclencheurs de données](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers).

Le fichier code-behind contient les gestionnaires pour les événements `Clicked` de bouton :

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

Comme `AutoPlay` a la valeur `false` dans le fichier **CustomTransport.xaml**, vous devez appuyer sur le bouton de **lecture** quand celui-ci est activé pour démarrer la vidéo. Les boutons sont définis afin que les caractères Unicode abordés ci-dessus soient accompagnés de leurs équivalents textuels. Les boutons ont une apparence cohérente sur chaque plateforme lors de la lecture de la vidéo :

[![Transport personnalisé en train de fonctionner](custom-transport-images/customtransportplaying-small.png "Transport personnalisé en train de fonctionner")](custom-transport-images/customtransportplaying-large.png#lightbox "Transport personnalisé en train de fonctionner")

Toutefois, sur Android et UWP, le bouton de **lecture** semble très différent quand la vidéo est en pause :

[![Transport personnalisé suspendu](custom-transport-images/customtransportpaused-small.png "Transport personnalisé suspendu")](custom-transport-images/customtransportpaused-large.png#lightbox "Transport personnalisé suspendu")

Dans une application de production, vous voudrez probablement utiliser vos propres images bitmap pour les boutons afin d’obtenir une uniformité visuelle.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de lecteur vidéo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
