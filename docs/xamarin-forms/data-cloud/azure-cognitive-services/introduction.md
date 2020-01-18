---
title: Présentation de Xamarin. Forms et d’Azure Cognitive Services
description: Cet article présente un exemple d’application qui montre comment appeler des API Microsoft Cognitive services.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 12802abe7b027f4e6d59abd62d2ae0611d71f438
ms.sourcegitcommit: ba83c107c87b015dbcc9db13964fe111a0573dca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76265189"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Présentation de Xamarin. Forms et d’Azure Cognitive Services

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Cognitive Services sont un ensemble d’API, de kits de développement logiciel (SDK) et de services accessibles aux développeurs pour rendre leurs applications plus intelligentes en ajoutant des fonctionnalités telles que la reconnaissance faciale, la reconnaissance vocale et la compréhension de la langue. Cet article fournit une présentation de l’exemple d’application qui montre comment appeler certaines des API Microsoft cognitive service._

## <a name="overview"></a>Vue d'ensemble de

L’exemple qui accompagne cet article est une application de liste todo qui fournit une fonctionnalité :

- Afficher la liste des tâches.
- Ajouter et modifier des tâches via le clavier logiciel, ou en effectuant la reconnaissance vocale avec l’API Microsoft Speech.
- L’orthographe des tâches de vérification à l’aide de l’API vérification orthographique Bing. Pour plus d’informations, consultez [orthographique à l’aide de l’API vérification orthographique Bing](spell-check.md).
- Traduire des tâches de l’anglais vers allemand à l’aide de l’API Translator. Pour plus d’informations, consultez [traduction de texte à l’aide de l’API Translator](text-translation.md).
- Supprimer des tâches.
- Définissez l’état d’une tâche à « terminé ».
- Taux de l’application avec la reconnaissance des émotions, à l’aide de l’API visage. Pour plus d’informations, consultez [la reconnaissance d’émotions à l’aide de l’API visage](emotion-recognition.md).

> [!WARNING]
> La API Reconnaissance vocale Bing a été dépréciée en faveur du service Azure Speech. Pour obtenir un exemple dédié au service de reconnaissance vocale Azure, consultez [reconnaissance vocale avec l’API Speech service](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md).

Les tâches sont stockées dans une base de données SQLite locale. Pour plus d’informations sur l’utilisation d’une base de données SQLite locale, consultez [fonctionne avec une base de données locale](~/xamarin-forms/data-cloud/data/databases.md).

Le `TodoListPage` s’affiche lorsque l’application est lancée. Cette page affiche une liste de toutes les tâches stockées dans la base de données locale et permet à l’utilisateur pour créer une nouvelle tâche ou pour évaluer l’application :

![](introduction-images/sample-application-1.png "TodoListPage")

Nouveaux éléments peuvent être créés en cliquant sur le *+* bouton qui accède à la `TodoItemPage`. Cette page peut également être parcourue à en sélectionnant une tâche :

![](introduction-images/sample-application-2.png "TodoItemPage")

Le `TodoItemPage` permet aux tâches doit être créée, modifiée, une vérification orthographique, traduite, enregistrés et supprimés. La reconnaissance vocale peut être utilisée pour créer ou modifier une tâche. Cela est possible en appuyant sur le bouton du microphone pour commencer l’enregistrement et en appuyant sur le même bouton une deuxième fois pour arrêter l’enregistrement, qui envoie l’enregistrement à l’API de reconnaissance vocale Bing.

En cliquant sur le bouton smilies sur le `TodoListPage` accède à la `RateAppPage`, qui est utilisé pour effectuer la reconnaissance d’émotions sur une image d’une expression faciale :

![](introduction-images/sample-application-3.png "RateAppPage")

Le `RateAppPage` permet à l’utilisateur à prendre une photo de leur visage, qui est envoyé à l’API visage avec l’émotion retournée affichée.

## <a name="understand-the-application-anatomy"></a>Comprendre l’anatomie des applications

Le projet de code partagé pour l’exemple d’application se compose de cinq dossiers principaux :

|Folder|Fonction|
|--- |--- |
|Modèles|Contient les classes de modèle de données pour l’application. Cela inclut le `TodoItem` (classe), qui modélise un seul élément de données utilisées par l’application. Le dossier comprend également des classes utilisées pour modéliser les réponses JSON retournés à partir de différentes API Microsoft Cognitive services.|
|Référentiels|Contient le `ITodoItemRepository` interface et `TodoItemRepository` classe qui sont utilisés pour effectuer des opérations de base de données.|
|Services|Contient les interfaces et les classes qui sont utilisées pour accéder aux différentes API Microsoft Cognitive services, ainsi que des interfaces qui sont utilisées par la `DependencyService` classe pour localiser les classes qui implémentent les interfaces dans les projets de plateforme.|
|Utils|Contient le `Timer` (classe), qui est utilisé par le `AuthenticationService` classe pour renouveler un jeton d’accès JWT 9 minutes.|
|Affichages|Contient les pages de l’application.|

