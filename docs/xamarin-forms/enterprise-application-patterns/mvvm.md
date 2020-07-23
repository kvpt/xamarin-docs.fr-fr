---
title: Modèle Model-View-ViewModel
description: Ce chapitre explique comment l’application mobile eShopOnContainers utilise le modèle MVVM pour séparer correctement la logique métier et de présentation de l’application à partir de son interface utilisateur.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cc0528e7696130a772e93be67526ea9d6b10373f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936524"
---
# <a name="the-model-view-viewmodel-pattern"></a>Modèle Model-View-ViewModel

L' Xamarin.Forms expérience du développeur implique généralement la création d’une interface utilisateur en XAML, puis l’ajout du code-behind qui fonctionne sur l’interface utilisateur. À mesure que les applications sont modifiées et qu’elles augmentent la taille et la portée, des problèmes de maintenance complexes peuvent survenir. Ces problèmes incluent le couplage étroit entre les contrôles de l’interface utilisateur et la logique métier, ce qui augmente le coût des modifications apportées à l’interface utilisateur et la difficulté du test unitaire de ce code.

Le modèle MVVM (Model-View-ViewModel) permet de séparer correctement la logique métier et de présentation d’une application à partir de son interface utilisateur. Maintenir une séparation nette entre la logique d’application et l’interface utilisateur permet de résoudre de nombreux problèmes de développement et peut rendre une application plus facile à tester, à gérer et à évoluer. Cela peut également améliorer les opportunités de réutilisation de code et permet aux développeurs et aux concepteurs d’interface utilisateur de collaborer plus facilement lors du développement de leurs parties respectives d’une application.

## <a name="the-mvvm-pattern"></a>Le modèle MVVM

Il existe trois composants principaux dans le modèle MVVM : le modèle, la vue et le modèle de vue. Chacun remplit un rôle distinct. La figure 2-1 montre les relations entre les trois composants.

![Modèle MVVM](mvvm-images/mvvm.png)

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

Dans une Xamarin.Forms application, une vue est généralement une classe dérivée de ou dérivée de [`Page`](xref:Xamarin.Forms.Page) [`ContentView`](xref:Xamarin.Forms.ContentView) . Toutefois, les vues peuvent également être représentées par un modèle de données, qui spécifie les éléments d’interface utilisateur à utiliser pour représenter visuellement un objet lorsqu’il est affiché. Un modèle de données en tant que vue n’a pas de code-behind et est conçu pour être lié à un type de modèle de vue spécifique.

> [!TIP]
> Évitez d’activer et de désactiver les éléments d’interface utilisateur dans le code-behind. Vérifiez que les modèles de vue sont responsables de la définition des modifications d’état logique qui affectent certains aspects de l’affichage de la vue, par exemple si une commande est disponible ou si une opération est en attente. Par conséquent, activez et désactivez les éléments d’interface utilisateur en liant les propriétés du modèle de vue, plutôt que de les activer et de les désactiver dans le code-behind.

Il existe plusieurs options pour exécuter du code sur le modèle de vue en réponse aux interactions sur la vue, telles qu’un clic de bouton ou une sélection d’élément. Si un contrôle prend en charge les commandes, la propriété du contrôle `Command` peut être liée aux données d’une `ICommand` propriété sur le modèle de vue. Lorsque la commande du contrôle est appelée, le code dans le modèle de vue est exécuté. En plus des commandes, les comportements peuvent être attachés à un objet dans la vue et peuvent écouter l’appel d’une commande ou déclencher l’événement. En réponse, le comportement peut appeler un `ICommand` sur le modèle de vue ou une méthode sur le modèle de vue.

### <a name="viewmodel"></a>ViewModel

Le modèle de vue implémente les propriétés et les commandes auxquelles la vue peut être liée, et notifie la vue de toute modification d’état via des événements de notification de modification. Les propriétés et les commandes que le modèle de vue fournit définissent les fonctionnalités que l’interface utilisateur peut offrir, mais la vue détermine la manière dont cette fonctionnalité doit être affichée.

> [!TIP]
> Maintenir la réactivité de l’interface utilisateur avec les opérations asynchrones. Les applications mobiles doivent garder le thread d’interface utilisateur débloqué pour améliorer la perception des performances de l’utilisateur. Par conséquent, dans le modèle de vue, utilisez des méthodes asynchrones pour les opérations d’e/s et déclenchez des événements pour notifier de façon asynchrone des vues de modifications de propriété.

