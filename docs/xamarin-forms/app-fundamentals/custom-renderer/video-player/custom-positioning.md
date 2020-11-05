---
title: Positionnement vidéo personnalisé
description: Cet article explique comment implémenter une barre de position personnalisée dans une application de lecteur vidéo à l’aide de Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 15529520668f4d6353fe99c13ddb2e6cc2801500
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367229"
---
# <a name="custom-video-positioning"></a>Positionnement vidéo personnalisé

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Les contrôles de transport implémentés par chaque plateforme incluent une barre de position. Cette barre ressemble à un curseur ou une barre de défilement et indique la position actuelle de la vidéo au sein de sa durée totale. En outre, l’utilisateur peut avancer ou reculer la barre de position vers une nouvelle position dans la vidéo.

Cet article explique comment vous pouvez implémenter votre propre barre de position personnalisée.

## <a name="the-duration-property"></a>Propriété Duration

Un élément d’information nécessaire au `VideoPlayer` pour prendre en charge une barre de position personnalisée est la durée de la vidéo. Le `VideoPlayer` définit une propriété `Duration` en lecture seule de type `TimeSpan` :

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

Comme la propriété `Status` décrite dans l’[article précédent](custom-transport.md), cette propriété `Duration` est en lecture seule. Elle est définie avec une `BindablePropertyKey` privée et peut uniquement être définie en référençant l’interface `IVideoPlayerController`, ce qui inclut cette propriété `Duration` :

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

Notez également le gestionnaire de modification de propriété qui appelle une méthode nommée `SetTimeToEnd`, décrite plus loin dans cet article.

La durée d’une vidéo n’est *pas* disponible immédiatement après la définition de la propriété `Source` du `VideoPlayer`. Le fichier vidéo doit être partiellement téléchargé avant que le lecteur vidéo sous-jacent ne puisse déterminer sa durée.

Voici comment chacun des renderers de plateforme obtient la durée de la vidéo :

### <a name="video-duration-in-ios"></a>Durée de la vidéo dans iOS

Dans iOS, la durée d’une vidéo est obtenue à partir de la propriété `Duration` de `AVPlayerItem`, mais pas immédiatement après la création de `AVPlayerItem`. Il est possible de définir un observateur iOS pour la propriété `Duration`, mais le `VideoPlayerRenderer` obtient la durée dans la méthode `UpdateStatus`, qui est appelée 10 fois par seconde :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

La méthode `ConvertTime` convertit un objet `CMTime` en une valeur `TimeSpan`.

### <a name="video-duration-in-android"></a>Durée de la vidéo dans Android

La propriété `Duration` du `VideoView` Android signale une durée valide en millisecondes quand l’événement `Prepared` de `VideoView` est déclenché. La classe `VideoPlayerRenderer` Android utilise ce gestionnaire pour obtenir la propriété `Duration` :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>Durée de la vidéo dans UWP

La propriété `NaturalDuration` de `MediaElement` est une valeur `TimeSpan` et devient valide quand `MediaElement` déclenche l’événement `MediaOpened` :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>Propriété Position

`VideoPlayer` a également besoin d’une propriété `Position` qui passe de zéro à `Duration` lors de la lecture de la vidéo. `VideoPlayer` implémente cette propriété comme la propriété `Position` dans le `MediaElement` UWP, qui est une propriété pouvant être liée normale avec des accesseurs `set` et `get` publics :

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

L’accesseur `get` retourne la position actuelle de la vidéo en cours de lecture, mais l’accesseur `set` vise à répondre à la manipulation de la barre de position par l’utilisateur en avançant ou en reculant la position de la vidéo.

