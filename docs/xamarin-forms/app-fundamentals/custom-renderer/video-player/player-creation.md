---
title: Création des lecteurs vidéo de la plateforme
description: Cet article explique comment implémenter un renderer personnalisé de lecteur vidéo sur chaque plateforme à l’aide de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: e14008341e531844851452daeee4c730565a2843
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650496"
---
# <a name="creating-the-platform-video-players"></a>Création des lecteurs vidéo de la plateforme

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

La solution [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) contient tout le code afin d’implémenter un lecteur vidéo pour Xamarin.Forms. Elle inclut également une série de pages qui montre comment utiliser le lecteur vidéo dans une application. L’ensemble du code `VideoPlayer` et ses renderers de plateforme se trouvent dans des dossiers de projet nommés `FormsVideoLibrary` et utilisent également l’espace de noms `FormsVideoLibrary`. Cela doit faciliter la copie des fichiers dans votre propre application et la référence des classes.

## <a name="the-video-player"></a>Lecteur vidéo

La classe [`VideoPlayer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs) fait partie de la bibliothèque .NET Standard **VideoPlayerDemos** qui est partagée entre les plateformes. Elle dérive de `View` :

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

Les membres de cette classe (et l’interface `IVideoPlayerController`) sont décrits dans les articles qui suivent.

Chacune des plateformes comprend une classe nommée `VideoPlayerRenderer` qui contient le code spécifique à la plateforme pour implémenter un lecteur vidéo. La tâche principale de ce renderer consiste à créer un lecteur vidéo pour cette plateforme.

### <a name="the-ios-player-view-controller"></a>Contrôleur d’affichage de lecteur iOS

Plusieurs classes sont impliquées lors de l’implémentation d’un lecteur vidéo dans iOS. L’application crée d’abord un [`AVPlayerViewController`](xref:AVKit.AVPlayerViewController), puis définit la propriété [`Player`](xref:AVKit.AVPlayerViewController.Player*) sur un objet de type [`AVPlayer`](xref:AVFoundation.AVPlayer). Des classes supplémentaires sont nécessaires quand une source vidéo est affectée au lecteur.

Comme tous les renderers, le [`VideoPlayerRenderer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs) iOS contient un attribut `ExportRenderer` qui identifie l’affichage `VideoPlayer` avec le renderer :

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

