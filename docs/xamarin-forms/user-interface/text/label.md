---
title: Étiquette Xamarin. Forms
description: Cet article explique comment utiliser la classe Xamarin. Forms label pour afficher du texte sur une seule ligne dans des applications.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: 6ce4e39986afb7444e7d126e9789760d9311ca45
ms.sourcegitcommit: 737c7fbbe8aed1f33110a5217d7e6d6ef3e5b785
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793220"
---
# <a name="xamarinforms-label"></a>Étiquette Xamarin. Forms

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Afficher le texte dans Xamarin. Forms_

La [`Label`](xref:Xamarin.Forms.Label) vue est utilisée pour afficher du texte, qu’il s’agit d’une seule ou plusieurs lignes. Les étiquettes peuvent contenir des décorations de texte, du texte en couleur et utiliser des polices personnalisées (familles, tailles et options).

## <a name="text-decorations"></a>Décorations de texte

Les décorations de texte souligné et barré peuvent être [`Label`](xref:Xamarin.Forms.Label) appliquées aux instances en `Label.TextDecorations` affectant à la propriété `TextDecorations` un ou plusieurs membres de l’énumération :

- `None`
- `Underline`
- `Strikethrough`

L’exemple de code XAML suivant illustre `Label.TextDecorations` la définition de la propriété :

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

Le code C# équivalent est :

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

Les captures d’écran suivantes `TextDecorations` montrent les membres de [`Label`](xref:Xamarin.Forms.Label) l’énumération appliqués aux instances :

![Étiquettes avec décorations de texte](label-images/label-textdecorations.png)

