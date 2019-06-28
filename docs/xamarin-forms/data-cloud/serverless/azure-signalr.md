---
title: Services Azure SignalR avec Xamarin.Forms
description: Bien démarrer avec le Service Azure SignalR et Azure Functions avec Xamarin.Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: d79ffb844a65c145fd6cb22a5a3e1dfc7a6bfe41
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2019
ms.locfileid: "67418122"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Services Azure SignalR avec Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureSignalR)

ASP.NET Core SignalR est un modèle d’application qui simplifie le processus d’ajout de communication en temps réel aux applications. Service Azure SignalR permet un développement rapide et le déploiement d’applications évolutives de SignalR. Les fonctions Azure sont des méthodes de code de courte durée de vie, sans serveur qui peuvent être combinés au formulaire pilotée par événements des applications évolutives.

Cet article et les exemples montrent comment combiner Azure SignalR Service et Azure Functions avec Xamarin.Forms pour remettre des messages en temps réel aux clients connectés.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Créer un Service Azure SignalR et l’application Azure Functions

L’exemple d’application comprend trois composants clés : un Service Azure SignalR hub, une instance d’Azure Functions avec deux fonctions et une application mobile qui peut envoyer et recevoir des messages. Ces composants interagissent comme suit :

1. L’application mobile appelle un **Negotiate** Azure Function pour obtenir des informations sur le concentrateur SignalR.
1. L’application mobile utilise les informations de négociation pour s’inscrire auprès du hub SignalR et constitue une connexion.
1. Après l’inscription, l’application mobile publie des messages sur le **parler** Azure Function.
1. Le **parler** fonction transmet le message entrant pour le concentrateur SignalR.
1. Le concentrateur SignalR diffuse le message à toutes les instances de l’application mobile connectée, y compris l’expéditeur d’origine.

