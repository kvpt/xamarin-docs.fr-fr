---
title: Xamarin.Forms BoxView
description: Cet article explique comment utiliser un rectangle de couleur pour la décoration, les graphiques et l’interaction dans une Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 32571724c945c4bbd845943ac72fc959c42d584a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374353"
---
# <a name="no-locxamarinforms-boxview"></a>Xamarin.Forms BoxView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)

[`BoxView`](xref:Xamarin.Forms.BoxView) génère le rendu d’un rectangle simple d’une largeur, d’une hauteur et d’une couleur spécifiées. Vous pouvez utiliser `BoxView` pour la décoration, les graphiques rudimentaires et pour l’interaction avec l’utilisateur via Touch.

Étant donné que Xamarin.Forms n’a pas de système graphique vectoriel intégré, le `BoxView` permet de compenser. Certains des exemples de programmes décrits dans cet article utilisent `BoxView` pour le rendu des graphiques. `BoxView`Peut être dimensionné pour ressembler à une ligne d’une largeur et d’une épaisseur spécifiques, puis pivoter selon n’importe quel angle à l’aide de la `Rotation` propriété.

Bien que `BoxView` puisse imiter des graphiques simples, vous souhaiterez peut-être examiner [l’utilisation de SkiaSharp dans Xamarin.Forms ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) pour obtenir des spécifications graphiques plus sophistiquées.

## <a name="setting-boxview-color-and-size"></a>Définition de la couleur et de la taille BoxView

En général, vous définissez les propriétés suivantes de `BoxView` :

- [`Color`](xref:Xamarin.Forms.BoxView.Color) pour définir sa couleur.
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius) pour définir le rayon de l’angle.
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) pour définir la largeur du `BoxView` en unités indépendantes du périphérique.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) pour définir la hauteur du `BoxView` .

La `Color` propriété est de type `Color` ; la propriété peut être définie sur n’importe quelle `Color` valeur, y compris les champs statiques en lecture seule 141 des couleurs nommées, par ordre alphabétique de `AliceBlue` à `YellowGreen` .

La `CornerRadius` propriété est de type [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) ; la propriété peut être définie sur une seule `double` valeur de rayon d’angle uniforme, ou sur une `CornerRadius` structure définie par quatre `double` valeurs qui sont appliquées aux sommets en haut à gauche, en haut à droite, en bas à gauche et en bas à droite de `BoxView` .

Les `WidthRequest` `HeightRequest` Propriétés et jouent un rôle uniquement si le `BoxView` n’est pas *contraint* dans la disposition. C’est le cas lorsque le conteneur de disposition doit connaître la taille de l’enfant, par exemple lorsque `BoxView` est un enfant d’une cellule de taille automatique dans la `Grid` disposition. Un `BoxView` est également sans contrainte lorsque ses `HorizontalOptions` Propriétés et `VerticalOptions` ont des valeurs autres que `LayoutOptions.Fill` . Si le `BoxView` est sans contrainte, mais que les `WidthRequest` Propriétés et ne `HeightRequest` sont pas définies, la largeur ou la hauteur est définie sur les valeurs par défaut de 40 unités, ou environ 1/4 pouces sur les périphériques mobiles.

Les `WidthRequest` `HeightRequest` Propriétés et sont ignorées si le `BoxView` est *soumis* à une mise en page, auquel cas le conteneur de disposition impose sa propre taille sur le `BoxView` .

Un `BoxView` peut être contraint dans une dimension et sans contrainte dans l’autre. Par exemple, si `BoxView` est un enfant d’un vertical `StackLayout` , la dimension verticale du est sans `BoxView` contrainte et sa dimension horizontale est généralement contrainte. Toutefois, il existe des exceptions pour cette dimension horizontale : si la `BoxView` propriété de a `HorizontalOptions` une valeur autre que `LayoutOptions.Fill` , la dimension horizontale n’est pas non plus contrainte. Il est également possible que le `StackLayout` lui-même ait une dimension horizontale sans contrainte, auquel cas le `BoxView` sera également horizontalement sans contrainte.

L’exemple [**BasicBoxView**](/samples/xamarin/xamarin-forms-samples/boxview-basicboxview) affiche un carré d’un pouce sans contrainte `BoxView` au centre de sa page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

Voici le résultat :

