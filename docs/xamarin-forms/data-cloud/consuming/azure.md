---
title: Utilisation d’une application Mobile Azure
description: Cet article, qui s’applique uniquement aux Azure Mobile Apps qui utilisent un serveur principal Node.js, explique comment interroger, insérer, mettre à jour et supprimer les données stockées dans une table dans une instance d’Azure Mobile Apps.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1ac68992d36627eb5d6aee0d4d19564ce63a3936
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052133"
---
# <a name="consuming-an-azure-mobile-app"></a>Utilisation d’une application Mobile Azure

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)

_Azure Mobile Apps vous permet de vous permet de développer des applications avec les serveurs principaux évolutives hébergées dans Azure App Service, avec prise en charge pour l’authentification d’appareils mobiles, de synchronisation hors connexion et de notifications push. Cet article, qui s’applique uniquement aux Azure Mobile Apps qui utilisent un serveur principal Node.js, explique comment interroger, insérer, mettre à jour et supprimer les données stockées dans une table dans une instance d’Azure Mobile Apps._

> [!NOTE]
> À compter du 30 juin, toutes les nouvelles applications de Mobile Azure sera créées avec TLS 1.2 par défaut. En outre, il est également recommandé d’existants Azure Mobile Apps être reconfigurés pour utiliser TLS 1.2. Pour plus d’informations sur l’application de TLS 1.2 dans une application Mobile Azure, consultez [appliquer TLS 1.2](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-1112). Pour plus d’informations sur la façon de configurer des projets Xamarin pour utiliser TLS 1.2, consultez [sécurité TLS (Transport Layer) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md).

Pour plus d’informations sur la création d’une instance d’Azure Mobile Apps peut être consommée par Xamarin.Forms, consultez [créer une application Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). Après avoir suivi ces instructions, l’exemple téléchargeable d’application peut être configurée pour utiliser l’instance Azure Mobile Apps en définissant le `Constants.ApplicationURL` à l’URL de l’instance d’Azure Mobile Apps. Ensuite, lors de l’exécution de l’exemple d’application qu’il se connectera à l’instance Azure Mobile Apps, comme illustré dans la capture d’écran suivante :

![](azure-images/portal.png "Exemple d’Application")

Accès à Azure Mobile Apps s’effectue via le [kit SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), et toutes les connexions à partir de l’exemple d’application Xamarin.Forms vers Azure sont effectuées via le protocole HTTPS.

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
> ATS peuvent être ignorées si elle n’est pas possible d’utiliser le `HTTPS` de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Utilisation d’une Instance de l’application Mobile Azure

Le [kit SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fournit le `MobileServiceClient` (classe), qui est utilisé par une application Xamarin.Forms pour accéder à l’instance Azure Mobile Apps, comme indiqué dans l’exemple de code suivant :

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Lorsque le `MobileServiceClient` instance est créée, une URL d’application doit être spécifiée pour identifier l’instance d’Azure Mobile Apps. Cette valeur peut être obtenue à partir du tableau de bord pour l’application mobile dans le [portail Microsoft Azure](https://portal.azure.com/).

Une référence à la `TodoItem` table stockée dans l’instance d’Azure Mobile Apps doit être obtenu avant que les opérations peuvent être effectuées sur cette table. Cela s’effectue en appelant le `GetTable` méthode sur le `MobileServiceClient` de l’instance, qui retourne un `IMobileServiceTable<TodoItem>` référence.

### <a name="querying-data"></a>Interrogation des données

Le contenu d’une table peut être récupéré en appelant le `IMobileServiceTable.ToEnumerableAsync` méthode qui évalue la requête de façon asynchrone et retourne les résultats. Données peuvent également être filtrés côté serveur en incluant un `Where` clause dans la requête. Le `Where` clause applique un prédicat à la requête sur la table, de filtrage de ligne comme indiqué dans l’exemple de code suivant :

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

Cette requête retourne tous les éléments de la `TodoItem` table dont `Done` propriété est égale à `false`. Les résultats de requête sont ensuite placés dans un `ObservableCollection` pour l’affichage.

### <a name="inserting-data"></a>Insertion de données

Lors de l’insertion des données dans l’instance d’Azure Mobile Apps, les nouvelles colonnes seront générés automatiquement dans la table en fonction des besoins, fournie à ce schéma dynamique est activé dans l’instance d’Azure Mobile Apps. Le `IMobileServiceTable.InsertAsync` méthode est utilisée pour insérer une nouvelle ligne de données dans la table spécifiée, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

Lorsque vous élaborez une requête d’insertion, un ID ne doit pas spécifié dans les données passées à l’instance d’Azure Mobile Apps. Si la demande d’insertion contient un ID d’un `MobileServiceInvalidOperationException` sera levée.

Après le `InsertAsync` méthode se termine, l’ID des données dans l’instance d’Azure Mobile Apps est rempli dans la `TodoItem` instance dans l’application Xamarin.Forms.

### <a name="updating-data"></a>Mise à jour des données

Lors de la mise à jour des données dans l’instance d’Azure Mobile Apps, les nouvelles colonnes seront générés automatiquement dans la table en fonction des besoins, fournie à ce schéma dynamique est activé dans l’instance d’Azure Mobile Apps. Le `IMobileServiceTable.UpdateAsync` méthode est utilisée pour mettre à jour des données existantes avec de nouvelles informations, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

Lorsque vous élaborez une demande de mise à jour, un ID doit être spécifié afin que l’instance d’Azure Mobile Apps puisse identifier les données à mettre à jour. Cette valeur d’ID est stockée dans le `TodoItem.ID` propriété. Si la demande de mise à jour ne contient pas un ID il n’existe aucun moyen pour l’instance d’Azure Mobile Apps déterminer les données à mettre à jour et donc un `MobileServiceInvalidOperationException` sera levée.

### <a name="deleting-data"></a>Suppression de données

Le `IMobileServiceTable.DeleteAsync` méthode est utilisée pour supprimer des données à partir d’une table d’Azure Mobile Apps, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

Lorsque vous élaborez une demande de suppression, un ID doit être spécifié afin que l’application Mobile Azure sinstance puisse identifier les données à supprimer. Cette valeur d’ID est stockée dans le `TodoItem.ID` propriété. Si la demande de suppression ne contient pas un ID, il n’existe aucun moyen pour l’instance d’Azure Mobile Apps déterminer les données à supprimer et donc un `MobileServiceInvalidOperationException` sera levée.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment utiliser le [kit SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) à interroger, insérer, mettre à jour et supprimer les données stockées dans une table dans une instance d’applications Azure Mobile. Le SDK fournit la `MobileServiceClient` classe qui est utilisé par une application Xamarin.Forms pour accéder à l’instance d’Azure Mobile Apps.


## <a name="related-links"></a>Liens associés

- [TodoAzure (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Créer une application Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Client Mobile Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
