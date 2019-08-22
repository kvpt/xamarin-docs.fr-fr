---
title: Xamarin.Forms BoxView
description: Cet article explique comment utiliser un rectangle de couleur de décoration, des graphiques et des interactions dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
ms.openlocfilehash: 06b4764570205a39734d38e3e118b7dc1fa35d18
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888732"
---
# <a name="xamarinforms-boxview"></a>Xamarin.Forms BoxView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)

[`BoxView`](xref:Xamarin.Forms.BoxView) restitue un rectangle simple d’une largeur spécifiée, la hauteur et la couleur. Vous pouvez utiliser `BoxView` décoratif, rudimentaire graphiques et pour l’interaction avec l’utilisateur via le tactile.

Étant donné que Xamarin.Forms ne dispose pas d’un système de graphiques vectoriels intégrés, le `BoxView` permet de compenser. Certains des exemples de programmes décrits dans cet article utilisent `BoxView` pour le rendu de graphiques. Le `BoxView` peuvent être dimensionnés pour ressembler à une ligne d’une largeur spécifique et l’épaisseur et ensuite pivoter de n’importe quel angle à l’aide de la `Rotation` propriété.

Bien que `BoxView` peut simuler les graphiques simples, vous pouvez également examiner [à l’aide de SkiaSharp dans Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) pour les exigences de graphiques plus sophistiquées.

Cet article aborde les rubriques suivantes :

