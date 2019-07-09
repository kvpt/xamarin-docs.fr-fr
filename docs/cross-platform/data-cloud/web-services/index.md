---
title: Introduction aux services web
description: Ce guide montre comment utiliser les technologies de service web différent. Les sujets abordés incluent la communication avec les services REST, les services SOAP et les services Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b8edc0141807ead60170ec78b3f6348723ab8e18
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650138"
---
# <a name="introduction-to-web-services"></a>Introduction aux services web

_Ce guide montre comment utiliser les technologies de service web différent. Les sujets abordés incluent la communication avec les services REST, les services SOAP et les services Windows Communication Foundation._

Pour fonctionner correctement, de nombreuses applications mobiles sont dépendantes sur le cloud, et par conséquent, l’intégration de services web dans les applications mobiles est un scénario courant. La plateforme Xamarin prend en charge l’utilisation des technologies de services web différents et inclut la prise en charge intégrée et par des tiers pour consommer des services Windows Communication Foundation (WCF) RESTful et ASMX.

Pour les clients à l’aide de Xamarin.Forms, il existe des exemples complets d’utilisation de chacune de ces technologies dans le [Xamarin.Forms Web Services](~/xamarin-forms/data-cloud/index.yml) documentation.

> [!IMPORTANT]
> Dans iOS 9, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles.
> Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.

Vous pouvez adhérer à de ATS si elle n’est pas possible d’utiliser le `HTTPS` de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

Representational State Transfer (REST) est un style d’architecture pour la création de services web. Demandes REST sont établies via HTTP en utilisant les mêmes verbes HTTP qui utilisent de navigateurs web pour récupérer les pages web et d’envoyer des données aux serveurs. Les verbes sont :

- **OBTENIR** : cette opération est utilisée pour récupérer des données à partir du service web.
- **POST** : cette opération est utilisée pour créer un nouvel élément de données sur le service web.
- **PUT** – cette opération est utilisée pour mettre à jour un élément de données sur le service web.
- **CORRECTIF** : cette opération est utilisée pour mettre à jour un élément de données sur le service web en décrivant un ensemble d’instructions sur la façon dont l’élément doit être modifié. Ce verbe n’est pas utilisé dans l’exemple d’application.
- **SUPPRIMER** – cette opération est utilisée pour supprimer un élément de données sur le service web.

API qui adhèrent aux REST de service Web sont appelées des API RESTful et sont définis à l’aide de :

- Un URI de base.
- Méthodes HTTP, telles que GET, POST, PUT, PATCH ou DELETE.
- Un type de média pour les données, tels que JavaScript Objet Notation (JSON).

La simplicité de REST a permis de rendre la méthode principale pour accéder aux services web dans les applications mobiles.

## <a name="consuming-rest-services"></a>Consommation de Services REST

Il existe un certain nombre de bibliothèques de classes qui peuvent être utilisées pour l’utilisation des services REST, et les sous-sections suivantes décrivent les. Pour plus d’informations sur l’utilisation d’un service REST, consultez [consommer un Service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md).

### <a name="httpclient"></a>HttpClient

Le [Microsoft HTTP Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http) fournit le `HttpClient` (classe), qui est utilisé pour envoyer et recevoir des demandes via HTTP. Il fournit des fonctionnalités pour l’envoi de demandes HTTP et la réception des réponses HTTP à partir d’une ressource identifiée d’URI. Chaque requête est envoyée en tant qu’une opération asynchrone. Pour plus d’informations sur les opérations asynchrones, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

Le `HttpResponseMessage` classe représente un message de réponse HTTP reçu du service web après la réalisation d’une requête HTTP. Il contient des informations sur la réponse, y compris le code d’état, des en-têtes et corps. Le `HttpContent` classe représente le corps HTTP et des en-têtes de contenu, tel que `Content-Type` et `Content-Encoding`. Le contenu peut être lu à l’aide de la `ReadAs` méthodes, telles que `ReadAsStringAsync` et `ReadAsByteArrayAsync`, selon le format des données.

