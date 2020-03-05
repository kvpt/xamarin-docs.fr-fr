---
title: Xamarin. Forms, MediaElement
description: Cet article explique comment utiliser MediaElement pour lire des données audio et vidéo dans une application Xamarin. Forms.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 76a30bfb17c8263345d781386acf9d34c01aae3b
ms.sourcegitcommit: 5b6d3bddf7148f8bb374de5657bdedc125d72ea7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78292733"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin. Forms, MediaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/MediaElementDemos)

[`MediaElement`](xref:Xamarin.Forms.MediaElement) est une vue pour la lecture de données audio et vidéo. Les médias pris en charge par la plateforme sous-jacente peuvent être lus à partir des sources suivantes :

- Le Web, à l’aide d’un URI (HTTP ou HTTPs).
- Ressource incorporée dans l’application de plateforme, à l’aide du schéma d’URI `ms-appx:///`.
- Fichiers provenant des dossiers de données locaux et temporaires de l’application, à l’aide du schéma d’URI `ms-appdata:///`.
- Bibliothèque de l’appareil.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) pouvez utiliser les contrôles de lecture de la plateforme, appelés contrôles de transport. Toutefois, ils sont désactivés par défaut et peuvent être remplacés par vos propres contrôles de transport. Les captures d’écran suivantes montrent `MediaElement` lisant une vidéo avec les contrôles de transport de la plateforme :

[![Capture d’écran d’un MediaElement lisant une vidéo, sur iOS et Android](mediaelement-images/playback-controls.png "MediaElement lisant une vidéo")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement lisant une vidéo")

[`MediaElement`](xref:Xamarin.Forms.MediaElement) est disponible dans Xamarin. forms 4,5. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante à votre fichier *app.Xaml.cs* :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement) est disponible sur iOS, Android, le plateforme Windows universelle (UWP), macOS, Windows Presentation Foundation et Tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) définit les propriétés suivantes :

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), de type [`Aspect`](xref:Xamarin.Forms.Aspect), détermine la façon dont le média sera mis à l’échelle pour s’ajuster à la zone d’affichage. La valeur par défaut de cette propriété est `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), de type `bool`, indique si la lecture du média démarre automatiquement lorsque la propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) est définie. La valeur par défaut de cette propriété est `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), de type `double`, indique la progression de la mise en mémoire tampon actuelle. La valeur par défaut de cette propriété est 0,0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), de type `bool`, indique si le média peut être repositionné en définissant la valeur de la propriété [`Position`](xref:Xamarin.Forms.MediaElement.Position) . Il s’agit d’une propriété en lecture seule.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de type [`MediaElementState`](xref:Xamarin.Forms.MediaElementState), indique l’état actuel du contrôle. Il s’agit d’une propriété en lecture seule, dont la valeur par défaut est `MediaElementState.Closed`.
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), de type `TimeSpan?`, indique la durée du média actuellement ouvert. Il s’agit d’une propriété en lecture seule dont la valeur par défaut est `null`.
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), de type `bool`, indique si la source du média actuellement chargée doit reprendre la lecture à partir du début après avoir atteint sa fin. La valeur par défaut de cette propriété est `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), de type `bool`, détermine si l’écran de l’appareil doit rester allumé pendant la lecture du média. La valeur par défaut de cette propriété est `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), de type `TimeSpan`, décrit la progression actuelle par le temps de lecture du média. La valeur par défaut de cette propriété est `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), de type `bool`, détermine si les contrôles de lecture des plateformes sont affichés. La valeur par défaut de cette propriété est `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), de type [`MediaSource`](xref:Xamarin.Forms.MediaSource), indique la source du média chargé dans le contrôle.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), de type `int`, indique la hauteur du contrôle. Il s’agit d’une propriété en lecture seule.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), de type `int`, indique la largeur du contrôle. Il s’agit d’une propriété en lecture seule.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), de type `double`, détermine le volume du média, représenté sur une échelle linéaire comprise entre 0 et 1. Cette propriété utilise une liaison de `TwoWay`, et sa valeur par défaut est 1.

Ces propriétés, à l’exception de la propriété `CanSeek`, sont soulignes par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et un style.

