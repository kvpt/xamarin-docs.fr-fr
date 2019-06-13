---
title: L’utilisation de JSON pour créer une Interface utilisateur dans Xamarin.iOS
description: MonoTouch.Dialog (serveur maître cible. (D) inclut la prise en charge pour la génération dynamique de l’interface utilisateur via les données JSON. Dans ce didacticiel, nous allons via l’utilisation d’un JSONElement pour créer une interface utilisateur à partir de JSON qui est inclus dans une application, ou chargé à partir d’une Url distante.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: e1eae07fab4a74a4f47f565d4c4ca0b7f6bc1aa9
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039665"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>L’utilisation de JSON pour créer une interface utilisateur dans Xamarin.iOS

_MonoTouch.Dialog (serveur maître cible. (D) inclut la prise en charge pour la génération dynamique de l’interface utilisateur via les données JSON. Dans ce didacticiel, nous allons via l’utilisation d’un JSONElement pour créer une interface utilisateur à partir de JSON qui est inclus dans une application, ou chargé à partir d’une Url distante._

SERVEUR CIBLE MAÎTRE. D prend en charge la création d’interfaces utilisateur déclaré dans JSON. Lorsque les éléments sont déclarés à l’aide de JSON, serveur cible maître. D crée les éléments associés pour vous automatiquement. Le code JSON peut être chargé à partir d’un fichier local, un texte analysé `JsonObject` instance, ou même une Url distante.

SERVEUR CIBLE MAÎTRE. D prend en charge la gamme complète des fonctionnalités qui sont disponibles dans l’API d’éléments lors de l’utilisation de JSON. Par exemple, l’application dans la capture d’écran suivante est déclarée complètement à l’aide de JSON :

[![](json-element-walkthrough-images/01-load-from-file.png "Par exemple, l’application dans cette capture d’écran est complètement déclarée à l’aide de JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox) [![](json-element-walkthrough-images/01-load-from-file.png ", par exemple, l’application dans cette capture d’écran est complètement déclarée à l’aide de JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Nous allons revisiter l’exemple de la [API de procédure pas à pas les éléments](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) didacticiel, montrant comment ajouter un écran de détails des tâches à l’aide de JSON.

## <a name="setting-up-mtd"></a>Configuration de serveur cible maître. D

SERVEUR CIBLE MAÎTRE. D est distribué avec Xamarin.iOS. Pour l’utiliser, cliquez sur le **références** nœud d’un Xamarin.iOS de projet dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à la **MonoTouch.Dialog-1** assembly. Ensuite, ajoutez `using MonoTouch.Dialog` instructions dans votre source de code en fonction des besoins.

## <a name="json-walkthrough"></a>Procédure pas à pas JSON

L’exemple de cette procédure pas à pas permet la création de tâches. Quand une tâche est sélectionnée dans le premier écran, un écran de détails est présenté comme indiqué :

 [![](json-element-walkthrough-images/03-task-list.png "Quand une tâche est sélectionnée dans le premier écran, un écran de détails est présenté comme indiqué")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Création de l’objet JSON

Pour cet exemple, nous allons charger le fichier JSON à partir d’un fichier dans le projet nommé `task.json`. SERVEUR CIBLE MAÎTRE. D attend le JSON à se conformer à une syntaxe qui reflète l’API d’éléments. Comme à l’aide de l’API d’éléments à partir du code, lorsque vous utilisez JSON, nous déclarons les sections et des sections nous ajoutons des éléments. Pour déclarer des sections et des éléments au format JSON, nous utilisons les chaînes « sections » et les « éléments » respectivement comme les clés. Pour chaque élément, le type d’élément associé est défini à l’aide de la `type` clé. Toutes les autres propriétés éléments sont définie avec le nom de propriété comme clé.

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

Notez que le code JSON ci-dessus inclut un id pour chaque élément. N’importe quel élément peut inclure un id, pour faire référence lors de l’exécution. Nous allons voir comment il est utilisé dans un moment lorsque nous montrons comment charger le fichier JSON dans le code.

## <a name="loading-the-json-in-code"></a>Charger le fichier JSON dans le code

Une fois le fichier JSON a été défini, nous devons charger sur le serveur cible maître. D à l’aide de la `JsonElement` classe. En supposant qu’un fichier avec le code JSON que nous avons créé ci-dessus a été ajouté au projet avec le nom sample.json et étant donné une action de génération de contenu, le chargement du `JsonElement` est aussi simple que l’appel de la ligne de code suivante :

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Étant donné que nous l’ajoutons à la demande chaque fois qu’une tâche est créée, nous pouvons modifier le bouton cliqué à partir de l’exemple d’API Elements précédent comme suit :

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>L’accès aux éléments lors de l’exécution

Rappelez-vous que nous avons ajouté un id pour les deux éléments lorsque nous les déclaré dans le fichier JSON. Nous pouvons utiliser la propriété id pour accéder à chaque élément lors de l’exécution pour modifier leurs propriétés dans le code. Par exemple, le code suivant fait référence les éléments d’entrée et de date pour définir les valeurs à partir de l’objet de tâche :

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

## <a name="loading-json-from-a-url"></a>Le chargement de JSON à partir d’une url

SERVEUR CIBLE MAÎTRE. D prend également en charge le chargement dynamique de JSON à partir d’une Url externe en passant simplement l’Url au constructeur de la `JsonElement`. SERVEUR CIBLE MAÎTRE. D augmentera la hiérarchie déclarée dans le JSON à la demande lorsque vous naviguez entre les écrans. Par exemple, considérez un fichier JSON tel que celui ci-dessous situé à la racine du serveur web local :

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

Nous pouvons le charger à l’aide de la `JsonElement` comme dans le code suivant :

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

Lors de l’exécution, le fichier est récupéré et analysé par serveur cible maître. D lorsque l’utilisateur accède à la deuxième vue, comme indiqué dans la capture d’écran ci-dessous :

 [![](json-element-walkthrough-images/04-json-web-example.png "Le fichier est récupéré et analysé par serveur cible maître. D lorsque l’utilisateur accède à la deuxième vue")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Récapitulatif

Cet article vous a montré comment créer un à l’aide de l’interface avec le serveur cible maître. D à partir de JSON. Il vous a montré comment charger JSON inclus dans un fichier avec l’application ainsi qu’à partir d’une Url distante. Il a également montré comment accéder aux éléments décrits dans le JSON lors de l’exécution.

## <a name="related-links"></a>Liens connexes

- [MTDJsonDemo (sample)](https://developer.xamarin.com/samples/monotouch/MTDJsonDemo/)
- [Introduction à MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [API de procédure pas à pas les éléments](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procédure pas à pas API de réflexion](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Boîte de dialogue MonoTouch sur Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Application de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Référence de classe de UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de classe de UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
