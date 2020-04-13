---
title: Résumé du chapitre 14. Disposition absolue
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 14. Disposition absolue'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771129"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Résumé du chapitre 14. Disposition absolue

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Comme `StackLayout` [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , dérive `Layout<View>` et hérite d’une `Children` propriété. `AbsoluteLayout`met en œuvre un système de mise en page qui exige que le programmeur spécifie les positions de ses enfants et, en option, leur taille. La position est spécifiée par le coin supérieur gauche de l’enfant par rapport au coin supérieur gauche des unités indépendantes de l’appareil. `AbsoluteLayout` `AbsoluteLayout`implémente également une fonction de positionnement et de dimensionnement proportionnelle.

`AbsoluteLayout`devrait être considéré comme un système de mise en page à usage spécial à utiliser `BoxView` uniquement lorsque le programmeur peut imposer une taille aux enfants (par exemple, des éléments) ou lorsque la taille de l’élément n’affecte pas le positionnement d’autres enfants. Les `HorizontalOptions` `VerticalOptions` propriétés et n’ont `AbsoluteLayout`aucun effet sur les enfants d’un .

Ce chapitre introduit également la caractéristique importante des *propriétés liantes attachées* qui permettent aux propriétés définies dans une classe (dans ce cas) `AbsoluteLayout`d’être attachées à une autre classe (un enfant de la `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout en code

Vous pouvez ajouter un `Children` enfant `AbsoluteLayout` à la [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) collection `AbsoluteLayout` d’une [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) méthode en utilisant la [`Rectangle`](xref:Xamarin.Forms.Rectangle)méthode standard, mais fournit également une méthode étendue qui vous permet de spécifier un . Une [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) autre méthode [`Point`](xref:Xamarin.Forms.Point)ne nécessite qu’un , auquel cas l’enfant est sans contrainte et se taille.

Vous pouvez `Rectangle` créer une valeur avec un &mdash; [constructeur](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) qui nécessite quatre valeurs les deux premiers indiquant la position du coin supérieur gauche de l’enfant par rapport à son parent, et les deux autres indiquant la taille de l’enfant. Ou vous pouvez [constructor](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) utiliser un `Point` constructeur [`Size`](xref:Xamarin.Forms.Size) qui nécessite une valeur et une valeur.

Ces `Add` méthodes sont démontrées dans [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), qui positionne `BoxView` les éléments à l’aide de `Rectangle` valeurs, et un `Label` élément utilisant juste une `Point` valeur.

[**L’échantillon ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) utilise `BoxView` 32 éléments pour créer le motif d’échiquier. Le programme `BoxView` donne aux éléments une taille codée dur de 35 unités carrées. Le `AbsoluteLayout` a `HorizontalOptions` `VerticalOptions` son `LayoutOptions.Center`et mis `AbsoluteLayout` à , ce qui provoque la taille totale de 280 unités carrées.

## <a name="attached-bindable-properties"></a>Propriétés liantes attachées

Il est également possible de définir la position et, en `AbsoluteLayout` option, la taille `Children` d’un [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle))enfant d’un après qu’il a été ajouté à la collection en utilisant la méthode statique . Le premier argument est l’enfant; le second `Rectangle` est un objet. Vous pouvez spécifier que l’enfant se taille horizontalement et/ou verticalement en fixant la largeur et les valeurs de hauteur à la [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) constante.

L’échantillon [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) `AbsoluteLayout` met `ContentView` le `SizeChanged` dans `AbsoluteLayout.SetLayoutBounds` un avec un gestionnaire de faire appel à tous les enfants pour les rendre aussi grands que possible.  

La propriété liante `AbsoluteLayout` ci-jointe qui définit est `BindableProperty` le [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)champ statique de type de type nommé . La `AbsoluteLayout.SetLayoutBounds` méthode statique est `SetValue` mise en `AbsoluteLayout.LayoutBoundsProperty`œuvre en faisant appel à l’enfant avec le . L’enfant contient un dictionnaire dans lequel la propriété liante attachée et sa valeur sont stockées. Pendant la `AbsoluteLayout` mise en page, [`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject))le peut obtenir `GetValue` cette valeur en appelant , qui est implémenté avec un appel.

## <a name="proportional-sizing-and-positioning"></a>Dimensionnement et positionnement proportionnels

