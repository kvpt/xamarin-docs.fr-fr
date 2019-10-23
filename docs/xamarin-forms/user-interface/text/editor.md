---
title: Éditeur Xamarin. Forms
description: Cet article explique comment utiliser le contrôle Xamarin. Forms Editor pour accepter une entrée de texte sur plusieurs lignes dans une application.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 0c610d7bdecc5d3454079be38c7e6ede5f0596e1
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696805"
---
# <a name="xamarinforms-editor"></a>Éditeur Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrée de texte sur plusieurs lignes_

Le contrôle [`Editor`](xref:Xamarin.Forms.Editor) est utilisé pour accepter les entrées multiligne. Cet article couvre les sujets suivants :

- **[Personnalisation](#customization)** &ndash; options du clavier et de la couleur.
- **[Interactivité](#interactivity)** &ndash; événements qui peuvent être écoutés pour fournir une interactivité.

## <a name="customization"></a>Personnalisation

### <a name="setting-and-reading-text"></a>Définition et lecture de texte

Le [`Editor`](xref:Xamarin.Forms.Editor), comme d’autres affichages de présentation de texte, expose la propriété `Text`. Cette propriété peut être utilisée pour définir et lire le texte présenté par le `Editor`. L’exemple suivant illustre la définition de la propriété `Text` en XAML :

```xaml
<Editor Text="I am an Editor" />
```

En C# :

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Pour lire du texte, accédez à la propriété C#`Text` dans :

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Définition du texte de l’espace réservé

Le [`Editor`](xref:Xamarin.Forms.Editor) peut être défini pour afficher le texte de l’espace réservé lorsqu’il ne stocke pas l’entrée d’utilisateur. Pour ce faire, affectez à la propriété [`Placeholder`](xref:Xamarin.Forms.Editor.Placeholder) la valeur d’une `string`, qui est souvent utilisée pour indiquer le type de contenu approprié pour le `Editor`. En outre, vous pouvez contrôler la couleur du texte de l’espace réservé en affectant à la propriété [`PlaceholderColor`](xref:Xamarin.Forms.Editor.PlaceholderColor) la valeur [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Empêcher la saisie de texte

Les utilisateurs ne peuvent pas modifier le texte d’une [`Editor`](xref:Xamarin.Forms.Editor) en définissant la propriété `IsReadOnly`, qui a une valeur par défaut de `false`, pour `true` :

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La propriété `IsReadonly` ne modifie pas l’apparence visuelle d’une [`Editor`](xref:Xamarin.Forms.Editor), contrairement à la propriété `IsEnabled` qui modifie également l’apparence visuelle du `Editor` en gris.

### <a name="limiting-input-length"></a>Limitation de la longueur d’entrée

La propriété [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) peut être utilisée pour limiter la longueur d’entrée autorisée pour le [`Editor`](xref:Xamarin.Forms.Editor). Cette propriété doit être définie sur un entier positif :

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Une [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valeur de propriété de 0 indique qu’aucune entrée ne sera autorisée, et une valeur de `int.MaxValue`, qui est la valeur par défaut d’un [`Editor`](xref:Xamarin.Forms.Editor), indique qu’il n’existe aucune limite effective quant au nombre de caractères pouvant être entrés.

### <a name="character-spacing"></a>Espacement des caractères

L’espacement des caractères peut être appliqué à une [`Editor`](xref:Xamarin.Forms.Editor) en affectant à la propriété `Editor.CharacterSpacing` une valeur `double` :

```xaml
<Editor ...
        CharacterSpacing="10" />
```

Le code C# équivalent est :

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

Le résultat est que les caractères du texte affiché par le [`Editor`](xref:Xamarin.Forms.Editor) sont espacés `CharacterSpacing` des unités indépendantes du périphérique.

> [!NOTE]
> La valeur de la propriété `CharacterSpacing` est appliquée au texte affiché par les propriétés `Text` et `Placeholder`.

### <a name="auto-sizing-an-editor"></a>Redimensionnement automatique d’un éditeur

Une [`Editor`](xref:Xamarin.Forms.Editor) peut être configurée pour redimensionner automatiquement son contenu en affectant à la propriété [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) la valeur [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), qui est une valeur de l’énumération [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) . Cette énumération a deux valeurs :

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indique que le redimensionnement automatique est désactivé et qu’il s’agit de la valeur par défaut.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indique que le redimensionnement automatique est activé.

Pour ce faire, vous pouvez utiliser le code comme suit :

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Lorsque le redimensionnement automatique est activé, la hauteur de la [`Editor`](xref:Xamarin.Forms.Editor) augmente lorsque l’utilisateur la remplit avec du texte, et la hauteur diminue lorsque l’utilisateur supprime du texte.

> [!NOTE]
> Un [`Editor`](xref:Xamarin.Forms.Editor) ne se redimensionne pas automatiquement si la propriété [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) a été définie.

### <a name="customizing-the-keyboard"></a>Personnalisation du clavier

Le clavier présenté lorsque les utilisateurs interagissent avec un [`Editor`](xref:Xamarin.Forms.Editor) peut être défini par programme via la propriété [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , à l’une des propriétés suivantes à partir de la classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

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
<Editor Keyboard="Chat" />
```

Le code C# équivalent est :

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
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
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

Le code C# équivalent est :

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>Activation et désactivation de la vérification orthographique

La propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) contrôle si la vérification orthographique est activée. Par défaut, la propriété a la valeur `true`. À mesure que l’utilisateur entre du texte, des fautes d’orthographe sont indiquées.

Toutefois, pour certains scénarios de saisie de texte, tels que la saisie d’un nom d’utilisateur, la vérification orthographique fournit une expérience négative et doit donc être désactivée en affectant à la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) la valeur `false` :

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Lorsque la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) est définie sur `false` et qu’un clavier personnalisé n’est pas utilisé, le vérificateur d’orthographe natif est désactivé. Toutefois, si une [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été définie et que désactive la vérification de l’orthographe, comme [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), la propriété `IsSpellCheckEnabled` est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la vérification de l’orthographe d’un `Keyboard` qui le désactive explicitement.

### <a name="enabling-and-disabling-text-prediction"></a>Activation et désactivation de la prédiction de texte

La propriété `IsTextPredictionEnabled` contrôle si la prédiction de texte et la correction de texte automatique sont activées. Par défaut, la propriété a la valeur `true`. Lorsque l’utilisateur entre du texte, des prédictions de mot sont présentées.

Toutefois, pour certains scénarios de saisie de texte, tels que la saisie d’un nom d’utilisateur, la prédiction de texte et la correction automatique du texte, doivent être désactivées en affectant à la propriété `IsTextPredictionEnabled` la valeur `false` :

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Lorsque la propriété `IsTextPredictionEnabled` est définie sur `false` et qu’un clavier personnalisé n’est pas utilisé, la prédiction de texte et la correction automatique du texte sont désactivées. Toutefois, si une [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été définie et que la prédiction de texte est désactivée, la propriété `IsTextPredictionEnabled` est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la prédiction de texte pour un `Keyboard` qui le désactive explicitement.

### <a name="colors"></a>Couleurs

`Editor` peut être définie pour utiliser une couleur d’arrière-plan personnalisée via la propriété `BackgroundColor`. Une attention particulière est nécessaire pour s’assurer que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour la couleur de texte, vous devrez peut-être définir une couleur d’arrière-plan personnalisée pour chaque plateforme. Pour plus d’informations sur l’optimisation de l’interface utilisateur pour chaque plateforme, consultez [utilisation des ajustements de plateforme](~/xamarin-forms/platform/device.md) .

En C# :

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

En XAML :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "Editor with BackgroundColor Example")

Assurez-vous que les couleurs de texte et d’arrière-plan que vous choisissez sont utilisables sur chaque plateforme et ne masquez aucun texte d’espace réservé.

## <a name="interactivity"></a>Interactivité

`Editor` expose deux événements :

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash; déclenché lorsque le texte change dans l’éditeur. Fournit le texte avant et après la modification.
- [Terminé](xref:Xamarin.Forms.Editor.Completed) &ndash; déclenché lorsque l’utilisateur a terminé l’entrée en appuyant sur la touche retour du clavier.

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , à partir de laquelle [`Entry`](xref:Xamarin.Forms.Entry) hérite, a également des événements [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) et [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Terminé

L’événement `Completed` est utilisé pour réagir à l’achèvement d’une interaction avec une `Editor`. `Completed` est déclenché lorsque l’utilisateur termine l’entrée par un champ en entrant la touche retour sur le clavier (ou en appuyant sur la touche Tab sur UWP). Le gestionnaire de l’événement est un gestionnaire d’événements générique, qui prend l’expéditeur et `EventArgs` :

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

L’événement terminé peut être abonné à dans le code et XAML :

En C# :

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

En XAML :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

L’événement `TextChanged` est utilisé pour réagir à une modification du contenu d’un champ.

`TextChanged` est déclenché chaque fois que la `Text` du `Editor` change. Le gestionnaire de l’événement prend une instance de `TextChangedEventArgs`. `TextChangedEventArgs` permet d’accéder aux valeurs anciennes et nouvelles des `Editor` `Text` via les propriétés `OldTextValue` et `NewTextValue` :

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

L’événement terminé peut être abonné à dans le code et XAML :

En code :

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

En XAML :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

## <a name="related-links"></a>Liens associés

- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API Editor](xref:Xamarin.Forms.Editor)
