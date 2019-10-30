---
title: Injection de dépendances
description: Ce chapitre explique comment l’application mobile eShopOnContainers utilise l’injection de dépendances pour découpler des types concrets du code qui dépend de ces types.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 975b32610b4b496e329c5c5a29b79efd2874d8cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029502"
---
# <a name="dependency-injection"></a>Injection de dépendances

En général, un constructeur de classe est appelé lors de l’instanciation d’un objet, et toutes les valeurs requises par l’objet sont passées en tant qu’arguments au constructeur. Il s’agit d’un exemple d’injection de dépendances, et spécifiquement appelé *injection de constructeur*. Les dépendances requises par l’objet sont injectées dans le constructeur.

En spécifiant des dépendances comme types d’interfaces, l’injection de dépendances permet de découpler les types concrets du code qui dépend de ces types. Elle utilise généralement un conteneur qui contient une liste d’inscriptions et de mappages entre les interfaces et les types abstraits, ainsi que les types concrets qui implémentent ou étendent ces types.

Il existe également d’autres types d’injection de dépendances, tels que l' *injection d’accesseur Set de propriété*et l' *injection d’appel de méthode*, mais ils sont moins fréquents. Par conséquent, ce chapitre se concentre uniquement sur l’exécution de l’injection de constructeur avec un conteneur d’injection de dépendance.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Présentation de l’injection de dépendances

L’injection de dépendances est une version spécialisée du modèle d’inversion de contrôle (IoC, inversion of Control), où la préoccupation est le processus d’obtention de la dépendance requise. Avec l’injection de dépendances, une autre classe est chargée d’injecter les dépendances dans un objet au moment de l’exécution. L’exemple de code suivant montre comment la classe `ProfileViewModel` est structurée lors de l’utilisation de l’injection de dépendances :

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

Le constructeur `ProfileViewModel` reçoit une instance `IOrderService` en tant qu’argument, injectée par une autre classe. La seule dépendance dans la classe `ProfileViewModel` est sur le type interface. Par conséquent, la classe `ProfileViewModel` n’a aucune connaissance de la classe responsable de l’instanciation de l’objet `IOrderService`. La classe qui est chargée d’instancier l’objet `IOrderService`, et de l’insérer dans la classe `ProfileViewModel`, porte le nom de *conteneur d’injection de dépendances*.

Les conteneurs d’injection de dépendances réduisent le couplage entre les objets en fournissant une fonctionnalité permettant d’instancier des instances de classe et de gérer leur durée de vie en fonction de la configuration du conteneur. Lors de la création d’objets, le conteneur injecte toutes les dépendances que l’objet requiert dans celui-ci. Si ces dépendances n’ont pas encore été créées, le conteneur crée et résout d’abord leurs dépendances.

> [!NOTE]
> L’injection de dépendances peut également être implémentée manuellement à l’aide de fabriques. Toutefois, l’utilisation d’un conteneur fournit des fonctionnalités supplémentaires telles que la gestion de la durée de vie et l’inscription par le biais de l’analyse d’assembly.

L’utilisation d’un conteneur d’injection de dépendances présente plusieurs avantages :

- Un conteneur supprime la nécessité pour une classe de localiser ses dépendances et de gérer ses durées de vie.
- Un conteneur autorise le mappage des dépendances implémentées sans affecter la classe.
- Un conteneur facilite la testabilité en autorisant les dépendances à être simulées.
- Un conteneur augmente la maintenabilité en permettant aux nouvelles classes d’être facilement ajoutées à l’application.

Dans le contexte d’une application Xamarin. Forms qui utilise MVVM, un conteneur d’injection de dépendances est généralement utilisé pour l’inscription et la résolution des modèles de vue, ainsi que pour l’inscription des services et leur injection dans des modèles de vue.

