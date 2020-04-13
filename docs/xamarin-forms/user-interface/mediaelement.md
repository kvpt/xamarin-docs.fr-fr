---
title: Xamarin.Forms MédiaElement
description: Cet article explique comment utiliser MediaElement pour lire la vidéo et l’audio dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 6f6c51c428de569ceb09ed6a26cfc36881c86dc5
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628341"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin.Forms MédiaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)est une vue pour lire la vidéo et l’audio. Les médias pris en charge par la plate-forme sous-jacente peuvent être joués à partir des sources suivantes :

- Le web, à l’aide d’un URI (HTTP ou HTTPS).
- Une ressource intégrée dans l’application de plate-forme, en utilisant le `ms-appx:///` système URI.
- Fichiers provenant des dossiers de données locaux et `ms-appdata:///` temporaires de l’application, en utilisant le système URI.
- La bibliothèque de l’appareil.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)peut utiliser les commandes de lecture de la plate-forme, qui sont appelées contrôles de transport. Cependant, ils sont désactivés par défaut et peuvent être remplacés par vos propres contrôles de transport. Les captures `MediaElement` d’écran suivantes montrent la lecture d’une vidéo avec les commandes de transport de la plate-forme:

[![Capture d’écran d’un MediaElement jouant une vidéo, sur iOS et Android](mediaelement-images/playback-controls.png "MediaElement jouant une vidéo")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement jouant une vidéo")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)est disponible dans Xamarin.Forms 4.5. Cependant, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante à votre fichier *App.xaml.cs* :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)est disponible sur iOS, Android, la plate-forme Windows Universelle (UWP), macOS, Windows Presentation Foundation et Tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)définit les propriétés suivantes :

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), de [`Aspect`](xref:Xamarin.Forms.Aspect)type , détermine comment les supports seront mis à l’échelle pour s’adapter à la zone d’affichage. La valeur par défaut de cette propriété est `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), du `bool`type , indique si la [`Source`](xref:Xamarin.Forms.MediaElement.Source) lecture des médias commencera automatiquement lorsque la propriété est définie. La valeur par défaut de cette propriété est `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), de `double`type , indique le progrès tampon actuel. La valeur par défaut de cette propriété est de 0,0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), de `bool`type , indique si les supports peuvent [`Position`](xref:Xamarin.Forms.MediaElement.Position) être repositionnés en fixant la valeur de la propriété. Il s’agit d’une propriété en lecture seule.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de [`MediaElementState`](xref:Xamarin.Forms.MediaElementState)type , indique l’état actuel du contrôle. Il s’agit d’une propriété `MediaElementState.Closed`lue uniquement, dont la valeur par défaut est .
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), de `TimeSpan?`type , indique la durée du support actuellement ouvert. Il s’agit d’une propriété `null`lue seulement dont la valeur par défaut est .
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), de `bool`type , décrit si la source multimédia actuellement chargée devrait reprendre la lecture dès le début après avoir atteint sa fin. La valeur par défaut de cette propriété est `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), du `bool`type , détermine si l’écran de l’appareil doit rester allumé pendant la lecture des médias. La valeur par défaut de cette propriété est `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), de `TimeSpan`type , décrit les progrès actuels à travers le temps de lecture des médias. La valeur par défaut de cette propriété est `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), du `bool`type , détermine si les commandes de lecture des plates-formes sont affichées. La valeur par défaut de cette propriété est `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), de [`MediaSource`](xref:Xamarin.Forms.MediaSource)type , indique la source des médias chargés dans le contrôle.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), de `int`type , indique la hauteur du contrôle. Il s’agit d’une propriété en lecture seule.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), de `int`type , indique la largeur du contrôle. Il s’agit d’une propriété en lecture seule.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), de `double`type , détermine le volume du média, qui est représenté sur une échelle linéaire entre 0 et 1. Cette propriété `TwoWay` utilise une liaison, et sa valeur par défaut est de 1.