`AbsoluteLayout`met en œuvre une fonction de dimensionnement et de positionnement proportionnel. La classe définit une deuxième propriété [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)liante attachée, [`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) [`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)), avec les méthodes statiques connexes et .

L’argument `AbsoluteLayout.SetLayoutFlags` et la `AbsoluteLayout.GetLayoutFlags` valeur de rendement [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)est une valeur de type, un recensement avec les membres suivants:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None)(égal à 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional)(1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional)(2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional)(3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional)(4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional)(8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional)(12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All)(XFFFFFFFFFF)

Vous pouvez les combiner avec l’opérateur OU bitwise C.

Avec ces drapeaux fixés, `Rectangle` certaines propriétés de la structure de limites de mise en page utilisées pour positionner et tailler l’enfant sont interprétées proportionnellement.

Lorsque `WidthProportional` le drapeau est `Width` fixé, une valeur de 1 signifie `AbsoluteLayout`que l’enfant est de la même largeur que le . Une approche similaire est utilisée pour la hauteur.

Le positionnement proportionnel tient compte de la taille. Lorsque `XProportional` le drapeau est `X` placé, `Rectangle` la propriété des limites de mise en page est proportionnelle. Une valeur de 0 signifie que le bord gauche de l’enfant est positionné au bord gauche du `AbsoluteLayout`, mais une position de `AbsoluteLayout`1 signifie que `AbsoluteLayout` le bord droit de l’enfant est positionné au bord droit de la , pas au-delà du bord droit de la comme vous pouvez vous y attendre. Une `X` propriété de 0,5 centre l’enfant horizontalement dans le `AbsoluteLayout`.

[**L’échantillon ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) démontre l’utilisation du dimensionnement et du positionnement proportionnels.

## <a name="working-with-proportional-coordinates"></a>Travailler avec des coordonnées proportionnelles

Parfois, il est plus facile de penser au positionnement proportionnel `AbsoluteLayout`différemment de la façon dont il est mis en œuvre dans le . Vous préférerez peut-être travailler `X` avec des coordonnées proportionnelles où une propriété de 1 positionne `AbsoluteLayout`le bord gauche de l’enfant (plutôt que le bord droit) contre le bord droit de la .

Ce système de positionnement alternatif peut être appelé « coordonnées fractionnaires d’enfants ». Vous pouvez convertir des coordonnées fractionnaires d’enfant aux limites de disposition requises pour `AbsoluteLayout` l’utilisation des formules suivantes :

layoutBounds.X (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y - (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

C’est ce que démontre l’échantillon [**ProportionnelleCoordinateCalc.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout et XAML

Vous pouvez `AbsoluteLayout` utiliser un dans XAML et définir les `AbsoluteLayout` propriétés liantes attachées sur les enfants d’une valeur d’attribut `AbsoluteLayout.LayoutBounds` en utilisant et `AbsoluteLayout.LayoutFlags`. Ceci est démontré dans les échantillons [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) et [**ChessboardXaml.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) Ce dernier programme contient `BoxView` 32 `Style` éléments, `AbsoluteLayout.LayoutFlags` mais utilise un implicite qui comprend la propriété pour maintenir la majoration jusqu’au minimum.

Un attribut dans XAML qui se compose d’un nom de classe, d’un point et d’un nom de propriété est *toujours* une propriété liante attachée.

## <a name="overlays"></a>Superpositions

Vous pouvez `AbsoluteLayout` utiliser pour construire une *superposition*, qui couvre la page avec d’autres contrôles, peut-être pour protéger l’utilisateur contre interagir avec les contrôles normaux sur la page.

[**L’échantillon SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) démontre cette technique, [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)et démontre également le , qui montre la mesure dans laquelle un programme a accompli une tâche.

## <a name="some-fun"></a>Un peu de plaisir

[**L’échantillon DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) affiche l’heure actuelle avec un affichage simulé de matrice à points 5x7. Chaque point `BoxView` est un (il ya 228 d’entre eux) de taille et positionné sur le `AbsoluteLayout`.

[![Triple capture d’écran de l’horloge de matrice de point](images/ch14fg08-small.png "Horloge de matrice de point")](images/ch14fg08-large.png#lightbox "Horloge de matrice de point")

Le programme [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anime `Label` deux objets pour rebondir horizontalement et verticalement sur l’écran.

## <a name="related-links"></a>Liens connexes

- [Chapitre 14 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Échantillons du chapitre 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
