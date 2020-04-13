---
title: Étiquette Xamarin.Forms
description: Cet article explique comment utiliser la classe Xamarin.Forms Label pour afficher du texte simple et multi-lignes dans les applications.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: bed6b8a774ecb352427f16b78d10e50821f92701
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987593"
---
# <a name="xamarinforms-label"></a>Étiquette Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Afficher le texte dans Xamarin.Forms_

La [`Label`](xref:Xamarin.Forms.Label) vue est utilisée pour afficher le texte, à la fois simple et multi-ligne. Les étiquettes peuvent avoir des décorations de texte, du texte coloré, et utiliser des polices personnalisées (familles, tailles et options).

## <a name="text-decorations"></a>Décorations de texte

Des décorations de texte de soulignement [`Label`](xref:Xamarin.Forms.Label) et de `Label.TextDecorations` grève peuvent `TextDecorations` être appliquées aux instances en fixant la propriété à un ou plusieurs membres de recensement :

- `None`
- `Underline`
- `Strikethrough`

L’exemple XAML suivant `Label.TextDecorations` démontre l’établissement de la propriété :

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

Les captures d’écran suivantes montrent que les membres de l’énumération `TextDecorations` se sont appliqués aux [`Label`](xref:Xamarin.Forms.Label) cas suivants :

![Étiquettes avec décorations de texte](label-images/label-textdecorations.png)