[![BoxView de base](boxview-images/basicboxview-small.png "BoxView de base")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Si les `VerticalOptions` `HorizontalOptions` Propriétés et sont supprimées de la `BoxView` balise ou ont la valeur `Fill` , le `BoxView` devient alors restreint par la taille de la page et se développe pour remplir la page.

Un `BoxView` peut également être un enfant d’un `AbsoluteLayout` . Dans ce cas, l’emplacement et la taille du `BoxView` sont définis à l’aide de la `LayoutBounds` propriété pouvant être liée. `AbsoluteLayout`Est abordé dans l’article [**AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolutelayout.md).

Vous verrez des exemples de ces cas dans les exemples de programmes qui suivent.

## <a name="rendering-text-decorations"></a>Rendu des décorations de texte

Vous pouvez utiliser le `BoxView` pour ajouter des décorations simples sur vos pages sous forme de lignes horizontales et verticales. L’exemple [**TextDecoration**](/samples/xamarin/xamarin-forms-samples/boxview-textdecoration) illustre cela. Tous les éléments visuels du programme sont définis dans le fichier **MainPage. Xaml** , qui contient plusieurs `Label` éléments et `BoxView` dans le `StackLayout` illustré ici :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

Tout le balisage suivant est un enfant de `StackLayout` . Ce balisage est constitué de plusieurs types d’éléments décoratifs `BoxView` utilisés avec l' `Label` élément :

[![Décoration de texte](boxview-images/textdecoration-small.png "Décoration de texte")](boxview-images/textdecoration-large.png#lightbox "Décoration de texte")

L’en-tête élégant en haut de la page est obtenu avec un `AbsoluteLayout` dont les enfants sont quatre `BoxView` éléments et un `Label` , qui sont tous affectés à des emplacements et des tailles spécifiques :

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

Dans le fichier XAML, `AbsoluteLayout` est suivi d’un `Label` avec du texte mis en forme qui décrit le `AbsoluteLayout` .

Vous pouvez souligner une chaîne de texte en mettant à la fois `Label` et `BoxView` dans un `StackLayout` dont la `HorizontalOptions` valeur est différente de `Fill` . La largeur du `StackLayout` est ensuite régie par la largeur du `Label` , qui impose ensuite cette largeur sur le `BoxView` . La `BoxView` n’est assignée qu’à une hauteur explicite :

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

Vous ne pouvez pas utiliser cette technique pour souligner des mots individuels dans des chaînes de texte plus longues ou un paragraphe.

Il est également possible d’utiliser un `BoxView` pour ressembler à un `hr` élément HTML (règle horizontale). Laissez simplement la largeur du à `BoxView` être déterminée par son conteneur parent, qui est dans ce cas `StackLayout` :

```xaml
<BoxView HeightRequest="3" />
```

Enfin, vous pouvez dessiner une ligne verticale d’un côté d’un paragraphe de texte en plaçant à la fois le `BoxView` et le `Label` dans un horizontal `StackLayout` . Dans ce cas, la hauteur du `BoxView` est identique à la hauteur de `StackLayout` , qui est régie par la hauteur de `Label` :

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```

## <a name="listing-colors-with-boxview"></a>Affichage des couleurs avec BoxView

`BoxView`Est pratique pour afficher les couleurs. Ce programme utilise un `ListView` pour répertorier tous les champs statiques publics en lecture seule de la Xamarin.Forms `Color` structure :

[![Couleurs ListView](boxview-images/listviewcolors-small.png "Couleurs ListView")](boxview-images/listviewcolors-large.png#lightbox "Couleurs ListView")

Le programme [**ListViewColors**](/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors) comprend une classe nommée `NamedColor` . Le constructeur statique utilise la réflexion pour accéder à tous les champs de la `Color` structure et créer un `NamedColor` objet pour chacun d’eux. Celles-ci sont stockées dans la `All` propriété statique :

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

Les éléments visuels du programme sont décrits dans le fichier XAML. La `ItemsSource` propriété de `ListView` a la valeur de la propriété statique, ce qui `NamedColor.All` signifie que `ListView` affiche tous les `NamedColor` objets individuels :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Les `NamedColor` objets sont mis en forme par l' `ViewCell` objet défini en tant que modèle de données de `ListView` . Ce modèle comprend un `BoxView` dont `Color` la propriété est liée à la `Color` propriété de l' `NamedColor` objet.

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Jouer le jeu de la vie en sous-classant BoxView

Le jeu de vie est un Automaton cellulaire inventé par Mathematician John Conway et populaire dans les pages de l' *American scientifique* dans les années 1970. Une bonne présentation est fournie par le [jeu de vie de](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)l’article de Wikipédia Conway.

Le Xamarin.Forms programme [**GameOfLife**](/samples/xamarin/xamarin-forms-samples/boxview-gameoflife) définit une classe nommée `LifeCell` qui dérive de `BoxView` . Cette classe encapsule la logique d’une cellule individuelle dans le jeu de vie :

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell` ajoute trois propriétés supplémentaires à `BoxView` : les `Col` `Row` Propriétés et stockent la position de la cellule dans la grille, et la `IsAlive` propriété indique son état. La `IsAlive` propriété affecte également `Color` à la propriété de la `BoxView` valeur Black si la cellule est active, et blanc si la cellule n’est pas active.

`LifeCell` installe également un `TapGestureRecognizer` pour permettre à l’utilisateur de basculer l’état des cellules en appuyant dessus. La classe traduit l’événement du module `Tapped` de reconnaissance de mouvement dans son propre `Tapped` événement.

Le programme **GameOfLife** comprend également une `LifeGrid` classe qui encapsule une grande partie de la logique du jeu et une `MainPage` classe qui gère les éléments visuels du programme. Celles-ci incluent une superposition qui décrit les règles du jeu. Voici le programme en action qui présente quelques centaines `LifeCell` d’objets sur la page :

[![Jeu de vie](boxview-images/gameoflife-small.png "Jeu de vie")](boxview-images/gameoflife-large.png#lightbox "Jeu de vie")

## <a name="creating-a-digital-clock"></a>Création d’une horloge numérique

Le programme [**DotMatrixClock**](/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock) crée 210 `BoxView` éléments pour simuler les points d’un vieux affichage de matrice à 5 par 7. Vous pouvez lire l’heure en mode portrait ou paysage, mais elle est plus grande en mode paysage :

[![Horloge matricielle](boxview-images/dotmatrixclock-small.png "Horloge Dot-Matrix")](boxview-images/dotmatrixclock-large.png#lightbox "Horloge Dot-Matrix")

Le fichier XAML ne fait que instancier le `AbsoluteLayout` utilisé pour l’horloge :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

Tout le reste se produit dans le fichier code-behind. La logique d’affichage de la matrice matricielle est considérablement simplifiée par la définition de plusieurs tableaux qui décrivent les points correspondant à chacun des 10 chiffres et un signe deux-points :

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

Ces champs se terminent par un tableau à trois dimensions d' `BoxView` éléments pour le stockage des séquences de points pour les six chiffres.

Le constructeur crée tous les `BoxView` éléments pour les chiffres et les deux-points, et initialise également la `Color` propriété des `BoxView` éléments pour les deux-points :

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

Ce programme utilise la fonctionnalité de positionnement et de dimensionnement relative de `AbsoluteLayout` . La largeur et la hauteur de chaque `BoxView` sont définies sur des valeurs fractionnaires, en particulier 85% de 1 divisé par le nombre de points horizontaux et verticaux. Les positions sont également définies sur des valeurs fractionnaires.

Étant donné que toutes les positions et toutes les tailles sont relatives à la taille totale de `AbsoluteLayout` , le `SizeChanged` Gestionnaire de la page doit uniquement définir un `HeightRequest` des `AbsoluteLayout` éléments suivants :

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

La largeur du `AbsoluteLayout` est définie automatiquement, car elle s’étend à la largeur complète de la page.

Le code final de la `MainPage` classe traite le rappel de la minuterie et colore les points de chaque chiffre. La définition des tableaux multidimensionnels au début du fichier code-behind permet de rendre cette logique la partie la plus simple du programme :

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```

## <a name="creating-an-analog-clock"></a>Création d’une horloge analogique

Une horloge matricielle peut sembler être une application évidente de `BoxView` , mais `BoxView` les éléments peuvent également réaliser une horloge analogique :

[![Horloge BoxView](boxview-images/boxviewclock-small.png "Horloge BoxView")](boxview-images/boxviewclock-large.png#lightbox "Horloge BoxView")

Tous les éléments visuels dans le programme [**BoxViewClock**](/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) sont des enfants d’un `AbsoluteLayout` . Ces éléments sont dimensionnés à l’aide de la `LayoutBounds` propriété jointe et pivotés à l’aide de la `Rotation` propriété.

Les trois `BoxView` éléments des mains de l’horloge sont instanciés dans le fichier XAML, mais ils ne sont pas positionnés ou dimensionnés :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

Le constructeur du fichier code-behind instancie les éléments 60 `BoxView` pour les graduations autour de la circonférence de l’horloge :

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

Le dimensionnement et le positionnement de tous les `BoxView` éléments se produisent dans le `SizeChanged` Gestionnaire de `AbsoluteLayout` . Une petite structure interne à la classe appelée `HandParams` décrit la taille de chacun des trois mains par rapport à la taille totale de l’horloge :

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

Le `SizeChanged` gestionnaire détermine le centre et le rayon du `AbsoluteLayout` , puis dimensionne et positionne les éléments 60 `BoxView` utilisés comme graduations. La `for` boucle se termine par la définition `Rotation` de la propriété de chacun de ces `BoxView` éléments. À la fin du `SizeChanged` Gestionnaire, la `LayoutHand` méthode est appelée pour dimensionner et positionner les trois aiguilles de l’horloge :

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

La `LayoutHand` méthode dimensionne et place chaque main pour pointer vers la position 12:00. À la fin de la méthode, la `AnchorY` propriété a la valeur d’une position correspondant au centre de l’horloge. Cela indique le centre de rotation.

Les mains sont pivotées dans la fonction de rappel de la minuterie :

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

La deuxième main est traitée un peu différemment : une fonction d’accélération d’animation est appliquée pour que le mouvement semble mécanique plutôt que lisse. À chaque battement, la deuxième main récupère un peu, puis déjoue sa destination. Ce petit morceau de code ajoute beaucoup au réalisme du mouvement.

## <a name="related-links"></a>Liens associés

- [BoxView de base (exemple)](/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)
- [Décoration de texte (exemple)](/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)
- [Couleurs ListView (exemple)](/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)
- [Jeu de vie (exemple)](/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)
- [Horloge matricielle (exemple)](/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)
- [BoxView Clock (exemple)](/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)
- [BoxView](xref:Xamarin.Forms.BoxView)