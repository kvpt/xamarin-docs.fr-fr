---
title: Modèle Model-View-ViewModel
description: Ce chapitre explique comment l’application mobile eShopOnContainers utilise le modèle MVVM pour séparer correctement la logique métier et de présentation de l’application à partir de son interface utilisateur.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d6c9b74c9abc1a2c493c31699b52969a7d129429
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305268"
---
# <a name="the-model-view-viewmodel-pattern"></a>Modèle Model-View-ViewModel

L’expérience de développement Xamarin. Forms implique généralement la création d’une interface utilisateur en XAML, puis l’ajout du code-behind qui fonctionne sur l’interface utilisateur. À mesure que les applications sont modifiées et qu’elles augmentent la taille et la portée, des problèmes de maintenance complexes peuvent survenir. Ces problèmes incluent le couplage étroit entre les contrôles de l’interface utilisateur et la logique métier, ce qui augmente le coût des modifications apportées à l’interface utilisateur et la difficulté du test unitaire de ce code.

Le modèle MVVM (Model-View-ViewModel) permet de séparer correctement la logique métier et de présentation d’une application à partir de son interface utilisateur. Maintenir une séparation nette entre la logique d’application et l’interface utilisateur permet de résoudre de nombreux problèmes de développement et peut rendre une application plus facile à tester, à gérer et à évoluer. Cela peut également améliorer les opportunités de réutilisation de code et permet aux développeurs et aux concepteurs d’interface utilisateur de collaborer plus facilement lors du développement de leurs parties respectives d’une application.

## <a name="the-mvvm-pattern"></a>Modèle MVVM

Il existe trois composants principaux dans le modèle MVVM : le modèle, la vue et le modèle de vue. Chacun remplit un rôle distinct. La figure 2-1 montre les relations entre les trois composants.

![](mvvm-images/mvvm.png "The MVVM pattern")

**Figure 2-1**: modèle MVVM

En plus de comprendre les responsabilités de chaque composant, il est également important de comprendre comment elles interagissent entre elles. À un niveau élevé, la vue « sait » le modèle de vue et le modèle de vue « connaît » le modèle, mais le modèle n’est pas conscient du modèle de vue et le modèle de vue n’est pas conscient de la vue. Par conséquent, le modèle de vue isole la vue du modèle et permet au modèle d’évoluer indépendamment de la vue.

Les avantages de l’utilisation du modèle MVVM sont les suivants :

- S’il existe une implémentation de modèle existante qui encapsule la logique métier existante, il peut être difficile ou risqué de la modifier. Dans ce scénario, le modèle de vue agit comme un adaptateur pour les classes de modèle et vous permet d’éviter d’apporter des modifications majeures au code du modèle.
- Les développeurs peuvent créer des tests unitaires pour le modèle de vue et le modèle, sans utiliser la vue. Les tests unitaires pour le modèle de vue peuvent exercer exactement la même fonctionnalité que celle utilisée par la vue.
- L’interface utilisateur de l’application peut être repensée sans toucher au code, à condition que la vue soit entièrement implémentée en XAML. Par conséquent, une nouvelle version de la vue doit fonctionner avec le modèle de vue existant.
- Les concepteurs et les développeurs peuvent travailler de manière indépendante et simultanée sur leurs composants pendant le processus de développement. Les concepteurs peuvent se concentrer sur la vue, tandis que les développeurs peuvent travailler sur le modèle de vue et les composants du modèle.

La clé de l’utilisation de MVVM s’appuie sur la manière de factoriser le code d’application dans les classes appropriées et de comprendre comment les classes interagissent. Les sections suivantes décrivent les responsabilités de chacune des classes dans le modèle MVVM.

### <a name="view"></a>Affichage

La vue est chargée de définir la structure, la disposition et l’apparence de ce que l’utilisateur voit sur l’écran. Dans l’idéal, chaque vue est définie en XAML, avec un code-behind limité qui ne contient pas de logique métier. Toutefois, dans certains cas, le code-behind peut contenir une logique d’interface utilisateur qui implémente un comportement visuel difficile à exprimer en XAML, tel que les animations.

Dans une application Xamarin. Forms, une vue est généralement une classe dérivée de [`Page`](xref:Xamarin.Forms.Page)ou dérivée de [`ContentView`](xref:Xamarin.Forms.ContentView). Toutefois, les vues peuvent également être représentées par un modèle de données, qui spécifie les éléments d’interface utilisateur à utiliser pour représenter visuellement un objet lorsqu’il est affiché. Un modèle de données en tant que vue n’a pas de code-behind et est conçu pour être lié à un type de modèle de vue spécifique.

