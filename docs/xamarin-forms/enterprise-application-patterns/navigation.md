---
title: Navigation dans les applications d’entreprise
description: Ce chapitre explique comment l’application mobile eShopOnContainers effectue la navigation à partir des modèles de vue.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0f523c7149366cff85164f26f3f47b87801002cb
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916496"
---
# <a name="enterprise-app-navigation"></a>Navigation dans les applications d’entreprise

Xamarin. Forms comprend la prise en charge de la navigation entre les pages, qui résulte généralement de l’interaction de l’utilisateur avec l’interface utilisateur ou à partir de l’application elle-même en raison des modifications d’État pilotées par la logique interne. Toutefois, la navigation peut être complexe à implémenter dans les applications qui utilisent le modèle MVVM (Model-View-ViewModel), car les défis suivants doivent être respectés :

- Comment identifier la vue vers laquelle naviguer, à l’aide d’une approche qui n’introduit pas un couplage étroit et des dépendances entre les vues.
- Comment coordonner le processus par lequel la vue vers laquelle la navigation est exécutée est instanciée et initialisée. Quand vous utilisez MVVM, le modèle de vue et de vue doit être instancié et associé l’un à l’autre via le contexte de liaison de la vue. Quand une application utilise un conteneur d’injection de dépendances, l’instanciation des vues et des modèles de vue peut nécessiter un mécanisme de construction spécifique.
- Indique s’il faut effectuer la navigation au premier affichage ou afficher la première navigation au modèle. Avec la navigation affichage en premier, la page à laquelle accéder fait référence au nom du type de vue. Pendant la navigation, la vue spécifiée est instanciée, ainsi que le modèle de vue correspondant et les autres services dépendants. Une autre approche consiste à utiliser la navigation afficher en premier modèle, où la page à laquelle accéder fait référence au nom du type de modèle de vue.
- Comment séparer correctement le comportement de navigation de l’application sur les vues et les modèles de vue. Le modèle MVVM fournit une séparation entre l’interface utilisateur de l’application et sa présentation et sa logique métier. Toutefois, le comportement de navigation d’une application s’étend souvent aux parties de l’interface utilisateur et des présentations de l’application. L’utilisateur lance souvent la navigation à partir d’une vue, et la vue est remplacée à la suite de la navigation. Toutefois, la navigation doit souvent être initiée ou coordonnée à partir du modèle de vue.
- Comment passer des paramètres au cours de la navigation à des fins d’initialisation. Par exemple, si l’utilisateur accède à une vue pour mettre à jour les détails de la commande, les données de commande doivent être transmises à la vue afin qu’elles puissent afficher les données correctes.
- Comment coordonner la navigation pour garantir que certaines règles d’entreprise sont respectées. Par exemple, les utilisateurs peuvent être invités à quitter une vue pour qu’ils puissent corriger des données non valides ou être invités à envoyer ou ignorer les modifications de données apportées dans la vue.

Ce chapitre aborde ces défis en présentant une classe `NavigationService` qui est utilisée pour effectuer la navigation dans la première page du modèle de vue.

> [!NOTE]
> Le `NavigationService` utilisé par l’application est conçu uniquement pour effectuer une navigation hiérarchique entre les instances ContentPage. L’utilisation du service pour naviguer entre les autres types de page peut entraîner un comportement inattendu.

## <a name="navigating-between-pages"></a>Navigation entre les pages

La logique de navigation peut résider dans le code-behind d’une vue ou dans un modèle de vue lié aux données. Le fait de placer une logique de navigation dans une vue peut être l’approche la plus simple, mais elle n’est pas facile à tester par le biais de tests unitaires. Le fait de placer une logique de navigation dans les classes de modèle de vue signifie que la logique peut être testée par le biais de tests unitaires. En outre, le modèle de vue peut ensuite implémenter une logique pour contrôler la navigation afin de s’assurer que certaines règles d’entreprise sont appliquées. Par exemple, une application peut ne pas permettre à l’utilisateur de quitter une page sans s’assurer d’abord que les données entrées sont valides.

Une classe `NavigationService` est généralement appelée à partir de modèles de vue, afin de promouvoir la testabilité. Toutefois, la navigation vers les affichages à partir des modèles de vue nécessiterait que les modèles de vue référencent les vues, et en particulier les vues auxquelles le modèle de vue actif n’est pas associé, ce qui n’est pas recommandé. Par conséquent, les `NavigationService` présentées ici spécifient le type de modèle de vue comme cible à atteindre.

