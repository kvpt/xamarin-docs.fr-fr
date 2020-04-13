---
title: Améliorer les performances des applications Xamarin.Forms
description: Il existe plusieurs techniques permettant d’accroître les performances des applications Xamarin.Forms. Utilisées conjointement, ces techniques peuvent considérablement réduire la charge de travail d’une UC, ainsi que la quantité de mémoire consommée par une application.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
ms.openlocfilehash: 4427d347723284a2f8897612f10857270c9631bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305212"
---
# <a name="improve-xamarinforms-app-performance"></a>Améliorer les performances des applications Xamarin.Forms

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolve 2016 : Optimisation des performances des applications avec Xamarin.Forms**

Le mauvais niveau de performance d’une application se présente de plusieurs façons. L’application semble ne pas répondre, le défilement de l’affichage est ralenti et la durée de vie de la batterie de l’appareil réduite. Toutefois, l’optimisation des performances implique davantage de choses que l’implémentation d’un code efficace. L’expérience utilisateur liée au niveau de performance de l’application doit également être prise en compte. Par exemple, pour contribuer à améliorer l’expérience utilisateur, vous devez vérifier que les opérations s’exécutent sans empêcher l’utilisateur d’effectuer d’autres activités.

De nombreuses techniques permettent d’accroître les performances, notamment celles qui sont visibles, d’applications Xamarin.Forms. Utilisées conjointement, ces techniques peuvent considérablement réduire la charge de travail d’une UC, ainsi que la quantité de mémoire consommée par une application.

> [!NOTE]
>  Avant de lire cet article, vous devez lire [Performances entre plateformes](~/cross-platform/deploy-test/memory-perf-best-practices.md), qui décrit des techniques non spécifiques aux plateformes pour améliorer l’utilisation de la mémoire et les performances des applications générées à l’aide de la plateforme Xamarin.

## <a name="enable-the-xaml-compiler"></a>Activer le compilateur XAML

XAML peut être éventuellement compilé directement en langage intermédiaire (IL) avec le compilateur XAML (XAMLC). XAMLC offre un certain nombre d’avantages :

- Il effectue une vérification au moment de la compilation du code XAML et informe l’utilisateur des erreurs rencontrées.
- Il supprime une partie du temps de chargement et d’instanciation pour les éléments XAML.
- Il permet de réduire la taille de fichier de l’assembly final en n’incluant plus les fichiers .xaml.

XAMLC est activé par défaut dans les nouvelles solutions Xamarin.Forms. Il peut toutefois être nécessaire de l’activer dans des solutions plus anciennes. Pour plus d’informations, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Utiliser des liaisons compilées

Les liaisons compilées améliorent les performances de liaison de données dans les applications Xamarin.Forms en résolvant les expressions de liaison au moment de la compilation plutôt qu’au moment de l’exécution avec réflexion. La compilation d’une expression de liaison génère du code compilé qui résout généralement une liaison 8 à 20 fois plus rapidement qu’avec une liaison classique. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Réduire les liaisons inutiles

N’utilisez pas de liaisons pour le contenu qui peut être aisément défini de manière statique. Il n’existe aucun avantage à lier des données qui n’ont pas besoin de l’être, car les liaisons ne sont pas rentables. Par exemple, `Button.Text = "Accept"` le paramètre [`Button.Text`](xref:Xamarin.Forms.Button.Text) comporte moins de `string` frais généraux que de lier à une propriété viewmodel avec valeur « Accepter ».

## <a name="use-fast-renderers"></a>Utiliser des renderers rapides

Les renderers rapides réduisent l’inflation et les coûts de rendu des contrôles Xamarin.Forms sur Android en aplanissant la hiérarchie de contrôles natifs obtenue. Cette technique améliore davantage les performances en créant moins d’objets, ce qui aboutit à une arborescence d’éléments visuels moins complexe et à une utilisation moindre de la mémoire.

