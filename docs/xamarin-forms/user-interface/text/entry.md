---
title: Entrée de Xamarin.Forms
description: Cet article explique comment utiliser la classe d’entrée de Xamarin.Forms pour accepter la ligne de texte unique ou une entrée de mot de passe dans une application.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: e712f0b69f87659499f7eab4955a9f943716f5b5
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227981"
---
# <a name="xamarinforms-entry"></a>Entrée de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Texte à ligne unique ou entrée de mot de passe_

Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry) est utilisé pour l’entrée de texte à ligne unique. Le `Entry`, comme le [ `Editor` ](xref:Xamarin.Forms.Editor) afficher, prend en charge plusieurs types de clavier. En outre, le `Entry` peut être utilisé comme un champ de mot de passe.

## <a name="display-customization"></a>Personnalisation de l’affichage

### <a name="setting-and-reading-text"></a>Définition et de lecture du texte

Le `Entry`, telles que d’autres affichages de présentation de texte, expose le [ `Text` ](xref:Xamarin.Forms.Entry.Text) propriété. Cette propriété peut être utilisée pour définir et lire le texte présenté par le `Entry`. L’exemple suivant montre comment définir le `Text` propriété dans XAML :

```xaml
<Entry Text="I am an Entry" />
```

En C# :

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Pour lire le texte, accéder à la `Text` propriété en C# :

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Texte d’espace réservé de paramètre

Le [ `Entry` ](xref:Xamarin.Forms.Entry) peut être définie pour afficher le texte d’espace réservé quand il ne stocke pas les entrées d’utilisateur. Cela s’effectue en définissant le [ `Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder) propriété à un `string`et est souvent utilisé pour indiquer le type de contenu qui est approprié pour le `Entry`. En outre, la couleur de texte d’espace réservé peut être contrôlée en définissant le [ `PlaceholderColor` ](xref:Xamarin.Forms.Entry.PlaceholderColor) propriété à un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> La largeur d’un `Entry` peut être définie en configurant ses `WidthRequest` propriété. Ne dépendent pas de la largeur d’un `Entry` qui est défini selon la valeur de son `Text` propriété.

### <a name="preventing-text-entry"></a>Empêcher la saisie de texte

Les utilisateurs peuvent empêcher la modification du texte [`Entry`](xref:Xamarin.Forms.Entry) d’un en définissant la propriété, qui `false`a la `IsReadOnly` valeur par défaut, pour `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` propriété ne modifie pas l’apparence visuelle d’un [`Entry`](xref:Xamarin.Forms.Entry), contrairement `Entry` à `IsEnabled` la propriété qui modifie également l’apparence visuelle du en gris.

### <a name="limiting-input-length"></a>Limiter la longueur d’entrée

Le [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propriété peut être utilisée pour limiter la longueur d’entrée est autorisée pour le [ `Entry` ](xref:Xamarin.Forms.Entry). Cette propriété doit être définie à un entier positif :

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Un [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propriété la valeur 0 indique qu’aucune entrée n’est autorisée et la valeur `int.MaxValue`, qui est la valeur par défaut pour un [ `Entry` ](xref:Xamarin.Forms.Entry), indique qu’il y a aucune limite effective sur le nombre de caractères qui peuvent être entrés.

### <a name="password-fields"></a>Champs de mot de passe

`Entry` fournit le `IsPassword` propriété. Lorsque `IsPassword` est `true`, le contenu du champ s’affiche sous forme de cercles noir :

Dans XAML :

```xaml
<Entry IsPassword="true" />
```

En C# :

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Exemple de IsPassword d’entrée](entry-images/password.png)

Espaces réservés peuvent être utilisés avec les instances de `Entry` qui sont configurés en tant que champs de mot de passe :

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

Le [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) propriété peut être utilisée pour renvoyer ou définir la position à laquelle le caractère suivant sera inséré dans la chaîne stockée dans le [ `Text` ](xref:Xamarin.Forms.Entry.Text) propriété :

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

La valeur par défaut de la [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) propriété est 0, ce qui indique que le texte est inséré au début de la `Entry`.

En outre, le [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) propriété peut être utilisée pour retourner ou définir la longueur de sélection de texte dans le `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

La valeur par défaut de la [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) propriété est 0, ce qui indique qu’aucun texte n’est sélectionné.

### <a name="customizing-the-keyboard"></a>Personnalisation du clavier

Le clavier qui s’affiche lorsque les utilisateurs interagissent avec un [ `Entry` ](xref:Xamarin.Forms.Entry) peut être définie par programme via le [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propriété, à une des propriétés suivantes à partir de la [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe :

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – utilisé pour aussi et les endroits où emoji sont utiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – le clavier par défaut.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – utilisé lors de la saisie d’adresses de messagerie.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – utilisé lors de la saisie de numéros.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – utilisé lors de la saisie de texte, sans aucune [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags) spécifié.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – utilisé lors de la saisie de numéros de téléphone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – utilisé lors de la saisie de texte.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – utilisé pour entrer des chemins d’accès de fichier & des adresses web.

Cela peut être accompli dans XAML comme suit :

```xaml
<Entry Keyboard="Chat" />
```

Le code C# équivalent est :

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Vous trouverez des exemples de chaque touche du clavier dans notre [recettes](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) référentiel.

Le [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe a également un [ `Create` ](xref:Xamarin.Forms.Keyboard.Create*) méthode de fabrique qui peut être utilisé pour personnaliser un clavier en spécifiant le comportement de mise en majuscules, de vérification orthographique et de suggestion. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) valeurs d’énumération sont spécifiés en tant qu’arguments à la méthode, avec un texte personnalisé `Keyboard` retournés. Le `KeyboardFlags` énumération contient les valeurs suivantes :

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – Aucuns fonctionnalités ne sont ajoutés à l’aide du clavier.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – Indique que la première lettre du premier mot de chaque phrase entré sera être automatiquement mis en majuscules.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – indique cette vérification orthographique sera effectuée sur le texte entré.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – signifie que des saisies semi-automatiques seront proposées sur le texte entré.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – Indique que la première lettre de chaque mot sera être automatiquement mis en majuscules.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) : indique que tous les caractères seront automatiquement en majuscules.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) : indique qu’aucune mise en majuscules automatique ne se produit.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – Indique que la vérification orthographique, les achèvements de word et mise en majuscules de la phrase seront produit sur le texte entré.

L’exemple de code XAML suivant montre comment personnaliser la valeur par défaut [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) de proposer des saisies semi-automatiques du mot et de tirer parti de chaque caractère entré :

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

L’apparence de la touche Retour sur le clavier logiciel, qui est affiché lorsque un [ `Entry` ](xref:Xamarin.Forms.Entry) a le focus, peut être personnalisé en définissant le [ `ReturnType` ](xref:Xamarin.Forms.Entry.ReturnType) propriété une valeur de la [ `ReturnType` ](xref:Xamarin.Forms.ReturnType) énumération :

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) : indique qu’aucune touche de retour spécifique n’est obligatoire et que la valeur par défaut de la plateforme sera utilisé.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) – Indique une touche de retour « Done ».
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) – Indique une touche de retour « Go ».
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) – Indique une touche de retour « Suivant ».
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) – Indique une touche de retour « Recherche ».
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) – Indique une touche de retour « Envoyer ».

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

