---
title: Gestion de la configuration
description: Ce chapitre explique comment l’application mobile eShopOnContainers implémente la gestion de la configuration pour fournir des paramètres d’application et des paramètres utilisateur.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f6f61503f619c08ed3e4eae2adf6ddb2c474f99f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84571478"
---
# <a name="configuration-management"></a>Gestion de la configuration

Les paramètres permettent de séparer les données qui configurent le comportement d’une application à partir du code, ce qui permet de modifier le comportement sans reconstruire l’application. Il existe deux types de paramètres : les paramètres de l’application et les paramètres utilisateur.

Les paramètres d’application sont des données créées et gérées par une application. Il peut inclure des données telles que des points de terminaison de service Web fixes, des clés API et l’état d’exécution. Les paramètres de l’application sont liés à l’existence de l’application et ne sont significatifs que pour cette application.

Les paramètres utilisateur sont les paramètres personnalisables d’une application qui affectent le comportement de l’application et ne nécessitent pas de réajustement fréquent. Par exemple, une application peut permettre à l’utilisateur de spécifier l’emplacement à partir duquel récupérer des données et comment l’afficher à l’écran.

Xamarin.Formscomprend un dictionnaire permanent qui peut être utilisé pour stocker des données de paramètres. Ce dictionnaire est accessible à l’aide de la [`Application.Current.Properties`](xref:Xamarin.Forms.Application.Properties) propriété, et toutes les données qui y sont placées sont enregistrées lorsque l’application passe à l’état de veille, et elle est restaurée lorsque l’application reprend ou redémarre. En outre, la [`Application`](xref:Xamarin.Forms.Application) classe a également une [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) méthode qui permet à une application d’enregistrer ses paramètres quand cela est nécessaire. Pour plus d’informations sur ce dictionnaire, consultez [dictionnaire des propriétés](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary).

