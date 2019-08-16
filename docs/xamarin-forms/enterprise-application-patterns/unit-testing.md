---
title: Tests unitaires d’applications d’entreprise
description: Ce chapitre explique comment effectuer des tests unitaires dans l’application mobile eShopOnContainers.
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: c631ca73d69ea630592920a32804512f89d5baaf
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529072"
---
# <a name="unit-testing-enterprise-apps"></a>Tests unitaires d’applications d’entreprise

Les applications mobiles présentent des problèmes uniques que les applications bureautiques et Web n’ont pas à se soucier. Les utilisateurs mobiles diffèrent par les appareils qu’ils utilisent, par la connectivité réseau, par la disponibilité des services et par un éventail d’autres facteurs. Par conséquent, les applications mobiles doivent être testées, car elles seront utilisées dans le monde réel pour améliorer la qualité, la fiabilité et les performances. Il existe de nombreux types de test qui doivent être effectués sur une application, notamment les tests unitaires, les tests d’intégration et les tests de l’interface utilisateur, avec des tests unitaires étant la forme de test la plus courante.

Un test unitaire prend une petite unité de l’application, généralement une méthode, l’isole du reste du code et vérifie qu’elle se comporte comme prévu. Son objectif est de vérifier que chaque unité de fonctionnalité fonctionne comme prévu, afin que les erreurs ne se propagent pas dans l’ensemble de l’application. La détection d’un bogue dans lequel il se produit est plus efficace que l’observation de l’effet d’un bogue indirectement à un point de défaillance secondaire.

Les tests unitaires ont l’effet le plus important sur la qualité du code lorsqu’il fait partie intégrante du flux de travail de développement de logiciels. Dès qu’une méthode a été écrite, des tests unitaires doivent être écrits afin de vérifier le comportement de la méthode en réponse à des cas de figure standard, de limite et incorrects des données d’entrée, et de vérifier toutes les hypothèses explicites ou implicites effectuées par le code. À l’inverse, avec le développement piloté par les tests, les tests unitaires sont écrits avant le code. Dans ce scénario, les tests unitaires jouent le rôle de documentation de conception et de spécifications fonctionnelles.

> [!NOTE]
> Les tests unitaires sont très efficaces par rapport à la régression, c’est-à-dire les fonctionnalités utilisées pour fonctionner, mais qui ont été perturbées par une mise à jour défectueuse.

Les tests unitaires utilisent généralement le modèle arrange-Act-Assert:

- La section *organiser* de la méthode de test unitaire initialise les objets et définit la valeur des données passées à la méthode testée.
- La section *Act* appelle la méthode testée avec les arguments requis.
- La section Assert vérifie que l’action de la méthode testée se comporte comme prévu.

Le respect de ce modèle permet de s’assurer que les tests unitaires sont lisibles et cohérents.

## <a name="dependency-injection-and-unit-testing"></a>Injection de dépendances et tests unitaires

L’une des motivations pour adopter une architecture faiblement couplée est qu’elle facilite les tests unitaires. L’un des types inscrits avec Autofac est la `OrderService` classe. L’exemple de code suivant illustre une structure de cette classe:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

La `OrderDetailViewModel` classe a une dépendance sur le `IOrderService` type que le conteneur résout lorsqu’il instancie un `OrderDetailViewModel` objet. Toutefois, au lieu de créer `OrderService` un objet pour effectuer un `OrderDetailViewModel` test unitaire de la classe, `OrderService` remplacez plutôt l’objet par un simulacre dans le cadre des tests. La figure 10-1 illustre cette relation.

![](unit-testing-images/unittesting.png "Classes qui implémentent l’interface IOrderService")

**Figure 10-1:** Classes qui implémentent l’interface IOrderService

Cette approche permet à `OrderService` l’objet d’être passé dans `OrderDetailViewModel` la classe au moment de l’exécution, et dans l’intérêt de la testabilité, il permet à la `OrderDetailViewModel` `OrderMockService` classe d’être passée dans la classe au moment du test. Le principal avantage de cette approche est qu’elle permet l’exécution de tests unitaires sans nécessiter de ressources peu maniables, telles que des services Web ou des bases de données.

