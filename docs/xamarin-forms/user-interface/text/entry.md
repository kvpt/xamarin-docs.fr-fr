---
title: Xamarin.FormsMention
description: Cet article explique comment utiliser la Xamarin.Forms classe Entry pour accepter une entrée de texte ou de mot de passe sur une seule ligne dans une application.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3738a0fa3519f18864c2430430a6716bed5be130
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918224"
---
# <a name="no-locxamarinforms-entry"></a>Xamarin.FormsMention

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) Est utilisé pour l’entrée de texte sur une seule ligne. `Entry`, Comme la [`Editor`](xref:Xamarin.Forms.Editor) vue, prend en charge plusieurs types de claviers. En outre, `Entry` peut être utilisé comme champ de mot de passe.

## <a name="set-and-read-text"></a>Définir et lire du texte

`Entry`, Comme d’autres affichages de présentation de texte, expose la [`Text`](xref:Xamarin.Forms.InputView.Text) propriété. Cette propriété peut être utilisée pour définir et lire le texte présenté par le `Entry` . L’exemple suivant illustre la définition `Text` de la propriété en XAML :

```xaml
<Entry Text="I am an Entry" />
```

En C# :

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Pour lire du texte, accédez à la `Text` propriété en C# :

```csharp
var text = MyEntry.Text;
```

## <a name="set-placeholder-text"></a>Définir le texte de l’espace réservé

[`Entry`](xref:Xamarin.Forms.Entry)Peut être défini pour afficher le texte de l’espace réservé lorsqu’il ne stocke pas l’entrée d’utilisateur. Pour ce faire, affectez [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) à la propriété la valeur `string` et est souvent utilisé pour indiquer le type de contenu approprié pour le `Entry` . En outre, la couleur du texte de l’espace réservé peut être contrôlée en affectant à la propriété la valeur [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> La largeur d’un `Entry` peut être définie en définissant sa `WidthRequest` propriété. Ne dépendez pas de la largeur d’un `Entry` en cours de définition en fonction de la valeur de sa `Text` propriété.

## <a name="prevent-text-entry"></a>Empêcher la saisie de texte

Les utilisateurs peuvent empêcher la modification du texte d’un [`Entry`](xref:Xamarin.Forms.Entry) en définissant la `IsReadOnly` propriété, qui a la valeur par défaut `false` , pour `true` :

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` propriété ne modifie pas l’apparence visuelle d’un [`Entry`](xref:Xamarin.Forms.Entry) , contrairement `IsEnabled` à la propriété qui modifie également l’apparence visuelle du `Entry` en gris.

## <a name="transform-text"></a>Transformer le texte

Un [`Entry`](xref:Xamarin.Forms.Entry) peut transformer la casse de son texte, stocké dans la `Text` propriété, en affectant `TextTransform` à la propriété une valeur de l' `TextTransform` énumération. Cette énumération a quatre valeurs :

- `None`indique que le texte ne sera pas transformé.
- `Default`indique que le comportement par défaut de la plateforme sera utilisé. C’est la valeur par défaut de la propriété `TextTransform`.
- `Lowercase`indique que le texte sera transformé en minuscules.
- `Uppercase`indique que le texte sera transformé en majuscules.

L’exemple suivant illustre la transformation de texte en majuscules :

```xaml
<Entry Text="This text will be displayed in uppercase."
       TextTransform="Uppercase" />
```

Le code C# équivalent est :

```csharp
Entry entry = new Entry
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>Limiter la longueur d’entrée

La [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) propriété peut être utilisée pour limiter la longueur d’entrée autorisée pour [`Entry`](xref:Xamarin.Forms.Entry) . Cette propriété doit être définie sur un entier positif :

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Une [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valeur de propriété de 0 indique qu’aucune entrée n’est autorisée, et la valeur `int.MaxValue` , qui est la valeur par défaut pour un [`Entry`](xref:Xamarin.Forms.Entry) , indique qu’il n’existe aucune limite effective quant au nombre de caractères pouvant être entrés.

## <a name="character-spacing"></a>Espacement des caractères

L’espacement des caractères peut être appliqué à un [`Entry`](xref:Xamarin.Forms.Entry) en affectant `Entry.CharacterSpacing` à la propriété une `double` valeur :

```xaml
<Entry ...
       CharacterSpacing="10" />
```

Le code C# équivalent est :

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

En conséquence, les caractères du texte affiché par le sont espacés d' [`Entry`](xref:Xamarin.Forms.Entry) `CharacterSpacing` unités indépendantes du périphérique.

> [!NOTE]
> La `CharacterSpacing` valeur de la propriété est appliquée au texte affiché par `Text` les `Placeholder` Propriétés et.

## <a name="password-fields"></a>Champs de mot de passe

`Entry`fournit la `IsPassword` propriété. Lorsque `IsPassword` a `true` la valeur, le contenu du champ est présenté sous la forme de cercles noirs :

En XAML :

```xaml
<Entry IsPassword="true" />
```

En C# :

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Exemple de IsPassword d’entrée](entry-images/password.png)

Les espaces réservés peuvent être utilisés avec des instances de `Entry` qui sont configurées en tant que champs de mot de passe :

En XAML :

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

En C# :

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Exemple d’entrée IsPassword et d’espace réservé](entry-images/passwordplaceholder.png)

