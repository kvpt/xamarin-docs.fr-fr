---
title: Xamarin.Forms Éditeur
description: Cet article explique comment utiliser le Xamarin.Forms contrôle de l’éditeur pour accepter une entrée de texte sur plusieurs lignes dans une application.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 36f4946ffc6d5a42e1997f389501f921449d0259
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937590"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms Éditeur

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrée de texte sur plusieurs lignes_

Le [`Editor`](xref:Xamarin.Forms.Editor) contrôle est utilisé pour accepter les entrées multiligne. Cet article couvre les points suivants :

- **[Personnalisation](#customization)** &ndash; options du clavier et de la couleur.
- **[Interactivité](#interactivity)** &ndash; événements qui peuvent être écoutés par pour fournir une interactivité.

## <a name="customization"></a>Personnalisation

### <a name="setting-and-reading-text"></a>Définition et lecture de texte

[`Editor`](xref:Xamarin.Forms.Editor), Comme d’autres affichages de présentation de texte, expose la `Text` propriété. Cette propriété peut être utilisée pour définir et lire le texte présenté par le `Editor` . L’exemple suivant illustre la définition `Text` de la propriété en XAML :

```xaml
<Editor Text="I am an Editor" />
```

En C# :

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Pour lire du texte, accédez à la `Text` propriété en C# :

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Définition du texte de l’espace réservé

[`Editor`](xref:Xamarin.Forms.Editor)Peut être défini pour afficher le texte de l’espace réservé lorsqu’il ne stocke pas l’entrée d’utilisateur. Pour ce faire, affectez [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) à la propriété la valeur `string` et est souvent utilisé pour indiquer le type de contenu approprié pour le `Editor` . En outre, la couleur du texte de l’espace réservé peut être contrôlée en affectant à la propriété la valeur [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Empêcher la saisie de texte

Les utilisateurs peuvent empêcher la modification du texte d’un [`Editor`](xref:Xamarin.Forms.Editor) en définissant la `IsReadOnly` propriété, qui a la valeur par défaut `false` , pour `true` :

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` propriété ne modifie pas l’apparence visuelle d’un [`Editor`](xref:Xamarin.Forms.Editor) , contrairement `IsEnabled` à la propriété qui modifie également l’apparence visuelle du `Editor` en gris.

### <a name="limiting-input-length"></a>Limitation de la longueur d’entrée

La [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) propriété peut être utilisée pour limiter la longueur d’entrée autorisée pour [`Editor`](xref:Xamarin.Forms.Editor) . Cette propriété doit être définie sur un entier positif :

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Une [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valeur de propriété de 0 indique qu’aucune entrée n’est autorisée, et la valeur `int.MaxValue` , qui est la valeur par défaut pour un [`Editor`](xref:Xamarin.Forms.Editor) , indique qu’il n’existe aucune limite effective quant au nombre de caractères pouvant être entrés.

### <a name="character-spacing"></a>Espacement des caractères

L’espacement des caractères peut être appliqué à un [`Editor`](xref:Xamarin.Forms.Editor) en affectant `Editor.CharacterSpacing` à la propriété une `double` valeur :

```xaml
<Editor ...
        CharacterSpacing="10" />
```

Le code C# équivalent est :

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

En conséquence, les caractères du texte affiché par le sont espacés d' [`Editor`](xref:Xamarin.Forms.Editor) `CharacterSpacing` unités indépendantes du périphérique.

> [!NOTE]
> La `CharacterSpacing` valeur de la propriété est appliquée au texte affiché par `Text` les `Placeholder` Propriétés et.

### <a name="auto-sizing-an-editor"></a>Redimensionnement automatique d’un éditeur

Un [`Editor`](xref:Xamarin.Forms.Editor) peut être configuré pour redimensionner automatiquement son contenu en affectant à la [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) propriété la [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) valeur, qui est une valeur de l' [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) énumération. Cette énumération a deux valeurs :

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled)indique que le redimensionnement automatique est désactivé et qu’il s’agit de la valeur par défaut.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)indique que le redimensionnement automatique est activé.

Pour ce faire, vous pouvez utiliser le code comme suit :

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Lorsque le redimensionnement automatique est activé, la hauteur du [`Editor`](xref:Xamarin.Forms.Editor) s’accroît lorsque l’utilisateur le remplit avec du texte, et la hauteur diminue lorsque l’utilisateur supprime du texte.

> [!NOTE]
> Un [`Editor`](xref:Xamarin.Forms.Editor) ne se redimensionne pas automatiquement si la [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) propriété a été définie.

### <a name="customizing-the-keyboard"></a>Personnalisation du clavier

Le clavier présenté lorsque les utilisateurs interagissent avec un [`Editor`](xref:Xamarin.Forms.Editor) peut être défini par programmation via la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propriété, à l’une des propriétés suivantes à partir de la [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe :

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
<Editor Keyboard="Chat" />
```

Le code C# équivalent est :

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
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

La [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété contrôle si la vérification orthographique est activée. Par défaut, la propriété a la valeur `true` . À mesure que l’utilisateur entre du texte, des fautes d’orthographe sont indiquées.

Toutefois, pour certains scénarios de saisie de texte, tels que la saisie d’un nom d’utilisateur, la vérification orthographique fournit une expérience négative et doit donc être désactivée en affectant à la propriété la valeur [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) `false` :

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Lorsque la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété a la valeur `false` et qu’un clavier personnalisé n’est pas utilisé, le vérificateur d’orthographe natif est désactivé. Toutefois, si un [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été défini et que désactive la vérification de l’orthographe, par exemple [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , la `IsSpellCheckEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la vérification de l’orthographe pour un `Keyboard` qui le désactive explicitement.

### <a name="enabling-and-disabling-text-prediction"></a>Activation et désactivation de la prédiction de texte

La `IsTextPredictionEnabled` propriété contrôle si la prédiction de texte et la correction de texte automatique sont activées. Par défaut, la propriété a la valeur `true` . Lorsque l’utilisateur entre du texte, des prédictions de mot sont présentées.

Toutefois, pour certains scénarios de saisie de texte, tels que l’entrée d’un nom d’utilisateur, la prédiction de texte et la correction automatique de texte, doivent être désactivées en affectant à la propriété la valeur `IsTextPredictionEnabled` `false` :

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Lorsque la `IsTextPredictionEnabled` propriété a la valeur `false` et qu’un clavier personnalisé n’est pas utilisé, la prédiction de texte et la correction automatique du texte sont désactivées. Toutefois, si un [`Keyboard`](xref:Xamarin.Forms.Keyboard) a été défini et que la prédiction de texte est désactivée, la `IsTextPredictionEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la prédiction de texte pour un `Keyboard` qui le désactive explicitement.

### <a name="colors"></a>Couleurs

`Editor`peut être défini pour utiliser une couleur d’arrière-plan personnalisée via la `BackgroundColor` propriété. Une attention particulière est nécessaire pour s’assurer que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour la couleur de texte, vous devrez peut-être définir une couleur d’arrière-plan personnalisée pour chaque plateforme. Pour plus d’informations sur l’optimisation de l’interface utilisateur pour chaque plateforme, consultez [utilisation des ajustements de plateforme](~/xamarin-forms/platform/device.md) .

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

![Éditeur avec l’exemple BackgroundColor](editor-images/textbackgroundcolor.png)

Assurez-vous que les couleurs de texte et d’arrière-plan que vous choisissez sont utilisables sur chaque plateforme et ne masquez aucun texte d’espace réservé.

## <a name="interactivity"></a>Interactivité

`Editor`expose deux événements :

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; déclenché lorsque le texte est modifié dans l’éditeur. Fournit le texte avant et après la modification.
- [Terminé](xref:Xamarin.Forms.Editor.Completed) &ndash; déclenché lorsque l’utilisateur a terminé l’entrée en appuyant sur la touche retour sur le clavier.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, dont [`Entry`](xref:Xamarin.Forms.Entry) hérite, possède également les [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) événements et.

### <a name="completed"></a>Completed

L' `Completed` événement est utilisé pour réagir à l’achèvement d’une interaction avec un `Editor` . `Completed`est déclenché lorsque l’utilisateur termine l’entrée par un champ en entrant la touche retour sur le clavier (ou en appuyant sur la touche Tab sur UWP). Le gestionnaire de l’événement est un gestionnaire d’événements générique, qui prend l’expéditeur et `EventArgs` :

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

L' `TextChanged` événement est utilisé pour réagir à une modification du contenu d’un champ.

`TextChanged`est déclenché chaque fois que le `Text` de `Editor` change. Le gestionnaire de l’événement prend une instance de `TextChangedEventArgs` . `TextChangedEventArgs`fournit l’accès aux valeurs anciennes et nouvelles de `Editor` `Text` via les `OldTextValue` Propriétés et `NewTextValue` :

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

L’événement terminé peut être abonné à dans le code et XAML :

Dans le code :

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
