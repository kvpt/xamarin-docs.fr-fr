---
titre : « Xamarin.Forms dispositions » Description : Xamarin.Forms les dispositions sont utilisées pour composer des contrôles d’interface utilisateur dans des structures visuelles. Cet article répertorie les mises en page incluses dans Xamarin.Forms .
ms. Prod : xamarin ms. AssetID : F4180997-BA21-453A-9958-D1E2940DF050 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 05/21/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-layouts"></a>Xamarin.FormsDispositions

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Les dispositions Xamarin. Forms sont utilisées pour composer des contrôles d’interface utilisateur dans des structures visuelles._

Les [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classes et dans Xamarin.Forms sont des sous-types spécialisés de vues qui jouent le rôle de conteneurs pour les vues et d’autres dispositions. La `Layout` classe elle-même dérive de [`View`](views.md) . Un `Layout` dérivé contient généralement une logique pour définir la position et la taille des éléments enfants dans les Xamarin.Forms applications.

[![Xamarin.FormsTypes de disposition](layouts-images/layouts-sml.png "[! Opérationnel. NO-LOC (Xamarin. Forms)] types de disposition")](layouts-images/layouts.png#lightbox "[! Opérationnel. NO-LOC (Xamarin. Forms)] types de disposition")

Les classes qui dérivent de `Layout` peuvent être divisées en deux catégories :

## <a name="layouts-with-single-content"></a>Dispositions avec contenu unique

Ces classes dérivent de [`Layout`](xref:Xamarin.Forms.Layout) , qui définit les [`Padding`](xref:Xamarin.Forms.Layout.Padding) [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) Propriétés et.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView)contient un seul enfant qui est défini avec la [`Content`](xref:Xamarin.Forms.ContentView.Content) propriété. La `Content` propriété peut être définie sur n’importe quel `View` dérivé, y compris d’autres `Layout` dérivés. `ContentView`est principalement utilisé comme un élément structurel et sert de classe de base à [`Frame`](#frame) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.ContentView)  /  [Guide](~/xamarin-forms/user-interface/layouts/contentview.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) | [![Exemple ContentView](layouts-images/ContentView.png "Exemple ContentView")](layouts-images/ContentView-Large.png#lightbox "Exemple ContentView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| La [`Frame`](xref:Xamarin.Forms.Frame) classe dérive de [`ContentView`](#contentView) et affiche une bordure, ou Frame, autour de son enfant. La `Frame` classe a une valeur par défaut [`Padding`](xref:Xamarin.Forms.Layout.Padding) de 20 et définit également [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) les [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) Propriétés, et [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Frame)  /  [Guide](~/xamarin-forms/user-interface/layouts/frame.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Exemple de frame](layouts-images/Frame.png "Exemple de frame")](layouts-images/Frame-Large.png#lightbox "Exemple de frame")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView)peut faire défiler son contenu. Affectez [`Content`](xref:Xamarin.Forms.ScrollView.Content) à la propriété une vue ou une disposition trop grande pour tenir sur l’écran. (Le contenu d’un `ScrollView` est très souvent un [`StackLayout`](#stackLayout) .) Définissez la [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propriété pour indiquer si le défilement doit être vertical, horizontal ou les deux.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.ScrollView)  /  [Guide](~/xamarin-forms/user-interface/layouts/scrollview.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple ScrollView](layouts-images/ScrollView.png "Exemple ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Exemple ScrollView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)affiche le contenu avec un modèle de contrôle et est la classe de base pour [`ContentView`](#contentView) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TemplatedView)  /  [Guide](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemple TemplatedView](layouts-images/TemplatedView.png "Exemple TemplatedView")](layouts-images/TemplatedView.png#lightbox "Exemple TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)est un gestionnaire de disposition pour les vues basées sur des modèles, utilisé dans un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pour marquer l’emplacement où le contenu à présenter s’affiche.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.ContentPresenter)  /  [Guide](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemple ContentPresenter](layouts-images/ContentPresenter.png "Exemple ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Exemple ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Dispositions avec plusieurs enfants

Ces classes dérivent de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) .

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout)positionne les éléments enfants dans une pile, horizontalement ou verticalement, en fonction de la [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriété. La [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propriété régit l’espacement entre les enfants et a une valeur par défaut de 6.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.StackLayout)  /  [Guide](~/xamarin-forms/user-interface/layouts/stacklayout.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Exemple StackLayout](layouts-images/StackLayout.png "Exemple StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Exemple StackLayout")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grille

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid)positionne ses éléments enfants dans une grille de lignes et de colonnes. La position d’un enfant est indiquée à l’aide des [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty) ,, [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) et [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Grid)  /  [Guide](~/xamarin-forms/user-interface/layouts/grid.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple de grille](layouts-images/Grid.png "Exemple de grille")](layouts-images/Grid-Large.png#lightbox "Exemple de grille")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)positionne des éléments enfants à des emplacements spécifiques par rapport à son parent. La position d’un enfant est indiquée à l’aide des [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) et [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) . Un `AbsoluteLayout` est utile pour animer les positions des vues.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.AbsoluteLayout)  /  [Guide](~/xamarin-forms/user-interface/layouts/absolute-layout.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple AbsoluteLayout](layouts-images/AbsoluteLayout.png "Exemple AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Exemple AbsoluteLayout")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)positionne des éléments enfants par rapport à `RelativeLayout` lui-même ou à leurs frères. La position d’un enfant est indiquée à l’aide des [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) qui sont définies sur des objets de type [`Constraint`](xref:Xamarin.Forms.Constraint) et [`BoundsConstraint`](xref:Xamarin.Forms.Constraint) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.RelativeLayout)  /  [Guide](~/xamarin-forms/user-interface/layouts/relative-layout.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple RelativeLayout](layouts-images/RelativeLayout.png "Exemple RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Exemple RelativeLayout")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)est basé sur le [module de disposition de zone flexible](https://www.w3.org/TR/css-flexbox-1/)CSS, communément appelé _disposition flexible_ ou _zone flexible_. `FlexLayout`définit six propriétés pouvant être liées et cinq propriétés pouvant être liées qui permettent d’empiler des enfants ou de les envelopper avec de nombreuses options d’alignement et d’orientation.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.FlexLayout)  /  [Guide](~/xamarin-forms/user-interface/layouts/flex-layout.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Exemple FlexLayout](layouts-images/FlexLayout.png "Exemple FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Exemple FlexLayout")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsExemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsExtraits](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentation sur les API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
