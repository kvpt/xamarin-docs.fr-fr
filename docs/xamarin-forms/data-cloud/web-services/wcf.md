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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305219"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Utiliser un service Web Windows Communication Foundation (WCF)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF est l’infrastructure unifiée de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées sécurisées, fiables, transactionnelles et interopérables. Cet article explique comment utiliser un service SOAP (Simple Object Access Protocol) WCF à partir d’une application Xamarin. Forms._

WCF décrit un service avec différents contrats, notamment :

- **Contrats de données** : définissez les structures de données qui forment la base du contenu dans un message.
- **Contrats de message** : composez des messages à partir de contrats de données existants.
- **Contrats d’erreur** : permet de spécifier des erreurs SOAP personnalisées.
- **Contrats de service** : spécifiez les opérations prises en charge par les services et les messages nécessaires à l’interaction avec chaque opération. Ils spécifient également tout comportement d’erreur personnalisé qui peut être associé à des opérations sur chaque service.

Il existe des différences entre les services Web ASP.NET (ASMX) et WCF, mais WCF prend en charge les mêmes fonctionnalités que celles fournies par le service ASMX : les messages SOAP sur HTTP. Pour plus d’informations sur l’utilisation d’un service ASMX, consultez [utilisation des services Web ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> La prise en charge de la plateforme Xamarin pour WCF est limitée aux messages SOAP encodés en texte via HTTP/HTTPs à l’aide de la classe `BasicHttpBinding`.
>
> La prise en charge de WCF requiert l’utilisation d’outils uniquement disponibles dans un environnement Windows pour générer le proxy et héberger le TodoWCFService. Pour générer et tester l’application iOS, vous devez déployer le TodoWCFService sur un ordinateur Windows ou en tant que service Web Azure.
>
> Les applications Xamarin Forms natives partagent généralement du code avec une bibliothèque de classes .NET Standard. Toutefois, .NET Core ne prend pas en charge WCF pour le moment, le projet partagé doit être une bibliothèque de classes portables héritée. Pour plus d’informations sur la prise en charge de WCF dans .NET Core, consultez [choix entre .net Core et .NET Framework pour les applications serveur](/dotnet/standard/choosing-core-framework-server).

L’exemple de solution d’application comprend un service WCF qui peut être exécuté localement et est illustré dans la capture d’écran suivante :

![](wcf-images/portal.png "Sample Application")

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
>
> L’ATS peut être exclu de s’il n’est pas possible d’utiliser le protocole `HTTPS` et de sécuriser la communication pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consommer le services web

Le service WCF fournit les opérations suivantes :

|Opération|Description|Paramètres|
|--- |--- |--- |
|GetTodoItems|Obtenir une liste de tâches|
|CreateTodoItem|Créer un élément de tâche|Un document XML sérialisé TodoItem|
|EditTodoItem|Mettre à jour une tâche|Un document XML sérialisé TodoItem|
|DeleteTodoItem|Supprimer une tâche|Un document XML sérialisé TodoItem|

Pour plus d’informations sur le modèle de données utilisé dans l’application, consultez [modélisation des données](~/xamarin-forms/data-cloud/web-services/introduction.md).

Un *proxy* doit être généré pour consommer un service WCF, ce qui permet à l’application de se connecter au service. Le proxy est construit par la consommation des métadonnées de service qui définissent les méthodes et la configuration de service associé. Ces métadonnées sont présentées sous la forme d’un document Web Services Description Language (WSDL) généré par le service web. Le proxy peut être généré à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 pour ajouter une référence de service pour le service web à une bibliothèque .NET Standard. Une alternative à la création du proxy à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 consiste à utiliser ServiceModel Metadata Utility Tool (svcutil.exe). Pour plus d’informations, consultez [outil ServiceModel Metadata Utility Tool (Svcutil. exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Les classes proxy générées fournissent des méthodes de consommer les services web qui utilisent le modèle de conception de modèle de programmation asynchrone (APM). Dans ce modèle, une opération asynchrone est implémentée sous la forme de deux méthodes nommées *BeginNomOpération* et *EndNomOpération*, qui commencent et terminent l’opération asynchrone.

La méthode *BeginNomOpération* commence l’opération asynchrone et retourne un objet qui implémente l’interface `IAsyncResult`. Après l’appel de *BeginNomOpération*, une application peut continuer à exécuter des instructions sur le thread appelant, tandis que l’opération asynchrone a lieu sur un thread de pool de threads.

Pour chaque appel à *BeginNomOpération*, l’application doit également appeler *EndNomOpération* pour obtenir les résultats de l’opération. La valeur de retour de *EndNomOpération* est identique à celle retournée par la méthode de service Web synchrone. Par exemple, la méthode `EndGetTodoItems` retourne une collection d’instances de `TodoItem`. La méthode *EndNomOpération* comprend également un paramètre `IAsyncResult` qui doit être défini sur l’instance retournée par l’appel correspondant à la méthode *BeginNomOpération* .

La bibliothèque parallèle de tâches (TPL) peut simplifier le processus d’utilisation d’une paire de méthodes de programmation APM Begin/End en encapsulant les opérations asynchrones dans le même objet `Task`. Cette encapsulation est fournie par plusieurs surcharges de la méthode `TaskFactory.FromAsync`.

Pour plus d’informations sur APM, consultez [modèle de programmation asynchrone](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) et [bibliothèque parallèle de tâches et programmation asynchrone .NET Framework traditionnelle](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) sur MSDN.

### <a name="create-the-todoserviceclient-object"></a>Créer l’objet TodoServiceClient

La classe proxy générée fournit la classe `TodoServiceClient`, qui est utilisée pour communiquer avec le service WCF via HTTP. Il offre des fonctionnalités permettant d’appeler des méthodes de service web comme des opérations asynchrones à partir d’un URI identifié l’instance de service. Pour plus d’informations sur les opérations asynchrones, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

L’instance de `TodoServiceClient` est déclarée au niveau de la classe afin que l’objet soit valable tant que l’application a besoin de consommer le service WCF, comme illustré dans l’exemple de code suivant :

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

L’instance de `TodoServiceClient` est configurée avec des informations de liaison et une adresse de point de terminaison. Une liaison est utilisée pour spécifier le transport, le codage et les détails de protocole requis pour les applications et services communiquer entre eux. L' `BasicHttpBinding` spécifie que les messages SOAP encodés en texte seront envoyés via le protocole de transport HTTP. Spécification d’une adresse de point de terminaison permet à l’application pour se connecter à différentes instances du service WCF, si plusieurs instances publiées.

Pour plus d’informations sur la configuration de la référence de service, consultez [configuration de la référence de service](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Créer des objets de transfert de données

L’exemple d’application utilise la classe `TodoItem` pour modéliser les données. Pour stocker un élément `TodoItem` dans le service Web, il doit d’abord être converti en type `TodoItem` généré par le proxy. Pour ce faire, la méthode `ToWCFServiceTodoItem`, comme indiqué dans l’exemple de code suivant :

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

Cette méthode crée simplement une instance de `TodoWCFService.TodoItem` et affecte à chaque propriété la même propriété à partir de l’instance `TodoItem`.

De même, lorsque les données sont récupérées à partir du service Web, elles doivent être converties du type de `TodoItem` généré par le proxy vers une instance `TodoItem`. Cela s’effectue à l’aide de la méthode `FromWCFServiceTodoItem`, comme illustré dans l’exemple de code suivant :

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

Cette méthode récupère simplement les données du type de `TodoItem` généré par le proxy et les définit dans l’instance de `TodoItem` nouvellement créée.

### <a name="retrieve-data"></a>Récupérer des données

Les méthodes `TodoServiceClient.BeginGetTodoItems` et `TodoServiceClient.EndGetTodoItems` sont utilisées pour appeler l’opération `GetTodoItems` fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un objet `Task`, comme illustré dans l’exemple de code suivant :

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

La méthode `Task.Factory.FromAsync` crée une `Task` qui exécute la méthode `TodoServiceClient.EndGetTodoItems` une fois la méthode `TodoServiceClient.BeginGetTodoItems` terminée, avec le paramètre `null` indiquant qu’aucune donnée n’est passée dans le délégué `BeginGetTodoItems`. Enfin, la valeur de l’énumération `TaskCreationOptions` spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

La méthode `TodoServiceClient.EndGetTodoItems` retourne une `ObservableCollection` d’instances `TodoWCFService.TodoItem`, qui est ensuite convertie en `List` de `TodoItem` instances à afficher.

### <a name="create-data"></a>Créer des données

Les méthodes `TodoServiceClient.BeginCreateTodoItem` et `TodoServiceClient.EndCreateTodoItem` sont utilisées pour appeler l’opération `CreateTodoItem` fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un objet `Task`, comme illustré dans l’exemple de code suivant :

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

La méthode `Task.Factory.FromAsync` crée une `Task` qui exécute la méthode `TodoServiceClient.EndCreateTodoItem` une fois la méthode `TodoServiceClient.BeginCreateTodoItem` terminée, avec le paramètre `todoItem` représentant les données passées dans le délégué `BeginCreateTodoItem` pour spécifier la `TodoItem` à créer par le service Web. Enfin, la valeur de l’énumération `TaskCreationOptions` spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Le service Web lève une `FaultException` s’il ne parvient pas à créer le `TodoItem`, qui est géré par l’application.

### <a name="update-data"></a>Mettre à jour des données

Les méthodes `TodoServiceClient.BeginEditTodoItem` et `TodoServiceClient.EndEditTodoItem` sont utilisées pour appeler l’opération `EditTodoItem` fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un objet `Task`, comme illustré dans l’exemple de code suivant :

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

La méthode `Task.Factory.FromAsync` crée une `Task` qui exécute la méthode `TodoServiceClient.EndEditTodoItem` une fois la méthode `TodoServiceClient.BeginCreateTodoItem` terminée, avec le paramètre `todoItem` représentant les données passées dans le délégué `BeginEditTodoItem` pour spécifier la `TodoItem` à mettre à jour par le service Web. Enfin, la valeur de l’énumération `TaskCreationOptions` spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Le service Web lève une `FaultException` s’il ne parvient pas à localiser ou à mettre à jour le `TodoItem`, qui est géré par l’application.

### <a name="delete-data"></a>Suppression de données

Les méthodes `TodoServiceClient.BeginDeleteTodoItem` et `TodoServiceClient.EndDeleteTodoItem` sont utilisées pour appeler l’opération `DeleteTodoItem` fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un objet `Task`, comme illustré dans l’exemple de code suivant :

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

La méthode `Task.Factory.FromAsync` crée une `Task` qui exécute la méthode `TodoServiceClient.EndDeleteTodoItem` une fois la méthode `TodoServiceClient.BeginDeleteTodoItem` terminée, avec le paramètre `id` représentant les données passées dans le délégué `BeginDeleteTodoItem` pour spécifier la `TodoItem` à supprimer par le service Web. Enfin, la valeur de l’énumération `TaskCreationOptions` spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Le service Web lève une `FaultException` s’il ne parvient pas à localiser ou à supprimer le `TodoItem`, qui est géré par l’application.

## <a name="configure-remote-access-to-iis-express"></a>Configuration de l’accès à distance à IIS Express
Dans Visual Studio 2017 ou Visual Studio 2019, vous devez être en mesure de tester l’application UWP sur un PC sans aucune configuration supplémentaire. Le test des clients Android et iOS peut nécessiter les étapes supplémentaires de cette section. Pour plus d’informations, consultez [se connecter à des services Web locaux à partir des simulateurs iOS et des émulateurs Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) .

Par défaut, IIS Express répondra uniquement aux demandes adressées à `localhost`. Les appareils distants (par exemple, un appareil Android, un iPhone ou même un simulateur) n’ont pas accès à votre service WCF local. Vous devez connaître l’adresse IP de votre station de travail Windows 10 sur le réseau local. Dans le cadre de cet exemple, supposons que votre station de travail possède l’adresse IP `192.168.1.143`. Les étapes suivantes expliquent comment configurer Windows 10 et IIS Express pour accepter les connexions à distance et se connecter au service à partir d’un appareil physique ou virtuel :

1. **Ajoutez une exception au pare-feu Windows**. Vous devez ouvrir un port via le pare-feu Windows, que les applications de votre sous-réseau peuvent utiliser pour communiquer avec le service WCF. Créez une règle de trafic entrant ouvrant le port 49393 dans le pare-feu. À partir d’une invite de commandes d’administration, exécutez la commande suivante :

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurez IIS Express pour accepter les connexions à distance**. Vous pouvez configurer IIS Express en modifiant le fichier de configuration pour IIS Express dans **[répertoire de la solution]\.vs\config\applicationhost.config**. Recherchez l’élément `site` portant le nom `TodoWCFService`. Elle doit ressembler au code XML suivant :

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

    Vous devez ajouter deux éléments de `binding` pour ouvrir le port 49393 au trafic externe et l’émulateur Android. La liaison utilise un format de `[IP address]:[port]:[hostname]` qui spécifie comment IIS Express répondra aux demandes. Les demandes externes auront des noms d’hôtes qui doivent être spécifiés en tant que `binding`. Ajoutez le code XML suivant à l’élément `bindings`, en remplaçant l’adresse IP par votre propre adresse IP :

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Une fois vos modifications apportées, l’élément `bindings` doit ressembler à ce qui suit :

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

    Une fois ces étapes terminées, vous devez être en mesure d’exécuter le TodoWCFService et de vous connecter à partir d’autres appareils sur votre sous-réseau. Vous pouvez tester cela en exécutant votre application et en visitant `http://localhost:49393/TodoService.svc`. Si vous recevez une erreur de **Demande incorrecte** lors de la visite de cette URL, votre `bindings` peut être incorrecte dans la configuration du IIS Express (la demande atteint IIS Express mais est rejetée). Si vous recevez une erreur différente, il se peut que votre application ne soit pas en cours d’exécution ou que votre pare-feu ne soit pas correctement configuré.

    Pour permettre à IIS Express de continuer à s’exécuter et à servir le service, désactivez l’option **modifier & continuer** dans les **propriétés du projet > débogueurs de > Web**.

1. **Personnaliser les appareils de point de terminaison à utiliser pour accéder au service**. Cette étape implique la configuration de l’application cliente, exécutée sur un appareil physique ou émulé, pour accéder au service WCF.

    L’émulateur Android utilise un proxy interne qui empêche l’émulateur d’accéder directement à l’adresse `localhost` de l’ordinateur hôte. Au lieu de cela, l’adresse `10.0.2.2` sur l’émulateur est routée vers `localhost` sur l’ordinateur hôte via un proxy interne. Ces demandes de proxy auront `127.0.0.1` comme nom d’hôte dans l’en-tête de la demande, c’est pourquoi vous avez créé la liaison IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    Le simulateur iOS s’exécute sur un hôte de build Mac, même si vous utilisez le [simulateur iOS distant pour Windows](~/tools/ios-simulator/index.md). Les demandes réseau du simulateur auront votre adresse IP de station de travail sur le réseau local en tant que nom d’hôte (dans cet exemple, elle est `192.168.1.143`, mais votre adresse IP réelle sera probablement différente). C’est la raison pour laquelle vous avez créé la liaison de IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    Vérifiez que la propriété `SoapUrl` dans le fichier **constants.cs** du projet TodoWCF (portable) a des valeurs correctes pour votre réseau :

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
- [Comment : créer un client Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Outil utilitaire de métadonnées ServiceModel (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
