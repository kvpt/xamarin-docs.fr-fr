---
title: Balisage C# Xamarin. Forms
description: Le balisage C# est un ensemble de méthodes et de classes d’assistance Fluent qui permet de simplifier le processus de création d’interfaces utilisateur Xamarin. Forms déclaratives en C#.
ms.prod: xamarin
ms.assetid: D41B9DCD-5C34-4C2F-B177-FC082AB2E9E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/15/2020
ms.openlocfilehash: 4fa8397dafbbdd836f88193081720b4960f1ce5d
ms.sourcegitcommit: bc0c1740aa0708459729c0e671ab3ff7de3e2eee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83425804"
---
# <a name="xamarinforms-c-markup"></a>Balisage C# Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)

Le balisage C# est un ensemble de méthodes et de classes d’assistance Fluent qui permet de simplifier le processus de création d’interfaces utilisateur Xamarin. Forms déclaratives en C#. L’API Fluent fournie par le balisage C# est disponible dans l' `Xamarin.Forms.Markup` espace de noms.

Tout comme avec XAML, le balisage C# permet une séparation nette entre le balisage de l’interface utilisateur et la logique d’interface utilisateur. Cela est possible en séparant le balisage de l’interface utilisateur et la logique d’interface utilisateur en fichiers de classe partielle distincts. Par exemple, pour une page de connexion, le balisage de l’interface utilisateur se trouve dans un fichier nommé *LoginPage.cs*, tandis que la logique de l’interface utilisateur se trouve dans un fichier nommé *LoginPage.Logic.cs*.

Le balisage C# est disponible à partir de Xamarin. Forms 4,6. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante à votre fichier *app.cs* :

```csharp
Device.SetFlags(new string[]{ "Markup_Experimental" });
```

> [!NOTE]
> Le balisage C# est disponible sur toutes les plateformes prises en charge par Xamarin. Forms.

## <a name="basic-example"></a>Exemple de base

L’exemple suivant montre comment définir le contenu de la page sur un nouveau [`Grid`](xref:Xamarin.Forms.Grid) contenant un [`Label`](xref:Xamarin.Forms.Label) et un [`Entry`](xref:Xamarin.Forms.Entry) , en C# :

```csharp
Grid grid = new Grid();

Label label = new Label { Text = "Code: " };
grid.Children.Add(label, 0, 1);

Entry entry = new Entry
{
    Placeholder = "Enter number",
    Keyboard = Keyboard.Numeric,
    BackgroundColor = Color.AliceBlue,
    TextColor = Color.Black,
    FontSize = 15,
    HeightRequest = 44,
    Margin = fieldMargin
};
grid.Children.Add(entry, 0, 2);
Grid.SetColumnSpan(entry, 2);
entry.SetBinding(Entry.TextProperty, new Binding("RegistrationCode"));

Content = grid;
```

Cet exemple crée un [`Grid`](xref:Xamarin.Forms.Grid) objet avec des [`Label`](xref:Xamarin.Forms.Label) objets enfants et [`Entry`](xref:Xamarin.Forms.Entry) . Le `Label` affiche du texte, et les `Entry` données sont liées à la `RegistrationCode` propriété du ViewModel. Chaque vue enfant est définie pour apparaître dans une ligne spécifique du `Grid` , et s' `Entry` étend sur toutes les colonnes de `Grid` . En outre, la hauteur du `Entry` est définie, avec le clavier, les couleurs, la taille de police de son texte et son `Margin` . Enfin, la `Page.Content` propriété a la valeur de l' `Grid` objet.

Le balisage C# permet de réécrire ce code à l’aide de son API Fluent :

```csharp
using Xamarin.Forms.Markup;
using static Xamarin.Forms.Markup.GridRowsColumns;

Content = new Grid
{
  Children =
  {
    new Label { Text = "Code:" }
               .Row (BodyRow.CodeHeader) .Column (BodyCol.Header),

    new Entry { Placeholder = "Enter number", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
               .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
               .Bind (nameof(vm.RegistrationCode))
  }
}};
```

