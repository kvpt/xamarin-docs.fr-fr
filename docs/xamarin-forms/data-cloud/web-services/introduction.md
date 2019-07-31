---
title: Présentation des services Web Xamarin. Forms
description: Ce guide fournit une procédure pas à pas de l’exemple d’application Xamarin. Forms qui montre comment communiquer avec différents services Web. Bien que chaque service web utilise une application d’exemple distinct, ils sont fonctionnellement similaires et partager des classes courantes.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656640"
---
# <a name="xamarinforms-web-services-introduction"></a>Présentation des services Web Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Cette rubrique fournit une procédure pas à pas de l’exemple d’application Xamarin.Forms qui montre comment communiquer avec les services web différents. Bien que chaque service web utilise une application d’exemple distinct, ils sont fonctionnellement similaires et partager des classes courantes._

L’exemple d’application de liste des tâches décrite ci-dessous est utilisé pour illustrer comment accéder aux différents types de serveurs principaux de service web avec Xamarin.Forms. Il fournit des fonctionnalités pour :

- Afficher la liste des tâches.
- Ajouter, modifier et supprimer des tâches.
- Définissez l’état d’une tâche à « terminé ».
- Énonce les champs nom et les notes de la tâche.

Dans tous les cas, les tâches sont stockées dans un serveur principal qui est accessible via un service web.

Lorsque l’application est lancée, une page s’affiche qui répertorie toutes les tâches extraites du service web et permet à l’utilisateur créer une nouvelle tâche. En cliquant sur une tâche accède à l’application vers une deuxième page où la tâche peut être modifiée, enregistrée, supprimée et parlée. L’application finale est indiquée ci-dessous :

![](introduction-images/app-example-1.png "Application TODO - première page")
![](introduction-images/app-example-2.png "application Todo - deuxième page")

Chaque rubrique de ce guide fournit un lien de téléchargement vers un *différents* version de l’application qui illustre un type spécifique de serveur principal du service web. Télécharger le code exemple pertinentes sur la page relative à chaque service web de style.

## <a name="understand-the-application-anatomy"></a>Comprendre l’anatomie des applications

Le projet de code partagé pour chaque exemple d’application se compose de trois dossiers principaux:

|Dossier|Objectif|
|--- |--- |
|Données|Contient les classes et les interfaces utilisées pour gérer les éléments de données et communiquer avec le service web. Au minimum, cela inclut le `TodoItemManager` (classe), qui est exposé via une propriété dans la `App` classe pour appeler des opérations de service web.|
|Modèles|Contient les classes de modèle de données pour l’application. Au minimum, cela inclut le `TodoItem` (classe), qui modélise un seul élément de données utilisées par l’application. Le dossier peut également inclure les classes supplémentaires utilisées pour modéliser les données utilisateur.|
|Affichages|Contient les pages de l’application. Il s’agit généralement de la `TodoListPage` et `TodoItemPage` classes et les classes supplémentaires utilisés à des fins d’authentification.|

Le projet de code partagé pour chaque application se compose également d’un certain nombre de fichiers importants:

|Fichier|Objectif|
|--- |--- |
|Constants.cs|Le `Constants` (classe), qui spécifie les constantes utilisées par l’application pour communiquer avec le service web. Ces constantes nécessitent la mise à jour pour accéder à votre service principal personnel créé sur un fournisseur.|
|ITextToSpeech.cs|Le `ITextToSpeech` interface, qui spécifie que le `Speak` méthode doit être fournie par les classes d’implémentation.|
|TODO.cs|Le `App` classe qui est responsable de l’instanciation à la fois la première page qui sera affichée par l’application sur chaque plateforme, et le `TodoItemManager` classe qui est utilisée pour appeler des opérations de service web.|

### <a name="view-pages"></a>Afficher les pages

La plupart des exemples d’applications contiennent au moins deux pages :

- **TodoListPage** : cette page affiche une liste de `TodoItem` instances et une icône représentant une coche si le `TodoItem.Done` propriété est `true`. En cliquant sur un élément accède à la `TodoItemPage`. En outre, les nouveaux éléments peuvent être créés en cliquant sur le *+* symbole.
- **TodoItemPage** : cette page affiche les détails correspondant à votre `TodoItem`et lui permet d’être modifié, enregistré, supprimés et parlée.

En outre, certains exemples d’applications contiennent des pages supplémentaires sont utilisées pour gérer le processus d’authentification utilisateur.

### <a name="model-the-data"></a>Modéliser les données

Chaque exemple d’application utilise le `TodoItem` classe pour modéliser les données qui s’affiche et est envoyées au service web pour le stockage. L’exemple de code suivant illustre la classe `TodoItem` :

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Le `ID` propriété est utilisée pour identifier de manière unique chaque `TodoItem` d’instance et est utilisé par chaque service web pour identifier les données mises à jour ou à supprimer.

### <a name="invoke-web-service-operations"></a>Appeler des opérations de service Web

Opérations de service Web sont accessibles via le `TodoItemManager` classe et une instance de la classe est accessible via la `App.TodoManager` propriété. Le `TodoItemManager` classe fournit les méthodes suivantes pour appeler des opérations de service web :

- **GetTasksAsync** – cette méthode est utilisée pour remplir le `ListView` control sur le `TodoListPage` avec la `TodoItem` instances récupérées à partir du service web.
- **SaveTaskAsync** – cette méthode est utilisée pour créer ou mettre à jour un `TodoItem` instance sur le service web.
- **DeleteTaskAsync** – cette méthode est utilisée pour supprimer un `TodoItem` instance sur le service web.

En outre, certains exemples d’applications contiennent des méthodes supplémentaires dans le `TodoItemManager` (classe), qui sont utilisés pour gérer le processus d’authentification utilisateur.

Plutôt que d’appeler les opérations de service web directement, le `TodoItemManager` méthodes appellent des méthodes sur une classe dépendante est injecté dans le `TodoItemManager` constructeur. Par exemple, un exemple d’application injecte le `RestService` classe dans le `TodoItemManager` constructeur pour fournir l’implémentation qui utilise des API REST pour accéder aux données.

## <a name="related-links"></a>Liens connexes

- [ASMX (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
