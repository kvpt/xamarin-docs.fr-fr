---
title: Transformations SkiaSharp
description: Cet article explore les transformations pour l’affichage des graphiques SkiaSharp dans les Xamarin.Forms applications et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 68cff80395f4bccf6acd718e044f708108ce4d18
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563859"
---
# <a name="skiasharp-transforms"></a>Transformations SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_En savoir plus sur les transformations pour l’affichage des graphiques SkiaSharp_

SkiaSharp prend en charge les transformations graphiques traditionnelles qui sont implémentées en tant que méthodes de l' [`SKCanvas`](xref:SkiaSharp.SKCanvas) objet. Mathématiquement, les transformations modifient les coordonnées et les tailles que vous spécifiez dans `SKCanvas` les fonctions de dessin au fur et à mesure que les objets graphiques sont rendus. Les transformations sont souvent pratiques pour dessiner des graphiques répétitifs ou pour l’animation. Certaines techniques &mdash; , telles que la rotation des bitmaps ou du texte, ne &mdash; sont pas possibles sans l’utilisation de transformations.

Les transformations SkiaSharp prennent en charge les opérations suivantes :

- *Traduire* en coordonnées de décalage d’un emplacement à un autre
- Mettre à l' *échelle* pour augmenter ou diminuer les coordonnées et les tailles
- *Pivoter* pour faire pivoter les coordonnées autour d’un point
- *Skew* pour déplacer des coordonnées horizontalement ou verticalement pour qu’un rectangle devienne un parallélogramme

Il s’agit des transformations *affines* . Les transformations affines préservent toujours les lignes parallèles et n’entraînent jamais l’indisponibilité d’une coordonnée ou d’une taille. Un carré n’est jamais transformé en autre chose qu’un parallélogramme et un cercle n’est jamais transformé en autre chose qu’une ellipse.

SkiaSharp prend également en charge les transformations non affines (également appelées transformations de *perspective* ou *projective* ) basées sur une matrice de transformation 3 par 3 standard. Une transformation non affine permet de transformer un carré en un quadrilatère convexe, qui est un nombre à quatre côtés avec tous les angles intérieurs inférieurs à 180 degrés. Les transformations non affines peuvent entraîner une indisponibilité des coordonnées ou des tailles, mais elles sont vitales pour les effets 3D.

## <a name="differences-between-skiasharp-and-no-locxamarinforms-transforms"></a>Différences entre les SkiaSharp et les Xamarin.Forms transformations

Xamarin.Forms prend également en charge les transformations qui sont similaires à celles de SkiaSharp. La Xamarin.Forms [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe définit les propriétés de transformation suivantes :

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) les [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) , [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) et [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

Les `RotationX` `RotationY` Propriétés et sont des transformations de perspective qui créent des effets quasi 3D.

Il existe plusieurs différences essentielles entre les transformations et les transformations SkiaSharp Xamarin.Forms :

La première différence est que les transformations SkiaSharp sont appliquées à l’ensemble de l' `SKCanvas` objet alors que les Xamarin.Forms transformations sont appliquées à des `VisualElement` dérivés individuels. (Vous pouvez appliquer les Xamarin.Forms transformations à l' `SKCanvasView` objet lui-même, car `SKCanvasView` dérive de `VisualElement` , mais dans cela `SKCanvasView` , les transformations SkiaSkarp s’appliquent.)

Les transformations SkiaSharp sont relatives au coin supérieur gauche de et les `SKCanvas` Xamarin.Forms transformations sont relatives au coin supérieur gauche du auquel `VisualElement` ils sont appliqués. Cette différence est importante lors de l’application de transformations de mise à l’échelle et de rotation, car ces transformations sont toujours relatives à un point particulier.

La différence très importante est que les transformations SKiaSharp sont des *méthodes* alors que les Xamarin.Forms transformations sont des *Propriétés*. Il s’agit d’une différence sémantique au-delà de la différence syntaxique : les transformations SkiaSharp effectuent une opération alors que les Xamarin.Forms transformations définissent un État. Les transformations SkiaSharp s’appliquent aux objets graphiques dessinés par la suite, mais pas aux objets graphiques dessinés avant l’application de la transformation. En revanche, une Xamarin.Forms transformation s’applique à un élément rendu précédemment, dès que la propriété est définie. Les transformations SkiaSharp sont cumulatives à mesure que les méthodes sont appelées ; Xamarin.Forms les transformations sont remplacées lorsque la propriété est définie avec une autre valeur.

Tous les exemples de programmes de cette section s’affichent dans la section **transformations SkiaSharp** du programme [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Le code source se trouve dans le dossier [**transformations**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) de la solution.

## <a name="the-translate-transform"></a>[La transformation de traduction](translate.md)

Découvrez comment utiliser la transformation Translate pour déplacer des graphiques SkiaSharp.

## <a name="the-scale-transform"></a>[La transformation d’échelle](scale.md)

Découvrez la transformation d’échelle SkiaSharp pour la mise à l’échelle d’objets à différentes tailles.

## <a name="the-rotate-transform"></a>[La transformation de rotation](rotate.md)

Explorez les effets et les animations possibles avec la transformation de rotation SkiaSharp.

## <a name="the-skew-transform"></a>[La transformation d’inclinaison](skew.md)

Découvrez comment la transformation d’inclinaison peut créer un objet graphique incliné.

## <a name="matrix-transforms"></a>[Transformations de matrice](matrix.md)

Explorez plus en détail les transformations SkiaSharp avec la matrice de transformation polyvalente.

## <a name="touch-manipulations"></a>[Manipulations tactiles](touch.md)

Utilisez des transformations de matrices pour implémenter des manipulations tactiles pour le glissement, la mise à l’échelle et la rotation.

## <a name="non-affine-transforms"></a>[Transformations non affines](non-affine.md)

Allez au-delà de la oridinary avec des effets de transformation non affins.

## <a name="3d-rotation"></a>[Rotation 3D](3d-rotation.md)

Utilisez des transformations non affines pour faire pivoter des objets 2D dans l’espace 3D.

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)