Cet exemple est identique à l’exemple précédent, mais l’API Fluent du balisage C# simplifie le processus de création de l’interface utilisateur en C#.

> [!NOTE]
> Le balisage C# comprend des méthodes d’extension qui définissent des propriétés d’affichage spécifiques. Ces méthodes d’extension ne sont pas destinées à remplacer tous les accesseurs set de propriété. Au lieu de cela, elles sont conçues pour améliorer la lisibilité du code et peuvent être utilisées en association avec les accesseurs set de propriété. Il est recommandé de toujours utiliser une méthode d’extension lorsqu’il en existe une pour une propriété, mais vous pouvez choisir votre solde préféré.

## <a name="data-binding"></a>Liaison de données

Le balisage C# comprend une `Bind` méthode d’extension, ainsi que des surcharges, qui crée une liaison de données entre une propriété de vue pouvant être liée et une propriété spécifiée. La `Bind` méthode connaît la propriété pouvant être liée par défaut pour la majorité des contrôles inclus dans Xamarin. Forms. Par conséquent, il n’est généralement pas nécessaire de spécifier la propriété cible lors de l’utilisation de cette méthode. Toutefois, vous pouvez également inscrire la propriété pouvant être liée par défaut pour des contrôles supplémentaires :

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.Register(HoverButton.CommandProperty, RadialGauge.ValueProperty);
```

La `Bind` méthode peut être utilisée pour établir une liaison à une propriété pouvant être liée :

```csharp
using Xamarin.Forms.Markup;
// ...

new Label { Text = "No data available" }
           .Bind (Label.IsVisibleProperty, nameof(vm.Empty))
```

En outre, la `BindCommand` méthode d’extension peut être liée à la valeur par défaut et aux propriétés d’un contrôle `Command` `CommandParameter` dans un appel de méthode unique :

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap me" }
              .BindCommand (nameof(vm.TapCommand))
```

Par défaut, le `CommandParameter` est lié au contexte de liaison. Vous pouvez également spécifier le chemin d’accès de liaison et la source pour les `Command` `CommandParameter` liaisons et :

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap Me" }
              .BindCommand (nameof(vm.TapCommand), vm, nameof(Item.Id))
```

Dans cet exemple, le contexte de liaison est une `Item` instance, vous n’avez donc pas besoin de spécifier une source pour la `Id` `CommandParameter` liaison.

Si vous devez uniquement effectuer une liaison à `Command` , vous pouvez passer `null` à l' `parameterPath` argument de la `BindCommand` méthode. Vous pouvez également utiliser la `Bind` méthode.

Vous pouvez également enregistrer les `Command` Propriétés et par défaut `CommandParameter` des contrôles supplémentaires :

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.RegisterCommand(
    (CustomViewA.CommandProperty, CustomViewA.CommandParameterProperty),
    (CustomViewB.CommandProperty, CustomViewB.CommandParameterProperty)
);
```

Le code de convertisseur Inline peut être passé à la `Bind` méthode avec les `convert` `convertBack` paramètres et :

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth),
                  convert: (int depth) => new Thickness(depth * 20, 0, 0, 0))
```

Les paramètres de convertisseur de type sécurisé sont également pris en charge :

```csharp
using Xamarin.Forms.Markup;
//...

new Label { }
           .Bind (nameof(viewModel.Text),
                  convert: (string text, int repeat) => string.Concat(Enumerable.Repeat(text, repeat)))
```

En outre, le code et les instances du convertisseur peuvent être réutilisés avec la `FuncConverter` classe :

```csharp
using Xamarin.Forms.Markup;
//...

FuncConverter<int, Thickness> treeMarginConverter = new FuncConverter<int, Thickness>(depth => new Thickness(depth * 20, 0, 0, 0));
new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth), converter: treeMarginConverter),
```

La `FuncConverter` classe prend également en charge les `CultureInfo` objets :

```csharp
using Xamarin.Forms.Markup;
//...

