---
title: Consommer un service web ASP.NET (ASMX)
description: Cet article montre comment utiliser un service SOAP ASMX à partir d’une Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 90f93c2fd17771ccd8bc5dc67404b76cd4ab9c30
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563001"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Consommer un service web ASP.NET (ASMX)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX offre la possibilité de créer des services Web qui envoient des messages à l’aide du protocole SOAP (Simple Object Access Protocol). SOAP est un protocole indépendant de la plateforme et indépendant du langage pour la création et l’accès aux services Web. Les consommateurs d’un service ASMX n’ont pas besoin de savoir quoi que ce soit sur la plateforme, le modèle objet ou le langage de programmation utilisé pour implémenter le service. Ils doivent uniquement comprendre comment envoyer et recevoir des messages SOAP. Cet article montre comment utiliser un service SOAP ASMX à partir d’une Xamarin.Forms application._

Un message SOAP est un document XML contenant les éléments suivants :

- Élément racine nommé *Envelope* qui identifie le document XML en tant que message SOAP.
- Élément d' *en-tête* facultatif qui contient des informations spécifiques à l’application, telles que des données d’authentification. Si l’élément d' *en-tête* est présent, il doit s’agir du premier élément enfant de l’élément *Envelope* .
- Élément de *corps* requis qui contient le message SOAP destiné au destinataire.
- *Élément d’erreur facultatif* utilisé pour indiquer les messages d’erreur. Si l’élément *Fault* est présent, il doit s’agir d’un élément enfant de l’élément *Body* .

SOAP peut fonctionner sur de nombreux protocoles de transport, y compris HTTP, SMTP, TCP et UDP. Toutefois, un service ASMX peut uniquement fonctionner sur HTTP. La plateforme Xamarin prend en charge les implémentations SOAP 1,1 standard sur HTTP, ce qui implique la prise en charge de nombreuses configurations de service ASMX standard.

Cet exemple inclut les applications mobiles qui s’exécutent sur des appareils physiques ou émulés, et un service ASMX qui fournit des méthodes pour récupérer, ajouter, modifier et supprimer des données. Lorsque les applications mobiles sont exécutées, elles se connectent au service ASMX hébergé localement comme indiqué dans la capture d’écran suivante :

