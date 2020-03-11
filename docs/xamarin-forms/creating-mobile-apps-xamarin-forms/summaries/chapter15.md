---
title: Résumé du chapitre 15. L’interface interactive
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 15. L’interface interactive'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291906"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Résumé du chapitre 15. L’interface interactive

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Ce chapitre explore huit `View` dérivés qui permettent l’interaction avec l’utilisateur.

## <a name="view-overview"></a>Vue d’ensemble de la vue

Xamarin. Forms contient 20 classes instanciables qui dérivent de `View` mais pas `Layout`. Six d'entre eux ont été traités dans les chapitres précédents :

- `Label`: [ **Chapitre 2. Anatomie d’une application**](chapter02.md)
- `BoxView`: [ **chapitre 3. Défilement de la pile**](chapter03.md)
- `Button`: [ **chapitre 6. Clics de bouton**](chapter06.md)
- `Image`: [ **chapitre 13. Images bitmap**](chapter13.md)
- `ActivityIndicator`: [ **chapitre 13. Images bitmap**](chapter13.md)
- `ProgressBar`: [ **chapitre 14. AbsoluteLayout**](chapter14.md)

Les huit vues dans ce chapitre autoriser efficacement l’utilisateur d’interagir avec les types de données de base .NET :

|Type de données|Les vues|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor) [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Vous pouvez considérer de ces vues en tant que représentations interactives visuelles des types de données sous-jacente. Ce concept est abordé plus en revue dans le chapitre suivant, [**chapitre 16. Liaison de données**](chapter16.md).

Les vues de six restants sont traités dans les chapitres suivants :

- `WebView`: [ **chapitre 16. Liaison de données**](chapter16.md)
- `Picker`: [ **chapitre 19. Vues de collection**](chapter19.md)
- `ListView`: [ **chapitre 19. Vues de collection**](chapter19.md)
- `TableView`: [ **chapitre 19. Vues de collection**](chapter19.md)
- `Map`: [ **chapitre 28. Emplacement et mappages**](chapter28.md)
- `OpenGLView`: non couverts dans ce livre (et aucune prise en charge des plateformes Windows)

## <a name="slider-and-stepper"></a>Curseur et l’exécution pas à pas

[`Slider`](xref:Xamarin.Forms.Slider) et [`Stepper`](xref:Xamarin.Forms.Stepper) permettent à l’utilisateur de choisir une valeur numérique à partir d’une plage. Le `Slider` est une plage continue, tandis que le `Stepper` implique des valeurs discrètes.

### <a name="slider-basics"></a>Principes de base de curseur

La [`Slider`](xref:Xamarin.Forms.Slider) est une barre horizontale qui représente une plage de valeurs comprises entre un minimum à gauche et un maximum à droite. Il définit trois propriétés publiques :

- [`Value`](xref:Xamarin.Forms.Slider.Value) de type `double`, valeur par défaut 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) de type `double`, valeur par défaut 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) de type `double`, valeur par défaut de 1

Les propriétés pouvant être liées qui assortir ces propriétés de vous assurer qu’elles sont cohérentes :

- Pour les trois propriétés, la méthode [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) spécifiée pour la propriété pouvant être liée garantit que `Value` se trouve entre `Minimum` et `Maximum`.
- La méthode [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) sur `MinimumProperty` retourne `false` si `Minimum` est définie sur une valeur supérieure ou égale à `Maximum`, et similaire pour `MaximumProperty`. Le renvoi d' `false` à partir de la méthode `validateValue` entraîne le déclenchement d’un `ArgumentException`.

`Slider` déclenche l’événement [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) avec un argument [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) lorsque la propriété `Value` change, soit par programmation, soit lorsque l’utilisateur manipule le `Slider`.

L’exemple [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) illustre l’utilisation simple de l' `Slider`.

### <a name="common-pitfalls"></a>Pièges courants

À la fois dans le code et dans XAML, les propriétés `Minimum` et `Maximum` sont définies dans l’ordre que vous spécifiez. Veillez à initialiser ces propriétés afin que `Maximum` soit toujours supérieur à `Minimum`. Dans le cas contraire, une exception est levée.

L’initialisation des propriétés de `Slider` peut entraîner la modification de la propriété `Value` et l’événement `ValueChanged` à déclencher. Vous devez vous assurer que le gestionnaire d’événements `Slider` n’accède pas à des vues qui n’ont pas encore été créées pendant l’initialisation de la page.

L’événement `ValueChanged` ne se déclenche pas pendant l’initialisation d' `Slider` à moins que la propriété `Value` ne change. Vous pouvez appeler le gestionnaire de `ValueChanged` directement à partir du code.

