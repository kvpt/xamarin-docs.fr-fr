---
title: Peinture au doigt dans SkiaSharp
description: Cet article explique comment utiliser vos doigts sur lequel peindre la zone de dessin SkiaSharp dans une application Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: 571ddae0757691cd7fee301076f0b1310749531d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657458"
---
# <a name="finger-painting-in-skiasharp"></a>Peinture au doigt dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utilisez vos doigts pour peindre sur le canevas._

Un `SKPath` objet peut être mis à jour et affiché en permanence. Cette fonctionnalité permet à un chemin d’accès à utiliser pour le dessin interactive, comme dans un programme de peinture.

![](finger-paint-images/fingerpaintsample.png "Un exercice de peinture au doigt")

La prise en charge tactile dans Xamarin.Forms ne permet pas de suivre chaque doigt sur l’écran, donc un effet de suivi de tactile Xamarin.Forms a été développé pour fournir la prise en charge tactile supplémentaires. Cet effet est décrite dans l’article [ **appel des événements à partir des effets**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). L’exemple de programme [ **-suivi tactile effet démonstrations** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) inclut deux pages qui utilisent SkiaSharp, y compris un programme de peinture.

Le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) solution inclut cet événement de suivi de tactile. Le projet de bibliothèque .NET Standard inclut la `TouchEffect` (classe), le `TouchActionType` énumération, le `TouchActionEventHandler` déléguer et le `TouchActionEventArgs` classe. Chacun des projets de plateforme inclut un `TouchEffect` classe pour cette plateforme ; le projet iOS contient également un `TouchRecognizer` classe.

Le **peinture au doigt** page **SkiaSharpFormsDemos** est une implémentation simplifiée de peinture au doigt. Il ne pas autoriser la sélection de couleur ou rayer la largeur, il n’a aucun moyen pour effacer la zone de dessin, et bien sûr vous ne pouvez pas enregistrer votre illustration.

Le [ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml) fichier met le `SKCanvasView` dans une cellule unique `Grid` et attache le `TouchEffect` à celle `Grid`:

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

Attacher le `TouchEffect` directement à la `SKCanvasView` ne fonctionne pas sous toutes les plateformes.

Le [ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs) fichier code-behind définit deux collections pour stocker le `SKPath` objets, ainsi qu’une `SKPaint` objet pour le rendu de ces chemins d’accès :

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

Comme son nom l’indique, le `inProgressPaths` dictionnaire stocke les chemins d’accès qui sont actuellement en cours dessinées par un ou plusieurs doigts. La clé de dictionnaire est l’ID tactile qui accompagne les événements tactiles. Le `completedPaths` champ est une collection de chemins d’accès qui ont été terminées lorsqu’un doigt qui a été le tracé levé à partir de l’écran.

Le `TouchAction` Gestionnaire gère ces deux collections. Lorsqu’un doigt touche en premier l’écran, un nouveau `SKPath` est ajouté à `inProgressPaths`. Comme ce doigt se déplace, points supplémentaires sont ajoutés au chemin d’accès. Lorsque le doigt est lancé, le chemin d’accès est transféré vers le `completedPaths` collection. Vous pouvez peindre avec plusieurs doigts simultanément. Après chaque modification apportée à un des chemins d’accès ou des collections, la `SKCanvasView` est non valide :

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

Les points qui accompagne les événements de suivi de tactiles sont des coordonnées de Xamarin.Forms ; Il doivent être convertis en coordonnées de SkiaSharp, qui sont des pixels. C’est l’objectif de la `ConvertToPixel` (méthode).

Le `PaintSurface` Gestionnaire simplement restitue ensuite les deux collections de chemins d’accès. Les chemins d’accès précédemment terminées s’affichent sous les chemins d’accès en cours d’exécution :

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

[![](finger-paint-images/fingerpaint-small.png "Capture d’écran triple de la page de peinture au doigt")](finger-paint-images/fingerpaint-large.png#lightbox "Triple capture d’écran de la page de peinture au doigt")

Vous avez désormais appris à dessiner des lignes et de définir des courbes à l’aide des équations paramétriques. Une section ultérieure sur [ **SkiaSharp courbes et chemins d’accès** ](../curves/index.md) couvre les différents types de courbes qui `SKPath` prend en charge. Mais une condition préalable utile est une exploration de [ **SkiaSharp transforme**](../transforms/index.md).

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Suivi tactile effet démonstrations (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
