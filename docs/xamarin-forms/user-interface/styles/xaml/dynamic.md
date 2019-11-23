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
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228196"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Styles dynamiques dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Les styles ne répondent pas aux modifications de propriété et restent inchangés pour la durée d’une application. Par exemple, après avoir assigné un style à un élément visuel, si l’une des instances d’accesseur Set est modifiée, supprimée ou qu’une nouvelle instance d’accesseur Set a été ajoutée, les modifications ne seront pas appliquées à l’élément visuel. Toutefois, les applications peuvent répondre de manière dynamique aux modifications de style au moment de l’exécution à l’aide de ressources dynamiques._

Le `DynamicResource` extension de balisage est similaire à la `StaticResource` une extension de balisage dans que les deux utilisent une clé de dictionnaire pour extraire une valeur à partir d’un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Toutefois, même si le `StaticResource` effectue une recherche dans le dictionnaire unique, le `DynamicResource` maintient un lien vers la clé de dictionnaire. Par conséquent, si l’entrée de dictionnaire associée à la clé est remplacée, la modification est appliquée à l’élément visuel. Ainsi, les modifications de style de runtime doit être faite dans une application.

L’exemple de code suivant montre *dynamique* styles dans une page XAML :

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

Le [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instances utilisation le `DynamicResource` extension de balisage pour faire référence à un [ `Style` ](xref:Xamarin.Forms.Style) nommé `searchBarStyle`, qui n’est pas défini dans le XAML. Toutefois, étant donné que le [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés de la `SearchBar` instances sont définies à l’aide un `DynamicResource`, la clé de dictionnaire manquante ne produit pas une exception est levée.

Au lieu de cela, dans le fichier code-behind, le constructeur crée un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) entrée avec la clé `searchBarStyle`, comme illustré dans l’exemple de code suivant :

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

Lorsque le `OnButtonClicked` Gestionnaire d’événements est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

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

En c#, le [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instances utilisation le [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) méthode à référencer `searchBarStyle`. Le `OnButtonClicked` code gestionnaire d’événements est identique à l’exemple XAML et lors de l’exécution, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Héritage de style dynamique

Dérivation d’un style à partir d’un style dynamique ne peut pas être obtenue à l’aide de la [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) propriété. Au lieu de cela, le [ `Style` ](xref:Xamarin.Forms.Style) classe inclut le [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriété, qui peut être définie sur une clé de dictionnaire dont la valeur peut changer de manière dynamique.

L’exemple de code suivant montre *dynamique* l’héritage de style dans une page XAML :

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

Le [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instances utilisation le `StaticResource` extension de balisage pour faire référence à un [ `Style` ](xref:Xamarin.Forms.Style) nommé `tealSearchBarStyle`. Cela `Style` définit des propriétés supplémentaires et utilise le [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriété à référencer `searchBarStyle`. Le `DynamicResource` extension de balisage n’est pas nécessaire car `tealSearchBarStyle` ne changera pas, à l’exception de la `Style` il dérive. Par conséquent, `tealSearchBarStyle` maintient un lien vers `searchBarStyle` et est modifié lorsque le style de base est modifié.

Dans le fichier code-behind, le constructeur crée un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) entrée avec la clé `searchBarStyle`, selon l’exemple précédent qui a démontré styles dynamiques. Lorsque le `OnButtonClicked` Gestionnaire d’événements est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

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

Le `tealSearchBarStyle` est affectée directement à la [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriété de la [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instances. Cela `Style` définit des propriétés supplémentaires et utilise le [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriété à référencer `searchBarStyle`. Le [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) méthode n’est pas nécessaire ici car `tealSearchBarStyle` ne changera pas, à l’exception de la `Style` il dérive. Par conséquent, `tealSearchBarStyle` maintient un lien vers `searchBarStyle` et est modifié lorsque le style de base est modifié.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles dynamiques (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilisation des Styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
