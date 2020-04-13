---
title: Xamarin.Forms dictionnaires de ressources
description: Les ressources Xamarin.Forms XAML sont des objets qui peuvent être partagés et réutilisés dans le cadre d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.custom: video
ms.openlocfilehash: 8dd3c7f36ddd436a812927816a1326dbb7c48341
ms.sourcegitcommit: ee9e48e2ec643915f42a6c1641077970ae20cb17
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523218"
---
# <a name="xamarinforms-resource-dictionaries"></a>Xamarin.Forms dictionnaires de ressources

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) est un référentiel pour les ressources qui sont utilisées par une application Xamarin.Forms. Les ressources typiques `ResourceDictionary` qui sont stockées dans un comprennent les [styles,](~/xamarin-forms/user-interface/styles/index.md) [les modèles de contrôle,](~/xamarin-forms/app-fundamentals/templates/control-template.md) [les modèles de données,](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)les couleurs et les convertisseurs.

Dans XAML, les ressources [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) stockées dans un peuvent être `StaticResource` référencées et appliquées aux éléments en utilisant l’extension ou `DynamicResource` l’extension de balisage. Dans le C, les ressources `ResourceDictionary` peuvent également être définies dans un, puis référencées et appliquées aux éléments à l’aide d’un indexeur à cordes. Cependant, il y a peu `ResourceDictionary` d’avantage à utiliser un en C, car les objets partagés peuvent être stockés comme champs ou propriétés, et accessibles directement sans avoir à les récupérer d’abord à partir d’un dictionnaire.

## <a name="create-resources-in-xaml"></a>Créer des ressources dans XAML

Chaque [`VisualElement`](xref:Xamarin.Forms.VisualElement) objet dérivé [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) a une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) propriété, qui est un qui peut contenir des ressources. De même, un [`Application`](xref:Xamarin.Forms.Application) [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) objet dérivé a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) une propriété, qui est un qui peut contenir des ressources.

