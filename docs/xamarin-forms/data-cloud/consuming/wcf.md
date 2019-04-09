---
title: Utiliser un Service Web de Windows Communication Foundation (WCF)
description: Cet article illustre l’utilisation d’un service WCF SOAP Simple Object Access Protocol () à partir d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 7106c0aed03800d3479471caab0974be3c09c1f8
ms.sourcegitcommit: cc750b0d8086ed14f84cd8eb9a06f45c719b3cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59239912"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Utiliser un Service Web de Windows Communication Foundation (WCF)

[![Délécharger exemple](~/media/shared/download.png) Télécharger l'exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)

_WCF est un cadre unifié de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées traitées, fiables, sécurisées et interopérables. Cet article illustre l’utilisation d’un service WCF SOAP Simple Object Access Protocol () à partir d’une application Xamarin.Forms._

WCF décrit un service avec un large éventail de contrats différents, y compris :

- **Contrats de données** : définir les structures de données qui constituent la base pour le contenu d’un message.
- **Contrats de message** – composer des messages à partir des contrats de données existants.
- **Contrats de l’erreur** – autoriser les erreurs SOAP personnalisées à être spécifié.
- **Contrats de service** : spécifier les opérations qui prennent en charge des services et les messages requis pour l’interaction avec chaque opération. Ils spécifient également tout comportement d’erreur personnalisé qui peut être associé à des opérations sur chaque service.

Voici les différences entre ASP.NET Web Services (ASMX) et WCF, mais WCF prend en charge les mêmes fonctionnalités que ASMX fournit – les messages SOAP sur HTTP. Pour plus d’informations sur l’utilisation d’un service ASMX, consultez [consommation ASP.NET Web Services (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

> [!IMPORTANT]
> La prise en charge de la plateforme Xamarin pour WCF est limité à texte encodé des messages SOAP via HTTP/HTTPS en utilisant la `BasicHttpBinding` classe.
>
> Prise en charge WCF requiert l’utilisation d’outils disponibles uniquement dans un environnement Windows pour générer le proxy et d’héberger le TodoWCFService. Créer et tester l’application iOS nécessite le déploiement de la TodoWCFService sur un ordinateur Windows, ou en tant que service web Azure.
>
> Applications natives Xamarin Forms généralement partagent du code avec une bibliothèque de classes .NET Standard. Toutefois, .NET Core ne prend pas en charge WCF le projet partagé doit donc être une bibliothèque de classes Portable héritée. Pour plus d’informations sur la prise en charge WCF dans .NET Core, consultez [choix entre .NET Core et .NET Framework pour les applications serveur](/dotnet/standard/choosing-core-framework-server).

L’exemple de solution d’application inclut un service WCF qui peut être exécuté localement et est indiqué dans la capture d’écran suivante :

![](wcf-images/portal.png "Exemple d’Application")

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
>
> ATS peuvent être ignorées si elle n’est pas possible d’utiliser le `HTTPS` de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consommer le service web

Le service WCF fournit les opérations suivantes :

|Opération|Description|Paramètres|
|--- |--- |--- |
|GetTodoItems|Obtenir une liste de tâches|
|CreateTodoItem|Créer un élément de tâche|Un document XML sérialisé TodoItem|
|EditTodoItem|Mettre à jour une tâche|Un document XML sérialisé TodoItem|
|DeleteTodoItem|Supprimer une tâche|Un document XML sérialisé TodoItem|

Pour plus d’informations sur le modèle de données utilisé dans l’application, consultez [les données de modélisation](~/xamarin-forms/data-cloud/walkthrough.md).

Un *proxy* doit être généré pour utiliser un service WCF, ce qui permet à l’application pour se connecter au service. Le proxy est construit par la consommation des métadonnées de service qui définissent les méthodes et la configuration de service associé. Ces métadonnées sont présentées sous la forme d’un document Web Services Description Language (WSDL) généré par le service web. Le proxy peut être généré à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 pour ajouter une référence de service pour le service web à une bibliothèque .NET Standard. Une alternative à la création du proxy à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 consiste à utiliser ServiceModel Metadata Utility Tool (svcutil.exe). Pour plus d’informations, consultez [ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Les classes proxy générées fournissent des méthodes de consommer les services web qui utilisent le modèle de conception de modèle de programmation asynchrone (APM). Dans ce modèle, une opération asynchrone est implémentée en tant que deux méthodes nommées *Beginnomopération* et *Endnomopération*, qui commencent et terminent l’opération asynchrone.

Le *Beginnomopération* méthode commence l’opération asynchrone et retourne un objet qui implémente le `IAsyncResult` interface. Après avoir appelé *Beginnomopération*, une application peut continuer à exécuter des instructions sur le thread appelant, tandis que l’opération asynchrone se déroule sur un thread de pool de threads.

Pour chaque appel à *Beginnomopération*, l’application doit également appeler *Endnomopération* pour obtenir les résultats de l’opération. La valeur de retour de *Endnomopération* est du même type que celui retourné par la méthode de service web synchrone. Par exemple, le `EndGetTodoItems` méthode retourne une collection de `TodoItem` instances. Le *Endnomopération* inclut également un `IAsyncResult` paramètre qui doit être définie sur l’instance retournée par l’appel correspondant à la *Beginnomopération* (méthode).

La bibliothèque parallèle de tâches (TPL) peut simplifier le processus d’utilisation d’une paire de méthodes begin/end APM en encapsulant les opérations asynchrones dans le même `Task` objet. Cette encapsulation est fournie par plusieurs surcharges de la `TaskFactory.FromAsync` (méthode).

Pour plus d’informations sur APM consultez [modèle de programmation asynchrone](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) et [TPL et Framework programmation asynchrone .NET traditionnelle](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) sur MSDN.

### <a name="create-the-todoserviceclient-object"></a>Créer l’objet TodoServiceClient

Fournit la classe proxy générée la `TodoServiceClient` (classe), qui est utilisé pour communiquer avec le service WCF via HTTP. Il offre des fonctionnalités permettant d’appeler des méthodes de service web comme des opérations asynchrones à partir d’un URI identifié l’instance de service. Pour plus d’informations sur les opérations asynchrones, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

Le `TodoServiceClient` instance est déclarée au niveau de la classe afin que l’objet est utilisable que pendant tant que l’application doit utiliser le service WCF, comme indiqué dans l’exemple de code suivant :

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

Le `TodoServiceClient` instance est configurée avec une adresse de point de terminaison et les informations de liaison. Une liaison est utilisée pour spécifier le transport, le codage et les détails de protocole requis pour les applications et services communiquer entre eux. Le `BasicHttpBinding` Spécifie que les messages SOAP encodés en texte seront envoyés via le protocole de transport HTTP. Spécification d’une adresse de point de terminaison permet à l’application pour se connecter à différentes instances du service WCF, si plusieurs instances publiées.

Pour plus d’informations sur la configuration de la référence de service, consultez [configuration de la référence de Service](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Créer des objets de transfert de données

L’exemple d’application utilise le `TodoItem` classe pour modéliser les données. Pour stocker un `TodoItem` élément dans le service web, il doit d’abord être converti vers le proxy généré `TodoItem` type. Pour cela le `ToWCFServiceTodoItem` méthode, comme indiqué dans l’exemple de code suivant :

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Cette méthode crée simplement un `TodoWCFService.TodoItem` de l’instance et définit chaque propriété à la propriété identique à partir de la `TodoItem` instance.

De même, lorsque les données sont récupérées à partir du service web, il doit être converti à partir du proxy généré `TodoItem` type à un `TodoItem` instance. Pour cela, avec la `FromWCFServiceTodoItem` méthode, comme indiqué dans l’exemple de code suivant :

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Cette méthode récupère simplement les données à partir du proxy généré `TodoItem` tapez, puis il définit dans nouvellement créé `TodoItem` instance.

### <a name="retrieve-data"></a>Récupérer des données

Le `TodoServiceClient.BeginGetTodoItems` et `TodoServiceClient.EndGetTodoItems` méthodes sont utilisées pour appeler le `GetTodoItems` opération fournie par le service web. Ces méthodes asynchrones sont encapsulées dans un `Task` de l’objet, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

Le `Task.Factory.FromAsync` méthode crée un `Task` qui exécute le `TodoServiceClient.EndGetTodoItems` méthode une fois le `TodoServiceClient.BeginGetTodoItems` méthode se termine, avec le `null` paramètre indiquant qu’aucune donnée n’est passée dans le `BeginGetTodoItems` déléguer. Enfin, la valeur de la `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Le `TodoServiceClient.EndGetTodoItems` méthode retourne un `ObservableCollection` de `TodoWCFService.TodoItem` instances, qui est ensuite convertie en un `List` de `TodoItem` instances pour l’affichage.

### <a name="create-data"></a>Créer des données

Le `TodoServiceClient.BeginCreateTodoItem` et `TodoServiceClient.EndCreateTodoItem` méthodes sont utilisées pour appeler le `CreateTodoItem` opération fournie par le service web. Ces méthodes asynchrones sont encapsulées dans un `Task` de l’objet, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

Le `Task.Factory.FromAsync` méthode crée un `Task` qui exécute le `TodoServiceClient.EndCreateTodoItem` méthode une fois le `TodoServiceClient.BeginCreateTodoItem` méthode se termine, avec le `todoItem` paramètre en cours de données qui a été passées dans le `BeginCreateTodoItem` délégué pour spécifier le `TodoItem` doit être créé par le service web. Enfin, la valeur de la `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

La service web lève une exception une `FaultException` en cas d’échec créer le `TodoItem`, qui est géré par l’application.

### <a name="update-data"></a>mettre à jour des données

Le `TodoServiceClient.BeginEditTodoItem` et `TodoServiceClient.EndEditTodoItem` méthodes sont utilisées pour appeler le `EditTodoItem` opération fournie par le service web. Ces méthodes asynchrones sont encapsulées dans un `Task` de l’objet, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

Le `Task.Factory.FromAsync` méthode crée un `Task` qui exécute le `TodoServiceClient.EndEditTodoItem` méthode une fois le `TodoServiceClient.BeginCreateTodoItem` méthode se termine, avec le `todoItem` paramètre en cours de données qui a été passées dans le `BeginEditTodoItem` délégué pour spécifier le `TodoItem` à mettre à jour par le service web. Enfin, la valeur de la `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

La service web lève une exception une `FaultException` si elle ne parvient pas à localiser ou de mettre à jour le `TodoItem`, qui est géré par l’application.

### <a name="delete-data"></a>Supprimer des données

Le `TodoServiceClient.BeginDeleteTodoItem` et `TodoServiceClient.EndDeleteTodoItem` méthodes sont utilisées pour appeler le `DeleteTodoItem` opération fournie par le service web. Ces méthodes asynchrones sont encapsulées dans un `Task` de l’objet, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

Le `Task.Factory.FromAsync` méthode crée un `Task` qui exécute le `TodoServiceClient.EndDeleteTodoItem` méthode une fois le `TodoServiceClient.BeginDeleteTodoItem` méthode se termine, avec le `id` paramètre en cours de données qui a été passées dans le `BeginDeleteTodoItem` délégué pour spécifier le `TodoItem` doit être supprimé par le service web. Enfin, la valeur de la `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

La service web lève une exception une `FaultException` si elle ne parvient pas à localiser ou de supprimer le `TodoItem`, qui est géré par l’application.

## <a name="configure-remote-access-to-iis-express"></a>Configurer l’accès à distance pour IIS Express
Dans Visual Studio 2017 ou Visual Studio 2019, vous pourrez tester l’application UWP sur un PC sans aucune configuration supplémentaire. Tester des clients iOS et Android peut nécessiter des étapes supplémentaires dans cette section. Consultez [se connecter à des Services Web locaux à partir d’iOS simulateurs et émulateurs Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) pour plus d’informations.

Par défaut, IIS Express répond uniquement aux demandes de `localhost`. Les appareils distants (par exemple, un appareil Android, un iPhone ou même un simulateur) n’aura pas accès à votre service WCF local. Vous devez connaître votre adresse de station de travail Windows 10 sur le réseau local. Pour les besoins de cet exemple, supposons que votre station de travail possède l’adresse IP `192.168.1.143`. Les étapes suivantes expliquent comment configurer Windows 10 et IIS Express pour accepter les connexions à distance et la connexion au service à partir d’un appareil physique ou virtuel :

1. **Ajouter une exception au pare-feu de Windows**. Vous devez ouvrir un port via le pare-feu Windows que les applications sur votre sous-réseau peuvent utiliser pour communiquer avec le service WCF. Créer une règle de trafic entrant ouverture 49393 de port dans le pare-feu. À partir d’une invite de commandes d’administration, exécutez cette commande :
    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurer IIS Express pour accepter distants**. Vous pouvez configurer IIS Express en modifiant le fichier de configuration pour IIS Express à **[répertoire de la solution]\.vs\config\applicationhost.config**. Rechercher la `site` élément portant le nom `TodoWCFService`. Il doit ressembler au code XML suivant :

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    Vous devez ajouter deux `binding` éléments pour ouvrir le port 49393 pour le trafic externe et l’émulateur Android. La liaison utilise un `[IP address]:[port]:[hostname]` format qui spécifie la façon dont IIS Express répond aux demandes. Demandes externes auront des noms d’hôtes qui doivent être spécifiées comme un `binding`. Ajoutez le code XML suivant à la `bindings` élément, en remplaçant l’adresse IP avec votre propre adresse IP :

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Une fois vos modifications le `bindings` élément devrait ressembler à ce qui suit :

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >Par défaut, IIS Express n’accepte pas les connexions à partir de sources externes pour des raisons de sécurité. Pour activer les connexions à partir d’appareils à distance vous devez exécuter IIS Express avec des autorisations administratives. Pour ce faire, le plus simple consiste à exécuter Visual Studio 2017 avec des autorisations administratives. Ceci lancera IIS Express avec des autorisations administratives lors de l’exécution de la TodoWCFService.

    Avec ces étapes terminées, vous pourrez exécuter le TodoWCFService et se connecter à partir d’autres appareils sur votre sous-réseau. Vous pouvez tester cela à votre application en cours d’exécution en accédant à `http://localhost:49393/TodoService.svc`. Si vous obtenez un **demande incorrecte** erreur lors de la visite de cette URL, votre `bindings` est peut-être incorrect dans la configuration d’IIS Express (la demande atteint IIS Express, mais est donc rejetée). Si vous obtenez une erreur différente, qu'il peut être que votre application n’est pas en cours d’exécution ou votre pare-feu est configuré correctement.

    Pour permettre à IIS Express maintenir en cours d’exécution et desservant le service, désactivez le **Modifier & Continuer** option **propriétés du projet > Web > débogueurs**.

1. **Personnaliser le point de terminaison appareils utilisent pour accéder au service**. Cette étape implique la configuration de l’application client, en cours d’exécution sur un appareil physique ou émulé, pour accéder au service WCF.

    L’émulateur Android utilise un proxy interne qui empêche l’émulateur d’accéder directement à l’ordinateur hôte `localhost` adresse. Au lieu de cela, l’adresse `10.0.2.2` sur l’émulateur est acheminé vers `localhost` sur l’ordinateur hôte via un proxy interne. Ces requêtes en proxy aura `127.0.0.1` en tant que le nom d’hôte dans l’en-tête de demande, c’est pourquoi de la création de la liaison IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    IOS Simulator s’exécute sur un Mac build hôte, même si vous utilisez le [Remoted iOS Simulator pour Windows](~/tools/ios-simulator/index.md). Demandes du réseau à partir du simulateur aura une adresse IP de votre station de travail sur le réseau local en tant que le nom d’hôte (dans cet exemple il a `192.168.1.143`, mais votre adresse IP réelle sera probablement différente). C’est pourquoi vous avez créé la liaison IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    Vérifiez le `SoapUrl` propriété dans le **Constants.cs** fichier dans le projet TodoWCF (Portable) ont des valeurs qui sont corrects pour votre réseau :

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    Une fois que vous avez configuré le **Constants.cs** avec les points de terminaison appropriés, vous devriez être en mesure de se connecter à la TodoWCFService en cours d’exécution sur votre station de travail Windows 10 à partir d’appareils physiques ou virtuels.

## <a name="related-links"></a>Liens connexes

- [TodoWCF (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [Procédure : Créer un Client Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Service Model Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
