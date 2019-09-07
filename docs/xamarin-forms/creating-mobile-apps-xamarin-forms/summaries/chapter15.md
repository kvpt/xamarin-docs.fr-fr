---
title: Résumé du chapitre 15. L’interface interactive
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 15. L’interface interactive'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1c30f87b9173d2ca4de0b2d91ad13145031e9b0a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760754"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Résumé du chapitre 15. L’interface interactive

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Ce chapitre explore huit `View` dérivés qui permettent d’interagir avec l’utilisateur.

## <a name="view-overview"></a>Vue d’ensemble de la vue

Xamarin.Forms contient 20 instanciables classes dérivées de `View` mais pas `Layout`. Six d'entre eux ont été traités dans les chapitres précédents :

- `Label`: [**Chapitre 2. Anatomie d’une application**](chapter02.md)
- `BoxView`: [**Chapitre 3. Défilement de la pile**](chapter03.md)
- `Button`: [**Chapitre 6. Clics de bouton**](chapter06.md)
- `Image`: [**Chapitre 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [**Chapitre 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [**Chapitre 14. DispositionAbsolue**](chapter14.md)

Les huit vues dans ce chapitre autoriser efficacement l’utilisateur d’interagir avec les types de données de base .NET :

|Type de données|Affichages|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Vous pouvez considérer de ces vues en tant que représentations interactives visuelles des types de données sous-jacente. Ce concept est expliquée plus dans le chapitre suivant, [ **chapitre 16. Liaison de données**](chapter16.md).

Les vues de six restants sont traités dans les chapitres suivants :

- `WebView`: [**Chapitre 16. Liaison de données**](chapter16.md)
- `Picker`: [**Chapitre 19. Vues de collection**](chapter19.md)
- `ListView`: [**Chapitre 19. Vues de collection**](chapter19.md)
- `TableView`: [**Chapitre 19. Vues de collection**](chapter19.md)
- `Map`: [**Chapitre 28. Emplacement et mappages**](chapter28.md)
- `OpenGLView`: Non couverts dans ce manuel (et aucune prise en charge des plateformes Windows)

## <a name="slider-and-stepper"></a>Curseur et l’exécution pas à pas

Les deux [ `Slider` ](xref:Xamarin.Forms.Slider) et [ `Stepper` ](xref:Xamarin.Forms.Stepper) autoriser l’utilisateur de choisir une valeur numérique à partir d’une plage. Le `Slider` est une plage continue, tandis que le `Stepper` implique des valeurs discrètes.

### <a name="slider-basics"></a>Principes de base de curseur

Le [ `Slider` ](xref:Xamarin.Forms.Slider) est une barre représentant une plage de valeurs à partir d’un minimum de gauche à un maximum sur la droite horizontale. Il définit trois propriétés publiques :

- [`Value`](xref:Xamarin.Forms.Slider.Value) de type `double`, valeur 0 par défaut
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) de type `double`, valeur 0 par défaut
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) de type `double`, la valeur 1 par défaut

Les propriétés pouvant être liées qui assortir ces propriétés de vous assurer qu’elles sont cohérentes :

- Pour toutes les trois propriétés, le [ `coerceValue` ](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) méthode spécifiée pour la propriété pouvant être liée garantit que `Value` entre `Minimum` et `Maximum`.
- Le [ `validateValue` ](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) méthode sur `MinimumProperty` retourne `false` si `Minimum` est définie sur une valeur supérieure ou égale à `Maximum`et il est similaire pour `MaximumProperty`. Retour `false` à partir de la `validateValue` causes de la méthode un `ArgumentException` à signaler.

`Slider` se déclenche le [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) événement avec un [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) argument lorsque la `Value` propriété change, par programme ou lorsque l’utilisateur manipule le `Slider`.

Le [ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) exemple illustre l’utilisation simple du `Slider`.

### <a name="common-pitfalls"></a>Pièges courants

À la fois dans le code et XAML, le `Minimum` et `Maximum` propriétés sont définies dans l’ordre que vous spécifiez. Veillez à initialiser ces propriétés afin que `Maximum` est toujours supérieure à `Minimum`. Dans le cas contraire, une exception est levée.

L’initialisation de la `Slider` propriétés peuvent entraîner la `Value` propriété à modifier et le `ValueChanged` événement à déclencher. Vous devez vous assurer que le `Slider` Gestionnaire d’événements n’accède pas à des vues qui n’ont pas encore été créés pendant l’initialisation de la page.

Le `ValueChanged` événement ne se déclenche pendant `Slider` l’initialisation, sauf si le `Value` les modifications de propriété. Vous pouvez appeler la `ValueChanged` gestionnaire directement à partir du code.

### <a name="slider-color-selection"></a>Sélection de couleur de curseur