L’application mobile eShopOnContainers utilise la classe `NavigationService` pour fournir la navigation du modèle d’affichage en premier. Cette classe implémente l’interface `INavigationService`, qui est illustrée dans l’exemple de code suivant :

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

Cette interface spécifie qu’une classe d’implémentation doit fournir les méthodes suivantes :

|Méthode|Objectif|
|--- |--- |
|`InitializeAsync`|Effectue une navigation vers l’une des deux pages lorsque l’application est lancée.|
|`NavigateToAsync`|Effectue une navigation hiérarchique vers une page spécifiée.|
|`NavigateToAsync(parameter)`|Effectue une navigation hiérarchique vers une page spécifiée, en passant un paramètre.|
|`RemoveLastFromBackStackAsync`|Supprime la page précédente de la pile de navigation.|
|`RemoveBackStackAsync`|Supprime toutes les pages précédentes de la pile de navigation.|

En outre, l’interface `INavigationService` spécifie qu’une classe d’implémentation doit fournir une propriété `PreviousPageViewModel`. Cette propriété retourne le type de modèle d’affichage associé à la page précédente dans la pile de navigation.

> [!NOTE]
> Une interface `INavigationService` spécifie généralement également une méthode `GoBackAsync`, qui est utilisée pour revenir par programmation à la page précédente dans la pile de navigation. Toutefois, cette méthode est absente de l’application mobile eShopOnContainers, car elle n’est pas nécessaire.

### <a name="creating-the-navigationservice-instance"></a>Création de l’instance NavigationService

La classe `NavigationService`, qui implémente l’interface `INavigationService`, est inscrite en tant que singleton avec le conteneur d’injection de dépendances Autofac, comme illustré dans l’exemple de code suivant :

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

L’interface `INavigationService` est résolue dans le constructeur de classe `ViewModelBase`, comme illustré dans l’exemple de code suivant :

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Cela retourne une référence à l’objet `NavigationService` stocké dans le conteneur d’injection de dépendances Autofac, qui est créé par la méthode `InitNavigation` dans la classe `App`. Pour plus d’informations, consultez [navigation au moment du lancement de l’application](#navigating_when_the_app_is_launched).

La classe `ViewModelBase` stocke l’instance `NavigationService` dans une propriété `NavigationService`, de type `INavigationService`. Par conséquent, toutes les classes de modèle de vue, qui dérivent de la classe `ViewModelBase`, peuvent utiliser la propriété `NavigationService` pour accéder aux méthodes spécifiées par l’interface `INavigationService`. Cela évite la surcharge liée à l’injection de l’objet `NavigationService` à partir du conteneur d’injection de dépendances Autofac dans chaque classe de modèle de vue.

### <a name="handling-navigation-requests"></a>Gestion des demandes de navigation

Xamarin. Forms fournit la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , qui implémente une expérience de navigation hiérarchique dans laquelle l’utilisateur peut naviguer dans les pages, vers l’avant et vers l’arrière, selon les besoins. Pour plus d’informations sur la navigation hiérarchique, consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Au lieu d’utiliser directement la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , l’application eShopOnContainers encapsule la classe `NavigationPage` dans la classe `CustomNavigationView`, comme indiqué dans l’exemple de code suivant :

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

L’objectif de cet encapsulage est de faciliter le style de l’instance de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) à l’intérieur du fichier XAML pour la classe.

La navigation s’effectue dans les classes de modèle de vue en appelant l’une des méthodes `NavigateToAsync`, en spécifiant le type de modèle de vue pour la page vers laquelle la navigation est effectuée, comme illustré dans l’exemple de code suivant :

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

