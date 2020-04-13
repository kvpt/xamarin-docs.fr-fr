---
title: Résumé du chapitre 15. L’interface interactive
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 15. L’interface interactive'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291906"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Résumé du chapitre 15. L’interface interactive

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Ce chapitre explore `View` huit dérivés qui permettent l’interaction avec l’utilisateur.

## <a name="view-overview"></a>Afficher la vue d’ensemble

Xamarin.Forms contient 20 classes instantanées `View` qui `Layout`dérivent de mais pas . Six d’entre eux ont été couverts dans des chapitres précédents :

- `Label`: [ **Chapitre 2. Anatomie d’une application**](chapter02.md)
- `BoxView`: [ **Chapitre 3. Faire défiler la pile**](chapter03.md)
- `Button`: [ **Chapitre 6. Clics sur les boutons**](chapter06.md)
- `Image`: [ **Chapitre 13. Bitmaps Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **Chapitre 13. Bitmaps Bitmaps**](chapter13.md)
- `ProgressBar`: [ **Chapitre 14. AbsoluteLayout (en)**](chapter14.md)

Les huit vues de ce chapitre permettent effectivement à l’utilisateur d’interagir avec les types de données .NET de base :

|Type de données|Les vues|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Vous pouvez considérer ces vues comme des représentations interactives visuelles des types de données sous-jacents. Ce concept est exploré davantage dans le chapitre [**suivant, chapitre 16. Liaison de données**](chapter16.md).

Les six autres points de vue sont abordés dans les chapitres suivants :

- `WebView`: [ **Chapitre 16. Liaison de données**](chapter16.md)
- `Picker`: [ **Chapitre 19. Vues de collection**](chapter19.md)
- `ListView`: [ **Chapitre 19. Vues de collection**](chapter19.md)
- `TableView`: [ **Chapitre 19. Vues de collection**](chapter19.md)
- `Map`: [ **Chapitre 28. Emplacement et cartes**](chapter28.md)
- `OpenGLView`: Non couvert dans ce livre (et pas de support pour les plates-formes Windows)

## <a name="slider-and-stepper"></a>Curseur et stepper

Les [`Slider`](xref:Xamarin.Forms.Slider) [`Stepper`](xref:Xamarin.Forms.Stepper) deux et permettent à l’utilisateur de choisir une valeur numérique à partir d’une gamme. Il `Slider` s’agit d’une gamme continue tandis que l’implique `Stepper` des valeurs discrètes.

### <a name="slider-basics"></a>Bases de curseur

Il [`Slider`](xref:Xamarin.Forms.Slider) s’agit d’une barre horizontale représentant une gamme de valeurs d’un minimum à gauche à un maximum sur la droite. Il définit trois biens publics :

- [`Value`](xref:Xamarin.Forms.Slider.Value)`double`type, valeur par défaut de 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)`double`type, valeur par défaut de 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)`double`type, valeur par défaut de 1

Les propriétés liantes qui soutiennent ces propriétés garantissent qu’elles sont cohérentes :

- Pour les trois [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) propriétés, la méthode spécifiée `Value` pour `Minimum` `Maximum`la propriété liant assure qu’il est entre et .
- La [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) méthode `MinimumProperty` `false` sur `Minimum` les rendements si est réglé `Maximum`à une `MaximumProperty`valeur supérieure ou égale à , et similaire pour . Le `false` retour `validateValue` de `ArgumentException` la méthode provoque une augmentation.

`Slider`déclenche [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) l’événement [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) avec `Value` un argument lorsque la propriété change, soit `Slider`programmatiquement ou lorsque l’utilisateur manipule le .

[**L’échantillon SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) démontre la `Slider`simple utilisation de la .

### <a name="common-pitfalls"></a>Pièges courants

Tant dans le code que `Minimum` dans `Maximum` XAML, les propriétés et les propriétés sont définies dans l’ordre que vous spécifiez. Assurez-vous d’initialiser `Maximum` ces propriétés `Minimum`de sorte que c’est toujours plus grand que . Dans le cas contraire, une exception est levée.

L’initialisation des `Slider` `Value` propriétés peut `ValueChanged` entraîner le changement de propriété et le feu de l’événement. Vous devez vous `Slider` assurer que le gestionnaire d’événements n’a pas accès aux vues qui n’ont pas encore été créées lors de l’initialisation de la page.

L’événement `ValueChanged` ne tire `Slider` pas pendant `Value` l’initialisation à moins que la propriété change. Vous pouvez `ValueChanged` appeler le gestionnaire directement à partir du code.

### <a name="slider-color-selection"></a>Sélection de couleurs slider

