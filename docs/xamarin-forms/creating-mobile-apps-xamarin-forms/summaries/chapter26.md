---
title: Résumé du chapitre 26. Dispositions personnalisées
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 26. Dispositions personnalisées'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70770939"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Résumé du chapitre 26. Dispositions personnalisées

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin. Forms comprend plusieurs classes dérivées de [`Layout<View>`](xref:Xamarin.Forms.Layout`1):

- `StackLayout`
- `Grid`
- `AbsoluteLayout` et
- `RelativeLayout`.

Ce chapitre décrit comment créer vos propres classes qui dérivent de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Une vue d’ensemble de mise en page

Il n’existe aucun système centralisé qui gère la disposition de Xamarin.Forms. Chaque élément est chargé de déterminer ce que sa taille doit être et comment effectuer le rendu de lui-même au sein d’une zone particulière.

### <a name="parents-and-children"></a>Parents et enfants

Chaque élément qui a des enfants est chargé pour le positionnement de ces enfants à l’intérieur de lui-même. Il est le parent qui détermine la taille adaptée à ses enfants doit être basé sur la taille, il propose disponible et la taille de l’enfant souhaite être.

### <a name="sizing-and-positioning"></a>Dimensionnement et de positionnement

Disposition commence en haut de l’arborescence d’éléments visuels avec la page et se poursuit dans toutes les branches. La méthode publique la plus importante dans la disposition est [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) définie par `VisualElement`. Chaque élément qui est un parent d’autres éléments appelle `Layout` pour chacun de ses enfants afin de lui attribuer une taille et une position par rapport à lui-même sous la forme d’une valeur de [`Rectangle`](xref:Xamarin.Forms.Rectangle) . Ces appels de `Layout` se propagent via l’arborescence d’éléments visuels.

Un appel à `Layout` est requis pour qu’un élément apparaisse sur l’écran et entraîne la définition des propriétés en lecture seule suivantes. Elles sont cohérentes avec les `Rectangle` transmises à la méthode :

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) de type `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) de type `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) de type `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) de type `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) de type `double`

Avant l’appel de `Layout`, `Height` et `Width` ont des valeurs factices de &ndash;1.

Un appel à `Layout` déclenche également des appels aux méthodes protégées suivantes :

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), qui appelle
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), qui peut être substitué.

Enfin, l’événement suivant sont déclenché :

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

La méthode `OnSizeAllocated` est remplacée par `Page` et `Layout`, qui sont les deux seules classes dans Xamarin. Forms qui peuvent avoir des enfants. Les appels de méthode substituée

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) pour les dérivés de `Page` et les [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) pour les dérivés `Layout`, qui appelle
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) pour les dérivés de `Page` et les [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) pour les dérivés `Layout`.

`LayoutChildren` appelle ensuite `Layout` pour chacun des enfants de l’élément. Si au moins un enfant a un nouveau paramètre `Bounds`, l’événement suivant est déclenché :

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) pour les dérivés de `Page` et les [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) pour les dérivés `Layout`

### <a name="constraints-and-size-requests"></a>Contraintes et des demandes de taille

Pour `LayoutChildren` d’appeler intelligemment `Layout` sur tous ses enfants, il doit connaître la taille *préférée* ou *souhaitée* pour les enfants. Par conséquent, les appels à `Layout` pour chacun des enfants sont généralement précédés d’appels à

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Une fois le livre publié, la méthode `GetSizeRequest` a été dépréciée et remplacée par

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

La méthode `Measure` prend en charge la propriété [`Margin`](xref:Xamarin.Forms.View.Margin) et inclut un argument de type [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags), qui a deux membres :

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) pour ne pas inclure les marges

Pour de nombreux éléments, `GetSizeRequest` ou `Measure` obtient la taille native de l’élément à partir de son convertisseur. Les deux méthodes ont des paramètres pour les *contraintes*de largeur et de hauteur. Par exemple, un `Label` utilisera la contrainte de largeur pour déterminer comment encapsuler plusieurs lignes de texte.

`GetSizeRequest`et `Measure` retournent une valeur de type [`SizeRequest`](xref:Xamarin.Forms.SizeRequest), qui a deux propriétés :

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) de type `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) de type `Size`

