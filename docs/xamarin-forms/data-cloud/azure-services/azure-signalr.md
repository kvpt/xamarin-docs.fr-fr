---
title: Service Azure Signalr avecXamarin.Forms
description: Prise en main du service et des Azure Functions Azure Signalr avecXamarin.Forms
ms.prod: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ffa44beb68dc845a64d8bf2a9f86f6d7e56df8f9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139436"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Service Azure Signalr avecXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core Signalr est un modèle d’application qui simplifie le processus d’ajout de communication en temps réel aux applications. Le service Azure Signalr permet de développer et de déployer rapidement des applications Signalr évolutives. Azure Functions sont des méthodes de code sans serveur à courte durée de vie qui peuvent être combinées pour former des applications pilotées par des événements et des applications évolutives.

Cet article et cet exemple montrent comment combiner le service et le Azure Functions Azure Signalr avec Xamarin.Forms pour fournir des messages en temps réel aux clients connectés.

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Créer un service Azure Signalr et Azure Functions application

L’exemple d’application comprend trois composants clés : un hub de service Azure Signalr, une instance Azure Functions avec deux fonctions et une application mobile qui peut envoyer et recevoir des messages. Ces composants interagissent comme suit :

1. L’application mobile appelle une fonction **négocier** Azure pour obtenir des informations sur le concentrateur signalr.
1. L’application mobile utilise les informations de négociation pour s’inscrire auprès du concentrateur Signalr et forme une connexion.
1. Après l’inscription, l’application mobile publie des messages dans la fonction Azure de **discussion** .
1. La fonction de **communication** transmet le message entrant au concentrateur signalr.
1. Le concentrateur Signalr diffuse le message à toutes les instances d’application mobile connectée, y compris à l’expéditeur d’origine.

