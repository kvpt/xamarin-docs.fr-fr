---
title: Hello, iOS – Exploration approfondie
description: Ce document examine l’exemple d’application Hello, iOS de manière approfondie, notamment son architecture, son interface utilisateur, sa hiérarchie d’affichage du contenu, les tests et le déploiement.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 61ba3a7e-fe11-4439-8bc8-9809512b8eff
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 4b9043d70bb7460abf62c964da8041f345cd1be6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435609"
---
# <a name="hello-ios--deep-dive"></a>Hello, iOS – Exploration approfondie

La procédure de démarrage rapide a montré comment créer et exécuter une application Xamarin.iOS de base. Maintenant, vous allez approfondir votre connaissance du fonctionnement des applications iOS, en vue de créer des programmes plus complexes. Ce guide passe en revue les étapes décrites dans la procédure pas à pas Hello, iOS pour vous permettre de mieux comprendre les concepts fondamentaux du développement d’applications iOS.

Ce guide a pour but de vous aider à développer les compétences et les connaissances nécessaires à la génération d’une application iOS à écran unique. À la fin de ce guide, vous connaîtrez les différents composants d’une application Xamarin.iOS et comprendrez la logique de leur organisation.

::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduction à Visual Studio pour Mac

Visual Studio pour Mac est un environnement de développement intégré (IDE) gratuit et open source qui combine les fonctionnalités de Visual Studio et XCode. Il comprend un concepteur visuel entièrement intégré, un éditeur de texte avec outils de refactorisation, un explorateur d’assembly, l’intégration du code source, et bien plus encore. Ce guide présente certaines fonctionnalités essentielles de Visual Studio pour Mac, mais si vous ne connaissez pas cet outil, consultez la documentation de [Visual Studio pour Mac](/visualstudio/mac/).

Visual Studio pour Mac suit la pratique Visual Studio qui consiste à organiser le code en *solutions* et *projets*. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application (par exemple, iOS ou Android), une bibliothèque de prise en charge, une application de test, etc. Dans l’application Phoneword, un nouveau projet iPhone a été ajouté à l’aide du modèle **application avec affichage unique** . La solution initiale se présentait ainsi :

![Capture d’écran de la solution initiale](hello-ios-deepdive-images/image30.png)

::: zone-end
::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduction à Visual Studio

Visual Studio est un environnement IDE puissant, développé par Microsoft. Il comprend un concepteur visuel entièrement intégré, un éditeur de texte avec outils de refactorisation, un explorateur d’assembly, l’intégration du code source, et bien plus encore. Ce guide présente certaines fonctionnalités de base de Visual Studio avec Xamarin Tools pour Visual Studio.

Visual Studio organise le code en solutions et en projets. Une solution est un conteneur qui peut comprendre un ou plusieurs projets. Un projet peut être une application (par exemple, iOS ou Android), une bibliothèque de prise en charge, une application de test, etc. Dans l’application Phoneword, un nouveau projet iPhone a été ajouté à l’aide du modèle **application avec affichage unique** . La solution initiale se présentait ainsi :

![Capture d’écran de la solution initiale](hello-ios-deepdive-images/vs-image30.png)

::: zone-end

## <a name="anatomy-of-a-xamarinios-application"></a>Anatomie d’une application Xamarin.iOS

::: zone pivot="macos"

À gauche se trouve le panneau **solution**, qui contient la structure de répertoires et tous les fichiers associés à la solution :

![Panneau Solutions, qui contient la structure de répertoires et tous les fichiers associés à la solution](hello-ios-deepdive-images/image31.png)

::: zone-end
::: zone pivot="windows"

À droite se trouve le **volet solution**, qui contient la structure de répertoires et tous les fichiers associés à la solution :

![Volet Solutions, qui contient la structure de répertoires et tous les fichiers associés à la solution](hello-ios-deepdive-images/vs-image31.png)

::: zone-end

Dans la procédure pas à pas [Hello, iOS](~/ios/get-started/hello-ios/hello-ios-quickstart.md) , vous avez créé une solution appelée **Phoneword** et placé un projet iOS **Phoneword_iOS** à l’intérieur de celle-ci. Les éléments inclus dans le projet sont les suivants :

