---
title: Style des applications Xamarin.Forms avec des feuilles de style en cascade (CSS)
description: Xamarin.Forms prend en charge le style des éléments visuels à l’aide de feuilles de Style en cascade (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 726ebd55b38460ee966113e4ee487327cd42b03d
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303931"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Styler des applications Xamarin.Forms à l’aide de feuilles de Style en cascade (CSS)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin. Forms prend en charge le style des éléments visuels à l’aide d’feuilles de style en cascade (CSS)._

Les applications Xamarin.Forms styles peuvent leur être à l’aide de CSS. Une feuille de style se compose d’une liste de règles, chacune comprenant un ou plusieurs sélecteurs et un bloc de déclaration. Un bloc de déclaration se compose d’une liste des déclarations entre accolades, avec chaque déclaration consistant en une propriété, un signe deux-points et la valeur. Lorsqu’il existe plusieurs déclarations dans un bloc, un point-virgule est inséré comme un séparateur. L’exemple de code suivant montre du code Xamarin.Forms conforme CSS :

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

Dans Xamarin.Forms, feuilles de style CSS sont analysées et évaluées au moment de l’exécution, plutôt que de compilation, et les feuilles de style sont réanalysés sur l’utilisation.

> [!NOTE]
> Actuellement, tous des éléments de style qui est possible avec les styles XAML ne peut pas être effectuée avec CSS. Toutefois, les styles XAML peuvent être utilisés pour compléter la CSS pour les propriétés qui ne sont actuellement pas pris en charge par Xamarin.Forms. Pour plus d’informations sur les styles XAML, consultez [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

L’exemple [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) illustre l’utilisation de CSS pour styliser une application simple et est illustré dans les captures d’écran suivantes :

[![Page principale MonkeyApp avec style CSS](css-images/MonkeyAppMainPage.png "Page principale MonkeyApp avec style CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Page principale MonkeyApp avec style CSS")

[![Page de détails MonkeyApp avec style CSS](css-images/MonkeyAppDetailPage.png "Page de détails MonkeyApp avec style CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Page de détails MonkeyApp avec style CSS")

## <a name="consuming-a-style-sheet"></a>Utilisation d’une feuille de style

Le processus d’ajout d’une feuille de style à une solution est la suivante :

1. Ajouter un fichier CSS vide à votre projet de bibliothèque .NET Standard.
1. Définissez l’action de génération du fichier CSS sur **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Le chargement d’une feuille de style

Il existe plusieurs approches qui peuvent être utilisées pour charger une feuille de style.

### <a name="xaml"></a>XAML

Une feuille de style peut être chargée et analysée avec la classe [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) avant d’être ajoutée à un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

La propriété [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) spécifie la feuille de style en tant qu’URI par rapport à l’emplacement du fichier XAML englobant, ou par rapport à la racine du projet si l’URI commence par une `/`.

> [!WARNING]
> Le chargement du fichier CSS échoue si son action de génération n’a pas la valeur **EmbeddedResource**.

Une feuille de style peut également être chargée et analysée avec la classe [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) , avant d’être ajoutée à un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), en l’incorporant dans une section `CDATA` :

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

Dans C#, une feuille de style peut être chargée à partir d’un `StringReader` et ajoutée à un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

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

L’argument de la méthode `StyleSheet.FromReader` est le `TextReader` qui a lu la feuille de style.

## <a name="selecting-elements-and-applying-properties"></a>Sélection des éléments et en appliquant des propriétés

CSS utilise les sélecteurs pour déterminer les éléments à cibler. Styles avec mise en correspondance les sélecteurs sont appliqués de manière consécutive, dans l’ordre de définition. Les styles définis sur un élément spécifique sont toujours appliqués en dernier. Pour plus d’informations sur les [sélecteurs](#selector-reference)pris en charge, consultez Référence du sélecteur.

CSS utilise les propriétés pour définir le style d’un élément sélectionné. Chaque propriété possède un ensemble de valeurs possibles, et certaines propriétés peuvent affecter n’importe quel type d’élément, tandis que d’autres s’appliquent à des groupes d’éléments. Pour plus d’informations sur les propriétés prises en charge, consultez [référence des propriétés](#property-reference).

### <a name="selecting-elements-by-type"></a>Sélection des éléments par type

Les éléments de l’arborescence d’éléments visuels peuvent être sélectionnés par type avec le sélecteur de `element` ne respectant pas la casse :

```css
stacklayout {
    margin: 20;
}
```

Ce sélecteur identifie tous les éléments de [`StackLayout`](xref:Xamarin.Forms.StackLayout) sur les pages qui utilisent la feuille de style et définit leurs marges sur une épaisseur uniforme de 20.

> [!NOTE]
> Le sélecteur de `element` n’identifie pas les sous-classes du type spécifié.

### <a name="selecting-elements-by-base-class"></a>Sélection des éléments par classe de base

Les éléments de l’arborescence d’éléments visuels peuvent être sélectionnés par classe de base avec le sélecteur de `^base` ne respectant pas la casse :

```css
^contentpage {
    background-color: lightgray;
}
```

Ce sélecteur identifie tous les éléments de [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui consomment la feuille de style et définit leur couleur d’arrière-plan sur `lightgray`.

> [!NOTE]
> Le sélecteur de `^base` est spécifique à Xamarin. Forms et ne fait pas partie de la spécification CSS.

### <a name="selecting-an-element-by-name"></a>Sélection d’un élément par nom

Les éléments individuels de l’arborescence d’éléments visuels peuvent être sélectionnés avec le sélecteur de `#id` sensible à la casse :

```css
#listView {
    background-color: lightgray;
}
```

Ce sélecteur identifie l’élément dont la propriété [`StyleId`](xref:Xamarin.Forms.Element.StyleId) a la valeur `listView`. Toutefois, si la propriété `StyleId` n’est pas définie, le sélecteur revient à l’utilisation du `x:Name` de l’élément. Par conséquent, dans l’exemple XAML suivant, le sélecteur de `#listView` identifie le [`ListView`](xref:Xamarin.Forms.ListView) dont l’attribut `x:Name` est défini sur `listView`et définit sa couleur d’arrière-plan sur `lightgray`.

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

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Sélection des éléments avec un attribut de classe spécifique

Les éléments avec un attribut de classe spécifique peuvent être sélectionnés avec le sélecteur de `.class` sensible à la casse :

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

Une classe CSS peut être assignée à un élément XAML en affectant au [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) propriété de l’élément le nom de la classe CSS. Par conséquent, dans l’exemple XAML suivant, les styles définis par la classe `.detailPageTitle` sont assignés au premier [`Label`](xref:Xamarin.Forms.Label), tandis que les styles définis par la classe `.detailPageSubtitle` sont assignés au deuxième `Label`.

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

Les éléments enfants de l’arborescence d’éléments visuels peuvent être sélectionnés avec le sélecteur de `element element` ne respectant pas la casse :

```css
listview image {
    height: 60;
    width: 60;
}
```

Ce sélecteur identifie tous les éléments de [`Image`](xref:Xamarin.Forms.Image) qui sont des enfants de [`ListView`](xref:Xamarin.Forms.ListView) éléments et définit leur hauteur et leur largeur sur 60. Par conséquent, dans l’exemple XAML suivant, le sélecteur de `listview image` identifie le [`Image`](xref:Xamarin.Forms.Image) qui est un enfant du [`ListView`](xref:Xamarin.Forms.ListView)et définit sa hauteur et sa largeur sur 60.

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
> Le sélecteur de `element element` ne requiert pas que l’élément enfant soit un enfant _direct_ du parent : l’élément enfant peut avoir un autre parent. Sélection se produit si un ancêtre est le premier élément spécifié.

### <a name="selecting-direct-child-elements"></a>Sélection d’éléments enfants directs

Les éléments enfants directs dans l’arborescence d’éléments visuels peuvent être sélectionnés avec le sélecteur de `element>element` ne respectant pas la casse :

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Ce sélecteur identifie tous les éléments [`Image`](xref:Xamarin.Forms.Image) qui sont des enfants directs des éléments [`StackLayout`](xref:Xamarin.Forms.StackLayout) et définit leur hauteur et leur largeur sur 200. Par conséquent, dans l’exemple XAML suivant, le sélecteur de `stacklayout>image` identifie le [`Image`](xref:Xamarin.Forms.Image) qui est un enfant direct du [`StackLayout`](xref:Xamarin.Forms.StackLayout)et définit sa hauteur et sa largeur sur 200.

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
> Le sélecteur `element>element` nécessite que l’élément enfant soit un enfant _direct_ du parent.

## <a name="selector-reference"></a>Référence du sélecteur

Les sélecteurs CSS suivants sont pris en charge par Xamarin.Forms :

|Sélecteur|Exemple|Description|
|---|---|---|
|`.class`|`.header`|Sélectionne tous les éléments avec la propriété `StyleClass` contenant « Header ». Notez que ce sélecteur respecte la casse.|
|`#id`|`#email`|Sélectionne tous les éléments avec `StyleId` défini sur `email`. Si `StyleId` n’est pas défini, le secours est `x:Name`. Lors de l’utilisation de XAML, `x:Name` est préférable sur `StyleId`. Notez que ce sélecteur respecte la casse.|
|`*`|`*`|Sélectionne tous les éléments.|
|`element`|`label`|Sélectionne tous les éléments de type `Label`, mais pas les sous-classes. Notez que ce sélecteur respecte la casse.|
|`^base`|`^contentpage`|Sélectionne tous les éléments avec `ContentPage` comme classe de base, y compris `ContentPage` lui-même. Notez que ce sélecteur respecte la casse et ne fait pas partie de la spécification CSS.|
|`element,element`|`label,button`|Sélectionne tous les éléments de `Button` et tous les éléments de `Label`. Notez que ce sélecteur respecte la casse.|
|`element element`|`stacklayout label`|Sélectionne tous les éléments `Label` à l’intérieur d’une `StackLayout`. Notez que ce sélecteur respecte la casse.|
|`element>element`|`stacklayout>label`|Sélectionne tous les éléments `Label` avec `StackLayout` en tant que parent direct. Notez que ce sélecteur respecte la casse.|
|`element+element`|`label+entry`|Sélectionne tous les éléments `Entry`s directement après une `Label`. Notez que ce sélecteur respecte la casse.|
|`element~element`|`label~entry`|Sélectionne tous les éléments `Entry` précédés d’un `Label`. Notez que ce sélecteur respecte la casse.|

Styles avec mise en correspondance les sélecteurs sont appliqués de manière consécutive, dans l’ordre de définition. Les styles définis sur un élément spécifique sont toujours appliqués en dernier.

> [!TIP]
> Les sélecteurs peuvent être combinés sans limitation, par exemple `StackLayout>ContentView>label.email`.

Les sélecteurs suivants sont actuellement pas pris en charge :

- `[attribute]`
- `@media` et `@supports`
- `:` et `::`

> [!NOTE]
> Spécificité et des remplacements de spécificité sont pris en charge.

## <a name="property-reference"></a>Informations de référence sur les propriétés

Les propriétés CSS suivantes sont prises en charge par Xamarin. Forms (dans la colonne **valeurs** , les types sont en _italique_, tandis que les littéraux de chaîne sont `gray`) :

|Propriété|S’applique à|Valeurs|Exemple|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|\| de _couleur_ `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_chaîne_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|\| de _couleur_ `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_double_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_double_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|\| de _couleur_ `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_double_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. En outre, un pourcentage compris entre 0 et 100% peut être spécifié avec le signe `%`.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|`initial` \| _float_|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|`initial` \| _float_|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_chaîne_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_\| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_double_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_double_ \| `initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_double_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|\| d' _épaisseur_ `initial` |`margin: 6 12;`|
|`margin-left`|`View`|\| d' _épaisseur_ `initial` |`margin-left: 3;`|
|`margin-top`|`View`|\| d' _épaisseur_ `initial` |`margin-top: 2;`|
|`margin-right`|`View`|\| d' _épaisseur_ `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|\| d' _épaisseur_ `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|\| _int_ `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_double_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_double_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_double_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|\| _int_ `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|\| d' _épaisseur_ `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _double_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` et `right` doivent être évités dans les environnements de droite à gauche.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _double_, _double_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_double_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` est une valeur valide pour toutes les propriétés. Elle efface la valeur (réinitialise par défaut) qui a été définie à partir d’un autre style.

Les propriétés suivantes sont actuellement pas pris en charge :

- `all: initial`.
- Propriétés de disposition (zone ou la grille).
- Propriétés sténographique, telles que `font`et `border`.

En outre, il n’y a aucune valeur `inherit` et l’héritage n’est donc pas pris en charge. Par conséquent, vous ne pouvez pas, par exemple, définir la propriété `font-size` sur une mise en page et vous attendre à ce que toutes les instances de [`Label`](xref:Xamarin.Forms.Label) dans la disposition héritent de la valeur. La seule exception est la propriété `direction`, qui a une valeur par défaut de `inherit`.

Le ciblage des éléments de `Span` a un problème connu empêchant les étendues d’être la cible des styles CSS par élément et par nom (à l’aide du symbole `#`). L’élément `Span` dérive de `GestureElement`, qui n’a pas la propriété `StyleClass`, les étendues ne prennent pas en charge le ciblage de classe CSS. Pour plus d’informations, consultez l' [impossibilité d’appliquer un style CSS pour étendre le contrôle](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Propriétés spécifiques de Xamarin. Forms

Les propriétés CSS spécifiques à Xamarin. Forms suivantes sont également prises en charge (dans la colonne **valeurs** , les types sont en _italique_, tandis que les littéraux de chaîne sont `gray`) :

|Propriété|S’applique à|Valeurs|Exemple|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|\| de _couleur_ `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|\| de _couleur_ `initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`|\| _int_ `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|\| de _couleur_ `initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|\| de _couleur_ `initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` est pris en charge uniquement sur un `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|\| de _texte entre guillemets_ `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|\| de _couleur_ `initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_double_ \| `initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|\| de _couleur_ `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_chaîne_ \| `initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Propriétés spécifiques de l’interpréteur de commandes Xamarin. Forms

Les propriétés CSS spécifiques à l’interpréteur de commandes Xamarin. Forms sont également prises en charge (dans la colonne **valeurs** , les types sont en _italique_, tandis que les littéraux de chaîne sont `gray`) :

|Propriété|S’applique à|Valeurs|Exemple|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|\| de _couleur_ `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|\| de _couleur_ `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|\| de _couleur_ `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|\| de _couleur_ `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|\| de _couleur_ `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|\| de _couleur_ `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|\| de _couleur_ `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|\| de _couleur_ `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|\| de _couleur_ `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|\| de _couleur_ `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|\| de _couleur_ `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Couleur

Les valeurs de `color` suivantes sont prises en charge :

- `X11` [couleurs](https://en.wikipedia.org/wiki/X11_color_names), qui correspondent aux couleurs CSS, aux couleurs prédéfinies UWP et aux couleurs Xamarin. Forms. Notez que ces valeurs de couleur respectent la casse.
- couleurs hexadécimales : `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- couleurs RVB : `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Les valeurs sont dans la plage 0-255, ou 0 à 100 %.
- couleurs RVBA : `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. La valeur d’opacité est dans la plage 0.0-1.0.
- couleurs TSL : `hsl(120, 100%, 50%)`. La valeur h est dans la plage 0-360, tandis que s et l sont dans la plage 0 à 100 %.
- couleurs HSLA : `hsla(120, 100%, 50%, .8)`. La valeur d’opacité est dans la plage 0.0-1.0.

### <a name="thickness"></a>Thickness

Une, deux, trois ou quatre valeurs `thickness` sont prises en charge, chacune étant séparée par un espace blanc :

- Une valeur unique indique l’épaisseur uniforme.
- Deux valeurs indiquent l’épaisseur verticale puis horizontal.
- Trois valeurs indiquent haut, puis horizontal (gauche et droite), puis épaisseur du bas.
- Quatre valeurs indiquent haut, droite, bas, puis sur épaisseur gauche.

> [!NOTE]
> Les valeurs de `thickness` CSS diffèrent des valeurs de [`Thickness`](xref:Xamarin.Forms.Thickness) XAML. Par exemple, en XAML, un `Thickness` à deux valeurs indique une épaisseur horizontale et verticale, tandis qu’un `Thickness` à quatre valeurs indique gauche, puis en haut, puis à droite, puis en bas. En outre, les valeurs `Thickness` XAML sont délimitées par des virgules.

### <a name="namedsize"></a>NamedSize

Les valeurs de `namedsize` qui ne respectent pas la casse suivantes sont prises en charge :

- `default`
- `micro`
- `small`
- `medium`
- `large`

La signification exacte de chaque valeur `namedsize` dépend de la plateforme et de la vue.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS dans Xamarin.Forms avec Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Vidéo CSS Xamarin. Forms 3,0**

## <a name="related-links"></a>Liens connexes

- [MonkeyAppCSS (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
