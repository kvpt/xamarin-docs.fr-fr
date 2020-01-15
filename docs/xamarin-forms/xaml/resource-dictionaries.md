---
title: Dictionnaires de ressources
description: Les ressources XAML sont des objets qui peuvent être partagés et réutilisés tout au long d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2019
ms.custom: video
ms.openlocfilehash: 49ea5c2a8332e625710af8a9947e1cb0e0338040
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955826"
---
# <a name="resource-dictionaries"></a>Dictionnaires de ressources

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Les ressources XAML sont des définitions d’objets qui peuvent être partagés et réutilisés dans une application Xamarin. Forms. Ces objets de ressource sont stockés dans un dictionnaire de ressources._

Un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) est un référentiel pour les ressources qui sont utilisés par une application Xamarin.Forms. Les ressources classiques qui sont stockées dans un `ResourceDictionary` incluent [styles](~/xamarin-forms/user-interface/styles/index.md), [modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md), [modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), couleurs et les convertisseurs.

Dans XAML, les ressources qui sont stockées dans un `ResourceDictionary` peuvent être récupérées et appliqués aux éléments à l’aide de la `StaticResource` extension de balisage. En c#, les ressources peuvent également être définies dans un `ResourceDictionary` puis récupérés et appliqués aux éléments à l’aide d’un indexeur de chaîne. Toutefois, il existe peu d’avantages à l’aide un `ResourceDictionary` en c#, que les objets partagés peuvent simplement être stockées en tant que champs ou propriétés et accessible directement sans avoir d’abord récupérer à partir d’un dictionnaire.

## <a name="create-and-consume-a-resourcedictionary"></a>Créer et consommer un ResourceDictionary

Ressources sont définies dans un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) qui est alors définies sur une de ces `Resources` propriétés :

- Le [ `Resources` ](xref:Xamarin.Forms.Application.Resources) propriété de n’importe quelle classe qui dérive de [`Application`](xref:Xamarin.Forms.Application)
- Le [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) propriété de n’importe quelle classe qui dérive de [`VisualElement`](xref:Xamarin.Forms.Application)

Un programme de Xamarin.Forms contienne uniquement une classe qui dérive de `Application` mais utilise souvent de nombreuses classes qui dérivent de `VisualElement`, y compris les pages, des dispositions et des contrôles. Un de ces objets peut avoir son `Resources` propriété définie sur un `ResourceDictionary`. Choix de l’emplacement placer un particulier `ResourceDictionary` impacts où les ressources peuvent être utilisées :

- Ressources dans un `ResourceDictionary` qui est attaché à une vue comme `Button` ou `Label` uniquement applicable à cet objet particulier, il est donc pas très utile.
- Ressources dans un `ResourceDictionary` attaché à une mise en page telles que `StackLayout` ou `Grid` peut être appliqué à la disposition et tous les enfants de cette disposition.
- Ressources dans un `ResourceDictionary` défini sur la page de niveau peut être appliqué à la page et à tous ses enfants.
- Ressources dans un `ResourceDictionary` défini au niveau de l’application niveau peut être appliqué dans toute l’application.

