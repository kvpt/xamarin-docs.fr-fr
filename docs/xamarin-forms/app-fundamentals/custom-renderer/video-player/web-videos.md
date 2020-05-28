---
title: ''
description: Cet article explique comment lire des vidéos Web dans une application de lecteur vidéo à l’aide de Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4269b768d6dd9a0eadbbe557f701f8a43b74c99
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133677"
---
# <a name="playing-a-web-video"></a>Lecture d’une vidéo web

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

La classe `VideoPlayer` définit une propriété `Source` utilisée pour spécifier la source du fichier vidéo ainsi qu’une propriété `AutoPlay`. `AutoPlay` a la valeur par défaut `true`, ce qui signifie que la lecture de la vidéo doit démarrer automatiquement après la définition de `Source` :

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

La `Source` propriété est de type `VideoSource` , qui est modèle après la Xamarin.Forms [`ImageSource`](xref:Xamarin.Forms.ImageSource) classe abstraite, et ses trois dérivés, [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) , [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) et [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) . Toutefois, aucune option de flux n’est disponible pour le `VideoPlayer`, car iOS et Android ne prennent pas en charge la lecture d’une vidéo à partir d’un flux.

## <a name="video-sources"></a>Sources vidéo

La classe abstraite `VideoSource` se compose uniquement de trois méthodes statiques qui instancient les trois classes qui dérivent de `VideoSource` :

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

La classe `UriVideoSource` est utilisée pour spécifier un fichier vidéo téléchargeable avec un URI. Elle définit une seule propriété de type `string` :

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

La gestion des objets de type `UriVideoSource` est décrite ci-dessous.

La classe `ResourceVideoSource` est utilisée pour accéder à des fichiers vidéo stockés en tant que ressources incorporées dans l’application de plateforme, également spécifiée avec une propriété `string` :

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

La gestion des objets de type `ResourceVideoSource` est décrite dans l’article [Chargement de vidéos de ressource d’application](loading-resources.md). La classe `VideoPlayer` ne dispose d’aucune fonctionnalité pour charger un fichier vidéo stocké en tant que ressource dans la bibliothèque .NET Standard.

La classe `FileVideoSource` est utilisée pour accéder à des fichiers vidéo à partir de la vidéothèque de l’appareil. L’unique propriété est également de type `string` :

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

La gestion des objets de type `FileVideoSource` est décrite dans l’article [Accès à la vidéothèque de l’appareil](accessing-library.md).

La classe `VideoSource` inclut un attribut `TypeConverter` qui fait référence à `VideoSourceConverter` :

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

Ce convertisseur de type est appelé quand la propriété `Source` a pour valeur une chaîne en XAML. Voici la classe `VideoSourceConverter` :

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

