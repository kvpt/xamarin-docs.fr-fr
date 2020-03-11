---
title: Résumé du chapitre 8. Code et XAML en harmonie
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 8. Code et XAML en harmonie'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334248"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Résumé du chapitre 8. Code et XAML en harmonie

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

Ce chapitre explore XAML de façon plus approfondie, et en particulier comment code et XAML interagissent.

## <a name="passing-arguments"></a>Passage d’arguments

En règle générale, une classe instanciée dans XAML doit avoir un constructeur sans paramètre public ; l’objet résultant est initialisé par le biais des paramètres de propriété. Toutefois, il existe deux autres méthodes que les objets peuvent être instanciés et initialisés.

Bien qu’il s’agit des techniques à usage général, ils sont principalement utilisés dans le cadre de modèles de vue de MVVM.

### <a name="constructors-with-arguments"></a>Constructeurs avec des arguments

L’exemple [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) montre comment utiliser la balise `x:Arguments` pour spécifier des arguments de constructeur. Ces arguments doivent être délimités par des balises d’élément indiquant le type de l’argument. Pour les types de données .NET base, les balises suivantes sont disponibles :

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>Puis-je appeler des méthodes à partir de XAML ?

L’exemple [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) montre comment utiliser l’élément `x:FactoryMethod` pour spécifier une méthode de fabrique appelée pour créer un objet. Une telle méthode de fabrique doit être publique et statique, et il doit créer un objet du type dans lequel elle est définie. (Par exemple, la méthode [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) est qualifiée, car elle est publique et statique et retourne une valeur de type `Color`.) Les arguments de la méthode de fabrique sont spécifiés dans les balises `x:Arguments`.

## <a name="the-xname-attribute"></a>L’attribut x : Name

L’attribut `x:Name` permet de donner un nom à un objet instancié en XAML. Les règles pour ces noms sont les mêmes que pour les noms de variables c#. Après le retour de l’appel de `InitializeComponent` dans le constructeur, le fichier code-behind peut faire référence à ces noms pour accéder à l’élément XAML correspondant. Les noms sont convertis en fait par l’analyseur XAML dans les champs privés de la classe partielle générée.

L’exemple [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) illustre l’utilisation de `x:Name` pour permettre au fichier code-behind de conserver deux éléments `Label` définis dans XAML mis à jour avec la date et l’heure actuelles.

Le même nom ne peut pas être utilisé pour plusieurs éléments dans la même page. Il s’agit d’un problème particulier si vous utilisez `OnPlatform` pour créer des objets nommés parallèles pour chaque plateforme. L’exemple [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) illustre une meilleure manière d’effectuer une opération similaire.

## <a name="custom-xaml-based-views"></a>Vues personnalisées en fonction du XAML

Il existe plusieurs façons d’éviter la répétition de balisage en XAML. Une technique courante consiste à créer une nouvelle classe XAML qui dérive de [`ContentView`](xref:Xamarin.Forms.ContentView). Cette technique est illustrée dans l’exemple [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) . La classe `ColorView` dérive de `ContentView` pour afficher une couleur particulière et son nom, tandis que la classe `ColorViewListPage` dérive de `ContentPage` comme d’habitude et crée explicitement 17 instances de `ColorView`.

L’accès à la classe `ColorView` dans XAML requiert une autre déclaration d’espace de noms XML, communément nommée `local` pour les classes dans le même assembly.

## <a name="events-and-handlers"></a>Événements et gestionnaires

Événements peuvent être affectés aux gestionnaires d’événements dans XAML, mais le Gestionnaire d’événements doit être implémenté dans le fichier code-behind. Le [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) montre comment créer une interface utilisateur du clavier dans XAML et comment implémenter les gestionnaires d' `Clicked` dans le fichier code-behind.

## <a name="tap-gestures"></a>Appuyez sur les mouvements

Tout objet `View` peut obtenir une entrée tactile et générer des événements à partir de cette entrée. La classe `View` définit une propriété de collection [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) qui peut contenir une ou plusieurs instances de classes qui dérivent de [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer).

Le [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) génère des événements [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) . Le programme [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) montre comment attacher des objets `TapGestureRecognizer` à quatre éléments `BoxView` pour créer un jeu d’imitation :

[![Capture triple de l’appui du singe](images/ch08fg07-small.png "Jeu d’imitation")](images/ch08fg07-large.png#lightbox "Jeu d’imitation")

Mais le programme **MonkeyTap** a vraiment besoin de son. (Voir [le chapitre suivant](chapter09.md)).

## <a name="related-links"></a>Liens connexes

- [Chapitre 8 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Exemples du chapitre 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Exemple de F# chapitre 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