Lorsque la touche retour est enfoncée, le [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) événement se déclenche et les `ICommand` spécifié par le [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) propriété est exécutée. En outre, n’importe quel `object` spécifié par le [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriété sera passée à la `ICommand` en tant que paramètre. Pour plus d’informations sur les commandes, consultez [l’Interface de commande](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Activation et désactivation de la vérification orthographique

Le [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété contrôle si vérification orthographique est activée. Par défaut, la propriété est définie `true`. Comme l’utilisateur entre du texte, les fautes d’orthographe sont indiquées.

Toutefois, pour certains scénarios d’entrée de texte, comme la saisie d’un nom d’utilisateur, la vérification orthographique offre une expérience négatif et doit être désactivée en définissant le [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Lorsque le [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété est définie sur `false`et un clavier personnalisé n’est pas utilisé, le vérificateur orthographique natif va être désactivé. Toutefois, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) a été ensemble qui désactive orthographique vérification, tel que [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), le `IsSpellCheckEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la vérification orthographique pour un `Keyboard` qui désactive explicitement.

### <a name="enabling-and-disabling-text-prediction"></a>Activation et désactivation de la prédiction de texte

Le [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriété détermine si la prédiction de texte et automatiques correction du texte est activée. Par défaut, la propriété est définie `true`. Comme l’utilisateur entre du texte, des prédictions de word sont présentées.

Toutefois, pour certains scénarios d’entrée de texte, comme la saisie d’un nom d’utilisateur, de la prédiction de texte et de texte automatique correction offre une expérience négatif et doit être désactivée en définissant le [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriété `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Lorsque le [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriété est définie sur `false`, et un clavier personnalisé n’est pas en cours utilisé, la prédiction de texte et automatiques correction de texte est désactivée. Toutefois, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) a été définie qui désactive la prédiction de texte, le `IsTextPredictionEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la prédiction de texte pour un `Keyboard` qui désactive explicitement.

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

Notez que l’espace réservé n’est pas affecté par le `TextColor`.

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

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; déclenché lorsque le texte modifié dans l’entrée. Fournit le texte avant et après la modification.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; déclenché lorsque l’utilisateur s’est terminée entrée en appuyant sur la touche Retour sur le clavier.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, dont [`Entry`](xref:Xamarin.Forms.Entry) hérite, possède [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) également les événements [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) et.

### <a name="completed"></a>Terminé

Le `Completed` événement est utilisé pour réagir à la fin d’une interaction avec une entrée. `Completed`est déclenché lorsque l’utilisateur termine l’entrée par un champ en appuyant sur la touche retour sur le clavier (ou en appuyant sur la touche Tab sur UWP). Le gestionnaire pour l’événement est un gestionnaire d’événements générique, en prenant l’expéditeur et `EventArgs`:

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

et C# :

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Après le [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) événement est déclenché, toute `ICommand` spécifié par le [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) propriété est exécutée, avec le `object` spécifié par le [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriété passée à la `ICommand`.

### <a name="textchanged"></a>TextChanged

Le `TextChanged` événement est utilisé pour réagir à une modification dans le contenu d’un champ.

`TextChanged` est déclenché chaque fois que le `Text` de la `Entry` modifications. Le gestionnaire pour l’événement prend une instance de `TextChangedEventArgs`. `TextChangedEventArgs` fournit l’accès aux valeurs anciennes et nouvelles de la `Entry` `Text` via la `OldTextValue` et `NewTextValue` propriétés :

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

Le `TextChanged` peut être abonné à l’événement dans XAML :

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
- [Entrée API](xref:Xamarin.Forms.Entry)
