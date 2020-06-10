---
titre : « Xamarin.Forms StackLayout » Description : « un StackLayout organise les vues enfants dans une pile unidimensionnelle, horizontalement ou verticalement. »
ms. Prod : xamarin ms. AssetID : 6A91EA70-268C-462C-AAAF-F8DA011403F8 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 05/11/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-stacklayout"></a>Xamarin.FormsStackLayout

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)

[![Xamarin.FormsStackLayout](stacklayout-images/layouts.png "[! Opérationnel. NO-LOC (Xamarin. Forms)] StackLayout")](stacklayout-images/layouts-large.png#lightbox "[! Opérationnel. NO-LOC (Xamarin. Forms)] StackLayout")

[`StackLayout`](xref:Xamarin.Forms.StackLayout)Organise les vues enfants dans une pile unidimensionnelle, horizontalement ou verticalement. Par défaut, un `StackLayout` est orienté verticalement. En outre, un `StackLayout` peut être utilisé comme une disposition parente qui contient d’autres dispositions enfants.

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe définit les propriétés suivantes :

- [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation), de type [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) , représente la direction dans laquelle les vues enfants sont positionnées. La valeur par défaut de cette propriété est `Vertical`.
- [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing), de type `double` , indique la quantité d’espace entre chaque vue enfant. La valeur par défaut de cette propriété est six unités indépendantes du périphérique.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données et un style.

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe dérive de la `Layout<T>` classe, qui définit une `Children` propriété de type `IList<T>` . La `Children` propriété est du `ContentProperty` de la `Layout<T>` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

> [!TIP]
> Pour obtenir les meilleures performances possibles en matière de disposition, suivez les instructions fournies dans [optimiser les performances](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)de la disposition.

## <a name="vertical-orientation"></a>Orientation verticale

Le code XAML suivant montre comment créer une orientation verticale [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui contient différentes vues enfants :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.VerticalStackLayoutPage"
             Title="Vertical StackLayout demo">
    <StackLayout Margin="20">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

Cet exemple crée un [`StackLayout`](xref:Xamarin.Forms.StackLayout) objet vertical contenant [`Label`](xref:Xamarin.Forms.Label) des [`BoxView`](xref:Xamarin.Forms.BoxView) objets et. Par défaut, il existe six unités d’espace indépendantes des appareils entre les vues enfants :

[![Capture d’écran d’un StackLayout orienté verticalement](stacklayout-images/vertical.png "StackLayout orienté verticalement")](stacklayout-images/vertical-large.png#lightbox "StackLayout orienté verticalement")

Le code C# équivalent est :

```csharp
public class VerticalStackLayoutPageCS : ContentPage
{
    public VerticalStackLayoutPageCS()
    {
        Title = "Vertical StackLayout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

> [!NOTE]
> La valeur de la [`Margin`](xref:Xamarin.Forms.View.Margin) propriété représente la distance entre un élément et ses éléments adjacents. Pour plus d’informations, consultez la page [Marge et remplissage](margin-and-padding.md).

## <a name="horizontal-orientation"></a>Orientation horizontale

Le code XAML suivant montre comment créer un orienté horizontalement [`StackLayout`](xref:Xamarin.Forms.StackLayout) en affectant [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) à sa propriété la valeur `Horizontal` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.HorizontalStackLayoutPage"
             Title="Horizontal StackLayout demo">
    <StackLayout Margin="20"
                 Orientation="Horizontal"
                 HorizontalOptions="Center">
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

Cet exemple crée un [`StackLayout`](xref:Xamarin.Forms.StackLayout) objet contenant [`BoxView`](xref:Xamarin.Forms.BoxView) des objets horizontaux, avec six unités d’espace indépendantes des appareils entre les vues enfants :

[![Capture d’écran d’un StackLayout orienté horizontalement](stacklayout-images/horizontal.png "StackLayout orienté horizontalement")](stacklayout-images/horizontal-large.png#lightbox "StackLayout orienté horizontalement")

Le code C# équivalent est :

```csharp
public HorizontalStackLayoutPageCS()
{
    Title = "Horizontal StackLayout demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Orientation = StackOrientation.Horizontal,
        HorizontalOptions = LayoutOptions.Center,
        Children =
        {
            new BoxView { Color = Color.Red },
            new BoxView { Color = Color.Yellow },
            new BoxView { Color = Color.Blue },
            new BoxView { Color = Color.Green },
            new BoxView { Color = Color.Orange },
            new BoxView { Color = Color.Purple }
        }
    };
}
```

## <a name="space-between-child-views"></a>Espace entre les vues enfants

L’espacement entre les vues enfants d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) peut être modifié en affectant [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) à la propriété une `double` valeur :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.StackLayoutSpacingPage"
             Title="StackLayout Spacing demo">
    <StackLayout Margin="20"
                 Spacing="0">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

Cet exemple crée un [`StackLayout`](xref:Xamarin.Forms.StackLayout) objet vertical contenant [`Label`](xref:Xamarin.Forms.Label) des [`BoxView`](xref:Xamarin.Forms.BoxView) objets et qui n’ont pas d’espace entre eux :

[![Capture d’écran d’un StackLayout sans espacement](stacklayout-images/spacing.png "StackLayout sans espace")](stacklayout-images/spacing-large.png#lightbox "StackLayout sans espace")

> [!TIP]
> La [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propriété peut être définie sur des valeurs négatives pour faire en sorte que les vues enfants se chevauchent.

Le code C# équivalent est :

```csharp
public class StackLayoutSpacingPageCS : ContentPage
{
    public StackLayoutSpacingPageCS()
    {
        Title = "StackLayout Spacing demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Spacing = 0,
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

## <a name="position-and-size-of-child-views"></a>Position et taille des vues enfants

La taille et la position des vues enfants dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) dépendent des valeurs des propriétés et des vues [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) enfants [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) , ainsi que des valeurs de leurs [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés et. Dans une [`StackLayout`](xref:Xamarin.Forms.StackLayout) vue verticale, les vues enfants se développent pour remplir la largeur disponible lorsque leur taille n’est pas définie explicitement. De même, dans une `StackLayout` vue horizontale, les vues enfants se développent pour remplir la hauteur disponible lorsque leur taille n’est pas définie explicitement.

Les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés et d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) et de ses vues enfants peuvent être définies sur des champs de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) structure, qui encapsule deux préférences de disposition :

- L' *alignement* détermine la position et la taille d’une vue enfant dans sa disposition parente.
- L' *expansion* indique si la vue enfant doit utiliser de l’espace supplémentaire, si elle est disponible.

> [!TIP]
> Ne définissez pas [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) les [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés et d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , sauf si vous en avez besoin. Les valeurs par défaut de `LayoutOptions.Fill` et `LayoutOptions.FillAndExpand` permettent la meilleure optimisation de la disposition. La modification de ces propriétés a un coût et consomme de la mémoire, même lors de la restauration des valeurs par défaut.

### <a name="alignment"></a>Alignement

L’exemple XAML suivant définit des préférences d’alignement sur chaque vue enfant dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.AlignmentPage"
             Title="Alignment demo">
    <StackLayout Margin="20">
        <Label Text="Start"
               BackgroundColor="Gray"
               HorizontalOptions="Start" />
        <Label Text="Center"
               BackgroundColor="Gray"
               HorizontalOptions="Center" />
        <Label Text="End"
               BackgroundColor="Gray"
               HorizontalOptions="End" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               HorizontalOptions="Fill" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, les préférences d’alignement sont définies sur les [`Label`](xref:Xamarin.Forms.Label) objets pour contrôler leur position dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Les [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) champs,, [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) [`End`](xref:Xamarin.Forms.LayoutOptions.End) et [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) sont utilisés pour définir l’alignement des [`Label`](xref:Xamarin.Forms.Label) objets dans le parent `StackLayout` :

[![Capture d’écran d’un StackLayout avec des options d’alignement définies](stacklayout-images/alignment.png "StackLayout avec options d’alignement")](stacklayout-images/alignment-large.png#lightbox "StackLayout avec options d’alignement")

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) respecte uniquement les préférences d’alignement sur les vues enfants qui se trouvent dans la direction opposée à l’orientation `StackLayout`. Par conséquent, les vues enfants [`Label`](xref:Xamarin.Forms.Label) dans `StackLayout` orienté verticalement définissent leurs propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) sur l’un des champs d’alignement :

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), qui positionne [`Label`](xref:Xamarin.Forms.Label) sur le côté gauche du [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), qui centre [`Label`](xref:Xamarin.Forms.Label) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout).
- [`End`](xref:Xamarin.Forms.LayoutOptions.End), qui positionne le [`Label`](xref:Xamarin.Forms.Label) sur le côté droit du [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), qui garantit que [`Label`](xref:Xamarin.Forms.Label) remplit la largeur de [`StackLayout`](xref:Xamarin.Forms.StackLayout).