cultureAwareConverter = new FuncConverter<DateTimeOffset, string, int>(
    (date, daysToAdd, culture) => date.AddDays(daysToAdd).ToString(culture)
);
```

Il est également possible de lier des données à `Span` des objets spécifiés avec la `FormattedText` propriété :

```csharp
using Xamarin.Forms.Markup;
//...

new Label { } .FormattedText (
    new Span { Text = "Built with " },
    new Span { TextColor = Color.Blue, TextDecorations = TextDecorations.Underline }
              .BindTapGesture (nameof(vm.ContinueToCSharpForMarkupCommand))
              .Bind (nameof(vm.Title))
)
```

### <a name="gesture-recognizers"></a>Modules de reconnaissance des mouvements

`Command``CommandParameter`les propriétés et peuvent être liées aux données `GestureElement` et aux `View` types à l’aide des `BindClickGesture` méthodes d’extension, `BindSwipeGesture` et `BindTapGesture` :

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .BindTapGesture (nameof(vm.TapCommand))
```

Cet exemple crée un module de reconnaissance de mouvement du type spécifié et l’ajoute à [`Label`](xref:Xamarin.Forms.Label) . Les `Bind*Gesture` méthodes d’extension offrent les mêmes paramètres que les `BindCommand` méthodes d’extension. Toutefois, par défaut, `Bind*Gesture` n’effectue pas de liaison `CommandParameter` , contrairement à `BindCommand` .

Pour initialiser un module de reconnaissance de mouvement avec des paramètres, utilisez les `ClickGesture` méthodes d’extension,, `PanGesture` `PinchGesture` , `SwipeGesture` et `TapGesture` :

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .TapGesture (g => g.Bind(nameof(vm.DoubleTapCommand)).NumberOfTapsRequired = 2)
```

Étant donné qu’un module de reconnaissance de mouvement est un `BindableObject` , vous pouvez utiliser les `Bind` `BindCommand` méthodes d’extension et lorsque vous l’initialisez. Vous pouvez également initialiser des types de module de reconnaissance de mouvement personnalisés avec la `Gesture<TGestureElement, TGestureRecognizer>` méthode d’extension.

## <a name="layout"></a>Layout

Le balisage C# comprend une série de méthodes d’extension de disposition qui prennent en charge le positionnement des vues dans les dispositions et le contenu dans les vues :

| Type | Méthodes d’extension |
|---|---|
| `FlexLayout` | `AlignSelf`, `Basis`, `Grow`, `Menu`, `Order`, `Shrink` |
| `Grid` | `Row`, `Column`, `RowSpan`, `ColumnSpan` |
| `Label` | `TextLeft`, `TextCenterHorizontal`, `TextRight` <br/> `TextTop`, `TextCenterVertical`, `TextBottom` <br/> `TextCenter` |
| `Layout` | `Padding`, `Paddings` |
| `LayoutOptions` | `Left`, `CenterHorizontal`, `FillHorizontal`, `Right` <br/> `LeftExpand`, `CenterExpandHorizontal`, `FillExpandHorizontal`, `RightExpand` <br /> `Top`, `Bottom`, `CenterVertical`, `FillVertical` <br /> `TopExpand`, `BottomExpand`, `CenterExpandVertical`, `FillExpandVertical` <br /> `Center`, `Fill`, `CenterExpand`, `FillExpand` |
| `View` | `Margin`, `Margins` |
| `VisualElement` | `Height`, `Width`, `MinHeight`, `MinWidth`, `Size`, `MinSize` |

### <a name="left-to-right-and-right-to-left-support"></a>Prise en charge de gauche à droite et de droite à gauche

Pour le balisage C# conçu pour prendre en charge le sens de déroulement de gauche à droite ou de droite à gauche (RTL), les méthodes d’extension listées ci-dessus offrent l’ensemble de noms le plus intuitif : `Left` , `Right` `Top` et `Bottom` .

Pour rendre disponible l’ensemble correct des méthodes d’extension de gauche et de droite, et dans le processus rendre explicite le sens du déroulement pour lequel le balisage est conçu, incluez l’une des deux `using` directives suivantes : `using Xamarin.Forms.Markup.LeftToRight;` , ou `using Xamarin.Forms.Markup.RightToLeft;` .

Pour le balisage C# conçu pour prendre en charge le sens du déroulement de gauche à droite et de droite à gauche, il est recommandé d’utiliser les méthodes d’extension dans le tableau suivant au lieu de l’un des espaces de noms ci-dessus :

| Type | Méthodes d’extension |
|---|---|
| `Label` | `TextStart`, `TextEnd` |
| `LayoutOptions` | `Start`, `End` <br/> `StartExpand`, `EndExpand` |

### <a name="layout-line-convention"></a>Convention de ligne de disposition

La Convention recommandée consiste à placer toutes les méthodes d’extension de disposition pour une vue sur une seule ligne dans l’ordre suivant :

1. Ligne et colonne contenant la vue.
1. Alignement dans la ligne et la colonne.
1. Marges autour de la vue.
1. Taille de l’affichage.
1. Remplissage dans la vue.
1. Alignement du contenu dans le remplissage.

Le code suivant illustre un exemple de cette Convention :

```csharp
new Label { }
           .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal () // Layout line
