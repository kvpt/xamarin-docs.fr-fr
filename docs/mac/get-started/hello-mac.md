---
title: Hello, Mac – Procédure pas à pas
description: Ce document montre comment créer une application Xamarin.Mac et présente Visual Studio pour Mac, Xcode et Interface Builder. Il traite de l’exposition des contrôles d’interface utilisateur au code par le biais d’outlets et d’actions, et montre comment générer, exécuter et tester une application Xamarin.Mac.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 37D0E9E6-979B-7069-B3BE-C5F0AF99BA72
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/02/2018
ms.openlocfilehash: 767282516d9fc2ce097d13641697290dad7bd8ad
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938071"
---
# <a name="hello-mac-walkthrough"></a>Hello, Mac – Procédure pas à pas

Xamarin.Mac permet le développement d’applications Mac entièrement natives en C# et .NET à l’aide des mêmes API macOS que celles utilisées lors du développement en *Objective-C* ou *Swift*. Comme Xamarin. Mac s’intègre directement à Xcode, le développeur peut utiliser l' _Interface Builder_ de Xcode pour créer les interfaces utilisateur d’une application (ou éventuellement les créer directement en code C#).

De plus, étant donné que les applications Xamarin.Mac sont écrites en C# et .NET, le code peut être partagé avec les applications mobiles Xamarin.iOS et Xamarin.Android, tout en proposant une expérience native sur chaque plateforme.

Cet article vous présente les concepts clés nécessaires pour créer une application Mac à l’aide de Xamarin.Mac, Visual Studio pour Mac et Interface Builder de Xcode en passant en revue le processus de génération d’une simple application **Hello, Mac** qui compte le nombre de clics sur un bouton :

