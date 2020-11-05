---
title: Xamarin.Forms Présentation des services Web
description: Ce guide fournit une procédure pas à pas de l' Xamarin.Forms exemple d’application qui montre comment communiquer avec différents services Web. Bien que chaque service Web utilise un exemple d’application distinct, il est similaire au fonctionnement et partage des classes communes.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7d1cb1d9fa418cd16cb25519680e526864c9917c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367723"
---
# <a name="no-locxamarinforms-web-services-introduction"></a>Xamarin.Forms Présentation des services Web

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Cette rubrique fournit une procédure pas à pas de l' Xamarin.Forms exemple d’application qui montre comment communiquer avec différents services Web. Bien que chaque service Web utilise un exemple d’application distinct, il est similaire au fonctionnement et partage des classes communes._

L’exemple d’application de liste de tâches décrite ci-dessous est utilisé pour montrer comment accéder aux différents types de serveurs principaux de service Web avec Xamarin.Forms . Il offre les fonctionnalités suivantes :

- Affichez une liste de tâches.
- Ajouter, modifier et supprimer des tâches.
- Définissez l’état d’une tâche sur « terminé ».
- Parlez les champs nom et notes de la tâche.

Dans tous les cas, les tâches sont stockées dans un serveur principal accessible par le biais d’un service Web.

Lorsque l’application est lancée, une page s’affiche et répertorie toutes les tâches récupérées à partir du service Web et permet à l’utilisateur de créer une nouvelle tâche. Cliquez sur une tâche pour accéder à une deuxième page dans laquelle la tâche peut être modifiée, enregistrée, supprimée et parlée. L’application finale est indiquée ci-dessous :

![Tâche TODO application-première page ](introduction-images/app-example-1.png)
 ![ TODO application-second](introduction-images/app-example-2.png)

Chaque rubrique de ce guide fournit un lien de téléchargement vers une *autre* version de l’application qui illustre un type spécifique de serveur principal du service Web. Téléchargez l’exemple de code approprié sur la page relative à chaque style de service Web.

## <a name="understand-the-application-anatomy"></a>Comprendre l’anatomie des applications

Le projet de code partagé pour chaque exemple d’application se compose de trois dossiers principaux :

|Dossier|Objectif|
|--- |--- |
|Données|Contient les classes et les interfaces utilisées pour gérer les éléments de données, et communiquer avec le service Web. Au minimum, cela comprend la `TodoItemManager` classe, qui est exposée via une propriété de la `App` classe pour appeler des opérations de service Web.|
|Modèles|Contient les classes de modèle de données pour l’application. Au minimum, cela comprend la `TodoItem` classe, qui modélise un élément unique de données utilisé par l’application. Le dossier peut également inclure toutes les classes supplémentaires utilisées pour modéliser les données utilisateur.|
|Les vues|Contient les pages de l’application. Il s’agit généralement des `TodoListPage` `TodoItemPage` classes et, ainsi que de toute classe supplémentaire utilisée à des fins d’authentification.|

Le projet de code partagé pour chaque application se compose également d’un certain nombre de fichiers importants :

|Fichier|Objectif|
|--- |--- |
|Constants.cs|La `Constants` classe, qui spécifie les constantes utilisées par l’application pour communiquer avec le service Web. Ces constantes requièrent une mise à jour pour accéder à votre service principal personnel créé sur un fournisseur.|
|ITextToSpeech.cs|L' `ITextToSpeech` interface, qui spécifie que la `Speak` méthode doit être fournie par toutes les classes d’implémentation.|
|Todo.cs|`App`Classe responsable de l’instanciation de la première page qui sera affichée par l’application sur chaque plateforme, et de la `TodoItemManager` classe utilisée pour appeler les opérations de service Web.|

### <a name="view-pages"></a>Afficher les pages

La majorité des exemples d’applications contiennent au moins deux pages :

- **TodoListPage** : cette page affiche une liste d' `TodoItem` instances et une icône de graduation si la `TodoItem.Done` propriété est `true` . Cliquez sur un élément pour accéder à `TodoItemPage` . En outre, vous pouvez créer des éléments en cliquant sur le *+* symbole.
- **TodoItemPage** : cette page affiche les détails de l’option sélectionnée `TodoItem` et permet son modification, son enregistrement, sa suppression et son discours.

En outre, certains exemples d’applications contiennent des pages supplémentaires qui sont utilisées pour gérer le processus d’authentification de l’utilisateur.

### <a name="model-the-data"></a>Modéliser les données

Chaque exemple d’application utilise la `TodoItem` classe pour modéliser les données qui sont affichées et envoyées au service Web pour le stockage. L’exemple de code suivant illustre la classe `TodoItem` :

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

La `ID` propriété est utilisée pour identifier de manière unique chaque `TodoItem` instance, et est utilisée par chaque service Web pour identifier les données à mettre à jour ou à supprimer.

### <a name="invoke-web-service-operations"></a>Appeler des opérations de service Web

Les opérations de service Web sont accessibles par le biais de la `TodoItemManager` classe, et une instance de la classe est accessible via la `App.TodoManager` propriété. La `TodoItemManager` classe fournit les méthodes suivantes pour appeler des opérations de service Web :

- **GetTasksAsync** : cette méthode est utilisée pour remplir le `ListView` contrôle sur le `TodoListPage` avec les `TodoItem` instances récupérées à partir du service Web.
- **SaveTaskAsync** : cette méthode est utilisée pour créer ou mettre à jour une `TodoItem` instance sur le service Web.
- **DeleteTaskAsync** : cette méthode est utilisée pour supprimer une `TodoItem` instance sur le service Web.

En outre, certains exemples d’applications contiennent des méthodes supplémentaires dans la `TodoItemManager` classe, qui sont utilisées pour gérer le processus d’authentification de l’utilisateur.

Au lieu d’appeler directement les opérations de service Web, les `TodoItemManager` méthodes appellent des méthodes sur une classe dépendante qui est injectée dans le `TodoItemManager` constructeur. Par exemple, un exemple d’application injecte la `RestService` classe dans le `TodoItemManager` constructeur pour fournir l’implémentation qui utilise les API REST pour accéder aux données.

## <a name="related-links"></a>Liens connexes

- [ASMX (exemple)](/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (exemple)](/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (exemple)](/samples/xamarin/xamarin-forms-samples/webservices-todorest)