## <a name="set-the-cursor-position-and-text-selection-length"></a>Définir la position du curseur et la longueur de la sélection du texte

La [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) propriété peut être utilisée pour retourner ou définir la position à laquelle le caractère suivant sera inséré dans la chaîne stockée dans la [`Text`](xref:Xamarin.Forms.InputView.Text) propriété :

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

La valeur par défaut de la [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) propriété est 0, ce qui indique que le texte est inséré au début de `Entry` .

En outre, la [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) propriété peut être utilisée pour retourner ou définir la longueur de la sélection de texte dans le `Entry` :

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

La valeur par défaut de la [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) propriété est 0, ce qui indique qu’aucun texte n’est sélectionné.

## <a name="display-a-clear-button"></a>Afficher un bouton Effacer

La `ClearButtonVisibility` propriété peut être utilisée pour contrôler si une [`Entry`](xref:Xamarin.Forms.Entry) affiche un bouton Effacer, ce qui permet à l’utilisateur d’effacer le texte. Cette propriété doit être définie sur un `ClearButtonVisibility` membre de l’énumération :

- `Never`indique qu’un bouton Effacer ne sera jamais affiché. Il s'agit de la valeur par défaut de la propriété `Entry.ClearButtonVisibility`.
- `WhileEditing`indique qu’un bouton Effacer s’affichera dans le [`Entry`](xref:Xamarin.Forms.Entry) , alors qu’il a le focus et le texte.

L’exemple suivant illustre la définition de la propriété en XAML :

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

Le code C# équivalent est :

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

Les captures d’écran suivantes montrent une [`Entry`](xref:Xamarin.Forms.Entry) avec le bouton Effacer activé :

![Capture d’écran d’une entrée avec un bouton Effacer, sur iOS et Android](entry-images/entry-clear-button.png)

## <a name="customize-the-keyboard"></a>Personnaliser le clavier

Le clavier présenté lorsque les utilisateurs interagissent avec un [`Entry`](xref:Xamarin.Forms.Entry) peut être défini par programmation via la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propriété, à l’une des propriétés suivantes à partir de la [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe :

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat): utilisé pour le texte et les emplacements où les Emoji sont utiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default): clavier par défaut.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)– utilisé lors de la saisie des adresses e-mail.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric): utilisé lors de l’entrée de nombres.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain): utilisé lors de l’entrée de texte, sans aucun [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) spécifié.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)– utilisé lors de l’entrée de numéros de téléphone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text): utilisé lors de la saisie de texte.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url): permet d’entrer des chemins de fichier & des adresses Web.

Cela peut être accompli en XAML de la façon suivante :

```xaml
<Entry Keyboard="Chat" />
```

