---
title: Présentation des services Web Xamarin. Forms
description: Ce guide fournit une procédure pas à pas de l’exemple d’application Xamarin. Forms qui montre comment communiquer avec différents services Web. Bien que chaque service Web utilise un exemple d’application distinct, il est similaire au fonctionnement et partage des classes communes.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656640"
---
# <a name="xamarinforms-web-services-introduction"></a>Présentation des services Web Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Cette rubrique fournit une procédure pas à pas de l’exemple d’application Xamarin. Forms qui montre comment communiquer avec différents services Web. Bien que chaque service Web utilise un exemple d’application distinct, il est similaire au fonctionnement et partage des classes communes._

L’exemple d’application de liste de tâches décrite ci-dessous est utilisé pour montrer comment accéder à différents types de serveurs principaux de service Web avec Xamarin. Forms. Il offre les fonctionnalités suivantes :

- Affichez une liste de tâches.
- Ajouter, modifier et supprimer des tâches.
- Définissez l’état d’une tâche sur « terminé ».
- Parlez les champs nom et notes de la tâche.

Dans tous les cas, les tâches sont stockées dans un serveur principal accessible par le biais d’un service Web.

Lorsque l’application est lancée, une page s’affiche et répertorie toutes les tâches récupérées à partir du service Web et permet à l’utilisateur de créer une nouvelle tâche. Cliquez sur une tâche pour accéder à une deuxième page dans laquelle la tâche peut être modifiée, enregistrée, supprimée et parlée. L’application finale est indiquée ci-dessous :

![](introduction-images/app-example-1.png "Todo application - first page")
![](introduction-images/app-example-2.png "Todo application - second page")

Chaque rubrique de ce guide fournit un lien de téléchargement vers une *autre* version de l’application qui illustre un type spécifique de serveur principal du service Web. Téléchargez l’exemple de code approprié sur la page relative à chaque style de service Web.

## <a name="understand-the-application-anatomy"></a>Comprendre l’anatomie des applications

Le projet de code partagé pour chaque exemple d’application se compose de trois dossiers principaux :

|Dossier|Fonction|
|--- |--- |
|Données|Contient les classes et les interfaces utilisées pour gérer les éléments de données, et communiquer avec le service Web. Au minimum, cela comprend la classe `TodoItemManager`, qui est exposée via une propriété de la classe `App` pour appeler des opérations de service Web.|
|Modèles|Contient les classes de modèle de données pour l’application. Au minimum, cela comprend la classe `TodoItem`, qui modélise un seul élément de données utilisé par l’application. Le dossier peut également inclure toutes les classes supplémentaires utilisées pour modéliser les données utilisateur.|
|Affichages|Contient les pages de l’application. Il s’agit généralement des classes `TodoListPage` et `TodoItemPage`, ainsi que des classes supplémentaires utilisées à des fins d’authentification.|

Le projet de code partagé pour chaque application se compose également d’un certain nombre de fichiers importants :

|Fichier|Fonction|
|--- |--- |
|Constants.cs|La classe `Constants`, qui spécifie les constantes utilisées par l’application pour communiquer avec le service Web. Ces constantes requièrent une mise à jour pour accéder à votre service principal personnel créé sur un fournisseur.|
|ITextToSpeech.cs|L’interface `ITextToSpeech`, qui spécifie que la méthode `Speak` doit être fournie par toutes les classes d’implémentation.|
|Todo.cs|La classe `App` qui est chargée d’instancier la première page qui sera affichée par l’application sur chaque plateforme, et la classe `TodoItemManager` utilisée pour appeler les opérations de service Web.|

### <a name="view-pages"></a>Afficher les pages

La majorité des exemples d’applications contiennent au moins deux pages :

- **TodoListPage** : cette page affiche la liste des instances de `TodoItem`, ainsi qu’une icône de graduation si la propriété `TodoItem.Done` est `true`. Cliquez sur un élément pour accéder à l' `TodoItemPage`. En outre, vous pouvez créer des éléments en cliquant sur le symbole *+* .
- **TodoItemPage** : cette page affiche les détails de la `TodoItem` sélectionnée et permet son modification, son enregistrement, sa suppression et son discours.

En outre, certains exemples d’applications contiennent des pages supplémentaires qui sont utilisées pour gérer le processus d’authentification de l’utilisateur.

### <a name="model-the-data"></a>Modéliser les données

Chaque exemple d’application utilise la classe `TodoItem` pour modéliser les données qui sont affichées et envoyées au service Web pour le stockage. L’exemple de code suivant illustre la classe `TodoItem` :

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

La propriété `ID` est utilisée pour identifier de façon unique chaque instance de `TodoItem`, et est utilisée par chaque service Web pour identifier les données à mettre à jour ou à supprimer.

### <a name="invoke-web-service-operations"></a>Appeler des opérations de service Web

Les opérations de service Web sont accessibles via la classe `TodoItemManager`, et une instance de la classe est accessible via la propriété `App.TodoManager`. La classe `TodoItemManager` fournit les méthodes suivantes pour appeler des opérations de service Web :

- **GetTasksAsync** : cette méthode est utilisée pour remplir le contrôle `ListView` sur le `TodoListPage` avec les instances `TodoItem` récupérées à partir du service Web.
- **SaveTaskAsync** : cette méthode est utilisée pour créer ou mettre à jour une instance de `TodoItem` sur le service Web.
- **DeleteTaskAsync** : cette méthode permet de supprimer une instance de `TodoItem` sur le service Web.

En outre, certains exemples d’applications contiennent des méthodes supplémentaires dans la classe `TodoItemManager`, qui sont utilisées pour gérer le processus d’authentification utilisateur.

Au lieu d’appeler directement les opérations de service Web, les méthodes `TodoItemManager` appellent des méthodes sur une classe dépendante qui est injectée dans le constructeur `TodoItemManager`. Par exemple, un exemple d’application injecte la classe `RestService` dans le constructeur `TodoItemManager` pour fournir l’implémentation qui utilise les API REST pour accéder aux données.

## <a name="related-links"></a>Liens connexes

- [ASMX (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
