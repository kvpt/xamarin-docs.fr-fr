---
title: Visual Basic dans Xamarin. Android et Xamarin. iOS
description: Cette procédure pas à pas montre comment générer des applications Xamarin. iOS et Xamarin. Android natives qui utilisent une logique métier écrite en Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 9f227f51596a4ed93fd830c3f3495a90c1f7f722
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014566"
---
# <a name="visual-basic-in-xamarin-android-and-ios"></a>Visual Basic dans Xamarin Android et iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)

L’exemple d’application [TaskyVB](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/) montre comment Visual Basic code compilé dans une bibliothèque de .NET standard peut être utilisé avec Xamarin. Voici quelques captures d’écran des applications qui s’exécutent sur Android et iOS :

 [![Android et iOS exécutant une application générée avec Visual Basic](native-apps-images/simulators-sml.png)](native-apps-images/simulators.png#lightbox)

Les projets Android et iOS de l’exemple sont tous écrits dans C#. L’interface utilisateur de chaque application est générée avec des technologies natives, tandis que la gestion des `TodoItem` est assurée par la bibliothèque Visual Basic .NET Standard à l’aide d’un fichier XML (à des fins de démonstration, et non comme une base de données complète).

## <a name="sample-walkthrough"></a>Exemple de procédure pas à pas

Ce guide explique comment Visual Basic a été implémenté dans l’exemple [TaskyVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB) Xamarin pour iOS et Android.

> [!NOTE]
> Avant de poursuivre ce guide, passez en revue les instructions sur [Visual Basic et .NET standard](index.md) .
>
> Reportez-vous à [Xamarin. Forms à l’aide](xamarin-forms.md) des instructions Visual Basic pour savoir comment créer une application avec du code Visual Basic de l’interface utilisateur partagée.

## <a name="visualbasicnetstandard"></a>VisualBasicNetStandard

Visual Basic bibliothèques de .NET Standard ne peuvent être créées que dans Visual Studio sur Windows.
L’exemple de bibliothèque contient les principes fondamentaux de notre application dans les fichiers Visual Basic suivants :

- TodoItem. vb
- TodoItemManager. vb
- TodoItemRepositoryXML. vb
- XmlStorage. vb

### <a name="todoitemvb"></a>TodoItem. vb

Cette classe contient l’objet métier à utiliser dans toute l’application. Elle sera définie dans Visual Basic et partagée avec les projets Android et iOS écrits dans C#.

La définition de la classe est illustrée ici :

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

L’exemple utilise la sérialisation et la désérialisation XML pour charger et enregistrer les objets TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager. vb

La classe Manager présente l’API pour le code portable. Il fournit des opérations CRUD de base pour la classe `TodoItem`, mais aucune implémentation de ces opérations.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

Le constructeur prend une instance de IXmlStorage en tant que paramètre. Cela permet à chaque plateforme de fournir sa propre implémentation de travail tout en laissant le code portable décrire d’autres fonctionnalités qui peuvent être partagées.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository. vb

La classe de référentiel contient la logique de gestion de la liste des objets TodoItem. Le code complet est présenté ci-dessous : la logique existe principalement pour gérer une valeur d’ID unique sur les TodoItems à mesure qu’elles sont ajoutées et supprimées de la collection.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String)
        _filename = filename
        _storage = New XmlStorage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> Ce code est un exemple de mécanisme de stockage de données très basique.
> Il est fourni pour illustrer comment une bibliothèque .NET Standard peut coder par rapport à une interface pour accéder aux fonctionnalités spécifiques à la plateforme (dans ce cas, charger et enregistrer un fichier XML). Il ne s’agit pas d’une base de données de qualité production.

## <a name="android-and-ios-application-projects"></a>Projets d’application Android et iOS

### <a name="ios"></a>iOS

Dans l’application iOS, les `TodoItemManager` et les `XmlStorageImplementation` sont créés dans le fichier **AppDelegate.cs** , comme indiqué dans cet extrait de code. Les quatre premières lignes génèrent simplement le chemin d’accès au fichier dans lequel les données seront stockées ; les deux dernières lignes affichent les deux classes qui sont instanciées.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

### <a name="android"></a>Android

Dans l’application Android, les `TodoItemManager` et les `XmlStorageImplementation` sont créés dans le fichier **application.cs** , comme indiqué dans cet extrait de code. Les trois premières lignes génèrent simplement le chemin d’accès au fichier dans lequel les données seront stockées ; les deux dernières lignes affichent les deux classes qui sont instanciées.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

Le reste du code d’application s’intéresse principalement à l’interface utilisateur et à l’utilisation de la classe `TaskMgr` pour charger et enregistrer des classes `TodoItem`.

## <a name="visual-studio-2019-for-mac"></a>Visual Studio 2019 pour Mac

> [!WARNING]
> Visual Studio pour Mac ne prend pas en charge la modification de la langue Visual Basic : il n’existe aucun élément de menu pour la création de projets ou de fichiers Visual Basic. Si vous ouvrez un **. vb** , aucune syntaxe de langage n’est mise en surbrillance, saisie semi-automatique ou IntelliSense.

Visual Studio 2019 pour Mac _permet_ de compiler des projets visual studio .NET standard créés sur Windows, de sorte que les applications iOS peuvent référencer ces projets.

Visual Studio 2017 _ne peut pas_ générer des projets Visual Basic.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser Visual Basic code dans des applications Xamarin à l’aide de Visual Studio et des bibliothèques de .NET Standard. Même si Xamarin ne prend pas en charge directement Visual Basic, la compilation des Visual Basic dans une bibliothèque de .NET Standard permet d’inclure du code écrit avec Visual Basic dans des applications iOS et Android.

## <a name="related-links"></a>Liens associés

- [TaskyVB (exemple .NET Standard)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB)
- [Nouveautés de .NET Standard](https://docs.microsoft.com/dotnet/standard/whats-new/whats-new-in-dotnet-standard?tabs=csharp)
