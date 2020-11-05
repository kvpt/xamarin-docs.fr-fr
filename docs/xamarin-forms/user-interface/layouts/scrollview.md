---
title: Xamarin.Forms ScrollView
description: Le Xamarin.Forms ScrollView est une disposition capable de faire défiler son contenu.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f59d58ed582b3fd6fd5cc25b08bbf1c262d602b4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372679"
---
# <a name="no-locxamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)

[![::: No-Loc (Xamarin. Forms) ::: ScrollView](scrollview-images/layouts.png "::: No-Loc (Xamarin. Forms) ::: ScrollView")](scrollview-images/layouts-large.png#lightbox "::: No-Loc (Xamarin. Forms) ::: ScrollView")

[`ScrollView`](xref:Xamarin.Forms.ScrollView) est une disposition qui est capable de faire défiler son contenu. La `ScrollView` classe dérive de la [`Layout`](xref:Xamarin.Forms.Layout) classe et, par défaut, fait défiler le contenu verticalement. Un `ScrollView` ne peut avoir qu’un seul enfant, bien qu’il puisse s’agir d’autres dispositions.

> [!WARNING]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView) les objets ne doivent pas être imbriqués. En outre, les `ScrollView` objets ne doivent pas être imbriqués avec d’autres contrôles qui fournissent le défilement, tels que [`CollectionView`](xref:Xamarin.Forms.CollectionView) , [`ListView`](xref:Xamarin.Forms.ListView) et [`WebView`](xref:Xamarin.Forms.WebView) .

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit les propriétés suivantes :

