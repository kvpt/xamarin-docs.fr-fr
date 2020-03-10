---
title: Introduction aux services web
description: Ce guide montre comment utiliser différentes technologies de service Web. Les sujets abordés incluent la communication avec les services REST, les services SOAP et les services Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: ebd7cad9ef33a44dbc7aa469bb4e866bdfea2e61
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915380"
---
# <a name="introduction-to-web-services"></a>Introduction aux services web

_Ce guide montre comment utiliser différentes technologies de service Web. Les sujets abordés incluent la communication avec les services REST, les services SOAP et les services Windows Communication Foundation._

Pour fonctionner correctement, de nombreuses applications mobiles dépendent du Cloud et l’intégration des services Web dans les applications mobiles est un scénario courant. La plateforme Xamarin prend en charge l’utilisation de différentes technologies de services Web et comprend une prise en charge intégrée et intégrée de la consommation des services RESTful, ASMX et Windows Communication Foundation (WCF).

Pour les clients qui utilisent Xamarin. Forms, il existe des exemples complets qui utilisent chacune de ces technologies dans la documentation des [services Web Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml) .

> [!IMPORTANT]
> Dans iOS 9, application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles.
> Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.

Vous pouvez désactiver l’ATS s’il n’est pas possible d’utiliser le protocole `HTTPS` et de sécuriser la communication pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

Representational State Transfer (REST) est un style d’architecture pour la création de services web. Demandes REST sont établies via HTTP en utilisant les mêmes verbes HTTP qui utilisent de navigateurs web pour récupérer les pages web et d’envoyer des données aux serveurs. Les verbes sont :

- **Obtenir** : cette opération est utilisée pour récupérer des données du service Web.
- **Après** : cette opération est utilisée pour créer un nouvel élément de données sur le service Web.
- **Put** : cette opération est utilisée pour mettre à jour un élément de données sur le service Web.
- **Correctif** : cette opération est utilisée pour mettre à jour un élément de données sur le service Web en décrivant un ensemble d’instructions sur la façon dont l’élément doit être modifié. Ce verbe n’est pas utilisé dans l’exemple d’application.
- **Supprimer** : cette opération permet de supprimer un élément de données sur le service Web.

API qui adhèrent aux REST de service Web sont appelées des API RESTful et sont définis à l’aide de :

- Un URI de base.
- Méthodes HTTP, telles que GET, POST, PUT, PATCH ou DELETE.
- Un type de média pour les données, tels que JavaScript Objet Notation (JSON).

La simplicité de REST a permis de rendre la méthode principale pour accéder aux services web dans les applications mobiles.

## <a name="consuming-rest-services"></a>Utilisation des services REST

Il existe plusieurs bibliothèques et classes qui peuvent être utilisées pour consommer des services REST, et les sous-sections suivantes les discutent. Pour plus d’informations sur l’utilisation d’un service REST, consultez [consommer un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md).

### <a name="httpclient"></a>HttpClient

Les [bibliothèques clientes http Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http) fournissent la classe `HttpClient`, qui est utilisée pour envoyer et recevoir des demandes via http. Il fournit des fonctionnalités permettant d’envoyer des requêtes HTTP et de recevoir des réponses HTTP d’une ressource identifiée par un URI. Chaque requête est envoyée en tant qu’une opération asynchrone. Pour plus d’informations sur les opérations asynchrones, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

La classe `HttpResponseMessage` représente un message de réponse HTTP reçu du service Web après l’établissement d’une requête HTTP. Elle contient des informations sur la réponse, notamment le code d’État, les en-têtes et le corps. La classe `HttpContent` représente le corps HTTP et les en-têtes de contenu, tels que `Content-Type` et `Content-Encoding`. Le contenu peut être lu à l’aide de l’une des méthodes de `ReadAs`, telles que `ReadAsStringAsync` et `ReadAsByteArrayAsync`, en fonction du format des données.

