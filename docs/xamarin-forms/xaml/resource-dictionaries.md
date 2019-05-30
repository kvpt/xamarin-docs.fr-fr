---
title: Dictionnaires de ressources
description: Les ressources XAML sont des objets qui peuvent être partagés et réutilisés tout au long d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: ba0a76b44f5942db5528e9f5d11a1d2c3f027c40
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354017"
---
# <a name="resource-dictionaries"></a>Dictionnaires de ressources

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/XAML/ResourceDictionaries/)

_Les ressources XAML sont des définitions d’objets qui peuvent être partagés et réutilisés tout au long d’une application Xamarin.Forms._

Ces objets de ressource sont stockés dans un dictionnaire de ressources. Cet article décrit comment créer et utiliser un dictionnaire de ressources et comment fusionner les dictionnaires de ressources.

## <a name="overview"></a>Vue d'ensemble

Un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) est un référentiel pour les ressources qui sont utilisés par une application Xamarin.Forms. Les ressources classiques qui sont stockées dans un `ResourceDictionary` incluent [styles](~/xamarin-forms/user-interface/styles/index.md), [modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), couleurs et les convertisseurs.

Dans XAML, les ressources qui sont stockées dans un `ResourceDictionary` peuvent être récupérées et appliqués aux éléments à l’aide de la `StaticResource` extension de balisage. En c#, les ressources peuvent également être définies dans un `ResourceDictionary` puis récupérés et appliqués aux éléments à l’aide d’un indexeur de chaîne. Toutefois, il existe peu d’avantages à l’aide un `ResourceDictionary` en c#, que les objets partagés peuvent simplement être stockées en tant que champs ou propriétés et accessible directement sans avoir d’abord récupérer à partir d’un dictionnaire.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Création et utilisation d’un ResourceDictionary

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

La première [ `Label` ](xref:Xamarin.Forms.Label) instance récupère et consomme les `LabelPageHeadingStyle` ressource définie dans le niveau de l’application `ResourceDictionary`, et de la seconde `Label` instance extraction et l’utilisation de la `LabelNormalStyle`ressources définies dans le niveau de contrôle `ResourceDictionary`. De même, le [ `Button` ](xref:Xamarin.Forms.Button) instance récupère et consomme les `NormalTextColor` ressource définie dans le niveau de l’application `ResourceDictionary`et le `MediumBoldText` ressources définies dans le niveau de contrôle `ResourceDictionary`. Cela provoque l’affichage indiqué dans les captures d’écran suivante :