> [!NOTE]
> Les décorations de texte peuvent également être [`Span`](xref:Xamarin.Forms.Span) appliquées aux instances. Pour plus d’informations sur `Span` la classe, consultez [texte mis en forme](#Formatted_Text).

## <a name="character-spacing"></a>Espacement des caractères

L’espacement des caractères peut [`Label`](xref:Xamarin.Forms.Label) être appliqué aux instances `Label.CharacterSpacing` en affectant `double` à la propriété une valeur :

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Le code C# équivalent est :

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

En conséquence, les caractères du texte affiché par le sont [`Label`](xref:Xamarin.Forms.Label) espacés d' `CharacterSpacing` unités indépendantes du périphérique.

## <a name="new-lines"></a>Nouvelles lignes

Il existe deux techniques principales pour forcer le texte d' [`Label`](xref:Xamarin.Forms.Label) un sur une nouvelle ligne, à partir de XAML :

1. Utilisez le caractère de saut de ligne Unicode, qui&amp;est « #10 ; ».
1. Spécifiez votre texte à l’aide de la syntaxe d' *élément de propriété* .

Le code suivant montre un exemple des deux techniques :

```xaml
<!-- Unicode line feed character -->
<Label Text="First line &#10; Second line" />

<!-- Property element syntax -->
<Label>
    <Label.Text>
        First line
        Second line
    </Label.Text>
</Label>
```

En C#, le texte peut être forcé sur une nouvelle ligne avec le caractère « \n » :

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Couleurs

Les étiquettes peuvent être définies pour utiliser une couleur de texte personnalisée via la [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propriété pouvant être liée.

Une attention particulière est nécessaire pour s’assurer que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour les couleurs de texte et d’arrière-plan, vous devez veiller à choisir une valeur par défaut qui fonctionne sur chacune d’elles.

L’exemple de code XAML suivant définit la couleur de `Label`texte d’un :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a green label." />
    </StackLayout>
</ContentPage>
```

Le code C# équivalent est :

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a green label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

Les captures d’écran suivantes montrent le résultat de `TextColor` la définition de la propriété :

![Exemple d’étiquette TextColor](label-images/textcolor.png)

Pour plus d’informations sur les couleurs, consultez [couleurs](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Polices

Pour plus d’informations sur la spécification des `Label`polices sur un, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Troncation et encapsulation

Les étiquettes peuvent être définies pour gérer du texte qui ne peut pas tenir sur une ligne de plusieurs façons, exposées `LineBreakMode` par la propriété. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)est une énumération avec les valeurs suivantes :

- **HeadTruncation** &ndash; tronque le début du texte, en présentant la fin.
- **CharacterWrap** &ndash; renvoie le texte sur une nouvelle ligne à la limite d’un caractère.
- **MiddleTruncation** &ndash; affiche le début et la fin du texte, en remplaçant par des points de suspension.
- **Nowrap** &ndash; n’habille pas le texte et n’affiche que le texte qui peut tenir sur une seule ligne.
- **TailTruncation** &ndash; affiche le début du texte, en tronquant la fin.
- **WordWrap** &ndash; renvoie le texte à la limite de mot.

## <a name="display-a-specific-number-of-lines"></a>Afficher un nombre spécifique de lignes

Le nombre de lignes affichées par un [`Label`](xref:Xamarin.Forms.Label) peut être spécifié en affectant `Label.MaxLines` à la propriété `int` une valeur :

- Quand `MaxLines` a la valeur-1, qui est sa valeur par `Label` défaut, le respecte la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) valeur de la propriété pour afficher une seule ligne, éventuellement tronquée, ou toutes les lignes avec tout le texte.
- Lorsque `MaxLines` a la valeur 0 `Label` , le n’est pas affiché.
- Lorsque `MaxLines` a la valeur 1, le résultat est identique à [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) l’affectation [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)de [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode)la [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)valeur à [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode)la propriété,, ou. Toutefois, le `Label` respecte la valeur de la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propriété en ce qui concerne l’emplacement des points de suspension, le cas échéant.
- Lorsque `MaxLines` est supérieur à 1, le `Label` s’affiche jusqu’au nombre de lignes spécifié, tout en respectant la valeur de [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) la propriété par rapport à l’emplacement des points de suspension, le cas échéant. Toutefois, l’affectation `MaxLines` d’une valeur supérieure à 1 à la propriété n’a aucun [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) effet si la propriété [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)a la valeur.

L’exemple de code XAML suivant illustre `MaxLines` la définition de [`Label`](xref:Xamarin.Forms.Label)la propriété sur un :

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

Le code C# équivalent est :

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

Les captures d’écran suivantes montrent le résultat de `MaxLines` l’affectation de la valeur 2 à la propriété, lorsque le texte est suffisamment long pour occuper plus de 2 lignes :

![Exemple d’étiquette MaxLines](label-images/label-maxlines.png)

## <a name="display-html"></a>Afficher le code HTML

La [`Label`](xref:Xamarin.Forms.Label) classe a une `TextType` propriété qui détermine si l’instance `Label` doit afficher du texte brut ou du texte html. Cette propriété doit être définie sur l’un des membres de l' `TextType` énumération :

- `Text`indique que le `Label` affiche du texte brut et est la valeur par défaut de la `Label.TextType` propriété.
- `Html`indique que le `Label` affiche le texte html.

Par conséquent [`Label`](xref:Xamarin.Forms.Label) , les instances peuvent afficher du code `Label.TextType` html en `Html`affectant à `Label.Text` la propriété la valeur et à la propriété une chaîne HTML :

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

Dans l’exemple ci-dessus, les guillemets doubles du code HTML doivent être placés dans une `\` séquence d’échappement à l’aide du symbole.

En XAML, les chaînes HTML peuvent devenir illisibles en raison de l’échappement en plus `<` des `>` symboles et :

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

En guise d’alternative, pour une meilleure lisibilité, le code HTML peut `CDATA` être inline dans une section :

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

Dans cet exemple, la `Label.Text` propriété est définie sur la chaîne HTML qui est incluse dans la `CDATA` section. Cela fonctionne, car `Text` la propriété est `ContentProperty` le de `Label` la classe.

Les captures d’écran suivantes [`Label`](xref:Xamarin.Forms.Label) montrent un affichage HTML :

![Captures d’écran d’une étiquette affichant du code HTML, sur iOS et Android](label-images/label-html.png)

> [!IMPORTANT]
> L’affichage du HTML [`Label`](xref:Xamarin.Forms.Label) dans un est limité aux balises HTML prises en charge par la plateforme sous-jacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texte mis en forme

Les étiquettes exposent une [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propriété qui autorise la présentation de texte avec plusieurs polices et couleurs dans la même vue.

La `FormattedText` propriété est de type [`FormattedString`](xref:Xamarin.Forms.FormattedString), qui comprend une ou plusieurs [`Span`](xref:Xamarin.Forms.Span) instances définies via la [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) propriété. Les propriétés `Span` suivantes peuvent être utilisées pour définir l’apparence visuelle :

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor): couleur de l’arrière-plan de l’étendue.
- `CharacterSpacing`, de type `double`, est l’espacement entre les caractères `Span` du texte.
- [`Font`](xref:Xamarin.Forms.Span.Font): police du texte de l’étendue.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes): attributs de police pour le texte de l’étendue.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily): famille de polices à laquelle appartient la police du texte de l’étendue.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize): taille de la police du texte de l’étendue.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor): couleur du texte de l’étendue. Cette propriété est obsolète et a été remplacée par la `TextColor` propriété.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight): multiplicateur à appliquer à la hauteur de ligne par défaut de l’étendue. Pour plus d’informations, consultez [hauteur de ligne](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style): style à appliquer à l’étendue.
- [`Text`](xref:Xamarin.Forms.Span.Text): texte de l’étendue.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor): couleur du texte de l’étendue.
- `TextDecorations`: décorations à appliquer au texte de l’étendue. Pour plus d’informations, consultez [décorations de texte](#text-decorations).

Les [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)propriétés [`Text`](xref:Xamarin.Forms.Span.Text), et [`Text`](xref:Xamarin.Forms.Span.Text) pouvant être liées ont un mode de liaison par [`OneWay`](xref:Xamarin.Forms.BindingMode)défaut de. Pour plus d’informations sur ce mode de liaison, consultez [le mode de liaison par défaut](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) dans le Guide de [mode de liaison](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

En outre, la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) propriété peut être utilisée pour définir une collection de détecteurs de mouvement qui répondront aux gestes sur [`Span`](xref:Xamarin.Forms.Span).

> [!NOTE]
> Il n’est pas possible d’afficher du code [`Span`](xref:Xamarin.Forms.Span)html dans un.

L’exemple de code XAML suivant `FormattedText` illustre une propriété qui se [`Span`](xref:Xamarin.Forms.Span) compose de trois instances :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label LineBreakMode="WordWrap">
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}">
                        <Span.GestureRecognizers>
                            <TapGestureRecognizer Command="{Binding TapCommand}" />
                        </Span.GestureRecognizers>
                    </Span>
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

Le code C# équivalent est :

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });

        var span = new Span { Text = "default, " };
        span.GestureRecognizers.Add(new TapGestureRecognizer { Command = new Command(async () => await DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!IMPORTANT]
> La [`Text`](xref:Xamarin.Forms.Span.Text) propriété d’un `Span` peut être définie via la liaison de données. Pour plus d’informations, consultez [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Notez qu’un [`Span`](xref:Xamarin.Forms.Span) peut également répondre à tous les mouvements ajoutés à la collection de [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) l’étendue. Par exemple, un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a été ajouté au deuxième `Span` dans les exemples de code ci-dessus. Par conséquent, lorsque `Span` cette opération est `TapGestureRecognizer` exploitée, le répond en `ICommand` exécutant le défini [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) par la propriété. Pour plus d’informations sur les détecteurs de mouvement, consultez [Xamarin. Forms gestes](~/xamarin-forms/app-fundamentals/gestures/index.md).

Les captures d’écran suivantes montrent le résultat de `FormattedString` la définition de `Span` la propriété sur trois instances :

![Exemple d’étiquette FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Hauteur de ligne

La hauteur verticale d’un [`Label`](xref:Xamarin.Forms.Label) et d' [`Span`](xref:Xamarin.Forms.Span) un peut être personnalisée en affectant [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) à [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) la propriété `double` ou à une valeur. Sur iOS et Android, ces valeurs sont des multiplicateurs de la hauteur de ligne d’origine et, sur le plateforme Windows universelle `Label.LineHeight` (UWP), la valeur de propriété est un multiplicateur de la taille de police de l’étiquette.

> [!NOTE]
>
> - Sur iOS, les [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriétés [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) et modifient la hauteur de ligne du texte qui s’ajuste sur une seule ligne, et le texte qui s’encapsule sur plusieurs lignes.
> - Sur Android, les [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriétés [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) et modifient uniquement la hauteur de ligne du texte encapsulé sur plusieurs lignes.
> - Sur UWP, la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriété modifie la hauteur de ligne du texte qui s’encapsule sur plusieurs lignes, [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) et la propriété n’a aucun effet.

L’exemple de code XAML suivant illustre [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) la définition de [`Label`](xref:Xamarin.Forms.Label)la propriété sur un :

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

Le code C# équivalent est :

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

Les captures d’écran suivantes montrent le résultat de [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) la définition de la propriété sur 1,8 :

![Exemple d’étiquette LineHeight](label-images/label-lineheight.png)

L’exemple de code XAML suivant illustre [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) la définition de [`Span`](xref:Xamarin.Forms.Span)la propriété sur un :

```xaml
<Label LineBreakMode="WordWrap">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. "
                  LineHeight="1.8"/>
            <Span Text="Nullam feugiat sodales elit, et maximus nibh vulputate id."
                  LineHeight="1.8" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Le code C# équivalent est :

```csharp
var formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. ",
  LineHeight = 1.8
});
formattedString.Spans.Add(new Span
{
  Text = "Nullam feugiat sodales elit, et maximus nibh vulputate id.",
  LineHeight = 1.8
});
var label = new Label
{
  FormattedText = formattedString,
  LineBreakMode = LineBreakMode.WordWrap
};
```

Les captures d’écran suivantes montrent le résultat de [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) la définition de la propriété sur 1,8 :

![Exemple d’étendue de LineHeight](label-images/span-lineheight.png)

## <a name="padding"></a>Remplissage

Le remplissage représente l’espace entre un élément et ses éléments enfants, et est utilisé pour séparer l’élément de son propre contenu. Le remplissage peut être appliqué aux [`Label`](xref:Xamarin.Forms.Label) instances en affectant `Label.Padding` à la propriété [`Thickness`](xref:Xamarin.Forms.Thickness) une valeur :

```xaml
<Label Padding="10">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum" />
            <Span Text="dolor sit amet." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Le code C# équivalent est :

```csharp
FormattedString formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum"
});
formattedString.Spans.Add(new Span
{
  Text = "dolor sit amet."
});
Label label = new Label
{
    FormattedText = formattedString,
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> Sur iOS, lors de [`Label`](xref:Xamarin.Forms.Label) la création d’un qui `Padding` définit la propriété, le remplissage est appliqué et la valeur de remplissage peut être mise à jour ultérieurement. Toutefois, lors de `Label` la création d’un qui ne `Padding` définit pas la propriété, la tentative de sa définition ultérieurement n’aura aucun effet.
>
> Sur Android et le plateforme Windows universelle, la `Padding` valeur de la propriété peut être spécifiée `Label` lors de la création du, ou ultérieurement.

Pour plus d’informations sur le remplissage, consultez [marges et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Liens hypertexte

Le texte affiché par [`Label`](xref:Xamarin.Forms.Label) les [`Span`](xref:Xamarin.Forms.Span) instances et peut être converti en liens hypertexte avec l’approche suivante :

1. Définissez les `TextColor` propriétés `TextDecoration` et de [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span).
1. Ajoutez un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) à la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) collection du [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span)de, dont [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) la propriété est liée à `ICommand`un, et [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) dont la propriété contient l’URL à ouvrir.
1. Définissez le `ICommand` qui sera exécuté par le [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Écrivez le code qui sera exécuté par le `ICommand`.

L’exemple de code suivant, tiré de l’exemple de [démonstrations de lien hypertexte](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , montre un dont [`Label`](xref:Xamarin.Forms.Label) le [`Span`](xref:Xamarin.Forms.Span) contenu est défini à partir de plusieurs instances :

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Alternatively, click " />
            <Span Text="here"
                  TextColor="Blue"
                  TextDecorations="Underline">
                <Span.GestureRecognizers>
                    <TapGestureRecognizer Command="{Binding TapCommand}"
                                          CommandParameter="https://docs.microsoft.com/xamarin/" />
                </Span.GestureRecognizers>
            </Span>
            <Span Text=" to view Xamarin documentation." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Dans cet exemple, la première et la [`Span`](xref:Xamarin.Forms.Span) troisième instance comprennent du texte, tandis `Span` que la seconde représente un lien hypertexte tappable. Sa couleur de texte est définie sur bleu et a une décoration de texte souligné. Cela crée l’apparence d’un lien hypertexte, comme illustré dans les captures d’écran suivantes :

[![Liens hypertexte](label-images/hyperlinks-small.png "Liens hypertexte")](label-images/hyperlinks-large.png#lightbox)

Lorsque le lien hypertexte est frappé, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) le répond en exécutant le `ICommand` défini par sa [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriété. En outre, l’URL spécifiée par la [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriété est passée à `ICommand` en tant que paramètre.

Le code-behind de la page XAML contient l' `TapCommand` implémentation :

```csharp
public partial class MainPage : ContentPage
{
    // Launcher.OpenAsync is provided by Xamarin.Essentials.
    public ICommand TapCommand => new Command<string>(async (url) => await Launcher.OpenAsync(url));

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }
}
```

`TapCommand` Exécute la `Launcher.OpenAsync` méthode, en passant la valeur [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) de la propriété en tant que paramètre. La `Launcher.OpenAsync` méthode est fournie par Xamarin. Essentials et ouvre l’URL dans un navigateur Web. Par conséquent, l’effet global est que lorsque le lien hypertexte est frappé sur la page, un navigateur Web s’affiche et l’URL associée au lien hypertexte est vers laquelle il est accédé.

### <a name="creating-a-reusable-hyperlink-class"></a>Création d’une classe HYPERLINK réutilisable

L’approche précédente de la création d’un lien hypertexte requiert l’écriture d’un code répétitif chaque fois que vous avez besoin d’un lien hypertexte dans votre application. Toutefois, les classes [`Label`](xref:Xamarin.Forms.Label) et [`Span`](xref:Xamarin.Forms.Span) peuvent être sous-classées pour créer `HyperlinkLabel` des `HyperlinkSpan` classes et, avec le module de reconnaissance de mouvement et le code de mise en forme du texte ajoutés ici.

L’exemple de code suivant, tiré de l’exemple de [démonstrations de lien hypertexte](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , illustre une `HyperlinkSpan` classe :

```csharp
public class HyperlinkSpan : Span
{
    public static readonly BindableProperty UrlProperty =
        BindableProperty.Create(nameof(Url), typeof(string), typeof(HyperlinkSpan), null);

    public string Url
    {
        get { return (string)GetValue(UrlProperty); }
        set { SetValue(UrlProperty, value); }
    }

    public HyperlinkSpan()
    {
        TextDecorations = TextDecorations.Underline;
        TextColor = Color.Blue;
        GestureRecognizers.Add(new TapGestureRecognizer
        {
            // Launcher.OpenAsync is provided by Xamarin.Essentials.
            Command = new Command(async () => await Launcher.OpenAsync(Url))
        });
    }
}
```

La `HyperlinkSpan` classe définit une `Url` propriété, et associée [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), et le constructeur définit l’apparence du lien hypertexte [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) et le qui répondra lorsque le lien hypertexte sera appuyé. Lorsqu’un `HyperlinkSpan` est frappé, le `TapGestureRecognizer` répond en exécutant la `Launcher.OpenAsync` méthode pour ouvrir l’URL, spécifiée par la `Url` propriété, dans un navigateur Web.

La `HyperlinkSpan` classe peut être consommée en ajoutant une instance de la classe au XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:HyperlinkDemo"
             x:Class="HyperlinkDemo.MainPage">
    <StackLayout>
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Alternatively, click " />
                    <local:HyperlinkSpan Text="here"
                                         Url="https://docs.microsoft.com/appcenter/" />
                    <Span Text=" to view AppCenter documentation." />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

## <a name="styling-labels"></a>Styliser des étiquettes

Les sections précédentes ont abordé [`Label`](xref:Xamarin.Forms.Label) les [`Span`](xref:Xamarin.Forms.Span) paramètres et les propriétés par instance. Toutefois, les ensembles de propriétés peuvent être regroupés dans un style qui est appliqué de façon cohérente à une ou plusieurs vues. Cela peut augmenter la lisibilité du code et faciliter l’implémentation des modifications de conception. Pour plus d’informations, consultez [styles](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Liens connexes

- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Liens hypertexte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Création d’Mobile Apps avec Xamarin. Forms, chapitre 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API Label](xref:Xamarin.Forms.Label)
- [API span](xref:Xamarin.Forms.Span)