- **Références** : contient les assemblys requis pour générer et exécuter l’application. Développez le répertoire pour voir les références aux assemblys .NET tels que [System](/dotnet/api/system), System.Core et [System.Xml](/dotnet/api/system.xml), ainsi qu’une référence à l’assembly Xamarin.iOS.
- **Packages** : le répertoire packages contient des packages NuGet prêts à l’emploi.
- **Ressources** : le dossier ressources stocke d’autres médias.
- **Main.cs** : Ce fichier contient le principal point d’entrée de l’application. Pour démarrer l’application, le nom de la classe d’application principale, `AppDelegate`, est passé.
- **AppDelegate.cs** : Ce fichier contient la classe d’application principale et sert à créer la fenêtre, générer l’interface utilisateur et écouter les événements du système d’exploitation.
- **Main.Storyboard** : le Storyboard contient la conception visuelle de l’interface utilisateur de l’application. Les fichiers de Storyboard s’ouvrent dans un éditeur graphique appelé concepteur iOS.
- **ViewController.cs** : le contrôleur d’affichage alimente l’écran (vue) qu’un utilisateur voit et touche. Il est responsable de la gestion des interactions entre l’utilisateur et la vue.
- **ViewController.Designer.cs** : `designer.cs` est un fichier généré automatiquement qui sert de liant entre les contrôles de la vue et leurs représentations de code dans le contrôleur d’affichage. Comme il s’agit d’un fichier interne, l’IDE écrase toutes les modifications manuelles et la plupart du temps, ce fichier peut être ignoré. Pour plus d’informations sur la relation entre le concepteur visuel et le code de stockage, reportez-vous au guide de [présentation du concepteur iOS](~/ios/user-interface/designer/introduction.md).
- **Info.plist** : le fichier **Info.plist** définit les propriétés d’application comme le nom de l’application, ses icônes, ses images de lancement, etc. Il s’agit d’un fichier puissant et une présentation détaillée est disponible dans le guide [utilisation des listes de propriétés](~/ios/app-fundamentals/property-lists.md) .
- **Habilitations. plist** : la liste de propriétés des droits permet de spécifier des *fonctionnalités* d’application (également appelées technologies App Store) telles que iCloud, PassKit et bien plus encore. Pour plus d’informations sur **Entitlements.plist**, consultez le guide d’[utilisation des listes de propriétés](~/ios/app-fundamentals/property-lists.md). Pour obtenir une présentation générale des droits, reportez-vous au guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md).

## <a name="architecture-and-app-fundamentals"></a>Concepts fondamentaux de l’architecture et des applications

Pour qu’une application iOS puisse charger une interface utilisateur, deux éléments doivent préalablement être en place. Tout d’abord, l’application doit définir un *point d’entrée* : le premier code qui s’exécute lorsque le processus de l’application est chargé en mémoire. Ensuite, elle doit définir une classe pour gérer les événements à l’échelle de l’application et interagir avec le système d’exploitation.

Cette section étudie les relations illustrées dans le diagramme suivant :