- **[Définition BoxView couleur et taille](#colorandsize)**  &ndash; définir le `BoxView` propriétés.
- **[Ornements de texte rendu](#textdecorations)**  &ndash; utiliser un `BoxView` pour les lignes de rendu.
- **[Liste des couleurs avec BoxView](#listingcolors)**  &ndash; afficher toutes les couleurs du système dans un `ListView`.
- **[Lecture de la partie de la vie en sous-classant BoxView](#subclassing)**  &ndash; implémenter un célèbre automate cellulaire.
- **[Création d’une horloge digitale](#digitalclock)**  &ndash; simuler un affichage matricielles.
- **[Création d’une horloge analogique](#analogclock)**  &ndash; transformer et animer `BoxView` éléments.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Paramètre BoxView couleur et taille

En général, vous devez définir les propriétés suivantes de `BoxView`:

- [`Color`](xref:Xamarin.Forms.BoxView.Color) Pour définir sa couleur.
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius) Pour définir son rayon d’angle.
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Pour définir la largeur de la `BoxView` en unités indépendantes du périphérique.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Pour définir la hauteur de la `BoxView`.

Le `Color` propriété est de type `Color`; la propriété peut être définie à tout `Color` valeur, y compris les champs statiques 141 en lecture seule de nommé couleurs allant par ordre alphabétique `AliceBlue` à `YellowGreen`.

Le `CornerRadius` propriété est de type [ `CornerRadius` ](xref:Xamarin.Forms.CornerRadius); la propriété peut être définie à un seul `double` glyphes de largeurs uniformes valeur de rayon d’angle, ou un `CornerRadius` structure définie par quatre `double` valeurs qui sont appliqués à la zone supérieure gauche, en haut à droite, en bas à gauche et bas à droite de la `BoxView`.

Le `WidthRequest` et `HeightRequest` propriétés uniquement jouent un rôle que si le `BoxView` est *sans contrainte* dans mise en page. C’est le cas lorsque le conteneur de disposition doit connaître l’enfant de dimensions, par exemple, lorsque le `BoxView` est un enfant d’une cellule dimensionnée automatiquement dans le `Grid` mise en page. Un `BoxView` est sans contrainte également lorsque son `HorizontalOptions` et `VerticalOptions` propriétés sont définies sur les valeurs autres que `LayoutOptions.Fill`. Si le `BoxView` n’est pas limitée, mais le `WidthRequest` et `HeightRequest` propriétés ne sont pas définies, puis la largeur ou hauteur sont définies sur les valeurs par défaut de 40 unités, ou environ 1/4 pouces sur les appareils mobiles.

Le `WidthRequest` et `HeightRequest` propriétés sont ignorées si le `BoxView` est *contraint* dans la mise en page, dans lequel cas le conteneur de disposition impose son propre taille sur le `BoxView`.

Un `BoxView` peut être limité dans une dimension et sans contraintes dans l’autre. Par exemple, si le `BoxView` est un enfant d’une verticale `StackLayout`, la dimension verticale de la `BoxView` est sans contrainte et sa dimension horizontale est généralement limitée. Toutefois, il existe des exceptions pour cette dimension horizontale: Si la `BoxView` `HorizontalOptions` propriété de a une valeur autre que `LayoutOptions.Fill`, la dimension horizontale est également sans contrainte. Il est également possible pour le `StackLayout` lui-même pour avoir une dimension horizontale sans contrainte, auquel cas la `BoxView` sera également horizontalement sans contrainte.

Le [ **BasicBoxView** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview) exemple affiche un carré de pouce de celui sans contrainte `BoxView` dans le centre de sa page :

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

[![Base BoxView](boxview-images/basicboxview-small.png "BoxView base")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Si le `VerticalOptions` et `HorizontalOptions` propriétés sont supprimées de la `BoxView` balise ou utilisent `Fill`, puis le `BoxView` devient limité par la taille de la page et se développe pour remplir la page.

Un `BoxView` peut également être un enfant d’un `AbsoluteLayout`. Dans ce cas, l’emplacement et la taille de la `BoxView` sont définies à l’aide de la `LayoutBounds` attaché de propriété pouvant être liée. Le `AbsoluteLayout` est décrite dans l’article [ **DispositionAbsolue**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Vous verrez des exemples de tous ces cas dans les exemples de programmes qui suivent.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Rendu des ornements de texte

Vous pouvez utiliser le `BoxView` pour ajouter des ornements simples sur vos pages sous la forme de lignes horizontales et verticales. Le [ **TextDecoration** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration) illustre cela. Tous les éléments visuels du programme sont définis dans le **MainPage.xaml** fichier qui contient plusieurs `Label` et `BoxView` éléments dans le `StackLayout` illustré ici :

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

Le balisage qui suit les enfants de le `StackLayout`. Ce balisage se compose de plusieurs types de décoratif `BoxView` éléments utilisés avec la `Label` élément :

[![Décoration de texte](boxview-images/textdecoration-small.png "décoration de texte")](boxview-images/textdecoration-large.png#lightbox "décoration de texte")

L’en-tête élégant en haut de la page est obtenue avec un `AbsoluteLayout` dont les enfants sont les quatre `BoxView` éléments et un `Label`, tous les des qui sont affectés à des emplacements spécifiques et des tailles :

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

Dans le fichier XAML, le `AbsoluteLayout` est suivie d’un `Label` avec mise en forme de texte qui décrit le `AbsoluteLayout`.

Vous pouvez souligner une chaîne de texte en mettant à la fois le `Label` et `BoxView` dans un `StackLayout` qui a son `HorizontalOptions` valeur définie sur une valeur autre que `Fill`. La largeur de la `StackLayout` est alors régi par la largeur de la `Label`, ce qui impose cette largeur sur le `BoxView`. Le `BoxView` est affectée uniquement une hauteur explicite :

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

Vous ne pouvez pas utiliser cette technique pour souligner les mots individuels dans les chaînes de texte plus longues ou un paragraphe.

Il est également possible d’utiliser un `BoxView` pour ressembler à un élément HTML `hr` élément (horizontal rule). Permettent simplement de la largeur de la `BoxView` être déterminée par son conteneur parent, qui est dans ce cas le `StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

Enfin, vous pouvez dessiner une ligne verticale sur le côté « un » d’un paragraphe de texte en mettant à la fois le `BoxView` et `Label` un horizontal `StackLayout`. Dans ce cas, la hauteur de la `BoxView` est identique à la hauteur de `StackLayout`, qui est régi par la hauteur de la `Label`:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```

<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>Liste des couleurs avec BoxView

Le `BoxView` est pratique pour afficher les couleurs. Ce programme utilise une `ListView` pour répertorier tous les champs statiques publics en lecture seule de la Xamarin.Forms `Color` structure :

[![Couleurs de ListView](boxview-images/listviewcolors-small.png "ListView couleurs")](boxview-images/listviewcolors-large.png#lightbox "couleurs de ListView")

Le [ **ListViewColors** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors) programme inclut une classe nommée `NamedColor`. Le constructeur statique utilise la réflexion pour accéder à tous les champs de la `Color` structurer et créer un `NamedColor` objet pour chacun d'entre eux. Ceux-ci sont stockés dans la méthode statique `All` propriété :

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

Les visuels de programme sont décrits dans le fichier XAML. Le `ItemsSource` propriété de la `ListView` est défini sur la méthode statique `NamedColor.All` propriété, ce qui signifie que le `ListView` affiche tous les `NamedColor` objets :

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

Le `NamedColor` objets sont mis en forme par le `ViewCell` objet qui est défini comme le modèle de données de la `ListView`. Ce modèle inclut un `BoxView` dont `Color` propriété est liée à la `Color` propriété de la `NamedColor` objet.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Jouez au jeu de la vie en sous-classant BoxView

La partie de la vie est un automate cellulaire inventé par mathématicien John Conway et populaire dans les pages de *scientifique American* dans les années 1970. Une bonne introduction est fournie par l’article de Wikipedia [jeu de la vie de Conway développé](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

Xamarin.Forms [ **GameOfLife** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife) programme définit une classe nommée `LifeCell` qui dérive de `BoxView`. Cette classe encapsule la logique d’une cellule individuelle dans la partie de la vie :

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

`LifeCell` Ajoute les trois propriétés supplémentaires à `BoxView`: le `Col` et `Row` propriétés stockent la position de la cellule dans la grille et le `IsAlive` propriété indique son état. Le `IsAlive` propriété définit également la `Color` propriété de la `BoxView` noire si la cellule est actif et blanc si la cellule n’est pas active.

`LifeCell` installe également un `TapGestureRecognizer` pour autoriser l’utilisateur à activer/désactiver l’état de cellules en appuyant sur eux. La classe traduit la `Tapped` événement à partir de la reconnaissance de mouvement dans son propre `Tapped` événement.

Le **GameOfLife** programme inclut également un `LifeGrid` classe qui encapsule une bonne partie de la logique du jeu, et un `MainPage` classe qui gère les éléments visuels du programme. Ceux-ci incluent un segment de recouvrement qui décrit les règles du jeu. Voici le programme en action montrant quelques centaines `LifeCell` objets sur la page :

[![Le Game of Life](boxview-images/gameoflife-small.png "le Game of Life")](boxview-images/gameoflife-large.png#lightbox "le Game of Life")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Création d’une horloge digitale

Le [ **DotMatrixClock** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock) programme crée 210 `BoxView` éléments pour simuler les points d’un affichage de 5 à 7 matricielle traditionnelle. Vous pouvez lire l’heure en mode portrait ou paysage, mais elle est supérieure en mode paysage :

[![Matricielle horloge](boxview-images/dotmatrixclock-small.png "matricielle horloge")](boxview-images/dotmatrixclock-large.png#lightbox "matricielle horloge")

Le fichier XAML instancie rien de plus que le `AbsoluteLayout` utilisé pour l’horloge :

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

Tout le reste se produit dans le fichier code-behind. La logique d’affichage matricielle est considérablement simplifiée par la définition de plusieurs tableaux qui décrivent les points correspondant à chaque les 10 chiffres et un signe deux-points :

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

Ces champs se terminent par un tableau tridimensionnel de `BoxView` éléments pour stocker les modèles de point pour les six chiffres.

Le constructeur crée tous les le `BoxView` éléments de chiffres et signe deux-points et également initialise le `Color` propriété de la `BoxView` éléments pour le signe deux-points :

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

Ce programme utilise le positionnement relatif et la fonctionnalité de dimensionnement de `AbsoluteLayout`. La largeur et la hauteur de chaque `BoxView` sont définies sur les valeurs fractionnaires, spécifiquement 85 % 1 divisée par le nombre de points horizontales et verticales. Les positions sont également définies pour les valeurs fractionnaires.

Étant donné que les positions et les tailles sont par rapport à la taille totale de la `AbsoluteLayout`, le `SizeChanged` Gestionnaire de la page suffit de définir un `HeightRequest` de la `AbsoluteLayout`:

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

La largeur de la `AbsoluteLayout` est définie automatiquement, car il s’étend sur toute la largeur de la page.

Le code final dans le `MainPage` classe traite le rappel de minuterie et les points de chaque chiffre de couleurs. La définition des tableaux multidimensionnels au début du fichier code-behind permet de rendre cette logique de la partie la plus simple du programme :

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

<a name="analogclock" />

## <a name="creating-an-analog-clock"></a>Création d’une horloge analogique

Une horloge matricielles peut sembler être une application évident de `BoxView`, mais `BoxView` éléments sont également capables de réaliser une horloge analogique :

[![Horloge de BoxView](boxview-images/boxviewclock-small.png "BoxView horloge")](boxview-images/boxviewclock-large.png#lightbox "BoxView horloge")

Tous les éléments visuels dans le [ **BoxViewClock** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) programme sont les enfants d’un `AbsoluteLayout`. Ces éléments sont dimensionnés à l’aide de la `LayoutBounds` propriété jointe et pivoté à l’aide du `Rotation` propriété.

Les trois `BoxView` éléments pour les aiguilles de l’horloge sont instanciés dans le fichier XAML, mais pas positionnés ou dimensionnés :

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

Le constructeur du fichier code-behind instancie le 60 `BoxView` éléments pour les graduations autour de la circonférence de l’horloge :

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

Le dimensionnement et le positionnement de tous les `BoxView` éléments se produit dans le `SizeChanged` gestionnaire pour le `AbsoluteLayout`. Une petite structure interne à la classe appelée `HandParams` décrit la taille de chacun des trois mains par rapport à la taille totale de l’horloge :

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

Le `SizeChanged` gestionnaire détermine le centre et le rayon de le `AbsoluteLayout`et puis dimensionne et positionne le 60 `BoxView` éléments utilisés en tant que marques de graduation. Le `for` boucle se termine en définissant le `Rotation` propriété de chacun de ces `BoxView` éléments. À la fin de la `SizeChanged` gestionnaire, la `LayoutHand` méthode est appelée pour dimensionner et positionner les trois aiguilles de l’horloge :

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

Le `LayoutHand` méthode dimensionne et positionne chaque aiguille pour pointer directement jusqu'à la position de 12:00. À la fin de la méthode, le `AnchorY` propriété est définie sur une position correspondant au centre de l’horloge. Cela indique le centre de rotation.

Entre les mains pivotent dans la fonction de rappel de minuteur :

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

La deuxième main est traitée un peu différemment: Une fonction d’accélération d’animation est appliquée pour que le mouvement semble mécanique plutôt que lisse. À chaque graduation, la seconde aiguille extrait revenez un peu et puis dès sa destination. Ce petit bout de code ajoute beaucoup au réalisme du mouvement.

## <a name="conclusion"></a>Conclusion

Le `BoxView` peut sembler simple au début, mais comme vous l’avez vu, il peut s’agir d’une grande polyvalence et pouvant presque reproduire des éléments visuels qui sont normalement uniquement possibles avec graphismes vectoriels. Pour les graphiques plus sophistiquées, consultez [à l’aide de SkiaSharp dans Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).


## <a name="related-links"></a>Liens associés

- [Base BoxView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)
- [Décoration de texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)
- [Couleurs ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)
- [Game of Life (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)
- [Horloge matricielle (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)
- [Horloge BoxView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)
- [BoxView](xref:Xamarin.Forms.BoxView)
