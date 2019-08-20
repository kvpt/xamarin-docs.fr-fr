---
title: Styler des applications Xamarin.Forms à l’aide de feuilles de Style en cascade (CSS)
description: Xamarin.Forms prend en charge le style des éléments visuels à l’aide de feuilles de Style en cascade (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: ed9a376da0fcfebffd707e2e93919237adfef87b
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620835"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Styler des applications Xamarin.Forms à l’aide de feuilles de Style en cascade (CSS)

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin.Forms prend en charge le style des éléments visuels à l’aide de feuilles de Style en cascade (CSS)._

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
> Actuellement, tous des éléments de style qui est possible avec les styles XAML ne peut pas être effectuée avec CSS. Toutefois, les styles XAML peuvent être utilisés pour compléter la CSS pour les propriétés qui ne sont actuellement pas pris en charge par Xamarin.Forms. Pour plus d’informations sur les styles XAML, consultez [styles des applications de Xamarin.Forms à l’aide de Styles de XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

Le [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) exemple illustre l’utilisation de CSS pour une application simple de style et est indiqué dans les captures d’écran suivante :

[![Page principale de MonkeyApp avec des styles CSS](css-images/MonkeyAppMainPage.png "MonkeyApp Main Page avec les styles CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp Main Page avec les styles CSS")

[![Page des détails avec les styles CSS MonkeyApp](css-images/MonkeyAppDetailPage.png "MonkeyApp Page de détails avec les styles CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp Page de détails avec les styles CSS")

## <a name="consuming-a-style-sheet"></a>Utilisation d’une feuille de style

Le processus d’ajout d’une feuille de style à une solution est la suivante :

1. Ajouter un fichier CSS vide à votre projet de bibliothèque .NET Standard.
1. Action de génération du fichier CSS à **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Le chargement d’une feuille de style

Il existe plusieurs approches qui peuvent être utilisées pour charger une feuille de style.

### <a name="xaml"></a>XAML

Une feuille de style peut être chargée et analysée avec le [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe avant d’être ajouté à un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

Le [ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propriété spécifie la feuille de style en tant qu’URI relatif à l’emplacement du fichier XAML englobant, ou par rapport à la racine du projet si l’URI commence par un `/`.

> [!WARNING]
> Le fichier CSS ne sera pas chargé si son action de génération n’est pas définie sur **EmbeddedResource**.

Vous pouvez également une feuille de style peut être chargée et analysée avec le [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) (classe), avant d’être ajouté à un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), par incorporation (inlining) dans un `CDATA` section :

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

Dans C#, une feuille de style peut être chargée sous la forme d’une ressource incorporée et ajoutée à un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

Le premier argument de la `StyleSheet.FromAssemblyResource` méthode est l’assembly contenant la feuille de style, tandis que le second argument est un `string` représentant l’identificateur de ressource. L’identificateur de ressource peut être obtenu à partir de la **propriétés** fenêtre lorsque le fichier CSS est sélectionné.

Vous pouvez également une feuille de style peut être chargée à partir d’un `StringReader` et ajouté à un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

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

L’argument pour le `StyleSheet.FromReader` méthode est le `TextReader` qui a lu la feuille de style.

## <a name="selecting-elements-and-applying-properties"></a>Sélection des éléments et en appliquant des propriétés

CSS utilise les sélecteurs pour déterminer les éléments à cibler. Styles avec mise en correspondance les sélecteurs sont appliqués de manière consécutive, dans l’ordre de définition. Les styles définis sur un élément spécifique sont toujours appliqués en dernier. Pour plus d’informations sur les sélecteurs pris en charge, consultez [sélecteur de référence](#selector-reference).

CSS utilise les propriétés pour définir le style d’un élément sélectionné. Chaque propriété possède un ensemble de valeurs possibles, et certaines propriétés peuvent affecter n’importe quel type d’élément, tandis que d’autres s’appliquent à des groupes d’éléments. Pour plus d’informations sur les propriétés prises en charge, consultez [référence de propriété](#property-reference).

### <a name="selecting-elements-by-type"></a>Sélection des éléments par type

Éléments dans l’arborescence visuelle peuvent être sélectionnés par type avec la respectent pas la casse `element` sélecteur :

```css
stacklayout {
    margin: 20;
}
```

Ce sélecteur identifie les [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) éléments des pages qui consomment de la feuille de style et définit les marges pour une épaisseur uniforme de 20.

> [!NOTE]
> Le `element` sélecteur n’identifie pas les sous-classes du type spécifié.

### <a name="selecting-elements-by-base-class"></a>Sélection des éléments par classe de base

Éléments dans l’arborescence visuelle peuvent être utilisés par la classe de base avec la respectent pas la casse `^base` sélecteur :

```css
^contentpage {
    background-color: lightgray;
}
```

Ce sélecteur identifie les [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) éléments qui consomment de la feuille de style et définit leur arrière-plan couleur `lightgray`.

> [!NOTE]
> Le `^base` sélecteur est spécifique à Xamarin.Forms et ne fait pas partie de la spécification CSS.

### <a name="selecting-an-element-by-name"></a>Sélection d’un élément par nom

Les éléments individuels dans l’arborescence visuelle peuvent être utilisés avec le compte de la casse `#id` sélecteur :

```css
#listView {
    background-color: lightgray;
}
```

Ce sélecteur identifie l’élément dont [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propriété est définie sur `listView`. Toutefois, si le `StyleId` propriété n’est pas définie, le sélecteur reviendra à l’aide du `x:Name` de l’élément. Par conséquent, dans l’exemple XAML suivant, le `#listView` sélecteur identifiera le [ `ListView` ](xref:Xamarin.Forms.ListView) dont `x:Name` attribut a la valeur `listView`et définit sa couleur d’arrière-plan à `lightgray`.

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

Éléments avec un attribut de classe spécifique peuvent être sélectionnés avec le compte de la casse `.class` sélecteur :

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

Une classe CSS peut être assignée à un élément XAML en définissant le [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) propriété de l’élément pour le nom de la classe CSS. Par conséquent, dans l’exemple XAML suivant, les styles définis par le `.detailPageTitle` classe sont affectés à la première [ `Label` ](xref:Xamarin.Forms.Label), tandis que les styles définis par le `.detailPageSubtitle` classe sont affectés à la seconde `Label`.

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

Les éléments enfants dans l’arborescence visuelle peuvent être sélectionnés avec la respectent pas la casse `element element` sélecteur :

```css
listview image {
    height: 60;
    width: 60;
}
```

Ce sélecteur identifie les [ `Image` ](xref:Xamarin.Forms.Image) les éléments qui sont des enfants [ `ListView` ](xref:Xamarin.Forms.ListView) éléments et définit leur hauteur et largeur à 60. Par conséquent, dans l’exemple XAML suivant, le `listview image` sélecteur identifiera le [ `Image` ](xref:Xamarin.Forms.Image) qui est un enfant de la [ `ListView` ](xref:Xamarin.Forms.ListView)et définit sa hauteur et sa largeur à 60.

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
> Le `element element` sélecteur ne nécessite pas l’élément enfant à être un _direct_ enfant du parent : l’élément enfant peut avoir un parent différent. Sélection se produit si un ancêtre est le premier élément spécifié.

### <a name="selecting-direct-child-elements"></a>Sélection d’éléments enfants directs

Diriger des éléments enfants dans l’arborescence visuelle peuvent être sélectionnés avec la respectent pas la casse `element>element` sélecteur :

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Ce sélecteur identifie les [ `Image` ](xref:Xamarin.Forms.Image) les éléments qui sont des enfants directs de [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) éléments et définit leur hauteur et largeur à 200. Par conséquent, dans l’exemple XAML suivant, le `stacklayout>image` sélecteur identifiera le [ `Image` ](xref:Xamarin.Forms.Image) qui est un enfant direct de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)et définit sa hauteur et sa largeur à 200.

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
> Le `element>element` sélecteur nécessite que l’élément enfant est un _direct_ enfant du parent.

## <a name="selector-reference"></a>Référence du sélecteur

Les sélecteurs CSS suivants sont pris en charge par Xamarin.Forms :

|Sélecteur|Exemple|Description|
|---|---|---|
|`.class`|`.header`|Sélectionne tous les éléments avec le `StyleClass` propriété contenant « header ». Notez que ce sélecteur respecte la casse.|
|`#id`|`#email`|Sélectionne tous les éléments avec `StyleId` défini sur `email`. Si `StyleId` n’est pas définie, en secours `x:Name`. Lors de l’utilisation de XAML, `x:Name` est préféré sur `StyleId`. Notez que ce sélecteur respecte la casse.|
|`*`|`*`|Sélectionne tous les éléments.|
|`element`|`label`|Sélectionne tous les éléments de `Label`type, mais pas les sous-classes. Notez que ce sélecteur respecte la casse.|
|`^base`|`^contentpage`|Sélectionne tous les éléments avec `ContentPage` comme classe de base, y compris `ContentPage` lui-même. Notez que ce sélecteur respecte la casse et ne fait pas partie de la spécification CSS.|
|`element,element`|`label,button`|Sélectionne tous les `Button` éléments et tous `Label` éléments. Notez que ce sélecteur respecte la casse.|
|`element element`|`stacklayout label`|Sélectionne tous les `Label` éléments à l’intérieur d’un `StackLayout`. Notez que ce sélecteur respecte la casse.|
|`element>element`|`stacklayout>label`|Sélectionne tous les `Label` éléments avec `StackLayout` comme un parent direct. Notez que ce sélecteur respecte la casse.|
|`element+element`|`label+entry`|Sélectionne tous les `Entry` éléments directement après une `Label`. Notez que ce sélecteur respecte la casse.|
|`element~element`|`label~entry`|Sélectionne tous les `Entry` éléments précédé par un `Label`. Notez que ce sélecteur respecte la casse.|

Styles avec mise en correspondance les sélecteurs sont appliqués de manière consécutive, dans l’ordre de définition. Les styles définis sur un élément spécifique sont toujours appliqués en dernier.

> [!TIP]
> Sélecteurs peuvent être combinés sans limitation, tel que `StackLayout>ContentView>label.email`.

Les sélecteurs suivants sont actuellement pas pris en charge :

- `[attribute]`
- `@media` et `@supports`
- `:` et `::`

> [!NOTE]
> Spécificité et des remplacements de spécificité sont pris en charge.

## <a name="property-reference"></a>Référence de propriété

Les propriétés CSS suivantes sont prises en charge par Xamarin.Forms (dans le **valeurs** sont des types de colonne, _italique_, tandis que les littéraux de chaîne sont `gray`) :

|Propriété|S'applique à|Valeurs|Exemple|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_Couleur_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_Chaîne_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_Couleur_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_Double_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_Double_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_Couleur_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_Double_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. En outre, un pourcentage dans la plage 0 à 100 % peut être spécifié avec le `%` signe.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_Float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_Float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Chaîne_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Double_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Double_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`line-height`|`Label`, `Span`|_Double_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_Épaisseur_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Épaisseur_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Épaisseur_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Épaisseur_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Épaisseur_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_Int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_Double_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Double_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Double_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_Int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_Épaisseur_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_Double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _Double_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _Double_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _Double_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _Double_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` et `right` doivent être évitées dans les environnements de droite à gauche.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, _double_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_Double_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` est une valeur valide pour toutes les propriétés. Elle efface la valeur (réinitialise par défaut) qui a été définie à partir d’un autre style.

Les propriétés suivantes sont actuellement pas pris en charge :

- `all: initial`.
- Propriétés de disposition (zone ou la grille).
- Propriétés de raccourci, tel que `font`, et `border`.

En outre, il existe aucune `inherit` l’héritage de valeur et donc n’est pas pris en charge. Par conséquent vous ne pouvez pas, par exemple, définir le `font-size` propriété sur une mise en page et attendre tous les [ `Label` ](xref:Xamarin.Forms.Label) instances dans la disposition pour hériter de la valeur. La seule exception concerne le `direction` propriété, qui a comme valeur par défaut de `inherit`.

### <a name="xamarinforms-specific-properties"></a>Propriétés spécifiques de Xamarin. Forms

Les propriétés CSS spécifiques Xamarin.Forms suivantes sont également autorisées (dans le **valeurs** sont des types de colonne, _italique_, tandis que les littéraux de chaîne sont `gray`) :

|Propriété|S'applique à|Valeurs|Exemple|
|---|---|---|---|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_texte entre guillemets_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_Couleur_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-max-length`|`Entry`, `Editor`|_Int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_Couleur_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_Couleur_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` est uniquement pris en charge sur un `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-min-track-color`|`Slider`|_Couleur_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-max-track-color`|`Slider`|_Couleur_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-thumb-color`|`Slider`|_Couleur_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-spacing`|`StackLayout`|_Double_ \| `initial` |`-xf-spacing: 8;`|

### <a name="xamarinforms-shell-specific-properties"></a>Propriétés spécifiques de l’interpréteur de commandes Xamarin. Forms

Les propriétés CSS spécifiques à l’interpréteur de commandes Xamarin. Forms sont également prises en charge (dans la colonne **valeurs** , les types `gray`sont en _italique_, tandis que les littéraux de chaîne sont):

|Propriété|S'applique à|Valeurs|Exemple|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_Couleur_ \| `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_Couleur_ \| `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_Couleur_ \| `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_Couleur_ \| `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_Couleur_ \| `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_Couleur_ \| `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_Couleur_ \| `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_Couleur_ \| `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_Couleur_ \| `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_Couleur_ \| `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_Couleur_ \| `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Color

Ce qui suit `color` valeurs sont prises en charge :

- `X11` [couleurs](https://en.wikipedia.org/wiki/X11_color_names/), qui correspond à CSS couleurs, couleurs prédéfinies UWP et les couleurs de Xamarin.Forms. Notez que ces valeurs de couleur respectent la casse.
- hex couleurs : `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- couleurs RVB : `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Les valeurs sont dans la plage 0-255, ou 0 à 100 %.
- couleurs RVBA : `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. La valeur d’opacité est dans la plage 0.0-1.0.
- les couleurs TSL : `hsl(120, 100%, 50%)`. La valeur h est dans la plage 0-360, tandis que s et l sont dans la plage 0 à 100 %.
- les couleurs hsla : `hsla(120, 100%, 50%, .8)`. La valeur d’opacité est dans la plage 0.0-1.0.

### <a name="thickness"></a>Thickness

Un, deux, trois ou quatre `thickness` valeurs sont prises en charge, séparés par un espace blanc :

- Une valeur unique indique l’épaisseur uniforme.
- Deux valeurs indiquent l’épaisseur verticale puis horizontal.
- Trois valeurs indiquent haut, puis horizontal (gauche et droite), puis épaisseur du bas.
- Quatre valeurs indiquent haut, droite, bas, puis sur épaisseur gauche.

> [!NOTE]
> CSS `thickness` diffèrent des valeurs à partir de XAML [ `Thickness` ](xref:Xamarin.Forms.Thickness) valeurs. Par exemple, dans les deux valeurs XAML `Thickness` indique l’épaisseur horizontal puis verticale, tandis qu’une valeur de quatre `Thickness` indique à gauche, puis en haut et à droite, puis bottom épaisseur. En outre, XAML `Thickness` les valeurs sont séparés par des virgules.

### <a name="namedsize"></a>NamedSize

Les éléments suivants de la casse `namedsize` valeurs sont prises en charge :

- `default`
- `micro`
- `small`
- `medium`
- `large`

La signification exacte de chaque `namedsize` valeur est dépendante de la plateforme et dépendantes de la vue.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS dans Xamarin.Forms avec Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Vidéo CSS Xamarin. Forms 3,0**

## <a name="related-links"></a>Liens associés

- [MonkeyAppCSS (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
