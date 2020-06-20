---
title: Injection de dépendances
description: Ce chapitre explique comment l’application mobile eShopOnContainers utilise l’injection de dépendances pour découpler des types concrets du code qui dépend de ces types.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 99cac3c597cf1d7351d3214ce59fd0a0a0a46400
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572349"
---
# <a name="dependency-injection"></a>Injection de dépendances

En général, un constructeur de classe est appelé lors de l’instanciation d’un objet, et toutes les valeurs requises par l’objet sont passées en tant qu’arguments au constructeur. Il s’agit d’un exemple d’injection de dépendances, et spécifiquement appelé *injection de constructeur*. Les dépendances requises par l’objet sont injectées dans le constructeur.

En spécifiant des dépendances comme types d’interfaces, l’injection de dépendances permet de découpler les types concrets du code qui dépend de ces types. Elle utilise généralement un conteneur qui contient une liste d’inscriptions et de mappages entre les interfaces et les types abstraits, ainsi que les types concrets qui implémentent ou étendent ces types.

Il existe également d’autres types d’injection de dépendances, tels que l' *injection d’accesseur Set de propriété*et l' *injection d’appel de méthode*, mais ils sont moins fréquents. Par conséquent, ce chapitre se concentre uniquement sur l’exécution de l’injection de constructeur avec un conteneur d’injection de dépendance.

## <a name="introduction-to-dependency-injection"></a>Présentation de l’injection de dépendances

L’injection de dépendances est une version spécialisée du modèle d’inversion de contrôle (IoC, inversion of Control), où la préoccupation est le processus d’obtention de la dépendance requise. Avec l’injection de dépendances, une autre classe est chargée d’injecter les dépendances dans un objet au moment de l’exécution. L’exemple de code suivant montre comment la `ProfileViewModel` classe est structurée lors de l’utilisation de l’injection de dépendances :

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

Le `ProfileViewModel` constructeur reçoit une `IOrderService` instance en tant qu’argument, injecté par une autre classe. La seule dépendance dans la `ProfileViewModel` classe est sur le type d’interface. Par conséquent, la `ProfileViewModel` classe n’a aucune connaissance de la classe responsable de l’instanciation de l' `IOrderService` objet. La classe qui est chargée de l’instanciation de l' `IOrderService` objet et de son insertion dans la `ProfileViewModel` classe est connue sous le nom de *conteneur d’injection de dépendances*.

Les conteneurs d’injection de dépendances réduisent le couplage entre les objets en fournissant une fonctionnalité permettant d’instancier des instances de classe et de gérer leur durée de vie en fonction de la configuration du conteneur. Lors de la création d’objets, le conteneur injecte toutes les dépendances que l’objet requiert dans celui-ci. Si ces dépendances n’ont pas encore été créées, le conteneur crée et résout d’abord leurs dépendances.

> [!NOTE]
> L’injection de dépendances peut également être implémentée manuellement à l’aide de fabriques. Toutefois, l’utilisation d’un conteneur fournit des fonctionnalités supplémentaires telles que la gestion de la durée de vie et l’inscription par le biais de l’analyse d’assembly.

L’utilisation d’un conteneur d’injection de dépendances présente plusieurs avantages :

- Un conteneur supprime la nécessité pour une classe de localiser ses dépendances et de gérer ses durées de vie.
- Un conteneur autorise le mappage des dépendances implémentées sans affecter la classe.
- Un conteneur facilite la testabilité en autorisant les dépendances à être simulées.
- Un conteneur augmente la maintenabilité en permettant aux nouvelles classes d’être facilement ajoutées à l’application.

Dans le contexte d’une Xamarin.Forms application qui utilise MVVM, un conteneur d’injection de dépendances est généralement utilisé pour l’inscription et la résolution des modèles de vue, ainsi que pour l’inscription des services et leur injection dans des modèles de vue.