Très souvent, ces deux valeurs sont les mêmes, et la valeur `Minimum` peut généralement être ignorée.

`VisualElement` définit également une méthode protégée semblable à `GetSizeRequest` qui est appelée à partir de `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) retourne une valeur de `SizeRequest`

Cette méthode est maintenant déconseillée et remplacée par :

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Chaque classe qui dérive de `Layout` ou `Layout<T>` doit substituer `OnSizeRequest` ou `OnMeasure`. C’est là qu’une classe Layout détermine sa propre taille, qui est généralement basée sur la taille de ses enfants, qu’elle obtient en appelant `GetSizeRequest` ou `Measure` sur les enfants. Avant et après l’appel de `OnSizeRequest` ou `OnMeasure`, `GetSizeRequest` ou `Measure` apporte des ajustements basés sur les propriétés suivantes :

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)de type `double`, affecte la propriété `Request` de `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de type `double`, affecte la propriété `Request` de `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) de type `double`, affecte la propriété `Minimum` de `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) de type `double`, affecte la propriété `Minimum` de `SizeRequest`

### <a name="infinite-constraints"></a>Contraintes infinies

Les arguments de contrainte passés à `GetSizeRequest` (ou `Measure`) et `OnSizeRequest` (ou `OnMeasure`) peuvent être infinis (c’est-à-dire des valeurs de `Double.PositiveInfinity`). Toutefois, les `SizeRequest` retournées à partir de ces méthodes ne peuvent pas contenir de dimensions infinies.

Contraintes infinies indiquent que la taille demandée doit refléter la taille naturelle de l’élément. Une `StackLayout` verticale appelle `GetSizeRequest` (ou `Measure`) sur ses enfants avec une contrainte de hauteur infinie. Une disposition de pile horizontale appelle `GetSizeRequest` (ou `Measure`) sur ses enfants avec une contrainte de largeur infinie. Une `AbsoluteLayout` appelle `GetSizeRequest` (ou `Measure`) sur ses enfants avec des contraintes de largeur et de hauteur infinies.

### <a name="peeking-inside-the-process"></a>Lecture à l’intérieur du processus

[**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) affiche les informations de demande de contrainte et de taille pour une mise en page simple.

## <a name="deriving-from-layoutview"></a>Dérivation à partir de la disposition\<vue >

Une classe de disposition personnalisée dérive de `Layout<View>`. Il possède deux responsabilités :

- Substituez `OnMeasure` pour appeler `Measure` sur tous les enfants de la disposition. Retourner une taille demandée pour la disposition lui-même
- Substituez `LayoutChildren` pour appeler `Layout` sur tous les enfants de la disposition

La boucle `for` ou `foreach` dans ces substitutions doit ignorer tout enfant dont la propriété `IsVisible` a la valeur `false`.

Un appel à `OnMeasure` n’est pas garanti. `OnMeasure` ne sera pas appelée si le parent de la disposition régit la taille de la disposition (par exemple, une disposition qui remplit une page). Pour cette raison, les `LayoutChildren` ne peuvent pas reposer sur les tailles enfants obtenues pendant l’appel de `OnMeasure`. Très souvent, `LayoutChildren` doit lui-même appeler `Measure` sur les enfants de la disposition, ou vous pouvez implémenter un type de logique de mise en cache de taille (à discuter plus tard).

### <a name="an-easy-example"></a>Un exemple simple

