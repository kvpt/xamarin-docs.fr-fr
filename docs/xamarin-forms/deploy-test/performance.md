---
title: Améliorer les Xamarin.Forms performances des applications
description: Il existe de nombreuses techniques pour améliorer les performances des Xamarin.Forms applications. Utilisées conjointement, ces techniques peuvent considérablement réduire la charge de travail d’une UC, ainsi que la quantité de mémoire consommée par une application.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 96b5939fd1f8448d45d1398fd56770f9032de083
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139111"
---
# <a name="improve-xamarinforms-app-performance"></a>Améliorer les Xamarin.Forms performances des applications

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Évolution 2016 : optimisation des performances des applications avecXamarin.Forms**

Le mauvais niveau de performance d’une application se présente de plusieurs façons. L’application semble ne pas répondre, le défilement de l’affichage est ralenti et la durée de vie de la batterie de l’appareil réduite. Toutefois, l’optimisation des performances implique davantage de choses que l’implémentation d’un code efficace. L’expérience utilisateur liée au niveau de performance de l’application doit également être prise en compte. Par exemple, pour contribuer à améliorer l’expérience utilisateur, vous devez vérifier que les opérations s’exécutent sans empêcher l’utilisateur d’effectuer d’autres activités.

Il existe de nombreuses techniques pour améliorer les performances et les performances perçues des Xamarin.Forms applications. Utilisées conjointement, ces techniques peuvent considérablement réduire la charge de travail d’une UC, ainsi que la quantité de mémoire consommée par une application.

> [!NOTE]
>  Avant de lire cet article, vous devez lire [Performances entre plateformes](~/cross-platform/deploy-test/memory-perf-best-practices.md), qui décrit des techniques non spécifiques aux plateformes pour améliorer l’utilisation de la mémoire et les performances des applications générées à l’aide de la plateforme Xamarin.

## <a name="enable-the-xaml-compiler"></a>Activer le compilateur XAML

XAML peut être éventuellement compilé directement en langage intermédiaire (IL) avec le compilateur XAML (XAMLC). XAMLC offre un certain nombre d’avantages :

- Il effectue une vérification au moment de la compilation du code XAML et informe l’utilisateur des erreurs rencontrées.
- Il supprime une partie du temps de chargement et d’instanciation pour les éléments XAML.
- Il permet de réduire la taille de fichier de l’assembly final en n’incluant plus les fichiers .xaml.

XAMLC est activé par défaut dans les nouvelles Xamarin.Forms solutions. Il peut toutefois être nécessaire de l’activer dans des solutions plus anciennes. Pour plus d’informations, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Utiliser des liaisons compilées

Les liaisons compilées améliorent les performances de liaison de données dans les Xamarin.Forms applications en résolvant les expressions de liaison au moment de la compilation, plutôt qu’au moment de l’exécution avec réflexion. La compilation d’une expression de liaison génère du code compilé qui résout généralement une liaison 8 à 20 fois plus rapidement qu’avec une liaison classique. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Réduire les liaisons inutiles

N’utilisez pas de liaisons pour le contenu qui peut être aisément défini de manière statique. Il n’existe aucun avantage à lier des données qui n’ont pas besoin de l’être, car les liaisons ne sont pas rentables. Par exemple, la définition de `Button.Text = "Accept"` la charge est moins lourde que [`Button.Text`](xref:Xamarin.Forms.Button.Text) la liaison à une `string` propriété ViewModel avec la valeur « Accept ».

## <a name="use-fast-renderers"></a>Utiliser des renderers rapides

Les convertisseurs rapides réduisent les coûts d’inflation et de rendu des Xamarin.Forms contrôles sur Android en aplatint la hiérarchie des contrôles natifs résultante. Cette technique améliore davantage les performances en créant moins d’objets, ce qui aboutit à une arborescence d’éléments visuels moins complexe et à une utilisation moindre de la mémoire.