De nombreux conteneurs d’injection de dépendances sont disponibles, avec l’application mobile eShopOnContainers à l’aide de TinyIoC pour gérer l’instanciation du modèle de vue et des classes de service dans l’application. TinyIoC a été choisi après l’évaluation de plusieurs conteneurs différents, et offre des performances supérieures sur les plateformes mobiles par rapport à la majorité des conteneurs connus. Il facilite la création d’applications faiblement couplées et fournit toutes les fonctionnalités couramment utilisées dans les conteneurs d’injection de dépendances, notamment les méthodes d’enregistrement des mappages de types, la résolution des objets, la gestion des durées de vie des objets et l’injection d’objets dépendants dans des constructeurs d’objets qu’il résout. Pour plus d’informations sur TinyIoC, consultez [TinyIoC](https://github.com/grumpydev/TinyIoC/wiki) sur GitHub.com.

Dans TinyIoC, le `TinyIoCContainer` type fournit le conteneur d’injection de dépendances. La figure 3-1 montre les dépendances lors de l’utilisation de ce conteneur, qui instancie un `IOrderService` objet et l’injecte dans la `ProfileViewModel` classe.

![](dependency-injection-images/dependencyinjection.png "Dependencies example when using dependency injection")

**Figure 3-1 :** Dépendances lors de l’utilisation de l’injection de dépendances

Au moment de l’exécution, le conteneur doit connaître l’implémentation de l' `IOrderService` interface qu’il doit instancier, avant de pouvoir instancier un `ProfileViewModel` objet. Cela implique les opérations suivantes :

- Conteneur déterminant comment instancier un objet qui implémente l' `IOrderService` interface. C’est ce que l’on appelle *l’inscription*.
- Conteneur qui instancie l’objet qui implémente l' `IOrderService` interface et l' `ProfileViewModel` objet. C’est ce que l’on appelle la *résolution*.

Finalement, l’application se terminera à l’aide de l' `ProfileViewModel` objet et sera disponible pour garbage collection. À ce stade, le garbage collector doit supprimer l' `IOrderService` instance si d’autres classes ne partagent pas la même instance.

> [!TIP]
> Écrire du code agnostique au conteneur. Essayez toujours d’écrire du code indépendant du conteneur pour découpler l’application du conteneur de dépendances spécifique utilisé.

## <a name="registration"></a>Inscription

Avant de pouvoir injecter des dépendances dans un objet, les types des dépendances doivent d’abord être inscrits auprès du conteneur. L’inscription d’un type implique généralement le passage du conteneur à une interface et un type concret qui implémente l’interface.

Il existe deux façons d’inscrire des types et des objets dans le conteneur par le biais du code :

- Inscrire un type ou un mappage avec le conteneur. Quand cela est nécessaire, le conteneur génère une instance du type spécifié.
- Inscrit un objet existant dans le conteneur en tant que singleton. Lorsque cela est nécessaire, le conteneur retourne une référence à l’objet existant.

> [!TIP]
> Les conteneurs d’injection de dépendances ne sont pas toujours appropriés. L’injection de dépendances introduit une complexité et des exigences supplémentaires qui peuvent ne pas être appropriées ou utiles pour les petites applications. Si une classe n’a pas de dépendances ou n’est pas une dépendance pour d’autres types, il peut s’avérer inutile de la placer dans le conteneur. En outre, si une classe a un seul ensemble de dépendances qui font partie intégrante du type et qui ne changera jamais, il peut s’avérer inutile de la placer dans le conteneur.

L’inscription de types qui nécessitent une injection de dépendances doit être effectuée dans une méthode unique dans une application, et cette méthode doit être appelée tôt dans le cycle de vie de l’application pour s’assurer que l’application connaît les dépendances entre ses classes. Dans l’application mobile eShopOnContainers, cette opération est effectuée par la `ViewModelLocator` classe, qui génère l' `TinyIoCContainer` objet et est la seule classe de l’application qui contient une référence à cet objet. L’exemple de code suivant montre comment l’application mobile eShopOnContainers déclare l' `TinyIoCContainer` objet dans la `ViewModelLocator` classe :

```csharp
private static TinyIoCContainer _container;
```

Les types sont inscrits dans le `ViewModelLocator` constructeur. Pour cela, vous devez d’abord créer une `TinyIoCContainer` instance, qui est illustrée dans l’exemple de code suivant :

```csharp
_container = new TinyIoCContainer();
```

Les types sont ensuite inscrits avec l' `TinyIoCContainer` objet, et l’exemple de code suivant montre la forme la plus courante d’inscription de type :

```csharp
_container.Register<IRequestProvider, RequestProvider>();
```

La `Register` méthode illustrée ici mappe un type d’interface à un type concret. Par défaut, chaque inscription d’interface est configurée en tant que singleton, de sorte que chaque objet dépendant reçoit la même instance partagée. Par conséquent, une seule `RequestProvider` instance existera dans le conteneur, qui est partagé par des objets qui requièrent une injection d’un `IRequestProvider` par le biais d’un constructeur.

Les types concrets peuvent également être enregistrés directement sans mappage à partir d’un type d’interface, comme indiqué dans l’exemple de code suivant :

```csharp
_container.Register<ProfileViewModel>();
```

Par défaut, chaque inscription de classe concrète est configurée en tant que multi-instance afin que chaque objet dépendant reçoive une nouvelle instance. Par conséquent, lorsque le `ProfileViewModel` est résolu, une nouvelle instance est créée et le conteneur injecte ses dépendances requises.

## <a name="resolution"></a>Résolution

Une fois qu’un type est inscrit, il peut être résolu ou injecté en tant que dépendance. Lorsqu’un type est résolu et que le conteneur doit créer une nouvelle instance, il injecte toutes les dépendances dans l’instance.

En règle générale, lorsqu’un type est résolu, l’une des trois situations suivantes se produit :

1. Si le type n’a pas été inscrit, le conteneur lève une exception.
1. Si le type a été inscrit en tant que singleton, le conteneur retourne l’instance singleton. S’il s’agit de la première fois que le type est appelé pour, le conteneur le crée si nécessaire, et conserve une référence à celui-ci.
1. Si le type n’a pas été inscrit en tant que singleton, le conteneur retourne une nouvelle instance et ne conserve pas de référence à celui-ci.

L’exemple de code suivant montre comment le `RequestProvider` type qui a été précédemment inscrit avec TinyIoC peut être résolu :

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

Dans cet exemple, TinyIoC est invité à résoudre le type concret pour le `IRequestProvider` type, ainsi que toutes les dépendances. En général, la `Resolve` méthode est appelée lorsqu’une instance d’un type spécifique est requise. Pour plus d’informations sur le contrôle de la durée de vie des objets résolus, consultez [gestion de la durée de vie des objets résolus](#managing-the-lifetime-of-resolved-objects).

L’exemple de code suivant montre comment l’application mobile eShopOnContainers instancie les types de modèle de vue et leurs dépendances :

```csharp
var viewModel = _container.Resolve(viewModelType);
```

Dans cet exemple, TinyIoC est invité à résoudre le type de modèle de vue pour un modèle de vue demandé, et le conteneur résout également toutes les dépendances. Lors de la résolution du `ProfileViewModel` type, les dépendances à résoudre sont un `ISettingsService` objet et un `IOrderService` objet. Étant donné que les inscriptions d’interface ont été utilisées lors de l’inscription des `SettingsService` `OrderService` classes et, TinyIoC retourne des instances singleton pour les `SettingsService` classes et, `OrderService` puis les passe au constructeur de la `ProfileViewModel` classe. Pour plus d’informations sur la façon dont l’application mobile eShopOnContainers construit les modèles de vue et les associe aux vues, consultez [création automatique d’un modèle de vue avec un localisateur de modèle de vue](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator).

> [!NOTE]
> L’inscription et la résolution des types avec un conteneur ont un coût en termes de performances en raison de l’utilisation par le conteneur de la réflexion pour créer chaque type, et plus particulièrement si les dépendances sont reconstruites pour chaque navigation entre les pages de l’application. S’il existe de nombreuses dépendances ou des dépendances profondes, le coût de création peut augmenter de manière significative.

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gestion de la durée de vie des objets résolus

Après l’inscription d’un type à l’aide d’une inscription de classe concrète, le comportement par défaut de TinyIoC consiste à créer une nouvelle instance du type inscrit chaque fois que le type est résolu, ou lorsque le mécanisme de dépendance injecte des instances dans d’autres classes. Dans ce scénario, le conteneur ne contient pas de référence à l’objet résolu. Toutefois, lors de l’inscription d’un type à l’aide de l’inscription d’interface, le comportement par défaut de TinyIoC consiste à gérer la durée de vie de l’objet en tant que singleton. Par conséquent, l’instance reste dans la portée pendant que le conteneur est dans la portée, et est supprimée lorsque le conteneur est hors de portée et est récupéré par le garbage collector, ou lorsque le code supprime explicitement le conteneur.

Le comportement par défaut de l’enregistrement TinyIoC peut être substitué à l’aide des méthodes d’influence `AsSingleton` et d' `AsMultiInstance` API. Par exemple, la `AsSingleton` méthode peut être utilisée avec la `Register` méthode, afin que le conteneur crée ou retourne une instance singleton d’un type lors de l’appel de la `Resolve` méthode. L’exemple de code suivant montre comment TinyIoC est chargé de créer une instance singleton de la `LoginViewModel` classe :

```csharp
_container.Register<LoginViewModel>().AsSingleton();
```

La première fois que le `LoginViewModel` type est résolu, le conteneur crée un nouvel `LoginViewModel` objet et conserve une référence à celui-ci. Sur les résolutions suivantes du `LoginViewModel` , le conteneur retourne une référence à l' `LoginViewModel` objet créé précédemment.

> [!NOTE]
> Les types inscrits en tant que singletons sont supprimés lorsque le conteneur est supprimé.

## <a name="summary"></a>Résumé

L’injection de dépendances permet de découpler des types concrets du code qui dépend de ces types. Elle utilise généralement un conteneur qui contient une liste d’inscriptions et de mappages entre les interfaces et les types abstraits, ainsi que les types concrets qui implémentent ou étendent ces types.

TinyIoC est un conteneur léger qui offre des performances supérieures sur les plateformes mobiles par rapport à la majorité des conteneurs connus. Il facilite la création d’applications faiblement couplées et fournit toutes les fonctionnalités couramment utilisées dans les conteneurs d’injection de dépendances, notamment les méthodes d’enregistrement des mappages de types, la résolution des objets, la gestion des durées de vie des objets et l’injection d’objets dépendants dans des constructeurs d’objets qu’il résout.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