La classe [`MediaElement`](xref:Xamarin.Forms.MediaElement) définit également quatre événements :

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened) est déclenché lors de la validation et de l’ouverture du flux multimédia.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded) est déclenchée lorsque l' `MediaElement` a terminé de reproduire son contenu multimédia.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed) est déclenché en cas d’erreur associée à la source du média.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted) est déclenché lorsque le point de recherche d’une opération de recherche demandée est prêt pour la lecture.

En outre, [`MediaElement`](xref:Xamarin.Forms.MediaElement) comprend des méthodes de [`Play`](xref:Xamarin.Forms.MediaElement.Play), [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)et [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

Pour plus d’informations sur les formats multimédias pris en charge sur Android, voir [formats multimédias pris en charge](https://developer.android.com/guide/topics/media/media-formats) sur Developer.Android.com. Pour plus d’informations sur les formats de média pris en charge sur la plateforme Windows universelle (UWP), consultez [codecs pris en charge](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Lire un média à distance

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) pouvez lire des fichiers multimédias à distance à l’aide des schémas d’URI HTTP et HTTPS. Pour ce faire, affectez à la propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) l’URI du fichier multimédia :

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

Par défaut, le média défini par la propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) est lu immédiatement après l’ouverture du média. Pour supprimer la lecture automatique du média, affectez à la propriété [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) la valeur `false`.

Les contrôles de lecture de média sont désactivés par défaut et sont activés en affectant à la propriété [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) la valeur `true`. [`MediaElement`](xref:Xamarin.Forms.MediaElement) utilisera ensuite les contrôles de lecture de la plateforme.

## <a name="play-local-media"></a>Lire un support local

Les supports locaux peuvent être lus à partir des sources suivantes :

- Ressource incorporée dans l’application de plateforme, à l’aide du schéma d’URI `ms-appx:///`.
- Fichiers provenant des dossiers de données locaux et temporaires de l’application, à l’aide du schéma d’URI `ms-appdata:///`.
- Bibliothèque de l’appareil.

Pour plus d’informations sur ces schémas d’URI, consultez [schémas d’URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Lire le média incorporé dans le package d’application

Une [`MediaElement`](xref:Xamarin.Forms.MediaElement) peut lire des fichiers multimédias incorporés dans le package d’application, à l’aide du schéma d’URI `ms-appx:///`. Les fichiers multimédias sont incorporés dans le package d’application en les plaçant dans le projet de plateforme.

Le stockage d’un fichier multimédia dans le projet de plateforme est différent pour chaque plateforme :

- Sur iOS, les fichiers multimédias doivent être stockés dans le dossier **Resources** ou dans un sous-dossier du dossier **Resources** . Le fichier multimédia doit avoir une `Build Action` de `BundleResource`.
- Sur Android, les fichiers multimédias doivent être stockés dans un sous-dossier de **ressources** nommées **RAW**. Le dossier **raw** ne peut pas contenir de sous-dossiers. Le fichier multimédia doit avoir une `Build Action` de `AndroidResource`.
- Sur UWP, les fichiers multimédias peuvent être stockés dans n’importe quel dossier du projet. Le fichier multimédia doit avoir une `BuildAction` de `Content`.

Les fichiers multimédias qui répondent à ces critères peuvent ensuite être lus à l’aide de l' `ms-appx:///` schéma d’URI :

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Lors de l’utilisation de la liaison de données, un convertisseur de valeurs peut être utilisé pour appliquer ce modèle d’URI :

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }
    // ...
}
```

Une instance du `VideoSourceConverter` peut ensuite être utilisée pour appliquer le schéma d’URI `ms-appx:///` à un fichier multimédia incorporé :

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Pour plus d’informations sur le schéma d’URI MS-AppX, consultez [MS-AppX et MS-AppX-Web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Lire un média à partir des dossiers locaux et temporaires de l’application

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) pouvez lire des fichiers multimédias copiés dans les dossiers de données locaux ou temporaires de l’application, à l’aide du schéma d’URI `ms-appdata:///`.

L’exemple suivant illustre la propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) définie sur un fichier multimédia stocké dans le dossier Data local de l’application :

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

