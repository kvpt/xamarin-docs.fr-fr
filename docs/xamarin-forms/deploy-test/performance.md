---
title: Améliorer les performances des applications Xamarin.Forms
description: Il existe plusieurs techniques permettant d’accroître les performances des applications Xamarin.Forms. Ensemble, ces techniques peuvent considérablement réduire la charge de travail d’un processeur, de même que la quantité de mémoire consommée par une application.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2019
ms.openlocfilehash: 0841cb0cbe97644f3bb53105887f3adadf9bf6c5
ms.sourcegitcommit: 266e75fa6893d3732e4e2c0c8e79c62be2804468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820947"
---
# <a name="improve-xamarinforms-app-performance"></a>Améliorer les performances des applications Xamarin.Forms

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolve 2016 : Optimisation des performances des applications avec Xamarin.Forms**

Le mauvais niveau de performance d’une application se présente de plusieurs façons. L’application semble ne pas répondre, le défilement de l’affichage est ralenti et la durée de vie de la batterie de l’appareil réduite. Toutefois, l’optimisation des performances implique davantage de choses que l’implémentation d’un code efficace. L’expérience utilisateur liée au niveau de performance de l’application doit également être prise en compte. Par exemple, pour contribuer à améliorer l’expérience utilisateur, vous devez vérifier que les opérations s’exécutent sans empêcher l’utilisateur d’effectuer d’autres activités.

De nombreuses techniques permettent d’accroître les performances, notamment celles qui sont visibles, d’applications Xamarin.Forms. Ensemble, ces techniques peuvent considérablement réduire la charge de travail d’un processeur, de même que la quantité de mémoire consommée par une application.

> [!NOTE]
> Avant de lire cet article, lisez d’abord [Niveau de performance multiplateforme](~/cross-platform/deploy-test/memory-perf-best-practices.md), qui décrit les techniques spécifiques indépendantes des plateformes qui permettent d’améliorer l’utilisation de la mémoire et le niveau de performance des applications générées à l’aide de la plateforme Xamarin.

## <a name="enable-the-xaml-compiler"></a>Activer le compilateur XAML

XAML peut être éventuellement compilé directement en langage intermédiaire (IL) avec le compilateur XAML (XAMLC). XAMLC offre un certain nombre d’avantages :

- Il effectue une vérification au moment de la compilation du code XAML et informe l’utilisateur des erreurs rencontrées.
- Il supprime une partie du temps de chargement et d’instanciation pour les éléments XAML.
- Il permet de réduire la taille de fichier de l’assembly final en n’incluant plus les fichiers .xaml.

XAMLC est activé par défaut dans les nouvelles solutions Xamarin.Forms. Il peut toutefois être nécessaire de l’activer dans des solutions plus anciennes. Pour plus d’informations, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Utiliser des liaisons compilées

Les liaisons compilées améliorent les performances de liaison de données dans les applications Xamarin.Forms en résolvant les expressions de liaison au moment de la compilation plutôt qu’au moment de l’exécution avec réflexion. La compilation d’une expression de liaison génère du code compilé qui résout généralement une liaison 8 à 20 fois plus rapidement qu’avec une liaison classique. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Réduire les liaisons inutiles

N’utilisez pas de liaisons pour le contenu qui peut être aisément défini de manière statique. Il n’existe aucun avantage à lier des données qui n’ont pas besoin de l’être, car les liaisons ne sont pas rentables. Par exemple, la définition de `Button.Text = "Accept"` présente moins de surcharge que la liaison de [`Button.Text`](xref:Xamarin.Forms.Button.Text) à une propriété ViewModel `string` avec la valeur « Accepter ».

## <a name="use-fast-renderers"></a>Utiliser des renderers rapides

Les renderers rapides réduisent l’inflation et les coûts de rendu des contrôles Xamarin.Forms sur Android en aplanissant la hiérarchie de contrôles natifs obtenue. Cette technique améliore davantage les performances en créant moins d’objets, ce qui aboutit à une arborescence d’éléments visuels moins complexe et à une utilisation moindre de la mémoire.

