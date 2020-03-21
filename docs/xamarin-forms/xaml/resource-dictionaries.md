---
title: Dictionnaires de ressources
description: Les ressources XAML sont des objets qui peuvent être partagés et réutilisés tout au long d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2020
ms.custom: video
ms.openlocfilehash: 496251ec9596b9ef76fb34149acca184b5934c37
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070396"
---
# <a name="resource-dictionaries"></a>Dictionnaires de ressources

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Les ressources XAML sont des définitions d’objets qui peuvent être partagés et réutilisés dans une application Xamarin. Forms. Ces objets de ressource sont stockés dans un dictionnaire de ressources._

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) est un référentiel pour les ressources utilisées par une application Xamarin. Forms. Les ressources typiques stockées dans un `ResourceDictionary` incluent les [styles](~/xamarin-forms/user-interface/styles/index.md), les [modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md), les modèles de [données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), les couleurs et les convertisseurs.

En XAML, les ressources stockées dans un `ResourceDictionary` peuvent ensuite être récupérées et appliquées aux éléments à l’aide de l’extension de balisage `StaticResource`. Dans C#, les ressources peuvent également être définies dans une `ResourceDictionary`, puis récupérées et appliquées à des éléments à l’aide d’un indexeur basé sur une chaîne. Toutefois, il y a peu d’avantages à l’utilisation C#d’un `ResourceDictionary` dans, car les objets partagés peuvent simplement être stockés en tant que champs ou propriétés, et être accessibles directement sans avoir à les récupérer d’abord à partir d’un dictionnaire.

## <a name="create-and-consume-a-resourcedictionary"></a>Créer et consommer un ResourceDictionary

Les ressources sont définies dans une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui est ensuite définie sur l’une des propriétés de `Resources` suivantes :

