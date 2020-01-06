---
title: Vues Xamarin.Forms
description: Vues Xamarin.Forms sont les blocs de construction d’interfaces utilisateur mobiles multiplateformes. Cet article répertorie les vues qui sont inclus dans Xamarin.Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2019
ms.openlocfilehash: f9a83bb2ce924265e8d493064087c4e1446ab892
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489927"
---
# <a name="xamarinforms-views"></a>Vues Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Vues Xamarin.Forms sont les blocs de construction d’interfaces utilisateur mobiles multiplateformes._

Les vues sont des objets d’interface utilisateur tels que des étiquettes, des boutons et des curseurs qui sont communément appelés *contrôles* ou *widgets* dans d’autres environnements de programmation graphiques. Les vues prises en charge par Xamarin.Forms dérivent toutes de la [ `View` ](xref:Xamarin.Forms.View) classe. Il peuvent être divisés en plusieurs catégories :

## <a name="views-for-presentation"></a>Affichages de présentation

### <a name="label"></a>Ajouter des contrôles

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) Affiche les chaînes de texte à ligne unique ou de blocs à plusieurs lignes de texte, soit avec mise en forme de constante ou variable. Définir le [ `Text` ](xref:Xamarin.Forms.Label.Text) en une chaîne pour une constante, ou ensemble la [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propriété à un [ `FormattedString` ](xref:Xamarin.Forms.FormattedString) objet de variable mise en forme.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Label) / [Guide](~/xamarin-forms/user-interface/text/label.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’étiquette](views-images/Label.png "Exemple d’étiquette")](views-images/Label-Large.png#lightbox "Exemple d’étiquette")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) affiche une image bitmap. Les images bitmap peuvent être téléchargées sur le Web, incorporés comme ressources dans l’ou les projets de plateforme commune ou créé à l’aide de .NET `Stream` objet.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Image) / [Guide](~/xamarin-forms/user-interface/images.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![Exemple d’image](views-images/Image.png "Exemple d’image")](views-images/Image-Large.png#lightbox "Exemple d’image")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) affiche un rectangle plein coloré par le [ `Color` ](xref:Xamarin.Forms.BoxView.Color) propriété. `BoxView` a une demande de taille par défaut de 40 x 40. Pour les autres tailles, affecter le [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) et [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriétés.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.BoxView) / [Guide](~/xamarin-forms/user-interface/boxview.md) / [exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4 ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock), et [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Exemple BoxView](views-images/BoxView.png "Exemple BoxView")](views-images/BoxView-Large.png#lightbox "Exemple BoxView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) Affiche les pages Web ou du contenu, HTML, selon que le [ `Source` ](xref:Xamarin.Forms.WebView.Source) propriété est définie sur une [ `UriWebViewSource` ](xref:Xamarin.Forms.UrlWebViewSource) ou un [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource) objet.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.WebView) / [Guide](~/xamarin-forms/user-interface/webview.md) / [exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Exemple WebView](views-images/WebView.png "Exemple WebView")](views-images/WebView-Large.png#lightbox "Exemple WebView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) Affiche des graphiques OpenGL dans les projets iOS et Android. Il n’existe aucune prise en charge pour la plateforme Windows universelle. Les projets iOS et Android requièrent une référence à la **OpenTK-1.0** assembly ou le **OpenTK** assembly version 1.0.0.0. `OpenGLView` est plus facile à utiliser dans un projet partagé. Si utilisé dans une bibliothèque .NET Standard, puis un Service de dépendance sera également requis (comme indiqué dans l’exemple de code).<br /><br />Il s’agit de la seule fonctionnalité graphique intégrée à Xamarin. Forms, mais une application Xamarin. Forms peut également restituer des graphiques à l’aide de [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)ou [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Exemple OpenGLView](views-images/OpenGLView.png "Exemple OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Exemple OpenGLView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>Carte

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) affiche une carte. Le **Xamarin.Forms.Maps** package Nuget doit être installé. Android et de la plateforme Windows universelle nécessitent une clé d’autorisation de carte.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Maps.Map) / [Guide](~/xamarin-forms/user-interface/map/index.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Exemple de carte](views-images/Map.png "Exemple de carte")](views-images/Map-Large.png#lightbox "Exemple de carte")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Vues qui lancer des commandes

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) est un objet rectangulaire qui affiche du texte, et qui déclenche un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) événement été dessus.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Button) / [Guide](~/xamarin-forms/user-interface/button.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![Exemple de bouton](views-images/Button.png "Exemple de bouton")](views-images/Button-Large.png#lightbox "Exemple de bouton")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` est l’objet rectangulaire qui affiche une image, et qui déclenche un `Clicked` événement été dessus.<br /><br /> [Guide](~/xamarin-forms/user-interface/imagebutton.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemple ImageButton](views-images/ImageButton.png "Exemple ImageButton")](views-images/ImageButton-Large.png#lightbox "Exemple ImageButton")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView` est un contrôle conteneur qui fournit des fonctionnalités d’extraction pour le contenu défilant. La `ICommand` définie par la propriété `Command` est exécutée lorsqu’une actualisation est déclenchée, et la propriété `IsRefreshing` indique l’état actuel du contrôle.<br /><br /> [Guide](~/xamarin-forms/user-interface/refreshview.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemple RefreshView](views-images/RefreshView.png "Exemple RefreshView")](views-images/RefreshView-Large.png#lightbox "Exemple RefreshView")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) affiche une zone pour l’utilisateur à saisir une chaîne de texte et un bouton (ou une touche du clavier) qui signale l’application pour effectuer une recherche. Le [ `Text` ](xref:Xamarin.Forms.SearchBar.Text) propriété fournit l’accès au texte et le [ `SearchButtonPressed` ](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) événement indique que le bouton a été enfoncé.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.SearchBar) / [Guide](~/xamarin-forms/user-interface/searchbar.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![Exemple SearchBar](views-images/SearchBar.png "Exemple SearchBar")](views-images/SearchBar-Large.png#lightbox "Exemple SearchBar")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView` est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel qui sont révélés par un mouvement de balayage. Chaque élément de menu est représenté par un `SwipeItem`, qui a une propriété `Command` qui exécute un `ICommand` lorsque l’élément est frappé.<br /><br /> [Guide](~/xamarin-forms/user-interface/swipeview.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemple SwipeView](views-images/SwipeView.png "Exemple SwipeView")](views-images/SwipeView-Large.png#lightbox "Exemple SwipeView")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Vues de définition des valeurs

### <a name="checkbox"></a>Case à cocher

|     |     |
| --- | --- |
| `CheckBox` permet à l’utilisateur de sélectionner une valeur booléenne à l’aide d’un type de bouton qui peut être activé ou vide. La propriété `IsChecked` correspond à l’état de la `CheckBox`, et l’événement `CheckedChanged` est déclenché lorsque l’état change.<br /><br />Documentation/ [Guide](~/xamarin-forms/user-interface/checkbox.md) de l’API / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Exemple de case à cocher](views-images/CheckBox.png "Exemple de case à cocher")](views-images/CheckBox-Large.png#lightbox "Exemple de case à cocher")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxDemoPage.xaml) |
|     |     |

### <a name="slider"></a>Curseur

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) permet à l’utilisateur à sélectionner un `double` valeur à partir d’une plage continue spécifiée avec le [ `Minimum` ](xref:Xamarin.Forms.Slider.Minimum) et [ `Maximum` ](xref:Xamarin.Forms.Slider.Maximum) propriétés.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Slider) / [Guide](~/xamarin-forms/user-interface/slider.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![Exemple de curseur](views-images/Slider.png "Exemple de curseur")](views-images/Slider-Large.png#lightbox "Exemple de curseur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Exécution pas à pas

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) permet à l’utilisateur à sélectionner un `double` valeur à partir d’une plage de valeurs incrémentielles spécifié avec le [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum), [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), et [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) propriétés.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Stepper)  / [Guide](~/xamarin-forms/user-interface/stepper.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![Exemple de pas à pas](views-images/Stepper.png "Exemple de pas à pas")](views-images/Stepper-Large.png#lightbox "Exemple de pas à pas")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Basculer

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) prend la forme d’un commutateur activé/désactivé pour autoriser l’utilisateur à sélectionner une valeur booléenne. Le [ `IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) propriété est l’état du commutateur et le [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) événement est déclenché lorsque l’état change.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Switch) / [Guide](~/xamarin-forms/user-interface/switch.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![Exemple de commutateur](views-images/Switch.png "Exemple de commutateur")](views-images/Switch-Large.png#lightbox "Exemple de commutateur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) permet à l’utilisateur de sélectionner une date avec le sélecteur de dates de plateforme. Définir une plage de dates autorisées avec la [ `MinimumDate` ](xref:Xamarin.Forms.DatePicker.MinimumDate) et [ `MaximumDate` ](xref:Xamarin.Forms.DatePicker.MaximumDate) propriétés. Le [ `Date` ](xref:Xamarin.Forms.DatePicker.Date) propriété est la date sélectionnée et le [ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected) événement est déclenché lorsque cette propriété est modifiée.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.DatePicker) / [Guide](~/xamarin-forms/user-interface/datepicker.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![Exemple de DatePicker](views-images/DatePicker.png "Exemple de DatePicker")](views-images/DatePicker-Large.png#lightbox "Exemple de DatePicker")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>Sélecteur d'heure

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) permet à l’utilisateur à sélectionner une fois avec le sélecteur d’heure de plateforme. Le [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriété est l’heure sélectionnée. Une application peut surveiller les modifications dans le `Time` propriété en installant un gestionnaire pour le [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.TimePicker) / [Guide](~/xamarin-forms/user-interface/timepicker.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![Exemple TimePicker](views-images/TimePicker.png "Exemple TimePicker")](views-images/TimePicker-Large.png#lightbox "Exemple TimePicker")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Vues de modification de texte

Ces deux classes dérivent les [ `InputView` ](xref:Xamarin.Forms.InputView) (classe), qui définit le [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propriété.

### <a name="entry"></a>Entrée

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) permet à l’utilisateur à entrer et de modifier une seule ligne de texte. Le texte est disponible en tant que le [ `Text` ](xref:Xamarin.Forms.Entry.Text) propriété et le [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) et [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) les événements sont déclenchés lorsque les modifications de texte ou de l’utilisateur signale la fin en appuyant sur la touche ENTRÉE.<br /><br />Utilisez un [ `Editor` ](#editor) pour entrer et de modifier plusieurs lignes de texte.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Entry) / [Guide](~/xamarin-forms/user-interface/text/entry.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’entrée](views-images/Entry.png "Exemple d’entrée")](views-images/Entry-Large.png#lightbox "Exemple d’entrée")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Éditeur

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) permet à l’utilisateur à entrer et de modifier plusieurs lignes de texte. Le texte est disponible en tant que le [ `Text` ](xref:Xamarin.Forms.Editor.Text) propriété et le [ `TextChanged` ](xref:Xamarin.Forms.Editor.TextChanged) et [ `Completed` ](xref:Xamarin.Forms.Editor.Completed) les événements sont déclenchés lorsque les modifications de texte ou de l’utilisateur signale la fin.<br /><br />Utilisez un [ `Entry` ](#entry) vue pour entrer et de modifier une seule ligne de texte.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Editor) / [Guide](~/xamarin-forms/user-interface/text/editor.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’entrée](views-images/Editor.png "Exemple d’éditeur")](views-images/Editor-Large.png#lightbox "Exemple d’éditeur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Vues pour indiquer l’activité

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) utilise une animation pour montrer que l’application est engagée dans une activité de longue durée sans perdre aucune indication de progression. Le [ `IsRunning` ](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propriété détermine l’animation.<br /><br />Si l’état d’avancement de l’activité est connu, utilisez un [ `ProgressBar` ](#progressbar) à la place.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ActivityIndicator) / [Guide](~/xamarin-forms/user-interface/activityindicator.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![Exemple ActivityIndicator](views-images/ActivityIndicator.png "Exemple ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Exemple ActivityIndicator")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) utilise une animation pour montrer que l’application est mise en œuvre une activité de longue durée. Définir le [ `Progress` ](xref:Xamarin.Forms.ProgressBar.Progress) propriété pour les valeurs comprises entre 0 et 1 pour indiquer la progression.<br /><br />Si l’état d’avancement de l’activité n’est pas connu, utilisez un [ `ActivityIndicator` ](#activityindicator) à la place.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ProgressBar) / [Guide](~/xamarin-forms/user-interface/progressbar.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![Exemple de ProgressBar](views-images/ProgressBar.png "Exemple de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Exemple de ProgressBar")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Vues qui affichent des collections

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche une liste déroulante d’éléments de données. Affectez à la propriété `ItemsSource` la valeur d’une collection d’objets et affectez à la propriété `ItemTemplate` la valeur d’un objet [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrivant la manière dont les éléments doivent être mis en forme. L’événement `CurrentItemChanged` signale que l’élément actuellement affiché a changé, ce qui est disponible en tant que propriété `CurrentItem`.<br /><br />[Guide](~/xamarin-forms/user-interface/carouselview/index.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) | [![Exemple CarouselView](views-images/CarouselView.png "Exemple CarouselView")](views-images/CarouselView-Large.png#lightbox "Exemple CarouselView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche une liste déroulante d’éléments de données sélectionnables, à l’aide de différentes spécifications de disposition. Il vise à fournir une alternative plus flexible et plus performante pour [`ListView`](xref:Xamarin.Forms.ListView). Affectez à la propriété `ItemsSource` la valeur d’une collection d’objets et affectez à la propriété `ItemTemplate` la valeur d’un objet [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrivant la manière dont les éléments doivent être mis en forme. L’événement `SelectionChanged` signale qu’une sélection a été effectuée, qui est disponible en tant que propriété `SelectedItem`.<br /><br />[Guide](~/xamarin-forms/user-interface/collectionview/index.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) | [![Exemple CollectionView](views-images/CollectionView.png "Exemple CollectionView")](views-images/CollectionView-Large.png#lightbox "Exemple CollectionView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView` affiche des indicateurs qui représentent le nombre d’éléments d’une `CarouselView`. Affectez à la propriété `ItemsSourceBy` la `CarouselView` objet pour lequel afficher les indicateurs. <br /><br />[Guide](~/xamarin-forms/user-interface/indicatorview.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) | [![Exemple IndicatorView](views-images/IndicatorView.png "Exemple IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Exemple IndicatorView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>Affichage de liste

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) dérive de [ `ItemsView` ](xref:Xamarin.Forms.ItemsView`1) et affiche une liste déroulante d’éléments de données sélectionnables. Définir le [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété à une collection d’objets et définissez le [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriété un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) objet décrivant la façon dont les éléments sont à mettre en forme. Le [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) événement signale qu’une sélection a été effectuée, qui est disponible en tant que le [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriété.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ListView) / [Guide](~/xamarin-forms/user-interface/listview/index.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![Exemple de ListView](views-images/ListView.png "Exemple de ListView")](views-images/ListView-Large.png#lightbox "Exemple de ListView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Sélecteur

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) affiche un élément sélectionné dans une liste de chaînes de texte et permet de sélectionner cet élément lors de l’appui sur la vue. Définir le [ `Items` ](xref:Xamarin.Forms.Picker.Items) propriété à une liste de chaînes, ou le [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriété à une collection d’objets. Le [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement est déclenché lorsqu’un élément est sélectionné.<br /><br />Le `Picker` affiche la liste des éléments uniquement lorsqu’il est sélectionné. Utilisez un [ `ListView` ](#listview) ou [ `TableView` ](#tableview) pour une liste déroulante qui reste sur la page.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.Picker) / [Guide](~/xamarin-forms/user-interface/picker/index.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![Exemple de sélecteur](views-images/Picker.png "Exemple de sélecteur")](views-images/Picker-Large.png#lightbox "Exemple de sélecteur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) affiche une liste de lignes de type [ `Cell` ](xref:Xamarin.Forms.Cell) avec en-têtes facultatifs et les sous-en-têtes. Définir le [ `Root` ](xref:Xamarin.Forms.TableView.Root) propriété à un objet de type [ `TableRoot` ](xref:Xamarin.Forms.TableRoot)et ajoutez [ `TableSection` ](xref:Xamarin.Forms.TableSection) objets à celle `TableRoot`. Chaque `TableSection` est une collection de `Cell` objets.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.TableView) / [Guide](~/xamarin-forms/user-interface/tableview.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![Exemple TableView](views-images/TableView.png "Exemple TableView")](views-images/TableView-Large.png#lightbox "Exemple TableView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Liens associés

- [Exemple de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