Pour plus d’informations sur la classe `HttpClient`, consultez [création de l’objet httpclient](~/xamarin-forms/data-cloud/web-services/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

L’appel de services Web avec `HTTPWebRequest` implique :

- Création de l’instance de requête pour un URI particulier.
- Définition de différentes propriétés HTTP sur l’instance de demande.
- Récupération d’un `HttpWebResponse` à partir de la demande.
- Lecture des données en dehors de la réponse.

Par exemple, le code suivant récupère des données à partir de la Bibliothèque nationale américaine du service Web Medicine :

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

L’exemple ci-dessus crée une `HttpWebRequest` qui retournera des données au format JSON. Les données sont retournées dans un `HttpWebResponse`, à partir duquel un `StreamReader` peut être obtenu pour lire les données.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Une autre approche de l’utilisation des services REST consiste à utiliser la bibliothèque [RestSharp](http://restsharp.org/) . RestSharp encapsule les requêtes HTTP, y compris la prise en charge de la récupération des résultats en tant que contenu de C# chaîne brute ou en tant qu’objet désérialisé. Par exemple, le code suivant envoie une demande à la Bibliothèque nationale de médecine des États-Unis et récupère les résultats sous la forme d’une chaîne au format JSON :

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` est une méthode qui prend la chaîne JSON brute de la propriété `RestSharp.RestResponse.Content` et la convertit en C# objet. La désérialisation des données retournées par les services Web est décrite plus loin dans cet article.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

En plus des classes disponibles dans la bibliothèque de classes de base mono (BCL), telles que les `HttpWebRequest`et C# les bibliothèques tierces, telles que RestSharp, les classes spécifiques à la plateforme sont également disponibles pour l’utilisation des services Web. Par exemple, dans iOS, les classes `NSUrlConnection` et `NSMutableUrlRequest` peuvent être utilisées.

L’exemple de code suivant montre comment appeler la Bibliothèque nationale américaine du service Web Medicine à l’aide des classes iOS :

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

En règle générale, les classes spécifiques à la plateforme pour consommer des services Web doivent être limitées aux scénarios où le code natif C#est porté vers. Dans la mesure du possible, le code d’accès au service Web doit être portable pour pouvoir être partagé entre les plateformes.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Une autre option pour appeler des services Web est la bibliothèque de [pile de services](https://servicestack.net) . Par exemple, le code suivant montre comment utiliser la méthode `IServiceClient.GetAsync` de la pile de services pour émettre une demande de service :

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> Bien que les outils tels que ServiceStack et RestSharp facilitent l’appel et l’utilisation des services REST, il est parfois difficile de consommer du code XML ou JSON qui n’est pas conforme aux conventions de sérialisation _DataContract_ standard. Si nécessaire, appelez la demande et gérez explicitement la sérialisation appropriée à l’aide de la bibliothèque ServiceStack. Text présentée ci-dessous.

<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Consommation de données RESTful

Les services web rESTful utilisent généralement des messages JSON pour retourner des données au client. JSON est un format texte d’échange de données qui produit des charges utiles compactes, ce qui entraîne une réduction des besoins en bande passante lors de l’envoi de données. Dans cette section, les mécanismes de consommation des réponses RESTful en JSON et en Plain-Old-XML (POX) seront examinés.

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

La plateforme Xamarin est fournie avec la prise en charge de JSON prête à l’emploi. À l’aide d’un `JsonObject`, les résultats peuvent être récupérés comme indiqué dans l’exemple de code suivant :

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

Toutefois, il est important de savoir que les outils de `System.Json` chargent l’intégralité des données en mémoire.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

La [bibliothèque NewtonSoft JSON.net](https://www.newtonsoft.com/json) est une bibliothèque largement utilisée pour sérialiser et désérialiser des messages JSON. L’exemple de code suivant montre comment utiliser JSON.NET pour désérialiser un message JSON dans un C# objet :

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack. Text est une bibliothèque de sérialisation JSON conçue pour fonctionner avec la bibliothèque ServiceStack. L’exemple de code suivant montre comment analyser JSON à l’aide d’un `ServiceStack.Text.JsonObject`:

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

En cas d’utilisation d’un service Web REST basé sur XML, LINQ to XML peut être utilisé pour analyser le code XML et remplir un C# objet en ligne, comme illustré dans l’exemple de code suivant :

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>Service Web ASP.NET (ASMX)

ASMX offre la possibilité de créer des services Web qui envoient des messages à l’aide du protocole SOAP (Simple Object Access Protocol). SOAP est un protocole indépendant de la plateforme et indépendant du langage pour la création et l’accès aux services web. Consommateurs d’un service ASMX n’avez pas besoin de savoir quoi que ce soit sur la plateforme, le modèle d’objet ou le langage de programmation utilisé pour implémenter le service. Ils doivent uniquement comprendre comment envoyer et recevoir des messages SOAP.

Un message SOAP est un document XML contenant les éléments suivants :

- Élément racine nommé *Envelope* qui identifie le document XML en tant que message SOAP.
- Élément d' *en-tête* facultatif qui contient des informations spécifiques à l’application, telles que des données d’authentification. Si l’élément d' *en-tête* est présent, il doit s’agir du premier élément enfant de l’élément *Envelope* .
- Élément de *corps* requis qui contient le message SOAP destiné au destinataire.
- *Élément d’erreur facultatif* utilisé pour indiquer les messages d’erreur. Si l’élément *Fault* est présent, il doit s’agir d’un élément enfant de l’élément *Body* .

SOAP peut opérer sur plusieurs protocoles de transport, y compris HTTP, SMTP, TCP et UDP. Toutefois, un service ASMX ne peut agir que sur HTTP. La plateforme Xamarin prend en charge les implémentations SOAP 1.1 standard sur HTTP, et cela inclut la prise en charge pour la plupart des configurations de service ASMX standards.

### <a name="generating-a-proxy"></a>Génération d’un proxy

Un *proxy* doit être généré pour consommer un service asmx, ce qui permet à l’application de se connecter au service. Le proxy est construit par la consommation des métadonnées de service qui définissant les méthodes et la configuration de service associé. Ces métadonnées sont exposées en tant que document Web Services Description Language (WSDL) qui est généré par le service Web. Le proxy est généré à l’aide de Visual Studio pour Mac ou Visual Studio pour ajouter une référence Web pour le service Web aux projets spécifiques à la plateforme.

L’URL du service Web peut être une source distante hébergée ou une ressource du système de fichiers local accessible via le préfixe de chemin d’accès `file:///`, par exemple :

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "The web service URL can either be a hosted remote source or local file system resource accessible via the file path prefix")](images/add-webreference-dialog.png#lightbox)

Cela génère le proxy dans le dossier Web ou Service References du projet. Étant donné qu’un proxy est généré en code, il ne doit pas être modifié.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Ajout manuel d’un proxy à un projet

Si vous avez un proxy existant qui a été généré à l’aide d’outils compatibles, cette sortie peut être consommée lorsqu’elle est incluse dans le cadre de votre projet. Dans Visual Studio pour Mac, utilisez **Ajouter des fichiers...** option de menu permettant d’ajouter le proxy. En outre, cela nécessite que *System. Web. services. dll* soit explicitement référencé à l’aide de la fonction **Ajouter des références...** dialogue.

### <a name="consuming-the-proxy"></a>Consommation du proxy

Les classes proxy générées fournissent des méthodes pour la consommation du service web qui utilisent le modèle de conception de modèle de programmation asynchrone (APM). Dans ce modèle, une opération asynchrone est implémentée sous la forme de deux méthodes nommées *BeginNomOpération* et *EndNomOpération*, qui commencent et terminent l’opération asynchrone.

La méthode *BeginNomOpération* commence l’opération asynchrone et retourne un objet qui implémente l’interface `IAsyncResult`. Après l’appel de *BeginNomOpération*, une application peut continuer à exécuter des instructions sur le thread appelant, tandis que l’opération asynchrone a lieu sur un thread de pool de threads.

Pour chaque appel à *BeginNomOpération*, l’application doit également appeler *EndNomOpération* pour obtenir les résultats de l’opération. La valeur de retour de *EndNomOpération* est identique à celle retournée par la méthode de service Web synchrone. L’exemple de code ci-dessous en est un exemple :

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

La bibliothèque parallèle de tâches (TPL) peut simplifier le processus d’utilisation d’une paire de méthodes de programmation APM Begin/End en encapsulant les opérations asynchrones dans le même objet `Task`. Cette encapsulation est fournie par plusieurs surcharges de la méthode `Task.Factory.FromAsync`. Cette méthode crée une `Task` qui exécute la méthode `TodoService.EndGetTodoItems` une fois la méthode `TodoService.BeginGetTodoItems` terminée, avec le paramètre `null` indiquant qu’aucune donnée n’est passée dans le délégué `BeginGetTodoItems`. Enfin, la valeur de l’énumération `TaskCreationOptions` spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Pour plus d’informations sur APM, consultez [modèle de programmation asynchrone](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) et [bibliothèque parallèle de tâches et programmation asynchrone .NET Framework traditionnelle](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) sur MSDN.

Pour plus d’informations sur l’utilisation d’un service ASMX, consultez [consommer un service Web ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF est l’infrastructure unifiée de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées traitées, fiables, sécurisées et interopérables.

WCF décrit un service avec un large éventail de marchés différents qui sont les suivantes :

- **Contrats de données** : définissez les structures de données qui forment la base du contenu dans un message.
- **Contrats de message** : composez des messages à partir de contrats de données existants.
- **Contrats d’erreur** : permet de spécifier des erreurs SOAP personnalisées.
- **Contrats de service** : spécifiez les opérations prises en charge par les services et les messages nécessaires à l’interaction avec chaque opération. Ils spécifient également tout comportement d’erreur personnalisé qui peut être associé à des opérations sur chaque service.

Il existe des différences entre ASP.NET Web Services (ASMX) et WCF, mais il est important de comprendre que WCF prend en charge les mêmes fonctionnalités que ASMX fournit – les messages SOAP sur HTTP.

> [!IMPORTANT]
> La prise en charge de la plateforme Xamarin pour WCF est limitée aux messages SOAP encodés en texte via HTTP/HTTPs à l’aide de la classe `BasicHttpBinding`. En outre, la prise en charge WCF nécessite l’utilisation des outils disponibles uniquement dans un environnement Windows pour générer le proxy.

### <a name="generating-a-proxy"></a>Génération d’un proxy

Un *proxy* doit être généré pour consommer un service WCF, ce qui permet à l’application de se connecter au service. Le proxy est construit par la consommation des métadonnées de service qui définissent les méthodes et la configuration de service associé. Ces métadonnées sont présentées sous la forme d’un document Web Services Description Language (WSDL) généré par le service web. Le proxy peut être généré à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 pour ajouter une référence de service pour le service Web à une bibliothèque de .NET Standard.

Une alternative à la création du proxy à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 consiste à utiliser ServiceModel Metadata Utility Tool (svcutil.exe). Pour plus d’informations, consultez [outil ServiceModel Metadata Utility Tool (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configuration du proxy

La configuration du proxy généré prend généralement deux arguments de configuration (selon SOAP 1.1/ASMX ou WCF) pendant l’initialisation : le `EndpointAddress` et/ou les informations de liaison associées, comme indiqué dans l’exemple ci-dessous :

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

Une liaison est utilisée pour spécifier le transport, le codage et les détails de protocole requis pour les applications et services communiquer entre eux. L' `BasicHttpBinding` spécifie que les messages SOAP encodés en texte seront envoyés via le protocole de transport HTTP. Spécification d’une adresse de point de terminaison permet à l’application pour se connecter à différentes instances du service WCF, si plusieurs instances publiées.

### <a name="consuming-the-proxy"></a>Consommation du proxy

Les classes proxy générées fournissent des méthodes de consommer les services web qui utilisent le modèle de conception de modèle de programmation asynchrone (APM). Dans ce modèle, une opération asynchrone est implémentée sous la forme de deux méthodes nommées *BeginNomOpération* et *EndNomOpération*, qui commencent et terminent l’opération asynchrone.

La méthode *BeginNomOpération* commence l’opération asynchrone et retourne un objet qui implémente l’interface `IAsyncResult`. Après l’appel de *BeginNomOpération*, une application peut continuer à exécuter des instructions sur le thread appelant, tandis que l’opération asynchrone a lieu sur un thread de pool de threads.

Pour chaque appel à *BeginNomOpération*, l’application doit également appeler *EndNomOpération* pour obtenir les résultats de l’opération. La valeur de retour de *EndNomOpération* est identique à celle retournée par la méthode de service Web synchrone. L’exemple de code ci-dessous en est un exemple :

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

La bibliothèque parallèle de tâches (TPL) peut simplifier le processus d’utilisation d’une paire de méthodes de programmation APM Begin/End en encapsulant les opérations asynchrones dans le même objet `Task`. Cette encapsulation est fournie par plusieurs surcharges de la méthode `Task.Factory.FromAsync`. Cette méthode crée une `Task` qui exécute la méthode `TodoServiceClient.EndGetTodoItems` une fois la méthode `TodoServiceClient.BeginGetTodoItems` terminée, avec le paramètre `null` indiquant qu’aucune donnée n’est passée dans le délégué `BeginGetTodoItems`. Enfin, la valeur de l’énumération `TaskCreationOptions` spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Pour plus d’informations sur APM, consultez [modèle de programmation asynchrone](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) et [bibliothèque parallèle de tâches et programmation asynchrone .NET Framework traditionnelle](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) sur MSDN.

Pour plus d’informations sur l’utilisation d’un service WCF, consultez [consommer un service Web Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Utilisation de la sécurité de transport

Les services WCF peuvent utiliser la sécurité au niveau du transport pour empêcher l’interception des messages. La plateforme Xamarin prend en charge les liaisons qui utilisent la sécurité au niveau du transport à l’aide de SSL. Toutefois, il peut arriver que la pile doive valider le certificat, ce qui entraîne un comportement inattendu. La validation peut être substituée en inscrivant un délégué `ServerCertificateValidationCallback` avant d’appeler le service, comme illustré dans l’exemple de code suivant :

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Cela maintient le chiffrement du transport tout en ignorant la validation du certificat côté serveur. Toutefois, cette approche ne tient pas compte des préoccupations d’approbation associées au certificat et peut ne pas convenir. Pour plus d’informations, consultez [utilisation des racines de confiance respectueusement](https://www.mono-project.com/UsingTrustedRootsRespectfully) sur [mono-Project.com](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Utilisation de la sécurité des informations d’identification du client

Les services WCF peuvent également nécessiter que les clients du service s’authentifient à l’aide des informations d’identification. La plateforme Xamarin ne prend pas en charge le protocole WS-Security, qui permet aux clients d’envoyer des informations d’identification à l’intérieur de l’enveloppe de message SOAP. Toutefois, la plateforme Xamarin prend en charge la possibilité d’envoyer des informations d’authentification de base HTTP au serveur en spécifiant le `ClientCredentialType`approprié :

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Ensuite, vous pouvez spécifier les informations d’identification de l’authentification de base :

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

Dans l’exemple ci-dessus, si vous recevez le message « trampolines de type 0 », vous pouvez augmenter le nombre de type 0 trampolines en ajoutant l’argument `–aot “trampolines={number of trampolines}”` à la Build. Pour plus d’informations, consultez la page [Dépannage](~/ios/troubleshooting/troubleshooting.md#trampolines).

Pour plus d’informations sur l’authentification HTTP de base, bien que dans le contexte d’un service Web REST, consultez [authentification d’un service Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Liens connexes

- [Services Web dans Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml)
- [Outil utilitaire de métadonnées ServiceModel (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