Le code C# équivalent est :

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Vous trouverez des exemples de chaque clavier dans notre référentiel [Recipes](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

La [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe a également une [`Create`](xref:Xamarin.Forms.Keyboard.Create*) méthode de fabrique qui peut être utilisée pour personnaliser un clavier en spécifiant le comportement de mise en majuscules, de vérification orthographique et de suggestion. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)les valeurs d’énumération sont spécifiées comme arguments de la méthode, avec un personnalisé `Keyboard` qui est retourné. L’énumération `KeyboardFlags` contient les valeurs suivantes :

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None): aucune fonctionnalité n’est ajoutée au clavier.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence): indique que la première lettre du premier mot de chaque phrase entrée est automatiquement mise en majuscules.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck): indique que l’orthographe est effectuée sur le texte entré.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions): indique que les saisies semi-automatiques de mot seront proposées dans le texte entré.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord): indique que la première lettre de chaque mot sera automatiquement mise en majuscules.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter): indique que chaque caractère sera automatiquement mis en majuscules.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone): indique qu’aucune mise en majuscule automatique n’aura lieu.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All): indique que l’orthographe, la saisie semi-automatique des mots et la mise en majuscule des phrases se produisent dans le texte entré.

L’exemple de code XAML suivant montre comment personnaliser la valeur par défaut [`Keyboard`](xref:Xamarin.Forms.Keyboard) pour proposer des saisies semi-automatiques de mots et mettre en majuscules chaque caractère entré :

```xaml
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

Le code C# équivalent est :

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="customize-the-return-key"></a>Personnaliser la clé de retour

L’apparence de la touche retour sur le clavier logiciel, qui est affichée lorsqu’un [`Entry`](xref:Xamarin.Forms.Entry) a le focus, peut être personnalisée en affectant [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) à la propriété une valeur de l' [`ReturnType`](xref:Xamarin.Forms.ReturnType) énumération :

- [`Default`](xref:Xamarin.Forms.ReturnType.Default): indique qu’aucune clé de retour spécifique n’est requise et que la valeur par défaut de la plateforme est utilisée.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done): indique une clé de retour « Done ».
- [`Go`](xref:Xamarin.Forms.ReturnType.Go): indique une clé de retour « Go ».
- [`Next`](xref:Xamarin.Forms.ReturnType.Next): indique une clé de retour « Next ».
- [`Search`](xref:Xamarin.Forms.ReturnType.Search): indique une clé de retour « Search ».
- [`Send`](xref:Xamarin.Forms.ReturnType.Send): indique une clé de retour « Send ».

L’exemple de code XAML suivant montre comment définir la clé de retour :

```xaml
<Entry ReturnType="Send" />
```

Le code C# équivalent est :

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> L’apparence exacte de la clé de retour dépend de la plateforme. Sur iOS, la touche retour est un bouton basé sur du texte. Toutefois, sur les plateformes Android et Windows universelles, la touche retour est un bouton basé sur une icône.

Lorsque la touche retour est enfoncée, l' [`Completed`](xref:Xamarin.Forms.Entry.Completed) événement se déclenche et tout `ICommand` spécifié par la [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) propriété est exécuté. En outre, tout `object` spécifié par la [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriété est passé à `ICommand` en tant que paramètre. Pour plus d’informations sur les commandes, consultez [Interface de commande](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="enable-and-disable-spell-checking"></a>Activer et désactiver la vérification orthographique

La [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété contrôle si la vérification orthographique est activée. Par défaut, la propriété a la valeur `true` . À mesure que l’utilisateur entre du texte, des fautes d’orthographe sont indiquées.

Toutefois, pour certains scénarios de saisie de texte, tels que la saisie d’un nom d’utilisateur, la vérification orthographique fournit une expérience négative et doit être désactivée en affectant à la propriété la valeur [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) `false` :

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Lorsque la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété a la valeur `false` et qu’un clavier personnalisé n’est pas utilisé, le vérificateur d’orthographe natif est désactivé. Toutefois, si un [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été défini et que désactive la vérification de l’orthographe, par exemple [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , la `IsSpellCheckEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la vérification de l’orthographe pour un `Keyboard` qui le désactive explicitement.