> [!NOTE]
> Le **Negotiate** et **parler** fonctions dans l’exemple d’application peuvent être exécutées localement à l’aide de Visual Studio 2019 et les outils de runtime Azure. Toutefois, le Service Azure SignalR ne peut pas être émulé localement, et il est difficile d’exposer hébergées localement Azure Functions aux appareils physiques ou virtuels pour le test. Il est recommandé de déployer les fonctions Azure à une instance de l’application de fonctions Azure car il autorise le test inter-plateformes. Pour plus d’informations de déploiement, consultez [déployer Azure Functions avec Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Créer un Service Azure SignalR

Un Service Azure SignalR peuvent être créé en choisissant **créer une ressource** dans le coin supérieur gauche du portail Azure et recherchez **SignalR**. Le Service Azure SignalR peuvent être créé au niveau gratuit. Le Service Azure SignalR doit être dans **sans serveur** mode service. Si vous choisissez accidentellement le par défaut ou le mode de service classique, vous pouvez la modifier ultérieurement dans les propriétés du Service Azure SignalR.

La capture d’écran suivante illustre la création d’un nouveau Service de SignalR Azure :

![La création de la capture d’écran du Service Azure SignalR dans le portail Azure](azure-signalr-images/azure-signalr-create.png "création d’un Service Azure SignalR")

Une fois créé, le **clés** section d’un Service Azure SignalR contient un **chaîne de connexion**, ce qui permet de connecter l’application de fonctions Azure pour le concentrateur SignalR. La capture d’écran suivante montre où trouver la chaîne de connexion dans le Service Azure SignalR :

![Chaîne de connexion de capture d’écran de Azure SignalR dans le portail Azure](azure-signalr-images/azure-signalr-connection-string.png "chaîne de connexion Azure SignalR")

Cette chaîne de connexion est utilisée pour [déployer Azure Functions avec Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Créer une application Azure Functions

Pour tester l’exemple d’application, vous devez créer une nouvelle application de fonctions Azure dans le portail Azure. Prenez note de la **nom de l’application** car cette URL est utilisée dans l’exemple d’application **Constants.cs** fichier. La capture d’écran suivante illustre la création d’une nouvelle application de fonctions Azure appelé « xdocsfunctions » :

[![Création de la capture d’écran de l’application Azure Functions](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Fonctions Azure peuvent être déployées à une instance d’application Azure Function App à partir de Visual Studio 2019. Les sections suivantes décrivent le déploiement de deux fonctions dans l’exemple d’application à une instance d’application Azure Functions.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Créer des fonctions Azure dans Visual Studio 2019

L’exemple d’application contienne une bibliothèque de classes appelée **ChatServer**, qui inclut deux fonctions Azure sans serveur de fichiers appelés **Negotiate.cs** et **Talk.cs**.

Le `Negotiate` fonction répond aux demandes web avec un `SignalRConnectionInfo` objet qui contient un `AccessToken` propriété et un `Url` propriété. L’application mobile utilise ces valeurs pour s’inscrire auprès du hub SignalR. Le code suivant montre le `Negotiate` (fonction) :

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

Le `Talk` fonction répond aux requêtes HTTP POST qui fournissent un objet de message dans le corps POST. Le corps POST est transformé en un `SignalRMessage` et transféré vers le concentrateur SignalR. Le code suivant montre le `Talk` (fonction) :

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

Pour en savoir plus sur Azure functions et les applications de fonctions Azure, consultez [documentation Azure Functions](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Déployer Azure Functions avec Visual Studio 2019

Visual Studio 2019 vous permet de déployer des fonctions à une application de fonctions Azure. Fonctions hébergées par Azure facilitent le test d’inter-plateformes en fournissant un point de terminaison test accessible pour tous les appareils.

L’exemple d’application fonctions effectuant un clic droit et en choisissant **publier** lance la boîte de dialogue pour publier des fonctions dans votre application Azure Functions. Si vous avez suivi les étapes précédentes pour configurer une application de fonction Azure, vous pouvez choisir **sélectionner** pour publier les exemples d’applications pour votre application Azure Functions. La capture d’écran suivante montre les options de la boîte de dialogue de la publication dans Visual Studio 2019 :

![Boîte de dialogue Publier choix dans Visual Studio 2019](azure-signalr-images/vs-publish-target.png "publier les options dans Visual Studio 2019")

Une fois que vous êtes connecté à votre compte Microsoft, vous pouvez rechercher et choisir votre application Azure Functions en tant que cible de publication. La capture d’écran suivante illustre une application Azure Function App dans la boîte de dialogue de publication de Visual Studio 2019 :

![Une application de fonctions Azure dans la boîte de dialogue de publication de Visual Studio 2019](azure-signalr-images/vs-app-selection.png "application Azure Function App dans la boîte de dialogue publication de Visual Studio 2019")

Après avoir sélectionné une application de fonctions Azure instance, l’URL du Site, configuration et autres informations sur l’application de fonctions Azure cible sont affichés. Choisissez **modifier les paramètres Azure App Service** et entrez votre chaîne de connexion dans le **distant** champ. La chaîne de connexion est utilisée par le **Negotiate** et **parler** fonctions pour vous connecter au Service Azure SignalR et est disponible dans le **clés** section de SignalR Azure Service dans votre portail Azure. Pour plus d’informations sur la chaîne de connexion, consultez [créer un Service Azure SignalR](#create-an-azure-signalr-service).

Une fois que vous avez entré la chaîne de connexion, vous pouvez cliquer sur **publier** pour déployer vos fonctions à l’application de fonctions Azure. Une fois terminé, les fonctions seront afficheront dans l’application de fonctions Azure dans le portail Azure. La capture d’écran suivante montre les fonctions publiées dans le portail Azure :

![Fonctions publiées dans l’application de fonctions Azure](azure-signalr-images/azure-functions-deployed.png "fonctions publiées dans l’application de fonctions Azure")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Intégrer le Service Azure SignalR avec Xamarin.Forms

L’intégration entre le Service Azure SignalR et de l’application Xamarin.Forms est une classe de service de SignalR est instanciée dans le `MainPage` classe avec les gestionnaires d’événements affectés à trois événements. Pour plus d’informations sur ces gestionnaires d’événements, consultez [utiliser la classe de service SignalR dans Xamarin.Forms](#use-the-signalr-service-class-in-xamarinforms).

L’exemple d’application inclut un **Constants.cs** classe doit être personnalisée avec le point de terminaison de l’URL de votre application Azure Functions. Définissez la valeur de la `HostName` propriété à votre adresse de l’application Azure Functions. Le code suivant illustre la **Constants.cs** propriétés avec un exemple `HostName` valeur :

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
> Le `Username` propriété dans l’exemple d’application **Constants.cs** fichier utilise l’appareil `RuntimePlatform` valeur en tant que le nom d’utilisateur. Cela rend plus facile pour les appareils inter-plateformes de test et identifier le périphérique qui envoie le message. Dans une application réelle, cette valeur peut être un nom d’utilisateur unique, recueillies pendant une connexion ou connectez-vous aux processus.

### <a name="the-signalr-service-class"></a>La classe de service de SignalR

Le `SignalRService` classe dans le **ChatClient** projet dans l’exemple d’application illustre une implémentation qui appelle des fonctions dans une application de fonctions Azure pour se connecter à un Service Azure SignalR.

Le `SendMessageAsync` méthode dans la `SignalRService` classe est utilisée pour envoyer des messages aux clients connectés au Service Azure SignalR. Cette méthode effectue une demande HTTP POST vers le **parler** fonction hébergée dans l’application de fonctions Azure, y compris un JSON sérialisé `Message` objet en tant que la charge utile POST. Le **parler** fonction transmet le message au Service Azure SignalR pour les clients de diffusion à tous les éléments. Le code suivant montre la méthode `SendMessageAsync` :

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

Le `ConnectAsync` méthode dans le `SignalRService` classe effectue une requête HTTP GET à le **Negotiate** fonction hébergée dans l’application de fonctions Azure. Le **Negotiate** fonction retourne JSON est désérialisé dans une instance de la `NegotiateInfo` classe. Une fois le `NegotiateInfo` objet est récupéré, il est utilisé pour inscrire directement avec le Service Azure SignalR à l’aide d’une instance de la `HubConnection` classe.

ASP.NET Core SignalR convertit les données entrantes à partir de la connexion ouverte en messages et permet aux développeurs de définir les types de messages et lier des gestionnaires d’événements pour les messages entrants par type. Le `ConnectAsync` méthode inscrit un gestionnaire d’événements pour le nom de message défini dans l’exemple d’application **Constants.cs** fichier, qui est « newMessage » par défaut.

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

Le `AddNewMessage` méthode est liée en tant que le Gestionnaire d’événements dans le `ConnectAsync` message comme indiqué dans le code précédent. Lorsqu’un message est reçu, le `AddNewMessage` méthode est appelée avec les données du message fournies comme un `JObject`. Le `AddNewMessage` méthode convertit le `JObject` à une instance de la `Message` classe, puis appelle le Gestionnaire de `NewMessageReceived` si un a été lié. Le code suivant montre la méthode `AddNewMessage` :

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Utilisez la classe de service SignalR dans Xamarin.Forms

Utilisation de la classe de service SignalR dans Xamarin.Forms s’effectue en liant la `SignalRService` classe d’événements dans le `MainPage` classe code-behind.

Le `Connected` événement dans le `SignalRService` classe est déclenchée lorsqu’une connexion SignalR est effectuée avec succès. Le `ConnectionFailed` événement dans le `SignalRService` classe est déclenchée en cas d’échec d’une connexion SignalR. Le `SignalR_ConnectionChanged` méthode de gestionnaire d’événements est lié pour les événements dans le `MainPage` constructeur. Ce gestionnaire d’événements met à jour les États de bouton se connecter et d’envoi est basées sur la connexion `success` argument et ajoute le message fourni par l’événement à la file d’attente de conversation à l’aide de la `AddMessage` (méthode). Le code suivant illustre la `SignalR_ConnectionChanged` méthode de gestionnaire d’événements :

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

Le `NewMessageReceived` événement dans le `SignalRService` classe est déclenchée lorsqu’un nouveau message est reçu du Service Azure SignalR. Le `SignalR_NewMessageReceived` méthode de gestionnaire d’événements est lié à la `NewMessageReceived` événement dans le `MainPage` constructeur. Ce gestionnaire d’événements convertit entrant `Message` de l’objet en une chaîne et l’ajoute à la file d’attente de conversation à l’aide de la `AddMessage` (méthode). Le code suivant illustre la `SignalR_NewMessageReceived` méthode de gestionnaire d’événements :

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

Le `AddMessage` méthode ajoute un nouveau message, comme un `Label` objet à la file d’attente de la conversation. Le `AddMessage` méthode est souvent appelée par des gestionnaires d’événements en dehors du thread d’interface utilisateur principal, afin qu’il force les mises à jour de l’interface utilisateur de se produire sur le thread principal pour éviter les exceptions. Le code suivant montre la méthode `AddMessage` :

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

## <a name="test-the-application"></a>Tester l’application

L’application de conversation SignalR peut être testée sur iOS, Android et UWP autant que vous avez :

1. Créé un Service Azure SignalR.
1. Créé une application Azure Functions.
1. Personnaliser le **Constants.cs** fichier avec le point de terminaison d’application Azure Functions.

Une fois ces étapes sont terminées et l’application est exécutée, en cliquant sur le **Connect** forms bouton une connexion avec le Service Azure SignalR. Tapez un message et cliquez sur le **envoyer** les résultats de bouton dans les messages qui apparaissent dans la file d’attente de conversation sur n’importe quel connecté des applications mobiles.

## <a name="related-links"></a>Liens connexes

* [Élaboration d’applications mobiles en temps réel avec Xamarin et SignalR](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introduction à SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Introduction à Azure Functions](/azure/azure-functions/functions-overview)
* [Documentation Azure Functions](/azure/azure-functions/)
* [Exemple de conversation MVVM SignalR](https://github.com/lbugnion/sample-xamarin-signalr)
