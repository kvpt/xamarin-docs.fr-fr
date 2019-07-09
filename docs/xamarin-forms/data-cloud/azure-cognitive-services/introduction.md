---
title: Présentation de Cognitive Services Xamarin.Forms et Azure
description: Cet article présente un exemple d’application qui montre comment appeler des API Microsoft Cognitive services.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 36aa53a6d257d8f5311cab84485e608bef3e97f8
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659266"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Présentation de Cognitive Services Xamarin.Forms et Azure

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_Microsoft Cognitive Services sont un ensemble d’API, les kits de développement logiciel et les services qui permettent aux développeurs de créer des applications plus intelligentes en ajoutant des fonctionnalités telles que la reconnaissance faciale, reconnaissance vocale et compréhension du langage. Cet article fournit une introduction à l’exemple d’application qui montre comment appeler des API Microsoft Cognitive services._

## <a name="overview"></a>Vue d'ensemble

L’exemple qui accompagne cet article est une application de liste todo qui fournit une fonctionnalité :

- Afficher la liste des tâches.
- Ajouter et modifier des tâches via le clavier logiciel, ou en effectuant la reconnaissance vocale avec l’API Microsoft Speech. Pour plus d’informations sur l’exécution de la reconnaissance vocale, consultez [la reconnaissance vocale à l’aide de l’API Microsoft Speech](speech-recognition.md).
- L’orthographe des tâches de vérification à l’aide de l’API vérification orthographique Bing. Pour plus d’informations, consultez [orthographique à l’aide de l’API vérification orthographique Bing](spell-check.md).
- Traduire des tâches de l’anglais vers allemand à l’aide de l’API Translator. Pour plus d’informations, consultez [traduction de texte à l’aide de l’API Translator](text-translation.md).
- Supprimer des tâches.
- Définissez l’état d’une tâche à « terminé ».
- Taux de l’application avec la reconnaissance des émotions, à l’aide de l’API visage. Pour plus d’informations, consultez [la reconnaissance d’émotions à l’aide de l’API visage](emotion-recognition.md).

Les tâches sont stockées dans une base de données SQLite locale. Pour plus d’informations sur l’utilisation d’une base de données SQLite locale, consultez [fonctionne avec une base de données locale](~/xamarin-forms/data-cloud/data/databases.md).

Le `TodoListPage` s’affiche lorsque l’application est lancée. Cette page affiche une liste de toutes les tâches stockées dans la base de données locale et permet à l’utilisateur pour créer une nouvelle tâche ou pour évaluer l’application :

![](introduction-images/sample-application-1.png "TodoListPage")

Nouveaux éléments peuvent être créés en cliquant sur le *+* bouton qui accède à la `TodoItemPage`. Cette page peut également être parcourue à en sélectionnant une tâche :

![](introduction-images/sample-application-2.png "TodoItemPage")

Le `TodoItemPage` permet aux tâches doit être créée, modifiée, une vérification orthographique, traduite, enregistrés et supprimés. La reconnaissance vocale peut être utilisée pour créer ou modifier une tâche. Cela est possible en appuyant sur le bouton du microphone pour commencer l’enregistrement et en appuyant sur le même bouton une deuxième fois pour arrêter l’enregistrement, qui envoie l’enregistrement à l’API de reconnaissance vocale Bing.

En cliquant sur le bouton smilies sur le `TodoListPage` accède à la `RateAppPage`, qui est utilisé pour effectuer la reconnaissance d’émotions sur une image d’une expression faciale :

![](introduction-images/sample-application-3.png "RateAppPage")

Le `RateAppPage` permet à l’utilisateur à prendre une photo de leur visage, qui est envoyé à l’API visage avec l’émotion retournée affichée.

## <a name="understand-the-application-anatomy"></a>Découvrez l’anatomie d’application

Le projet de code partagé pour l’exemple d’application se compose de cinq dossiers principaux :

|Dossier|Objectif|
|--- |--- |
|Modèles|Contient les classes de modèle de données pour l’application. Cela inclut le `TodoItem` (classe), qui modélise un seul élément de données utilisées par l’application. Le dossier comprend également des classes utilisées pour modéliser les réponses JSON retournés à partir de différentes API Microsoft Cognitive services.|
|Référentiels|Contient le `ITodoItemRepository` interface et `TodoItemRepository` classe qui sont utilisés pour effectuer des opérations de base de données.|
|Services|Contient les interfaces et les classes qui sont utilisées pour accéder aux différentes API Microsoft Cognitive services, ainsi que des interfaces qui sont utilisées par la `DependencyService` classe pour localiser les classes qui implémentent les interfaces dans les projets de plateforme.|
|Utils|Contient le `Timer` (classe), qui est utilisé par le `AuthenticationService` classe pour renouveler un jeton d’accès JWT 9 minutes.|
|Affichages|Contient les pages de l’application.|

Le projet de code partagé contient également des fichiers importants :

|Fichier|Objectif|
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

### <a name="platform-project-implementations"></a>Implémentations de projet de plateforme

Le `Services` dossier dans le projet de code partagé contient la `IFileHelper` et `IAudioRecorderService` interfaces qui sont utilisées par la `DependencyService` classe pour localiser les classes qui implémentent les interfaces dans les projets de plateforme.

Le `IFileHelper` interface est implémentée par le `FileHelper` classe dans chaque projet de plateforme. Cette classe se compose d’une méthode unique, `GetLocalFilePath`, qui retourne un chemin d’accès de fichier local pour stocker la base de données SQLite.

Le `IAudioRecorderService` interface est implémentée par le `AudioRecorderService` classe dans chaque projet de plateforme. Cette classe se compose de `StartRecording`, `StopRecording`, ainsi que les méthodes, qui utilisent des API de la plate-forme à enregistrer l’audio à partir du microphone de l’appareil et stockez-le dans un fichier wav. Sur iOS, le `AudioRecorderService` utilise le `AVFoundation` API pour enregistrer l’audio. Sur Android, le `AudioRecordService` utilise le `AudioRecord` API pour enregistrer l’audio. Sur la plateforme de Windows universelle (UWP), le `AudioRecorderService` utilise le `AudioGraph` API pour enregistrer l’audio.

### <a name="invoke-cognitive-services"></a>Appeler cognitives services

L’exemple d’application appelle les Services cognitifs Microsoft suivants :

- Microsoft Speech API. Pour plus d’informations, consultez [la reconnaissance vocale à l’aide de l’API Microsoft Speech](speech-recognition.md).
- API vérification orthographique Bing. Pour plus d’informations, consultez [orthographique à l’aide de l’API vérification orthographique Bing](spell-check.md).
- API de traduction. Pour plus d’informations, consultez [traduction de texte à l’aide de l’API Translator](text-translation.md).
- API visage. Pour plus d’informations, consultez [la reconnaissance d’émotions à l’aide de l’API visage](emotion-recognition.md).

## <a name="related-links"></a>Liens connexes

- [Documentation de Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/documentation)
- [TODO Cognitive Services (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
