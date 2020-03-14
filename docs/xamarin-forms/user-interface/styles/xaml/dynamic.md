---
title: Styles dynamiques dans Xamarin.Forms
description: Cet article explique comment une application Xamarin.Forms peut répondre aux modifications de style dynamiquement lors de l’exécution à l’aide de ressources dynamiques.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305548"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Styles dynamiques dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Les styles ne répondent pas aux modifications de propriété et restent inchangés pour la durée d’une application. Par exemple, après avoir assigné un style à un élément visuel, si l’une des instances d’accesseur Set est modifiée, supprimée ou qu’une nouvelle instance d’accesseur Set a été ajoutée, les modifications ne seront pas appliquées à l’élément visuel. Toutefois, les applications peuvent répondre de manière dynamique aux modifications de style au moment de l’exécution à l’aide de ressources dynamiques._

L’extension de balisage `DynamicResource` est semblable à l’extension de balisage `StaticResource` en ce sens qu’elles utilisent toutes deux une clé de dictionnaire pour extraire une valeur d’un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Toutefois, pendant que l' `StaticResource` effectue une recherche dans un dictionnaire unique, le `DynamicResource` conserve un lien vers la clé du dictionnaire. Par conséquent, si l’entrée de dictionnaire associée à la clé est remplacée, la modification est appliquée à l’élément visuel. Ainsi, les modifications de style de runtime doit être faite dans une application.

L’exemple de code suivant illustre des styles *dynamiques* dans une page XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Les instances [`SearchBar`](xref:Xamarin.Forms.SearchBar) utilisent l’extension de balisage `DynamicResource` pour référencer une [`Style`](xref:Xamarin.Forms.Style) nommée `searchBarStyle`, qui n’est pas définie dans le XAML. Toutefois, étant donné que les propriétés [`Style`](xref:Xamarin.Forms.NavigableElement.Style) des instances de `SearchBar` sont définies à l’aide d’un `DynamicResource`, la clé de dictionnaire manquante n’entraîne pas la levée d’une exception.

Au lieu de cela, dans le fichier code-behind, le constructeur crée une entrée [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) avec la clé `searchBarStyle`, comme indiqué dans l’exemple de code suivant :

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Lorsque le gestionnaire d’événements `OnButtonClicked` est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Exemple de style dynamique bleu-](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![exemple de style dynamique vert](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

L’exemple de code suivant montre la page équivalente dans c# :

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

Dans C#, les instances [`SearchBar`](xref:Xamarin.Forms.SearchBar) utilisent la méthode [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) pour référencer `searchBarStyle`. Le code du gestionnaire d’événements `OnButtonClicked` est identique à l’exemple XAML, et lorsqu’il est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Héritage de style dynamique

La dérivation d’un style à partir d’un style dynamique ne peut pas être obtenue à l’aide de la propriété [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) . Au lieu de cela, la classe [`Style`](xref:Xamarin.Forms.Style) comprend la propriété [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) , qui peut être définie sur une clé de dictionnaire dont la valeur peut changer dynamiquement.

L’exemple de code suivant illustre l’héritage de style *dynamique* dans une page XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Les instances [`SearchBar`](xref:Xamarin.Forms.SearchBar) utilisent l’extension de balisage `StaticResource` pour référencer une [`Style`](xref:Xamarin.Forms.Style) nommée `tealSearchBarStyle`. Cette `Style` définit des propriétés supplémentaires et utilise la propriété [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) pour référencer `searchBarStyle`. L’extension de balisage `DynamicResource` n’est pas requise, car `tealSearchBarStyle` ne changera pas, à l’exception de la `Style` dérivée de. Par conséquent, `tealSearchBarStyle` conserve un lien vers `searchBarStyle` et est modifié lorsque le style de base change.

Dans le fichier code-behind, le constructeur crée une entrée [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) avec la clé `searchBarStyle`, conformément à l’exemple précédent qui a démontré les styles dynamiques. Lorsque le gestionnaire d’événements `OnButtonClicked` est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Exemple d’héritage](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)
[![destyledynamiquebleuexempled’héritagedestyledynamiquevert](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

L’exemple de code suivant montre la page équivalente dans c# :

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

La `tealSearchBarStyle` est assignée directement à la propriété [`Style`](xref:Xamarin.Forms.NavigableElement.Style) des instances de [`SearchBar`](xref:Xamarin.Forms.SearchBar) . Cette `Style` définit des propriétés supplémentaires et utilise la propriété [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) pour référencer `searchBarStyle`. La méthode [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) n’est pas requise ici, car `tealSearchBarStyle` ne changera pas, à l’exception de la `Style` dérivée de. Par conséquent, `tealSearchBarStyle` conserve un lien vers `searchBarStyle` et est modifié lorsque le style de base change.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles dynamiques (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilisation des styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Déclaré](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
