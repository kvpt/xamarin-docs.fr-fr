---
titre : « liaison de sources vidéo au lecteur » Description : « cet article explique comment lier des sources vidéo au lecteur vidéo à l’aide de Xamarin.Forms . »
ms. Prod : xamarin ms. AssetID : 504E0C7E-051A-4AF2-B654-BAB4D0957928 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 02/12/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="binding-video-sources-to-the-player"></a>Liaison de sources vidéo au lecteur

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Quand la propriété `Source` de l’affichage `VideoPlayer` est définie sur un nouveau fichier vidéo, la lecture de la vidéo existante s’arrête et celle de la nouvelle vidéo commence. Cela est illustré par la page **Sélectionner une vidéo web** de l’exemple [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos). La page inclut un `ListView` avec les titres des trois vidéos référencées à partir du fichier **App.xaml** :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

Quand une vidéo est sélectionnée, le gestionnaire d’événements `ItemSelected` dans le fichier code-behind est exécuté. Le gestionnaire supprime les espaces et les apostrophes du titre et l’utilise en tant que clé pour obtenir l’une des ressources définies dans le fichier **App.xaml**. Cet objet `UriVideoSource` est ensuite défini sur la propriété `Source` du `VideoPlayer`.

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

Lors du premier chargement de la page, aucun élément n’est sélectionné dans le `ListView` de sorte que vous devez en sélectionner un pour commencer la lecture de la vidéo :

[![Sélectionner une vidéo Web](source-bindings-images/selectwebvideo-small.png "Sélectionner une vidéo Web")](source-bindings-images/selectwebvideo-large.png#lightbox "Sélectionner une vidéo Web")

La propriété `Source` de `VideoPlayer` repose sur une propriété pouvant être liée, ce qui signifie qu’elle peut être la cible d’une liaison de données. Cela est illustré par la page **Lier à VideoPlayer**. La balise dans le fichier **BindToVideoPlayer.xaml** est prise en charge par la classe suivante qui encapsule un titre d’une vidéo et un objet `VideoSource` correspondant :

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

Le `ListView` dans le fichier **BindToVideoPlayer.xaml** contient un tableau de ces objets `VideoInfo`, chacun d’eux initialisé avec un titre de vidéo et l’objet `UriVideoSource` du dictionnaire de ressources dans ** App.XAML** :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

La propriété `Source` de `VideoPlayer` est liée au `ListView`. Le `Path` de la liaison est spécifié en tant que `SelectedItem.VideoSource`, qui est un tracé composite constitué de deux propriétés : `SelectedItem` est une propriété de `ListView`. L’élément sélectionné est de type `VideoInfo`, qui a une propriété `VideoSource`.

Comme avec la première page **Sélectionner une vidéo web**, aucun élément n’est sélectionné initialement à partir du `ListView` de sorte que vous devez sélectionner l’une des vidéos avant le début de la lecture.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de lecteur vidéo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
