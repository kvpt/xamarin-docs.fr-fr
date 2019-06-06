---
title: Dispositions Xamarin.Forms
description: Dispositions Xamarin.Forms sont utilisées pour composer des contrôles d’interface utilisateur dans des structures visual. Cet article répertorie les dispositions incluses dans Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 84ebe6b12e34a5e84c03b095c3909945e84a17f8
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66741085"
---
# <a name="xamarinforms-layouts"></a>Dispositions Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)

_Dispositions Xamarin.Forms sont utilisées pour composer des contrôles d’interface utilisateur dans des structures visual._

Le [ `Layout` ](xref:Xamarin.Forms.Layout) et [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classes dans Xamarin.Forms sont des sous-types spécialisés de vues qui agissent comme conteneurs pour les vues et d’autres dispositions. Le `Layout` dérive de la classe elle-même [ `View` ](views.md). Un `Layout` dérivé contient généralement une logique pour définir la position et la taille des éléments enfants dans les applications Xamarin.Forms.

[![Types de disposition de Xamarin.Forms](layouts-images/layouts-sml.png "Xamarin.Forms disposition Types")](layouts-images/layouts.png#lightbox "Types de disposition de Xamarin.Forms")

Les classes qui dérivent de `Layout` peut être divisé en deux catégories :

## <a name="layouts-with-single-content"></a>Dispositions de contenu unique

Ces classes dérivent [ `Layout` ](xref:Xamarin.Forms.Layout), qui définit [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) et [ `IsClippedToBounds` ](xref:Xamarin.Forms.Layout.IsClippedToBounds) propriétés.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contient un seul enfant qui est défini avec la [ `Content` ](xref:Xamarin.Forms.ContentView.Content) propriété. Le `Content` propriété peut être définie sur any `View` dérivé, y compris d’autres `Layout` dérivés. `ContentView` est principalement utilisé comme un élément structurel et sert de classe de base à [ `Frame` ](#frame).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ContentView) | [![Exemple de ContentView](layouts-images/ContentView.png "ContentView exemple")](layouts-images/ContentView-Large.png#lightbox "ContentView exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| Le [ `Frame` ](xref:Xamarin.Forms.Frame) dérive de la classe [ `ContentView` ](#contentView) et affiche un cadre rectangulaire autour de ses enfants. `Frame` a une valeur par défaut [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) valeur 20 et définit également [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor), [ `CornerRadius` ](xref:Xamarin.Forms.Frame.CornerRadius), et [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow)propriétés.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Frame) | [![Exemple de frame](layouts-images/Frame.png "Frame exemple")](layouts-images/Frame-Large.png#lightbox "Frame exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) est capable de faire défiler son contenu. Définir le [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) propriété à une vue ou une disposition trop volumineuse pour tenir sur l’écran. (Le contenu d’un `ScrollView` est très souvent une [ `StackLayout` ](#stackLayout).) Définir le [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) propriété pour indiquer si le défilement doit être vertical, horizontal, ou les deux.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ScrollView) / [Guide](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemple de ScrollView](layouts-images/ScrollView.png "ScrollView exemple")](layouts-images/ScrollView-Large.png#lightbox "ScrollView exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) Affiche le contenu avec un modèle de contrôle, et est la classe de base pour [ `ContentView` ](#contentView).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.TemplatedView) / [Guide](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemple de TemplatedView](layouts-images/TemplatedView.png "TemplatedView exemple")](layouts-images/TemplatedView.png#lightbox "TemplatedView exemple") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) est un gestionnaire de disposition pour les vues basées sur un modèle, utilisé dans un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) pour marquer où s’affiche le contenu qui doit être présentées.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ContentPresenter) / [Guide](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemple de ContentPresenter](layouts-images/ContentPresenter.png "ContentPresenter exemple")](layouts-images/ContentPresenter.png#lightbox "ContentPresenter exemple") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Dispositions avec plusieurs enfants

Ces classes dérivent [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) Positionne des éléments enfants dans une pile soit horizontalement ou verticalement en se basant sur le [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propriété. Le [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) propriété régit l’espacement entre les enfants et a la valeur par défaut de 6.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.StackLayout) / [Guide](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![Exemple de StackLayout](layouts-images/StackLayout.png "StackLayout exemple")](layouts-images/StackLayout-Large.png#lightbox "StackLayout exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grille

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) Positionne ses éléments enfants dans une grille de lignes et colonnes. Position d’un enfant est indiquée à l’aide de la [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](xref:Xamarin.Forms.Grid.RowProperty), [ `Column` ](xref:Xamarin.Forms.Grid.ColumnProperty), [ `RowSpan` ](xref:Xamarin.Forms.Grid.RowSpanProperty), et [ `ColumnSpan` ](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Grid) / [Guide](~/xamarin-forms/user-interface/layouts/grid.md) / [exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemple de grille](layouts-images/Grid.png "exemple de grille")](layouts-images/Grid-Large.png#lightbox "exemple de grille")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>DispositionAbsolue

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) Positionne des éléments enfants à des emplacements spécifiques par rapport à son parent. Position d’un enfant est indiquée à l’aide de la [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) et [ `LayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Un `AbsoluteLayout` est utile pour animer la position des vues.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.AbsoluteLayout) / [Guide](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemple de DispositionAbsolue](layouts-images/AbsoluteLayout.png "DispositionAbsolue exemple")](layouts-images/AbsoluteLayout-Large.png#lightbox "DispositionAbsolue exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Positionne des éléments enfants relatif à la `RelativeLayout` lui-même ou à leurs frères. Position d’un enfant est indiquée à l’aide de la [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) définies sur des objets de type [ `Constraint` ](xref:Xamarin.Forms.Constraint) et [ `BoundsConstraint` ](xref:Xamarin.Forms.Constraint).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.RelativeLayout) / [Guide](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemple de RelativeLayout](layouts-images/RelativeLayout.png "RelativeLayout exemple")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) est basé sur le code CSS [Module de disposition de zone Flexible](http://www.w3.org/TR/css-flexbox-1/), couramment appelé _flex disposition_ ou _flex-box_. `FlexLayout` définit six propriétés pouvant être liées et cinq propriétés jointes pouvant être liées qui permettent à des enfants à empilés ou encapsulé avec de nombreuses options d’alignement et l’orientation.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.FlexLayout) / [Guide](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/) | [![Exemple de FlexLayout](layouts-images/FlexLayout.png "FlexLayout exemple")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Liens associés

- [Exemple de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Exemples Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
