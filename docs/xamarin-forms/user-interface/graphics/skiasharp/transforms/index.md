---
title: Transformations SkiaSharp
description: Cet article explore les transformations pour afficher les graphiques de SkiaSharp dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d9c153f8da44c525b8851afb48682bd7a14a8c47
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770504"
---
# <a name="skiasharp-transforms"></a>Transformations SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_En savoir plus sur les transformations pour afficher les graphiques de SkiaSharp_

SkiaSharp prend en charge les transformations graphiques traditionnels qui sont implémentées en tant que méthodes de la [ `SKCanvas` ](xref:SkiaSharp.SKCanvas) objet. Point de vue mathématique, les transformations modifier les coordonnées et les tailles que vous spécifiez dans `SKCanvas` fonctions de dessin, comme les objets graphiques sont rendus. Les transformations sont souvent pratiques pour dessiner des graphiques répétitives ou pour l’animation. Certaines techniques &mdash; telles que la rotation des fichiers bitmap ou texte &mdash; ne sont pas possibles sans recourir à des transformations.

Transformations SkiaSharp prennent en charge les opérations suivantes :

- *Traduire* MAJ les coordonnées d’un emplacement vers un autre
- *Mise à l’échelle* pour augmenter ou diminuer les coordonnées et tailles
- *Faire pivoter* pour faire pivoter des coordonnées autour d’un point
- *Incliner* de décalage coordonne horizontalement ou verticalement afin qu’un rectangle devienne un parallélogramme

Ils sont appelés *affine* transforme. Transformations affines conservent toujours des lignes parallèles et jamais provoquent une coordonnée ou une taille de devenir infinie. Un carré n’est jamais transformé en autre chose qu’un parallélogramme, et un cercle est jamais transformé en autre chose qu’une ellipse.

SkiaSharp prend également en charge les transformations non affines (également appelé *projectives* ou *perspective* transforme) selon une matrice 3 x 3 transformation standard. Une transformation non affine permet un carré être transformées en un quadrilatère convexe, qui est une figure quatre côtés avec tous les angles intérieurs inférieur à 180 degrés. Transformations non affines peuvent provoquer des coordonnées ou les tailles de devenir infinie, mais elles sont essentielles pour les effets 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Différences entre SkiaSharp et transformations de Xamarin.Forms

Xamarin.Forms prend également en charge les transformations qui sont similaires à ceux de SkiaSharp. Xamarin.Forms [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe définit les propriétés de transformation suivantes :

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation), [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX), et [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

Le `RotationX` et `RotationY` propriétés sont des transformations de point de vue qui créent des effets de la quasi-3D.

Il existe plusieurs différences essentielles entre transformations SkiaSharp et les transformations de Xamarin.Forms :

La première différence est que SkiaSharp sont appliquées à l’intégralité de `SKCanvas` objet alors que les transformations de Xamarin.Forms sont appliquées à personne `VisualElement` dérivés. (Vous pouvez appliquer des transformations Xamarin.Forms à le `SKCanvasView` de l’objet lui-même, étant donné que `SKCanvasView` dérive `VisualElement`, mais dans ce `SKCanvasView`, appliquent les transformations SkiaSkarp.)

Les transformations SkiaSharp sont par rapport à l’angle supérieur gauche de la `SKCanvas` tandis que les transformations de Xamarin.Forms sont par rapport à l’angle supérieur gauche de la `VisualElement` auquel ils sont appliqués. Cette différence est importante lors de l’application mise à l’échelle et rotation transforme, car ces transformations sont toujours relatives à un moment donné.

La très grande différence est que les transformations SKiaSharp sont *méthodes* tandis que les transformations de Xamarin.Forms sont *propriétés*. Il s’agit d’une différence sémantique au-delà de la différence syntaxique : Les transformations SkiaSharp effectuent une opération alors que les transformations de Xamarin. Forms définissent un État. Transformations SkiaSharp s’appliquent aux objets de graphiques dessinés par la suite, mais pas à des objets graphiques sont dessinés avant que la transformation est appliquée. En revanche, une transformation Xamarin.Forms s’applique à un élément précédemment rendu dès que la propriété est définie. Transformations SkiaSharp sont cumulatifs, tandis que les méthodes sont appelées ; Transformations de Xamarin.Forms sont remplacées quand la propriété est définie avec une autre valeur.

Tous les exemples de programmes dans cette section s’affichent dans le **SkiaSharp transforme** section de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programme. Code source peut être trouvé dans le [ **transforme** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) dossier de la solution.

## <a name="the-translate-transformtranslatemd"></a>[La transformation de traduction](translate.md)

Découvrez comment utiliser la transformation de traduction de décalage SkiaSharp graphics.

## <a name="the-scale-transformscalemd"></a>[La transformation d’échelle](scale.md)

Découvrez la transformation d’échelle SkiaSharp mise à l’échelle des objets à différentes tailles.

## <a name="the-rotate-transformrotatemd"></a>[La transformation de rotation](rotate.md)

Explorez les effets et les animations possibles avec la transformation de rotation SkiaSharp.

## <a name="the-skew-transformskewmd"></a>[La transformation d’inclinaison](skew.md)

Découvrez comment la transformation d’inclinaison peut créer inclinée objet graphique.

## <a name="matrix-transformsmatrixmd"></a>[Transformations de matrice](matrix.md)

Approfondissez vos connaissances concernant les transformations SkiaSharp avec la matrice de transformation polyvalent.

## <a name="touch-manipulationstouchmd"></a>[Manipulations tactiles](touch.md)

Matrice d’utilisation se transforme pour implémenter les manipulations tactiles pour faire glisser, mise à l’échelle et la rotation.

## <a name="non-affine-transformsnon-affinemd"></a>[Transformations non affines](non-affine.md)

Aller au-delà du oridinary avec des effets de transformation non affine.

## <a name="3d-rotation3d-rotationmd"></a>[Rotation 3D](3d-rotation.md)

Utiliser des transformations non affines pour faire pivoter des objets 2D dans l’espace 3D.

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