Le projet de code partagé contient également des fichiers importants :

|File|Fonction|
|--- |--- |
|Constants.cs|Le `Constants` (classe), qui spécifie les clés de l’API et les points de terminaison pour l’API Microsoft Cognitive services qui sont appelées. Les constantes de clé API nécessitent la mise à jour pour accéder à l’API Cognitive services différents.|
|App.xaml.cs|Le `App` classe est chargée de l’instanciation à la fois la première page qui sera affichée par l’application sur chaque plateforme, et le `TodoManager` classe qui est utilisée pour appeler des opérations de base de données.|

### <a name="nuget-packages"></a>Packages NuGet

L’exemple d’application utilise les packages NuGet suivants :

- `Newtonsoft.Json` – Fournit une infrastructure JSON pour .NET.
- `PCLStorage` – fournit un ensemble de fichiers local inter-plateformes API d’e/s.
- `sqlite-net-pcl` – fournit le stockage de base de données SQLite.
- `Xam.Plugin.Media` – Fournit la prise de photo inter-plateformes et API d’enlèvement.

En outre, ces packages NuGet installent leurs propres dépendances.

### <a name="model-the-data"></a>Modéliser les données

L’exemple d’application utilise le `TodoItem` classe pour modéliser les données qui sont affichées et stockées dans la base de données SQLite locale. L’exemple de code suivant illustre la classe `TodoItem` :

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

Le `ID` propriété est utilisée pour identifier de manière unique chaque `TodoItem` instance et elle est décorée avec les attributs de SQLite qui rendent la propriété d’une clé primaire auto-incrémentée dans la base de données.

### <a name="invoke-database-operations"></a>Appeler des opérations de base de données

Le `TodoItemRepository` classe implémente les opérations de base de données, et une instance de la classe sont accessibles via le `App.TodoManager` propriété. Le `TodoItemRepository` classe fournit les méthodes suivantes pour appeler des opérations de base de données :

- **GetAllItemsAsync** – récupère tous les éléments de la base de données SQLite locale.
- **GetItemAsync** : récupère un élément spécifié à partir de la base de données SQLite locale.
- **SaveItemAsync** : crée ou met à jour un élément dans la base de données SQLite locale.
- **DeleteItemAsync** : supprime l’élément spécifié à partir de la base de données SQLite locale.

### <a name="platform-project-implementations"></a>Implémentations de projets de plateforme

Le dossier `Services` dans le projet de code partagé contient les interfaces `IFileHelper` et `IAudioRecorderService` utilisées par la classe `DependencyService` pour localiser les classes qui implémentent les interfaces dans les projets de plateforme.

Le `IFileHelper` interface est implémentée par le `FileHelper` classe dans chaque projet de plateforme. Cette classe se compose d’une méthode unique, `GetLocalFilePath`, qui retourne un chemin d’accès de fichier local pour stocker la base de données SQLite.

Le `IAudioRecorderService` interface est implémentée par le `AudioRecorderService` classe dans chaque projet de plateforme. Cette classe se compose de `StartRecording`, `StopRecording`, ainsi que les méthodes, qui utilisent des API de la plate-forme à enregistrer l’audio à partir du microphone de l’appareil et stockez-le dans un fichier wav. Sur iOS, le `AudioRecorderService` utilise le `AVFoundation` API pour enregistrer l’audio. Sur Android, le `AudioRecordService` utilise le `AudioRecord` API pour enregistrer l’audio. Sur la plateforme de Windows universelle (UWP), le `AudioRecorderService` utilise le `AudioGraph` API pour enregistrer l’audio.

### <a name="invoke-cognitive-services"></a>Appeler cognitive services

L’exemple d’application appelle les Services cognitifs Microsoft suivants :

- Microsoft Speech API. Pour plus d’informations, consultez [la reconnaissance vocale à l’aide de l’API Microsoft Speech](speech-recognition.md).
- API vérification orthographique Bing. Pour plus d’informations, consultez [orthographique à l’aide de l’API vérification orthographique Bing](spell-check.md).
- API de traduction. Pour plus d’informations, consultez [traduction de texte à l’aide de l’API Translator](text-translation.md).
- API visage. Pour plus d’informations, consultez [la reconnaissance d’émotions à l’aide de l’API visage](emotion-recognition.md).

## <a name="related-links"></a>Liens connexes

- [Reconnaissance vocale avec l’API Speech service](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Documentation de Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/documentation)
- [TODO Cognitive Services (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
