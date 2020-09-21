---
title: Xamarin.Forms fonctionnalités de l’appareil à deux écrans
description: Ce guide explique comment utiliser la Xamarin.Forms classe DualScreenInfo pour optimiser votre expérience d’application pour les appareils à deux écrans tels que surface Duo et surface Neo.
ms.prod: xamarin
ms.assetid: dd5eb074-f4cb-4ab4-b47d-76f862ac7cfa
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8081eb604da0c9d2de07ee17abe05030efdc1005
ms.sourcegitcommit: 69bd0fdc698c9b0c0d73217776d7084f32ae88ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832292"
---
# <a name="no-locxamarinforms-dualscreeninfo-helper-class"></a>Xamarin.Forms Classe d’assistance DualScreenInfo

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La classe `DualScreenInfo` vous permet de déterminer le volet où se trouve votre vue, sa taille, la position de l’appareil, l’angle de la charnière, etc.

## <a name="configure-dualscreeninfo"></a>Configurer DualScreenInfo

Suivez ces instructions pour créer une disposition à deux écrans dans votre application :

1. Suivez les instructions de [prise en main](index.md) pour ajouter NuGet et configurer la `MainActivity` classe Android.
1. Ajoutez `using Xamarin.Forms.DualScreen;` à votre fichier de classe.
1. Utilisez la `DualScreenInfo.Current` classe dans votre application.

## <a name="properties"></a>Propriétés

- `SpanningBounds` retourne, dans le cas d’une répartition sur deux écrans, deux rectangles indiquant les limites de chaque zone visible. Si la fenêtre n’est pas répartie sur deux écrans, un tableau vide est retourné.
- `HingeBounds` indique la position de la charnière sur l’écran.
- `IsLandscape` : indique si l’appareil est en mode paysage. Ceci est utile, car les API d’orientation natives ne signalent pas correctement l’orientation quand l’application est étendue.
- `SpanMode` : indique si la disposition est en mode Tall, Wide ou SinglePane.

En outre, l' `PropertyChanged` événement se déclenche quand des propriétés changent et que l' `HingeAngleChanged` événement se déclenche lorsque l’angle de la charnière change.

## <a name="poll-hinge-angle-on-android-and-uwp"></a>Angle d’interrogation des charnières sur Android et UWP

La méthode suivante est disponible lors de l’accès `DualScreenInfo` à partir de projets de plateforme Android et UWP :

- `GetHingeAngleAsync` : récupère l’angle actuel de la charnière de l’appareil. Quand vous utilisez le simulateur, vous pouvez définir HingeAngle en modifiant le capteur de pression.

Cette méthode peut être appelée à partir de convertisseurs personnalisés sur Android et UWP. Le code suivant illustre un exemple de convertisseur personnalisé Android :

```csharp
public class HingeAngleLabelRenderer : Xamarin.Forms.Platform.Android.FastRenderers.LabelRenderer
{
    System.Timers.Timer _hingeTimer;
    public HingeAngleLabelRenderer(Context context) : base(context)
    {
    }

    async void OnTimerElapsed(object sender, System.Timers.ElapsedEventArgs e)
    {
        if (_hingeTimer == null)
            return;

        _hingeTimer.Stop();
        var hingeAngle = await DualScreenInfo.Current.GetHingeAngleAsync();

        Device.BeginInvokeOnMainThread(() =>
        {
            if (_hingeTimer != null)
                Element.Text = hingeAngle.ToString();
        });

        if (_hingeTimer != null)
            _hingeTimer.Start();
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Label> e)
    {
        base.OnElementChanged(e);

        if (_hingeTimer == null)
        {
            _hingeTimer = new System.Timers.Timer(100);
            _hingeTimer.Elapsed += OnTimerElapsed;
            _hingeTimer.Start();
        }
    }

    protected override void Dispose(bool disposing)
    {
        if (_hingeTimer != null)
        {
            _hingeTimer.Elapsed -= OnTimerElapsed;
            _hingeTimer.Stop();
            _hingeTimer = null;
        }

        base.Dispose(disposing);
    }
}
```

## <a name="access-dualscreeninfo-in-your-application-window"></a>Accéder à DualScreenInfo pour la fenêtre de votre application

Le code suivant montre comment accéder à `DualScreenInfo` pour la fenêtre de votre application :

```csharp
DualScreenInfo currentWindow = DualScreenInfo.Current;

// Retrieve absolute position of the hinge on the screen
var hingeBounds = currentWindow.HingeBounds;

// check if app window is spanned across two screens
if(currentWindow.SpanMode == TwoPaneViewMode.SinglePane)
{
    // window is only on one screen
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Tall)
{
    // window is spanned across two screens and oriented top-bottom
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Wide)
{
    // window is spanned across two screens and oriented side-by-side
}

// Detect if any of the properties on DualScreenInfo change.
// This is useful to detect if the app window gets spanned
// across two screens or put on only one  
currentWindow.PropertyChanged += OnDualScreenInfoChanged;
```

## <a name="apply-dualscreeninfo-to-layouts"></a>Appliquer DualScreenInfo à des dispositions

La classe `DualScreenInfo` a un constructeur qui peut prendre une disposition et vous fournit des informations sur la disposition relativement aux deux écrans des appareils :

```xaml
<Grid x:Name="grid" ColumnSpacing="0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="{Binding Column1Width}" />
        <ColumnDefinition Width="{Binding Column2Width}" />
        <ColumnDefinition Width="{Binding Column3Width}" />
    </Grid.ColumnDefinitions>
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="End"
           Text="I should be on the left side of the hinge" />
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="Start"
           Grid.Column="2"
           Text="I should be on the right side of the hinge" />
</Grid>
```

```csharp
public partial class GridUsingDualScreenInfo : ContentPage
{
    public DualScreenInfo DualScreenInfo { get; }
    public double Column1Width { get; set; }
    public double Column2Width { get; set; }
    public double Column3Width { get; set; }

    public GridUsingDualScreenInfo()
    {
        InitializeComponent();
        DualScreenInfo = new DualScreenInfo(grid);
        BindingContext = this;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        DualScreenInfo.PropertyChanged += OnInfoPropertyChanged;
        UpdateColumns();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        DualScreenInfo.PropertyChanged -= OnInfoPropertyChanged;
    }

    void UpdateColumns()
    {
        // Check if grid is on two screens
        if (DualScreenInfo.SpanningBounds.Length > 0)
        {
            // set the width of the first column to the width of the layout
            // that's on the left screen
            Column1Width = DualScreenInfo.SpanningBounds[0].Width;

            // set the middle column to the width of the hinge
            Column2Width = DualScreenInfo.HingeBounds.Width;

            // set the width of the third column to the width of the layout
            // that's on the right screen
            Column3Width = DualScreenInfo.SpanningBounds[1].Width;
        }
        else
        {
            Column1Width = 100;
            Column2Width = 0;
            Column3Width = 100;
        }

        OnPropertyChanged(nameof(Column1Width));
        OnPropertyChanged(nameof(Column2Width));
        OnPropertyChanged(nameof(Column3Width));

    }

    void OnInfoPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
    {
        UpdateColumns();
    }
}
```

La capture d’écran suivante montre la disposition résultante :

![Grille de positionnement sur deux écrans](dual-screen-info-images/grid-on-two-screens.png)

## <a name="related-links"></a>Liens connexes

- [DualScreen (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
