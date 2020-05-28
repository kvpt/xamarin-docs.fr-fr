---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 18. MVVM''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1f180173a42654c54c5686e423ba20d9586271ea
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136706"
---
# <a name="summary-of-chapter-18-mvvm"></a>Résumé du chapitre 18. MVVM

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

L’une des meilleures façons d’élaborer une application consiste à séparer l’interface utilisateur du code sous-jacent, qui est parfois appelé la *logique métier*. Plusieurs techniques existent, mais celle qui est adaptée aux environnements XAML est appelée Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Interrelations MVVM

Une application MVVM a trois couches :

- Le modèle fournit des données sous-jacentes, parfois par le biais de fichiers ou d’accès Web.
- La vue est l’interface utilisateur ou la couche de présentation, généralement implémentée en XAML
- Le ViewModel connecte le modèle et la vue

Le modèle ignore le ViewModel et le ViewModel ignore la vue. Ces trois couches se connectent généralement les unes aux autres à l’aide des mécanismes suivants :

![Affichage, ViewModel et affichage](images/ch18fg03.png "MVVM")

Dans de nombreux programmes plus petits (et même plus grands), le modèle est souvent absent ou ses fonctionnalités sont intégrées au ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModels et liaison de données

Pour s’engager dans les liaisons de données, un ViewModel doit être en capacité de notifier la vue lorsqu’une propriété du ViewModel a changé. Le ViewModel effectue cette mise en œuvre en implémentant l' [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) interface dans l' `System.ComponentModel` espace de noms. Cela fait partie de .NET plutôt que de Xamarin.Forms . (En général, ViewModels tente de maintenir l’indépendance de la plateforme.)

L' `INotifyPropertyChanged` interface déclare un événement unique nommé [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) qui indique la propriété qui a changé.

### <a name="a-viewmodel-clock"></a>Horloge ViewModel

Le [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) définit une propriété de type `DateTime` qui change en fonction d’un minuteur. La classe implémente `INotifyPropertyChanged` et déclenche l' `PropertyChanged` événement chaque fois que la `DateTime` propriété est modifiée.

L’exemple [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) instancie ce ViewModel et utilise des liaisons de données au ViewModel pour afficher les informations de date et d’heure mises à jour.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriétés interactives dans un ViewModel

Les propriétés d’un ViewModel peuvent être plus interactives, comme illustré par la [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) classe, qui fait partie de l’exemple [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) . Les liaisons de données fournissent des valeurs multiplicande et multiplicateur à partir de deux `Slider` éléments et affichent le produit avec un `Label` . Toutefois, vous pouvez apporter des modifications apportées à cette interface utilisateur en XAML, sans apporter de modifications consécutives au ViewModel ou au fichier code-behind.

### <a name="a-color-viewmodel"></a>Un ViewModel de couleur

Le [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) intègre les modèles de couleurs RVB et TSL. Il est illustré dans l’exemple [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) :

[![Capture d’écran triple de TK](images/ch18fg08-small.png "TSL, modèle de couleurs")](images/ch18fg08-large.png#lightbox "TSL, modèle de couleurs")

### <a name="streamlining-the-viewmodel"></a>Rationalisation du ViewModel

Le code dans ViewModels peut être rationalisé en définissant une `OnPropertyChanged` méthode à l’aide de l' [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) attribut, qui obtient automatiquement le nom de la propriété appelante. La [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) effectue cette définition et fournit une classe de base pour ViewModels.

## <a name="the-command-interface"></a>Interface de commande

MVVM fonctionne avec les liaisons de données, et les liaisons de données fonctionnent avec les propriétés. par conséquent, MVVM semble inadapté lorsqu’il s’agit de gérer un `Clicked` événement de `Button` ou un `Tapped` événement de `TapGestureRecognizer` . Pour permettre à ViewModels de gérer ces événements, Xamarin.Forms prend en charge l' *interface de commande*.

L’interface de commande se manifeste dans le `Button` avec deux propriétés publiques :

- [`Command`](xref:Xamarin.Forms.Button.Command)de type [`ICommand`](xref:System.Windows.Input.ICommand) (défini dans l' `System.Windows.Input` espace de noms)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)de type`Object`

Pour prendre en charge l’interface de commande, un ViewModel doit définir une propriété de type `ICommand` qui est ensuite liée aux données `Command` de la propriété de `Button` . L' `ICommand` interface déclare deux méthodes et un événement :

- Une [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) méthode avec un argument de type`object`
- Une [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) méthode avec un argument de type `object` qui retourne`bool`
- [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)Événement

En interne, un ViewModel définit chaque propriété de type `ICommand` sur une instance d’une classe qui implémente l' `ICommand` interface. Par le biais de la liaison de données, le `Button` appelle initialement la `CanExecute` méthode et se désactive si la méthode retourne `false` . Il définit également un gestionnaire pour l' `CanExecuteChanged` événement et appelle `CanExecute` chaque fois que cet événement est déclenché. Si `Button` est activé, il appelle la `Execute` méthode chaque fois que l' `Button` utilisateur clique sur.

Il se peut que vous ayez des ViewModels qui se Xamarin.Forms préprennent, et que ceux-ci prennent déjà en charge l’interface de commande. Pour les nouvelles ViewModels destinées à être utilisées uniquement avec Xamarin.Forms , Xamarin.Forms fournit une [`Command`](xref:Xamarin.Forms.Command) classe et une [`Command<T>`](xref:Xamarin.Forms.Command`1) classe qui implémente l' `ICommand` interface. Le type générique est le type de l’argument des `Execute` méthodes et `CanExecute` .

### <a name="simple-method-executions"></a>Exécutions de méthodes simples

L’exemple [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) montre comment utiliser l’interface de commande dans un ViewModel. La [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe définit deux propriétés de type `ICommand` et définit également deux propriétés privées qu’elle passe au [ `Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action))le plus simple. Le programme contient des liaisons de données de ce ViewModel vers les `Command` Propriétés de deux `Button` éléments.

Les `Button` éléments peuvent être facilement remplacés par des `TapGestureRecognizer` objets en XAML sans modification du code.

### <a name="a-calculator-almost"></a>Une calculatrice, presque

L’exemple [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) utilise à la fois les `Execute` `CanExecute` méthodes et de `ICommand` . Elle utilise une [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) . Le ViewModel contient six propriétés de type `ICommand` . Ceux-ci sont initialisés à partir du [ `Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action)) [ `Command` et du constructeur de](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) `Command` et du [ `Command<T>` constructeur](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>` . Les clés numériques de l’ordinateur en cours d’ajout sont toutes liées à la propriété qui est initialisée avec `Command<T>` , et un `string` argument à `Execute` et `CanExecute` identifie la clé particulière.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels et cycle de vie de l’application

Le `AdderViewModel` utilisé dans l’exemple **AddingMachine** définit également deux méthodes nommées `SaveState` et `RestoreState` . Ces méthodes sont appelées à partir de l’application lorsqu’elle passe en mode veille et lorsqu’elle recommence.

## <a name="related-links"></a>Liens connexes

- [Chapitre 18 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Exemples du chapitre 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modèles d’application d’entreprise utilisant Xamarin.Forms ebook](~/xamarin-forms/enterprise-application-patterns/index.md)
