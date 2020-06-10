---
title : " Xamarin.Forms Description de l’interpréteur de commandes" : après un menu volant, le niveau de navigation suivant dans une application Shell est la barre d’onglets inférieure. Le modèle de navigation pour une application peut également commencer avec des onglets en bas et n’utiliser aucun menu volant. Dans les deux cas, lorsqu’un onglet inférieur contient plusieurs pages, les pages sont explorées par des onglets supérieurs.»
ms. Prod : xamarin ms. AssetID : 318D81DB-E456-4E44-B083-36A27DBD9523 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 11/06/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-shell-tabs"></a>Xamarin.FormsOnglets de Shell

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Lorsque le modèle de navigation pour une application comprend un menu volant, le niveau de navigation suivant dans l’application se situe dans la barre d’onglets du bas. De plus, lorsque le menu volant est fermé, la barre d’onglets du bas peut être considérée comme le plus haut niveau de navigation.

Le modèle de navigation pour une application peut également commencer avec des onglets en bas et n’utiliser aucun menu volant. Dans ce scénario, l’enfant de l’objet `Shell` doit être un objet `TabBar` qui représente la barre d’onglet du bas.

> [!NOTE]
> Le type `TabBar` désactive le menu volant.

Chaque objet `FlyoutItem` ou `TabBar` peut contenir un ou plusieurs objets `Tab`, où chaque objet `Tab` représente un onglet dans la barre d’onglets du bas. Chaque `Tab` objet peut contenir un ou plusieurs `ShellContent` objets, et chaque `ShellContent` objet affichera un seul [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet. Lorsque plusieurs objets `ShellContent` sont présents dans un objet `Tab`, il est possible de naviguer entre les objets `ContentPage` grâce aux onglets du haut.

Dans chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet, `ContentPage` il est possible d’accéder à des objets supplémentaires. Pour plus d’informations sur la navigation, consultez navigation dans le [ Xamarin.Forms Shell](navigation.md).

## <a name="single-page-application"></a>Application monopage

L’application Shell la plus simple est une application monopage, créée en ajoutant un seul objet `Tab` à un objet `TabBar`. Dans l' `Tab` objet, un objet `ShellContent` doit être défini sur un [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Cet exemple de code génère l’application monopage suivante :

[![Capture d’écran d’une application à page unique shell, sur iOS et Android](tabs-images/single-page-app.png "Application à page unique de l’interpréteur de commandes")](tabs-images/single-page-app-large.png#lightbox "Application à page unique de l’interpréteur de commandes")

> [!NOTE]
> La barre de navigation peut être masquée, si nécessaire, en affectant à la `Shell.NavBarIsVisible` propriété jointe `false` la valeur sur l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet.

Shell comporte des opérateurs de conversion implicite qui permettent de simplifier la hiérarchie visuelle de Shell sans introduire de vues supplémentaires dans l’arborescence d’éléments visuels. En effet, un objet `Shell` sous-classé ne peut contenir que des objets `FlyoutItem` ou un objet `TabBar`, qui ne peuvent contenir que des objets `Tab`, lesquels ne peuvent contenir que des objets `ShellContent`. Ces opérateurs de conversion implicite peuvent permettre de supprimer les objets `TabBar`, `Tab` et `ShellContent` de l’exemple précédent :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <views:CatsPage />
</Shell>
```

Cette conversion implicite encapsule automatiquement l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet dans un `ShellContent` objet, qui est encapsulé dans un objet `Tab` , qui est encapsulé dans un `FlyoutItem` objet. Un menu volant n’est pas nécessaire dans une application monopage ; par conséquent, la propriété `Shell.FlyoutBehavior` est définie sur `Disabled`.

> [!IMPORTANT]
> Dans une application Shell, chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet qui est un enfant d’un `ShellContent` objet est créé au démarrage de l’application. L’ajout d’autres objets `ShellContent` avec cette approche crée des pages supplémentaires au démarrage de l’application, ce qui peut nuire à l’expérience de démarrage. Mais Shell est également capable de créer des pages à la demande, en réponse à la navigation. Pour plus d’informations, voir [Chargement efficace des pages](tabs.md#efficient-page-loading).

## <a name="bottom-tabs"></a>Onglets du bas

Les objets `Tab` sont rendus sous forme d'onglets inférieurs, à condition qu'il y ait plusieurs objets `Tab` dans un seul objet `TabBar`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Le titre et l’icône des onglets, définis sur chaque objet `Tab`, s’affichent dans les onglets du bas :

[![Capture d’écran d’une application Shell à deux pages avec des onglets inférieurs, sur iOS et Android](tabs-images/two-page-app-bottom-tabs.png "Application Shell à deux pages avec onglets inférieurs")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "Application Shell à deux pages avec onglets inférieurs")

Lorsqu’il existe plus de cinq onglets, un onglet **plus** s’affiche, qui peut être utilisé pour accéder aux onglets supplémentaires :

[![Capture d’écran d’une application Shell avec un onglet plus, sur iOS et Android](tabs-images/more-tabs.png "Application Shell avec onglets supplémentaires")](tabs-images/more-tabs-large.png#lightbox "Shellapp avec plus d’onglets")

Les opérateurs de conversion implicite de Shell peuvent sinon permettre de supprimer les objets `ShellContent` et `Tab` de l’exemple précédent :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <views:CatsPage IconImageSource="cat.png" />
        <views:DogsPage IconImageSource="dog.png" />
    </TabBar>
</Shell>
```

Cette conversion implicite encapsule automatiquement chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet dans un `ShellContent` objet, qui sont ensuite encapsulés dans un `Tab` objet.

> [!IMPORTANT]
> Dans une application Shell, chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet qui est un enfant d’un `ShellContent` objet est créé au démarrage de l’application. L’ajout d’autres objets `ShellContent` avec cette approche crée des pages supplémentaires au démarrage de l’application, ce qui peut nuire à l’expérience de démarrage. Mais Shell est également capable de créer des pages à la demande, en réponse à la navigation. Pour plus d’informations, voir [Chargement efficace des pages](tabs.md#efficient-page-loading).

### <a name="tab-class"></a>Classe Tab

La classe `Tab` comporte différentes propriétés qui contrôlent l’apparence et le comportement des onglets :

- `CurrentItem`, de type `ShellContent`, représente l’élément sélectionné.
- `FlyoutDisplayOptions`, de type `FlyoutDisplayOptions`, définit la façon dont l’élément et ses enfants s’affichent dans le menu volant. La valeur par défaut est `AsSingleItem`.
- `FlyoutIcon`, de type `ImageSource`, définit l’icône à afficher dans le menu volant.
- `Icon`, de type `ImageSource`, définit l’icône à afficher dans les parties du chrome autres que le menu volant.
- `IsChecked`, de type `boolean`, détermine si l’élément est actuellement en surbrillance dans le menu volant.
- `IsEnabled`, de type `boolean`, détermine si l’élément est sélectionnable dans le chrome.
- `IsTabStop`, de type `bool`, indique si un `Tab` est inclus dans la navigation par onglets. Sa valeur par défaut est `true`. Lorsqu’il a la valeur`false`, `Tab` est ignoré par l’infrastructure de navigation par onglets, indépendamment de `TabIndex`.
- `Items`, de type `IList<ShellContent>`, définit tout le contenu d’un `Tab`.
- `TabIndex`, de type `int`, indique l’ordre dans lequel les objets `Tab` reçoivent le focus lorsque l’utilisateur parcourt des éléments en appuyant sur la touche Tab. La valeur par défaut de la propriété est 0.
- `Title`, de type `string`, représente le titre à afficher sur l’onglet dans l’interface utilisateur.

## <a name="shell-content"></a>Contenu de Shell

L’enfant de chaque `Tab` objet est un `ShellContent` objet dont la `Content` propriété a la valeur [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Dans chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet, `ContentPage` il est possible d’accéder à des objets supplémentaires. Pour plus d’informations sur la navigation, consultez navigation dans le [ Xamarin.Forms Shell](navigation.md).

> [!NOTE]
> Le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de chaque `ShellContent` objet est hérité de l' `Tab` objet parent.

### <a name="shellcontent-class"></a>Classe ShellContent

La classe `ShellContent` comporte différentes propriétés qui contrôlent l’apparence et le comportement du contenu des onglets :

- `Content`, de type `object`, représente le contenu de `ShellContent`.
- `ContentTemplate`, de type `DataTemplate`, représente le modèle servant à grossir dynamiquement le contenu de `ShellContent`.
- `FlyoutIcon`, de type `ImageSource`, définit l’icône à afficher dans le menu volant.
- `Icon`, de type `ImageSource`, définit l’icône à afficher dans les parties du chrome autres que le menu volant.
- `IsChecked`, de type `boolean`, détermine si l’élément est actuellement en surbrillance dans le menu volant.
- `IsEnabled`, de type `boolean`, détermine si l’élément est sélectionnable dans le chrome.
- `MenuItems`, de type `MenuItemCollection`, représente les éléments de menu à afficher dans le menu volant lorsque ce `ShellContent` est la page présentée.
- `Title`, de type `string`, représente le titre à afficher dans l’interface utilisateur.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

## <a name="bottom-and-top-tabs"></a>Onglets du haut et du bas

Quand plusieurs objets `ShellContent` sont présents dans un `Tab` objet, une barre d’onglets supérieure est ajoutée à l’onglet inférieur, qui permet de parcourir les [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Monkeys"
             Icon="monkey.png">
            <ShellContent>
                <views:MonkeysPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

En résulte la disposition des captures d’écran suivantes :

[![Capture d’écran d’une application Shell à deux pages avec onglets supérieur et inférieur, sur iOS et Android](tabs-images/two-page-app-top-tabs.png "Application Shell à deux pages avec onglets supérieur et inférieur")](tabs-images/two-page-app-top-tabs-large.png#lightbox "Application Shell à deux pages avec onglets supérieur et inférieur")

Les opérateurs de conversion implicite de Shell peuvent sinon permettre de supprimer les objets `ShellContent` et le deuxième objet `Tab` de l’exemple précédent :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <views:CatsPage />
            <views:DogsPage />
        </Tab>
        <views:MonkeysPage IconImageSource="monkey.png" />
    </TabBar>
</Shell>
```

Cette conversion implicite encapsule automatiquement `MonkeysPage` dans un objet `ShellContent`, qui est ensuite encapsulé dans un objet `Tab`. En outre, `CatsPage` et `DogsPage` sont implicitement encapsulées dans des objets `ShellContent`.

## <a name="efficient-page-loading"></a>Chargement efficace des pages

Dans une application Shell, chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet d’un `ShellContent` objet est créé au démarrage de l’application, ce qui peut entraîner une mauvaise expérience de démarrage. Cependant, Shell permet également de créer des pages à la demande, en réponse à la navigation. Pour ce faire, vous pouvez utiliser l' `DataTemplate` extension de balisage pour convertir chaque `ContentPage` en, puis [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définir le résultat en tant que `ShellContent.ContentTemplate` valeur de propriété :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">    
    <TabBar>
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
    </TabBar>    
</Shell>
```

Ce XAML crée et affiche `CatsPage`, car il s’agit du premier élément de contenu déclaré dans l’objet `Shell` sous-classé. Les pages `CatsPage` et `MonkeysPage`, accessibles par les onglets du bas, ne sont créées que lorsque l’utilisateur y accède. Cette approche présente l’avantage d’éviter une mauvaise expérience de démarrage, car les pages sont créées à la demande en réponse à la navigation, plutôt qu’au lancement de l’application.

## <a name="tab-appearance"></a>Apparence des onglets

La classe `Shell` définit différentes propriétés jointes qui contrôlent l’apparence des onglets :

- `TabBarBackgroundColor`, de type `Color` : définit la couleur d’arrière-plan de la barre d’onglets. Si la propriété n’est pas définie, la valeur de propriété `BackgroundColor` est utilisée.
- `TabBarDisabledColor`, de type `Color` : définit la couleur des éléments désactivés de la barre d’onglets. Si la propriété n’est pas définie, la valeur de propriété `DisabledColor` est utilisée.
- `TabBarForegroundColor`, de type `Color` : définit la couleur de premier plan de la barre d’onglets. Si la propriété n’est pas définie, la valeur de propriété `ForegroundColor` est utilisée.
- `TabBarTitleColor`, de type `Color` : définit la couleur des titres de la barre d’onglets. Si la propriété n’est pas définie, la valeur de propriété `TitleColor` est utilisée.
- `TabBarUnselectedColor`, de type `Color` : définit la couleur des éléments non sélectionnés de la barre d’onglets. Si la propriété n’est pas définie, la valeur de propriété `UnselectedColor` est utilisée.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données, et un style.

L’exemple suivant en montre un qui définit différentes propriétés de couleur des onglets :

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

Il est également possible de styliser les onglets avec des feuilles de style en cascade (CSS). Pour plus d’informations, consultez [ Xamarin.Forms propriétés spécifiques à l’interpréteur](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)de commandes.

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsNavigation dans le shell](navigation.md)
- [Xamarin.FormsPropriétés spécifiques de l’interpréteur de commandes CSS](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
