---
title: Données du chemin d’accès SVG dans SkiaSharp
description: Cet article explique comment définir des chemins d’accès SkiaSharp à l’aide de chaînes de texte dans le format de graphique vectoriel Scalable et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f2e6735be2593133f755f87b365b8352bf09ebb4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563079"
---
# <a name="svg-path-data-in-skiasharp"></a>Données du chemin d’accès SVG dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Définir des chemins d’accès à l’aide de chaînes de texte dans le format graphique vectoriel Scalable_

La [`SKPath`](xref:SkiaSharp.SKPath) classe prend en charge la définition d’objets de chemin d’accès complets à partir de chaînes de texte dans un format défini par la spécification SVG (Scalable Vector Graphics). Vous verrez plus loin dans cet article comment vous pouvez représenter un chemin d’accès complet tel que celui-ci dans une chaîne de texte :

![Exemple de chemin d’accès défini avec des données de chemin d’accès SVG](path-data-images/pathdatasample.png)

SVG est un langage de programmation graphique basé sur XML pour les pages Web. Étant donné que SVG doit autoriser les chemins d’accès à être définis dans le balisage plutôt que dans une série d’appels de fonction, la norme SVG comprend un moyen extrêmement concis de spécifier un chemin d’accès graphique entier comme une chaîne de texte.