L’exemple suivant montre la propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) à un fichier multimédia stocké dans le dossier de données temporaires de l’application :

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> En plus de lire des fichiers multimédias stockés dans les dossiers de données locaux ou temporaires de l’application, UWP peut également lire des fichiers multimédias qui se trouvent dans le dossier itinérant de l’application. Pour ce faire, vous devez préfixer le fichier multimédia avec `ms-appdata:///roaming/`.

Lors de l’utilisation de la liaison de données, un convertisseur de valeurs peut être utilisé pour appliquer ce modèle d’URI :

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        return new Uri($"ms-appdata:///{value}");
    }
    // ...
}
```

Une instance du `VideoSourceConverter` peut ensuite être utilisée pour appliquer le schéma d’URI `ms-appdata:///` à un fichier multimédia dans le dossier de données local ou temporaire de l’application :

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Pour plus d’informations sur le schéma d’URI MS-AppData, consultez [MS-AppData](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copie d’un fichier multimédia dans le dossier de données local ou temporaire de l’application

La présence d’un fichier multimédia stocké dans le dossier de données local ou temporaire de l’application nécessite que le fichier multimédia soit copié ici par l’application. Cela peut être accompli, par exemple, en copiant un fichier multimédia à partir du package d’application :

```csharp
// This method copies the video from the app package to the app data
// directory for your app. To copy the video to the temp directory
// for your app, comment out the first line of code, and uncomment
// the second line of code.
public static async Task CopyVideoIfNotExists(string filename)
{
    string folder = FileSystem.AppDataDirectory;
    //string folder = Path.GetTempPath();
    string videoFile = Path.Combine(folder, "XamarinVideo.mp4");

    if (!File.Exists(videoFile))
    {
        using (Stream inputStream = await FileSystem.OpenAppPackageFileAsync(filename))
        {
            using (FileStream outputStream = File.Create(videoFile))
            {
                await inputStream.CopyToAsync(outputStream);
            }
        }
    }
}
```

> [!NOTE]
> L’exemple de code ci-dessus utilise la classe `FileSystem` incluse dans Xamarin. Essentials. Pour plus d’informations, consultez [Xamarin. Essentials : programme d’assistance du système de fichiers](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Lire un média à partir de la bibliothèque d’appareils

La plupart des appareils mobiles et des ordinateurs de bureau modernes peuvent enregistrer des vidéos et de l’audio à l’aide de l’appareil photo et du microphone de l’appareil. Le support créé est ensuite stocké sous forme de fichiers sur l’appareil. Ces fichiers peuvent être récupérés à partir de la bibliothèque et lus par le [`MediaElement`](xref:Xamarin.Forms.MediaElement).

Chacune des plateformes comprend une fonctionnalité qui permet à l’utilisateur de sélectionner un support à partir de la bibliothèque de l’appareil. Dans Xamarin. Forms, les projets de plateforme peuvent appeler cette fonctionnalité et peuvent être appelés par la classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

Le service de dépendance de sélection vidéo utilisé dans l’exemple d’application est très similaire à celui défini dans [le choix d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), sauf que le sélecteur retourne un nom de fichier plutôt qu’un objet `Stream`. Le projet de code partagé définit une interface nommée `IVideoPicker`, qui définit une méthode unique nommée `GetVideoFileAsync`. Chaque plateforme implémente ensuite cette interface dans une classe `VideoPicker`.

L’exemple de code suivant montre comment récupérer un fichier multimédia à partir de la bibliothèque d’appareils :

```csharp
string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();
if (!string.IsNullOrWhiteSpace(filename))
{
    mediaElement.Source = new FileMediaSource
    {
        File = filename
    };
}
```

Le service de dépendance de sélection vidéo est appelé en appelant la méthode `DependencyService.Get` pour obtenir l’implémentation d’une interface `IVideoPicker` dans le projet de plateforme. La méthode `GetVideoFileAsync` est ensuite appelée sur cette instance, et le nom de fichier retourné est utilisé pour créer un objet [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) et pour lui affecter la propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) de l' [`MediaElement`](xref:Xamarin.Forms.MediaElement).

## <a name="change-video-aspect-ratio"></a>Changer le proportions de la vidéo

La propriété [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) détermine la façon dont les supports vidéo sont mis à l’échelle pour s’ajuster à la zone d’affichage. Par défaut, cette propriété est définie sur le `AspectFit` membre de l’énumération, mais elle peut être définie sur l’un des membres de l’énumération [`Aspect`](xref:Xamarin.Forms.Aspect) :

- `AspectFit` indique que la vidéo sera letterboxed, si nécessaire, pour s’ajuster à la zone d’affichage, tout en conservant les proportions.
- `AspectFill` indique que la vidéo sera découpée de façon à remplir la zone d’affichage, tout en conservant les proportions.
- `Fill` indique que la vidéo sera étirée pour remplir la zone d’affichage.

## <a name="poll-for-position-data"></a>Interroger les données de position

La notification de modification de propriété pour la [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriété pouvant être liée ne se déclenche qu’à des moments clés tels que le début et la fin de la lecture, et l’interruption de l’occurrence. Par conséquent, la liaison de données à la propriété `Position` ne produira pas de données de position précises. Au lieu de cela, vous devez configurer un minuteur et interroger la propriété.

Pour ce faire, il est préférable d’utiliser la `OnAppearing` remplacement de la page qui requiert les données de position en tant que média joué :

```csharp
bool polling = true;

protected override void OnAppearing()
{
    base.OnAppearing();

    Device.StartTimer(TimeSpan.FromMilliseconds(1000), () =>
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            positionLabel.Text = mediaElement.Position.ToString("hh\\:mm\\:ss");
        });
        return polling;
    });
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    polling = false;
}
```

Dans cet exemple, le `OnAppearing` remplacement démarre une minuterie qui met à jour `positionLabel` avec la valeur de `Position` chaque seconde. Le rappel de la minuterie est appelé chaque seconde, jusqu’à ce que le rappel retourne `false`. Lorsque la navigation entre les pages se produit, le `OnDisappearing` remplacement est exécuté, ce qui arrête le rappel de la minuterie appelée.

## <a name="understand-mediasource-types"></a>Comprendre les types MediaSource

Une [`MediaElement`](xref:Xamarin.Forms.MediaElement) peut lire un média en affectant à sa propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) la valeur d’un fichier de média local ou distant. La propriété `Source` est de type [`MediaSource`](xref:Xamarin.Forms.MediaSource), et cette classe définit deux méthodes statiques :

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), retourne une instance [`MediaSource`](xref:Xamarin.Forms.MediaSource) à partir d’un argument `string`.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), retourne une instance [`MediaSource`](xref:Xamarin.Forms.MediaSource) à partir d’un argument `Uri`.