Une application Xamarin.Forms ne contient [`Application`](xref:Xamarin.Forms.Application)que la classe qui dérive de [`VisualElement`](xref:Xamarin.Forms.VisualElement), mais fait souvent usage de nombreuses classes qui dérivent de , y compris les pages, les mises en page et les contrôles. N’importe lequel de `Resources` ces objets [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peut avoir sa propriété réglée à une ressource contenante. Choisir où mettre `ResourceDictionary` un impact particulier où les ressources peuvent être utilisées :

- Les ressources `ResourceDictionary` d’un point de [`Button`](xref:Xamarin.Forms.Button) [`Label`](xref:Xamarin.Forms.Label) vue tel que l’objet ne peuvent être appliquées qu’à cet objet particulier.
- Les ressources `ResourceDictionary` d’une mise [`StackLayout`](xref:Xamarin.Forms.StackLayout) [`Grid`](xref:Xamarin.Forms.Grid) en page comme ou peuvent être appliquées à la mise en page et à tous les enfants de cette mise en page.
- Les ressources `ResourceDictionary` définies au niveau de la page peuvent être appliquées à la page et à tous ses enfants.
- Les ressources `ResourceDictionary` définies au niveau de l’application peuvent être appliquées tout au long de la demande.

À l’exception des styles implicites, chaque ressource dans le dictionnaire `x:Key` des ressources doit avoir une clé de chaîne unique qui est définie avec l’attribut.

Le XAML suivant affiche les `ResourceDictionary` ressources définies dans un niveau d’application dans le fichier **App.xaml** :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.App">
    <Application.Resources>

        <Thickness x:Key="PageMargin">20</Thickness>

        <!-- Colors -->
        <Color x:Key="AppBackgroundColor">AliceBlue</Color>
        <Color x:Key="NavigationBarColor">#1976D2</Color>
        <Color x:Key="NavigationBarTextColor">White</Color>
        <Color x:Key="NormalTextColor">Black</Color>

        <!-- Implicit styles -->
        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{StaticResource NavigationBarColor}" />
            <Setter Property="BarTextColor"
                    Value="{StaticResource NavigationBarTextColor}" />
        </Style>

        <Style TargetType="{x:Type ContentPage}"
               ApplyToDerivedTypes="True">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>

    </Application.Resources>
</Application>
```

Dans cet exemple, le dictionnaire [`Thickness`](xref:Xamarin.Forms.Thickness) des [`Color`](xref:Xamarin.Forms.Color) ressources définit une [`Style`](xref:Xamarin.Forms.Style) ressource, de multiples ressources et deux ressources implicites. Pour plus d’informations sur la `App` classe, voir [Xamarin.Forms App Class](~/xamarin-forms/app-fundamentals/application-class.md).

> [!NOTE]
> Il est également valable de placer `ResourceDictionary` toutes les ressources entre les balises explicites. Cependant, puisque Xamarin.Forms 3.0 les `ResourceDictionary` balises ne sont pas nécessaires. Au lieu `ResourceDictionary` de cela, l’objet est créé `Resources` automatiquement, et vous pouvez insérer les ressources directement entre les balises de propriété-élément.

## <a name="consume-resources-in-xaml"></a>Consommer des ressources dans XAML

Chaque ressource a une clé `x:Key` qui est spécifiée à `ResourceDictionary`l’aide de l’attribut, qui devient sa clé de dictionnaire dans le . La clé est utilisée pour [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) faire [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) référence [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) à une ressource de l’extension ou de l’extension de balisage.

L’extension `StaticResource` de balisage est similaire à l’extension de `DynamicResource` balisage en ce que les deux utilisent une clé de dictionnaire pour référencer une valeur d’un dictionnaire de ressources. Cependant, alors `StaticResource` que l’extension de balisage `DynamicResource` effectue une seule recherche de dictionnaire, l’extension de balisage maintient un lien vers la clé du dictionnaire. Par conséquent, si l’entrée du dictionnaire associée à la clé est remplacée, la modification est appliquée à l’élément visuel. Cela permet de modifier les ressources en temps d’exécution dans une application. Pour plus d’informations sur les extensions de balisage, voir [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md).

L’exemple XAML suivant montre comment consommer des ressources, [`StackLayout`](xref:Xamarin.Forms.StackLayout)et définit également des ressources supplémentaires dans un :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.HomePage"
             Title="Home Page">
    <StackLayout Margin="{StaticResource PageMargin}">
        <StackLayout.Resources>
            <!-- Implicit style -->
            <Style TargetType="Button">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </StackLayout.Resources>

        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries." />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) consomme le style implicite défini dans le dictionnaire des ressources de niveau d’application. L’objet [`StackLayout`](xref:Xamarin.Forms.StackLayout) consomme la `PageMargin` ressource définie dans le [`Button`](xref:Xamarin.Forms.Button) dictionnaire de ressources de [`StackLayout`](xref:Xamarin.Forms.StackLayout) niveau d’application, tandis que l’objet consomme le style implicite défini dans le dictionnaire des ressources. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Consommation de ressourcesdictionnaires](resource-dictionaries-images/consuming.png "Consommation de ressourcesdictionnelles")](resource-dictionaries-images/consuming-large.png#lightbox "Consommation de ressourcesdictionnelles")

> [!IMPORTANT]
> Les ressources spécifiques à une seule page ne devraient pas être incluses dans un dictionnaire de ressources de niveau d’application, car ces ressources seront ensuite analysées au démarrage de l’application plutôt que lorsque la page l’exige. Pour plus d’informations, voir [Réduire la taille du dictionnaire de ressources d’application](~/xamarin-forms/deploy-test/performance.md).

## <a name="resource-lookup-behavior"></a>Comportement de recherche de ressources

Le processus de recherche suivant se produit [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) lorsqu’une ressource est référencée avec l’extension ou [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) l’extension de balisage :

- La clé demandée est vérifiée dans le dictionnaire des ressources, s’il existe, pour l’élément qui définit la propriété. Si la clé demandée est trouvée, sa valeur est retournée et le processus de recherche se termine.
- Si une correspondance n’est pas trouvée, le processus de recherche recherche l’arbre visuel vers le haut, en vérifiant le dictionnaire des ressources de chaque élément parent. Si la clé demandée est trouvée, sa valeur est retournée et le processus de recherche se termine. Sinon, le processus se poursuit vers le haut jusqu’à ce que l’élément racine soit atteint.
- Si une correspondance n’est pas trouvée à l’élément racine, le dictionnaire de ressources de niveau d’application est examiné.
- Si un match n’est `XamlParseException` toujours pas trouvé, un est jeté.

Par conséquent, lorsque le parsier `StaticResource` `DynamicResource` XAML rencontre une extension ou une extension de balisage, il recherche une clé assortie en voyageant à travers l’arbre visuel, en utilisant le premier match qu’il trouve. Si cette recherche se termine à la page et que la clé n’a toujours pas été trouvée, le parsiétateur XAML recherche l’objet `ResourceDictionary` joint. `App` Si la clé n’est toujours pas trouvée, une exception est lancée.

## <a name="override-resources"></a>Remplacer les ressources

Lorsque les ressources partagent les clés, les ressources définies plus bas dans l’arbre visuel primeront sur celles définies plus haut. Par exemple, `AppBackgroundColor` la `AliceBlue` définition d’une ressource au niveau de `AppBackgroundColor` l’application sera remplacée par une ressource de niveau de page fixée à `Teal`. De même, `AppBackgroundColor` une ressource de niveau de `AppBackgroundColor` page sera remplacée par une ressource de niveau de contrôle.

## <a name="stand-alone-resource-dictionaries"></a>Dictionnaires de ressources autonomes

Une classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) dérivée peut également être dans un fichier distinct autonome. Le fichier qui en résulte peut ensuite être partagé entre les applications.

Pour créer un tel fichier, ajoutez un nouvel élément **de vision** de contenu ou de page **de contenu** au projet (mais pas une page de vision de **contenu** ou **de contenu** avec seulement un fichier C). Supprimer le fichier de code-derrière, et dans le fichier XAML changer le nom de la classe de base de [`ContentView`](xref:Xamarin.Forms.ContentView) ou [`ContentPage`](xref:Xamarin.Forms.ContentPage) de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). En outre, `x:Class` supprimez l’attribut de l’étiquette racine du fichier.

L’exemple XAML [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) suivant montre un **nommé MyResourceDictionary.xaml**:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}"
                       TextColor="{StaticResource NormalTextColor}"
                       FontAttributes="Bold" />
                <Label Grid.Column="1"
                       Text="{Binding Age}"
                       TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2"
                       Text="{Binding Location}"
                       TextColor="{StaticResource NormalTextColor}"
                       HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

Dans cet exemple, le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contient une seule ressource, qui est un objet de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). **MyResourceDictionary.xaml** peut être consommé en le fusionnant dans un autre dictionnaire de ressources.

## <a name="merged-resource-dictionaries"></a>Dictionnaires de ressources fusionnés

Les dictionnaires de ressources fusionnés combinent un ou plusieurs [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) objets dans un autre. `ResourceDictionary`

### <a name="merge-local-resource-dictionaries"></a>Fusionner les dictionnaires de ressources locaux

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) fichier local peut être `ResourceDictionary` fusionné `ResourceDictionary` en [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) un autre en créant un objet dont la propriété est réglée sur le nom de fichier du fichier XAML avec les ressources :

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

Cette syntaxe n’instantanét pas la `MyResourceDictionary` classe. Au lieu de cela, il fait référence au fichier XAML. Pour cette raison, [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) lors de la configuration de la propriété, `x:Class` un fichier de code-derrière n’est pas nécessaire, et l’attribut peut être supprimé de l’étiquette racine du fichier **MyResourceDictionary.xaml.**

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriété ne peut être définie qu’à partir de XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Fusionner les dictionnaires de ressources d’autres assemblées

A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peut également être `ResourceDictionary` fusionné dans un [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) autre `ResourceDictionary`en l’ajoutant dans la propriété de la . Cette technique permet de fusionner les dictionnaires de ressources, quel que soit l’assemblage dans lequel ils résident. La fusion des dictionnaires de ressources [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) à partir d’assemblages externes exige que l’action de construction `x:Class` soit définie pour **EmbeddedResource,** d’avoir un fichier de code-derrière, et de définir l’attribut dans l’étiquette racine du fichier.

> [!WARNING]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe définit [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) également une propriété. Cependant, cette propriété a été dépréciée et ne doit plus être utilisée.

L’exemple de code suivant montre l’ajout de deux dictionnaires de ressources à la [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) collection d’un niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de page :

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

Dans cet exemple, un dictionnaire de ressources d’une même assemblée et un dictionnaire de ressources provenant d’un assemblage externe sont fusionnés dans le dictionnaire des ressources du niveau de page. En outre, vous pouvez `ResourceDictionary` également [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) ajouter d’autres objets dans les balises de propriété-élément, et d’autres ressources en dehors de ces balises.

> [!IMPORTANT]
> Il ne peut `MergedDictionaries` y avoir qu’une seule étiquette d’élément de propriété dans un, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)mais vous pouvez mettre autant d’objets `ResourceDictionary` là-dedans que nécessaire.

Lorsque les [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ressources `x:Key` fusionnées partagent des valeurs d’attribut identiques, Xamarin.Forms utilise la priorité de ressources suivante :

1. Les ressources locales au dictionnaire des ressources.
1. Les ressources contenues dans les dictionnaires de `MergedDictionaries` ressources qui ont été fusionnés `MergedDictionaries` par l’intermédiaire de la collection, dans l’ordre inverse, elles sont répertoriées dans la propriété.

> [!NOTE]
> La recherche de dictionnaires de ressources peut être une tâche intensive en calcul si une application contient plusieurs grands dictionnaires de ressources. Par conséquent, pour éviter les recherches inutiles, vous devez vous assurer que chaque page d’une application n’utilise que des dictionnaires de ressources qui sont appropriés à la page.

## <a name="related-links"></a>Liens connexes

- [Dictionnaires de ressources (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Styles Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
