---
title: Résumé du chapitre 14. Disposition absolue
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 14. Disposition absolue'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771129"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Résumé du chapitre 14. Disposition absolue

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Comme `StackLayout`, [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout) dérive `Layout<View>` et hérite d’un `Children` propriété. `AbsoluteLayout` implémente un système de disposition qui exige que le programmeur spécifier les positions de ses enfants et, éventuellement, de leur taille. La position est spécifiée par le coin supérieur gauche de l’enfant par rapport à l’angle supérieur gauche de la `AbsoluteLayout` en unités indépendantes du périphérique. `AbsoluteLayout` implémente également une fonctionnalité de dimensionnement et de positionnement proportionnel.

`AbsoluteLayout` doit être considérée comme un système de disposition de spécial à être utilisé uniquement quand le programmeur peut imposer une taille sur les enfants (par exemple, `BoxView` éléments) ou lorsque la taille de l’élément n’affecte pas le positionnement d’autres enfants. Le `HorizontalOptions` et `VerticalOptions` propriétés n’ont aucun effet sur les enfants d’un `AbsoluteLayout`.

Ce chapitre présente également la fonctionnalité importante de *attaché propriétés pouvant être liées* qui autorisent les propriétés définies dans une classe (dans ce cas `AbsoluteLayout`) à joindre à une autre classe (un enfant de le `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>DispositionAbsolue dans le code

Vous pouvez ajouter un enfant à la `Children` collection d’un `AbsoluteLayout` à l’aide de la norme [ `Add` ](xref:System.Collections.Generic.ICollection`1.Add*) (méthode), mais `AbsoluteLayout` fournit également une étendue [ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) méthode qui vous permet de spécifier un [ `Rectangle` ](xref:Xamarin.Forms.Rectangle). Un autre [ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) méthode nécessite uniquement un [ `Point` ](xref:Xamarin.Forms.Point), auquel cas l’enfant n’est pas limitée et se redimensionne.

Vous pouvez créer un `Rectangle` valeur avec un [constructeur](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) qui requiert quatre valeurs &mdash; les deux premiers indiquant la position du coin supérieur gauche de l’enfant par rapport à son parent et les deux indiquant le taille de l’enfant. Vous pouvez également utiliser un [constructeur](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) qui nécessite un `Point` et un [ `Size` ](xref:Xamarin.Forms.Size) valeur.

Ces `Add` méthodes sont illustrées dans [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), les positions `BoxView` à l’aide des éléments `Rectangle` valeurs et un `Label` élément à l’aide de simplement un `Point` valeur.

Le [ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) exemple utilise 32 `BoxView` éléments pour créer le modèle échiquier. Le programme donne le `BoxView` taille éléments codés en dur du carré de 35 unités. Le `AbsoluteLayout` a son `HorizontalOptions` et `VerticalOptions` définie sur `LayoutOptions.Center`, ce qui conduit le `AbsoluteLayout` pour avoir une taille totale du carré de 280 unités.

## <a name="attached-bindable-properties"></a>Joint des propriétés pouvant être liées

