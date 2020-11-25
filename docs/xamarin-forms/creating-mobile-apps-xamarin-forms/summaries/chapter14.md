---
title: Résumé du chapitre 14. Disposition absolue
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 14. Disposition absolue'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09011647428e2af1bdfcdb2f9def8da64ef84144
ms.sourcegitcommit: bda07768b51e6d24f897ccae16e152dc7a83effa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96039111"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Résumé du chapitre 14. Disposition absolue

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Comme `StackLayout` , [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) dérive de `Layout<View>` et hérite d’une `Children` propriété. `AbsoluteLayout` implémente un système de disposition qui requiert que le programmeur spécifie les positions de ses enfants et, éventuellement, leur taille. La position est spécifiée par l’angle supérieur gauche de l’enfant par rapport au coin supérieur gauche du `AbsoluteLayout` dans les unités indépendantes du périphérique. `AbsoluteLayout` implémente également une fonctionnalité de positionnement et de dimensionnement proportionnel.

`AbsoluteLayout` doit être considéré comme un système de disposition spécial à utiliser uniquement lorsque le programmeur peut imposer une taille aux enfants (par exemple, des `BoxView` éléments) ou lorsque la taille de l’élément n’affecte pas la position des autres enfants. Les `HorizontalOptions` Propriétés et n' `VerticalOptions` ont aucun effet sur les enfants d’un `AbsoluteLayout` .

Ce chapitre présente également la fonctionnalité importante des *propriétés pouvant être liées attachées* qui permettent aux propriétés définies dans une classe (dans ce cas `AbsoluteLayout` ) d’être attachées à une autre classe (un enfant de l’élément `AbsoluteLayout` ).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout dans le code

Vous pouvez ajouter un enfant à la `Children` collection d’un `AbsoluteLayout` à l’aide de la [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) méthode standard, mais `AbsoluteLayout` fournit également une [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) méthode étendue qui vous permet de spécifier un [`Rectangle`](xref:Xamarin.Forms.Rectangle) . Une autre [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) méthode requiert uniquement un [`Point`](xref:Xamarin.Forms.Point) , auquel cas l’enfant est sans contrainte et se redimensionne lui-même.

Vous pouvez créer une `Rectangle` valeur avec un [constructeur](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) qui requiert quatre valeurs &mdash; , les deux premières indiquant la position de l’angle supérieur gauche de l’enfant par rapport à son parent, et les deux secondes indiquant la taille de l’enfant. Ou vous pouvez utiliser un [constructeur] (XREF : Xamarin.Forms . Rectangle .% 23ctor ( Xamarin.Forms . Point, Xamarin.Forms . Size)) qui requiert un `Point` et une [`Size`](xref:Xamarin.Forms.Size) valeur.

Ces `Add` méthodes sont illustrées dans [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), qui positionne les `BoxView` éléments à l’aide de `Rectangle` valeurs et un `Label` élément à l’aide d’une seule `Point` valeur.

L’exemple [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) utilise `BoxView` les éléments 32 pour créer le modèle échiquier. Le programme donne aux `BoxView` éléments une taille codée en dur de 35 unités carrées. `AbsoluteLayout`A son et a la `HorizontalOptions` `VerticalOptions` valeur `LayoutOptions.Center` , ce qui a `AbsoluteLayout` pour effet d’avoir une taille totale de 280 unités carrées.

## <a name="attached-bindable-properties"></a>Propriétés pouvant être liées attachées

Il est également possible de définir la position et, éventuellement, la taille d’un enfant d’un `AbsoluteLayout` après qu’il a été ajouté à la `Children` collection à l’aide de la méthode statique [ `AbsoluteLayout.SetLayoutBounds` ] (XREF : Xamarin.Forms . AbsoluteLayout. SetLayoutBounds ( Xamarin.Forms . BindableObject, Xamarin.Forms . Rectangle)). Le premier argument est l’enfant ; le deuxième est un `Rectangle` objet. Vous pouvez spécifier que l’enfant se redimensionne horizontalement et/ou verticalement en définissant les valeurs de largeur et de hauteur sur la  [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) constante.

L’exemple [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) place le `AbsoluteLayout` dans un `ContentView` avec un `SizeChanged` Gestionnaire à appeler `AbsoluteLayout.SetLayoutBounds` sur tous les enfants pour les rendre le plus grand possible.  

La propriété pouvant être liée qui `AbsoluteLayout` définit est le champ statique en lecture seule de type `BindableProperty` nommé [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) . La `AbsoluteLayout.SetLayoutBounds` méthode statique est implémentée en appelant `SetValue` sur l’enfant avec `AbsoluteLayout.LayoutBoundsProperty` . L’enfant contient un dictionnaire dans lequel la propriété liée pouvant être liée et sa valeur sont stockées. Pendant la disposition, le `AbsoluteLayout` peut obtenir cette valeur en appelant [ `AbsoluteLayout.GetLayoutBounds` ] (XREF : Xamarin.Forms . AbsoluteLayout. GetLayoutBounds ( Xamarin.Forms . BindableObject)), qui est implémenté avec un `GetValue` appel.

