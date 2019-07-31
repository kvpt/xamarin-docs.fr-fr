---
title: Éditeur de Xamarin.Forms
description: Cet article explique comment utiliser le contrôle de l’éditeur de Xamarin.Forms pour accepter l’entrée de texte multiligne dans une application.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: 1f9cd0acd201f124f4a4577fec23dce52aee627d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657506"
---
# <a name="xamarinforms-editor"></a>Éditeur de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrée de texte multiligne_

Le [ `Editor` ](xref:Xamarin.Forms.Editor) contrôle est utilisé pour accepter l’entrée de plusieurs lignes. Cet article couvre les sujets suivants :

- **[Personnalisation](#customization)**  &ndash; options de couleur et de clavier.
- **[Interactivité](#interactivity)**  &ndash; les événements qui peuvent être écoutés pour fournir l’interactivité.

## <a name="customization"></a>Personnalisation

### <a name="setting-and-reading-text"></a>Définition et de lecture du texte

Le [ `Editor` ](xref:Xamarin.Forms.Editor), telles que d’autres affichages de présentation de texte, expose le `Text` propriété. Cette propriété peut être utilisée pour définir et lire le texte présenté par le `Editor`. L’exemple suivant montre comment définir le `Text` propriété dans XAML :

```xaml
<Editor Text="I am an Editor" />
```

En C# :

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Pour lire le texte, accéder à la `Text` propriété en C# :

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Texte d’espace réservé de paramètre

Le [ `Editor` ](xref:Xamarin.Forms.Editor) peut être définie pour afficher le texte d’espace réservé quand il ne stocke pas les entrées d’utilisateur. Cela s’effectue en définissant le [ `Placeholder` ](xref:Xamarin.Forms.Editor.Placeholder) propriété à un `string`et est souvent utilisé pour indiquer le type de contenu qui est approprié pour le `Editor`. En outre, la couleur de texte d’espace réservé peut être contrôlée en définissant le [ `PlaceholderColor` ](xref:Xamarin.Forms.Editor.PlaceholderColor) propriété à un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Empêcher la saisie de texte

Les utilisateurs peuvent empêcher la modification du texte [`Editor`](xref:Xamarin.Forms.Editor) d’un en définissant la propriété, qui `false`a la `IsReadOnly` valeur par défaut, pour `true`:

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` propriété ne modifie pas l’apparence visuelle d’un [`Editor`](xref:Xamarin.Forms.Editor), contrairement `Editor` à `IsEnabled` la propriété qui modifie également l’apparence visuelle du en gris.

### <a name="limiting-input-length"></a>Limiter la longueur d’entrée

Le [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propriété peut être utilisée pour limiter la longueur d’entrée est autorisée pour le [ `Editor` ](xref:Xamarin.Forms.Editor). Cette propriété doit être définie à un entier positif :

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Un [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propriété la valeur 0 indique qu’aucune entrée n’est autorisée et la valeur `int.MaxValue`, qui est la valeur par défaut pour un [ `Editor` ](xref:Xamarin.Forms.Editor), indique qu’il y a aucune limite effective sur le nombre de caractères qui peuvent être entrés.

### <a name="auto-sizing-an-editor"></a>Un éditeur de redimensionnement automatique

Un [ `Editor` ](xref:Xamarin.Forms.Editor) possible de faire la taille automatique vers son contenu en définissant le [ `Editor.AutoSize` ](xref:Xamarin.Forms.Editor.AutoSize) propriété [ `TextChanges` ](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), qui est une valeur de la [ `EditoAutoSizeOption` ](xref:Xamarin.Forms.EditorAutoSizeOption) énumération. Cette énumération a deux valeurs :

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) Indique que le redimensionnement automatique est désactivé et est la valeur par défaut.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) Indique que le redimensionnement automatique est activé.

Cela peut être effectué dans le code comme suit :

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Lorsque le redimensionnement automatique est activé, la hauteur de la [ `Editor` ](xref:Xamarin.Forms.Editor) augmente lorsque l’utilisateur remplit avec du texte, et la hauteur diminue à mesure que l’utilisateur supprime le texte.

> [!NOTE]
> Un [ `Editor` ](xref:Xamarin.Forms.Editor) sera pas de la taille automatique if le [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriété a été définie.

### <a name="customizing-the-keyboard"></a>Personnalisation du clavier

Le clavier qui s’affiche lorsque les utilisateurs interagissent avec un [ `Editor` ](xref:Xamarin.Forms.Editor) peut être définie par programme via le [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propriété, à une des propriétés suivantes à partir de la [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe :

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
<Editor Keyboard="Chat" />
```

Le code C# équivalent est :

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
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

Le [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété contrôle si vérification orthographique est activée. Par défaut, la propriété est définie `true`. Comme l’utilisateur entre du texte, les fautes d’orthographe sont indiquées.

Toutefois, dans certains scénarios d’entrée de texte, comme la saisie d’un nom d’utilisateur, la vérification orthographique permet une expérience négative et donc doivent être désactivées en définissant le [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Lorsque le [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriété est définie sur `false`et un clavier personnalisé n’est pas utilisé, le vérificateur orthographique natif va être désactivé. Toutefois, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) a été ensemble qui désactive orthographique vérification, tel que [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), le `IsSpellCheckEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la vérification orthographique pour un `Keyboard` qui désactive explicitement.

### <a name="enabling-and-disabling-text-prediction"></a>Activation et désactivation de la prédiction de texte

La `IsTextPredictionEnabled` propriété contrôle si la prédiction de texte et la correction de texte automatique sont activées. Par défaut, la propriété est définie `true`. Comme l’utilisateur entre du texte, des prédictions de word sont présentées.

Toutefois, pour certains scénarios de saisie de texte, tels que l’entrée d’un nom d’utilisateur, la prédiction de texte et la correction automatique de texte, `IsTextPredictionEnabled` doivent être `false`désactivées en affectant à la propriété la valeur:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Lorsque la `IsTextPredictionEnabled` propriété a la `false`valeur et qu’un clavier personnalisé n’est pas utilisé, la prédiction de texte et la correction automatique du texte sont désactivées. Toutefois, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) a été définie qui désactive la prédiction de texte, le `IsTextPredictionEnabled` propriété est ignorée. Par conséquent, la propriété ne peut pas être utilisée pour activer la prédiction de texte pour un `Keyboard` qui désactive explicitement.

### <a name="colors"></a>Couleurs

`Editor` peut être définie pour utiliser une couleur d’arrière-plan personnalisée via le `BackgroundColor` propriété. Une attention particulière est nécessaire pour garantir que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour la couleur de texte, vous devrez peut-être définir une couleur d’arrière-plan personnalisée pour chaque plateforme. Consultez [fonctionne avec la plateforme Tweaks](~/xamarin-forms/platform/device.md) pour plus d’informations sur l’optimisation de l’interface utilisateur pour chaque plateforme.

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

Dans XAML :

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

![](editor-images/textbackgroundcolor.png "Éditeur avec BackgroundColor exemple")

Assurez-vous que les couleurs d’arrière-plan et le texte que vous choisissez sont utilisables sur chaque plateforme et n’assombrissent pas n’importe quel texte d’espace réservé.

## <a name="interactivity"></a>Interactivité

`Editor` expose deux événements :

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash; déclenché lorsque le texte modifié dans l’éditeur. Fournit le texte avant et après la modification.
- [Terminé](xref:Xamarin.Forms.Editor.Completed) &ndash; déclenché lorsque l’utilisateur s’est terminée entrée en appuyant sur la touche Retour sur le clavier.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, dont [`Entry`](xref:Xamarin.Forms.Entry) hérite, possède [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) également les événements [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) et.

### <a name="completed"></a>Terminé

Le `Completed` événement est utilisé pour réagir à la fin d’une interaction avec un `Editor`. `Completed`est déclenché lorsque l’utilisateur termine l’entrée par un champ en entrant la touche retour sur le clavier (ou en appuyant sur la touche Tab sur UWP). Le gestionnaire pour l’événement est un gestionnaire d’événements générique, en prenant l’expéditeur et `EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

Il est possible de s’abonner à l’événement terminé dans le code et XAML :

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

Dans XAML :

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

Le `TextChanged` événement est utilisé pour réagir à une modification dans le contenu d’un champ.

`TextChanged` est déclenché chaque fois que le `Text` de la `Editor` modifications. Le gestionnaire pour l’événement prend une instance de `TextChangedEventArgs`. `TextChangedEventArgs` fournit l’accès aux valeurs anciennes et nouvelles de la `Editor` `Text` via la `OldTextValue` et `NewTextValue` propriétés :

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

Il est possible de s’abonner à l’événement terminé dans le code et XAML :

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

Dans XAML :

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
- [API de l’éditeur](xref:Xamarin.Forms.Editor)
