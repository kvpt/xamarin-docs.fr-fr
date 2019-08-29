---
title: Résumé du chapitre 26. Dispositions personnalisées
description: 'Création d’Mobile Apps avec Xamarin. Forms: Résumé du chapitre 26. Dispositions personnalisées'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1f96a69cbb12748a77aa6ebeb92c4ac191ce4bad
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120542"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Résumé du chapitre 26. Dispositions personnalisées

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms inclut plusieurs classes dérivées de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1):

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` et
- `RelativeLayout`.

Ce chapitre explique comment créer vos propres classes qui dérivent de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Une vue d’ensemble de mise en page

Il n’existe aucun système centralisé qui gère la disposition de Xamarin.Forms. Chaque élément est chargé de déterminer ce que sa taille doit être et comment effectuer le rendu de lui-même au sein d’une zone particulière.

### <a name="parents-and-children"></a>Parents et enfants

Chaque élément qui a des enfants est chargé pour le positionnement de ces enfants à l’intérieur de lui-même. Il est le parent qui détermine la taille adaptée à ses enfants doit être basé sur la taille, il propose disponible et la taille de l’enfant souhaite être.

### <a name="sizing-and-positioning"></a>Dimensionnement et de positionnement

Disposition commence en haut de l’arborescence d’éléments visuels avec la page et se poursuit dans toutes les branches. La méthode la plus importante publique dans la disposition est [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) défini par `VisualElement`. Chaque élément est parent d’autres appels éléments `Layout` pour chacun de ses enfants à donner à l’enfant, une taille et la position par rapport à lui-même sous la forme d’un [ `Rectangle` ](xref:Xamarin.Forms.Rectangle) valeur. Ces `Layout` propagent des appels dans l’arborescence visuelle.

Un appel à `Layout` est requis pour un élément à l’écran et les propriétés en lecture seule suivantes à définir. Elles sont cohérentes avec le `Rectangle` transmis à la méthode :

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) de type `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) de type `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) de type `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) de type `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) de type `double`

Avant la `Layout` appeler, `Height` et `Width` comportent des valeurs fictifs &ndash;1.

Un appel à `Layout` déclenche également des appels aux méthodes protégées suivantes :

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), qui appelle
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), qui peut être substitué.

Enfin, l’événement suivant sont déclenché :

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

Le `OnSizeAllocated` méthode est substituée par `Page` et `Layout`, qui sont les seules deux classes dans Xamarin.Forms qui peut avoir des enfants. Les appels de méthode substituée

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) pour `Page` dérivés et [ `UpdateChildrenLayout` ](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) pour `Layout` dérivés, qui appelle
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) pour `Page` dérivés et [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) pour `Layout` dérivés.

`LayoutChildren` appelle ensuite `Layout` pour chacun des enfants de l’élément. Si au moins un enfant dispose d’un nouveau `Bounds` définition, puis l’événement suivant sont déclenché :

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) pour `Page` dérivés et [ `LayoutChanged` ](xref:Xamarin.Forms.Layout.LayoutChanged) pour `Layout` dérivés

### <a name="constraints-and-size-requests"></a>Contraintes et des demandes de taille

Pour `LayoutChildren` intelligemment appeler `Layout` sur tous ses enfants, il doit connaître un *préféré* ou *souhaitée* taille pour les enfants. Par conséquent, les appels à `Layout` pour chacun des enfants sont généralement précédé par des appels à

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Une fois que le livre a été publié, le `GetSizeRequest` méthode a été déconseillée et remplacée par

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

Le `Measure` méthode prend en compte la [ `Margin` ](xref:Xamarin.Forms.View.Margin) propriété et comprend un argument de type [ `MeasureFlag` ](xref:Xamarin.Forms.MeasureFlags), qui a deux membres :

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) Pour ne pas inclure les marges

Pour de nombreux éléments, `GetSizeRequest` ou `Measure` Obtient la taille native de l’élément à partir de son convertisseur. Les deux méthodes ont des paramètres pour la largeur et la hauteur *contraintes*. Par exemple, un `Label` utilisera la contrainte de largeur pour déterminer comment encapsuler plusieurs lignes de texte.

Les deux `GetSizeRequest`et `Measure` retournent une valeur de type [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest), qui a deux propriétés :

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) de type `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) de type `Size`

