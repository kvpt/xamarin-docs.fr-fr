---
title: Résumé du chapitre 14. Disposition absolue
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 14. Disposition absolue'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771129"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Résumé du chapitre 14. Disposition absolue

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Comme `StackLayout`, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) dérive de `Layout<View>` et hérite d’une propriété `Children`. `AbsoluteLayout` implémente un système de disposition qui oblige le programmeur à spécifier les positions de ses enfants et, éventuellement, leur taille. La position est spécifiée par l’angle supérieur gauche de l’enfant par rapport au coin supérieur gauche de la `AbsoluteLayout` dans les unités indépendantes du périphérique. `AbsoluteLayout` implémente également une fonctionnalité de positionnement et de dimensionnement proportionnel.

`AbsoluteLayout` doit être considéré comme un système de disposition spécial à utiliser uniquement lorsque le programmeur peut imposer une taille sur les enfants (par exemple, `BoxView` éléments) ou lorsque la taille de l’élément n’affecte pas la position des autres enfants. Les propriétés `HorizontalOptions` et `VerticalOptions` n’ont aucun effet sur les enfants d’un `AbsoluteLayout`.

Ce chapitre présente également la fonctionnalité importante des *propriétés pouvant être liées attachées* qui autorisent l’attachement de propriétés définies dans une classe (dans ce cas `AbsoluteLayout`) à une autre classe (un enfant du `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>DispositionAbsolue dans le code

Vous pouvez ajouter un enfant à la collection `Children` d’un `AbsoluteLayout` à l’aide de la méthode [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) standard, mais `AbsoluteLayout` fournit également une méthode [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) étendue qui vous permet de spécifier un [`Rectangle`](xref:Xamarin.Forms.Rectangle). Une autre méthode [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) nécessite uniquement une [`Point`](xref:Xamarin.Forms.Point), auquel cas l’enfant est sans contrainte et se redimensionne lui-même.

Vous pouvez créer une valeur de `Rectangle` avec un [constructeur](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) qui requiert quatre valeurs &mdash; les deux premières indiquant la position de l’angle supérieur gauche de l’enfant par rapport à son parent, et les deux secondes indiquant la taille de l’enfant. Ou vous pouvez utiliser un [constructeur](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) qui requiert une `Point` et une valeur de [`Size`](xref:Xamarin.Forms.Size) .

Ces méthodes `Add` sont illustrées dans [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), qui positionne `BoxView` éléments à l’aide de valeurs `Rectangle` et un élément `Label` à l’aide d’une seule valeur `Point`.

L’exemple [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) utilise des éléments de `BoxView` 32 pour créer le modèle échiquier. Le programme donne aux éléments `BoxView` une taille codée en dur de 35 unités carrées. La `AbsoluteLayout` a sa `HorizontalOptions` et `VerticalOptions` définie sur `LayoutOptions.Center`, ce qui a pour effet que la `AbsoluteLayout` a une taille totale de 280 unités carrées.

## <a name="attached-bindable-properties"></a>Joint des propriétés pouvant être liées

Il est également possible de définir la position et, éventuellement, la taille d’un enfant d’un `AbsoluteLayout` après qu’il a été ajouté à la collection de `Children` à l’aide de la méthode statique [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). Le premier argument est l’enfant ; le deuxième est un objet `Rectangle`. Vous pouvez spécifier que l’enfant se redimensionne horizontalement et/ou verticalement en définissant les valeurs de largeur et de hauteur sur la constante [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) .

L’exemple [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) place les `AbsoluteLayout` dans un `ContentView` avec un gestionnaire de `SizeChanged` pour appeler `AbsoluteLayout.SetLayoutBounds` sur tous les enfants afin de les rendre le plus grand possible.  

La propriété pouvant être liée qui `AbsoluteLayout` définit est le champ statique en lecture seule de type `BindableProperty` nommé [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty). La méthode `AbsoluteLayout.SetLayoutBounds` statique est implémentée en appelant `SetValue` sur l’enfant avec le `AbsoluteLayout.LayoutBoundsProperty`. L’enfant contienne un dictionnaire dans lequel la propriété jointe peut être liée et sa valeur sont stockées. Pendant la disposition, les `AbsoluteLayout` peuvent obtenir cette valeur en appelant [`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), qui est implémenté avec un appel de `GetValue`.

