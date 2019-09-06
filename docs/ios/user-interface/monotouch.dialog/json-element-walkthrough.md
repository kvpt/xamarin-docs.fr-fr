---
title: Utilisation de JSON pour créer une interface utilisateur dans Xamarin. iOS
description: Boîte de dialogue MonoTouch. (MT. D) prend en charge la génération dynamique d’interfaces utilisateur via des données JSON. Dans ce didacticiel, nous allons découvrir comment utiliser un JSONElement pour créer une interface utilisateur à partir de JSON qui est incluse dans une application ou chargée à partir d’une URL distante.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: conceptdev
ms.author: crdun
ms.openlocfilehash: c7deda17a7a4936f000fbfce285b3dc3932795e2
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292280"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Utilisation de JSON pour créer une interface utilisateur dans Xamarin. iOS

_Boîte de dialogue MonoTouch. (MT. D) prend en charge la génération dynamique d’interfaces utilisateur via des données JSON. Dans ce didacticiel, nous allons découvrir comment utiliser un JSONElement pour créer une interface utilisateur à partir de JSON qui est incluse dans une application ou chargée à partir d’une URL distante._

MT. D prend en charge la création d’interfaces utilisateur déclarées en JSON. Lorsque les éléments sont déclarés à l’aide de JSON, MT. D crée automatiquement les éléments associés pour vous. Le code JSON peut être chargé à partir d’un fichier local, d' `JsonObject` une instance analysée ou même d’une URL distante.

MT. D prend en charge la gamme complète des fonctionnalités disponibles dans l’API d’éléments lors de l’utilisation de JSON. Par exemple, l’application de la capture d’écran suivante est entièrement déclarée à l’aide de JSON :