De nombreux conteneurs d’injection de dépendances sont disponibles, avec l’application mobile eShopOnContainers à l’aide de Autofac pour gérer l’instanciation du modèle de vue et des classes de service dans l’application. Autofac facilite la création d’applications faiblement couplées et fournit toutes les fonctionnalités couramment utilisées dans les conteneurs d’injection de dépendance, y compris les méthodes d’enregistrement des mappages de types et d’objets, la résolution des objets, la gestion des durées de vie des objets et l’injection objets dépendants dans des constructeurs d’objets qu’il résout. Pour plus d’informations sur Autofac, consultez [Autofac](https://autofac.readthedocs.io/en/latest/index.html) sur readthedocs.IO.

Dans Autofac, l’interface `IContainer` fournit le conteneur d’injection de dépendances. La figure 3-1 montre les dépendances lors de l’utilisation de ce conteneur, qui instancie un objet `IOrderService` et l’injecte dans la classe `ProfileViewModel`.

![](dependency-injection-images/dependencyinjection.png "Dependencies example when using dependency injection")

**Figure 3-1 :** Dépendances lors de l’utilisation de l’injection de dépendances

Au moment de l’exécution, le conteneur doit connaître l’implémentation de l’interface `IOrderService` qu’il doit instancier, avant de pouvoir instancier un objet `ProfileViewModel`. Cela implique les opérations suivantes :

- Conteneur déterminant comment instancier un objet qui implémente l’interface `IOrderService`. C’est ce que l’on appelle *l’inscription*.
- Conteneur qui instancie l’objet qui implémente l’interface `IOrderService` et l’objet `ProfileViewModel`. C’est ce que l’on appelle la *résolution*.

Finalement, l’application se terminera à l’aide de l’objet `ProfileViewModel` et sera disponible pour la garbage collection. À ce stade, le garbage collector doit supprimer l’instance de `IOrderService` si d’autres classes ne partagent pas la même instance.

> [!TIP]
> Écrire du code agnostique au conteneur. Essayez toujours d’écrire du code indépendant du conteneur pour découpler l’application du conteneur de dépendances spécifique utilisé.

## <a name="registration"></a>Inscription

Avant de pouvoir injecter des dépendances dans un objet, les types des dépendances doivent d’abord être inscrits auprès du conteneur. L’inscription d’un type implique généralement le passage du conteneur à une interface et un type concret qui implémente l’interface.

Il existe deux façons d’inscrire des types et des objets dans le conteneur par le biais du code :

- Inscrire un type ou un mappage avec le conteneur. Quand cela est nécessaire, le conteneur génère une instance du type spécifié.
- Inscrit un objet existant dans le conteneur en tant que singleton. Lorsque cela est nécessaire, le conteneur retourne une référence à l’objet existant.

> [!TIP]
> Les conteneurs d’injection de dépendances ne sont pas toujours appropriés. L’injection de dépendances introduit une complexité et des exigences supplémentaires qui peuvent ne pas être appropriées ou utiles pour les petites applications. Si une classe n’a pas de dépendances ou n’est pas une dépendance pour d’autres types, il peut s’avérer inutile de la placer dans le conteneur. En outre, si une classe a un seul ensemble de dépendances qui font partie intégrante du type et qui ne changera jamais, il peut s’avérer inutile de la placer dans le conteneur.

L’inscription de types qui nécessitent une injection de dépendances doit être effectuée dans une méthode unique dans une application, et cette méthode doit être appelée tôt dans le cycle de vie de l’application pour s’assurer que l’application connaît les dépendances entre ses classes. Dans l’application mobile eShopOnContainers, cette opération est effectuée par la classe `ViewModelLocator`, qui génère l’objet `IContainer` et est la seule classe de l’application qui contient une référence à cet objet. L’exemple de code suivant montre comment l’application mobile eShopOnContainers déclare l’objet `IContainer` dans la classe `ViewModelLocator` :

```csharp
private static IContainer _container;
```

Les types et les instances sont inscrits dans la méthode `RegisterDependencies` de la classe `ViewModelLocator`. Pour cela, vous devez d’abord créer une instance de `ContainerBuilder`, qui est illustrée dans l’exemple de code suivant :

```csharp
var builder = new ContainerBuilder();
```

Les types et les instances sont ensuite inscrits avec l’objet `ContainerBuilder`, et l’exemple de code suivant montre la forme la plus courante d’inscription de type :

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

La méthode `RegisterType` présentée ici mappe un type d’interface à un type concret. Elle indique au conteneur d’instancier un objet `RequestProvider` lorsqu’il instancie un objet qui requiert une injection d’un `IRequestProvider` par le biais d’un constructeur.

Les types concrets peuvent également être enregistrés directement sans mappage à partir d’un type d’interface, comme indiqué dans l’exemple de code suivant :

```csharp
builder.RegisterType<ProfileViewModel>();
```

Lorsque le type de `ProfileViewModel` est résolu, le conteneur injecte ses dépendances requises.

Autofac autorise également l’inscription d’instance, où le conteneur est responsable de la gestion d’une référence à une instance singleton d’un type. Par exemple, l’exemple de code suivant montre comment l’application mobile eShopOnContainers inscrit le type concret à utiliser lorsqu’une instance de `ProfileViewModel` requiert une instance `IOrderService` :

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

La méthode `RegisterType` présentée ici mappe un type d’interface à un type concret. La méthode `SingleInstance` configure l’inscription afin que chaque objet dépendant reçoive la même instance partagée. Par conséquent, une seule instance de `OrderService` existe dans le conteneur, qui est partagé par les objets qui requièrent une injection d’un `IOrderService` par le biais d’un constructeur.

L’inscription de l’instance peut également être effectuée à l’aide de la méthode `RegisterInstance`, qui est illustrée dans l’exemple de code suivant :

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

La méthode `RegisterInstance` présentée ici crée une nouvelle instance `OrderMockService` et l’enregistre avec le conteneur. Par conséquent, une seule instance de `OrderMockService` existe dans le conteneur, qui est partagé par les objets qui requièrent une injection d’un `IOrderService` par le biais d’un constructeur.

Après l’inscription du type et de l’instance, l’objet `IContainer` doit être généré, ce qui est illustré dans l’exemple de code suivant :

```csharp
_container = builder.Build();
```

L’appel de la méthode `Build` sur l’instance `ContainerBuilder` génère un nouveau conteneur d’injection de dépendances qui contient les inscriptions effectuées.

> [!TIP]
> Considérez une `IContainer` comme étant immuable. Bien que Autofac fournisse une méthode `Update` pour mettre à jour les inscriptions dans un conteneur existant, l’appel de cette méthode doit être évité dans la mesure du possible. Il y a des risques de modifier un conteneur après qu’il a été généré, en particulier si le conteneur a été utilisé. Pour plus d’informations, consultez [considérer un conteneur comme immuable](https://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) sur readthedocs.IO.

<a name="resolution" />

## <a name="resolution"></a>Résolution

Une fois qu’un type est inscrit, il peut être résolu ou injecté en tant que dépendance. Lorsqu’un type est résolu et que le conteneur doit créer une nouvelle instance, il injecte toutes les dépendances dans l’instance.

En règle générale, lorsqu’un type est résolu, l’une des trois situations suivantes se produit :

1. Si le type n’a pas été inscrit, le conteneur lève une exception.
1. Si le type a été inscrit en tant que singleton, le conteneur retourne l’instance singleton. S’il s’agit de la première fois que le type est appelé pour, le conteneur le crée si nécessaire, et conserve une référence à celui-ci.
1. Si le type n’a pas été inscrit en tant que singleton, le conteneur retourne une nouvelle instance et ne conserve pas de référence à celui-ci.

L’exemple de code suivant montre comment le type de `RequestProvider` précédemment inscrit avec Autofac peut être résolu :

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

Dans cet exemple, Autofac est invité à résoudre le type concret pour le type de `IRequestProvider`, ainsi que toutes les dépendances. En général, la méthode `Resolve` est appelée lorsqu’une instance d’un type spécifique est requise. Pour plus d’informations sur le contrôle de la durée de vie des objets résolus, consultez [gestion de la durée de vie des objets résolus](#managing_the_lifetime_of_resolved_objects).

L’exemple de code suivant montre comment l’application mobile eShopOnContainers instancie les types de modèle de vue et leurs dépendances :

```csharp
var viewModel = _container.Resolve(viewModelType);
```

Dans cet exemple, Autofac est invité à résoudre le type de modèle de vue pour un modèle de vue demandé, et le conteneur résout également toutes les dépendances. Lors de la résolution du type de `ProfileViewModel`, la dépendance à résoudre est un objet `IOrderService`. Par conséquent, Autofac construit tout d’abord un objet `OrderService` puis le passe au constructeur de la classe `ProfileViewModel`. Pour plus d’informations sur la façon dont l’application mobile eShopOnContainers construit les modèles de vue et les associe aux vues, consultez [création automatique d’un modèle de vue avec un localisateur de modèle de vue](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> L’inscription et la résolution des types avec un conteneur ont un coût en termes de performances en raison de l’utilisation par le conteneur de la réflexion pour créer chaque type, et plus particulièrement si les dépendances sont reconstruites pour chaque navigation entre les pages de l’application. S’il existe de nombreuses dépendances ou des dépendances profondes, le coût de création peut augmenter de manière significative.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gestion de la durée de vie des objets résolus

Après l’inscription d’un type, le comportement par défaut de Autofac consiste à créer une nouvelle instance du type inscrit chaque fois que le type est résolu, ou lorsque le mécanisme de dépendance injecte des instances dans d’autres classes. Dans ce scénario, le conteneur ne contient pas de référence à l’objet résolu. Toutefois, lors de l’inscription d’une instance, le comportement par défaut de Autofac consiste à gérer la durée de vie de l’objet en tant que singleton. Par conséquent, l’instance reste dans la portée pendant que le conteneur est dans la portée, et est supprimée lorsque le conteneur est hors de portée et est récupéré par le garbage collector, ou lorsque le code supprime explicitement le conteneur.

Une portée d’instance Autofac peut être utilisée pour spécifier le comportement Singleton d’un objet que Autofac crée à partir d’un type inscrit. Les étendues d’instance Autofac gèrent les durées de vie des objets instanciées par le conteneur. La portée d’instance par défaut pour la méthode `RegisterType` est l’étendue `InstancePerDependency`. Toutefois, la portée de `SingleInstance` peut être utilisée avec la méthode `RegisterType`, afin que le conteneur crée ou retourne une instance singleton d’un type lors de l’appel de la méthode `Resolve`. L’exemple de code suivant montre comment Autofac est chargé de créer une instance singleton de la classe `NavigationService` :

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La première fois que l’interface `INavigationService` est résolue, le conteneur crée un nouvel objet `NavigationService` et conserve une référence à celui-ci. Sur les résolutions suivantes de l’interface `INavigationService`, le conteneur retourne une référence à l’objet `NavigationService` qui a été créé précédemment.

> [!NOTE]
> L’étendue SingleInstance supprime les objets créés lorsque le conteneur est supprimé.

Autofac comprend des étendues d’instance supplémentaires. Pour plus d’informations, consultez étendue de l' [instance](https://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) sur readthedocs.IO.

## <a name="summary"></a>Récapitulatif

L’injection de dépendances permet de découpler des types concrets du code qui dépend de ces types. Elle utilise généralement un conteneur qui contient une liste d’inscriptions et de mappages entre les interfaces et les types abstraits, ainsi que les types concrets qui implémentent ou étendent ces types.

Autofac facilite la création d’applications faiblement couplées et fournit toutes les fonctionnalités couramment utilisées dans les conteneurs d’injection de dépendance, y compris les méthodes d’enregistrement des mappages de types et d’objets, la résolution des objets, la gestion des durées de vie des objets et l’injection objets dépendants dans des constructeurs d’objets qu’il résout.

## <a name="related-links"></a>Liens associés

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
