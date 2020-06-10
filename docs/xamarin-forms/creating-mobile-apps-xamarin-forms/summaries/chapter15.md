---
titre : «Résumé du chapitre 15. L’interface interactive « Description : » création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 15. L’interface interactive «ms. Prod : xamarin ms. Technology : xamarin-Forms ms. AssetID : F54E86F4-1CDA-474E-9B09-242060C2C13D auteur : davidbritch ms. Author : dabritch ms. Date : 11/07/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-15-the-interactive-interface"></a>Résumé du chapitre 15. Interface interactive

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Ce chapitre explore huit `View` dérivés qui permettent l’interaction avec l’utilisateur.

## <a name="view-overview"></a>Vue d’ensemble

Xamarin.Formscontient 20 classes instanciables qui dérivent de, `View` mais pas `Layout` . Six de ces éléments ont été traités dans les chapitres précédents :

- `Label`: [ **Chapitre 2. Anatomie d’une application**](chapter02.md)
- `BoxView`: [ **Chapitre 3. Défilement de la pile**](chapter03.md)
- `Button`: [ **Chapitre 6. Clics de bouton**](chapter06.md)
- `Image`: [ **Chapitre 13. Images bitmap**](chapter13.md)
- `ActivityIndicator`: [ **Chapitre 13. Images bitmap**](chapter13.md)
- `ProgressBar`: [ **Chapitre 14. AbsoluteLayout**](chapter14.md)

Les huit vues de ce chapitre permettent à l’utilisateur d’interagir de manière efficace avec les types de données .NET de base :

|Type de données|Les vues|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Vous pouvez considérer ces vues comme des représentations interactives visuelles des types de données sous-jacents. Ce concept est abordé plus en revue dans le chapitre suivant, [**chapitre 16. Liaison de données**](chapter16.md).

Les six autres vues sont traitées dans les chapitres suivants :

- `WebView`: [ **Chapitre 16. Liaison de données**](chapter16.md)
- `Picker`: [ **Chapitre 19. Vues de collection**](chapter19.md)
- `ListView`: [ **Chapitre 19. Vues de collection**](chapter19.md)
- `TableView`: [ **Chapitre 19. Vues de collection**](chapter19.md)
- `Map`: [ **Chapitre 28. Emplacement et mappages**](chapter28.md)
- `OpenGLView`: Non couverts dans ce manuel (et aucune prise en charge des plateformes Windows)

## <a name="slider-and-stepper"></a>Slider et pas à pas

[`Slider`](xref:Xamarin.Forms.Slider)Et [`Stepper`](xref:Xamarin.Forms.Stepper) permettent à l’utilisateur de choisir une valeur numérique à partir d’une plage. `Slider`Est une plage continue, tandis que `Stepper` implique des valeurs discrètes.

### <a name="slider-basics"></a>Notions de base sur les curseurs

[`Slider`](xref:Xamarin.Forms.Slider)Est une barre horizontale qui représente une plage de valeurs comprises entre un minimum et un maximum à droite. Il définit trois propriétés publiques :

- [`Value`](xref:Xamarin.Forms.Slider.Value)de type `double` , valeur par défaut 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)de type `double` , valeur par défaut 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)de type `double` , valeur par défaut de 1

Les propriétés pouvant être liées qui stockent ces propriétés garantissent qu’elles sont cohérentes :

- Pour les trois propriétés, la [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) méthode spécifiée pour la propriété pouvant être liée garantit que `Value` est comprise entre `Minimum` et `Maximum` .
- La [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) méthode sur `MinimumProperty` retourne `false` si `Minimum` est défini sur une valeur supérieure ou égale à `Maximum` , et similaire pour `MaximumProperty` . Si `false` vous retournez à partir de la `validateValue` méthode, un `ArgumentException` est déclenché.

`Slider`déclenche l' [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) événement avec un [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) argument lorsque la `Value` propriété change, par programmation ou lorsque l’utilisateur manipule le `Slider` .

L’exemple [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) illustre l’utilisation simple de `Slider` .

### <a name="common-pitfalls"></a>Pièges courants

À la fois dans le code et dans XAML, les `Minimum` `Maximum` Propriétés et sont définies dans l’ordre que vous spécifiez. Veillez à initialiser ces propriétés afin que `Maximum` soit toujours supérieur à `Minimum` . Dans le cas contraire, une exception est levée.

L’initialisation des `Slider` Propriétés peut entraîner la `Value` modification de la propriété et l' `ValueChanged` événement à déclencher. Vous devez vous assurer que le `Slider` Gestionnaire d’événements n’accède pas à des vues qui n’ont pas encore été créées pendant l’initialisation de la page.

L' `ValueChanged` événement ne se déclenche pas pendant l' `Slider` initialisation à moins que la `Value` propriété ne change. Vous pouvez appeler le `ValueChanged` Gestionnaire directement à partir du code.

