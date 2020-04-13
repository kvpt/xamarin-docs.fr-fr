---
title: Disposition Shell Xamarin.Forms
description: Le niveau de navigation suivant le menu volant dans une application Shell est la barre d’onglets du bas. Le modèle de navigation pour une application peut également commencer avec des onglets en bas et n’utiliser aucun menu volant. Dans les deux cas, lorsqu’un onglet contient plusieurs pages, il est possible de passer de l’une à l’autre grâce aux onglets du haut.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
ms.openlocfilehash: 0ffcbe99ef9696c5fde501809bea5ddcc7a793a7
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305142"
---
# <a name="xamarinforms-shell-tabs"></a>Onglets Shell Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Lorsque le modèle de navigation pour une application comprend un menu volant, le niveau de navigation suivant dans l’application se situe dans la barre d’onglets du bas. De plus, lorsque le menu volant est fermé, la barre d’onglets du bas peut être considérée comme le plus haut niveau de navigation.

Le modèle de navigation pour une application peut également commencer avec des onglets en bas et n’utiliser aucun menu volant. Dans ce scénario, l’enfant de l’objet `Shell` doit être un objet `TabBar` qui représente la barre d’onglet du bas.

> [!NOTE]
> Le type `TabBar` désactive le menu volant.

Chaque objet `FlyoutItem` ou `TabBar` peut contenir un ou plusieurs objets `Tab`, où chaque objet `Tab` représente un onglet dans la barre d’onglets du bas. Chaque `Tab` objet peut contenir `ShellContent` un ou `ShellContent` plusieurs objets, [`ContentPage`](xref:Xamarin.Forms.ContentPage) et chaque objet affichera un seul objet. Lorsque plusieurs objets `ShellContent` sont présents dans un objet `Tab`, il est possible de naviguer entre les objets `ContentPage` grâce aux onglets du haut.

Dans [`ContentPage`](xref:Xamarin.Forms.ContentPage) chaque objet, des objets supplémentaires `ContentPage` peuvent être navigués vers. Pour plus d’informations sur la navigation, voir [Navigation Shell Xamarin.Forms](navigation.md).

## <a name="single-page-application"></a>Application monopage

L’application Shell la plus simple est une application monopage, créée en ajoutant un seul objet `Tab` à un objet `TabBar`. Dans `Tab` l’objet, un `ShellContent` objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) doit être réglé sur un objet :

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

[![Capture d’écran d’une application Shell d’une seule page, sur iOS et Android](tabs-images/single-page-app.png "Application Shell page unique")](tabs-images/single-page-app-large.png#lightbox "Application Shell page unique")

> [!NOTE]
> La barre de navigation peut être cachée, si nécessaire, en fixant la `Shell.NavBarIsVisible` propriété attachée à `false` l’objet. [`ContentPage`](xref:Xamarin.Forms.ContentPage)

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

Cette conversion implicite enveloppe [`ContentPage`](xref:Xamarin.Forms.ContentPage) automatiquement `ShellContent` l’objet dans un `Tab` objet, qui est `FlyoutItem` enveloppé dans un objet, qui est enveloppé dans un objet. Un menu volant n’est pas nécessaire dans une application monopage ; par conséquent, la propriété `Shell.FlyoutBehavior` est définie sur `Disabled`.

> [!IMPORTANT]
> Dans une application [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, chacun d’eux est un enfant d’un `ShellContent` objet est créé lors du démarrage de l’application. L’ajout d’autres objets `ShellContent` avec cette approche crée des pages supplémentaires au démarrage de l’application, ce qui peut nuire à l’expérience de démarrage. Mais Shell est également capable de créer des pages à la demande, en réponse à la navigation. Pour plus d’informations, voir [Chargement efficace des pages](tabs.md#efficient-page-loading).

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

[![Capture d’écran d’une application Shell de deux pages avec onglets inférieurs, sur iOS et Android](tabs-images/two-page-app-bottom-tabs.png "Shell application de deux pages avec des onglets inférieurs")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "Shell application de deux pages avec des onglets inférieurs")

Lorsqu’il y a plus de cinq onglets, un onglet **Plus** apparaîtra, qui peut être utilisé pour accéder aux onglets supplémentaires :

[![Capture d’écran d’une application Shell avec un onglet Plus, sur iOS et Android](tabs-images/more-tabs.png "Application Shell avec plus d’onglet")](tabs-images/more-tabs-large.png#lightbox "Shellapp avec plus d’onglets")

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

Cette conversion implicite enveloppe [`ContentPage`](xref:Xamarin.Forms.ContentPage) automatiquement `ShellContent` chaque objet dans un objet, qui sont ensuite enveloppés dans un `Tab` objet.

> [!IMPORTANT]
> Dans une application [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, chacun d’eux est un enfant d’un `ShellContent` objet est créé lors du démarrage de l’application. L’ajout d’autres objets `ShellContent` avec cette approche crée des pages supplémentaires au démarrage de l’application, ce qui peut nuire à l’expérience de démarrage. Mais Shell est également capable de créer des pages à la demande, en réponse à la navigation. Pour plus d’informations, voir [Chargement efficace des pages](tabs.md#efficient-page-loading).

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

L’enfant `Tab` de chaque `ShellContent` objet `Content` est un objet, dont la propriété est réglée sur un [`ContentPage`](xref:Xamarin.Forms.ContentPage):

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

Dans [`ContentPage`](xref:Xamarin.Forms.ContentPage) chaque objet, des objets supplémentaires `ContentPage` peuvent être navigués vers. Pour plus d’informations sur la navigation, voir [Navigation Shell Xamarin.Forms](navigation.md).

> [!NOTE]
> [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) L’objet `ShellContent` de chaque objet `Tab` est hérité de l’objet parent.

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

Toutes ces propriétés [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) sont sauvegardées par des objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

## <a name="bottom-and-top-tabs"></a>Onglets du haut et du bas

Lorsque plus `ShellContent` d’un objet `Tab` est présent dans un objet, une barre [`ContentPage`](xref:Xamarin.Forms.ContentPage) d’onglet supérieur est ajoutée à l’onglet inférieur, à travers lequel les objets sont navigables :

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

[![Capture d’écran d’une application Shell de deux pages avec des onglets haut et bas, sur iOS et Android](tabs-images/two-page-app-top-tabs.png "Shell application de deux pages avec des onglets haut et bas")](tabs-images/two-page-app-top-tabs-large.png#lightbox "Shell application de deux pages avec des onglets haut et bas")

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

Dans une application [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, `ShellContent` chaque objet d’un objet est créé lors du démarrage de l’application, ce qui peut conduire à une mauvaise expérience de démarrage. Cependant, Shell permet également de créer des pages à la demande, en réponse à la navigation. Ceci peut être accompli `DataTemplate` en utilisant l’extension [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)de balisage pour `ShellContent.ContentTemplate` convertir chacun `ContentPage` en un , puis en définissant le résultat comme la valeur de propriété :

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

Toutes ces propriétés [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) sont sauvegardées par des objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données, et stylées.

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

Il est également possible de styliser les onglets avec des feuilles de style en cascade (CSS). Pour plus d’informations, consultez [Propriétés spécifiques de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navigation Shell Xamarin.Forms](navigation.md)
- [Propriétés spécifiques du CSS Shell Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