Ces propriétés, à `CanSeek` l’exception de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) la propriété, sont soutenues par des objets, ce qui signifie qu’ils peuvent être des cibles de liaisons de données, et de style.

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe définit également quatre événements :

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)est tiré lorsque le flux multimédia a été validé et ouvert.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)est congédié `MediaElement` quand les finitions jouent ses médias.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)est congédié quand il ya une erreur associée à la source des médias.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)est congédié lorsque le point de recherche d’une opération de recherche demandée est prêt pour la lecture.

En [`MediaElement`](xref:Xamarin.Forms.MediaElement) outre, [`Play`](xref:Xamarin.Forms.MediaElement.Play) [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)comprend [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) , , et les méthodes.

Pour plus d’informations sur les formats multimédias pris en charge sur Android, consultez [les formats multimédias pris](https://developer.android.com/guide/topics/media/media-formats) en charge sur developer.android.com. Pour plus d’informations sur les formats multimédias pris en charge sur la plate-forme Windows universelle (UWP), voir [codecs pris en charge](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Jouer aux médias distants

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) peut lire des fichiers multimédias distants à l’aide des systèmes HTTP et HTTPS URI. Ceci est accompli [`Source`](xref:Xamarin.Forms.MediaElement.Source) en fixant la propriété à l’URI du fichier média :

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

Par défaut, les médias qui [`Source`](xref:Xamarin.Forms.MediaElement.Source) sont définis par la propriété joue immédiatement après l’ouverture des médias. Pour supprimer la lecture automatique [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) des `false`médias, définissez la propriété à .

Les contrôles de lecture des médias sont désactivés par défaut, et sont activés en définissant la [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) propriété à `true`. [`MediaElement`](xref:Xamarin.Forms.MediaElement)utilisera ensuite les commandes de lecture de la plate-forme.

## <a name="play-local-media"></a>Jouer aux médias locaux

Les médias locaux peuvent être joués à partir des sources suivantes :

- Une ressource intégrée dans l’application de plate-forme, en utilisant le `ms-appx:///` système URI.
- Fichiers provenant des dossiers de données locaux et `ms-appdata:///` temporaires de l’application, en utilisant le système URI.
- La bibliothèque de l’appareil.

Pour plus d’informations sur ces régimes URI, voir [URI régimes](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Jouer les supports intégrés dans le paquet d’applications

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) peut lire des fichiers multimédias qui `ms-appx:///` sont intégrés dans le paquet d’applications, en utilisant le système URI. Les fichiers multimédias sont intégrés dans le paquet d’applications en les plaçant dans le projet de plate-forme.

Le stockage d’un fichier multimédia dans le projet de plate-forme est différent pour chaque plate-forme :

- Sur iOS, les fichiers multimédias doivent être stockés dans le dossier **Ressources** ou dans un dossier des **Ressources.** Le fichier des `Build Action` médias `BundleResource`doit avoir un de .
- Sur Android, les fichiers multimédias doivent être stockés dans un sous-pli des **ressources** nommée **brute**. Le dossier **raw** ne peut pas contenir de sous-dossiers. Le fichier des `Build Action` médias `AndroidResource`doit avoir un de .
- Sur UWP, les fichiers multimédias peuvent être stockés dans n’importe quel dossier du projet. Le fichier des `BuildAction` médias `Content`doit avoir un de .

Les fichiers multimédias qui répondent à `ms-appx:///` ces critères peuvent ensuite être rejoués à l’aide du régime URI :

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Lors de l’utilisation de la liaison de données, un convertisseur de valeur peut être utilisé pour appliquer ce régime URI:

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

Une instance `VideoSourceConverter` de la peut ensuite `ms-appx:///` être utilisée pour appliquer le régime URI à un fichier multimédia intégré:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Pour plus d’informations sur le système ms-appx URI, voir [ms-appx et ms-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Jouez les médias à partir des dossiers locaux et temporaires de l’application

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) peut lire des fichiers multimédias qui sont copiés dans les `ms-appdata:///` dossiers de données locaux ou temporaires de l’application, en utilisant le système URI.

L’exemple suivant [`Source`](xref:Xamarin.Forms.MediaElement.Source) montre l’ensemble de propriété à un fichier multimédia stocké dans le dossier de données local de l’application :

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

