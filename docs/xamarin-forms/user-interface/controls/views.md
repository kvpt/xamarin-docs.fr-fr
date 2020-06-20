---
title: Xamarin.FormsAffichage
description: Xamarin.Formsles vues sont les blocs de construction des interfaces utilisateur mobiles multiplateformes. Cet article répertorie les affichages inclus dans Xamarin.Forms .
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c15626e405645d28a785c32d276860f9751ea25
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132377"
---
# <a name="xamarinforms-views"></a>Xamarin.FormsAffichage

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Les vues Xamarin. Forms sont les blocs de construction des interfaces utilisateur mobiles multiplateforme._

Les vues sont des objets d’interface utilisateur, tels que des étiquettes, des boutons et des curseurs, communément appelés *contrôles* ou *widgets* dans d’autres environnements de programmation graphiques. Les vues prises en charge par sont Xamarin.Forms toutes dérivées de la [`View`](xref:Xamarin.Forms.View) classe. Elles peuvent être divisées en plusieurs catégories :

## <a name="views-for-presentation"></a>Affichages pour la présentation

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)affiche un rectangle plein coloré par la [`Color`](xref:Xamarin.Forms.BoxView.Color) propriété. `BoxView`a une demande de taille par défaut de 40 x 40. Pour les autres tailles, assignez les [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Propriétés et.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.BoxView)  /  [Guide](~/xamarin-forms/user-interface/boxview.md)  /  [Exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)et [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Exemple BoxView](views-images/BoxView.png "Exemple BoxView")](views-images/BoxView-Large.png#lightbox "Exemple BoxView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="expander"></a>Expander

|     |     |
| --- | --- |
| `Expander`fournit un conteneur extensible pour héberger tout contenu, et est constitué d’un en-tête et d’un contenu. Affectez `Header` à la propriété une valeur [`View`](xref:Xamarin.Forms.View) qui sera affichée comme en-tête et la `Content` propriété à un [`View`](xref:Xamarin.Forms.View) qui s’affichera lorsque l’en-tête sera développé par un TAP.<br /><br />[Guide](~/xamarin-forms/user-interface/expander.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos) | [![Exemple d’Expander](views-images/Expander.png "Exemple d’Expander")](views-images/Expander-Large.png#lightbox "Exemple d’Expander")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ExpanderDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ExpanderDemoPage.xaml) |
|     |     |

### <a name="label"></a>Étiquette

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)affiche des chaînes de texte sur une seule ligne ou des blocs de texte à plusieurs lignes, avec une mise en forme constante ou variable. Affectez [`Text`](xref:Xamarin.Forms.Label.Text) à la propriété une chaîne pour la mise en forme constante, ou affectez [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) à la propriété un [`FormattedString`](xref:Xamarin.Forms.FormattedString) objet pour la mise en forme des variables.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Label)  /  [Guide](~/xamarin-forms/user-interface/text/label.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’étiquette](views-images/Label.png "Exemple d’étiquette")](views-images/Label-Large.png#lightbox "Exemple d’étiquette")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)affiche une image bitmap. Les bitmaps peuvent être téléchargées sur le Web, incorporées en tant que ressources dans les projets de plateforme ou de projet communs, ou créées à l’aide d’un `Stream` objet .net.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Image)  /  [Guide](~/xamarin-forms/user-interface/images.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![Exemple d’image](views-images/Image.png "Exemple d’image")](views-images/Image-Large.png#lightbox "Exemple d’image")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="map"></a>Mappage

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)affiche une carte. ** Xamarin.Forms . **Le package NuGet Maps doit être installé. Android et plateforme Windows universelle requièrent une clé d’autorisation cartographique.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Maps.Map)  /  [Guide](~/xamarin-forms/user-interface/map/index.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Exemple de carte](views-images/Map.png "Exemple de carte")](views-images/Map-Large.png#lightbox "Exemple de carte")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement)lit la vidéo ou l’audio. Les médias peuvent être lus à partir d’une URL ou d’un fichier local, selon que la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriété est définie sur [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) ou [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.MediaElement)  /  [Guide](~/xamarin-forms/user-interface/mediaelement.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) | [![Exemple MediaElement](views-images/MediaElement.png "Exemple MediaElement")](views-images/MediaElement-Large.png#lightbox "Exemple MediaElement")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)affiche des graphiques OpenGL dans des projets iOS et Android. La plateforme Windows universelle n’est pas prise en charge. Les projets iOS et Android requièrent une référence à l’assembly **OpenTK-1,0** ou à l’assembly **OpenTK** version 1.0.0.0. `OpenGLView`est plus facile à utiliser dans un projet partagé ; s’il est utilisé dans une bibliothèque .NET Standard, un service de dépendances est également requis (comme indiqué dans l’exemple de code).<br /><br />Il s’agit de la seule fonctionnalité graphique intégrée à Xamarin.Forms , mais une Xamarin.Forms application peut également restituer des graphiques à l’aide [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) de, ou [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md) .<br /><br />[Documentation sur les API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Exemple OpenGLView](views-images/OpenGLView.png "Exemple OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Exemple OpenGLView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)affiche des pages Web ou du contenu HTML, selon que la [`Source`](xref:Xamarin.Forms.WebView.Source) propriété a la valeur [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) ou un [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) objet.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.WebView)  /  [Guide](~/xamarin-forms/user-interface/webview.md)  /  [Exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Exemple WebView](views-images/WebView.png "Exemple WebView")](views-images/WebView-Large.png#lightbox "Exemple WebView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Affichages lançant des commandes

### <a name="button"></a>Bouton

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)est un objet rectangulaire qui affiche du texte, et qui déclenche un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) événement lorsqu’il a été enfoncé.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Button)  /  [Guide](~/xamarin-forms/user-interface/button.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![Exemple de bouton](views-images/Button.png "Exemple de bouton")](views-images/Button-Large.png#lightbox "Exemple de bouton")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton`est un objet rectangulaire qui affiche une image et qui déclenche un `Clicked` événement lorsqu’il a été enfoncé.<br /><br /> [Guide](~/xamarin-forms/user-interface/imagebutton.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemple ImageButton](views-images/ImageButton.png "Exemple ImageButton")](views-images/ImageButton-Large.png#lightbox "Exemple ImageButton")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="radiobutton"></a>RadioButton

|     |     |
| --- | --- |
| `RadioButton`permet la sélection d’une option dans un jeu et déclenche un `CheckedChanged` événement lorsque la sélection se produit.<br /><br />[Guide](~/xamarin-forms/user-interface/radiobutton.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/) | [![Exemple de RadioButton](views-images/RadioButton.png "Exemple de RadioButton")](views-images/RadioButton-Large.png#lightbox "Exemple de RadioButton")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RadioButtonDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView`est un contrôle conteneur qui fournit des fonctionnalités d’extraction pour le contenu défilant. Le `ICommand` défini par la `Command` propriété est exécuté lorsqu’une actualisation est déclenchée, et la `IsRefreshing` propriété indique l’état actuel du contrôle.<br /><br /> [Guide](~/xamarin-forms/user-interface/refreshview.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemple RefreshView](views-images/RefreshView.png "Exemple RefreshView")](views-images/RefreshView-Large.png#lightbox "Exemple RefreshView")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)affiche une zone permettant à l’utilisateur de taper une chaîne de texte et un bouton (ou une touche de clavier) qui indique à l’application d’effectuer une recherche. La [`Text`](xref:Xamarin.Forms.InputView.Text) propriété permet d’accéder au texte, et l' [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) événement indique que le bouton a été enfoncé.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.SearchBar)  /  [Guide](~/xamarin-forms/user-interface/searchbar.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![Exemple SearchBar](views-images/SearchBar.png "Exemple SearchBar")](views-images/SearchBar-Large.png#lightbox "Exemple SearchBar")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView`est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel qui sont révélés par un mouvement de balayage. Chaque élément de menu est représenté par un `SwipeItem` , qui a une `Command` propriété qui exécute une `ICommand` lorsque l’élément est frappé.<br /><br /> [Guide](~/xamarin-forms/user-interface/swipeview.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemple SwipeView](views-images/SwipeView.png "Exemple SwipeView")](views-images/SwipeView-Large.png#lightbox "Exemple SwipeView")<br /> [Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Affichages pour les valeurs des paramètres

### <a name="checkbox"></a>Case à cocher

|     |     |
| --- | --- |
| `CheckBox`permet à l’utilisateur de sélectionner une valeur booléenne à l’aide d’un type de bouton qui peut être activé ou vide. La `IsChecked` propriété est l’état de la `CheckBox` , et l' `CheckedChanged` événement est déclenché lorsque l’état change.<br /><br />Exemple de documentation/ [Guide](~/xamarin-forms/user-interface/checkbox.md)d’API  /  [Sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Exemple de case à cocher](views-images/CheckBox.png "Exemple de case à cocher")](views-images/CheckBox-Large.png#lightbox "Exemple de case à cocher")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) |
|     |     |

### <a name="slider"></a>Curseur

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)permet à l’utilisateur de sélectionner une `double` valeur dans une plage continue spécifiée avec [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) les [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) Propriétés et.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Slider)  /  [Guide](~/xamarin-forms/user-interface/slider.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![Exemple de curseur](views-images/Slider.png "Exemple de curseur")](views-images/Slider-Large.png#lightbox "Exemple de curseur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Exécution pas à pas

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)permet à l’utilisateur de sélectionner une `double` valeur dans une plage de valeurs incrémentielles spécifiées avec les [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) Propriétés, et [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Stepper)   /  [Guide](~/xamarin-forms/user-interface/stepper.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![Exemple de pas à pas](views-images/Stepper.png "Exemple de pas à pas")](views-images/Stepper-Large.png#lightbox "Exemple de pas à pas")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Basculer

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)prend la forme d’un commutateur activé/désactivé pour permettre à l’utilisateur de sélectionner une valeur booléenne. La [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriété correspond à l’état du commutateur, et l' [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) événement est déclenché lorsque l’état change.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Switch)  /  [Guide](~/xamarin-forms/user-interface/switch.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![Exemple de commutateur](views-images/Switch.png "Exemple de commutateur")](views-images/Switch-Large.png#lightbox "Exemple de commutateur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)permet à l’utilisateur de sélectionner une date avec le sélecteur de dates de plateforme. Définissez une plage de dates autorisées avec les [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) Propriétés et [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) . La [`Date`](xref:Xamarin.Forms.DatePicker.Date) propriété est la date sélectionnée et l' [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) événement est déclenché lorsque cette propriété change.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.DatePicker)  /  [Guide](~/xamarin-forms/user-interface/datepicker.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![Exemple de DatePicker](views-images/DatePicker.png "Exemple de DatePicker")](views-images/DatePicker-Large.png#lightbox "Exemple de DatePicker")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>Sélecteur d'heure

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)permet à l’utilisateur de sélectionner une heure avec le sélecteur d’heure de la plateforme. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriété est l’heure sélectionnée. Une application peut surveiller les modifications apportées à la `Time` propriété en installant un gestionnaire pour l' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TimePicker)  /  [Guide](~/xamarin-forms/user-interface/timepicker.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![Exemple TimePicker](views-images/TimePicker.png "Exemple TimePicker")](views-images/TimePicker-Large.png#lightbox "Exemple TimePicker")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Affichages pour la modification de texte