### <a name="slider-color-selection"></a>Sélection de couleur de curseur

Le programme [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contient trois éléments `Slider` qui vous permettent de sélectionner de manière interactive une couleur en spécifiant ses valeurs RVB :

[![Capture d’écran triple des curseurs R G B](images/ch15fg03-small.png "Curseurs RGB")](images/ch15fg03-large.png#lightbox "Curseurs RGB")

L’exemple [**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) utilise deux éléments `Slider` pour déplacer deux éléments `Label` sur un `AbsoluteLayout` et les faire disparaître l’un à l’autre.

### <a name="the-stepper-difference"></a>La différence de l’exécution pas à pas

Le [`Stepper`](xref:Xamarin.Forms.Stepper) définit les mêmes propriétés et événements que `Slider` mais la propriété `Maximum` est initialisée à 100 et `Stepper` définit une quatrième propriété :

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) de type `double`, initialisé à 1

Visuellement, le `Stepper` se compose de deux boutons libellés **&ndash;** et **+** . Si vous appuyez sur **&ndash;** , vous réduisez `Value` en `Increment` au minimum `Minimum`. Si vous appuyez sur **+** , `Value` `Increment` à un maximum de `Maximum`.

Cela est illustré par l’exemple [**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) .

## <a name="switch-and-checkbox"></a>Commutateur et la case à cocher

Le [`Switch`](xref:Xamarin.Forms.Switch) permet à l’utilisateur de spécifier une valeur booléenne.

### <a name="switch-basics"></a>Principes fondamentaux de commutateur

Visuellement, le `Switch` se compose d’un bouton bascule qui peut être désactivé et activé. La classe définit une propriété :

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) de type `bool`

`Switch` définit un événement :

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) accompagné d’un objet [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) , déclenché lorsque la propriété `IsToggled` change.

Le programme [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) illustre le `Switch`.

### <a name="a-traditional-checkbox"></a>Une case à cocher traditionnel

Certains développeurs préfèrent un `CheckBox` plus traditionnel à la `Switch`. La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une classe `CheckBox` qui dérive de `ContentView`. `CheckBox` est implémenté par les fichiers [CheckBox. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) et [CheckBox.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) . `CheckBox` définit trois propriétés (`Text`, `FontSize`et `IsChecked`) et un événement `CheckedChanged`.

L’exemple [**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) illustre cette `CheckBox`.

## <a name="typing-text"></a>Saisie de texte

Xamarin.Forms définit trois vues qui permettent l’utilisateur d’entrer et de modifier le texte :

- [`Entry`](xref:Xamarin.Forms.Entry) pour une seule ligne de texte
- [`Editor`](xref:Xamarin.Forms.Editor) pour plusieurs lignes de texte
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) pour une seule ligne de texte à des fins de recherche.

`Entry` et `Editor` dérivent de [`InputView`](xref:Xamarin.Forms.InputView), qui dérive de `View`. `SearchBar` dérive directement de `View`.

### <a name="keyboard-and-focus"></a>Clavier et le focus

Sur les téléphones et les tablettes sans claviers physiques, les éléments `Entry`, `Editor`et `SearchBar` provoquent tous un clavier virtuel. La présence de ce clavier sur l’écran concerne le focus d’entrée. Les propriétés [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) et [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) de la vue doivent être définies sur `true` pour obtenir le focus d’entrée.

Deux méthodes, une propriété en lecture seule et deux événements sont impliqués avec focus d’entrée. Ils sont tous définis par `VisualElement`:

- La méthode [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) tente de définir le focus d’entrée sur un élément et retourne `true` en cas de réussite
- La méthode [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) supprime le focus d’entrée d’un élément
- La propriété en lecture seule [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) indique si l’élément a le focus d’entrée
- L’événement [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) indique quand un élément obtient le focus d’entrée
- L’événement [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) indique lorsqu’un élément perd le focus d’entrée

### <a name="choosing-the-keyboard"></a>Choix du clavier