Le code C# équivalent est :

```csharp
public class AlignmentPageCS : ContentPage
{
    public AlignmentPageCS()
    {
        Title = "Alignment demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
                new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
                new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
                new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
            }
        };
    }
}
```

### <a name="expansion"></a>Expansion

L’exemple XAML suivant définit des préférences d’expansion sur chaque [`Label`](xref:Xamarin.Forms.Label) dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.ExpansionPage"
             Title="Expansion demo">
    <StackLayout Margin="20">
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Start"
               BackgroundColor="Gray"
               VerticalOptions="StartAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Center"
               BackgroundColor="Gray"
               VerticalOptions="CenterAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="End"
               BackgroundColor="Gray"
               VerticalOptions="EndAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               VerticalOptions="FillAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, les préférences de développement sont définies sur les [`Label`](xref:Xamarin.Forms.Label) objets pour contrôler leur taille dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Les [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) champs,, [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand) [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) et [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) sont utilisés pour définir la préférence d’alignement et s’ils `Label` occupent plus d’espace s’ils sont disponibles dans le parent `StackLayout` :

[![Capture d’écran d’un StackLayout avec les options d’expansion définies](stacklayout-images/expansion.png "StackLayout avec options d’expansion")](stacklayout-images/expansion-large.png#lightbox "StackLayout avec options d’expansion")

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) peut uniquement développer les vues enfants dans le sens de son orientation. Par conséquent, le `StackLayout` orienté verticalement peut développer les vues enfants [`Label`](xref:Xamarin.Forms.Label) qui définissent leurs propriétés [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) sur l’un des champs d’extension. Cela signifie que, pour l’alignement vertical, chaque `Label` occupe la même quantité d’espace dans `StackLayout`. En revanche, seule la dernière instance `Label`, qui définit sa propriété [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) sur [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) a une taille différente.

