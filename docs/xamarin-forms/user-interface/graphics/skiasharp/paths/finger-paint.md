---
title: Peinture au doigt dans SkiaSharp
description: Cet article explique comment utiliser vos doigts sur lequel peindre la zone de dessin SkiaSharp dans une application Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: d5cf0927c64732d6d0a44204db9509fae77f0d1d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291986"
---
# <a name="finger-painting-in-skiasharp"></a>Peinture au doigt dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utilisez vos doigts pour peindre sur le canevas._

Un objet `SKPath` peut être continuellement mis à jour et affiché. Cette fonctionnalité permet à un chemin d’accès à utiliser pour le dessin interactive, comme dans un programme de peinture.

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

La prise en charge tactile dans Xamarin.Forms ne permet pas de suivre chaque doigt sur l’écran, donc un effet de suivi de tactile Xamarin.Forms a été développé pour fournir la prise en charge tactile supplémentaires. Cet effet est décrit dans l’article [**appel d’événements à partir d’effets**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Les [**démonstrations d’effet tactile**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) du programme incluent deux pages qui utilisent SkiaSharp, y compris un programme de peinture par doigt.

La solution [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) comprend cet événement de suivi tactile. Le projet de bibliothèque de .NET Standard comprend la classe `TouchEffect`, l’énumération `TouchActionType`, le `TouchActionEventHandler` délégué et la classe `TouchActionEventArgs`. Chacun des projets de plateforme comprend une classe `TouchEffect` pour cette plateforme. le projet iOS contient également une classe `TouchRecognizer`.

La page de **peinture par doigt** dans **SkiaSharpFormsDemos** est une implémentation simplifiée de la peinture par doigt. Il ne pas autoriser la sélection de couleur ou rayer la largeur, il n’a aucun moyen pour effacer la zone de dessin, et bien sûr vous ne pouvez pas enregistrer votre illustration.

Le fichier [**FingerPaintPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) place l' `SKCanvasView` dans un `Grid` à une seule cellule et joint le `TouchEffect` à ce `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

L’attachement du `TouchEffect` directement au `SKCanvasView` ne fonctionne pas sur toutes les plateformes.

Le fichier code-behind [**FingerPaintPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) définit deux collections pour le stockage des objets `SKPath`, ainsi qu’un objet `SKPaint` pour le rendu de ces chemins :

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

Comme son nom l’indique, le dictionnaire `inProgressPaths` stocke les tracés qui sont actuellement dessinés par un ou plusieurs doigts. La clé de dictionnaire est l’ID tactile qui accompagne les événements tactiles. Le champ `completedPaths` est une collection de chemins d’accès qui se sont terminés lorsqu’un doigt qui dessine le tracé a été levé à partir de l’écran.

Le gestionnaire de `TouchAction` gère ces deux collections. Quand un doigt touche l’écran pour la première fois, un nouveau `SKPath` est ajouté à `inProgressPaths`. Comme ce doigt se déplace, points supplémentaires sont ajoutés au chemin d’accès. Lorsque le doigt est relâché, le chemin d’accès est transféré à la collection `completedPaths`. Vous pouvez peindre avec plusieurs doigts simultanément. Après chaque modification apportée à l’un des chemins d’accès ou collections, l' `SKCanvasView` est invalidée :

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

Les points qui accompagne les événements de suivi de tactiles sont des coordonnées de Xamarin.Forms ; Il doivent être convertis en coordonnées de SkiaSharp, qui sont des pixels. C’est l’objectif de la méthode `ConvertToPixel`.

Le gestionnaire de `PaintSurface` restitue ensuite simplement les deux collections de chemins d’accès. Les chemins d’accès précédemment terminées s’affichent sous les chemins d’accès en cours d’exécution :

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

Votre peintures doigt sont uniquement limités par votre talent :

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Vous avez désormais appris à dessiner des lignes et de définir des courbes à l’aide des équations paramétriques. Une section ultérieure sur les [**courbes et chemins d’accès SkiaSharp**](../curves/index.md) couvre les différents types de courbes que `SKPath` prend en charge. Mais un prérequis utile est l’exploration des [**transformations SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Démonstrations d’effet de suivi tactile (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