La méthode `ConvertFromInvariantString` tente de convertir la chaîne en un objet `Uri`. Si cette tentative réussit et que le schéma n’est pas `file:`, la méthode retourne un objet `UriVideoSource`. Sinon, elle retourne un objet `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Définition de la source vidéo

Toute autre logique impliquant des sources vidéo est implémentée dans les renderers de plateforme individuels. Les sections suivantes montrent comment les renderers de plateforme lisent des vidéos quand la propriété `Source` a pour valeur un objet `UriVideoSource`.

### <a name="the-ios-video-source"></a>Source vidéo iOS

Deux sections du `VideoPlayerRenderer` sont impliquées dans la définition de la source vidéo du lecteur vidéo. Lorsque Xamarin.Forms crée d’abord un objet de type `VideoPlayer` , la `OnElementChanged` méthode est appelée avec la `NewElement` propriété de l’objet arguments défini sur ce `VideoPlayer` . La méthode `OnElementChanged` appelle `SetSource` :

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
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

Par la suite, quand la propriété `Source` est modifiée, la méthode `OnElementPropertyChanged` est appelée avec une propriété `PropertyName` « Source » et `SetSource` est rappelée.

Pour lire un fichier vidéo dans iOS, un objet de type [`AVAsset`](xref:AVFoundation.AVAsset) est d’abord créé pour encapsuler le fichier vidéo et utilisé pour créer un [`AVPlayerItem`](xref:AVFoundation.AVPlayerItem) , qui est ensuite transmis à l' `AVPlayer` objet. Voici comment la méthode `SetSource` gère la propriété `Source` quand elle est de type `UriVideoSource` :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

La propriété `AutoPlay` n’ayant aucun analogue dans les classes de vidéos iOS, la propriété est examinée à la fin de la méthode `SetSource` pour appeler la méthode `Play` sur l’objet `AVPlayer`.

Dans certains cas, la lecture des vidéos se poursuit une fois que la page avec le `VideoPlayer` retourne à la page d’accueil. Pour arrêter la vidéo, `ReplaceCurrentItemWithPlayerItem` est également défini dans le remplacement de `Dispose` :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>Source vidéo Android

Le `VideoPlayerRenderer` Android doit définir la source vidéo du lecteur quand le `VideoPlayer` est tout d’abord créé et par la suite quand la propriété `Source` est modifiée :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

La méthode `SetSource` gère les objets de type `UriVideoSource` en appelant `SetVideoUri` sur le `VideoView` avec un objet `Uri` Android créé à partir de l’URI de chaîne. La classe `Uri` est complète ici pour la différencier de la classe .NET `Uri` :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

Le `VideoView` Android n’ayant pas de propriété `AutoPlay` correspondante, la méthode `Start` est appelée si une nouvelle vidéo a été définie.

Il existe une différence de comportement entre les renderers iOS et Android si la propriété `Source` de `VideoPlayer` a la valeur `null`, ou si la propriété `Uri` de `UriVideoSource` a pour valeur `null` ou une chaîne vide. Si le lecteur vidéo iOS est en train de lire une vidéo et que `Source` a la valeur `null` (ou que la chaîne est `null` ou vide), `ReplaceCurrentItemWithPlayerItem` est appelé avec la valeur `null`. La vidéo en cours est remplacée et s’arrête.

Android ne prend pas en charge une fonctionnalité similaire. Si la propriété `Source` a pour valeur `null`, la méthode `SetSource` l’ignore simplement et la lecture de la vidéo en cours se poursuit.

### <a name="the-uwp-video-source"></a>Source vidéo UWP

Le `MediaElement` UWP définit une propriété `AutoPlay`, qui est gérée dans le renderer comme toute autre propriété :

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
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

La propriété `SetSource` gère un objet `UriVideoSource` en définissant la propriété `Source` de `MediaElement` sur une valeur .NET `Uri`, ou sur `null` si la propriété `Source` de `VideoPlayer` a la valeur `null` :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>Définition de la source d’une URL

Avec l’implémentation de ces propriétés dans les trois renderers, il est possible de lire une vidéo à partir de la source d’une URL. La page **Lire une vidéo web** dans le programme [**VideoPlayDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) est définie par le fichier XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

La classe `VideoSourceConverter` convertit la chaîne en `UriVideoSource`. Quand vous accédez à la page **Lire une vidéo web**, le chargement de la vidéo commence et la lecture démarre quand une quantité suffisante de données a été téléchargée et mise en mémoire tampon. La vidéo dure environ 10 minutes :

[![Lire une vidéo Web](web-videos-images/playwebvideo-small.png "Lire une vidéo Web")](web-videos-images/playwebvideo-large.png#lightbox "Lire une vidéo Web")

Sur chacune des plateformes, les contrôles de transport disparaissent s’ils ne sont pas utilisés, mais peuvent être de nouveau affichés en appuyant sur la vidéo.

Vous pouvez empêcher la vidéo de démarrer automatiquement en affectant à la propriété `AutoPlay` la valeur `false` :

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Vous devez appuyer sur le bouton de **lecture** pour démarrer la vidéo.

De même, vous pouvez supprimer l’affichage des contrôles de transport en affectant à la propriété `AreTransportControlsEnabled` la valeur `false` :

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Si vous affectez aux deux propriétés la valeur `false`, la lecture de la vidéo ne commence pas et il n’existe aucun moyen de la démarrer ! Vous devez appeler `Play` à partir du fichier code-behind ou pour créer vos propres contrôles de transport, comme décrit dans l’article [Implémentation des contrôles de transport vidéo personnalisés](custom-transport.md).

Le fichier **App.xaml** inclut des ressources pour deux vidéos supplémentaires :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Pour référencer l’un de ces autres films, vous pouvez remplacer l’URL explicite dans le fichier **PlayWebVideo.xaml** par une extension de balise `StaticResource`, auquel cas `VideoSourceConverter` n’est pas obligatoire pour créer l’objet `UriVideoSource` :

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

Vous pouvez également définir la propriété `Source` à partir d’un fichier vidéo dans un `ListView`, comme décrit dans l’article suivant, [Liaison de sources vidéo au lecteur](source-bindings.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de lecteur vidéo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
