---
title: Windows dans Xamarin.Mac
description: Cet article aborde l’utilisation des fenêtres et des panneaux dans une application Xamarin.Mac. Il décrit la création des fenêtres et des panneaux dans Xcode et Interface Builder, les charger à partir des tables de montage séquentiel et les fichiers .xib et leur utilisation par programmation.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f23a506acda385b53942661244bcd7a8510c8c7f
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865997"
---
# <a name="windows-in-xamarinmac"></a>Windows dans Xamarin.Mac

_Cet article aborde l’utilisation des fenêtres et des panneaux dans une application Xamarin.Mac. Il décrit la création des fenêtres et des panneaux dans Xcode et Interface Builder, les charger à partir des tables de montage séquentiel et les fichiers .xib et leur utilisation par programmation._

Lorsque vous travaillez avec C# et .NET dans une application Xamarin.Mac, vous avez accès à la même Windows et panneaux qui un développeur travaillant *Objective-C* et *Xcode* est. Comme Xamarin.Mac s’intègre directement à Xcode, vous pouvez utiliser de Xcode _Interface Builder_ pour créer et maintenir les panneaux et Windows (ou éventuellement pour les créer directement en code c#).

En fonction de son objectif, une application Xamarin.Mac peut présenter un ou plusieurs Windows sur l’écran pour gérer et coordonner les informations qu’il affiche et fonctionne avec. Les principales fonctions d’une fenêtre sont :

1. Pour fournir une zone dans laquelle les vues et les contrôles peuvent être placés et gérées.
2. Pour accepter et répondre aux événements en réponse à une interaction utilisateur avec le clavier et la souris.

Windows peut être utilisé dans un état non modal (par exemple, un éditeur de texte qui peut ouvrir plusieurs documents à la fois) ou modale (par exemple, une boîte de dialogue d’exportation qui doit être fermée avant que l’application peut continuer).

Les panneaux sont un type spécial de fenêtre (une sous-classe de la base de `NSWindow` classe), qui servent généralement une fonction auxiliaire dans une application, telles que des fenêtres d’utilitaire telles que les inspecteurs de format de texte et de système de sélecteur de couleurs.

