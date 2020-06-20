---
title: Résumé du chapitre 1. Comment s’intègre-t- Xamarin.Forms il ?
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 1. Comment s’intègre-t- Xamarin.Forms il ?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 48b2fb429d206f6582886c94d4d99839d790dc8d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136925"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Résumé du chapitre 1. Comment s’intègre-t- Xamarin.Forms il ?

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

L’une des tâches les plus déplaisantes en programmation est le portage d’une base de code d’une plateforme vers une autre, en particulier si cette plateforme implique un langage de programmation différent. Il y a une tentation pour le portage du code pour le refactoriser également, mais si les deux plateformes doivent être maintenues en parallèle, les différences entre les deux bases de code rendent la maintenance future plus difficile.

## <a name="cross-platform-mobile-development"></a>Développement mobile multiplateforme

Ce problème est courant lors du ciblage de plateformes mobiles. Actuellement, il existe deux principales plateformes mobiles : la famille Apple de iPhone et iPad exécutant le système d’exploitation iOS et le système d’exploitation Android qui s’exécute sur divers téléphones et tablettes. Une autre plateforme importante est la plateforme Windows universelle de Microsoft (UWP), qui permet à un programme unique de cibler à la fois Windows 10.

Un éditeur de logiciels qui souhaite cibler ces plates-formes doit gérer différents paradigmes d’interface utilisateur, trois environnements de développement différents, trois interfaces de programmation différentes et, &mdash; peut-être, &mdash; les trois langages de programmation les plus délicats : Objective-C pour iPhone et iPad, Java pour Android et C# pour Windows.

## <a name="the-c-and-net-solution"></a>La solution C# et .NET

Bien que objective-C, Java et C# soient tous dérivés du langage de programmation C, ils ont évolué par des chemins très différents. C# est le plus récent de ces langages et s’est terminé de manière très utile. En outre, C# est étroitement associé à une infrastructure de programmation complète nommée .NET, qui prend en charge les opérations mathématiques, le débogage, la réflexion, les collections, la globalisation, les e/s de fichier, la mise en réseau, la sécurité, les threads, les services Web, la gestion des données et la lecture et l’écriture XML et JSON.

Xamarin fournit actuellement des outils pour cibler les API Mac, iOS et Android natives à l’aide de C# et de .NET. Ces outils sont appelés Xamarin. Mac, Xamarin. iOS et Xamarin. Android, collectivement appelés plateforme Xamarin. Il s’agit de bibliothèques et de liaisons qui expriment les API natives de ces plateformes avec les idiomes .NET.

Les développeurs peuvent utiliser la plateforme Xamarin pour écrire des applications en C# qui ciblent Mac, iOS ou Android. Toutefois, lorsque vous ciblez plusieurs plateformes, il est important de partager du code entre les plateformes cibles. Cela implique de séparer le programme en code dépendant de la plateforme (généralement impliquant l’interface utilisateur) et du code indépendant de la plateforme, qui requiert généralement uniquement le .NET Framework de base. Ce code indépendant de la plateforme peut résider dans une bibliothèque de classes portables (PCL) ou un projet partagé, souvent appelé projet de ressource partagé ou SAP.

> [!NOTE]
> Les bibliothèques de classes portables ont été remplacées par des bibliothèques de .NET Standard. Tout l’exemple de code du livre a été converti pour utiliser des bibliothèques .NET standard.

## <a name="introducing-xamarinforms"></a>À découvrirXamarin.Forms

Lorsque vous ciblez plusieurs plateformes mobiles, Xamarin.Forms offre encore plus de partage de code. Un seul programme écrit pour Xamarin.Forms peut cibler ces plateformes :

- iOS pour les programmes qui s’exécutent sur l’iPhone, l’iPad et l’iPod Touch
- Android pour les programmes qui s’exécutent sur des téléphones et tablettes Android
- plateforme Windows universelle pour cibler Windows 10

> [!NOTE]
> Xamarin.Formsne prend plus en charge Windows 8.1, Windows Phone 8,1 ou Windows 10 mobile, mais Xamarin.Forms les applications s’exécutent sur le bureau Windows 10. Il existe également une prise en charge de l’Aperçu pour les plateformes [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md)et [Tizen](~/xamarin-forms/platform/other/tizen.md) .

