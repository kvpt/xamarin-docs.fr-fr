---
title: Xamarin.Formset présentation d’Azure Cognitive Services
description: Cet article fournit une introduction à un exemple d’application qui montre comment appeler certaines des API Microsoft cognitive service.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cce5b0fc9c3d1d04c20b1be242197e3bc9e4f901
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929335"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin.Formset présentation d’Azure Cognitive Services

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Cognitive Services sont un ensemble d’API, de kits de développement logiciel (SDK) et de services accessibles aux développeurs pour rendre leurs applications plus intelligentes en ajoutant des fonctionnalités telles que la reconnaissance faciale, la reconnaissance vocale et la compréhension de la langue. Cet article fournit une présentation de l’exemple d’application qui montre comment appeler certaines des API Microsoft cognitive service._

## <a name="overview"></a>Vue d’ensemble

L’exemple qui l’accompagne est une application de liste de tâches qui fournit les fonctionnalités suivantes :

- Affichez une liste de tâches.
- Ajoutez et modifiez des tâches à l’aide du clavier logiciel ou en effectuant une reconnaissance vocale avec l’API Microsoft Speech.
- Vérifier l’orthographe des tâches à l’aide du API Vérification orthographique Bing. Pour plus d’informations, consultez [vérification de l’orthographe à l’aide de l’API vérification orthographique Bing](spell-check.md).
- Traduisez les tâches de l’anglais vers l’allemand à l’aide de l’API translator. Pour plus d’informations, consultez [traduction de texte à l’aide de l’API Translator](text-translation.md).
- Supprimer des tâches.
- Définissez l’état d’une tâche sur « terminé ».
- Évaluez l’application avec la reconnaissance des émotions à l’aide de la API Visage. Pour plus d’informations, consultez [reconnaissance des émotions à l’aide de l’API visage](emotion-recognition.md).

> [!WARNING]
> La API Reconnaissance vocale Bing a été dépréciée en faveur du service Azure Speech. Pour obtenir un exemple dédié au service de reconnaissance vocale Azure, consultez [reconnaissance vocale avec l’API Speech service](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md).

Les tâches sont stockées dans une base de données SQLite locale. Pour plus d’informations sur l’utilisation d’une base de données SQLite locale, consultez [utilisation d’une base de données locale](~/xamarin-forms/data-cloud/data/databases.md).

Le `TodoListPage` s’affiche lorsque l’application est lancée. Cette page affiche une liste de toutes les tâches stockées dans la base de données locale et permet à l’utilisateur de créer une nouvelle tâche ou d’évaluer l’application :

![TodoListPage](introduction-images/sample-application-1.png)

Vous pouvez créer des éléments en cliquant sur le *+* bouton qui navigue vers le `TodoItemPage` . Vous pouvez également accéder à cette page en sélectionnant une tâche :

![TodoItemPage](introduction-images/sample-application-2.png)

Le `TodoItemPage` permet la création, la modification, la vérification orthographique, la traduction, l’enregistrement et la suppression des tâches. La reconnaissance vocale peut être utilisée pour créer ou modifier une tâche. Pour ce faire, appuyez sur le bouton microphone pour démarrer l’enregistrement, puis appuyez une deuxième fois sur le même bouton pour arrêter l’enregistrement, qui envoie l’enregistrement à l’API de reconnaissance Reconnaissance vocale Bing.

Le fait de cliquer sur le bouton smils sur le `TodoListPage` permet de naviguer jusqu’au `RateAppPage` , qui est utilisé pour effectuer la reconnaissance des émotions sur une image d’une expression faciale :

![RateAppPage](introduction-images/sample-application-3.png)

Le `RateAppPage` permet à l’utilisateur de prendre une photo de son visage, qui est soumise au API visage avec l’émotion retournée affichée.

## <a name="understand-the-application-anatomy"></a>Comprendre l’anatomie des applications

Le projet de code partagé pour l’exemple d’application se compose de cinq dossiers principaux :

|Dossier|Objectif|
|--- |--- |
|Modèles|Contient les classes de modèle de données pour l’application. Cela comprend la `TodoItem` classe, qui modélise un seul élément de données utilisé par l’application. Le dossier comprend également les classes utilisées pour modéliser les réponses JSON retournées par différentes API Microsoft cognitive service.|
|Référentiels|Contient l' `ITodoItemRepository` interface et la `TodoItemRepository` classe utilisées pour effectuer des opérations de base de données.|
|Services|Contient les interfaces et les classes utilisées pour accéder à différentes API Microsoft cognitive service, ainsi que les interfaces utilisées par la `DependencyService` classe pour localiser les classes qui implémentent les interfaces dans les projets de plateforme.|
|Utils|Contient la `Timer` classe, qui est utilisée par la `AuthenticationService` classe pour renouveler un jeton d’accès JWT toutes les 9 minutes.|
|Affichages|Contient les pages de l’application.|

