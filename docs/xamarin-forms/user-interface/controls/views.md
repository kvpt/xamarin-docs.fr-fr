---
title: Vues Xamarin. Forms
description: Les vues Xamarin. Forms sont les blocs de construction des interfaces utilisateur mobiles multiplateforme. Cet article répertorie les affichages inclus dans Xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 0094fbc73e88dc4e84d8bf415db30c17f955ddcc
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "69976601"
---
# <a name="xamarinforms-views"></a>Vues Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Les vues Xamarin. Forms sont les blocs de construction des interfaces utilisateur mobiles multiplateforme._

Les vues sont des objets d’interface utilisateur, tels que des étiquettes, des boutons et des curseurs, communément appelés *contrôles* ou *widgets* dans d’autres environnements de programmation graphiques. Les vues prises en charge par Xamarin. Forms dérivent toutes de la classe [`View`](xref:Xamarin.Forms.View) . Elles peuvent être divisées en plusieurs catégories :

## <a name="views-for-presentation"></a>Affichages pour la présentation

### <a name="label"></a>Etiquette

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) affiche des chaînes de texte sur une seule ligne ou des blocs de texte à plusieurs lignes, avec une mise en forme constante ou variable. Affectez à la propriété [`Text`](xref:Xamarin.Forms.Label.Text) une chaîne pour la mise en forme constante, ou affectez à la propriété [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) la valeur d’un objet [`FormattedString`](xref:Xamarin.Forms.FormattedString) pour la mise en forme des variables.<br /><br />[Documentation](xref:Xamarin.Forms.Label) sur l’API  / [Guide](~/xamarin-forms/user-interface/text/label.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’étiquette](views-images/Label.png "Exemple d’étiquette")](views-images/Label-Large.png#lightbox "Exemple d’étiquette")<br /> code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) affiche une image bitmap. Les bitmaps peuvent être téléchargées sur le Web, incorporées en tant que ressources dans les projets de plateforme ou de projet communs, ou créées à l’aide d’un objet .NET `Stream`.<br /><br />[Documentation](xref:Xamarin.Forms.Image) sur l’API  / [Guide](~/xamarin-forms/user-interface/images.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![Exemple d’image](views-images/Image.png "Exemple d’image")](views-images/Image-Large.png#lightbox "Exemple d’image")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) affiche un rectangle plein coloré par la propriété [`Color`](xref:Xamarin.Forms.BoxView.Color) . `BoxView` a une demande de taille par défaut de 40 x 40. Pour les autres tailles, assignez les propriétés [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) .<br /><br />[Documentation](xref:Xamarin.Forms.BoxView) sur les API  / [Guide](~/xamarin-forms/user-interface/boxview.md)  / [exemples 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)et [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Exemple BoxView](views-images/BoxView.png "Exemple BoxView")](views-images/BoxView-Large.png#lightbox "Exemple BoxView")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) affiche des pages Web ou du contenu HTML, selon que la propriété [`Source`](xref:Xamarin.Forms.WebView.Source) est définie sur un [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) ou un objet [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) .<br /><br />[Documentation](xref:Xamarin.Forms.WebView) sur l’API  / [Guide](~/xamarin-forms/user-interface/webview.md)  /  les[exemples 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Exemple WebView](views-images/WebView.png "Exemple WebView")](views-images/WebView-Large.png#lightbox "Exemple WebView")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) affiche les graphiques OpenGL dans les projets iOS et Android. La plateforme Windows universelle n’est pas prise en charge. Les projets iOS et Android requièrent une référence à l’assembly **OpenTK-1,0** ou à l’assembly **OpenTK** version 1.0.0.0. `OpenGLView` est plus facile à utiliser dans un projet partagé ; s’il est utilisé dans une bibliothèque .NET Standard, un service de dépendances est également requis (comme indiqué dans l’exemple de code).<br /><br />Il s’agit de la seule fonctionnalité graphique intégrée à Xamarin. Forms, mais une application Xamarin. Forms peut également restituer des graphiques à l’aide de [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)ou [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Exemple OpenGLView](views-images/OpenGLView.png "Exemple OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Exemple OpenGLView")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) |
|     |     |

### <a name="map"></a>Carte

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) affiche une carte. Le package NuGet **Xamarin. Forms. Maps** doit être installé. Android et plateforme Windows universelle requièrent une clé d’autorisation cartographique.<br /><br />[Documentation](xref:Xamarin.Forms.Maps.Map) sur l’API  / [Guide](~/xamarin-forms/user-interface/map.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Exemple de carte](views-images/Map.png "Exemple de carte")](views-images/Map-Large.png#lightbox "Exemple de carte")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) |
|     |     |

## <a name="views-that-initiate-commands"></a>Vues qui initient des commandes

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) est un objet rectangulaire qui affiche du texte, et qui déclenche un événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) lorsqu’il est enfoncé.<br /><br />[Documentation](xref:Xamarin.Forms.Button) sur l’API  / [Guide](~/xamarin-forms/user-interface/button.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![Exemple de bouton](views-images/Button.png "Exemple de bouton")](views-images/Button-Large.png#lightbox "Exemple de bouton")<br /> code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` est un objet rectangulaire qui affiche une image, et qui déclenche un événement `Clicked` lorsqu’il est enfoncé.<br /><br /> [Guide](~/xamarin-forms/user-interface/imagebutton.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemple ImageButton](views-images/ImageButton.png "Exemple ImageButton")](views-images/ImageButton-Large.png#lightbox "Exemple ImageButton")<br /> code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) affiche une zone permettant à l’utilisateur de taper une chaîne de texte et un bouton (ou une touche de clavier) qui indique à l’application d’effectuer une recherche. La propriété [`Text`](xref:Xamarin.Forms.SearchBar.Text) permet d’accéder au texte, tandis que l’événement [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) indique que le bouton a été enfoncé.<br /><br />[Documentation](xref:Xamarin.Forms.SearchBar) sur l’API  / [Guide](~/xamarin-forms/user-interface/searchbar.md)  / [exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SearchBar) | [![Exemple SearchBar](views-images/SearchBar.png "Exemple SearchBar")](views-images/SearchBar-Large.png#lightbox "Exemple SearchBar")<br /> code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Affichages pour les valeurs de paramètres

### <a name="checkbox"></a>Case à cocher

|     |     |
| --- | --- |
| `CheckBox` permet à l’utilisateur de sélectionner une valeur booléenne à l’aide d’un type de bouton qui peut être activé ou vide. La propriété `IsChecked` correspond à l’état de la `CheckBox`, et l’événement `CheckedChanged` est déclenché lorsque l’état change.<br /><br />Documentation/ [Guide](~/xamarin-forms/user-interface/checkbox.md) de l’API  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Exemple de case à cocher](views-images/CheckBox.png "Exemple de case à cocher")](views-images/CheckBox-Large.png#lightbox "Exemple de case à cocher")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxDemoPage.cs) |
|     |     |

### <a name="slider"></a>Curseur

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) permet à l’utilisateur de sélectionner une valeur de `double` à partir d’une plage continue spécifiée avec les propriétés [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) et [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) .<br /><br />[Documentation](xref:Xamarin.Forms.Slider) sur l’API  / [Guide](~/xamarin-forms/user-interface/slider.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![Exemple de curseur](views-images/Slider.png "Exemple de curseur")](views-images/Slider-Large.png#lightbox "Exemple de curseur")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) |
|     |     |

### <a name="stepper"></a>Exécution pas à pas

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) permet à l’utilisateur de sélectionner une valeur de `double` à partir d’une plage de valeurs incrémentielles spécifiées avec les propriétés [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum), [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)et [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />[Documentation](xref:Xamarin.Forms.Stepper) sur l’API   / [Guide](~/xamarin-forms/user-interface/stepper.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![Exemple de pas à pas](views-images/Stepper.png "Exemple de pas à pas")](views-images/Stepper-Large.png#lightbox "Exemple de pas à pas")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) |
|     |     |

### <a name="switch"></a>Basculer

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) prend la forme d’un commutateur activé/désactivé pour permettre à l’utilisateur de sélectionner une valeur booléenne. La propriété [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) correspond à l’état du commutateur, et l’événement [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) est déclenché lorsque l’état change.<br /><br />[Documentation](xref:Xamarin.Forms.Switch) sur l’API  / [Guide](~/xamarin-forms/user-interface/switch.md)  / [exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos) | [![Exemple de commutateur](views-images/Switch.png "Exemple de commutateur")](views-images/Switch-Large.png#lightbox "Exemple de commutateur")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) permet à l’utilisateur de sélectionner une date avec le sélecteur de dates de plateforme. Définissez une plage de dates autorisées avec les propriétés [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) et [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) . La propriété [`Date`](xref:Xamarin.Forms.DatePicker.Date) correspond à la date sélectionnée et l’événement [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) est déclenché lorsque cette propriété change.<br /><br />[Documentation](xref:Xamarin.Forms.DatePicker) sur l’API  / [Guide](~/xamarin-forms/user-interface/datepicker.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![Exemple de DatePicker](views-images/DatePicker.png "Exemple de DatePicker")](views-images/DatePicker-Large.png#lightbox "Exemple de DatePicker")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) |
|     |     |

### <a name="timepicker"></a>Sélecteur d'heure

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) permet à l’utilisateur de sélectionner une heure avec le sélecteur d’heure de la plateforme. La propriété [`Time`](xref:Xamarin.Forms.TimePicker.Time) est l’heure sélectionnée. Une application peut surveiller les modifications apportées à la propriété `Time` en installant un gestionnaire pour l’événement [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) .<br /><br />[Documentation](xref:Xamarin.Forms.TimePicker) sur l’API  / [Guide](~/xamarin-forms/user-interface/timepicker.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![Exemple TimePicker](views-images/TimePicker.png "Exemple TimePicker")](views-images/TimePicker-Large.png#lightbox "Exemple TimePicker")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) |
|     |     |

## <a name="views-for-editing-text"></a>Affichages pour la modification de texte

Ces deux classes dérivent de la classe [`InputView`](xref:Xamarin.Forms.InputView) , qui définit la propriété [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) .

<a name="entry" />

### <a name="entry"></a>Entrée

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) permet à l’utilisateur d’entrer et de modifier une seule ligne de texte. Le texte est disponible en tant que propriété [`Text`](xref:Xamarin.Forms.Entry.Text) , et les événements [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) et [`Completed`](xref:Xamarin.Forms.Entry.Completed) sont déclenchés lorsque le texte change ou lorsque l’utilisateur signale la saisie semi-automatique en appuyant sur la touche entrée.<br /><br />Utilisez une [`Editor`](#editor) pour la saisie et la modification de plusieurs lignes de texte.<br /><br />[Documentation](xref:Xamarin.Forms.Entry) sur l’API  / [Guide](~/xamarin-forms/user-interface/text/entry.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’entrée](views-images/Entry.png "Exemple d’entrée")](views-images/Entry-Large.png#lightbox "Exemple d’entrée")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) |
|     |     |

<a name="editor" />

### <a name="editor"></a>Éditeur

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) permet à l’utilisateur d’entrer et de modifier plusieurs lignes de texte. Le texte est disponible en tant que propriété [`Text`](xref:Xamarin.Forms.Editor.Text) , et les événements [`TextChanged`](xref:Xamarin.Forms.Editor.TextChanged) et [`Completed`](xref:Xamarin.Forms.Editor.Completed) sont déclenchés lorsque le texte change ou lorsque l’utilisateur signale la fin de l’opération.<br /><br />Utilisez une vue de [`Entry`](#entry) pour la saisie et la modification d’une seule ligne de texte.<br /><br />[Documentation](xref:Xamarin.Forms.Editor) sur l’API  / [Guide](~/xamarin-forms/user-interface/text/editor.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’entrée](views-images/Editor.png "Exemple d’éditeur")](views-images/Editor-Large.png#lightbox "Exemple d’éditeur")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) |
|     |     |

## <a name="views-to-indicate-activity"></a>Affichages pour indiquer l’activité

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) utilise une animation pour montrer que l’application est engagée dans une activité de longue durée sans donner aucune indication de la progression. La propriété [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) contrôle l’animation.<br /><br />Si la progression de l’activité est connue, utilisez plutôt une [`ProgressBar`](#progressbar) .<br /><br />[Documentation](xref:Xamarin.Forms.ActivityIndicator) sur l’API  / [Guide](~/xamarin-forms/user-interface/activityindicator.md)  / [exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos) | [![Exemple ActivityIndicator](views-images/ActivityIndicator.png "Exemple ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Exemple ActivityIndicator")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>Barre de progression

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) utilise une animation pour montrer que l’application progresse dans une activité longue. Affectez à la propriété [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) la valeur comprise entre 0 et 1 pour indiquer la progression.<br /><br />Si la progression de l’activité n’est pas connue, utilisez plutôt une [`ActivityIndicator`](#activityindicator) .<br /><br />[Documentation](xref:Xamarin.Forms.ProgressBar) sur l’API  / [Guide](~/xamarin-forms/user-interface/progressbar.md)  / [exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos) | [![Exemple de ProgressBar](views-images/ProgressBar.png "Exemple de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Exemple de ProgressBar")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Vues qui affichent des collections

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche une liste déroulante d’éléments de données sélectionnables, à l’aide de différentes spécifications de disposition. Il vise à fournir une alternative plus flexible et plus performante pour [`ListView`](xref:Xamarin.Forms.ListView). Affectez à la propriété `ItemsSource` la valeur d’une collection d’objets et affectez à la propriété `ItemTemplate` la valeur d’un objet [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrivant la manière dont les éléments doivent être mis en forme. L’événement `SelectionChanged` signale qu’une sélection a été effectuée, qui est disponible en tant que propriété `SelectedItem`.<br /><br />[Guide](~/xamarin-forms/user-interface/collectionview/index.md)  / [exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/) | [![Exemple CollectionView](views-images/CollectionView.png "Exemple CollectionView")](views-images/CollectionView-Large.png#lightbox "Exemple CollectionView")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>Affichage de liste

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) dérive de [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) et affiche une liste déroulante d’éléments de données sélectionnables. Affectez à la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) la valeur d’une collection d’objets et affectez à la propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) la valeur d’un objet [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrivant la manière dont les éléments doivent être mis en forme. L’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) signale qu’une sélection a été effectuée, qui est disponible en tant que propriété [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) .<br /><br />[Documentation](xref:Xamarin.Forms.ListView) sur l’API  / [Guide](~/xamarin-forms/user-interface/listview/index.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![Exemple de ListView](views-images/ListView.png "Exemple de ListView")](views-images/ListView-Large.png#lightbox "Exemple de ListView")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) |
|     |     |

### <a name="picker"></a>Sélecteur

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) affiche un élément sélectionné dans une liste de chaînes de texte et permet de sélectionner cet élément lorsque la vue est frappée. Définissez la propriété [`Items`](xref:Xamarin.Forms.Picker.Items) sur une liste de chaînes ou la propriété [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) sur une collection d’objets. L’événement [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) est déclenché lorsqu’un élément est sélectionné.<br /><br />La `Picker` affiche la liste des éléments uniquement lorsqu’elle est sélectionnée. Utilisez une [`ListView`](#listView) ou [`TableView`](#tableView) pour une liste déroulante qui reste sur la page.<br /><br />[Documentation](xref:Xamarin.Forms.Picker) sur l’API  / [Guide](~/xamarin-forms/user-interface/picker/index.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![Exemple de sélecteur](views-images/Picker.png "Exemple de sélecteur")](views-images/Picker-Large.png#lightbox "Exemple de sélecteur")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) affiche une liste de lignes de type [`Cell`](xref:Xamarin.Forms.Cell) avec des en-têtes et des sous-en-têtes facultatifs. Affectez à la propriété [`Root`](xref:Xamarin.Forms.TableView.Root) un objet de type [`TableRoot`](xref:Xamarin.Forms.TableRoot)et ajoutez des objets [`TableSection`](xref:Xamarin.Forms.TableSection) à cette `TableRoot`. Chaque `TableSection` est une collection d’objets `Cell`.<br /><br />[Documentation](xref:Xamarin.Forms.TableView) sur l’API  / [Guide](~/xamarin-forms/user-interface/tableview.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![Exemple TableView](views-images/TableView.png "Exemple TableView")](views-images/TableView-Large.png#lightbox "Exemple TableView")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Liens associés

- [Xamarin. Forms FormsGallery, exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
