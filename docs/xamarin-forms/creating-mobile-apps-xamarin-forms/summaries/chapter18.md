---
title: Résumé du chapitre 18. MVVM
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771074"
---
# <a name="summary-of-chapter-18-mvvm"></a>Résumé du chapitre 18. MVVM

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Une des meilleures façons d’architectes d’une application est de séparer l’interface utilisateur du code sous-jacent, qui est parfois appelé la *logique d’entreprise*. Plusieurs techniques existent, mais celle qui est adaptée aux environnements basés sur XAML est connue sous le nom de Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Interdépendances MVVM

Une application MVVM comporte trois couches :

- Le modèle fournit des données sous-jacentes, parfois par le biais de fichiers ou d’accès Web
- La vue est l’interface utilisateur ou la couche de présentation, généralement implémentée dans XAML
- Le ViewModel connecte le modèle et la vue

Le modèle est ignorant du ViewModel et le ViewModel est ignorant de la vue. Ces trois couches se connectent généralement les unes aux autres à l’aide des mécanismes suivants :

![Afficher, Affichermodel et Afficher](images/ch18fg03.png "MVVM")

Dans de nombreux programmes plus petits (et encore plus grands), souvent le modèle est absent ou sa fonctionnalité est intégrée dans le ViewModel.

## <a name="viewmodels-and-data-binding"></a>VoirModels et liaison de données

Pour s’engager dans des liaisons de données, un ViewModel doit être capable d’aviser la vue lorsqu’une propriété du ViewModel a changé. Le ViewModel le fait [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) en implémentant l’interface dans l’espace nom. `System.ComponentModel` Cela fait partie de .NET plutôt que Xamarin.Forms. (Généralement ViewModels tenter de maintenir l’indépendance de la plate-forme.)

L’interface `INotifyPropertyChanged` déclare un [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) seul événement nommé qui indique la propriété qui a changé.

### <a name="a-viewmodel-clock"></a>Une horloge ViewModel

La [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) définit une propriété `DateTime` de type qui change en fonction d’une minuterie. La classe `INotifyPropertyChanged` met en `PropertyChanged` œuvre et déclenche l’événement chaque fois que la `DateTime` propriété change.

[**L’échantillon MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) instantané ce ViewModel et utilise des liaisons de données pour le ViewModel pour afficher les informations mises à jour date et heure.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriétés interactives dans un ViewModel

Les propriétés d’un ViewModel peuvent [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) être plus interactives, comme l’a démontré la classe, qui fait partie de [**l’échantillon SimpleMultiplier.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) Les liaisons de données fournissent des valeurs `Slider` multiplicanes et `Label`multiplicateurs à partir de deux éléments et affichent le produit avec un . Toutefois, vous pouvez apporter des modifications importantes à cette interface utilisateur dans XAML sans modifications conséquentes au ViewModel ou au fichier de code.

### <a name="a-color-viewmodel"></a>Un viewModel de couleur

La [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) intègre les modèles couleur RGB et HSL. Il est démontré dans l’échantillon [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) :

[![Triple capture d’écran de TK](images/ch18fg08-small.png "Modèle de couleur HSL")](images/ch18fg08-large.png#lightbox "Modèle de couleur HSL")

### <a name="streamlining-the-viewmodel"></a>Rationaliser le ViewModel

Le code dans ViewModels peut être `OnPropertyChanged` simplifié en [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) définissant une méthode utilisant l’attribut, qui obtient automatiquement le nom de propriété d’appel. La [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) le fait et fournit une classe de base pour ViewModels.

## <a name="the-command-interface"></a>L’interface Command

MVVM travaille avec des liaisons de données, et les liaisons de données fonctionnent avec `Clicked` les propriétés, de sorte MVVM semble être déficient quand il s’agit de gérer un événement d’un `Button` ou un `Tapped` événement d’un `TapGestureRecognizer`. Pour permettre à ViewModels de gérer de tels événements, Xamarin.Forms prend en charge *l’interface de commande*.

L’interface de commande `Button` se manifeste dans le avec deux propriétés publiques :

- [`Command`](xref:Xamarin.Forms.Button.Command)type [`ICommand`](xref:System.Windows.Input.ICommand) (défini dans `System.Windows.Input` l’espace nom)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)de type`Object`

Pour prendre en charge l’interface de commande, `ICommand` un ViewModel `Command` doit définir `Button`une propriété de type qui est alors des données liées à la propriété de la . L’interface `ICommand` déclare deux méthodes et un événement :

- Une [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) méthode avec un argument de type`object`
- Une [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) méthode avec un `object` argument de type qui revient`bool`
- Un [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged) événement

En interne, un ViewModel `ICommand` définit chaque propriété de type `ICommand` à une instance d’une classe qui implémente l’interface. Grâce à la `Button` liaison de `CanExecute` données, le commencement appelle `false`la méthode, et se désactive si la méthode revient . Il définit également un `CanExecuteChanged` gestionnaire `CanExecute` pour l’événement et appelle chaque fois que cet événement est déclenché. Si `Button` le est activé, `Execute` il `Button` appelle la méthode chaque fois que le est cliqué.

Vous pourriez avoir quelques ViewModels qui précèdent Xamarin.Forms, et ceux-ci pourraient déjà prendre en charge l’interface de commande. Pour les nouveaux ViewModels destinés à être utilisés uniquement avec Xamarin.Forms, Xamarin.Forms fournit une [`Command`](xref:Xamarin.Forms.Command) classe et une [`Command<T>`](xref:Xamarin.Forms.Command`1) classe qui implémentent l’interface. `ICommand` Le type générique est le type `Execute` `CanExecute` de l’argument à la et les méthodes.

### <a name="simple-method-executions"></a>Exécutions de méthodes simples

[**L’échantillon PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) montre comment utiliser l’interface de commande dans un ViewModel. La [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe définit deux `ICommand` propriétés de type et définit également deux propriétés privées qu’elle passe au [ `Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action))le plus simple. Le programme contient des liaisons de `Command` données de `Button` ce ViewModel aux propriétés de deux éléments.

Les `Button` éléments peuvent être `TapGestureRecognizer` facilement remplacés par des objets dans XAML sans modification de code.

### <a name="a-calculator-almost"></a>Une calculatrice, presque

[**L’échantillon AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) utilise `Execute` à `CanExecute` la `ICommand`fois le et les méthodes de . Il utilise [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) une classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) Le ViewModel contient six `ICommand`propriétés de type . Ceux-ci sont [ `Command` ](xref:Xamarin.Forms.Command.%23ctor(System.Action)) parasécés à partir du `Command<T>`constructeur et [ `Command` constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` et le [ `Command<T>` constructeur](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de . Les clés numériques de la machine d’ajout sont `Command<T>`toutes `string` liées `Execute` à `CanExecute` la propriété qui est parasitée avec, et un argument à et identifie la clé particulière.

## <a name="viewmodels-and-the-application-lifecycle"></a>VoirModels et le cycle de vie de l’application

L’utilisé `AdderViewModel` dans **l’échantillon AddingMachine** `SaveState` définit `RestoreState`également deux méthodes nommées et . Ces méthodes sont appelées à partir de l’application quand il va dormir et quand il recommence.

## <a name="related-links"></a>Liens connexes

- [Chapitre 18 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Échantillons du chapitre 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modèles d’applications d’entreprise à l’aide de Xamarin.Forms eBook](~/xamarin-forms/enterprise-application-patterns/index.md)
