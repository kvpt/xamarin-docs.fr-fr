---
title: Styles dynamiques dansXamarin.Forms
description: Cet article explique comment une Xamarin.Forms application peut répondre de manière dynamique aux modifications de style au moment de l’exécution à l’aide de ressources dynamiques.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: ''
ms.openlocfilehash: d40ca3423cca68757cf458faf5cca1138aec5461
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140086"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Styles dynamiques dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Les styles ne répondent pas aux modifications de propriété et restent inchangés pour la durée d’une application. Par exemple, après avoir assigné un style à un élément visuel, si l’une des instances d’accesseur Set est modifiée, supprimée ou qu’une nouvelle instance d’accesseur Set a été ajoutée, les modifications ne seront pas appliquées à l’élément visuel. Toutefois, les applications peuvent répondre de manière dynamique aux modifications de style au moment de l’exécution à l’aide de ressources dynamiques._

L' `DynamicResource` extension de balisage est semblable à l' `StaticResource` extension de balisage dans qui utilise toutes deux une clé de dictionnaire pour extraire une valeur d’un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Toutefois, si `StaticResource` effectue une recherche dans un dictionnaire unique, le `DynamicResource` maintient un lien vers la clé du dictionnaire. Par conséquent, si l’entrée de dictionnaire associée à la clé est remplacée, la modification est appliquée à l’élément visuel. Cela permet d’apporter des modifications de style au moment de l’exécution dans une application.

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

Les [`SearchBar`](xref:Xamarin.Forms.SearchBar) instances utilisent l' `DynamicResource` extension de balisage pour référencer un [`Style`](xref:Xamarin.Forms.Style) nommé `searchBarStyle` , qui n’est pas défini dans le XAML. Toutefois, étant donné que les [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés des `SearchBar` instances sont définies à l’aide d’un `DynamicResource` , la clé de dictionnaire manquante n’entraîne pas la levée d’une exception.

Au lieu de cela, dans le fichier code-behind, le constructeur crée une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) entrée avec la clé `searchBarStyle` , comme indiqué dans l’exemple de code suivant :

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

Lorsque le `OnButtonClicked` Gestionnaire d’événements est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle` . Cela donne l’affichage illustré dans les captures d’écran suivantes :

Exemple de style dynamique [ ![ bleu](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)- 
 [ ![ exemple de style dynamique vert](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

L’exemple de code suivant illustre la page équivalente en C# :

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

En C#, les [`SearchBar`](xref:Xamarin.Forms.SearchBar) instances utilisent la [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) méthode pour référencer `searchBarStyle` . Le `OnButtonClicked` Code du gestionnaire d’événements est identique à l’exemple XAML et, lorsqu’il est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle` .

## <a name="dynamic-style-inheritance"></a>Héritage de style dynamique

La dérivation d’un style à partir d’un style dynamique ne peut pas être obtenue à l’aide de la [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) propriété. Au lieu de cela, la [`Style`](xref:Xamarin.Forms.Style) classe comprend la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propriété, qui peut être définie sur une clé de dictionnaire dont la valeur peut changer dynamiquement.

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

Les [`SearchBar`](xref:Xamarin.Forms.SearchBar) instances utilisent l' `StaticResource` extension de balisage pour référencer un [`Style`](xref:Xamarin.Forms.Style) nommé `tealSearchBarStyle` . Cela `Style` définit des propriétés supplémentaires et utilise la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propriété pour faire référence à `searchBarStyle` . L' `DynamicResource` extension de balisage n’est pas requise, car `tealSearchBarStyle` ne change pas, sauf si `Style` elle dérive de. Par conséquent, `tealSearchBarStyle` conserve un lien vers `searchBarStyle` et est modifié lorsque le style de base change.

Dans le fichier code-behind, le constructeur crée une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) entrée avec la clé `searchBarStyle` , comme dans l’exemple précédent qui a démontré des styles dynamiques. Lorsque le `OnButtonClicked` Gestionnaire d’événements est exécuté, `searchBarStyle` bascule entre `blueSearchBarStyle` et `greenSearchBarStyle` . Cela donne l’affichage illustré dans les captures d’écran suivantes :

Exemple d' [ ![ héritage de style dynamique bleu](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)exemple d' 
 [ ![ héritage de style dynamique vert](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

L’exemple de code suivant illustre la page équivalente en C# :

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

Le `tealSearchBarStyle` est assigné directement à la [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriété des [`SearchBar`](xref:Xamarin.Forms.SearchBar) instances. Cela `Style` définit des propriétés supplémentaires et utilise la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propriété pour faire référence à `searchBarStyle` . La [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) méthode n’est pas requise ici, car `tealSearchBarStyle` ne change pas, à l’exception du `Style` dérive de. Par conséquent, `tealSearchBarStyle` conserve un lien vers `searchBarStyle` et est modifié lorsque le style de base change.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles dynamiques (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilisation des styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