Le projet de code partagé contient également des fichiers importants :

|Fichier|Objectif|
|--- |--- |
|Constants.cs|La `Constants` classe, qui spécifie les clés et les points de terminaison API pour les API Microsoft cognitive service qui sont appelées. Les constantes de clé API requièrent une mise à jour pour accéder aux différentes API de service cognitive.|
|App.xaml.cs|La `App` classe est chargée d’instancier la première page qui sera affichée par l’application sur chaque plateforme, et la `TodoManager` classe utilisée pour appeler les opérations de base de données.|

### <a name="nuget-packages"></a>Packages NuGet

L’exemple d’application utilise les packages NuGet suivants :

- `Newtonsoft.Json`: fournit une infrastructure JSON pour .NET.
- `PCLStorage`: fournit un ensemble d’API d’e/s de fichier local multiplateforme.
- `sqlite-net-pcl`: fournit le stockage de base de données SQLite.
- `Xam.Plugin.Media`: fournit des API de prise et de sélection de photos multiplateforme.

En outre, ces packages NuGet installent également leurs propres dépendances.

### <a name="model-the-data"></a>Modéliser les données

L’exemple d’application utilise la `TodoItem` classe pour modéliser les données qui sont affichées et stockées dans la base de données SQLite locale. L’exemple de code suivant illustre la classe `TodoItem` :

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

La `ID` propriété est utilisée pour identifier de manière unique chaque `TodoItem` instance et est décorée avec des attributs SQLite qui font de la propriété une clé primaire auto-incrémentée dans la base de données.

### <a name="invoke-database-operations"></a>Appeler des opérations de base de données

La `TodoItemRepository` classe implémente les opérations de base de données, et une instance de la classe est accessible via la `App.TodoManager` propriété. La `TodoItemRepository` classe fournit les méthodes suivantes pour appeler des opérations de base de données :

- **GetAllItemsAsync** : récupère tous les éléments de la base de données SQLite locale.
- **GetItemAsync** : récupère un élément spécifié dans la base de données SQLite locale.
- **SaveItemAsync** : crée ou met à jour un élément dans la base de données SQLite locale.
- **DeleteItemAsync** : supprime l’élément spécifié de la base de données SQLite locale.

### <a name="platform-project-implementations"></a>Implémentations de projets de plateforme

Le `Services` dossier du projet de code partagé contient les `IFileHelper` `IAudioRecorderService` interfaces et utilisées par la `DependencyService` classe pour localiser les classes qui implémentent les interfaces dans les projets de plateforme.

L' `IFileHelper` interface est implémentée par la `FileHelper` classe dans chaque projet de plateforme. Cette classe se compose d’une méthode unique, `GetLocalFilePath` , qui retourne un chemin de fichier local pour stocker la base de données SQLite.

L' `IAudioRecorderService` interface est implémentée par la `AudioRecorderService` classe dans chaque projet de plateforme. Cette classe se compose `StartRecording` des `StopRecording` méthodes, et de prise en charge, qui utilisent des API de plateforme pour enregistrer l’audio à partir du microphone de l’appareil et le stocker sous forme de fichier WAV. Sur iOS, `AudioRecorderService` utilise l' `AVFoundation` API pour enregistrer l’audio. Sur Android, le `AudioRecordService` utilise l' `AudioRecord` API pour enregistrer l’audio. Sur la plateforme Windows universelle (UWP), le `AudioRecorderService` utilise l' `AudioGraph` API pour enregistrer l’audio.

### <a name="invoke-cognitive-services"></a>Appeler cognitive services

L’exemple d’application appelle la Microsoft Cognitive Services suivante :

- API Microsoft Speech. Pour plus d’informations, consultez [reconnaissance vocale à l’aide de l’API Microsoft Speech](speech-recognition.md).
- API Vérification orthographique Bing. Pour plus d’informations, consultez [vérification de l’orthographe à l’aide de l’API vérification orthographique Bing](spell-check.md).
- Traduction de l’API. Pour plus d’informations, consultez [traduction de texte à l’aide de l’API Translator](text-translation.md).
- API Visage. Pour plus d’informations, consultez [reconnaissance des émotions à l’aide de l’API visage](emotion-recognition.md).

## <a name="related-links"></a>Liens connexes

- [Reconnaissance vocale avec l’API Speech service](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Documentation Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/documentation)
- [Cognitive Services todo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