Le modèle de vue est également chargé de coordonner les interactions de la vue avec toutes les classes de modèle requises. Il y a généralement une relation un-à-plusieurs entre le modèle de vue et les classes de modèle. Le modèle de vue peut choisir d’exposer des classes de modèle directement à la vue afin que les contrôles de la vue puissent se lier directement aux données. Dans ce cas, les classes de modèle devront être conçues pour prendre en charge la liaison de données et les événements de notification de modification.

Chaque modèle de vue fournit des données à partir d’un modèle dans un formulaire que la vue peut facilement consommer. Pour ce faire, le modèle de vue effectue parfois la conversion des données. Le fait de placer cette conversion de données dans le modèle de vue est une bonne idée, car elle fournit des propriétés auxquelles la vue peut se lier. Par exemple, le modèle de vue peut combiner les valeurs de deux propriétés pour faciliter l’affichage par la vue.

> [!TIP]
> Centralisez les conversions de données dans une couche de conversion. Il est également possible d’utiliser des convertisseurs comme une couche de conversion de données distincte qui se situe entre le modèle de vue et la vue. Cela peut être nécessaire, par exemple, lorsque les données requièrent une mise en forme spéciale que le modèle de vue ne fournit pas.

Pour que le modèle de vue participe à la liaison de données bidirectionnelle avec la vue, ses propriétés doivent déclencher l' `PropertyChanged` événement. Les modèles de vue répondent à cette exigence en implémentant l' `INotifyPropertyChanged` interface et en déclenchant l' `PropertyChanged` événement lorsqu’une propriété est modifiée.

Pour les collections, le mode convivial `ObservableCollection<T>` est fourni. Cette collection implémente une notification de modification de la collection, ce qui évite au développeur d’avoir à implémenter l' `INotifyCollectionChanged` interface sur les collections.

### <a name="model"></a>Modèle

Les classes de modèle sont des classes non visuelles qui encapsulent les données de l’application. Par conséquent, le modèle peut être considéré comme représentant le modèle de domaine de l’application, qui comprend généralement un modèle de données avec la logique métier et la logique de validation. Parmi les exemples d’objets de modèle figurent les objets DTO (Data Transfer Objects), les anciens objets CLR (Plains) et les objets d’entité et de proxy générés.

Les classes de modèle sont généralement utilisées conjointement avec les services ou les référentiels qui encapsulent l’accès aux données et la mise en cache.

## <a name="connecting-view-models-to-views"></a>Connexion de modèles de vue à des vues

Les modèles de vue peuvent être connectés à des vues à l’aide des fonctionnalités de liaison de données de Xamarin.Forms . Il existe de nombreuses approches qui peuvent être utilisées pour construire des vues et afficher des modèles et les associer au moment de l’exécution. Ces approches se répartissent en deux catégories, appelées première composition de vue et vue de la composition du modèle de vue. Le choix entre la première composition de la vue et de la vue de la composition du modèle est un problème de préférence et de complexité. Toutefois, toutes les approches partagent le même objectif, pour que la vue ait un modèle de vue affecté à sa propriété BindingContext.

Avec la composition View First, l’application est conceptuellement composée de vues qui se connectent aux modèles de vue dont elles dépendent. Le principal avantage de cette approche est qu’il permet de créer facilement des applications testables faiblement couplées, car les modèles de vue n’ont aucune dépendance vis-à-vis des vues elles-mêmes. Il est également facile de comprendre la structure de l’application en suivant sa structure visuelle, plutôt que d’avoir à effectuer le suivi de l’exécution du code pour comprendre comment les classes sont créées et associées. En outre, View First construction s’aligne avec le Xamarin.Forms système de navigation chargé de construire des pages lorsque la navigation se produit, ce qui rend une composition de modèle d’affichage complexe et mal alignée avec la plateforme.

Avec la première composition de modèle de vue, l’application est conceptuellement composée de modèles de vue, avec un service chargé de localiser la vue d’un modèle de vue. La première composition du modèle de vue semble plus naturelle pour certains développeurs, étant donné que la création de la vue peut être extraite, ce qui leur permet de se concentrer sur la structure logique non-interface utilisateur de l’application. En outre, il permet de créer des modèles de vue par d’autres modèles de vue. Toutefois, cette approche est souvent complexe et peut devenir difficile à comprendre comment les différentes parties de l’application sont créées et associées.

