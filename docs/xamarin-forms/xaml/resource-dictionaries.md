---
title: Xamarin.Formsdictionnaires de ressources
description: Xamarin.FormsLes ressources XAML sont des objets qui peuvent être partagés et réutilisés dans une Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: a1c7cfd4a0f3549b11ac51dc13b40da552f6b758
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139410"
---
# <a name="xamarinforms-resource-dictionaries"></a>Xamarin.Formsdictionnaires de ressources

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) est un référentiel pour les ressources utilisées par une Xamarin.Forms application. Les ressources typiques stockées dans un `ResourceDictionary` incluent des [styles](~/xamarin-forms/user-interface/styles/index.md), des [modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md), des modèles de [données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), des couleurs et des convertisseurs.

En XAML, les ressources stockées dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peuvent être référencées et appliquées à des éléments à l’aide de l' `StaticResource` extension de `DynamicResource` balisage ou. En C#, les ressources peuvent également être définies dans un `ResourceDictionary` , puis référencées et appliquées à des éléments à l’aide d’un indexeur basé sur une chaîne. Toutefois, il y a peu d’avantages à utiliser un `ResourceDictionary` en C#, car les objets partagés peuvent être stockés en tant que champs ou propriétés, et être accessibles directement sans avoir à les récupérer d’abord dans un dictionnaire.

## <a name="create-resources-in-xaml"></a>Créer des ressources en XAML