- [`Content`](xref:Xamarin.Forms.ScrollView.Content), de type [`View`](xref:Xamarin.Forms.View) , représente le contenu à afficher dans le [`ScrollView`](xref:Xamarin.Forms.ScrollView) .
- [`ContentSize`](xref:Xamarin.Forms.ScrollView), de type [`Size`](xref:Xamarin.Forms.Size) , représente la taille du contenu. Il s’agit d’une propriété en lecture seule.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), de type [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , représente le moment où la barre de défilement horizontale est visible.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation), de type [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , représente le sens de défilement du [`ScrollView`](xref:Xamarin.Forms.ScrollView) . La valeur par défaut de cette propriété est `Vertical`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollX), de type `double` , indique la position de défilement X actuelle. La valeur par défaut de cette propriété en lecture seule est 0.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollY), de type `double` , indique la position de défilement Y actuelle. La valeur par défaut de cette propriété en lecture seule est 0.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), de type [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , représente le moment où la barre de défilement verticale est visible.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, à l’exception de la [`Content`](xref:Xamarin.Forms.ScrollView.Content) propriété, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et de style.

La [`Content`](xref:Xamarin.Forms.ScrollView.Content) propriété est du [`ContentProperty`](xref:Xamarin.Forms.ContentPropertyAttribute) de la [`ScrollView`](xref:Xamarin.Forms.ScrollView) classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

> [!TIP]
> Pour obtenir les meilleures performances possibles en matière de disposition, suivez les instructions fournies dans [optimiser les performances](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)de la disposition.

## <a name="scrollview-as-a-root-layout"></a>ScrollView comme disposition racine

Un [`ScrollView`](xref:Xamarin.Forms.ScrollView) ne peut avoir qu’un seul enfant, qui peut être d’autres dispositions. Il est donc courant qu’un `ScrollView` soit la disposition racine sur une page. Pour faire défiler le contenu enfant, [`ScrollView`](xref:Xamarin.Forms.ScrollView) calcule la différence entre la hauteur de son contenu et sa propre hauteur. Cette différence correspond à la quantité que `ScrollView` peut faire défiler son contenu.

[`StackLayout`](xref:Xamarin.Forms.StackLayout)Est souvent l’enfant d’un `ScrollView` . Dans ce scénario, le `ScrollView` est le résultat `StackLayout` comme la somme des hauteurs de ses enfants. Ensuite, le `ScrollView` peut déterminer le degré de défilement de son contenu. Pour plus d’informations sur le `StackLayout` , consultez [ Xamarin.Forms StackLayout](stacklayout.md).

> [!CAUTION]
> Dans un vertical [`ScrollView`](xref:Xamarin.Forms.ScrollView) , évitez d’affecter `VerticalOptions` à la propriété la valeur `Start` , `Center` ou `End` . Cela `ScrollView` permet d’indiquer à la valeur la plus haute, ce qui peut être égal à zéro. Bien qu' Xamarin.Forms elle protège contre cette éventualité, il est préférable d’éviter du code qui suggère un problème que vous ne souhaitez pas faire.

L’exemple XAML suivant présente un [`ScrollView`](xref:Xamarin.Forms.ScrollView) comme une disposition racine sur une page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ScrollViewDemos"
             x:Class="ScrollViewDemos.Views.ColorListPage"
             Title="ScrollView demo">
    <ScrollView>
        <StackLayout BindableLayout.ItemsSource="{x:Static local:NamedColor.All}">
            <BindableLayout.ItemTemplate>
                <DataTemplate>
                    <StackLayout Orientation="Horizontal">
                        <BoxView Color="{Binding Color}"
                                 HeightRequest="32"
                                 WidthRequest="32"
                                 VerticalOptions="Center" />
                        <Label Text="{Binding FriendlyName}"
                               FontSize="24"
                               VerticalOptions="Center" />
                    </StackLayout>
                </DataTemplate>
            </BindableLayout.ItemTemplate>
        </StackLayout>
    </ScrollView>
</ContentPage>
```

Dans cet exemple, le [`ScrollView`](xref:Xamarin.Forms.ScrollView) contenu de est défini sur un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui utilise une disposition pouvant être liée pour afficher les [`Color`](xref:Xamarin.Forms.Color) champs définis par Xamarin.Forms . Par défaut, un `ScrollView` fait défiler verticalement, ce qui révèle plus de contenu :

[![Capture d’écran d’une disposition racine ScrollView](scrollview-images/root-layout.png "Disposition ScrollView racine")](scrollview-images/root-layout-large.png#lightbox "Disposition ScrollView racine")

Le code C# équivalent est :

```csharp
public class ColorListPageCode : ContentPage
{
    public ColorListPageCode()
    {
        DataTemplate dataTemplate = new DataTemplate(() =>
        {
            BoxView boxView = new BoxView
            {
                HeightRequest = 32,
                WidthRequest = 32,
                VerticalOptions = LayoutOptions.Center
            };
            boxView.SetBinding(BoxView.ColorProperty, "Color");

            Label label = new Label
            {
                FontSize = 24,
                VerticalOptions = LayoutOptions.Center
            };
            label.SetBinding(Label.TextProperty, "FriendlyName");

            StackLayout horizontalStackLayout = new StackLayout
            {
                Orientation = StackOrientation.Horizontal,
                Children = { boxView, label }
            };
            return horizontalStackLayout;
        });

        StackLayout stackLayout = new StackLayout();
        BindableLayout.SetItemsSource(stackLayout, NamedColor.All);
        BindableLayout.SetItemTemplate(stackLayout, dataTemplate);

        ScrollView scrollView = new ScrollView { Content = stackLayout };

        Title = "ScrollView demo";
        Content = scrollView;
    }
}
```

Pour plus d’informations sur les dispositions pouvant être liées, consultez [dispositions pouvant être Xamarin.Forms liées dans ](bindable-layouts.md).

## <a name="scrollview-as-a-child-layout"></a>ScrollView en tant que disposition enfant

Un [`ScrollView`](xref:Xamarin.Forms.ScrollView) peut être une disposition enfant à une disposition parente différente.

[`ScrollView`](xref:Xamarin.Forms.ScrollView)Est souvent l’enfant d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Un `ScrollView` requiert une hauteur spécifique pour calculer la différence entre la hauteur de son contenu et sa propre hauteur, à la différence près que le `ScrollView` peut faire défiler son contenu. Lorsqu’un `ScrollView` est l’enfant d’un `StackLayout` , il ne reçoit pas de hauteur spécifique. Le `StackLayout` veut que soit le `ScrollView` plus bref possible, soit la hauteur du `ScrollView` contenu ou zéro. Pour gérer ce scénario, la `VerticalOptions` propriété de `ScrollView` doit avoir la valeur `FillAndExpand` . Ainsi `StackLayout` , le doit donner à l' `ScrollView` ensemble de l’espace supplémentaire non requis par les autres enfants, et l' `ScrollView` objet aura alors une hauteur spécifique.

L’exemple XAML suivant présente un [`ScrollView`](xref:Xamarin.Forms.ScrollView) comme une disposition enfant à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ScrollViewDemos.Views.BlackCatPage"
             Title="ScrollView as a child layout demo">
    <StackLayout Margin="20">
        <Label Text="THE BLACK CAT by Edgar Allan Poe"
               FontSize="Medium"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <ScrollView VerticalOptions="FillAndExpand">
            <StackLayout>
                <Label Text="FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects." />
                <!-- More Label objects go here -->
            </StackLayout>
        </ScrollView>
    </StackLayout>
</ContentPage>
```

Dans cet exemple, il y a deux [`StackLayout`](xref:Xamarin.Forms.StackLayout) objets. La première `StackLayout` est l’objet de disposition racine, qui a un [`Label`](xref:Xamarin.Forms.Label) objet et un [`ScrollView`](xref:Xamarin.Forms.ScrollView) comme ses enfants. `ScrollView`A un `StackLayout` comme son contenu, avec le `StackLayout` contenant plusieurs `Label` objets. Cette organisation garantit que la première `Label` est toujours affichée à l’écran, tandis que le texte affiché par les autres objets peut faire l’objet d’un `Label` défilement :

[![Capture d’écran d’une disposition ScrollView enfant](scrollview-images/child-layout.png "Disposition des ScrollView enfants")](scrollview-images/child-layout-large.png#lightbox "Disposition des ScrollView enfants")

Le code C# équivalent est :

```csharp
public class BlackCatPageCS : ContentPage
{
    public BlackCatPageCS()
    {
        Label titleLabel = new Label
        {
            Text = "THE BLACK CAT by Edgar Allan Poe",
            // More properties set here to define the Label appearance
        };

        ScrollView scrollView = new ScrollView
        {
            VerticalOptions = LayoutOptions.FillAndExpand,
            Content = new StackLayout
            {
                Children =
                {
                    new Label
                    {
                        Text = "FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects.",
                    },
                    // More Label objects go here
                }
            }
        };

        Title = "ScrollView as a child layout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { titleLabel, scrollView }
        };
    }
}
```

## <a name="orientation"></a>Orientation

[`ScrollView`](xref:Xamarin.Forms.ScrollView) a une [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propriété, qui représente la direction de défilement du `ScrollView` . Cette propriété est de type [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , qui définit les membres suivants :

- `Vertical` indique que le `ScrollView` fait défiler verticalement. Ce membre est la valeur par défaut de la [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propriété.
- `Horizontal` indique que le `ScrollView` défile horizontalement.
- `Both` indique que le `ScrollView` défile horizontalement et verticalement.
- `Neither` indique que le `ScrollView` ne défilera pas.

> [!TIP]
> Le défilement peut être désactivé en affectant [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) à la propriété la valeur `Neither` .

## <a name="detect-scrolling"></a>Détecter le défilement

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit un [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) événement qui est déclenché pour indiquer que le défilement s’est produit. L' [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) objet qui accompagne l' `Scrolled` événement possède les `ScrollX` Propriétés et, à la `ScrollY` fois de type `double` .

> [!IMPORTANT]
> Les `ScrolledEventArgs.ScrollX` `ScrolledEventArgs.ScrollY` Propriétés et peuvent avoir des valeurs négatives, en raison de l’effet de rebond qui se produit lors du défilement vers le début d’un [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

L’exemple de code XAML suivant montre un [`ScrollView`](xref:Xamarin.Forms.ScrollView) qui définit un gestionnaire d’événements pour l' [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) événement :

```xaml
<ScrollView Scrolled="OnScrollViewScrolled">
        ...
</ScrollView>
```

Le code C# équivalent est :

```csharp
ScrollView scrollView = new ScrollView();
scrollView.Scrolled += OnScrollViewScrolled;
```

Dans cet exemple, le `OnScrollViewScrolled` Gestionnaire d’événements est exécuté lorsque l' [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) événement se déclenche :

```csharp
void OnScrollViewScrolled(object sender, ScrolledEventArgs e)
{
    Console.WriteLine($"ScrollX: {e.ScrollX}, ScrollY: {e.ScrollY}");
}
```

Dans cet exemple, le `OnScrollViewScrolled` Gestionnaire d’événements génère les valeurs de l' [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) objet qui accompagne l’événement.

> [!NOTE]
> L' [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) événement est déclenché pour les défilement initiés par l’utilisateur et pour les défilement par programmation.

## <a name="scroll-programmatically"></a>Faire défiler par programmation

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit deux [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) méthodes qui défilent de façon asynchrone le `ScrollView` . L’une des surcharges défile jusqu’à une position spécifiée dans `ScrollView` , tandis que l’autre fait défiler un élément spécifié dans la vue. Les deux surcharges ont un argument supplémentaire qui peut être utilisé pour indiquer s’il faut animer le défilement.

> [!IMPORTANT]
> Les [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) méthodes n’entraînent pas de défilement lorsque la [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propriété a la valeur `Neither` .

### <a name="scroll-a-position-into-view"></a>Faire défiler une position dans la vue

[`ScrollView`](xref:Xamarin.Forms.ScrollView)Vous pouvez faire défiler une position dans une avec la [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) méthode qui accepte les `double` `x` `y` arguments et. À partir d’un `ScrollView` objet vertical nommé `scrollView` , l’exemple suivant montre comment faire défiler jusqu’à 150 unités indépendantes du périphérique à partir du haut de `ScrollView` :

```csharp
await scrollView.ScrollToAsync(0, 150, true);
```

Le troisième argument de [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) est l' `animated` argument, qui détermine si une animation de défilement est affichée lors du défilement par programmation d’un [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

### <a name="scroll-an-element-into-view"></a>Faire défiler un élément dans l’affichage

Vous [`ScrollView`](xref:Xamarin.Forms.ScrollView) pouvez faire défiler un élément dans un affichage avec la [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) méthode qui accepte les [`Element`](xref:Xamarin.Forms.Element) [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) arguments et. À partir d’un `ScrollView` nommé vertical `scrollView` , et d’un [`Label`](xref:Xamarin.Forms.Label) nommé `label` , l’exemple suivant montre comment faire défiler un élément dans la vue :

```csharp
await scrollView.ScrollToAsync(label, ScrollToPosition.End, true);
```

Le troisième argument de [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) est l' `animated` argument, qui détermine si une animation de défilement est affichée lors du défilement par programmation d’un [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

Lorsque vous faites défiler un élément dans la vue, la position exacte de l’élément après la fin du défilement peut être définie avec le deuxième argument, `position` , de la [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) méthode. Cet argument accepte un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membre de l’énumération :

- `MakeVisible` indique que l’élément doit défiler jusqu’à ce qu’il soit visible dans le `ScrollView` .
- `Start` indique que l’élément doit défiler jusqu’au début de `ScrollView` .
- `Center` indique que l’élément doit défiler jusqu’au centre de `ScrollView` .
- `End` indique que l’élément doit défiler jusqu’à la fin de `ScrollView` .

## <a name="scroll-bar-visibility"></a>Visibilité de la barre de défilement

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) les propriétés et, qui sont sauvegardées par des propriétés pouvant être liées. Ces propriétés obtiennent ou définissent une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) valeur d’énumération qui indique si la barre de défilement horizontale ou verticale est visible. L’énumération `ScrollBarVisibility` définit les membres suivants :

- `Default` indique le comportement par défaut de la barre de défilement pour la plateforme, et est la valeur par défaut des `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` Propriétés et.
- `Always` indique que les barres de défilement sont visibles, même lorsque le contenu s’ajuste à la vue.
- `Never` indique que les barres de défilement ne sont pas visibles, même si le contenu ne tient pas dans la vue.

## <a name="related-links"></a>Liens connexes

- [Démonstrations ScrollView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)
- [Xamarin.Forms StackLayout](stacklayout.md)
- [Dispositions pouvant être liées dans Xamarin.Forms](bindable-layouts.md)