Le XAML suivant montre les ressources définies dans le niveau de l’application `ResourceDictionary` dans le **App.xaml** fichier créé dans le cadre du programme Xamarin.Forms standard :

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Cela `ResourceDictionary` définit trois [ `Color` ](xref:Xamarin.Forms.Color) ressources et un [ `Style` ](xref:Xamarin.Forms.Style) ressource. Pour plus d’informations sur la `App` de classe, consultez [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

À compter de Xamarin.Forms 3.0, explicites `ResourceDictionary` balises ne sont pas requis. Le `ResourceDictionary` objet est créé automatiquement, et vous pouvez insérer les ressources directement entre le `Resources` balises d’élément de propriété :

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

Chaque ressource a une clé qui est spécifiée en utilisant le `x:Key` attribut, qui elle devient la clé de dictionnaire dans le `ResourceDictionary`. La clé est utilisée pour récupérer une ressource à partir de la `ResourceDictionary` par le [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) extension de balisage, comme illustré dans l’exemple de code XAML suivant qui affiche les ressources supplémentaires définies dans le `StackLayout`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

La première [ `Label` ](xref:Xamarin.Forms.Label) instance récupère et consomme les `LabelPageHeadingStyle` ressource définie dans le niveau de l’application `ResourceDictionary`, et de la seconde `Label` instance extraction et l’utilisation de la `LabelNormalStyle`ressources définies dans le niveau de contrôle `ResourceDictionary`. De même, le [ `Button` ](xref:Xamarin.Forms.Button) instance récupère et consomme les `NormalTextColor` ressource définie dans le niveau de l’application `ResourceDictionary`et le `MediumBoldText` ressources définies dans le niveau de contrôle `ResourceDictionary`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![ressources ResourceDictionary consommatrices](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> Ressources qui sont spécifiques à une page unique ne doit pas être inclus dans un application au niveau dictionnaire de ressources, par conséquent ressources sont alors analysées au démarrage de l’application au lieu de lorsque vous invitent à une page. Pour plus d’informations, consultez [réduire la taille de dictionnaire de ressources Application](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Remplacer les ressources

Lorsque `ResourceDictionary` partagent des ressources `x:Key` valeurs d’attribut, ressources définies inférieure dans la hiérarchie d’affichage ont priorité sur ceux définis plus haut. Par exemple, si le `PageBackgroundColor` ressource `Blue` au niveau de l’application niveau sera remplacé par un niveau de la page `PageBackgroundColor` ressource définie sur `Yellow`. De même, le niveau de la page `PageBackgroundColor` ressource sera remplacée par un niveau de contrôle `PageBackgroundColor` ressource. Cette priorité est illustrée par l’exemple de code XAML suivant :

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

La version d’origine `PageBackgroundColor` et `NormalTextColor` instances, définis au niveau de l’application, sont remplacées par les `PageBackgroundColor` et `NormalTextColor` instances définies au niveau de la page. Par conséquent, la couleur d’arrière-plan de page devient bleue et le texte dans la page devient jaune, comme illustré dans les captures d’écran suivante :

[![remplacement des ressources ResourceDictionary](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

Toutefois, notez que la barre de l’arrière-plan de la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) est toujours jaune, étant donné que le [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) propriété est définie sur la valeur de la `PageBackgroundColor` ressource définie dans l’application niveau `ResourceDictionary`.

Voici une autre façon de réfléchir à `ResourceDictionary` priorité : lorsque XAML de l’analyseur rencontre un `StaticResource`, il recherche une clé correspondante en parcourant les via l’arborescence visuelle, à l’aide de la première correspondance qu’il trouve. Si cette recherche se termine à la page et la clé n’a pas encore été trouvée, l’analyseur XAML recherche le `ResourceDictionary` attaché à la `App` objet. Si la clé est toujours introuvable, une exception est levée.

## <a name="stand-alone-resource-dictionaries"></a>Dictionnaires de ressources autonomes

Une classe dérivée de `ResourceDictionary` peuvent également être dans un fichier distinct autonome. (Plus précisément, une classe dérivée de `ResourceDictionary` nécessite généralement une _paire_ de fichiers, car les ressources sont définies dans un fichier XAML, mais un fichier code-behind avec un appel `InitializeComponent` est également nécessaire.) Le fichier résultant peut ensuite être partagé entre les applications.

Pour créer un tel fichier, ajoutez un nouveau **affichage du contenu** ou **Page de contenu** élément au projet (mais pas un **affichage du contenu** ou **Page de contenu** avec uniquement un fichier c#). Dans le fichier XAML et le fichier c#, modifiez le nom de la classe de base à partir de `ContentView` ou `ContentPage` à `ResourceDictionary`. Dans le fichier XAML, le nom de la classe de base est l’élément de niveau supérieur.

L’exemple XAML suivant montre un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) nommé `MyResourceDictionary`:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

Cela `ResourceDictionary` contient une seule ressource, qui est un objet de type `DataTemplate`.

Vous pouvez instancier `MyResourceDictionary` en le plaçant entre une paire de `Resources` élément de propriété des balises, par exemple, dans un `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Une instance de `MyResourceDictionary` est défini sur le `Resources` propriété de la `ContentPage` objet.

Toutefois, cette approche présente certaines limitations : le `Resources` propriété de la `ContentPage` fait référence à cette seule `ResourceDictionary`. Dans la plupart des cas, vous souhaitez que l’option d’y compris d’autres `ResourceDictionary` instances et peut-être autres ressources également.

Cette tâche nécessite des dictionnaires de ressources fusionnés.

## <a name="merged-resource-dictionaries"></a>Dictionnaires de ressources fusionnés

Les dictionnaires de ressources fusionnés associent un ou plusieurs objets [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) dans une autre `ResourceDictionary`.

### <a name="merge-local-resource-dictionaries"></a>Fusionner les dictionnaires de ressources locales

Une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) locale peut être fusionnée dans une autre `ResourceDictionary` en affectant à la propriété [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) le nom de fichier du fichier XAML avec les ressources :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <!-- Add more resources here -->
        <ResourceDictionary Source="MyResourceDictionary.xaml" />
        <!-- Add more resources here -->
    </ContentPage.Resources>
    ...
</ContentPage>
```

Cette syntaxe n’instancie pas la classe `MyResourceDictionary`. Au lieu de cela, il fait référence le fichier XAML. Pour cette raison, lors de la définition de la propriété [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) , le fichier code-behind (**MyResourceDictionary.Xaml.cs**) n’est pas obligatoire et l’attribut `x:Class` peut être supprimé de la balise racine du fichier **MyResourceDictionary. Xaml** . En outre, lors de la fusion de dictionnaires de ressources à l’aide de cette approche, Xamarin. Forms instancie automatiquement le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), par conséquent, les balises de `ResourceDictionary` externes ne sont pas requises.

> [!IMPORTANT]
> La propriété [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) ne peut être définie qu’à partir de XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Fusionner des dictionnaires de ressources à partir d’autres assemblys

Une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peut également être fusionnée dans une autre `ResourceDictionary` en l’ajoutant à la propriété [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) de la `ResourceDictionary`. Cette technique permet de fusionner les dictionnaires de ressources, quel que soit l’assembly dans lequel ils résident.

> [!IMPORTANT]
> La classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) définit également une propriété [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) . Toutefois, cette propriété est dépréciée et ne doit plus être utilisée.

L’exemple de code suivant montre `MyResourceDictionary` ajoutée à la collection [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) d’un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)au niveau de la page :

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Cet exemple montre une instance de `MyResourceDictionary`, qui réside dans le même assembly et qui est ajoutée au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). En outre, vous pouvez également ajouter des dictionnaires de ressources à partir d’autres assemblys, d’autres `ResourceDictionary` objets au sein des balises d’éléments de propriété [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) et d’autres ressources en dehors de ces balises :

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo"
             xmlns:theme="clr-namespace:MyThemes;assembly=MyThemes">
    <ContentPage.Resources>
        <ResourceDictionary>
            <!-- Add more resources here -->
            <ResourceDictionary.MergedDictionaries>
                <!-- Add more resource dictionaries here -->
                <local:MyResourceDictionary />
                <theme:LightTheme />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Add more resources here -->
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

> [!IMPORTANT]
> Il ne peut y avoir qu’une seule balise `MergedDictionaries` élément de propriété dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), mais vous pouvez y placer autant d’objets `ResourceDictionary` que vous le souhaitez.

Lors de la fusion [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) partagent des ressources identiques `x:Key` valeurs d’attribut, Xamarin.Forms utilise la priorité de ressource suivant :

1. Les ressources locales pour le dictionnaire de ressources.
1. Les ressources contenues dans les dictionnaires de ressources qui ont été fusionnées via le `MergedDictionaries` collection, dans l’ordre inverse, ils sont répertoriés dans le `MergedDictionaries` propriété.

> [!NOTE]
> Recherche les dictionnaires de ressources peut être une tâche intensif si une application contient plusieurs dictionnaires de ressources de grande taille. Par conséquent, pour éviter des recherches superflues, vous devez vous assurer que chaque page dans une application utilise uniquement les dictionnaires de ressources qui sont appropriées à la page.

## <a name="related-links"></a>Liens connexes

- [Dictionnaires de ressources (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