> [!TIP]
> Gardez les modèles de vue et les vues indépendants. La liaison de vues à une propriété dans une source de données doit être la dépendance principale de la vue sur le modèle de vue correspondant. En particulier, ne référencez pas les types de vue, tels que [`Button`](xref:Xamarin.Forms.Button) et [`ListView`](xref:Xamarin.Forms.ListView) , à partir des modèles de vue. En suivant les principes décrits ici, les modèles de vue peuvent être testés de manière isolée, ce qui réduit la probabilité d’erreurs logicielles en limitant l’étendue.

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

Lorsque le [`ContentPage`](xref:Xamarin.Forms.ContentPage) est créé, une instance du `LoginViewModel` est construite automatiquement et définie comme étant le de la vue [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) .

Cette construction et assignation déclarative du modèle de vue par la vue présente l’avantage d’être simple, mais présente l’inconvénient qu’elle nécessite un constructeur par défaut (sans paramètre) dans le modèle de vue.

### <a name="creating-a-view-model-programmatically"></a>Création d’un modèle de vue par programme

Une vue peut avoir du code dans le fichier code-behind qui produit le modèle de vue qui est assigné à sa [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété. Cela est souvent accompli dans le constructeur de la vue, comme illustré dans l’exemple de code suivant :

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

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Création automatique d’un modèle de vue avec un localisateur de modèle de vue

Un localisateur de modèle de vue est une classe personnalisée qui gère l’instanciation des modèles de vue et leur association aux vues. Dans l’application mobile eShopOnContainers, la `ViewModelLocator` classe a une propriété jointe, `AutoWireViewModel` , qui est utilisée pour associer des modèles de vue à des vues. Dans le XAML de la vue, cette propriété jointe a la valeur true pour indiquer que le modèle de vue doit être connecté automatiquement à la vue, comme illustré dans l’exemple de code suivant :

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

La `AutoWireViewModel` propriété est une propriété pouvant être liée qui est initialisée avec la valeur false, et lorsque sa valeur change, le `OnAutoWireViewModelChanged` Gestionnaire d’événements est appelé. Cette méthode résout le modèle de vue pour la vue. L’exemple de code suivant montre comment procéder :

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

La `OnAutoWireViewModelChanged` méthode tente de résoudre le modèle de vue à l’aide d’une approche basée sur une convention. Cette Convention suppose que :

- Les modèles d’affichage se trouvent dans le même assembly que les types de vues.
- Les vues se trouvent dans un. Espace de noms enfant views.
- Les modèles de vue sont dans un. Espace de noms enfant ViewModels.
- Les noms de modèle de vue correspondent aux noms de vue et se terminent par « ViewModel ».

Enfin, la `OnAutoWireViewModelChanged` méthode définit le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du type de vue sur le type de modèle de vue résolu. Pour plus d’informations sur la résolution du type de modèle de vue, consultez [Resolution](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Cette approche présente l’avantage qu’une application a une seule classe qui est responsable de l’instanciation des modèles de vue et de leur connexion aux vues.

> [!TIP]
> Utilisez un localisateur de modèle de vue pour faciliter la substitution. Un localisateur de modèle de vue peut également être utilisé comme point de substitution pour d’autres implémentations de dépendances, telles que le test unitaire ou les données au moment de la conception.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Mise à jour des vues en réponse aux modifications dans le modèle ou le modèle de vue sous-jacent

Toutes les classes de modèle et de modèle d’affichage qui sont accessibles à une vue doivent implémenter l' `INotifyPropertyChanged` interface. L’implémentation de cette interface dans un modèle de vue ou une classe de modèle permet à la classe de fournir des notifications de modifications à tous les contrôles liés aux données dans la vue lorsque la valeur de propriété sous-jacente est modifiée.

Les applications doivent être conçues pour l’utilisation correcte de la notification de modification de propriété, en répondant aux exigences suivantes :

- Déclenchement systématique d’un `PropertyChanged` événement si la valeur d’une propriété publique change. Ne supposez pas que le déclenchement de l' `PropertyChanged` événement peut être ignoré en raison de la connaissance de la liaison XAML.
- En déclenchant toujours un `PropertyChanged` événement pour toutes les propriétés calculées dont les valeurs sont utilisées par d’autres propriétés dans le modèle ou le modèle de vue.
- Déclenche toujours l' `PropertyChanged` événement à la fin de la méthode qui effectue une modification de propriété ou lorsque l’objet est dans un État sûr. Le déclenchement de l’événement interrompt l’opération en appelant les gestionnaires de l’événement de façon synchrone. Si cela se produit au milieu d’une opération, il peut exposer l’objet aux fonctions de rappel lorsqu’il est dans un État non sécurisé, partiellement mis à jour. En outre, il est possible que les modifications en cascade soient déclenchées par des `PropertyChanged` événements. Les modifications en cascade nécessitent généralement l’achèvement des mises à jour avant que le changement en cascade ne soit sécurisé.
- Ne déclenche jamais d' `PropertyChanged` événement si la propriété ne change pas. Cela signifie que vous devez comparer les valeurs anciennes et nouvelles avant de déclencher l' `PropertyChanged` événement.
- Ne déclenchez jamais l' `PropertyChanged` événement pendant le constructeur d’un modèle de vue si vous initialisez une propriété. Les contrôles liés aux données dans la vue ne sont pas abonnés à recevoir des notifications de modification à ce stade.
- Ne déclenchez jamais plus d’un `PropertyChanged` événement avec le même argument de nom de propriété dans un appel synchrone unique d’une méthode publique d’une classe. Par exemple, à partir d’une `NumberOfItems` propriété dont le magasin de stockage est le `_numberOfItems` champ, si une méthode incrémente `_numberOfItems` 50 fois pendant l’exécution d’une boucle, elle doit uniquement déclencher la notification de modification de propriété sur la `NumberOfItems` propriété une fois que tout le travail est terminé. Pour les méthodes asynchrones, déclenchez l' `PropertyChanged` événement pour un nom de propriété donné dans chaque segment synchrone d’une chaîne de continuation asynchrone.

L’application mobile eShopOnContainers utilise la `ExtendedBindableObject` classe pour fournir des notifications de modification, qui est illustrée dans l’exemple de code suivant :

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

La classe de Xamarin. Form [`BindableObject`](xref:Xamarin.Forms.BindableObject) implémente l' `INotifyPropertyChanged` interface et fournit une [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) méthode. La `ExtendedBindableObject` classe fournit la `RaisePropertyChanged` méthode pour appeler la notification de modification de propriété et, dans ce cas, utilise la fonctionnalité fournie par la `BindableObject` classe.

Chaque classe de modèle de vue de l’application mobile eShopOnContainers dérive de la `ViewModelBase` classe, qui, à son tour, dérive de la `ExtendedBindableObject` classe. Par conséquent, chaque classe de modèle de vue utilise la `RaisePropertyChanged` méthode de la `ExtendedBindableObject` classe pour fournir une notification de modification de propriété. L’exemple de code suivant montre comment l’application mobile eShopOnContainers appelle la notification de modification de propriété à l’aide d’une expression lambda :

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

Les commandes offrent un moyen pratique de représenter des actions qui peuvent être liées à des contrôles dans l’interface utilisateur. Ils encapsulent le code qui implémente l’action et aident à le conserver découplé de sa représentation visuelle dans la vue. Xamarin.Formscomprend des contrôles qui peuvent être connectés de façon déclarative à une commande, et ces contrôles appellent la commande lorsque l’utilisateur interagit avec le contrôle.

Les comportements permettent également de connecter de façon déclarative des contrôles à une commande. Toutefois, les comportements peuvent être utilisés pour appeler une action associée à une plage d’événements déclenchés par un contrôle. Par conséquent, les comportements traitent un grand nombre des mêmes scénarios que les contrôles activés pour les commandes, tout en fournissant un degré de flexibilité et de contrôle plus élevé. En outre, les comportements peuvent également être utilisés pour associer des objets ou des méthodes de commande à des contrôles qui n’ont pas été spécifiquement conçus pour interagir avec les commandes.

### <a name="implementing-commands"></a>Implémentation de commandes

Les modèles d’affichage exposent généralement des propriétés de commande, pour la liaison à partir de la vue, qui sont des instances d’objets qui implémentent l' `ICommand` interface. Un certain nombre de Xamarin.Forms contrôles fournissent une `Command` propriété, qui peut être liée aux données d’un `ICommand` objet fourni par le modèle de vue. L' `ICommand` interface définit une `Execute` méthode, qui encapsule l’opération elle-même, une `CanExecute` méthode, qui indique si la commande peut être appelée, et un `CanExecuteChanged` événement qui se produit lorsque des modifications se produisent et qui affectent l’exécution de la commande. Les [`Command`](xref:Xamarin.Forms.Command) [`Command<T>`](xref:Xamarin.Forms.Command) classes et, fournies par Xamarin.Forms , implémentent l' `ICommand` interface, où `T` est le type des arguments de `Execute` et `CanExecute` .

Dans un modèle de vue, il doit y avoir un objet de type [`Command`](xref:Xamarin.Forms.Command) ou [`Command<T>`](xref:Xamarin.Forms.Command) pour chaque propriété publique dans le modèle de vue de type `ICommand` . Le `Command` `Command<T>` constructeur ou requiert un `Action` objet de rappel appelé lorsque la `ICommand.Execute` méthode est appelée. La `CanExecute` méthode est un paramètre de constructeur facultatif et est un `Func` qui retourne un `bool` .

Le code suivant montre comment une [`Command`](xref:Xamarin.Forms.Command) instance, qui représente une commande Register, est construite en spécifiant un délégué à la `Register` méthode de modèle de vue :

```csharp
public ICommand RegisterCommand => new Command(Register);
```

La commande est exposée à la vue via une propriété qui retourne une référence à un `ICommand` . Lorsque la `Execute` méthode est appelée sur l' [`Command`](xref:Xamarin.Forms.Command) objet, elle transfère simplement l’appel à la méthode dans le modèle de vue via le délégué spécifié dans le `Command` constructeur.

Une méthode asynchrone peut être appelée par une commande à l’aide des `async` `await` Mots clés et lors de la spécification du délégué de la commande `Execute` . Cela indique que le rappel est un `Task` et doit être attendu. Par exemple, le code suivant montre comment une [`Command`](xref:Xamarin.Forms.Command) instance, qui représente une commande de connexion, est construite en spécifiant un délégué à la `SignInAsync` méthode de modèle de vue :

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Les paramètres peuvent être passés aux `Execute` actions et à l' `CanExecute` aide de la [`Command<T>`](xref:Xamarin.Forms.Command) classe pour instancier la commande. Par exemple, le code suivant montre comment une `Command<T>` instance est utilisée pour indiquer que la `NavigateAsync` méthode nécessitera un argument de type `string` :

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

Dans les [`Command`](xref:Xamarin.Forms.Command) classes et [`Command<T>`](xref:Xamarin.Forms.Command) , le délégué à la `CanExecute` méthode dans chaque constructeur est facultatif. Si un délégué n’est pas spécifié, le `Command` retourne `true` pour `CanExecute` . Toutefois, le modèle de vue peut indiquer une modification de l’état de la commande `CanExecute` en appelant la `ChangeCanExecute` méthode sur l' `Command` objet. Cela entraîne le `CanExecuteChanged` déclenchement de l’événement. Tous les contrôles de l’interface utilisateur qui sont liés à la commande mettent à jour leur état activé pour refléter la disponibilité de la commande liée aux données.

#### <a name="invoking-commands-from-a-view"></a>Appel de commandes à partir d’une vue

L’exemple de code suivant montre comment un [`Grid`](xref:Xamarin.Forms.Grid) dans `LoginView` lie à `RegisterCommand` dans la classe à l' `LoginViewModel` aide d’une [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) instance de :

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Un paramètre de commande peut également être défini éventuellement à l’aide de la [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriété. Le type de l’argument attendu est spécifié dans les `Execute` `CanExecute` méthodes cibles et. L’objet appellera [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) automatiquement la commande cible lorsque l’utilisateur interagit avec le contrôle attaché. Le paramètre de commande, s’il est fourni, est passé comme argument au délégué de la commande `Execute` .

### <a name="implementing-behaviors"></a>Implémentation des comportements

Les comportements permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. En effet, vous implémentez les fonctionnalités dans une classe de comportement et les attachez au contrôle comme si elles en faisaient partie. Les comportements vous permettent d’implémenter du code que vous devriez normalement écrire comme code-behind, car il interagit directement avec l’API du contrôle, de telle sorte qu’il peut être attaché de manière concise au contrôle et empaqueté pour une réutilisation sur plusieurs vues ou applications. Dans le contexte de MVVM, les comportements sont une approche utile pour connecter des contrôles à des commandes.

Un comportement attaché à un contrôle par le biais de propriétés jointes est connu sous le nom de *comportement attaché*. Le comportement peut ensuite utiliser l’API exposée de l’élément auquel il est attaché pour ajouter des fonctionnalités à ce contrôle, ou à d’autres contrôles, dans l’arborescence d’éléments visuels de la vue. L’application mobile eShopOnContainers contient la `LineColorBehavior` classe, qui est un comportement attaché. Pour plus d’informations sur ce comportement, consultez [affichage des erreurs de validation](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying-validation-errors).

Un Xamarin.Forms comportement est une classe qui dérive de la [`Behavior`](xref:Xamarin.Forms.Behavior) [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou, où `T` est le type du contrôle auquel le comportement doit s’appliquer. Ces classes fournissent `OnAttachedTo` des `OnDetachingFrom` méthodes et, qui doivent être substituées pour fournir une logique qui sera exécutée lorsque le comportement est attaché et détaché des contrôles.

Dans l’application mobile eShopOnContainers, la `BindableBehavior<T>` classe dérive de la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe. L’objectif de la `BindableBehavior<T>` classe est de fournir une classe de base pour les Xamarin.Forms comportements qui requièrent que le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du comportement soit défini sur le contrôle attaché.

La `BindableBehavior<T>` classe fournit une méthode substituable `OnAttachedTo` qui définit le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du comportement, et une méthode substituable `OnDetachingFrom` qui nettoie le `BindingContext` . De plus, la classe stocke une référence au contrôle attaché dans la propriété `AssociatedObject`.

L’application mobile eShopOnContainers comprend une `EventToCommandBehavior` classe, qui exécute une commande en réponse à un événement qui se produit. Cette classe dérive de la `BindableBehavior<T>` classe afin que le comportement puisse se lier à et exécuter un `ICommand` spécifié par une `Command` propriété lorsque le comportement est consommé. L’exemple de code suivant illustre la classe `EventToCommandBehavior` :

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

Les `OnAttachedTo` `OnDetachingFrom` méthodes et sont utilisées pour enregistrer et annuler l’inscription d’un gestionnaire d’événements pour l’événement défini dans la `EventName` propriété. Ensuite, lorsque l’événement se déclenche, la `OnFired` méthode est appelée, ce qui exécute la commande.

L’avantage de l’utilisation de `EventToCommandBehavior` pour exécuter une commande lorsqu’un événement se déclenche, c’est que les commandes peuvent être associées à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. En outre, ce code déplace le code de gestion des événements pour afficher les modèles, où il peut être testé.

#### <a name="invoking-behaviors-from-a-view"></a>Appel de comportements à partir d’une vue

`EventToCommandBehavior`Est particulièrement utile pour attacher une commande à un contrôle qui ne prend pas en charge les commandes. Par exemple, le `ProfileView` utilise `EventToCommandBehavior` pour exécuter `OrderDetailCommand` lorsque l' [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) événement se déclenche sur le [`ListView`](xref:Xamarin.Forms.ListView) qui répertorie les commandes de l’utilisateur, comme indiqué dans le code suivant :

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

Lors de l’exécution, le `EventToCommandBehavior` répond à l’interaction avec le [`ListView`](xref:Xamarin.Forms.ListView) . Lorsqu’un élément est sélectionné dans le `ListView` , l' [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) événement se déclenche, ce qui entraîne l’exécution `OrderDetailCommand` de dans le `ProfileViewModel` . Par défaut, les arguments d’événement pour l’événement sont passés à la commande. Ces données sont converties à mesure qu’elles sont passées entre la source et la cible par le convertisseur spécifié dans la `EventArgsConverter` propriété, qui retourne le de l’objet [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) de `ListView` l’objet [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) . Par conséquent, lorsque le `OrderDetailCommand` est exécuté, le sélectionné `Order` est passé en tant que paramètre à l’action inscrite.

Pour plus d’informations sur les comportements, consultez [comportements](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Résumé

Le modèle MVVM (Model-View-ViewModel) permet de séparer correctement la logique métier et de présentation d’une application à partir de son interface utilisateur. Maintenir une séparation nette entre la logique d’application et l’interface utilisateur permet de résoudre de nombreux problèmes de développement et peut rendre une application plus facile à tester, à gérer et à évoluer. Cela peut également améliorer les opportunités de réutilisation de code et permet aux développeurs et aux concepteurs d’interface utilisateur de collaborer plus facilement lors du développement de leurs parties respectives d’une application.

À l’aide du modèle MVVM, l’interface utilisateur de l’application et la présentation et la logique métier sous-jacentes sont divisées en trois classes distinctes : la vue, qui encapsule l’interface utilisateur et la logique de l’interface utilisateur. le modèle de vue, qui encapsule la logique et l’état de la présentation ; et le modèle, qui encapsule la logique métier et les données de l’application.

## <a name="related-links"></a>Liens associés

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