- La propriété [`Resources`](xref:Xamarin.Forms.Application.Resources) de toute classe qui dérive de [`Application`](xref:Xamarin.Forms.Application)
- La propriété [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de toute classe qui dérive de [`VisualElement`](xref:Xamarin.Forms.Application)

Un programme Xamarin. Forms contient une seule classe qui dérive de `Application` mais utilise souvent de nombreuses classes qui dérivent de `VisualElement`, y compris les pages, les dispositions et les contrôles. La propriété `Resources` de l’un de ces objets peut avoir la valeur d’une `ResourceDictionary`. Le choix de l’emplacement d’un `ResourceDictionary` particulier qui a un impact sur l’utilisation des ressources :

- Les ressources d’un `ResourceDictionary` attaché à une vue comme `Button` ou `Label` peuvent être appliquées uniquement à cet objet particulier, ce qui n’est donc pas très utile.
- Les ressources d’un `ResourceDictionary` attaché à une disposition telle que `StackLayout` ou `Grid` peuvent être appliquées à la disposition et à tous les enfants de cette disposition.
- Les ressources d’un `ResourceDictionary` définies au niveau de la page peuvent être appliquées à la page et à tous ses enfants.
- Les ressources d’un `ResourceDictionary` définies au niveau de l’application peuvent être appliquées dans l’ensemble de l’application.

Le code XAML suivant montre les ressources définies au niveau de l’application `ResourceDictionary` dans le fichier **app. Xaml** créé dans le cadre du programme Xamarin. Forms standard :

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

Ce `ResourceDictionary` définit trois ressources [`Color`](xref:Xamarin.Forms.Color) et une ressource [`Style`](xref:Xamarin.Forms.Style) . Pour plus d’informations sur la classe `App`, consultez [App, classe](~/xamarin-forms/app-fundamentals/application-class.md).

À compter de Xamarin. Forms 3,0, les balises de `ResourceDictionary` explicites ne sont pas requises. L’objet `ResourceDictionary` est créé automatiquement et vous pouvez insérer les ressources directement entre les balises d’éléments de propriété `Resources` :

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

Chaque ressource a une clé spécifiée à l’aide de l’attribut `x:Key`, qui devient la clé de dictionnaire dans le `ResourceDictionary`. La clé est utilisée pour récupérer une ressource du `ResourceDictionary` par l’extension de balisage [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) , comme illustré dans l’exemple de code XAML suivant qui montre des ressources supplémentaires définies dans le `StackLayout`:

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

La première instance de [`Label`](xref:Xamarin.Forms.Label) récupère et consomme la ressource `LabelPageHeadingStyle` définie au niveau de l’application `ResourceDictionary`, avec la deuxième `Label` instance qui récupère et consomme la ressource `LabelNormalStyle` définie au niveau du contrôle `ResourceDictionary`. De même, l’instance [`Button`](xref:Xamarin.Forms.Button) récupère et consomme la ressource `NormalTextColor` définie au niveau de l’application `ResourceDictionary`, ainsi que la ressource `MediumBoldText` définie au niveau du contrôle `ResourceDictionary`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![ressources ResourceDictionary consommatrices](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> Ressources qui sont spécifiques à une page unique ne doit pas être inclus dans un application au niveau dictionnaire de ressources, par conséquent ressources sont alors analysées au démarrage de l’application au lieu de lorsque vous invitent à une page. Pour plus d’informations, consultez [réduire la taille du dictionnaire de ressources d’application](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Remplacer les ressources

Lorsque `ResourceDictionary` ressources partagent `x:Key` valeurs d’attribut, les ressources définies plus bas dans la hiérarchie de la vue ont priorité sur celles définies plus haut. Par exemple, la définition de la ressource `PageBackgroundColor` pour `Blue` au niveau de l’application est remplacée par un niveau page `PageBackgroundColor` ressource définie sur `Yellow`. De même, une ressource de niveau page `PageBackgroundColor` est remplacée par un niveau de contrôle `PageBackgroundColor` ressource. Cette priorité est illustrée par l’exemple de code XAML suivant :

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

Les instances d’origine `PageBackgroundColor` et `NormalTextColor`, définies au niveau de l’application, sont remplacées par les instances `PageBackgroundColor` et `NormalTextColor` définies au niveau de la page. Par conséquent, la couleur d’arrière-plan de page devient bleue et le texte dans la page devient jaune, comme illustré dans les captures d’écran suivante :

[![remplacement des ressources ResourceDictionary](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

Toutefois, Notez que la barre d’arrière-plan de la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) est toujours jaune, car la propriété [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) est définie sur la valeur de la ressource `PageBackgroundColor` définie au niveau de l’application `ResourceDictionary`.

Voici une autre façon de penser `ResourceDictionary` priorité : lorsque l’analyseur XAML rencontre un `StaticResource`, il recherche une clé correspondante en parcourant l’arborescence d’éléments visuels, à l’aide de la première correspondance trouvée. Si cette recherche se termine à la page et que la clé n’a toujours pas été trouvée, l’analyseur XAML recherche le `ResourceDictionary` attaché à l’objet `App`. Si la clé est toujours introuvable, une exception est levée.

## <a name="stand-alone-resource-dictionaries"></a>Dictionnaires de ressources autonomes

Une classe dérivée de `ResourceDictionary` peut également se trouver dans un fichier autonome séparé. Le fichier résultant peut ensuite être partagé entre les applications.

Pour créer un fichier de ce type, ajoutez un nouvel **affichage de contenu** ou un élément de **page de contenu** au projet (mais pas un affichage de **contenu** ou une **page de contenu** avec un C# seul fichier). Dans le fichier et C# le fichier XAML, remplacez le nom de la classe de base `ContentView` ou `ContentPage` par `ResourceDictionary`. Dans le fichier XAML, le nom de la classe de base est l’élément de niveau supérieur.

L’exemple de code XAML suivant illustre un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) nommé `MyResourceDictionary`:

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

Ce `ResourceDictionary` contient une ressource unique, qui est un objet de type `DataTemplate`.

Vous pouvez instancier `MyResourceDictionary` en le plaçant entre une paire de balises d’éléments de propriété `Resources`, par exemple, dans une `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Une instance de `MyResourceDictionary` est définie sur la propriété `Resources` de l’objet `ContentPage`.

Toutefois, cette approche présente certaines limitations : la propriété `Resources` du `ContentPage` référence uniquement celle-ci `ResourceDictionary`. Dans la plupart des cas, vous souhaiterez peut-être également inclure d’autres instances de `ResourceDictionary` et éventuellement d’autres ressources.

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

Cette syntaxe n’instancie pas la classe `MyResourceDictionary`. Au lieu de cela, il fait référence le fichier XAML. Pour cette raison, lors de la définition de la propriété [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) , un fichier code-behind n’est pas obligatoire et l’attribut `x:Class` peut être supprimé de la balise racine du fichier **MyResourceDictionary. Xaml** . En outre, lors de la fusion de dictionnaires de ressources à l’aide de cette approche, Xamarin. Forms instancie automatiquement le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), par conséquent, les balises de `ResourceDictionary` externes ne sont pas requises.

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

Lorsque vous placez un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) dans un assembly externe, assurez-vous que son action de génération est définie sur **EmbeddedResource**. En outre, assurez-vous qu’il possède un fichier code-behind.

> [!IMPORTANT]
> Il ne peut y avoir qu’une seule balise `MergedDictionaries` élément de propriété dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), mais vous pouvez y placer autant d’objets `ResourceDictionary` que vous le souhaitez.

Quand les ressources de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) fusionnées partagent des valeurs d’attribut `x:Key` identiques, Xamarin. Forms utilise la priorité de ressource suivante :

1. Les ressources locales pour le dictionnaire de ressources.
1. Les ressources contenues dans les dictionnaires de ressources qui ont été fusionnés via la collection `MergedDictionaries`, dans l’ordre inverse, sont répertoriées dans la propriété `MergedDictionaries`.

> [!NOTE]
> Recherche les dictionnaires de ressources peut être une tâche intensif si une application contient plusieurs dictionnaires de ressources de grande taille. Par conséquent, pour éviter des recherches superflues, vous devez vous assurer que chaque page dans une application utilise uniquement les dictionnaires de ressources qui sont appropriées à la page.

## <a name="related-links"></a>Liens connexes

- [Dictionnaires de ressources (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
