---
title: Résumé du chapitre 1. Comment Xamarin.Forms est-il intégré ?
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 1. Comment Xamarin.Forms est-il intégré ?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032885"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Résumé du chapitre 1. Comment Xamarin.Forms est-il intégré ?

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

L’un des emplois les plus désagréables dans la programmation est le portage d’une base de code d’une plate-forme à l’autre, en particulier si cette plate-forme implique un langage de programmation différent. Il y a une tentation lors du portage du code de le refactoriser ainsi, mais si les deux plates-formes doivent être maintenues en parallèle, alors les différences entre les deux bases de code rendront la maintenance future plus difficile.

## <a name="cross-platform-mobile-development"></a>Développement mobile multiplateforme

Ce problème est courant lorsque vous ciblez les plates-formes mobiles. Actuellement, il existe deux plates-formes mobiles majeures, la famille Apple d’iPhones et iPads exécutant le système d’exploitation iOS, et le système d’exploitation Android qui fonctionne sur une variété de téléphones et de tablettes. Une autre plate-forme importante est la plate-forme Windows universelle (UWP) de Microsoft, qui permet à un seul programme de cibler les deux Windows 10.

Un fournisseur de logiciels qui souhaite cibler ces plates-formes doit faire face à différents paradigmes d’interface utilisateur, trois environnements de développement différents, trois interfaces de programmation différentes, et&mdash;peut-être le plus maladroitement&mdash;trois langages de programmation différents: Objectif-C pour l’iPhone et iPad, Java pour Android, et C pour Windows.

## <a name="the-c-and-net-solution"></a>La solution C et .NET

Bien qu’Objectif-C, Java et CMD soient tous dérivés du langage de programmation C, ils ont évolué par des chemins très différents. C est la plus récente de ces langues et a mûri de manière très utile. En outre, C est étroitement associé à toute une infrastructure de programmation appelée .NET, qui fournit un soutien pour les mathématiques, débogage, réflexion, collections, mondialisation, fichier I / O, le réseautage, la sécurité, filetage, services Web, la manipulation des données, et XML et JSON lecture et rédaction.

Xamarin fournit actuellement des outils pour cibler les API mac, iOS et Android natifs à l’aide de C et .NET. Ces outils sont appelés Xamarin.Mac, Xamarin.iOS, et Xamarin.Android, collectivement connu sous le nom de la plate-forme Xamarin. Ce sont des bibliothèques et des reliures qui expriment les API indigènes de ces plates-formes avec des idiomes .NET.

Les développeurs peuvent utiliser la plate-forme Xamarin pour écrire des applications dans C ' qui ciblent Mac, iOS, ou Android. Mais lorsque vous ciblez plus d’une plate-forme, il est très logique de partager une partie du code entre les plates-formes cibles. Il s’agit de séparer le programme en code dépendant de la plate-forme (généralement impliquant l’interface utilisateur), et le code indépendant de la plate-forme, qui ne nécessite généralement que le cadre base .NET. Ce code indépendant de la plate-forme peut résider soit dans une bibliothèque de classe portable (PCL), soit dans un projet partagé, souvent appelé un projet d’actif partagé ou SAP.

> [!NOTE]
> Les bibliothèques de classe portatives ont été remplacées par des bibliothèques standard .NET. Tout le code de l’échantillon du livre a été converti pour utiliser des bibliothèques standard .NET.

## <a name="introducing-xamarinforms"></a>Présentation de Xamarin.Forms

Lors du ciblage de plusieurs plates-formes mobiles, Xamarin.Forms permet encore plus de partage de code. Un seul programme écrit pour Xamarin.Forms peut cibler ces plates-formes :

- iOS pour les programmes qui s’exécutent sur l’iPhone, iPad et iPod touch
- Android pour les programmes qui fonctionnent sur les téléphones et tablettes Android
- la plate-forme Windows universelle pour cibler Windows 10

> [!NOTE]
> Xamarin.Forms ne prend plus en charge Windows 8.1, Windows Phone 8.1 ou Windows 10 Mobile, mais les applications Xamarin.Forms s’exécutent sur le bureau Windows 10. Il y a également un aperçu des plates-formes [Mac,](~/xamarin-forms/platform/other/mac.md) [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK et](~/xamarin-forms/platform/other/gtk.md) [Tizen.](~/xamarin-forms/platform/other/tizen.md)