À partir de Xamarin.Forms 4.0, toutes les applications ciblant `FormsAppCompatActivity` utilisent des renderers rapides par défaut. Pour plus d’informations, consultez [Renderers rapides](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Activer le suivi de démarrage sur Android

La compilation Ahead Of Time (AOT) sur Android réduit la charge de démarrage de l’application juste-à-temps (JIT) et l’utilisation de la mémoire, au détriment de la création d’un APK de plus grande taille. Une alternative consiste à utiliser le suivi de démarrage, qui fournit un compromis entre taille d’APK Android et temps de démarrage par rapport à la compilation AOT conventionnelle.

Plutôt que de compiler la plus grande partie possible de l’application en code non managé, le suivi de démarrage compile uniquement l’ensemble des méthodes managées qui représentent les parties les plus coûteuses du démarrage de l’application dans une application Xamarin.Forms vide. Cette approche entraîne une réduction de la taille de l’APK par rapport à la compilation AOT conventionnelle, tout en fournissant des améliorations similaires au démarrage.

## <a name="enable-layout-compression"></a>Activer la compression des dispositions

La compression des dispositions supprime les dispositions spécifiées de l’arborescence d’éléments visuels dans le but d’améliorer les performances de rendu des pages. Les avantages en matière de performances de cette technique varient selon la complexité d’une page, la version du système d’exploitation utilisé et l’appareil sur lequel l’application est en cours d’exécution. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens. Pour plus d’informations, consultez [Compression des dispositions](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Choisir la disposition correcte

Une disposition qui est capable d’afficher plusieurs enfants, mais qui n’en a qu’un seul, est inutile. Par exemple, l’exemple [`StackLayout`](xref:Xamarin.Forms.StackLayout) de code suivant montre un avec un seul enfant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Il s’agit [`StackLayout`](xref:Xamarin.Forms.StackLayout) d’un gaspillage et l’élément doit être supprimé, comme le montre l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

En outre, n’essayez pas de reproduire l’apparence d’une disposition spécifique à l’aide de combinaisons d’autres dispositions, car cela aboutirait à des calculs de dispositions inutiles. Par exemple, n’essayez pas [`Grid`](xref:Xamarin.Forms.Grid) de reproduire une [`StackLayout`](xref:Xamarin.Forms.StackLayout) mise en page en utilisant une combinaison d’instances. L’exemple de code ci-dessous illustre cette mauvaise pratique :

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

Cela ne sert à rien, car des calculs de dispositions inutiles sont effectués. Au lieu de cela, la [`Grid`](xref:Xamarin.Forms.Grid)mise en page souhaitée peut être mieux réalisée en utilisant un , comme indiqué dans l’exemple de code suivant:

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

- Réduisez la profondeur des hiérarchies [`Margin`](xref:Xamarin.Forms.View.Margin) d’aménagement en spécifiant les valeurs des propriétés, permettant la création d’aménagements avec moins de vues d’emballage. Pour plus d’informations, consultez [Marges et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Lors de [`Grid`](xref:Xamarin.Forms.Grid)l’utilisation d’un , essayez de vous [`Auto`](xref:Xamarin.Forms.GridLength.Auto) assurer que le moins de lignes et de colonnes que possible sont mis à la taille. Pour chaque ligne ou colonne dimensionnée automatiquement, le moteur de disposition effectue des calculs de dispositions supplémentaires. Utilisez plutôt des lignes et colonnes de taille fixe si possible. Alternativement, définir des lignes et des colonnes [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) pour occuper une quantité proportionnelle d’espace avec la valeur de recensement, à condition que l’arbre parent respecte ces lignes directrices d’aménagement.
- Ne définissez [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) pas [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) les propriétés et les propriétés d’une mise en page à moins que nécessaire. Les valeurs [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) par [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) défaut de et de permettre la meilleure optimisation de la mise en page. La modification de ces propriétés a un coût et consomme de la mémoire, même en leur affectant les valeurs par défaut.
- Évitez d’utiliser un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) chaque fois que c’est possible. Le processeur aurait considérablement plus de travail à effectuer.
- Lors de [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)l’utilisation [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) d’un , éviter d’utiliser la propriété chaque fois que possible.
- Lors de [`StackLayout`](xref:Xamarin.Forms.StackLayout)l’utilisation d’un [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands), assurez-vous qu’un seul enfant est réglé à . Cette propriété permet de garantir que l’enfant spécifié occupe le plus grand espace que l’élément `StackLayout` peut lui donner, et il est inutile d’effectuer ces calculs plusieurs fois.
- Évitez d’appeler l’une des méthodes de la [`Layout`](xref:Xamarin.Forms.Layout) classe, car elles entraînent des calculs de mise en page coûteux. Au lieu de cela, il est probable que le [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) comportement de mise en page souhaité peut être obtenu en définissant le et les propriétés. Alternativement, sous-classez la [`Layout<View>`](xref:Xamarin.Forms.Layout`1) classe pour atteindre le comportement de mise en page désiré.
- Ne mettez pas [`Label`](xref:Xamarin.Forms.Label) à jour les instances plus fréquemment que nécessaire, car le changement de taille de l’étiquette peut entraîner la recalcul de la mise en page de l’écran entier.
- Ne définissez [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) pas la propriété sauf si nécessaire.
- Définissez [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) les [`Label`](xref:Xamarin.Forms.Label) cas [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) dans la mesure du possible.

## <a name="use-asynchronous-programming"></a>Utiliser la programmation asynchrone

La réactivité globale de votre application peut être améliorée, et les goulots d’étranglement de performance souvent évités, en utilisant la programmation asynchrone. Dans .NET, le [modèle asynchrone basé sur les tâches (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) est le modèle de conception recommandé pour les opérations asynchrones. Cependant, une utilisation incorrecte du TAP peut entraîner des applications non performantes. Par conséquent, les lignes directrices suivantes doivent être suivies lors de l’utilisation du TAP.

### <a name="fundamentals"></a>Notions de base

- Comprendre le cycle de vie des `TaskStatus` tâches, qui est représenté par l’énumération. Pour plus d’informations, voir [La signification de TaskStatus](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/) et [le statut de tâche](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status).
- Utilisez `Task.WhenAll` la méthode pour attendre asynchronement pour plusieurs opérations asynchrones pour finir, plutôt que individuellement `await` une série d’opérations asynchrones. Pour plus d’informations, voir [Task.WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Utilisez `Task.WhenAny` la méthode pour attendre asynchronement l’une des opérations asynchrones multiples pour finir. Pour plus d’informations, voir [Task.WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Utilisez `Task.Delay` la méthode `Task` pour produire un objet qui se termine après le temps spécifié. Ceci est utile pour des scénarios tels que le sondage pour les données, et le report de la manipulation des entrées des utilisateurs pour une période prédéterminée. Pour plus d’informations, voir [Task.Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- Exécutez des opérations de processeur synchrones `Task.Run` intensives sur le pool de threads avec la méthode. Cette méthode est un `TaskFactory.StartNew` raccourci pour la méthode, avec les arguments les plus optimaux ensemble. Pour plus d’informations, voir [Task.Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun).
- Évitez d’essayer de créer des constructeurs asynchrones. Utilisez plutôt des événements du cycle de `await` vie ou une logique d’initialisation distincte pour correctement toute initialisation. Pour plus d’informations, voir [Async Constructors](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html) sur blog.stephencleary.com.
- Utilisez le modèle de tâche paresseux pour éviter d’attendre que les opérations asynchrones se terminent pendant le démarrage de l’application. Pour plus d’informations, voir [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).
- Créez un emballage de tâches pour les opérations asynchrones existantes, qui n’utilisent pas le TAP, en créant des `TaskCompletionSource<T>` objets. Ces objets bénéficient `Task` des avantages de la programmabilité, et vous `Task`permettent de contrôler la durée de vie et l’achèvement de la . Pour plus d’informations, voir [La nature de TaskCompletionSource](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/).
 
- Retournez `Task` un objet, au `Task` lieu de retourner un objet attendu, lorsqu’il n’est pas nécessaire de traiter le résultat d’une opération asynchrone. Ceci est plus performant en raison de moins de commutation de contexte étant effectuée.
- Utilisez la bibliothèque de flux de données De la Bibliothèque parallèle de travail (TPL) dans des scénarios tels que le traitement des données dès qu’elles sont disponibles, ou lorsque vous avez plusieurs opérations qui doivent communiquer les unes avec les autres de façon asynchrone. Pour plus d’informations, voir [Dataflow (Task Parallel Library)](/dotnet/standard/parallel-programming/dataflow-task-parallel-library).

### <a name="ui"></a>UI

- Appelez une version asynchrone d’une API, si elle est disponible. Ainsi, le thread d’interface utilisateur reste non bloqué, ce qui améliore l’expérience utilisateur.
- Mettre à jour les éléments de l’interface utilisateur avec les données des opérations asynchrones sur le thread d’interface utilisateur, afin d’éviter que des exceptions ne soient lancées. Cependant, les `ListView.ItemsSource` mises à jour de la propriété seront automatiquement marshaled au fil d’interface utilisateur. Pour plus d’informations sur la détermination si le code est en cours d’exécution sur le thread d’interface utilisateur, voir [Xamarin.Essentials: MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms).

    > [!IMPORTANT]
    > Toutes les propriétés de contrôle mises à jour via la liaison de données seront automatiquement intégrées au thread d’interface utilisateur.

### <a name="error-handling"></a>Gestion des erreurs

- Renseignez-vous sur la manipulation d’exception asynchrone. Les exceptions non manipulées qui sont jetées par le code qui fonctionne asynchronement sont propagés de nouveau au fil d’appel, sauf dans certains scénarios. Pour plus d’informations, voir [Traitement d’exception (Task Parallel Library)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library).
- Évitez `async void` de créer des `async Task` méthodes, et plutôt de créer des méthodes. Ceux-ci permettent une manipulation d’erreur, une composition et une testabilité plus faciles. L’exception à cette ligne directrice est asynchrone `void`gestionnaires d’événements, qui doivent revenir . Pour plus d’informations, voir [Éviter Async Void](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void).
- Ne mélangez pas le blocage et le `Task.Wait`code `Task.Result`asynchrone en appelant le , , ou `GetAwaiter().GetResult` des méthodes, car ils peuvent entraîner une impasse se produisant. Toutefois, si cette directive doit être violée, l’approche privilégiée est d’appeler la `GetAwaiter().GetResult` méthode parce qu’elle préserve les exceptions de tâche. Pour plus d’informations, voir [Async All the Way](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) and [Task Exception Handling in .NET 4.5](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/).
- Utilisez `ConfigureAwait` la méthode dans la mesure du possible, pour créer un code sans contexte. Le code sans contexte a de meilleures performances pour les applications mobiles et est une technique utile pour éviter l’impasse lorsque vous travaillez avec une base de code partiellement asynchrone. Pour plus d’informations, voir [Configure Context](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context).
- Utilisez *des tâches de continuation* pour des fonctionnalités telles que la manipulation d’exceptions jetées par l’opération asynchrone précédente, et l’annulation d’une continuation avant qu’elle ne commence ou pendant qu’elle est en cours d’exécution. Pour plus d’informations, voir [Tâches d’enchaînement en utilisant des tâches continues](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks).
- Utilisez une `ICommand` implémentation asynchrone lorsque des opérations `ICommand`asynchrones sont invoquées à partir de la . Cela garantit que toutes les exceptions dans la logique de commande asynchrone peuvent être traitées. Pour plus d’informations, voir [Async Programming: Patterns for Asynchronous MVVM Applications: Commands](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands).

## <a name="choose-a-dependency-injection-container-carefully"></a>Choisir un conteneur d’injection de dépendances avec précaution

Les conteneurs d’injection de dépendances introduisent des contraintes de performances supplémentaires dans les applications mobiles. L’inscription et la résolution des types avec un conteneur ont un coût en termes de performances en raison de l’utilisation par le conteneur de la réflexion pour créer chaque type, et plus particulièrement si les dépendances sont reconstruites pour chaque navigation entre les pages de l’application. S’il existe de nombreuses dépendances ou des dépendances profondes, le coût de création peut augmenter de manière significative. En outre, l’inscription de type, qui se produit généralement au démarrage de l’application, peut avoir un impact notable sur le temps de démarrage selon le conteneur utilisé.

En guise d’alternative, l’injection de dépendances peut être rendue plus performante en l’implémentant manuellement à l’aide de fabriques.

## <a name="create-shell-applications"></a>Créer des applications d’interpréteur de commandes

Les applications d’interpréteur de commandes Xamarin.Forms fournissent une expérience de navigation robuste, basée sur des menus volants et des onglets. Si l’expérience utilisateur de votre application peut être implémentée avec l’interpréteur de commandes, il est préférable de le faire. Les applications d’interpréteur de commandes permettent d’éviter une mauvaise expérience de démarrage, car les pages sont créées à la demande en réponse à la navigation plutôt qu’au démarrage de l’application, ce qui est le cas avec les applications qui utilisent un [« TabbedPage »](xref:Xamarin.Forms.TabbedPage). Pour plus d’informations, voir [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Utiliser CollectionView au lieu de ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)est une vue pour présenter des listes de données en utilisant différentes spécifications de mise en page. Il offre une alternative plus [`ListView`](xref:Xamarin.Forms.ListView)flexible, et performante à . Pour plus d’informations, consultez [CollectionView Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Optimiser les performances de ListView

Lors [`ListView`](xref:Xamarin.Forms.ListView)de l’utilisation , il ya un certain nombre d’expériences utilisateur qui doivent être optimisés:

- **Initialisation** : intervalle de temps qui commence quand le contrôle est créé et se termine quand les éléments sont affichés à l’écran.
- **Défilement** : possibilité de faire défiler la liste et de vérifier que l’interface utilisateur est synchrone avec les entrées tactiles.
- **Interaction** pour l’ajout, la suppression et la sélection d’éléments.

Le [`ListView`](xref:Xamarin.Forms.ListView) contrôle nécessite une application pour fournir des données et des modèles cellulaires. Cette opération aura un impact important sur les performances du contrôle. Pour plus d’informations, consultez [Performances de ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Optimiser les ressources d’images

L’affichage de ressources d’images peut augmenter considérablement l’encombrement mémoire d’une application. Par conséquent, elles ne doivent être créées que si elles sont nécessaires, et doivent être libérées dès que l’application n’en a plus besoin. Par exemple, si une application affiche une image en lisant les données à partir d’un flux, vérifiez que ce flux est créé uniquement si nécessaire et qu’il est libéré quand il ne l’est plus. Ceci peut être réalisé en créant le flux lorsque [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) la page est créée, ou [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) lorsque l’événement s’allume, puis en se débarrassant du flux lorsque l’événement s’allume.

Lors du téléchargement d’une [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) image pour l’affichage avec [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) la méthode, cachez l’image téléchargée en s’assurant que la propriété est définie à `true`. Pour plus d’informations, consultez [Utilisation des images](~/xamarin-forms/user-interface/images.md).

Pour plus d’informations, consultez [Optimiser les ressources d’images](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

## <a name="reduce-the-visual-tree-size"></a>Réduire la taille de l’arborescence d’éléments visuels

La réduction du nombre d’éléments dans une page accélère l’affichage de la page. Pour ce faire, il existe deux techniques principales. La première consiste à masquer les éléments qui ne sont pas visibles. La [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriété de chaque élément détermine si l’élément doit faire partie de l’arbre visuel ou non. Par conséquent, si un élément n’est pas visible parce qu’il est masqué par d’autres éléments, supprimez l’élément ou affectez à sa propriété `IsVisible` la valeur `false`.

La seconde technique consiste à supprimer les éléments inutiles. Par exemple, l’exemple de code [`Label`](xref:Xamarin.Forms.Label) suivant affiche une mise en page contenant plusieurs objets :

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

- [Niveau de performance multiplateforme](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Compilation de XAML](~/xamarin-forms/xaml/xamlc.md)
- [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [Renderers rapides](~/xamarin-forms/internals/fast-renderers.md)
- [Compression de mise en page](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Interpréteur de commandes Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [CollectionView Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md)
- [Performances de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Optimiser les ressources en images](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personnaliser les contrôles sur chaque plateforme](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