> [!TIP]
> Lorsque vous utilisez une [`StackLayout`](xref:Xamarin.Forms.StackLayout) , assurez-vous qu’une seule vue enfant a la valeur [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Cette propriété permet de garantir que l’enfant spécifié occupe le plus grand espace que l’élément `StackLayout` peut lui donner, et il est inutile d’effectuer ces calculs plusieurs fois.

Le code C# équivalent est :

```csharp
public ExpansionPageCS()
{
    Title = "Expansion demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children =
        {
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
        }
    };
}
```

> [!IMPORTANT]
> Lorsque tout l’espace de [`StackLayout`](xref:Xamarin.Forms.StackLayout) est utilisé, les préférences d’extension n’ont aucun effet.

Pour plus d’informations sur l’alignement et l’expansion, consultez [options de disposition dans Xamarin.Forms ](layout-options.md).

## <a name="nested-stacklayout-objects"></a>Objets StackLayout imbriqués

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) peut être utilisé comme une disposition parente qui contient des objets enfants imbriqués `StackLayout` ou d’autres dispositions enfants.

Le code XAML suivant montre un exemple d’imbrication d' [`StackLayout`](xref:Xamarin.Forms.StackLayout) objets :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.CombinedStackLayoutPage"
             Title="Combined StackLayouts demo">
    <StackLayout Margin="20">
        ...
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Red" />
                <Label Text="Red"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Yellow" />
                <Label Text="Yellow"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Blue" />
                <Label Text="Blue"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        ...
    </StackLayout>
</ContentPage>
```

Dans cet exemple, le parent [`StackLayout`](xref:Xamarin.Forms.StackLayout) contient des objets imbriqués `StackLayout` à l’intérieur d' [`Frame`](xref:Xamarin.Forms.Frame) objets. Le parent `StackLayout` est orienté verticalement, tandis que les `StackLayout` objets enfants sont orientés horizontalement :

[![Capture d’écran des objets StackLayout imbriqués](stacklayout-images/combined.png "StackLayouts imbriqués")](stacklayout-images/combined-large.png#lightbox "StackLayouts imbriqués")

> [!IMPORTANT]
> Plus vous imbriquez des [`StackLayout`](xref:Xamarin.Forms.StackLayout) objets et d’autres dispositions, plus les dispositions imbriquées auront un impact sur les performances. Pour plus d’informations, consultez [choisir la disposition correcte](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

Le code C# équivalent est :

```csharp
public class CombinedStackLayoutPageCS : ContentPage
{
    public CombinedStackLayoutPageCS()
    {
        Title = "Combined StackLayouts demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Red },
                            new Label { Text = "Red", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Yellow },
                            new Label { Text = "Yellow", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Blue },
                            new Label { Text = "Blue", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                // ...
            }
        };
    }
}
```

## <a name="related-links"></a>Liens connexes

- [Démonstrations StackLayout (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)
- [Options de disposition dansXamarin.Forms](layout-options.md)
- [Choisir une Xamarin.Forms disposition](choose-layout.md)
- [Améliorer les Xamarin.Forms performances des applications](~/xamarin-forms/deploy-test/performance.md)
