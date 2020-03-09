---
title: Entrée de Xamarin.Forms
description: Cet article explique comment utiliser la classe d’entrée de Xamarin.Forms pour accepter la ligne de texte unique ou une entrée de mot de passe dans une application.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: c7daad8898d3048f10c9489ee4e2c78f147c6e52
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914619"
---
# <a name="xamarinforms-entry"></a>Entrée de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrée de texte ou de mot de passe sur une seule ligne_

Le [`Entry`](xref:Xamarin.Forms.Entry) Xamarin. Forms est utilisé pour l’entrée de texte sur une seule ligne. La `Entry`, comme la vue [`Editor`](xref:Xamarin.Forms.Editor) , prend en charge plusieurs types de claviers. En outre, le `Entry` peut être utilisé comme champ de mot de passe.

## <a name="display-customization"></a>Personnalisation de l’affichage

### <a name="setting-and-reading-text"></a>Définition et de lecture du texte

Le `Entry`, comme d’autres affichages de présentation de texte, expose la propriété [`Text`](xref:Xamarin.Forms.InputView.Text) . Cette propriété peut être utilisée pour définir et lire le texte présenté par le `Entry`. L’exemple suivant illustre la définition de la propriété `Text` en XAML :

```xaml
<Entry Text="I am an Entry" />
```

En C# :

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Pour lire du texte, accédez à la propriété C#`Text` dans :

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Texte d’espace réservé de paramètre

Le [`Entry`](xref:Xamarin.Forms.Entry) peut être défini pour afficher le texte de l’espace réservé lorsqu’il ne stocke pas l’entrée d’utilisateur. Pour ce faire, affectez à la propriété [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) la valeur d’une `string`, qui est souvent utilisée pour indiquer le type de contenu approprié pour le `Entry`. En outre, vous pouvez contrôler la couleur du texte de l’espace réservé en affectant à la propriété [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) la valeur [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> La largeur d’un `Entry` peut être définie en définissant sa propriété `WidthRequest`. Ne dépendez pas de la largeur d’un `Entry` qui est défini en fonction de la valeur de sa propriété `Text`.

### <a name="preventing-text-entry"></a>Empêcher la saisie de texte

Les utilisateurs ne peuvent pas modifier le texte d’une [`Entry`](xref:Xamarin.Forms.Entry) en définissant la propriété `IsReadOnly`, qui a une valeur par défaut de `false`, pour `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> La propriété `IsReadonly` ne modifie pas l’apparence visuelle d’une [`Entry`](xref:Xamarin.Forms.Entry), contrairement à la propriété `IsEnabled` qui modifie également l’apparence visuelle du `Entry` en gris.

### <a name="limiting-input-length"></a>Limiter la longueur d’entrée

La propriété [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) peut être utilisée pour limiter la longueur d’entrée autorisée pour le [`Entry`](xref:Xamarin.Forms.Entry). Cette propriété doit être définie à un entier positif :

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Une [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valeur de propriété de 0 indique qu’aucune entrée ne sera autorisée, et une valeur de `int.MaxValue`, qui est la valeur par défaut d’un [`Entry`](xref:Xamarin.Forms.Entry), indique qu’il n’existe aucune limite effective quant au nombre de caractères pouvant être entrés.

### <a name="character-spacing"></a>Espacement des caractères

L’espacement des caractères peut être appliqué à une [`Entry`](xref:Xamarin.Forms.Entry) en affectant à la propriété `Entry.CharacterSpacing` une valeur `double` :

```xaml
<Entry ...
       CharacterSpacing="10" />
```

Le code C# équivalent est :

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

Le résultat est que les caractères du texte affiché par le [`Entry`](xref:Xamarin.Forms.Entry) sont espacés `CharacterSpacing` des unités indépendantes du périphérique.

> [!NOTE]
> La valeur de la propriété `CharacterSpacing` est appliquée au texte affiché par les propriétés `Text` et `Placeholder`.

### <a name="password-fields"></a>Champs de mot de passe

`Entry` fournit la propriété `IsPassword`. Lorsque `IsPassword` est `true`, le contenu du champ sera présenté sous la forme de cercles noirs :

Dans XAML :

```xaml
<Entry IsPassword="true" />
```

En C# :

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Exemple de IsPassword d’entrée](entry-images/password.png)

Les espaces réservés peuvent être utilisés avec des instances de `Entry` qui sont configurées en tant que champs de mot de passe :

Dans XAML :

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

En C# :

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Exemple d’entrée IsPassword et d’espace réservé](entry-images/passwordplaceholder.png)

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Définition de la Position du curseur et la longueur de sélection de texte

La propriété [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) peut être utilisée pour retourner ou définir la position à laquelle le caractère suivant sera inséré dans la chaîne stockée dans la propriété [`Text`](xref:Xamarin.Forms.InputView.Text) :

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

La valeur par défaut de la propriété [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) est 0, ce qui signifie que le texte est inséré au début du `Entry`.

En outre, la propriété [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) peut être utilisée pour retourner ou définir la longueur de la sélection de texte dans le `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

La valeur par défaut de la propriété [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) est 0, ce qui indique qu’aucun texte n’est sélectionné.

### <a name="displaying-a-clear-button"></a>Affichage d’un bouton Effacer

La propriété `ClearButtonVisibility` peut être utilisée pour contrôler si une [`Entry`](xref:Xamarin.Forms.Entry) affiche un bouton Effacer, ce qui permet à l’utilisateur d’effacer le texte. Cette propriété doit être définie sur un `ClearButtonVisibility` membre de l’énumération :

- `Never` indique qu’un bouton Effacer ne sera jamais affiché. Il s'agit de la valeur par défaut de la propriété `Entry.ClearButtonVisibility`.
- `WhileEditing` indique qu’un bouton Effacer sera affiché dans le [`Entry`](xref:Xamarin.Forms.Entry), alors qu’il a le focus et le texte.

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

### <a name="customizing-the-keyboard"></a>Personnalisation du clavier

Le clavier présenté lorsque les utilisateurs interagissent avec un [`Entry`](xref:Xamarin.Forms.Entry) peut être défini par programme via la propriété [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , à l’une des propriétés suivantes à partir de la classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) : utilisé pour envoyer des SMS et place les emojis là où ils sont utiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) : le raccourci clavier par défaut.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) : utilisé lorsque des adresses e-mail sont saisies.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) : utilisé lorsque des nombres sont saisis.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) : utilisé lorsque du texte est saisi sans que des [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) ne soient spécifiés.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) : utilisé lorsque des numéros de téléphone sont saisis.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) : utilisé lorsque du texte est saisi.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) : utilisé pour entrer des chemins d’accès de fichier et des URL.

