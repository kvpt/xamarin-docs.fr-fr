---
title: Utilisation d’un Service Web ASP.NET (ASMX)
description: Cet article illustre l’utilisation d’un service ASMX SOAP à partir d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 73e7d13188c4c2ea1be39a237c3dc5cfd7fd8a7d
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659056"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Utilisation d’un Service Web ASP.NET (ASMX)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX offre la possibilité de créer des services web qui envoient des messages à l’aide de l’accès protocole SOAP (Simple Object). SOAP est un protocole indépendant de la plateforme et indépendant du langage pour la création et l’accès aux services web. Consommateurs d’un service ASMX n’avez pas besoin de savoir quoi que ce soit sur la plateforme, le modèle d’objet ou le langage de programmation utilisé pour implémenter le service. Ils doivent uniquement comprendre comment envoyer et recevoir des messages SOAP. Cet article illustre l’utilisation d’un service ASMX SOAP à partir d’une application Xamarin.Forms._

Un message SOAP est un document XML contenant les éléments suivants :

- Un élément racine nommé *enveloppe* qui identifie le document XML sous forme de message SOAP.
- Facultatif *en-tête* élément qui contient des informations spécifiques aux applications telles que les données de l’authentification. Si le *en-tête* élément est présent il doit être le premier élément enfant de le *enveloppe* élément.
- Obligatoire *corps* élément qui contient le message SOAP destiné au destinataire.
- Facultatif *pannes* élément qui est utilisé pour indiquer les messages d’erreur. Si le *erreur* élément est présent, il doit être un élément enfant de le *corps* élément.

SOAP peut opérer sur plusieurs protocoles de transport, y compris HTTP, SMTP, TCP et UDP. Toutefois, un service ASMX ne peut agir que sur HTTP. La plateforme Xamarin prend en charge les implémentations SOAP 1.1 standard sur HTTP, et cela inclut la prise en charge pour la plupart des configurations de service ASMX standards.

Cet exemple inclut les applications mobiles qui s’exécutent sur des appareils physiques ou émulés et un service ASMX qui fournit des méthodes pour obtenir, ajouter, modifier et supprimer des données. Lorsque les applications mobiles sont exécutées, ils se connectent au service ASMX hébergées localement, comme illustré dans la capture d’écran suivante :

![](asmx-images/portal.png "Exemple d’Application")

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
> ATS peuvent être ignorées si elle n’est pas possible d’utiliser le `HTTPS` de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consommer le service web

Le service ASMX fournit les opérations suivantes :

|Opération|Description|Paramètres|
|--- |--- |--- |
|GetTodoItems|Obtenir une liste de tâches|
|CreateTodoItem|Créer un élément de tâche|Un document XML sérialisé TodoItem|
|EditTodoItem|Mettre à jour une tâche|Un document XML sérialisé TodoItem|
|DeleteTodoItem|Supprimer une tâche|Un document XML sérialisé TodoItem|

