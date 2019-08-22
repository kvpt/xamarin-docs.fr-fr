---
title: Utiliser un service Web Windows Communication Foundation (WCF)
description: Cet article illustre l’utilisation d’un service WCF SOAP Simple Object Access Protocol () à partir d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 28cb1573262b63cc2b0ccad9f468fe36c682718d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888845"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Utiliser un service Web Windows Communication Foundation (WCF)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF est un cadre unifié de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées traitées, fiables, sécurisées et interopérables. Cet article illustre l’utilisation d’un service WCF SOAP Simple Object Access Protocol () à partir d’une application Xamarin.Forms._

WCF décrit un service avec différents contrats, notamment:

- **Contrats de données** : définir les structures de données qui constituent la base pour le contenu d’un message.
- **Contrats de message** – composer des messages à partir des contrats de données existants.
- **Contrats de l’erreur** – autoriser les erreurs SOAP personnalisées à être spécifié.
- **Contrats de service** : spécifier les opérations qui prennent en charge des services et les messages requis pour l’interaction avec chaque opération. Ils spécifient également tout comportement d’erreur personnalisé qui peut être associé à des opérations sur chaque service.

Il existe des différences entre les services Web ASP.NET (ASMX) et WCF, mais WCF prend en charge les mêmes fonctionnalités que celles fournies par le service ASMX: les messages SOAP sur HTTP. Pour plus d’informations sur l’utilisation d’un service ASMX, consultez [utilisation des services Web ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> La prise en charge de la plateforme Xamarin pour WCF est limitée aux messages SOAP encodés en texte via http/ `BasicHttpBinding` HTTPS à l’aide de la classe.
>
> La prise en charge de WCF requiert l’utilisation d’outils uniquement disponibles dans un environnement Windows pour générer le proxy et héberger le TodoWCFService. Pour générer et tester l’application iOS, vous devez déployer le TodoWCFService sur un ordinateur Windows ou en tant que service Web Azure.
>
> Les applications Xamarin Forms natives partagent généralement du code avec une bibliothèque de classes .NET Standard. Toutefois, .NET Core ne prend pas en charge WCF pour le moment, le projet partagé doit être une bibliothèque de classes portables héritée. Pour plus d’informations sur la prise en charge de WCF dans .NET Core, consultez [choix entre .net Core et .NET Framework pour les applications serveur](/dotnet/standard/choosing-core-framework-server).

L’exemple de solution d’application comprend un service WCF qui peut être exécuté localement et est illustré dans la capture d’écran suivante:

![](wcf-images/portal.png "Exemple d’Application")

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
>
> ATS peuvent être ignorées si elle n’est pas possible d’utiliser le `HTTPS` de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Utiliser le service Web

Le service WCF fournit les opérations suivantes :

|Opération|Description|Paramètres|
|--- |--- |--- |
|GetTodoItems|Obtenir une liste de tâches|
|CreateTodoItem|Créer un élément de tâche|Un document XML sérialisé TodoItem|
|EditTodoItem|Mettre à jour une tâche|Un document XML sérialisé TodoItem|
|DeleteTodoItem|Supprimer une tâche|Un document XML sérialisé TodoItem|

Pour plus d’informations sur le modèle de données utilisé dans l’application, consultez [les données de modélisation](~/xamarin-forms/data-cloud/web-services/introduction.md).

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

### <a name="update-data"></a>Mettre à jour les données

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

## <a name="configure-remote-access-to-iis-express"></a>Configuration de l’accès à distance à IIS Express
Dans Visual Studio 2017 ou Visual Studio 2019, vous devez être en mesure de tester l’application UWP sur un PC sans aucune configuration supplémentaire. Le test des clients Android et iOS peut nécessiter les étapes supplémentaires de cette section. Pour plus d’informations, consultez [se connecter à des services Web locaux à partir des simulateurs iOS et des émulateurs Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) .

Par défaut, IIS Express répond uniquement aux demandes à `localhost`. Les appareils distants (par exemple, un appareil Android, un iPhone ou même un simulateur) n’ont pas accès à votre service WCF local. Vous devez connaître l’adresse IP de votre station de travail Windows 10 sur le réseau local. Dans le cadre de cet exemple, supposons que votre station de travail possède `192.168.1.143`l’adresse IP. Les étapes suivantes expliquent comment configurer Windows 10 et IIS Express pour accepter les connexions à distance et se connecter au service à partir d’un appareil physique ou virtuel:

1. **Ajoutez une exception au pare-feu Windows**. Vous devez ouvrir un port via le pare-feu Windows, que les applications de votre sous-réseau peuvent utiliser pour communiquer avec le service WCF. Créez une règle de trafic entrant ouvrant le port 49393 dans le pare-feu. À partir d’une invite de commandes d’administration, exécutez la commande suivante:

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurez IIS Express pour accepter les connexions à distance**. Vous pouvez configurer IIS Express en modifiant le fichier de configuration pour IIS Express dans **[répertoire de\.la solution] vs\config\applicationhost.config**. Recherchez l' `site` élément portant le nom `TodoWCFService`. Elle doit ressembler au code XML suivant:

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

    Vous devez ajouter deux `binding` éléments pour ouvrir le port 49393 vers le trafic externe et l’émulateur Android. La liaison utilise un `[IP address]:[port]:[hostname]` format qui spécifie comment IIS Express répondra aux demandes. Les demandes externes auront des noms d' `binding`hôtes qui doivent être spécifiés en tant que. Ajoutez le code XML suivant à `bindings` l’élément, en remplaçant l’adresse IP par votre propre adresse IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Après vos modifications, `bindings` l’élément doit ressembler à ce qui suit:

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
    >Par défaut, IIS Express n’acceptera pas les connexions à partir de sources externes pour des raisons de sécurité. Pour activer les connexions à partir d’appareils distants, vous devez exécuter IIS Express avec des autorisations d’administration. Pour ce faire, la méthode la plus simple consiste à exécuter Visual Studio 2017 avec des autorisations d’administration. Cela permet de lancer IIS Express avec des autorisations d’administration lors de l’exécution de TodoWCFService.

    Une fois ces étapes terminées, vous devez être en mesure d’exécuter le TodoWCFService et de vous connecter à partir d’autres appareils sur votre sous-réseau. Vous pouvez tester cela en exécutant votre application et en `http://localhost:49393/TodoService.svc`visitant. Si vous recevez une erreur de **demande** incorrecte lors de la `bindings` visite de cette URL, cela peut être incorrect dans la configuration du IIS Express (la demande atteint IIS Express mais est rejetée). Si vous recevez une erreur différente, il se peut que votre application ne soit pas en cours d’exécution ou que votre pare-feu ne soit pas correctement configuré.

    Pour permettre à IIS Express de continuer à s’exécuter et à servir le service, désactivez l’option **modifier & continuer** dans les **propriétés du projet > débogueurs de > Web**.

1. **Personnaliser les appareils de point de terminaison à utiliser pour accéder au service**. Cette étape implique la configuration de l’application cliente, exécutée sur un appareil physique ou émulé, pour accéder au service WCF.

    L’émulateur Android utilise un proxy interne qui empêche l’émulateur d’accéder directement à l’adresse de `localhost` l’ordinateur hôte. Au lieu de cela `10.0.2.2` , l’adresse sur l’émulateur est `localhost` routée vers sur l’ordinateur hôte via un proxy interne. Ces demandes `127.0.0.1` de proxy auront comme nom d’hôte dans l’en-tête de la demande, c’est pourquoi vous avez créé la liaison IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    Le simulateur iOS s’exécute sur un hôte de build Mac, même si vous utilisez le [simulateur iOS distant pour Windows](~/tools/ios-simulator/index.md). Les demandes réseau du simulateur auront votre adresse IP de station de travail sur le réseau local en tant que nom d' `192.168.1.143`hôte (dans cet exemple, il s’agit de, mais votre adresse IP réelle sera probablement différente). C’est la raison pour laquelle vous avez créé la liaison de IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    Vérifiez que `SoapUrl` la propriété dans le fichier **constants.cs** du projet TodoWCF (portable) a des valeurs correctes pour votre réseau:

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

    Une fois que vous avez configuré le **constants.cs** avec les points de terminaison appropriés, vous devez être en mesure de vous connecter au TodoWCFService en cours d’exécution sur votre station de travail Windows 10 à partir d’appareils physiques ou virtuels.

## <a name="related-links"></a>Liens connexes

- [TodoWCF (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [Guide pratique pour Créer un client Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Outil utilitaire de métadonnées ServiceModel (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