Cela peut être accompli en XAML de la façon suivante :

```xaml
<Entry Keyboard="Chat" />
```

Le code C# équivalent est :

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Vous trouverez des exemples de chaque clavier dans notre référentiel [Recipes](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

La classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) dispose également d’une méthode de fabrique [`Create`](xref:Xamarin.Forms.Keyboard.Create*) qui peut être utilisée pour personnaliser un clavier en spécifiant le comportement de mise en majuscules, de vérification orthographique et de suggestion. Les valeurs d’énumération [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) sont spécifiées en tant qu’arguments pour la méthode en retournant un `Keyboard` personnalisé. L’énumération `KeyboardFlags` contient les valeurs suivantes :

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) : aucune fonctionnalité n’est ajoutée au clavier.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) : indique que les premières lettres des premiers mots de chaque phrase saisie sont automatiquement mises en majuscules.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) : indique que la vérification orthographique sera effectuée sur le texte saisi.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) : indique que la saisie semi-automatique des mots sera proposée pour le texte saisi.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) : indique que la première lettre de chaque mot est automatiquement mise en majuscules.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) : indique que tous les caractères sont automatiquement mis en majuscules.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) : indique qu’aucune lettre n’est mise en majuscules automatiquement.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) : indique que la vérification orthographique, la saisie semi-automatique des mots et la mise en majuscules de la première lettre de la phrase seront appliquées au texte saisi.

L’exemple de code XAML suivant montre comment personnaliser le [`Keyboard`](xref:Xamarin.Forms.Keyboard) par défaut afin de proposer des saisies semi-automatiques et de mettre en majuscules chaque caractère saisi :

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

#### <a name="customizing-the-return-key"></a>Personnalisation de la touche Retour

L’apparence de la touche retour sur le clavier logiciel, qui est affichée lorsqu’un [`Entry`](xref:Xamarin.Forms.Entry) a le focus, peut être personnalisée en affectant à la propriété [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) la valeur de l’énumération [`ReturnType`](xref:Xamarin.Forms.ReturnType) :

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) : indique qu’aucune clé de retour spécifique n’est requise et que la valeur par défaut de la plateforme est utilisée.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) : indique une clé de retour « Done ».
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) : indique une clé de retour « Go ».
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) : indique une clé de retour « Next ».
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) : indique une clé de retour « Search ».
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) : indique une clé de retour « Send ».

L’exemple XAML suivant montre comment définir la touche Retour :

```xaml
<Entry ReturnType="Send" />
```

Le code C# équivalent est :

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> L’apparence exacte de la touche retour dépend de la plateforme. Sur iOS, la touche retour est un bouton textuel. Toutefois, sur les plateformes Windows universelles et Android, la touche retour est un bouton basé sur l’icône.