L’exemple suivant [`Source`](xref:Xamarin.Forms.MediaElement.Source) montre la propriété à un fichier multimédia stocké dans le dossier de données temporaire de l’application :

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> En plus de lire les fichiers multimédias qui sont stockés dans les dossiers de données locaux ou temporaires de l’application, UWP peut également lire des fichiers multimédias qui sont situés dans le dossier d’itinérance de l’application. Ceci peut être réalisé en préfixant le fichier média avec `ms-appdata:///roaming/`.

Lors de l’utilisation de la liaison de données, un convertisseur de valeur peut être utilisé pour appliquer ce régime URI:

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

Une instance `VideoSourceConverter` de la peut ensuite `ms-appdata:///` être utilisée pour appliquer le régime URI à un fichier multimédia dans le dossier de données local ou temporaire de l’application:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Pour plus d’informations sur le système ms-appdata URI, voir [ms-appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copier un fichier multimédia dans le dossier de données local ou temporaire de l’application

La lecture d’un fichier multimédia stocké dans le dossier de données local ou temporaire de l’application nécessite que le fichier multimédia y soit copié par l’application. Cela peut être accompli, par exemple, en copiant un fichier multimédia à partir du paquet d’applications :

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
> L’exemple de `FileSystem` code ci-dessus utilise la classe incluse dans Xamarin.Essentials. Pour plus d’informations, voir [Xamarin.Essentials: File System Helpers](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Jouer les médias de la bibliothèque d’appareils

La plupart des appareils mobiles et ordinateurs de bureau modernes ont la possibilité d’enregistrer des vidéos et des audios à l’aide de la caméra et du microphone de l’appareil. Les supports créés sont ensuite stockés sous forme de fichiers sur l’appareil. Ces fichiers peuvent être récupérés à [`MediaElement`](xref:Xamarin.Forms.MediaElement)la bibliothèque et joués par le .

Chacune des plates-formes comprend une installation qui permet à l’utilisateur de sélectionner des médias à partir de la bibliothèque de l’appareil. Dans Xamarin.Forms, les projets de plate-forme peuvent [`DependencyService`](xref:Xamarin.Forms.DependencyService) invoquer cette fonctionnalité, et il peut être appelé par la classe.

Le service de dépendance à la sélection vidéo utilisé dans l’application de l’échantillon est très similaire à `Stream` celui défini dans la sélection [d’une photo de la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), sauf que le cueilleur retourne un nom de fichier plutôt qu’un objet. Le projet de code partagé `IVideoPicker`définit une interface nommée `GetVideoFileAsync`, qui définit une méthode unique nommée . Chaque plate-forme implémente ensuite cette interface en `VideoPicker` classe.

L’exemple de code suivant montre comment récupérer un fichier multimédia de la bibliothèque de l’appareil :

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

Le service de dépendance à la `DependencyService.Get` sélection vidéo est `IVideoPicker` invoqué en appelant la méthode pour obtenir la mise en œuvre d’une interface dans le projet de plate-forme. La `GetVideoFileAsync` méthode est alors appelée sur cette instance, et le [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) nom de fichier [`Source`](xref:Xamarin.Forms.MediaElement.Source) retourné est [`MediaElement`](xref:Xamarin.Forms.MediaElement)utilisé pour créer un objet et le définir à la propriété de la .

## <a name="change-video-aspect-ratio"></a>Modifier le rapport d’aspect vidéo

La [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) propriété détermine comment les supports vidéo seront mis à l’échelle pour s’adapter à la zone d’affichage. Par défaut, cette propriété `AspectFit` est réglée au membre de recensement, mais [`Aspect`](xref:Xamarin.Forms.Aspect) elle peut être réglée à l’un des membres de l’énumération :

- `AspectFit`indique que la vidéo sera boîte aux lettres, si nécessaire, pour s’insérer dans la zone d’affichage, tout en préservant le rapport d’aspect.
- `AspectFill`indique que la vidéo sera coupée de sorte qu’elle remplit la zone d’affichage, tout en préservant le rapport d’aspect.
- `Fill`indique que la vidéo sera étirée pour remplir la zone d’affichage.

## <a name="poll-for-position-data"></a>Sondage pour les données de position

La notification de [`Position`](xref:Xamarin.Forms.MediaElement.Position) modification de propriété pour la propriété liant ne tire qu’à des moments clés tels que le début et la fin de lecture, et la pause se produisant. Par conséquent, les `Position` données liant à la propriété ne donneront pas de données de position précises. Au lieu de cela, vous devez configurer une minuterie et sonder la propriété.

Un bon endroit pour le `OnAppearing` faire est dans la dérogation pour la page qui nécessite les données de position que les médias sont joués:

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

Dans cet exemple, le `OnAppearing` remplacement commence `positionLabel` une `Position` minuterie qui met à jour avec la valeur chaque seconde. Le rappel de minuterie est invoqué chaque seconde, jusqu’à ce que le rappel retourne `false`. Lorsque la navigation `OnDisappearing` de page se produit, le remplacement est exécuté, ce qui empêche le rappel de minuterie d’être invoqué.

## <a name="understand-mediasource-types"></a>Comprendre les types MediaSource

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) peut jouer aux [`Source`](xref:Xamarin.Forms.MediaElement.Source) médias en définissant sa propriété dans un fichier média éloigné ou local. La `Source` propriété est [`MediaSource`](xref:Xamarin.Forms.MediaSource)de type, et cette classe définit deux méthodes statiques:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), renvoie [`MediaSource`](xref:Xamarin.Forms.MediaSource) un `string` cas d’un argument.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), renvoie [`MediaSource`](xref:Xamarin.Forms.MediaSource) un `Uri` cas d’un argument.