Pour plus d’informations sur la `HttpClient` de classe, consultez [création de l’objet HTTPClient](~/xamarin-forms/data-cloud/web-services/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

Appel de services web avec `HTTPWebRequest` implique :

-  Création de l’instance de la demande d’un URI particulier.
-  Définir plusieurs propriétés HTTP sur l’instance de requête.
-  Récupérer un `HttpWebResponse` à partir de la demande.
-  Lecture des données en dehors de la réponse.

Par exemple, le code suivant récupère des données aux États-Unis. Service web de bibliothèque de médecine national :

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
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

L’exemple ci-dessus crée un `HttpWebRequest` qui retourne les données au format JSON. Les données sont retournées dans un `HttpWebResponse`, à partir de laquelle un `StreamReader` peut être obtenu pour lire les données.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

À l’aide d’une autre approche pour la consommation de services REST le [RestSharp](http://restsharp.org/) bibliothèque. RestSharp encapsule les requêtes HTTP, y compris la prise en charge de la récupération des résultats en tant que contenu de chaîne brute ou comme un désérialisées C# objet. Par exemple, le code suivant effectue une demande vers les États-Unis Service web de bibliothèque de médecine nationales et récupère les résultats sous forme de JSON au format chaîne :

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` est une méthode qui prendra la chaîne JSON brute à partir de la `RestSharp.RestResponse.Content` propriété et le convertir en un C# objet. La désérialisation des données retournées à partir de services web est abordée plus loin dans cet article.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

En plus des classes disponibles dans la base de Mono bibliothèque de classes (BCL), tel que `HttpWebRequest`et les tiers C# bibliothèques, telles que RestSharp, classes spécifiques à la plateforme sont également disponibles pour la consommation de services web. Par exemple, dans iOS, le `NSUrlConnection` et `NSMutableUrlRequest` classes peuvent être utilisées.

L’exemple de code suivant montre comment appeler le fuseau horaire Service web de bibliothèque de médecine nationaux à l’aide des classes d’iOS :

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
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

En règle générale, les classes spécifiques à la plateforme pour consommer des services web doivent être limitées aux scénarios où le code natif est porté vers C#. Si possible, code d’accès aux services web doivent être portable afin qu’il puisse être partagé inter-plateformes.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Une autre option pour appeler des services web est la [Service Stack](http://www.servicestack.net/) bibliothèque. Par exemple, le code suivant montre comment utiliser la pile Service `IServiceClient.GetAsync` méthode pour émettre une demande de service :

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
> Bien que les outils, tels que ServiceStack et RestSharp facilitent appeler et consommer REST des services, il est parfois non négligeable pour consommer XML ou JSON n’est pas conforme à la norme _DataContract_ conventions de sérialisation. Si nécessaire, appelez la demande et gérer la sérialisation appropriée explicitement à l’aide de la bibliothèque ServiceStack.Text décrite ci-dessous.


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Utilisation des données RESTful

Les services web rESTful utilisent généralement des messages JSON pour retourner des données au client. JSON est un texte, échange de données format qui produit compact charges utiles, entraînant ainsi la bande passante réduite lors de l’envoi de données. Dans cette section, nous étudierons mécanismes pour consommer des réponses RESTful dans JSON et Plain-Old-XML (POX).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

La plateforme Xamarin est livré avec la prise en charge de JSON prêts à l’emploi. En utilisant un `JsonObject`, résultats peuvent être récupérés comme indiqué dans l’exemple de code suivant :

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

Toutefois, il est important de savoir que le `System.Json` outils chargent l’intégralité des données en mémoire.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

Le [bibliothèque NewtonSoft JSON.NET](http://www.newtonsoft.com/json) est une bibliothèque largement utilisée pour sérialiser et désérialiser les messages JSON. L’exemple de code suivant montre comment utiliser JSON.NET pour désérialiser un message JSON dans un C# objet :

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

ServiceStack.Text est une bibliothèque de sérialisation JSON conçue pour fonctionner avec la bibliothèque ServiceStack. L’exemple de code suivant montre comment analyser le JSON en utilisant un `ServiceStack.Text.JsonObject`:

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

En cas d’utilisation d’un service de web REST basé sur XML, LINQ to XML peut être utilisé pour analyser le code XML et remplir un C# objet inline, comme illustré dans l’exemple de code suivant :

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

ASMX offre la possibilité de créer des services web qui envoient des messages à l’aide de l’accès protocole SOAP (Simple Object). SOAP est un protocole indépendant de la plateforme et indépendant du langage pour la création et l’accès aux services web. Consommateurs d’un service ASMX n’avez pas besoin de savoir quoi que ce soit sur la plateforme, le modèle d’objet ou le langage de programmation utilisé pour implémenter le service. Ils doivent uniquement comprendre comment envoyer et recevoir des messages SOAP.

Un message SOAP est un document XML contenant les éléments suivants :

- Un élément racine nommé *enveloppe* qui identifie le document XML sous forme de message SOAP.
- Facultatif *en-tête* élément qui contient des informations spécifiques aux applications telles que les données de l’authentification. Si le *en-tête* élément est présent il doit être le premier élément enfant de le *enveloppe* élément.
- Obligatoire *corps* élément qui contient le message SOAP destiné au destinataire.
- Facultatif *pannes* élément qui est utilisé pour indiquer les messages d’erreur. Si le *erreur* élément est présent, il doit être un élément enfant de le *corps* élément.

SOAP peut opérer sur plusieurs protocoles de transport, y compris HTTP, SMTP, TCP et UDP. Toutefois, un service ASMX ne peut agir que sur HTTP. La plateforme Xamarin prend en charge les implémentations SOAP 1.1 standard sur HTTP, et cela inclut la prise en charge pour la plupart des configurations de service ASMX standards.

### <a name="generating-a-proxy"></a>Génération d’un Proxy

Un *proxy* doit être généré pour l’utilisation d’un service ASMX, ce qui permet à l’application pour se connecter au service. Le proxy est construit par la consommation des métadonnées de service qui définissant les méthodes et la configuration de service associé. Ces métadonnées sont exposée comme un document Web Services Description Language (WSDL) généré par le service web. Le proxy est généré à l’aide de Visual Studio pour Mac ou Visual Studio pour ajouter une référence web pour le service web pour les projets spécifiques à la plateforme.

L’URL du service web peut être une source distante hébergée ou une ressource de système de fichiers local accessible via la `file:///` préfixe de chemin d’accès, par exemple :

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "L’URL du service web peut être une source distante hébergée ou une ressource de système de fichiers local accessible via le préfixe de chemin d’accès de fichier")](images/add-webreference-dialog.png#lightbox)

Cela génère le proxy dans le dossier Web ou des références de Service du projet. Dans la mesure où un proxy est généré le code, il ne doit pas être modifié.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Ajout d’un Proxy manuellement à un projet

Si vous avez un proxy existant qui a été généré à l’aide d’outils compatibles, cette sortie peut être consommée lorsque inclus dans le cadre de votre projet. Dans Visual Studio pour Mac, utilisez le **ajouter des fichiers...** option de menu pour ajouter le proxy. En outre, cela nécessite *System.Web.Services.dll* à être référencée explicitement en utilisant le **ajouter des références...** boîte de dialogue.

### <a name="consuming-the-proxy"></a>Utilisation du Proxy

Les classes proxy générées fournissent des méthodes pour la consommation du service web qui utilisent le modèle de conception de modèle de programmation asynchrone (APM). Dans ce modèle, une opération asynchrone est implémentée en tant que deux méthodes nommées *Beginnomopération* et *Endnomopération*, qui commencent et terminent l’opération asynchrone.

Le *Beginnomopération* méthode commence l’opération asynchrone et retourne un objet qui implémente le `IAsyncResult` interface. Après avoir appelé *Beginnomopération*, une application peut continuer à exécuter des instructions sur le thread appelant, tandis que l’opération asynchrone se déroule sur un thread de pool de threads.

Pour chaque appel à *Beginnomopération*, l’application doit également appeler *Endnomopération* pour obtenir les résultats de l’opération. La valeur de retour de *Endnomopération* est du même type que celui retourné par la méthode de service web synchrone. L’exemple de code suivant montre un exemple de ceci :

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

La bibliothèque parallèle de tâches (TPL) peut simplifier le processus d’utilisation d’une paire de méthodes begin/end APM en encapsulant les opérations asynchrones dans le même `Task` objet. Cette encapsulation est fournie par plusieurs surcharges de la `Task.Factory.FromAsync` (méthode). Cette méthode crée un `Task` qui exécute le `TodoService.EndGetTodoItems` méthode une fois le `TodoService.BeginGetTodoItems` méthode se termine, avec le `null` paramètre indiquant qu’aucune donnée n’est passée dans le `BeginGetTodoItems` déléguer. Enfin, la valeur de la `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Pour plus d’informations sur l’APM, consultez [modèle de programmation asynchrone](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) et [TPL et Framework programmation asynchrone .NET traditionnelle](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) sur MSDN.

Pour plus d’informations sur l’utilisation d’un service ASMX, consultez [consommer un Service Web de ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF est un cadre unifié de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées traitées, fiables, sécurisées et interopérables.

WCF décrit un service avec un large éventail de marchés différents qui sont les suivantes :

- **Contrats de données** : définir les structures de données qui constituent la base pour le contenu d’un message.
- **Contrats de message** – composer des messages à partir des contrats de données existants.
- **Contrats de l’erreur** – autoriser les erreurs SOAP personnalisées à être spécifié.
- **Contrats de service** : spécifier les opérations qui prennent en charge des services et les messages requis pour l’interaction avec chaque opération. Ils spécifient également tout comportement d’erreur personnalisé qui peut être associé à des opérations sur chaque service.

Il existe des différences entre ASP.NET Web Services (ASMX) et WCF, mais il est important de comprendre que WCF prend en charge les mêmes fonctionnalités que ASMX fournit – les messages SOAP sur HTTP.

> [!IMPORTANT]
> La prise en charge de la plateforme Xamarin pour WCF est limité à texte encodé des messages SOAP via HTTP/HTTPS en utilisant la `BasicHttpBinding` classe. En outre, la prise en charge WCF nécessite l’utilisation des outils disponibles uniquement dans un environnement Windows pour générer le proxy.

### <a name="generating-a-proxy"></a>Génération d’un Proxy

Un *proxy* doit être généré pour utiliser un service WCF, ce qui permet à l’application pour se connecter au service. Le proxy est construit par la consommation des métadonnées de service qui définissent les méthodes et la configuration de service associé. Ces métadonnées sont présentées sous la forme d’un document Web Services Description Language (WSDL) généré par le service web. Le proxy peut être généré à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 pour ajouter une référence de service pour le service web à une bibliothèque .NET Standard.

Une alternative à la création du proxy à l’aide de la Microsoft WCF Web Service Reference Provider dans Visual Studio 2017 consiste à utiliser ServiceModel Metadata Utility Tool (svcutil.exe). Pour plus d’informations, consultez [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configuration du Proxy

Configuration du proxy généré prennent généralement deux arguments de configuration (en fonction de SOAP 1.1/ASMX ou WCF) lors de l’initialisation : le `EndpointAddress` et/ou les informations de liaison associées, comme illustré dans l’exemple ci-dessous :

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

Une liaison est utilisée pour spécifier le transport, le codage et les détails de protocole requis pour les applications et services communiquer entre eux. Le `BasicHttpBinding` Spécifie que les messages SOAP encodés en texte seront envoyés via le protocole de transport HTTP. Spécification d’une adresse de point de terminaison permet à l’application pour se connecter à différentes instances du service WCF, si plusieurs instances publiées.

### <a name="consuming-the-proxy"></a>Utilisation du Proxy

Les classes proxy générées fournissent des méthodes de consommer les services web qui utilisent le modèle de conception de modèle de programmation asynchrone (APM). Dans ce modèle, une opération asynchrone est implémentée en tant que deux méthodes nommées *Beginnomopération* et *Endnomopération*, qui commencent et terminent l’opération asynchrone.

Le *Beginnomopération* méthode commence l’opération asynchrone et retourne un objet qui implémente le `IAsyncResult` interface. Après avoir appelé *Beginnomopération*, une application peut continuer à exécuter des instructions sur le thread appelant, tandis que l’opération asynchrone se déroule sur un thread de pool de threads.

Pour chaque appel à *Beginnomopération*, l’application doit également appeler *Endnomopération* pour obtenir les résultats de l’opération. La valeur de retour de *Endnomopération* est du même type que celui retourné par la méthode de service web synchrone. L’exemple de code suivant montre un exemple de ceci :

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

La bibliothèque parallèle de tâches (TPL) peut simplifier le processus d’utilisation d’une paire de méthodes begin/end APM en encapsulant les opérations asynchrones dans le même `Task` objet. Cette encapsulation est fournie par plusieurs surcharges de la `Task.Factory.FromAsync` (méthode). Cette méthode crée un `Task` qui exécute le `TodoServiceClient.EndGetTodoItems` méthode une fois le `TodoServiceClient.BeginGetTodoItems` méthode se termine, avec le `null` paramètre indiquant qu’aucune donnée n’est passée dans le `BeginGetTodoItems` déléguer. Enfin, la valeur de la `TaskCreationOptions` énumération spécifie que le comportement par défaut pour la création et l’exécution de tâches doit être utilisé.

Pour plus d’informations sur l’APM, consultez [modèle de programmation asynchrone](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) et [TPL et Framework programmation asynchrone .NET traditionnelle](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) sur MSDN.

Pour plus d’informations sur l’utilisation d’un service WCF, consultez [consommer un Service Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>À l’aide de la sécurité du Transport

Les Services WCF peuvent utiliser la sécurité au niveau du transport pour vous protéger contre l’interception de messages. La plateforme Xamarin prend en charge les liaisons qui utilisent la sécurité de niveau transport à l’aide de SSL. Toutefois, il peut arriver dans lequel la pile devrez peut-être valider le certificat, ce qui entraîne un comportement imprévu. La validation peut être substituée en inscrivant un `ServerCertificateValidationCallback` délégué avant d’appeler le service, comme illustré dans l’exemple de code suivant :

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Cela maintient le chiffrement du transport en ignorant la validation de certificat côté serveur. Toutefois, cette approche ne tient pas compte les problèmes de confiance associées au certificat efficacement et peut ne pas convenir. Pour plus d’informations, consultez [à l’aide de confiance racines Sincères salutations](https://www.mono-project.com/UsingTrustedRootsRespectfully) sur [mono-Project](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>À l’aide des informations d’identification de Client Security

Les services WCF peuvent également nécessiter les clients de service pour s’authentifier à l’aide des informations d’identification. La plateforme Xamarin ne prend pas en charge le protocole WS-Security, ce qui permet aux clients d’envoyer des informations d’identification à l’intérieur de l’enveloppe du message SOAP. Toutefois, la plateforme Xamarin prend en charge la possibilité d’envoyer des informations d’identification de l’authentification de base HTTP sur le serveur en spécifiant la `ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Ensuite, les informations d’identification de l’authentification de base peuvent être spécifiées :

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

Dans l’exemple ci-dessus, si vous recevez le message « Ran hors trampolines de type 0 » vous pouvez augmenter le nombre de trampolines de type 0 en ajoutant le `–aot “trampolines={number of trampolines}”` l’argument de la build. Pour plus d’informations, consultez [Dépannage](~/ios/troubleshooting/troubleshooting.md#trampolines).

Pour plus d’informations sur l’authentification de base HTTP, bien que dans le contexte d’un service web REST, consultez [l’authentification d’un Service Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Liens associés

- [Services Web dans Xamarin.Forms](~/xamarin-forms/data-cloud/index.yml)
- [Service Model Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
