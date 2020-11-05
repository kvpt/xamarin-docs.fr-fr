---
title: Xamarin.Forms Expander
description: Le Xamarin.Forms contrôle Expander fournit un conteneur extensible pour héberger tout contenu. Le contenu est affiché ou masqué en appuyant sur l’en-tête Expander.
ms.prod: xamarin
ms.assetid: 381DCB55-522D-4414-B45B-E8DD70AA9985
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0d6cf880be995949553602f9925535e55a8dbb49
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369504"
---
# <a name="no-locxamarinforms-expander"></a>Xamarin.Forms Expander

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)

Le Xamarin.Forms `Expander` contrôle fournit un conteneur développable pour héberger tout contenu. Le contrôle possède un en-tête et un contenu, et le contenu est affiché ou masqué en appuyant sur l' `Expander` en-tête. Lorsque seul l' `Expander` en-tête est affiché, `Expander` est *réduit*. Lorsque le `Expander` contenu est visible `Expander` , le est *développé*.

Les captures d’écran suivantes montrent un `Expander` dans ses États réduit et développé, avec des zones rouges indiquant l’en-tête et le contenu :

![Capture d’écran d’un Expander dans des États réduits et développés, sur iOS et Android](expander-images/expander.png "Expander sur iOS et Android")

> [!IMPORTANT]
> `Expander` est actuellement expérimental et ne peut être utilisé qu’en définissant l' `Expander_Experimental` indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).
>
> En outre, le `Expander` contrôle est entièrement implémenté dans l' `Xamarin.Forms` espace de noms. Par conséquent, il est disponible sur toutes les plateformes prises en charge par Xamarin.Forms .

Le `Expander` contrôle définit les propriétés suivantes :

- `CollapseAnimationEasing`, de type [`Easing`](xref:Xamarin.Forms.Easing) , qui représente la fonction d’accélération à appliquer au `Expander` contenu lors de sa réduction.
- `CollapseAnimationLength`, de type `uint` , qui définit la durée de l’animation lorsque le `Expander` est réduit. La valeur par défaut de cette propriété est 250 ms.
- `Command`, de type `ICommand` , qui est exécuté lorsque l' `Expander` en-tête est frappé.
- `CommandParameter`, de type `object` : paramètre passé à la commande `Command`.
- `Content`, de type [`View`](xref:Xamarin.Forms.View) , qui définit le contenu à afficher lorsque le se `Expander` développe.
- `ContentTemplate`, de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui est le modèle utilisé pour augmenter dynamiquement le contenu du `Expander` .
- `ExpandAnimationEasing`, de type [`Easing`](xref:Xamarin.Forms.Easing) , qui représente la fonction d’accélération à appliquer au `Expander` contenu pendant l’expansion.
- `ExpandAnimationLength`, de type `uint` , qui définit la durée de l’animation lorsque le s' `Expander` étend. La valeur par défaut de cette propriété est 250 ms.
- `ForceUpdateSizeCommand`, de type `ICommand` , qui définit la commande qui est exécutée lorsque la taille de la `Expander` est mise à jour par force. Cette propriété utilise le `OneWayToSource` mode de liaison.
- `Header`, de type [`View`](xref:Xamarin.Forms.View) , qui définit le contenu de l’en-tête.
- `IsExpanded`, de type `bool` , qui détermine si le `Expander` est développé. Cette propriété utilise le `TwoWay` mode de liaison et a une valeur par défaut de `false` .
- `State`, de type `ExpanderState` , qui représente l’état de `Expander` . Cette propriété utilise le `OneWayToSource` mode de liaison.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

> [!NOTE]
> La `Content` propriété est la propriété de contenu de la `Expander` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

L’énumération `ExpanderState` définit les membres suivants :

- `Expanding` indique que le `Expander` s’étend.
- `Expanded` indique que le `Expander` est développé.
- `Collapsing` indique que le `Expander` se réduit.
- `Collapsed` indique que `Expander` est réduit.

Le `Expander` contrôle définit également un `Tapped` événement qui est déclenché lorsque l' `Expander` en-tête est frappé. De plus, `Expander` comprend une `ForceUpdateSize` méthode qui peut être appelée pour redimensionner par programmation au `Expander` moment de l’exécution.

## <a name="create-an-expander"></a>Créer un Expander

L’exemple suivant montre comment instancier un `Expander` en XAML :