En outre, la classe [`MediaSource`](xref:Xamarin.Forms.MediaSource) a également des opérateurs implicites qui retournent des instances de `MediaSource` à partir d' `string` et `Uri` arguments.

> [!NOTE]
> Quand la propriété [`Source`](xref:Xamarin.Forms.MediaElement.Source) est définie en XAML, un convertisseur de type est appelé pour retourner une instance de [`MediaSource`](xref:Xamarin.Forms.MediaSource) à partir d’un `string` ou `Uri`.

La classe [`MediaSource`](xref:Xamarin.Forms.MediaSource) possède également deux classes dérivées :

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), qui est utilisé pour spécifier un fichier multimédia à distance à partir d’un URI. Cette classe a une propriété [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) qui peut être définie sur une `Uri`.
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), qui permet de spécifier un fichier de média local à partir d’un `string`. Cette classe a une propriété [`File`](xref:Xamarin.Forms.FileMediaSource.File) qui peut être définie sur une `string`. De plus, cette classe a des opérateurs implicites pour convertir un `string` en objet `FileMediaSource` et un objet `FileMediaSource` en `string`.

> [!NOTE]
> Quand un objet [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) est créé en XAML, un convertisseur de type est appelé pour retourner une instance de [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) à partir d’un `string`.

## <a name="determine-mediaelement-status"></a>Déterminer l’état de MediaElement

