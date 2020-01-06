---
title: Stocker des données et y accéder dans stockage Azure à partir de Xamarin. Forms
description: Stockage Azure est une solution de stockage cloud évolutif qui peut être utilisée pour stocker des données non structurées et structurées. Cet article explique comment utiliser Xamarin. Forms pour stocker des données texte et binaires dans le stockage Azure et comment accéder aux données.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
ms.openlocfilehash: 8d773abbca348d09d3359f09cbded22f6521fb7f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487319"
---
# <a name="store-and-access-data-in-azure-storage-from-xamarinforms"></a>Stocker des données et y accéder dans stockage Azure à partir de Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_Le stockage Azure est une solution de stockage cloud évolutive qui peut être utilisée pour stocker des données structurées et non structurées. Cet article explique comment utiliser Xamarin. Forms pour stocker du texte et des données binaires dans le stockage Azure, et comment accéder aux données._

Stockage Azure fournit quatre services de stockage :

- Stockage d’objets BLOB. Un objet blob peut être des données texte ou binaires, tels que les sauvegardes, les machines virtuelles, les fichiers multimédias ou les documents.
- Stockage de table est un magasin de clés-attributs NoSQL.
- Stockage file d’attente est un service de messagerie pour le traitement de flux de travail et de la communication entre les services cloud.
- Stockage de fichiers fournit un stockage partagé en utilisant le protocole SMB.

Il existe deux types de comptes de stockage :

- Un compte de stockage à usage général fournit l’accès aux services de stockage Azure à partir d’un seul compte.
- Un compte de stockage d’objets Blob est un compte de stockage spécialisé pour le stockage BLOB. Ce type de compte est recommandé lorsque vous avez besoin uniquement stocker des données blob.

Cet article et exemple d’application, d’accompagnement illustre le téléchargement de fichiers image et le texte pour le stockage d’objets blob et les télécharger. En outre, il montre également récupérer une liste de fichiers à partir du stockage d’objets blob et la suppression des fichiers.

