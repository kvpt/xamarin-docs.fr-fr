---
title: ''
description: Cet article explique comment charger des vidéos stockées en tant que ressources d’application dans une application de lecteur vidéo à l’aide de Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 472c6c2a7c5a084520aee6b8ae160d9329c3f593
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133761"
---
# <a name="loading-application-resource-videos"></a>Chargement de vidéos de ressource d’application

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Les renderers personnalisés pour l’affichage `VideoPlayer` sont capables de lire des fichiers vidéo qui ont été incorporés dans les projets de plateforme individuels en tant que ressources d’application. Toutefois, la version actuelle de `VideoPlayer` ne peut pas accéder aux ressources incorporées dans une bibliothèque .NET Standard.

Pour charger ces ressources, créez une instance de `ResourceVideoSource` en affectant à la propriété `Path` le nom de fichier (ou le dossier et le nom de fichier) de la ressource. Vous pouvez également appeler la méthode `VideoSource.FromResource` statique pour référencer la ressource. Affectez ensuite à l’objet `ResourceVideoSource` la propriété `Source` de `VideoPlayer`.

## <a name="storing-the-video-files"></a>Stockage des fichiers vidéo

Le stockage d’un fichier vidéo dans le projet de plateforme est différente pour chaque plateforme.

### <a name="ios-video-resources"></a>Ressources vidéo iOS

Dans un projet iOS, vous pouvez stocker une vidéo dans le dossier **Resources** ou un sous-dossier du dossier **Resources**. Le fichier vidéo doit présenter un élément `Build Action` avec la valeur `BundleResource`. Affectez à la propriété `Path` de `ResourceVideoSource` le nom de fichier, par exemple **MyFile.mp4** pour un fichier dans le dossier **Resources** ou **MyFolder/MyFile.mp4**, où **MyFolder** est un sous-dossier de **Resources**.

Dans la solution **VideoPlayerDemos**, le projet **VideoPlayerDemos.iOS** contient un sous-dossier de **Resources** nommé **Videos** comprenant un fichier nommé **iOSApiVideo.mp4**. Il s’agit d’une courte vidéo qui vous montre comment utiliser le site web Xamarin afin de trouver la documentation pour la classe iOS `AVPlayerViewController`.

### <a name="android-video-resources"></a>Ressources vidéo Android

Dans un projet Android, les vidéos doivent être stockées dans un sous-dossier de **Resources** nommé **raw**. Le dossier **raw** ne peut pas contenir de sous-dossiers. Attribuez au fichier vidéo un élément `Build Action` avec la valeur `AndroidResource`. Affectez à la propriété `Path` de `ResourceVideoSource` le nom de fichier, par exemple **MyFile.mp4**.

Le projet **VideoPlayerDemos.Android** contient un sous-dossier de **Resources** nommé **raw** comprenant un fichier nommé **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Ressources vidéo UWP

Dans un projet de plateforme Windows universelle, vous pouvez stocker des vidéos dans n’importe quel dossier du projet. Attribuez au fichier un élément `Build Action` avec la valeur `Content`. Affectez à la propriété `Path` de `ResourceVideoSource` le dossier et le nom de fichier, par exemple **MyFolder/MyVideo.mp4**.

Le projet **VideoPlayerDemos.UWP** contient un dossier nommé **Videos** avec le fichier **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Chargement des fichiers vidéo

Chacune des classes de renderer de plateforme contient du code dans sa méthode `SetSource` pour le chargement des fichiers vidéo stockés en tant que ressources.

### <a name="ios-resource-loading"></a>Chargement de ressource iOS

La version iOS de `VideoPlayerRenderer` utilise la méthode `GetUrlForResource` de `NSBundle` pour le chargement de la ressource. Le chemin complet doit être constitué du nom de fichier, de l’extension et du répertoire. Le code utilise la classe `Path` dans l’espace de noms .NET `System.IO` pour diviser le chemin de fichier en ces composants :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Chargement de ressource Android

Le `VideoPlayerRenderer` Android utilise le nom de fichier et le nom de package pour construire un objet `Uri`. Le nom de package est le nom de l’application, dans ce cas **VideoPlayerDemos.Android**, qui peut être obtenu à partir de la propriété `Context.PackageName` statique. L’objet `Uri` obtenu est ensuite transmis à la méthode `SetVideoURI` de `VideoView` :

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
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>Chargement de ressource UWP

Le `VideoPlayerRenderer` UWP construit un objet `Uri` pour le chemin et lui affecte la propriété `Source` de `MediaElement` :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>Lecture du fichier de ressources

La page **Lire la ressource vidéo** dans la solution **VideoPlayerDemos** utilise la classe `OnPlatform` pour spécifier le fichier vidéo de chaque plateforme :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

Si la ressource iOS est stockée dans le dossier **Resources** et si la ressource UWP est stockée dans le dossier racine du projet, vous pouvez utiliser le même nom de fichier pour chaque plateforme. Si tel est le cas, vous pouvez définir ce nom directement sur la propriété `Source` de `VideoPlayer`.

Voici cette page en cours d’exécution :

[![Lire la ressource vidéo](loading-resources-images/playvideoresource-small.png "Lire la ressource vidéo")](loading-resources-images/playvideoresource-large.png#lightbox "Lire la ressource vidéo")

Vous avez maintenant vu comment [charger des vidéos à partir d’un URI web](web-videos.md) et comment lire des ressources incorporées. En outre, vous pouvez [charger des vidéos à partir de la vidéothèque de l’appareil](accessing-library.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de lecteur vidéo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
