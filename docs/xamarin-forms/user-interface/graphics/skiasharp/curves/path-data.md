---
title: Données de chemin SVG dans SkiaSharp
description: Cet article explique comment définir les chemins d’accès SkiaSharp à l’aide de chaînes de texte dans le format Scalable Vector Graphics et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
ms.openlocfilehash: a645ef4ed55c8b4c78bad94865777088f62f1175
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647403"
---
# <a name="svg-path-data-in-skiasharp"></a>Données de chemin SVG dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Définir les chemins d’accès à l’aide de chaînes de texte dans le format Scalable Vector Graphics_

Le [ `SKPath` ](xref:SkiaSharp.SKPath) classe prend en charge la définition des objets de chemin d’accès complet à partir de chaînes de texte dans un format établi par la spécification de graphiques SVG (Scalable Vector). Vous verrez plus loin dans cet article comment vous pouvez représenter un chemin d’accès complet comme celle-ci dans une chaîne de texte :

![](path-data-images/pathdatasample.png "Un chemin d’accès de l’exemple défini avec les données de chemin SVG")

SVG est un langage de programmation pour les pages web des graphiques basés sur XML. Étant donné que SVG doit autoriser les chemins d’accès soient définies dans le balisage plutôt qu’une série d’appels de fonction, le format SVG standard inclut un moyen extrêmement concis de la spécification d’un chemin d’accès de graphique entière comme une chaîne de texte.

