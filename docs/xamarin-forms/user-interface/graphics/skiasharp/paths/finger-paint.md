---
title: Peinture par doigt dans SkiaSharp
description: Cet article explique comment utiliser vos doigts pour peindre sur le canevas SkiaSharp dans une Xamarin.Forms application et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98722d29a674e4d69f31a670a541eaabb550898e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555643"
---
# <a name="finger-painting-in-skiasharp"></a>Peinture par doigt dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utilisez vos doigts pour peindre sur le canevas._

Un `SKPath` objet peut être continuellement mis à jour et affiché. Cette fonctionnalité permet d’utiliser un chemin d’accès pour le dessin interactif, par exemple dans un programme de peinture par doigt.

![Exercice de peinture par doigt](finger-paint-images/fingerpaintsample.png)

La prise en charge de Touch dans Xamarin.Forms n’autorise pas le suivi des doigts individuels sur l’écran, ce qui Xamarin.Forms a pour effet de développer un effet tactile pour fournir un support tactile supplémentaire. Cet effet est décrit dans l’article [**appel d’événements à partir d’effets**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Les [**démonstrations d’effet tactile**](/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) du programme incluent deux pages qui utilisent SkiaSharp, y compris un programme de peinture par doigt.

La solution [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) comprend cet événement de suivi tactile. Le projet de bibliothèque .NET Standard comprend la `TouchEffect` classe, l' `TouchActionType` énumération, le `TouchActionEventHandler` délégué et la `TouchActionEventArgs` classe. Chacun des projets de plateforme inclut une `TouchEffect` classe pour cette plateforme ; le projet iOS contient également une `TouchRecognizer` classe.

La page de **peinture par doigt** dans **SkiaSharpFormsDemos** est une implémentation simplifiée de la peinture par doigt. Elle n’autorise pas la sélection de la couleur ou de la largeur du trait, mais elle n’a aucun moyen d’effacer la zone de dessin et, bien sûr, vous ne pouvez pas enregistrer votre illustration.

Le fichier [**FingerPaintPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) place le `SKCanvasView` dans une cellule unique `Grid` et l’attache `TouchEffect` à ce qui `Grid` suit :

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

L’attachement de l' `TouchEffect` directement à l' `SKCanvasView` ne fonctionne pas sur toutes les plateformes.

Le fichier code-behind  [**FingerPaintPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) définit deux collections pour le stockage des `SKPath` objets, ainsi qu’un `SKPaint` objet pour le rendu de ces chemins :

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

Comme son nom l’indique, le `inProgressPaths` dictionnaire stocke les tracés qui sont actuellement dessinés par un ou plusieurs doigts. La clé du dictionnaire est l’ID tactile qui accompagne les événements tactiles. Le `completedPaths` champ est une collection de chemins d’accès qui se sont terminés lorsqu’un doigt qui dessine le tracé a été levé à partir de l’écran.

Le `TouchAction` Gestionnaire gère ces deux collections. Quand un doigt touche l’écran pour la première fois, un nouveau `SKPath` est ajouté à `inProgressPaths` . Au fur et à mesure que ce doigt se déplace, des points supplémentaires sont ajoutés au tracé. Lorsque le doigt est relâché, le chemin d’accès est transféré à la `completedPaths` collection. Vous pouvez peindre avec plusieurs doigts simultanément. Après chaque modification apportée à l’un des chemins d’accès ou collections, `SKCanvasView` est invalidée :

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

Les points accompagnant les événements de suivi tactile sont des Xamarin.Forms coordonnées ; ils doivent être convertis en coordonnées SkiaSharp, qui sont des pixels. C’est l’objectif de la `ConvertToPixel` méthode.

Le `PaintSurface` Gestionnaire restitue ensuite simplement les deux collections de chemins d’accès. Les chemins d’accès terminés précédemment s’affichent sous les chemins d’accès en cours :

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

Vos peintures Finger ne sont limitées que par vos talents :

[![Triple capture d’écran de la page Finger Paint](finger-paint-images/fingerpaint-small.png)](finger-paint-images/fingerpaint-large.png#lightbox "Triple capture d’écran de la page Finger Paint")

Vous avez maintenant vu comment dessiner des lignes et définir des courbes à l’aide d’équations paramétriques. Une section ultérieure sur les [**courbes et chemins d’accès SkiaSharp**](../curves/index.md) couvre les différents types de courbes `SKPath` pris en charge par. Mais un prérequis utile est l’exploration des [**transformations SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Démonstrations d’effet de suivi tactile (exemple)](/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)