Le programme [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contient trois `Slider` éléments qui vous permettent de sélectionner interactivement une couleur en spécifiant ses valeurs RGB :

[![Triple capture d’écran des curseurs R G B](images/ch15fg03-small.png "RGB Sliders")](images/ch15fg03-large.png#lightbox "RGB Sliders")

[**L’échantillon TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) utilise `Slider` deux `Label` éléments `AbsoluteLayout` pour déplacer deux éléments à travers un et s’estomper l’un dans l’autre.

### <a name="the-stepper-difference"></a>La différence Stepper

Le [`Stepper`](xref:Xamarin.Forms.Stepper) définit les mêmes propriétés et événements que, `Slider` mais la `Maximum` propriété est initialisée à 100 et `Stepper` définit une quatrième propriété:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)de `double`type , parasécé à 1

Visuellement, `Stepper` le se compose de **&ndash;** **+** deux boutons étiquetés et . Presser **&ndash;** `Value` diminue `Increment` d’au `Minimum`moins. **+** Presser `Value` `Increment` augmente jusqu’à un maximum de `Maximum`.

Ceci est démontré par l’échantillon [**StepperDemo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)

## <a name="switch-and-checkbox"></a>Commutateur et CheckBox

L’utilisateur [`Switch`](xref:Xamarin.Forms.Switch) permet de spécifier une valeur Boolean.

### <a name="switch-basics"></a>Changer de base

Visuellement, `Switch` le se compose d’un bascule qui peut être éteint et allumé. La classe définit une propriété :

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)de type`bool`

`Switch`définit un événement :

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)accompagné d’un [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) objet, `IsToggled` tiré lorsque la propriété change.

Le programme [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) `Switch`démontre le .

### <a name="a-traditional-checkbox"></a>Une boîte à contrôle traditionnelle

Certains développeurs pourraient préférer `CheckBox` un `Switch`plus traditionnel à la . La bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une `CheckBox` `ContentView`classe qui dérive de . `CheckBox`est implémenté par les fichiers [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) et [CheckBox.xaml.cs.](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) `CheckBox`définit trois`Text`propriétés `FontSize`( `IsChecked`, `CheckedChanged` , et ) et un événement.

[**L’échantillon CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) `CheckBox`le démontre .

## <a name="typing-text"></a>Texte de frappe

Xamarin.Forms définit trois vues qui permettent à l’utilisateur d’entrer et d’éditer du texte :

- [`Entry`](xref:Xamarin.Forms.Entry)pour une seule ligne de texte
- [`Editor`](xref:Xamarin.Forms.Editor)pour plusieurs lignes de texte
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)pour une seule ligne de texte à des fins de recherche.

`Entry`et `Editor` dérivent de [`InputView`](xref:Xamarin.Forms.InputView), `View`qui dérive de . `SearchBar`dérive directement `View`de .

### <a name="keyboard-and-focus"></a>Clavier et mise au point

Sur les téléphones et les `Entry`tablettes sans claviers physiques, le , `Editor`, et `SearchBar` les éléments font tous apparaître un clavier virtuel. La présence de ce clavier à l’écran est liée à la mise au point des entrées. Une vue doit [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) avoir [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) à `true` la fois son et les propriétés définies pour obtenir l’accent sur l’entrée.

Deux méthodes, une propriété de lecture seulement, et deux événements sont impliqués dans l’accent sur les commentaires. Tous sont définis par `VisualElement`:

- La [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) méthode tente de mettre l’accent sur l’entrée à un élément et les retours `true` en cas de succès
- La [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) méthode supprime la mise au point des entrées d’un élément
- La [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) propriété de lecture seulement indique si l’élément a une orientation d’entrée
- L’événement [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) indique quand un élément obtient la mise au point des entrées
- L’événement [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) indique quand un élément perd la mise au point d’entrée

### <a name="choosing-the-keyboard"></a>Choisir le clavier

La [`InputView`](xref:Xamarin.Forms.InputView) classe `Entry` à `Editor` partir de laquelle et dérive définit une seule propriété:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)de type[`Keyboard`](xref:Xamarin.Forms.Keyboard)

Cela indique le type de clavier qui s’affiche. Certains claviers sont optimisés pour les URI ou les nombres.

