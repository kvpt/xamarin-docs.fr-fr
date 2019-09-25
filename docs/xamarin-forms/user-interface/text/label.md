---
title: Étiquette de Xamarin.Forms
description: Cet article explique comment utiliser la classe d’étiquette de Xamarin.Forms pour afficher le texte unique et plusieurs lignes dans les applications.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 19cc24c3937921f4f4d95584e1cd656c37b4155f
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71105967"
---
# <a name="xamarinforms-label"></a>Étiquette de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Texte affiché dans Xamarin.Forms_

Le [ `Label` ](xref:Xamarin.Forms.Label) vue est utilisée pour afficher du texte, à la fois unique et plusieurs lignes. Les étiquettes peuvent possèdent des ornements de texte, couleur de texte et utiliser des polices personnalisées (familles de tailles et options).

## <a name="text-decorations"></a>Ornements de texte

Ornements de texte souligné et barré peuvent être appliquées à [ `Label` ](xref:Xamarin.Forms.Label) instances en définissant le `Label.TextDecorations` propriété à un ou plusieurs `TextDecorations` membres de l’énumération :

- `None`
- `Underline`
- `Strikethrough`

L’exemple XAML suivant montre comment définir le `Label.TextDecorations` propriété :

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

Le code c# équivalent est :

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

L’émission des captures d’écran suivante le `TextDecorations` appliqués aux membres de l’énumération [ `Label` ](xref:Xamarin.Forms.Label) instances :

![Étiquettes avec décorations de texte](label-images/label-textdecorations.png)