[![Les relations de base de l’architecture et de l’application sont illustrées dans ce diagramme](hello-ios-deepdive-images/image32.png)](hello-ios-deepdive-images/image32.png#lightbox)

### <a name="main-method"></a>Méthode principale

Le point d’entrée principal d’une application iOS est la classe `Application`. La classe `Application` est définie dans le fichier **Main.cs** et contient une méthode `Main` statique. Il crée une instance d’application Xamarin.iOS et passe le nom de la classe de *délégué d’application* qui gère les événements du système d’exploitation. Le code du modèle pour la méthode `Main` statique apparaît ci-dessous :

```csharp
using System;
using UIKit;

namespace Phoneword_iOS
{
    public class Application
    {
        static void Main (string[] args)
        {
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="application-delegate"></a>Délégué d’application

Dans iOS, la classe de *délégué d’application* gère les événements système. Elle se trouve dans **AppDelegate.cs**. La classe `AppDelegate` gère la *fenêtre* d’application. La fenêtre est une instance unique de la classe `UIWindow` qui sert de conteneur pour l’interface utilisateur. Par défaut, une application n’obtient qu’une seule fenêtre dans laquelle charger son contenu. Celle-ci est attachée à un *écran* (instance `UIScreen` unique) qui fournit le rectangle englobant correspondant aux dimensions de l’écran physique de l’appareil.

*AppDelegate* est également responsable de l’abonnement aux mises à jour du système liées aux événements importants de l’application comme la fin de son lancement ou l’insuffisance de la mémoire.

Le code du modèle pour AppDelegate est présenté ci-dessous :

```csharp
using System;
using Foundation;
using UIKit;

namespace Phoneword_iOS
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window {
            get;
            set;
        }

        ...
    }
}
```

Une fois que l’application a défini sa fenêtre, elle peut commencer à charger l’interface utilisateur. La section qui suit décrit la création de l’interface utilisateur.

## <a name="user-interface"></a>Interface utilisateur

L’interface utilisateur d’une application iOS est comme une vitrine : l’application obtient généralement une seule fenêtre, mais elle peut y placer autant d’objets que nécessaire, et ces objets et mises en scène peuvent changer selon ce que l’application veut montrer. Les objets dans ce scénario, les éléments que l’utilisateur voit, sont appelés des affichages. Pour créer un seul écran dans une application, les vues sont empilées les unes sur les autres dans une *hiérarchie d’affichage de contenu*, et la hiérarchie est gérée par un contrôleur d’affichage unique. Les applications à plusieurs écrans ont plusieurs hiérarchies de vues de contenu, chacune avec son propre contrôleur de vue. L’application place les vues dans la fenêtre pour créer une hiérarchie de vues de contenu différente selon l’écran sur lequel se trouve l’utilisateur.

Cette section analyse l’interface utilisateur en décrivant les affichages, les hiérarchies d’affichage de contenu et le concepteur iOS.

### <a name="ios-designer-and-storyboards"></a>Concepteur iOS et Storyboards

Le concepteur iOS est un outil visuel permettant de générer des interfaces utilisateur dans Xamarin. Vous pouvez le lancer en double-cliquant sur un fichier de Storyboard (.storyboard), qui s’ouvre dans un affichage qui ressemble à la capture d’écran suivante :

::: zone pivot="macos"

![Interface du concepteur iOS](hello-ios-deepdive-images/image33.png)

Un *Storyboard* est un fichier qui contient les conceptions visuelles des écrans de l’application, ainsi que les transitions et les relations entre les écrans. La représentation d’un écran de l’application dans un Storyboard est appelée _scène_. Chaque scène représente un contrôleur de vue et la pile de vues gérée (hiérarchie de vues de contenu). Lorsqu’un nouveau projet d' **application de vue unique** est créé à partir d’un modèle, Visual Studio pour Mac génère automatiquement un fichier de Storyboard appelé `Main.storyboard` et le renseigne avec une scène unique, comme illustré dans la capture d’écran ci-dessous :

![Visual Studio pour Mac génère automatiquement un fichier de Storyboard appelé Main.storyboard, et le renseigne avec une scène unique](hello-ios-deepdive-images/image34.png)

La barre noire située au bas de l’écran de Storyboard permet de choisir le contrôleur de vue de la scène. Le contrôleur de vue est une instance de la classe `UIViewController` qui contient le code sur lequel repose la hiérarchie de vues de contenu. Les propriétés de ce contrôleur d’affichage peuvent être affichées et définies à l’intérieur du **panneau Propriétés**, comme illustré dans la capture d’écran ci-dessous :

![Volet Propriétés](hello-ios-deepdive-images/image35.png)

::: zone-end
::: zone pivot="windows"

![Interface du concepteur iOS](hello-ios-deepdive-images/vs-image33.png)

Un *Storyboard* est un fichier qui contient les conceptions visuelles des écrans de l’application, ainsi que les transitions et les relations entre les écrans. La représentation d’un écran de l’application dans un Storyboard est appelée _scène_. Chaque scène représente un contrôleur de vue et la pile de vues gérée (hiérarchie de vues de contenu). Lorsqu’un nouveau projet d' **application de vue unique** est créé à partir d’un modèle, Visual Studio génère automatiquement un fichier de Storyboard appelé `Main.storyboard` et le renseigne avec une scène unique, comme illustré dans la capture d’écran ci-dessous :

![Visual Studio génère automatiquement un fichier de Storyboard appelé Main.storyboard, et le renseigne avec une scène unique](hello-ios-deepdive-images/vs-image34.png)

La barre située au bas de l’écran de Storyboard permet de choisir le contrôleur de vue de la scène. Le contrôleur de vue est une instance de la classe `UIViewController` qui contient le code sur lequel repose la hiérarchie de vues de contenu. Vous pouvez afficher et définir les propriétés de ce contrôleur d’affichage dans le **volet Propriétés**, comme illustré dans la capture d’écran ci-dessous :

![Volet Propriétés](hello-ios-deepdive-images/vs-image35.png)

::: zone-end

Vous pouvez sélectionner l’_affichage_ en cliquant dans la partie blanche de la scène. L’affichage est une instance de la classe `UIView` qui définit une zone de l’écran et fournit des interfaces permettant d’utiliser le contenu dans cette zone. L’affichage par défaut est un *affichage racine* unique qui remplit tout l’écran de l’appareil.

À gauche de la scène se trouve une flèche grise avec une icône représentant un drapeau, comme l’illustre la capture d’écran ci-dessous :

 [![Flèche grise avec une icône de drapeau](hello-ios-deepdive-images/image37.png)](hello-ios-deepdive-images/image37.png#lightbox)

La flèche grise représente une transition de plan conceptuel appelée *segue* (prononcer « segmentation »). Étant donné que ce segue n’a pas d’origine, il est appelé *segue sans source*. Un segue sans source pointe vers la première scène dont les vues sont chargées dans la fenêtre de l’application au démarrage de cette dernière. La scène et les vues qu’elle contient sont la première chose que l’utilisateur voit quand l’application se charge.

Lors de la création d’une interface utilisateur, vous pouvez faire glisser d’autres affichages de la **boîte à outils** vers la vue principale de l’aire de conception, comme illustré dans la capture d’écran ci-dessous :

::: zone pivot="macos"

![Vous pouvez faire glisser d’autres affichages de la boîte à outils vers l’affichage principal dans l’aire de conception](hello-ios-deepdive-images/image38.png)

::: zone-end
::: zone pivot="windows"

![Vous pouvez faire glisser d’autres affichages de la boîte à outils vers l’affichage principal dans l’aire de conception](hello-ios-deepdive-images/vs-image38.png)

::: zone-end

Ces affichages supplémentaires sont appelés *sous-affichages*. Ensemble, l’affichage racine et les sous-vues font partie d’une *hiérarchie d’affichage de contenu* qui est gérée par le `ViewController` . Vous pouvez afficher le contour de tous les éléments de la scène en l’examinant dans le bloc de **structure du document** :

::: zone pivot="macos"

![Bloc Structure du document](hello-ios-deepdive-images/image39.png)

::: zone-end
::: zone pivot="windows"

![Bloc Structure du document](hello-ios-deepdive-images/vs-image39.png)

::: zone-end

Les sous-affichages sont mis en surbrillance dans le diagramme ci-dessous :

::: zone pivot="macos"

![Les sous-affichages sont mis en surbrillance dans le diagramme ci-dessous](hello-ios-deepdive-images/image40.png)

::: zone-end
::: zone pivot="windows"

![Les sous-affichages sont mis en surbrillance dans le diagramme ci-dessous](hello-ios-deepdive-images/vs-image40.png)

::: zone-end

La section suivante détaille la hiérarchie d’affichage de contenu représentée par cette scène.

## <a name="content-view-hierarchy"></a>Hiérarchie d’affichage de contenu

Une _hiérarchie d’affichage de contenu_ est une pile d’affichages et de sous-affichages gérés par un contrôleur d’affichage unique, comme illustré dans le diagramme ci-dessous :

 [![Hiérarchie d’affichage de contenu](hello-ios-deepdive-images/image41.png)](hello-ios-deepdive-images/image41.png#lightbox)

Nous pouvons rendre la hiérarchie d’affichage de contenu de notre `ViewController` plus facile à lire en remplaçant temporairement la couleur d’arrière-plan de l’affichage racine par du jaune dans la section Affichage du **Panneau Propriétés**, comme l’illustre la capture d’écran ci-dessous :

::: zone pivot="macos"

![Remplacement de la couleur d’arrière-plan de l’affichage racine par du jaune dans la section Affichage du Panneau Propriétés](hello-ios-deepdive-images/image42.png)

::: zone-end
::: zone pivot="windows"

![Remplacement de la couleur d’arrière-plan de l’affichage racine par du jaune dans la section Affichage du Panneau Propriétés](hello-ios-deepdive-images/vs-image42.png)

::: zone-end

Le diagramme ci-dessous illustre les relations entre la fenêtre, les vues, les sous-vues et le contrôleur de vue qui font apparaître l’interface utilisateur sur l’écran de l’appareil :

[![Relations entre la fenêtre, les vues, les sous-vues et le contrôleur de vue](hello-ios-deepdive-images/image43.png)](hello-ios-deepdive-images/image43.png#lightbox)

La prochaine section explique comment utiliser des vues dans le code et programmer les interactions de l’utilisateur à l’aide de contrôleurs de vues et du cycle de vie de la vue.

## <a name="view-controllers-and-the-view-lifecycle"></a>Contrôleurs d’affichage et cycle de vie de l’affichage

Chaque hiérarchie de vues de contenu a un contrôleur de vue correspondant pour permettre à l’utilisateur d’interagir. Le rôle du contrôleur de vue est de gérer les vues dans la hiérarchie de vues de contenu. Le contrôleur de vue ne fait pas partie de la hiérarchie de vues de contenu et n’est pas un élément de l’interface. Il fournit le code qui alimente les interactions de l’utilisateur avec les objets sur l’écran.

### <a name="view-controllers-and-storyboards"></a>Contrôleurs d’affichage et Storyboards

::: zone pivot="macos"

Dans un Storyboard, le contrôleur de vue est représenté sous la forme d’une barre en bas de la scène. La sélection du contrôleur d’affichage fait apparaître ses propriétés dans le **panneau Propriétés**:

![La sélection du contrôleur de vue fait apparaître ses propriétés dans le volet Propriétés :](hello-ios-deepdive-images/image44.png)

Une classe de contrôleur d’affichage personnalisée pour la hiérarchie d’affichage de contenu représentée par cette scène peut être définie en modifiant la propriété de **classe** dans la section **identité** du **panneau Propriétés**. Par exemple, notre application **Phoneword** définit `ViewController` en tant que contrôleur d’affichage pour notre premier écran, comme illustré dans la capture d’écran ci-dessous :

![L’application Phoneword définit ViewController en tant que contrôleur de vue](hello-ios-deepdive-images/image45new.png)

::: zone-end
::: zone pivot="windows"

Dans un Storyboard, le contrôleur de vue est représenté sous la forme d’une barre en bas de la scène. La sélection du contrôleur d’affichage fait apparaître ses propriétés dans le **volet Propriétés**:

![La sélection du contrôleur de vue fait apparaître ses propriétés dans le volet Propriétés :](hello-ios-deepdive-images/vs-image44.png)

Une classe de contrôleur d’affichage personnalisée pour la hiérarchie d’affichage de contenu représentée par cette scène peut être définie en modifiant la propriété de **classe** dans la section **identité** du **volet Propriétés**. Par exemple, notre application **Phoneword** définit `ViewController` en tant que contrôleur d’affichage pour notre premier écran, comme illustré dans la capture d’écran ci-dessous :

![L’application Phoneword définit ViewController en tant que contrôleur de vue](hello-ios-deepdive-images/vs-image45.png)

::: zone-end

Cette opération lie la représentation sous forme de Storyboard du contrôleur de vue à la classe C# `ViewController`. Ouvrez le `ViewController.cs` fichier et notez que le contrôleur d’affichage est une sous- *classe* de `UIViewController` , comme l’illustre le code ci-dessous :

```csharp
public partial class ViewController : UIViewController
{
    public ViewController (IntPtr handle) : base (handle)
    {

    }
}
```

`ViewController` pilote à présent les interactions de la hiérarchie de vues de contenu associée à ce contrôleur de vue dans le Storyboard. Vous allez ensuite apprendre le rôle du contrôleur de vue dans la gestion des vues en découvrant un processus appelé cycle de vie de la vue.

> [!NOTE]
> Pour les écrans uniquement visuels qui n’exigent pas d’interaction de l’utilisateur, vous pouvez laisser la propriété **Classe** vide dans le **Panneau Propriétés**. Ainsi, la classe de support du contrôleur de vue est définie en tant qu’implémentation par défaut d’un `UIViewController`, ce qui convient parfaitement si vous ne prévoyez pas d’ajouter du code personnalisé.

### <a name="view-lifecycle"></a>Cycle de vie de l’affichage

Le contrôleur de vue est responsable du chargement et du déchargement des hiérarchies de vues de contenu à partir de la fenêtre. Quand un événement important se produit pour une vue dans la hiérarchie de vues de contenu, le système d’exploitation notifie le contrôleur de vue via des événements dans le cycle de vie de la vue. En substituant des méthodes dans le cycle de vie de la vue, vous pouvez interagir avec les objets à l’écran et créer une interface utilisateur dynamique et réactive.

Voici les méthodes de cycle de vie de base et leur fonction :

- **ViewDidLoad** : appelée  *une fois* la première fois que le contrôleur d’affichage charge sa hiérarchie d’affichage de contenu en mémoire. Il s’agit de l’endroit idéal pour effectuer la configuration initiale, car c’est là que les sous-affichages sont disponibles pour la première fois dans le code.
- **ViewWillAppear** : appelée chaque fois que la vue d’un contrôleur d’affichage est sur le point d’être ajoutée à une hiérarchie d’affichage de contenu et qu’elle s’affiche à l’écran.
- **ViewWillDisappear** : appelée chaque fois que la vue d’un contrôleur d’affichage est sur le point d’être supprimée d’une hiérarchie d’affichage de contenu et disparaît de l’écran. Cet événement du cycle de vie est utilisé pour le nettoyage et l’enregistrement de l’état.
- **ViewDidAppear** et **ViewDidDisappear** : appelées quand un affichage est ajouté ou supprimé de la hiérarchie d’affichage de contenu, respectivement.

Quand du code personnalisé est ajouté à une phase du cycle de vie, l’*implémentation de base* de cette méthode de cycle de vie doit être *substituée*. Pour cela, accédez à la méthode de cycle de vie existante, à laquelle du code est déjà attaché, puis étendez-la avec du code supplémentaire. L’implémentation de base est appelée à partir de la méthode pour veiller à ce que le code d’origine s’exécute avant le nouveau code. Un exemple de ce type d’appel est présenté dans la section suivante.

Pour plus d’informations sur l’utilisation des contrôleurs d’affichage, consultez le Guide de programmation d’un contrôleur d’affichage d’Apple [pour iOS](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1) et la [référence UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller?language=objc).

### <a name="responding-to-user-interaction"></a>Réponse aux interactions de l’utilisateur

Le rôle le plus important du contrôleur de vue est de répondre aux interactions de l’utilisateur, par exemple l’activation des boutons, la navigation, etc. Le moyen le plus simple de gérer les interactions de l’utilisateur consiste à associer un contrôle pour écouter les entrées d’utilisateur et à attacher un gestionnaire d’événements pour répondre à ces entrées. Par exemple, un bouton peut être associé à la réponse à un événement tactile, comme illustré dans l’application Phoneword.

Voyons comment cela fonctionne.
Dans le projet `Phoneword_iOS`, un bouton appelé `TranslateButton` a été ajouté à la hiérarchie de vues de contenu :

[![Un bouton appelé TranslateButton a été ajouté à la hiérarchie d’affichage de contenu](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

Quand un **Nom** est attribué au contrôle **Bouton** dans le **Panneau Propriétés**, le concepteur iOS le mappe automatiquement sur un contrôle dans le fichier **ViewController.designer.cs**, ce qui rend `TranslateButton` disponible à l’intérieur de la classe `ViewController`. Les contrôles deviennent disponibles à la phase `ViewDidLoad` du cycle de vie de la vue. Cette méthode de cycle de vie est donc utilisée pour répondre à l’interaction tactile de l’utilisateur :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // wire up TranslateButton here
}
```