Le [ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) programme contienne trois `Slider` les éléments qui vous permettent de sélectionner une couleur de manière interactive en spécifiant ses valeurs RVB :

[![Capture d’écran de triple des curseurs de R G B](images/ch15fg03-small.png "RVB curseurs")](images/ch15fg03-large.png#lightbox "RVB curseurs")

Le [ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) exemple utilise deux `Slider` éléments à déplacer deux `Label` éléments entre un `AbsoluteLayout` et fondu dans l’autre.

### <a name="the-stepper-difference"></a>La différence de l’exécution pas à pas

Le [ `Stepper` ](xref:Xamarin.Forms.Stepper) définit les propriétés et les événements en tant que même `Slider` mais le `Maximum` propriété est initialisée à 100 et `Stepper` définit une propriété quatrième :

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) de type `double`, initialisé à 1

Visuellement, la `Stepper` se compose de deux boutons nommés **&ndash;** et **+** . En appuyant sur **&ndash;** diminue `Value` par `Increment` à un minimum de `Minimum`. En appuyant sur **+** augmente `Value` par `Increment` à un maximum de `Maximum`.

Cela est illustré par la [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) exemple.

## <a name="switch-and-checkbox"></a>Commutateur et la case à cocher

Le [ `Switch` ](xref:Xamarin.Forms.Switch) permet à l’utilisateur de spécifier une valeur booléenne.

### <a name="switch-basics"></a>Principes fondamentaux de commutateur

Visuellement, le `Switch` se compose d’un bouton bascule qui peut être désactivée puis activé. La classe définit une propriété :

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) de type `bool`

`Switch` définit un événement :

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) accompagné d’un [ `ToggledEventArgs` ](xref:Xamarin.Forms.ToggledEventArgs) objet, déclenché lorsque le `IsToggled` les modifications de propriété.

Le [ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) programme illustre le `Switch`.

### <a name="a-traditional-checkbox"></a>Une case à cocher traditionnel

Certains développeurs peuvent préférer une plus traditionnelle `CheckBox` à la `Switch`. Le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque contient un `CheckBox` classe qui dérive de `ContentView`. `CheckBox` est implémentée par le [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) et [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) fichiers. `CheckBox` définit trois propriétés (`Text`, `FontSize`, et `IsChecked`) et un `CheckedChanged` événement.

Le [ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) illustre cela `CheckBox`.

## <a name="typing-text"></a>Saisie de texte

Xamarin.Forms définit trois vues qui permettent l’utilisateur d’entrer et de modifier le texte :

- [`Entry`](xref:Xamarin.Forms.Entry) pour une seule ligne de texte
- [`Editor`](xref:Xamarin.Forms.Editor) plusieurs lignes de texte
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) pour une seule ligne de texte à des fins de recherche.

`Entry` et `Editor` dérivent [ `InputView` ](xref:Xamarin.Forms.InputView), qui dérive à son `View`. `SearchBar` dérive directement de `View`.

### <a name="keyboard-and-focus"></a>Clavier et le focus

Sur les téléphones et tablettes sans claviers physiques, le `Entry`, `Editor`, et `SearchBar` tous les éléments de provoquent un clavier virtuel à la fenêtre contextuelle. La présence de ce clavier sur l’écran concerne le focus d’entrée. Une vue doit avoir à la fois son [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) et [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriétés définies sur `true` pour obtenir le focus d’entrée.

Deux méthodes, une propriété en lecture seule et deux événements sont impliqués avec focus d’entrée. Elles sont définies par `VisualElement`:

- Le [ `Focus` ](xref:Xamarin.Forms.VisualElement.Focus) méthode tente de définir le focus d’entrée à un élément et retourne `true` en cas de réussite
- Le [ `Unfocus` ](xref:Xamarin.Forms.VisualElement.Unfocus) méthode supprime le focus d’entrée à partir d’un élément
- Le [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) propriété en lecture seule indique si l’élément a le focus d’entrée
- Le [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) événements indique quand un élément obtient le focus d’entrée
- Le [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused) événements indique quand un élément perd le focus d’entrée

### <a name="choosing-the-keyboard"></a>Choix du clavier

Le [ `InputView` ](xref:Xamarin.Forms.InputView) classe à partir de laquelle `Entry` et `Editor` dériver ne définit qu’une seule propriété :

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) de type [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Indique le type de clavier qui s’affiche. Certains claviers sont optimisés pour des URI ou des nombres.

Le `Keyboard` classe permet la définition d’un clavier avec un ligne statique [ `Keyboard.Create` ](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) méthode avec un argument de type [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags), une énumération avec les indicateurs de bits suivants :

- `None` la valeur 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) la valeur 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) la valeur est 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) la valeur 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) la valeur \xFFFFFFFF

