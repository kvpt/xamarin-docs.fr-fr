---
title: Stylisation d' Xamarin.Forms applications à l’aide de feuilles de style en cascade (CSS)
description: Xamarin.Formsprend en charge le style des éléments visuels à l’aide d’feuilles de style en cascade (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 62c8fdd6a0dd2f07b17891346081ccbcebe26c77
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918581"
---
# <a name="styling-no-locxamarinforms-apps-using-cascading-style-sheets-css"></a>Stylisation d' Xamarin.Forms applications à l’aide de feuilles de style en cascade (CSS)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin.Formsprend en charge le style des éléments visuels à l’aide d’feuilles de style en cascade (CSS)._

Xamarin.Formsles applications peuvent être stylisées à l’aide de CSS. Une feuille de style se compose d’une liste de règles, chaque règle comprenant un ou plusieurs sélecteurs et un bloc de déclaration. Un bloc de déclaration se compose d’une liste de déclarations entre accolades, chaque déclaration composée d’une propriété, d’un signe deux-points et d’une valeur. Lorsqu’il y a plusieurs déclarations dans un bloc, un point-virgule est inséré comme séparateur. L’exemple de code suivant montre une Xamarin.Forms CSS conforme :

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

Dans Xamarin.Forms , les feuilles de style CSS sont analysées et évaluées au moment de l’exécution, et non pas au moment de la compilation, et les feuilles de style sont réanalysées en cas d’utilisation.

> [!NOTE]
> Actuellement, tout le style possible avec le style XAML ne peut pas être exécuté avec CSS. Toutefois, les styles XAML peuvent être utilisés pour compléter CSS pour les propriétés qui ne sont pas prises en charge actuellement par Xamarin.Forms . Pour plus d’informations sur les styles XAML, consultez [stylisation d' Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

L’exemple [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) illustre l’utilisation de CSS pour styliser une application simple et est illustré dans les captures d’écran suivantes :

[![Page principale MonkeyApp avec style CSS](css-images/MonkeyAppMainPage.png "Page principale MonkeyApp avec style CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Page principale MonkeyApp avec style CSS")

[![Page de détails MonkeyApp avec style CSS](css-images/MonkeyAppDetailPage.png "Page de détails MonkeyApp avec style CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Page de détails MonkeyApp avec style CSS")

## <a name="consuming-a-style-sheet"></a>Utilisation d’une feuille de style

Le processus d’ajout d’une feuille de style à une solution est le suivant :

1. Ajoutez un fichier CSS vide à votre projet de bibliothèque de .NET Standard.
1. Définissez l’action de génération du fichier CSS sur **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Chargement d’une feuille de style

Il existe plusieurs approches qui peuvent être utilisées pour charger une feuille de style.

> [!NOTE]
> Actuellement, il n’est pas possible de modifier une feuille de style au moment de l’exécution et d’appliquer la nouvelle feuille de style.

### <a name="xaml"></a>XAML

Une feuille de style peut être chargée et analysée avec la [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe avant d’être ajoutée à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

La [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propriété spécifie la feuille de style en tant qu’URI par rapport à l’emplacement du fichier XAML englobant, ou par rapport à la racine du projet si l’URI commence par un `/` .

> [!WARNING]
> Le chargement du fichier CSS échoue si son action de génération n’a pas la valeur **EmbeddedResource**.

Une feuille de style peut également être chargée et analysée avec la [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe, avant d’être ajoutée à un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , en l’incorporant dans une `CDATA` section :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Pour plus d’informations sur les dictionnaires de ressources, consultez [dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md).

### <a name="c"></a>C\#

En C#, une feuille de style peut être chargée à partir d’un `StringReader` et ajoutée à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

L’argument de la `StyleSheet.FromReader` méthode est le `TextReader` qui a lu la feuille de style.

## <a name="selecting-elements-and-applying-properties"></a>Sélection d’éléments et application de propriétés

CSS utilise des sélecteurs pour déterminer les éléments à cibler. Les styles avec des sélecteurs correspondants sont appliqués consécutivement, dans l’ordre de définition. Les styles définis sur un élément spécifique sont toujours appliqués en dernier. Pour plus d’informations sur les [sélecteurs](#selector-reference)pris en charge, consultez Référence du sélecteur.

CSS utilise des propriétés pour styliser un élément sélectionné. Chaque propriété a un ensemble de valeurs possibles, et certaines propriétés peuvent affecter n’importe quel type d’élément, tandis que d’autres s’appliquent à des groupes d’éléments. Pour plus d’informations sur les propriétés prises en charge, consultez [référence des propriétés](#property-reference).

Les feuilles de style enfants remplacent toujours les feuilles de style parentes si elles définissent les mêmes propriétés. Par conséquent, les règles de précédence suivantes sont respectées lors de l’application de styles qui définissent les mêmes propriétés :

- Un style défini dans les ressources de l’application sera remplacé par un style défini dans les ressources de la page, s’il définit les mêmes propriétés.
- Un style défini dans les ressources de page sera remplacé par un style défini dans les ressources de contrôle, s’il définit les mêmes propriétés.
- Un style défini dans les ressources d’application est remplacé par un style défini dans les ressources de contrôle, s’il définit les mêmes propriétés.

> [!IMPORTANT]
> Les variables CSS ne sont pas prises en charge.

### <a name="selecting-elements-by-type"></a>Sélection d’éléments par type

Les éléments de l’arborescence d’éléments visuels peuvent être sélectionnés par type avec le sélecteur sans respect de la casse `element` :

```css
stacklayout {
    margin: 20;
}
```

Ce sélecteur identifie tous les [`StackLayout`](xref:Xamarin.Forms.StackLayout) éléments des pages qui utilisent la feuille de style et définit leurs marges sur une épaisseur uniforme de 20.

> [!NOTE]
> Le `element` sélecteur n’identifie pas les sous-classes du type spécifié.

### <a name="selecting-elements-by-base-class"></a>Sélection d’éléments par classe de base

Les éléments de l’arborescence d’éléments visuels peuvent être sélectionnés par classe de base avec le sélecteur sans respect de la casse `^base` :

```css
^contentpage {
    background-color: lightgray;
}
```

Ce sélecteur identifie tous les [`ContentPage`](xref:Xamarin.Forms.ContentPage) éléments qui consomment la feuille de style et affecte à leur couleur d’arrière-plan la valeur `lightgray` .

> [!NOTE]
> Le `^base` sélecteur est spécifique à Xamarin.Forms et ne fait pas partie de la spécification CSS.

### <a name="selecting-an-element-by-name"></a>Sélection d’un élément par son nom

Les éléments individuels de l’arborescence d’éléments visuels peuvent être sélectionnés avec le sélecteur respectant la casse `#id` :

```css
#listView {
    background-color: lightgray;
}
```

Ce sélecteur identifie l’élément dont la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propriété a la valeur `listView` . Toutefois, si la `StyleId` propriété n’est pas définie, le sélecteur revient à l’utilisation du `x:Name` de l’élément. Par conséquent, dans l’exemple XAML suivant, le `#listView` Sélecteur identifie le [`ListView`](xref:Xamarin.Forms.ListView) dont l' `x:Name` attribut a la valeur `listView` et définit sa couleur d’arrière-plan sur `lightgray` .

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Sélection d’éléments avec un attribut de classe spécifique

Les éléments avec un attribut de classe spécifique peuvent être sélectionnés avec le sélecteur respectant la casse `.class` :

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

Une classe CSS peut être assignée à un élément XAML en affectant [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) à la propriété de l’élément le nom de la classe CSS. Par conséquent, dans l’exemple XAML suivant, les styles définis par la `.detailPageTitle` classe sont assignés au premier [`Label`](xref:Xamarin.Forms.Label) , tandis que les styles définis par la `.detailPageSubtitle` classe sont assignés au second `Label` .

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>Sélection d’éléments enfants

Les éléments enfants de l’arborescence d’éléments visuels peuvent être sélectionnés avec le sélecteur sans respect de la casse `element element` :

```css
listview image {
    height: 60;
    width: 60;
}
```

Ce sélecteur identifie tous les [`Image`](xref:Xamarin.Forms.Image) éléments qui sont des enfants d' [`ListView`](xref:Xamarin.Forms.ListView) éléments et définit leur hauteur et leur largeur sur 60. Par conséquent, dans l’exemple XAML suivant, le `listview image` Sélecteur identifie le [`Image`](xref:Xamarin.Forms.Image) qui est un enfant du [`ListView`](xref:Xamarin.Forms.ListView) et définit sa hauteur et sa largeur sur 60.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Le `element element` sélecteur ne requiert pas que l’élément enfant soit un enfant _direct_ du parent : l’élément enfant peut avoir un parent différent. La sélection se produit si un ancêtre est le premier élément spécifié.

### <a name="selecting-direct-child-elements"></a>Sélection d’éléments enfants directs

Les éléments enfants directs dans l’arborescence d’éléments visuels peuvent être sélectionnés avec le sélecteur sans respect de la casse `element>element` :

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Ce sélecteur identifie tous les [`Image`](xref:Xamarin.Forms.Image) éléments qui sont des enfants directs des [`StackLayout`](xref:Xamarin.Forms.StackLayout) éléments et définit leur hauteur et leur largeur sur 200. Par conséquent, dans l’exemple XAML suivant, le `stacklayout>image` Sélecteur identifie le [`Image`](xref:Xamarin.Forms.Image) qui est un enfant direct du [`StackLayout`](xref:Xamarin.Forms.StackLayout) et définit sa hauteur et sa largeur sur 200.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> Le `element>element` Sélecteur requiert que l’élément enfant soit un enfant _direct_ du parent.

## <a name="selector-reference"></a>Référence du sélecteur

Les sélecteurs CSS suivants sont pris en charge par Xamarin.Forms :

|Sélecteur|Exemple|Description|
|---|---|---|
|`.class`|`.header`|Sélectionne tous les éléments avec la `StyleClass` propriété contenant « Header ». Notez que ce sélecteur respecte la casse.|
|`#id`|`#email`|Sélectionne tous les éléments dont la `StyleId` valeur est `email` . Si `StyleId` n’est pas défini, revenir à `x:Name` . Lors de l’utilisation de XAML, `x:Name` est préférable à `StyleId` . Notez que ce sélecteur respecte la casse.|
|`*`|`*`|Sélectionne tous les éléments.|
|`element`|`label`|Sélectionne tous les éléments de type `Label` , mais pas les sous-classes. Notez que ce sélecteur ne respecte pas la casse.|
|`^base`|`^contentpage`|Sélectionne tous les éléments avec `ContentPage` comme classe de base, y compris `ContentPage` lui-même. Notez que ce sélecteur ne respecte pas la casse et ne fait pas partie de la spécification CSS.|
|`element,element`|`label,button`|Sélectionne tous les `Button` éléments et tous les `Label` éléments. Notez que ce sélecteur ne respecte pas la casse.|
|`element element`|`stacklayout label`|Sélectionne tous les `Label` éléments à l’intérieur d’un `StackLayout` . Notez que ce sélecteur ne respecte pas la casse.|
|`element>element`|`stacklayout>label`|Sélectionne tous les `Label` éléments avec `StackLayout` comme parent direct. Notez que ce sélecteur ne respecte pas la casse.|
|`element+element`|`label+entry`|Sélectionne tous les `Entry` éléments directement après `Label` . Notez que ce sélecteur ne respecte pas la casse.|
|`element~element`|`label~entry`|Sélectionne tous les `Entry` éléments précédés d’un `Label` . Notez que ce sélecteur ne respecte pas la casse.|

Les styles avec des sélecteurs correspondants sont appliqués consécutivement, dans l’ordre de définition. Les styles définis sur un élément spécifique sont toujours appliqués en dernier.

> [!TIP]
> Les sélecteurs peuvent être combinés sans limitation, par exemple `StackLayout>ContentView>label.email` .

Les sélecteurs suivants ne sont pas pris en charge actuellement :

- `[attribute]`
- `@media` et `@supports`
- `:` et `::`

> [!NOTE]
> La spécificité et les remplacements de spécificité ne sont pas pris en charge.

## <a name="property-reference"></a>Informations de référence sur les propriétés

Les propriétés CSS suivantes sont prises en charge par Xamarin.Forms (dans la colonne **valeurs** , les types sont en _italique_, tandis que les littéraux de chaîne sont `gray` ) :

|Propriété|S’applique à|Valeurs|Exemple|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_couleur_ \|`initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_string_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_couleur_ \|`initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_double_ \|`initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_double_ \|`initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_couleur_ \|`initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_double_ \|`initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|valeur _float_ \| `auto` \| `initial`. En outre, un pourcentage compris entre 0 et 100% peut être spécifié avec le `%` signe.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|valeur _float_ \|`initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|valeur _float_ \|`initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_string_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_ \| _namedsize_ \|  `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_double_ \|`initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_double_ \|`initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_double_ \|`initial` |`line-height: 1.8;`|
|`margin`|`View`|_épaisseur_ \|`initial` |`margin: 6 12;`|
|`margin-left`|`View`|_épaisseur_ \|`initial` |`margin-left: 3;`|
|`margin-top`|`View`|_épaisseur_ \|`initial` |`margin-top: 2;`|
|`margin-right`|`View`|_épaisseur_ \|`initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_épaisseur_ \|`initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_double_ \|`initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_double_ \|`initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_double_ \|`initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_épaisseur_ \|`initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_double_ \|`initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _double_ \|`initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left`et `right` doivent être évités dans les environnements de droite à gauche.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`text-transform`|`Button`,`Editor`, `Entry`, `Label`, `SearchBar`, `SearchHandler`|`none` \| `default` \| `uppercase` \| `lowercase` \| `initial` |`text-transform: uppercase;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _double_, _double_ \|`initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_double_ \|`initial`|`min-width: 320;`|

> [!NOTE]
> `initial`est une valeur valide pour toutes les propriétés. Il efface la valeur (rétablit la valeur par défaut) qui a été définie à partir d’un autre style.

Les propriétés suivantes ne sont pas prises en charge actuellement :

- `all: initial`.
- Propriétés de la disposition (zone ou grille).
- Propriétés sténographique, telles que `font` et `border` .

En outre, il n’y a aucune `inherit` valeur et l’héritage n’est donc pas pris en charge. Par conséquent, vous ne pouvez pas, par exemple, définir la `font-size` propriété sur une mise en page et vous attendre à ce que toutes les [`Label`](xref:Xamarin.Forms.Label) instances de la disposition héritent de la valeur. La seule exception est la `direction` propriété, qui a la valeur par défaut `inherit` .

Le ciblage `Span` d’éléments a un problème connu empêchant les étendues d’être la cible des styles CSS par élément et par nom (à l’aide du `#` symbole). L' `Span` élément dérive de `GestureElement` , qui n’a pas la `StyleClass` propriété, les étendues ne prennent pas en charge le ciblage de classe CSS. Pour plus d’informations, consultez l' [impossibilité d’appliquer un style CSS pour étendre le contrôle](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="no-locxamarinforms-specific-properties"></a>Xamarin.Formspropriétés spécifiques

Les Xamarin.Forms Propriétés CSS spécifiques suivantes sont également prises en charge (dans la colonne **valeurs** , les types sont en _italique_, tandis que les littéraux de chaîne sont `gray` ) :

|Propriété|S’applique à|Valeurs|Exemple|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_couleur_ \|`initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_couleur_ \|`initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`, `SearchBar`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_couleur_ \|`initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_couleur_ \|`initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both`est pris en charge uniquement sur un `ScrollView` . |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|texte entre guillemets _quoted text_ \|`initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_couleur_ \|`initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_double_ \|`initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_couleur_ \|`initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_string_ \| `initial` |`-xf-visual: material;`|

### <a name="no-locxamarinforms-shell-specific-properties"></a>Xamarin.FormsPropriétés spécifiques à l’interpréteur de commandes

Les Xamarin.Forms Propriétés CSS spécifiques à l’interpréteur de commandes suivantes sont également prises en charge (dans la colonne **valeurs** , les types sont en _italique_, tandis que les littéraux de chaîne sont `gray` ) :

|Propriété|S’applique à|Valeurs|Exemple|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_couleur_ \|`initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_couleur_ \|`initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_couleur_ \|`initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_couleur_ \|`initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_couleur_ \|`initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_couleur_ \|`initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_couleur_ \|`initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_couleur_ \|`initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_couleur_ \|`initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_couleur_ \|`initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_couleur_ \|`initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Couleur

Les `color` valeurs suivantes sont prises en charge :

- `X11`[couleurs](https://en.wikipedia.org/wiki/X11_color_names), qui correspondent aux couleurs CSS, aux couleurs prédéfinies UWP et aux Xamarin.Forms couleurs. Notez que ces valeurs de couleur ne respectent pas la casse.
- couleurs hex : `#rgb` , `#argb` , `#rrggbb` ,`#aarrggbb`
- couleurs RVB : `rgb(255,0,0)` , `rgb(100%,0%,0%)` . Les valeurs sont comprises dans la plage 0-255, ou 0%-100%.
- couleurs RVBA : `rgba(255, 0, 0, 0.8)` , `rgba(100%, 0%, 0%, 0.8)` . La valeur d’opacité est comprise entre 0,0 et 1,0.
- couleurs TSL : `hsl(120, 100%, 50%)` . La valeur h est comprise dans la plage 0-360, tandis que s et l sont dans la plage 0%-100%.
- couleurs HSLA : `hsla(120, 100%, 50%, .8)` . La valeur d’opacité est comprise entre 0,0 et 1,0.

### <a name="thickness"></a>Thickness

Une, deux, trois ou quatre `thickness` valeurs sont prises en charge, chacune étant séparée par un espace blanc :

- Une valeur unique indique une épaisseur uniforme.
- Deux valeurs indiquent vertical, puis Epaisseur horizontale.
- Trois valeurs indiquent Top, horizontal (gauche et droite), puis Epaisseur inférieure.
- Quatre valeurs indiquent haut, puis droite, puis bas, puis gauche.

> [!NOTE]
> Les `thickness` valeurs CSS diffèrent des [`Thickness`](xref:Xamarin.Forms.Thickness) valeurs XAML. Par exemple, en XAML, une valeur à deux valeurs `Thickness` indique une épaisseur horizontale et verticale, tandis qu’une valeur de quatre valeurs est indiquée à gauche, puis à droite, puis à l' `Thickness` épaisseur inférieure. En outre, `Thickness` les valeurs XAML sont séparées par des virgules.

### <a name="namedsize"></a>NamedSize

Les valeurs non sensibles à la casse suivantes `namedsize` sont prises en charge :

- `default`
- `micro`
- `small`
- `medium`
- `large`

La signification exacte de chaque valeur dépend de la `namedsize` plateforme et de la vue.

## <a name="functions"></a>Fonctions

Les dégradés linéaires et radiaux peuvent être spécifiés à l’aide des `linear-gradient()` `radial-gradient()` fonctions CSS et, respectivement. Le résultat de ces fonctions doit être assigné à la `background` propriété d’un contrôle.

## <a name="css-in-no-locxamarinforms-with-xamarinuniversity"></a>CSS dans Xamarin.Forms avec Xamarin. University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Formsvidéo CSS 3,0**

## <a name="related-links"></a>Liens associés

- [MonkeyAppCSS (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styliser des Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