L’application Phoneword utilise un événement tactile appelé `TouchUpInside` pour écouter l’interaction tactile de l’utilisateur. `TouchUpInside` écoute un événement tactile ascendant (le doigt balaie l’écran vers le haut) qui suit une interaction tactile (le doigt touche l’écran) à l’intérieur des limites du contrôle. Le contraire de `TouchUpInside` est l’événement `TouchDown`, qui se déclenche quand l’utilisateur appuie sur un contrôle. L’événement `TouchDown` capture une grande quantité de bruit et ne permet pas à l’utilisateur d’annuler l’interaction tactile en faisant glisser son doigt en dehors du contrôle. `TouchUpInside` est le moyen le plus courant pour répondre à un appui sur un **bouton** et procure l’expérience à laquelle l’utilisateur s’attend quand il appuie sur un bouton. Pour plus d’informations à ce sujet, consultez les [Human Interface Guidelines pour iOS](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/MobileHIG/index.html).

L’application a géré l’événement `TouchUpInside` avec une expression lambda. Toutefois, un délégué ou un gestionnaire d’événements nommé auraient également pu être utilisés. Le code final du bouton ressemblait à ceci :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
      translatedNumber = Core.PhonewordTranslator.ToNumber(PhoneNumberText.Text);
      PhoneNumberText.ResignFirstResponder ();

      if (translatedNumber == "") {
        CallButton.SetTitle ("Call", UIControlState.Normal);
        CallButton.Enabled = false;
      } else {
        CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
        CallButton.Enabled = true;
      }
  };
}
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Autres concepts introduits dans Phoneword

