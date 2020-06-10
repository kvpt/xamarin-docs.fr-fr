---
titre : «Résumé du chapitre 26. Dispositions personnalisées "Description :" création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 26. Dispositions personnalisées "ms. Prod : xamarin ms. Technology : xamarin-Forms ms. AssetID : 2B7F4346-414E-49FF-97FB-B85E92D98A21 auteur : davidbritch ms. Author : dabritch ms. Date : 11/07/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-26-custom-layouts"></a>Résumé du chapitre 26. Dispositions personnalisées

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Formscomprend plusieurs classes dérivées de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) :

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` et
- `RelativeLayout`.

Ce chapitre décrit comment créer vos propres classes qui dérivent de `Layout<View>` .

## <a name="an-overview-of-layout"></a>Vue d’ensemble de la disposition

Il n’existe aucun système centralisé qui gère la Xamarin.Forms disposition. Chaque élément est responsable de la détermination de sa propre taille et de la manière de s’afficher dans une zone particulière.

### <a name="parents-and-children"></a>Parents et enfants

Chaque élément ayant des enfants est chargé de positionner ces enfants dans lui-même. Il s’agit du parent qui détermine la taille de ses enfants en fonction de la taille disponible et de la taille que l’enfant souhaite avoir.

### <a name="sizing-and-positioning"></a>Dimensionnement et positionnement

La disposition commence en haut de l’arborescence d’éléments visuels par la page, puis passe par toutes les branches. La méthode publique la plus importante de la disposition est [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)) défini par `VisualElement` . Chaque élément qui est un parent d’autres éléments appelle `Layout` pour chacun de ses enfants pour attribuer à l’enfant une taille et une position par rapport à lui-même sous la forme d’une [`Rectangle`](xref:Xamarin.Forms.Rectangle) valeur. Ces `Layout` appels se propagent via l’arborescence d’éléments visuels.

Un appel à `Layout` est requis pour qu’un élément apparaisse sur l’écran et entraîne la définition des propriétés en lecture seule suivantes. Ils sont cohérents avec le `Rectangle` passé à la méthode :

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)de type`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)de type`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)de type`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)de type`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)de type`double`

Avant l' `Layout` appel, `Height` et `Width` ont des valeurs factices de &ndash; 1.

Un appel à `Layout` déclenche également des appels aux méthodes protégées suivantes :

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), qui appelle
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), qui peut être substitué.

Enfin, l’événement suivant est déclenché :

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

La `OnSizeAllocated` méthode est substituée par `Page` et `Layout` , qui sont les seules deux classes de Xamarin.Forms qui peuvent avoir des enfants. La méthode substituée appelle

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)pour les `Page` dérivés et [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) pour les `Layout` dérivés, qui appellent
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))pour les `Page` dérivés et [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) pour les `Layout` dérivés.

`LayoutChildren`appelle ensuite `Layout` pour chacun des enfants de l’élément. Si au moins un enfant a un nouveau `Bounds` paramètre, l’événement suivant est déclenché :

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)pour les `Page` dérivés et [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) pour les `Layout` dérivés

### <a name="constraints-and-size-requests"></a>Contraintes et requêtes de taille

Pour `LayoutChildren` que appelle intelligemment `Layout` tous ses enfants, il doit connaître la taille *préférée* ou *souhaitée* pour les enfants. Par conséquent, les appels à `Layout` pour chacun des enfants sont généralement précédés d’appels à

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Une fois le livre publié, la `GetSizeRequest` méthode était dépréciée et remplacée par

- [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags))

La `Measure` méthode [`Margin`](xref:Xamarin.Forms.View.Margin) prend en charge la propriété et inclut un argument de type [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags) , qui a deux membres :

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)pour ne pas inclure de marges

Pour de nombreux éléments, `GetSizeRequest` ou `Measure` obtient la taille native de l’élément à partir de son convertisseur. Les deux méthodes ont des paramètres pour les *contraintes*de largeur et de hauteur. Par exemple, un `Label` utilise la contrainte Width pour déterminer comment encapsuler plusieurs lignes de texte.

`GetSizeRequest`Et `Measure` retournent toutes deux une valeur de type [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) , qui a deux propriétés :

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)de type`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)de type`Size`

Très souvent, ces deux valeurs sont les mêmes, et la `Minimum` valeur peut généralement être ignorée.