L’inconvénient du stockage de données à l’aide du Xamarin.Forms dictionnaire permanent est qu’il n’est pas facilement lié aux données. Par conséquent, l’application mobile eShopOnContainers utilise la bibliothèque Xam. plugins. Settings, disponible à partir de [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Cette bibliothèque fournit une approche multiplateforme, de type sécurisé et cohérente pour la persistance et la récupération des paramètres de l’application et de l’utilisateur, tout en utilisant la gestion des paramètres natifs fournie par chaque plateforme. En outre, il est facile d’utiliser la liaison de données pour accéder aux données de paramètres exposées par la bibliothèque.

> [!NOTE]
> Alors que la bibliothèque Xam. plugin. Settings peut stocker les paramètres de l’application et de l’utilisateur, elle ne fait aucune distinction entre les deux.

## <a name="creating-a-settings-class"></a>Création d’une classe de paramètres

Lors de l’utilisation de la bibliothèque Xam. plugins. Settings, vous devez créer une classe statique unique qui contiendra les paramètres de l’application et de l’utilisateur requis par l’application. L’exemple de code suivant montre la classe de paramètres dans l’application mobile eShopOnContainers :

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

Les paramètres peuvent être lus et écrits via l' `ISettings` API, qui est fournie par la bibliothèque Xam. plugins. Settings. Cette bibliothèque fournit un singleton qui peut être utilisé pour accéder à l’API, `CrossSettings.Current` , et la classe de paramètres d’une application doit exposer ce singleton par le biais d’une `ISettings` propriété.

> [!NOTE]
> Les directives using pour les espaces de noms plugin. Settings et plugin. Settings. abstractions doivent être ajoutées à une classe qui requiert l’accès aux types de bibliothèque Xam. plugins. Settings.

## <a name="adding-a-setting"></a>Ajout d’un paramètre

Chaque paramètre se compose d’une clé, d’une valeur par défaut et d’une propriété. L’exemple de code suivant affiche les trois éléments pour un paramètre utilisateur qui représente l’URL de base pour l’services en ligne à laquelle l’application mobile eShopOnContainers se connecte :

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

La clé est toujours une chaîne const qui définit le nom de la clé, la valeur par défaut du paramètre étant une valeur ReadOnly statique du type requis. Le fait de fournir une valeur par défaut garantit qu’une valeur valide est disponible si un paramètre unset est récupéré.

La `UrlBase` propriété statique utilise deux méthodes de l' `ISettings` API pour lire ou écrire la valeur de paramètre. La `ISettings.GetValueOrDefault` méthode est utilisée pour récupérer la valeur d’un paramètre à partir d’un stockage spécifique à la plateforme. Si aucune valeur n’est définie pour le paramètre, sa valeur par défaut est récupérée à la place. De même, la `ISettings.AddOrUpdateValue` méthode est utilisée pour rendre la valeur d’un paramètre persistant dans un stockage spécifique à la plateforme.

Plutôt que de définir une valeur par défaut à l’intérieur de la `Settings` classe, la `UrlBaseDefault` chaîne obtient sa valeur à partir de la `GlobalSetting` classe. L’exemple de code suivant montre la `BaseEndpoint` propriété et la `UpdateEndpoint` méthode de cette classe :

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Chaque fois que la `BaseEndpoint` propriété est définie, la `UpdateEndpoint` méthode est appelée. Cette méthode met à jour une série de propriétés, toutes basées sur le `UrlBase` paramètre utilisateur fourni par la `Settings` classe qui représente les différents points de terminaison auxquels l’application mobile eShopOnContainers se connecte.

## <a name="data-binding-to-user-settings"></a>Liaison de données aux paramètres utilisateur

Dans l’application mobile eShopOnContainers, le `SettingsView` expose deux paramètres utilisateur. Ces paramètres permettent de configurer si l’application doit récupérer des données à partir de microservices qui sont déployés en tant que conteneurs d’ancrage, ou si l’application doit récupérer des données à partir de services fictifs qui ne nécessitent pas de connexion Internet. Lorsque vous choisissez de récupérer des données à partir de microservices en conteneur, vous devez spécifier une URL de point de terminaison de base pour les microservices. La figure 7-1 montre le `SettingsView` moment où l’utilisateur a choisi de récupérer des données à partir de microservices en conteneur.

![](configuration-management-images/settings-endpoint.png "User settings exposed by the eShopOnContainers mobile app")

**Figure 7-1**: paramètres utilisateur exposés par l’application mobile eShopOnContainers

La liaison de données peut être utilisée pour récupérer et définir les paramètres exposés par la `Settings` classe. Cela est réalisé par les contrôles sur la liaison d’affichage pour afficher les propriétés du modèle qui, à leur tour, accèdent aux propriétés de la `Settings` classe, et le déclenchement d’une notification de modification de propriété si la valeur des paramètres a changé. Pour plus d’informations sur la façon dont l’application mobile eShopOnContainers construit les modèles de vue et les associe aux vues, consultez [création automatique d’un modèle de vue avec un localisateur de modèle de vue](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator).

L’exemple de code suivant affiche le [`Entry`](xref:Xamarin.Forms.Entry) contrôle de `SettingsView` qui permet à l’utilisateur d’entrer une URL de point de terminaison de base pour les microservices en conteneur :

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Ce [`Entry`](xref:Xamarin.Forms.Entry) contrôle crée une liaison avec la `Endpoint` propriété de la `SettingsViewModel` classe, à l’aide d’une liaison bidirectionnelle. L’exemple de code suivant montre la propriété de point de terminaison :

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Lorsque la `Endpoint` propriété est définie `UpdateEndpoint` , la méthode est appelée, à condition que la valeur fournie soit valide, et que la notification de modification de propriété soit levée. L’exemple de code suivant montre la méthode `UpdateEndpoint` :

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Cette méthode met à jour la `UrlBase` propriété de la `Settings` classe avec la valeur d’URL de point de terminaison de base entrée par l’utilisateur, ce qui entraîne sa conservation dans un stockage spécifique à la plateforme.

Quand `SettingsView` accède à, la `InitializeAsync` méthode dans la `SettingsViewModel` classe est exécutée. L’exemple de code suivant illustre cette méthode :

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

La méthode affecte `Endpoint` à la propriété la valeur de la `UrlBase` propriété dans la `Settings` classe. L’accès à la `UrlBase` propriété entraîne la récupération par la bibliothèque Xam. plugins. Settings de la valeur des paramètres à partir d’un stockage spécifique à la plateforme. Pour plus d’informations sur l’appel de la `InitializeAsync` méthode, consultez [passage de paramètres pendant la navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing-parameters-during-navigation).

Ce mécanisme garantit que chaque fois qu’un utilisateur accède au SettingsView, les paramètres utilisateur sont récupérés à partir du stockage spécifique à la plateforme et présentés via la liaison de données. Ensuite, si l’utilisateur modifie les valeurs des paramètres, la liaison de données garantit qu’ils sont immédiatement rendus persistants dans un stockage spécifique à la plateforme.

## <a name="summary"></a>Résumé

Les paramètres permettent de séparer les données qui configurent le comportement d’une application à partir du code, ce qui permet de modifier le comportement sans reconstruire l’application. Les paramètres de l’application sont les données créées et gérées par une application, et les paramètres utilisateur sont les paramètres personnalisables d’une application qui affectent le comportement de l’application et ne nécessitent pas de réajustement fréquent.

La bibliothèque Xam. plugins. Settings fournit une approche multiplateforme, de type sécurisé et cohérente pour la persistance et la récupération des paramètres de l’application et de l’utilisateur, et la liaison de données peut être utilisée pour accéder aux paramètres créés avec la bibliothèque.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
