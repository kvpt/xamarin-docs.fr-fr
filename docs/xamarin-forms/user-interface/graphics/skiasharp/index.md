---
title: SkiaSharp Graphics in Xamarin.Forms
description: SkiaSharp est un système graphique 2D pour .NET et C#, alimenté par le moteur graphique skia Open source qui est largement utilisé dans Google Products. Ce guide explique comment utiliser SkiaSharp pour les graphiques 2D dans vos Xamarin.Forms applications.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 733f640a18196e6bf31eadfa1c632a7202fc1ccc
ms.sourcegitcommit: 836d54779190b1bef1b43bc0c2016c9b3034bfda
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93281272"
---
# <a name="skiasharp-graphics-in-no-locxamarinforms"></a>SkiaSharp Graphics in Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utiliser SkiaSharp pour les graphiques 2D dans vos Xamarin.Forms applications_

SkiaSharp est un système graphique 2D pour .NET et C#, alimenté par le moteur graphique skia Open source qui est largement utilisé dans Google Products. Vous pouvez utiliser SkiaSharp dans vos Xamarin.Forms applications pour dessiner des graphiques vectoriels 2D, des bitmaps et du texte.

Ce guide part du principe que vous êtes familiarisé avec la Xamarin.Forms programmation.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Webinaire : SkiaSharp pour Xamarin.Forms**

## <a name="skiasharp-preliminaries"></a>SkiaSharp préliminaires

SkiaSharp pour Xamarin.Forms est empaqueté en tant que package NuGet. Une fois que vous avez créé une Xamarin.Forms solution dans Visual Studio ou Visual Studio pour Mac, vous pouvez utiliser le gestionnaire de package NuGet pour rechercher le package **SkiaSharp. views. Forms** et l’ajouter à votre solution. Si vous vérifiez la section **références** de chaque projet après avoir ajouté SkiaSharp, vous pouvez voir que différentes bibliothèques **SkiaSharp** ont été ajoutées à chacun des projets de la solution.

Si votre Xamarin.Forms application cible iOS, modifiez son fichier **info. plist** pour changer la cible de déploiement minimale en iOS 8,0.

Dans une page C# qui utilise SkiaSharp, vous souhaiterez inclure une `using` directive pour l' [`SkiaSharp`](xref:SkiaSharp) espace de noms, qui englobe toutes les classes, structures et énumérations SkiaSharp que vous allez utiliser dans la programmation graphique. Vous devez également disposer d’une `using` directive pour l' [`SkiaSharp.Views.Forms`](xref:SkiaSharp.Views.Forms) espace de noms pour les classes spécifiques à Xamarin.Forms . Il s’agit d’un espace de noms bien plus petit, avec la classe la plus importante [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) . Cette classe dérive de la Xamarin.Forms `View` classe et héberge votre sortie de graphiques SkiaSharp.

> [!IMPORTANT]
> L' `SkiaSharp.Views.Forms` espace de noms contient également une `SKGLView` classe qui dérive de, `View` mais utilise OpenGL pour le rendu des graphiques. À des fins de simplicité, ce guide se limite à `SKCanvasView` , mais l’utilisation de à `SKGLView` la place est assez similaire.

## <a name="skiasharp-drawing-basics"></a>[Principes de base de dessin SkiaSharp](basics/index.md)

Voici quelques-uns des figures graphiques les plus simples que vous pouvez dessiner avec SkiaSharp : des cercles, des ovales et des rectangles. Pour afficher ces figures, vous allez découvrir les coordonnées, les tailles et les couleurs SkiaSharp. L’affichage du texte et des bitmaps est plus complexe, mais ces articles présentent également ces techniques.

## <a name="skiasharp-lines-and-paths"></a>[Lignes et chemins d’accès SkiaSharp](paths/index.md)

Un tracé graphique est une série de lignes droites et de courbes connectées. Les chemins d’accès peuvent être rayés, remplis ou les deux. Cet article englobe de nombreux aspects du dessin en courbes, y compris les extrémités de trait et les jointures, et les lignes en pointillés et en pointillés, mais il arrête les géométries de courbe.

## <a name="skiasharp-transforms"></a>[Transformations SkiaSharp](transforms/index.md)

Les transformations permettent aux objets graphiques d’être traduits uniformément, mis à l’échelle, pivotés ou inclinés. Cet article montre également comment vous pouvez utiliser une matrice de transformation 3 par 3 standard pour créer des transformations non affines et appliquer des transformations à des chemins d’accès.

## <a name="skiasharp-curves-and-paths"></a>[Courbes et chemins d’accès SkiaSharp](curves/index.md)

L’exploration des chemins d’accès se poursuit avec l’ajout de courbes à un objet Path et l’exploitation d’autres fonctionnalités puissantes de chemin d’accès. Vous verrez comment vous pouvez spécifier un chemin d’accès complet dans une chaîne de texte concise, comment utiliser les effets de tracé et comment explorer les éléments internes du chemin d’accès.

## <a name="skiasharp-bitmaps"></a>[Bitmaps SkiaSharp](bitmaps/index.md)

Les bitmaps sont des tableaux rectangulaires de bits correspondant aux pixels d’un périphérique d’affichage. Cette série d’articles explique comment charger, enregistrer, afficher, créer, dessiner, animer et accéder aux bits des bitmaps SkiaSharp.

## <a name="skiasharp-effects"></a>[Effets SkiaSharp](effects/index.md)

Les effets sont des propriétés qui modifient l’affichage normal des graphiques, y compris les dégradés linéaires et circulaires, la mosaïque d’images bitmap, les modes de fusion, le flou, etc.

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp avec Xamarin.Forms webinaire (vidéo)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