> [!TIP]
> Évitez d’activer et de désactiver les éléments d’interface utilisateur dans le code-behind. Vérifiez que les modèles de vue sont responsables de la définition des modifications d’état logique qui affectent certains aspects de l’affichage de la vue, par exemple si une commande est disponible ou si une opération est en attente. Par conséquent, activez et désactivez les éléments d’interface utilisateur en liant les propriétés du modèle de vue, plutôt que de les activer et de les désactiver dans le code-behind.

Il existe plusieurs options pour exécuter du code sur le modèle de vue en réponse aux interactions sur la vue, telles qu’un clic de bouton ou une sélection d’élément. Si un contrôle prend en charge les commandes, la propriété `Command` du contrôle peut être liée aux données d’une `ICommand` propriété sur le modèle de vue. Lorsque la commande du contrôle est appelée, le code dans le modèle de vue est exécuté. En plus des commandes, les comportements peuvent être attachés à un objet dans la vue et peuvent écouter l’appel d’une commande ou déclencher l’événement. En réponse, le comportement peut ensuite appeler un `ICommand` sur le modèle de vue ou une méthode sur le modèle de vue.

### <a name="viewmodel"></a>ViewModel

Le modèle de vue implémente les propriétés et les commandes auxquelles la vue peut être liée, et notifie la vue de toute modification d’état via des événements de notification de modification. Les propriétés et les commandes que le modèle de vue fournit définissent les fonctionnalités que l’interface utilisateur peut offrir, mais la vue détermine la manière dont cette fonctionnalité doit être affichée.

> [!TIP]
> Maintenir la réactivité de l’interface utilisateur avec les opérations asynchrones. Les applications mobiles doivent garder le thread d’interface utilisateur débloqué pour améliorer la perception des performances de l’utilisateur. Par conséquent, dans le modèle de vue, utilisez des méthodes asynchrones pour les opérations d’e/s et déclenchez des événements pour notifier de façon asynchrone des vues de modifications de propriété.

Le modèle de vue est également chargé de coordonner les interactions de la vue avec toutes les classes de modèle requises. Il y a généralement une relation un-à-plusieurs entre le modèle de vue et les classes de modèle. Le modèle de vue peut choisir d’exposer des classes de modèle directement à la vue afin que les contrôles de la vue puissent se lier directement aux données. Dans ce cas, les classes de modèle devront être conçues pour prendre en charge la liaison de données et les événements de notification de modification.

Chaque modèle de vue fournit des données à partir d’un modèle dans un formulaire que la vue peut facilement consommer. Pour ce faire, le modèle de vue effectue parfois la conversion des données. Le fait de placer cette conversion de données dans le modèle de vue est une bonne idée, car elle fournit des propriétés auxquelles la vue peut se lier. Par exemple, le modèle de vue peut combiner les valeurs de deux propriétés pour faciliter l’affichage par la vue.

> [!TIP]
> Centralisez les conversions de données dans une couche de conversion. Il est également possible d’utiliser des convertisseurs comme une couche de conversion de données distincte qui se situe entre le modèle de vue et la vue. Cela peut être nécessaire, par exemple, lorsque les données requièrent une mise en forme spéciale que le modèle de vue ne fournit pas.

Pour que le modèle de vue participe à la liaison de données bidirectionnelle avec la vue, ses propriétés doivent déclencher l’événement `PropertyChanged`. Les modèles de vue répondent à cette exigence en implémentant l’interface `INotifyPropertyChanged` et en déclenchant l’événement `PropertyChanged` lorsqu’une propriété est modifiée.

Pour les collections, le `ObservableCollection<T>` convivial de vue est fourni. Cette collection implémente une notification de modification de la collection, ce qui évite au développeur d’avoir à implémenter l’interface `INotifyCollectionChanged` sur les collections.

### <a name="model"></a>Modèle

Les classes de modèle sont des classes non visuelles qui encapsulent les données de l’application. Par conséquent, le modèle peut être considéré comme représentant le modèle de domaine de l’application, qui comprend généralement un modèle de données avec la logique métier et la logique de validation. Parmi les exemples d’objets de modèle figurent les objets DTO (Data Transfer Objects), les anciens objets CLR (Plains) et les objets d’entité et de proxy générés.

