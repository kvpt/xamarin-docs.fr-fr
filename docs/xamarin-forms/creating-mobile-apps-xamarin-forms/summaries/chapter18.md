---
title: Résumé du chapitre 18. MVVM
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771074"
---
# <a name="summary-of-chapter-18-mvvm"></a>Résumé du chapitre 18. MVVM

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

L’une des meilleures façons d’élaborer une application consiste à séparer l’interface utilisateur du code sous-jacent, qui est parfois appelé la *logique métier*. Plusieurs techniques existent, mais celui qui est adapté aux environnements basés sur le XAML est appelé Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Relations MVVM

Une application MVVM a trois couches :

- Le modèle fournit des données sous-jacentes, parfois dans les fichiers ou web accède à
- La vue est l’utilisateur interface ou la couche présentation, généralement implémentée dans XAML
- Le ViewModel connecte le modèle et la vue

Le modèle est ignorant la du ViewModel et le ViewModel est ignorant la de la vue. Généralement, ces trois couches se connectent entre eux à l’aide des mécanismes suivants :

![Affichage, ViewModel et affichage](images/ch18fg03.png "MVVM")

Dans de nombreux programmes plus petits (et ceux qui sont encore plus important), le modèle est souvent absent ou sa fonctionnalité est intégrée dans le ViewModel.

## <a name="viewmodels-and-data-binding"></a>Liaison de données et ViewModels

Pour s’engager dans des liaisons de données, un ViewModel doit être capable d’informer la vue lorsqu’une propriété du ViewModel a changé. Pour ce faire, le ViewModel implémente l’interface [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) dans l’espace de noms `System.ComponentModel`. Il s’agit de partie de .NET au lieu de Xamarin.Forms. (Généralement ViewModels tenter de maintenir l’indépendance de la plateforme.)

L’interface `INotifyPropertyChanged` déclare un événement unique nommé [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) qui indique la propriété qui a changé.

### <a name="a-viewmodel-clock"></a>Une horloge de ViewModel

La [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) définit une propriété de type `DateTime` qui change en fonction d’un minuteur. La classe implémente `INotifyPropertyChanged` et déclenche l’événement `PropertyChanged` chaque fois que la propriété `DateTime` change.

L’exemple [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) instancie ce ViewModel et utilise des liaisons de données au ViewModel pour afficher les informations de date et d’heure mises à jour.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriétés interactives d’un ViewModel

Les propriétés d’un ViewModel peuvent être plus interactives, comme le montre la classe [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) , qui fait partie de l’exemple [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) . Les liaisons de données fournissent des valeurs multiplicande et multiplicateur à partir de deux éléments `Slider` et affichent le produit avec une `Label`. Toutefois, vous pouvez modifier considérablement cette interface utilisateur dans XAML sans aucune modification à la suite de ViewModel ou le fichier code-behind.

### <a name="a-color-viewmodel"></a>Un ViewModel de couleur

La [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) intègre les modèles de couleurs RVB et TSL. Il est illustré dans l’exemple [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) :

[![Capture d’écran triple de TK](images/ch18fg08-small.png "TSL, modèle de couleurs")](images/ch18fg08-large.png#lightbox "TSL, modèle de couleurs")

### <a name="streamlining-the-viewmodel"></a>Rationalisation du ViewModel

Le code dans ViewModels peut être rationalisé en définissant une méthode `OnPropertyChanged` à l’aide de l’attribut [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) , qui obtient automatiquement le nom de la propriété appelante. La classe [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) effectue cette définition et fournit une classe de base pour ViewModels.

## <a name="the-command-interface"></a>L’interface de commande

MVVM fonctionne avec les liaisons de données, et les liaisons de données fonctionnent avec les propriétés. par conséquent, MVVM semble inadapté lorsqu’il s’agit de gérer un événement `Clicked` d’un `Button` ou d’un événement `Tapped` d’un `TapGestureRecognizer`. Pour permettre à ViewModels de gérer ces événements, Xamarin. Forms prend en charge l' *interface de commande*.

L’interface de commande se manifeste dans le `Button` avec deux propriétés publiques :

- [`Command`](xref:Xamarin.Forms.Button.Command) de type [`ICommand`](xref:System.Windows.Input.ICommand) (défini dans l’espace de noms `System.Windows.Input`)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de type `Object`

Pour prendre en charge l’interface de commande, un ViewModel doit définir une propriété de type `ICommand` qui est ensuite liée aux données de la propriété `Command` du `Button`. L’interface `ICommand` déclare deux méthodes et un événement :

- Une méthode [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) avec un argument de type `object`
- Une méthode [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) avec un argument de type `object` qui retourne `bool`
- Événement [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)

En interne, un ViewModel définit chaque propriété de type `ICommand` sur une instance d’une classe qui implémente l’interface `ICommand`. Par le biais de la liaison de données, le `Button` appelle initialement la méthode `CanExecute` et se désactive si la méthode retourne `false`. Il définit également un gestionnaire pour l’événement `CanExecuteChanged` et appelle `CanExecute` chaque fois que cet événement est déclenché. Si la `Button` est activée, elle appelle la méthode `Execute` chaque fois que l’utilisateur clique sur l' `Button`.

Vous devrez peut-être certains ViewModels antérieures à Xamarin.Forms, et ceux-ci peuvent prennent déjà en charge l’interface de commande. Pour les nouvelles ViewModels destinées à être utilisées uniquement avec Xamarin. Forms, Xamarin. Forms fournit une classe [`Command`](xref:Xamarin.Forms.Command) et une classe [`Command<T>`](xref:Xamarin.Forms.Command`1) qui implémentent l’interface `ICommand`. Le type générique est le type de l’argument pour les méthodes `Execute` et `CanExecute`.

### <a name="simple-method-executions"></a>Exécutions de méthode simple

L’exemple [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) montre comment utiliser l’interface de commande dans un ViewModel. La classe [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) définit deux propriétés de type `ICommand` et définit également deux propriétés privées qu’elle passe au [constructeur`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action))le plus simple. Le programme contient des liaisons de données de ce ViewModel vers les propriétés de `Command` de deux éléments `Button`.

Les éléments `Button` peuvent être facilement remplacés par des objets `TapGestureRecognizer` en XAML sans modification du code.

### <a name="a-calculator-almost"></a>Une calculatrice, presque

L’exemple [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) utilise à la fois les méthodes `Execute` et `CanExecute` de `ICommand`. Elle utilise une classe [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) . Le ViewModel contient six propriétés de type `ICommand`. Ceux-ci sont initialisés à partir du [constructeur`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action)) et [`Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` et le [constructeur`Command<T>`](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>`. Les clés numériques de l’ordinateur en cours d’ajout sont toutes liées à la propriété qui est initialisée avec `Command<T>`et un argument `string` à `Execute` et `CanExecute` identifie la clé particulière.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels et le cycle de vie d’application

Le `AdderViewModel` utilisé dans l’exemple **AddingMachine** définit également deux méthodes nommées `SaveState` et `RestoreState`. Ces méthodes sont appelées à partir de l’application lorsqu’elle se met en veille et lorsqu’il démarre à nouveau.

## <a name="related-links"></a>Liens connexes

- [Chapitre 18 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Exemples du chapitre 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modèles d’application d’entreprise utilisant le livre électronique Xamarin. Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
