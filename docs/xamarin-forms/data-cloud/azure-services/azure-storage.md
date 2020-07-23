---
title: Stocker des données et y accéder dans stockage Azure à partir deXamarin.Forms
description: Le stockage Azure est une solution de stockage cloud évolutive qui peut être utilisée pour stocker des données structurées et non structurées. Cet article explique comment utiliser Xamarin.Forms pour stocker du texte et des données binaires dans le stockage Azure et comment accéder aux données.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f67543a6c678e2c3a1395f816e020d69af4bf873
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936654"
---
# <a name="store-and-access-data-in-azure-storage-from-xamarinforms"></a>Stocker des données et y accéder dans stockage Azure à partir deXamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_Le stockage Azure est une solution de stockage cloud évolutive qui peut être utilisée pour stocker des données structurées et non structurées. Cet article explique comment utiliser Xamarin.Forms pour stocker du texte et des données binaires dans le stockage Azure, et comment accéder aux données._

Le stockage Azure fournit quatre services de stockage :

- Stockage d’objets BLOB. Un objet BLOB peut être du texte ou des données binaires, telles que des sauvegardes, des machines virtuelles, des fichiers multimédias ou des documents.
- Le stockage table est un magasin de clés NoSQL.
- Le stockage file d’attente est un service de messagerie pour le traitement des flux de travail et la communication entre les services Cloud.
- Le stockage de fichiers fournit un stockage partagé à l’aide du protocole SMB.

Il existe deux types de comptes de stockage :

- Les comptes de stockage à usage général permettent d’accéder aux services de stockage Azure à partir d’un seul compte.
- Un compte de stockage d’objets BLOB est un compte de stockage spécialisé pour le stockage des objets BLOB. Ce type de compte est recommandé lorsque vous devez uniquement stocker des données BLOB.

Cet article, ainsi que l’exemple d’application qui l’accompagne, illustre le téléchargement de fichiers image et texte dans le stockage d’objets BLOB, et leur téléchargement. En outre, il montre également comment récupérer une liste de fichiers à partir du stockage d’objets BLOB et supprimer des fichiers.