```

À la suite de cette Convention, vous permet de lire rapidement le balisage C# et de créer une carte mentale de l’emplacement du contenu de la vue dans l’interface utilisateur.

## <a name="grid-rows-and-columns"></a>Lignes et colonnes d’une Grid

Les énumérations peuvent être utilisées pour définir [`Grid`](xref:Xamarin.Forms.Grid) des lignes et des colonnes, au lieu d’utiliser des nombres. Cela offre l’avantage que la renumérotation n’est pas nécessaire lors de l’ajout ou de la suppression de lignes ou de colonnes.

> [!IMPORTANT]
> La définition de [`Grid`](xref:Xamarin.Forms.Grid) lignes et de colonnes à l’aide d’énumérations requiert la `using` directive suivante :`using static Xamarin.Forms.Markup.GridRowsColumns;`

Le code suivant montre un exemple de définition et de consommation de [`Grid`](xref:Xamarin.Forms.Grid) lignes et de colonnes à l’aide d’énumérations :

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms.Markup.LeftToRight;
using static Xamarin.Forms.Markup.GridRowsColumns;
// ...

enum BodyRow
{
    Prompt,
    CodeHeader,
    CodeEntry,
    Button
}

enum BodyCol
{
    FieldLabel,
    FieldValidation
}

View Build() => new Grid
{
    RowDefinitions = Rows.Define(
        (BodyRow.Prompt    , 170 ),
        (BodyRow.CodeHeader, 75  ),
        (BodyRow.CodeEntry , Auto),
        (BodyRow.Button    , Auto)
    ),

    ColumnDefinitions = Columns.Define(
        (BodyCol.FieldLabel     , 160 ),
        (BodyCol.FieldValidation, Star)
    ),

    Children =
    {
        new Label { LineBreakMode = LineBreakMode.WordWrap } .Font (15) .Bold ()
                   .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal ()
                   .Bind (nameof(vm.RegistrationPrompt)),

        new Label { Text = "Registration code" } .Bold ()
                   .Row (BodyRow.CodeHeader) .Column(BodyCol.FieldLabel) .Bottom () .Margin (fieldNameMargin),

        new Label { } .Italic ()
                   .Row (BodyRow.CodeHeader) .Column (BodyCol.FieldValidation) .Right () .Bottom () .Margin (fieldNameMargin)
                   .Bind (nameof(vm.RegistrationCodeValidationMessage)),

        new Entry { Placeholder = "E.g. 123456", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
                   .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
                   .Bind (nameof(vm.RegistrationCode), BindingMode.TwoWay),

        new Button { Text = "Verify" } .Style (FilledButton)
                    .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (PageMarginSize)
                    .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode))
                    .Bind (nameof(vm.VerifyRegistrationCodeCommand)),
    }
};
```

