---
title: Dispositions Xamarin.Forms
description: Dispositions Xamarin.Forms sont utilisées pour composer des contrôles d’interface utilisateur dans des structures visual. Cet article répertorie les dispositions incluses dans Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 4747ce6555a6440c687dc3d239d75307f68683ca
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291833"
---
# <a name="xamarinforms-layouts"></a>Dispositions Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Les dispositions Xamarin. Forms sont utilisées pour composer des contrôles d’interface utilisateur dans des structures visuelles._

Les classes [`Layout`](xref:Xamarin.Forms.Layout) et [`Layout<T>`](xref:Xamarin.Forms.Layout`1) dans Xamarin. Forms sont des sous-types spécialisés de vues qui jouent le rôle de conteneurs pour les vues et d’autres dispositions. La classe `Layout` elle-même dérive de [`View`](views.md). Un dérivé de `Layout` contient généralement une logique pour définir la position et la taille des éléments enfants dans les applications Xamarin. Forms.

[![Types de disposition Xamarin. Forms](layouts-images/layouts-sml.png "Types de disposition Xamarin. Forms")](layouts-images/layouts.png#lightbox "Types de disposition Xamarin. Forms")

Les classes qui dérivent de `Layout` peuvent être divisées en deux catégories :

## <a name="layouts-with-single-content"></a>Dispositions de contenu unique

Ces classes dérivent de [`Layout`](xref:Xamarin.Forms.Layout), qui définit les propriétés de [`Padding`](xref:Xamarin.Forms.Layout.Padding) et de [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) .

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contient un seul enfant défini avec la propriété [`Content`](xref:Xamarin.Forms.ContentView.Content) . La propriété `Content` peut être définie sur n’importe quel `View` dérivé, y compris d’autres dérivés `Layout`. `ContentView` est principalement utilisé comme un élément structurel et sert de classe de base pour [`Frame`](#frame).<br /><br />[Documentation](xref:Xamarin.Forms.ContentView) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/contentview.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) | [![Exemple ContentView](layouts-images/ContentView.png "Exemple ContentView")](layouts-images/ContentView-Large.png#lightbox "Exemple ContentView")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| La classe [`Frame`](xref:Xamarin.Forms.Frame) dérive de [`ContentView`](#contentView) et affiche une bordure, ou Frame, autour de son enfant. La classe `Frame` a une valeur de [`Padding`](xref:Xamarin.Forms.Layout.Padding) par défaut de 20 et définit également les propriétés [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor), [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)et [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentation](xref:Xamarin.Forms.Frame) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/frame.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Exemple de frame](layouts-images/Frame.png "Exemple de frame")](layouts-images/Frame-Large.png#lightbox "Exemple de frame")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) peut faire défiler son contenu. Définissez la propriété [`Content`](xref:Xamarin.Forms.ScrollView.Content) sur une vue ou une disposition trop grande pour tenir sur l’écran. (Le contenu d’un `ScrollView` est très souvent un [`StackLayout`](#stackLayout).) Définissez la propriété [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) pour indiquer si le défilement doit être vertical, horizontal ou les deux.<br /><br />[Documentation](xref:Xamarin.Forms.ScrollView) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple ScrollView](layouts-images/ScrollView.png "Exemple ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Exemple ScrollView")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) affiche le contenu avec un modèle de contrôle et constitue la classe de base de [`ContentView`](#contentView).<br /><br />[Documentation](xref:Xamarin.Forms.TemplatedView) de l’API / [Guide](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemple TemplatedView](layouts-images/TemplatedView.png "Exemple TemplatedView")](layouts-images/TemplatedView.png#lightbox "Exemple TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) est un gestionnaire de disposition pour les vues basées sur des modèles, utilisé dans une [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pour marquer l’emplacement où le contenu à présenter s’affiche.<br /><br />[Documentation](xref:Xamarin.Forms.ContentPresenter) de l’API / [Guide](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemple ContentPresenter](layouts-images/ContentPresenter.png "Exemple ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Exemple ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Dispositions avec plusieurs enfants

Ces classes dérivent de [`Layout<View>`](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) positionne les éléments enfants dans une pile, horizontalement ou verticalement, en fonction de la propriété [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . La propriété [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) régit l’espacement entre les enfants et a une valeur par défaut de 6.<br /><br />[Documentation](xref:Xamarin.Forms.StackLayout) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Exemple StackLayout](layouts-images/StackLayout.png "Exemple StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Exemple StackLayout")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grille

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) positionne ses éléments enfants dans une grille de lignes et de colonnes. La position d’un enfant est indiquée à l’aide des [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty), [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)et [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentation](xref:Xamarin.Forms.Grid) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/grid.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple de grille](layouts-images/Grid.png "Exemple de grille")](layouts-images/Grid-Large.png#lightbox "Exemple de grille")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) positionne les éléments enfants à des emplacements spécifiques par rapport à son parent. La position d’un enfant est indiquée à l’aide des [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) et [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Une `AbsoluteLayout` est utile pour animer les positions des vues.<br /><br />[Documentation](xref:Xamarin.Forms.AbsoluteLayout) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple AbsoluteLayout](layouts-images/AbsoluteLayout.png "Exemple AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Exemple AbsoluteLayout")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) positionne les éléments enfants par rapport au `RelativeLayout` lui-même ou à leurs frères. La position d’un enfant est indiquée à l’aide des [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md) qui sont définies sur des objets de type [`Constraint`](xref:Xamarin.Forms.Constraint) et [`BoundsConstraint`](xref:Xamarin.Forms.Constraint).<br /><br />[Documentation](xref:Xamarin.Forms.RelativeLayout) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemple RelativeLayout](layouts-images/RelativeLayout.png "Exemple RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Exemple RelativeLayout")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) est basé sur le [module de mise en page flexible](https://www.w3.org/TR/css-flexbox-1/)CSS, communément appelé _disposition Flex_ ou _Flex-Box_. `FlexLayout` définit six propriétés pouvant être liées et cinq propriétés pouvant être liées qui permettent d’empiler des enfants ou de les envelopper avec de nombreuses options d’alignement et d’orientation.<br /><br />[Documentation](xref:Xamarin.Forms.FlexLayout) sur l’API / [Guide](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Exemple FlexLayout](layouts-images/FlexLayout.png "Exemple FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Exemple FlexLayout")<br />code pour cette page / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Liens connexes

- [Xamarin. Forms FormsGallery, exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
