---
title: Contrôles XAML
description: Toutes les vues qui sont définies dans Xamarin.Forms peuvent être référencés à partir des fichiers XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2019
ms.openlocfilehash: dc903cf4ff8eaced18df3271e5e4dbdde6845b6a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832639"
---
# <a name="xaml-controls"></a>Contrôles XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)

Les vues sont des objets d’interface utilisateur tels que des étiquettes, des boutons et des curseurs qui sont communément appelés *contrôles* ou *widgets* dans d’autres environnements de programmation graphiques. Les vues prises en charge par Xamarin.Forms dérivent toutes de la [ `View` ](xref:Xamarin.Forms.View) classe.

Toutes les vues qui sont définies dans Xamarin.Forms peuvent être référencés à partir des fichiers XAML.

## <a name="views-for-presentation"></a>Affichages de présentation

|     |     |
| --- | --- |
| <h3>BoxView</h3>Affiche un rectangle d’une couleur particulière.<p align="center">![Capture d’écran d’un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView) / [Guide](https://developer.xamarin.com/guides/xamarin-forms/user-interface/boxview/) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Image</h3>affiche une image bitmap.<p align="center">![Capture d’écran d’une Image](xaml-controls-images/Image.png "Image")</p>[API](xref:Xamarin.Forms.Image) / [Guide](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Etiquette</h3>Affiche une ou plusieurs lignes de texte.<p align="center">![Capture d’écran d’une étiquette](xaml-controls-images/Label.png "étiquette")</p>[API](xref:Xamarin.Forms.Label) / [Guide](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mappage</h3>affiche une carte.<p align="center">![Capture d’écran d’un mappage](xaml-controls-images/Map.png "carte")</p>[API](xref:Xamarin.Forms.Maps.Map) / [Guide](~/xamarin-forms/user-interface/map.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Affiche les pages Web ou du contenu HTML.<p align="center">![Capture d’écran d’un affichage Web](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView) / [Guide](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Vues qui lancer des commandes

|     |     |
| --- | --- |
| <h3>Bouton</h3>Affiche le texte dans un objet rectangulaire.<p align="center">![Capture d’écran d’un bouton](xaml-controls-images/Button.png "bouton")</p>[API](xref:Xamarin.Forms.Button) / [Guide](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Affiche une image dans un objet rectangulaire.<p align="center">![Capture d’écran d’un contrôle ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [Guide](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>SearchBar</h3>Affiche une barre de recherche pour effectuer une recherche.<p align="center">![Capture d’écran d’un SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Vues de définition des valeurs

|     |     |
| --- | --- |
| <h3>Case à cocher</h3>Permet la sélection d’un `boolean` valeur.<p align="center">![Capture d’écran d’une case à cocher](xaml-controls-images/CheckBox.png "case à cocher")</p> [Guide](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Curseur</h3>Permet la sélection d’un `double` valeur à partir d’une plage continue.<p align="center">![Capture d’écran d’un curseur](xaml-controls-images/Slider.png "curseur")</p>[API](xref:Xamarin.Forms.Slider) / [Guide](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Exécution pas à pas</h3>Permet la sélection d’un `double` valeur à partir d’une plage incrémentielle.<p align="center">![Capture d’écran d’une exécution pas à pas](xaml-controls-images/Stepper.png "exécution pas à pas")</p>[API](xref:Xamarin.Forms.Stepper) / [Guide](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Basculer</h3>Permet la sélection d’un `boolean` valeur.<p align="center">![Capture d’écran d’un commutateur](xaml-controls-images/Switch.png "commutateur")</p>[API](xref:Xamarin.Forms.Switch) / [Guide](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Sélecteur de dates</h3>Permet la sélection d’une date.<p align="center">![Capture d’écran d’un sélecteur de dates](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [Guide](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Sélecteur d'heure</h3>Permet la sélection d’une heure.<p align="center">![Capture d’écran d’un TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [Guide](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Vues de modification de texte

|     |     |
| --- | --- |
| <h3>Entrée</h3>Permet à une seule ligne de texte entré et la modification.<p align="center">![Capture d’écran d’une entrée](xaml-controls-images/Entry.png "entrée")</p>[API](xref:Xamarin.Forms.Entry) / [Guide](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Éditeur</h3>Permet à plusieurs lignes de texte entré et la modification.<p align="center">![Capture d’écran d’un éditeur](xaml-controls-images/Editor.png "étiquette")</p>[API](xref:Xamarin.Forms.Editor) / [Guide](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Vues pour indiquer l’activité

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Affiche une animation pour montrer que l’application est engagée dans une activité de longue durée, sans perdre aucune indication de progression.<p align="center">![Capture d’écran d’un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Barre de progression</h3>Affiche une animation pour montrer que l’application est mise en œuvre une activité de longue durée.<p align="center">![Capture d’écran d’un ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Vues qui affichent des collections

|     |     |
| --- | --- |
| <h3>CollectionView</h3>affiche une liste déroulante peut être sélectionné d’éléments de données, à l’aide des spécifications de mise en page différente.<p align="center">![Capture d’écran d’un CollectionView de](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guide](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>Affichage de liste</h3>Affiche une liste déroulante d’éléments de données sélectionnables.<p align="center">![Capture d’écran d’un ListView](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [Guide](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Sélecteur</h3>Affiche un élément sélectionné dans une liste de chaînes de texte.<p align="center">![Capture d’écran d’un sélecteur de](xaml-controls-images/Picker.png "sélecteur")</p>[API](xref:Xamarin.Forms.Picker) / [Guide](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Affiche une liste de lignes interactives.<p align="center">![Capture d’écran d’un objet TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView) / [Guide](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Liens connexes

- [Exemple de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Exemples Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