Les classes de modèle sont généralement utilisées conjointement avec les services ou les référentiels qui encapsulent l’accès aux données et la mise en cache.

## <a name="connecting-view-models-to-views"></a>Connexion de modèles de vue à des vues

Les modèles de vue peuvent être connectés à des vues à l’aide des fonctionnalités de liaison de données de Xamarin. Forms. Il existe de nombreuses approches qui peuvent être utilisées pour construire des vues et afficher des modèles et les associer au moment de l’exécution. Ces approches se répartissent en deux catégories, appelées première composition de vue et vue de la composition du modèle de vue. Le choix entre la première composition de la vue et de la vue de la composition du modèle est un problème de préférence et de complexité. Toutefois, toutes les approches partagent le même objectif, pour que la vue ait un modèle de vue affecté à sa propriété BindingContext.

Avec la composition View First, l’application est conceptuellement composée de vues qui se connectent aux modèles de vue dont elles dépendent. Le principal avantage de cette approche est qu’il permet de créer facilement des applications testables faiblement couplées, car les modèles de vue n’ont aucune dépendance vis-à-vis des vues elles-mêmes. Il est également facile de comprendre la structure de l’application en suivant sa structure visuelle, plutôt que d’avoir à effectuer le suivi de l’exécution du code pour comprendre comment les classes sont créées et associées. En outre, View First construction s’aligne avec le système de navigation Xamarin. Forms chargé de construire des pages lorsque la navigation se produit, ce qui rend une composition de modèle d’affichage complexe et mal alignée avec la plateforme.

Avec la première composition de modèle de vue, l’application est conceptuellement composée de modèles de vue, avec un service chargé de localiser la vue d’un modèle de vue. La première composition du modèle de vue semble plus naturelle pour certains développeurs, étant donné que la création de la vue peut être extraite, ce qui leur permet de se concentrer sur la structure logique non-interface utilisateur de l’application. En outre, il permet de créer des modèles de vue par d’autres modèles de vue. Toutefois, cette approche est souvent complexe et peut devenir difficile à comprendre comment les différentes parties de l’application sont créées et associées.

> [!TIP]
> Gardez les modèles de vue et les vues indépendants. La liaison de vues à une propriété dans une source de données doit être la dépendance principale de la vue sur le modèle de vue correspondant. En particulier, ne référencez pas les types de vue, tels que [`Button`](xref:Xamarin.Forms.Button) et [`ListView`](xref:Xamarin.Forms.ListView), à partir des modèles de vue. En suivant les principes décrits ici, les modèles de vue peuvent être testés de manière isolée, ce qui réduit la probabilité d’erreurs logicielles en limitant l’étendue.

Les sections suivantes décrivent les principales approches pour connecter des modèles de vue à des vues.

### <a name="creating-a-view-model-declaratively"></a>Création d’un modèle de vue de manière déclarative

L’approche la plus simple est de faire en sorte que la vue instancie de manière déclarative son modèle de vue correspondant en XAML. Lorsque la vue est construite, l’objet de modèle de vue correspondant est également construit. Cette approche est illustrée dans l’exemple de code suivant :

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Lorsque le [`ContentPage`](xref:Xamarin.Forms.ContentPage) est créé, une instance du `LoginViewModel` est automatiquement construite et définie comme [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)de la vue.

Cette construction et assignation déclarative du modèle de vue par la vue présente l’avantage d’être simple, mais présente l’inconvénient qu’elle nécessite un constructeur par défaut (sans paramètre) dans le modèle de vue.

### <a name="creating-a-view-model-programmatically"></a>Création d’un modèle de vue par programme

Une vue peut avoir du code dans le fichier code-behind qui produit le modèle de vue qui est assigné à sa propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Cela est souvent accompli dans le constructeur de la vue, comme illustré dans l’exemple de code suivant :

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