La classe [`MediaElement`](xref:Xamarin.Forms.MediaElement) définit une propriété pouvant être liée en lecture seule nommée [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de type [`MediaElementState`](xref:Xamarin.Forms.MediaElementState). Cette propriété indique l’état actuel du contrôle, par exemple si le média est en cours de lecture ou en pause, ou s’il n’est pas encore prêt à lire le média.

L’énumération [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) définit les membres suivants :

- `Closed` indique que le `MediaElement` ne contient pas de support.
- `Opening` indique que le `MediaElement` valide et tente de charger la source spécifiée.
- `Buffering` indique que le `MediaElement` charge le média pour la lecture. Sa propriété [`Position`](xref:Xamarin.Forms.MediaElement.Position) n’avance pas au cours de cet État. Si le `MediaElement` était en train de visionner de la vidéo, il continue d’afficher la dernière image affichée.
- `Playing` indique que le `MediaElement` lit la source du média.
- `Paused` indique que la `MediaElement` n’avance pas sa propriété [`Position`](xref:Xamarin.Forms.MediaElement.Position) . Si le `MediaElement` était en train de visionner de la vidéo, il continue à afficher le frame actuel.
- `Stopped` indique que la `MediaElement` contient un support, mais qu’elle n’est pas en cours de lecture ou en pause. Sa propriété [`Position`](xref:Xamarin.Forms.MediaElement.Position) a la valeur 0 et n’avance pas. Si le média chargé est une vidéo, le `MediaElement` affiche la première image.

En général, il n’est pas nécessaire d’examiner la propriété [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) lors de l’utilisation des contrôles de transport [`MediaElement`](xref:Xamarin.Forms.MediaElement) . Toutefois, cette propriété devient importante lors de l’implémentation de vos propres contrôles de transport.

## <a name="implement-custom-transport-controls"></a>Implémenter des contrôles de transport personnalisés

Les contrôles de transport d’un lecteur multimédia incluent les boutons qui exécutent les fonctions **lire**, **suspendre**et **arrêter**. Ces boutons sont habituellement identifiés par des icônes familières plutôt que du texte, et les fonctions **Lecture** et **Pause** sont généralement combinées en un seul bouton.

Par défaut, les contrôles de lecture [`MediaElement`](xref:Xamarin.Forms.MediaElement) sont désactivés. Cela vous permet de contrôler le `MediaElement` par programme ou en fournissant vos propres contrôles de transport. Pour ce faire, `MediaElement` comprend des méthodes de [`Play`](xref:Xamarin.Forms.MediaElement.Play), [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)et [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

L’exemple de code XAML suivant montre une page qui contient un [`MediaElement`](xref:Xamarin.Forms.MediaElement) et des contrôles de transport personnalisés :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MediaElementDemos.CustomTransportPage"
             Title="Custom transport">
    <Grid>
        ...
        <MediaElement x:Name="mediaElement"
                      AutoPlay="False"
                      ... />
        <StackLayout BindingContext="{x:Reference mediaElement}"
                     ...>
            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Playing}">
                        <Setter Property="Text"
                                Value="&#x23F8; Pause" />
                    </DataTrigger>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Buffering}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Stopped}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

