---
title: Étiquette Xamarin. Forms
description: Cet article explique comment utiliser la classe Xamarin. Forms label pour afficher du texte sur une seule ligne dans des applications.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 6ea8195d422da3c64175b164c5fbf2885eb234ab
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696393"
---
# <a name="xamarinforms-label"></a>Étiquette Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Afficher le texte dans Xamarin. Forms_

La vue [`Label`](xref:Xamarin.Forms.Label) permet d’afficher du texte, qu’il s’agit d’une seule ou plusieurs lignes. Les étiquettes peuvent contenir des décorations de texte, du texte en couleur et utiliser des polices personnalisées (familles, tailles et options).

## <a name="text-decorations"></a>Décorations de texte

Les décorations de texte souligné et barré peuvent être appliquées à [`Label`](xref:Xamarin.Forms.Label) instances en affectant à la propriété `Label.TextDecorations` la valeur d’un ou plusieurs membres de l’énumération `TextDecorations` :

- `None`
- `Underline`
- `Strikethrough`

L’exemple de code XAML suivant illustre la définition de la propriété `Label.TextDecorations` :

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

Les captures d’écran suivantes montrent les `TextDecorations` membres de l’énumération appliqués aux instances de [`Label`](xref:Xamarin.Forms.Label) :

![Étiquettes avec décorations de texte](label-images/label-textdecorations.png)