## <a name="testing-mvvm-applications"></a>Test des applications MVVM

Les modèles de test et les modèles de vue des applications MVVM sont identiques au test de toute autre classe, et les mêmes outils et techniques, tels que les tests unitaires et les simulations, peuvent être utilisés. Toutefois, il existe des modèles qui sont typiques des classes de modèle de modèle et de vue, qui peuvent tirer parti de techniques de test unitaire spécifiques.

> [!TIP]
> Testez une chose avec chaque test unitaire. Ne vous inquiétez pas de faire en sorte qu’un test unitaire exerce plus d’un aspect du comportement de l’unité. Cela amène à des tests difficiles à lire et à mettre à jour. Cela peut également entraîner une confusion lors de l’interprétation d’une défaillance.

L’application mobile eShopOnContainers utilise [xUnit](https://xunit.github.io/) pour effectuer des tests unitaires, qui prend en charge deux types différents de tests unitaires:

- Les faits sont des tests qui ont toujours la valeur true, qui testent les conditions d’invariant.
- Les théories sont des tests qui ne sont vrais que pour un jeu de données particulier.

Les tests unitaires inclus avec l’application mobile eShopOnContainers sont des tests de faits. par conséquent, chaque méthode de test `[Fact]` unitaire est décorée avec l’attribut.

> [!NOTE]
> les tests xUnit sont exécutés par un test Runner. Pour exécuter Test Runner, exécutez le projet eShopOnContainers. TestRunner pour la plateforme requise.

### <a name="testing-asynchronous-functionality"></a>Test des fonctionnalités asynchrones

Lors de l’implémentation du modèle MVVM, les modèles de vue appellent généralement des opérations sur les services, souvent de manière asynchrone. Les tests pour le code qui appelle ces opérations utilisent généralement des simulacres comme remplacements pour les services réels. L’exemple de code suivant illustre le test des fonctionnalités asynchrones en passant un service factice dans un modèle de vue:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

Ce test unitaire vérifie que la `Order` propriété de l' `OrderDetailViewModel` instance aura une valeur une fois que `InitializeAsync` la méthode a été appelée. La `InitializeAsync` méthode est appelée lors de la navigation vers la vue correspondante du modèle de vue. Pour plus d’informations sur la navigation, consultez [navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Lorsque l' `OrderDetailViewModel` instance est créée, elle s’attend à `OrderService` ce qu’une instance soit spécifiée en tant qu’argument. Toutefois, le `OrderService` récupère les données d’un service Web. Par conséquent, `OrderMockService` une instance, qui est une version factice de `OrderService` la classe, est spécifiée comme argument pour le `OrderDetailViewModel` constructeur. Ensuite, lorsque la méthode du modèle `InitializeAsync` de vue est appelée, qui `IOrderService` appelle des opérations, les données fictives sont récupérées au lieu de communiquer avec un service Web.

### <a name="testing-inotifypropertychanged-implementations"></a>Test des implémentations INotifyPropertyChanged

L’implémentation `INotifyPropertyChanged` de l’interface permet aux vues de réagir aux modifications provenant des modèles de vue et des modèles. Ces modifications ne sont pas limitées aux données affichées dans les contrôles. elles sont également utilisées pour contrôler la vue, par exemple les États du modèle d’affichage qui entraînent le démarrage des animations ou la désactivation des contrôles.

Les propriétés qui peuvent être mises à jour directement par le test unitaire peuvent être testées en attachant un gestionnaire `PropertyChanged` d’événements à l’événement et en vérifiant si l’événement est déclenché après la définition d’une nouvelle valeur pour la propriété. L’exemple de code suivant illustre ce type de test:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

Ce test unitaire appelle la `InitializeAsync` méthode de la `OrderViewModel` classe, ce qui entraîne la `Order` mise à jour de sa propriété. Le test unitaire réussira, à condition que `PropertyChanged` l’événement soit déclenché pour `Order` la propriété.

### <a name="testing-message-based-communication"></a>Test de la communication basée sur les messages

Les modèles de vue qui [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) utilisent la classe pour communiquer entre des classes faiblement couplées peuvent être testés par unité en s’abonnant au message envoyé par le code testé, comme illustré dans l’exemple de code suivant:

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

Ce test unitaire vérifie que le `CatalogViewModel` publie le `AddProduct` message en réponse à son `AddCatalogItemCommand` exécution. Étant donné [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) que la classe prend en charge les abonnements `AddProduct` aux messages multidiffusion, le test unitaire peut s’abonner au message et exécuter un délégué de rappel en réponse à sa réception. Ce délégué de rappel, spécifié comme une expression lambda, définit `boolean` un champ utilisé par l' `Assert` instruction pour vérifier le comportement du test.

### <a name="testing-exception-handling"></a>Test de la gestion des exceptions

Il est également possible d’écrire des tests unitaires qui vérifient que des exceptions spécifiques sont levées pour des actions ou des entrées non valides, comme illustré dans l’exemple de code suivant:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

Ce test unitaire lèvera une exception, car le [`ListView`](xref:Xamarin.Forms.ListView) contrôle n’a pas d’événement nommé. `OnItemTapped` La `Assert.Throws<T>` méthode est une méthode générique où `T` est le type de l’exception attendue. L’argument passé à la `Assert.Throws<T>` méthode est une expression lambda qui lèvera l’exception. Par conséquent, le test unitaire passera à condition que l’expression lambda lève une `ArgumentException`.

> [!TIP]
> Évitez d’écrire des tests unitaires qui examinent les chaînes de message d’exception. Les chaînes de message d’exception peuvent changer au fil du temps et, par conséquent, les tests unitaires qui reposent sur leur présence sont considérés comme fragiles.

### <a name="testing-validation"></a>Test de la validation

Il existe deux aspects du test de l’implémentation de validation: le test de l’implémentation correcte des règles de validation et `ValidatableObject<T>` le test de l’exécution de la classe comme prévu.

La logique de validation est généralement simple à tester, car il s’agit généralement d’un processus autonome dans lequel la sortie dépend de l’entrée. Il doit y avoir des tests sur les résultats de l' `Validate` appel de la méthode sur chaque propriété qui possède au moins une règle de validation associée, comme illustré dans l’exemple de code suivant:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

Ce test unitaire vérifie que la validation s’effectue correctement lorsque `ValidatableObject<T>` les deux propriétés `MockViewModel` de l’instance contiennent des données.

En plus de vérifier que la validation est réussie, les tests unitaires de validation doivent également vérifier `Value`les `IsValid`valeurs de `Errors` la propriété, `ValidatableObject<T>` et de chaque instance, afin de vérifier que la classe s’exécute comme prévu. L’exemple de code suivant illustre un test unitaire qui effectue ce qui suit:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

Ce test unitaire vérifie que la validation échoue lorsque `Surname` la propriété `MockViewModel` du n’a pas de données et que `Value`la `IsValid`propriété, `Errors` et de chaque `ValidatableObject<T>` instance est correctement définie.

## <a name="summary"></a>Récapitulatif

Un test unitaire prend une petite unité de l’application, généralement une méthode, l’isole du reste du code et vérifie qu’elle se comporte comme prévu. Son objectif est de vérifier que chaque unité de fonctionnalité fonctionne comme prévu, afin que les erreurs ne se propagent pas dans l’ensemble de l’application.

Le comportement d’un objet testé peut être isolé en remplaçant les objets dépendants par des objets fictifs qui simulent le comportement des objets dépendants. Cela permet l’exécution de tests unitaires sans nécessiter de ressources peu maniables, telles que des services Web ou des bases de données.

Le test de modèles et de vues à partir d’applications MVVM est identique au test de toute autre classe, et les mêmes outils et techniques peuvent être utilisés.


## <a name="related-links"></a>Liens associés

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
