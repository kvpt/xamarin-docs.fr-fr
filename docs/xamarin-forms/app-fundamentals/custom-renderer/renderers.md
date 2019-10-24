---
title: Classes de base de renderer et contrôles natifs
description: Chaque contrôle Xamarin.Forms est accompagné d’un convertisseur pour chaque plateforme qui crée une instance de contrôle natif. Cet article liste les classes de renderer et de contrôle natif qui implémentent chaque page, disposition, vue et cellule Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/11/2019
ms.openlocfilehash: cf9c5d7aa018a6d12a6c4788dc1e8114088d056c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697134"
---
# <a name="renderer-base-classes-and-native-controls"></a>Classes de base de renderer et contrôles natifs

_Chaque contrôle Xamarin. Forms a un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Cet article répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque page Xamarin. Forms, la disposition, la vue et la cellule._

À l’exception de la classe `MapRenderer`, les renderers spécifiques à la plateforme se trouvent dans les espaces de noms suivants :

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **Plateforme Windows universelle (UWP)** – Xamarin.Forms.Platform.UWP

La classe `MapRenderer` se trouve dans les espaces de noms suivants :

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Plateforme Windows universelle (UWP)** – Xamarin.Forms.Maps.UWP

> [!NOTE]
> Pour plus d’informations sur la création de renderers personnalisés pour des applications Shell, consultez [Renderers Xamarin.Forms Shell personnalisés](~/xamarin-forms/app-fundamentals/shell/customrenderers.md).

## <a name="pages"></a>Pages

Le tableau suivant liste les classes de renderer et de contrôle natif qui implémentent chaque type de [page](~/xamarin-forms/user-interface/controls/pages.md) Xamarin.Forms :

|Page|Renderer|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – Téléphone), TabletMasterDetailPageRenderer (iOS – Tablette), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (Téléphone), UISplitViewController (Tablette)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (Contrôle personnalisé)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS et Android), NavigationPageRenderer (Android AppCompat), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (Contrôle personnalisé)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS et Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivot)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Dispositions

Le tableau suivant liste les classes de renderer et de contrôle natif qui implémentent chaque type de [disposition](~/xamarin-forms/user-interface/controls/layouts.md) Xamarin.Forms :

|Mise en page|Renderer|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Vue|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Vue|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Vue|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|Bordure|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Vue|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Vue|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Vue|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Vue|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Vue|FrameworkElement|

## <a name="views"></a>Affichages

Le tableau suivant liste les classes de renderer et de contrôle natif qui implémentent chaque type de [vue](~/xamarin-forms/user-interface/controls/views.md) Xamarin.Forms :

|Affichages|Renderer|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|Barre de progression||Barre de progression|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS et Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Rectangle|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Button|AppCompatButton|Button|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|`CheckBox`|CheckBoxRenderer|UIButton||AppCompatCheckBox|Case à cocher|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Image|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Button|
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|Affichage de liste||Affichage de liste|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MKMapView||MapControl|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|Barre de progression||Barre de progression|
|`RefreshView`|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Curseur|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Contrôle|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Basculer|SwitchCompat|Bouton bascule|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|Affichage de liste||Affichage de liste|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||Sélecteur d'heure|
|[`WebView`](xref:Xamarin.Forms.WebView)|WebViewRenderer|UIWebView|WebView||WebView|

## <a name="cells"></a>Cellules

Le tableau suivant liste les classes de renderer et de contrôle natif qui implémentent chaque type de [cellule](~/xamarin-forms/user-interface/controls/cells.md) Xamarin.Forms :

|Cellules|Renderer|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell avec un UITextField|LinearLayout avec un TextView et un EditText|DataTemplate avec un TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell avec un UISwitch|Basculer|DataTemplate avec un Grid contenant un TextBlock et un ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout avec deux TextViews|DataTemplate avec un StackPanel contenant deux TextBlocks|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell avec un UIImage|LinearLayout avec deux TextViews et un ImageView|DataTemplate avec un Grid contenant un Image et deux TextBlocks|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Vue|DataTemplate avec un ContentPresenter|

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons listé les classes de renderer et de contrôle natif qui implémentent chaque page, disposition, vue et cellule Xamarin.Forms. Chaque contrôle Xamarin.Forms est accompagné d’un convertisseur pour chaque plateforme qui crée une instance de contrôle natif.