Pour plus d’informations sur le stockage Azure, consultez [Introduction au stockage](https://azure.microsoft.com/documentation/articles/storage-introduction/).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

## <a name="introduction-to-blob-storage"></a>Introduction au stockage d’objets Blob

Stockage d’objets BLOB se compose de trois composants, qui sont présentées dans le diagramme suivant :

![](azure-storage-images/blob-storage.png "Blob Storage Concepts")

Tous les accès vers le stockage Azure se fait via un compte de stockage. Un compte de stockage peut contenir un nombre illimité de conteneurs, et un conteneur peut stocker un nombre illimité d’objets BLOB, jusqu'à la limite de capacité du compte de stockage.

Un objet blob est un fichier de n’importe quel type et la taille. Stockage Azure prend en charge trois types d’objets blob différents :

- Les objets BLOB de blocs sont optimisés pour la diffusion et le stockage d’objets Cloud, et sont un bon choix pour stocker des sauvegardes, des fichiers multimédias, des documents, etc. La taille des objets BLOB de blocs peut atteindre 195 Go.
- Ajouter des objets BLOB sont similaires aux objets BLOB de blocs, mais sont optimisés pour les opérations d’ajout, telles que la journalisation. Ajouter des objets BLOB peuvent être de taille maximale de 195 go.
- Objets BLOB de pages est optimisés pour les opérations de lecture/écriture fréquentes et est généralement utilisés pour stocker des machines virtuelles et leurs disques. Objets BLOB de pages peut être jusqu'à 1 To.

> [!NOTE]
> Notez que les comptes de stockage d’objets blob prennent en charge de bloc et ajouter des objets BLOB, mais pas objets BLOB de pages.

Un objet blob est téléchargé vers le stockage Azure et téléchargé depuis le stockage Azure, en tant que flux d’octets. Par conséquent, les fichiers doivent être convertis en un flux d’octets avant le téléchargement, puis est reconverti converti en leur représentation sous forme d’origine après le téléchargement.

Chaque objet qui est stocké dans le stockage Azure a une adresse URL unique. Le nom de compte de stockage constitue le sous-domaine de cette adresse et la combinaison des formes de nom de sous-domaine et de domaine un *point de terminaison* pour le compte de stockage. Par exemple, si votre compte de stockage est nommé *mystorageaccount*, le point de terminaison du blob par défaut pour le compte de stockage est `https://mystorageaccount.blob.core.windows.net`.

L’URL pour accéder à un objet dans un compte de stockage est créé en ajoutant l’emplacement de l’objet dans le compte de stockage pour le point de terminaison. Par exemple, une adresse de l’objet blob a le format `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Programme d'installation

Le processus d’intégration d’un compte de stockage Azure dans une application Xamarin.Forms est comme suit :

1. Créer un compte de stockage. Pour plus d’informations, consultez [créer un compte de stockage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Ajouter le [bibliothèque cliente Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) à l’application Xamarin.Forms.
1. Configurer la chaîne de connexion de stockage. Pour plus d’informations, consultez [connexion au stockage Azure](#connecting).
1. Ajouter `using` directives pour la `Microsoft.WindowsAzure.Storage` et `Microsoft.WindowsAzure.Storage.Blob` espaces de noms pour les classes qui accèderont à stockage Azure.

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Connexion à Azure Storage

Chaque demande adressée aux ressources de compte de stockage doit être authentifiée. Bien que les objets BLOB peut être configuré pour prendre en charge l’authentification anonyme, il existe deux approches principales, qu'une application peut utiliser pour s’authentifier avec un compte de stockage :

- Clé partagée. Cette approche utilise le stockage Azure et le nom clé de compte pour accéder aux services de stockage. Un compte de stockage est affecté à deux clés privées lors de la création qui peut être utilisé pour l’authentification par clé partagée.
- Signature d’accès partagé. Il s’agit d’un jeton qui peut être ajouté à une URL qui permet un accès délégué à une ressource de stockage, avec les autorisations qu’il spécifie, pour la durée pendant laquelle il est valide.

Chaînes de connexion peuvent être spécifiées qui incluent les informations d’authentification requises pour accéder aux ressources de stockage Azure à partir d’une application. En outre, une chaîne de connexion peut être configurée pour se connecter à l’émulateur de stockage Azure à partir de Visual Studio.

> [!NOTE]
> Stockage Azure prend en charge HTTP et HTTPS dans une chaîne de connexion. Toutefois, à l’aide de HTTPS est recommandé.

### <a name="connecting-to-the-azure-storage-emulator"></a>Connexion à l’émulateur de stockage Azure

L’émulateur de stockage Azure fournit un environnement local qui émule Azure blob, file d’attente et les services de table à des fins de développement.

La chaîne de connexion doit être utilisée pour se connecter à l’émulateur de stockage Azure :

```csharp
UseDevelopmentStorage=true
```

Pour plus d’informations sur l’émulateur de stockage Azure, consultez [utiliser l’émulateur de stockage Azure pour le développement et test](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Connexion au stockage Azure à l’aide d’une clé partagée

Le format de chaîne de connexion suivant doit être utilisé pour se connecter à stockage Azure avec une clé partagée :

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` doit être remplacé par le nom de votre compte de stockage et `myAccountKey` doit être remplacé par un de vos deux clés d’accès de compte.

> [!NOTE]
> À l’aide quand partagé l’authentification par clé, votre nom de compte et votre clé de compte sera distribué à chaque personne qui utilise votre application, ce qui vous fournira l’accès complet en lecture/écriture au compte de stockage. Par conséquent, l’authentification par clé partagée destiné à des fins de test et jamais distribuer les clés à d’autres utilisateurs.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Connexion au stockage Azure à l’aide d’une Signature d’accès partagé

Le format de chaîne de connexion suivant doit être utilisé pour se connecter à stockage Azure avec une SAP :

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` doit être remplacé par l’URL du point de terminaison blob, et `mySharedAccessSignature` doit être remplacé par votre SAS. La SAP fournit le protocole, le point de terminaison de service et les informations d’identification pour accéder à la ressource.

> [!NOTE]
> L’authentification SAP est recommandée pour les applications de production. Toutefois, dans une application de production, les associations de sécurité doivent être récupérées à partir d’une serveur principal service à la demande, au lieu d’en cours fourni avec l’application.

Pour plus d’informations sur les Signatures d’accès partagé, consultez [à l’aide de l’accès Signatures partagé (SAP)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Création d’un conteneur

Le `GetContainer` méthode est utilisée pour récupérer une référence à un conteneur nommé, ce qui peut ensuite être utilisé pour récupérer des objets BLOB à partir du conteneur ou pour ajouter des objets BLOB au conteneur. L’exemple de code suivant montre la méthode `GetContainer` :

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

Le `CloudStorageAccount.Parse` méthode analyse une chaîne de connexion et retourne un `CloudStorageAccount` instance qui représente le compte de stockage. Un `CloudBlobClient` instance, ce qui permet de récupérer les conteneurs et objets BLOB, est ensuite créé par le `CreateCloudBlobClient` (méthode). Le `GetContainerReference` méthode récupère le conteneur spécifié comme un `CloudBlobContainer` de l’instance, avant d’être retournée à la méthode appelante. Dans cet exemple, le nom du conteneur est le `ContainerType` valeur d’énumération, converti en une chaîne en minuscules.

> [!NOTE]
> Les noms de conteneur doivent être en minuscules et doivent commencer par une lettre ou un chiffre. En outre, ils peuvent contenir uniquement des lettres, des chiffres et des tirets et doivent être comprise entre 3 et 63 caractères.

Le `GetContainer` méthode est appelée comme suit :

```csharp
var container = GetContainer(containerType);
```

Le `CloudBlobContainer` instance peut ensuite être utilisée pour créer un conteneur s’il n’existe pas déjà :

```csharp
await container.CreateIfNotExistsAsync();
```

Par défaut, un conteneur nouvellement créé est privé. Cela signifie qu’une clé d’accès de stockage doit être spécifiée pour récupérer des objets BLOB à partir du conteneur. Pour plus d’informations sur les objets BLOB dans un conteneur de public, consultez [créer un conteneur](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Charger des données dans un conteneur

Le `UploadFileAsync` méthode est utilisée pour télécharger un flux de données d’octets dans le stockage blob et est illustrée dans l’exemple de code suivant :

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

Après avoir récupéré une référence de conteneur, la méthode crée le conteneur s’il n’existe pas déjà. Un nouveau `Guid` est ensuite créé pour agir comme un nom d’objet blob unique, et une référence de bloc d’objet blob est récupérée sous la forme un `CloudBlockBlob` instance. Le flux de données est ensuite chargé dans l’objet blob en utilisant le `UploadFromStreamAsync` (méthode), ce qui crée l’objet blob s’il n’existe pas déjà, ou le remplace s’il n’existe pas.

Avant de pouvoir télécharger un fichier à l’aide de cette méthode de stockage d’objets BLOB, il doit tout d’abord être converti en un flux d’octets. Cela est illustré dans l’exemple de code suivant :

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

Le `text` données sont converties en un tableau d’octets, qui est ensuite encapsulé sous forme de flux qui est passé à la `UploadFileAsync` (méthode).

## <a name="downloading-data-from-a-container"></a>Téléchargement des données à partir d’un conteneur

Le `GetFileAsync` (méthode) est utilisée pour télécharger des données d’objets blob de stockage Azure et est illustrée dans l’exemple de code suivant :

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

Après avoir récupéré une référence de conteneur, la méthode récupère une référence d’objet blob pour les données stockées. Si l’objet blob existe, ses propriétés sont récupérées par le `FetchAttributesAsync` (méthode). Un tableau d’octets de la taille correcte est créé, et l’objet blob est téléchargé sous forme de tableau d’octets qui est renvoyé à la méthode appelante.

Après avoir téléchargé les données blob d’octets, il doit être converti en sa représentation sous forme d’origine. Cela est illustré dans l’exemple de code suivant :

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

Le tableau d’octets est récupéré à partir du stockage Azure par le `GetFileAsync` chaîne encodée de méthode, avant leur conversion vers un UTF-8.

## <a name="listing-data-in-a-container"></a>Affichage des données dans un conteneur

Le `GetFilesListAsync` méthode est utilisée pour récupérer une liste d’objets BLOB stockés dans un conteneur et est illustré dans l’exemple de code suivant :

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

Après avoir récupéré une référence de conteneur, la méthode utilise le conteneur `ListBlobsSegmentedAsync` méthode pour extraire les références aux objets BLOB au sein du conteneur. Les résultats retournés par la `ListBlobsSegmentedAsync` méthode sont énumérées tandis que le `BlobContinuationToken` instance n’est pas `null`. Chaque objet blob est casté retournées `IListBlobItem` à un `CloudBlockBlob` dans access d’ordre le `Name` propriété de l’objet blob, avant qu’il soit la valeur est ajoutée à la `allBlobsList` collection. Une fois le `BlobContinuationToken` instance est `null`, le nom de l’objet blob a été retourné et l’exécution sort de la boucle.

## <a name="deleting-data-from-a-container"></a>Suppression de données dans un conteneur

Le `DeleteFileAsync` méthode est utilisée pour supprimer un objet blob à partir d’un conteneur et est illustré dans l’exemple de code suivant :

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Après avoir récupéré une référence de conteneur, la méthode récupère une référence d’objet blob pour l’objet blob spécifié. L’objet blob est ensuite supprimée avec le `DeleteIfExistsAsync` (méthode).

## <a name="related-links"></a>Liens associés

- [Stockage Azure (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [Présentation du stockage](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Comment utiliser le stockage d’objets Blob à partir de Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [À l’aide de Signatures d’accès partagé (SAP)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Stockage Windows Azure (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
