---
title: Contrôles XAML
description: Toutes les vues définies dans Xamarin. Forms peuvent être référencées à partir de fichiers XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2019
ms.openlocfilehash: 9d9d07388c2dc32e82fb780d0ed09e586e401662
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648644"
---
# <a name="xaml-controls"></a>Contrôles XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

Les vues sont des objets d’interface utilisateur tels que des étiquettes, des boutons et des curseurs qui sont communément appelés *contrôles* ou *widgets* dans d’autres environnements de programmation graphiques. Les vues prises en charge par Xamarin.Forms dérivent toutes de la [ `View` ](xref:Xamarin.Forms.View) classe.

Toutes les vues définies dans Xamarin. Forms peuvent être référencées à partir de fichiers XAML.

## <a name="views-for-presentation"></a>Affichages de présentation

|     |     |
| --- | --- |
| <h3>BoxView</h3>Affiche un rectangle d’une couleur particulière.<p align="center">![Capture d’écran d’un BoxView](xaml-controls-images/BoxView.png "BoxView")</p> / [Guide](~/xamarin-forms/user-interface/boxview.md) des [API](xref:Xamarin.Forms.BoxView) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Image</h3>Affiche une image bitmap.<p align="center">![Capture d’écran d’une image](xaml-controls-images/Image.png "Image")</p> / [Guide](~/xamarin-forms/user-interface/images.md) des [API](xref:Xamarin.Forms.Image) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Etiquette</h3>Affiche une ou plusieurs lignes de texte.<p align="center">![Capture d’écran d’une étiquette](xaml-controls-images/Label.png "Étiquette")</p> / [Guide](~/xamarin-forms/user-interface/text/label.md) des [API](xref:Xamarin.Forms.Label) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mappage</h3>Affiche une carte.<p align="center">![Capture d’écran d’une carte](xaml-controls-images/Map.png "Carte")</p> / [Guide](~/xamarin-forms/user-interface/map.md) des [API](xref:Xamarin.Forms.Maps.Map) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Affiche des pages Web ou du contenu HTML.<p align="center">![Capture d’écran d’une WebView](xaml-controls-images/WebView.png "Vue WebView")</p> / [Guide](~/xamarin-forms/user-interface/webview.md) des [API](xref:Xamarin.Forms.WebView) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Vues qui lancer des commandes

|     |     |
| --- | --- |
| <h3>Bouton</h3>Affiche du texte dans un objet rectangulaire.<p align="center">![Capture d’écran d’un bouton](xaml-controls-images/Button.png "Bouton")</p> / [Guide](~/xamarin-forms/user-interface/button.md) des [API](xref:Xamarin.Forms.Button) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Affiche une image dans un objet rectangulaire.<p align="center">![Capture d’écran d’un ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p> / [Guide](~/xamarin-forms/user-interface/imagebutton.md) des [API](xref:Xamarin.Forms.ImageButton) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>SearchBar</h3>Affiche une barre de recherche pour effectuer une recherche.<p align="center">![Capture d’écran d’un Searchbar](xaml-controls-images/SearchBar.png "Searchbar")</p>[API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Vues de définition des valeurs

|     |     |
| --- | --- |
| <h3>Case à cocher</h3>Autorise la sélection d’une `boolean` valeur.<p align="center">![Capture d’écran d’une case à cocher](xaml-controls-images/CheckBox.png "Case à cocher")</p> [Père](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Curseur</h3>Permet de sélectionner une `double` valeur dans une plage continue.<p align="center">![Capture d’écran d’un curseur](xaml-controls-images/Slider.png "Curseur")</p> / [Guide](~/xamarin-forms/user-interface/slider.md) des [API](xref:Xamarin.Forms.Slider) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Exécution pas à pas</h3>Permet de sélectionner une `double` valeur dans une plage incrémentielle.<p align="center">![Capture d’écran d’un] pas à pas (xaml-controls-images/Stepper.png "") Pas à pas</p> / [Guide](~/xamarin-forms/user-interface/stepper.md) des [API](xref:Xamarin.Forms.Stepper) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Basculer</h3>Autorise la sélection d’une `boolean` valeur.<p align="center">![Capture d’écran d’un commutateur](xaml-controls-images/Switch.png "Commutateur")</p> / [Guide](~/xamarin-forms/user-interface/switch.md) des [API](xref:Xamarin.Forms.Switch)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Sélecteur de dates</h3>Autorise la sélection d’une date.<p align="center">![Capture d’écran d’un DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p> / [Guide](~/xamarin-forms/user-interface/datepicker.md) des [API](xref:Xamarin.Forms.DatePicker) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Sélecteur d'heure</h3>Autorise la sélection d’une heure.<p align="center">![Capture d’écran d’un timepicker](xaml-controls-images/TimePicker.png "Timepicker")</p> / [Guide](~/xamarin-forms/user-interface/timepicker.md) des [API](xref:Xamarin.Forms.TimePicker) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Vues de modification de texte

|     |     |
| --- | --- |
| <h3>Entrée</h3>Permet d’entrer et de modifier une seule ligne de texte.<p align="center">![Capture d’écran d’une entrée](xaml-controls-images/Entry.png "Entrée")</p> / [Guide](~/xamarin-forms/user-interface/text/entry.md) des [API](xref:Xamarin.Forms.Entry) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Éditeur</h3>Permet d’entrer et de modifier plusieurs lignes de texte.<p align="center">![Capture d’écran d’un éditeur](xaml-controls-images/Editor.png "Étiquette")</p> / [Guide](~/xamarin-forms/user-interface/text/editor.md) des [API](xref:Xamarin.Forms.Editor) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Vues pour indiquer l’activité

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Affiche une animation qui montre que l’application est engagée dans une activité longue, sans donner aucune indication de la progression.<p align="center">![Capture d’écran d’un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Barre de progression</h3>Affiche une animation qui montre que l’application progresse dans une activité longue.<p align="center">![Capture d’écran d’un ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Vues qui affichent des collections

|     |     |
| --- | --- |
| <h3>CollectionView</h3>Affiche une liste déroulante d’éléments de données sélectionnables, à l’aide de différentes spécifications de disposition.<p align="center">![Capture d’écran d’un CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Père](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>Affichage de liste</h3>Affiche une liste déroulante d’éléments de données sélectionnables.<p align="center">![Capture d’écran d’un ListView](xaml-controls-images/ListView.png "Liste")</p> / [Guide](~/xamarin-forms/user-interface/listview/index.md) des [API](xref:Xamarin.Forms.ListView) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Sélecteur</h3>Affiche un élément sélectionné dans une liste de chaînes de texte.<p align="center">![Capture d’écran d’un sélecteur](xaml-controls-images/Picker.png "Sélecteur")</p> / [Guide](~/xamarin-forms/user-interface/picker/index.md) des [API](xref:Xamarin.Forms.Picker) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Affiche une liste de lignes interactives.<p align="center">![Capture d’écran d’un TableView](xaml-controls-images/TableView.png "TableView")</p> / [Guide](~/xamarin-forms/user-interface/tableview.md) des [API](xref:Xamarin.Forms.TableView) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Liens connexes

- [Exemple de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