Dans SkiaSharp, ce format est appelé « SVG Path-Data ». Le format est également pris en charge dans les environnements de programmation XAML Windows, y compris les Windows Presentation Foundation et les plateforme Windows universelle, où il est connu comme la [syntaxe de balisage de chemin d’accès](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) ou la [syntaxe de commandes Move et Draw](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Il peut également servir de format d’échange pour les images de graphiques vectoriels, en particulier dans les fichiers texte tels que XML.

La [`SKPath`](xref:SkiaSharp.SKPath) classe définit deux méthodes avec les mots `SvgPathData` de leur nom :

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

La [`ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) méthode statique convertit une chaîne en `SKPath` objet, tandis que [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) convertit un `SKPath` objet en chaîne.

Voici une chaîne SVG pour une étoile à cinq branches centrée sur le point (0,0) avec un rayon de 100 :

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Les lettres sont des commandes qui génèrent un `SKPath` objet : `M` indique un `MoveTo` appel, `L` est `LineTo` et `Z` est `Close` de fermer un contour. Chaque paire de nombres fournit une coordonnée X et Y d’un point. Notez que la `L` commande est suivie de plusieurs points séparés par des virgules. Dans une série de coordonnées et de points, les virgules et les espaces blancs sont traités de la même façon. Certains programmeurs préfèrent placer des virgules entre les coordonnées X et Y plutôt qu’entre les points, mais les virgules ou les espaces ne sont nécessaires que pour éviter toute ambiguïté. Cela est parfaitement légal :

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

La syntaxe des données de chemin d’accès SVG est formellement documentée dans [la Section 8,3 de la spécification SVG](https://www.w3.org/TR/SVG11/paths.html#PathData). Voici un résumé :

## <a name="moveto"></a>**DéplacerVers**

```
M x y
```

Cela commence un nouveau contour dans le tracé en définissant la position actuelle. Les données de chemin d’accès doivent toujours commencer par une `M` commande.

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

Cette commande ajoute une ligne droite (ou des lignes) au chemin d’accès et définit la nouvelle position actuelle à la fin de la dernière ligne. Vous pouvez suivre la `L` commande avec plusieurs paires de coordonnées *x* et *y* .

## <a name="horizontal-lineto"></a>**LineTo horizontal**

```
H x ...
```

Cette commande ajoute une ligne horizontale au tracé et définit la nouvelle position actuelle à la fin de la ligne. Vous pouvez suivre la `H` commande avec plusieurs coordonnées *x* , mais cela n’est pas très utile.

## <a name="vertical-line"></a>**Ligne verticale**

```
V y ...
```

Cette commande ajoute une ligne verticale au tracé et définit la nouvelle position actuelle à la fin de la ligne.

## <a name="close"></a>**Close**

```
Z
```

La `C` commande ferme le contour en ajoutant une ligne droite à partir de la position actuelle jusqu’au début du contour.

## <a name="arcto"></a>**ArcTo**

La commande permettant d’ajouter un arc elliptique au contour est de loin la commande la plus complexe dans la spécification de données de chemin d’accès SVG entière. Il s’agit de la seule commande dans laquelle les nombres peuvent représenter autre chose que les valeurs de coordonnée :

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

Les paramètres *RX* et *ry* sont le rayon horizontal et le rayon vertical de l’ellipse. L' *angle de rotation* est exprimé dans le sens des aiguilles d’une montre, en degrés.

Définissez l' *indicateur grand arc* sur 1 pour l’arc de grande taille ou sur 0 pour le petit arc.

Définissez l' *indicateur de balayage* sur 1 pour le sens des aiguilles d’une montre et sur 0 pour le sens des aiguilles d’une montre.

L’arc est dessiné jusqu’au point (*x*, *y*), qui devient la nouvelle position actuelle.

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

Cette commande ajoute une courbe de Bézier cubique de la position actuelle à (*x3*, *Y3*), qui devient la nouvelle position actuelle. Les points (*x1*, *Y1*) et (*x2*, *Y2*) sont des points de contrôle.

Plusieurs courbes de Bézier peuvent être spécifiées par une seule `C` commande. Le nombre de points doit être un multiple de 3.

Il y a également une commande de courbe de Bézier « lisse » :

```
S x2 y2 x3 y3 ...
```

Cette commande doit suivre une commande Bézier normale (bien que cela ne soit pas strictement requis). La commande de Bézier lisse calcule le premier point de contrôle de sorte qu’il s’agit d’une réflexion du deuxième point de contrôle de la courbe de Bézier précédente autour de son point mutuel. Ces trois points sont donc colinéaires, et la connexion entre les deux courbes de Bézier est lisse.

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

Pour les courbes de Bézier quadratiques, le nombre de points doit être un multiple de 2. Le point de contrôle est (*x1*, *Y1*) et le point de terminaison (et la nouvelle position actuelle) est (*x2*, *Y2*)

Il y a également une commande de courbe quadratique lisse :

```
T x2 y2 ...
```

Le point de contrôle est calculé en fonction du point de contrôle de la courbe quadratique précédente.

Toutes ces commandes sont également disponibles dans les versions « relatives », où les points de coordonnées sont relatifs à la position actuelle. Ces commandes relatives commencent par des lettres minuscules, par exemple `c` plutôt que `C` pour la version relative de la commande de Bézier cubique.

Il s’agit de l’étendue de la définition des données de chemin d’accès SVG. Il n’existe aucune fonctionnalité permettant de répéter des groupes de commandes ou d’effectuer n’importe quel type de calcul. Les commandes pour `ConicTo` ou les autres types de spécifications d’arc ne sont pas disponibles.

La [`SKPath.ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) méthode statique attend une chaîne valide de commandes SVG. Si une erreur de syntaxe est détectée, la méthode retourne `null` . Il s’agit de la seule indication d’erreur.

La [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) méthode est pratique pour obtenir des données de chemin d’accès SVG à partir d’un `SKPath` objet existant à transférer vers un autre programme, ou pour stocker dans un format de fichier texte tel que XML. (La `ToSvgPathData` méthode n’est pas illustrée dans l’exemple de code de cet article.) N' *not* attendez pas `ToSvgPathData` à retourner une chaîne correspondant exactement aux appels de méthode qui ont créé le chemin d’accès. En particulier, vous découvrirez que les arcs sont convertis en plusieurs `QuadTo` commandes, et c’est ainsi qu’ils apparaissent dans les données de chemin d’accès retournées par `ToSvgPathData` .

La page de **salutation des données de chemin d’accès** épele le mot « Hello » à l’aide de données de chemin SVG. Les `SKPath` objets et `SKPaint` sont définis en tant que champs dans la [`PathDataHelloPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe :

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

Le chemin d’accès qui définit la chaîne de texte commence à l’angle supérieur gauche au niveau du point (0,0). Chaque lettre est de 50 unités et de 100 unités de hauteur, et les lettres sont séparées par 25 unités, ce qui signifie que le chemin d’accès complet est de 350 unités de largeur.

Le « H » de « Hello » est composé de contournements de ligne 3 1, tandis que le « E » est deux courbes de Bézier cubiques connectées. Notez que la `C` commande est suivie de six points et que deux des points de contrôle ont des coordonnées y de-10 et 110, qui les place en dehors de la plage des coordonnées y des autres lettres. Le « L » est deux lignes connectées, tandis que le « O » est une ellipse qui est restituée avec une `A` commande.

Notez que la `M` commande qui commence le dernier contour définit la position sur le point (350, 50), qui est le centre vertical du côté gauche du « O ». Comme indiqué par les premiers nombres qui suivent la `A` commande, l’ellipse a un rayon horizontal de 25 et un rayon vertical de 50. Le point de terminaison est indiqué par la dernière paire de nombres dans la `A` commande, qui représente le point (300, 49,9). Cela est délibérément légèrement différent du point de départ. Si le point de terminaison est défini comme étant égal au point de départ, l’ARC ne sera pas rendu. Pour dessiner une ellipse complète, vous devez définir le point de terminaison proche (mais pas égal à) au point de départ, ou vous devez utiliser deux `A` commandes ou plus, chacune pour une partie de l’ellipse complète.

Vous pouvez ajouter l’instruction suivante au constructeur de la page, puis définir un point d’arrêt pour examiner la chaîne résultante :

```csharp
string str = helloPath.ToSvgPathData();
```

Vous découvrirez que l’ARC a été remplacé par une longue série de `Q` commandes pour une approximation fragmentaire de l’arc à l’aide des courbes de Bézier quadratiques.

Le `PaintSurface` Gestionnaire obtient les limites étroites du chemin d’accès, qui n’inclut pas les points de contrôle pour les courbes « E » et « O ». Les trois transformations déplacent le centre du tracé jusqu’au point (0,0), ajustent le tracé à la taille de la zone de dessin (mais prennent également en compte la largeur du trait), puis déplacent le centre du tracé au centre de la zone de dessin :

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

Le chemin d’accès remplit le canevas, ce qui semble plus raisonnable lorsqu’il est affiché en mode paysage :

[![Capture d’écran triple de la page de salutation des données de chemin](path-data-images/pathdatahello-small.png)](path-data-images/pathdatahello-large.png#lightbox "Capture d’écran triple de la page de salutation des données de chemin")

La page de **données du chemin d’accès** est similaire. Les objets Path et Paint sont définis en tant que champs dans la [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe :

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

Le début d’un chat est un cercle et, ici, il est affiché avec deux `A` commandes, chacune dessinant un demi-cercle. Les deux `A` commandes pour l’en-tête définissent des rayons horizontaux et verticaux de 100. Le premier arc commence à (240, 100) et se termine à (240, 300), qui devient le point de départ pour le deuxième arc qui se termine à (240, 100).

Les deux yeux sont également rendus avec deux `A` commandes, et comme avec la tête du chat, la deuxième `A` commande se termine au même point que le début de la première `A` commande. Toutefois, ces paires de `A` commandes ne définissent pas d’ellipse. Le avec de chaque arc est de 40 unités et le rayon est également de 40 unités, ce qui signifie que ces arcs ne sont pas des demi-cercles pleins.

Le `PaintSurface` Gestionnaire effectue des transformations similaires à l’exemple précédent, mais définit un `Scale` facteur unique pour conserver les proportions et fournir une petite marge afin que les moustaches de la CAT ne touchent pas les côtés de l’écran :

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

Voici le programme en cours d’exécution :

[![Capture d’écran triple de la page de la CAT des données de chemin](path-data-images/pathdatacat-small.png)](path-data-images/pathdatacat-large.png#lightbox "Capture d’écran triple de la page de la CAT des données de chemin")

Normalement, lorsqu’un `SKPath` objet est défini en tant que champ, les contournements du chemin d’accès doivent être définis dans le constructeur ou une autre méthode. Toutefois, lorsque vous utilisez des données de chemin d’accès SVG, vous avez vu que le chemin d’accès peut être spécifié entièrement dans la définition du champ.

L’exemple de l' **horloge analogique** la plus ancienne dans l’article [**transformation de rotation**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) a présenté les mains de l’horloge sous forme de lignes simples. Le programme d' **horloge Pretty-Analog** ci-dessous remplace ces lignes par des `SKPath` objets définis en tant que champs dans la classe avec les [`PrettyAnalogClockPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) `SKPaint` objets :

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

Les heures et les minutes comportent désormais des zones délimitées. Pour que ces mains soient distinctes les unes des autres, elles sont dessinées à la fois avec un contour noir et un remplissage gris à l’aide des `handStrokePaint` `handFillPaint` objets et.

Dans l’exemple d' **horloge analogique** inhabituelle précédent, les petits cercles qui ont marqué les heures et les minutes ont été dessinés dans une boucle. Dans cet exemple d' **horloge relativement analogique** , une approche entièrement différente est utilisée : les marques d’heure et de minute sont des lignes en pointillés dessinées avec les `minuteMarkPaint` `hourMarkPaint` objets et :

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

L’article [**points et tirets**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) a expliqué comment vous pouvez utiliser la [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash*) méthode pour créer une ligne en pointillés. Le premier argument est un `float` tableau qui a généralement deux éléments : le premier élément est la longueur des tirets, et le deuxième élément est l’intervalle entre les tirets. Lorsque la `StrokeCap` propriété a la valeur `SKStrokeCap.Round` , les extrémités arrondies du tiret allongent effectivement la longueur du tiret en fonction de la largeur du trait sur les deux côtés du tiret. Par conséquent, le fait de définir le premier élément du tableau sur 0 crée une ligne en pointillés.

La distance entre ces points est régie par le deuxième élément du tableau. Comme vous le verrez bientôt, ces deux `SKPaint` objets sont utilisés pour dessiner des cercles avec un rayon de 90 unités. La circonférence de ce cercle est donc de 180 π, ce qui signifie que les marques de 60 minutes doivent apparaître toutes les 3π, qui est la deuxième valeur du `float` tableau dans `minuteMarkPaint` . Les marques de 12 heures doivent apparaître toutes les unités 15π, qui est la valeur du deuxième `float` tableau.

La `PrettyAnalogClockPage` classe définit un minuteur pour invalider la surface toutes les 16 millisecondes, et le `PaintSurface` gestionnaire est appelé à ce taux. Les définitions précédentes des `SKPath` objets et `SKPaint` permettent un code de dessin très propre :

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

Toutefois, une opération spéciale est effectuée avec la seconde main. Étant donné que l’horloge est mise à jour toutes les 16 millisecondes, la `Millisecond` propriété de la `DateTime` valeur peut potentiellement être utilisée pour animer une seconde main au lieu d’une qui déplace les sauts discrets de la seconde à la seconde. Mais ce code ne permet pas de lisser le mouvement. Au lieu de cela, il utilise les Xamarin.Forms [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) fonctions d’accélération d’animation et pour un autre type de déplacement. Ces fonctions d’accélération provoquent le déplacement de la seconde main d’une manière jerkier, en &mdash; retirant un peu avant son déplacement, puis en déplaçant légèrement la destination, un effet qui ne peut malheureusement pas être reproduit dans les captures d’écran statiques suivantes :

[![Capture d’écran triple de la page de l’horloge analogique](path-data-images/prettyanalogclock-small.png)](path-data-images/prettyanalogclock-large.png#lightbox "Capture d’écran triple de la page de l’horloge analogique")

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)