Il est également possible de définir la position et, éventuellement, la taille d’un enfant d’un `AbsoluteLayout` après qu’il a été ajouté à la `Children` collection à l’aide de la méthode statique [ `AbsoluteLayout.SetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). Le premier argument est l’enfant ; le second est un `Rectangle` objet. Vous pouvez spécifier que l’enfant se redimensionne horizontalement et verticalement en définissant les valeurs de largeur et hauteur le [ `AbsoluteLayout.AutoSize` ](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) constante.

Le [ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) exemple met le `AbsoluteLayout` dans un `ContentView` avec un `SizeChanged` gestionnaire à appeler `AbsoluteLayout.SetLayoutBounds` sur tous les enfants pour les rendre aussi grand que possible.  

La propriété jointe peut être liée qui `AbsoluteLayout` définit le champ statique en lecture seule de type n’est `BindableProperty` nommé [ `AbsoluteLayout.LayoutBoundsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty). La méthode statique `AbsoluteLayout.SetLayoutBounds` méthode est implémentée en appelant `SetValue` sur l’enfant ayant le `AbsoluteLayout.LayoutBoundsProperty`. L’enfant contienne un dictionnaire dans lequel la propriété jointe peut être liée et sa valeur sont stockées. Pendant la disposition, le `AbsoluteLayout` pouvez obtenir cette valeur en appelant [ `AbsoluteLayout.GetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), qui est implémentée avec un `GetValue` appeler.

## <a name="proportional-sizing-and-positioning"></a>Redimensionnement proportionnel et positionnement

`AbsoluteLayout` implémente un redimensionnement proportionnel et la fonctionnalité de positionnement. La classe définit une deuxième propriété jointe peut être liée, [ `LayoutFlagsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), avec les méthodes statiques associées [ `AbsoluteLayout.SetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) et [ `AbsoluteLayout.GetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

L’argument `AbsoluteLayout.SetLayoutFlags` et la valeur de retour de `AbsoluteLayout.GetLayoutFlags` est une valeur de type [ `AbsoluteLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayoutFlags), une énumération avec les membres suivants :

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (égal à 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Vous pouvez combiner ces éléments avec le langage C# opérateur de bits OR.

Avec ces indicateurs définis, certaines propriétés de la `Rectangle` structure de limites de mise en page utilisée pour positionner et dimensionner l’enfant sont interprétés proportionnellement.

Lorsque le `WidthProportional` indicateur est défini, un `Width` valeur 1 indique que l’enfant est la même largeur que le `AbsoluteLayout`. Une approche similaire est utilisée pour la hauteur.

Le positionnement proportionnelle prend la taille en compte. Lorsque le `XProportional` indicateur est défini, le `X` propriété de la `Rectangle` limites de disposition est proportionnelle. Une valeur de 0 signifie que l’enfant du bord gauche est positionnée sur le bord gauche de la `AbsoluteLayout`, mais une position de 1 signifie que le bord droit de l’enfant est positionné sur le bord droit de la `AbsoluteLayout`, pas au-delà du bord droit de la `AbsoluteLayout` comme vous le feriez notre t. Un `X` l’enfant horizontalement dans les centres de propriété de 0,5 le `AbsoluteLayout`.

Le [ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) exemple illustre l’utilisation de redimensionnement proportionnel et de positionnement.

## <a name="working-with-proportional-coordinates"></a>Utilisation des coordonnées proportionnelles

Parfois, il est plus simple de considérer un positionnement proportionnelle différemment de la façon dont il est implémenté dans le `AbsoluteLayout`. Vous préférerez peut-être utiliser des coordonnées proportionnelles où un `X` propriété 1 positionne bord gauche de l’enfant (plutôt que le bord droit) sur le bord droit de la `AbsoluteLayout`.

Ce schéma de positionnement alternatif peut être appelé « coordonnées fractionnaires enfants. » Vous pouvez convertir à partir des coordonnées fractionnaires enfants pour les limites de disposition requis pour `AbsoluteLayout` à l’aide des formules suivantes :

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

Le [ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) illustre cela.

## <a name="absolutelayout-and-xaml"></a>DispositionAbsolue et XAML

Vous pouvez utiliser un `AbsoluteLayout` dans XAML et définissez les propriétés de peut être liées attachées sur les enfants d’un `AbsoluteLayout` à l’aide de valeurs d’attribut de `AbsoluteLayout.LayoutBounds` et `AbsoluteLayout.LayoutFlags`. Cela est illustré dans le [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) et [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) exemples. Le programme de ce dernier contient 32 `BoxView` éléments mais utilise implicite `Style` qui inclut le `AbsoluteLayout.LayoutFlags` propriété conserver le balisage jusqu’au minimum.

Est un attribut dans XAML se compose d’un nom de classe, un point et un nom de propriété *toujours* une propriété jointe peut être liée.

## <a name="overlays"></a>Superpositions

Vous pouvez utiliser `AbsoluteLayout` pour construire un *superposition*, qui couvre la page avec d’autres contrôles, peut-être pour protéger l’utilisateur d’interagir avec les contrôles normales sur la page.

Le [ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) exemple illustre cette technique et montre la [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), qui affiche l’étendue à laquelle un programme a terminé une tâche.

## <a name="some-fun"></a>Un peu de fantaisie

Le [ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) exemple affiche l’heure actuelle avec un affichage simulé matricielle 5 / 7. Chaque point est un `BoxView` (il y 228 d'entre eux) dimensionné et positionné sur le `AbsoluteLayout`.

[![Capture d’écran triple d’horloge de la matrice de points](images/ch14fg08-small.png "matricielle horloge")](images/ch14fg08-large.png#lightbox "matricielle horloge")

Le [ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) programme anime deux `Label` objets à rebondit horizontalement et verticalement sur l’écran.

## <a name="related-links"></a>Liens connexes

- [Chapitre 14 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Exemples de chapitre 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