[![](resource-dictionaries-images/screenshots-sml.png "Consommation des ressources de ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "consomme des ressources de ResourceDictionary")

> [!NOTE]
> Ressources qui sont spécifiques à une page unique ne doit pas être inclus dans un application au niveau dictionnaire de ressources, par conséquent ressources sont alors analysées au démarrage de l’application au lieu de lorsque vous invitent à une page. Pour plus d’informations, consultez [réduire la taille de dictionnaire de ressources Application](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Substitution de ressources

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

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Substitution de ressources de ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "substitution de ressources de ResourceDictionary")

Toutefois, notez que la barre de l’arrière-plan de la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) est toujours jaune, étant donné que le [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) propriété est définie sur la valeur de la `PageBackgroundColor` ressource définie dans l’application niveau `ResourceDictionary`.

Voici une autre façon de réfléchir à `ResourceDictionary` priorité : Lorsque l’analyseur XAML rencontre un `StaticResource`, il recherche une clé correspondante en parcourant les via l’arborescence visuelle, à l’aide de la première correspondance qu’il trouve. Si cette recherche se termine à la page et la clé n’a pas encore été trouvée, l’analyseur XAML recherche le `ResourceDictionary` attaché à la `App` objet. Si la clé est toujours introuvable, une exception est levée.

## <a name="stand-alone-resource-dictionaries"></a>Dictionnaires de ressources autonomes

Une classe dérivée de `ResourceDictionary` peuvent également être dans un fichier distinct autonome. (Plus précisément, une classe dérivée de `ResourceDictionary` nécessite généralement un _paire_ de fichiers, car les ressources sont définies dans un fichier XAML, mais un fichier code-behind avec un `InitializeComponent` appel est également nécessaire.) Le fichier résultant peut ensuite être partagé entre les applications.

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

Toutefois, cette approche présente certaines limitations : Le `Resources` propriété de la `ContentPage` fait référence à cette seule `ResourceDictionary`. Dans la plupart des cas, vous souhaitez que l’option d’y compris d’autres `ResourceDictionary` instances et peut-être autres ressources également.

Cette tâche nécessite des dictionnaires de ressources fusionnés.

## <a name="merged-resource-dictionaries"></a>Dictionnaires de ressources fusionnés

Dictionnaires de ressources fusionnés combinent un ou plusieurs `ResourceDictionary` instances dans un autre `ResourceDictionary`. Cela dans un fichier XAML en définissant le [ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) propriété à un ou plusieurs dictionnaires de ressources qui va être fusionnée dans l’application, une page ou un niveau de contrôle `ResourceDictionary`.

> [!IMPORTANT]
> `ResourceDictionary` définit également un [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) propriété. N’utilisez pas cette propriété. Il a été déconseillée à partir de Xamarin.Forms 3.0.

Une instance de `MyResourceDictionary` peuvent être fusionnés dans n’importe quel application, une page ou un niveau de contrôle `ResourceDictionary`. L’exemple de code XAML suivant montre qu’il est en cours de fusion dans le niveau de la page `ResourceDictionary` à l’aide de la `MergedDictionaries` propriété :

```xaml
<ContentPage ...>
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

Ce balisage n'indique qu’une instance de `MyResourceDictionary` ajouté à la `ResourceDictionary` , mais vous pouvez également référencer l’autre `ResourceDictionary` instances dans le `MergedDictionary` balises d’élément de propriété et d’autres ressources en dehors de ces balises :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary.MergedDictionaries>

                <!-- Add more resource dictionaries here -->

                <local:MyResourceDictionary />

                <!-- Add more resource dictionaries here -->

            </ResourceDictionary.MergedDictionaries>

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Il peut y avoir qu’un seul `MergedDictionaries` section dans un `ResourceDictionary`, mais vous pouvez placer autant `ResourceDictionary` instances ici que vous le souhaitez.

Lors de la fusion [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) partagent des ressources identiques `x:Key` valeurs d’attribut, Xamarin.Forms utilise la priorité de ressource suivant :

1. Les ressources locales pour le dictionnaire de ressources.
1. Les ressources contenues dans le dictionnaire de ressources qui a été fusionné via déconseillées [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) propriété.
1. Les ressources contenues dans les dictionnaires de ressources qui ont été fusionnées via le `MergedDictionaries` collection, dans l’ordre inverse, ils sont répertoriés dans le `MergedDictionaries` propriété.

> [!NOTE]
> Recherche les dictionnaires de ressources peut être une tâche intensif si une application contient plusieurs dictionnaires de ressources de grande taille. Par conséquent, pour éviter des recherches superflues, vous devez vous assurer que chaque page dans une application utilise uniquement les dictionnaires de ressources qui sont appropriées à la page.

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Fusion des dictionnaires dans Xamarin.Forms 3.0

À partir de Xamarin.Forms 3.0, le processus de fusion [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) instances est devenu plus facile et plus flexible. Le `MergedDictionaries` balises d’élément de propriété ne sont plus nécessaires. Au lieu de cela, vous ajoutez au dictionnaire de ressources un autre `ResourceDictionary` balise avec le nouveau [ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source) propriété définie sur le nom de fichier du fichier XAML avec les ressources :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary Source="MyResourceDictionary.xaml" />

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Étant donné que Xamarin.Forms 3.0 instancie automatiquement le `ResourceDictionary`, ces deux externe `ResourceDictionary` balises ne sont plus nécessaires :

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

Cette nouvelle syntaxe est _pas_ instancier la `MyResourceDictionary` classe. Au lieu de cela, il fait référence le fichier XAML. C’est pourquoi le fichier code-behind (**MyResourceDictionary.xaml.cs**) n’est plus nécessaire. Vous pouvez également supprimer la `x:Class` attribut à partir de la balise racine de la **MyResourceDictionary.xaml** fichier.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment créer et consommer un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)et comment fusionner les dictionnaires de ressources. Un `ResourceDictionary` permet aux ressources définies dans un emplacement unique et réutilisées tout au long d’une application Xamarin.Forms.

## <a name="related-links"></a>Liens associés

- [Dictionnaires de ressources (exemple)](https://developer.xamarin.com/samples/xamarin-forms/XAML/ResourceDictionaries/)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]

