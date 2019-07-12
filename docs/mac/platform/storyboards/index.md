---
title: Introduction aux Storyboards dans Xamarin.Mac
description: Cet article fournit une introduction à l’utilisation avec des Storyboards dans une application Xamarin.Mac. Il aborde la création et la gestion de l’interface utilisateur de l’application à l’aide de storyboards et d’Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8a5a2f87c16a5dd040cefb2fbc615b01431ebcf5
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832289"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introduction aux Storyboards dans Xamarin.Mac

_Cet article fournit une introduction à l’utilisation avec des Storyboards dans une application Xamarin.Mac. Il couvre la création et la gestion de l’interface utilisateur de l’application à l’aide de storyboards et Interface Builder de Xcode._

Storyboards permettent de développer une Interface utilisateur pour votre application Xamarin.Mac qui inclut non seulement les définitions de fenêtre et les contrôles, mais contient également les liens entre des fenêtres différentes (par le biais de segues) et des États d’affichage.

[![](images/intro01.png "Un exemple d’interface utilisateur dans Xcode")](images/intro01.png#lightbox)

Cet article fournit une introduction à l’aide de Storyboards pour définir l’Interface d’utilisateur d’une application Xamarin.Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>Quelles sont les tables de montage séquentiel ?

À l’aide de Storyboards, toutes de l’interface utilisateur d’une application Xamarin.Mac peuvent être définies dans un emplacement unique avec la navigation entre ses éléments individuels et les interfaces utilisateur. Tables de montage séquentiel pour Xamarin.Mac, fonctionnent de manière très similaire aux Storyboards pour Xamarin.iOS. Toutefois, ils contiennent un ensemble différent de _Types Segue_ en raison des idiomes d’interface différente.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Utilisation des séquences

Comme indiqué ci-dessus, une table de montage définit l’ensemble de l’interface utilisateur pour une application donnée est divisée en une vue d’ensemble fonctionnelle de son _contrôleurs d’affichage_. Dans l’Interface Builder de Xcode, chacun de ces contrôleurs se trouve dans son propre _scène_.

[![](images/intro02.png "Un exemple de contrôleur d’affichage")](images/intro02.png#lightbox)

Chaque scène représente une vue et la paire de contrôleur de vue donné avec un ensemble de lignes (appelée Segues) qui se connectent chaque scène dans l’interface utilisateur, montrant ainsi leurs relations. Certains Segues définir comment un contrôleur d’affichage contient une ou plusieurs vues enfants ou les contrôleurs d’affichage. Autres Segues, définir des transitions entre le contrôleur d’affichage (par exemple, l’affichage d’une boîte de dialogue ou un menu). 

[![](images/intro03.png "Un segue exemple")](images/intro03.png#lightbox)

Le plus important à noter est que chaque Segue représente le flux de certains types de données entre l’élément donné de l’interface utilisateur de l’application.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Utilisation des contrôleurs d’affichage

Contrôleurs d’affichage définissent les relations entre une vue donnée d’informations dans une application Mac et le modèle de données qui fournit cette information. Chaque scène de niveau supérieur dans le Storyboard représente un contrôleur d’affichage dans le code de l’application Xamarin.Mac.

[![](images/intro04.png "Un exemple des bons de contrôleur d’affichage")](images/intro04.png#lightbox)

De cette façon, chaque contrôleur d’affichage est un couplage autonome et réutilisable de représentation visuelle de l’information (vue) et la logique de présentation et de contrôler ces informations.

Dans une scène donnée, vous pouvez effectuer tous les aspects qui aurait normalement été traitée par individu `.xib` fichiers : 

- Place subviews et des contrôles (par exemple, des boutons et des zones de texte).
- Définir les positions de l’élément et les contraintes de disposition automatique.
- AutoEventWireup Actions et Outlets pour exposer les éléments d’interface utilisateur au code.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Utilisation de Segues

Comme indiqué ci-dessus, Segues fournissent les relations entre tous les scènes qui définissent l’interface utilisateur de votre application. Si vous êtes familiarisé avec l’utilisation dans des Storyboards dans iOS, vous savez qu’Enchaînements pour iOS définissent généralement des transitions entre les vues de plein écran. Cela diffère de macOS, lorsque Segues définissez généralement « imbrication » (où une scène est l’enfant d’un parent scène).

Dans macOS, la plupart des applications ont tendance à grouper leurs vues ensemble au sein de la même fenêtre à l’aide des éléments d’interface utilisateur tels que les onglets et les affichages fractionnés. Contrairement à iOS, où les vues doivent être transférées sur et hors écran, en raison de la limite physique affiche l’espace.

Étant donné les tendances de macOS vers la relation contenant-contenu, il existe des situations où _Segues de présentation_ sont utilisés, tels que Windows modale, vues de la feuille et Popovers.

Lorsque vous utilisez Segues de présentation, vous pouvez remplacer le `PrepareForSegue` méthode du contrôleur d’affichage parent pour les variables et de présentation pour initialiser et fournir toutes les données pour le contrôleur d’affichage en cours de présentation.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Conception et les temps d’exécution

Au moment du Design (lors de la disposition de l’interface utilisateur dans l’Interface Builder de Xcode), chaque élément d’interface utilisateur de l’application est composée de ses éléments constitutifs :

- **Arrière-plan** , sont composés de :
    - **Afficher le contrôleur** -qui définissent les relations entre les vues et les données qui les prennent en charge.
    - **Affichages et sous-affichages** -les éléments réels qui composent l’interface utilisateur.
    - **Relation contenant-contenu Segues** -qui définissent les relations parent-enfant entre les scènes.
- **Segues de présentation** -qui définissent des modes de présentation individuels. 

En définissant chaque élément de cette façon, il permet le chargement différé de chaque élément uniquement qu’il est nécessaire pendant l’exécution. Dans macOS, l’ensemble du processus a été conçu pour permettre au développeur de créer des Interfaces utilisateur complexes et flexible qui nécessitent un minimum de code pour qu’elles fonctionnent, de stockage tout en étant aussi efficace avec les ressources système que possible.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Démarrage rapide de table de montage séquentiel

Dans le [démarrage rapide de table de montage séquentiel](~/mac/platform/storyboards/quickstart.md) guide, nous allons créer une application Xamarin.Mac simple qui présente les concepts clés de l’utilisation de tables de montage séquentiel pour créer une Interface utilisateur. L’exemple d’application se compose d’une vue Spilt contenant un _zone de contenu_ et un _inspecteur zone_ et présente une fenêtre de boîte de dialogue Préférences simple. Nous allons utiliser Segues pour relier tous les éléments d’Interface utilisateur.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Utilisation de storyboards

Cette section couvre les données détaillées de [fonctionne avec des Storyboards](~/mac/platform/storyboards/indepth.md) dans une application Xamarin.Mac. Nous prenons un examen approfondi de l’arrière-plan et comment elles sont composées de contrôleurs d’affichage et de la vue. Ensuite, nous allons examiner comment les scènes sont liées par Segues. Enfin, nous allons utiliser les types de Segue personnalisés. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemple de table de montage séquentiel complexes

Pour obtenir un exemple d’un exemple complex de travailler avec des Storyboards dans une application Xamarin.Mac, veuillez consulter la [exemple d’application SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter est un éditeur de code source simple qui assure la prise en charge de la complétion de code et de la coloration syntaxique de base.

Le code SourceWriter a été entièrement commenté et, le cas échéant, des liens ont être fournis entre les principales technologies ou méthodes et des informations pertinentes dans la documentation des guides Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a examiné en rapide à l’utilisation avec des Storyboards dans une application Xamarin.Mac. Nous avons vu comment créer une application à l’aide de storyboards et comment définir une interface utilisateur. Nous avons également vu comment naviguer entre les différentes fenêtres et états d’affichage à l’aide de segues.


## <a name="related-links"></a>Liens associés

- [Hello, Mac (exemple)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduction à Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