En outre, [`MediaSource`](xref:Xamarin.Forms.MediaSource) la classe a `MediaSource` également `string` des `Uri` opérateurs implicites qui renvoient des instances et des arguments.

> [!NOTE]
> Lorsque [`Source`](xref:Xamarin.Forms.MediaElement.Source) la propriété est placée en XAML, un convertisseur de type est invoqué pour renvoyer une [`MediaSource`](xref:Xamarin.Forms.MediaSource) instance à partir d’un `string` ou `Uri`.

La [`MediaSource`](xref:Xamarin.Forms.MediaSource) classe a également deux classes dérivées:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), qui est utilisé pour spécifier un fichier média à distance à partir d’un URI. Cette classe [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) a une propriété qui `Uri`peut être réglé à un .
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), qui est utilisé pour spécifier un fichier des médias locaux à partir d’un `string`. Cette classe [`File`](xref:Xamarin.Forms.FileMediaSource.File) a une propriété qui `string`peut être réglé à un . En outre, cette classe a `string` des `FileMediaSource` opérateurs implicites `FileMediaSource` pour `string`convertir un objet, et un objet à un .

> [!NOTE]
> Lorsqu’un [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) objet est créé dans XAML, un convertisseur de type est invoqué pour renvoyer une [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) instance à partir d’un `string`.

## <a name="determine-mediaelement-status"></a>Déterminer le statut MediaElement

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe définit une propriété liant [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)lisible [`MediaElementState`](xref:Xamarin.Forms.MediaElementState)de lecture seulement nommée , de type . Cette propriété indique l’état actuel du contrôle, comme si les médias jouent ou s’arrêtent, ou si elle n’est pas encore prête à jouer les médias.

L’énumération [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) définit les membres suivants :