L’application Phoneword a introduit plusieurs concepts qui ne sont pas traités dans ce guide. Ces concepts sont les suivants :

- **Modifier le texte du bouton** : l’application Phoneword a montré comment modifier le texte d’un **bouton** en appelant `SetTitle` sur le **bouton** et en passant le nouveau texte et l’**état du contrôle** du _bouton_. Par exemple, le code suivant remplace le texte de CallButton par « Call » :

    ```csharp
    CallButton.SetTitle ("Call", UIControlState.Normal);
    ```

- **Activer et désactiver des boutons** : les **boutons** peuvent être dans un état `Enabled` ou `Disabled`. Un **bouton** désactivé ne répond pas aux entrées d’utilisateur. Par exemple, le code suivant désactive `CallButton` :

    ```csharp
    CallButton.Enabled = false;
    ```

    Pour plus d’informations sur les boutons, consultez le guide sur les [boutons](~/ios/user-interface/controls/buttons.md).

- **Faire disparaître le clavier** : lorsque l’utilisateur appuie sur le champ de texte, iOS affiche le clavier pour lui permettre d’entrer du texte. Malheureusement, il n’existe aucune fonctionnalité intégrée pour faire disparaître le clavier. Le code suivant est ajouté à `TranslateButton` pour masquer le clavier quand l’utilisateur appuie sur `TranslateButton` :

    ```csharp
    PhoneNumberText.ResignFirstResponder ();
    ```

    Pour obtenir un autre exemple de masquage du clavier, consultez la recette [Masquer le clavier](https://github.com/xamarin/recipes/tree/master/Recipes/ios/input/keyboards/dismiss_the_keyboard).

- **Passer l’appel téléphonique avec une URL** : dans l’application Phoneword, un modèle d’URL Apple est utilisé pour lancer l’application téléphonique du système. Le modèle d’URL personnalisé comprend un préfixe « tel: » suivi du numéro de téléphone converti, comme l’illustre le code ci-dessous :

    ```csharp
    var url = new NSUrl ("tel:" + translatedNumber);
    if (!UIApplication.SharedApplication.OpenUrl (url))
    {
        // show alert Controller
    }
    ```

- **Afficher une alerte** : lorsqu’un utilisateur tente de passer un appel téléphonique sur un appareil qui ne prend pas en charge les appels (par exemple, le simulateur ou un iPod Touch), une boîte de dialogue d’alerte s’affiche pour indiquer à l’utilisateur que l’appel téléphonique ne peut pas être placé. Le code ci-dessous crée et renseigne un contrôleur d’alerte :

    ```csharp
    if (!UIApplication.SharedApplication.OpenUrl (url)) {
                    var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                    alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                    PresentViewController (alert, true, null);
                }
    ```

    Pour plus d’informations sur les affichages d’alerte iOS, reportez-vous à la [procédure du contrôleur d’alerte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller).

## <a name="testing-deployment-and-finishing-touches"></a>Test, déploiement et finitions

Visual Studio pour Mac et Visual Studio proposent de nombreuses options pour tester et déployer les applications. Cette section traite des options de débogage, montre comment tester des applications sur un appareil et présente les outils permettant de créer des icônes d’application et des images de lancement personnalisées.

### <a name="debugging-tools"></a>Outils de débogage

Il est parfois difficile de détecter les problèmes dans le code d’une application. Pour mieux diagnostiquer les problèmes complexes qui se trouvent dans votre code, vous pouvez [définir un point d’arrêt](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [exécuter pas à pas votre code](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) ou [enregistrer des informations dans la fenêtre Journal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Déployer l’application sur un appareil

Le simulateur IOS offre un moyen rapide de tester une application. Il propose de nombreuses optimisations utiles pour les tests, notamment un emplacement fictif, une [simulation du mouvement](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/test_location_changes_in_simulator), etc. En revanche, les utilisateurs ne consomment pas l’application finale dans un simulateur. Toutes les applications doivent être testées sur des appareils réels le plus tôt et le plus souvent possible.

Un appareil a besoin de temps pour être provisionné et un compte de développeur Apple est nécessaire. Le guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md) fournit des instructions précises sur la préparation d’un appareil au développement.

> [!NOTE]
> En raison d’une exigence d’Apple, vous devez actuellement disposer d’un certificat de développement ou d’une _identité de signature_ afin de générer votre code pour un appareil physique ou le simulateur. Suivez les étapes indiquées dans le [guide de provisionnement des appareils](~/ios/get-started/installation/device-provisioning/manual-provisioning.md) pour les obtenir.

Une fois que l’appareil est provisionné, vous pouvez y déployer des applications en le branchant, en remplaçant la cible dans la barre d’outils de génération par l’appareil iOS et en appuyant sur **Démarrer** (**Lecture**), comme l’illustre la capture d’écran suivante :

::: zone pivot="macos"

![Appui sur Démarrer/Lecture](hello-ios-deepdive-images/image46new.png)

::: zone-end
::: zone pivot="windows"

![Appui sur Démarrer/Lecture](hello-ios-deepdive-images/vs-image46.png)

::: zone-end

L’application se déploie sur l’appareil iOS :

[![L’application se déploie sur l’appareil iOS et s’exécute](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

### <a name="generate-custom-icons-and-launch-images"></a>Générer des icônes et des images de lancement personnalisées

Tout le monde ne dispose pas d’un concepteur pour créer les icônes personnalisées et les images de lancement dont une application a besoin. Voici plusieurs autres approches de génération d’une illustration d’application personnalisée :

::: zone pivot="macos"

- [**Pixelmator**](https://www.pixelmator.com/) : Application polyvalente de modification d’image pour systèmes Mac. Coûte environ 30 $.
- [**Fiverr**](https://www.fiverr.com/) : Faites votre choix parmi une variété de concepteurs pour créer l’icône qui vous convient. À partir de 5 $. Parfois aléatoire. Cependant, une bonne ressource si vous avez besoin d’icônes conçues à la volée.

::: zone-end
::: zone pivot="windows"

- Visual Studio : Vous pouvez l’utiliser pour créer une icône simple pour votre application, directement dans l’IDE.
- [**Fiverr**](https://www.fiverr.com/) : Faites votre choix parmi une variété de concepteurs pour créer l’icône qui vous convient. À partir de 5 $. Parfois aléatoire. Cependant, une bonne ressource si vous avez besoin d’icônes conçues à la volée.

::: zone-end

Pour plus d’informations sur les tailles et spécifications des icônes et images de lancement, reportez-vous au [guide d’utilisation des images](~/ios/app-fundamentals/images-icons/index.md).

## <a name="summary"></a>Récapitulatif

Félicitations ! Vous avez maintenant une connaissance approfondie des composants des applications Xamarin.iOS, ainsi que des outils nécessaires à leur création.
Dans le [didacticiel suivant de la série de mise en route](~/ios/get-started/hello-ios-multiscreen/index.md), vous allez étendre notre application pour gérer plusieurs écrans. Vous allez au fur et à mesure implémenter un contrôleur de navigation, découvrir les enchaînements de Storyboard et faire connaissance avec le modèle MVC (modèle-vue-contrôleur) quand vous étendrez votre application pour gérer plusieurs écrans.

## <a name="related-links"></a>Liens connexes

- [Hello, iOS (exemple)](/samples/xamarin/ios-samples/hello-ios)
- [Human Interface Guidelines pour iOS](https://developer.apple.com/design/human-interface-guidelines/ios/overview/themes/)
- [Portail d’approvisionnement iOS](https://developer.apple.com/account/#/overview)