---
title: fichiers. XIB dans Xamarin. Mac
description: Cet article traite de l’utilisation des fichiers. XIB créés dans le Interface Builder de Xcode pour créer et gérer des interfaces utilisateur pour une application Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5e0f096e5f2c05452a285182db4e455769aa737b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529020"
---
# <a name="xib-files-in-xamarinmac"></a>fichiers. XIB dans Xamarin. Mac

_Cet article traite de l’utilisation des fichiers. XIB créés dans le Interface Builder de Xcode pour créer et gérer des interfaces utilisateur pour une application Xamarin. Mac._

> [!NOTE]
> La méthode recommandée pour créer une interface utilisateur pour une application Xamarin. Mac consiste à utiliser des storyboards. Cette documentation a été laissée en place pour des raisons historiques et pour l’utilisation de projets Xamarin. Mac plus anciens. Pour plus d’informations, consultez notre [Introduction à](~/mac/platform/storyboards/index.md) la documentation sur les storyboards.

## <a name="overview"></a>Vue d'ensemble

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes éléments d’interface utilisateur et aux mêmes outils que ceux d’un développeur qui travaille en *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser la _Interface Builder_ de Xcode pour créer et gérer vos interfaces utilisateur (ou éventuellement les créer directement dans C# le code).

Un fichier. XIB est utilisé par macOS pour définir des éléments de l’interface utilisateur de votre application (tels que des menus, des fenêtres, des vues, des étiquettes, des champs de texte) qui sont créés et gérés graphiquement dans le Interface Builder de Xcode.

[![Exemple d’application en cours d’exécution](xib-images/intro01.png "Exemple d’application en cours d’exécution")](xib-images/intro01-large.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des fichiers. XIB dans une application Xamarin. Mac. Il est fortement recommandé de travailler sur l’article [Hello, Mac](~/mac/get-started/hello-mac.md) en premier, car il aborde les concepts et les techniques clés que nous allons utiliser dans cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` attributs `Export` et utilisés pour associer vos C# classes à Objets objective-C et éléments d’interface utilisateur.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introduction à Xcode et Interface Builder

Dans le cadre de Xcode, Apple a créé un outil appelé Interface Builder, qui vous permet de créer visuellement votre interface utilisateur dans un concepteur. Xamarin. Mac s’intègre à Interface Builder, ce qui vous permet de créer votre interface utilisateur avec les mêmes outils que les utilisateurs objective-C.


### <a name="components-of-xcode"></a>Composants de Xcode

Lorsque vous ouvrez un fichier. XIB dans Xcode à partir de Visual Studio pour Mac, il s’ouvre avec un **navigateur de projet** à gauche, la **hiérarchie d’interface** et l’éditeur d' **interface** au milieu, ainsi qu’une section **Propriétés & utilitaires** à droite:

[![Composants de l’interface utilisateur de Xcode](xib-images/xcode03.png "Composants de l’interface utilisateur de Xcode")](xib-images/xcode03-large.png#lightbox)

Examinons ce que fait chacune de ces sections Xcode et comment vous allez les utiliser pour créer l’interface de votre application Xamarin. Mac.


#### <a name="project-navigation"></a>Navigation dans le projet

Lorsque vous ouvrez un fichier. XIB pour le modifier dans Xcode, Visual Studio pour Mac crée un fichier de projet XCode en arrière-plan pour communiquer les modifications entre lui-même et Xcode. Plus tard, lorsque vous rebasculerez vers Visual Studio pour Mac à partir de Xcode, toutes les modifications apportées à ce projet sont synchronisées avec votre projet Xamarin. Mac en Visual Studio pour Mac.

La section de **navigation de projet** vous permet de naviguer entre tous les fichiers qui composent ce projet XCode de _shim_ . En général, vous êtes intéressé uniquement par les fichiers. XIB dans cette liste, tels que **MainMenu. XIB** et **MainWindow. XIB**.


#### <a name="interface-hierarchy"></a>Hiérarchie d’interface

La section **hiérarchie d’interface** vous permet d’accéder facilement à plusieurs propriétés clés de l’interface utilisateur, telles que ses **espaces réservés** et la **fenêtre**principale. Vous pouvez également utiliser cette section pour accéder aux éléments individuels (vues) qui composent votre interface utilisateur et ajuster la façon dont ils sont imbriqués en les faisant glisser dans la hiérarchie.


#### <a name="interface-editor"></a>Éditeur d’interface

La section de l' **éditeur d’interface** fournit la surface sur laquelle vous mettez en forme graphiquement votre interface utilisateur. Vous allez faire glisser des éléments à partir de la section **bibliothèque** de la section **Propriétés & utilitaires** pour créer votre conception. Lorsque vous ajoutez des éléments d’interface utilisateur (vues) à l’aire de conception, ils sont ajoutés à la section **hiérarchie d’interface** dans l’ordre dans lequel ils apparaissent dans l' **éditeur d’interface**.


#### <a name="properties--utilities"></a>Propriétés & utilitaires

La section **propriétés & utilitaires** est divisée en deux sections principales que nous allons utiliser, les **Propriétés** (également appelées inspecteurs) et la **bibliothèque**:

![L’inspecteur de propriété](xib-images/xcode04.png "L’inspecteur de propriété")

Initialement, cette section est presque vide. Toutefois, si vous sélectionnez un élément dans l' **éditeur d’interface** ou la **hiérarchie d’interface**, la section **Propriétés** est remplie avec des informations sur l’élément et les propriétés donnés que vous pouvez Adjust.

Dans la section **Propriétés**, il existe 8 *onglets d’inspecteurs* différents, comme indiqué dans l’illustration suivante :

[![Vue d’ensemble de tous les inspecteurs](xib-images/xcode05.png "Vue d’ensemble de tous les inspecteurs")](xib-images/xcode05-large.png#lightbox)

De gauche à droite, ces onglets sont :

- **File Inspector** (Inspecteur de fichier) : l’onglet Inspecteur de fichier affiche des informations sur le fichier, telles que l’emplacement et le nom du fichier Xib qui est en cours de modification.
- **Aide rapide** : l’onglet Aide rapide fournit une aide contextuelle basée sur la sélection dans Xcode.
- **Identity Inspector** (Inspecteur d’identité) : l’onglet Inspecteur d’identité fournit des informations sur la vue/le contrôle sélectionné.
- **Inspecteur d’attributs** : l’inspecteur d’attributs vous permet de personnaliser différents attributs du contrôle/de la vue sélectionné (e).
- **Inspecteur de taille** : l’inspecteur de taille vous permet de contrôler la taille et le comportement de redimensionnement du contrôle/de la vue sélectionné (e).
- **Inspecteur des connexions** : l’inspecteur de connexions affiche les connexions de sortie et d’action des contrôles sélectionnés. Nous allons examiner les sorties et les actions dans un instant.
- **Inspecteur de liaisons** : l’inspecteur de liaisons vous permet de configurer des contrôles afin que leurs valeurs soient automatiquement liées aux modèles de données.
- **Inspecteur des effets de vue** : l’inspecteur des effets de vue vous permet de spécifier des effets sur les contrôles, tels que les animations.

Dans la section **bibliothèque** , vous pouvez trouver des contrôles et des objets à placer dans le concepteur pour créer graphiquement votre interface utilisateur:

![Exemple de l’inspecteur de bibliothèque](xib-images/xcode06.png "Exemple de l’inspecteur de bibliothèque")

Maintenant que vous êtes familiarisé avec l’IDE Xcode et Interface Builder, voyons comment l’utiliser pour créer une interface utilisateur.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Création et maintenance de fenêtres dans Xcode

La méthode recommandée pour créer une interface utilisateur de l’application Xamarin. Mac consiste à utiliser des storyboards (pour plus d’informations, consultez la documentation relative [à la présentation](~/mac/platform/storyboards/index.md) des storyboards) et, par conséquent, tout nouveau projet démarré dans Xamarin. Mac utilisera les storyboards en valeurs.

Pour basculer vers à l’aide d’une interface utilisateur basée sur. XIB, procédez comme suit:

1. Ouvrez Visual Studio pour Mac et démarrez un nouveau projet Xamarin. Mac.
2. Dans le **panneau solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **un nouveau fichier...**
3. Sélectionner le**contrôleur Windows** **Mac** > :

    ![Ajout d’un nouveau contrôleur de fenêtre](xib-images/setup00.png "Ajout d’un nouveau contrôleur de fenêtre")
4. Entrez `MainWindow` pour le nom et cliquez sur le bouton **nouveau** :

    ![Ajout d’une nouvelle fenêtre principale](xib-images/setup01.png "Ajout d’une nouvelle fenêtre principale")
5. Cliquez à nouveau avec le bouton droit sur le projet et sélectionnez **Ajouter** > **un nouveau fichier...**
6. Sélectionnez le**menu principal**de **Mac** > :

    ![Ajout d’un nouveau menu principal](xib-images/setup02.png "Ajout d’un nouveau menu principal")
7. Laissez le nom en `MainMenu` tant que et cliquez sur le bouton **nouveau** .
8. Dans la **panneau solutions** sélectionnez le fichier **main. Storyboard** , cliquez avec le bouton droit et sélectionnez **supprimer**:

    ![Sélection de la table de montage séquentiel principale](xib-images/setup03.png "Sélection de la table de montage séquentiel principale")
9. Dans la boîte de dialogue Supprimer, cliquez sur le bouton **supprimer** :

    ![Confirmation de la suppression](xib-images/setup04.png "Confirmation de la suppression")
10. Dans la **panneau solutions**, double-cliquez sur le fichier **info. plist** pour l’ouvrir et le modifier.
11. Sélectionnez `MainMenu` dans la liste déroulante **interface principale** :

    [![Définition du menu principal](xib-images/setup05.png "Définition du menu principal")](xib-images/setup05-large.png#lightbox)
12. Dans la **panneau solutions**, double-cliquez sur le fichier **MainMenu. XIB** pour l’ouvrir et le modifier dans le Interface Builder de Xcode.
13. Dans l' **inspecteur de bibliothèque**, `object` tapez dans le champ de recherche, puis faites glisser un nouvel **objet** sur l’aire de conception:

    [![Modification du menu principal](xib-images/setup06.png "Modification du menu principal")](xib-images/setup06-large.png#lightbox)
14. Dans l' **inspecteur d’identité**, `AppDelegate` entrez pour la **classe**:

    [![Sélection du délégué d’application](xib-images/setup07.png "Sélection du délégué d’application")](xib-images/setup07-large.png#lightbox)
15. Sélectionnez le **propriétaire du fichier** dans la hiérarchie de l' **interface**, basculez vers l' **inspecteur de connexion** et faites glisser une `AppDelegate` ligne du délégué vers l' **objet** que vous venez d’ajouter au projet:

    [![Connexion du délégué d’application](xib-images/setup08.png "Connexion du délégué d’application")](xib-images/setup08-large.png#lightbox)
16. Enregistrez les modifications et revenez à Visual Studio pour Mac.

Une fois toutes ces modifications en place, modifiez le fichier **AppDelegate.cs** et faites-le ressembler à ce qui suit:

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

À présent, la fenêtre principale de l’application est définie dans un fichier **. XIB** inclus automatiquement dans le projet lors de l’ajout d’un contrôleur de fenêtre. Pour modifier votre conception Windows, dans le **panneau solutions**, double-cliquez sur le fichier **MainWindow. XIB** :

![Sélection du fichier MainWindow. XIB](xib-images/edit01.png "Sélection du fichier MainWindow. XIB")

Cette opération ouvre la conception de la fenêtre dans le Interface Builder de Xcode:

[![Modification de MainWindow. XIB](xib-images/edit02.png "Modification de MainWindow. XIB")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Flux de travail de fenêtre standard

Pour toute fenêtre que vous créez et utilisez dans votre application Xamarin. Mac, le processus est fondamentalement le même:

1. Pour les nouvelles fenêtres qui ne sont pas la valeur par défaut automatiquement ajoutée à votre projet, ajoutez une nouvelle définition de fenêtre au projet.
2. Double-cliquez sur le fichier. XIB pour ouvrir la conception de la fenêtre et la modifier dans le Interface Builder de Xcode.
3. Définissez les propriétés de fenêtre requises dans l' **inspecteur d’attribut** et l’inspecteur de **taille**.
4. Faites glisser les contrôles requis pour générer votre interface et configurez-les dans l' **inspecteur d’attribut**.
5. Utilisez l' **inspecteur de taille** pour gérer le redimensionnement de vos éléments d’interface utilisateur.
6. Exposez les éléments d’interface C# utilisateur de la fenêtre au code via des prises et des actions.
7. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.


### <a name="designing-a-window-layout"></a>Conception d’une disposition de fenêtre

Le processus de disposition d’une interface utilisateur dans Interface Builder est fondamentalement le même pour chaque élément que vous ajoutez:

1. Recherchez le contrôle souhaité dans l' **inspecteur de bibliothèque** , puis faites-le glisser dans l' **éditeur d’interface** et positionnez-le.
2. Définissez les propriétés de fenêtre requises dans l' **inspecteur d’attribut**.
3. Utilisez l' **inspecteur de taille** pour gérer le redimensionnement de vos éléments d’interface utilisateur.
4. Si vous utilisez une classe personnalisée, définissez-la dans l' **inspecteur d’identité**.
5. Exposer les éléments d’interface C# utilisateur au code via des sorties et des actions.
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Par exemple :

1. Dans Xcode, faites glisser un **bouton de commande** à partir de la **section Bibliothèque** :

    [![Sélection d’un bouton dans la bibliothèque](xib-images/xcode07.png "Sélection d’un bouton dans la bibliothèque")](xib-images/xcode07-large.png#lightbox)
2. Déposez le bouton dans la **fenêtre** de l' **éditeur d’interface**:

    [![Ajout d’un bouton à la fenêtre](xib-images/xcode08.png "Ajout d’un bouton à la fenêtre")](xib-images/xcode08-large.png#lightbox)
3. Cliquez sur la propriété **Titre** dans l’**inspecteur d’attributs** et remplacez le titre du bouton par `Click Me` :

    ![Définition des attributs de bouton](xib-images/xcode09.png "Définition des attributs de bouton")
4. Faites glisser une **étiquette** à partir de la **section Bibliothèque** :

    [![Sélection d’une étiquette dans la bibliothèque](xib-images/xcode10.png "Sélection d’une étiquette dans la bibliothèque")](xib-images/xcode10-large.png#lightbox)
5. Placez l’étiquette sur la **fenêtre** à côté du bouton dans l’**Éditeur d’interface** :

    [![Ajout d’une étiquette à la fenêtre](xib-images/xcode11.png "Ajout d’une étiquette à la fenêtre")](xib-images/xcode11-large.png#lightbox)
6. Saisissez la poignée droite de l’étiquette et faites-la glisser jusqu’à ce qu’elle soit près du bord de la fenêtre :

    [![Redimensionnement de l’étiquette](xib-images/xcode12.png "Redimensionnement de l’étiquette")](xib-images/xcode12-large.png#lightbox)
7. Avec l’étiquette toujours sélectionnée dans l' **éditeur d’interface**, basculez vers l' **inspecteur de taille**:

    ![Sélection de l’inspecteur de taille](xib-images/xcode13.png "Sélection de l’inspecteur de taille")
8. Dans la **zone** redimensionnement automatique, cliquez sur le **crochet rouge gris** à droite et la **flèche horizontale rouge grise** dans le Centre:

    ![Modification des propriétés de] redimensionnement automatique (xib-images/xcode14.png "Modification des propriétés de") redimensionnement automatique
9. Cela garantit que l’étiquette est étirée pour s’agrandir et se rétrécir lorsque la fenêtre est redimensionnée dans l’application en cours d’exécution. Les **crochets rouges** et le haut et la gauche de la zone de **zone** de redimensionnement automatique indiquent que l’étiquette doit être bloquée à ses emplacements X et Y donnés.
10. Enregistrer les modifications apportées à l’interface utilisateur

Au fur et à mesure que vous redimensionnez et déplacez des contrôles, vous avez dû remarquer que Interface Builder vous donne des conseils d’accrochage basés sur [les instructions d’interface utilisateur OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Ces instructions vous aideront à créer des applications de haute qualité qui auront une apparence familière pour les utilisateurs Mac.

Si vous regardez dans la section **hiérarchie d’interface** , vous remarquerez comment la disposition et la hiérarchie des éléments qui composent notre interface utilisateur sont affichées:

![Sélection d’un élément dans la hiérarchie d’interface](xib-images/xcode15.png "Sélection d’un élément dans la hiérarchie d’interface")

À partir de là, vous pouvez sélectionner des éléments à modifier ou à faire glisser pour réorganiser les éléments d’interface utilisateur si nécessaire. Par exemple, si un élément d’interface utilisateur est couvert par un autre élément, vous pouvez le faire glisser vers le bas de la liste pour en faire l’élément le plus haut dans la fenêtre.

Pour plus d’informations sur l’utilisation de Windows dans une application Xamarin. Mac, consultez notre documentation [Windows](~/mac/user-interface/window.md) .


## <a name="exposing-ui-elements-to-c-code"></a>Exposer des éléments d’interface C# utilisateur au code

Une fois que vous avez fini de disposer l’apparence de votre interface utilisateur dans Interface Builder, vous devez exposer les éléments de l’interface utilisateur afin qu’ils soient accessibles à partir C# du code. Pour ce faire, vous allez utiliser actions et prises.


### <a name="setting-a-custom-main-window-controller"></a>Définition d’un contrôleur de fenêtre principale personnalisé

Pour pouvoir créer des sorties et des actions pour exposer des éléments d’interface C# utilisateur au code, l’application Xamarin. Mac doit utiliser un contrôleur de fenêtre personnalisé.

Effectuez ce qui suit :

1. Ouvrez le Storyboard de l’application dans le Interface Builder de Xcode.
2. `NSWindowController` Sélectionnez dans la aire de conception.
3. Basculez vers la vue de l’inspecteur `WindowController` d' **identité** et entrez comme nom de la **classe**:

    [![Modification du nom de la classe](xib-images/windowcontroller01.png "Modification du nom de la classe")](xib-images/windowcontroller01-large.png#lightbox)
4. Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser.
5. Un fichier **WindowController.cs** sera ajouté à votre projet dans le **panneau solutions** dans Visual Studio pour Mac:

    ![Nom de la nouvelle classe dans Visual Studio pour Mac](xib-images/windowcontroller02.png "Nom de la nouvelle classe dans Visual Studio pour Mac")
6. Rouvrez le Storyboard dans le Interface Builder de Xcode.
7. Le fichier **WindowController. h** sera disponible en vue de son utilisation:

    [![Fichier. h correspondant dans Xcode](xib-images/windowcontroller03.png "Fichier. h correspondant dans Xcode")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Prises et actions

Quelles sont les sorties et les actions? Dans la programmation de l’interface utilisateur .NET traditionnelle, un contrôle dans l’interface utilisateur est automatiquement exposé en tant que propriété quand il est ajouté. Le fonctionnement est différent dans Mac : le simple ajout d’un contrôle à une vue ne le rend pas accessible au code. Le développeur doit exposer explicitement l’élément d’interface utilisateur au code. Pour ce faire, Apple nous offre deux options:

- **Outlets** : les outlets ressemblent à des propriétés. Si vous associez un contrôle à une sortie, elle est exposée à votre code via une propriété, ce qui vous permet d’effectuer des opérations telles que joindre des gestionnaires d’événements, appeler des méthodes sur celle-ci, etc.
- **Actions** : les actions ressemblent au modèle de commande dans WPF. Par exemple, lorsqu’une action est effectuée sur un contrôle, par exemple un clic sur un bouton, le contrôle appelle automatiquement une méthode dans votre code. Les actions sont puissantes et pratiques, car vous pouvez associer de nombreux contrôles à la même action.

Dans Xcode, les prises et les actions sont ajoutées directement dans le code via *le glissement de contrôle*. Plus précisément, cela signifie que pour créer une sortie ou une action, vous choisissez l’élément de contrôle auquel vous souhaitez ajouter une prise ou une action, maintenez le bouton **CTRL** enfoncé sur le clavier, puis faites glisser ce contrôle directement dans votre code.

Pour les développeurs Xamarin. Mac, cela signifie que vous faites glisser dans les fichiers stub objective-C qui C# correspondent au fichier dans lequel vous souhaitez créer la sortie ou l’action. Visual Studio pour Mac créé un fichier appelé **MainWindow. h** dans le cadre du projet XCode de Shim qu’il a généré pour utiliser le Interface Builder:

[![Exemple de fichier. h dans Xcode](xib-images/xcode16.png "Exemple de fichier. h dans Xcode")](xib-images/xcode16-large.png#lightbox)

Ce fichier stub. h reflète le **MainWindow.Designer.cs** qui est automatiquement ajouté à un projet Xamarin. Mac lors de la `NSWindow` création d’un nouveau. Ce fichier sera utilisé pour synchroniser les modifications apportées par Interface Builder et c’est là que nous allons créer vos prises et actions afin que les éléments C# d’interface utilisateur soient exposés au code.


#### <a name="adding-an-outlet"></a>Ajout d’une sortie

Avec une compréhension élémentaire des sorties et des actions, voyons comment créer une prise pour exposer un élément d’interface utilisateur à votre C# code.

Effectuez ce qui suit :

1. Dans Xcode, dans le coin supérieur tout à droite de l’écran, cliquez sur le bouton **Double Circle** (Cercle double) pour ouvrir l’**Assistant Editor** (Éditeur d’Assistant) :

    [![Sélection de l’éditeur de l’Assistant](xib-images/outlet01.png "Sélection de l’éditeur de l’Assistant")](xib-images/outlet01-large.png#lightbox)
2. Xcode passe en mode fractionné avec l’**Éditeur d’interface** d’un côté et un **Éditeur de code** de l’autre.
3. Notez que Xcode a choisi automatiquement le fichier **MainWindowController. m** dans l' **éditeur de code**, ce qui est incorrect. Si vous vous souvenez de notre discussion sur les sorties et les actions ci-dessus, vous devez sélectionner **MainWindow. h** .
4. En haut de l' **éditeur de code** , cliquez sur le **lien automatique** , puis sélectionnez le fichier **MainWindow. h** :

    [![Sélection du fichier. h correct](xib-images/outlet02.png "Sélection du fichier. h correct")](xib-images/outlet02-large.png#lightbox)
5. Le fichier approprié doit maintenant être sélectionné pour Xcode :

    [![Le fichier correct sélectionné](xib-images/outlet03.png "Le fichier correct sélectionné")](xib-images/outlet03-large.png#lightbox)
6. **La dernière étape était très importante !** Si vous n’avez pas sélectionné le fichier approprié, vous ne pourrez pas créer de prises et d’actions, ou celles-ci seront exposées à la C#mauvaise classe dans!
7. Dans l **'éditeur d’interface**, maintenez la touche **CTRL** enfoncée sur le clavier, puis cliquez sur l’étiquette que nous avons créée ci- `@interface MainWindow : NSWindow { }` dessus dans l’éditeur de code, juste en dessous du code:

    [![Glissement pour créer un nouvel orifice](xib-images/outlet04.png "Glissement pour créer un nouvel orifice")](xib-images/outlet04-large.png#lightbox)
8. Une boîte de dialogue s’affiche. Laissez la **connexion** définie sur Outlet et entrez `ClickedLabel` pour le **nom**:

    [![Définition des propriétés de la sortie](xib-images/outlet05.png "Définition des propriétés de la sortie")](xib-images/outlet05-large.png#lightbox)
9. Cliquez sur le bouton **se connecter** pour créer la sortie:

    ![La sortie terminée](xib-images/outlet06.png "La sortie terminée")
10. Enregistrez les modifications dans le fichier.


#### <a name="adding-an-action"></a>Ajout d’une action

Ensuite, examinons la création d’une action pour exposer une interaction utilisateur avec l’élément d’interface C# utilisateur à votre code.

Effectuez ce qui suit :

1. Assurez-vous que nous sommes toujours dans l’éditeur de l' **Assistant** et que le fichier **MainWindow. h** est visible dans l' **éditeur de code**.
2. Dans l **'éditeur d’interface**, maintenez la touche **CTRL** enfoncée sur le clavier, puis cliquez sur le bouton que nous avons créé ci- `@property (assign) IBOutlet NSTextField *ClickedLabel;` dessus dans l’éditeur de code, juste en dessous du code:

    [![Glissement pour créer une action](xib-images/action01.png "Glissement pour créer une action")](xib-images/action01-large.png#lightbox)
3. Modifiez le type de **connexion** en action:

    [![Sélectionner un type d’action](xib-images/action02.png "Sélectionner un type d’action")](xib-images/action02-large.png#lightbox)
4. Entrez `ClickedButton` comme **Nom** :

    [![Configuration de l’action](xib-images/action03.png "Configuration de l’action")](xib-images/action03-large.png#lightbox)
5. Cliquez sur le bouton **se connecter** pour créer une action:

    ![Action terminée](xib-images/action04.png "Action terminée")
6. Enregistrez les modifications dans le fichier.

Avec votre interface utilisateur câblée et exposée au C# code, revenez à Visual Studio pour Mac et laissez-la synchroniser les modifications de Xcode et Interface Builder.


### <a name="writing-the-code"></a>Écriture du code

Avec votre interface utilisateur créée et ses éléments d’interface utilisateur exposés au code via des prises et des actions, vous êtes prêt à écrire le code pour donner vie à votre programme. Par exemple, ouvrez le fichier **MainWindow.cs** pour le modifier en double-cliquant dessus dans la **panneau solutions**:

[![Fichier MainWindow.cs](xib-images/code01.png "Fichier MainWindow.cs")](xib-images/code01-large.png#lightbox)

Et ajoutez le code suivant à la `MainWindow` classe pour travailler avec l’exemple de sortie que vous avez créé ci-dessus:

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Notez que `NSLabel` est accessible dans C# par le nom direct que vous lui avez attribué dans Xcode lorsque vous avez créé sa sortie dans Xcode, dans ce cas, il est `ClickedLabel`appelé. Vous pouvez accéder à toute méthode ou propriété de l’objet exposé de la même façon que n' C# importe quelle classe normale.

> [!IMPORTANT]
> Vous devez utiliser `AwakeFromNib`, au lieu d’une autre méthode `Initialize`comme, car `AwakeFromNib` est appelée _après que_ le système d’exploitation a chargé et instancié l’interface utilisateur à partir du fichier. XIB. Si vous avez essayé d’accéder au contrôle Label avant le chargement et l’instanciation complets du fichier. XIB, vous obtenez une `NullReferenceException` erreur, car le contrôle Label n’est pas encore créé.

Ensuite, ajoutez la classe partielle suivante à la `MainWindow` classe:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Ce code est associé à l’action que vous avez créée dans Xcode et Interface Builder et sera appelée chaque fois que l’utilisateur cliquera sur le bouton.

Certains éléments de l’interface utilisateur ont automatiquement des actions intégrées, par exemple des éléments dans la barre de menus par défaut, tels que l’élément`openDocument:`de menu **Ouvrir...** (). Dans la **panneau solutions**, double-cliquez sur le fichier **AppDelegate.cs** pour l’ouvrir et ajouter le code suivant sous la `DidFinishLaunching` méthode:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

La ligne de clé ici `[Export ("openDocument:")]`est, elle `NSMenu` indique que le **AppDelegate** a une `void OpenDialog (NSObject sender)` méthode qui répond à `openDocument:` l’action.

Pour plus d’informations sur l’utilisation des menus, consultez notre documentation sur les [menus](~/mac/user-interface/menu.md) .


## <a name="synchronizing-changes-with-xcode"></a>Synchronisation des modifications avec Xcode

Lorsque vous revenez à Visual Studio pour Mac à partir de Xcode, toutes les modifications que vous avez apportées à Xcode seront automatiquement synchronisées avec votre projet Xamarin. Mac.

Si vous sélectionnez le **MainWindow.Designer.cs** dans la **panneau solutions** vous pourrez voir comment notre sortie et action ont été raccordées dans notre C# code:

[![Synchronisation des modifications avec Xcode](xib-images/sync01.png "Synchronisation des modifications avec Xcode")](xib-images/sync01-large.png#lightbox)

Notez comment les deux définitions dans le fichier **MainWindow.Designer.cs** :

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Aligner les définitions dans le fichier **MainWindow. h** dans Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Comme vous pouvez le voir, Visual Studio pour Mac écoute les modifications apportées au fichier. h, puis synchronise automatiquement ces modifications dans le fichier **. Designer.cs** respectif pour les exposer à votre application. Vous pouvez également remarquer que **MainWindow.Designer.cs** est une classe partielle, de sorte que Visual Studio pour Mac n’a pas à modifier **MainWindow.cs** , ce qui remplacerait toute modification apportée à la classe.

Normalement, vous n’avez pas besoin d’ouvrir le **MainWindow.Designer.cs** vous-même, il a été présenté ici à titre éducatif uniquement.

> [!IMPORTANT]
> Dans la plupart des cas, Visual Studio pour Mac affiche automatiquement les modifications apportées à Xcode et les synchronise sur votre projet Xamarin. Mac. Dans le cas isolé où la synchronisation ne se produit pas automatiquement, revenez à Xcode, puis à nouveau à Visual Studio pour Mac. Cela déclenche normalement un cycle de synchronisation.


## <a name="adding-a-new-window-to-a-project"></a>Ajout d’une nouvelle fenêtre à un projet

Outre la fenêtre de document principale, une application Xamarin. Mac peut être amenée à afficher d’autres types de fenêtres pour l’utilisateur, telles que des préférences ou des panneaux d’inspecteur. Quand vous ajoutez une nouvelle fenêtre à votre projet, vous devez toujours utiliser l’option **de la fenêtre de cacao avec le contrôleur** , car cela facilite le chargement de la fenêtre à partir du fichier. XIB.

Pour ajouter une nouvelle fenêtre, procédez comme suit:

1. Dans le **panneau solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **un nouveau fichier.** ..
2. Dans la boîte de dialogue nouveau fichier, sélectionnez **Xamarin. Mac** > **cacao fenêtre avec contrôleur**:

    ![Ajout d’un nouveau contrôleur de fenêtre](xib-images/new01.png "Ajout d’un nouveau contrôleur de fenêtre")
3. Entrez `PreferencesWindow` comme **Nom**, puis cliquez sur le bouton **Nouveau**.
4. Double-cliquez sur le fichier **PreferencesWindow. XIB** pour l’ouvrir et le modifier dans Interface Builder:

    [![Modification de la fenêtre dans Xcode](xib-images/new02.png "Modification de la fenêtre dans Xcode")](xib-images/new02-large.png#lightbox)
5. Concevez votre interface:

    [![Conception de la disposition Windows](xib-images/new03.png "Conception de la disposition Windows")](xib-images/new03-large.png#lightbox)
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Ajoutez le code suivant à **AppDelegate.cs** pour afficher votre nouvelle fenêtre:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

La `var preferences = new PreferencesWindowController ();` ligne crée une nouvelle instance du contrôleur de fenêtre qui charge la fenêtre à partir du fichier. XIB et l’augmente. La `preferences.Window.MakeKeyAndOrderFront (this);` ligne affiche la nouvelle fenêtre à l’utilisateur.

Si vous exécutez le code et sélectionnez **Préférences...** dans le menu de l' **application**, la fenêtre s’affiche:

![Exécution de l’exemple d’application](xib-images/new04.png "Exécution de l’exemple d’application")

Pour plus d’informations sur l’utilisation de Windows dans une application Xamarin. Mac, consultez notre documentation [Windows](~/mac/user-interface/window.md) .


## <a name="adding-a-new-view-to-a-project"></a>Ajout d’une nouvelle vue à un projet

Dans certains cas, il est plus facile de décomposer la conception de votre fenêtre en plusieurs fichiers. XIB plus gérables. Par exemple, comme le remplacement du contenu de la fenêtre principale lors de la sélection d’un élément de barre d’outils dans une **fenêtre Préférences** ou la permutation du contenu en réponse à une sélection de **liste source** .

Lorsque vous ajoutez une nouvelle vue à votre projet, vous devez toujours utiliser l’option **vue du cacao avec contrôleur** , car cela rend le processus de chargement de la vue à partir du fichier. XIB plus facile.

Pour ajouter une nouvelle vue, procédez comme suit:

1. Dans le **panneau solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **un nouveau fichier.** ..
2. Dans la boîte de dialogue nouveau fichier, sélectionnez **Xamarin. Mac** > ,**vue du cacao, avec contrôleur**:

    ![Ajout d’une nouvelle vue](xib-images/view01.png "Ajout d’une nouvelle vue")
3. Entrez `SubviewTable` comme **Nom**, puis cliquez sur le bouton **Nouveau**.
4. Double-cliquez sur le fichier **SubviewTable. XIB** pour l’ouvrir et le modifier dans Interface Builder et concevoir l’interface utilisateur:

    [![Conception de la nouvelle vue dans Xcode](xib-images/view02.png "Conception de la nouvelle vue dans Xcode")](xib-images/view02-large.png#lightbox)
5. Connectez les actions et les prises nécessaires.
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Modifiez ensuite le **SubviewTable.cs** et ajoutez le code suivant au fichier **AwakeFromNib** pour remplir la nouvelle vue lorsqu’elle est chargée:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

Ajoutez une énumération au projet pour effectuer le suivi de la vue actuellement affichée. Par exemple, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Modifiez le fichier. XIB de la fenêtre qui consommera la vue et l’affichera. Ajoutez une **vue personnalisée** qui fera office de conteneur pour la vue une fois qu’elle est chargée en mémoire C# par le code et exposez- `ViewContainer`la à une sortie appelée:

[![Création de la sortie requise](xib-images/view03.png "Création de la sortie requise")](xib-images/view03-large.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Modifiez ensuite le fichier. cs de la fenêtre qui affichera le nouvel affichage (par exemple, **MainWindow.cs**) et ajoutez le code suivant:

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

Lorsque nous avons besoin d’afficher une nouvelle vue chargée à partir d’un fichier. XIB dans le conteneur de la fenêtre (la **vue personnalisée** ajoutée ci-dessus), ce code gère la suppression de toute vue existante et la transmutant pour la nouvelle. Il semble voir que vous avez déjà une vue affichée, si elle est supprimée de l’écran. Ensuite, il prend la vue qui a été transmise (telle qu’elle est chargée à partir d’un contrôleur d’affichage) la redimensionne pour s’ajuster à la zone de contenu et l’ajoute au contenu en vue de son affichage.

Pour afficher un nouvel affichage, utilisez le code suivant:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Cela crée une nouvelle instance du contrôleur d’affichage pour la nouvelle vue à afficher, définit son type (comme spécifié par l’énumération ajoutée au projet) et utilise la `DisplaySubview` méthode ajoutée à la classe de la fenêtre pour afficher réellement la vue. Exemple :

[![Exécution de l’exemple d’application](xib-images/view04.png "Exécution de l’exemple d’application")](xib-images/view04-large.png#lightbox)

Pour plus d’informations sur l’utilisation de Windows dans une application Xamarin. Mac, consultez la documentation relative à [Windows](~/mac/user-interface/window.md) et aux [boîtes de dialogue](~/mac/user-interface/dialog.md) .


## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des fichiers. XIB dans une application Xamarin. Mac. Nous avons vu les différents types et utilisations des fichiers. XIB pour créer l’interface utilisateur de votre application, comment créer et gérer des fichiers. XIB dans le Interface Builder de Xcode et comment utiliser des fichiers. C# XIB dans le code.


## <a name="related-links"></a>Liens associés

- [MacImages (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [Boîtes de dialogue](~/mac/user-interface/dialog.md)
- [Utilisation des images](~/mac/app-fundamentals/image.md)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
