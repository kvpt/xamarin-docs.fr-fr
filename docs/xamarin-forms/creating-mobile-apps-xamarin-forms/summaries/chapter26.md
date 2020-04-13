---
title: Résumé du chapitre 26. Dispositions personnalisées
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 26. Dispositions personnalisées'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70770939"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Résumé du chapitre 26. Dispositions personnalisées

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms comprend plusieurs [`Layout<View>`](xref:Xamarin.Forms.Layout`1)classes dérivées de :

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` et
- `RelativeLayout`.

Ce chapitre décrit comment créer vos propres `Layout<View>`classes qui dérivent de .

## <a name="an-overview-of-layout"></a>Un aperçu de la mise en page

Il n’y a pas de système centralisé qui gère la disposition Xamarin.Forms. Chaque élément est chargé de déterminer quelle devrait être sa propre taille et comment se rendre dans un domaine particulier.

### <a name="parents-and-children"></a>Parents et enfants

Chaque élément qui a des enfants est responsable de placer ces enfants en lui-même. C’est le parent qui détermine en fin de compte la taille de ses enfants en fonction de la taille qu’il a disponible et de la taille que l’enfant veut être.

### <a name="sizing-and-positioning"></a>Dimensionnement et positionnement

La disposition commence en haut de l’arbre visuel avec la page, puis procède à toutes les branches. La méthode publique la [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) plus `VisualElement`importante dans la mise en page est définie par . Chaque élément qui est un `Layout` parent à d’autres éléments exige que chacun de ses enfants [`Rectangle`](xref:Xamarin.Forms.Rectangle) donne à l’enfant une taille et une position par rapport à lui-même sous la forme d’une valeur. Ces `Layout` appels se propagent à travers l’arbre visuel.

Un appel `Layout` à est nécessaire pour qu’un élément apparaisse à l’écran, et provoque l’ensemble des propriétés suivantes. Ils sont compatibles avec le `Rectangle` passage à la méthode:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)de type`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)de type`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)de type`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)de type`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)de type`double`

Avant `Layout` l’appel, `Height` et `Width` ont &ndash;des valeurs simulées de 1.

Un appel `Layout` pour déclenche également des appels vers les méthodes protégées suivantes :

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), qui appelle
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), qui peut être remplacé.

Enfin, l’événement suivant est déclenché :

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

La `OnSizeAllocated` méthode est remplacée `Page` `Layout`par et , qui sont les deux seules classes dans Xamarin.Forms qui peuvent avoir des enfants. La méthode des appels

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)pour `Page` les [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) produits `Layout` dérivés et pour les dérivés, qui
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))pour `Page` les [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) dérivés et pour `Layout` les dérivés.

`LayoutChildren`appelle `Layout` alors chacun des enfants de l’élément. Si au moins un `Bounds` enfant a un nouveau paramètre, l’événement suivant est déclenché :

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)pour `Page` les [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) produits `Layout` dérivés et pour les dérivés

### <a name="constraints-and-size-requests"></a>Contraintes et demandes de taille

Car `LayoutChildren` pour faire `Layout` appel intelligemment à tous ses enfants, il doit connaître une taille *préférée* ou *désirée* pour les enfants. Par conséquent, `Layout` les appels à chacun des enfants sont généralement précédés d’appels

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Après la publication du `GetSizeRequest` livre, la méthode a été dépréciée et remplacée par

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

La `Measure` méthode s’adapte à la [`Margin`](xref:Xamarin.Forms.View.Margin) [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags)propriété et comprend un argument de type , qui a deux membres:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)de ne pas inclure les marges

Pour de `GetSizeRequest` nombreux `Measure` éléments, ou obtient la taille indigène de l’élément de son rendu. Les deux méthodes ont des paramètres pour la largeur et *les contraintes*de hauteur. Par exemple, `Label` un utilisera la contrainte de largeur pour déterminer comment envelopper plusieurs lignes de texte.