- `Closed`indique que `MediaElement` le ne contient aucun média.
- `Opening`indique que `MediaElement` le valide et tente de charger la source spécifiée.
- `Buffering`indique que `MediaElement` le chargement des médias pour la lecture. Sa [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriété n’avance pas pendant cet état. Si `MediaElement` la vidéo était en cours d’affiche, elle continue d’afficher le dernier cadre affiché.
- `Playing`indique que `MediaElement` le joue la source des médias.
- `Paused`indique que `MediaElement` le ne [`Position`](xref:Xamarin.Forms.MediaElement.Position) fait pas avancer sa propriété. Si `MediaElement` la vidéo a été la lecture, il continue à afficher le cadre actuel.
- `Stopped`indique que `MediaElement` les supports contiennent, mais il n’est pas joué ou mis en pause. Sa [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriété est 0 et n’avance pas. Si le support chargé `MediaElement` est vidéo, le montre la première image.

Il n’est généralement pas [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) nécessaire d’examiner la propriété lors de l’utilisation des contrôles de [`MediaElement`](xref:Xamarin.Forms.MediaElement) transport. Cependant, cette propriété devient importante lors de la mise en œuvre de vos propres contrôles de transport.

## <a name="implement-custom-transport-controls"></a>Mettre en œuvre des contrôles de transport personnalisés

Les commandes de transport d’un lecteur multimédia comprennent les boutons qui exécutent les fonctions **Play**, **Pause**, et **Stop**. Ces boutons sont habituellement identifiés par des icônes familières plutôt que du texte, et les fonctions **Lecture** et **Pause** sont généralement combinées en un seul bouton.

Par défaut, [`MediaElement`](xref:Xamarin.Forms.MediaElement) les contrôles de lecture sont désactivés. Cela vous permet `MediaElement` de contrôler le programme de manière programmatique, ou en fournissant vos propres contrôles de transport. À l’appui `MediaElement` [`Play`](xref:Xamarin.Forms.MediaElement.Play)de [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)cela, comprend , , et les [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) méthodes.

L’exemple XAML suivant montre [`MediaElement`](xref:Xamarin.Forms.MediaElement) une page qui contient un contrôle de transport personnalisé et :

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

Dans cet exemple, les contrôles [`Button`](xref:Xamarin.Forms.Button) de transport personnalisés sont définis comme des objets. Cependant, il n’y a `Button` `Button` que deux objets, le `Button` premier représentant **Play** and **Pause**, et le second représentant **Stop**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)objets sont utilisés pour activer et désactiver les boutons, et pour passer le premier bouton entre **Play** et **Pause**. Pour plus d’informations sur les déclencheurs de données, voir [Xamarin.Forms Triggers](~/xamarin-forms/app-fundamentals/triggers.md).

Le fichier de code-derrière a [`Clicked`](xref:Xamarin.Forms.Button.Clicked) les gestionnaires pour les événements:

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

Le bouton **Play** peut être appuyé, une fois qu’il est activé, pour commencer la lecture:

[![Capture d’écran d’un MediaElement avec des contrôles de transport personnalisés, sur iOS et Android](mediaelement-images/custom-transport-playback.png "MediaElement jouant une vidéo")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement jouant une vidéo")

Appuyer sur le bouton **Pause,** vous entraîne une pause de lecture :

[![Capture d’écran d’un MediaElement avec lecture en pause, sur iOS et Android](mediaelement-images/custom-transport-paused.png "MediaElement avec une vidéo en pause")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement avec une vidéo en pause")

Appuyer sur le bouton **Stop** arrête la lecture et renvoie la position du fichier multimédia au début.

## <a name="implement-a-custom-position-bar"></a>Mettre en œuvre une barre de position personnalisée

Les contrôles de transport implémentés par chaque plateforme incluent une barre de position. Cette barre ressemble à un curseur et montre l’emplacement actuel des médias dans sa durée totale. En outre, vous pouvez manipuler la barre de position pour avancer ou vers l’arrière à une nouvelle position dans la vidéo.

La mise en œuvre d’une barre de position personnalisée nécessite de connaître la durée des médias et la position de lecture actuelle. Ces données sont [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) disponibles dans le et [`Position`](xref:Xamarin.Forms.MediaElement.Position) les propriétés.

> [!IMPORTANT]
> Il [`Position`](xref:Xamarin.Forms.MediaElement.Position) faut sonder pour obtenir des données de position précises. Pour plus d’informations, voir [les données de Poll for Position](#poll-for-position-data).

Une barre de position personnalisée [`Slider`](xref:Xamarin.Forms.Slider)peut être implémentée à l’aide d’un , comme le montre l’exemple suivant :

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

La `PositionSlider` classe définit `Duration` ses `Position` propres propriétés `TimeToEnd` liantes et une propriété liant. Les trois propriétés `TimeSpan`sont de type . Le gestionnaire de la `Duration` propriété `Maximum` modifiée par [`Slider`](xref:Xamarin.Forms.Slider) la `TotalSeconds` propriété `TimeSpan` établit la propriété de la propriété de la valeur. La `TimeToEnd` propriété est calculée `Duration` en `Position` fonction des changements apportés aux propriétés et des propriétés, et commence à la durée du média et diminue à zéro au fur et à mesure que la lecture se poursuit.

Le `PositionSlider` est mis [`Slider`](xref:Xamarin.Forms.Slider) à `Slider` jour à partir de la sous-jacente lorsque le est déplacé pour indiquer que les médias doivent être avancés ou inversés à une nouvelle position. Ceci est détecté `PropertyChanged` dans le `PositionSlider` gestionnaire dans le constructeur. Le gestionnaire recherche un changement dans la propriété `Value` et, si elle est différente de la propriété `Position`, la propriété `Position` est définie à partir de la propriété `Value`. Pour plus d’informations sur l’utilisation d’un [`Slider`](xref:Xamarin.Forms.Slider) voir, [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> Sur Android, [`Slider`](xref:Xamarin.Forms.Slider) le seul a 1000 étapes `Minimum` `Maximum` discrètes, indépendamment de la et les paramètres. Si la longueur des médias est supérieure à 1000 secondes, alors deux valeurs différentes `Position` correspondraient à la même `Value` de la `Slider`. C’est pourquoi le code ci-dessus vérifie que la nouvelle position et la position existante sont supérieures à un centième de la durée globale.

L’exemple suivant `PositionSlider` montre l’être consommé sur une page :

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

Dans cet exemple, `Duration` la `PositionSlider` propriété de la [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) est [`MediaElement`](xref:Xamarin.Forms.MediaElement)liée à des données à la propriété de la . Lorsque [`Value`](xref:Xamarin.Forms.Slider.Value) la propriété [`Slider`](xref:Xamarin.Forms.Slider) des `ValueChanged` changements, l’événement s’allume et le `OnPositionSliderValueChanged` gestionnaire est exécuté. Ce gestionnaire [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) fixe la propriété `PositionSlider.Position` à la valeur de la propriété. Par conséquent, `Slider` en faisant glisser les résultats de la position de lecture des médias en changeant :

[![Capture d’écran d’un MediaElement avec une barre de position personnalisée, sur iOS et Android](mediaelement-images/custom-position-bar.png "MediaElement avec une barre de position personnalisée")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement avec une barre de position personnalisée")

En outre, [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) un objet est `PositionSlider` utilisé pour désactiver le moment où les médias sont tampons. Pour plus d’informations sur les déclencheurs de données, voir [Xamarin.Forms Triggers](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Mettre en œuvre un contrôle de volume personnalisé

Les contrôles de lecture des médias mis en œuvre par chaque plate-forme comprennent une barre de volume. Cette barre ressemble à un curseur et montre le volume des médias. En outre, vous pouvez manipuler la barre de volume pour augmenter ou diminuer le volume.

Une barre de volume personnalisée [`Slider`](xref:Xamarin.Forms.Slider)peut être implémentée à l’aide d’un , comme le montre l’exemple suivant :

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

Dans cet [`Slider`](xref:Xamarin.Forms.Slider) exemple, les `Value` données lie [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) sa [`MediaElement`](xref:Xamarin.Forms.MediaElement)propriété à la propriété de la . Cela est possible `Volume` parce `TwoWay` que la propriété utilise une liaison. Par conséquent, `Value` la modification `Volume` de la propriété entraînera le changement de propriété.

> [!NOTE]
> La [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) propriété a un rappel de vlidation qui garantit que sa valeur est supérieure ou égale à 0,0, et moins ou égale à 1,0.

Pour plus d’informations sur l’utilisation d’un [`Slider`](xref:Xamarin.Forms.Slider) voir, [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>Liens connexes

- [MediaElementDemos (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [Schémas d’URI](/windows/uwp/app-resources/uri-schemes)
- [Déclencheurs Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)
- [Android : Formats multimédias pris en charge](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: Codecs pris en charge](/windows/uwp/audio-video-camera/supported-codecs)
