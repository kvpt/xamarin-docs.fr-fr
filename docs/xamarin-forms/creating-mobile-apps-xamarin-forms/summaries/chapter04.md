---
titre : «Résumé du chapitre 4. Défilement de la pile « Description : » création de Mobile Apps avec Xamarin.Forms : Résumé du chapitre 4. Défilement de la pile «ms. Prod : xamarin ms. Technology : xamarin-Forms ms. AssetID : 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44 auteur : davidbritch ms. Author : dabritch ms. Date : 07/19/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Résumé du chapitre 4. Défilement de la pile

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Ce chapitre est principalement consacré à l’introduction du concept de *disposition*, qui est le terme global pour les classes et les techniques utilisées par Xamarin.Forms pour organiser l’affichage visuel de plusieurs vues sur la page.

La disposition implique plusieurs classes qui dérivent de [`Layout`](xref:Xamarin.Forms.Layout) et [`Layout<T>`](xref:Xamarin.Forms.Layout`1) . Ce chapitre se concentre sur [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

> [!NOTE]
> Le [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introduit dans Xamarin.Forms 3,0 peut être utilisé de manière similaire à `StackLayout` mais avec davantage de flexibilité.

Les [`ScrollView`](xref:Xamarin.Forms.ScrollView) classes, et sont également présentées dans ce chapitre [`Frame`](xref:Xamarin.Forms.Frame) [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="stacks-of-views"></a>Piles de vues

[`StackLayout`](xref:Xamarin.Forms.StackLayout)dérive de `Layout<View>` et hérite d’une [`Children`](xref:Xamarin.Forms.Layout`1) propriété de type `IList<View>` . Vous ajoutez plusieurs éléments d’affichage à cette collection et `StackLayout` les affiche dans une pile horizontale ou verticale.

Affectez [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) à la propriété de `StackLayout` la valeur un membre de l' [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) énumération, [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) ou [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) . Par défaut, il s’agit de `Vertical`.

Affectez [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) à la propriété de la `StackLayout` `double` valeur une valeur pour spécifier l’espacement entre les enfants. La valeur par défaut est 6.

Dans le code, vous pouvez ajouter des éléments à la `Children` collection de `StackLayout` dans une `for` boucle ou, `foreach` comme illustré dans l’exemple [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) , ou vous pouvez initialiser la `Children` collection avec une liste des vues individuelles, comme illustré dans [**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Les enfants doivent dériver de `View` , mais peuvent inclure d’autres `StackLayout` objets.

## <a name="scrolling-content"></a>Défilement du contenu

Si un `StackLayout` contient trop d’enfants à afficher sur une page, vous pouvez placer `StackLayout` dans un [`ScrollView`](xref:Xamarin.Forms.ScrollView) pour autoriser le défilement.

Affectez [`Content`](xref:Xamarin.Forms.ScrollView.Content) à la propriété de `ScrollView` la vue que vous souhaitez faire défiler. Il s’agit souvent d’un `StackLayout` , mais il peut s’agir de n’importe quelle vue.

Affectez [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) à la propriété de `ScrollView` la valeur un membre de la [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) propriété,, [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical) [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal) ou [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both) . Par défaut, il s’agit de `Vertical`. Si le contenu d’un `ScrollView` est un `StackLayout` , les deux orientations doivent être cohérentes.

L’exemple [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) illustre l’utilisation de `ScrollView` et `StackLayout` pour afficher les couleurs disponibles. L’exemple montre également comment utiliser la réflexion .NET pour obtenir toutes les propriétés et les champs statiques publics de la `Color` structure sans qu’il soit nécessaire de les répertorier explicitement.

## <a name="the-expands-option"></a>L’option développée

Quand un `StackLayout` empile ses enfants, chaque enfant occupe un emplacement particulier dans la hauteur totale du `StackLayout` qui dépend de la taille de l’enfant et des paramètres de ses `HorizontalOptions` Propriétés et `VerticalOptions` . Les valeurs de type sont affectées à ces propriétés [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) .

La `LayoutOptions` structure définit deux propriétés :

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)du type énumération [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) avec quatre membres,,, [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End) et[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)de type`bool`

Pour plus de commodité, la `LayoutOptions` structure définit également huit champs statiques en lecture seule de type `LayoutOptions` qui englobent toutes les combinaisons des deux propriétés d’instance :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La discussion suivante implique une `StackLayout` avec une orientation verticale par défaut. Le horizontal `StackLayout` est analogue.

Pour un vertical `StackLayout` , le `HorizontalOptions` paramètre détermine la position horizontale d’un enfant dans la largeur du `StackLayout` . Un `Alignment` paramètre `Start` , `Center` ou `End` provoque l’incontrainte horizontale de l’enfant. L’enfant détermine sa propre largeur et est positionné à gauche, au centre ou à droite du `StackLayout` . `Fill`Avec l’option, l’enfant est contraint horizontalement et remplit la largeur du `StackLayout` .

Pour un vertical `StackLayout` , chaque enfant est sans contrainte verticalement et obtient un emplacement vertical en fonction de la hauteur de l’enfant, auquel cas le `VerticalOptions` paramètre n’est pas pertinent.

