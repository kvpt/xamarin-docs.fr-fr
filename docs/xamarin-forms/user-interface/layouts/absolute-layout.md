---
titre : « Xamarin.Forms AbsoluteLayout » Description : «cet article explique comment utiliser la Xamarin.Forms classe AbsoluteLayout pour créer des interfaces utilisateur de type pixel-perfectly. Cette classe positionne et dimensionne les éléments enfants proportionnels à sa propre taille et position ou par valeurs absolues.»
ms. Prod : xamarin ms. AssetID : 01A5CCE0-AD45-4806-84FD-72C007005B38 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 11/25/2015 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-absolutelayout"></a>Xamarin.FormsAbsoluteLayout

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)positionne et redimensionne les éléments enfants proportionnels à sa propre taille et position ou par valeurs absolues. Les vues enfants peuvent être positionnées et dimensionnées à l’aide de valeurs proportionnelles ou de valeurs statiques, et les valeurs proportionnelles et statiques peuvent être mélangées.

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

Cet article couvre ce qui suit :

- **[Objet](#purpose)** &ndash; utilisations courantes de `AbsoluteLayout` .
- **[Utilisation](#usage)** &ndash; Comment utiliser `AbsoluteLayout` pour obtenir la conception de votre choix.
  - **[Dispositions](#proportional-layouts)** &ndash; proportionnelles comprendre le fonctionnement des valeurs proportionnelles dans un `AbsoluteLayout` .
  - **[Spécification de valeurs](#specifying-values)** &ndash; comprenez comment les valeurs proportionnelles et absolues sont spécifiées.
  - **[Valeurs](#proportional-values)** &ndash; proportionnelles comprendre le fonctionnement des valeurs proportionnelles.
    - **[Valeurs](#absolute-values)** &ndash; absolues comprendre le fonctionnement des valeurs absolues.

## <a name="purpose"></a>Objectif

En raison du modèle de positionnement de `AbsoluteLayout` , la disposition rend relativement simple la position des éléments afin qu’ils soient vidés avec n’importe quel côté de la disposition, ou centrés. Avec des tailles et des positions proportionnelles, les éléments d’un `AbsoluteLayout` peuvent être mis à l’échelle automatiquement en fonction de la taille de la vue. Pour les éléments où seule la position, mais pas la taille, doit être mise à l’échelle, les valeurs absolues et proportionnelles peuvent être mélangées.

`AbsoluteLayout`peut être utilisé partout où les éléments doivent être positionnés dans une vue et s’avèrent particulièrement utiles pour l’alignement des éléments sur les bords.

## <a name="usage"></a>Usage

### <a name="proportional-layouts"></a>Dispositions proportionnelles

`AbsoluteLayout`a un modèle d’ancrage unique dans lequel l’ancrage de l’élément est positionné par rapport à son élément lorsque l’élément est positionné par rapport à la disposition lorsque le positionnement proportionnel est utilisé. Lorsque le positionnement absolu est utilisé, l’ancre est à (0,0) dans la vue. Cela a deux conséquences importantes :

- Les éléments ne peuvent pas être positionnés hors de l’écran à l’aide de valeurs proportionnelles.
- Les éléments peuvent être positionnés de façon fiable sur n’importe quel côté de la disposition ou au centre, quelle que soit la taille de la disposition ou du périphérique.

`AbsoluteLayout`, comme `RelativeLayout` , peut positionner des éléments afin qu’ils se chevauchent.

Notez dans la capture d’écran suivante, l’ancre de la boîte est un point blanc. Notez la relation entre l’ancre et la zone à mesure qu’elle progresse dans la disposition :

![](absolute-layout-images/anchor-start.png "Anchor at Start")
![](absolute-layout-images/anchor-center.png "Anchor at Center")
![](absolute-layout-images/anchor-end.png "Anchor at End")

### <a name="specifying-values"></a>Spécification de valeurs

Les vues dans un `AbsoluteLayout` sont positionnées à l’aide de quatre valeurs :

- **X** &ndash; la position x (horizontale) de l’ancre de la vue
- **Y** &ndash; Position y (verticale) de l’ancre de la vue
- **Largeur** &ndash; largeur de la vue
- **Hauteur** &ndash; hauteur de la vue

Chacune de ces valeurs peut être définie sous la forme d’une valeur [proportionnelle](#proportional-values) ou d’une valeur [absolue](#absolute-values) .

Les valeurs sont spécifiées sous la forme d’une combinaison de limites et d’un indicateur. `LayoutBounds`est [`Rectangle`](xref:Xamarin.Forms.Rectangle) constitué de quatre valeurs : `x` , `y` , `width` , `height` .

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)spécifie la manière dont les valeurs sont interprétées et présente les options prédéfinies suivantes :

- **Aucun** &ndash; interprète toutes les valeurs comme absolues. Il s’agit de la valeur par défaut si aucun indicateur de disposition n’est spécifié.
- **Tout** &ndash; interprète toutes les valeurs comme étant proportionnelles.
- **WidthProportional** &ndash; interprète la `Width` valeur comme étant proportionnelle et toutes les autres valeurs comme absolues.
- **HeightProportional** &ndash; interprète uniquement la valeur de la hauteur comme proportionnelle avec toutes les autres valeurs absolues.
- **XProportional** &ndash; interprète la `X` valeur comme étant proportionnelle, tout en traitant toutes les autres valeurs comme absolues.
- **YProportional** &ndash; interprète la `Y` valeur comme étant proportionnelle, tout en traitant toutes les autres valeurs comme absolues.
- **PositionProportional** &ndash; interprète les `X` valeurs et `Y` comme étant proportionnelles, tandis que les valeurs de taille sont interprétées comme absolues.
- **SizeProportional** &ndash; interprète les `Width` valeurs et `Height` comme étant proportionnelles, tandis que les valeurs de position sont absolues.

En XAML, les limites et les indicateurs sont définis dans le cadre de la définition des vues dans la disposition, à l’aide de la `AbsoluteLayout.LayoutBounds` propriété. Les limites sont définies sous la forme d’une liste de valeurs séparées par des virgules,,, `X` `Y` `Width` et `Height` , dans cet ordre. Les indicateurs sont également spécifiés dans la déclaration des vues de la disposition à l’aide de la `AbsoluteLayout.LayoutFlags` propriété. Notez que les indicateurs peuvent être combinés en XAML à l’aide d’une liste séparée par des virgules. Prenons l’exemple suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "AbsoluteLayout Examples")

Notez les points suivants :

- L’étiquette au centre est positionnée à l’aide des valeurs de taille et de position absolues. Pour cette raison, elle est centrée sur iPhone et les plus basses, mais elle n’est pas centrée sur des appareils plus grands.
- Le texte en bas de la disposition est positionné à l’aide des valeurs de taille et de position proportionnelles. Il apparaîtra toujours au centre en bas de la disposition, mais sa taille augmentera avec des tailles de présentation supérieures.
- Trois `BoxView` s de couleur sont positionnés en haut, à gauche et à droite de l’écran à l’aide de la position proportionnelle et de la taille absolue.

L’exemple suivant atteint la même disposition en C# :

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```

### <a name="proportional-values"></a>Valeurs proportionnelles

Les valeurs proportionnelles définissent une relation entre une disposition et une vue. Cette relation définit la position ou la valeur de mise à l’échelle d’une vue enfant sous la forme d’une proportion de la valeur correspondante de la disposition parente. Ces valeurs sont exprimées en `double` s avec des valeurs comprises entre 0 et 1.

Les valeurs proportionnelles sont utilisées pour positionner et dimensionner les affichages dans la disposition. Ainsi, lorsque la largeur d’une vue est définie comme une proportion, la valeur de largeur résultante est la proportion multipliée par la `AbsoluteLayout` largeur de. Par exemple, avec `AbsoluteLayout` `500` la largeur et une vue dont la largeur proportionnelle est égale à 5, la largeur rendue de la vue sera 250 (500 x. 5).

Pour utiliser des valeurs proportionnelles, définissez à `LayoutBounds` l’aide des proportions (x, y) et des tailles proportionnelles, puis affectez la valeur `LayoutFlags` à `All` .

En XAML :

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

En C# :

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

### <a name="absolute-values"></a>Valeurs absolues

Les valeurs absolues définissent explicitement où les vues doivent être positionnées dans la disposition. Contrairement aux valeurs proportionnelles, les valeurs absolues sont en capacité de positionner et de redimensionner une vue qui ne tient pas dans les limites de la disposition.

L’utilisation de valeurs absolues pour le positionnement peut être dangereuse lorsque la taille de la disposition n’est pas connue. Lors de l’utilisation de positions absolues, un élément au centre de l’écran à une taille peut être décalé à toute autre taille. Il est important de tester votre application dans les différentes tailles d’écran de vos appareils pris en charge.

Pour utiliser des valeurs de disposition absolues, définissez la valeur à `LayoutBounds` l’aide des coordonnées (x, y) et des tailles explicites, puis définissez `LayoutFlags` sur `None` .

En XAML :

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

En C# :

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>Exploration d’une disposition complexe

Chacune des dispositions présente des forces et des faiblesses pour la création de dispositions particulières. Tout au long de cette série d’Articles de la disposition, un exemple d’application a été créé avec la même mise en page implémentée à l’aide de trois dispositions différentes.

Examinez le code XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

Le code ci-dessus donne la disposition suivante :

![](absolute-layout-images/abs.png "Complex AbsoluteLayout")

Notez que `AbsoluteLayout` les s sont imbriquées, car dans certains cas, l’imbrication des dispositions peut être plus facile que la présentation de tous les éléments dans la même disposition.

## <a name="related-links"></a>Liens connexes

- [Création d’Mobile Apps avec Xamarin.Forms , chapitre 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