La majeure partie d’un programme Xamarin.Forms existe dans une bibliothèque ou un SAP. Chacune des plates-formes se compose d’un petit talon d’application qui appelle dans ce code partagé.

La carte des API Xamarin.Forms aux commandes natives sur chaque plate-forme, de sorte que chaque plate-forme conserve son apparence et sa sensation caractéristiques :

[![Triple capture d’écran du partage de visuels de plate-forme](images/ch01fg03-small.png "Xamarin.Forms Contrôles sur chaque plate-forme")](images/ch01fg03-large.png#lightbox "Xamarin.Forms Contrôles sur chaque plate-forme")

Les captures d’écran de gauche à droite montrent un iPhone et un téléphone Android:

Sur chaque écran, la page contient [`Label`](xref:Xamarin.Forms.Label) un Xamarin.Forms pour afficher du texte, un [`Button`](xref:Xamarin.Forms.Button) pour initier des actions, un [`Switch`](xref:Xamarin.Forms.Switch) pour choisir une valeur on/off, et un [`Slider`](xref:Xamarin.Forms.Slider) pour spécifier une valeur dans une plage continue. Tous les quatre de ces [`StackLayout`](xref:Xamarin.Forms.StackLayout) points [`ContentPage`](xref:Xamarin.Forms.ContentPage)de vue sont des enfants d’un sur un .

Une barre d’outils Xamarin.Forms composée de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) plusieurs objets est également jointe à la page. Ceux-ci sont visibles comme des icônes sur le dessus des écrans iOS et Android, et sur le bas de l’écran Windows 10 Mobile.

Xamarin.Forms prend également en charge XAML, l’Extensible Application Markup Language développé chez Microsoft pour plusieurs plates-formes d’applications. Tous les visuels du programme ci-dessus sont définis dans XAML comme le démontre l’échantillon [**PlatformVisuals.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)

Un programme Xamarin.Forms peut déterminer la plate-forme sur lequel il fonctionne et exécuter différents codes en conséquence. Plus puissamment, les développeurs peuvent écrire du code personnalisé pour les différentes plates-formes et exécuter ce code à partir d’un programme Xamarin.Forms d’une manière indépendante de la plate-forme. Les développeurs peuvent également créer des contrôles supplémentaires en écrivant des rendus pour chaque plate-forme.

Bien que Xamarin.Forms soit une bonne solution pour les applications de ligne d’entreprise, ou pour le prototypage, ou une démonstration rapide de preuve de concept, il est moins idéal pour les applications qui nécessitent des graphiques vectoriels ou une interaction tactile complexe.

## <a name="your-development-environment"></a>Votre environnement de développement

Votre environnement de développement dépend des plates-formes que vous souhaitez cibler et des machines que vous souhaitez utiliser.

Si vous souhaitez cibler iOS, vous aurez besoin d’un Mac avec Xcode et la plate-forme Xamarin installée. Soutenir Android aussi bien nécessite l’installation de Java et les SDK requis. Vous pouvez ensuite cibler iOS et Android en utilisant Visual Studio pour Mac.

L’installation de Visual Studio permet sur le PC que vous ciblez iOS, Android et toutes les plates-formes Windows. Cependant, le ciblage iOS de Visual Studio nécessite toujours un Mac avec Xcode et la plate-forme Xamarin installée.

Vous pouvez tester des programmes sur un appareil réel connecté par USB à l’ordinateur, soit sur un simulateur.

## <a name="installation"></a>Installation

Avant de créer et de construire une application Xamarin.Forms, vous devriez essayer de créer et de construire séparément une application iOS, une application Android et une application UWP, en fonction des plates-formes que vous souhaitez cibler et de votre environnement de développement.

Les sites Web Xamarin et Microsoft contiennent des informations sur la façon de le faire:

- [Bien démarrer avec iOS](~/ios/get-started/index.md)
- [Bien démarrer avec Android](~/android/get-started/index.md)
- [Centre de développement Windows](https://dev.windows.com)

Une fois que vous pouvez créer et exécuter des projets pour ces plates-formes individuelles, vous ne devriez avoir aucun problème à créer et à exécuter une application Xamarin.Forms.

## <a name="related-links"></a>Liens connexes

- [Texte intégral du chapitre 1 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Échantillon du chapitre 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