`VisualElement`définit également une méthode protégée semblable à `GetSizeRequest` celle-ci qui est appelée à partir de `GetSizeRequest` :

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))retourne une `SizeRequest` valeur

Cette méthode est désormais dépréciée et remplacée par :

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Chaque classe qui dérive de `Layout` ou `Layout<T>` doit substituer `OnSizeRequest` ou `OnMeasure` . C’est là qu’une classe Layout détermine sa propre taille, qui est généralement basée sur la taille de ses enfants, qu’elle obtient en appelant `GetSizeRequest` ou `Measure` sur les enfants. Avant et après l’appel de `OnSizeRequest` ou `OnMeasure` , `GetSizeRequest` ou `Measure` apporte des ajustements basés sur les propriétés suivantes :

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)de type `double` , affecte la `Request` propriété de`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)de type `double` , affecte la `Request` propriété de`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)de type `double` , affecte la `Minimum` propriété de`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)de type `double` , affecte la `Minimum` propriété de`SizeRequest`

### <a name="infinite-constraints"></a>Contraintes infinies

Les arguments de contrainte passés à `GetSizeRequest` (ou `Measure` ) et `OnSizeRequest` (ou `OnMeasure` ) peuvent être infinis (c’est-à-dire, des valeurs de `Double.PositiveInfinity` ). Toutefois, le `SizeRequest` retourné à partir de ces méthodes ne peut pas contenir de dimensions infinies.

Les contraintes infinies indiquent que la taille demandée doit refléter la taille naturelle de l’élément. Un `StackLayout` appel vertical `GetSizeRequest` (ou `Measure` ) sur ses enfants avec une contrainte de hauteur infinie. Une disposition de pile horizontale appelle `GetSizeRequest` (ou `Measure` ) sur ses enfants avec une contrainte de largeur infinie. Un `AbsoluteLayout` appelle `GetSizeRequest` (ou `Measure` ) sur ses enfants avec des contraintes de largeur et de hauteur infinies.

### <a name="peeking-inside-the-process"></a>Lecture à l’intérieur du processus

[**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) affiche les informations de demande de contrainte et de taille pour une mise en page simple.

## <a name="deriving-from-layoutview"></a>Dérivation à partir de la disposition\<View>

Une classe de disposition personnalisée dérive de `Layout<View>` . Il a deux responsabilités :

- Substituez `OnMeasure` pour appeler `Measure` sur tous les enfants de la disposition. Retourner une taille demandée pour la disposition elle-même
- Substituer `LayoutChildren` pour appeler `Layout` sur tous les enfants de la disposition

La `for` `foreach` boucle ou dans ces substitutions doit ignorer tout enfant dont la `IsVisible` propriété a la valeur `false` .

Un appel à `OnMeasure` n’est pas garanti. `OnMeasure`n’est pas appelé si le parent de la disposition régit la taille de la disposition (par exemple, une disposition qui remplit une page). Pour cette raison, `LayoutChildren` ne peut pas reposer sur les tailles enfants obtenues pendant l' `OnMeasure` appel. Très souvent, `LayoutChildren` doit lui-même appeler `Measure` sur les enfants de la disposition, ou vous pouvez implémenter une logique de mise en cache de taille (à discuter plus tard).

### <a name="an-easy-example"></a>Exemple simple

L’exemple [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contient une classe simplifiée [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) et une démonstration de son utilisation.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Positionnement vertical et horizontal simplifié

L’une des tâches qui `VerticalStack` doivent être exécutées se produit pendant le `LayoutChildren` remplacement. La méthode utilise la propriété de l’enfant `HorizontalOptions` pour déterminer comment positionner l’enfant dans son emplacement dans le `VerticalStack` . Vous pouvez appeler à la place la méthode statique [ `Layout.LayoutChildIntoBoundingRect` ] (XREF : Xamarin.Forms . Layout. LayoutChildIntoBoundingRegion ( Xamarin.Forms . VisualElement, Xamarin.Forms . Rectangle)). Cette méthode appelle `Measure` sur l’enfant et utilise ses `HorizontalOptions` `VerticalOptions` Propriétés et pour positionner l’enfant dans le rectangle spécifié.

### <a name="invalidation"></a>Invalidation

Souvent, une modification de la propriété d’un élément affecte la façon dont cet élément apparaît dans la disposition. La disposition doit être invalidée pour déclencher une nouvelle disposition.

`VisualElement`définit une méthode protégée [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) , qui est généralement appelée par le gestionnaire de modification de propriété d’une propriété pouvant être liée dont la modification affecte la taille de l’élément. La `InvalidateMeasure` méthode déclenche un [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) événement.

La `Layout` classe définit une méthode protégée semblable nommée [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) , qu’un `Layout` dérivée doit appeler pour toute modification qui affecte la façon dont il positionne et dimensionne ses enfants.

### <a name="some-rules-for-coding-layouts"></a>Certaines règles de codage des dispositions

1. Les propriétés définies par `Layout<T>` les dérivés doivent être sauvegardées par des propriétés pouvant être liées et les gestionnaires de propriétés modifiées doivent appeler `InvalidateLayout` .

2. Un `Layout<T>` dérivé qui définit des propriétés pouvant être liées peuvent être substitués [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) pour ajouter un gestionnaire de modification de propriété à ses enfants et [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) pour supprimer ce gestionnaire. Le gestionnaire doit vérifier les modifications apportées à ces propriétés pouvant être liées et répondre en appelant `InvalidateLayout` .

3. Un `Layout<T>` dérivé qui implémente un cache de tailles enfants doit substituer `InvalidateLayout` et [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) et effacer le cache lorsque ces méthodes sont appelées.

### <a name="a-layout-with-properties"></a>Disposition avec des propriétés

La [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe de [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) suppose que tous ses enfants ont la même taille et encapsule les enfants d’une ligne (ou colonne) à la suivante. Il définit une `Orientation` propriété comme `StackLayout` , et, et des `ColumnSpacing` `RowSpacing` propriétés comme `Grid` , et il met en cache des tailles enfants.

L’exemple [**photowrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) met un `WrapLayout` dans un `ScrollView` pour afficher des photos boursières.

### <a name="no-unconstrained-dimensions-allowed"></a>Aucune dimension non contrainte n’est autorisée.

Le [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) est conçu pour afficher tous ses enfants dans lui-même. Par conséquent, il ne peut pas traiter les dimensions sans contrainte et lève une exception si une exception est rencontrée.

L’exemple [**photogrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) montre les `UniformGridLayout` éléments suivants :

[![Capture d’écran triple de la grille de photos](images/ch26fg08-small.png "Disposition de grille uniforme")](images/ch26fg08-large.png#lightbox "Disposition de grille uniforme")

### <a name="overlapping-children"></a>Chevauchement des enfants

Un `Layout<T>` dérivé peut chevaucher ses enfants. Toutefois, les enfants sont rendus dans leur ordre dans la `Children` collection, et non dans l’ordre dans lequel leurs `Layout` méthodes sont appelées.

La `Layout` classe définit deux méthodes qui vous permettent de déplacer un enfant dans la collection :

- [ `LowerChild` ] (XREF : Xamarin.Forms . Layout. LowerChild ( Xamarin.Forms . View)) pour déplacer un enfant au début de la collection
- [ `RaiseChild` ] (XREF : Xamarin.Forms . Layout. RaiseChild ( Xamarin.Forms . View)) pour déplacer un enfant à la fin de la collection

Pour les enfants qui se chevauchent, les enfants à la fin de la collection apparaissent visuellement en haut des enfants au début de la collection.

La [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit une propriété jointe pour indiquer l’ordre de rendu et permettre ainsi l’affichage de l’un de ses enfants en plus des autres. L’exemple [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) illustre ce qui suit :

[![Capture triple de la grille du fichier de carte étudiant](images/ch26fg10-small.png "Superposition des enfants de disposition")](images/ch26fg10-large.png#lightbox "Superposition des enfants de disposition")

### <a name="more-attached-bindable-properties"></a>Plus de propriétés pouvant être liées

La [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit des propriétés pouvant être liées et associées pour spécifier deux `Point` valeurs et une valeur d’épaisseur et manipuler des `BoxView` éléments pour ressembler à des lignes.

L’exemple [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) utilise cette valeur pour dessiner un cube 3D.

### <a name="layout-and-layoutto"></a>Disposition et LayoutTo

Un `Layout<T>` dérivé peut appeler `LayoutTo` plutôt que `Layout` d’animer la disposition. La [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe effectue cette opération, et l’exemple [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) l’illustre.

## <a name="related-links"></a>Liens connexes

- [Chapitre 26 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Exemples du chapitre 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Créer des dispositions personnalisées](~/xamarin-forms/user-interface/layouts/custom.md)