À partir de Xamarin.Forms 4.0, toutes les applications ciblant `FormsAppCompatActivity` utilisent des renderers rapides par défaut. Pour plus d’informations, consultez [Renderers rapides](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Activer le suivi de démarrage sur Android

La compilation Ahead Of Time (AOT) sur Android réduit la charge de démarrage de l’application juste-à-temps (JIT) et l’utilisation de la mémoire, au détriment de la création d’un APK de plus grande taille. Une alternative consiste à utiliser le suivi de démarrage, qui fournit un compromis entre taille d’APK Android et temps de démarrage par rapport à la compilation AOT conventionnelle.

Plutôt que de compiler la plus grande partie possible de l’application en code non managé, le suivi de démarrage compile uniquement l’ensemble des méthodes managées qui représentent les parties les plus coûteuses du démarrage de l’application dans une application Xamarin.Forms vide. Cette approche entraîne une réduction de la taille de l’APK par rapport à la compilation AOT conventionnelle, tout en fournissant des améliorations similaires au démarrage.

## <a name="enable-layout-compression"></a>Activer la compression des dispositions

La compression des dispositions supprime les dispositions spécifiées de l’arborescence d’éléments visuels dans le but d’améliorer les performances de rendu des pages. Les avantages en matière de performances de cette technique varient selon la complexité d’une page, la version du système d’exploitation utilisé et l’appareil sur lequel l’application est en cours d’exécution. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens. Pour plus d’informations, consultez [Compression des dispositions](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Choisir la disposition correcte

Une disposition qui est capable d’afficher plusieurs enfants, mais qui n’en a qu’un seul, est inutile. Par exemple, l’exemple de code suivant montre un élément [`StackLayout`](xref:Xamarin.Forms.StackLayout) avec un seul enfant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Cela est inutile et l’élément [`StackLayout`](xref:Xamarin.Forms.StackLayout) doit être supprimé, comme indiqué dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

En outre, n’essayez pas de reproduire l’apparence d’une disposition spécifique à l’aide de combinaisons d’autres dispositions, car cela aboutirait à des calculs de dispositions inutiles. Par exemple, n’essayez pas de reproduire une disposition [`Grid`](xref:Xamarin.Forms.Grid) à l’aide d’une combinaison d’instances de [`StackLayout`](xref:Xamarin.Forms.StackLayout). L’exemple de code ci-dessous illustre cette mauvaise pratique :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Cela ne sert à rien, car des calculs de dispositions inutiles sont effectués. Au lieu de cela, vous pouvez obtenir la disposition souhaitée à l’aide d’un élément [`Grid`](xref:Xamarin.Forms.Grid), comme illustré dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="100" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Text="Name:" />
        <Entry Grid.Column="1" Placeholder="Enter your name" />
        <Label Grid.Row="1" Text="Age:" />
        <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
        <Label Grid.Row="2" Text="Occupation:" />
        <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
        <Label Grid.Row="3" Text="Address:" />
        <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
    </Grid>
</ContentPage>
```

## <a name="optimize-layout-performance"></a>Optimiser les performances de disposition

Pour obtenir les meilleures performances de disposition possibles, suivez ces instructions :

- Réduisez la profondeur des hiérarchies de dispositions en spécifiant les valeurs de propriété [`Margin`](xref:Xamarin.Forms.View.Margin), ce qui permet de créer des dispositions avec moins de retours à la ligne. Pour plus d’informations, consultez [Marges et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Quand vous utilisez un élément [`Grid`](xref:Xamarin.Forms.Grid), essayez de vous assurer que le moins de lignes et de colonnes possible sont définies sur une taille [`Auto`](xref:Xamarin.Forms.GridLength.Auto). Pour chaque ligne ou colonne dimensionnée automatiquement, le moteur de disposition effectue des calculs de dispositions supplémentaires. Utilisez plutôt des lignes et colonnes de taille fixe si possible. Vous pouvez également définir des lignes et des colonnes pour occuper un montant proportionnel d’espace avec la valeur d’énumération [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star), à condition que l’arborescence parente respecte ces instructions de disposition.
- Ne définissez pas les propriétés [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) et [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) d’une disposition, sauf si cela est obligatoire. Les valeurs par défaut de [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) et [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) permettent la meilleure optimisation de la disposition. La modification de ces propriétés a un coût et consomme de la mémoire, même en leur affectant les valeurs par défaut.
- Évitez d’utiliser un élément [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) autant que possible. Le processeur aurait considérablement plus de travail à effectuer.
- Quand vous utilisez un élément [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), évitez d’utiliser la propriété [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) autant que possible.
- Quand vous utilisez un élément [`StackLayout`](xref:Xamarin.Forms.StackLayout), vérifiez qu’un seul enfant est défini sur [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Cette propriété permet de garantir que l’enfant spécifié occupe le plus grand espace que l’élément `StackLayout` peut lui donner, et il est inutile d’effectuer ces calculs plusieurs fois.
- Évitez d’appeler une des méthodes de la classe [`Layout`](xref:Xamarin.Forms.Layout), car elles entraînent l’exécution de calculs de dispositions coûteux. Au lieu de cela, il est probable que vous puissiez obtenir le comportement de disposition souhaité en définissant les propriétés [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY). Vous pouvez également sous-classer la classe [`Layout<View>`](xref:Xamarin.Forms.Layout`1) pour obtenir le comportement de disposition souhaité.
- Ne mettez à jour aucune instance de [`Label`](xref:Xamarin.Forms.Label) plus fréquemment que nécessaire, car la modification de la taille de l’étiquette peut entraîner le recalcul de l’intégralité de la disposition de l’écran.
- Ne définissez pas la propriété [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment), sauf si cela est obligatoire.
- Affectez à l’élément [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) des instances de [`Label`](xref:Xamarin.Forms.Label) la valeur [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) autant que possible.

## <a name="choose-a-dependency-injection-container-carefully"></a>Choisir un conteneur d’injection de dépendances avec précaution

Les conteneurs d’injection de dépendances introduisent des contraintes de performances supplémentaires dans les applications mobiles. L’inscription et la résolution des types avec un conteneur ont un coût en termes de performances en raison de l’utilisation par le conteneur de la réflexion pour créer chaque type, et plus particulièrement si les dépendances sont reconstruites pour chaque navigation entre les pages de l’application. S’il existe de nombreuses dépendances ou des dépendances profondes, le coût de création peut augmenter de manière significative. En outre, l’inscription de type, qui se produit généralement au démarrage de l’application, peut avoir un impact notable sur le temps de démarrage selon le conteneur utilisé.

En guise d’alternative, l’injection de dépendances peut être rendue plus performante en l’implémentant manuellement à l’aide de fabriques.

## <a name="create-shell-applications"></a>Créer des applications d’interpréteur de commandes

Les applications d’interpréteur de commandes Xamarin.Forms fournissent une expérience de navigation robuste, basée sur des menus volants et des onglets. Si l’expérience utilisateur de votre application peut être implémentée avec l’interpréteur de commandes, il est préférable de le faire. Les applications d’interpréteur de commandes permettent d’éviter une mauvaise expérience de démarrage, car les pages sont créées à la demande en réponse à la navigation plutôt qu’au démarrage de l’application, ce qui est le cas avec les applications qui utilisent un [« TabbedPage »](xref:Xamarin.Forms.TabbedPage). Pour plus d’informations, consultez [Interpréteur de commandes Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Utiliser CollectionView au lieu de ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView) est une vue de présentation de listes de données à l’aide de différentes spécifications de disposition. Il offre une alternative plus flexible et plus performante à [`ListView`](xref:Xamarin.Forms.ListView). Pour plus d’informations, consultez [CollectionView Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Optimiser les performances de ListView

Lorsque vous utilisez [`ListView`](xref:Xamarin.Forms.ListView), un certain nombre d’expériences utilisateur doit être optimisé :

- **Initialisation** : intervalle de temps qui commence quand le contrôle est créé et se termine quand les éléments sont affichés à l’écran.
- **Défilement** : possibilité de faire défiler la liste et de vérifier que l’interface utilisateur est synchrone avec les entrées tactiles.
- **Interaction** pour l’ajout, la suppression et la sélection d’éléments.

Le contrôle [`ListView`](xref:Xamarin.Forms.ListView) requiert une application pour fournir des modèles de données et de cellules. Cette opération aura un impact important sur les performances du contrôle. Pour plus d’informations, consultez [Performances de ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Optimiser les ressources d’images

L’affichage de ressources d’images peut augmenter considérablement l’encombrement mémoire d’une application. Par conséquent, elles ne doivent être créées que si elles sont nécessaires, et doivent être libérées dès que l’application n’en a plus besoin. Par exemple, si une application affiche une image en lisant les données à partir d’un flux, vérifiez que ce flux est créé uniquement si nécessaire et qu’il est libéré quand il ne l’est plus. Pour cela, créez le flux quand la page est créée ou que l’événement [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) se déclenche, puis supprimez-le quand l’événement [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) se déclenche.

Quand vous téléchargez une image à afficher avec la méthode [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)), mettez en cache l’image téléchargée en vérifiant que la propriété [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) a la valeur `true`. Pour plus d’informations, consultez [Utilisation des images](~/xamarin-forms/user-interface/images.md).

Pour plus d’informations, consultez [Optimiser les ressources d’images](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

## <a name="reduce-the-visual-tree-size"></a>Réduire la taille de l’arborescence d’éléments visuels

La réduction du nombre d’éléments dans une page accélère l’affichage de la page. Pour ce faire, il existe deux techniques principales. La première consiste à masquer les éléments qui ne sont pas visibles. La propriété [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) de chaque élément détermine si l’élément doit faire partie de l’arborescence d’éléments visuels ou pas. Par conséquent, si un élément n’est pas visible parce qu’il est masqué par d’autres éléments, supprimez l’élément ou affectez à sa propriété `IsVisible` la valeur `false`.

La seconde technique consiste à supprimer les éléments inutiles. Par exemple, l’exemple de code suivant illustre une mise en page contenant plusieurs objets [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Hello" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Welcome to the App!" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Downloading Data..." />
    </StackLayout>
</StackLayout>
```

La même mise en page peut être maintenue avec un nombre réduit d’éléments, comme indiqué dans l’exemple de code suivant :

```xaml
<StackLayout Padding="20,35,20,20" Spacing="25">
  <Label Text="Hello" />
  <Label Text="Welcome to the App!" />
  <Label Text="Downloading Data..." />
</StackLayout>
```

## <a name="reduce-the-application-resource-dictionary-size"></a>Réduire la taille du dictionnaire de ressources de l’application

Toutes les ressources qui sont utilisées dans l’application doivent être stockées dans le dictionnaire de ressources de l’application pour éviter la duplication. Cela permet de réduire la quantité de code XAML qui doit être analysé dans toute l’application. L’exemple de code suivant illustre la ressource `HeadingLabelStyle`, qui est utilisée au niveau de l’application et par conséquent définie dans le dictionnaire de ressources de l’application :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
         <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
         </ResourceDictionary>
     </Application.Resources>
</Application>
```

Toutefois, le code XAML qui est spécifique à une page ne doit pas être inclus dans le dictionnaire de ressources de l’application, car les ressources sont alors analysées au démarrage de l’application et non lorsqu’une page le demande. Si une ressource est utilisée par une page qui n’est pas la page de démarrage, elle doit être placée dans le dictionnaire de ressources pour cette page, ce qui contribue à réduire le code XAML qui est analysé quand l’application démarre. L’exemple de code suivant illustre la ressource `HeadingLabelStyle`, qui figure uniquement dans une seule page et est par conséquent définie dans le dictionnaire de ressources de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Resources>
        <ResourceDictionary>
          <Style x:Key="HeadingLabelStyle" TargetType="Label">
              <Setter Property="HorizontalOptions" Value="Center" />
              <Setter Property="FontSize" Value="Large" />
              <Setter Property="TextColor" Value="Red" />
          </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Pour plus d’informations sur les ressources d’application, consultez [Styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="use-the-custom-renderer-pattern"></a>Utiliser le modèle de renderer personnalisé

La plupart des classes de renderers Xamarin.Forms exposent la méthode `OnElementChanged`, qui est appelée lorsqu’un contrôle personnalisé Xamarin.Forms est créé pour restituer le contrôle natif correspondant. Les classes de renderers personnalisés dans chaque projet de plateforme remplacent ensuite cette méthode pour instancier et personnaliser le contrôle natif. La méthode `SetNativeControl` est utilisée pour instancier le contrôle natif, et cette méthode attribue également la référence de contrôle à la propriété `Control`.

Toutefois, dans certains cas, la méthode `OnElementChanged` peut être appelée plusieurs fois. Par conséquent, pour éviter les fuites de mémoire qui peuvent avoir un impact sur les performances, vous devez être vigilant lors de l’instanciation d’un nouveau contrôle natif. L’approche à utiliser lors de l’instanciation d’un nouveau contrôle natif dans un renderer personnalisé est indiquée dans l’exemple de code suivant :

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null)
  {
    if (Control == null)
    {
      // Instantiate the native control with the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Un nouveau contrôle natif doit uniquement être instancié une seule fois, quand la propriété `Control` a la valeur `null`. Par ailleurs, vous devez uniquement créer et configurer le contrôle et vous abonner aux gestionnaires d’événements quand le renderer personnalisé est attaché à un nouvel élément Xamarin.Forms. De même, vous devez vous désabonner des gestionnaires d’événements auxquels vous vous êtes abonné uniquement quand l’élément auquel le renderer est attaché change. L’adoption de cette approche permet de créer un renderer personnalisé efficace qui ne connaît pas les fuites de mémoire.

> [!IMPORTANT]
> La méthode `SetNativeControl` ne doit être appelée que si la propriété `e.NewElement` n’est pas `null`, et si la propriété `Control` est `null`.

Pour plus d’informations sur les renderers personnalisés, consultez [Personnalisation des contrôles sur chaque plateforme](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Liens connexes

- [Performances entre plateformes](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Compilation de XAML](~/xamarin-forms/xaml/xamlc.md)
- [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [Convertisseurs rapides](~/xamarin-forms/internals/fast-renderers.md)
- [Compression de la disposition](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Interpréteur de commandes Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [CollectionView Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md)
- [Performances de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Optimiser les ressources d’images](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personnaliser les contrôles sur chaque plateforme](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