## <a name="proportional-sizing-and-positioning"></a>Dimensionnement et positionnement proportionnels

`AbsoluteLayout` implémente une fonctionnalité de dimensionnement et de positionnement proportionnels. La classe définit une deuxième propriété attachée liée, [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) , avec les méthodes statiques associées [ `AbsoluteLayout.SetLayoutFlags` ] (XREF : Xamarin.Forms . AbsoluteLayout. SetLayoutFlags ( Xamarin.Forms . BindableObject, Xamarin.Forms . AbsoluteLayoutFlags)) et [ `AbsoluteLayout.GetLayoutFlags` ] (XREF : Xamarin.Forms . AbsoluteLayout. GetLayoutFlags ( Xamarin.Forms . BindableObject)).

L’argument de `AbsoluteLayout.SetLayoutFlags` et la valeur de retour de `AbsoluteLayout.GetLayoutFlags` est une valeur de type [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , une énumération avec les membres suivants :

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (égal à 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) 1,0
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) 2
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) 1,3
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) 4
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) version8
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) douze
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Vous pouvez les combiner à l’aide de l’opérateur or au niveau du bit C#.

Lorsque ces indicateurs sont définis, certaines propriétés de la `Rectangle` structure de limites de disposition utilisée pour positionner et dimensionner l’enfant sont interprétées proportionnellement.

Lorsque l' `WidthProportional` indicateur est défini, la `Width` valeur 1 signifie que l’enfant a la même largeur que le `AbsoluteLayout` . Une approche similaire est utilisée pour la hauteur.

Le positionnement proportionnel prend en compte la taille. Lorsque l' `XProportional` indicateur est défini, la `X` propriété des `Rectangle` limites de disposition est proportionnelle. La valeur 0 signifie que le bord gauche de l’enfant est positionné sur le bord gauche du `AbsoluteLayout` , mais une position de 1 signifie que le bord droit de l’enfant est positionné au bord droit du `AbsoluteLayout` , et non au-delà du bord droit du `AbsoluteLayout` comme vous pouvez vous attendre. Une `X` propriété de 0,5 Centre l’enfant horizontalement dans le `AbsoluteLayout` .

L’exemple [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) illustre l’utilisation de la taille et du positionnement proportionnels.

## <a name="working-with-proportional-coordinates"></a>Utilisation de coordonnées proportionnelles

Parfois, il est plus facile de considérer que le positionnement proportionnel diffère de la façon dont il est implémenté dans le `AbsoluteLayout` . Vous préférerez peut-être utiliser des coordonnées proportionnelles lorsqu’une `X` propriété de 1 positionne le bord gauche de l’enfant (plutôt que le bord droit) sur le bord droit du `AbsoluteLayout` .

Ce schéma de positionnement alternatif peut être appelé « coordonnées enfants fractionnaires ». Vous pouvez convertir des coordonnées enfants fractionnaires en limites de disposition requises pour `AbsoluteLayout` utiliser les formules suivantes :

layoutBounds. X = (fractionalChildCoordinate. X/(1-layoutBounds. Width))

layoutBounds. Y = (fractionalChildCoordinate. Y/(1-layoutBounds. Height))

L’exemple [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) illustre cela.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout et XAML

Vous pouvez utiliser un `AbsoluteLayout` en XAML et définir les propriétés pouvant être liées attachées sur les enfants d’un `AbsoluteLayout` à l’aide des valeurs d’attribut de `AbsoluteLayout.LayoutBounds` et de `AbsoluteLayout.LayoutFlags` . Cela est illustré dans les exemples [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) et [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) . Ce dernier programme contient 32 `BoxView` éléments, mais utilise un implicite `Style` qui inclut la `AbsoluteLayout.LayoutFlags` propriété pour réduire au minimum la balise.

Un attribut en XAML qui se compose d’un nom de classe, d’un point et d’un nom de propriété est *toujours* une propriété pouvant être liée.

## <a name="overlays"></a>Superpositions

Vous pouvez utiliser `AbsoluteLayout` pour construire une *superposition* qui couvre la page avec d’autres contrôles, peut-être pour empêcher l’utilisateur d’interagir avec les contrôles normaux sur la page.

L’exemple [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) illustre cette technique et montre également le [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , qui affiche l’étendue de la réalisation d’une tâche par un programme.

## <a name="some-fun"></a>Amusez-vous

L’exemple [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) affiche l’heure actuelle avec un affichage de matrice du point de 5x7 simulé. Chaque point est un `BoxView` (il y a 228) dimensionnés et positionnés sur le `AbsoluteLayout` .

[![Capture d’écran triple de l’horloge du point matriciel](images/ch14fg08-small.png "Horloge matricielle")](images/ch14fg08-large.png#lightbox "Horloge matricielle")

Le programme [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anime deux `Label` objets pour rebondir horizontalement et verticalement sur l’écran.

## <a name="related-links"></a>Liens connexes

- [Chapitre 14 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Exemples du chapitre 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolutelayout.md)
- [Propriétés attachées](~/xamarin-forms/xaml/attached-properties.md)