Très souvent ces deux valeurs sont identiques et le `Minimum` valeur peut généralement être ignorée.

`VisualElement` définit également une méthode protégée similaire à `GetSizeRequest` qui est appelée à partir de `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) Retourne un `SizeRequest` valeur

Cette méthode est maintenant déconseillée et remplacée par :

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Chaque classe qui dérive de `Layout` ou `Layout<T>` doit substituer `OnSizeRequest` ou `OnMeasure`. Il s’agit d’où une classe de disposition détermine sa propre taille, qui est généralement basé sur la taille de ses enfants, il obtient en appelant `GetSizeRequest` ou `Measure` sur les enfants. Avant et après l’appel `OnSizeRequest` ou `OnMeasure`, `GetSizeRequest` ou `Measure` apporte des ajustements basés sur les propriétés suivantes :

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)de type `double`, affecte le `Request` propriété de `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de type `double`, affecte le `Request` propriété de `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) de type `double`, affecte le `Minimum` propriété de `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) de type `double`, affecte le `Minimum` propriété de `SizeRequest`

### <a name="infinite-constraints"></a>Contraintes infinies

Les arguments de contrainte passés à `GetSizeRequest` (ou `Measure`) et `OnSizeRequest` (ou `OnMeasure`) peut être infinie (par exemple, les valeurs de `Double.PositiveInfinity`). Toutefois, le `SizeRequest` retourné à partir de ces méthodes ne peuvent pas contenir des dimensions infinies.

Contraintes infinies indiquent que la taille demandée doit refléter la taille naturelle de l’élément. Un vertical `StackLayout` appels `GetSizeRequest` (ou `Measure`) sur ses enfants, avec une contrainte de hauteur infinie. Appelle une disposition de pile horizontale `GetSizeRequest` (ou `Measure`) sur ses enfants, avec une contrainte de largeur infinie. Un `AbsoluteLayout` appels `GetSizeRequest` (ou `Measure`) sur ses enfants, avec des contraintes de la largeur et hauteur infinies.

### <a name="peeking-inside-the-process"></a>Lecture à l’intérieur du processus

Le [ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) affiche contrainte et la taille de demander des informations pour une disposition simple.

## <a name="deriving-from-layoutview"></a>Dérivation à partir du\<mode page >

Une classe de disposition personnalisée dérive `Layout<View>`. Il possède deux responsabilités :

- Substituer `OnMeasure` pour appeler `Measure` sur les enfants de tous les la disposition. Retourner une taille demandée pour la disposition lui-même
- Substituer `LayoutChildren` pour appeler `Layout` sur les enfants de tous les la disposition

Le `for` ou `foreach` boucle dans ces remplacements doit ignorer tout enfant dont `IsVisible` propriété est définie sur `false`.

Un appel à `OnMeasure` n’est pas garanti. `OnMeasure` ne sera pas appelé si le parent de la disposition est régissant la taille de la mise en page (par exemple, une disposition qui remplit une page). Pour cette raison, `LayoutChildren` ne peuvent pas reposer sur les tailles enfant obtenus au cours de la `OnMeasure` appeler. Très souvent, `LayoutChildren` doit lui-même appeler `Measure` sur les enfants de la mise en page, ou vous pouvez implémenter une sorte de taille de la mise en cache logique (décrite ultérieurement).

### <a name="an-easy-example"></a>Un exemple simple

Le [ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) exemple contient un simplifiée [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) classe et une démonstration de son utilisation.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Simplifié de positionnement vertical et horizontal

