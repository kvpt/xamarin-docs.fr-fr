---
title: Classes de base de renderer et contrôles natifs
description: Chaque Xamarin.Forms contrôle a un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Cet article répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque Xamarin.Forms page, disposition, vue et cellule.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 612200a23c198cbb1127119548c0a1dcc2928645
ms.sourcegitcommit: cd0c0999b53e825b60471bfbfd4144cfcd783587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225466"
---
# <a name="renderer-base-classes-and-native-controls"></a>Classes de base de renderer et contrôles natifs

_Chaque Xamarin.Forms contrôle a un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Cet article répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque Xamarin.Forms page, disposition, vue et cellule._

À l’exception de la classe `MapRenderer`, les renderers spécifiques à la plateforme se trouvent dans les espaces de noms suivants :

- **iOS** – Xamarin.Forms . Platform. iOS
- **Android** – Xamarin.Forms . Platform. Android
- **Android (AppCompat)** : Xamarin.Forms . Platform. Android. AppCompat
- **Android (FastRenderers)**  -  Xamarin.Forms . Platform. Android. FastRenderers
- **Plateforme Windows universelle (UWP)** : Xamarin.Forms . Platform. UWP

Pour plus d’informations sur les convertisseurs rapides, consultez les [ Xamarin.Forms convertisseurs rapides](~/xamarin-forms/internals/fast-renderers.md).

La classe `MapRenderer` se trouve dans les espaces de noms suivants :

- **iOS** – Xamarin.Forms . Maps. iOS
- **Android** – Xamarin.Forms . Maps. Android
- **Plateforme Windows universelle (UWP)** : Xamarin.Forms . Maps. UWP

> [!NOTE]
> Pour plus d’informations sur la création de convertisseurs personnalisés pour les applications de Shell, consultez [ Xamarin.Forms convertisseurs personnalisés de l’interpréteur](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)de commandes.

## <a name="pages"></a>Pages

Le tableau suivant répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque type de Xamarin.Forms [page](~/xamarin-forms/user-interface/controls/pages.md) :

|Page|Renderer|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – Téléphone), TabletMasterDetailPageRenderer (iOS – Tablette), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (Téléphone), UISplitViewController (Tablette)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (Contrôle personnalisé)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS et Android), NavigationPageRenderer (Android AppCompat), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (Contrôle personnalisé)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS et Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivot)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Dispositions

Le tableau suivant répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque type de Xamarin.Forms [disposition](~/xamarin-forms/user-interface/controls/layouts.md) :

|Layout|Renderer|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|CardView|Bordure|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Affichage|Affichage|FrameworkElement|

## <a name="views"></a>Les vues

Le tableau suivant répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque type d' Xamarin.Forms [affichage](~/xamarin-forms/user-interface/controls/views.md) :

|Les vues|Renderer|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS et Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Rectangle|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Button|AppCompatButton|Button|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`CheckBox`](xref:Xamarin.Forms.CheckBox)|CheckBoxRenderer|UIButton||AppCompatCheckBox|CheckBox|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse)|EllipseRenderer|Cacouche|Affichage||Ellipse|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Image|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Button|
|[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`Line`](xref:Xamarin.Forms.Shapes.Line)|LineRenderer|Cacouche|Affichage||Courbes|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)|MKMapView|MKMapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|MediaElementRenderer|UIView||VideoView|MediaElement|
|[`Path`](xref:Xamarin.Forms.Shapes.Path)|PathRenderer|Cacouche|Affichage||Path|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`Polygon`](xref:Xamarin.Forms.Shapes.Polygon)|PolygonRenderer|Cacouche|Affichage||Polygone|
|[`Polyline`](xref:Xamarin.Forms.Shapes.Polyline)|PolylineRenderer|Cacouche|Affichage||Polyligne|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`RadioButton`](xref:Xamarin.Forms.RadioButton)|RadioButtonRenderer|UIButton||AppCompatRadioButton|RadioButton|
|[`Rectangle`](xref:Xamarin.Forms.Shapes.Rectangle)|RectangleRenderer|Cacouche|Affichage||Rectangle|
|[`RefreshView`](xref:Xamarin.Forms.RefreshView)|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Control|
|[`SwipeView`](xref:Xamarin.Forms.SwipeView)|SwipeViewRenderer|UIView||Affichage|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Commutateur|SwitchCompat|Bouton bascule|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||Sélecteur d'heure|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS), WebViewRenderer (Android et UWP)|WkWebView|WebView||WebView|

> [!NOTE]
> Le `Expander` contrôle est implémenté à l’aide d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , avec une animation. Par conséquent, il n’a aucun convertisseur de plateforme.

## <a name="cells"></a>Cellules

Le tableau suivant répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque type de Xamarin.Forms [cellule](~/xamarin-forms/user-interface/controls/cells.md) :

|Cellules|Renderer|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell avec un UITextField|LinearLayout avec un TextView et un EditText|DataTemplate avec un TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell avec un UISwitch|Commutateur|DataTemplate avec un Grid contenant un TextBlock et un ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout avec deux TextViews|DataTemplate avec un StackPanel contenant deux TextBlocks|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell avec un UIImage|LinearLayout avec deux TextViews et un ImageView|DataTemplate avec un Grid contenant un Image et deux TextBlocks|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Affichage|DataTemplate avec un ContentPresenter|

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsConvertisseurs rapides](~/xamarin-forms/internals/fast-renderers.md)
- [Xamarin.FormsConvertisseurs personnalisés de l’interpréteur de commandes](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)