![Exemple d'application](asmx-images/portal.png)

> [!NOTE]
> Dans iOS 9 et versions ultérieures, application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Dans la mesure où ATS est activé par défaut dans les applications générées pour iOS 9, toutes les connexions sont soumises aux exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, elles échouent avec une exception.
> L’ATS peut être exclu de s’il n’est pas possible d’utiliser le `HTTPS` protocole et la communication sécurisée pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consommer le services web

Le service ASMX fournit les opérations suivantes :

|Opération|Description|Paramètres|
|--- |--- |--- |
|GetTodoItems|Obtenir une liste de tâches|
|CreateTodoItem|Créer un nouvel élément de tâche|TodoItem sérialisé XML|
|EditTodoItem|Mettre à jour une tâche|TodoItem sérialisé XML|
|DeleteTodoItem|Supprimer une tâche|TodoItem sérialisé XML|

Pour plus d’informations sur le modèle de données utilisé dans l’application, consultez [modélisation des données](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Créer le proxy TodoService

Une classe proxy, appelée `TodoService` , étend `SoapHttpClientProtocol` et fournit des méthodes pour communiquer avec le service asmx sur http. Le proxy est généré en ajoutant une référence Web à chaque projet spécifique à la plateforme dans Visual Studio 2019 ou Visual Studio 2017. La référence Web génère des méthodes et des événements pour chaque action définie dans le document Web Services Description Language (WSDL) du service.

Par exemple, l' `GetTodoItems` action de service entraîne une `GetTodoItemsAsync` méthode et un `GetTodoItemsCompleted` événement dans le proxy. La méthode générée a un type de retour void et appelle l' `GetTodoItems` action sur la classe parente `SoapHttpClientProtocol` . Quand la méthode appelée reçoit une réponse du service, elle déclenche l' `GetTodoItemsCompleted` événement et fournit les données de réponse dans la propriété de l’événement `Result` .

## <a name="create-the-isoapservice-implementation"></a>Créer l’implémentation de ISoapService

Pour permettre au projet multiplateforme partagé de fonctionner avec le service, l’exemple définit l' `ISoapService` interface, qui suit le [modèle de programmation asynchrone de tâche en C#](/dotnet/csharp/programming-guide/concepts/async/). Chaque plateforme implémente le `ISoapService` pour exposer le proxy spécifique à la plateforme. L’exemple utilise des `TaskCompletionSource` objets pour exposer le proxy en tant qu’interface asynchrone de tâche. Pour plus d’informations sur l’utilisation de, `TaskCompletionSource` consultez les implémentations de chaque type d’action dans les sections ci-dessous.

L’exemple `SoapService` :

1. Instancie le `TodoService` en tant qu’instance au niveau de la classe
1. Crée une collection appelée `Items` pour stocker des `TodoItem` objets
1. Spécifie un point de terminaison personnalisé pour la `Url` propriété facultative sur l' `TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>Créer des objets de transfert de données

L’exemple d’application utilise la `TodoItem` classe pour modéliser les données. Pour stocker un `TodoItem` élément dans le service Web, il doit d’abord être converti vers le type généré par le proxy `TodoItem` . Cela est accompli par la `ToASMXServiceTodoItem` méthode, comme illustré dans l’exemple de code suivant :

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
    return new ASMXService.TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

Cette méthode crée une nouvelle `ASMService.TodoItem` instance et affecte à chaque propriété la propriété identique de l' `TodoItem` instance.

De même, lorsque les données sont récupérées à partir du service Web, elles doivent être converties du type généré par le proxy `TodoItem` en une `TodoItem` instance. Cela s’effectue à l’aide de la `FromASMXServiceTodoItem` méthode, comme illustré dans l’exemple de code suivant :

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
{
    return new TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

Cette méthode récupère les données du type généré par le proxy `TodoItem` et les définit dans l’instance nouvellement créée `TodoItem` .

### <a name="retrieve-data"></a>Récupérer des données

L' `ISoapService` interface s’attend à ce que la `RefreshDataAsync` méthode retourne un `Task` avec la collection d’éléments. Toutefois, la `TodoService.GetTodoItemsAsync` méthode retourne void. Pour satisfaire le modèle d’interface, vous devez appeler `GetTodoItemsAsync` , attendre que l' `GetTodoItemsCompleted` événement se déclenche et remplir la collection. Cela vous permet de retourner une collection valide à l’interface utilisateur.

L’exemple ci-dessous crée un nouveau `TaskCompletionSource` , commence l’appel asynchrone dans la `RefreshDataAsync` méthode et attend le `Task` fourni par le `TaskCompletionSource` . Quand le `TodoService_GetTodoItemsCompleted` Gestionnaire d’événements est appelé, il remplit la `Items` collection et met à jour les `TaskCompletionSource` éléments suivants :

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

Pour plus d’informations, consultez [modèle de programmation asynchrone](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) et [bibliothèque parallèle de tâches et programmation asynchrone .NET Framework traditionnelle](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Créer ou modifier des données

Lorsque vous créez ou modifiez des données, vous devez implémenter la `ISoapService.SaveTodoItemAsync` méthode. Cette méthode détecte si `TodoItem` est un élément nouveau ou mis à jour et appelle la méthode appropriée sur l' `todoService` objet. Les `CreateTodoItemCompleted` `EditTodoItemCompleted` gestionnaires d’événements et doivent également être implémentés de sorte que vous savez quand le `todoService` a reçu une réponse du service asmx (ils peuvent être combinés en un seul gestionnaire, car ils effectuent la même opération). L’exemple suivant illustre l’interface et les implémentations du gestionnaire d’événements, ainsi que l' `TaskCompletionSource` objet utilisé pour fonctionner de façon asynchrone :

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>Suppression de données

La suppression de données requiert une implémentation similaire. Définissez un `TaskCompletionSource` , implémentez un gestionnaire d’événements et la `ISoapService.DeleteTodoItemAsync` méthode :

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>Test du service web

Le test des appareils physiques ou émulés avec un service hébergé localement requiert la mise en place d’une configuration IIS personnalisée, des adresses de point de terminaison et des règles de pare-feu. Pour plus d’informations sur la configuration de votre environnement à des fins de test, consultez [configurer l’accès à distance à IIS Express](wcf.md#configure-remote-access-to-iis-express). La seule différence entre le test de WCF et ASMX est le numéro de port du TodoService.

## <a name="related-links"></a>Liens connexes

- [TodoASMX (exemple)](/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](/dotnet/api/system.iasyncresult)