## <a name="proportional-sizing-and-positioning"></a>Redimensionnement proportionnel et positionnement

`AbsoluteLayout` implémente une fonctionnalité de dimensionnement et de positionnement proportionnel. La classe définit une deuxième propriété attachée liée, [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), avec les méthodes statiques associées [`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) et [`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

L’argument pour `AbsoluteLayout.SetLayoutFlags` et la valeur de retour de `AbsoluteLayout.GetLayoutFlags` est une valeur de type [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags), une énumération avec les membres suivants :

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (égal à 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Vous pouvez combiner ces éléments avec le langage c# opérateur de bits OR.

Lorsque ces indicateurs sont définis, certaines propriétés de la structure de limites de disposition `Rectangle` utilisées pour positionner et dimensionner l’enfant sont interprétées proportionnellement.

Lorsque l’indicateur `WidthProportional` est défini, la valeur de `Width` 1 signifie que l’enfant a la même largeur que le `AbsoluteLayout`. Une approche similaire est utilisée pour la hauteur.

Le positionnement proportionnelle prend la taille en compte. Lorsque l’indicateur `XProportional` est défini, la propriété `X` des limites de disposition `Rectangle` est proportionnelle. La valeur 0 signifie que le bord gauche de l’enfant est positionné sur le bord gauche du `AbsoluteLayout`, mais une position de 1 signifie que le bord droit de l’enfant est positionné sur le bord droit du `AbsoluteLayout`, et non au-delà du bord droit de la `AbsoluteLayout` comme vous pouvez l’attendre. Une `X` propriété de 0,5 Centre l’enfant horizontalement dans le `AbsoluteLayout`.

L’exemple [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) illustre l’utilisation de la taille et du positionnement proportionnels.

## <a name="working-with-proportional-coordinates"></a>Utilisation des coordonnées proportionnelles

Parfois, il est plus facile de considérer que le positionnement proportionnel diffère de la façon dont il est implémenté dans le `AbsoluteLayout`. Vous préférerez peut-être utiliser des coordonnées proportionnelles où une `X` propriété de 1 positionne le bord gauche de l’enfant (plutôt que le bord droit) sur le bord droit du `AbsoluteLayout`.

Ce schéma de positionnement alternatif peut être appelé « coordonnées fractionnaires enfants. » Vous pouvez convertir des coordonnées enfants fractionnaires en limites de disposition requises pour `AbsoluteLayout` à l’aide des formules suivantes :

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

L’exemple [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) illustre cela.

## <a name="absolutelayout-and-xaml"></a>DispositionAbsolue et XAML

Vous pouvez utiliser un `AbsoluteLayout` en XAML et définir les propriétés pouvant être liées attachées sur les enfants d’un `AbsoluteLayout` à l’aide des valeurs d’attribut de `AbsoluteLayout.LayoutBounds` et `AbsoluteLayout.LayoutFlags`. Cela est illustré dans les exemples [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) et [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) . Ce dernier programme contient 32 `BoxView` éléments, mais utilise un `Style` implicite qui inclut la propriété `AbsoluteLayout.LayoutFlags` pour réduire au minimum la balise.

Un attribut en XAML qui se compose d’un nom de classe, d’un point et d’un nom de propriété est *toujours* une propriété pouvant être liée.

## <a name="overlays"></a>Superpositions

Vous pouvez utiliser `AbsoluteLayout` pour créer une *superposition*qui couvre la page avec d’autres contrôles, peut-être pour empêcher l’utilisateur d’interagir avec les contrôles normaux sur la page.

L’exemple [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) illustre cette technique et montre également l' [`ProgressBar`](xref:Xamarin.Forms.ProgressBar), qui affiche l’étendue de la réalisation d’une tâche par un programme.

## <a name="some-fun"></a>Un peu de fantaisie

L’exemple [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) affiche l’heure actuelle avec un affichage de matrice du point de 5x7 simulé. Chaque point est un `BoxView` (il y a 228) dimensionnés et positionnés sur le `AbsoluteLayout`.

[![Capture d’écran triple de l’horloge du point matriciel](images/ch14fg08-small.png "Horloge matricielle")](images/ch14fg08-large.png#lightbox "Horloge matricielle")

Le programme [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anime deux objets `Label` pour rebondir horizontalement et verticalement sur l’écran.

## <a name="related-links"></a>Liens connexes

- [Chapitre 14 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Exemples du chapitre 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
