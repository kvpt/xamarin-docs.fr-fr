---
titre : « utiliser un service Web Windows Communication Foundation (WCF) » : « cet article montre comment utiliser un service SOAP (Simple Object Access Protocol) WCF à partir d’une Xamarin.Forms application ».
ms. Prod : xamarin ms. AssetID : 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 03/28/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Utiliser un service Web Windows Communication Foundation (WCF)

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF est l’infrastructure unifiée de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées sécurisées, fiables, transactionnelles et interopérables. Cet article montre comment utiliser un service SOAP (Simple Object Access Protocol) WCF à partir d’une Xamarin.Forms application._

WCF décrit un service avec différents contrats, notamment :

- **Contrats de données** : définissez les structures de données qui forment la base du contenu dans un message.
- **Contrats de message** : composez des messages à partir de contrats de données existants.
- **Contrats d’erreur** : permet de spécifier des erreurs SOAP personnalisées.
- **Contrats de service** : spécifiez les opérations prises en charge par les services et les messages nécessaires à l’interaction avec chaque opération. Ils spécifient également tout comportement d’erreur personnalisé qui peut être associé à des opérations sur chaque service.

Il existe des différences entre les services Web ASP.NET (ASMX) et WCF, mais WCF prend en charge les mêmes fonctionnalités que celles fournies par le service ASMX : les messages SOAP sur HTTP. Pour plus d’informations sur l’utilisation d’un service ASMX, consultez [utilisation des services Web ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> La prise en charge de la plateforme Xamarin pour WCF est limitée aux messages SOAP encodés en texte via HTTP/HTTPs à l’aide de la `BasicHttpBinding` classe.
>
> La prise en charge de WCF requiert l’utilisation d’outils uniquement disponibles dans un environnement Windows pour générer le proxy et héberger le TodoWCFService. Pour générer et tester l’application iOS, vous devez déployer le TodoWCFService sur un ordinateur Windows ou en tant que service Web Azure.
>
> Les applications Xamarin Forms natives partagent généralement du code avec une bibliothèque de classes .NET Standard. Toutefois, .NET Core ne prend pas en charge WCF pour le moment, le projet partagé doit être une bibliothèque de classes portables héritée. Pour plus d’informations sur la prise en charge de WCF dans .NET Core, consultez [choix entre .net Core et .NET Framework pour les applications serveur](/dotnet/standard/choosing-core-framework-server).

L’exemple de solution d’application comprend un service WCF qui peut être exécuté localement et est illustré dans la capture d’écran suivante :

![](wcf-images/portal.png "Sample Application")

> [!NOTE]
> Dans iOS 9 et versions ultérieures, application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Dans la mesure où ATS est activé par défaut dans les applications générées pour iOS 9, toutes les connexions sont soumises aux exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, elles échouent avec une exception.
>
> L’ATS peut être exclu de s’il n’est pas possible d’utiliser le `HTTPS` protocole et la communication sécurisée pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consommer le services web

Le service WCF fournit les opérations suivantes :

|Opération|Description|Paramètres|
|--- |--- |--- |
|GetTodoItems|Obtenir une liste de tâches|
|CreateTodoItem|Créer un nouvel élément de tâche|TodoItem sérialisé XML|
|EditTodoItem|Mettre à jour une tâche|TodoItem sérialisé XML|
|DeleteTodoItem|Supprimer une tâche|TodoItem sérialisé XML|

Pour plus d’informations sur le modèle de données utilisé dans l’application, consultez [modélisation des données](~/xamarin-forms/data-cloud/web-services/introduction.md).

Un *proxy* doit être généré pour consommer un service WCF, ce qui permet à l’application de se connecter au service. Le proxy est construit en consommant des métadonnées de service qui définissent les méthodes et la configuration de service associée. Ces métadonnées sont exposées sous la forme d’un document Web Services Description Language (WSDL) qui est généré par le service Web. Le proxy peut être généré à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 pour ajouter une référence de service pour le service Web à une bibliothèque de .NET Standard. Une alternative à la création du proxy à l’aide de l’Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 consiste à utiliser l’outil ServiceModel Metadata Utility Tool (svcutil.exe). Pour plus d’informations, consultez outil de l' [utilitaire de métadonnées ServiceModel (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Les classes proxy générées fournissent des méthodes pour consommer les services Web qui utilisent le modèle de conception APM (Asynchronous Programming Model). Dans ce modèle, une opération asynchrone est implémentée sous la forme de deux méthodes nommées *BeginNomOpération* et *EndNomOpération*, qui commencent et terminent l’opération asynchrone.

La méthode *BeginNomOpération* commence l’opération asynchrone et retourne un objet qui implémente l' `IAsyncResult` interface. Après l’appel de *BeginNomOpération*, une application peut continuer à exécuter des instructions sur le thread appelant, tandis que l’opération asynchrone a lieu sur un thread de pool de threads.

Pour chaque appel à *BeginNomOpération*, l’application doit également appeler *EndNomOpération* pour obtenir les résultats de l’opération. La valeur de retour de *EndNomOpération* est identique à celle retournée par la méthode de service Web synchrone. Par exemple, la `EndGetTodoItems` méthode retourne une collection d' `TodoItem` instances. La méthode *EndNomOpération* comprend également un `IAsyncResult` paramètre qui doit être défini sur l’instance retournée par l’appel correspondant à la méthode *BeginNomOpération* .

La bibliothèque parallèle de tâches (TPL) peut simplifier le processus d’utilisation d’une paire de méthodes de programmation APM Begin/End en encapsulant les opérations asynchrones dans le même `Task` objet. Cette encapsulation est fournie par plusieurs surcharges de la `TaskFactory.FromAsync` méthode.

Pour plus d’informations sur APM, consultez [modèle de programmation asynchrone](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) et [bibliothèque parallèle de tâches et programmation asynchrone .NET Framework traditionnelle](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) sur MSDN.

### <a name="create-the-todoserviceclient-object"></a>Créer l’objet TodoServiceClient

La classe proxy générée fournit la `TodoServiceClient` classe, qui est utilisée pour communiquer avec le service WCF via http. Il fournit des fonctionnalités permettant d’appeler des méthodes de service Web en tant qu’opérations asynchrones à partir d’une instance de service identifiée par un URI. Pour plus d’informations sur les opérations asynchrones, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

L' `TodoServiceClient` instance est déclarée au niveau de la classe afin que l’objet soit valable tant que l’application a besoin de consommer le service WCF, comme illustré dans l’exemple de code suivant :

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

L' `TodoServiceClient` instance est configurée avec des informations de liaison et une adresse de point de terminaison. Une liaison est utilisée pour spécifier le transport, l’encodage et les détails de protocole requis pour que les applications et les services communiquent entre eux. Le `BasicHttpBinding` spécifie que les messages SOAP encodés en texte seront envoyés via le protocole de transport http. La spécification d’une adresse de point de terminaison permet à l’application de se connecter à différentes instances du service WCF, à condition qu’il y ait plusieurs instances publiées.

Pour plus d’informations sur la configuration de la référence de service, consultez [configuration de la référence de service](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Créer des objets de transfert de données

L’exemple d’application utilise la `TodoItem` classe pour modéliser les données. Pour stocker un `TodoItem` élément dans le service Web, il doit d’abord être converti vers le type généré par le proxy `TodoItem` . Cela est accompli par la `ToWCFServiceTodoItem` méthode, comme illustré dans l’exemple de code suivant :

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

Cette méthode crée simplement une `TodoWCFService.TodoItem` instance de et affecte à chaque propriété la même propriété à partir de l' `TodoItem` instance.

De même, lorsque les données sont récupérées à partir du service Web, elles doivent être converties du type généré par le proxy `TodoItem` en une `TodoItem` instance. Cela s’effectue à l’aide de la `FromWCFServiceTodoItem` méthode, comme illustré dans l’exemple de code suivant :

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

Cette méthode récupère simplement les données du type généré par le proxy `TodoItem` et les définit dans l’instance nouvellement créée `TodoItem` .

### <a name="retrieve-data"></a>Récupérer des données

Les `TodoServiceClient.BeginGetTodoItems` `TodoServiceClient.EndGetTodoItems` méthodes et sont utilisées pour appeler l' `GetTodoItems` opération fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un `Task` objet, comme illustré dans l’exemple de code suivant :

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

La `Task.Factory.FromAsync` méthode crée un `Task` qui exécute la `TodoServiceClient.EndGetTodoItems` méthode une fois la `TodoServiceClient.BeginGetTodoItems` méthode terminée, avec le `null` paramètre indiquant qu’aucune donnée n’est passée dans le `BeginGetTodoItems` délégué. Enfin, la valeur de l' `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

La `TodoServiceClient.EndGetTodoItems` méthode retourne un objet `ObservableCollection` d' `TodoWCFService.TodoItem` instances, qui est ensuite converti en un `List` d' `TodoItem` instances pour l’affichage.

### <a name="create-data"></a>Créer des données

Les `TodoServiceClient.BeginCreateTodoItem` `TodoServiceClient.EndCreateTodoItem` méthodes et sont utilisées pour appeler l' `CreateTodoItem` opération fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un `Task` objet, comme illustré dans l’exemple de code suivant :

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

La `Task.Factory.FromAsync` méthode crée un `Task` qui exécute la `TodoServiceClient.EndCreateTodoItem` méthode une fois la `TodoServiceClient.BeginCreateTodoItem` méthode terminée, avec le `todoItem` paramètre représentant les données passées dans le `BeginCreateTodoItem` délégué pour spécifier le `TodoItem` à créer par le service Web. Enfin, la valeur de l' `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Le service Web lève une exception `FaultException` s’il ne parvient pas à créer le `TodoItem` , qui est géré par l’application.

### <a name="update-data"></a>Mettre à jour des données

Les `TodoServiceClient.BeginEditTodoItem` `TodoServiceClient.EndEditTodoItem` méthodes et sont utilisées pour appeler l' `EditTodoItem` opération fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un `Task` objet, comme illustré dans l’exemple de code suivant :

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

La `Task.Factory.FromAsync` méthode crée un `Task` qui exécute la `TodoServiceClient.EndEditTodoItem` méthode une fois la `TodoServiceClient.BeginCreateTodoItem` méthode terminée, avec le `todoItem` paramètre représentant les données passées dans le `BeginEditTodoItem` délégué pour spécifier le `TodoItem` à mettre à jour par le service Web. Enfin, la valeur de l' `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Le service Web lève une exception `FaultException` s’il ne parvient pas à localiser ou à mettre à jour le `TodoItem` , qui est géré par l’application.

### <a name="delete-data"></a>Suppression de données

Les `TodoServiceClient.BeginDeleteTodoItem` `TodoServiceClient.EndDeleteTodoItem` méthodes et sont utilisées pour appeler l' `DeleteTodoItem` opération fournie par le service Web. Ces méthodes asynchrones sont encapsulées dans un `Task` objet, comme illustré dans l’exemple de code suivant :

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

La `Task.Factory.FromAsync` méthode crée un `Task` qui exécute la `TodoServiceClient.EndDeleteTodoItem` méthode une fois la `TodoServiceClient.BeginDeleteTodoItem` méthode terminée, avec le `id` paramètre représentant les données passées dans le `BeginDeleteTodoItem` délégué pour spécifier le `TodoItem` à supprimer par le service Web. Enfin, la valeur de l' `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Le service Web lève une exception `FaultException` s’il ne parvient pas à localiser ou à supprimer le `TodoItem` , qui est géré par l’application.

## <a name="configure-remote-access-to-iis-express"></a>Configuration de l’accès à distance à IIS Express
Dans Visual Studio 2017 ou Visual Studio 2019, vous devez être en mesure de tester l’application UWP sur un PC sans aucune configuration supplémentaire. Le test des clients Android et iOS peut nécessiter les étapes supplémentaires de cette section. Pour plus d’informations, consultez [se connecter à des services Web locaux à partir des simulateurs iOS et des émulateurs Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) .

Par défaut, IIS Express répond uniquement aux demandes à `localhost` . Les appareils distants (par exemple, un appareil Android, un iPhone ou même un simulateur) n’ont pas accès à votre service WCF local. Vous devez connaître l’adresse IP de votre station de travail Windows 10 sur le réseau local. Dans le cadre de cet exemple, supposons que votre station de travail possède l’adresse IP `192.168.1.143` . Les étapes suivantes expliquent comment configurer Windows 10 et IIS Express pour accepter les connexions à distance et se connecter au service à partir d’un appareil physique ou virtuel :

1. **Ajoutez une exception au pare-feu Windows**. Vous devez ouvrir un port via le pare-feu Windows, que les applications de votre sous-réseau peuvent utiliser pour communiquer avec le service WCF. Créez une règle de trafic entrant ouvrant le port 49393 dans le pare-feu. À partir d’une invite de commandes d’administration, exécutez la commande suivante :

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurez IIS Express pour accepter les connexions à distance**. Vous pouvez configurer IIS Express en modifiant le fichier de configuration pour IIS Express à l’adresse **[répertoire de la solution] \.vs\config\applicationhost.config**. Recherchez l' `site` élément portant le nom `TodoWCFService` . Elle doit ressembler au code XML suivant :

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

    Vous devez ajouter deux `binding` éléments pour ouvrir le port 49393 vers le trafic externe et l’émulateur Android. La liaison utilise un `[IP address]:[port]:[hostname]` format qui spécifie comment IIS Express répondra aux demandes. Les demandes externes auront des noms d’hôtes qui doivent être spécifiés en tant que `binding` . Ajoutez le code XML suivant à l' `bindings` élément, en remplaçant l’adresse IP par votre propre adresse IP :

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Après vos modifications, l' `bindings` élément doit ressembler à ce qui suit :

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

    Une fois ces étapes terminées, vous devez être en mesure d’exécuter le TodoWCFService et de vous connecter à partir d’autres appareils sur votre sous-réseau. Vous pouvez tester cela en exécutant votre application et en visitant `http://localhost:49393/TodoService.svc` . Si vous recevez une erreur de **Demande incorrecte** lors de la visite de cette URL, cela `bindings` peut être incorrect dans la configuration du IIS Express (la demande atteint IIS Express mais est rejetée). Si vous recevez une erreur différente, il se peut que votre application ne soit pas en cours d’exécution ou que votre pare-feu ne soit pas correctement configuré.

    Pour permettre à IIS Express de continuer à s’exécuter et à servir le service, désactivez l’option **modifier & continuer** dans les **propriétés du projet > débogueurs de > Web**.

1. **Personnaliser les appareils de point de terminaison à utiliser pour accéder au service**. Cette étape implique la configuration de l’application cliente, exécutée sur un appareil physique ou émulé, pour accéder au service WCF.

    L’émulateur Android utilise un proxy interne qui empêche l’émulateur d’accéder directement à l’adresse de l’ordinateur hôte `localhost` . Au lieu de cela, l’adresse `10.0.2.2` sur l’émulateur est routée vers `localhost` sur l’ordinateur hôte via un proxy interne. Ces demandes de proxy auront `127.0.0.1` comme nom d’hôte dans l’en-tête de la demande, c’est pourquoi vous avez créé la liaison IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    Le simulateur iOS s’exécute sur un hôte de build Mac, même si vous utilisez le [simulateur iOS distant pour Windows](~/tools/ios-simulator/index.md). Les demandes réseau du simulateur auront votre adresse IP de station de travail sur le réseau local en tant que nom d’hôte (dans cet exemple, il s’agit de `192.168.1.143` , mais votre adresse IP réelle sera probablement différente). C’est la raison pour laquelle vous avez créé la liaison de IIS Express pour ce nom d’hôte dans les étapes ci-dessus.

    Vérifiez `SoapUrl` que la propriété dans le fichier **constants.cs** du projet TodoWCF (portable) a des valeurs correctes pour votre réseau :

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
- [Outil utilitaire de métadonnées ServiceModel (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