Lorsque vous appuyez sur la touche retour, l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed) se déclenche et toute `ICommand` spécifiée par la propriété [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) est exécutée. En outre, les `object` spécifiés par la propriété [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) sont passés à la `ICommand` en tant que paramètre. Pour plus d’informations sur les commandes, consultez [Interface de commande](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Activation et désactivation de la vérification orthographique

La propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) contrôle si la vérification orthographique est activée. Par défaut, la propriété a la valeur `true`. Comme l’utilisateur entre du texte, les fautes d’orthographe sont indiquées.

Toutefois, pour certains scénarios de saisie de texte, tels que la saisie d’un nom d’utilisateur, la vérification orthographique fournit une expérience négative et doit être désactivée en affectant à la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) la valeur `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Lorsque la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) est définie sur `false`et qu’un clavier personnalisé n’est pas utilisé, le vérificateur d’orthographe natif est désactivé. Toutefois, si une [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été définie et que désactive la vérification de l’orthographe, comme [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), la propriété `IsSpellCheckEnabled` est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la vérification de l’orthographe d’un `Keyboard` qui le désactive explicitement.

### <a name="enabling-and-disabling-text-prediction"></a>Activation et désactivation de la prédiction de texte

La propriété [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) contrôle si la prédiction de texte et la correction de texte automatique sont activées. Par défaut, la propriété a la valeur `true`. Comme l’utilisateur entre du texte, des prédictions de word sont présentées.

Toutefois, pour certains scénarios de saisie de texte, tels que la saisie d’un nom d’utilisateur, la prédiction de texte et la correction automatique du texte, doivent être désactivées en affectant à la propriété [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) la valeur `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Lorsque la propriété [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) est définie sur `false`et qu’un clavier personnalisé n’est pas utilisé, la prédiction de texte et la correction automatique du texte sont désactivées. Toutefois, si une [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été définie et que la prédiction de texte est désactivée, la propriété `IsTextPredictionEnabled` est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la prédiction de texte pour un `Keyboard` qui le désactive explicitement.

### <a name="colors"></a>Couleurs

Entrée peut être définie pour utiliser un arrière-plan personnalisé et les couleurs de texte via les propriétés pouvant être liées suivantes :

- **TextColor** &ndash; définit la couleur du texte.
- **BackgroundColor** &ndash; définit la couleur affichée derrière le texte.

Une attention particulière est nécessaire pour garantir que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour les couleurs de texte et d’arrière-plan, vous devrez souvent définissez à la fois si vous définissez une.

Utilisez le code suivant pour définir la couleur du texte d’une entrée :

Dans XAML :

```xaml
<Entry TextColor="Green" />
```

En C# :

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Exemple de TextColor d’entrée](entry-images/textcolor.png)

Notez que l’espace réservé n’est pas affecté par le `TextColor`spécifié.

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

Veillez à vous assurer que les couleurs d’arrière-plan et le texte que vous choisissez sont utilisables sur chaque plateforme et n’assombrissent pas n’importe quel texte d’espace réservé.

## <a name="events-and-interactivity"></a>Événements et interactivité

Entrée expose deux événements :

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) &ndash; déclenché lorsque le texte est modifié dans l’entrée. Fournit le texte avant et après la modification.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; déclenché lorsque l’utilisateur a terminé l’entrée en appuyant sur la touche retour du clavier.

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , à partir de laquelle [`Entry`](xref:Xamarin.Forms.Entry) hérite, a également des événements [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) et [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Completed

L’événement `Completed` est utilisé pour réagir à l’achèvement d’une interaction avec une entrée. `Completed` est déclenché lorsque l’utilisateur termine l’entrée par un champ en appuyant sur la touche retour sur le clavier (ou en appuyant sur la touche Tab sur UWP). Le gestionnaire de l’événement est un gestionnaire d’événements générique, qui prend l’expéditeur et `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

Il est possible de s’abonner à l’événement terminé dans XAML :

```xaml
<Entry Completed="Entry_Completed" />
```

et c# :

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Après le déclenchement de l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed) , toute `ICommand` spécifiée par la propriété [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) est exécutée, avec la `object` spécifiée par la propriété [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) passée au `ICommand`.

### <a name="textchanged"></a>TextChanged

L’événement `TextChanged` est utilisé pour réagir à une modification du contenu d’un champ.

`TextChanged` est déclenché chaque fois que la `Text` du `Entry` change. Le gestionnaire de l’événement prend une instance de `TextChangedEventArgs`. `TextChangedEventArgs` permet d’accéder aux valeurs anciennes et nouvelles des `Entry` `Text` via les propriétés `OldTextValue` et `NewTextValue` :

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

L’événement `TextChanged` peut être abonné à en XAML :

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

et c# :

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>Liens connexes

- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API Entry](xref:Xamarin.Forms.Entry)