```xaml
<Expander>
    <Expander.Header>
        <Label Text="Baboon"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Grid Padding="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
               Aspect="AspectFill"
               HeightRequest="120"
               WidthRequest="120" />
        <Label Grid.Column="1"
               Text="Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae."
               FontAttributes="Italic" />
    </Grid>
</Expander>
```

Dans cet exemple, le `Expander` est réduit par défaut et affiche un [`Label`](xref:Xamarin.Forms.Label) comme en-tête. Le fait de cliquer sur l’en-tête entraîne le `Expander` développement pour révéler son contenu, qui est un [`Grid`](xref:Xamarin.Forms.Grid) conteneur de contrôles enfants. Lorsque le `Expander` est développé, le fait de cliquer sur son en-tête réduit le `Expander` .

> [!IMPORTANT]
> Lors de la définition de la `Expander.Content` propriété, implicitement ou explicitement, le `Expander` contenu est créé lors de la navigation vers la page qui le contient, même si le `Expander` est réduit. Toutefois, la `Expander.ContentTemplate` propriété peut être définie sur du contenu qui est uniquement augmenté lorsque le s' `Expander` étend pour la première fois. Pour plus d’informations, consultez [créer un contenu Expander à la demande](#create-expander-content-on-demand).

Vous `Expander` pouvez également créer un dans le code :

```csharp
Expander expander = new Expander
{
    Header = new Label
    {
        Text = "Baboon",
        FontAttributes = FontAttributes.Bold,
        FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label))
    }
};

Grid grid = new Grid
{
    Padding = new Thickness(10),
    ColumnDefinitions =
    {
        new ColumnDefinition { Width = GridLength.Auto },
        new ColumnDefinition { Width = GridLength.Auto }
    }
};

grid.Children.Add(new Image
{
    Source = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg",
    Aspect = Aspect.AspectFill,
    HeightRequest = 120,
    WidthRequest = 120
});

grid.Children.Add(new Label
{
    Text = "Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae.",
    FontAttributes = FontAttributes.Italic
}, 1, 0);

expander.Content = grid;
```

## <a name="create-expander-content-on-demand"></a>Créer un contenu Expander à la demande

`Expander` le contenu peut être créé à la demande, en réponse au `Expander` développement. Pour ce faire, affectez `Expander.ContentTemplate` à la propriété une valeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui contient le contenu :

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

Dans cet exemple, le `Expander` contenu n’est pas gonflé lorsque le s' `Expander` étend pour la première fois.

L’avantage de cette approche est que lorsqu’une page contient plusieurs `Expander` objets, le contenu d’un `Expander` est créé uniquement lorsqu’il est développé pour la première fois par l’utilisateur.

## <a name="add-an-expansion-indicator"></a>Ajouter un indicateur d’expansion

Un [`Image`](xref:Xamarin.Forms.Image) peut être ajouté à un `Expander` en-tête pour fournir une indication visuelle de l’état d’expansion. Un [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) peut être attaché à `Image` , qui modifie la `Source` propriété en fonction de la valeur de la `Expander.IsExpanded` propriété :

```xaml
<Expander>
    <Expander.Header>
        <Grid>
            <Label Text="{Binding Name}"
                   FontAttributes="Bold"
                   FontSize="Medium" />
            <Image Source="expand.png"
                   HorizontalOptions="End"
                   VerticalOptions="Start">
                <Image.Triggers>
                    <DataTrigger TargetType="Image"
                                 Binding="{Binding Source={RelativeSource AncestorType={x:Type Expander}}, Path=IsExpanded}"
                                 Value="True">
                        <Setter Property="Source"
                                Value="collapse.png" />
                    </DataTrigger>
                </Image.Triggers>
            </Image>
        </Grid>
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

Dans cet exemple, le [`Image`](xref:Xamarin.Forms.Image) affiche l' `expand` icône par défaut :

![Capture d’écran d’une icône d’Expander dans un État réduit, sur iOS et Android](expander-images/icon-expand.png "Icône développée sur iOS et Android")

La `IsExpanded` propriété se transforme `true` lorsque l' `Expander` en-tête est frappé, ce qui entraîne l’affichage de l' `collapse` icône :

![Capture d’écran d’une icône d’Expander en état de développement, sur iOS et Android](expander-images/icon-collapse.png "Icône développée sur iOS et Android")

Pour plus d’informations sur les déclencheurs, consultez [ Xamarin.Forms déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="embed-an-expander-in-an-expander"></a>Incorporer un Expander dans un Expander

Le contenu d’un `Expander` peut être défini sur un autre `Expander` contrôle, afin d’activer plusieurs niveaux d’expansion. Le code XAML suivant montre un `Expander` objet dont le contenu est un autre `Expander` objet :

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander Padding="10">
        <Expander.Header>
            <Label Text="{Binding Location}"
                   FontSize="Medium" />
        </Expander.Header>
            <Expander.ContentTemplate>
                <DataTemplate>
                    <Grid>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="120"
                               WidthRequest="120" />
                        <Label Grid.Column="1"
                               Text="{Binding Details}"
                               FontAttributes="Italic" />
                    </Grid>
                </DataTemplate>
            </Expander.ContentTemplate>
    </Expander>
</Expander>
```