En outre, vous pouvez définir de façon concise les lignes et les colonnes sans énumérations :

```csharp
new Grid
{
    RowDefinitions = Rows.Define (Auto, Star, 20),
    ColumnDefinitions = Columns.Define (Auto, Star, 20, 40)
    // ...
}
```

## <a name="fonts"></a>Polices

Les contrôles de la liste suivante peuvent appeler les `FontSize` `Bold` méthodes d’extension,, `Italic` et `Font` pour définir l’apparence du texte affiché par le contrôle :

- `Button`
- `DatePicker`
- `Editor`
- `Entry`
- `Label`
- `Picker`
- `SearchBar`
- `Span`
- `TimePicker`

## <a name="effects"></a>Effets

Les effets peuvent être attachés aux contrôles avec la `Effect` méthode d’extension :

```csharp
using Xamarin.Forms.Markup;
// ...

new Button { Text = "Tap Me" }
            .Effects (new ButtonMixedCaps())
```

## <a name="logic-integration"></a>Intégration logique

La `Invoke` méthode d’extension peut être utilisée pour exécuter du code inline dans votre balisage C# :

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Invoke (l => l.ItemTapped += OnListViewItemTapped)
```

En outre, vous pouvez utiliser la `Assign` méthode d’extension pour accéder à un contrôle à partir de l’extérieur du balisage de l’interface utilisateur (dans le fichier de logique d’interface utilisateur) :

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Assign (out MyListView)
```

## <a name="styles"></a>Styles

L’exemple suivant montre comment créer des styles implicites et explicites à l’aide du balisage C# :

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms;

namespace CSharpForMarkupDemos
{
    public static class Styles
    {
        static Style<Button> buttons, filledButton;
        static Style<Label> labels;
        static Style<Span> link;

        #region Implicit styles

        public static ResourceDictionary Implicit => new ResourceDictionary { Buttons, Labels };

        public static Style<Button> Buttons => buttons ?? (buttons = new Style<Button>(
            (Button.HeightRequestProperty, 44),
            (Button.FontSizeProperty, 13),
            (Button.HorizontalOptionsProperty, LayoutOptions.Center),
            (Button.VerticalOptionsProperty, LayoutOptions.Center)
        ));

        public static Style<Label> Labels => labels ?? (labels = new Style<Label>(
            (Label.FontSizeProperty, 13),
            (Label.TextColorProperty, Color.Black)
        ));

        #endregion Implicit styles

        #region Explicit styles

        public static Style<Button> FilledButton => filledButton ?? (filledButton = new Style<Button>(
            (Button.TextColorProperty, Color.White),
            (Button.BackgroundColorProperty, Color.FromHex("#1976D2")),
            (Button.CornerRadiusProperty, 5)
        )).BasedOn(Buttons);

        public static Style<Span> Link => link ?? (link = new Style<Span>(
            (Span.TextColorProperty, Color.Blue),
            (Span.TextDecorationsProperty, TextDecorations.Underline)
        ));

        #endregion Explicit styles
    }
}
```

Les styles implicites peuvent être consommés en les chargeant dans le dictionnaire de ressources de l’application :

```csharp
public App()
{
    Resources = Styles.Implicit;
    // ...
}
```

Les styles explicites peuvent être consommés avec la `Style` méthode d’extension.

```csharp
using static CSharpForMarkupExample.Styles;
// ...