Si le vertical `StackLayout` lui-même est sans contrainte &mdash; si son `VerticalOptions` paramètre est `Start` , `Center` ou `End` , la hauteur de `StackLayout` est la hauteur totale de ses enfants.

Toutefois, si la verticale `StackLayout` est restreinte verticalement &mdash; si son `VerticalOptions` paramètre est, `Fill` &mdash; la hauteur de est `StackLayout` la hauteur de son conteneur, qui peut être supérieure à la hauteur totale de ses enfants. Si tel est le cas, et si au moins un enfant a un `VerticalOptions` paramètre avec un `Expands` indicateur `true` , alors l’espace supplémentaire dans le `StackLayout` est alloué de manière égale entre tous ces enfants avec un `Expands` indicateur de `true` . La hauteur totale des enfants est alors égale à la hauteur du `StackLayout` et la `Alignment` partie du `VerticalOptions` paramètre détermine la position verticale de l’enfant dans son emplacement.

Cela est illustré dans l’exemple [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) .

## <a name="frame-and-boxview"></a>Frame et BoxView

Ces deux vues rectangulaires sont souvent utilisées à des fins de présentation.

La [`Frame`](xref:Xamarin.Forms.Frame) vue affiche une image rectangulaire autour d’une autre vue, qui peut être une disposition telle que `StackLayout` . `Frame`hérite d’une [`Content`](xref:Xamarin.Forms.ContentView.Content) propriété de [`ContentView`](xref:Xamarin.Forms.ContentView) que vous affectez à la vue à afficher dans le `Frame` . `Frame`Est transparent par défaut. Définissez les trois propriétés suivantes pour personnaliser l’apparence du frame :

- [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor)Propriété pour la rendre visible. Il est courant de définir `OutlineColor` sur `Color.Accent` lorsque vous ne connaissez pas le modèle de couleurs sous-jacent.
- La [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) propriété peut avoir la valeur `true` pour afficher une ombre noire sur les appareils iOS.
- Affectez [`Padding`](xref:Xamarin.Forms.Layout.Padding) à la propriété une `Thickness` valeur pour conserver un espace entre le frame et le contenu du frame. La valeur par défaut est 20 unités sur tous les côtés.

`Frame`A la valeur par défaut `HorizontalOptions` et les `VerticalOptions` valeurs de, ce qui `LayoutOptions.Fill` signifie que le `Frame` remplit son conteneur. Avec d’autres paramètres, la taille de `Frame` est basée sur la taille de son contenu.

`Frame`Est illustré dans l’exemple [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) .

[`BoxView`](xref:Xamarin.Forms.BoxView)Affiche une zone rectangulaire de couleur spécifiée par sa [`Color`](xref:Xamarin.Forms.BoxView.Color) propriété.

Si le `BoxView` est imposé (ses `HorizontalOptions` `VerticalOptions` Propriétés et ont leurs paramètres par défaut `LayoutOptions.Fill` ), le `BoxView` remplit l’espace disponible. Si le `BoxView` n’est pas contraint (avec `HorizontalOptions` les `LayoutOptions` paramètres et de `Start` , `Center` ou `End` ), il a une dimension par défaut de 40 unités carrées. Un `BoxView` peut être contraint dans une dimension et sans contrainte dans l’autre.

Souvent, vous définissez les [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriétés et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de `BoxView` pour lui attribuer une taille spécifique. Cela est illustré par l’exemple [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) .

Vous pouvez utiliser plusieurs instances de `StackLayout` pour combiner une `BoxView` et plusieurs `Label` instances dans un `Frame` pour afficher une couleur particulière, puis placer chacune de ces vues dans un `StackLayout` `ScrollView` pour créer la liste attrayante de couleurs affichées dans l’exemple [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) :

[![Capture d’écran triple des blocs de couleur](images/ch04fg11-small.png "Liste de couleurs")](images/ch04fg11-large.png#lightbox "Liste de couleurs")

## <a name="a-scrollview-in-a-stacklayout"></a>Un ScrollView dans un StackLayout ?

Le fait de placer un `StackLayout` dans un `ScrollView` est courant, mais le fait de placer un `ScrollView` dans un `StackLayout` est également parfois pratique. En théorie, cela ne devrait pas être possible, car les enfants d’un vertical `StackLayout` sont non restreints verticalement. Mais un `ScrollView` doit être restreint verticalement. Elle doit avoir une hauteur spécifique pour pouvoir déterminer ensuite la taille de son enfant pour le défilement.

L’astuce consiste à attribuer à l' `ScrollView` enfant du `StackLayout` paramètre a la `VerticalOptions` valeur `FillAndExpand` . Cela est illustré dans l’exemple [**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) .

L’exemple **BlackCat** montre également comment définir des ressources de programme incorporées dans la bibliothèque partagée et y accéder. Cela peut également être accompli avec les SAP (Shared Asset Projects), mais le processus est un peu plus délicat, comme le montre l’exemple [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) .

## <a name="related-links"></a>Liens connexes

- [Chapitre 4 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Exemples du chapitre 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Exemples de chapitre 4 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scrollview.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