### <a name="slider-color-selection"></a>Sélection de la couleur du curseur

Le programme [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contient trois `Slider` éléments qui vous permettent de sélectionner de manière interactive une couleur en spécifiant ses valeurs RVB :

[![Capture d’écran triple des curseurs R G B](images/ch15fg03-small.png "Curseurs RGB")](images/ch15fg03-large.png#lightbox "Curseurs RGB")

L’exemple [**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) utilise deux `Slider` éléments pour déplacer deux `Label` éléments dans un `AbsoluteLayout` et en fondu un dans l’autre.

### <a name="the-stepper-difference"></a>Différence de pas à pas

[`Stepper`](xref:Xamarin.Forms.Stepper)Définit les mêmes propriétés et événements que, `Slider` mais la `Maximum` propriété est initialisée à 100 et `Stepper` définit une quatrième propriété :

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)de type `double` , initialisé à 1

Visuellement, le `Stepper` se compose de deux boutons étiquetés **&ndash;** et **+** . Si **&ndash;** vous appuyez sur `Value` , vous réduisez `Increment` au minimum `Minimum` . Si **+** vous appuyez sur, augmentez `Value` `Increment` jusqu’à un maximum de `Maximum` .

Cela est illustré par l’exemple [**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) .

## <a name="switch-and-checkbox"></a>Commutateur et case à cocher

[`Switch`](xref:Xamarin.Forms.Switch)Permet à l’utilisateur de spécifier une valeur booléenne.

### <a name="switch-basics"></a>Notions de base du commutateur

Visuellement, le `Switch` se compose d’un bouton bascule qui peut être désactivé et activé. La classe définit une propriété :

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)de type`bool`

`Switch`définit un événement :

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)accompagné d’un [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) objet, déclenché lorsque la `IsToggled` propriété change.

Le programme [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) illustre le `Switch` .

### <a name="a-traditional-checkbox"></a>Case à cocher traditionnelle

Certains développeurs préfèrent être plus traditionnels `CheckBox` à `Switch` . La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une `CheckBox` classe qui dérive de `ContentView` . `CheckBox`est implémenté par les fichiers [CheckBox. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) et [CheckBox.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) . `CheckBox`définit trois propriétés ( `Text` , `FontSize` et `IsChecked` ) et un `CheckedChanged` événement.

L’exemple [**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) illustre cela `CheckBox` .

## <a name="typing-text"></a>Taper du texte

Xamarin.Formsdéfinit trois vues qui permettent à l’utilisateur d’entrer et de modifier du texte :

- [`Entry`](xref:Xamarin.Forms.Entry)pour une seule ligne de texte
- [`Editor`](xref:Xamarin.Forms.Editor)pour plusieurs lignes de texte
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)pour une seule ligne de texte à des fins de recherche.

`Entry`et `Editor` dérivent de [`InputView`](xref:Xamarin.Forms.InputView) , qui dérive de `View` . `SearchBar`dérive directement de `View` .

### <a name="keyboard-and-focus"></a>Clavier et Focus

Sur les téléphones et les tablettes sans claviers physiques, les `Entry` `Editor` éléments, et `SearchBar` provoquent tous un clavier virtuel. La présence de ce clavier à l’écran est liée au focus d’entrée. Les propriétés et de la vue doivent avoir la [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) valeur `true` pour obtenir le focus d’entrée.

Deux méthodes, une propriété en lecture seule et deux événements sont impliqués dans le focus d’entrée. Ils sont tous définis par `VisualElement` :

- La [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) méthode tente de définir le focus d’entrée sur un élément et retourne en `true` cas de réussite
- La [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) méthode supprime le focus d’entrée d’un élément
- La [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) propriété en lecture seule indique si l’élément a le focus d’entrée
- L' [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) événement indique quand un élément obtient le focus d’entrée
- L' [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) événement indique lorsqu’un élément perd le focus d’entrée

### <a name="choosing-the-keyboard"></a>Choix du clavier

La [`InputView`](xref:Xamarin.Forms.InputView) classe à partir de laquelle `Entry` et `Editor` dérived définit une seule propriété :

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)de type[`Keyboard`](xref:Xamarin.Forms.Keyboard)

Cela indique le type de clavier qui est affiché. Certains claviers sont optimisés pour les URI ou les nombres.

La `Keyboard` classe permet de définir un clavier avec un [ `Keyboard.Create` ] statique [] (XREF : Xamarin.Forms . Keyboard. Create ( Xamarin.Forms . KeyboardFlags)) avec un argument de type [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) , une énumération avec les indicateurs binaires suivants :

- `None`défini sur 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)défini sur 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)défini sur 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)défini sur 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)définir sur \xFFFFFFFF