Un des travaux qui `VerticalStack` doit effectuer se produit pendant la `LayoutChildren` remplacer. La méthode utilise l’enfant `HorizontalOptions` propriété afin de déterminer comment positionner l’enfant dans son emplacement dans le `VerticalStack`. Vous pouvez appeler la méthode statique [ `Layout.LayoutChildIntoBoundingRect` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Cette méthode appelle `Measure` sur l’enfant et utilise son `HorizontalOptions` et `VerticalOptions` propriétés pour positionner l’enfant dans le rectangle spécifié.

### <a name="invalidation"></a>Invalidation

Souvent une modification de propriété d’un élément sur comment cet élément apparaît dans la disposition. La disposition doit être rendus non valide pour déclencher une nouvelle disposition.

`VisualElement` définit une méthode protégée [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), qui est généralement appelée par le Gestionnaire de modification de propriété de n’importe quelle propriété pouvant être liée dont la modification affecte la taille de l’élément. Le `InvalidateMeasure` méthode déclenche un [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) événement.

Le `Layout` classe définit une méthode protégée similaire nommée [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout), qui une `Layout` dérivé doit appeler pour toute modification qui affecte la façon dont il positionne et redimensionne ses enfants.

### <a name="some-rules-for-coding-layouts"></a>Certaines règles de codage des dispositions

1. Propriétés définies par `Layout<T>` dérivés doivent être assorties de propriétés pouvant être liées et les gestionnaires de modification de propriété doivent appeler `InvalidateLayout`.

2. Un `Layout<T>` dérivé qui définit les propriétés pouvant être liées attachées doit substituer [ `OnAdded` ](xref:Xamarin.Forms.Layout`1.OnAdded*) pour ajouter un gestionnaire de modification de propriété à ses enfants et [ `OnRemoved` ](xref:Xamarin.Forms.Layout`1.OnRemoved*) pour supprimer les Gestionnaire. Le gestionnaire doit vérifier pour les modifications dans ces attachés propriétés pouvant être liées et répondre en appelant `InvalidateLayout`.

3. Un `Layout<T>` dérivée qui implémente un cache de tailles d’enfants doit substituer `InvalidateLayout` et [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) et effacer le cache lorsque ces méthodes sont appelées.

### <a name="a-layout-with-properties"></a>Une disposition avec des propriétés

Le [ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) suppose que tous ses enfants sont la même taille et les enfants est renvoyé à partir d’une ligne (ou colonne) à l’autre. Il définit un `Orientation` propriété comme `StackLayout`, et `ColumnSpacing` et `RowSpacing` propriétés telles que `Grid`, et il met en cache les tailles d’enfant.

Le [ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) exemple met un `WrapLayout` dans un `ScrollView` pour l’affichage des photos en stock.

### <a name="no-unconstrained-dimensions-allowed"></a>Aucune dimension sans contrainte autorisée !

Le [ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque est destinée à afficher tous ses enfants en lui-même. Par conséquent, il ne peut pas traiter avec des dimensions sans contrainte et lève une exception si un est rencontré.

Le [ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) illustre `UniformGridLayout`:

[![Capture d’écran triple de grille de Photo](images/ch26fg08-small.png "mise en page de grille uniforme")](images/ch26fg08-large.png#lightbox "mise en page de grille uniforme")

### <a name="overlapping-children"></a>Enfants qui se chevauchent

Un `Layout<T>` dérivé peut se chevaucher ses enfants. Toutefois, les enfants sont rendus dans leur ordre dans le `Children` collection et pas l’ordre dans lequel leurs `Layout` méthodes sont appelées.

Le `Layout` classe définit deux méthodes qui vous permettent de déplacer un enfant au sein de la collection :

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) Pour déplacer un enfant au début de la collection
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) Pour déplacer un enfant à la fin de la collection

Pour les enfants qui se chevauchent, enfants à la fin de la collection s’affichent visuellement au-dessus enfants au début de la collection.

Le [ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque définit une propriété jointe pour indiquer l’ordre de rendu et permettra ainsi à un de ses enfants à afficher sur les autres. Le [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) illustre cela :

[![Triple capture d’écran de grille de fichier de carte étudiant](images/ch26fg10-small.png "enfants de la disposition qui se chevauchent")](images/ch26fg10-large.png#lightbox "enfants de la disposition qui se chevauchent")

### <a name="more-attached-bindable-properties"></a>Plus attaché propriétés pouvant être liées

Le [ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque définit des propriétés pouvant être liées attachées pour spécifier deux `Point` valeurs et un valeur d’épaisseur et manipule `BoxView` éléments qui ressemble à celle des lignes.

Le [ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) exemple qui utilise pour dessiner un cube 3D.

### <a name="layout-and-layoutto"></a>Mise en page et LayoutTo

Un `Layout<T>` dérivé peut appeler `LayoutTo` plutôt que `Layout` pour animer la disposition. Le [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe pour cela et le [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) illustre cela.



## <a name="related-links"></a>Liens connexes

- [Chapitre 26 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Exemples de chapitre 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Création de dispositions personnalisées](~/xamarin-forms/user-interface/layouts/custom.md)