Dans iOS et Android, la propriété qui obtient la position actuelle a uniquement un accesseur `get` et une méthode `Seek` est disponible pour effectuer cette deuxième tâche. En y réfléchissant, une méthode `Seek` distincte semble être une approche plus pratique qu’une seule propriété `Position`. Une seule propriété `Position` présente un problème inhérent : lors de la lecture de la vidéo, la propriété `Position` doit être mise à jour en permanence afin de refléter la nouvelle position. Toutefois, vous ne voulez pas que la plupart des modifications apportées à la propriété `Position` entraîne le déplacement du lecteur vidéo vers une nouvelle position dans la vidéo. Si cela se produit, le lecteur vidéo répond en cherchant la dernière valeur de la propriété `Position` et la vidéo n’avance pas.

Malgré les difficultés d’implémentation d’une propriété `Position` avec les accesseurs `set` et `get`, cette approche a été choisie, car elle est cohérente avec le `MediaElement` UWP et elle présente un avantage majeur avec la liaison de données : la propriété `Position` du `VideoPlayer` peut être liée au curseur qui est utilisé à la fois pour afficher la position et pour chercher une nouvelle position. Toutefois, quelques précautions sont nécessaires lors de l’implémentation de cette propriété `Position` afin d’éviter les boucles de rétroaction.

### <a name="setting-and-getting-ios-position"></a>Définition et obtention de la position iOS

Dans iOS, la propriété `CurrentTime` de l’objet `AVPlayerItem` indique la position actuelle de la vidéo en cours de lecture. Le `VideoPlayerRenderer` IOS définit la propriété `Position` dans le gestionnaire `UpdateStatus` en même temps qu’il définit la propriété `Duration` :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

Le renderer détecte quand la propriété `Position` définie à partir de `VideoPlayer` a changé dans le remplacement de `OnElementPropertyChanged` et utilise cette nouvelle valeur pour appeler une méthode `Seek` sur l’objet `AVPlayer` :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

Gardez à l’esprit que, chaque fois que la propriété `Position` dans `VideoPlayer` est définie à partir du gestionnaire `OnUpdateStatus`, la propriété `Position` déclenche un événement `PropertyChanged`, qui est détecté dans le remplacement de `OnElementPropertyChanged`. Pour la plupart de ces modifications, la méthode `OnElementPropertyChanged` ne doit rien faire. Sinon, avec chaque modification de la position de la vidéo, elle est déplacée vers la même position qu’elle vient d’atteindre !

Afin d’éviter cette boucle de rétroaction, la méthode `OnElementPropertyChanged` appelle uniquement `Seek` quand la différence entre la propriété `Position` et la position actuelle de `AVPlayer` est supérieure à une seconde.

### <a name="setting-and-getting-android-position"></a>Définition et obtention de la position Android

Comme dans le renderer iOS, le `VideoPlayerRenderer` Android définit une nouvelle valeur pour la propriété `Position` dans le gestionnaire `OnUpdateStatus`. La propriété `CurrentPosition` de `VideoView` contient la nouvelle position en unités de millisecondes :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

De plus, comme dans le renderer iOS, le renderer Android appelle la méthode `SeekTo` de `VideoView` quand la propriété `Position` a changé, mais uniquement lorsque la modification présente une différence de plus d’une seconde par rapport à la valeur `CurrentPosition` de `VideoView` :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>Définition et obtention de la position UWP

Le `VideoPlayerRenderer` UWP gère la `Position` de la même façon que les renderers iOS et Android mais, étant donné que la propriété `Position` de `MediaElement` UWP est également une valeur `TimeSpan`, aucune conversion n’est nécessaire :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>Calcul d’une propriété TimeToEnd

Les lecteurs vidéo affichent parfois la durée restante dans la vidéo. Cette valeur commence à la durée de la vidéo quand la vidéo démarre et diminue jusqu’à zéro quand la vidéo se termine.

`VideoPlayer` inclut une propriété `TimeToEnd` en lecture seule qui est gérée entièrement dans la classe selon les modifications apportées aux propriétés `Duration` et `Position` :

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

La méthode `SetTimeToEnd` est appelée à partir des gestionnaires de modification de propriété des deux propriétés `Duration` et `Position`.