Lorsque vous utilisez la multiligne [`Editor`](xref:Xamarin.Forms.Editor) lorsqu’un paragraphe ou plus de texte est attendu, l’appel de `Keyboard.Create` est une bonne approche de la sélection d’un clavier. Pour la ligne simple [`Entry`](xref:Xamarin.Forms.Entry) , les propriétés en lecture seule statiques suivantes de `Keyboard` sont utiles :

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)pour les nombres positifs avec ou sans virgule décimale.

Le [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) permet de spécifier ces propriétés en XAML comme indiqué par le programme [**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) .

### <a name="entry-properties-and-events"></a>Propriétés et événements d’entrée

La ligne simple [`Entry`](xref:Xamarin.Forms.Entry) définit les propriétés suivantes :

- [`Text`](xref:Xamarin.Forms.InputView.Text)de type `string` , le texte qui apparaît dans le`Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)de type`Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)de type`string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize)de type`double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)de type`FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword)de type `bool` , ce qui entraîne le masquage des caractères
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)de type `string` , pour le texte de couleur dimly qui apparaît dans le `Entry` avant que tout soit tapé
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)de type`Color`

Le `Entry` définit également deux événements :

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)avec un [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) objet, déclenché à chaque `Text` modification de la propriété
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), déclenché lorsque l’utilisateur a terminé et que le clavier est fermé. L’utilisateur indique l’achèvement de manière spécifique à une plateforme

L’exemple [**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) illustre ces deux événements.

### <a name="the-editor-difference"></a>Différences de l’éditeur

La ligne multiligne [`Editor`](xref:Xamarin.Forms.Editor) définit les `Text` mêmes `Font` Propriétés et que `Entry` les autres propriétés. `Editor`définit également les deux mêmes propriétés que `Entry` .

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) est un programme prenant en charge les notes de forme libre qui enregistre et restaure le contenu du `Editor` .

### <a name="the-searchbar"></a>SearchBar

Le [`SearchBar`](xref:Xamarin.Forms.SearchBar) ne dérive pas de `InputView` , donc il n’a pas de `Keyboard` propriété. Mais il possède toutes les `Text` Propriétés, `Font` et `Placeholder` qui `Entry` définissent. En outre, `SearchBar` définit trois propriétés supplémentaires :

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)de type`Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)de type [`ICommand`](xref:System.Windows.Input.ICommand) à utiliser avec les liaisons de données et MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)de type `Object` , à utiliser avec`SearchCommand`

Le bouton Annuler propre à la plateforme efface le texte. `SearchBar`Possède également un bouton de recherche spécifique à la plateforme. La pression sur l’un de ces boutons déclenche l’un des deux événements qui `SearchBar` définissent les éléments suivants :

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)accompagné d’un [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) objet
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

L’exemple [**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) illustre le `SearchBar` .

## <a name="date-and-time-selection"></a>Sélection de la date et de l’heure

Les [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`TimePicker`](xref:Xamarin.Forms.TimePicker) vues et implémentent des contrôles spécifiques à la plateforme qui permettent à l’utilisateur de spécifier une date ou une heure.

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker)définit quatre propriétés :

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)de type `DateTime` , initialisé au 1er janvier 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)de type `DateTime` , initialisé au 31 décembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)de type `DateTime` , initialisé à`DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)de type `string` , la chaîne de mise en forme .net initialisée à « d », le modèle de date abrégée, ce qui donne un affichage de date comme « 7/20/1969 » aux États-Unis.

Vous pouvez définir les `DateTime` Propriétés en XAML en exprimant les propriétés en tant qu’éléments de propriété et en utilisant le format de date abrégée de culture (« 7/20/1969 »).   

L’exemple [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcule le nombre de jours entre deux dates sélectionnées par l’utilisateur.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Le TimePicker (ou s’agit-il d’un TimeSpanPicker ?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker)définit deux propriétés et aucun événement :

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)est de type `TimeSpan` au lieu de `DateTime` , ce qui indique le temps écoulé depuis minuit
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)de type `string` , la chaîne de mise en forme .net initialisée sur « t », le modèle d’heure abrégée, ce qui donne un affichage de temps comme « 1:45 PM » aux États-Unis.

Le programme [**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) montre comment utiliser le `TimePicker` pour spécifier une heure pour une minuterie. Le programme ne fonctionne que si vous le conservez au premier plan.

**SetTimer** illustre également l’utilisation [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) de la méthode de `Page` pour afficher un message d’alerte.

## <a name="related-links"></a>Liens connexes

- [Chapitre 15 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Chapitre 15 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Curseur](~/xamarin-forms/user-interface/slider.md)
- [Entrée](~/xamarin-forms/user-interface/text/entry.md)
- [Éditeur](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