> [!NOTE]
> Des décorations de texte [`Span`](xref:Xamarin.Forms.Span) peuvent également être appliquées aux instances. Pour plus d’informations sur la `Span` classe, voir [Formatted Text](#Formatted_Text).

## <a name="character-spacing"></a>Espacement des caractères

L’espacement des personnages [`Label`](xref:Xamarin.Forms.Label) peut être `Label.CharacterSpacing` appliqué `double` aux instances en fixant la propriété à une valeur :

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Le code C# équivalent est :

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

Le résultat est que les caractères dans le texte affiché par les [`Label`](xref:Xamarin.Forms.Label) unités sont espacées `CharacterSpacing` d’appareils indépendants.

## <a name="new-lines"></a>Nouvelles lignes

Il existe deux techniques principales [`Label`](xref:Xamarin.Forms.Label) pour forcer le texte dans une nouvelle ligne, de XAML:

1. Utilisez le caractère d’alimentation de ligne unicode, qui est "&#10;".
1. Spécifiez votre texte à l’aide de la syntaxe *d’élément de propriété.*

Le code suivant montre un exemple des deux techniques :

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

Dans le texte, le texte peut être forcé sur une nouvelle ligne avec le caractère « n » :

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Couleurs

Les étiquettes peuvent être définies pour [`TextColor`](xref:Xamarin.Forms.Label.TextColor) utiliser une couleur de texte personnalisée via la propriété liant.

Un soin spécial est nécessaire pour s’assurer que les couleurs seront utilisables sur chaque plate-forme. Parce que chaque plate-forme a des défauts différents pour le texte et les couleurs de fond, vous devrez faire attention à choisir un défaut qui fonctionne sur chacun.

L’exemple XAML suivant définit `Label`la couleur du texte d’un :

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

Les captures d’écran suivantes `TextColor` montrent le résultat de la mise en place de la propriété:

![Exemple d’étiquette TextColor](label-images/textcolor.png)

Pour plus d’informations sur les couleurs, voir [Couleurs](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Polices

Pour plus d’informations sur `Label`la spécifier des polices sur un , voir [Fonts](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncation et emballage

Les étiquettes peuvent être définies pour manipuler le texte qui ne `LineBreakMode` peut pas s’adapter sur une ligne d’une ou de plusieurs façons, exposée par la propriété. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)est un recensement avec les valeurs suivantes :

- **HeadTruncation** &ndash; tronque la tête du texte, montrant la fin.
- **CharacterWrap** &ndash; enveloppe le texte sur une nouvelle ligne à une limite de caractère.
- **MiddleTruncation** &ndash; affiche le début et la fin du texte, avec le milieu remplacer par une ellipsis.
- **NoWrap** &ndash; n’enveloppe pas le texte, affichant seulement autant de texte que peut s’adapter sur une seule ligne.
- **TailTruncation** &ndash; montre le début du texte, tronquant la fin.
- **WordWrap** &ndash; enveloppe le texte à la limite du mot.

## <a name="display-a-specific-number-of-lines"></a>Afficher un nombre spécifique de lignes

Le nombre de lignes [`Label`](xref:Xamarin.Forms.Label) affichées par `Label.MaxLines` un peut `int` être spécifié en définissant la propriété à une valeur :

- Quand `MaxLines` est -1, qui est `Label` sa valeur par [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) défaut, le respecte la valeur de la propriété pour afficher soit une seule ligne, peut-être tronqué, ou toutes les lignes avec tout le texte.
- Quand `MaxLines` est 0, le `Label` n’est pas affiché.
- Quand `MaxLines` est 1, le résultat [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) est [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)identique [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode) [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)à [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode)la mise de la propriété à , , , ou . Toutefois, `Label` la volonté respectera [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) la valeur du bien en ce qui concerne le placement d’une élipsis, le cas échéant.
- Lorsqu’il `MaxLines` est supérieur `Label` à 1, le s’affiche jusqu’au nombre [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) spécifié de lignes, tout en respectant la valeur de la propriété en ce qui concerne le placement d’une ellipsis, le cas échéant. Toutefois, le `MaxLines` fait de fixer la propriété à [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) une valeur [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)supérieure à 1 n’a aucun effet si la propriété est fixée à .

L’exemple XAML suivant `MaxLines` démontre l’établissement de la propriété sur un [`Label`](xref:Xamarin.Forms.Label):

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

Les captures d’écran suivantes `MaxLines` montrent le résultat de la mise en place de la propriété à 2, lorsque le texte est assez long pour occuper plus de 2 lignes:

![Étiquette MaxLines Exemple](label-images/label-maxlines.png)

## <a name="display-html"></a>Afficher le code HTML

La [`Label`](xref:Xamarin.Forms.Label) classe `TextType` dispose d’une propriété, qui détermine si l’instance doit afficher du `Label` texte simple, ou du texte HTML. Cette propriété doit être fixée à `TextType` l’un des membres de l’énumération :

- `Text`indique que `Label` le texte sera affiché et est `Label.TextType` la valeur par défaut de la propriété.
- `Html`indique que `Label` le texte HTML affichera.

Par [`Label`](xref:Xamarin.Forms.Label) conséquent, les instances `Label.TextType` peuvent `Html`afficher HTML `Label.Text` en définissant la propriété à , et la propriété à une chaîne HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

Dans l’exemple ci-dessus, les caractères de `\` double citation dans le HTML doivent être échappés en utilisant le symbole.

Dans XAML, les chaînes HTML peuvent devenir illisibles en raison de l’évasion en outre de la `<` et `>` des symboles:

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

Alternativement, pour une plus grande lisibilité, `CDATA` le HTML peut être inlinisé dans une section :

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

Dans cet exemple, la `Label.Text` propriété est définie à la chaîne `CDATA` HTML qui est inlined dans la section. Cela fonctionne `Text` parce que `ContentProperty` la `Label` propriété est le pour la classe.

Les captures d’écran suivantes montrent un [`Label`](xref:Xamarin.Forms.Label) HTML affichant :

![Captures d’écran d’un label affichant HTML, sur iOS et Android](label-images/label-html.png)

> [!IMPORTANT]
> Afficher HTML [`Label`](xref:Xamarin.Forms.Label) dans un est limité aux balises HTML qui sont prises en charge par la plate-forme sous-jacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texte mis en forme

Les étiquettes exposent une [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propriété qui permet la présentation du texte avec plusieurs polices et couleurs dans la même vue.

La `FormattedText` propriété est [`FormattedString`](xref:Xamarin.Forms.FormattedString)de type , [`Span`](xref:Xamarin.Forms.Span) qui comprend un [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) ou plusieurs cas, fixés via la propriété. Les `Span` propriétés suivantes peuvent être utilisées pour définir l’apparence visuelle :

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)la couleur de l’arrière-plan de la travée.
- `CharacterSpacing`, de `double`type , est l’espacement entre les caractères du `Span` texte.
- [`Font`](xref:Xamarin.Forms.Span.Font)la police pour le texte dans la travée.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)- les attributs de police pour le texte dans la durée.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)- la famille de police à laquelle appartient la police pour le texte dans la travée.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize)la taille de la police pour le texte dans la travée.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)- la couleur du texte dans la travée. Cette propriété est obsolète et `TextColor` a été remplacée par la propriété.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)- le multiplicateur à appliquer à la hauteur de la ligne par défaut de la travée. Pour plus d’informations, voir [Line Height](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style)le style à appliquer à la portée.
- [`Text`](xref:Xamarin.Forms.Span.Text)le texte de la travée.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor)- la couleur du texte dans la travée.
- `TextDecorations`- les décorations à appliquer au texte dans la travée. Pour plus d’informations, voir [Décorations de texte](#text-decorations).

Les [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text)propriétés [`Text`](xref:Xamarin.Forms.Span.Text) , , et liantes [`OneWay`](xref:Xamarin.Forms.BindingMode)ont un mode de liaison par défaut de . Pour plus d’informations sur ce mode de liaison, consultez [le mode de liaison par défaut](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) dans le guide mode de [liaison.](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)

En outre, [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) la propriété peut être utilisée pour définir une collection de [`Span`](xref:Xamarin.Forms.Span)reconnaissances de geste qui répondront aux gestes sur le .

> [!NOTE]
> Il n’est pas possible [`Span`](xref:Xamarin.Forms.Span)d’afficher HTML dans un .

L’exemple XAML suivant `FormattedText` démontre une [`Span`](xref:Xamarin.Forms.Span) propriété qui se compose de trois cas :

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
> La [`Text`](xref:Xamarin.Forms.Span.Text) propriété `Span` d’un peut être définie par la liaison de données. Pour plus d’informations, consultez [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Notez [`Span`](xref:Xamarin.Forms.Span) qu’un peut également répondre à tous les [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) gestes qui sont ajoutés à la collection de la portée. Par exemple, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a été ajouté `Span` au deuxième dans les exemples de code ci-dessus. Par conséquent, `Span` lorsque cela `TapGestureRecognizer` est exploité le `ICommand` répond [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) à l’exécution de la définie par la propriété. Pour plus d’informations sur les reconnaisseurs de gestes, voir [Xamarin.Forms Gestures](~/xamarin-forms/app-fundamentals/gestures/index.md).

Les captures d’écran suivantes `FormattedString` montrent `Span` le résultat de la mise en place de la propriété à trois cas:

![Étiquette FormattedText Exemple](label-images/formattedtext.png)

## <a name="line-height"></a>Hauteur de ligne

La hauteur verticale [`Label`](xref:Xamarin.Forms.Label) d’un et d’un [`Span`](xref:Xamarin.Forms.Span) peut être personnalisée en définissant la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriété ou [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) à une `double` valeur. Sur iOS et Android ces valeurs sont multiplicateurs de la hauteur de `Label.LineHeight` la ligne d’origine, et sur la plate-forme Windows universelle (UWP) la valeur de la propriété est un multiplicateur de la taille de police d’étiquette.

> [!NOTE]
>
> - Sur iOS, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) les propriétés modifient la hauteur de ligne du texte qui s’adapte sur une seule ligne, et le texte qui s’enroule sur plusieurs lignes.
> - Sur Android, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) les propriétés et les propriétés ne modifient que la hauteur de ligne du texte qui s’enroule sur plusieurs lignes.
> - Sur UWP, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) la propriété modifie la hauteur de ligne du [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) texte qui s’enroule sur plusieurs lignes, et la propriété n’a aucun effet.

L’exemple XAML suivant [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) démontre l’établissement de la propriété sur un [`Label`](xref:Xamarin.Forms.Label):

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

Les captures d’écran suivantes [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) montrent le résultat de la mise en place de la propriété à 1,8:

![Label LineHeight Exemple](label-images/label-lineheight.png)

L’exemple XAML suivant [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) démontre l’établissement de la propriété sur un [`Span`](xref:Xamarin.Forms.Span):

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

Les captures d’écran suivantes [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) montrent le résultat de la mise en place de la propriété à 1,8:

![Span LineHeight Exemple](label-images/span-lineheight.png)

## <a name="padding"></a>Remplissage

Le rembourrage représente l’espace entre un élément et ses éléments enfant, et est utilisé pour séparer l’élément de son propre contenu. Le rembourrage [`Label`](xref:Xamarin.Forms.Label) peut être `Label.Padding` appliqué aux [`Thickness`](xref:Xamarin.Forms.Thickness) instances en fixant la propriété à une valeur :

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
> Sur iOS, [`Label`](xref:Xamarin.Forms.Label) lorsqu’un est `Padding` créé qui définit la propriété, le rembourrage sera appliqué et la valeur de rembourrage peut être mise à jour plus tard. Toutefois, lorsque `Label` l’un est créé `Padding` qui ne fixe pas la propriété, tenter de le définir plus tard n’aura aucun effet.
>
> Sur Android et la plate-forme Windows universelle, la valeur de la `Padding` propriété peut être spécifiée lorsque la `Label` propriété est créée, ou plus tard.

Pour plus d’informations sur le rembourrage, voir [Margins and Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Liens hypertexte

Le texte [`Label`](xref:Xamarin.Forms.Label) affiché [`Span`](xref:Xamarin.Forms.Span) par et les instances peuvent être transformés en hyperliens avec l’approche suivante :

1. Définissez `TextColor` `TextDecoration` les propriétés et les propriétés du [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span).
1. Ajoutez [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) un [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) à la [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span)collection [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) de ou , `ICommand`dont [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) la propriété se lie à un , et dont la propriété contient l’URL à ouvrir.
1. Définir `ICommand` le qui sera exécuté [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)par le .
1. Écrivez le code qui sera `ICommand`exécuté par le .

L’exemple de code suivant, tiré de l’échantillon [Hyperlink Demos,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) montre un dont le [`Label`](xref:Xamarin.Forms.Label) contenu est défini à partir de plusieurs [`Span`](xref:Xamarin.Forms.Span) cas :

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

Dans cet exemple, les [`Span`](xref:Xamarin.Forms.Span) premier et troisième `Span` cas comprennent le texte, tandis que le second représente un lien hypertexte tappable. Il a sa couleur de texte réglée au bleu, et a une décoration de texte de souligner. Cela crée l’apparence d’un hyperlien, comme le montrent les captures d’écran suivantes:

[![Liens hypertexte](label-images/hyperlinks-small.png "Liens hypertexte")](label-images/hyperlinks-large.png#lightbox)

Lorsque l’hyperlien est [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) exploité, le `ICommand` répond en [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) exécutant le défini par sa propriété. En outre, l’URL [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) spécifiée par `ICommand` la propriété sera transmise au paramètre.

Le code-derrière pour la page `TapCommand` XAML contient la mise en œuvre:

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

L’exécute `TapCommand` `Launcher.OpenAsync` la méthode, [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) en passant la valeur de la propriété comme un paramètre. La `Launcher.OpenAsync` méthode est fournie par Xamarin.Essentials, et ouvre l’URL dans un navigateur web. Par conséquent, l’effet global est que lorsque l’hyperlien est exploité sur la page, un navigateur Web apparaît et l’URL associée à l’hyperlien est navigué vers.

### <a name="creating-a-reusable-hyperlink-class"></a>Création d’une classe hyperliens réutilisable

L’approche précédente pour créer un hyperlien nécessite l’écriture de code répétitif chaque fois que vous avez besoin d’un lien hypertexte dans votre application. Cependant, les [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) classes et les classes `HyperlinkLabel` peuvent `HyperlinkSpan` être sous-classées pour créer et classes, avec le code de reconnaissance de geste et de formatage de texte ajouté là-bas.

L’exemple de code suivant, tiré de l’échantillon [Hyperlink Demos,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) montre une `HyperlinkSpan` classe :

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

La `HyperlinkSpan` classe définit `Url` une propriété, et associée, [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)et le constructeur [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) définit l’apparence hyperlien et le qui répondra lorsque l’hyperlien est exploité. Lorsqu’un `HyperlinkSpan` est exploité, le `TapGestureRecognizer` répond `Launcher.OpenAsync` en exécutant la méthode pour `Url` ouvrir l’URL, spécifiée par la propriété, dans un navigateur Web.

La `HyperlinkSpan` classe peut être consommée en ajoutant une instance de la classe à la XAML:

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

## <a name="styling-labels"></a>Étiquettes de style

Les sections précédentes couvraient le réglage [`Label`](xref:Xamarin.Forms.Label) et [`Span`](xref:Xamarin.Forms.Span) les propriétés par instance. Cependant, les ensembles de propriétés peuvent être regroupés en un seul modèle qui est appliqué uniformément à une ou plusieurs vues. Cela peut augmenter la lisibilité du code et faciliter la mise en œuvre des modifications de conception. Pour plus d’informations, voir [Styles](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Liens connexes

- [Texte (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hyperliens (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Création d’applications mobiles avec Xamarin.Forms, Chapitre 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API Label](xref:Xamarin.Forms.Label)
- [Span API](xref:Xamarin.Forms.Span)