[![Exemple d’application Hello, Mac en cours d’exécution](hello-mac-images/run02-sml.png)](hello-mac-images/run02.png#lightbox)

Les concepts suivants sont abordés :

- **Visual Studio pour Mac** : introduction à Visual Studio pour Mac et comment l’utiliser pour créer des applications Xamarin.Mac.
- **Structure d’une application Xamarin.Mac** : en quoi consiste une application Xamarin.Mac.
- **Interface Builder de Xcode** : comment utiliser Interface Builder de Xcode pour définir l’interface utilisateur d’une application.
- **Outlets et actions** : comment utiliser les outlets et actions pour relier des contrôles dans l’interface utilisateur.
- **Déploiement/tests** : comment exécuter et tester une application Xamarin.Mac.

## <a name="requirements"></a>Spécifications

Le développement d’applications Xamarin.Mac nécessite :

- Un ordinateur Mac exécutant macOS High Sierra (10.13) ou une version ultérieure.
- [Xcode 10 ou version ultérieure](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
- La dernière version de [Xamarin.Mac et Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation/).

Pour exécuter une application générée avec Xamarin.Mac, vous aurez besoin des éléments suivants :

- Un ordinateur Mac exécutant macOS X 10.7 ou version ultérieure.

> [!WARNING]
> La prochaine version de Xamarin.Mac (4.8) prendra uniquement en charge macOS 10.9 ou ultérieur.
> Les versions précédentes de Xamarin.Mac prenaient en charge macOS 10.7 ou ultérieur, mais l’infrastructure TLS des anciennes versions de macOS n’était pas suffisante pour prendre en charge TLS 1.2. Pour cibler macOS 10.7 ou macOS 10.8, utilisez Xamarin.Mac 4.6 ou antérieur.

## <a name="starting-a-new-xamarinmac-app-in-visual-studio-for-mac"></a>Démarrage d’une nouvelle application Xamarin.Mac dans Visual Studio pour Mac

Comme indiqué ci-dessus, ce guide passe en revue les étapes pour créer une application Mac appelée `Hello_Mac` qui ajoute un seul bouton et une étiquette à la fenêtre principale. Quand vous cliquez sur le bouton, l’étiquette affiche le nombre de clics effectués.

Pour commencer, suivez les étapes ci-dessous :

1. Démarrez Visual Studio pour Mac :

    [![Interface de Visual Studio pour Mac principale](hello-mac-images/setup01-sml.png)](hello-mac-images/setup01.png#lightbox)

2. Cliquez sur le bouton **Nouveau projet...** pour ouvrir la boîte de dialogue **Nouveau projet**, puis sélectionnez **Mac** > **Application** > **Application Cocoa** et cliquez sur le bouton **Suivant** :

    [![Sélection d’une application de cacao](hello-mac-images/setup02-sml.png)](hello-mac-images/setup02.png#lightbox)

3. Entrez `Hello_Mac` pour **Nom de l’application** et gardez toutes les autres valeurs par défaut. Cliquez sur **suivant**:

    [![Définition du nom de l’application](hello-mac-images/setup03-sml.png)](hello-mac-images/setup03.png#lightbox)

4. Vérifiez l’emplacement du nouveau projet sur votre ordinateur :

    [![Vérification des détails de la nouvelle solution](hello-mac-images/setup04-sml.png)](hello-mac-images/setup04.png#lightbox)

5. Cliquez sur le bouton **Créer**.

Visual Studio pour Mac crée l’application Xamarin.Mac et affiche les fichiers par défaut qui sont ajoutés à la solution de l’application :

[![Nouvelle vue par défaut de la solution](hello-mac-images/project01-sml.png)](hello-mac-images/project01.png#lightbox)

Visual Studio pour Mac utilise la même structure **Solution** et **Projet** que Visual Studio 2019. Une solution est un conteneur qui peut contenir un ou plusieurs projets. les projets peuvent inclure des applications, des bibliothèques de prise en charge, des applications de test, etc. Le **fichier > nouveau** modèle de projet crée automatiquement une solution et un projet d’application.

## <a name="anatomy-of-a-xamarinmac-application"></a>Structure d’une application Xamarin.Mac

La programmation d’une application Xamarin.Mac est très similaire à l’utilisation de Xamarin.iOS. iOS utilise le framework CocoaTouch, qui est une version épurée de Cocoa utilisée par Mac.

Examinez les fichiers dans le projet :

- **Main.cs** ce fichier contient le point d’entrée principal de l’application. Quand l’application est lancée, la classe `Main` contient la toute première méthode qui est exécutée.
- **AppDelegate.cs** contient la classe `AppDelegate` qui est chargée d’écouter les événements du système d’exploitation.
- **Info.plist** contient des propriétés de l’application telles que le nom de l’application, les icônes, etc.
- **Entitlements.plist** contient les droits de l’application et permet d’accéder à des éléments tels que le sandboxing et la prise en charge d’iCloud.
- **Main.storyboard** définit l’interface utilisateur (fenêtres et menus) pour une application et prépare les interconnexions entre fenêtres par le biais de Segues. Les storyboards sont des fichiers XML qui contiennent la définition des vues (éléments de l’interface utilisateur). Ce fichier peut être créé et géré par Interface Builder à l’intérieur de Xcode.
- **ViewController.cs** est le contrôleur pour la fenêtre principale. Les contrôleurs seront abordés en détail dans un autre article, mais pour l’instant, un contrôleur peut être considéré comme étant le moteur principal de n’importe quelle vue particulière.
- **ViewController.designer.cs** contient le code de base qui facilite l’intégration à l’interface utilisateur de l’écran principal.

Les sections suivantes vont parcourir rapidement certains de ces fichiers. Ils seront examinés plus en détail ultérieurement, mais il est judicieux de comprendre leurs concepts de base dès maintenant.

### <a name="maincs"></a>Main.cs

Le fichier **Main.cs** est très simple. Il contient une méthode `Main` statique qui crée une instance d’application Xamarin.Mac et transmet le nom de la classe qui gère les événements du système d’exploitation, qui est ici la classe `AppDelegate` :

```csharp
using System;
using System.Drawing;
using Foundation;
using AppKit;
using ObjCRuntime;

namespace Hello_Mac
{
    class MainClass
    {
        static void Main (string[] args)
        {
            NSApplication.Init ();
            NSApplication.Main (args);
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

Le fichier `AppDelegate.cs` contient une classe `AppDelegate`, qui est chargée de créer des fenêtres et d’écouter les événements du système d’exploitation :

```csharp
using AppKit;
using Foundation;

namespace Hello_Mac
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

Ce code est probablement inconnu, sauf si le développeur a créé une application iOS auparavant, mais il est relativement simple.

La méthode `DidFinishLaunching` est exécutée après l’instanciation de l’application et elle est en fait chargée de créer la fenêtre de l’application et de commencer le processus d’affichage de la vue qu’elle contient.

La méthode `WillTerminate` est appelée quand l’utilisateur ou le système a instancié un arrêt de l’application. Le développeur doit utiliser cette méthode pour finaliser l’application avant de quitter (par exemple, l’enregistrement des préférences de l’utilisateur ou de l’emplacement et de la taille de fenêtre).

### <a name="viewcontrollercs"></a>ViewController.cs

Cocoa (et, par dérivation, CocoaTouch) utilise le modèle appelé MVC (*Model View Controller*). La déclaration `ViewController` représente l’objet qui contrôle la fenêtre d’application réelle. En règle générale, pour chaque fenêtre créée (et pour de nombreux autres éléments dans les fenêtres), il existe un contrôleur qui est chargé du cycle de vie de la fenêtre, par exemple son affichage, l’ajout de nouvelles vues (contrôles), etc.

La classe `ViewController` est le contrôleur de la fenêtre principale. Le contrôleur est responsable du cycle de vie de la fenêtre principale. Elle sera étudiée en détail ultérieurement ; pour l’instant, examinons-la rapidement :

```csharp
using System;

using AppKit;
using Foundation;

namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }
    }
}
```

### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

Le fichier designer pour la classe de fenêtre principale est initialement vide, mais il sera rempli automatiquement par Visual Studio pour Mac quand l’interface utilisateur sera créée avec Xcode Interface Builder :

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac to store outlets and
// actions made in the UI designer. If it is removed, they will be lost.
// Manual changes to this file may not be handled correctly.
//
using Foundation;

namespace Hello_Mac
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

Les fichiers designer ne doivent pas être modifiés directement, car ils sont gérés automatiquement par Visual Studio pour Mac pour fournir le code de base qui permet d’accéder aux contrôles ajoutés aux fenêtres ou vues de l’application.

Une fois le projet d’application Xamarin.Mac créé et quelques notions de base acquises sur ses composants, basculez vers Xcode pour créer l’interface utilisateur à l’aide d’Interface Builder.

### <a name="infoplist"></a>Info.plist

Le fichier `Info.plist` contient des informations sur l’application Xamarin.Mac telles que son **Nom** et son **Identificateur de bundle** :

[![L’éditeur Visual Studio pour Mac plist](hello-mac-images/infoplist01.png)](hello-mac-images/infoplist01.png#lightbox)

Il définit aussi le _storyboard_ qui sera utilisé pour afficher l’interface utilisateur de l’application Xamarin.Mac sous la liste déroulante **Interface principale**. Dans l’exemple ci-dessus, `Main` dans la liste déroulante fait référence à `Main.storyboard` dans l’arborescence source du projet dans **l’Explorateur de solutions**. Il définit également les icônes de l’application en spécifiant le *Catalogue de ressources* qui les contient (**AppIcon** ici).

### <a name="entitlementsplist"></a>Entitlements.plist

Le fichier `Entitlements.plist` de l’application contrôle les droits détenus par l’application Xamarin.Mac, par exemple **Sandboxing** et **iCloud** :

[![Éditeur de droits Visual Studio pour Mac](hello-mac-images/entitlements01.png)](hello-mac-images/entitlements01.png#lightbox)

Pour l’exemple Hello World, aucun droit n’est requis. La section suivante montre comment utiliser Interface Builder de Xcode pour modifier le fichier **Main.storyboard** et définir l’interface utilisateur de l’application Xamarin.Mac.

## <a name="introduction-to-xcode-and-interface-builder"></a>Introduction à Xcode et Interface Builder

Dans le cadre de Xcode, Apple a créé un outil appelé Interface Builder qui permet à un développeur de créer une interface utilisateur visuellement dans un concepteur. Xamarin.Mac s’intègre facilement à Interface Builder, ce qui permet de créer l’interface utilisateur avec les mêmes outils que les utilisateurs Objective-C.

Pour commencer, double-cliquez sur le fichier `Main.storyboard` dans l’**Explorateur de solutions** afin de l’ouvrir pour modification dans Xcode et Interface Builder :

[![Fichier main. Storyboard dans le Explorateur de solutions](hello-mac-images/xcode01.png)](hello-mac-images/xcode01.png#lightbox)

Cela doit lancer Xcode et ressembler à cette capture d’écran :

[![Vue Interface Builder Xcode par défaut](hello-mac-images/xcode02.png)](hello-mac-images/xcode02.png#lightbox)

Avant de commencer à concevoir l’interface, jetez un coup d’œil à Xcode pour mieux connaître les principales fonctionnalités qui seront utilisées.

> [!NOTE]
> Le développeur n’est pas obligé d’utiliser Xcode et Interface Builder pour créer l’interface utilisateur pour une application Xamarin.Mac ; l’interface utilisateur peut être créée directement à partir du code C#, mais cela n’entre pas dans le cadre de cet article. Par souci de simplicité, il utilisera Interface Builder pour créer l’interface utilisateur dans le reste de ce tutoriel.

### <a name="components-of-xcode"></a>Composants de Xcode

Lors de l’ouverture d’un fichier **.storyboard** dans Xcode à partir de Visual Studio pour Mac, il s’ouvre avec **Navigation dans le projet** sur la gauche, **Hiérarchie des interfaces** et **Éditeur d’interface** au milieu, et **Propriétés et utilitaires** sur la droite :

[![Les différentes sections de Interface Builder dans Xcode](hello-mac-images/xcode03.png)](hello-mac-images/xcode03.png#lightbox)

Les sections suivantes décrivent la fonction de chacune de ces fonctionnalités Xcode et comment les utiliser afin de créer l’interface pour une application Xamarin.Mac.

### <a name="project-navigation"></a>Navigation dans le projet

Lors de l’ouverture d’un fichier **.storyboard** pour modification dans Xcode, Visual Studio pour Mac crée un *fichier projet Xcode* en arrière-plan pour communiquer les modifications entre lui-même et Xcode. Par la suite, quand le développeur revient à Visual Studio pour Mac à partir de Xcode, toutes les modifications apportées à ce projet sont synchronisées avec le projet Xamarin.Mac par Visual Studio pour Mac.

La section de **navigation de projet** permet au développeur de naviguer entre tous les fichiers qui composent ce projet XCode de _shim_ . En règle générale, il est uniquement intéressé par les fichiers `.storyboard` de cette liste tels que `Main.storyboard`.

### <a name="interface-hierarchy"></a>Hiérarchie des interfaces

La section **Hiérarchie des interfaces** permet au développeur d’accéder facilement à plusieurs propriétés clés de l’interface utilisateur comme les **espaces réservés** et la **fenêtre** principale. Cette section peut être utilisée pour accéder aux éléments individuels (vues) qui composent l’interface utilisateur et ajuster la façon dont ils sont imbriqués en les faisant glisser dans la hiérarchie.

### <a name="interface-editor"></a>Éditeur d’interface

La section de l' **éditeur d’interface** fournit la surface sur laquelle l’interface utilisateur est graphiquement présentée. Faites glisser les éléments de la section **bibliothèque** de la section **Propriétés & utilitaires** pour créer la conception. Comme les éléments d’interface utilisateur (vues) sont ajoutés à l’aire de conception, ils sont ajoutés à la section **Hiérarchie des interfaces** dans l’ordre où ils apparaissent dans l’**Éditeur d’interface**.

### <a name="properties--utilities"></a>Propriétés et utilitaires

La section **Propriétés et utilitaires** est divisée en deux sections principales, **Propriétés** (également appelées inspecteurs) et **Bibliothèque** :

[![Inspecteur des propriétés](hello-mac-images/xcode04.png)](hello-mac-images/xcode04.png#lightbox)

Initialement, cette section est pratiquement vide, mais si le développeur sélectionne un élément dans **Éditeur d’interface** ou **Hiérarchie des interfaces**, la section **Propriétés** sera remplie avec des informations sur l’élément donné et les propriétés qui peuvent être ajustées.

Dans la section **Propriétés**, il existe huit *onglets d’inspecteurs* différents, comme indiqué dans l’illustration suivante :

[![Vue d’ensemble de tous les inspecteurs](hello-mac-images/xcode05.png)](hello-mac-images/xcode05.png#lightbox)

### <a name="properties--utility-types"></a>Types de propriétés et d’utilitaires

De gauche à droite, ces onglets sont :

- **File Inspector** (Inspecteur de fichier) : l’onglet Inspecteur de fichier affiche des informations sur le fichier, telles que l’emplacement et le nom du fichier Xib qui est en cours de modification.
- **Aide rapide** : l’onglet Aide rapide fournit une aide contextuelle basée sur la sélection dans Xcode.
- **Identity Inspector** (Inspecteur d’identité) : l’onglet Inspecteur d’identité fournit des informations sur la vue/le contrôle sélectionné.
- **Attributes Inspector** (Inspecteur d’attributs) : l’onglet Inspecteur d’attributs permet au développeur de personnaliser différents attributs de la vue/du contrôle sélectionné.
- **Size Inspector** (Inspecteur de taille) : l’onglet Inspecteur de taille permet au développeur de contrôler la taille et le comportement de redimensionnement de la vue/du contrôle sélectionné.
- **Connections Inspector** (Inspecteur de connexions) : l’onglet Inspecteur de connexions affiche les connexions des **outlets** et **actions** des contrôles sélectionnés. Les outlets et actions sont abordés en détail ci-dessous.
- **Bindings Inspector** (Inspecteur de liaisons) : l’onglet Inspecteur de liaisons permet au développeur de configurer des contrôles afin que leurs valeurs soient automatiquement liées aux modèles de données.
- **View Effects Inspector** (Inspecteur d’effets sur les vues) : l’onglet Inspecteur d’effets sur les vues permet au développeur de spécifier des effets sur les contrôles, tels que les animations.

Utilisez la section **Bibliothèque** pour rechercher des contrôles et objets à placer dans le concepteur afin de générer graphiquement l’interface utilisateur :

[![Inspecteur de bibliothèque Xcode](hello-mac-images/xcode06.png)](hello-mac-images/xcode06.png#lightbox)

## <a name="creating-the-interface"></a>Création de l'interface utilisateur

Les principes de base de l’IDE Xcode et d’Interface Builder couverts, le développeur peut créer l’interface utilisateur pour la vue principale.

Suivez ces étapes pour utiliser Interface Builder :

1. Dans Xcode, faites glisser un **bouton de commande** à partir de la **section Bibliothèque** :

    [![Sélection d’un NSButton à partir de l’inspecteur de bibliothèque](hello-mac-images/xcode07.png)](hello-mac-images/xcode07.png#lightbox)

2. Placez le bouton sur la **vue** (sous le **Contrôleur de fenêtre**) dans l’**Éditeur d’interface** :

    [![Ajout d’un bouton à la conception de l’interface](hello-mac-images/xcode08.png)](hello-mac-images/xcode08.png#lightbox)

3. Cliquez sur la propriété **Titre** dans **l’inspecteur d’attributs** et remplacez le titre du bouton par **Click Me** :

    [![](hello-mac-images/xcode09.png "Setting the button's properties")](hello-mac-images/xcode09.png#lightbox)

4. Faites glisser une **étiquette** à partir de la **section Bibliothèque** :

    [![Sélection d’une étiquette à partir de l’inspecteur de bibliothèque](hello-mac-images/xcode10.png)](hello-mac-images/xcode10.png#lightbox)

5. Placez l’étiquette sur la **fenêtre** à côté du bouton dans l’**Éditeur d’interface** :

    [![Ajout d’une étiquette à la conception de l’interface](hello-mac-images/xcode11.png)](hello-mac-images/xcode11.png#lightbox)

6. Saisissez la poignée droite de l’étiquette et faites-la glisser jusqu’à ce qu’elle soit près du bord de la fenêtre :

    [![Redimensionnement de l’étiquette](hello-mac-images/xcode12.png)](hello-mac-images/xcode12.png#lightbox)

7. Sélectionnez le bouton que vous venez d’ajouter dans l’**Éditeur d’interface**, puis cliquez sur l’icône **Constraints Editor** (Éditeur de contraintes) au bas de la fenêtre :

    [![Ajout de contraintes au bouton](hello-mac-images/xcode13.png)](hello-mac-images/xcode13.png#lightbox)

8. En haut de l’éditeur, cliquez sur les **traits rouges en forme de I** en haut et à gauche. Quand la fenêtre est redimensionnée, le bouton reste au même emplacement dans le coin supérieur gauche de l’écran.

9. Vérifiez ensuite les zones **Hauteur** et **Largeur**, et utilisez les tailles par défaut. Le bouton conserve la même taille quand la fenêtre est redimensionnée.

10. Cliquez sur le bouton **Add 4 Constraints** (Ajouter 4 contraintes) pour ajouter les contraintes et fermer l’éditeur.

11. Sélectionnez l’étiquette, puis cliquez à nouveau sur l’icône de l' **éditeur de contraintes** :

    [![Ajout de contraintes à l’étiquette](hello-mac-images/xcode14.png)](hello-mac-images/xcode14.png#lightbox)

12. Un clic sur les **traits rouges en forme de I** en haut, à droite et à gauche de l’**Éditeur de contraintes** indique à l’étiquette de rester aux emplacements X et Y donnés, et d’augmenter ou de réduire sa taille quand la fenêtre est redimensionnée dans l’application en cours d’exécution.

13. Là encore, vérifiez la zone **Hauteur** et utilisez la taille par défaut, puis cliquez sur le bouton **Ajouter 4 contraintes** pour ajouter les contraintes et fermer l’éditeur.

14. Enregistrez les modifications apportées à l’interface utilisateur.

Lors du redimensionnement et du déplacement des contrôles, notez qu’Interface Builder donne de brèves indications utiles basées sur [Human Interface Guidelines pour macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/). Ces instructions aident le développeur à créer des applications de haute qualité avec un aspect familier pour les utilisateurs Mac.

Examinez la section **Hiérarchie des interfaces** pour connaître la manière dont la disposition et la hiérarchie des éléments qui composent l’interface utilisateur sont affichés :

[![Sélection d’un élément dans la hiérarchie d’interface](hello-mac-images/xcode15.png)](hello-mac-images/xcode15.png#lightbox)

À partir de là, le développeur peut sélectionner des éléments à modifier ou à déplacer pour réorganiser les éléments d’interface utilisateur, si nécessaire. Par exemple, si un élément d’interface utilisateur a été couvert par un autre élément, il peut le faire glisser vers le bas de la liste pour qu’il devienne le premier élément de la fenêtre.

Une fois l’interface utilisateur créée, le développeur doit exposer les éléments d’interface utilisateur pour que Xamarin.Mac puisse y accéder et interagir avec eux dans le code C#. La section suivante, **Outlets et actions**, montre comment procéder.

### <a name="outlets-and-actions"></a>Outlets et actions

Que sont les **outlets** et **actions** ? Dans la programmation de l’interface utilisateur .NET traditionnelle, un contrôle dans l’interface utilisateur est automatiquement exposé en tant que propriété quand il est ajouté. Le fonctionnement est différent dans Mac : le simple ajout d’un contrôle à une vue ne le rend pas accessible au code. Le développeur doit exposer explicitement l’élément d’interface utilisateur au code. Pour ce faire, Apple fournit deux options :

- **Outlets** : les outlets ressemblent à des propriétés. Si le développeur relie un contrôle à un outlet, il est exposé au code via une propriété afin de pouvoir effectuer des opérations comme attacher des gestionnaires d’événements, appeler des méthodes, etc.
- **Actions** : les actions ressemblent au modèle de commande dans WPF. Par exemple, quand une action est effectuée sur un contrôle, comme un clic sur un bouton, le contrôle appelle automatiquement une méthode dans le code. Les actions sont efficaces et pratiques, car le développeur peut associer plusieurs contrôles à la même action.

Dans Xcode, les **outlets** et **actions** sont ajoutés directement dans le code via un *déplacement des contrôles*. Plus précisément, cela signifie que, pour créer un **outlet** ou une **action**, le développeur choisit un élément de contrôle auquel ajouter un **outlet** ou une **action**, maintient la touche **Ctrl** enfoncée sur le clavier et fait glisser ce contrôle directement dans le code.

Pour les développeurs Xamarin.Mac, cela signifie que le développeur fait glisser dans le stub Objective-C des fichiers qui correspondent au fichier C# à l’endroit où il souhaite créer l’**outlet** ou l’**action**. Visual Studio pour Mac a créé un fichier appelé `ViewController.h` dans le cadre du projet Xcode shim qu’il a généré pour utiliser Interface Builder :

[![Affichage de la source dans Xcode](hello-mac-images/xcode16-sml.png)](hello-mac-images/xcode16.png#lightbox)

Ce fichier stub `.h` reflète le fichier `ViewController.designer.cs` ajouté automatiquement à un projet Xamarin.Mac quand un `NSWindow` est créé. Ce fichier permet de synchroniser les modifications apportées par Interface Builder et représente l’emplacement où les **outlets** et **actions** sont créés afin que les éléments d’interface utilisateur soient exposés au code C#.

#### <a name="adding-an-outlet"></a>Ajout d’un outlet

Une fois quelques notions de base acquises sur les **outlets** et **actions**, créez un **outlet** pour exposer l’étiquette créée à notre code C#.

Effectuez les actions suivantes :

1. Dans Xcode, dans le coin supérieur tout à droite de l’écran, cliquez sur le bouton **Double Circle** (Cercle double) pour ouvrir l’**Assistant Editor** (Éditeur d’Assistant) :

    [![Affichage de l’éditeur de l’Assistant](hello-mac-images/outlet01.png)](hello-mac-images/outlet01.png#lightbox)

2. Xcode passe en mode fractionné avec l’**Éditeur d’interface** d’un côté et un **Éditeur de code** de l’autre.

3. Notez que Xcode a automatiquement sélectionné le fichier **ViewController.m** dans l’**Éditeur de code**, ce qui est incorrect. Suite à la discussion sur la définition des **outlets** et **actions** ci-dessus, le développeur doit sélectionner le fichier **ViewController.h**.

4. En haut de l’**Éditeur de code** cliquez sur le **Automatic Link** (Lien automatique) et sélectionnez le fichier `ViewController.h` :

    [![Sélection du fichier approprié](hello-mac-images/outlet02.png)](hello-mac-images/outlet02.png#lightbox)

5. Le fichier approprié doit maintenant être sélectionné pour Xcode :

    [![Affichage du fichier ViewController. h](hello-mac-images/outlet03.png)](hello-mac-images/outlet03.png#lightbox)

6. **La dernière étape était très importante !** : si vous n’avez pas sélectionné le fichier approprié, vous ne pourrez pas créer des **outlets** ni des **actions**, ou ils seront exposés à la classe incorrecte en C# !

7. Dans l’**Éditeur d’interface**, maintenez la touche **Ctrl** enfoncée sur le clavier, puis cliquez et faites glisser l’étiquette créée précédemment sur l’éditeur de code sous le code `@interface ViewController : NSViewController {}` :

    [![Glissement pour créer un orifice](hello-mac-images/outlet04.png)](hello-mac-images/outlet04.png#lightbox)

8. Une boîte de dialogue s’affiche. Laissez l’option **Connexion** définie sur **Outlet** et entrez `ClickedLabel` pour **Nom** :

    [![Définition de la sortie](hello-mac-images/outlet05.png)](hello-mac-images/outlet05.png#lightbox)

9. Cliquez sur le bouton **Se connecter** pour créer l’**outlet** :

    [![Affichage de la sortie finale](hello-mac-images/outlet06.png)](hello-mac-images/outlet06.png#lightbox)

10. Enregistrez les modifications du fichier.

#### <a name="adding-an-action"></a>Ajout d’une action

Ensuite, exposez le bouton au code C#. Tout comme l’étiquette ci-dessus, le développeur peut associer le bouton à un **outlet**. Étant donné que nous voulons uniquement répondre au bouton sur lequel le clic est effectué, utilisez une **action** à la place.

Effectuez les actions suivantes :

1. Vérifiez que Xcode est encore dans l’**Éditeur d’Assistant** et que le fichier **ViewController.h** est visible dans l’**Éditeur de code**.

2. Dans l’**Éditeur d’interface**, maintenez la touche **Ctrl** enfoncée sur le clavier, puis cliquez et faites glisser le bouton créé précédemment sur l’éditeur de code sous le code `@property (assign) IBOutlet NSTextField *ClickedLabel;` :

    [![Glissement pour créer une action](hello-mac-images/action01.png)](hello-mac-images/action01.png#lightbox)

3. Remplacez le type de **Connexion** par **Action** :

    [![Définition de l’action](hello-mac-images/action02.png)](hello-mac-images/action02.png#lightbox)

4. Entrez `ClickedButton` comme **Nom** :

    [![Attribution d’un nom à la nouvelle action](hello-mac-images/action03.png)](hello-mac-images/action03.png#lightbox)

5. Cliquez sur le bouton **Se connecter** pour créer l’**action** :

    [![Affichage de l’action finale](hello-mac-images/action04.png)](hello-mac-images/action04.png#lightbox)

6. Enregistrez les modifications du fichier.

Avec l’interface utilisateur reliée et exposée au code C#, revenez à Visual Studio pour Mac et laissez-le synchroniser les modifications effectuées dans Xcode et Interface Builder.

> [!NOTE]
> Il a probablement fallu beaucoup de temps pour créer l’interface utilisateur et les **prises** et **actions** pour cette première application, et cela peut sembler un travail considérable, mais un grand nombre de nouveaux concepts ont été introduits et beaucoup de temps ont été consacrés à la nouvelle terre. Après avoir utilisé Interface Builder pendant un certain temps, cette interface ainsi que l’ensemble des **outlets** et **actions** peuvent être créés en seulement une ou deux minutes.

### <a name="synchronizing-changes-with-xcode"></a>Synchronisation des modifications avec Xcode

Quand le développeur revient à Visual Studio pour Mac à partir de Xcode, toutes les modifications apportées à Xcode sont automatiquement synchronisées avec le projet Xamarin.Mac.

Sélectionnez le fichier **ViewController.designer.cs** dans l’**Explorateur de solutions** pour voir comment l’**outlet** et l’**action** ont été reliés dans le code C# :

[![Synchronisation des modifications avec Xcode](hello-mac-images/sync01-sml.png)](hello-mac-images/sync01.png#lightbox)

Notez comment les deux définitions dans le fichier **ViewController.designer.cs** :

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

S’alignent sur les définitions dans le fichier `ViewController.h` dans Xcode :

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Visual Studio pour Mac écoute les modifications apportées au fichier **.h**, puis synchronise automatiquement ces modifications dans le fichier **. designer.cs** respectif pour les exposer à l’application. Notez que **ViewController.designer.cs** étant une classe partielle, Visual Studio pour Mac n’a pas à modifier **ViewController.cs**, ce qui remplacerait toutes les modifications que le développeur a apportées à la classe.

Normalement, le développeur ne devra jamais ouvrir **ViewController.designer.cs** ; il est présenté ici à des fins pédagogiques uniquement.

> [!NOTE]
> Dans la plupart des cas, Visual Studio pour Mac détecte automatiquement les modifications effectuées dans Xcode et les synchronise avec le projet Xamarin.Mac. Dans le cas isolé où la synchronisation ne se produit pas automatiquement, revenez à Xcode, puis à nouveau à Visual Studio pour Mac. Cela déclenche normalement un cycle de synchronisation.

## <a name="writing-the-code"></a>Écriture du code

Avec l’interface utilisateur créée et ses éléments d’interface utilisateur exposés au code via des **prises** et des **actions**, nous sommes enfin prêts à écrire le code pour donner vie au programme.

Pour cet exemple d’application, à chaque clic sur le premier bouton, l’étiquette est actualisée pour afficher le nombre de clics effectués. Pour ce faire, ouvrez le fichier `ViewController.cs` pour modification en double-cliquant dessus dans l’**Explorateur de solutions** :

[![Affichage du fichier ViewController.cs dans Visual Studio pour Mac](hello-mac-images/code01-sml.png)](hello-mac-images/code01.png#lightbox)

Tout d’abord, créez une variable de niveau classe dans la classe `ViewController` pour effectuer le suivi du nombre de clics effectués. Modifiez la définition de classe pour qu’elle ressemble à ce qui suit :

```csharp
namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

Ensuite, dans la même classe (`ViewController`), remplacez la méthode `ViewDidLoad` et ajoutez du code afin de définir le message initial pour l’étiquette :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Utilisez `ViewDidLoad` au lieu d’une autre méthode comme `Initialize`, car `ViewDidLoad` est appelée *après* que le système d’exploitation a chargé et instancié l’interface utilisateur à partir du fichier **.storyboard**. Si le développeur tente d’accéder au contrôle label avant le chargement et l’instanciation complets du fichier **.storyboard**, il obtient une erreur `NullReferenceException`, car le contrôle label n’existe pas encore.

Ensuite, ajoutez le code pour répondre à l’utilisateur qui clique sur le bouton. Ajoutez la méthode partielle suivante à la classe `ViewController` :

```csharp
partial void ClickedButton (Foundation.NSObject sender) {
    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Ce code est associé à l’**action** créée dans Xcode et Interface Builder, et est appelé chaque fois que l’utilisateur clique sur le bouton.

## <a name="testing-the-application"></a>Test de l’application

Il est temps de générer et d’exécuter l’application pour vérifier qu’elle s’exécute comme prévu. Le développeur peut la générer et l’exécuter en une seule étape, ou il peut la générer sans l’exécuter.

Chaque fois qu’une application est générée, le développeur peut choisir le type de génération :

- **Debug** : une version Debug est compilée dans un fichier **.app** (application) avec un ensemble de métadonnées supplémentaires qui permettent au développeur de procéder au débogage pendant que l’application est en cours d’exécution.
- **Release** : une version Release crée également un fichier **.app**, mais qui n’inclut pas les informations de débogage ; il est donc plus petit et s’exécute plus rapidement.

Le développeur peut sélectionner le type de génération à partir du **Sélecteur de configurations** dans le coin supérieur gauche de l’écran Visual Studio pour Mac :

[![Sélection d’une version Debug](hello-mac-images/run01-sml.png)](hello-mac-images/run01.png#lightbox)

## <a name="building-the-application"></a>Génération de l'application

Dans le cas de cet exemple, comme nous voulons simplement une version Debug, vérifiez que l’option **Debug** est sélectionnée. Générez d’abord l’application en appuyant sur **⌘+B** ou, à partir du menu **Générer **, choisissez **Générer tout**.

Si aucune erreur ne s’affiche, un message **La génération a réussi** apparaît dans la barre d’état Visual Studio pour Mac. Si des erreurs s’affichent, passez en revue le projet et vérifiez que les étapes décrites ci-dessus ont été correctement suivies. Commencez par confirmer que le code (à la fois dans Xcode et dans Visual Studio pour Mac) correspond au code dans le didacticiel.

## <a name="running-the-application"></a>Exécution de l'application

Il existe trois façons d’exécuter l’application :

- Appuyez sur **⌘ + entrée**.
- Dans le menu **Exécuter**, choisissez **Déboguer**.
- Cliquez sur le bouton **Play** (Lire) dans la barre d’outils Visual Studio pour Mac (juste au-dessus de l’**Explorateur de solutions**).

L’application est générée (si ce n’est pas déjà fait), démarre en mode débogage et affiche sa fenêtre d’interface principale :

[![Exécution de l'application](hello-mac-images/run02-sml.png)](hello-mac-images/run02.png#lightbox)

Si plusieurs clics sont effectués sur le bouton, l’étiquette doit être mise à jour avec le nombre :

[![Indication des résultats d’un clic sur le bouton](hello-mac-images/run03-sml.png)](hello-mac-images/run03.png#lightbox)

## <a name="where-to-next"></a>Étapes suivantes

Les principes de base de l’utilisation d’une application Xamarin.Mac étant acquis, examinez les documents suivants pour approfondir vos connaissances :

- [Introduction aux storyboards](~/mac/platform/storyboards/index.md) : Cet article présente l’utilisation des storyboards dans une application Xamarin.Mac. Il aborde la création et la gestion de l’interface utilisateur de l’application à l’aide de storyboards et d’Interface Builder de Xcode.
- [Fenêtres](~/mac/user-interface/window.md) : Cet article aborde l’utilisation des fenêtres et des panneaux dans une application Xamarin.Mac. Il aborde la création et la gestion des fenêtres et des panneaux dans Xcode et Interface Builder, le chargement des fenêtres et des panneaux à partir des fichiers .xib, l’utilisation des fenêtres et la réponse aux fenêtres en code C#.
- [Boîtes de dialogue](~/mac/user-interface/dialog.md) : Cet article aborde l’utilisation des boîtes de dialogue et des fenêtres modales dans une application Xamarin.Mac. Il aborde la création et la gestion des fenêtres modales dans Xcode et Interface Builder, l’utilisation des boîtes de dialogue standard, l’affichage des fenêtres et la réponse aux fenêtres en code C#.
- [Alertes](~/mac/user-interface/alert.md) : Cet article aborde l’utilisation des alertes dans une application Xamarin.Mac. Il aborde la création et l’affichage des alertes à partir de code C# ainsi que la réponse aux alertes.
- [Menus](~/mac/user-interface/menu.md) : les menus sont utilisés dans différentes parties de l’interface utilisateur d’une application Mac, du menu principal de l’application en haut de l’écran jusqu’aux menus contextuels qui peuvent apparaître n’importe où dans une fenêtre. Les menus sont une partie intégrante de l’expérience utilisateur d’une application Mac. Cet article aborde l’utilisation des menus Cocoa dans une application Xamarin.Mac.
- [Barres d’outils](~/mac/user-interface/toolbar.md) : Cet article aborde l’utilisation des barres d’outils dans une application Xamarin.Mac. Il aborde la création et la maintenance. Les barres d’outils dans Xcode et Interface Builder, l’exposition des éléments de barres d’outils au code à l’aide des outlets et actions, l’activation et la désactivation des éléments de barres d’outils, et enfin la réponse aux éléments de barres d’outils en code C#.
- [Vues Table](~/mac/user-interface/table-view.md) : Cet article aborde l’utilisation des vues Table dans une application Xamarin.Mac. Il aborde la création et la gestion des vues Table dans Xcode et Interface Builder, l’exposition des éléments des vues Table au code à l’aide des outlets et actions, le remplissage des éléments des vues Table et enfin la réponse aux éléments des vues Table en code C#.
- [Modes Plan](~/mac/user-interface/outline-view.md) : Cet article aborde l’utilisation des modes Plan dans une application Xamarin.Mac. Il aborde la création et la gestion des modes Plan dans Xcode et Interface Builder, l’exposition des éléments des modes Plan au code à l’aide des outlets et actions, le remplissage des éléments des modes Plan et enfin la réponse aux éléments des modes Plan en code C#.
- [Listes de sources](~/mac/user-interface/source-list.md) : Cet article aborde l’utilisation des listes de sources dans une application Xamarin.Mac. Il aborde la création et la gestion des listes de sources dans Xcode et Interface Builder, l’exposition des éléments des listes de sources au code à l’aide des outlets et actions, le remplissage des éléments des listes de sources et enfin la réponse aux éléments des listes de sources en code C#.
- [Vues des collections](~/mac/user-interface/collection-view.md) : Cet article aborde l’utilisation des vues des collections dans une application Xamarin.Mac. Il aborde la création et la gestion des vues des collections dans Xcode et Interface Builder, l’exposition des éléments des vues des collections au code à l’aide des outlets et actions, le remplissage des vues des collections et enfin la réponse aux vues des collections en code C#.
- [Utilisation des images](~/mac/app-fundamentals/image.md) : Cet article aborde l’utilisation des images et icônes dans une application Xamarin.Mac. Il aborde la création et la gestion des images nécessaires pour créer l’icône d’une application et l’utilisation des images à la fois dans le code C# et dans Interface Builder de Xcode.

La [Galerie d’exemples Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac) contient des exemples de code prêts à l’emploi pour vous aider à vous familiariser avec Xamarin.Mac.

[L’exemple d’application SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter) est un exemple d’une application Xamarin.Mac complète qui inclut une grande partie des fonctionnalités qu’un utilisateur s’attend à trouver dans une application Mac classique. SourceWriter est un éditeur de code source simple qui assure la prise en charge de la complétion de code et de la coloration syntaxique de base.

Le code SourceWriter a été entièrement commenté et, le cas échéant, des liens ont été fournis à partir des principales technologies ou méthodes vers des informations pertinentes dans la documentation Xamarin.Mac.

## <a name="summary"></a>Résumé

Cet article a couvert les principes de base d’une application Xamarin.Mac standard. Il a abordé la création d’une application dans Visual Studio pour Mac, la conception de l’interface utilisateur dans Xcode et Interface Builder, l’exposition des éléments d’interface utilisateur au code C# à l’aide des **outlets** et **actions**, l’ajout de code pour utiliser les éléments d’interface utilisateur et, enfin, la génération et le test d’une application Xamarin.Mac.

## <a name="related-links"></a>Liens associés

- [Hello, Mac (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/hello-mac)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