Pour plus d’informations sur le modèle de données utilisé dans l’application, consultez [les données de modélisation](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Créer le proxy TodoService

Une classe proxy, appelée `TodoService`, étend `SoapHttpClientProtocol` et fournit des méthodes permettant de communiquer avec le service ASMX via HTTP. Le proxy est généré en ajoutant une référence web à chaque projet spécifique à la plateforme dans Visual Studio 2019 ou Visual Studio 2017. La référence web génère des méthodes et des événements pour chaque action définie dans le document de Web Services Description Language (WSDL) du service.

Par exemple, le `GetTodoItems` service qui entraîne un `GetTodoItemsAsync` (méthode) et un `GetTodoItemsCompleted` événement dans le proxy. La méthode générée possède un type de retour void et appelle le `GetTodoItems` action sur le parent `SoapHttpClientProtocol` classe. Lorsque la méthode appelée reçoit une réponse du service, elle déclenche le `GetTodoItemsCompleted` événement et fournit les données de réponse au sein de l’événement `Result` propriété.

## <a name="create-the-isoapservice-implementation"></a>Créer l’implémentation ISoapService

Pour activer le projet partagé, inter-plateformes fonctionner avec le service, l’exemple définit le `ISoapService` interface, qui suit le [modèle de programmation asynchrone tâche dans C# ](/dotnet/csharp/programming-guide/concepts/async/). Chaque plateforme implémente la `ISoapService` pour exposer le proxy spécifiques à la plateforme. L’exemple utilise `TaskCompletionSource` objets d’exposer le proxy comme une interface asynchrone de la tâche. Pour plus d’informations sur l’utilisation de `TaskCompletionSource` se trouvent dans les implémentations de chaque type d’action dans les sections ci-dessous.

L’exemple `SoapService`:

1. Instancie le `TodoService` comme une instance de niveau classe
1. Crée une collection appelée `Items` pour stocker `TodoItem` objets
1. Spécifie un point de terminaison personnalisé pour le paramètre facultatif `Url` propriété sur le `TodoService`

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

L’exemple d’application utilise le `TodoItem` classe pour modéliser les données. Pour stocker un `TodoItem` élément dans le service web, il doit d’abord être converti vers le proxy généré `TodoItem` type. Pour cela le `ToASMXServiceTodoItem` méthode, comme indiqué dans l’exemple de code suivant :

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

Cette méthode crée un nouveau `ASMService.TodoItem` de l’instance et définit chaque propriété à la propriété identique à partir de la `TodoItem` instance.

De même, lorsque les données sont récupérées à partir du service web, il doit être converti à partir du proxy généré `TodoItem` type à un `TodoItem` instance. Pour cela, avec la `FromASMXServiceTodoItem` méthode, comme indiqué dans l’exemple de code suivant :

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

Cette méthode récupère les données à partir du proxy généré `TodoItem` tapez, puis il définit dans nouvellement créé `TodoItem` instance.

### <a name="retrieve-data"></a>Récupérer des données

Le `ISoapService` interface attend le `RefreshDataAsync` méthode pour retourner un `Task` avec la collection d’éléments. Toutefois, le `TodoService.GetTodoItemsAsync` méthode retourne void. Pour satisfaire le modèle d’interface, vous devez appeler `GetTodoItemsAsync`, attendez que le `GetTodoItemsCompleted` événement à déclencher et remplir la collection. Cela vous permet de retourner une collection valide à l’interface utilisateur.

L’exemple suivant crée un nouveau `TaskCompletionSource`, commence l’appel asynchrone dans le `RefreshDataAsync` (méthode) et attend le `Task` fournie par le `TaskCompletionSource`. Lorsque le `TodoService_GetTodoItemsCompleted` Gestionnaire d’événements est appelé il remplit la `Items` collection et les mises à jour le `TaskCompletionSource`:

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

Pour plus d’informations, consultez [modèle de programmation asynchrone](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) et [TPL et Framework programmation asynchrone .NET traditionnelle](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Créer ou modifier des données

Lorsque vous créez ou modifiez des données, vous devez implémenter le `ISoapService.SaveTodoItemAsync` (méthode). Cette méthode détecte si le `TodoItem` est un élément nouveau ou mis à jour et appelle la méthode appropriée sur le `todoService` objet. Le `CreateTodoItemCompleted` et `EditTodoItemCompleted` gestionnaires d’événements doivent également être implémentés afin de savoir quand le `todoService` a reçu une réponse à partir du service ASMX (elles peuvent être combinées dans un gestionnaire unique, car ils exécutent la même opération). L’exemple suivant montre les implémentations de gestionnaire interface et des événements, ainsi que le `TaskCompletionSource` objet utilisé pour opérer de manière asynchrone :

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

### <a name="delete-data"></a>Supprimer des données

Suppression de données nécessite une implémentation semblable. Définir un `TaskCompletionSource`, implémentez un gestionnaire d’événements et le `ISoapService.DeleteTodoItemAsync` méthode :

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

## <a name="test-the-web-service"></a>Tester le service web

Test des appareils physiques ou émulées avec un service hébergé localement requiert la Configuration IIS personnalisée, les adresses de point de terminaison et les règles de pare-feu en place. Pour plus d’informations sur la façon de configurer votre environnement de test, consultez le [configurer l’accès à distance pour IIS Express](wcf.md#configure-remote-access-to-iis-express). La seule différence entre le test WCF et ASMX est le numéro de port de la TodoService.

## <a name="related-links"></a>Liens connexes

- [TodoASMX (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