> [!IMPORTANT]
> Les fonctions **Negotiate** et **débats** de l’exemple d’application peuvent être exécutées localement à l’aide de Visual Studio 2019 et des outils d’exécution Azure. Toutefois, le service Azure Signalr ne peut pas être émulé localement et il est difficile d’exposer des Azure Functions hébergées localement à des appareils physiques ou virtuels à des fins de test. Il est recommandé de déployer le Azure Functions sur une instance d’application Azure Functions, car cela permet un test multiplateforme. Pour plus d’informations sur le déploiement, consultez [déployer des Azure Functions avec Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Créer un service Azure Signalr

Un service Azure Signalr peut être créé en choisissant **créer une ressource** dans le coin supérieur gauche de la portail Azure et en recherchant **signalr**. Le service Azure Signalr peut être créé au niveau gratuit. Le service Azure Signalr doit être en mode de service sans **serveur** . Si vous choisissez accidentellement le mode par défaut ou le mode de service classique, vous pouvez le modifier ultérieurement dans les propriétés du service Azure Signalr.

La capture d’écran suivante montre la création d’un nouveau service Signalr Azure :

![Capture d’écran de la création du service Azure Signalr dans le Portail Azure](azure-signalr-images/azure-signalr-create.png "Création d’un service Azure Signalr")

Une fois créé, la section **clés** d’un service Azure signalr contient une **chaîne de connexion**, qui est utilisée pour connecter l’application Azure Functions au concentrateur signalr. La capture d’écran suivante indique où trouver la chaîne de connexion dans le service Azure Signalr :

![Capture d’écran de la chaîne de connexion Azure Signalr dans le Portail Azure](azure-signalr-images/azure-signalr-connection-string.png "Chaîne de connexion Azure Signalr")

Cette chaîne de connexion est utilisée pour [déployer des Azure Functions à l’aide de Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Créer une application Azure Functions

Pour tester l’exemple d’application, vous devez créer une nouvelle Azure Functions application dans le Portail Azure. Prenez note du nom de l' **application** , car cette URL est utilisée dans le fichier **constants.cs** de l’exemple d’application. La capture d’écran suivante montre la création d’une nouvelle Azure Functions application appelée « xdocsfunctions » :

[![Capture d’écran de la création d’une application Azure Functions](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Azure Functions peut être déployé sur une instance d’application Azure Functions à partir de Visual Studio 2019. Les sections suivantes décrivent le déploiement de deux fonctions dans l’exemple d’application dans une instance d’application Azure Functions.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Azure Functions de build dans Visual Studio 2019

L’exemple d’application contient une bibliothèque de classes appelée **ChatServer**, qui inclut deux Azure Functions sans serveur dans les fichiers nommés **Negotiate.cs** et **Talk.cs**.

La `Negotiate` fonction répond aux requêtes Web avec un `SignalRConnectionInfo` objet qui contient une `AccessToken` propriété et une `Url` propriété. L’application mobile utilise ces valeurs pour s’inscrire auprès du concentrateur Signalr. Le code suivant illustre la `Negotiate` fonction :

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

La `Talk` fonction répond aux requêtes HTTP http qui fournissent un objet de message dans le corps de publication. Le corps de publication est transformé en un `SignalRMessage` et transmis au concentrateur signalr. Le code suivant illustre la `Talk` fonction :

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

Pour en savoir plus sur Azure Functions et les applications de Azure Functions, consultez [Azure Functions documentation](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Déployer Azure Functions avec Visual Studio 2019

Visual Studio 2019 vous permet de déployer des fonctions dans une application Azure Functions. Les fonctions hébergées par Azure facilitent les tests inter-plateformes en fournissant un point de terminaison de test accessible pour tous les appareils.

Cliquez avec le bouton droit sur l’application Sample Functions et choisissez **publier** pour lancer la boîte de dialogue permettant de publier des fonctions sur votre application Azure functions. Si vous avez suivi les étapes précédentes pour configurer un Function App Azure, vous pouvez choisir **Sélectionner existant** pour publier les exemples d’applications dans votre application Azure functions. La capture d’écran suivante montre les options de la boîte de dialogue publier dans Visual Studio 2019 :

![Boîte de dialogue publier les choix dans Visual Studio 2019](azure-signalr-images/vs-publish-target.png "Options de publication dans Visual Studio 2019")

Une fois que vous êtes connecté à votre compte Microsoft, vous pouvez rechercher et choisir votre application Azure Functions comme cible de publication. La capture d’écran suivante montre un exemple Azure Functions application dans la boîte de dialogue de publication de Visual Studio 2019 :

![Une application Azure Functions dans la boîte de dialogue de publication de Visual Studio 2019](azure-signalr-images/vs-app-selection.png "Azure Functions application dans la boîte de dialogue de publication de Visual Studio 2019")

Après la sélection d’une instance d’application Azure Functions, l’URL du site, la configuration et d’autres informations sur l’application Azure Functions cible sont affichées. Choisissez **modifier les paramètres de Azure App service** et entrez votre chaîne de connexion dans le champ **distant** . La chaîne de connexion est utilisée par les fonctions **Negotiate** et **débats** pour se connecter au service Azure signalr et est disponible dans la section **clés** du service Azure signalr de votre portail Azure. Pour plus d’informations sur la chaîne de connexion, consultez [Create a Azure signalr service](#create-an-azure-signalr-service).

Une fois que vous avez entré la chaîne de connexion, vous pouvez cliquer sur **publier** pour déployer vos fonctions vers l’application Azure functions. Une fois l’opération terminée, les fonctions sont listées dans l’application Azure Functions dans le Portail Azure. La capture d’écran suivante montre les fonctions publiées dans le Portail Azure :

![Fonctions publiées dans l’application Azure Functions](azure-signalr-images/azure-functions-deployed.png "Fonctions publiées dans l’application Azure Functions")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Intégrer le service Azure Signalr avecXamarin.Forms

L’intégration entre le service Azure Signalr et l' Xamarin.Forms application est une classe de service signalr qui est instanciée dans la `MainPage` classe avec des gestionnaires d’événements assignés à trois événements. Pour plus d’informations sur ces gestionnaires d’événements, consultez [utiliser la classe de service signalr dans Xamarin.Forms ](#use-the-signalr-service-class-in-xamarinforms).

L’exemple d’application comprend une classe **constants.cs** qui doit être personnalisée avec le point de terminaison URL de votre application Azure functions. Affectez à la propriété la valeur de `HostName` votre Azure Functions adresse de l’application. Le code suivant montre les propriétés **constants.cs** avec un exemple de `HostName` valeur :

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> La `Username` propriété dans le fichier **constants.cs** de l’exemple d’application utilise la valeur de l’appareil `RuntimePlatform` comme nom d’utilisateur. Cela facilite le test multiplateforme des appareils et l’identification de l’appareil qui envoie le message. Dans une application réelle, cette valeur est probablement un nom d’utilisateur unique, collecté au cours d’un processus d’inscription ou de connexion.

### <a name="the-signalr-service-class"></a>Classe de service Signalr

La `SignalRService` classe du projet **ChatClient** dans l’exemple d’application illustre une implémentation de qui appelle des fonctions dans une application Azure Functions pour se connecter à un service Azure signalr.

La `SendMessageAsync` méthode de la `SignalRService` classe est utilisée pour envoyer des messages aux clients connectés au service Azure signalr. Cette méthode exécute une requête HTTP POSTÉRIEURe à la fonction de **communication** hébergée dans l’application Azure functions, y compris un `Message` objet sérialisé JSON en tant que charge utile de publication. La fonction de **communication** transmet le message au service Azure signalr pour la diffusion à tous les clients connectés. Le code suivant montre la méthode `SendMessageAsync` :

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

La `ConnectAsync` méthode de la `SignalRService` classe exécute une requête HTTP sur la fonction **Negotiate** hébergée dans l’application Azure functions. La fonction **Negotiate** retourne JSON qui est désérialisé dans une instance de la `NegotiateInfo` classe. Une fois l' `NegotiateInfo` objet récupéré, il est utilisé pour s’inscrire directement auprès du service Azure signalr à l’aide d’une instance de la `HubConnection` classe.

ASP.NET Core Signalr traduit les données entrantes à partir de la connexion ouverte dans des messages, et permet aux développeurs de définir des types de messages et de lier des gestionnaires d’événements aux messages entrants par type. La `ConnectAsync` méthode inscrit un gestionnaire d’événements pour le nom de message défini dans le fichier **constants.cs** de l’exemple d’application, qui est « newMessage » par défaut.

Le code suivant montre la méthode `ConnectAsync` :

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .AddNewtonsoftJsonProtocol()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

> [!NOTE]
> Le service Signalr utilise `System.Text.Json` pour sérialiser et désérialiser JSON par défaut. Les données sérialisées avec d’autres bibliothèques, telles que Newtonsoft, peuvent ne pas être désérialisées par le service Signalr. L' `HubConnection` instance dans l’exemple de projet comprend un appel à `AddNewtonsoftJsonProtocol` pour spécifier le sérialiseur JSON. Cette méthode est définie dans un package NuGet spécial appelé **Microsoft. AspNetCore. signalr. Protocols. NewtonsoftJson** qui doit être inclus dans le projet. Si vous utilisez `System.Text.Json` pour sérialiser/désérialiser des données JSON, cette méthode et le package NuGet ne doivent pas être utilisés.

La `AddNewMessage` méthode est liée en tant que gestionnaire d’événements dans le `ConnectAsync` message, comme indiqué dans le code précédent. Lorsqu’un message est reçu, la `AddNewMessage` méthode est appelée avec les données de message fournies sous forme de `JObject` . La `AddNewMessage` méthode convertit `JObject` en une instance de la `Message` classe, puis appelle le gestionnaire pour `NewMessageReceived` s’il a été lié. Le code suivant montre la méthode `AddNewMessage` :

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Utilisez la classe de service Signalr dansXamarin.Forms

L’utilisation de la classe de service Signalr dans Xamarin.Forms est effectuée en liant les `SignalRService` événements de classe dans la `MainPage` classe code-behind.

L' `Connected` événement de la `SignalRService` classe est déclenché lorsqu’une connexion signalr est correctement effectuée. L' `ConnectionFailed` événement de la `SignalRService` classe est déclenché lors de l’échec d’une connexion signalr. La `SignalR_ConnectionChanged` méthode de gestionnaire d’événements est liée aux deux événements dans le `MainPage` constructeur. Ce gestionnaire d’événements met à jour les États de bouton se connecter et envoyer en fonction de l’argument de connexion `success` et ajoute le message fourni par l’événement à la file d’attente de conversation à l’aide de la `AddMessage` méthode. Le code suivant illustre la `SignalR_ConnectionChanged` méthode du gestionnaire d’événements :

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

L' `NewMessageReceived` événement de la `SignalRService` classe est déclenché lors de la réception d’un nouveau message à partir du service Azure signalr. La `SignalR_NewMessageReceived` méthode de gestionnaire d’événements est liée à l' `NewMessageReceived` événement dans le `MainPage` constructeur. Ce gestionnaire d’événements convertit l' `Message` objet entrant en une chaîne et l’ajoute à la file d’attente de conversation à l’aide de la `AddMessage` méthode. Le code suivant illustre la `SignalR_NewMessageReceived` méthode du gestionnaire d’événements :

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

La `AddMessage` méthode ajoute un nouveau message en tant qu' `Label` objet à la file d’attente de conversation. La `AddMessage` méthode est souvent appelée par les gestionnaires d’événements à partir de l’extérieur du thread d’interface utilisateur principal. elle force donc les mises à jour de l’interface utilisateur sur le thread principal pour empêcher les exceptions. Le code suivant montre la méthode `AddMessage` :

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>Test de l’application

L’application Signalr chat peut être testée sur iOS, Android et UWP, à condition que vous disposiez des éléments suivants :

1. Création d’un service Signalr Azure.
1. Création d’une application Azure Functions.
1. Personnalisation du fichier **constants.cs** avec le point de terminaison de l’application Azure functions.

Une fois que ces étapes sont terminées et que l’application est exécutée, le fait de cliquer sur le bouton **Connect** forme une connexion avec le service Azure signalr. Si vous tapez un message, puis cliquez sur le bouton **Envoyer** , les messages s’affichent dans la file d’attente de conversation sur toute application mobile connectée.

## <a name="related-links"></a>Liens connexes

* [Création d’applications mobiles en temps réel avec Xamarin et Signalr](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introduction à SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Présentation d’Azure Functions](/azure/azure-functions/functions-overview)
* [Documentation sur Azure Functions](/azure/azure-functions/)
* [Exemple de conversation MVVM Signalr](https://github.com/lbugnion/sample-xamarin-signalr)
