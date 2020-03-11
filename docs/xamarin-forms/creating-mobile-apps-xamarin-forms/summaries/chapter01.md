---
title: Résumé du chapitre 1. Comment Xamarin.Forms est-il intégré ?
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 1. Comment Xamarin.Forms est-il intégré ?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73032885"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Résumé du chapitre 1. Comment Xamarin.Forms est-il intégré ?

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Une des tâches plus désagréables de programmation consiste à porter un code de base à partir d’une plateforme à l’autre, en particulier si cette plateforme implique un langage de programmation différents. Il est tentant, lorsque le portage du code pour refactoriser d’également, mais si les deux plateformes doivent être conservées en parallèle, puis les différences entre les deux bases de code seront compliquer une maintenance ultérieure.

## <a name="cross-platform-mobile-development"></a>Développement mobile multiplateforme

Ce problème est courant lors du ciblage de plateformes mobiles. Actuellement, il existe deux principales plateformes mobiles, de la famille de Apple de l’iPhone et iPad exécutant le système d’exploitation iOS et le système d’exploitation Android qui s’exécute sur un large éventail de téléphones et tablettes. Une autre plateforme importante est la plateforme Windows universelle de Microsoft (UWP), qui permet à un programme unique de cibler à la fois Windows 10.

Un éditeur de logiciels qui souhaite cibler ces plateformes doit gérer différents paradigmes de l’interface utilisateur, trois environnements de développement différents, trois interfaces de programmation différentes et&mdash;peut-être le plus difficile&mdash;trois langages de programmation différents : Objective-C pour iPhone et iPad C# , Java pour Android et pour Windows.

## <a name="the-c-and-net-solution"></a>La solution c# et .NET

Bien que Objective-C, Java et c# sont toutes dérivées de langage de programmation C, ils ont évolué en chemins d’accès très différentes. C# est la plus récente de ces langages et a été maturation de façons très utile. En outre, c# est étroitement associé à une infrastructure de programmation entière, appelée .NET, qui prend en charge la mathématiques, le débogage, la réflexion, collections, globalisation, d’e/s de fichier, mise en réseau, sécurité, threading, services web, gestion des données et XML et JSON de lecture et l’écriture.

Xamarin fournit actuellement des outils pour cibler le Mac, iOS et Android API à l’aide de c# et .NET native. Ces outils sont appelés Xamarin.Mac, Xamarin.iOS et Xamarin.Android, collectivement regroupés sous la plateforme Xamarin. Il s’agit des bibliothèques et des liaisons qui expriment les API natives de ces plateformes avec les langages .NET.

Les développeurs peuvent utiliser la plateforme Xamarin pour écrire des applications en c# qui cible Mac, iOS ou Android. Mais si vous ciblez plusieurs plateformes, il est judicieux de partager une partie du code entre les plateformes cibles. Cela implique la séparation du programme en code dépend de la plateforme (généralement impliquant l’interface utilisateur) et de code indépendant de la plateforme, ce qui nécessite généralement uniquement base .NET framework. Ce code indépendant de la plateforme peut résider dans une bibliothèque de classes Portable (PCL), ou un projet partagé, souvent appelé projet de ressource partagé ou SAP.

> [!NOTE]
> Bibliothèques de classes portables ont été remplacés par des bibliothèques .NET Standard. Exemples de code à partir de l’ouvrage a été converti pour utiliser les bibliothèques .NET standard.

## <a name="introducing-xamarinforms"></a>Présentation de Xamarin.Forms

Lorsque vous ciblez plusieurs plateformes mobiles, Xamarin.Forms permet le partage de code encore plus. Un seul programme écrit pour Xamarin. Forms peut cibler ces plateformes :

- iOS pour les programmes qui s’exécutent sur l’iPhone, iPad et iPod touch
- Android pour les programmes qui s’exécutent sur les téléphones et tablettes Android
- plateforme Windows universelle pour cibler Windows 10

> [!NOTE]
> Xamarin.Forms ne gère plus de Windows 8.1, Windows Phone 8.1 ou Windows 10 Mobile, mais les applications Xamarin.Forms s’exécutent sur le bureau Windows 10. Il existe également une prise en charge de l’Aperçu pour les plateformes [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md)et [Tizen](~/xamarin-forms/platform/other/tizen.md) .