Dans cet exemple, les contrôles de transport personnalisés sont définis en tant qu’objets [`Button`](xref:Xamarin.Forms.Button) . Toutefois, il n’y a que deux objets `Button`, la première `Button` représentant la **lecture** et la **Pause**, et la deuxième `Button` représentant **Stop**. les objets [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) sont utilisés pour activer et désactiver les boutons, et pour basculer le premier bouton entre **lecture** et **Pause**. Pour plus d’informations sur les déclencheurs de données, consultez [déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

Le fichier code-behind contient les gestionnaires des événements de [`Clicked`](xref:Xamarin.Forms.Button.Clicked) :

```csharp
void OnPlayPauseButtonClicked(object sender, EventArgs args)
{
    if (mediaElement.CurrentState == MediaElementState.Stopped ||
        mediaElement.CurrentState == MediaElementState.Paused)
    {
        mediaElement.Play();
    }
    else if (mediaElement.CurrentState == MediaElementState.Playing)
    {
        mediaElement.Pause();
    }
}

void OnStopButtonClicked(object sender, EventArgs args)
{
    mediaElement.Stop();
}
```

Vous pouvez appuyer sur le bouton de **lecture** , une fois qu’il est activé, pour commencer la lecture :

[![Capture d’écran d’un MediaElement avec des contrôles de transport personnalisés, sur iOS et Android](mediaelement-images/custom-transport-playback.png "MediaElement lisant une vidéo")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement lisant une vidéo")

Si vous appuyez sur le bouton **Pause** , la lecture est suspendue :

[![Capture d’écran d’un MediaElement avec la lecture suspendue, sur iOS et Android](mediaelement-images/custom-transport-paused.png "MediaElement avec une vidéo en pause")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement avec une vidéo en pause")

Appuyer sur le bouton **arrêter** arrête la lecture et retourne la position du fichier multimédia au début.

## <a name="implement-a-custom-position-bar"></a>Implémenter une barre de position personnalisée

Les contrôles de transport implémentés par chaque plateforme incluent une barre de position. Cette barre ressemble à un curseur et indique l’emplacement actuel du support dans sa durée totale. En outre, vous pouvez manipuler la barre de positionnement pour avancer ou reculer vers une nouvelle position dans la vidéo.

L’implémentation d’une barre de position personnalisée nécessite de connaître la durée du média et la position de lecture actuelle. Ces données sont disponibles dans les propriétés [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) et [`Position`](xref:Xamarin.Forms.MediaElement.Position) .

> [!IMPORTANT]
> Le [`Position`](xref:Xamarin.Forms.MediaElement.Position) doit être interrogé pour obtenir des données de position précises. Pour plus d’informations, consultez [interrogation des données de position](#poll-for-position-data).

Une barre de position personnalisée peut être implémentée à l’aide d’un [`Slider`](xref:Xamarin.Forms.Slider), comme illustré dans l’exemple suivant :

```csharp
public class PositionSlider : Slider
{
    public static readonly BindableProperty DurationProperty =
        BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                propertyChanged: (bindable, oldValue, newValue) =>
                                {
                                    ((PositionSlider)bindable).SetTimeToEnd();
                                    double seconds = ((TimeSpan)newValue).TotalSeconds;
                                    ((Slider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                });

    public TimeSpan Duration
    {
        get { return (TimeSpan)GetValue(DurationProperty); }
        set { SetValue(DurationProperty, value); }
    }

    public static readonly BindableProperty PositionProperty =
        BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                propertyChanged: (bindable, oldValue, newValue) => ((PositionSlider)bindable).SetTimeToEnd());

    public TimeSpan Position
    {
        get { return (TimeSpan)GetValue(PositionProperty); }
        set { SetValue(PositionProperty, value); }
    }

    static readonly BindablePropertyKey TimeToEndPropertyKey =
        BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan());

    public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

    public TimeSpan TimeToEnd
    {
        get { return (TimeSpan)GetValue(TimeToEndProperty); }
        private set { SetValue(TimeToEndPropertyKey, value); }
    }

    public PositionSlider()
    {
        PropertyChanged += (sender, args) =>
        {
            if (args.PropertyName == "Value")
            {
                TimeSpan newPosition = TimeSpan.FromSeconds(Value);
                if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                {
                    Position = newPosition;
                }
            }
        };
    }

    void SetTimeToEnd()
    {
        TimeToEnd = Duration - Position;
    }
}
```

La classe `PositionSlider` définit son propre `Duration` et `Position` propriétés pouvant être liées, ainsi qu’une `TimeToEnd` propriété pouvant être liée. Les trois propriétés sont de type `TimeSpan`. Le gestionnaire de modification de propriété pour la propriété `Duration` affecte à la propriété `Maximum` de la [`Slider`](xref:Xamarin.Forms.Slider) la propriété `TotalSeconds` de la valeur `TimeSpan`. La propriété `TimeToEnd` est calculée en fonction des modifications apportées aux propriétés de `Duration` et de `Position`, et commence à la durée du média et diminue jusqu’à zéro à mesure que la lecture se poursuit.

Le `PositionSlider` est mis à jour à partir de la [`Slider`](xref:Xamarin.Forms.Slider) sous-jacente lorsque la `Slider` est déplacée pour indiquer que le support doit être avancé ou inversé à une nouvelle position. Cela est détecté dans le gestionnaire de `PropertyChanged` dans le constructeur `PositionSlider`. Le gestionnaire recherche un changement dans la propriété `Value` et, si elle est différente de la propriété `Position`, la propriété `Position` est définie à partir de la propriété `Value`. Pour plus d’informations sur l’utilisation d’un [`Slider`](xref:Xamarin.Forms.Slider) consultez le [curseur Xamarin. Forms](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> Sur Android, la [`Slider`](xref:Xamarin.Forms.Slider) n’a que des étapes discrètes 1000, quels que soient les paramètres de `Minimum` et de `Maximum`. Si la longueur du support est supérieure à 1000 secondes, deux valeurs de `Position` différentes correspondent à la même `Value` du `Slider`. C’est pourquoi le code ci-dessus vérifie que la nouvelle position et la position existante sont supérieures à un centième de la durée globale.

L’exemple suivant montre le `PositionSlider` consommé sur une page :

```xaml
<controls:PositionSlider x:Name="positionSlider"
                         BindingContext="{x:Reference mediaElement}"
                         Duration="{Binding Duration}"
                         ValueChanged="OnPositionSliderValueChanged">
    <controls:PositionSlider.Triggers>
        <DataTrigger TargetType="controls:PositionSlider"
                     Binding="{Binding CurrentState}"
                     Value="{x:Static MediaElementState.Buffering}">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </controls:PositionSlider.Triggers>
</controls:PositionSlider>
```

Dans cet exemple, la propriété `Duration` du `PositionSlider` est liée aux données de la propriété [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) de l' [`MediaElement`](xref:Xamarin.Forms.MediaElement). Lorsque la propriété [`Value`](xref:Xamarin.Forms.Slider.Value) du [`Slider`](xref:Xamarin.Forms.Slider) change, l’événement `ValueChanged` se déclenche et le gestionnaire de `OnPositionSliderValueChanged` est exécuté. Ce gestionnaire affecte à la propriété [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) la valeur de la propriété `PositionSlider.Position`. Par conséquent, le fait de faire glisser le `Slider` entraîne une modification de la position de lecture du média :

[![Capture d’écran d’un MediaElement avec une barre de position personnalisée, sur iOS et Android](mediaelement-images/custom-position-bar.png "MediaElement avec une barre de position personnalisée")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement avec une barre de position personnalisée")

En outre, un objet [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) est utilisé pour désactiver l' `PositionSlider` lorsque le média est mis en mémoire tampon. Pour plus d’informations sur les déclencheurs de données, consultez [déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implémenter un contrôle de volume personnalisé

Les contrôles de lecture de média implémentés par chaque plateforme incluent une barre de volume. Cette barre ressemble à un curseur et montre le volume du média. En outre, vous pouvez manipuler la barre de volume pour augmenter ou diminuer le volume.

Une barre de volume personnalisée peut être implémentée à l’aide d’un [`Slider`](xref:Xamarin.Forms.Slider), comme illustré dans l’exemple suivant :

```xaml
<StackLayout>
    <MediaElement AutoPlay="False"
                  Source="{StaticResource AdvancedAsync}" />
    <Slider Maximum="1.0"
            Minimum="0.0"
            Value="{Binding Volume}"
            Rotation="270"
            WidthRequest="100" />
</StackLayout>
```

Dans cet exemple, la [`Slider`](xref:Xamarin.Forms.Slider) données lie sa propriété `Value` à la propriété [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) de l' [`MediaElement`](xref:Xamarin.Forms.MediaElement). Cela est possible, car la propriété `Volume` utilise une liaison `TwoWay`. Par conséquent, la modification de la propriété `Value` entraîne la modification de la propriété `Volume`.

> [!NOTE]
> La propriété [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) a un rappel vlidation qui garantit que sa valeur est supérieure ou égale à 0,0, et inférieure ou égale à 1,0.

Pour plus d’informations sur l’utilisation d’un [`Slider`](xref:Xamarin.Forms.Slider) consultez le [curseur Xamarin. Forms](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>Liens connexes

- [MediaElementDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/MediaElementDemos)
- [Schémas d’URI](/windows/uwp/app-resources/uri-schemes)
- [Déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Curseur Xamarin. Forms](~/xamarin-forms/user-interface/slider.md)
- [Android : formats multimédias pris en charge](https://developer.android.com/guide/topics/media/media-formats)
- [UWP : codecs pris en charge](/windows/uwp/audio-video-camera/supported-codecs)