## <a name="enable-and-disable-text-prediction"></a>Activer et désactiver la prédiction de texte

La [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriété contrôle si la prédiction de texte et la correction de texte automatique sont activées. Par défaut, la propriété a la valeur `true` . Lorsque l’utilisateur entre du texte, des prédictions de mot sont présentées.

Toutefois, pour certains scénarios de saisie de texte, tels que l’entrée d’un nom d’utilisateur, la prédiction de texte et la correction automatique de texte, doivent être désactivées en affectant à la propriété la valeur [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) `false` :

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Lorsque la [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriété a la valeur `false` et qu’un clavier personnalisé n’est pas utilisé, la prédiction de texte et la correction automatique du texte sont désactivées. Toutefois, si un [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été défini et que la prédiction de texte est désactivée, la `IsTextPredictionEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la prédiction de texte pour un `Keyboard` qui le désactive explicitement.

## <a name="colors"></a>Couleurs

L’entrée peut être définie pour utiliser une couleur d’arrière-plan et de texte personnalisée via les propriétés pouvant être liées suivantes :

- **TextColor** &ndash; définit la couleur du texte.
- **BackgroundColor** &ndash; définit la couleur affichée derrière le texte.

Une attention particulière est nécessaire pour s’assurer que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour les couleurs de texte et d’arrière-plan, vous devez souvent définir les deux si vous en définissez une.

Utilisez le code suivant pour définir la couleur de texte d’une entrée :

En XAML :

```xaml
<Entry TextColor="Green" />
```

En C# :

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Exemple de TextColor d’entrée](entry-images/textcolor.png)

Notez que l’espace réservé n’est pas affecté par le spécifié `TextColor` .

Pour définir la couleur d’arrière-plan en XAML :

```xaml
<Entry BackgroundColor="#2c3e50" />
```

En C# :

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![Exemple d’entrée BackgroundColor](entry-images/textbackgroundcolor.png)

Veillez à vous assurer que les couleurs de texte et d’arrière-plan que vous choisissez sont utilisables sur chaque plateforme et ne masquez aucun texte d’espace réservé.

## <a name="events-and-interactivity"></a>Événements et interactivité

L’entrée expose deux événements :

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)&ndash;déclenché lorsque le texte est modifié dans l’entrée. Fournit le texte avant et après la modification.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)&ndash;déclenché lorsque l’utilisateur a terminé l’entrée en appuyant sur la touche retour sur le clavier.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, dont [`Entry`](xref:Xamarin.Forms.Entry) hérite, possède également les [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) événements et.

### <a name="completed"></a>Completed

L' `Completed` événement est utilisé pour réagir à l’achèvement d’une interaction avec une entrée. `Completed`est déclenché lorsque l’utilisateur termine l’entrée par un champ en appuyant sur la touche retour sur le clavier (ou en appuyant sur la touche Tab sur UWP). Le gestionnaire de l’événement est un gestionnaire d’événements générique, qui prend l’expéditeur et `EventArgs` :

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

L’événement terminé peut être abonné à en XAML :

```xaml
<Entry Completed="Entry_Completed" />
```

et C# :

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Une fois l' [`Completed`](xref:Xamarin.Forms.Entry.Completed) événement déclenché, tout `ICommand` spécifié par la [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) propriété est exécuté, avec le `object` spécifié par la [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriété passée à l' `ICommand` .

### <a name="textchanged"></a>TextChanged

L' `TextChanged` événement est utilisé pour réagir à une modification du contenu d’un champ.

`TextChanged`est déclenché chaque fois que le `Text` de `Entry` change. Le gestionnaire de l’événement prend une instance de `TextChangedEventArgs` . `TextChangedEventArgs`fournit l’accès aux valeurs anciennes et nouvelles de `Entry` `Text` via les `OldTextValue` Propriétés et `NewTextValue` :

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

L' `TextChanged` événement peut être abonné à en XAML :

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

et C# :

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>Liens associés

- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API Entry](xref:Xamarin.Forms.Entry)