Lors de l’utilisation de la propriété multiline [ `Editor` ](xref:Xamarin.Forms.Editor) quand au moins un paragraphe de texte est attendu, l’appel `Keyboard.Create` constitue une bonne approche à la sélection d’un clavier. Pour la ligne unique [ `Entry` ](xref:Xamarin.Forms.Entry), les propriétés statiques suivantes en lecture seule de `Keyboard` sont utiles :

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) pour les nombres positifs, avec ou sans virgule décimale.

Le [ `KeyboardTypeConverter` ](xref:Xamarin.Forms.KeyboardTypeConverter) permet de spécifier ces propriétés dans XAML, comme illustré par la [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programme.

### <a name="entry-properties-and-events"></a>Événements et propriétés de l’entrée

La ligne unique [ `Entry` ](xref:Xamarin.Forms.Entry) définit les propriétés suivantes :

- [`Text`](xref:Xamarin.Forms.Entry.Text) de type `string`, le texte qui apparaît dans le `Entry`
- [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) de type `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) de type `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) de type `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) de type `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) de type `bool`, ce qui entraîne des caractères à masquer
- [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) de type `string`, pour les couleur atténuées texte qui apparaît dans le `Entry` avant que quoi que ce soit est typé
- [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) de type `Color`

Le `Entry` définit également deux événements :

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) avec un [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) objet, déclenché chaque fois que le `Text` les modifications de propriété
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), déclenché lorsque l’utilisateur est terminée et que le clavier est fermé. L’utilisateur indique l’achèvement de manière spécifique à la plateforme

Le [ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) illustre ces deux événements.

### <a name="the-editor-difference"></a>La différence de l’éditeur

La propriété multiline [ `Editor` ](xref:Xamarin.Forms.Editor) définit les mêmes `Text` et `Font` propriétés comme `Entry` mais pas les autres propriétés. `Editor` définit également les deux mêmes propriétés que `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) est un programme de prise de notes de forme libre qui enregistre et restaure le contenu de la `Editor`.

### <a name="the-searchbar"></a>Le SearchBar

Le [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ne dérive pas de `InputView`, donc il n’a pas un `Keyboard` propriété. Mais il dispose de tous les `Text`, `Font`, et `Placeholder` propriétés qui `Entry` définit. En outre, `SearchBar` définit trois propriétés supplémentaires :

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) de type `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) de type [ `ICommand` ](xref:System.Windows.Input.ICommand) pour une utilisation avec les liaisons de données et MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) de type `Object`, pour une utilisation avec `SearchCommand`

La plateforme spécifique Annuler bouton efface le texte. Le `SearchBar` comporte également un bouton de recherche spécifiques à la plateforme. En appuyant sur un de ces boutons déclenche un des deux événements qui `SearchBar` définit :

- [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) accompagné d’un [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) objet
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

Le [ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) exemple illustre la `SearchBar`.

## <a name="date-and-time-selection"></a>Sélection de date et d’heure

Le [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) et [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) vues instaurez des contrôles spécifiques à une plateforme permettant aux utilisateurs de spécifier une date ou heure.

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) définit les quatre propriétés :

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) de type `DateTime`, initialisé pour le 1er janvier 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) de type `DateTime`, initialisé pour le 31 décembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) de type `DateTime`, initialisée à `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) de type `string`, .NET mise en forme de chaîne initialisé à « d », le modèle de date courte, ce qui entraîne un affichage de la date comme « 7/20/1969 » dans le fuseau horaire.

Vous pouvez définir le `DateTime` propriétés dans XAML en exprimant les propriétés en tant qu’éléments de propriété et à l’aide de la culture dite indifférente courte-date de format (« 20/7/1969 »).   

Le [ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) exemple calcule le nombre de jours entre deux dates sélectionnées par l’utilisateur.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Le TimePicker (ou s’agit-il d’un TimeSpanPicker ?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) définit deux propriétés et aucun événement :

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) est de type `TimeSpan` plutôt que `DateTime`, indiquant le temps écoulé depuis minuit
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) de type `string`, la mise en forme de chaîne initialisée à « t », le modèle d’heure courte, ce qui entraîne un affichage de l’heure comme « 1:45 PM » dans le fuseau horaire de .NET.

Le [ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) programme montre comment utiliser le `TimePicker` pour spécifier une heure d’une minuterie. Le programme ne fonctionne que si vous le conservez au premier plan.

**SetTimer** montre également comment utiliser le [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) méthode de `Page` pour afficher un message d’alerte.

## <a name="related-links"></a>Liens connexes

- [Chapitre 15 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Exemples de chapitre 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entrée](~/xamarin-forms/user-interface/text/entry.md)
- [Éditeur](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