Ces deux classes dérivent de la [`InputView`](xref:Xamarin.Forms.InputView) classe, qui définit la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propriété.

### <a name="entry"></a>Entrée

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)permet à l’utilisateur d’entrer et de modifier une seule ligne de texte. Le texte est disponible en tant que [`Text`](xref:Xamarin.Forms.InputView.Text) propriété, et [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) les [`Completed`](xref:Xamarin.Forms.Entry.Completed) événements et sont déclenchés lorsque le texte change ou lorsque l’utilisateur signale la saisie semi-automatique en appuyant sur la touche entrée.<br /><br />Utilisez un [`Editor`](#editor) pour la saisie et la modification de plusieurs lignes de texte.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Entry)  /  [Guide](~/xamarin-forms/user-interface/text/entry.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’entrée](views-images/Entry.png "Exemple d’entrée")](views-images/Entry-Large.png#lightbox "Exemple d’entrée")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Éditeur

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)permet à l’utilisateur d’entrer et de modifier plusieurs lignes de texte. Le texte est disponible en tant que [`Text`](xref:Xamarin.Forms.InputView.Text) propriété, et [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) les [`Completed`](xref:Xamarin.Forms.Editor.Completed) événements et sont déclenchés lorsque le texte change ou lorsque l’utilisateur signale la fin de l’opération.<br /><br />Utilisez une [`Entry`](#entry) vue pour la saisie et la modification d’une seule ligne de texte.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Editor)  /  [Guide](~/xamarin-forms/user-interface/text/editor.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemple d’entrée](views-images/Editor.png "Exemple d’éditeur")](views-images/Editor-Large.png#lightbox "Exemple d’éditeur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Affichages pour indiquer une activité

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)utilise une animation pour montrer que l’application est engagée dans une activité de longue durée sans donner aucune indication de la progression. La [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propriété contrôle l’animation.<br /><br />Si la progression de l’activité est connue, utilisez à la [`ProgressBar`](#progressbar) place.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.ActivityIndicator)  /  [Guide](~/xamarin-forms/user-interface/activityindicator.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![Exemple ActivityIndicator](views-images/ActivityIndicator.png "Exemple ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Exemple ActivityIndicator")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>Barre de progression

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)utilise une animation pour montrer que l’application progresse dans une activité longue. Affectez [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) à la propriété des valeurs comprises entre 0 et 1 pour indiquer la progression.<br /><br />Si la progression de l’activité n’est pas connue, utilisez à la [`ActivityIndicator`](#activityindicator) place.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.ProgressBar)  /  [Guide](~/xamarin-forms/user-interface/progressbar.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![Exemple de ProgressBar](views-images/ProgressBar.png "Exemple de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Exemple de ProgressBar")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Affichages qui montrent des collections

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)affiche une liste déroulante d’éléments de données. Affectez `ItemsSource` à la propriété une collection d’objets et affectez `ItemTemplate` à la propriété un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objet décrivant la manière dont les éléments doivent être mis en forme. L' `CurrentItemChanged` événement signale que l’élément actuellement affiché a été modifié, ce qui est disponible en tant que `CurrentItem` propriété.<br /><br />[Guide](~/xamarin-forms/user-interface/carouselview/index.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) | [![Exemple CarouselView](views-images/CarouselView.png "Exemple CarouselView")](views-images/CarouselView-Large.png#lightbox "Exemple CarouselView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)affiche une liste déroulante d’éléments de données sélectionnables, à l’aide de différentes spécifications de disposition. Il vise à fournir une alternative plus flexible et plus performante à [`ListView`](xref:Xamarin.Forms.ListView) . Affectez `ItemsSource` à la propriété une collection d’objets et affectez `ItemTemplate` à la propriété un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objet décrivant la manière dont les éléments doivent être mis en forme. L' `SelectionChanged` événement signale qu’une sélection a été effectuée, qui est disponible en tant que `SelectedItem` propriété.<br /><br />[Guide](~/xamarin-forms/user-interface/collectionview/index.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) | [![Exemple CollectionView](views-images/CollectionView.png "Exemple CollectionView")](views-images/CollectionView-Large.png#lightbox "Exemple CollectionView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView`affiche des indicateurs qui représentent le nombre d’éléments dans un `CarouselView` . Affectez `CarouselView.IndicatorView` à la propriété l' `IndicatorView` objet pour afficher les indicateurs de `CarouselView` . <br /><br />[Guide](~/xamarin-forms/user-interface/indicatorview.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) | [![Exemple IndicatorView](views-images/IndicatorView.png "Exemple IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Exemple IndicatorView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>Affichage de liste

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)dérive de [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) et affiche une liste déroulante d’éléments de données sélectionnables. Affectez [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) à la propriété une collection d’objets et affectez [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) à la propriété un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objet décrivant la manière dont les éléments doivent être mis en forme. L' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement signale qu’une sélection a été effectuée, qui est disponible en tant que [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriété.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.ListView)  /  [Guide](~/xamarin-forms/user-interface/listview/index.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![Exemple de ListView](views-images/ListView.png "Exemple de ListView")](views-images/ListView-Large.png#lightbox "Exemple de ListView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Sélecteur

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)affiche un élément sélectionné dans une liste de chaînes de texte et permet de sélectionner cet élément lorsque la vue est frappée. Affectez [`Items`](xref:Xamarin.Forms.Picker.Items) à la propriété une liste de chaînes ou la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriété à une collection d’objets. L' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement est déclenché lorsqu’un élément est sélectionné.<br /><br />La `Picker` affiche la liste des éléments uniquement lorsqu’elle est sélectionnée. Utilisez [`ListView`](#listview) ou [`TableView`](#tableview) pour une liste déroulante qui reste sur la page.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.Picker)  /  [Guide](~/xamarin-forms/user-interface/picker/index.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![Exemple de sélecteur](views-images/Picker.png "Exemple de sélecteur")](views-images/Picker-Large.png#lightbox "Exemple de sélecteur")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)affiche une liste de lignes de type [`Cell`](xref:Xamarin.Forms.Cell) avec des en-têtes et des en-têtes facultatifs. Affectez [`Root`](xref:Xamarin.Forms.TableView.Root) à la propriété un objet de type [`TableRoot`](xref:Xamarin.Forms.TableRoot) et ajoutez des [`TableSection`](xref:Xamarin.Forms.TableSection) objets à ce `TableRoot` . Chaque `TableSection` est une collection d' `Cell` objets.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TableView)  /  [Guide](~/xamarin-forms/user-interface/tableview.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![Exemple TableView](views-images/TableView.png "Exemple TableView")](views-images/TableView-Large.png#lightbox "Exemple TableView")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsExemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsExtraits](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentation sur les API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