L’exemple [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contient une classe de [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) simplifiée et une démonstration de son utilisation.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Simplifié de positionnement vertical et horizontal

L’une des tâches que `VerticalStack` doit effectuer se produit pendant la substitution de `LayoutChildren`. La méthode utilise la propriété `HorizontalOptions` de l’enfant pour déterminer comment positionner l’enfant dans son emplacement dans le `VerticalStack`. Vous pouvez appeler à la place la méthode statique [`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Cette méthode appelle `Measure` sur l’enfant et utilise ses propriétés `HorizontalOptions` et `VerticalOptions` pour positionner l’enfant dans le rectangle spécifié.

### <a name="invalidation"></a>Invalidation

Souvent une modification de propriété d’un élément sur comment cet élément apparaît dans la disposition. La disposition doit être rendus non valide pour déclencher une nouvelle disposition.

`VisualElement` définit une méthode protégée [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), qui est généralement appelée par le gestionnaire de modification de propriété d’une propriété pouvant être liée dont la modification affecte la taille de l’élément. La méthode `InvalidateMeasure` déclenche un événement [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) .

La classe `Layout` définit une méthode protégée similaire nommée [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout), qu’un `Layout` dérivé doit appeler pour toute modification qui affecte la façon dont il positionne et dimensionne ses enfants.

### <a name="some-rules-for-coding-layouts"></a>Certaines règles de codage des dispositions

1. Les propriétés définies par `Layout<T>` dérivés doivent être sauvegardées par des propriétés pouvant être liées, et les gestionnaires de propriétés modifiées doivent appeler `InvalidateLayout`.

2. Une `Layout<T>` dérivée qui définit des propriétés pouvant être liées peuvent remplacer [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) pour ajouter un gestionnaire de modification de propriété à ses enfants et [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) pour supprimer ce gestionnaire. Le gestionnaire doit vérifier les modifications apportées à ces propriétés pouvant être liées et répondre en appelant `InvalidateLayout`.

3. Une `Layout<T>` dérivée qui implémente un cache de tailles enfants doit remplacer `InvalidateLayout` et [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) et effacer le cache lorsque ces méthodes sont appelées.

### <a name="a-layout-with-properties"></a>Une disposition avec des propriétés

La classe [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) dans [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) suppose que tous ses enfants ont la même taille et encapsule les enfants d’une ligne (ou colonne) à la suivante. Il définit une propriété `Orientation` comme `StackLayout`, ainsi que des propriétés `ColumnSpacing` et `RowSpacing` comme `Grid`, et il met en cache des tailles enfants.

L’exemple [**photowrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) met un `WrapLayout` dans un `ScrollView` pour afficher des photos boursières.

### <a name="no-unconstrained-dimensions-allowed"></a>Aucune dimension sans contrainte autorisée !

Le [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) est destiné à afficher tous ses enfants dans lui-même. Par conséquent, il ne peut pas traiter avec des dimensions sans contrainte et lève une exception si un est rencontré.

L’exemple [**photogrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) montre `UniformGridLayout`:

[![Capture d’écran triple de la grille de photos](images/ch26fg08-small.png "Disposition de grille uniforme")](images/ch26fg08-large.png#lightbox "Disposition de grille uniforme")

### <a name="overlapping-children"></a>Enfants qui se chevauchent

Un `Layout<T>` dérivé peut chevaucher ses enfants. Toutefois, les enfants sont rendus dans leur ordre dans la collection `Children`, et non dans l’ordre dans lequel leurs méthodes `Layout` sont appelées.

La classe `Layout` définit deux méthodes qui vous permettent de déplacer un enfant dans la collection :

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) de déplacer un enfant au début de la collection
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) de déplacer un enfant à la fin de la collection

Pour les enfants qui se chevauchent, enfants à la fin de la collection s’affichent visuellement au-dessus enfants au début de la collection.

La classe [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit une propriété jointe pour indiquer l’ordre de rendu et permettre ainsi l’affichage de l’un de ses enfants en plus des autres. L’exemple [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) illustre ce qui suit :

[![Capture triple de la grille du fichier de carte étudiant](images/ch26fg10-small.png "Superposition des enfants de disposition")](images/ch26fg10-large.png#lightbox "Superposition des enfants de disposition")

### <a name="more-attached-bindable-properties"></a>Plus attaché propriétés pouvant être liées

La classe [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit des propriétés pouvant être liées et jointes pour spécifier deux valeurs `Point` et une valeur d’épaisseur et manipule les éléments `BoxView` pour ressembler aux lignes.

L’exemple [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) utilise cette valeur pour dessiner un cube 3D.

### <a name="layout-and-layoutto"></a>Mise en page et LayoutTo

Une `Layout<T>` dérivée peut appeler `LayoutTo` plutôt que `Layout` pour animer la disposition. La classe [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) effectue cette opération, et l’exemple [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) l’illustre.

## <a name="related-links"></a>Liens connexes

- [Chapitre 26 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Exemples du chapitre 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Créer des dispositions personnalisées](~/xamarin-forms/user-interface/layouts/custom.md)