À partir de Xamarin.Forms 4,0, toutes les applications ciblant `FormsAppCompatActivity` utilisent des convertisseurs rapides par défaut. Pour plus d’informations, consultez [Renderers rapides](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Activer le suivi de démarrage sur Android

La compilation Ahead Of Time (AOT) sur Android réduit la charge de démarrage de l’application juste-à-temps (JIT) et l’utilisation de la mémoire, au détriment de la création d’un APK de plus grande taille. Une alternative consiste à utiliser le suivi de démarrage, qui fournit un compromis entre taille d’APK Android et temps de démarrage par rapport à la compilation AOT conventionnelle.

Au lieu de compiler la plus grande partie possible de l’application au code non managé, le suivi de démarrage compile uniquement l’ensemble des méthodes managées qui représentent les parties les plus coûteuses du démarrage de l’application dans une Xamarin.Forms application vide. Cette approche entraîne une réduction de la taille de l’APK par rapport à la compilation AOT conventionnelle, tout en fournissant des améliorations similaires au démarrage.

## <a name="enable-layout-compression"></a>Activer la compression des dispositions

La compression des dispositions supprime les dispositions spécifiées de l’arborescence d’éléments visuels dans le but d’améliorer les performances de rendu des pages. Les avantages en matière de performances de cette technique varient selon la complexité d’une page, la version du système d’exploitation utilisé et l’appareil sur lequel l’application est en cours d’exécution. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens. Pour plus d’informations, consultez [Compression des dispositions](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Choisir la disposition correcte

Une disposition qui est capable d’afficher plusieurs enfants, mais qui n’en a qu’un seul, est inutile. Par exemple, l’exemple de code suivant montre un [`StackLayout`](xref:Xamarin.Forms.StackLayout) avec un seul enfant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Il s’agit d’un gaspillage et l' [`StackLayout`](xref:Xamarin.Forms.StackLayout) élément doit être supprimé, comme illustré dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

En outre, n’essayez pas de reproduire l’apparence d’une disposition spécifique à l’aide de combinaisons d’autres dispositions, car cela aboutirait à des calculs de dispositions inutiles. Par exemple, n’essayez pas de reproduire une [`Grid`](xref:Xamarin.Forms.Grid) disposition à l’aide d’une combinaison d' [`StackLayout`](xref:Xamarin.Forms.StackLayout) instances. L’exemple de code ci-dessous illustre cette mauvaise pratique :

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

Cela ne sert à rien, car des calculs de dispositions inutiles sont effectués. Au lieu de cela, la disposition souhaitée peut être mieux obtenue à l’aide d’un [`Grid`](xref:Xamarin.Forms.Grid) , comme illustré dans l’exemple de code suivant :

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

- Réduisez la profondeur des hiérarchies de disposition en spécifiant des [`Margin`](xref:Xamarin.Forms.View.Margin) valeurs de propriété, ce qui permet de créer des dispositions avec moins de vues d’encapsulage. Pour plus d’informations, consultez [Marges et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Quand vous utilisez un [`Grid`](xref:Xamarin.Forms.Grid) , essayez de vous assurer que le nombre de lignes et de colonnes le plus limité possible est défini sur [`Auto`](xref:Xamarin.Forms.GridLength.Auto) Size. Pour chaque ligne ou colonne dimensionnée automatiquement, le moteur de disposition effectue des calculs de dispositions supplémentaires. Utilisez plutôt des lignes et colonnes de taille fixe si possible. Vous pouvez également définir des lignes et des colonnes pour occuper une quantité proportionnelle d’espace avec la [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valeur d’énumération, à condition que l’arborescence parente respecte ces instructions de disposition.
- Ne définissez pas [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) les [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés et d’une disposition à moins que cela ne soit requis. Les valeurs par défaut de [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) et [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) permettent une meilleure optimisation de la disposition. La modification de ces propriétés a un coût et consomme de la mémoire, même en leur affectant les valeurs par défaut.
- Évitez d’utiliser dans la mesure du [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) possible. Le processeur aurait considérablement plus de travail à effectuer.
- Quand vous utilisez un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , évitez d’utiliser la propriété dans la mesure du [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) possible.
- Quand vous utilisez un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , assurez-vous qu’un seul enfant a la valeur [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Cette propriété permet de garantir que l’enfant spécifié occupe le plus grand espace que l’élément `StackLayout` peut lui donner, et il est inutile d’effectuer ces calculs plusieurs fois.
- Évitez d’appeler l’une des méthodes de la [`Layout`](xref:Xamarin.Forms.Layout) classe, car elles entraînent l’exécution de calculs de disposition coûteux. Au lieu de cela, il est probable que le comportement de disposition souhaité puisse être obtenu en définissant les [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriétés et. Vous pouvez également sous-classer la [`Layout<View>`](xref:Xamarin.Forms.Layout`1) classe pour obtenir le comportement de disposition souhaité.
- Ne mettez pas à jour les [`Label`](xref:Xamarin.Forms.Label) instances plus fréquemment que nécessaire, car la modification de la taille de l’étiquette peut entraîner le recalcul de la disposition de l’écran entière.
- Ne définissez pas la [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) propriété, sauf si elle est requise.
- Affectez la valeur [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) de toutes les [`Label`](xref:Xamarin.Forms.Label) instances à [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) chaque fois que possible.

## <a name="use-asynchronous-programming"></a>Utiliser la programmation asynchrone

La réactivité globale de votre application peut être améliorée et les goulots d’étranglement des performances sont souvent évités, à l’aide de la programmation asynchrone. Dans .NET, le [modèle asynchrone basé sur les tâches (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) est le modèle de conception recommandé pour les opérations asynchrones. Toutefois, l’utilisation incorrecte d’un TAP peut entraîner des applications non exécutées. Par conséquent, les instructions suivantes doivent être suivies lors de l’utilisation du TAP.

### <a name="fundamentals"></a>Notions de base

- Comprendre le cycle de vie de la tâche, qui est représenté par l' `TaskStatus` énumération. Pour plus d’informations, consultez [la signification de TaskStatus et état de la](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/) [tâche](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status).
- Utilisez la `Task.WhenAll` méthode pour attendre de façon asynchrone que plusieurs opérations asynchrones se terminent, plutôt qu' `await` une série d’opérations asynchrones individuellement. Pour plus d’informations, consultez [Task. WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Utilisez la `Task.WhenAny` méthode pour attendre de façon asynchrone l’achèvement de l’une de plusieurs opérations asynchrones. Pour plus d’informations, consultez [Task. WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Utilisez la `Task.Delay` méthode pour produire un `Task` objet qui se termine après l’heure spécifiée. Cela est utile pour des scénarios tels que l’interrogation de données et le retardement de la gestion des entrées d’utilisateur pendant une durée prédéterminée. Pour plus d’informations, consultez [Task. Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- Exécutez des opérations intensives de processeur synchrones sur le pool de threads avec la `Task.Run` méthode. Cette méthode est un raccourci pour la `TaskFactory.StartNew` méthode, avec les arguments les plus optimaux définis. Pour plus d’informations, consultez [Task. Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun).
- Évitez d’essayer de créer des constructeurs asynchrones. Utilisez plutôt des événements de cycle de vie ou une logique d’initialisation distincte pour `await` une initialisation correcte. Pour plus d’informations, consultez [constructeurs Async](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html) sur blog.stephencleary.com.
- Utilisez le modèle de tâche paresseux pour éviter d’attendre que les opérations asynchrones se terminent au démarrage de l’application. Pour plus d’informations, consultez [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).
- Créez un wrapper de tâche pour les opérations asynchrones existantes, qui n’utilisent pas le TAP, en créant des `TaskCompletionSource<T>` objets. Ces objets bénéficient des avantages de la `Task` programmabilité et vous permettent de contrôler la durée de vie et l’achèvement du associé `Task` . Pour plus d’informations, consultez [la nature de TaskCompletionSource](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/).
 
- Retourne un `Task` objet, au lieu de retourner un `Task` objet attendu, lorsqu’il n’est pas nécessaire de traiter le résultat d’une opération asynchrone. Cette opération est plus performante en raison de la réduction du basculement de contexte.
- Utilisez la bibliothèque de flux de données de la bibliothèque parallèle de tâches (TPL) dans des scénarios tels que le traitement des données lorsqu’elles sont disponibles, ou lorsque vous avez plusieurs opérations qui doivent communiquer entre elles de manière asynchrone. Pour plus d’informations, consultez flux de données [(bibliothèque parallèle de tâches)](/dotnet/standard/parallel-programming/dataflow-task-parallel-library).

### <a name="ui"></a>UI

- Appelez une version asynchrone d’une API, si elle est disponible. Ainsi, le thread d’interface utilisateur reste non bloqué, ce qui améliore l’expérience utilisateur.
- Met à jour les éléments d’interface utilisateur avec des données provenant d’opérations asynchrones sur le thread d’interface utilisateur, pour éviter la levée d’exceptions. Toutefois, les mises à jour de la `ListView.ItemsSource` propriété seront automatiquement marshalées vers le thread d’interface utilisateur. Pour plus d’informations sur la façon de déterminer si le code est en cours d’exécution sur le thread d’interface utilisateur, consultez [ Xamarin.Essentials : MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms).

    > [!IMPORTANT]
    > Toutes les propriétés de contrôle mises à jour via la liaison de données sont automatiquement marshalées vers le thread d’interface utilisateur.

### <a name="error-handling"></a>Gestion des erreurs

- En savoir plus sur la gestion asynchrone des exceptions. Les exceptions non gérées levées par le code qui s’exécute de façon asynchrone sont propagées vers le thread appelant, sauf dans certains scénarios. Pour plus d’informations, consultez [gestion des exceptions (bibliothèque parallèle de tâches)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library).
- Évitez de créer des `async void` méthodes et créez plutôt des `async Task` méthodes. Ils facilitent la gestion des erreurs, la composabilité et la testabilité. L’exception à cette règle est les gestionnaires d’événements asynchrones, qui doivent retourner `void` . Pour plus d’informations, consultez [Évitez Async void](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void).
- Ne mélangez pas le code bloquant et le code asynchrone en appelant les `Task.Wait` `Task.Result` méthodes, ou `GetAwaiter().GetResult` , car elles peuvent entraîner un interblocage. Toutefois, si cette instruction ne doit pas être respectée, l’approche recommandée consiste à appeler la `GetAwaiter().GetResult` méthode, car elle conserve les exceptions de tâche. Pour plus d’informations, consultez [Async All the mode](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) and [Task Exception Handling in .net 4,5](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/).
- Utilisez la `ConfigureAwait` méthode dans la mesure du possible pour créer du code sans contexte. Le code sans contexte offre de meilleures performances pour les applications mobiles et constitue une technique utile pour éviter les interblocages lors de l’utilisation d’une base de code partiellement asynchrone. Pour plus d’informations, consultez [configure Context](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context).
- Utilisez des *tâches de continuation* pour des fonctionnalités telles que la gestion des exceptions levées par l’opération asynchrone précédente et l’annulation d’une continuation avant son démarrage ou pendant son exécution. Pour plus d’informations, consultez [chaînage de tâches à l’aide de tâches continues](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks).
- Utilisez une `ICommand` implémentation asynchrone lorsque des opérations asynchrones sont appelées à partir de `ICommand` . Cela garantit que toutes les exceptions dans la logique de commande asynchrone peuvent être gérées. Pour plus d’informations, consultez [programmation asynchrone : modèles pour les applications MVVM asynchrones : commandes](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands).

## <a name="choose-a-dependency-injection-container-carefully"></a>Choisir un conteneur d’injection de dépendances avec précaution

Les conteneurs d’injection de dépendances introduisent des contraintes de performances supplémentaires dans les applications mobiles. L’inscription et la résolution des types avec un conteneur ont un coût en termes de performances en raison de l’utilisation par le conteneur de la réflexion pour créer chaque type, et plus particulièrement si les dépendances sont reconstruites pour chaque navigation entre les pages de l’application. S’il existe de nombreuses dépendances ou des dépendances profondes, le coût de création peut augmenter de manière significative. En outre, l’inscription de type, qui se produit généralement au démarrage de l’application, peut avoir un impact notable sur le temps de démarrage selon le conteneur utilisé.

En guise d’alternative, l’injection de dépendances peut être rendue plus performante en l’implémentant manuellement à l’aide de fabriques.

## <a name="create-shell-applications"></a>Créer des applications d’interpréteur de commandes

Xamarin.FormsLes applications Shell offrent une expérience de navigation consignes strictes basée sur des menus volants et des onglets. Si l’expérience utilisateur de votre application peut être implémentée avec l’interpréteur de commandes, il est préférable de le faire. Les applications d’interpréteur de commandes permettent d’éviter une mauvaise expérience de démarrage, car les pages sont créées à la demande en réponse à la navigation plutôt qu’au démarrage de l’application, ce qui est le cas avec les applications qui utilisent un [« TabbedPage »](xref:Xamarin.Forms.TabbedPage). Pour plus d’informations, consultez [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Utiliser CollectionView au lieu de ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)est une vue pour la présentation de listes de données à l’aide de différentes spécifications de disposition. Il offre une alternative plus flexible et plus performante à [`ListView`](xref:Xamarin.Forms.ListView) . Pour plus d’informations, consultez [ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Optimiser les performances de ListView

Lorsque [`ListView`](xref:Xamarin.Forms.ListView) vous utilisez, il existe un certain nombre d’expériences utilisateur qui doivent être optimisées :

- **Initialisation** : intervalle de temps qui commence quand le contrôle est créé et se termine quand les éléments sont affichés à l’écran.
- **Défilement** : possibilité de faire défiler la liste et de vérifier que l’interface utilisateur est synchrone avec les entrées tactiles.
- **Interaction** pour l’ajout, la suppression et la sélection d’éléments.

Le [`ListView`](xref:Xamarin.Forms.ListView) contrôle requiert une application pour fournir des modèles de données et de cellules. Cette opération aura un impact important sur les performances du contrôle. Pour plus d’informations, consultez [Performances de ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Optimiser les ressources d’images

L’affichage de ressources d’images peut augmenter considérablement l’encombrement mémoire d’une application. Par conséquent, elles ne doivent être créées que si elles sont nécessaires, et doivent être libérées dès que l’application n’en a plus besoin. Par exemple, si une application affiche une image en lisant les données à partir d’un flux, vérifiez que ce flux est créé uniquement si nécessaire et qu’il est libéré quand il ne l’est plus. Pour ce faire, vous pouvez créer le flux lorsque la page est créée, ou quand l' [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) événement se déclenche, puis supprimer le flux lorsque l' [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) événement se déclenche.

Lors du téléchargement d’une image à afficher avec la [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) méthode, mettez en cache l’image téléchargée en vous assurant que la [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) propriété a la valeur `true` . Pour plus d’informations, consultez [Utilisation des images](~/xamarin-forms/user-interface/images.md).

Pour plus d’informations, consultez [Optimiser les ressources d’images](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

## <a name="reduce-the-visual-tree-size"></a>Réduire la taille de l’arborescence d’éléments visuels

La réduction du nombre d’éléments dans une page accélère l’affichage de la page. Pour ce faire, il existe deux techniques principales. La première consiste à masquer les éléments qui ne sont pas visibles. La [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriété de chaque élément détermine si l’élément doit faire partie de l’arborescence d’éléments visuels. Par conséquent, si un élément n’est pas visible parce qu’il est masqué par d’autres éléments, supprimez l’élément ou affectez à sa propriété `IsVisible` la valeur `false`.

La seconde technique consiste à supprimer les éléments inutiles. Par exemple, l’exemple de code suivant montre une mise en page contenant plusieurs [`Label`](xref:Xamarin.Forms.Label) objets :

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

La plupart des Xamarin.Forms classes de convertisseur exposent la `OnElementChanged` méthode, qui est appelée lorsqu’un Xamarin.Forms contrôle personnalisé est créé pour restituer le contrôle natif correspondant. Les classes de renderers personnalisés dans chaque projet de plateforme remplacent ensuite cette méthode pour instancier et personnaliser le contrôle natif. La méthode `SetNativeControl` est utilisée pour instancier le contrôle natif, et cette méthode attribue également la référence de contrôle à la propriété `Control`.

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

Un nouveau contrôle natif doit uniquement être instancié une seule fois, quand la propriété `Control` a la valeur `null`. En outre, le contrôle doit uniquement être créé, configuré et les gestionnaires d’événements auxquels il est abonné lorsque le convertisseur personnalisé est attaché à un nouvel Xamarin.Forms élément. De même, vous devez vous désabonner des gestionnaires d’événements auxquels vous vous êtes abonné uniquement quand l’élément auquel le renderer est attaché change. L’adoption de cette approche permet de créer un renderer personnalisé efficace qui ne connaît pas les fuites de mémoire.

> [!IMPORTANT]
> La méthode `SetNativeControl` ne doit être appelée que si la propriété `e.NewElement` n’est pas `null`, et si la propriété `Control` est `null`.

Pour plus d’informations sur les renderers personnalisés, consultez [Personnalisation des contrôles sur chaque plateforme](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Liens connexes

- [Niveau de performance multiplateforme](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Compilation de XAML](~/xamarin-forms/xaml/xamlc.md)
- [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [Renderers rapides](~/xamarin-forms/internals/fast-renderers.md)
- [Compression des dispositions](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Xamarin.FormsShell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.FormsCollectionView](~/xamarin-forms/user-interface/collectionview/index.md)
- [Performances de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Optimiser les ressources d’image](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personnaliser les contrôles sur chaque plateforme](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
