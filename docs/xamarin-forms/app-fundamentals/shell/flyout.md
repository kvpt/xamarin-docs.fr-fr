---
title: Menu volant Shell Xamarin.Forms
description: Le menu volant est le menu racine d’une application Shell, accessible via une icône ou en balayant depuis le côté de l’écran. Le menu volant se compose d’un en-tête facultatif, de différents éléments de menu volant et d’éléments de menu facultatifs.
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2019
ms.openlocfilehash: 4049b3bdfdd6077dcfa151df9553722e63def0ba
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489958"
---
# <a name="xamarinforms-shell-flyout"></a>Menu volant Shell Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Le menu volant est le menu racine d’une application Shell, accessible via une icône ou en balayant depuis le côté de l’écran. Il se compose d’un en-tête facultatif, d’éléments de menu volant et d’éléments de menu facultatifs :

![Capture d’écran d’un menu volant annoté](flyout-images/flyout-annotated.png "Menu volant annoté")

Si nécessaire, il est possible de définir la couleur d’arrière-plan du menu volant sur une [`Color`](xref:Xamarin.Forms.Color) par la propriété `Shell.FlyoutBackgroundColor` pouvant être liée. Cette propriété peut également être spécifiée avec une feuille de style en cascade (CSS). Pour plus d’informations, consultez [Propriétés spécifiques de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="flyout-icon"></a>Icône du menu volant

Par défaut, les applications Shell comportent une icône représentant un hamburger qui a pour effet d’ouvrir le menu volant. Pour modifier cette icône, définissez la propriété `Shell.FlyoutIcon` pouvant être liée, de type [`ImageSource`](xref:Xamarin.Forms.ImageSource), sur l’icône choisie :

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-behavior"></a>Comportement du menu volant

Le menu volant est accessible par l’icône en forme de hamburger ou par balayage en partant du côté de l’écran. Pour modifier ce comportement, définissez la propriété jointe `Shell.FlyoutBehavior` sur l’un des membres de l’énumération `FlyoutBehavior` :

- `Disabled` : indique que l’utilisateur ne peut pas ouvrir le menu volant.
- `Flyout` : indique que l’utilisateur peut ouvrir et fermer le menu volant. C’est la valeur par défaut de la propriété `FlyoutBehavior`.
- `Locked` : indique que l’utilisateur ne peut pas fermer le menu volant, et que celui-ci ne présente pas de chevauchement de contenu.

L’exemple suivant montre comment désactiver le menu volant :

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

> [!NOTE]
> La propriété jointe `FlyoutBehavior` peut être définie sur `Shell`, `FlyoutItem`, `ShellContent` et des objets de page pour remplacer le comportement par défaut du menu volant.

En outre, il est possible d’ouvrir et de fermer le menu volant programmatiquement en définissant la propriété `Shell.FlyoutIsPresented` pouvant être liée sur une valeur `boolean` indiquant si le menu volant est visible :

```csharp
Shell.Current.FlyoutIsPresented = false;
```

## <a name="flyout-header"></a>En-tête de menu volant

L’en-tête du menu volant correspond au contenu qui apparaît de manière facultative en haut du menu volant et dont l’apparence est définie par un `object` spécifié par la valeur de propriété `Shell.FlyoutHeader` :

```xaml
<Shell.FlyoutHeader>
    <controls:FlyoutHeader />
</Shell.FlyoutHeader>
```

Le type `FlyoutHeader` s’affiche dans l’exemple suivant :

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Xaminals.Controls.FlyoutHeader"
             HeightRequest="200">
    <Grid BackgroundColor="Black">
        <Image Aspect="AspectFill"
               Source="xamarinstore.jpg"
               Opacity="0.6" />
        <Label Text="Animals"
               TextColor="White"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentView>
```

En résulte l’en-tête de menu volant suivant :

![Capture d’écran de l’en-tête du menu volant](flyout-images/flyout-header.png "En-tête de menu volant")

Il est également possible de définir l’apparence de l’en-tête du menu volant en spécifiant la propriété `Shell.FlyoutHeaderTemplate` sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <Grid BackgroundColor="Black"
              HeightRequest="200">
            <Image Aspect="AspectFill"
                   Source="xamarinstore.jpg"
                   Opacity="0.6" />
            <Label Text="Animals"
                   TextColor="White"
                   FontAttributes="Bold"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center" />
        </Grid>            
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

Par défaut, l’en-tête de menu volant occupe une position fixe dans le menu volant, tandis que le contenu situé en dessous défile s’il comprend suffisamment d’éléments. Pour modifier ce comportement, définissez la propriété `Shell.FlyoutHeaderBehavior` pouvant être liée sur l’un des membres de l’énumération `FlyoutHeaderBehavior` :

- `Default` : indique que le comportement par défaut de la plateforme doit être utilisé. C’est la valeur par défaut de la propriété `FlyoutHeaderBehavior`.
- `Fixed` : indique que l’en-tête du menu volant reste en permanence visible et inchangé.
- `Scroll` : indique que l’en-tête du menu volant disparaît de l’écran lorsque l’utilisateur fait défiler les éléments.
- `CollapseOnScroll` : indique que l’en-tête du menu volant se réduit à un simple titre lorsque l’utilisateur fait défiler les éléments.

L’exemple suivant montre comment réduire l’en-tête du menu volant lorsque l’utilisateur fait défiler la page :

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

## <a name="flyout-background-image"></a>Image d'arrière-plan du menu volant

Le menu volant peut avoir une image d’arrière-plan facultative, qui apparaît sous son en-tête et derrière ses éléments et les éléments de menu. L’image d’arrière-plan peut être spécifiée en définissant la propriété liable `FlyoutBackgroundImage` de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) à un fichier, une ressource incorporée, un URI ou un flux.

Les proportions de l’image d’arrière-plan peuvent être configurées en définissant la propriété liable `FlyoutBackgroundImageAspect` de type [`Aspect`](xref:Xamarin.Forms.Aspect) sur l’un des membres d’énumération `Aspect` :

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) - découpe l’image pour qu’elle remplisse la zone d’affichage tout en conservant les proportions.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) - cadre l’image, si nécessaire, pour que l’image s’ajuste à la zone d’affichage, avec un espace ajouté en haut/bas ou sur les côtés selon que l’image est large ou haute.
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) - étire l’image pour qu’elle remplisse complètement et exactement la zone d’affichage. Cela peut entraîner une distorsion de l’image.

Par défaut, la propriété `FlyoutBackgroundImageAspect` a la valeur `AspectFit`.

L’exemple suivant illustre la définition de ces propriétés :

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

Cela entraîne l’affichage d’une image d’arrière-plan dans le menu volant :

![Capture d’écran d’une image d’arrière-plan de menu volant](flyout-images/flyout-backgroundimage.png "Image d'arrière-plan du menu volant")

## <a name="flyout-items"></a>Éléments de menu volant

Lorsque le modèle de navigation d’une application comprend un menu volant, l’objet `Shell` sous-classé doit contenir au moins un objet `FlyoutItem`, chaque objet `FlyoutItem` représentant un élément du menu volant. Chaque objet `FlyoutItem` doit être un enfant de l’objet `Shell`.

L’exemple suivant crée un menu volant comportant un en-tête et deux éléments :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <FlyoutItem Title="Cats"
                Icon="cat.png">
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
        <Tab>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
</Shell>
```

Dans cet exemple, les [`ContentPage`](xref:Xamarin.Forms.ContentPage) ne sont accessibles que par le biais des éléments de menu volant :

[![Capture d’écran d’une application Shell à deux pages avec des éléments de menu volant sur iOS et Android](flyout-images/two-page-app-flyout.png "Application Shell à deux pages avec éléments de menu volant")](flyout-images/two-page-app-flyout-large.png#lightbox "Application Shell à deux pages avec éléments de menu volant")

> [!NOTE]
> En l’absence d’un en-tête de menu volant, les éléments apparaissent en haut du menu volant. Sinon, ils apparaissent sous l’en-tête de menu volant.

Shell comporte des opérateurs de conversion implicite qui permettent de simplifier la hiérarchie visuelle de Shell sans introduire de vues supplémentaires dans l’arborescence d’éléments visuels. En effet, un objet `Shell` sous-classé ne peut contenir que des objets `FlyoutItem` ou un objet `TabBar`, qui ne peuvent contenir que des objets `Tab`, lesquels ne peuvent contenir que des objets `ShellContent`. Ces opérateurs de conversion implicite peuvent permettre de supprimer les objets `FlyoutItem`, `Tab` et `ShellContent` de l’exemple précédent :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <views:CatsPage IconImageSource="cat.png" />
    <views:DogsPage IconImageSource="dog.png" />
</Shell>
```

Cette conversion implicite encapsule automatiquement chaque objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) dans des objets `ShellContent` encapsulés dans des objets `Tab`, eux-mêmes encapsulés dans des objets `FlyoutItem`.

> [!IMPORTANT]
> Dans une application Shell, tous les [`ContentPage`](xref:Xamarin.Forms.ContentPage) enfants d’un objet `ShellContent` sont créés au démarrage de l’application. L’ajout d’autres objets `ShellContent` avec cette approche crée des pages supplémentaires au démarrage de l’application, ce qui peut nuire à l’expérience de démarrage. Toutefois, Shell est également capable de créer des pages à la demande en réponse à la navigation. Pour plus d’informations, consultez [Chargement efficace d’une page](tabs.md#efficient-page-loading) dans le guide [Onglets Shell Xamarin.Forms](tabs.md).

### <a name="flyoutitem-class"></a>Classe FlyoutItem

La classe `FlyoutItem` comporte différentes propriétés qui contrôlent l’apparence et le comportement des éléments de menu volant :

- `FlyoutDisplayOptions`, de type `FlyoutDisplayOptions`, définit la façon dont l’élément et ses enfants s’affichent dans le menu volant. La valeur par défaut est `AsSingleItem`.
- `CurrentItem`, de type `Tab`, représente l’élément sélectionné.
- `Items`, de type `IList<Tab>`, définit l’ensemble des onglets d’un `FlyoutItem`.
- `FlyoutIcon`, de type `ImageSource`, représente l’icône à utiliser pour l’élément. Si cette propriété n’est pas définie, la valeur de propriété `Icon` est utilisée.
- `Icon`, de type `ImageSource`, définit l’icône à afficher dans les parties du chrome autres que le menu volant.
- `IsChecked`, de type `boolean`, détermine si l’élément est actuellement en surbrillance dans le menu volant.
- `IsEnabled`, de type `boolean`, détermine si l’élément est sélectionnable dans le chrome.
- `IsTabStop`, de type `bool`, indique si un `FlyoutItem` est inclus dans la navigation par onglets. Sa valeur par défaut est `true`. Lorsqu’il a la valeur`false`, `FlyoutItem` est ignoré par l’infrastructure de navigation par onglets, indépendamment de `TabIndex`.
- `TabIndex`, de type `int`, indique l’ordre dans lequel les objets `FlyoutItem` reçoivent le focus lorsque l’utilisateur parcourt des éléments en appuyant sur la touche Tab. La valeur par défaut de la propriété est 0.
- `Title`, de type `string`, représente le titre à afficher dans l’interface utilisateur.
- `Route`, de type `string`, représente la chaîne utilisée pour traiter l’élément.

Toutes ces propriétés sauf `Route` s’appuient sur des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

> [!NOTE]
> Tous les objets `FlyoutItem` d’un objet Shell sous-classé sont ajoutés à la collection `Shell.Items`, qui définit la liste des éléments à afficher dans le menu volant.

En outre, la classe `FlyoutItem` expose les méthodes substituables suivantes :

- `OnTabIndexPropertyChanged`, qui est appelée à chaque modification de la propriété `TabIndex`.
- `OnTabStopPropertyChanged`, qui est appelée à chaque modification de la propriété `IsTabStop`.
- `TabIndexDefaultValueCreator`, qui retourne un `int` et est appelée pour définir la valeur par défaut de la propriété `TabIndex`.
- `TabStopDefaultValueCreator`, qui retourne un `bool` et est appelée pour définir la valeur par défaut de la propriété `TabStop`.

## <a name="flyout-vertical-scroll"></a>Défilement vertical du menu volant

Par défaut, un menu volant peut faire défiler verticalement quand les éléments du menu volant ne rentrent pas dans le menu volant. Ce comportement peut être modifié en affectant à la `Shell.FlyoutVerticalScrollMode` propriété pouvant être liée la valeur l’un des membres de l’énumération `ScrollMode` :

- `Disabled` : indique que le défilement vertical est désactivé.
- `Enabled` : indique que le défilement vertical est activé.
- `Auto` : indique que le défilement vertical est activé si les éléments du menu volant ne rentrent pas dans le menu volant. C’est la valeur par défaut de la propriété `Shell.FlyoutVerticalScrollMode`.

L’exemple suivant montre comment désactiver le défilement vertical :

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled"
    ...
</Shell>
```

## <a name="flyout-display-options"></a>Options d’affichage du menu volant

L’énumération `FlyoutDisplayOptions` définit les membres suivants :

- `AsSingleItem` indique que l’élément est visible sous la forme d’un seul élément.
- `AsMultipleItems` indique que l’élément et ses enfants sont visibles dans le menu volant sous la forme d’un groupe d’éléments.

En définissant la propriété `FlyoutItem.FlyoutDisplayOptions` sur `AsMultipleItems`, un élément de menu volant est créé pour chaque `Tab` d’un `FlyoutItem` :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>

    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />  
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>

    <ShellContent Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />    
</Shell>
```

Dans cet exemple, des éléments de menu volant sont créés pour l’objet `Tab` enfant de l’objet `FlyoutItem` et les objets `ShellContent` enfants de l’objet `FlyoutItem`. En effet, chaque objet `ShellContent` enfant de l’objet `FlyoutItem` est automatiquement encapsulé dans un objet `Tab`. En outre, un élément de menu volant est créé pour le dernier objet `ShellContent`, qui est encapsulé dans un objet `Tab`, puis dans un objet `FlyoutItem`.

En résultent les éléments de menu volant suivants :

[![Capture d’écran du lanceur contenant des objets FlyoutItem, sur iOS et Android](flyout-images/flyout-reduced.png "Lanceur d’interpréteur de commandes contenant des objets FlyoutItem")](flyout-images/flyout-reduced-large.png#lightbox "Lanceur d’interpréteur de commandes contenant des objets FlyoutItem")

## <a name="define-flyoutitem-appearance"></a>Apparence de FlyoutItem

Pour personnaliser l’apparence de chaque `FlyoutItem`, définissez la propriété jointe `Shell.ItemTemplate` sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding FlyoutIcon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Title}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.ItemTemplate>
</Shell>
```

Cet exemple affiche le titre de chaque objet `FlyoutItem` en italique :

[![Capture d’écran des objets FlyoutItem basés sur des modèles, sur iOS et Android](flyout-images/flyoutitem-templated.png "Objets FlyoutItem basés sur un modèle de Shell")](flyout-images/flyoutitem-templated-large.png#lightbox "Objets FlyoutItem basés sur un modèle de Shell")


Étant donné que `Shell.ItemTemplate` est une propriété jointe, différents modèles peuvent être attachés à des objets `FlyoutItem` spécifiques.

> [!NOTE]
> Shell fournit les propriétés `Title` et `FlyoutIcon` au [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `ItemTemplate`.


### <a name="default-template-for-flyoutitems-and-menuitems"></a>Modèle par défaut pour FlyoutItems et MenuItems
L’interpréteur de commandes utilise le modèle suivant en interne pour son implémentation par défaut. Il s’agit d’un excellent point de départ si vous souhaitez faire des petites modifications aux dispositions existantes. Cela illustre également les fonctionnalités du gestionnaire d’état visuel des éléments du menu volant. Ce même modèle peut également être utilisé pour les MenuItems

```xaml
<DataTemplate x:Key="FlyoutTemplates">
    <Grid HeightRequest="{x:OnPlatform Android=50}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal">
                    </VisualState>
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="#F2F2F2" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50}"></ColumnDefinition>
            <ColumnDefinition Width="*"></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Image Source="{Binding FlyoutIcon}"
            VerticalOptions="Center"
            HorizontalOptions="Center"
            HeightRequest="{x:OnPlatform Android=24, iOS=22}"
            WidthRequest="{x:OnPlatform Android=24, iOS=22}">
        </Image>
        <Label VerticalOptions="Center"
                Text="{Binding Title}"
                FontSize="{x:OnPlatform Android=14, iOS=Small}"
                FontAttributes="Bold" Grid.Column="1">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

## <a name="flyoutitem-tab-order"></a>Ordre de tabulation FlyoutItem

Par défaut, l’ordre de tabulation des objets `FlyoutItem` est identique à l’ordre dans lequel ils apparaissent dans XAML ou sont ajoutés programmatiquement à une collection d’enfants. Les objets `FlyoutItem` seront parcourus dans cet ordre avec un clavier. Cet ordre par défaut est souvent le meilleur possible.

Pour modifier l’ordre de tabulation par défaut, définissez la propriété `FlyoutItem.TabIndex`, qui indique l’ordre dans lequel les objets `FlyoutItem` reçoivent le focus lorsque l’utilisateur parcourt des éléments en appuyant sur la touche Tab. La valeur par défaut de la propriété est 0 et elle peut être définie sur une valeur `int` quelconque.

Les règles suivantes s’appliquent lorsque vous utilisez l’ordre des tabulations par défaut ou définissez la propriété `TabIndex` :

- Les objets `FlyoutItem` pour lesquels `TabIndex` est égal à 0 sont ajoutés à l’ordre de tabulation selon leur ordre de déclaration dans XAML ou les collections enfant.
- Les objets `FlyoutItem` pour lesquels `TabIndex` est supérieur à 0 sont ajoutés à l’ordre de tabulation selon leur valeur `TabIndex`.
- Les objets `FlyoutItem` pour lesquels `TabIndex` est inférieur à 0 sont ajoutés à l’ordre de tabulation et apparaissent avant toute valeur zéro.
- Les conflits sur une propriété `TabIndex` sont résolus par ordre de déclaration.

Après avoir défini un ordre de tabulation, appuyez sur la touche Tab pour faire passer le focus d’un objet `FlyoutItem` à l’autre par ordre croissant `TabIndex`, en recommençant au début une fois que le dernier objet atteint.

Il peut être nécessaire d’exclure certains objets `FlyoutItem` de l’ordre de tabulation. Pour cela, il est possible d’utiliser la propriété `FlyoutItem.IsTabStop`, qui indique si un `FlyoutItem` est inclus dans la navigation par onglets. Sa valeur par défaut est `true`. Lorsqu’il a la valeur`false`, `FlyoutItem` est ignoré par l’infrastructure de navigation par onglets, indépendamment de `TabIndex`.

## <a name="set-the-current-flyoutitem"></a>Définir le FlyoutItem actif

La classe `Shell` comporte une propriété nommée `CurrentItem`, de type `FlyoutItem`, qui représente le `FlyoutItem` actuellement sélectionné. Lors de la première exécution d’une application Shell, cette propriété sera définie sur le premier `FlyoutItem` de l’objet `Shell` sous-classé. Toutefois, elle peut avoir pour valeur un autre `FlyoutItem`, comme dans l’exemple suivant :

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

Ce code définit l’objet `ShellContent` nommé `aboutItem` comme la propriété `CurrentItem`, ce qui provoque son affichage. Dans cet exemple, une conversion implicite est utilisée pour encapsuler l’objet `ShellContent` dans un objet `Tab`, qui est encapsulé dans un objet `FlyoutItem`.

Le code C# équivalent est :

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="menu-items"></a>Éléments de menu

Des éléments de menu en option peuvent être ajoutés au menu volant et chaque élément de menu est représenté par un objet [`MenuItem`](xref:Xamarin.Forms.MenuItem). La position des objets `MenuItem` du menu volant dépend de leur ordre de déclaration dans la hiérarchie visuelle Shell. Par conséquent, tous les objets `MenuItem` déclarés avant les objets `FlyoutItem` s’afficheront en haut du menu volant tandis que les objets `MenuItem` déclarés après les objets `FlyoutItem` s’afficheront en bas.

> [!NOTE]
> La classe `MenuItem` comporte un événement [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) et une propriété [`Command`](xref:Xamarin.Forms.MenuItem.Command). Par conséquent, les objets `MenuItem` donnent accès à des scénarios qui exécutent une action en réponse au `MenuItem` sélectionné : navigation et ouverture d’un navigateur web sur une page spécifique.

Des objets [`MenuItem`](xref:Xamarin.Forms.MenuItem) peuvent être ajoutés au menu volant comme indiqué dans l’exemple suivant :

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

Ce code ajoute deux objets [`MenuItem`](xref:Xamarin.Forms.MenuItem) au menu volant, en dessous de tous les éléments du menu :

[![Capture d’écran du menu volant contenant des objets MenuItem, sur iOS et Android](flyout-images/flyout.png "Lanceur d’interpréteur de commandes contenant des objets MenuItem")](flyout-images/flyout-large.png#lightbox "Lanceur d’interpréteur de commandes contenant des objets MenuItem")

La premier objet [`MenuItem`](xref:Xamarin.Forms.MenuItem) exécute une `ICommand` nommée `RandomPageCommand`, qui accède à une page aléatoire de l’application. Le second objet `MenuItem` exécute une `ICommand` nommée `HelpCommand`, qui ouvre l’URL spécifiée par la propriété `CommandParameter` dans un navigateur web.

> [!NOTE]
> Le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de chaque `MenuItem` est hérité de l’objet sous-classé `Shell`.

## <a name="define-menuitem-appearance"></a>Définir l’apparence de MenuItem

Pour personnaliser l’apparence de chaque `MenuItem`, définissez la propriété jointe `Shell.MenuItemTemplate` sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

Cet exemple joint le `MenuItemTemplate` au niveau du Shell à chaque objet `MenuItem`, affichant ainsi le titre de chaque objet `MenuItem` en italique :

[![Capture d’écran des objets MenuItem basés sur des modèles, sur iOS et Android](flyout-images/menuitem-templated.png "Objets MenuItem basés sur un modèle de Shell")](flyout-images/menuitem-templated-large.png#lightbox "Objets MenuItem basés sur un modèle de Shell")

> [!NOTE]
> L’interpréteur de commandes fournit les propriétés [`Text`](xref:Xamarin.Forms.MenuItem.Text) et [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) au [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du `MenuItemTemplate`. Vous pouvez également utiliser `Title` à la place de `Text` et `Icon` à la place de `IconImageSource`, ce qui vous permet de réutiliser le même modèle pour les éléments de menu et les éléments de menu volant

`Shell.MenuItemTemplate` étant une propriété jointe, les différents modèles peuvent joints à des objets `MenuItem` spécifiques :

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              Icon="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell">
        <Shell.MenuItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </Shell.MenuItemTemplate>
    </MenuItem>
</Shell>
```


> [!NOTE]
> Le même modèle utilisé pour les éléments de menu [volant](#default-template-for-flyoutitems-and-menuitems) peut également être utilisé pour les éléments de menu.

Cet exemple joint le `MenuItemTemplate` au niveau du Shell au premier objet `MenuItem` et joint le `MenuItemTemplate` inclus au second `MenuItem`.

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