> [!NOTE]
> Ornements de texte peuvent également être appliqués aux [ `Span` ](xref:Xamarin.Forms.Span) instances. Pour plus d’informations sur la `Span` de classe, consultez [texte au format](#Formatted_Text).

## <a name="colors"></a>Couleurs

Étiquettes peuvent être définies pour utiliser une couleur de texte personnalisé via la peut être liée [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propriété.

Une attention particulière est nécessaire pour garantir que les couleurs seront utilisables sur chaque plateforme. Étant donné que chaque plateforme a des valeurs par défaut différentes pour les couleurs de texte et d’arrière-plan, vous devrez veiller à choisir une valeur par défaut qui fonctionne sur chacun.

L’exemple XAML suivant définit la couleur du texte d’un `Label`:

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

Le code c# équivalent est :

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

Les captures d’écran suivantes affichent le résultat du paramètre de la `TextColor` propriété :

![Exemple d’étiquette TextColor](label-images/textcolor.png)

Pour plus d’informations sur les couleurs, consultez [couleurs](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Polices

Pour plus d’informations sur la spécification des polices dans une `Label`, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Troncation et habillage

Étiquettes peuvent être définies pour gérer le texte qui ne rentrent pas sur une seule ligne de plusieurs manières, exposées par le `LineBreakMode` propriété. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) est une énumération avec les valeurs suivantes :

- **HeadTruncation** &ndash; tronque la tête du texte, indiquant la fin.
- **CharacterWrap** &ndash; encapsule le texte sur une nouvelle ligne à une limite de caractère.
- **MiddleTruncation** &ndash; affiche le début et la fin du texte, avec l’opération de remplacement intermédiaire par les points de suspension.
- **NoWrap** &ndash; n’encapsule pas de texte, afficher uniquement de texte peut tenir sur une seule ligne.
- **TailTruncation** &ndash; illustre le début du texte, la troncation de la fin.
- **WordWrap** &ndash; encapsule le texte à la limite de mot.

## <a name="displaying-a-specific-number-of-lines"></a>Affichage d’un nombre spécifique de lignes

Le nombre de lignes affichées par un [ `Label` ](xref:Xamarin.Forms.Label) peut être spécifié en définissant le `Label.MaxLines` propriété un `int` valeur :

- Lorsque `MaxLines` est 0, le `Label` respecte la valeur de la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriété pour montrer qu’une seule ligne, éventuellement tronquée, toutes les lignes ou avec tout le texte.
- Lorsque `MaxLines` est 1, le résultat est identique au paramètre la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriété [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode), ou [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode). Toutefois, le `Label` respecte la valeur de la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriété en ce qui concerne le positionnement des points de suspension, le cas échéant.
- Lorsque `MaxLines` est supérieur à 1, le `Label` affiche le nombre spécifié de lignes, tout en respectant la valeur de la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriété en ce qui concerne le positionnement des points de suspension, le cas échéant. Cependant, la définition de la `MaxLines` propriété à une valeur supérieure à 1 n’a aucun effet si le [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriété est définie sur [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode).

L’exemple XAML suivant montre comment définir le `MaxLines` propriété sur un [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

Le code c# équivalent est :

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

Les captures d’écran suivantes affichent le résultat du paramètre de la `MaxLines` propriété à 2, lorsque le texte est suffisamment long pour occuper plus de 2 lignes :

![Exemple d’étiquette MaxLines](label-images/label-maxlines.png)

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texte mis en forme

Les étiquettes exposent une [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propriété qui autorise la présentation de texte avec plusieurs polices et couleurs dans la même vue.

Le `FormattedText` propriété est de type [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), qui comprend un ou plusieurs [ `Span` ](xref:Xamarin.Forms.Span) instances, définis par le biais du [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propriété . Ce qui suit `Span` propriétés peuvent être utilisées pour définir l’apparence visuelle :

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) – la couleur d’arrière-plan span.
- [`Font`](xref:Xamarin.Forms.Span.Font) – la police du texte dans l’étendue.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) – les attributs de police du texte dans l’étendue.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – la famille de polices à laquelle appartient la police du texte dans l’étendue.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – la taille de la police du texte dans l’étendue.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) – la couleur du texte dans l’étendue. Cette propriété est obsolète et a été remplacée par le `TextColor` propriété.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -le multiplicateur à appliquer à la hauteur de ligne par défaut de l’étendue. Pour plus d’informations, consultez [hauteur de ligne](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style): style à appliquer à l’étendue.
- [`Text`](xref:Xamarin.Forms.Span.Text) – le texte de l’étendue.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) – la couleur du texte dans l’étendue.
- `TextDecorations` -les ornements à appliquer au texte dans l’étendue. Pour plus d’informations, consultez [ornements de texte](#text-decorations).

> [!NOTE]
> Les [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)propriétés [`Text`](xref:Xamarin.Forms.Span.Text), [`OneWay`](xref:Xamarin.Forms.BindingMode)et [`Text`](xref:Xamarin.Forms.Span.Text) pouvant être liées ont un mode de liaison par défaut de. Pour plus d’informations sur ce mode de liaison, consultez [le mode de liaison par défaut](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) dans le Guide de [mode de liaison](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

En outre, le [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) propriété peut être utilisée pour définir une collection de modules de reconnaissance de mouvement qui répondront aux mouvements sur le [ `Span` ](xref:Xamarin.Forms.Span).

L’exemple XAML suivant montre un `FormattedText` propriété se compose de trois [ `Span` ](xref:Xamarin.Forms.Span) instances :

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

Le code c# équivalent est :

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
> Le [ `Text` ](xref:Xamarin.Forms.Span.Text) propriété d’un `Span` peut être définie via la liaison de données. Pour plus d’informations, consultez [une liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Notez qu’un [ `Span` ](xref:Xamarin.Forms.Span) peut également répondre à n’importe quel mouvements sont ajoutés à la balise span [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) collection. Par exemple, un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) a été ajouté à la seconde `Span` dans les exemples de code ci-dessus. Par conséquent, lorsque cela `Span` l’appui sur la `TapGestureRecognizer` répond en exécutant la `ICommand` défini par le [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriété. Pour plus d’informations sur les modules de reconnaissance de mouvement, consultez [Xamarin.Forms mouvements](~/xamarin-forms/app-fundamentals/gestures/index.md).

Les captures d’écran suivantes affichent le résultat du paramètre de la `FormattedString` propriété à trois `Span` instances :

![Exemple d’étiquette FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Hauteur de ligne

La hauteur verticale d’un [ `Label` ](xref:Xamarin.Forms.Label) et un [ `Span` ](xref:Xamarin.Forms.Span) peut être personnalisé en définissant le [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriété ou [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) à un `double` valeur. Sur iOS et Android, ces valeurs sont des multiplicateurs de la hauteur de ligne d’origine et sur la plateforme Windows universelle (UWP) le `Label.LineHeight` valeur de propriété est un multiplicateur de la taille de police d’étiquette.

> [!NOTE]
>
> - Sur iOS, le [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) et [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriétés modifier la hauteur de ligne de texte qui tienne sur une seule ligne et qui est automatiquement renvoyée à plusieurs lignes.
> - Sur Android, le [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) et [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriétés modifier uniquement la hauteur de ligne de texte est automatiquement renvoyée à plusieurs lignes.
> - Sur UWP, le [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriété change la hauteur de ligne de texte est automatiquement renvoyée à plusieurs lignes, et le [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriété n’a aucun effet.

L’exemple XAML suivant montre comment définir le [ `LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriété sur un [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

Le code c# équivalent est :

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

Les captures d’écran suivantes affichent le résultat du paramètre de la [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriété 1.8 :

![Exemple d’étiquette LineHeight](label-images/label-lineheight.png)

L’exemple XAML suivant montre comment définir le [ `LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriété sur un [ `Span` ](xref:Xamarin.Forms.Span):

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

Le code c# équivalent est :

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

Les captures d’écran suivantes affichent le résultat du paramètre de la [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriété 1.8 :

![Exemple d’étendue de LineHeight](label-images/span-lineheight.png)

## <a name="hyperlinks"></a>Liens hypertexte

Le texte affiché par [`Label`](xref:Xamarin.Forms.Label) les [`Span`](xref:Xamarin.Forms.Span) instances et peut être converti en liens hypertexte avec l’approche suivante :

1. Définissez les `TextColor` propriétés `TextDecoration` et de [`Label`](xref:Xamarin.Forms.Label) ou. [`Span`](xref:Xamarin.Forms.Span)
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

Lorsque le lien hypertexte est frappé, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) le répond en exécutant le `ICommand` défini par sa [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriété. En outre, l’URL spécifiée par la [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriété est passée `ICommand` à en tant que paramètre.

Le code-behind de la page XAML contient l' `TapCommand` implémentation :

```csharp
public partial class MainPage : ContentPage
{
    public ICommand TapCommand => new Command<string>(OpenBrowser);

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }

    void OpenBrowser(string url)
    {
        Device.OpenUri(new Uri(url));
    }
}
```

Exécute la `OpenBrowser` méthode, en passant la valeur [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) de la propriété en tant que paramètre. `TapCommand` Ensuite, cette méthode appelle la [`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*) méthode pour ouvrir l’URL dans un navigateur Web. Par conséquent, l’effet global est que lorsque le lien hypertexte est frappé sur la page, un navigateur Web s’affiche et l’URL associée au lien hypertexte est vers laquelle il est accédé.

### <a name="creating-a-reusable-hyperlink-class"></a>Création d’une classe HYPERLINK réutilisable

L’approche précédente de la création d’un lien hypertexte requiert l’écriture d’un code répétitif chaque fois que vous avez besoin d’un lien hypertexte dans votre application. Toutefois, [`Label`](xref:Xamarin.Forms.Label) les classes et [`Span`](xref:Xamarin.Forms.Span) peuvent être sous-classées pour créer `HyperlinkLabel` des `HyperlinkSpan` classes et, avec le module de reconnaissance de mouvement et le code de mise en forme du texte ajoutés ici.

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
            Command = new Command(() => Device.OpenUri(new Uri(Url)))
        });
    }
}
```

La `HyperlinkSpan` classe définit une `Url` propriété, et associée [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), et le constructeur définit l’apparence du lien hypertexte [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) et le qui répondra lorsque le lien hypertexte sera appuyé. Lorsqu’un `HyperlinkSpan` est frappé, le `TapGestureRecognizer` répond en exécutant la [`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*) méthode pour ouvrir l’URL, spécifiée par la `Url` propriété, dans un navigateur Web.

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

## <a name="styling-labels"></a>Étiquettes de style

Les sections précédentes couverts paramètre [ `Label` ](xref:Xamarin.Forms.Label) et [ `Span` ](xref:Xamarin.Forms.Span) propriétés sur chaque instance. Toutefois, les ensembles de propriétés peuvent être regroupées dans un style qui est appliqué uniformément à une ou plusieurs vues. Cela peut augmenter la lisibilité du code et apporter des modifications de conception plus facile à implémenter. Pour plus d’informations, consultez [Styles](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Liens connexes

- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Liens hypertexte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Création d’applications mobiles avec Xamarin.Forms, chapitre 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de l’étiquette](xref:Xamarin.Forms.Label)
- [Étendue API](xref:Xamarin.Forms.Span)
