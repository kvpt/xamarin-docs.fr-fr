---
titre : « contrôles XAML » Description : « toutes les vues définies dans Xamarin.Forms peuvent être référencées à partir de fichiers XAML ».
ms. topic : article ms. Prod : xamarin ms. AssetID : 639BD392-1496-41BB-BB09-7652273AC9D8 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 04/17/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-controls"></a>Contrôles XAML

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

Les vues sont des objets d’interface utilisateur, tels que des étiquettes, des boutons et des curseurs, communément appelés *contrôles* ou *widgets* dans d’autres environnements de programmation graphiques. Les vues prises en charge par sont Xamarin.Forms toutes dérivées de la [`View`](xref:Xamarin.Forms.View) classe.

Toutes les vues définies dans Xamarin.Forms peuvent être référencées à partir de fichiers XAML.

## <a name="views-for-presentation"></a>Affichages pour la présentation

|     |     |
| --- | --- |
| <h3>BoxView</h3>Affiche un rectangle d’une couleur particulière.<p align="center">![Capture d’écran d’un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView)  /  [Guide](~/xamarin-forms/user-interface/boxview.md) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Expander</h3>Fournit un conteneur développable pour héberger tout contenu.<p align="center">![Capture d’écran d’un Expander](xaml-controls-images/Expander.png "Expander")</p>[Père](~/xamarin-forms/user-interface/expander.md) | <pre>&lt;Expander&gt;<br />    &lt;Expander.Header&gt;<br />        &lt;Label Text=&quot;Baboon&quot; /&gt;<br />    &lt;/Expander.Header&gt;<br />    &lt;Image Source=&quot;Baboon.png&quot;<br />           Aspect=&quot;AspectFill&quot; /&gt;<br />&lt;/Expander&gt;</pre></p> |
| <h3>Image</h3>Affiche une image bitmap.<p align="center">![Capture d’écran d’une image](xaml-controls-images/Image.png "Image")</p>[API](xref:Xamarin.Forms.Image)  /  [Guide](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Etiquette</h3>Affiche une ou plusieurs lignes de texte.<p align="center">![Capture d’écran d’une étiquette](xaml-controls-images/Label.png "Etiquette")</p>[API](xref:Xamarin.Forms.Label)  /  [Guide](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mappage</h3>Affiche une carte.<p align="center">![Capture d’écran d’une carte](xaml-controls-images/Map.png "Carte")</p>[API](xref:Xamarin.Forms.Maps.Map)  /  [Guide](~/xamarin-forms/user-interface/map/index.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>Lire de la vidéo ou de l’audio.<p align="center">![Capture d’écran d’un MediaElement](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement)  /  [Guide](~/xamarin-forms/user-interface/mediaelement.md) | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>WebView</h3>Affiche des pages Web ou du contenu HTML.<p align="center">![Capture d’écran d’une WebView](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView)  /  [Guide](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Affichages lançant des commandes

|     |     |
| --- | --- |
| <h3>Bouton</h3>Affiche du texte dans un objet rectangulaire.<p align="center">![Capture d’écran d’un bouton](xaml-controls-images/Button.png "Bouton")</p>[API](xref:Xamarin.Forms.Button)  /  [Guide](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Affiche une image dans un objet rectangulaire.<p align="center">![Capture d’écran d’un ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton)  /  [Guide](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RadioButton</h3>Permet la sélection d’une option dans un ensemble.<p align="center">![Capture d’écran d’un RadioButton](xaml-controls-images/RadioButton.png "RadioButton")</p>[Père](~/xamarin-forms/user-interface/radiobutton.md) | <p valign="center"><pre>&lt;RadioButton Text=&quot;Pineapple&quot;<br/>             CheckedChanged=&quot;OnRadioButtonCheckedChanged&quot; /&gt;</pre></p> |
| <h3>RefreshView</h3>Fournit des fonctionnalités d’extraction pour le contenu défilant.<p align="center">![Capture d’écran d’un RefreshView](xaml-controls-images/RefreshView.png "RefreshView")</p>[Père](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>SearchBar</h3> Accepte l’entrée d’utilisateur qu’il utilise pour effectuer une recherche.<p align="center">![Capture d’écran d’un SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[Père](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder=&quot;Enter search term&quot;<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> Fournit des éléments de menu contextuel qui sont révélés par un mouvement de balayage.<p align="center">![Capture d’écran d’un SwipeView](xaml-controls-images/SwipeView.png "SwipeView")</p>[Père](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Affichages pour les valeurs des paramètres

|     |     |
| --- | --- |
| <h3>Case à cocher</h3>Autorise la sélection d’une `boolean` valeur.<p align="center">![Capture d’écran d’une case à cocher](xaml-controls-images/CheckBox.png "Case à cocher")</p> [Père](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Curseur</h3>Permet de sélectionner une `double` valeur dans une plage continue.<p align="center">![Capture d’écran d’un curseur](xaml-controls-images/Slider.png "Curseur")</p>[API](xref:Xamarin.Forms.Slider)  /  [Guide](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Exécution pas à pas</h3>Permet de sélectionner une `double` valeur dans une plage incrémentielle.<p align="center">![Capture d’écran d’un pas à pas](xaml-controls-images/Stepper.png "Exécution pas à pas")</p>[API](xref:Xamarin.Forms.Stepper)  /  [Guide](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Basculer</h3>Autorise la sélection d’une `boolean` valeur.<p align="center">![Capture d’écran d’un commutateur](xaml-controls-images/Switch.png "Basculer")</p>[API](xref:Xamarin.Forms.Switch)  /  [Guide](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Autorise la sélection d’une date.<p align="center">![Capture d’écran d’un DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker)  /  [Guide](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Sélecteur d'heure</h3>Autorise la sélection d’une heure.<p align="center">![Capture d’écran d’un TimePicker](xaml-controls-images/TimePicker.png "Sélecteur d'heure")</p>[API](xref:Xamarin.Forms.TimePicker)  /  [Guide](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Affichages pour la modification de texte

|     |     |
| --- | --- |
| <h3>Entrée</h3>Permet d’entrer et de modifier une seule ligne de texte.<p align="center">![Capture d’écran d’une entrée](xaml-controls-images/Entry.png "Entrée")</p>[API](xref:Xamarin.Forms.Entry)  /  [Guide](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Éditeur</h3>Permet d’entrer et de modifier plusieurs lignes de texte.<p align="center">![Capture d’écran d’un éditeur](xaml-controls-images/Editor.png "Etiquette")</p>[API](xref:Xamarin.Forms.Editor)  /  [Guide](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Affichages pour indiquer une activité

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Affiche une animation qui montre que l’application est engagée dans une activité longue, sans donner aucune indication de la progression.<p align="center">![Capture d’écran d’un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator)  /  [Guide](~/xamarin-forms/user-interface/activityindicator.md) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Barre de progression</h3>Affiche une animation qui montre que l’application progresse dans une activité longue.<p align="center">![Capture d’écran d’un ProgressBar](xaml-controls-images/ProgressBar.png "Barre de progression")</p>[API](xref:Xamarin.Forms.ProgressBar)  /  [Guide](~/xamarin-forms/user-interface/progressbar.md) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Affichages qui montrent des collections

|     |     |
| --- | --- |
| <h3>CarouselView</h3>Affiche une liste déroulante d’éléments de données.<p align="center">![Capture d’écran d’un CarouselView](xaml-controls-images/CarouselView.png "CarouselView")</p>[Père](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>Affiche une liste déroulante d’éléments de données sélectionnables, à l’aide de différentes spécifications de disposition.<p align="center">![Capture d’écran d’un CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Père](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>IndicatorView</h3>Affiche des indicateurs qui représentent le nombre d’éléments dans un `CarouselView` .<p align="center">![Capture d’écran d’un IndicatorView](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[Père](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView x:Name="indicatorView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>Affichage de liste</h3>Affiche une liste déroulante d’éléments de données sélectionnables.<p align="center">![Capture d’écran d’un ListView](xaml-controls-images/ListView.png "Affichage de liste")</p>[API](xref:Xamarin.Forms.ListView)  /  [Guide](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Sélecteur</h3>Affiche un élément sélectionné dans une liste de chaînes de texte.<p align="center">![Capture d’écran d’un sélecteur](xaml-controls-images/Picker.png "Sélecteur")</p>[API](xref:Xamarin.Forms.Picker)  /  [Guide](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Affiche une liste de lignes interactives.<p align="center">![Capture d’écran d’un TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView)  /  [Guide](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsExemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsExtraits](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentation sur les API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
