---
title : « SkiaSharp Lines and paths » Description :» cet article explique comment utiliser SkiaSharp pour dessiner des lignes et des chemins d’accès graphiques dans des Xamarin.Forms applications, et illustre cela avec un exemple de code.
ms. Prod : xamarin ms. AssetID : 316A15FE-383D-4D06-8641-BAC7EE7474CA ms. Technology : xamarin-skiasharp auteur : davidbritch ms. Author : dabritch ms. Date : 03/10/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-lines-and-paths"></a>Lignes et chemins d’accès SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utiliser SkiaSharp pour dessiner des lignes et des tracés graphiques_

La [section précédente](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) a démontré que la `SKCanvas` classe SkiaSharp comprend plusieurs méthodes pour dessiner des cercles, des ovales, des rectangles, des rectangles arrondis, du texte et des bitmaps. Cette section et les sections suivantes couvrent les différentes classes connectées à la création et au rendu des *chemins graphiques*.

Le chemin d’accès aux graphiques est l’approche la plus généralisée pour dessiner des lignes et des courbes dans SkiaSharp. Cette section traite de l’utilisation d’un [`SKPath`](xref:SkiaSharp.SKPath) objet pour dessiner des lignes droites et de l’utilisation d’une collection de petites lignes droites (appelée *polyligne*) pour dessiner des courbes que vous pouvez définir de façon algorithmique. Une section ultérieure sur les [**courbes et chemins d’accès SkiaSharp**](../curves/index.md) aborde les différents types de courbes pris en charge par `SKPath` .

Tous les exemples de programmes de cette section s’affichent sous les lignes d’en-tête **et les chemins d’accès** dans la page d’hébergement du programme [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) , et dans le dossier [**paths**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) de cette solution.

## <a name="lines-and-stroke-caps"></a>[Lignes et embouts de trait](lines.md)

Découvrez comment utiliser SkiaSharp pour dessiner des lignes avec des épaisseurs de trait différentes.

## <a name="path-basics"></a>[Principes de base du chemin d’accès](paths.md)

Explorez l' `SKPath` objet SkiaSharp pour la combinaison de lignes et de courbes.

## <a name="the-path-fill-types"></a>[Les types de remplissage de chemin d’accès](fill-types.md)

Découvrez les différents effets possibles avec les types de remplissage de chemin SkiaSharp.

## <a name="polylines-and-parametric-equations"></a>[Polylignes et équations paramétriques](polylines.md)

Utilisez SkiaSharp pour afficher une ligne que vous pouvez définir avec des équations paramétriques.

## <a name="dots-and-dashes"></a>[Points et tirets](dots.md)

Maîtrisez les subtilités de dessin de lignes en pointillés et en pointillés dans SkiaSharp.

## <a name="finger-painting"></a>[Peinture au doigt](finger-paint.md)

Utilisez vos doigts pour peindre sur le canevas.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