La `Keyboard` classe permet de définir [`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) un clavier avec [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)une méthode statique avec un argument de type, un recensement avec les drapeaux bit suivants:

- `None`réglé à 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)réglé à 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)réglé à 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)réglé à 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)set à 'xFFFFFFFFFF

Lorsque vous utilisez [`Editor`](xref:Xamarin.Forms.Editor) le multiligne lorsqu’un `Keyboard.Create` paragraphe ou plus de texte est attendu, l’appel est une bonne approche pour sélectionner un clavier. Pour la seule [`Entry`](xref:Xamarin.Forms.Entry)ligne, les propriétés `Keyboard` statiques suivantes sont utiles :

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)pour les chiffres positifs avec ou sans décimale.

Le [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) permet de spécifier ces propriétés dans XAML comme démontré par le programme [**EntryKeyboards.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)

### <a name="entry-properties-and-events"></a>Propriétés et événements d’entrée

La seule [`Entry`](xref:Xamarin.Forms.Entry) ligne définit les propriétés suivantes :

- [`Text`](xref:Xamarin.Forms.InputView.Text)de `string`type, le texte qui apparaît dans le`Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)de type`Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)de type`string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize)de type`double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)de type`FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword)de `bool`type , ce qui provoque des caractères à masquer
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)de `string`type , pour le texte `Entry` faiblement coloré qui apparaît dans le avant quoi que ce soit est tapé
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)de type`Color`

Le `Entry` définit également deux événements :

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)avec [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) un objet, `Text` tiré chaque fois que la propriété change
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), tiré lorsque l’utilisateur est fini et le clavier est rejeté. L’utilisateur indique l’achèvement d’une manière spécifique à la plate-forme

[**L’échantillon QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) démontre ces deux événements.

### <a name="the-editor-difference"></a>La différence de l’éditeur

La [`Editor`](xref:Xamarin.Forms.Editor) multiline définit `Text` les `Font` mêmes `Entry` propriétés que les autres propriétés, mais pas les autres. `Editor`définit également les deux `Entry`mêmes propriétés que .

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) est un programme de prise de notes gratuit qui `Editor`permet d’économiser et de restaurer le contenu de la .

### <a name="the-searchbar"></a>Le SearchBar

Le [`SearchBar`](xref:Xamarin.Forms.SearchBar) ne dérive `InputView`pas de , `Keyboard` de sorte qu’il n’a pas de propriété. Mais il a `Text`tous `Font`les `Placeholder` , `Entry` , et les propriétés qui définit. En outre, `SearchBar` définit trois propriétés supplémentaires :

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)de type`Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)type [`ICommand`](xref:System.Windows.Input.ICommand) pour une utilisation avec des liaisons de données et MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)de `Object`type , pour une utilisation avec`SearchCommand`

Le bouton d’annulation spécifique à la plate-forme efface le texte. Le `SearchBar` dispose également d’un bouton de recherche spécifique à la plate-forme. Appuyer sur l’un ou l’autre `SearchBar` de ces boutons soulève l’un des deux événements qui définit:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)accompagné d’un [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) objet
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[**L’échantillon SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) `SearchBar`démontre le .

## <a name="date-and-time-selection"></a>Sélection des dates et de l’heure

Les [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`TimePicker`](xref:Xamarin.Forms.TimePicker) contrôles spécifiques à la plate-forme et les vues implémentent les contrôles spécifiques à la plate-forme qui permettent à l’utilisateur de spécifier une date ou une heure.

### <a name="the-datepicker"></a>Le DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker)définit quatre propriétés :

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)de `DateTime`type , parasécé au 1er janvier 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)de `DateTime`type , parasécé au 31 décembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)de `DateTime`type, parasés pour`DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)de `string`type , la chaîne de formatage .NET parasitée à "d", le modèle de date courte, résultant en un affichage de date comme "7/20/1969" aux États-Unis.

Vous pouvez `DateTime` définir les propriétés dans XAML en exprimant les propriétés comme éléments de propriété et en utilisant le format de la culture-invariante à court terme ("7/20/1969").   

[**L’échantillon DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcule le nombre de jours entre deux dates sélectionnées par l’utilisateur.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Le TimePicker (ou est-ce un TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker)définit deux propriétés et aucun événement :

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)est de `TimeSpan` type `DateTime`plutôt que , indiquant l’heure écoulée depuis minuit
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)de `string`type , la chaîne de formatage .NET initialisé à "t", le modèle de temps court, résultant en un affichage de temps comme "1:45 PM" aux États-Unis.

Le programme [**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) montre comment `TimePicker` utiliser le pour spécifier un temps pour une minuterie. Le programme ne fonctionne que si vous le gardez au premier plan.

**SetTimer** démontre également [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) l’utilisation de la méthode d’affichage d’une `Page` boîte d’alerte.

## <a name="related-links"></a>Liens connexes

- [Chapitre 15 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Échantillons du chapitre 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Curseur](~/xamarin-forms/user-interface/slider.md)
- [Entrée](~/xamarin-forms/user-interface/text/entry.md)
- [Éditeur](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
