---
title: Résumé du chapitre 18. MVVM
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771074"
---
# <a name="summary-of-chapter-18-mvvm"></a>Résumé du chapitre 18. MVVM

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Un des meilleurs moyens de concevoir une application est en séparant l’interface utilisateur à partir du code sous-jacent, ce qui est parfois appelé le *logique métier*. Plusieurs techniques existent, mais celui qui est adapté aux environnements basés sur le XAML est appelé Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Relations MVVM

Une application MVVM a trois couches :

- Le modèle fournit des données sous-jacentes, parfois dans les fichiers ou web accède à
- La vue est l’utilisateur interface ou la couche présentation, généralement implémentée dans XAML
- Le ViewModel connecte le modèle et la vue

Le modèle est ignorant la du ViewModel et le ViewModel est ignorant la de la vue. Généralement, ces trois couches se connectent entre eux à l’aide des mécanismes suivants :

![Affichage, ViewModel et vue](images/ch18fg03.png "MVVM")

Dans de nombreux programmes plus petits (et ceux qui sont encore plus important), le modèle est souvent absent ou sa fonctionnalité est intégrée dans le ViewModel.

## <a name="viewmodels-and-data-binding"></a>Liaison de données et ViewModels

Pour s’engager dans des liaisons de données, un ViewModel doit être capable d’informer la vue lorsqu’une propriété du ViewModel a changé. Le ViewModel fait ceci en implémentant la [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) interface dans le `System.ComponentModel` espace de noms. Il s’agit de partie de .NET au lieu de Xamarin.Forms. (Généralement ViewModels tenter de maintenir l’indépendance de la plateforme.)

Le `INotifyPropertyChanged` interface déclare un événement unique nommé [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) qui indique la propriété qui a changé.

### <a name="a-viewmodel-clock"></a>Une horloge de ViewModel

Le [ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) bibliothèque définit une propriété de type `DateTime` que les modifications basé sur un minuteur. La classe implémente `INotifyPropertyChanged` et déclenche le `PropertyChanged` événement chaque fois que le `DateTime` modifications apportées aux propriétés.

Le [ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) exemple instancie ce ViewModel et utilise des liaisons de données au ViewModel pour afficher les mises à jour informations de date et heure.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriétés interactives d’un ViewModel

Propriétés d’un ViewModel peuvent être plus interactives, tel qu’indiqué par le [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) (classe), qui fait partie de la [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) exemple. Les liaisons de données fournissent des valeurs multiplicande et le multiplicateur de deux `Slider` éléments et afficher le produit avec un `Label`. Toutefois, vous pouvez modifier considérablement cette interface utilisateur dans XAML sans aucune modification à la suite de ViewModel ou le fichier code-behind.

### <a name="a-color-viewmodel"></a>Un ViewModel de couleur

Le [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) bibliothèque intègre les modèles de couleurs RVB et TSL. Il est montré dans le [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) exemple :

[![Capture d’écran triple de TK](images/ch18fg08-small.png "modèle de couleurs TSL")](images/ch18fg08-large.png#lightbox "modèle de couleurs TSL")

### <a name="streamlining-the-viewmodel"></a>Rationalisation du ViewModel

Le code dans le ViewModel peut être rationalisé en définissant un `OnPropertyChanged` à l’aide de la méthode la [ `CallerMemberName` ](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) attribut, qui obtient le nom de propriété appelant automatiquement. Le [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) bibliothèque effectue cette et fournit une classe de base ViewModel.

## <a name="the-command-interface"></a>L’interface de commande

MVVM fonctionne avec les liaisons de données et des liaisons de données fonctionnent avec des propriétés, afin de MVVM semble déficientes lorsqu’il s’agit de la gestion d’un `Clicked` événement d’un `Button` ou un `Tapped` événement d’un `TapGestureRecognizer`. Pour autoriser les ViewModels gérer ces événements, Xamarin.Forms prend en charge la *interface de commande*.

L’interface de commande se présente sous la `Button` avec deux propriétés publiques :

- [`Command`](xref:Xamarin.Forms.Button.Command) de type [ `ICommand` ](xref:System.Windows.Input.ICommand) (défini dans le `System.Windows.Input` espace de noms)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de type `Object`

Pour prendre en charge l’interface de commande, un ViewModel doit définir une propriété de type `ICommand` puis les données liées à la `Command` propriété de la `Button`. Le `ICommand` interface déclare deux méthodes et un événement :

- Un [ `Execute` ](xref:System.Windows.Input.ICommand.Execute(System.Object)) méthode avec un argument de type `object`
- Un [ `CanExecute` ](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) méthode avec un argument de type `object` qui retourne `bool`
- Un [ `CanExecuteChanged` ](xref:System.Windows.Input.ICommand.CanExecuteChanged) événement

En interne, un ViewModel définit chaque propriété de type `ICommand` à une instance d’une classe qui implémente le `ICommand` interface. Via la liaison de données, le `Button` appelle initialement le `CanExecute` (méthode) et se désactive si la méthode retourne `false`. Il définit également un gestionnaire pour le `CanExecuteChanged` événements et les appels `CanExecute` chaque fois que cet événement est déclenché. Si le `Button` est activé, il appelle le `Execute` méthode chaque fois que le `Button` un clic sur.

Vous devrez peut-être certains ViewModels antérieures à Xamarin.Forms, et ceux-ci peuvent prennent déjà en charge l’interface de commande. Pour les ViewModels nouveau destiné à être utilisé uniquement avec Xamarin.Forms, Xamarin.Forms fournit un [ `Command` ](xref:Xamarin.Forms.Command) classe et un [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe qui implémentent le `ICommand` interface. Le type générique est le type de l’argument à la `Execute` et `CanExecute` méthodes.

### <a name="simple-method-executions"></a>Exécutions de méthode simple

Le [ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) exemple montre comment utiliser l’interface de commande dans un ViewModel. Le [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe définit deux propriétés de type `ICommand` et définit également deux propriétés privées qu’il transmet à la plus simple [ `Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action)). Le programme contient des liaisons de données à partir de ce ViewModel à la `Command` propriétés de deux `Button` éléments.

Le `Button` éléments peuvent être facilement remplacées avec `TapGestureRecognizer` objets dans XAML sans aucune modification de code.

### <a name="a-calculator-almost"></a>Une calculatrice, presque

Le [ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) exemple rend utilisez à la fois le `Execute` et `CanExecute` méthodes de `ICommand`. Il utilise un [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) bibliothèque. Le ViewModel contient six propriétés de type `ICommand`. Celles-ci sont initialisées à partir de la [ `Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action)) et [ `Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` et le [ `Command<T>` constructeur](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>`. Les touches numériques de la machine à additionner sont liés à la propriété qui est initialisée avec `Command<T>`et un `string` l’argument de `Execute` et `CanExecute` identifie la clé.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels et le cycle de vie d’application

Le `AdderViewModel` utilisé dans le **AddingMachine** exemple définit également deux méthodes nommées `SaveState` et `RestoreState`. Ces méthodes sont appelées à partir de l’application lorsqu’elle se met en veille et lorsqu’il démarre à nouveau.

## <a name="related-links"></a>Liens connexes

- [Chapitre 18 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Exemples de chapitre 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modèles d’Application d’entreprise à l’aide de livre électronique Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