L’exemple de code suivant montre les méthodes `NavigateToAsync` fournies par la classe `NavigationService` :

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Chaque méthode autorise toute classe de modèle de vue qui dérive de la classe `ViewModelBase` pour effectuer la navigation hiérarchique en appelant la méthode `InternalNavigateToAsync`. En outre, la deuxième méthode `NavigateToAsync` permet de spécifier les données de navigation en tant qu’argument passé au modèle de vue cible de la navigation, où elles sont généralement utilisées pour effectuer l’initialisation. Pour plus d’informations, consultez [passage de paramètres pendant la navigation](#passing_parameters_during_navigation).

La méthode `InternalNavigateToAsync` exécute la demande de navigation et est illustrée dans l’exemple de code suivant :

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

La méthode `InternalNavigateToAsync` effectue une navigation vers un modèle de vue en appelant d’abord la méthode `CreatePage`. Cette méthode localise la vue qui correspond au type de modèle de vue spécifié et crée et retourne une instance de ce type d’affichage. La localisation de la vue qui correspond au type de modèle de vue utilise une approche basée sur une convention, qui suppose que :

- Les vues se trouvent dans le même assembly que les types de modèle de vue.
- Les vues se trouvent dans un. Espace de noms enfant views.
- Les modèles de vue sont dans un. Espace de noms enfant ViewModels.
- Les noms de vues correspondent aux noms de modèle de vue, avec « modèle » supprimé.

Lorsqu’une vue est instanciée, elle est associée à son modèle de vue correspondant. Pour plus d’informations sur la façon dont cela se produit, consultez [création automatique d’un modèle de vue avec un localisateur de modèle de vue](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Si la vue en cours de création est un `LoginView`, elle est encapsulée à l’intérieur d’une nouvelle instance de la classe `CustomNavigationView` et assignée à la propriété [`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage) . Dans le cas contraire, l’instance de `CustomNavigationView` est récupérée et, à condition qu’elle ne soit pas null, la méthode [`PushAsync`](xref:Xamarin.Forms.NavigationPage) est appelée pour pousser la vue créée sur la pile de navigation. Toutefois, si l’instance de `CustomNavigationView` Récupérée est `null`, la vue créée est encapsulée à l’intérieur d’une nouvelle instance de la classe `CustomNavigationView` et assignée à la propriété `Application.Current.MainPage`. Ce mécanisme garantit que, pendant la navigation, les pages sont ajoutées correctement à la pile de navigation lorsqu’elles sont vides, et lorsqu’elles contiennent des données.

> [!TIP]
> Envisagez de mettre en cache les pages. La mise en cache de la page entraîne une consommation de mémoire pour les vues qui ne sont pas actuellement affichées. Toutefois, sans mise en cache de page, cela signifie que l’analyse XAML et la construction de la page et de son modèle de vue se produisent chaque fois qu’une nouvelle page est parcourue, ce qui peut avoir un impact sur les performances d’une page complexe. Pour une page bien conçue qui n’utilise pas un nombre excessif de contrôles, les performances doivent être suffisantes. Toutefois, la mise en cache de page peut être utile si les temps de chargement de page lents sont rencontrés.

Une fois la vue créée et parcourue, la méthode `InitializeAsync` du modèle de vue associé de la vue est exécutée. Pour plus d’informations, consultez [passage de paramètres pendant la navigation](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Navigation lors du lancement de l’application

Lorsque l’application est lancée, la méthode `InitNavigation` de la classe `App` est appelée. L’exemple de code suivant illustre cette méthode :

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

La méthode crée un objet `NavigationService` dans le conteneur d’injection de dépendances Autofac et retourne une référence à celle-ci, avant d’appeler sa méthode `InitializeAsync`.

> [!NOTE]
> Lorsque l’interface `INavigationService` est résolue par la classe `ViewModelBase`, le conteneur retourne une référence à l’objet `NavigationService` qui a été créé lors de l’appel de la méthode InitNavigation.

L’exemple de code suivant montre la méthode `NavigationService` `InitializeAsync` :

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

La `MainView` est accessible si l’application a un jeton d’accès mis en cache, qui est utilisé pour l’authentification. Dans le cas contraire, le `LoginView` est accédé à.

Pour plus d’informations sur le conteneur d’injection de dépendances Autofac, consultez [Introduction à l’injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Passage de paramètres pendant la navigation

L’une des méthodes `NavigateToAsync`, spécifiée par l’interface `INavigationService`, permet de spécifier les données de navigation en tant qu’argument passé au modèle de vue cible de la navigation, où elle est généralement utilisée pour effectuer l’initialisation.

Par exemple, la classe `ProfileViewModel` contient une `OrderDetailCommand` qui est exécutée lorsque l’utilisateur sélectionne une commande sur la page de `ProfileView`. À son tour, cette méthode exécute la méthode `OrderDetailAsync`, qui est illustrée dans l’exemple de code suivant :

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Cette méthode appelle la navigation vers le `OrderDetailViewModel`, en passant une instance `Order` qui représente l’ordre que l’utilisateur a sélectionné dans la page de `ProfileView`. Lorsque la classe `NavigationService` crée le `OrderDetailView`, la classe `OrderDetailViewModel` est instanciée et assignée au [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)de la vue. Après avoir accédé à la `OrderDetailView`, la méthode `InternalNavigateToAsync` exécute la méthode `InitializeAsync` du modèle de vue associé de la vue.

La méthode `InitializeAsync` est définie dans la classe `ViewModelBase` en tant que méthode qui peut être substituée. Cette méthode spécifie un argument `object` qui représente les données à passer à un modèle de vue pendant une opération de navigation. Par conséquent, les classes de modèle de vue qui souhaitent recevoir des données à partir d’une opération de navigation fournissent leur propre implémentation de la méthode `InitializeAsync` pour effectuer l’initialisation requise. L’exemple de code suivant montre la méthode `InitializeAsync` à partir de la classe `OrderDetailViewModel` :

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Cette méthode récupère l’instance de `Order` qui a été passée dans le modèle de vue pendant l’opération de navigation et l’utilise pour récupérer les détails de la commande complète de l’instance de `OrderService`.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Appel de la navigation à l’aide de comportements

La navigation est généralement déclenchée par une interaction de l’utilisateur à partir d’une vue. Par exemple, le `LoginView` effectue la navigation après une authentification réussie. L’exemple de code suivant montre comment la navigation est appelée par un comportement :

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

Au moment de l’exécution, le `EventToCommandBehavior` répondra à l’interaction avec le [`WebView`](xref:Xamarin.Forms.WebView). Lorsque le `WebView` accède à une page Web, l’événement [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) se déclenche, ce qui entraîne l’exécution de la `NavigateCommand` dans le `LoginViewModel`. Par défaut, les arguments d’événement pour l’événement sont passés à la commande. Ces données sont converties lorsqu’elles sont passées entre la source et la cible par le convertisseur spécifié dans la propriété `EventArgsConverter`, qui retourne le [`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) à partir du [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs). Par conséquent, lors de l’exécution du `NavigationCommand`, l’URL de la page Web est transmise en tant que paramètre au `Action`inscrit.

À son tour, le `NavigationCommand` exécute la méthode `NavigateAsync`, qui est illustrée dans l’exemple de code suivant :

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Cette méthode appelle la navigation vers le `MainViewModel`et, à la suite de la navigation, supprime la page `LoginView` de la pile de navigation.

### <a name="confirming-or-cancelling-navigation"></a>Confirmation ou annulation de la navigation

Une application peut avoir besoin d’interagir avec l’utilisateur pendant une opération de navigation, afin que l’utilisateur puisse confirmer ou annuler la navigation. Cela peut être nécessaire, par exemple, lorsque l’utilisateur tente de naviguer avant d’avoir entièrement terminé une page d’entrée de données. Dans ce cas, une application doit fournir une notification qui permet à l’utilisateur de quitter la page, ou d’annuler l’opération de navigation avant qu’elle ne se produise. Cela peut être réalisé dans une classe de modèle de vue à l’aide de la réponse d’une notification pour contrôler si la navigation est appelée.

## <a name="summary"></a>Résumé

Xamarin. Forms prend en charge la navigation entre les pages, qui résulte généralement de l’interaction de l’utilisateur avec l’interface utilisateur, ou à partir de l’application elle-même, en raison des modifications d’État pilotées par la logique interne. Toutefois, la navigation peut être complexe à implémenter dans les applications qui utilisent le modèle MVVM.

Ce chapitre a présenté une classe `NavigationService`, qui est utilisée pour effectuer la navigation dans le modèle de vue, en commençant par les modèles de vue. Le fait de placer une logique de navigation dans les classes de modèle de vue signifie que la logique peut être testée par le biais de tests automatisés. En outre, le modèle de vue peut ensuite implémenter une logique pour contrôler la navigation afin de s’assurer que certaines règles d’entreprise sont appliquées.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