Pour plus d’informations sur le stockage Azure, consultez [Présentation du stockage](https://azure.microsoft.com/documentation/articles/storage-introduction/).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

## <a name="introduction-to-blob-storage"></a>Présentation du stockage d’objets BLOB

Le stockage d’objets BLOB est constitué de trois composants, qui sont présentés dans le diagramme suivant :

![Concepts du stockage d’objets BLOB](azure-storage-images/blob-storage.png)

Tout accès au stockage Azure s’effectue via un compte de stockage. Un compte de stockage peut contenir un nombre illimité de conteneurs, et un conteneur peut stocker un nombre illimité d’objets BLOB, jusqu’à la limite de capacité du compte de stockage.

Un objet blob est un fichier de tout type et de toute taille. Le stockage Azure prend en charge trois types d’objets BLOB différents :

- Les objets BLOB de blocs sont optimisés pour la diffusion et le stockage d’objets Cloud, et sont un bon choix pour stocker des sauvegardes, des fichiers multimédias, des documents, etc. La taille des objets BLOB de blocs peut atteindre 195 Go.
- Les objets BLOB d’ajout sont similaires aux objets BLOB de blocs, mais ils sont optimisés pour les opérations d’ajout, telles que la journalisation. La taille des objets BLOB d’ajout peut atteindre 195 Go.
- Les objets BLOB de pages sont optimisés pour les opérations de lecture/écriture fréquentes et sont généralement utilisés pour stocker des ordinateurs virtuels et leurs disques. La taille des objets BLOB de pages peut atteindre 1 to.

> [!NOTE]
> Notez que les comptes de stockage d’objets BLOB prennent en charge les objets BLOB de blocs et d’ajout, mais pas les objets BLOB de pages.

Un objet blob est chargé dans le stockage Azure et téléchargé à partir du stockage Azure, sous la forme d’un flux d’octets. Par conséquent, les fichiers doivent être convertis en un flux d’octets avant le chargement, puis reconvertis en leur représentation d’origine après le téléchargement.

Chaque objet stocké dans le stockage Azure a une adresse URL unique. Le nom du compte de stockage constitue le sous-domaine de cette adresse et la combinaison du sous-domaine et du nom de domaine forme un *point de terminaison* pour le compte de stockage. Par exemple, si votre compte de stockage est nommé *mystorageaccount*, le point de terminaison d’objet BLOB par défaut pour le compte de stockage est `https://mystorageaccount.blob.core.windows.net` .

L’URL permettant d’accéder à un objet dans un compte de stockage est constituée de l’ajout de l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d’objet BLOB sera au format `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob` .

## <a name="setup"></a>Installation

Le processus d’intégration d’un compte de stockage Azure dans une Xamarin.Forms application est le suivant :

1. Créez un compte de stockage. Pour plus d’informations, consultez la rubrique [Création d’un compte de stockage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account) .
1. Ajoutez la [bibliothèque cliente de stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) à l' Xamarin.Forms application.
1. Configurez la chaîne de connexion de stockage. Pour plus d’informations, consultez [connexion à Azure Storage](#connecting-to-azure-storage).
1. Ajoutez `using` des directives pour `Microsoft.WindowsAzure.Storage` les `Microsoft.WindowsAzure.Storage.Blob` espaces de noms et aux classes qui accéderont au stockage Azure.

## <a name="connecting-to-azure-storage"></a>Connexion à Stockage Azure

Chaque demande effectuée sur des ressources de compte de stockage doit être authentifiée. Alors que les objets BLOB peuvent être configurés pour prendre en charge l’authentification anonyme, il existe deux approches principales qu’une application peut utiliser pour s’authentifier avec un compte de stockage :

- Clé partagée. Cette approche utilise le nom du compte de stockage Azure et la clé de compte pour accéder aux services de stockage. Deux clés privées sont affectées à un compte de stockage lors de la création et peuvent être utilisées pour l’authentification par clé partagée.
- Signature d’accès partagé. Il s’agit d’un jeton qui peut être ajouté à une URL qui permet l’accès délégué à une ressource de stockage, avec les autorisations qu’il spécifie, pour la durée pendant laquelle il est valide.

Vous pouvez spécifier des chaînes de connexion qui incluent les informations d’authentification requises pour accéder aux ressources de stockage Azure à partir d’une application. En outre, une chaîne de connexion peut être configurée pour se connecter à l’émulateur de stockage Azure à partir de Visual Studio.

> [!NOTE]
> Le stockage Azure prend en charge les protocoles HTTP et HTTPs dans une chaîne de connexion. Toutefois, il est recommandé d’utiliser le protocole HTTPs.

### <a name="connecting-to-the-azure-storage-emulator"></a>Connexion à l’émulateur de stockage Azure

L’émulateur de stockage Azure fournit un environnement local qui émule les services d’objets BLOB, de file d’attente et de table Azure à des fins de développement.

La chaîne de connexion suivante doit être utilisée pour se connecter à l’émulateur de stockage Azure :

```csharp
UseDevelopmentStorage=true
```

Pour plus d’informations sur l’émulateur de stockage Azure, consultez [utiliser l’émulateur de stockage Azure pour le développement et le test](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Connexion au stockage Azure à l’aide d’une clé partagée

Le format de chaîne de connexion suivant doit être utilisé pour se connecter au stockage Azure avec une clé partagée :

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName`doit être remplacé par le nom de votre compte de stockage et `myAccountKey` doit être remplacé par l’une des deux clés d’accès de votre compte.

> [!NOTE]
> Lors de l’utilisation de l’authentification par clé partagée, le nom et la clé de votre compte seront distribués à chaque personne qui utilise votre application, qui fournira un accès complet en lecture/écriture au compte de stockage. Par conséquent, utilisez l’authentification par clé partagée à des fins de test uniquement et ne distribuez jamais de clés à d’autres utilisateurs.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Connexion au stockage Azure à l’aide d’une signature d’accès partagé

Le format de chaîne de connexion suivant doit être utilisé pour se connecter au stockage Azure avec une signature d’accès partagé :

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint`doit être remplacé par l’URL de votre point de terminaison d’objet BLOB et `mySharedAccessSignature` doit être remplacé par votre SAP. La signature d’accès partagé fournit le protocole, le point de terminaison de service et les informations d’identification pour accéder à la ressource.

> [!NOTE]
> L’authentification SAS est recommandée pour les applications de production. Toutefois, dans une application de production, la signature d’accès partagé doit être récupérée à partir d’un service principal à la demande, au lieu d’être regroupée avec l’application.

Pour plus d’informations sur les signatures d’accès partagé, consultez [utilisation des signatures d’accès partagé (SAP)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Création d’un conteneur

La `GetContainer` méthode est utilisée pour récupérer une référence à un conteneur nommé, qui peut ensuite être utilisée pour récupérer des objets BLOB du conteneur ou pour ajouter des objets BLOB au conteneur. L’exemple de code suivant montre la méthode `GetContainer` :

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

La `CloudStorageAccount.Parse` méthode analyse une chaîne de connexion et retourne une `CloudStorageAccount` instance qui représente le compte de stockage. Une `CloudBlobClient` instance, qui est utilisée pour récupérer des conteneurs et des objets BLOB, est ensuite créée par la `CreateCloudBlobClient` méthode. La `GetContainerReference` méthode récupère le conteneur spécifié en tant qu' `CloudBlobContainer` instance, avant qu’il ne soit retourné à la méthode d’appel. Dans cet exemple, le nom du conteneur est la `ContainerType` valeur d’énumération, convertie en une chaîne minuscule.

> [!NOTE]
> Les noms de conteneur doivent être en minuscules et doivent commencer par une lettre ou un chiffre. En outre, ils peuvent contenir uniquement des lettres, des chiffres et le tiret, et doivent comprendre entre 3 et 63 caractères.

La `GetContainer` méthode est appelée comme suit :

```csharp
var container = GetContainer(containerType);
```

L' `CloudBlobContainer` instance peut ensuite être utilisée pour créer un conteneur s’il n’existe pas déjà :

```csharp
await container.CreateIfNotExistsAsync();
```

Par défaut, un conteneur nouvellement créé est privé. Cela signifie qu’une clé d’accès de stockage doit être spécifiée pour récupérer des objets BLOB du conteneur. Pour plus d’informations sur la création d’objets BLOB dans un conteneur, consultez [créer un conteneur](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Chargement de données dans un conteneur

La `UploadFileAsync` méthode est utilisée pour charger un flux de données d’octets dans le stockage d’objets BLOB, et est illustrée dans l’exemple de code suivant :

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

Après avoir récupéré une référence de conteneur, la méthode crée le conteneur s’il n’existe pas déjà. Un nouveau `Guid` est ensuite créé pour jouer le rôle d’un nom d’objet BLOB unique, et une référence de bloc BLOB est récupérée en tant qu' `CloudBlockBlob` instance. Le flux de données est ensuite téléchargé vers l’objet blob à l’aide de la `UploadFromStreamAsync` méthode, qui crée l’objet BLOB s’il n’existe pas déjà, ou le remplace s’il existe.

Avant qu’un fichier puisse être téléchargé vers le stockage d’objets BLOB à l’aide de cette méthode, il doit d’abord être converti en un flux d’octets. Cela est illustré dans l’exemple de code suivant :

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

Les `text` données sont converties en un tableau d’octets, qui est ensuite encapsulé en tant que flux passé à la `UploadFileAsync` méthode.

## <a name="downloading-data-from-a-container"></a>Téléchargement de données à partir d’un conteneur

La `GetFileAsync` méthode est utilisée pour télécharger des données BLOB à partir du stockage Azure et est illustrée dans l’exemple de code suivant :

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

Après avoir récupéré une référence de conteneur, la méthode récupère une référence d’objet BLOB pour les données stockées. Si l’objet BLOB existe, ses propriétés sont récupérées par la `FetchAttributesAsync` méthode. Un tableau d’octets de la taille correcte est créé, et l’objet blob est téléchargé sous la forme d’un tableau d’octets qui est retourné à la méthode appelante.

Une fois les données d’octets BLOB téléchargées, elles doivent être converties dans leur représentation d’origine. Cela est illustré dans l’exemple de code suivant :

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

Le tableau d’octets est récupéré à partir du stockage Azure par la `GetFileAsync` méthode, avant d’être converti en chaîne encodée en UTF8.

## <a name="listing-data-in-a-container"></a>Affichage des données dans un conteneur

La `GetFilesListAsync` méthode est utilisée pour récupérer une liste d’objets BLOB stockés dans un conteneur, et est illustrée dans l’exemple de code suivant :

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

Après avoir récupéré une référence de conteneur, la méthode utilise la méthode du conteneur `ListBlobsSegmentedAsync` pour récupérer les références aux objets BLOB dans le conteneur. Les résultats retournés par la `ListBlobsSegmentedAsync` méthode sont énumérés alors que l’instance ne l' `BlobContinuationToken` est pas `null` . Chaque objet blob est converti à partir du retourné `IListBlobItem` en un `CloudBlockBlob` dans l’ordre d’accès à la `Name` propriété de l’objet BLOB, avant sa valeur ajoutée à la `allBlobsList` collection. Une fois l' `BlobContinuationToken` instance de `null` , le dernier nom d’objet BLOB a été retourné, et l’exécution quitte la boucle.

## <a name="deleting-data-from-a-container"></a>Suppression de données d’un conteneur

La `DeleteFileAsync` méthode est utilisée pour supprimer un objet blob d’un conteneur et est illustrée dans l’exemple de code suivant :

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Après avoir récupéré une référence de conteneur, la méthode récupère une référence d’objet BLOB pour l’objet BLOB spécifié. L’objet blob est ensuite supprimé avec la `DeleteIfExistsAsync` méthode.

## <a name="related-links"></a>Liens associés

- [Stockage Azure (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [Présentation du stockage](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Utilisation du stockage d’objets blob à partir de Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Utilisation des signatures d’accès partagé (SAP)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Stockage Windows Azure (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