Les `GetSizeRequest` `Measure` deux et de [`SizeRequest`](xref:Xamarin.Forms.SizeRequest)retourner une valeur de type , qui a deux propriétés:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)de type`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)de type`Size`

Très souvent, ces deux valeurs `Minimum` sont les mêmes, et la valeur peut généralement être ignorée.

`VisualElement`définit également une méthode `GetSizeRequest` protégée similaire `GetSizeRequest`à celle qui s’appelle de :

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))retourne `SizeRequest` une valeur

Cette méthode est maintenant dépréciée et remplacée par :

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Chaque classe qui `Layout` dérive `Layout<T>` ou `OnSizeRequest` doit `OnMeasure`remplacer ou . C’est là qu’une classe de mise en page détermine sa propre taille, `GetSizeRequest` qui `Measure` est généralement basée sur la taille de ses enfants, qu’elle obtient en appelant ou sur les enfants. Avant et `OnSizeRequest` après `OnMeasure` `GetSizeRequest` l’appel ou, ou `Measure` effectue des ajustements en fonction des propriétés suivantes :

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)de `double`type, `Request` affecte la propriété de`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)de `double`type, `Request` affecte la propriété de`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)de `double`type, `Minimum` affecte la propriété de`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)de `double`type, `Minimum` affecte la propriété de`SizeRequest`

### <a name="infinite-constraints"></a>Contraintes infinies

Les arguments de `GetSizeRequest` contrainte `Measure`transmis `OnSizeRequest` à `OnMeasure`(ou) et (ou ) peuvent `Double.PositiveInfinity`être infinis (c.-à-d., valeurs de ). Cependant, `SizeRequest` le retour de ces méthodes ne peut pas contenir des dimensions infinies.

Les contraintes infinies indiquent que la taille demandée doit refléter la taille naturelle de l’élément. Un `StackLayout` appel `GetSizeRequest` vertical `Measure`(ou) sur ses enfants avec une contrainte de hauteur infinie. Une disposition horizontale de la pile appelle `GetSizeRequest` (ou `Measure`) sur ses enfants avec une contrainte de largeur infinie. Un `AbsoluteLayout` `GetSizeRequest` appel `Measure`(ou) sur ses enfants avec une largeur infinie et des contraintes de hauteur.

### <a name="peeking-inside-the-process"></a>Jeter un coup d’œil à l’intérieur du processus

[**L’ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) affiche des informations de contrainte et de demande de taille pour une mise en page simple.

## <a name="deriving-from-layoutview"></a>Dérivé de Layout\<View>

Une classe de mise `Layout<View>`en page personnalisée dérive de . Il a deux responsabilités :

- Remplacer `OnMeasure` pour `Measure` faire appel à tous les enfants de la mise en page. Retourner une taille demandée pour la mise en page elle-même
- Remplacement `LayoutChildren` pour `Layout` faire appel à tous les enfants de la mise en page

La `for` `foreach` boucle ou la boucle dans ces `IsVisible` remplacements `false`devrait sauter n’importe quel enfant dont la propriété est réglée à .

Un appel `OnMeasure` n’est pas garanti. `OnMeasure`ne sera pas appelé si le parent de la mise en page est la gouvernance de la taille de la mise en page (par exemple, une mise en page qui remplit une page). Pour cette `LayoutChildren` raison, ne peut pas compter `OnMeasure` sur la taille des enfants obtenues au cours de l’appel. Très souvent, `LayoutChildren` doit `Measure` lui-même faire appel aux enfants de la mise en page, ou vous pouvez implémenter une sorte de logique de mise en cache de taille (à discuter plus tard).

### <a name="an-easy-example"></a>Un exemple facile

[**L’échantillon VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) contient une classe simplifiée et une démonstration de son utilisation.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Positionnement vertical et horizontal simplifié