> [!NOTE]
> Les décorations de texte peuvent également être appliquées à des instances de [`Span`](xref:Xamarin.Forms.Span) . Pour plus d’informations sur la classe `Span`, consultez [texte mis en forme](#Formatted_Text).

## <a name="character-spacing"></a>Espacement des caractères

L’espacement des caractères peut être appliqué aux instances de [`Label`](xref:Xamarin.Forms.Label) en affectant à la propriété `Label.CharacterSpacing` une valeur `double` :

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Le code C# équivalent est :

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

Le résultat est que les caractères du texte affiché par le [`Label`](xref:Xamarin.Forms.Label) sont espacés `CharacterSpacing` des unités indépendantes du périphérique.

## <a name="padding"></a>Padding

Le remplissage représente l’espace entre un élément et ses éléments enfants, et est utilisé pour séparer l’élément de son propre contenu. Le remplissage peut être appliqué aux instances de [`Label`](xref:Xamarin.Forms.Label) en affectant à la propriété `Label.Padding` une valeur [`Thickness`](xref:Xamarin.Forms.Thickness) :

```xaml
<Label Text="Padded text"
       Padding="20" />
```

Le code C# équivalent est :

```csharp
Label label = new Label
{
    Text = "Padded text",
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> Sur iOS, lors de la création d’un [`Label`](xref:Xamarin.Forms.Label) qui définit la propriété `Padding`, le remplissage est appliqué et la valeur de remplissage peut être mise à jour ultérieurement. Toutefois, lors de la création d’un `Label` qui ne définit pas la propriété `Padding`, toute tentative de sa définition ultérieure n’aura aucun effet.
>
> Sur Android et le plateforme Windows universelle, la valeur de la propriété `Padding` peut être spécifiée lors de la création du `Label`, ou version ultérieure.

Pour plus d’informations sur le remplissage, consultez [marges et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="colors"></a>Couleurs

Les étiquettes peuvent être définies pour utiliser une couleur de texte personnalisée via la propriété [`TextColor`](xref:Xamarin.Forms.Label.TextColor) pouvant être liée.

Une attention particulière est nécessaire pour s’assurer que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour les couleurs de texte et d’arrière-plan, vous devez veiller à choisir une valeur par défaut qui fonctionne sur chacune d’elles.

L’exemple de code XAML suivant définit la couleur de texte d’un `Label` :

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

Les captures d’écran suivantes montrent le résultat de la définition de la propriété `TextColor` :

![Exemple d’étiquette TextColor](label-images/textcolor.png)

Pour plus d’informations sur les couleurs, consultez [couleurs](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Polices

Pour plus d’informations sur la spécification des polices sur un `Label`, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Troncation et encapsulation

Les étiquettes peuvent être définies pour gérer du texte qui ne peut pas tenir sur une ligne de plusieurs façons, exposées par la propriété `LineBreakMode`. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) est une énumération avec les valeurs suivantes :

- **HeadTruncation** &ndash; tronque le début du texte, en présentant la fin.
- **CharacterWrap** &ndash; renvoie le texte sur une nouvelle ligne à la limite d’un caractère.
- **MiddleTruncation** &ndash; affiche le début et la fin du texte, en remplaçant par des points de suspension.
- **Nowrap** &ndash; n’habille pas le texte et n’affiche que le texte qui peut tenir sur une seule ligne.
- **TailTruncation** &ndash; affiche le début du texte, en tronquant la fin.
- **WordWrap** &ndash; renvoie le texte à la limite de mot.

## <a name="display-a-specific-number-of-lines"></a>Afficher un nombre spécifique de lignes

Vous pouvez spécifier le nombre de lignes affichées par un [`Label`](xref:Xamarin.Forms.Label) en affectant à la propriété `Label.MaxLines` une valeur `int` :

- Lorsque `MaxLines` a la valeur 0, le `Label` respecte la valeur de la propriété [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) pour afficher une seule ligne, éventuellement tronquée, ou toutes les lignes avec tout le texte.
- Lorsque `MaxLines` a la valeur 1, le résultat est identique à la définition de la propriété [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) sur [`NoWrap`](xref:Xamarin.Forms.LineBreakMode), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)ou [0](xref:Xamarin.Forms.LineBreakMode). Toutefois, le `Label` respecte la valeur de la propriété [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) en ce qui concerne l’emplacement des points de suspension, le cas échéant.
- Lorsque `MaxLines` est supérieur à 1, le `Label` s’affiche jusqu’au nombre de lignes spécifié, tout en respectant la valeur de la propriété [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) en ce qui concerne l’emplacement des points de suspension, le cas échéant. Toutefois, la définition de la propriété `MaxLines` sur une valeur supérieure à 1 n’a aucun effet si la propriété [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) a la valeur [`NoWrap`](xref:Xamarin.Forms.LineBreakMode).

L’exemple de code XAML suivant illustre la définition de la propriété `MaxLines` sur un [`Label`](xref:Xamarin.Forms.Label):

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

Les captures d’écran suivantes montrent le résultat de la définition de la propriété `MaxLines` sur la valeur 2, lorsque le texte est suffisamment long pour occuper plus de 2 lignes :

![Exemple d’étiquette MaxLines](label-images/label-maxlines.png)


## <a name="display-html"></a>Afficher le code HTML

La classe [`Label`](xref:Xamarin.Forms.Label) a une propriété `TextType`, qui détermine si l’instance `Label` doit afficher du texte brut ou du texte html. Cette propriété doit être définie sur l’un des membres de l’énumération `TextType` :

- `Text` indique que le `Label` affichera du texte brut et qu’il s’agit de la valeur par défaut de la propriété `Label.TextType`.
- `Html` indique que le `Label` affichera le texte HTML.

Par conséquent, [`Label`](xref:Xamarin.Forms.Label) instances peuvent afficher du code HTML en affectant à la propriété `Label.TextType` la valeur `Html` et à la propriété `Label.Text` la valeur d’une chaîne HTML :

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

Dans l’exemple ci-dessus, les guillemets doubles du code HTML doivent être placés dans une séquence d’échappement à l’aide du symbole `\`.

En XAML, les chaînes HTML peuvent devenir illisibles en raison de l’échappement en plus de la `<` et des symboles `>` :

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

Sinon, pour une meilleure lisibilité, le code HTML peut être inséré dans une section `CDATA` :

```xaml
<Label TextType="Html">
    <x:String>
        <![CDATA[
        This is <strong style="color:red">HTML</strong> text.
        ]]>
    </x:String>
</Label>
```

Dans cet exemple, la propriété `Label.Text` est définie sur la chaîne HTML qui est incluse dans la section `CDATA`. Cela fonctionne, car la propriété `Text` est la `ContentProperty` pour la classe `Label`.

Les captures d’écran suivantes montrent une [`Label`](xref:Xamarin.Forms.Label) afficher le code HTML :

![Captures d’écran d’une étiquette affichant du code HTML, sur iOS et Android](label-images/label-html.png)

> [!IMPORTANT]
> L’affichage du HTML dans un [`Label`](xref:Xamarin.Forms.Label) est limité aux balises HTML prises en charge par la plateforme sous-jacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texte mis en forme

Les étiquettes exposent une propriété [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) qui autorise la présentation de texte avec plusieurs polices et couleurs dans la même vue.

La propriété `FormattedText` est de type [`FormattedString`](xref:Xamarin.Forms.FormattedString), qui comprend une ou plusieurs instances [`Span`](xref:Xamarin.Forms.Span) , définies via la propriété [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) . Vous pouvez utiliser les propriétés de `Span` suivantes pour définir l’apparence visuelle :

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) : couleur de l’arrière-plan de l’étendue.
- `CharacterSpacing`, de type `double`, est l’espacement entre les caractères du texte de `Span`.
- [`Font`](xref:Xamarin.Forms.Span.Font) : police du texte de l’étendue.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) : attributs de police pour le texte de l’étendue.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) : famille de polices à laquelle appartient la police du texte de l’étendue.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) : taille de la police du texte de l’étendue.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) : couleur du texte de l’étendue. Cette propriété est obsolète et a été remplacée par la propriété `TextColor`.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) : multiplicateur à appliquer à la hauteur de ligne par défaut de l’étendue. Pour plus d’informations, consultez [hauteur de ligne](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style) : style à appliquer à l’étendue.
- [`Text`](xref:Xamarin.Forms.Span.Text) : texte de l’étendue.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) : couleur du texte de l’étendue.
- `TextDecorations` : décorations à appliquer au texte de l’étendue. Pour plus d’informations, consultez [décorations de texte](#text-decorations).

Les propriétés [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor), [`Text`](xref:Xamarin.Forms.Span.Text)et [`Text`](xref:Xamarin.Forms.Span.Text) pouvant être liées ont un mode de liaison par défaut de [`OneWay`](xref:Xamarin.Forms.BindingMode). Pour plus d’informations sur ce mode de liaison, consultez [le mode de liaison par défaut](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) dans le Guide de [mode de liaison](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

En outre, la propriété [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) peut être utilisée pour définir une collection de détecteurs de mouvement qui répondront aux gestes sur le [`Span`](xref:Xamarin.Forms.Span).

> [!NOTE]
> Il n’est pas possible d’afficher du code HTML dans un [`Span`](xref:Xamarin.Forms.Span).

L’exemple de code XAML suivant illustre une propriété `FormattedText` qui se compose de trois instances [`Span`](xref:Xamarin.Forms.Span) :

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
> La propriété [`Text`](xref:Xamarin.Forms.Span.Text) d’un `Span` peut être définie via la liaison de données. Pour plus d’informations, consultez [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Notez qu’un [`Span`](xref:Xamarin.Forms.Span) peut également répondre à tous les mouvements ajoutés à la collection de [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) de l’étendue. Par exemple, un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a été ajouté au deuxième `Span` dans les exemples de code ci-dessus. Par conséquent, lorsque cette `Span` est exploitée, le `TapGestureRecognizer` répond en exécutant la `ICommand` définie par la propriété [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) . Pour plus d’informations sur les détecteurs de mouvement, consultez [Xamarin. Forms gestes](~/xamarin-forms/app-fundamentals/gestures/index.md).

Les captures d’écran suivantes montrent le résultat de la définition de la propriété `FormattedString` sur trois instances `Span` :

![Exemple d’étiquette FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Hauteur de ligne

La hauteur verticale d’un [`Label`](xref:Xamarin.Forms.Label) et d’un [`Span`](xref:Xamarin.Forms.Span) peut être personnalisée en affectant à la propriété [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) ou [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) la valeur `double`. Sur iOS et Android, ces valeurs sont des multiplicateurs de la hauteur de ligne d’origine, et sur la plateforme Windows universelle (UWP) la valeur de la propriété `Label.LineHeight` est un multiplicateur de la taille de police de l’étiquette.

> [!NOTE]
>
> - Sur iOS, les propriétés [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) et [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) modifient la hauteur de ligne du texte qui s’ajuste sur une seule ligne, et le texte qui s’encapsule sur plusieurs lignes.
> - Sur Android, les propriétés [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) et [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) modifient uniquement la hauteur de ligne du texte encapsulé sur plusieurs lignes.
> - Sur UWP, la propriété [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) modifie la hauteur de ligne du texte encapsulé sur plusieurs lignes, et la propriété [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) n’a aucun effet.

L’exemple de code XAML suivant illustre la définition de la propriété [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) sur un [`Label`](xref:Xamarin.Forms.Label):

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

Les captures d’écran suivantes montrent le résultat de la définition de la propriété [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) sur 1,8 :

![Exemple d’étiquette LineHeight](label-images/label-lineheight.png)

L’exemple de code XAML suivant illustre la définition de la propriété [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) sur un [`Span`](xref:Xamarin.Forms.Span):

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

Les captures d’écran suivantes montrent le résultat de la définition de la propriété [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) sur 1,8 :

![Exemple d’étendue de LineHeight](label-images/span-lineheight.png)

## <a name="hyperlinks"></a>Liens hypertexte

Le texte affiché par les instances [`Label`](xref:Xamarin.Forms.Label) et [`Span`](xref:Xamarin.Forms.Span) peut être converti en liens hypertexte à l’aide de l’approche suivante :

1. Définissez les propriétés `TextColor` et `TextDecoration` de la [`Label`](xref:Xamarin.Forms.Label) ou de la [`Span`](xref:Xamarin.Forms.Span).
1. Ajoutez une [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) à la collection de [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) du [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span), dont la propriété [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) est liée à un 0, et dont la propriété [2](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) contient l’URL à ouvrir.
1. Définissez les `ICommand` qui seront exécutées par le [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Écrivez le code qui sera exécuté par le `ICommand`.

L’exemple de code suivant, tiré de l’exemple de [démonstrations de lien hypertexte](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , illustre une [`Label`](xref:Xamarin.Forms.Label) dont le contenu est défini à partir de plusieurs instances de [`Span`](xref:Xamarin.Forms.Span) :

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

Dans cet exemple, la première et la troisième instance [`Span`](xref:Xamarin.Forms.Span) comprennent du texte, tandis que la deuxième `Span` représente un lien hypertexte tappable. Sa couleur de texte est définie sur bleu et a une décoration de texte souligné. Cela crée l’apparence d’un lien hypertexte, comme illustré dans les captures d’écran suivantes :

[![Liens hypertexte](label-images/hyperlinks-small.png "Liens hypertexte")](label-images/hyperlinks-large.png#lightbox)

Lorsque le lien hypertexte est frappé, le [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) répond en exécutant la `ICommand` définie par sa propriété [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) . En outre, l’URL spécifiée par la propriété [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) est transmise à la `ICommand` en tant que paramètre.

Le code-behind de la page XAML contient l’implémentation de `TapCommand` :

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

L' `TapCommand` exécute la méthode `Launcher.OpenAsync`, en passant la valeur de la propriété [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) en tant que paramètre. La méthode `Launcher.OpenAsync` est fournie par Xamarin. Essentials et ouvre l’URL dans un navigateur Web. Par conséquent, l’effet global est que lorsque le lien hypertexte est frappé sur la page, un navigateur Web s’affiche et l’URL associée au lien hypertexte est vers laquelle il est accédé.

### <a name="creating-a-reusable-hyperlink-class"></a>Création d’une classe HYPERLINK réutilisable

L’approche précédente de la création d’un lien hypertexte requiert l’écriture d’un code répétitif chaque fois que vous avez besoin d’un lien hypertexte dans votre application. Toutefois, les classes [`Label`](xref:Xamarin.Forms.Label) et [`Span`](xref:Xamarin.Forms.Span) peuvent être sous-classées pour créer des classes `HyperlinkLabel` et `HyperlinkSpan`, avec la reconnaissance de mouvement et le code de mise en forme du texte ajoutés ici.

L’exemple de code suivant, tiré de l’exemple de [démonstrations de lien hypertexte](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , illustre une classe `HyperlinkSpan` :

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

La classe `HyperlinkSpan` définit une propriété `Url`, et associée [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), et le constructeur définit l’apparence du lien hypertexte et le [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) qui répondra lorsque le lien hypertexte sera appuyé. Lorsqu’un `HyperlinkSpan` est frappé, le `TapGestureRecognizer` répond en exécutant la méthode `Launcher.OpenAsync` pour ouvrir l’URL, spécifiée par la propriété `Url`, dans un navigateur Web.

La classe `HyperlinkSpan` peut être consommée en ajoutant une instance de la classe au XAML :

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

Les sections précédentes décrivaient le paramétrage des propriétés [`Label`](xref:Xamarin.Forms.Label) et [`Span`](xref:Xamarin.Forms.Span) par instance. Toutefois, les ensembles de propriétés peuvent être regroupés dans un style qui est appliqué de façon cohérente à une ou plusieurs vues. Cela peut augmenter la lisibilité du code et faciliter l’implémentation des modifications de conception. Pour plus d’informations, consultez [styles](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Liens connexes

- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Liens hypertexte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Création d’Mobile Apps avec Xamarin. Forms, chapitre 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API Label](xref:Xamarin.Forms.Label)
- [API span](xref:Xamarin.Forms.Span)