[![](json-element-walkthrough-images/01-load-from-file.png "Par exemple, l’application dans cette capture d’écran est complètement déclarée à l’aide de JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox) [![](json-element-walkthrough-images/01-load-from-file.png ", par exemple, l’application dans cette capture d’écran est complètement déclarée à l’aide de JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Revenons à l’exemple du didacticiel sur les éléments de l' [API procédures](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) , qui montre comment ajouter un écran de détails de tâche à l’aide de JSON.

## <a name="setting-up-mtd"></a>Configuration de MT. E

MT. D est distribué avec Xamarin. iOS. Pour l’utiliser, cliquez avec le bouton droit sur le nœud **références** d’un projet Xamarin. iOS dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à l’assembly **MonoTouch. boîte de dialogue-1** . Ajoutez `using MonoTouch.Dialog` ensuite des instructions dans votre code source, si nécessaire.

## <a name="json-walkthrough"></a>Procédure pas à pas JSON

L’exemple de cette procédure pas à pas permet de créer des tâches. Quand une tâche est sélectionnée sur le premier écran, un écran de détails s’affiche, comme indiqué ci-dessous :

 [![](json-element-walkthrough-images/03-task-list.png "Quand une tâche est sélectionnée sur le premier écran, un écran de détails s’affiche comme indiqué")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Création du JSON

Pour cet exemple, nous chargeons le JSON à partir d’un fichier dans le `task.json`projet nommé. MT. D s’attend à ce que le JSON soit conforme à une syntaxe qui reflète l’API Elements. Tout comme l’utilisation de l’API Elements à partir du code, lors de l’utilisation de JSON, nous déclarons des sections et, dans ces sections, nous ajoutons des éléments. Pour déclarer des sections et des éléments dans JSON, nous utilisons les chaînes « sections » et « Elements » respectivement comme clés. Pour chaque élément, le type d’élément associé est défini à `type` l’aide de la clé. Chaque autre propriété Elements est définie avec le nom de la propriété comme clé.

Par exemple, le code JSON suivant décrit les sections et les éléments pour les détails de la tâche :

```json
{
    "title": "Task",
    "sections": [
        {
            "elements" : [
                {
                    "id" : "task-description",
                    "type": "entry",
                    "placeholder": "Enter task description"
                },
                {
                    "id" : "task-duedate",
                    "type": "date",
                    "caption": "Due Date",
                    "value": "00:00"
                }
            ]
        }
    ]
}
```

Notez que le JSON ci-dessus comprend un ID pour chaque élément. Tout élément peut inclure un ID, pour y faire référence au moment de l’exécution. Nous allons voir comment cela est utilisé dans un moment lorsque nous montrons comment charger le JSON dans le code.

## <a name="loading-the-json-in-code"></a>Chargement du JSON dans le code

Une fois le JSON défini, nous devons le charger dans MT. D à l' `JsonElement` aide de la classe. En supposant qu’un fichier avec le JSON créé ci-dessus a été ajouté au projet avec le nom sample. JSON et qu’une action de génération de contenu `JsonElement` soit générée, le chargement de est aussi simple que l’appel de la ligne de code suivante :

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Étant donné que nous ajoutons cette à la demande à chaque fois qu’une tâche est créée, nous pouvons modifier le bouton cliqué dans l’exemple d’API d’éléments précédents comme suit :

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>Accès aux éléments au moment de l’exécution

Rappelez-vous que nous avons ajouté un ID aux deux éléments lorsque nous les avons déclarés dans le fichier JSON. Nous pouvons utiliser la propriété ID pour accéder à chaque élément au moment de l’exécution afin de modifier ses propriétés dans le code. Par exemple, le code suivant fait référence aux éléments d’entrée et de date pour définir les valeurs de l’objet de tâche :

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    taskElement.Caption = task.Name;

    var description = taskElement ["task-description"] as EntryElement;

    if (description != null) {
        description.Caption = task.Name;
        description.Value = task.Description;       
    }

    var duedate = taskElement ["task-duedate"] as DateElement;

    if (duedate != null) {                
        duedate.DateValue = task.DueDate;
    }
    _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>Chargement de JSON à partir d’une URL

MT. D prend également en charge le chargement dynamique de JSON à partir d’une URL externe en passant simplement l’URL `JsonElement`au constructeur de. MT. D développe la hiérarchie déclarée dans le JSON à la demande à mesure que vous naviguez entre les écrans. Par exemple, imaginez un fichier JSON, tel que celui ci-dessous, situé à la racine du serveur Web local :

```json
{
    "type": "root",
    "title": "home",
    "sections": [
        {
            "header": "Nested view!",
            "elements": [
                {
                    "type": "boolean",
                    "caption": "Just a boolean",
                    "id": "first-boolean",
                    "value": false
                },
                {
                    "type": "string",
                    "caption": "Welcome to the nested controller"
                }
            ]
        }
    ]
}
```

Nous pouvons charger ce code à `JsonElement` l’aide de, comme dans le code suivant :

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

Au moment de l’exécution, le fichier est récupéré et analysé par MT. D lorsque l’utilisateur accède à la deuxième vue, comme illustré dans la capture d’écran ci-dessous :

 [![](json-element-walkthrough-images/04-json-web-example.png "Le fichier sera récupéré et analysé par MT. D lorsque l’utilisateur accède à la deuxième vue")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Récapitulatif

Cet article a montré comment créer une interface using avec MT. D à partir de JSON. Il a montré comment charger JSON inclus dans un fichier avec l’application et à partir d’une URL distante. Elle a également montré comment accéder aux éléments décrits dans JSON au moment de l’exécution.

## <a name="related-links"></a>Liens connexes

- [MTDJsonDemo (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdjsondemo)
- [Présentation de la boîte de dialogue de monopression.](~/ios/user-interface/monotouch.dialog/index.md)
- [Procédure pas à pas d’API d’éléments](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Démonstration de l’API de réflexion](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Boîte de dialogue MonoTouch sur GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Application TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Référence de la classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de la classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