L’essentiel d’un Xamarin.Forms programme existe dans une bibliothèque ou un SAP. Chacune des plateformes se compose d’un petit stub d’application qui appelle ce code partagé.

Les Xamarin.Forms API sont mappées à des contrôles natifs sur chaque plateforme, de sorte que chaque plateforme conserve son apparence caractéristique :

[![Capture triple des éléments visuels de la plateforme partage](images/ch01fg03-small.png "[! Opérationnel. Contrôles NO-LOC (Xamarin. Forms)] sur chaque plateforme")](images/ch01fg03-large.png#lightbox "[! Opérationnel. Contrôles NO-LOC (Xamarin. Forms)] sur chaque plateforme")

Les captures d’écran de gauche à droite montrent un iPhone et un téléphone Android :

Sur chaque écran, la page contient un Xamarin.Forms [`Label`](xref:Xamarin.Forms.Label) pour l’affichage de texte, un [`Button`](xref:Xamarin.Forms.Button) pour les actions d’initiation, un [`Switch`](xref:Xamarin.Forms.Switch) pour le choix d’une valeur d’activation/désactivation et un [`Slider`](xref:Xamarin.Forms.Slider) pour la spécification d’une valeur dans une plage continue. Les quatre vues sont les enfants d’un [`StackLayout`](xref:Xamarin.Forms.StackLayout) sur un [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

L’attachement à la page est également une Xamarin.Forms barre d’outils composée de plusieurs [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objets. Celles-ci sont visibles sous forme d’icônes en haut des écrans iOS et Android, et en bas de l’écran Windows 10 mobile.

Xamarin.Formsprend également en charge XAML, le Extensible Application Markup Language développé chez Microsoft pour plusieurs plateformes d’application. Tous les éléments visuels du programme illustré ci-dessus sont définis en XAML, comme illustré dans l’exemple [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) .

Un Xamarin.Forms programme peut déterminer la plateforme sur laquelle il s’exécute et exécuter un code différent en conséquence. Plus puissant, les développeurs peuvent écrire du code personnalisé pour les différentes plateformes et exécuter ce code à partir d’un Xamarin.Forms programme de manière indépendante de la plateforme. Les développeurs peuvent également créer des contrôles supplémentaires en écrivant des convertisseurs pour chaque plateforme.

Bien que Xamarin.Forms soit une bonne solution pour les applications métier, ou pour le prototypage, ou pour effectuer une démonstration de preuve de concept, elle est moins idéale pour les applications qui nécessitent des graphiques vectoriels ou une interaction tactile complexe.

## <a name="your-development-environment"></a>Votre environnement de développement

Votre environnement de développement dépend des plateformes que vous souhaitez cibler et des ordinateurs que vous souhaitez utiliser.

Si vous souhaitez cibler iOS, vous aurez besoin d’un Mac avec Xcode et la plateforme Xamarin installée. La prise en charge d’Android nécessite également l’installation de Java et des kits de développement logiciel (SDK) requis. Vous pouvez ensuite cibler à la fois iOS et Android à l’aide de Visual Studio pour Mac.

L’installation de Visual Studio permet sur le PC que vous souhaitez cibler iOS, Android et toutes les plateformes Windows. Toutefois, le ciblage d’iOS à partir de Visual Studio nécessite toujours un Mac avec Xcode et la plateforme Xamarin installée.

Vous pouvez tester des programmes sur un appareil réel connecté par USB à l’ordinateur ou sur un simulateur.

## <a name="installation"></a>Installation

Avant de créer et de générer une Xamarin.Forms application, vous devez essayer de créer et de générer séparément une application iOS, une application Android et une application UWP, selon les plateformes que vous souhaitez cibler et votre environnement de développement.

Les sites Web Xamarin et Microsoft contiennent des informations sur la façon de procéder :

- [Bien démarrer avec iOS](~/ios/get-started/index.md)
- [Bien démarrer avec Android](~/android/get-started/index.md)
- [Centre de développement Windows](https://dev.windows.com)

Une fois que vous pouvez créer et exécuter des projets pour ces plateformes individuelles, vous ne devriez pas avoir de problème pour créer et exécuter une Xamarin.Forms application.

## <a name="related-links"></a>Liens connexes

- [Chapitre 1 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Exemple de chapitre 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