Chaque [`VisualElement`](xref:Xamarin.Forms.VisualElement) objet dérivé a une [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propriété, qui [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peut contenir des ressources. De même, un [`Application`](xref:Xamarin.Forms.Application) objet dérivé a une [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propriété, qui [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peut contenir des ressources.

Une Xamarin.Forms application contient uniquement une classe qui dérive de [`Application`](xref:Xamarin.Forms.Application) , mais utilise souvent de nombreuses classes qui dérivent de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , y compris les pages, les dispositions et les contrôles. La propriété de l’un de ces objets peut être `Resources` définie sur un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui contient des ressources. Choix de l’emplacement où `ResourceDictionary` les ressources peuvent être utilisées :

- Les ressources d’un `ResourceDictionary` attaché à une vue telle que [`Button`](xref:Xamarin.Forms.Button) ou [`Label`](xref:Xamarin.Forms.Label) peuvent être appliquées uniquement à cet objet particulier.
- Les ressources d’un `ResourceDictionary` attaché à une disposition telle que [`StackLayout`](xref:Xamarin.Forms.StackLayout) ou [`Grid`](xref:Xamarin.Forms.Grid) peuvent être appliquées à la disposition et à tous les enfants de cette disposition.
- Les ressources d’un `ResourceDictionary` défini au niveau de la page peuvent être appliquées à la page et à tous ses enfants.
- Les ressources d’un `ResourceDictionary` défini au niveau de l’application peuvent être appliquées dans l’ensemble de l’application.

À l’exception des styles implicites, chaque ressource dans le dictionnaire de ressources doit avoir une clé de chaîne unique définie avec l' `x:Key` attribut.

Le code XAML suivant montre les ressources définies au niveau de `ResourceDictionary` l’application dans le fichier **app. Xaml** :

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

Dans cet exemple, le dictionnaire de ressources définit une [`Thickness`](xref:Xamarin.Forms.Thickness) ressource, plusieurs [`Color`](xref:Xamarin.Forms.Color) ressources et deux ressources implicites [`Style`](xref:Xamarin.Forms.Style) . Pour plus d’informations sur la `App` classe, consultez [ Xamarin.Forms App, classe](~/xamarin-forms/app-fundamentals/application-class.md).

> [!NOTE]
> Il est également valide de placer toutes les ressources entre les `ResourceDictionary` balises explicites. Toutefois, étant donné que Xamarin.Forms 3,0, les `ResourceDictionary` balises ne sont pas requises. Au lieu de cela, l' `ResourceDictionary` objet est créé automatiquement et vous pouvez insérer les ressources directement entre les `Resources` balises d’éléments de propriété.

## <a name="consume-resources-in-xaml"></a>Consommer des ressources en XAML

Chaque ressource a une clé spécifiée à l’aide de l' `x:Key` attribut, qui devient sa clé de dictionnaire dans le `ResourceDictionary` . La clé est utilisée pour référencer une ressource du [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) avec l' [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) extension de balisage ou.

L' `StaticResource` extension de balisage est semblable à l' `DynamicResource` extension de balisage dans qui utilise toutes deux une clé de dictionnaire pour référencer une valeur d’un dictionnaire de ressources. Toutefois, si l' `StaticResource` extension de balisage effectue une recherche dans un dictionnaire unique, l' `DynamicResource` extension de balisage maintient un lien vers la clé du dictionnaire. Par conséquent, si l’entrée de dictionnaire associée à la clé est remplacée, la modification est appliquée à l’élément visuel. Cela permet d’apporter des modifications aux ressources du runtime dans une application. Pour plus d’informations sur les extensions de balisage, consultez [extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

L’exemple de code XAML suivant montre comment consommer des ressources et définit également des ressources supplémentaires dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

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

Dans cet exemple, l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet consomme le style implicite défini dans le dictionnaire de ressources au niveau de l’application. L' [`StackLayout`](xref:Xamarin.Forms.StackLayout) objet consomme la `PageMargin` ressource définie dans le dictionnaire de ressources au niveau de l’application, tandis que l' [`Button`](xref:Xamarin.Forms.Button) objet consomme le style implicite défini dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) dictionnaire de ressources. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Consommation de ressources ResourceDictionary](resource-dictionaries-images/consuming.png "Consommation de ressources ResourceDictionary")](resource-dictionaries-images/consuming-large.png#lightbox "Consommation de ressources ResourceDictionary")

> [!IMPORTANT]
> Les ressources spécifiques à une seule page ne doivent pas être incluses dans un dictionnaire de ressources au niveau de l’application, car ces ressources seront ensuite analysées au démarrage de l’application plutôt qu’en cas de demande par une page. Pour plus d’informations, consultez [réduire la taille du dictionnaire de ressources d’application](~/xamarin-forms/deploy-test/performance.md).

## <a name="resource-lookup-behavior"></a>Comportement de la recherche de ressources

Le processus de recherche suivant se produit lorsqu’une ressource est référencée avec l' [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) extension de balisage ou :

- La clé demandée est vérifiée dans le dictionnaire de ressources, le cas échéant, pour l’élément qui définit la propriété. Si la clé demandée est trouvée, sa valeur est retournée et le processus de recherche se termine.
- Si aucune correspondance n’est trouvée, le processus de recherche recherche dans l’arborescence d’éléments visuels vers le haut, en vérifiant le dictionnaire de ressources de chaque élément parent. Si la clé demandée est trouvée, sa valeur est retournée et le processus de recherche se termine. Dans le cas contraire, le processus continue jusqu’à ce que l’élément racine soit atteint.
- Si aucune correspondance n’est trouvée au niveau de l’élément racine, le dictionnaire de ressources de niveau application est examiné.
- Si aucune correspondance n’est trouvée, une `XamlParseException` exception est levée.

Par conséquent, lorsque l’analyseur XAML rencontre une `StaticResource` extension de `DynamicResource` balisage ou, il recherche une clé correspondante en parcourant l’arborescence d’éléments visuels, à l’aide de la première correspondance trouvée. Si cette recherche se termine à la page et que la clé n’a toujours pas été trouvée, l’analyseur XAML recherche l' `ResourceDictionary` objet attaché à l' `App` objet. Si la clé est toujours introuvable, une exception est levée.

## <a name="override-resources"></a>Remplacer les ressources

Lorsque les ressources partagent des clés, les ressources définies plus bas dans l’arborescence d’éléments visuels ont priorité sur celles définies plus haut. Par exemple, la définition d’une `AppBackgroundColor` ressource au `AliceBlue` niveau de l’application est remplacée par une ressource au niveau de la page `AppBackgroundColor` définie sur `Teal` . De même, une ressource au niveau de la page `AppBackgroundColor` est remplacée par une ressource de niveau contrôle `AppBackgroundColor` .

## <a name="stand-alone-resource-dictionaries"></a>Dictionnaires de ressources autonomes

Une classe dérivée de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peut également se trouver dans un fichier autonome distinct. Le fichier résultant peut ensuite être partagé entre les applications.

Pour créer un fichier de ce type, ajoutez un nouvel **affichage de contenu** ou un élément de **page de contenu** au projet (mais pas un affichage de **contenu** ou une **page de contenu** avec un seul fichier C#). Supprimez le fichier code-behind, et dans le fichier XAML, remplacez le nom de la classe de base [`ContentView`](xref:Xamarin.Forms.ContentView) ou par [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . En outre, supprimez l' `x:Class` attribut de la balise racine du fichier.

L’exemple de code XAML suivant montre un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) nommé **MyResourceDictionary. Xaml**:

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

Dans cet exemple, le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contient une ressource unique, qui est un objet de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . **MyResourceDictionary. Xaml** peut être consommé en le fusionnant dans un autre dictionnaire de ressources.

## <a name="merged-resource-dictionaries"></a>Dictionnaires de ressources fusionnés

Les dictionnaires de ressources fusionnés combinent un ou plusieurs [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) objets dans un autre `ResourceDictionary` .

### <a name="merge-local-resource-dictionaries"></a>Fusionner les dictionnaires de ressources locales

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) fichier local peut être fusionné dans un autre `ResourceDictionary` en créant un `ResourceDictionary` objet dont la [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriété a pour valeur le nom de fichier du fichier XAML avec les ressources :

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

Cette syntaxe n’instancie pas la `MyResourceDictionary` classe. Au lieu de cela, il fait référence au fichier XAML. Pour cette raison, lors de la définition de la [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriété, un fichier code-behind n’est pas obligatoire et l' `x:Class` attribut peut être supprimé de la balise racine du fichier **MyResourceDictionary. Xaml** .

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriété ne peut être définie qu’à partir de XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Fusionner des dictionnaires de ressources à partir d’autres assemblys

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peut également être fusionné dans un autre `ResourceDictionary` en l’ajoutant à la [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) propriété de `ResourceDictionary` . Cette technique permet de fusionner les dictionnaires de ressources, quel que soit l’assembly dans lequel ils résident. La fusion de dictionnaires de ressources à partir d’assemblys externes requiert que la valeur de l' [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) action de génération soit définie sur **EmbeddedResource**, sur un fichier code-behind et sur la définition de l' `x:Class` attribut dans la balise racine du fichier.

> [!WARNING]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe définit également une [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) propriété. Toutefois, cette propriété est dépréciée et ne doit plus être utilisée.

L’exemple de code suivant illustre l’ajout de deux dictionnaires de ressources à la [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) collection d’un niveau de page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

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

Dans cet exemple, un dictionnaire de ressources du même assembly et un dictionnaire de ressources d’un assembly externe sont fusionnés dans le dictionnaire de ressources de niveau page. En outre, vous pouvez également ajouter d’autres `ResourceDictionary` objets dans les [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) balises d’éléments de propriété et d’autres ressources en dehors de ces balises.

> [!IMPORTANT]
> Il ne peut y avoir qu’une seule `MergedDictionaries` balise d’élément de propriété dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , mais vous pouvez y placer autant `ResourceDictionary` d’objets que nécessaire.

Lorsque [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) les ressources fusionnées partagent des valeurs d' `x:Key` attribut identiques, Xamarin.Forms utilise la priorité de ressource suivante :

1. Ressources locales du dictionnaire de ressources.
1. Les ressources contenues dans les dictionnaires de ressources qui ont été fusionnés par le biais de la `MergedDictionaries` collection, dans l’ordre inverse, sont répertoriées dans la `MergedDictionaries` propriété.

> [!NOTE]
> La recherche de dictionnaires de ressources peut être une tâche nécessitant beaucoup de ressources de calcul si une application contient plusieurs dictionnaires de ressources de grande taille. Par conséquent, pour éviter toute recherche inutile, vous devez vous assurer que chaque page d’une application utilise uniquement des dictionnaires de ressources qui sont appropriés à la page.

## <a name="related-links"></a>Liens connexes

- [Dictionnaires de ressources (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsIls](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