new Button { Text = "Tap Me" } .Style (FilledButton),
```

> [!NOTE]
> En plus de la `Style` méthode d’extension, il existe également des méthodes d’extension,, `ApplyToDerivedTypes` `BasedOn` `Add` et `CanCascade` .

Vous pouvez également créer vos propres méthodes d’extension de style :

```csharp
public static TButton Filled<TButton>(this TButton button) where TButton : Button
{
    button.Buttons(); // Equivalent to Style .BasedOn (Buttons)
    button.TextColor = Color.White;
    button.BackgroundColor = Color.Red;
    return button;
}
```

La `Filled` méthode d’extension peut ensuite être consommée comme suit :

```csharp
new Button { Text = "Tap Me" } .Filled ()
```

## <a name="platform-specifics"></a>Spécificités des plateformes

La `Invoke` méthode d’extension peut être utilisée pour appliquer des caractéristiques spécifiques à la plateforme. Toutefois, pour éviter les erreurs d’ambiguïté, n’incluez pas `using` directement de directives pour les `Xamarin.Forms.PlatformConfiguration.*Specific` espaces de noms. Au lieu de cela, créez un alias d’espace de noms et consommez le propre à la plateforme via l’alias :

```csharp
using Xamarin.Forms.Markup;
using PciOS = Xamarin.Forms.PlatformConfiguration.iOSSpecific;
// ...

new ListView { } .Invoke (l => PciOS.ListView.SetGroupHeaderStyle(l, PciOS.GroupHeaderStyle.Grouped))
```

En outre, si vous utilisez fréquemment certaines caractéristiques spécifiques à la plateforme, vous pouvez créer des méthodes d’extension Fluent pour celles-ci dans votre propre classe d’extensions :

```csharp
public static T iOSGroupHeaderStyle<T>(this T listView, PciOS.GroupHeaderStyle style) where T : Forms.ListView
{
  PciOS.ListView.SetGroupHeaderStyle(listView, style);
  return listView;
}
```

La méthode d’extension peut ensuite être consommée comme suit :

```csharp
new ListView { } .iOSGroupHeaderStyle(PciOS.GroupHeaderStyle.Grouped)
```

Pour plus d’informations sur les spécificités des plateformes, consultez fonctionnalités de la plateforme [Android](~/xamarin-forms/platform/android/index.md), fonctionnalités de la plateforme [iOS](~/xamarin-forms/platform/ios/index.md)et fonctionnalités de la [plateforme Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="recommended-convention"></a>Convention recommandée

L’ordre et le regroupement recommandés des propriétés et des méthodes d’assistance sont les suivants :

- **Objectif**: toute propriété ou méthode d’assistance dont la valeur identifie l’objectif du contrôle (par exemple `Text` ,, `Placeholder` . `Assign` ).
- **Autre**: toutes les propriétés ou méthodes d’assistance qui ne sont pas de disposition ou de liaison, sur la même ligne ou sur plusieurs lignes.
- **Disposition**: la disposition est ordonnée à l’intérieur : lignes et colonnes, options de disposition, marge, taille, marge intérieure et alignement du contenu.
- **Liaison**: la liaison de données est effectuée à la fin de la chaîne de méthode, avec une propriété liée par ligne. Si la propriété pouvant être liée *par défaut* est liée, elle doit être à la fin de la chaîne de méthode.

Le code suivant montre un exemple de la Convention suivante :

```csharp
new Button { Text = "Verify" /* purpose */ } .Style (FilledButton) // other
            .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (10) // layout
            .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode)) // bind
            .Bind (nameof(vm.VerifyRegistrationCodeCommand)), // bind default

new Label { }
           .Assign (out animatedMessageLabel) // purpose
           .Invoke (label => label.SizeChanged += MessageLabel_SizeChanged) // other
           .Row (BodyRow.Message) .ColumnSpan (All<BodyCol>()) // layout
           .Bind (nameof(vm.Message)), // bind default
```

L’application cohérente de cette Convention vous permet d’analyser rapidement votre balisage C# et de générer une image mentale de la disposition de l’interface utilisateur.

## <a name="related-links"></a>Liens connexes

- [CSharpForMarkupDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)
- [Fonctionnalités de la plateforme Android](~/xamarin-forms/platform/android/index.md)
- [fonctionnalités de la plateforme iOS](~/xamarin-forms/platform/ios/index.md)
- [Fonctionnalités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md)