La majeure partie d’un programme de Xamarin.Forms existe dans une bibliothèque ou un SAP. Chacune des plateformes se compose d’un stub de petite application qui appelle ce code partagé.

Les APIs Xamarin.Forms mapper à des contrôles natifs sur chaque plateforme, afin que chaque plateforme gère son apparence caractéristique :

[![Capture triple des éléments visuels de la plateforme partage](images/ch01fg03-small.png "Contrôles Xamarin. Forms sur chaque plateforme")](images/ch01fg03-large.png#lightbox "Contrôles Xamarin. Forms sur chaque plateforme")

Les captures d’écran de gauche à droite montrent un iPhone et un téléphone Android :

Sur chaque écran, la page contient un [`Label`](xref:Xamarin.Forms.Label) Xamarin. Forms permettant d’afficher du texte, une [`Button`](xref:Xamarin.Forms.Button) pour initier des actions, une [`Switch`](xref:Xamarin.Forms.Switch) pour le choix d’une valeur d’activation/désactivation, et une [`Slider`](xref:Xamarin.Forms.Slider) pour la spécification d’une valeur dans une plage continue. Les quatre vues sont les enfants d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) sur une [`ContentPage`](xref:Xamarin.Forms.ContentPage).

L’attachement à la page est également une barre d’outils Xamarin. Forms composée de plusieurs objets [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . Ceux-ci sont visibles sous forme d’icônes en haut de l’iOS et Android écrans et en bas de l’écran de Windows 10 Mobile.

Xamarin.Forms prend également en charge XAML, l’Extensible Application Markup Language développés par Microsoft pour plusieurs plateformes d’application. Tous les éléments visuels du programme illustré ci-dessus sont définis en XAML, comme illustré dans l’exemple [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) .

Un programme de Xamarin.Forms peut déterminer quelle plate-forme il s’exécute et exécuter un code différent en conséquence. Plus puissant, les développeurs peuvent écrire du code personnalisé pour les différentes plateformes et exécuter ce code à partir d’un programme de Xamarin.Forms de manière indépendante de la plateforme. Les développeurs peuvent également créer des contrôles supplémentaires en écrivant des convertisseurs pour chaque plateforme.

Xamarin.Forms est une bonne solution pour les applications line of business, ou pour créer des prototypes ou effectuer une démonstration rapide de la preuve de concept, il est moins idéal pour les applications qui nécessitent des graphiques vectoriels ou interaction tactile complexes.

## <a name="your-development-environment"></a>Votre environnement de développement

Votre environnement de développement dépend de ce que vous souhaitez cibler des plateformes et les ordinateurs que vous voulez utiliser.

Si vous souhaitez cibler iOS, vous devez un Mac avec Xcode et la plateforme Xamarin installé. Prenant en charge Android ainsi nécessite l’installation de Java et les kits de développement logiciel requis. Vous pouvez ensuite cibler iOS et Android à l’aide de Visual Studio pour Mac.

Installation de Visual Studio vous permet de sur le PC cibler iOS, Android et toutes les plateformes Windows. Toutefois, cibler iOS à partir de Visual Studio nécessite toujours un Mac avec Xcode et la plateforme Xamarin installé.

Vous pouvez tester les programmes sur un appareil réel que connectés par USB à l’ordinateur ou sur un simulateur.

## <a name="installation"></a>Installation

Avant de créer et de création d’une application Xamarin.Forms, vous devriez créer et créer séparément une application iOS, une application Android et une application UWP, selon les plateformes que vous souhaitez cible et votre environnement de développement.

Les sites web de Xamarin et Microsoft contiennent des informations sur la façon de procéder :

- [Prise en main avec iOS](~/ios/get-started/index.md)
- [Prise en main avec Android](~/android/get-started/index.md)
- [Centre de développement Windows](https://dev.windows.com)

Une fois que vous pouvez créer et n’exécuter des projets pour ces plateformes individuelles, vous devez avoir aucun problème de création et exécution d’une application Xamarin.Forms.

## <a name="related-links"></a>Liens connexes

- [Chapitre 1 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Exemple de chapitre 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