Dans cet exemple, le fait de cliquer sur l' `Expander` en-tête racine révèle l’en-tête de l’enfant `Expander` :

![Capture d’écran d’un Expander incorporé, sur iOS et Android](expander-images/embedded-expander1.png "Expander incorporé sur iOS et Android")

Le `Expander` fait de cliquer sur l’en-tête enfant entraîne la gonflage et l’affichage de son contenu :

![Capture d’écran d’un Expander incorporé, sur iOS et Android](expander-images/embedded-expander2.png "Expander incorporé sur iOS et Android")

## <a name="define-the-expand-and-collapse-animation"></a>Définir l’animation de développement et de réduction

L’animation qui se produit lorsqu’une `Expander` expansion ou une réduction peut être définie en affectant `ExpandAnimationEasing` les `CollapseAnimationEasing` Propriétés et à l’une des fonctions d’accélération incluses dans Xamarin.Forms , ou à des fonctions d’accélération personnalisées. Par défaut, les animations de développement et de réduction se produisent sur 250 ms. Toutefois, ces durées peuvent être modifiées en affectant `ExpandAnimationLength` `CollapseAnimationLength` aux propriétés et les `uint` valeurs.

Le code XAML suivant montre un exemple de définition de l’animation qui se produit lorsque le `Expander` est développé ou réduit par l’utilisateur :

```xaml
<Expander ExpandAnimationEasing="{x:Static Easing.CubicIn}"
          ExpandAnimationLength="500"
          CollapseAnimationEasing="{x:Static Easing.CubicOut}"
          CollapseAnimationLength="500">
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

Dans cet exemple, la `CubicIn` fonction d’accélération accélère lentement l’animation de développement sur 500 ms, et la fonction d’accélération ralentit `CubicOut` rapidement l’animation de réduction sur 500 ms.

Pour plus d’informations sur les fonctions d’accélération, consultez [ Xamarin.Forms fonctions d’accélération](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="resize-an-expander-at-runtime"></a>Redimensionner un Expander au moment de l’exécution

Un `Expander` peut être redimensionné par programmation au moment de l’exécution avec la `ForceUpdateSize` méthode.

À partir d’un `Expander` nommé `expander` , dont le contenu inclut un [`Label`](xref:Xamarin.Forms.Label) auquel un est `TapGestureRecognizer` attaché, l’exemple de code suivant illustre l’appel de la `ForceUpdateSize` méthode :

```csharp
void OnLabelTapped(object sender, EventArgs e)
{
    Label label = sender as Label;
    Expander expander = label.Parent.Parent.Parent as Expander;

    if (label.FontSize == Device.GetNamedSize(NamedSize.Default, label))
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Large, label);
    }
    else
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Default, label);
    }
    expander.ForceUpdateSize();
}
```

Dans cet exemple, le `FontSize` d’un [`Label`](xref:Xamarin.Forms.Label) change lorsque le `Label` est frappé. En raison de la taille de la police qui change, il est nécessaire de mettre à jour la taille du `Expander` en appelant sa `ForceUpdateSize` méthode.

## <a name="disable-an-expander"></a>Désactiver un Expander

Une application peut entrer dans un État où le développement d’un `Expander` n’est pas une opération valide. Dans ce cas, le `Expander` peut être désactivé en affectant `IsEnabled` à sa propriété la valeur false. Cela empêchera les utilisateurs de développer ou de réduire le `Expander` .

## <a name="related-links"></a>Liens connexes

- [Démonstrations de l’Expander (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)
- [Xamarin.Forms Fonctions d’accélération](~/xamarin-forms/user-interface/animation/easing.md)
- [Xamarin.Forms Déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Dispositions pouvant être liées](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)