[![](window-images/intro01.png "Modification d’une fenêtre dans Xcode")](window-images/intro01.png#lightbox)

Dans cet article, nous traiterons les notions de base de l’utilisation de Windows et des panneaux dans une application Xamarin.Mac. Il est fortement recommandé que vous travaillez via le [Hello, Mac](~/mac/get-started/hello-mac.md) article tout d’abord, en particulier le [Introduction à Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et [Outlets et Actions](~/mac/get-started/hello-mac.md#outlets-and-actions) sections, tel qu’il couvre les principaux concepts et techniques que nous utilisons dans cet article.

Vous pouvez souhaiter de jeter un coup de œil à la [C# exposition de classes / méthodes vers Objective-C](~/mac/internals/how-it-works.md) section de la [éléments internes de Xamarin.Mac](~/mac/internals/how-it-works.md) de document, il explique le `Register` et `Export` commandes utilisé pour AutoEventWireup vos classes c# pour les objets Objective-C et les éléments d’interface utilisateur.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introduction à Windows

Comme indiqué ci-dessus, une fenêtre fournit une zone dans laquelle les vues et les contrôles peuvent être placés et gérées et répond aux événements en fonction de l’interaction de l’utilisateur (soit via le clavier ou la souris).

En fonction d’Apple, il existe cinq types de Windows dans une application de macOS :

- **Fenêtre de document** -une fenêtre de document contient des données utilisateur basée sur le fichier comme une feuille de calcul ou un document texte.
- **Fenêtre de l’application** -une fenêtre de l’application est la fenêtre principale d’une application qui n’est pas en fonction de document (par exemple, l’application de calendrier sur un Mac).
- **Panneau** : un panneau flotte au-dessus des autres fenêtres et fournit des outils ou ouvrir des contrôles utilisateurs peuvent travailler avec si des documents sont. Dans certains cas, un panneau peut être translucide (par exemple quand utilisation des graphiques volumineux).
- **Boîte de dialogue** -une boîte de dialogue s’affiche en réponse à une action de l’utilisateur et fournit généralement des utilisateurs de façons de procéder à l’action. Une boîte de dialogue nécessite une réponse de l’utilisateur avant d’être fermée. (Consultez [utilisation des boîtes de dialogue](~/mac/user-interface/dialog.md))
- **Alertes** -une alerte est un type spécial de boîte de dialogue qui s’affiche lorsqu’un problème grave se produit (par exemple, une erreur) ou en tant qu’avertissement (par exemple, la préparation de la suppression d’un fichier). Une alerte étant une boîte de dialogue, elle requiert également une réponse de l’utilisateur avant d’être fermée. (Consultez [utilisation des alertes](~/mac/user-interface/alert.md))

Pour plus d’informations, consultez le [sur Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) section d’Apple [X Human Interface Guidelines pour OS](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Principal, la clé et Windows Inactive

Windows dans une application Xamarin.Mac peut rechercher et se comportent différemment en fonction de la façon dont l’utilisateur est actuellement interagir avec elles. Le Document ou la fenêtre de l’application qui est actuellement le focus de l’attention des utilisateurs plus importante est appelée le _fenêtre principale_. Dans la plupart des instances de cette fenêtre sera également le _fenêtre clé_ (la fenêtre qui reçoit actuellement l’entrée d’utilisateur). Mais ce n’est pas toujours le cas, par exemple, un sélecteur de couleurs peut être ouvert et être la fenêtre de la clé qui interagit avec l’utilisateur pour modifier l’état d’un élément dans la fenêtre de Document (ce qui serait toujours la fenêtre principale).

La principale et une clé Windows (si elles sont séparées) sont toujours actives, _Windows Inactive_ sont des fenêtres ouvertes qui ne sont pas au premier plan. Par exemple, une application d’éditeur de texte peut avoir plus d’un document ouvert à la fois, seule la fenêtre principale est donc active, tous les autres seraient inactifs. 

Pour plus d’informations, consultez le [sur Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) section d’Apple [X Human Interface Guidelines pour OS](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Windows d’affectation de noms

Une fenêtre peut afficher une barre de titre et lorsque le titre est affiché, il est généralement le nom de l’application, le nom du document en cours de traitement ou la fonction de la fenêtre (par exemple, l’inspecteur). Certaines applications ne pas afficher une barre de titre, car elles sont identifiables par la vue et ne fonctionnent pas avec les documents.

Apple suggère les recommandations suivantes :

- Utilisez le nom de votre application pour le titre d’une fenêtre principale, non de document. 
- Nom d’une nouvelle fenêtre de document `untitled`. Pour le premier nouveau document, ne pas ajouter un numéro au titre (comme `untitled 1`). Si l’utilisateur crée un autre document avant d’enregistrer et des titres de la première, appelez cette fenêtre `untitled 2`, `untitled 3`, etc.

Pour plus d’informations, consultez le [Windows d’affectation de noms](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) section d’Apple [X Human Interface Guidelines pour OS](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Plein écran Windows

Dans macOS, fenêtre de l’application peut accéder masquage de plein écran tout y compris la barre de menus d’Application (qui peut être affichée en déplaçant le curseur vers le haut de l’écran) pour fournir de vous laisser distraire par interaction gratuite avec il est contenue.

Apple suggère les recommandations suivantes :

- Déterminer s’il est judicieux d’une fenêtre en plein écran. Les applications qui fournissent des interactions brèves (par exemple, une calculatrice) ne doit pas fournir un mode plein écran.
- Afficher la barre d’outils si la tâche de plein écran exige. En règle générale, la barre d’outils est masquée en mode plein écran.
- La fenêtre en plein écran doit avoir tous les utilisateurs de fonctionnalités à effectuer la tâche.
- Si possible, évitez d’interaction du Finder pendant que l’utilisateur est dans une fenêtre plein écran.
- Tirez parti de l’espace d’écran accrue sans décalage de se concentrer sur la tâche principale.

Pour plus d’informations, consultez le [plein écran Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) section d’Apple [X Human Interface Guidelines pour OS](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Panneaux

Un panneau est une fenêtre auxiliaire qui contient des contrôles et des options qui affectent le document actif ou la sélection (par exemple, le sélecteur de couleurs système) :

[![](window-images/panel01.png "Un panneau de couleurs")](window-images/panel01.png#lightbox)

Panneaux peuvent indiquer qu’il _spécifiques de l’application_ ou _tout le système_. Les panneaux spécifiques de l’application flotter au-dessus des fenêtres de document de l’application et disparaissent lorsque l’application est en arrière-plan. Les panneaux du système (telles que la **polices** Panneau de configuration), float sur toutes les fenêtres actives, quel que soit l’application. 

Apple suggère les recommandations suivantes :

- En général, utilisez un panneau de configuration standard, les panneaux transparents ne doivent être utilisées avec parcimonie et pour les tâches gourmandes en ressources graphiques.
- Envisagez d’utiliser un panneau pour permettre aux utilisateurs un accès facile aux contrôles importants ou des informations qui affecte directement leur tâche.
- Masquer et afficher les panneaux en fonction des besoins.
- Panneaux doivent toujours inclure la barre de titre.
- Panneaux ne doivent pas inclure un bouton de réduction actif.

#### <a name="inspectors"></a>Inspecteurs

La plupart des applications macOS présentent contrôles auxiliaires et options qui affectent le document actif ou la sélection en tant que _inspecteurs_ qui font partie de la fenêtre principale (comme le **Pages** application indiquée ci-dessous), au lieu d’utiliser le panneau de configuration Windows :

[![](window-images/panel02.png "Un inspecteur de l’exemple")](window-images/panel02.png#lightbox)

Pour plus d’informations, consultez le [panneaux](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) section d’Apple [X Human Interface Guidelines pour OS](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) et notre [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) exemple d’application pour une implémentation complète d’un **Inspecteur Interface** dans une application Xamarin.Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Création et la maintenance de Windows dans Xcode

Lorsque vous créez une nouvelle application Xamarin.Mac Cocoa, vous obtenez une fenêtre vide, standard par défaut. Ce paramètre, windows est défini dans un `.storyboard` fichier automatiquement inclus dans le projet. Pour modifier votre conception de windows, dans le **l’Explorateur de solutions**, double-cliquez sur le `Main.storyboard` fichier :

[![](window-images/edit01.png "En sélectionnant le storyboard principal")](window-images/edit01.png#lightbox)

La conception de la fenêtre s’ouvre dans Xcode Interface Builder :

[![](window-images/edit02.png "Modification de l’interface utilisateur dans Xcode")](window-images/edit02.png#lightbox)

Dans le **inspecteur d’attributs**, il existe plusieurs propriétés que vous pouvez utiliser pour définir et contrôler votre fenêtre :

- **Titre** -il s’agit du texte qui s’affichera dans la barre de titre de la fenêtre.
- **Enregistrement automatique** -il s’agit du _clé_ qui sera utilisé pour l’ID de la fenêtre lors de sa position et les paramètres sont enregistrés automatiquement.
- **Barre de titre** -la fenêtre affiche une barre de titre.
- **Titre et barre d’outils unifiés** : si la fenêtre inclut une barre d’outils, il doit être la partie de la barre de titre.
- **Complète l’affichage de contenu en taille réelle** -permet à la zone de contenu de la fenêtre d’être sous la barre de titre.
- **Clichés instantanés** -la fenêtre possède une ombre.
- **Une texture** -texture windows peuvent utiliser des effets (tels que le dynamisme de notre place) et peuvent être déplacées en faisant glisser n’importe où dans leurs corps.
- **Fermer** -la fenêtre possède un bouton Fermer.
- **Réduire** -la fenêtre possède un bouton réduire.
- **Redimensionner** -la fenêtre possède un contrôle de redimensionnement.
- **Bouton de barre d’outils** -la fenêtre possède un bouton de barre d’outils masquer/afficher.
- **Pouvant être restaurées** -est la position de la fenêtre et les paramètres automatiquement enregistré et restauré.
- **Visible à lancer** -la fenêtre apparaît automatiquement quand le `.xib` fichier est chargé.
- **Masquer sur désactivé** -la fenêtre est masquée lors de l’application entre dans l’arrière-plan.
- **Lors de la fermeture de la version** -est la fenêtre purgée de la mémoire lorsqu’il est fermé.
- **Toujours les info-bulles de l’affichage** -sont les info-bulles affichés en permanence.
- **Recalcule la boucle d’affichage** -réside dans l’ordre d’affichage recalculée avant que la fenêtre est dessinée.
- **Espaces**, **Exposé** et **cycles** -définir le comportement de la fenêtre dans ces environnements macOS. 
- **Plein écran** -détermine si cette fenêtre peut entrer le mode plein écran. 
- **Animation** -contrôle le type d’animation disponible pour la fenêtre.
- **Apparence** -contrôle l’apparence de la fenêtre. Pour l’instant, il n'est qu’un seul aspect, Aqua.

Consultez d’Apple [Introduction à Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) et [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) documentation pour plus d’informations.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Définition de la taille par défaut et l’emplacement

Pour définir la position initiale de la fenêtre et pour contrôler sa taille, basculez vers le **inspecteur de taille**:

[![](window-images/edit07.png "La taille par défaut et l’emplacement")](window-images/edit07.png#lightbox)

À partir de là vous pouvez définir la taille initiale de la fenêtre, lui donner une taille minimale et maximale, définir l’emplacement initial sur l’écran et contrôler les bordures autour de la fenêtre.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Définition d’un contrôleur de fenêtre principale personnalisée

Pour être en mesure de créer des Outlets et Actions pour exposer les éléments d’interface utilisateur au code c#, l’application Xamarin.Mac doit utiliser un contrôleur de fenêtre personnalisée.

Effectuez ce qui suit :

1. Ouvrez le plan conceptuel de l’application dans Interface Builder de Xcode.
2. Sélectionnez le `NSWindowController` dans l’aire de conception.
3. Basculez vers le **inspecteur d’identité** afficher et entrez `WindowController` en tant que le **nom de la classe**: 

    [![](window-images/windowcontroller01.png "Définition du nom de classe")](window-images/windowcontroller01.png#lightbox)
4. Enregistrez vos modifications et revenir à Visual Studio pour Mac pour la synchronisation.
5. Un `WindowController.cs` fichier sera ajouté à votre projet dans le **l’Explorateur de solutions** dans Visual Studio pour Mac : 

    [![](window-images/windowcontroller02.png "Sélection du contrôleur de windows")](window-images/windowcontroller02.png#lightbox)
6. Rouvrez le plan conceptuel dans Interface Builder de Xcode.
7. Le `WindowController.h` fichier sera disponible pour une utilisation : 

    [![](window-images/windowcontroller03.png "Modification du fichier WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Ajout d’éléments d’interface utilisateur

Pour définir le contenu d’une fenêtre, faire glisser des contrôles à partir de la **inspecteur de bibliothèque** sur le **Éditeur d’Interface**. Consultez notre [Introduction à Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentation pour plus d’informations sur l’utilisation d’Interface Builder pour créer et activer des contrôles.

Par exemple, nous allons faire glisser une barre d’outils à partir de la **inspecteur de bibliothèque** vers la fenêtre dans le **Éditeur d’Interface**:

[![](window-images/edit03.png "Sélection d’une barre d’outils à partir de la bibliothèque")](window-images/edit03.png#lightbox)

Ensuite, faites glisser un **affichage de texte** et le redimensionner pour remplir la zone sous la barre d’outils :

[![](window-images/edit04.png "Ajout d’une vue de texte")](window-images/edit04.png#lightbox)

Dans la mesure où nous voulons le **affichage de texte** pour réduire et développer en tant que les modifications de taille de la fenêtre, passons à la **éditeur de contrainte** et ajouter les contraintes suivantes :

[![](window-images/edit05.png "Modification des contraintes")](window-images/edit05.png#lightbox)

En cliquant sur le pour **-rouges I** en haut de l’éditeur et en cliquant sur **ajouter 4 contraintes**, nous indiquons à l’affichage de texte à respecter coordonnées X, Y donnée et augmenter ou réduire horizontalement et verticalement en tant que la fenêtre est redimensionnée.

Enfin, nous allons exposer le **affichage de texte** vers du code à l’aide d’un **prise** (veillant à sélectionner le `ViewController.h` fichier) :

[![](window-images/edit06.png "Configuration d’un Outlet")](window-images/edit06.png#lightbox)

Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.

Pour plus d’informations sur l’utilisation de **Outlets** et **Actions**, consultez notre [prise et Action](~/mac/get-started/hello-mac.md#outlets-and-actions) documentation.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Fenêtre standard de flux de travail

Pour n’importe quelle fenêtre que vous créez et que vous travaillez dans votre application Xamarin.Mac, le processus est essentiellement identique à ce que nous l’avons simplement fait ci-dessus :

1. Pour les nouvelles fenêtres qui ne sont pas la valeur par défaut ajouté automatiquement à votre projet, ajoutez une nouvelle définition de la fenêtre au projet. Ce sujet sera abordé en détail ci-dessous.
1. Double-cliquez sur le `Main.storyboard` fichier pour ouvrir la fenêtre de conception pour la modification dans Xcode Interface Builder.
1. Faites glisser une nouvelle fenêtre à la conception de l’Interface utilisateur et raccorder l’à l’aide de la fenêtre principale de la fenêtre _Segues_ (pour plus d’informations, consultez le [Segues](~/mac/platform/storyboards/indepth.md#Segues) section de notre [fonctionne avec des Storyboards](~/mac/platform/storyboards/indepth.md) documentation).
1. Définissez les propriétés de fenêtre requis le **inspecteur d’attributs** et **inspecteur de taille**.
1. Faites glisser dans les contrôles nécessaires pour générer votre interface et de les configurer dans le **inspecteur d’attributs**.
1. Utilisez le **inspecteur de taille** pour gérer le redimensionnement des éléments d’interface.
1. Exposer les éléments d’interface utilisateur de la fenêtre de code c# via **Outlets** et **Actions**.
1. Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.

Maintenant que nous disposons d’une fenêtre de base créée, nous allons examiner les processus classiques un Xamarin.Mac fait de l’application lorsque vous travaillez avec windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Affichage de la fenêtre par défaut

Par défaut, une nouvelle application Xamarin.Mac s’affiche automatiquement la fenêtre définie dans le `MainWindow.xib` fichier lorsqu’il est démarré :

[![](window-images/display01.png "Un exemple de fenêtre en cours d’exécution")](window-images/display01.png#lightbox)

Étant donné que nous avons modifié la conception de cette fenêtre ci-dessus, il inclut désormais une barre d’outils par défaut et **affichage de texte** contrôle. La section suivante dans le `Info.plist` fichier est chargé d’afficher cette fenêtre :

[![](window-images/display00.png "Modification du fichier Info.plist")](window-images/display00.png#lightbox)

Le **Interface principale** liste déroulante permet de sélectionner la table de montage séquentiel qui sera utilisé en tant que l’application principale de l’interface utilisateur (dans ce cas `Main.storyboard`).

Un contrôleur d’affichage est automatiquement ajouté au projet pour contrôler ce Windows principale qui s’affiche (ainsi que sa vue principale). Il est défini dans le `ViewController.cs` de fichiers et attaché à la **le propriétaire du fichier** dans Interface Builder sous le **inspecteur d’identité**:

[![](window-images/display02.png "Définir le propriétaire du fichier")](window-images/display02.png#lightbox)

Pour notre fenêtre, nous aimerions qu’il ait un titre de `untitled` quand il commence par conséquent, nous allons remplacement le `ViewWillAppear` méthode dans le `ViewController.cs` ressemble à ce qui suit :

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> Nous définissons la valeur de la fenêtre `Title` propriété dans le `ViewWillAppear` méthode au lieu du `ViewDidLoad` méthode car, alors que la vue peut être chargée en mémoire, il n’est pas encore entièrement instancié. Si nous avons essayé d’accéder à la `Title` propriété dans le `ViewDidLoad` méthode nous obtiendrions une `null` exception étant donné que la fenêtre n’a pas été construite et câblé à distance à la propriété encore.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Fermeture d’une fenêtre par programmation

Il peut arriver que vous souhaitez fermer par programmation une fenêtre dans une application Xamarin.Mac, hormis le fait que l’utilisateur, cliquez sur la fenêtre **fermer** bouton ou à l’aide d’un élément de menu. macOS fournit deux façons de fermer un `NSWindow` par programmation : `PerformClose` et `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Appelant le `PerformClose` méthode d’un `NSWindow` simule l’utilisateur clique sur la fenêtre **fermer** bouton par momentanément mettant en évidence le bouton, puis en fermant la fenêtre.

Si l’application implémente le `NSWindow`de `WillClose` événement, il sera déclenché avant la fermeture de la fenêtre. Si l’événement retourne `false`, puis la fenêtre ne sera pas fermée. Si la fenêtre n’a pas un **fermer** bouton ou ne peut pas être fermé pour une raison quelconque, le système d’exploitation émet le son d’alerte.

Exemple :

```csharp
MyWindow.PerformClose(this);
```

Tente de fermer le `MyWindow` `NSWindow` instance. Si elle a réussi, la fenêtre sera fermée, sinon le son d’alerte est émis et le reste ouverte.

<a name="Close" />

### <a name="close"></a>Fermer

Appelant le `Close` méthode d’un `NSWindow` ne simule pas l’utilisateur clique sur la fenêtre **fermer** bouton en surbrillance momentanément le bouton, il ferme simplement la fenêtre.

Une fenêtre ne doit pas être visible à fermer et un `NSWindowWillCloseNotification` notification est publiée sur le centre de Notification par défaut pour la fenêtre en cours de fermeture.

Le `Close` méthode diffère sur deux points importants à partir de la `PerformClose` méthode :

1. Il ne tente pas de déclencher le `WillClose` événement.
2. Il ne simule pas l’utilisateur clique sur le **fermer** bouton en surbrillance momentanément le bouton.

Exemple :

```csharp
MyWindow.Close();
```

Pour fermer la `MyWindow` `NSWindow` instance.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenu de Windows modifiés

Dans macOS, Apple a permis d’informer l’utilisateur que le contenu d’une fenêtre (`NSWindow`) a été modifié par l’utilisateur et doit être enregistré. Si la fenêtre contient le contenu modifié, un petit point noir s’affichera dans son **fermer** widget :

[![](window-images/close01.png "Une fenêtre avec le marqueur modifié")](window-images/close01.png#lightbox)

Si l’utilisateur tente de fermer la fenêtre ou quitter l’application Mac, bien qu’il existe non enregistrées change pour le contenu de la fenêtre, vous devez présenter un [boîte de dialogue](~/mac/user-interface/dialog.md) ou [feuille modale](~/mac/user-interface/dialog.md) et autoriser l’utilisateur à enregistrer leurs modifications premier :

[![](window-images/close02.png "Un enregistrement feuille affiché lorsque la fenêtre est fermée.")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marquage d’une fenêtre modifiée

Pour marquer une fenêtre en tant qu’avoir modifié le contenu, utilisez le code suivant :

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

Et une fois que la modification a été enregistrée, désactivez l’indicateur modifié à l’aide de :

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Enregistrer les modifications avant de fermer une fenêtre

Pour surveiller l’utilisateur de fermeture d’une fenêtre et en leur permettant d’enregistrer le contenu modifié au préalable, vous devez créer une sous-classe de `NSWindowDelegate` et substituer sa `WindowShouldClose` (méthode). Exemple :

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on result
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

Pour attacher une instance de ce délégué à votre fenêtre, utilisez le code suivant :

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Enregistrer les modifications avant de fermer l’application

Enfin, votre application Xamarin.Mac doit vérifier si un des ses Windows contiennent le contenu modifié et que vous autorisez l’utilisateur à enregistrer les modifications avant de quitter. Pour ce faire, modifiez votre `AppDelegate.cs` de fichiers, substituez le `ApplicationShouldTerminate` (méthode) et qu’elle ressemble à ce qui suit :

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>Utilisation de plusieurs Windows

La plupart des applications pour Mac en fonction de document peuvent modifier plusieurs documents en même temps. Par exemple, un éditeur de texte peut avoir plusieurs fichiers texte ouvert pour modification en même temps. Par défaut, notre nouvelle application Xamarin.Mac a un **fichier** menu avec un **New** élément automatiquement câblé-jusqu'à la `newDocument:` **Action**.

Nous allons activer ce nouvel élément et autoriser l’utilisateur à ouvrir plusieurs copies de notre fenêtre principale pour modifier plusieurs documents à la fois.

Nous allons modifier notre `AppDelegate.cs` fichier, puis ajoutez la propriété calculée de ce qui suit :

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Nous allons utiliser cela pour suivre le nombre de fichiers non enregistrés afin de nous pouvons envoyer des commentaires à l’utilisateur (par les règles d’Apple comme indiqué ci-dessus).

Ensuite, ajoutez la méthode suivante :

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Ce code crée une nouvelle version de notre contrôleur de fenêtre charge de la nouvelle fenêtre, rend la fenêtre de la clé et le principal et lui affecte un titre. Maintenant, si nous pouvons exécuter notre application et que vous sélectionnez **New** à partir de la **fichier** menu sera ouvert et affichée une nouvelle fenêtre d’éditeur :

[![](window-images/display04.png "Une nouvelle fenêtre sans titre a été ajoutée.")](window-images/display04.png#lightbox)

Si nous ouvrons le **Windows** menu, vous pouvez voir l’application est automatiquement suivi et de gestion de nos fenêtres ouvertes :

[![](window-images/display05.png "Le menu windows")](window-images/display05.png#lightbox)

Pour plus d’informations sur l’utilisation des Menus dans une application Xamarin.Mac, veuillez consulter notre [utilisation des Menus](~/mac/user-interface/menu.md) documentation.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Obtention de la fenêtre actuellement Active.

Dans une application Xamarin.Mac capable d’ouvrir plusieurs fenêtres (documents), il est parfois lorsque vous devez obtenir la fenêtre en cours, au premier plan (la fenêtre clée). Le code suivant retourne la fenêtre clée :

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Elle peut être appelée dans une classe ou d’une méthode qui doit accéder à la fenêtre en cours, la clé. Si aucune fenêtre est actuellement ouverte, elle retournera `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>L’accès à toutes les applications Windows

Il peut arriver dans lequel vous avez besoin pour accéder à toutes les fenêtres que votre application Xamarin.Mac a actuellement ouvert. Par exemple, pour voir si un fichier que l’utilisateur souhaite ouvrir est déjà ouvert dans une fenêtre de sortie.

Le `NSApplication.SharedApplication` conserve un `Windows` propriété qui contient un tableau de toutes les fenêtres ouvertes dans votre application. Vous pouvez itérer sur ce tableau pour accéder à toutes les fenêtres de l’application. Exemple :

```csharp
// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

Dans l’exemple de code cast est effectué chaque fenêtre retournée à la personnalisée `ViewController` classe dans notre application et le test de la valeur d’un personnalisé `Path` propriété sur le chemin d’accès d’un fichier de l’utilisateur souhaite ouvrir. Si le fichier est déjà ouvert, nous la mettons maintenant cette fenêtre au premier plan.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Ajuster la taille de fenêtre dans le Code

Il est parfois lorsque l’application a besoin pour redimensionner une fenêtre dans le code. Pour redimensionner et repositionner une fenêtre, vous le régler de `Frame` propriété. Lorsque vous adaptez la taille d’une fenêtre, vous devez généralement également ajuster son origine, pour garder la fenêtre dans le même emplacement en raison de système de coordonnées de macOS.

Contrairement à iOS où l’angle supérieur gauche représente (0,0), macOS utilise un système de coordonnées mathématique où le coin inférieur gauche de l’écran représente (0,0). Dans iOS les coordonnées augmenter à mesure que vous déplacez vers le bas vers la droite. Dans macOS, les coordonnées augmentent la valeur vers le haut à droite. 

L’exemple de code suivant redimensionne une fenêtre :

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> Lorsque vous ajustez une taille de windows et l’emplacement dans le code, vous devez vous assurer que vous respectez les tailles minimales et maximales que vous avez défini dans l’Interface Builder. Cela n’est pas automatiquement honorée et vous serez en mesure de rendre la fenêtre supérieure ou inférieure à ces limites.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Surveillance des modifications de taille de fenêtre

Il peut arriver dans lequel vous avez besoin pour surveiller les modifications de taille d’une fenêtre à l’intérieur de votre application Xamarin.Mac. Par exemple, pour redessiner le contenu en fonction de la nouvelle taille.

Pour surveiller les modifications de taille, vous devez d’abord vérifier que vous avez affecté une classe personnalisée pour le contrôleur de fenêtre dans l’Interface Builder de Xcode. Par exemple, `MasterWindowController` dans l’exemple suivant :

[![](window-images/resize01.png "L’inspecteur d’identité")](window-images/resize01.png#lightbox)

Ensuite, modifiez la classe de contrôleur de fenêtre personnalisés et les analyse le `DidResize` événement sur la fenêtre du contrôleur d’être averti des modifications de taille dynamique. Exemple :

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Si vous le souhaitez, vous pouvez utiliser le `DidEndLiveResize` événement à ne reçoit une notification une fois que l’utilisateur a fini de modifier la taille de la fenêtre. Par exemple :

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>Définition d’une fenêtre de titre et représentée de fichier

Lorsque vous travaillez avec windows qui représentent des documents, `NSWindow` a un `DocumentEdited` propriété que si la valeur `true` affiche un petit point dans le bouton Fermer pour donner à l’utilisateur une indication que le fichier a été modifié et qu’il doit être enregistré avant sa fermeture.

Nous allons modifier notre `ViewController.cs` de fichiers et apportez les modifications suivantes :

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

Nous contrôlons la `WillClose` événement sur la fenêtre et en vérifiant l’état de la `DocumentEdited` propriété. S’il s’agit `true` vous devez donner à l’utilisateur la possibilité d’enregistrer les modifications au fichier. Si nous pouvons exécuter notre application et que vous entrez du texte, le point s’affiche :

[![](window-images/file01.png "Une fenêtre modifiée")](window-images/file01.png#lightbox)

Si nous essayons de fermer la fenêtre, nous obtenons une alerte :

[![](window-images/file02.png "Affichage d’un enregistrement boîte de dialogue")](window-images/file02.png#lightbox)

Si nous chargeons un document à partir d’un fichier, nous pouvons définir le titre de la fenêtre dans le fichier nom en utilisant le `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` (méthode) (étant donné que `path` est une chaîne représentant le fichier en cours d’ouverture). En outre, nous pouvons définir l’URL du fichier en utilisant le `window.RepresentedUrl = url;` (méthode).

Si l’URL pointe vers un type de fichier connu par le système d’exploitation, son icône s’affichera dans la barre de titre. Si l’utilisateur clique le bouton droit sur l’icône, le chemin d’accès au fichier s’affichera.

Nous allons modifier le `AppDelegate.cs` fichier, puis ajoutez la méthode suivante :

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = true;
    dlg.CanChooseDirectories = false;

    if (dlg.RunModal () == 1) {
        // Nab the first file
        var url = dlg.Urls [0];

        if (url != null) {
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

Si nous exécutons notre application, sélectionnez maintenant **ouvrir...**  à partir de la **fichier** menu, sélectionnez un fichier texte à partir de la **ouvrir** boîte de dialogue zone et l’ouvrir :

[![](window-images/file03.png "Une boîte de dialogue Ouvrir")](window-images/file03.png#lightbox)

Le fichier s’affiche et le titre sera défini avec l’icône du fichier :

[![](window-images/file04.png "Le contenu d’un fichier chargé")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Ajout d’une nouvelle fenêtre à un projet

À part la fenêtre de document principal, une application Xamarin.Mac devrez peut-être afficher d’autres types de fenêtres à l’utilisateur, telles que les préférences ou les panneaux de l’inspecteur.

Pour ajouter une nouvelle fenêtre, procédez comme suit :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur le `Main.storyboard` fichier à ouvrir pour modification dans Xcode Interface Builder.
2. Faites glisser une nouvelle **contrôleur de fenêtre** à partir de la **bibliothèque** et déposez-la sur la **aire de conception**:

    [![](window-images/new01.png "Sélection d’un nouveau contrôleur de fenêtre dans la bibliothèque")](window-images/new01.png#lightbox)
3. Dans le **inspecteur d’identité**, entrez `PreferencesWindow` pour le **ID de Storyboard**: 

    [![](window-images/new02.png "Définition de l’ID de storyboard")](window-images/new02.png#lightbox)
4. Concevoir votre interface : 

    [![](window-images/new03.png "Conception de l’interface utilisateur")](window-images/new03.png#lightbox)
5. Ouvrez le Menu de l’application (`MacWindows`), sélectionnez **préférences...** , Cliquez et faites glisser vers la nouvelle fenêtre : 

    [![](window-images/new05.png "Création d’un segue")](window-images/new05.png#lightbox)
6. Sélectionnez **afficher** dans le menu contextuel.
7. Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.

Si nous pouvons exécuter le code et que vous sélectionnez le **préférences...**  à partir de la **Menu Application**, la fenêtre s’affichera :

[![](window-images/new04.png "Un exemple de menu Préférences")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Utilisation des panneaux

Comme indiqué au début de cet article, un panneau flotte au-dessus des autres fenêtres et fournit des outils ou les contrôles que les utilisateurs peuvent travailler avec tandis que les documents sont ouverts. 

Comme tout autre type de fenêtre que vous créez et que vous travaillez dans votre application Xamarin.Mac, le processus est essentiellement le même :

1. Ajouter une nouvelle définition de fenêtre pour le projet.
2. Double-cliquez sur le `.xib` fichier pour ouvrir la fenêtre de conception pour la modification dans Xcode Interface Builder.
3. Définissez les propriétés de fenêtre requis le **inspecteur d’attributs** et **inspecteur de taille**.
4. Faites glisser dans les contrôles nécessaires pour générer votre interface et de les configurer dans le **inspecteur d’attributs**.
5. Utilisez le **inspecteur de taille** pour gérer le redimensionnement des éléments d’interface.
6. Exposer les éléments d’interface utilisateur de la fenêtre de code c# via **Outlets** et **Actions**.
7. Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.

Dans le **inspecteur d’attributs**, vous disposez des options suivantes spécifiques aux panneaux :

[![](window-images/panel03.png "L’inspecteur d’attributs")](window-images/panel03.png#lightbox)

- **Style** -vous permettent d’ajuster le style du panneau à partir de : Panneau normal (ressemble à une fenêtre standard), utilitaire du Panneau de configuration (a une plus petite barre de titre), HUD panneau (est translucide et la barre de titre fait partie de l’arrière-plan).
- **Non activation** -détermine dans le panneau de configuration devient la fenêtre de clé.
- **Document Modal** -si Document modale, le panneau uniquement flotte au-dessus des fenêtres de l’application, sinon elle flotte au-dessus de toutes les.


Pour ajouter un nouveau panneau, procédez comme suit :

1. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le projet et sélectionnez **ajouter** > **nouveau fichier...** .
2. Dans la boîte de dialogue Nouveau fichier, sélectionnez **Xamarin.Mac** > **fenêtre Cocoa avec contrôleur**:

    [![](window-images/panels00.png "Ajoutez un nouveau contrôleur de fenêtre")](window-images/panels00.png#lightbox)
3. Entrez `DocumentPanel` comme **Nom**, puis cliquez sur le bouton **Nouveau**.
4. Double-cliquez sur le `DocumentPanel.xib` fichier à ouvrir pour modification dans l’Interface Builder : 

    [![](window-images/new02.png "Le panneau de montage")](window-images/new02.png#lightbox)
5. Supprimer la fenêtre existante et faites glisser un panneau à partir de la **inspecteur de bibliothèque** dans le **Éditeur d’Interface**: 

    [![](window-images/panels01.png "Suppression de la fenêtre existante")](window-images/panels01.png#lightbox)
6. Le panneau de raccordement jusqu'à la **le propriétaire du fichier** - **fenêtre** - **Outlet**: 

    [![](window-images/panels02.png "En faisant glisser vers le panneau associer")](window-images/panels02.png#lightbox)
7. Basculez vers le **inspecteur d’identité** et la valeur de la classe du panneau par `DocumentPanel`: 

    [![](window-images/panels03.png "Définition de la classe du panneau")](window-images/panels03.png#lightbox)
8. Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.
9. Modifier le `DocumentPanel.cs` du fichier et changer la définition de classe à ce qui suit : 

    `public partial class DocumentPanel : NSPanel`
10. Enregistrez les modifications dans le fichier.

Modifier le `AppDelegate.cs` et effectuez le `DidFinishLaunching` méthode ressemble ce qui suit :

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Si nous exécutons notre application, le panneau s’affiche :

[![](window-images/panels04.png "Le panneau de configuration dans une application en cours d’exécution")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Panneau de configuration Windows ont été déconseillés par Apple et doit être remplacés par **Interfaces d’inspecteur**. Pour obtenir un exemple complet de la création d’un **inspecteur** dans une application Xamarin.Mac, veuillez consulter notre [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) exemple d’application.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a examiné en détail à l’utilisation de Windows et des panneaux dans une application Xamarin.Mac. Nous avons vu les différents types et les utilisations de Windows et panneaux, comment créer et tenir à jour Windows et des panneaux dans Xcode Interface Builder et comment travailler avec Windows et les panneaux en code c#.

## <a name="related-links"></a>Liens associés

- [MacWindows (sample)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (exemple)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation des Menus](~/mac/user-interface/menu.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduction à Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