La construction de programmation et l’assignation du modèle de vue dans le code-behind de la vue ont l’avantage d’être simple. Toutefois, le principal inconvénient de cette approche est que la vue doit fournir le modèle de vue avec toutes les dépendances requises. L’utilisation d’un conteneur d’injection de dépendances peut contribuer à maintenir un couplage faible entre le modèle de vue et de vue. Pour plus d’informations, consultez [injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Création d’une vue définie en tant que modèle de données

Une vue peut être définie en tant que modèle de données et associée à un type de modèle de vue. Les modèles de données peuvent être définis en tant que ressources, ou ils peuvent être définis inline dans le contrôle qui affichera le modèle de vue. Le contenu du contrôle est l’instance de modèle de vue et le modèle de données est utilisé pour le représenter visuellement. Cette technique est un exemple de situation dans laquelle le modèle de vue est instancié en premier, suivi de la création de la vue.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Création automatique d’un modèle de vue avec un localisateur de modèle de vue

Un localisateur de modèle de vue est une classe personnalisée qui gère l’instanciation des modèles de vue et leur association aux vues. Dans l’application mobile eShopOnContainers, la classe `ViewModelLocator` a une propriété jointe, `AutoWireViewModel`, qui est utilisée pour associer des modèles de vue à des vues. Dans le XAML de la vue, cette propriété jointe a la valeur true pour indiquer que le modèle de vue doit être connecté automatiquement à la vue, comme illustré dans l’exemple de code suivant :

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

La propriété `AutoWireViewModel` est une propriété pouvant être liée qui est initialisée sur false, et lorsque sa valeur change, le gestionnaire d’événements `OnAutoWireViewModelChanged` est appelé. Cette méthode résout le modèle de vue pour la vue. L’exemple de code suivant montre comment procéder :

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

La méthode `OnAutoWireViewModelChanged` tente de résoudre le modèle de vue à l’aide d’une approche basée sur une convention. Cette Convention suppose que :

- Les modèles d’affichage se trouvent dans le même assembly que les types de vues.
- Les vues se trouvent dans un. Espace de noms enfant views.
- Les modèles de vue sont dans un. Espace de noms enfant ViewModels.
- Les noms de modèle de vue correspondent aux noms de vue et se terminent par « ViewModel ».

Enfin, la méthode `OnAutoWireViewModelChanged` définit la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du type de vue sur le type de modèle de vue résolu. Pour plus d’informations sur la résolution du type de modèle de vue, consultez [Resolution](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Cette approche présente l’avantage qu’une application a une seule classe qui est responsable de l’instanciation des modèles de vue et de leur connexion aux vues.

> [!TIP]
> Utilisez un localisateur de modèle de vue pour faciliter la substitution. Un localisateur de modèle de vue peut également être utilisé comme point de substitution pour d’autres implémentations de dépendances, telles que le test unitaire ou les données au moment de la conception.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Mise à jour des vues en réponse aux modifications dans le modèle ou le modèle de vue sous-jacent

Toutes les classes de modèle et de modèle d’affichage qui sont accessibles à une vue doivent implémenter l’interface `INotifyPropertyChanged`. L’implémentation de cette interface dans un modèle de vue ou une classe de modèle permet à la classe de fournir des notifications de modifications à tous les contrôles liés aux données dans la vue lorsque la valeur de propriété sous-jacente est modifiée.

Les applications doivent être conçues pour l’utilisation correcte de la notification de modification de propriété, en répondant aux exigences suivantes :

- En levant toujours un événement `PropertyChanged` si la valeur d’une propriété publique change. Ne supposez pas que le déclenchement de l’événement `PropertyChanged` peut être ignoré en raison de la façon dont se produit la liaison XAML.
- En levant toujours un événement `PropertyChanged` pour toutes les propriétés calculées dont les valeurs sont utilisées par d’autres propriétés dans le modèle ou le modèle de vue.
- En levant toujours l’événement `PropertyChanged` à la fin de la méthode qui effectue une modification de propriété ou lorsque l’objet est dans un État sûr. Le déclenchement de l’événement interrompt l’opération en appelant les gestionnaires de l’événement de façon synchrone. Si cela se produit au milieu d’une opération, il peut exposer l’objet aux fonctions de rappel lorsqu’il est dans un État non sécurisé, partiellement mis à jour. En outre, il est possible que les modifications en cascade soient déclenchées par des événements de `PropertyChanged`. Les modifications en cascade nécessitent généralement l’achèvement des mises à jour avant que le changement en cascade ne soit sécurisé.
- Ne déclenche jamais un événement `PropertyChanged` si la propriété ne change pas. Cela signifie que vous devez comparer les valeurs anciennes et nouvelles avant de déclencher l’événement `PropertyChanged`.
- Ne déclenchez jamais l’événement `PropertyChanged` pendant le constructeur d’un modèle de vue si vous initialisez une propriété. Les contrôles liés aux données dans la vue ne sont pas abonnés à recevoir des notifications de modification à ce stade.
- Ne déclenchez jamais plus d’un événement `PropertyChanged` avec le même argument de nom de propriété dans un appel synchrone unique d’une méthode publique d’une classe. Par exemple, pour une `NumberOfItems` propriété dont le magasin de stockage est le champ `_numberOfItems`, si une méthode incrémente `_numberOfItems` 50 fois pendant l’exécution d’une boucle, elle doit uniquement déclencher la notification de modification de propriété sur la propriété `NumberOfItems` une fois le travail terminé. Pour les méthodes asynchrones, déclenchez l’événement `PropertyChanged` pour un nom de propriété donné dans chaque segment synchrone d’une chaîne de continuation asynchrone.

L’application mobile eShopOnContainers utilise la classe `ExtendedBindableObject` pour fournir des notifications de modification, qui est illustrée dans l’exemple de code suivant :

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

La classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) de Xamarin. Form implémente l’interface `INotifyPropertyChanged` et fournit une méthode [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) . La classe `ExtendedBindableObject` fournit la méthode `RaisePropertyChanged` pour appeler la notification de modification de propriété et, dans ce cas, utilise la fonctionnalité fournie par la classe `BindableObject`.

Chaque classe de modèle de vue de l’application mobile eShopOnContainers dérive de la classe `ViewModelBase`, qui à son tour dérive de la classe `ExtendedBindableObject`. Par conséquent, chaque classe de modèle de vue utilise la méthode `RaisePropertyChanged` dans la classe `ExtendedBindableObject` pour fournir la notification de modification de propriété. L’exemple de code suivant montre comment l’application mobile eShopOnContainers appelle la notification de modification de propriété à l’aide d’une expression lambda :

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Notez que l’utilisation d’une expression lambda de cette manière implique un coût de performance réduit, car l’expression lambda doit être évaluée pour chaque appel. Bien que le coût des performances soit faible et n’affecte normalement pas une application, les coûts peuvent s’accumuler lorsqu’il y a de nombreuses notifications de modification. Toutefois, l’avantage de cette approche est qu’elle assure la sécurité des types au moment de la compilation et la prise en charge de la refactorisation lors du changement de nom des propriétés.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interaction de l’interface utilisateur à l’aide des commandes et des comportements

Dans Mobile Apps, les actions sont généralement appelées en réponse à une action de l’utilisateur, telle qu’un clic de bouton, qui peut être implémentée en créant un gestionnaire d’événements dans le fichier code-behind. Toutefois, dans le modèle MVVM, la responsabilité de l’implémentation de l’action réside dans le modèle de vue et le placement du code dans le code-behind doit être évité.

Les commandes offrent un moyen pratique de représenter des actions qui peuvent être liées à des contrôles dans l’interface utilisateur. Ils encapsulent le code qui implémente l’action et aident à le conserver découplé de sa représentation visuelle dans la vue. Xamarin. Forms comprend des contrôles qui peuvent être connectés de façon déclarative à une commande, et ces contrôles appellent la commande lorsque l’utilisateur interagit avec le contrôle.

Les comportements permettent également de connecter de façon déclarative des contrôles à une commande. Toutefois, les comportements peuvent être utilisés pour appeler une action associée à une plage d’événements déclenchés par un contrôle. Par conséquent, les comportements traitent un grand nombre des mêmes scénarios que les contrôles activés pour les commandes, tout en fournissant un degré de flexibilité et de contrôle plus élevé. En outre, les comportements peuvent également être utilisés pour associer des objets ou des méthodes de commande à des contrôles qui n’ont pas été spécifiquement conçus pour interagir avec les commandes.

### <a name="implementing-commands"></a>Implémentation de commandes

Les modèles d’affichage exposent généralement des propriétés de commande, pour la liaison à partir de la vue, qui sont des instances d’objets qui implémentent l’interface `ICommand`. Un certain nombre de contrôles Xamarin. Forms fournissent une propriété `Command`, qui peut être liée aux données d’un objet `ICommand` fourni par le modèle de vue. L’interface `ICommand` définit une méthode `Execute`, qui encapsule l’opération elle-même, une méthode `CanExecute`, qui indique si la commande peut être appelée, et un événement `CanExecuteChanged` qui se produit lorsque des modifications se produisent et qui affectent l’exécution de la commande. Les classes [`Command`](xref:Xamarin.Forms.Command) et [`Command<T>`](xref:Xamarin.Forms.Command) , fournies par Xamarin. Forms, implémentent l’interface `ICommand`, où `T` est le type des arguments pour `Execute` et `CanExecute`.

Dans un modèle de vue, il doit y avoir un objet de type [`Command`](xref:Xamarin.Forms.Command) ou [`Command<T>`](xref:Xamarin.Forms.Command) pour chaque propriété publique dans le modèle de vue de type `ICommand`. Le constructeur `Command` ou `Command<T>` requiert un objet de rappel `Action` appelé lorsque la méthode `ICommand.Execute` est appelée. La méthode `CanExecute` est un paramètre de constructeur facultatif, et est un `Func` qui retourne un `bool`.

Le code suivant montre comment une instance de [`Command`](xref:Xamarin.Forms.Command) , qui représente une commande Register, est construite en spécifiant un délégué à la méthode de modèle de vue `Register` :

```csharp
public ICommand RegisterCommand => new Command(Register);
```

La commande est exposée à la vue via une propriété qui retourne une référence à un `ICommand`. Lorsque la méthode `Execute` est appelée sur l’objet [`Command`](xref:Xamarin.Forms.Command) , elle transfère simplement l’appel à la méthode dans le modèle de vue via le délégué spécifié dans le constructeur `Command`.

Une méthode asynchrone peut être appelée par une commande à l’aide des mots clés `async` et `await` lors de la spécification du délégué `Execute` de la commande. Cela indique que le rappel est un `Task` et doit être attendu. Par exemple, le code suivant montre comment une instance de [`Command`](xref:Xamarin.Forms.Command) , qui représente une commande de connexion, est construite en spécifiant un délégué à la méthode de modèle de vue `SignInAsync` :

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Les paramètres peuvent être passés aux actions `Execute` et `CanExecute` à l’aide de la classe [`Command<T>`](xref:Xamarin.Forms.Command) pour instancier la commande. Par exemple, le code suivant montre comment une instance de `Command<T>` est utilisée pour indiquer que la méthode `NavigateAsync` requiert un argument de type `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

Dans les classes [`Command`](xref:Xamarin.Forms.Command) et [`Command<T>`](xref:Xamarin.Forms.Command) , le délégué à la méthode `CanExecute` dans chaque constructeur est facultatif. Si un délégué n’est pas spécifié, le `Command` retourne `true` pour `CanExecute`. Toutefois, le modèle de vue peut indiquer une modification de l’état de la `CanExecute` de la commande en appelant la méthode `ChangeCanExecute` sur l’objet `Command`. Cela entraîne le déclenchement de l’événement `CanExecuteChanged`. Tous les contrôles de l’interface utilisateur qui sont liés à la commande mettent à jour leur état activé pour refléter la disponibilité de la commande liée aux données.

#### <a name="invoking-commands-from-a-view"></a>Appel de commandes à partir d’une vue

L’exemple de code suivant montre comment un [`Grid`](xref:Xamarin.Forms.Grid) dans le `LoginView` se lie au `RegisterCommand` dans la classe `LoginViewModel` à l’aide d’une instance [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) :

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Un paramètre de commande peut également être défini éventuellement à l’aide de la propriété [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) . Le type de l’argument attendu est spécifié dans les méthodes cibles `Execute` et `CanExecute`. Le [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) appellera automatiquement la commande cible lorsque l’utilisateur interagit avec le contrôle attaché. Le paramètre de commande, s’il est fourni, est passé comme argument au délégué `Execute` de la commande.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implémentation des comportements

Les comportements permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. En effet, vous implémentez les fonctionnalités dans une classe de comportement et les attachez au contrôle comme si elles en faisaient partie. Les comportements vous permettent d’implémenter du code que vous devriez normalement écrire comme code-behind, car il interagit directement avec l’API du contrôle, de telle sorte qu’il peut être attaché de manière concise au contrôle et empaqueté pour une réutilisation sur plusieurs vues ou applications. Dans le contexte de MVVM, les comportements sont une approche utile pour connecter des contrôles à des commandes.

Un comportement attaché à un contrôle par le biais de propriétés jointes est connu sous le nom de *comportement attaché*. Le comportement peut ensuite utiliser l’API exposée de l’élément auquel il est attaché pour ajouter des fonctionnalités à ce contrôle, ou à d’autres contrôles, dans l’arborescence d’éléments visuels de la vue. L’application mobile eShopOnContainers contient la classe `LineColorBehavior`, qui est un comportement attaché. Pour plus d’informations sur ce comportement, consultez [affichage des erreurs de validation](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Un comportement Xamarin. Forms est une classe qui dérive de la classe [`Behavior`](xref:Xamarin.Forms.Behavior) ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) , où `T` est le type du contrôle auquel le comportement doit s’appliquer. Ces classes fournissent des méthodes `OnAttachedTo` et `OnDetachingFrom`, qui doivent être substituées pour fournir une logique qui sera exécutée lorsque le comportement est attaché et détaché des contrôles.

Dans l’application mobile eShopOnContainers, la classe `BindableBehavior<T>` dérive de la classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) . L’objectif de la classe `BindableBehavior<T>` est de fournir une classe de base pour les comportements Xamarin. Forms qui requièrent que la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du comportement soit définie sur le contrôle attaché.

La classe `BindableBehavior<T>` fournit une méthode substituable `OnAttachedTo` qui définit la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du comportement, et une méthode `OnDetachingFrom` substituable qui nettoie le `BindingContext`. De plus, la classe stocke une référence au contrôle attaché dans la propriété `AssociatedObject`.

L’application mobile eShopOnContainers comprend une classe `EventToCommandBehavior`, qui exécute une commande en réponse à un événement qui se produit. Cette classe dérive de la classe `BindableBehavior<T>` afin que le comportement puisse se lier à un `ICommand` spécifié par une propriété `Command` et l’exécuter lorsque le comportement est consommé. L’exemple de code suivant illustre la classe `EventToCommandBehavior` :

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

Les méthodes `OnAttachedTo` et `OnDetachingFrom` sont utilisées pour inscrire et annuler l’inscription d’un gestionnaire d’événements pour l’événement défini dans la propriété `EventName`. Ensuite, lorsque l’événement se déclenche, la méthode `OnFired` est appelée, ce qui exécute la commande.

L’avantage de l’utilisation de la `EventToCommandBehavior` pour exécuter une commande lorsqu’un événement se déclenche, c’est que les commandes peuvent être associées à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. En outre, ce code déplace le code de gestion des événements pour afficher les modèles, où il peut être testé.

#### <a name="invoking-behaviors-from-a-view"></a>Appel de comportements à partir d’une vue

La `EventToCommandBehavior` est particulièrement utile pour attacher une commande à un contrôle qui ne prend pas en charge les commandes. Par exemple, le `ProfileView` utilise le `EventToCommandBehavior` pour exécuter le `OrderDetailCommand` lorsque l’événement [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se déclenche sur le [`ListView`](xref:Xamarin.Forms.ListView) qui répertorie les commandes de l’utilisateur, comme illustré dans le code suivant :

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

Au moment de l’exécution, le `EventToCommandBehavior` répondra à l’interaction avec le [`ListView`](xref:Xamarin.Forms.ListView). Lorsqu’un élément est sélectionné dans la `ListView`, l’événement [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se déclenche, ce qui entraîne l’exécution de la `OrderDetailCommand` dans le `ProfileViewModel`. Par défaut, les arguments d’événement pour l’événement sont passés à la commande. Ces données sont converties à mesure qu’elles sont passées entre la source et la cible par le convertisseur spécifié dans la propriété `EventArgsConverter`, qui retourne la [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) du `ListView` à partir de la [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs). Par conséquent, lorsque le `OrderDetailCommand` est exécuté, le `Order` sélectionné est passé en tant que paramètre à l’action inscrite.

Pour plus d’informations sur les comportements, consultez [comportements](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Résumé

Le modèle MVVM (Model-View-ViewModel) permet de séparer correctement la logique métier et de présentation d’une application à partir de son interface utilisateur. Maintenir une séparation nette entre la logique d’application et l’interface utilisateur permet de résoudre de nombreux problèmes de développement et peut rendre une application plus facile à tester, à gérer et à évoluer. Cela peut également améliorer les opportunités de réutilisation de code et permet aux développeurs et aux concepteurs d’interface utilisateur de collaborer plus facilement lors du développement de leurs parties respectives d’une application.

À l’aide du modèle MVVM, l’interface utilisateur de l’application et la présentation et la logique métier sous-jacentes sont divisées en trois classes distinctes : la vue, qui encapsule l’interface utilisateur et la logique de l’interface utilisateur. le modèle de vue, qui encapsule la logique et l’état de la présentation ; et le modèle, qui encapsule la logique métier et les données de l’application.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