En règle générale, un renderer qui définit un contrôle de plateforme dérive de la classe [`ViewRenderer<View, NativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs), où `View` est le dérivé `View` Xamarin.Forms (dans ce cas, `VideoPlayer`) et `NativeView` est un dérivé `UIView` iOS pour la classe de renderer. Pour ce renderer, cet argument générique est simplement défini sur `UIView`, pour des raisons que vous verrez bientôt.

Quand un renderer est basé sur un dérivé `UIViewController` (comme celui-ci), la classe doit remplacer la propriété `ViewController` et retourner le contrôleur d’affichage, dans ce cas `AVPlayerViewController`. C’est l’objectif du champ `_playerViewController` :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

La responsabilité principale du remplacement de `OnElementChanged` consiste à vérifier si la propriété `Control` a la valeur `null` et, dans ce cas, à créer un contrôle de plateforme à transmettre ensuite à la méthode `SetNativeControl`. Cet objet est ici uniquement disponible à partir de la propriété `View` du `AVPlayerViewController`. Ce dérivé `UIView` correspond en fait à une classe privée nommée `AVPlayerView` mais, comme il est privé, il ne peut pas être explicitement spécifié en tant que deuxième argument générique pour `ViewRenderer`.

Généralement, la propriété `Control` de la classe de renderer fait référence par la suite au `UIView` utilisé pour implémenter le renderer mais, dans ce cas, la propriété `Control` n’est pas utilisée ailleurs.

### <a name="the-android-video-view"></a>Affichage vidéo Android

Le renderer Android pour `VideoPlayer` repose sur la classe Android [`VideoView`](xrtef:Android.Widget.VideoView). Toutefois, si `VideoView` est utilisé seul pour lire une vidéo dans une application Xamarin.Forms, la vidéo remplit la zone allouée pour le `VideoPlayer` sans conserver les proportions correctes. Pour cette raison (comme vous le verrez bientôt), le `VideoView` devient un enfant d’un `RelativeLayout` Android. Une directive `using` définit `ARelativeLayout` pour le différencier du `RelativeLayout` Xamarin.Forms, et il s’agit du deuxième argument générique dans le `ViewRenderer` :

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

À compter de Xamarin.Forms 2.5, les renderers Android doivent inclure un constructeur avec un argument `Context`.

Le remplacement de `OnElementChanged` crée à la fois le `VideoView` et le `RelativeLayout`, et définit les paramètres de disposition pour le `VideoView` afin de le centrer dans le `RelativeLayout`.


```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

Un gestionnaire pour l’événement `Prepared` est attaché dans cette méthode et détaché dans la méthode `Dispose`. Cet événement est déclenché quand le `VideoView` dispose d’informations suffisantes pour commencer à lire un fichier vidéo.

### <a name="the-uwp-media-element"></a>Élément multimédia UWP

Dans la plateforme Windows universelle (UWP), le lecteur vidéo le plus courant est [`MediaElement`](xref:Windows.UI.Xaml.Controls.MediaElement). Cette documentation de `MediaElement` indique que le [`MediaPlayerElement`](xref:Windows.UI.Xaml.Controls.MediaPlayerElement) doit être utilisé à la place quand il est uniquement nécessaire de prendre en charge des versions de Windows 10 à compter de la build 1607.

Le remplacement de `OnElementChanged` doit créer un `MediaElement`, définir deux gestionnaires d’événements et passer l’objet `MediaElement` à `SetNativeControl` :

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

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
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

Les deux gestionnaires d’événements sont détachés dans l’événement `Dispose` pour le renderer.

## <a name="showing-the-transport-controls"></a>Affichage des contrôles de transport

Tous les lecteurs vidéo contenus dans les plateformes prennent en charge un ensemble par défaut de contrôles de transport qui incluent des boutons pour la lecture et la pause ainsi qu’une barre pour indiquer la position actuelle dans la vidéo et pour accéder à une nouvelle position.

La classe `VideoPlayer` définit une propriété nommée `AreTransportControlsEnabled` et la valeur par défaut `true` :


```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

Même si cette propriété dispose à la fois des accesseurs `set` et `get`, le renderer doit gérer les cas uniquement quand la propriété est définie. L’accesseur `get` retourne simplement la valeur actuelle de la propriété.

Les propriétés telles que `AreTransportControlsEnabled` sont gérées dans les renderers de plateforme de deux manières :

- La première fois est quand Xamarin.Forms crée un élément `VideoPlayer`. Cela est indiqué dans le remplacement de `OnElementChanged` du renderer quand la propriété `NewElement` n’a pas la valeur `null`. À ce stade, le renderer peut définir son propre lecteur vidéo de plateforme à partir de la valeur initiale de la propriété, comme défini dans le `VideoPlayer`.

- Si la propriété dans `VideoPlayer` change ultérieurement, la méthode `OnElementPropertyChanged` dans le renderer est appelée. Le renderer peut ainsi mettre à jour le lecteur vidéo de plateforme selon le nouveau paramètre de propriété.

Les sections suivantes décrivent comment la propriété `AreTransportControlsEnabled` est gérée sur chaque plateforme.

### <a name="ios-playback-controls"></a>Contrôles de lecture iOS

La propriété du `AVPlayerViewController` iOS qui régit l’affichage des contrôles de transport est [`ShowsPlaybackControls`](xref:AVKit.AVPlayerViewController.ShowsPlaybackControls*). Voici comment cette propriété est définie dans le `VideoViewRenderer` iOS :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

La propriété `Element` du renderer fait référence à la classe `VideoPlayer`.

### <a name="the-android-media-controller"></a>Contrôleur multimédia Android

Dans Android, l’affichage des contrôles de transport requiert la création d’un objet [`MediaController`](xref:Android.Widget.MediaController) et son association à l’objet `VideoView`. Les mécanismes sont illustrés dans la méthode `SetAreTransportControlsEnabled` :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>Propriété des contrôles de transport UWP

Le `MediaElement` UWP définissant une propriété nommée [`AreTransportControlsEnabled`](xref:Windows.UI.Xaml.Controls.MediaElement.AreTransportControlsEnabled*), cette propriété est définie à partir de la propriété `VideoPlayer` du même nom :

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
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

Une autre propriété supplémentaire est nécessaire pour commencer la lecture d’une vidéo : Il s’agit de la propriété `Source` cruciale qui fait référence à un fichier vidéo. L’implémentation de la propriété `Source` est décrite dans l’article suivant, [Lecture d’une vidéo web](web-videos.md).


## <a name="related-links"></a>Liens associés

- [Démonstrations de lecteur vidéo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