## <a name="a-custom-slider-for-video"></a>Curseur personnalisé pour la vidéo

Il est possible d’écrire un contrôle personnalisé pour une barre de positionnement, ou d’utiliser Xamarin.Forms `Slider` ou une classe qui dérive de `Slider` , telle que la `PositionSlider` classe suivante. La classe définit deux nouvelles propriétés nommées `Duration` et `Position` de type `TimeSpan` qui sont destinées à être liées aux données des deux propriétés portant le même nom dans `VideoPlayer`. Notez que le mode de liaison par défaut de la propriété `Position` est la liaison bidirectionnelle :

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
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
    }
}
```

Le gestionnaire de modification de propriété pour la propriété `Duration` définit la propriété `Maximum` du `Slider` sous-jacent sur la propriété `TotalSeconds` de la valeur `TimeSpan`. De même, le gestionnaire de modification de propriété pour `Position` définit la propriété `Value` du `Slider`. De cette façon, le `Slider` sous-jacent effectue le suivi de la position du `PositionSlider`.

Le `PositionSlider` est mis à jour à partir du `Slider` sous-jacent dans une seule instance : quand l’utilisateur manipule le `Slider` pour indiquer que la vidéo doit être avancée ou inversée vers une nouvelle position. Cela est détecté dans le gestionnaire `PropertyChanged` dans le constructeur du `PositionSlider`. Le gestionnaire recherche un changement dans la propriété `Value` et, si elle est différente de la propriété `Position`, la propriété `Position` est définie à partir de la propriété `Value`.

En théorie, l’instruction `if` interne peut être écrite comme suit :

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

Toutefois, l’implémentation Android de `Slider` n’a que 1 000 étapes discrètes, quels que soient les paramètres `Minimum` et `Maximum`. Si la longueur d’une vidéo est supérieure à 1 000 secondes, deux valeurs `Position` différentes correspondent au même paramètre `Value` du `Slider` et cette instruction `if` déclenche un faux positif pour une manipulation du `Slider` par l’utilisateur. Il est préférable de vérifier plutôt que la nouvelle position et la position existante sont supérieures à un centième de la durée globale.

## <a name="using-the-positionslider"></a>Utilisation de PositionSlider

La documentation de la série UWP [`MediaElement`](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) vous avertit de la liaison à la `Position` propriété, car la propriété est fréquemment mise à jour. La documentation recommande d’utiliser une minuterie (Timer) pour interroger la propriété `Position`.

Il s’agit d’un conseil judicieux, mais les trois classes `VideoPlayerRenderer` utilisent déjà indirectement une minuterie pour mettre à jour la propriété `Position`. La propriété `Position` est modifiée dans un gestionnaire pour l’événement `UpdateStatus`, qui est déclenché uniquement 10 fois par seconde.

Par conséquent, la propriété `Position` du `VideoPlayer` peut être liée à la propriété `Position` du `PositionSlider` sans problèmes de performances, comme illustré dans la page **Barre de position personnalisée**  :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

Les premiers points de suspension (···) masquent l’élément `ActivityIndicator` ; il est identique à celui de la page **Transport personnalisé** précédente. Notez les deux éléments `Label` affichant les propriétés `Position` et `TimeToEnd`. Les points de suspension entre ces deux éléments `Label` masquent les deux éléments `Button` indiqués dans la page **Transport personnalisé** pour la lecture, la pause et l’arrêt. La logique code-behind est également la même que dans la page **Transport personnalisé**.

[![Positionnement personnalisé](custom-positioning-images/custompositioning-small.png "Positionnement personnalisé")](custom-positioning-images/custompositioning-large.png#lightbox "Positionnement personnalisé")

Ainsi se termine la discussion sur le `VideoPlayer`.

## <a name="related-links"></a>Liens associés

- [Démonstrations de lecteur vidéo (exemple)](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)