La classe [`InputView`](xref:Xamarin.Forms.InputView) à partir de laquelle dérivent `Entry` et `Editor` définissent une seule propriété :

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) de type [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Indique le type de clavier qui s’affiche. Certains claviers sont optimisés pour des URI ou des nombres.

La classe `Keyboard` permet de définir un clavier avec une méthode [`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) statique avec un argument de type [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags), une énumération avec les indicateurs binaires suivants :

- `None` défini sur 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) défini sur 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) défini sur 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) défini sur 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) défini sur \xFFFFFFFF

Lorsque vous utilisez la [`Editor`](xref:Xamarin.Forms.Editor) multiligne lorsqu’un paragraphe ou plus de texte est attendu, l’appel de `Keyboard.Create` est une bonne approche de la sélection d’un clavier. Pour les [`Entry`](xref:Xamarin.Forms.Entry)à une seule ligne, les propriétés en lecture seule statiques suivantes de `Keyboard` sont utiles :

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) pour les nombres positifs avec ou sans virgule décimale.

Le [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) permet de spécifier ces propriétés en XAML, comme le montre le programme [**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) .

### <a name="entry-properties-and-events"></a>Événements et propriétés de l’entrée

Le [`Entry`](xref:Xamarin.Forms.Entry) à une seule ligne définit les propriétés suivantes :

- [`Text`](xref:Xamarin.Forms.InputView.Text) de type `string`, le texte qui apparaît dans le `Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) de type `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) de type `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) de type `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) de type `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) de type `bool`, qui entraîne le masquage des caractères
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) de type `string`, pour le texte en couleur dimly qui apparaît dans le `Entry` avant que tout soit tapé
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) de type `Color`

Le `Entry` définit également deux événements :

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) avec un objet [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) , déclenché chaque fois que la propriété `Text` change
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), déclenché lorsque l’utilisateur a terminé et que le clavier est fermé. L’utilisateur indique l’achèvement de manière spécifique à la plateforme

L’exemple [**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) illustre ces deux événements.

### <a name="the-editor-difference"></a>La différence de l’éditeur

L' [`Editor`](xref:Xamarin.Forms.Editor) multiligne définit les mêmes propriétés de `Text` et de `Font` que `Entry`, mais pas les autres propriétés. `Editor` définit également les deux mêmes propriétés que `Entry`.

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) est un programme prenant en charge les notes de forme libre qui enregistre et restaure le contenu du `Editor`.

### <a name="the-searchbar"></a>Le SearchBar

Le [`SearchBar`](xref:Xamarin.Forms.SearchBar) ne dérive pas de `InputView`, donc il n’a pas de propriété `Keyboard`. Mais il possède toutes les propriétés `Text`, `Font`et `Placeholder` que `Entry` définit. En outre, `SearchBar` définit trois propriétés supplémentaires :

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) de type `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) de type [`ICommand`](xref:System.Windows.Input.ICommand) à utiliser avec les liaisons de données et MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) de type `Object`, à utiliser avec `SearchCommand`

La plateforme spécifique Annuler bouton efface le texte. Le `SearchBar` possède également un bouton de recherche spécifique à la plateforme. La pression sur l’un de ces boutons déclenche l’un des deux événements que `SearchBar` définit :

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) accompagné d’un objet [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

L’exemple [**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) illustre l' `SearchBar`.

## <a name="date-and-time-selection"></a>Sélection de date et d’heure

Les vues [`DatePicker`](xref:Xamarin.Forms.DatePicker) et [`TimePicker`](xref:Xamarin.Forms.TimePicker) implémentent des contrôles spécifiques à la plateforme qui permettent à l’utilisateur de spécifier une date ou une heure.

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) définit quatre propriétés :

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) de type `DateTime`, initialisée au 1er janvier 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) de type `DateTime`, initialisée au 31 décembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) de type `DateTime`, initialisé à `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) de type `string`, la chaîne de mise en forme .net initialisée à « d », le modèle de date abrégée, ce qui donne un affichage de date comme « 7/20/1969 » aux États-Unis.

Vous pouvez définir les propriétés de `DateTime` en XAML en exprimant les propriétés en tant qu’éléments de propriété et en utilisant le format de date abrégée de culture (« 7/20/1969 »).   

L’exemple [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcule le nombre de jours entre deux dates sélectionnées par l’utilisateur.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Le TimePicker (ou s’agit-il d’un TimeSpanPicker ?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) définit deux propriétés et aucun événement :

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) est de type `TimeSpan` plutôt que `DateTime`, ce qui indique le temps écoulé depuis minuit
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) de type `string`, la chaîne de mise en forme .net initialisée sur « t », le modèle d’heure abrégée, ce qui donne un affichage de temps comme « 1:45 PM » aux États-Unis.

Le programme [**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) montre comment utiliser le `TimePicker` pour spécifier une heure pour une minuterie. Le programme ne fonctionne que si vous le conservez au premier plan.

**SetTimer** illustre également l’utilisation de la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) de `Page` pour afficher une boîte de message d’alerte.

## <a name="related-links"></a>Liens connexes

- [Chapitre 15 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Chapitre 15 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entrée](~/xamarin-forms/user-interface/text/entry.md)
- [Éditeur](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
