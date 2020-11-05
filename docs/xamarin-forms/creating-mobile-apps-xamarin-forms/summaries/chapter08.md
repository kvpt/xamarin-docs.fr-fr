---
title: Résumé du chapitre 8. Code et XAML en harmonie
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 8. Code et XAML en harmonie'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a88f0ef4e04bf6884f1b49811663f611d3e54a0a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366215"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Résumé du chapitre 8. Code et XAML en harmonie

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Ce chapitre explore le XAML plus en profondeur, et en particulier la manière dont le code et XAML interagissent.

## <a name="passing-arguments"></a>Passage d’arguments

Dans le cas général, une classe instanciée en XAML doit avoir un constructeur sans paramètre public ; l’objet résultant est initialisé par le biais des paramètres de propriété. Toutefois, il existe deux autres façons dont les objets peuvent être instanciés et initialisés.

Bien qu’il s’agisse de techniques à usage général, elles sont principalement utilisées en relation avec les modèles de vue MVVM.

### <a name="constructors-with-arguments"></a>Constructeurs avec arguments

L’exemple [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) montre comment utiliser la `x:Arguments` balise pour spécifier des arguments de constructeur. Ces arguments doivent être délimités par des balises d’élément indiquant le type de l’argument. Pour les types de données .NET de base, les balises suivantes sont disponibles :

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

L’exemple [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) montre comment utiliser l' `x:FactoryMethod` élément pour spécifier une méthode de fabrique appelée pour créer un objet. Une telle méthode de fabrique doit être publique et statique, et doit créer un objet du type dans lequel elle est définie. (Par exemple [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) , la méthode qualifie, car elle est publique et statique et retourne une valeur de type `Color` .) Les arguments de la méthode de fabrique sont spécifiés dans les `x:Arguments` balises.

## <a name="the-xname-attribute"></a>Attribut x :Name

L' `x:Name` attribut permet de donner un nom à un objet instancié en XAML. Les règles de ces noms sont les mêmes que pour les noms de variables C#. Après le retour de l' `InitializeComponent` appel dans le constructeur, le fichier code-behind peut faire référence à ces noms pour accéder à l’élément XAML correspondant. Les noms sont en fait convertis par l’analyseur XAML en champs privés dans la classe partielle générée.

L’exemple [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) illustre l’utilisation de `x:Name` pour permettre au fichier code-behind de conserver deux `Label` éléments définis dans XAML mis à jour avec la date et l’heure actuelles.

Le même nom ne peut pas être utilisé pour plusieurs éléments sur la même page. Il s’agit d’un problème particulier si vous utilisez `OnPlatform` pour créer des objets nommés parallèles pour chaque plateforme. L’exemple [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) illustre une meilleure manière d’effectuer une opération similaire.

## <a name="custom-xaml-based-views"></a>Vues personnalisées basées sur XAML

Il existe plusieurs façons d’éviter la répétition du balisage en XAML. Une technique courante consiste à créer une nouvelle classe XAML qui dérive de [`ContentView`](xref:Xamarin.Forms.ContentView) . Cette technique est illustrée dans l’exemple [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) . La `ColorView` classe dérive de `ContentView` pour afficher une couleur et son nom particuliers, tandis que la `ColorViewListPage` classe dérive de `ContentPage` comme d’habitude et crée explicitement 17 instances de `ColorView` .

L’accès à la `ColorView` classe dans XAML requiert une autre déclaration d’espace de noms XML, communément nommée `local` pour les classes dans le même assembly.

## <a name="events-and-handlers"></a>Événements et gestionnaires

Les événements peuvent être assignés à des gestionnaires d’événements en XAML, mais le gestionnaire d’événements lui-même doit être implémenté dans le fichier code-behind. Le [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) montre comment créer une interface utilisateur du clavier dans XAML et comment implémenter les `Clicked` gestionnaires dans le fichier code-behind.

## <a name="tap-gestures"></a>Gestes TAP

Tout `View` objet peut obtenir une entrée tactile et générer des événements à partir de cette entrée. La `View` classe définit une [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) propriété de collection qui peut contenir une ou plusieurs instances de classes qui dérivent de [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) .

[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)Génère des [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) événements. Le programme [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) montre comment attacher des `TapGestureRecognizer` objets à quatre `BoxView` éléments pour créer un jeu d’imitation :

[![Capture triple de l’appui du singe](images/ch08fg07-small.png "Jeu d’imitation")](images/ch08fg07-large.png#lightbox "Jeu d’imitation")

Mais le programme **MonkeyTap** a vraiment besoin de son. (Voir [le chapitre suivant](chapter09.md)).

## <a name="related-links"></a>Liens connexes

- [Chapitre 8 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Exemples du chapitre 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Exemple de chapitre 8 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
