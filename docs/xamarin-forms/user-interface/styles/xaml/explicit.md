---
title : "styles explicites dans Xamarin.Forms " Description : "un style explicite est appliqué de manière sélective aux contrôles en définissant leurs propriétés de style. Cet article explique comment utiliser des styles explicites dans une Xamarin.Forms application.
ms. Prod : xamarin ms. AssetID : C0DF9F8F-B431-4374-A574-325BC3C41A3B ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 02/17/2016 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="explicit-styles-in-xamarinforms"></a>Styles explicites dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un style explicite est appliqué de manière sélective aux contrôles en définissant leurs propriétés de style._

## <a name="create-an-explicit-style-in-xaml"></a>Créer un style explicite en XAML

Pour déclarer un [`Style`](xref:Xamarin.Forms.Style) au niveau de la page, un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) doit être ajouté à la page, puis une ou plusieurs `Style` déclarations peuvent être incluses dans le `ResourceDictionary` . Un `Style` est rendu *explicite* en donnant à sa déclaration un `x:Key` attribut, qui lui donne une clé descriptive dans le `ResourceDictionary` . Les styles *explicites* doivent ensuite être appliqués à des éléments visuels spécifiques en définissant leurs [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés.

L’exemple de code suivant montre les styles *explicites* déclarés en XAML dans une page `ResourceDictionary` et appliqués aux instances de la page [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)Définit trois styles *explicites* qui sont appliqués aux instances de la page [`Label`](xref:Xamarin.Forms.Label) . Chaque `Style` est utilisé pour afficher du texte dans une couleur différente, tout en définissant également la taille de police et les options de disposition horizontale et verticale. Chaque `Style` est appliqué à un différent `Label` en définissant ses [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriétés à l’aide de l' `StaticResource` extension de balisage. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Exemple de styles explicites](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

En outre, la dernière [`Label`](xref:Xamarin.Forms.Label) a [`Style`](xref:Xamarin.Forms.Style) été appliquée, mais elle remplace également la [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propriété par une autre `Color` valeur.

### <a name="create-an-explicit-style-at-the-control-level"></a>Créer un style explicite au niveau du contrôle

Outre la création de styles *explicites* au niveau de la page, ils peuvent également être créés au niveau du contrôle, comme indiqué dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Dans cet exemple, les instances *explicites* [`Style`](xref:Xamarin.Forms.Style) sont assignées à la [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) collection du [`StackLayout`](xref:Xamarin.Forms.StackLayout) contrôle. Les styles peuvent ensuite être appliqués au contrôle et à ses enfants.

Pour plus d’informations sur la création de styles dans une application [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , consultez [styles globaux](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-explicit-style-in-c35"></a>Créer un style explicite en C&#35;

[`Style`](xref:Xamarin.Forms.Style)les instances peuvent être ajoutées à la collection d’une page [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) en C# en créant un nouveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , puis en ajoutant les `Style` instances à `ResourceDictionary` , comme illustré dans l’exemple de code suivant :

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

Le constructeur définit trois styles *explicites* qui sont appliqués aux instances de la page [`Label`](xref:Xamarin.Forms.Label) . Chaque *explicite* [`Style`](xref:Xamarin.Forms.Style) est ajouté au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) à l’aide de la [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) méthode, en spécifiant une `key` chaîne pour faire référence à l' `Style` instance. Chaque `Style` est appliqué à un différent `Label` en définissant leurs [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés.

Toutefois, l’utilisation d’un ici n’offre aucun avantage [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Au lieu de cela, [`Style`](xref:Xamarin.Forms.Style) les instances peuvent être assignées directement aux [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés des éléments visuels requis, et le `ResourceDictionary` peut être supprimé, comme indiqué dans l’exemple de code suivant :

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

Le constructeur définit trois styles *explicites* qui sont appliqués aux instances de la page [`Label`](xref:Xamarin.Forms.Label) . Chaque `Style` est utilisé pour afficher du texte dans une couleur différente, tout en définissant également la taille de police et les options de disposition horizontale et verticale. Chaque `Style` est appliqué à un différent `Label` en définissant ses [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés. En outre, la dernière `Label` a `Style` été appliquée, mais elle remplace également la `TextColor` propriété par une autre `Color` valeur.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilisation des styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