L’un des `VerticalStack` travaux qui `LayoutChildren` doivent être effectué se produit pendant la dérogation. La méthode utilise les `HorizontalOptions` biens de l’enfant pour déterminer `VerticalStack`comment positionner l’enfant dans sa fente dans le . Vous pouvez plutôt appeler [`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))la méthode statique . Cette méthode `Measure` fait appel à `HorizontalOptions` `VerticalOptions` l’enfant et utilise son et ses propriétés pour positionner l’enfant dans le rectangle spécifié.

### <a name="invalidation"></a>Invalidation

Souvent, un changement dans la propriété d’un élément affecte la façon dont cet élément apparaît dans la mise en page. La mise en page doit être invalidée pour déclencher une nouvelle mise en page.

`VisualElement`définit une méthode [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)protégée, qui est généralement appelée par le gestionnaire de propriété modifiée de toute propriété liant dont le changement affecte la taille de l’élément. La `InvalidateMeasure` méthode [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) déclenche un événement.

La `Layout` classe définit une méthode [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)protégée `Layout` similaire nommée , qu’un dérivé devrait appeler à tout changement qui affecte la façon dont il positionne et taille ses enfants.

### <a name="some-rules-for-coding-layouts"></a>Quelques règles pour les mises en page de codage

1. Les propriétés définies par `Layout<T>` les produits dérivés doivent être adossées à des propriétés liants, et les gestionnaires modifiés par la propriété devraient appeler `InvalidateLayout`.

2. Un `Layout<T>` dérivé qui définit les propriétés [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) liantes attachées devrait remplacer [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) pour ajouter un gestionnaire modifié par la propriété à ses enfants et pour enlever ce gestionnaire. Le gestionnaire doit vérifier les changements dans ces `InvalidateLayout`propriétés liantes attachées et répondre en appelant .

3. Un `Layout<T>` dérivé qui implémente une cache `InvalidateLayout` [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) de tailles d’enfants doit remplacer et effacer le cache lorsque ces méthodes sont appelées.

### <a name="a-layout-with-properties"></a>Une mise en page avec des propriétés

La [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe dans le [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) suppose que tous ses enfants sont de la même taille, et enveloppe les enfants d’une rangée (ou colonne) à l’autre. Il définit `Orientation` une `StackLayout`propriété `ColumnSpacing` comme, et les `RowSpacing` propriétés comme `Grid`, et il cache tailles d’enfants.

[**L’échantillon PhotoWrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) met un `WrapLayout` dans un `ScrollView` pour afficher des photos stock.

### <a name="no-unconstrained-dimensions-allowed"></a>Aucune dimension non contrainte n’est permise !

La [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) est destinée à exposer tous ses enfants en elle-même. Par conséquent, il ne peut pas traiter des dimensions sans contrainte et soulève une exception si l’on est rencontré.

[**L’échantillon PhotoGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) démontre `UniformGridLayout`:

[![Triple capture d’écran de Photo Grid](images/ch26fg08-small.png "Disposition uniforme de grille")](images/ch26fg08-large.png#lightbox "Disposition uniforme de grille")

### <a name="overlapping-children"></a>Enfants qui se chevauchent

Un `Layout<T>` dérivé peut chevaucher ses enfants. Cependant, les enfants sont rendus dans `Children` leur ordre dans la `Layout` collection, et non dans l’ordre dans lequel leurs méthodes sont appelées.

La `Layout` classe définit deux méthodes qui vous permettent de déplacer un enfant au sein de la collection :

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View))pour déplacer un enfant au début de la collection
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View))pour déplacer un enfant à la fin de la collection

Pour les enfants qui se chevauchent, les enfants à la fin de la collection apparaissent visuellement au-dessus des enfants au début de la collection.

La [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit une propriété ci-jointe pour indiquer l’ordre de rendu et ainsi permettre à l’un de ses enfants d’être affiché sur les autres. [**L’échantillon StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) démontre ceci :

[![Triple capture d’écran de Student Card File Grid](images/ch26fg10-small.png "Enfants de mise en page qui se chevauchent")](images/ch26fg10-large.png#lightbox "Enfants de mise en page qui se chevauchent")

### <a name="more-attached-bindable-properties"></a>Propriétés liantes plus attachées

La [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit les `Point` propriétés liantes ci-jointes pour spécifier deux valeurs et une valeur d’épaisseur et manipule des `BoxView` éléments pour ressembler aux lignes.

[**L’échantillon UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) utilise cela pour dessiner un cube 3D.

### <a name="layout-and-layoutto"></a>Layout et LayoutTo

Un `Layout<T>` dérivé `LayoutTo` peut `Layout` appeler plutôt que d’animer la mise en page. La [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe le fait, et [**l’échantillon AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) le démontre.

## <a name="related-links"></a>Liens connexes

- [Chapitre 26 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Échantillons du chapitre 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Création de mises en page personnalisées](~/xamarin-forms/user-interface/layouts/custom.md)