Dans SkiaSharp, ce format est dénommé « Chemin d’accès SVG-données ». Le format est également pris en charge dans les environnements programmation basée sur XAML de Windows, y compris la Windows Presentation Foundation et la plateforme Windows universelle, où il est connu comme le [syntaxe de balisage de chemin d’accès](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) ou le [déplacer Dessinez la syntaxe des commandes](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Il peut également servir à un format d’échange pour les images de graphiques vectoriels, en particulier dans des fichiers texte tel que XML.

Le [ `SKPath` ](xref:SkiaSharp.SKPath) classe définit deux méthodes avec les mots `SvgPathData` dans leurs noms :

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

La méthode statique [ `ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) méthode convertit une chaîne en un `SKPath` objet, tandis que [ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData) convertit un `SKPath` objet en une chaîne.

Voici une chaîne SVG pour une étoile à cinq branches centrée sur le point (0, 0) avec un rayon de 100 :

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Les lettres sont des commandes qui génèrent un `SKPath` objet : `M` indique un `MoveTo` appeler, `L` est `LineTo`, et `Z` est `Close` pour fermer un contour. Chaque paire de numéros fournit une coordonnée X et Y d’un point. Notez que le `L` commande suivie de plusieurs points séparés par des virgules. Dans une série de coordonnées et les points, les virgules et les espaces blancs sont traités de manière identique. Certains programmeurs préfèrent placé des virgules entre les coordonnées X et Y, et non entre les points, mais des virgules ou des espaces sont uniquement requises pour éviter toute ambiguïté. Cela est parfaitement légal :

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

La syntaxe de données de chemin SVG est documentée de façon formelle dans [8.3 de Section de la spécification SVG](http://www.w3.org/TR/SVG11/paths.html#PathData). Voici un résumé :

## <a name="moveto"></a>**MoveTo**

```
M x y
```

Commence un nouveau profil dans le chemin d’accès en définissant la position actuelle. Les données de chemin d’accès doivent commencer par un `M` commande.

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

Cette commande ajoute une ligne droite (ou lignes) pour le chemin d’accès et définit la nouvelle position actuelle jusqu'à la fin de la dernière ligne. Vous pouvez suivre la `L` avec plusieurs paires de *x* et *y* coordonnées.

## <a name="horizontal-lineto"></a>**LineTo horizontal**

```
H x ...
```

Cette commande ajoute une ligne horizontale dans le chemin d’accès et définit la nouvelle position actuelle jusqu'à la fin de la ligne. Vous pouvez suivre la `H` commande avec plusieurs *x* coordonnées, mais il ne rend pas beaucoup de sens.

## <a name="vertical-line"></a>**Ligne verticale**

```
V y ...
```

Cette commande ajoute une ligne verticale dans le chemin d’accès et définit la nouvelle position actuelle jusqu'à la fin de la ligne.

## <a name="close"></a>**Fermer**

```
Z
```

Le `C` commande ferme le contour en ajoutant une ligne droite à partir de la position actuelle vers le début du contour.

## <a name="arcto"></a>**ArcTo**

La commande pour ajouter un arc elliptique au contour est de loin la commande plus complexe dans la spécification de données de chemin SVG entière. Il est la seule commande dans lequel nombres peuvent représenter un élément autre que les valeurs de coordonnées :

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

Le *rx* et *ry* paramètres sont les rayons horizontaux et verticaux de l’ellipse. Le *angle de rotation* est dans le sens horaire en degrés.

Définir le *grand arc-indicateur* 1 de l’arc volumineux ou 0 pour le petit arc.

Définir le *balayage-indicateur* à 1 pour dans le sens horaire et la valeur 0 pour le sens antihoraire.

L’arc est dessiné au point (*x*, *y*), qui devient la nouvelle position actuelle.

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

Cette commande ajoute une courbe de Bézier cubique à partir de la position actuelle pour (*x3*, *y3*), qui devient la nouvelle position actuelle. Les points (*x1*, *y1*) et (*x2*, *y2*) sont des points de contrôle.

Plusieurs courbes de Bézier peuvent être spécifiés par un seul `C` commande. Le nombre de points doit être un multiple de 3.

Il existe également une commande de courbe de Bézier « bon » :

```
S x2 y2 x3 y3 ...
```

Cette commande doit suivre une commande de Bézier régulière (bien que cela n’est pas strictement nécessaire). La commande de Bézier smooth calcule le premier point de contrôle afin qu’il soit une réflexion du deuxième point de contrôle de la courbe de Bézier précédent autour de leur point mutuelle. Ces trois points sont par conséquent colinéaires, et la connexion entre les deux courbes de Bézier est lisse.

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

Pour les courbes de Bézier quadratiques, le nombre de points doit être un multiple de 2. Est le point de contrôle (*x1*, *y1*) et le point de terminaison (et la nouvelle position actuelle) est (*x2*, *y2*)

Il existe également une commande de courbe quadratique lissée :

```
T x2 y2 ...
```

Le point de contrôle est calculé en fonction du point de contrôle de la courbe quadratique précédente.

Toutes ces commandes sont également disponibles dans les versions « relatives », où les points de coordonnées sont relatif à la position actuelle. Ces commandes relatifs commencent par des lettres minuscules, par exemple `c` plutôt que `C` pour la version relative de la commande de courbe de Bézier cubique.

Il s’agit de l’étendue de la définition de données de chemin SVG. Il n’existe aucune fonctionnalité pour répéter des groupes de commandes ou l’exécution de n’importe quel type de calcul. Commandes pour `ConicTo` ou les autres types de spécifications de l’arc ne sont pas disponibles.

La méthode statique [ `SKPath.ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) méthode attend une chaîne valide des commandes SVG. Si une erreur de syntaxe est détectée, la méthode retourne `null`. C’est l’indication d’erreur uniquement.

Le [ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData) méthode est pratique pour obtenir des données de chemin SVG existant `SKPath` objet à transférer vers un autre programme ou à stocker dans un format de fichier texte tel que XML. (Le `ToSvgPathData` méthode n’est pas décrite dans l’exemple de code dans cet article.) Faire *pas* attendent `ToSvgPathData` pour retourner une chaîne correspondant exactement les appels de méthode qui a créé le chemin d’accès. En particulier, vous découvrirez que les arcs sont convertis en plusieurs `QuadTo` commandes, et c’est comment ils apparaissent dans les données de chemin d’accès retournées par `ToSvgPathData`.

Le **chemin d’accès données Hello** page correcteurs orthographiques le mot « HELLO » à l’aide des données de chemin SVG. À la fois le `SKPath` et `SKPaint` objets sont définis en tant que champs dans le [ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe :

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

Le chemin d’accès de définition de la chaîne de texte commence dans le coin supérieur gauche au niveau du point (0, 0). Chaque lettre est 50 unités de large et 100 unités de hauteur et des lettres sont séparés par un autre 25 unités, ce qui signifie que le chemin d’accès complet est 350 unités large.

« H » de « Hello » est composée de trois profils d’une seule ligne, tandis que le « E » est deux courbes de Bézier cubiques connectées. Notez que la `C` commande est suivie de six points, et deux les points de contrôle possèdent des coordonnées Y de -10 et 110, ce qui les met en dehors de la plage des coordonnées Y des autres lettres. La lettre « L » est de deux lignes connectées, tandis que le ' o ' est une ellipse qui est restituée avec un `A` commande.

Notez que le `M` commande qui commence le contour dernière définit la position au point (350, 50), qui est au centre vertical de la gauche de la « o '. Comme indiqué par la première qui suit les numéros les `A` commande, l’ellipse a un rayon horizontal du 25 et un rayon vertical de 50. Le point de terminaison est indiqué par la dernière paire de nombres dans la `A` commande, qui représente le point (300, 49,9). Qui est délibérément légèrement différent du point de départ. Si le point de terminaison est défini comme égal au point de départ, l’arc n’est pas rendue. Pour dessiner une ellipse complète, vous devez définir le point de terminaison proche de (mais non égal à) le point de départ, ou vous devez utiliser deux ou plusieurs `A` des commandes, chacun d’eux pour la partie de l’ellipse complète.

Vous souhaiterez peut-être ajouter l’instruction suivante au constructeur de la page et définissez un point d’arrêt pour examiner la chaîne résultante :

```csharp
string str = helloPath.ToSvgPathData();
```

Vous découvrirez que l’arc a été remplacée par une longue série de `Q` commandes pour une approximation ponctuelle de l’arc à l’aide de courbes de Bézier quadratiques.

Le `PaintSurface` gestionnaire obtient les limites étroite du chemin d’accès, qui n’inclut pas les points de contrôle pour le « E » et « o ' courbes. Les trois transformations déplacement le centre du chemin d’accès au point (0, 0), le chemin d’accès à la taille de la zone de dessin (mais compte tenu également la largeur du trait) de mettre à l’échelle, puis déplacement le centre du chemin d’accès au centre de la zone de dessin :

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

Le chemin d’accès remplit la zone de dessin, ce qui ressemble plus raisonnable lorsqu’ils sont affichés en mode paysage :

[![](path-data-images/pathdatahello-small.png "Capture d’écran triple de la page Hello chemin d’accès données")](path-data-images/pathdatahello-large.png#lightbox "Triple capture d’écran de la page Hello chemin d’accès données")

Le **Cat de données de chemin d’accès** page est similaire. Les objets de chemin d’accès et paint sont définis en tant que champs dans le [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe :

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

L’en-tête d’un Chat est un cercle et ici, il est restitué avec deux `A` commandes, chacun d’eux Dessine un demi-cercle. Les deux `A` commandes pour la tête de définir des rayons horizontaux et verticaux de 100. Le premier secteur commence à (240, 100) et se termine à (240, 300), qui devient le point de départ pour le deuxième secteur qui se termine au (240, 100).

Les deux yeux sont également restituées avec deux `A` commandes et à l’instar de tête de la cat, la seconde `A` commande se termine au point même comme le début de la première `A` commande. Toutefois, ces paires de `A` commandes ne définissent pas une ellipse. Le de chaque arc est 40 unités et le rayon est également 40 unités, ce qui signifie que ces courbes ne sont pas semicircles complètes.

Le `PaintSurface` gestionnaire effectue les transformations similaire que l’exemple précédent, mais définit un seul `Scale` facteur à conserver les proportions et fournir une petite marge pour les valeurs du Chat ne pas toucher les côtés de l’écran :

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

[![](path-data-images/pathdatacat-small.png "Capture d’écran triple de la page de chemin d’accès données Cat")](path-data-images/pathdatacat-large.png#lightbox "Triple capture d’écran de la page de chemin d’accès données Cat")

Normalement, lorsque un `SKPath` objet est défini comme un champ, les contours du chemin d’accès doivent être définis dans le constructeur ou une autre méthode. Toutefois, lorsque vous utilisez des données de chemin SVG, vous avez vu que le chemin d’accès peut être spécifiée entièrement dans la définition de champ.

L’ancien **horrible horloge analogique** exemple dans le [ **le Rotate Transform** ](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) article affiché entre les mains de l’horloge sous forme de lignes simples. Le **assez analogique horloge** programme ci-dessous remplace ces lignes avec `SKPath` objets définis en tant que champs dans le [ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) classe avec `SKPaint` objets :

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

Entre les mains heure et minute ont désormais placés entre domaines. Pour rendre ces mains distinctes entre eux, ils sont dessinés avec un contour noir et remplissage gris à l’aide de la `handStrokePaint` et `handFillPaint` objets.

Dans l’ancien **horrible horloge analogique** exemple, les petits cercles qui marqué les heures et minutes ont été dessinés dans une boucle. Dans ce **assez analogique horloge** exemple, une approche totalement différente est utilisée : les marques d’heure et minute sont les lignes en pointillés dessinées avec la `minuteMarkPaint` et `hourMarkPaint` objets :

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

Le [ **points et tirets** ](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) article décrit comment vous pouvez utiliser la [ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash*) méthode pour créer une ligne en pointillés. Le premier argument est un `float` tableau qui a généralement deux éléments: Le premier élément est la longueur des tirets, et le deuxième élément est l’intervalle entre les tirets. Lorsque le `StrokeCap` propriété est définie sur `SKStrokeCap.Round`, puis les terminaisons arrondis du tiret allonger efficacement la longueur du tiret par la largeur de trait des deux côtés du tiret. Par conséquent, définissant le premier élément du tableau sur 0 crée une ligne en pointillés.

La distance entre ces points est régie par le deuxième élément du tableau. Comme vous le verrez bientôt, ces deux `SKPaint` objets sont utilisés pour dessiner les cercles avec un rayon de 90 unités. Par conséquent, la circonférence de ce cercle est 180π, ce qui signifie que les marques de 60 minutes doivent apparaître toutes les unités 3pi, qui est la deuxième valeur de la `float` de tableau dans `minuteMarkPaint`. Les marques de 12 heures doivent apparaître toutes les unités 15π, qui est la valeur de la seconde `float` tableau.

Le `PrettyAnalogClockPage` classe définit un minuteur pour invalider la surface de toutes les 16 millisecondes et le `PaintSurface` gestionnaire est appelé à ce rythme. Les définitions antérieures de la `SKPath` et `SKPaint` objets permettant la très propre code de dessin :

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

Quelque chose de spécial est effectuée avec la seconde aiguille, toutefois. Étant donné que l’horloge est mise à jour toutes les 16 millisecondes, le `Millisecond` propriété de la `DateTime` valeur peut potentiellement être utilisée pour animer un balayage ensuite manuellement plutôt qu’un seul qui déplace les sauts discrets à partir de la seconde à la seconde. Mais ce code n’autorise pas le déplacement à être lisse. Au lieu de cela, il utilise le Xamarin.Forms [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) et [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) animation fonctions pour un autre type de déplacement d’accélération. Ces fonctions d’accélération provoquent la seconde aiguille déplacer de manière plus rapide &mdash; extraire un peu avant son déplacement, puis légèrement investissements sa destination, un effet qui malheureusement ne peut pas être reproduit dans ces captures d’écran statiques :

[![](path-data-images/prettyanalogclock-small.png "Capture d’écran triple de la page de l’horloge analogique assez")](path-data-images/prettyanalogclock-large.png#lightbox "Triple capture d’écran de la page de l’horloge analogique assez")


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
