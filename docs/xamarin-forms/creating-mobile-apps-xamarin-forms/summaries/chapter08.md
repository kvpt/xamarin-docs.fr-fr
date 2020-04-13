---
title: Résumé du chapitre 8. Code et XAML en harmonie
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 8. Code et XAML en harmonie'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334248"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Résumé du chapitre 8. Code et XAML en harmonie

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

Ce chapitre explore XAML plus profondément, et en particulier comment le code et XAML interagissent.

## <a name="passing-arguments"></a>Passage d’arguments

Dans le cas général, une classe instantanée dans XAML doit avoir un constructeur public sans paramètres; l’objet qui en résulte est parasécé par les paramètres de propriété. Cependant, il existe deux autres façons dont les objets peuvent être instantanés et paralés.

Bien qu’il s’œdions de techniques polyvalentes, elles sont principalement utilisées en relation avec les modèles de vue MVVM.

### <a name="constructors-with-arguments"></a>Constructeurs avec des arguments

[**L’échantillon ParamétrielconstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) montre `x:Arguments` comment utiliser l’étiquette pour spécifier les arguments des constructeurs. Ces arguments doivent être délimités par des étiquettes d’élément indiquant le type de l’argument. Pour les types de données .NET de base, les balises suivantes sont disponibles :

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

### <a name="can-i-call-methods-from-xaml"></a>Puis-je appeler les méthodes de XAML?

[**L’échantillon FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) montre comment `x:FactoryMethod` utiliser l’élément pour spécifier une méthode d’usine qui est invoquée pour créer un objet. Une telle méthode d’usine doit être publique et statique, et elle doit créer un objet du type dans lequel elle est définie. (Par exemple, la [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) méthode est admissible parce qu’elle `Color`est publique et statique et renvoie une valeur de type .) Les arguments à la méthode `x:Arguments` de l’usine sont spécifiés dans les balises.

## <a name="the-xname-attribute"></a>L’attribut x:Nom

L’attribut `x:Name` permet de donner un nom à un objet instantané dans XAML. Les règles pour ces noms sont les mêmes que pour les noms variables C. Après le retour `InitializeComponent` de l’appel dans le constructeur, le fichier de code-derrière peut se référer à ces noms pour accéder à l’élément XAML correspondant. Les noms sont en fait convertis par le parser XAML en champs privés dans la classe partielle générée.

[**L’échantillon XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) démontre `x:Name` l’utilisation de pour permettre `Label` au fichier de code-derrière de garder deux éléments définis dans XAML mis à jour avec la date et l’heure actuelles.

Le même nom ne peut pas être utilisé pour plusieurs éléments sur la même page. Il s’agit d’un problème particulier si vous utilisez `OnPlatform` pour créer des objets nommés parallèles pour chaque plate-forme. [**L’échantillon PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) démontre une meilleure façon de faire quelque chose comme ça.

## <a name="custom-xaml-based-views"></a>Vues personnalisées basées sur XAML

Il existe plusieurs façons d’éviter la répétition de la majoration dans XAML. Une technique commune est de créer une nouvelle classe [`ContentView`](xref:Xamarin.Forms.ContentView)basée sur XAML qui dérive de . Cette technique est démontrée dans l’échantillon [**ColorViewList.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) La `ColorView` classe dérive `ContentView` de pour afficher une couleur `ColorViewListPage` particulière et `ContentPage` son nom, tandis que la `ColorView`classe dérive de comme d’habitude et crée explicitement 17 instances de .

L’accès `ColorView` à la classe dans XAML nécessite `local` une autre déclaration XML namespace, communément nommé pour les classes dans la même assemblée.

## <a name="events-and-handlers"></a>Événements et gestionnaires

Les événements peuvent être attribués aux gestionnaires d’événements dans XAML, mais le gestionnaire d’événements lui-même doit être implémenté dans le fichier de code.derrière. Le [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) montre comment construire une interface utilisateur keypad dans `Clicked` XAML et comment implémenter les gestionnaires dans le fichier de code-derrière.

## <a name="tap-gestures"></a>Gestes de robinet

N’importe quel `View` objet peut obtenir l’entrée tactile et générer des événements à partir de cette entrée. La `View` classe définit [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) une propriété de collecte qui peut contenir [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer)un ou plusieurs cas de classes qui dérivent de .

Le [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) génère [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) des événements. Le programme [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) montre `TapGestureRecognizer` comment attacher `BoxView` des objets à quatre éléments pour créer un jeu d’imitation :

[![Triple capture d’écran du robinet de singe](images/ch08fg07-small.png "Jeu d’imitation")](images/ch08fg07-large.png#lightbox "Jeu d’imitation")

Mais le programme **MonkeyTap** a vraiment besoin de son. (Voir [le chapitre suivant](chapter09.md).)

## <a name="related-links"></a>Liens connexes

- [Chapitre 8 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Échantillons du chapitre 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Échantillon du chapitre 8 F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Arguments de passage dans XAML](~/xamarin-forms/xaml/passing-arguments.md)
