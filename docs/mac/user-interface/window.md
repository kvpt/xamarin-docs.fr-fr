---
title: Windows dans Xamarin. Mac
description: Cet article traite de l’utilisation des fenêtres et des panneaux dans une application Xamarin. Mac. Il décrit comment créer des fenêtres et des panneaux dans Xcode et Interface Builder, les charger à partir de fichiers storyboards et de fichiers. XIB, et les utiliser par programme.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 6c7a236995bf2aa9677deb6fadacf76cb5726398
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008130"
---
# <a name="windows-in-xamarinmac"></a>Windows dans Xamarin. Mac

_Cet article traite de l’utilisation des fenêtres et des panneaux dans une application Xamarin. Mac. Il décrit comment créer des fenêtres et des panneaux dans Xcode et Interface Builder, les charger à partir de fichiers storyboards et de fichiers. XIB, et les utiliser par programme._

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux fenêtres et panneaux que le développeur travaille en *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser la _Interface Builder_ de Xcode pour créer et gérer vos fenêtres et panneaux (ou éventuellement les créer directement dans C# le code).

En fonction de son objectif, une application Xamarin. Mac peut présenter une ou plusieurs fenêtres à l’écran pour gérer et coordonner les informations qu’elle affiche et utilise. Les fonctions principales d’une fenêtre sont les suivantes :

1. Pour fournir une zone dans laquelle les vues et les contrôles peuvent être placés et gérés.
2. Pour accepter les événements et y répondre en réponse à l’interaction de l’utilisateur avec le clavier et la souris.

Les fenêtres peuvent être utilisées dans un État non modal (par exemple, un éditeur de texte qui peut avoir plusieurs documents ouverts en même temps) ou modale (par exemple, une boîte de dialogue d’exportation qui doit être fermée pour que l’application puisse continuer).

Les panneaux sont un type spécial de fenêtre (une sous-classe de la classe de `NSWindow` de base), qui sert généralement une fonction auxiliaire dans une application, par exemple des fenêtres d’utilitaire comme les inspecteurs de format de texte et le sélecteur de couleurs système.

[![](window-images/intro01.png "Editing a window in Xcode")](window-images/intro01.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des fenêtres et des panneaux dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les commandes`Register`et`Export`utilisées pour relier vos C# classes à objective-c. objets et éléments d’interface utilisateur.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Présentation de Windows

Comme indiqué ci-dessus, une fenêtre fournit une zone dans laquelle les vues et les contrôles peuvent être placés et gérés et répondent aux événements en fonction de l’interaction de l’utilisateur (par le biais du clavier ou de la souris).

D’après Apple, il existe cinq types principaux de fenêtres dans une application macOS :

- **Fenêtre de document** : une fenêtre de document contient des données utilisateur basées sur des fichiers, par exemple une feuille de calcul ou un document texte.
- **Fenêtre** d’application : une fenêtre d’application est la fenêtre principale d’une application qui n’est pas basée sur document (comme l’application de calendrier sur un Mac).
- **Panneau** : un panneau flotte au-dessus des autres fenêtres et fournit des outils ou des contrôles avec lesquels les utilisateurs peuvent travailler pendant que des documents sont ouverts. Dans certains cas, un panneau peut être translucide (par exemple, lors de l’utilisation de grands graphiques).
- **Boîte de dialogue** : une boîte de dialogue s’affiche en réponse à une action de l’utilisateur et permet généralement aux utilisateurs de terminer l’action. Une boîte de dialogue requiert une réponse de l’utilisateur avant de pouvoir être fermée. (Voir [utilisation des boîtes de dialogue](~/mac/user-interface/dialog.md))
- **Alertes** : une alerte est un type spécial de dialogue qui s’affiche lorsqu’un problème sérieux se produit (par exemple, une erreur) ou en tant qu’avertissement (par exemple, la préparation de la suppression d’un fichier). Comme une alerte est une boîte de dialogue, elle nécessite également une réponse de l’utilisateur avant de pouvoir être fermée. (Voir [utilisation des alertes](~/mac/user-interface/alert.md))

Pour plus d’informations, consultez la section [à propos de Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) des [recommandations relatives à l’interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) d’Apple

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Main, clé et fenêtres inactives

Dans une application Xamarin. Mac, les fenêtres peuvent s’afficher et se comporter différemment en fonction de la façon dont l’utilisateur interagit actuellement avec eux. La fenêtre du document ou de l’application qui se concentre actuellement sur l’attention de l’utilisateur est appelée _fenêtre principale_. Dans la plupart des cas, cette fenêtre est également la _fenêtre clé_ (la fenêtre qui accepte actuellement l’entrée de l’utilisateur). Mais ce n’est pas toujours le cas, par exemple, un sélecteur de couleurs peut être ouvert et être la fenêtre clé avec laquelle l’utilisateur interagit pour modifier l’état d’un élément dans la fenêtre de document (qui serait toujours la fenêtre principale).

Les fenêtres principale et clé (si elles sont séparées) sont toujours actives. les fenêtres _inactives_ sont des fenêtres ouvertes qui ne se trouvent pas au premier plan. Par exemple, une application d’éditeur de texte peut avoir plusieurs documents ouverts à la fois, seule la fenêtre principale est active, toutes les autres sont inactives. 

Pour plus d’informations, consultez la section [à propos de Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) des [recommandations relatives à l’interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) d’Apple

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Nommer des fenêtres

Une fenêtre peut afficher une barre de titre et, lorsque le titre est affiché, il s’agit généralement du nom de l’application, du nom du document en cours de traitement ou de la fonction de la fenêtre (par exemple, Inspector). Certaines applications n’affichent pas de barre de titre, car elles sont reconnaissables à la vue et ne fonctionnent pas avec les documents.

Apple suggère les instructions suivantes :

- Utilisez le nom de votre application pour le titre d’une fenêtre principale, sans document. 
- Nommez une nouvelle fenêtre de document `untitled`. Pour le premier nouveau document, n’ajoutez pas de numéro au titre (par exemple, `untitled 1`). Si l’utilisateur crée un autre document avant l’enregistrement et le titrage du premier, appelez cette fenêtre `untitled 2`, `untitled 3`, etc.

Pour plus d’informations, consultez la section relative aux [fenêtres d’attribution de noms](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) des instructions de l’interface utilisateur d’Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Fenêtres plein écran

Dans macOS, la fenêtre d’une application peut s’afficher en plein écran en masquant tout ce qui inclut la barre de menus de l’application (qui peut être révélée en déplaçant le curseur vers le haut de l’écran) pour fournir une interaction gratuite avec son contenu.

Apple suggère les instructions suivantes :

- Déterminez s’il est judicieux d’utiliser une fenêtre en mode plein écran. Les applications qui fournissent de brèves interactions (telles qu’une calculatrice) ne doivent pas fournir un mode plein écran.
- Affichez la barre d’outils si la tâche en plein écran l’exige. En règle générale, la barre d’outils est masquée en mode plein écran.
- La fenêtre plein écran doit avoir toutes les fonctionnalités dont les utilisateurs ont besoin pour effectuer la tâche.
- Si possible, évitez l’interaction de la recherche lorsque l’utilisateur se trouve dans une fenêtre plein écran.
- Tirez parti de l’espace d’écran accru sans décaler le focus de la tâche principale.

Pour plus d’informations, reportez-vous à la section [Windows plein écran](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) des [instructions relatives à l’interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) d’Apple

<a name="Panels" />

### <a name="panels"></a>Panneaux

Un panneau est une fenêtre auxiliaire qui contient des contrôles et des options qui affectent le document actif ou la sélection (par exemple, le sélecteur de couleurs système) :

[![](window-images/panel01.png "A color panel")](window-images/panel01.png#lightbox)

Les panneaux peuvent être _spécifiques à l’application ou à_ l' _échelle_du système. Les panneaux spécifiques à l’application flottent au-dessus des fenêtres de document de l’application et disparaissent quand l’application est en arrière-plan. Les panneaux du système (tels que le panneau **polices** ), flottent au-dessus de toutes les fenêtres ouvertes, quel que soit l’application. 

Apple suggère les instructions suivantes :

- En général, utilisez un panneau standard, les panneaux transparents ne doivent être utilisés avec modération et pour les tâches gourmandes en graphique.
- Envisagez d’utiliser un panneau pour permettre aux utilisateurs d’accéder facilement aux contrôles importants ou aux informations qui affectent directement leur tâche.
- Masquer et afficher les panneaux selon les besoins.
- Les panneaux doivent toujours inclure la barre de titre.
- Les volets ne doivent pas inclure un bouton réduire actif.

#### <a name="inspectors"></a>Within

La plupart des applications macOS modernes présentent des contrôles et des options auxiliaires qui affectent le document actif ou la sélection comme _inspecteurs_ qui font partie de la fenêtre principale (comme l’application **pages** illustrée ci-dessous), au lieu d’utiliser les fenêtres de panneau :

[![](window-images/panel02.png "An example inspector")](window-images/panel02.png#lightbox)

Pour plus d’informations, reportez-vous à la section des [panneaux](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) des guides de l' [interface utilisateur d’Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) et à notre exemple d’application [MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector) pour une implémentation complète d’une **interface Inspector** dans une application Xamarin. Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Création et maintenance de fenêtres dans Xcode

Lorsque vous créez une application de cacao Xamarin. Mac, vous recevez une fenêtre vide standard par défaut. Cette fenêtre est définie dans un fichier de `.storyboard` inclus automatiquement dans le projet. Pour modifier votre conception Windows, dans le **Explorateur de solutions**, double-cliquez sur le fichier `Main.storyboard` :

[![](window-images/edit01.png "Selecting the main storyboard")](window-images/edit01.png#lightbox)

Cette opération ouvre la conception de la fenêtre dans le Interface Builder de Xcode :

[![](window-images/edit02.png "Editing the UI in Xcode")](window-images/edit02.png#lightbox)

Dans l' **inspecteur d’attributs**, il existe plusieurs propriétés que vous pouvez utiliser pour définir et contrôler votre fenêtre :

- **Title** : texte qui sera affiché dans le TitleBar de la fenêtre.
- **Enregistrement** automatique : il s’agit de la _clé_ qui sera utilisée pour ID la fenêtre quand elle est positionnée et que les paramètres sont enregistrés automatiquement.
- **Barre de titre** : affiche une barre de titre dans la fenêtre.
- **Titre et barre d’outils unifiés** : si la fenêtre comprend une barre d’outils, doit-elle faire partie de la barre de titre.
- **Affichage de contenu complet** -permet à la zone de contenu de la fenêtre d’être sous la barre de titre.
- **Shadow** : la fenêtre a-t-elle une ombre.
- Les fenêtres texturées **texturées** peuvent utiliser des effets (comme la Vibrance) et peuvent être déplacées en les faisant glisser n’importe où sur leur corps.
- **Fermer** : la fenêtre a-t-elle un bouton Fermer.
- **Réduire** : la fenêtre a un bouton réduire.
- **Resize** : la fenêtre a-t-elle un contrôle Resize.
- **Bouton de barre d’outils** -est-ce que la fenêtre a un bouton Masquer/afficher la barre d’outils.
- **Restaurable** : la position et les paramètres de la fenêtre sont enregistrés et restaurés automatiquement.
- **Visible au lancement** : la fenêtre s’affiche automatiquement lors du chargement du fichier `.xib`.
- **Masquer lors de la désactivation** : fenêtre masquée lorsque l’application entre en arrière-plan.
- **Libérer quand** il est fermé : la fenêtre est-elle purgée de la mémoire lorsqu’elle est fermée.
- **Toujours afficher les info** -bulles : les info-bulles s’affichent en permanence.
- **Recalcule la boucle d’affichage** -l’ordre d’affichage est-il recalculé avant que la fenêtre ne soit dessinée.
- **Spaces**, **consolideront** et **cycle** -All définissent le comportement de la fenêtre dans ces environnements MacOS. 
- **Plein écran** -détermine si cette fenêtre peut passer en mode plein écran. 
- **Animation** : contrôle le type d’animation disponible pour la fenêtre.
- **Apparence** -contrôle l’apparence de la fenêtre. Pour le moment, il n’existe qu’une seule apparence cyan.

Pour plus d’informations, consultez la page présentation d’Apple [pour Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) et [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) .

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Définition de la taille et de l’emplacement par défaut

Pour définir la position initiale de votre fenêtre et contrôler sa taille, basculez vers l' **inspecteur de taille**:

[![](window-images/edit07.png "The default size and location")](window-images/edit07.png#lightbox)

À partir de là, vous pouvez définir la taille initiale de la fenêtre, lui attribuer une taille minimale et maximale, définir l’emplacement initial sur l’écran et contrôler les bordures dans la fenêtre.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Définition d’un contrôleur de fenêtre principale personnalisé

Pour pouvoir créer des sorties et des actions pour exposer des éléments d’interface C# utilisateur au code, l’application Xamarin. Mac doit utiliser un contrôleur de fenêtre personnalisé.

Effectuez ce qui suit :

1. Ouvrez le Storyboard de l’application dans le Interface Builder de Xcode.
2. Sélectionnez l' `NSWindowController` dans le Aire de conception.
3. Basculez vers la vue de l' **inspecteur d’identité** et entrez `WindowController` comme nom de la **classe**: 

    [![](window-images/windowcontroller01.png "Setting the class name")](window-images/windowcontroller01.png#lightbox)
4. Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser.
5. Un fichier de `WindowController.cs` sera ajouté à votre projet dans le **Explorateur de solutions** dans Visual Studio pour Mac : 

    [![](window-images/windowcontroller02.png "Selecting the windows controller")](window-images/windowcontroller02.png#lightbox)
6. Rouvrez le Storyboard dans le Interface Builder de Xcode.
7. Le fichier `WindowController.h` sera disponible pour utilisation : 

    [![](window-images/windowcontroller03.png "Editing the WindowController.h file")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Ajouter des éléments d’interface utilisateur

Pour définir le contenu d’une fenêtre, faites glisser les contrôles de l' **inspecteur de bibliothèque** vers l' **éditeur d’interface**. Pour plus d’informations sur l’utilisation de Interface Builder pour créer et activer des contrôles, consultez notre introduction à la documentation de [Xcode et de Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) .

À titre d’exemple, nous allons faire glisser une barre d’outils de l' **inspecteur de bibliothèque** vers la fenêtre de l' **éditeur d’interface**:

[![](window-images/edit03.png "Selecting a Toolbar from the Library")](window-images/edit03.png#lightbox)

Ensuite, faites glisser dans une **vue de texte** et dimensionnez-la pour remplir la zone sous la barre d’outils :

[![](window-images/edit04.png "Adding a Text View")](window-images/edit04.png#lightbox)

Étant donné que l' **affichage de texte** doit être réduit et croître à mesure que la taille de la fenêtre change, nous allons basculer vers l' **éditeur de contrainte** et ajouter les contraintes suivantes :

[![](window-images/edit05.png "Editing constraints")](window-images/edit05.png#lightbox)

En cliquant sur le bouton pour les **I en rouge** en haut de l’éditeur et en cliquant sur **Ajouter 4 contraintes**, nous indiquons à la vue de texte de s’ajuster aux coordonnées X, Y données et à augmenter ou à réduire horizontalement et verticalement lorsque la fenêtre est redimensionnée.

Enfin, nous allons exposer l' **affichage de texte** au code à l’aide d’une **prise** (en veillant à sélectionner le fichier `ViewController.h`) :

[![](window-images/edit06.png "Configuring an Outlet")](window-images/edit06.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Pour plus d’informations sur l’utilisation des **sorties** et des **actions**, consultez notre [sortie et](~/mac/get-started/hello-mac.md#outlets-and-actions) la documentation de l’action.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Flux de travail de fenêtre standard

Pour toute fenêtre que vous créez et utilisez dans votre application Xamarin. Mac, le processus est fondamentalement le même que ce que nous venons de faire ci-dessus :

1. Pour les nouvelles fenêtres qui ne sont pas la valeur par défaut automatiquement ajoutée à votre projet, ajoutez une nouvelle définition de fenêtre au projet. Ce sujet sera abordé en détail ci-dessous.
1. Double-cliquez sur le fichier `Main.storyboard` pour ouvrir la conception de la fenêtre et la modifier dans le Interface Builder de Xcode.
1. Faites glisser une nouvelle fenêtre dans la conception de l’interface utilisateur et raccordez la fenêtre à la fenêtre principale à l’aide de _SEGUES_ (pour plus d’informations, consultez la section [SEGUES](~/mac/platform/storyboards/indepth.md#Segues) de notre documentation [sur les storyboards](~/mac/platform/storyboards/indepth.md) ).
1. Définissez les propriétés de fenêtre requises dans l' **inspecteur d’attribut** et l’inspecteur de **taille**.
1. Faites glisser les contrôles requis pour générer votre interface et configurez-les dans l' **inspecteur d’attribut**.
1. Utilisez l' **inspecteur de taille** pour gérer le redimensionnement de vos éléments d’interface utilisateur.
1. Exposez les éléments d’interface C# utilisateur de la fenêtre au code via des **prises** et des **actions**.
1. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Maintenant que nous avons créé une fenêtre de base, nous allons examiner les processus typiques que fait une application Xamarin. Mac quand vous travaillez avec Windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Affichage de la fenêtre par défaut

Par défaut, une nouvelle application Xamarin. Mac affiche automatiquement la fenêtre définie dans le fichier `MainWindow.xib` au démarrage :

[![](window-images/display01.png "An example window running")](window-images/display01.png#lightbox)

Étant donné que nous avons modifié la conception de cette fenêtre ci-dessus, elle comprend désormais une barre d’outils et un contrôle d' **affichage de texte** par défaut. La section suivante dans le fichier `Info.plist` est responsable de l’affichage de cette fenêtre :

[![](window-images/display00.png "Editing Info.plist")](window-images/display00.png#lightbox)

La liste déroulante **interface principale** permet de sélectionner le Storyboard qui sera utilisé comme interface utilisateur principale de l’application (dans ce cas `Main.storyboard`).

Un contrôleur d’affichage est automatiquement ajouté au projet pour contrôler les fenêtres principales qui s’affichent (ainsi que sa vue principale). Il est défini dans le fichier `ViewController.cs` et associé au **propriétaire du fichier** dans Interface Builder sous l' **inspecteur d’identité**:

[![](window-images/display02.png "Setting the file's owner")](window-images/display02.png#lightbox)

Pour notre fenêtre, nous aimerions qu’il ait un titre de `untitled` lorsqu’il s’ouvre pour la première fois. par conséquent, nous allons remplacer la méthode `ViewWillAppear` dans le `ViewController.cs` de façon à ce qu’elle ressemble à ce qui suit :

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> Nous définissons la valeur de la propriété `Title` de la fenêtre dans la méthode `ViewWillAppear` au lieu de la méthode `ViewDidLoad`, car la vue peut être chargée en mémoire, mais elle n’est pas encore entièrement instanciée. Si nous avons essayé d’accéder à la propriété `Title` dans la méthode `ViewDidLoad`, nous obtenons une exception `null`, car la fenêtre n’a pas encore été construite et câblée à la propriété.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Fermeture d’une fenêtre par programmation

Il peut arriver que vous souhaitiez fermer par programmation une fenêtre dans une application Xamarin. Mac, à l’exception du fait que l’utilisateur clique sur le bouton **Fermer** de la fenêtre ou qu’il utilise un élément de menu. macOS offre deux méthodes différentes pour fermer un `NSWindow` par programme : `PerformClose` et `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

L’appel de la méthode `PerformClose` d’un `NSWindow` simule l’utilisateur qui clique sur le bouton **Fermer** de la fenêtre en mettant momentanément en surbrillance le bouton, puis en fermant la fenêtre.

Si l’application implémente l’événement `WillClose` de `NSWindow`, elle est déclenchée avant la fermeture de la fenêtre. Si l’événement retourne `false`, la fenêtre n’est pas fermée. Si la fenêtre n’a pas de bouton **Fermer** ou ne peut pas être fermée pour une raison quelconque, le système d’exploitation émet le son d’alerte.

Exemple :

```csharp
MyWindow.PerformClose(this);
```

Tente de fermer l’instance de `NSWindow` `MyWindow`. En cas de réussite, la fenêtre sera fermée, sinon le son d’alerte sera émis et le restera ouvert.

<a name="Close" />

### <a name="close"></a>Close

L’appel de la méthode `Close` d’un `NSWindow` ne simule pas l’utilisateur qui clique sur le bouton **Fermer** de la fenêtre en mettant momentanément en surbrillance le bouton, il ferme simplement la fenêtre.

Une fenêtre ne doit pas nécessairement être visible pour être fermée et une notification de `NSWindowWillCloseNotification` sera publiée dans le centre de notifications par défaut pour la fermeture de la fenêtre.

La méthode `Close` diffère de deux façons importantes par rapport à la méthode `PerformClose` :

1. Elle n’essaie pas de déclencher l’événement `WillClose`.
2. Il ne simule pas l’utilisateur qui clique sur le bouton **Fermer** en mettant momentanément en surbrillance le bouton.

Exemple :

```csharp
MyWindow.Close();
```

Pour fermer l’instance de `NSWindow` `MyWindow`.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenu Windows modifié

Dans macOS, Apple a fourni un moyen d’informer l’utilisateur que le contenu d’une fenêtre (`NSWindow`) a été modifié par l’utilisateur et qu’il doit être enregistré. Si la fenêtre contient du contenu modifié, un petit point noir s’affiche dans le widget de **fermeture** :

[![](window-images/close01.png "A window with the modified marker")](window-images/close01.png#lightbox)

Si l’utilisateur tente de fermer la fenêtre ou de quitter l’application Mac alors que des modifications n’ont pas été enregistrées sur le contenu de la fenêtre, vous devez présenter une [boîte de dialogue](~/mac/user-interface/dialog.md) ou une [feuille modale](~/mac/user-interface/dialog.md) et autoriser l’utilisateur à enregistrer d’abord ses modifications :

[![](window-images/close02.png "A save sheet being shown when the window is closed")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marquage d’une fenêtre comme modifiée

Pour marquer une fenêtre comme ayant un contenu modifié, utilisez le code suivant :

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

Une fois la modification enregistrée, effacez l’indicateur modifié à l’aide de :

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Enregistrement des modifications avant la fermeture d’une fenêtre

Pour surveiller l’utilisateur qui ferme une fenêtre et lui permettre d’enregistrer le contenu modifié au préalable, vous devez créer une sous-classe de `NSWindowDelegate` et remplacer sa méthode `WindowShouldClose`. Exemple :

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

Utilisez le code suivant pour attacher une instance de ce délégué à votre fenêtre :

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Enregistrement des modifications avant la fermeture de l’application

Enfin, votre application Xamarin. Mac doit vérifier si l’une de ses fenêtres contient du contenu modifié et autoriser l’utilisateur à enregistrer les modifications avant de quitter. Pour ce faire, modifiez votre fichier `AppDelegate.cs`, remplacez la méthode `ApplicationShouldTerminate` et faites-lui ressembler à ce qui suit :

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

## <a name="working-with-multiple-windows"></a>Utilisation de plusieurs fenêtres

La plupart des applications Mac basées sur des documents peuvent modifier plusieurs documents en même temps. Par exemple, un éditeur de texte peut avoir plusieurs fichiers texte ouverts en vue d’une modification en même temps. Par défaut, notre nouvelle application Xamarin. Mac possède un menu **fichier** avec un **nouvel** élément automatiquement câblé à l' **action**`newDocument:`.

Nous allons activer ce nouvel élément et autoriser l’utilisateur à ouvrir plusieurs copies de notre fenêtre principale pour modifier plusieurs documents à la fois.

Nous allons modifier notre fichier `AppDelegate.cs` et ajouter la propriété calculée suivante :

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Nous allons l’utiliser pour suivre le nombre de fichiers non enregistrés afin que nous puissions nous envoyer des commentaires à l’utilisateur (par Apple Guidelines, comme indiqué ci-dessus).

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

Ce code crée une nouvelle version de notre contrôleur de fenêtre, charge la nouvelle fenêtre, en fait la fenêtre principale et la fenêtre clé, puis définit son titre. Maintenant, si nous exécutons notre application et que vous sélectionnez **nouveau** dans le menu **fichier** , une nouvelle fenêtre d’éditeur s’ouvre et s’affiche :

[![](window-images/display04.png "A new untitled window was added")](window-images/display04.png#lightbox)

Si vous ouvrez le menu **Windows** , vous pouvez voir que l’application effectue automatiquement le suivi et la gestion de nos fenêtres ouvertes :

[![](window-images/display05.png "The windows menu")](window-images/display05.png#lightbox)

Pour plus d’informations sur l’utilisation des menus dans une application Xamarin. Mac, consultez notre documentation sur l' [utilisation des menus](~/mac/user-interface/menu.md) .

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Obtention de la fenêtre actuellement active

Dans une application Xamarin. Mac qui peut ouvrir plusieurs fenêtres (documents), il peut arriver que vous ayez besoin d’obtenir la fenêtre actuelle en haut (fenêtre clé). Le code suivant retournera la fenêtre clé :

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Il peut être appelé dans toute classe ou méthode qui doit accéder à la fenêtre de clé actuelle. Si aucune fenêtre n’est actuellement ouverte, elle retournera `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Accès à toutes les fenêtres d’application

Il peut arriver que vous ayez besoin d’accéder à toutes les fenêtres ouvertes par votre application Xamarin. Mac. Par exemple, pour voir si un fichier que l’utilisateur souhaite ouvrir est déjà ouvert dans une fenêtre de sortie.

Le `NSApplication.SharedApplication` gère une propriété `Windows` qui contient un tableau de toutes les fenêtres ouvertes dans votre application. Vous pouvez effectuer une itération sur ce tableau pour accéder à toutes les fenêtres actuelles de l’application. Exemple :

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

Dans l’exemple de code, nous transmettons chaque fenêtre retournée à la classe de `ViewController` personnalisée dans notre application, et nous testons la valeur d’une propriété `Path` personnalisée sur le chemin d’accès d’un fichier que l’utilisateur souhaite ouvrir. Si le fichier est déjà ouvert, cette fenêtre est déplacée au premier plan.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Ajustement de la taille de la fenêtre dans le code

Dans certains cas, l’application doit redimensionner une fenêtre dans le code. Pour redimensionner et repositionner une fenêtre, ajustez sa propriété `Frame`. Lorsque vous ajustez la taille d’une fenêtre, vous devez en général également ajuster son origine pour conserver la fenêtre au même emplacement en raison du système de coordonnées de macOS.

Contrairement à iOS, où l’angle supérieur gauche représente (0,0), macOS utilise un système de coordonnées solutions mathématiques dans lequel l’angle inférieur gauche de l’écran représente (0,0). Dans iOS, les coordonnées augmentent à mesure que vous descendez vers la droite. Dans macOS, les coordonnées augmentent la valeur vers la droite. 

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
> Lorsque vous ajustez la taille et l’emplacement de Windows dans le code, vous devez vérifier que vous respectez les tailles minimale et maximale que vous avez définies dans Interface Builder. Cela ne sera pas automatiquement respecté et vous pourrez agrandir ou réduire la taille de la fenêtre.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Modification de la taille de la fenêtre d’analyse

Il peut arriver que vous deviez surveiller les modifications apportées à la taille d’une fenêtre à l’intérieur de votre application Xamarin. Mac. Par exemple, pour redessiner le contenu en fonction de la nouvelle taille.

Pour surveiller les modifications de taille, commencez par vous assurer que vous avez affecté une classe personnalisée pour le contrôleur de fenêtre dans le Interface Builder de Xcode. Par exemple, `MasterWindowController` dans ce qui suit :

[![](window-images/resize01.png "The Identity Inspector")](window-images/resize01.png#lightbox)

Modifiez ensuite la classe de contrôleur de fenêtre personnalisée et surveillez l’événement `DidResize` dans la fenêtre du contrôleur pour être averti des modifications de taille dynamique. Exemple :

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Si vous le souhaitez, vous pouvez utiliser l’événement `DidEndLiveResize` pour être averti uniquement une fois que l’utilisateur a fini de modifier la taille de la fenêtre. Par exemple :

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

## <a name="setting-a-windows-title-and-represented-file"></a>Définition du titre d’une fenêtre et du fichier représenté

Lorsque vous utilisez des fenêtres qui représentent des documents, `NSWindow` a une propriété `DocumentEdited` qui, si elle est définie sur `true` affiche un petit point dans le bouton Fermer pour indiquer à l’utilisateur une indication que le fichier a été modifié et doit être enregistré avant la fermeture.

Nous allons modifier notre fichier de `ViewController.cs` et apporter les modifications suivantes :

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

Nous analysons également l’événement `WillClose` dans la fenêtre et vérifions l’état de la propriété `DocumentEdited`. Si c’est le cas `true` nous devons permettre à l’utilisateur d’enregistrer les modifications apportées au fichier. Si nous exécutons l’application et que vous entrez du texte, le point s’affiche :

[![](window-images/file01.png "A changed window")](window-images/file01.png#lightbox)

Si nous essayons de fermer la fenêtre, nous obtenons une alerte :

[![](window-images/file02.png "Displaying a save dialog")](window-images/file02.png#lightbox)

Si nous chargeons un document à partir d’un fichier, nous pouvons définir le titre de la fenêtre sur le nom du fichier à l’aide de la méthode `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` (étant donné que `path` est une chaîne représentant le fichier en cours d’ouverture). En outre, nous pouvons définir l’URL du fichier à l’aide de la méthode `window.RepresentedUrl = url;`.

Si l’URL pointe vers un type de fichier connu par le système d’exploitation, son icône s’affiche dans la barre de titre. Si l’utilisateur clique avec le bouton droit sur l’icône, le chemin d’accès au fichier s’affiche.

Modifions le fichier `AppDelegate.cs` et ajoutez la méthode suivante :

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

Maintenant, si nous exécutons notre application, sélectionnez **Ouvrir...** dans le menu **fichier** , sélectionnez un fichier texte dans la boîte de dialogue **ouvrir** et ouvrez-le :

[![](window-images/file03.png "An open dialog box")](window-images/file03.png#lightbox)

Le fichier s’affiche et le titre est défini avec l’icône du fichier :

[![](window-images/file04.png "The contents of a file loaded")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Ajout d’une nouvelle fenêtre à un projet

Outre la fenêtre de document principale, une application Xamarin. Mac peut être amenée à afficher d’autres types de fenêtres pour l’utilisateur, telles que des préférences ou des panneaux d’inspecteur.

Pour ajouter une nouvelle fenêtre, procédez comme suit :

1. Dans la **Explorateur de solutions**, double-cliquez sur le fichier `Main.storyboard` pour l’ouvrir et le modifier dans le Interface Builder de Xcode.
2. Faites glisser un nouveau **contrôleur de fenêtre** à partir de la **bibliothèque** et déposez-le sur le **aire de conception**:

    [![](window-images/new01.png "Selecting a new Window Controller in the Library")](window-images/new01.png#lightbox)
3. Dans l' **inspecteur d’identité**, entrez `PreferencesWindow` pour l' **ID de Storyboard**: 

    [![](window-images/new02.png "Setting the storyboard ID")](window-images/new02.png#lightbox)
4. Concevez votre interface : 

    [![](window-images/new03.png "Designing the UI")](window-images/new03.png#lightbox)
5. Ouvrez le menu de l’application (`MacWindows`), sélectionnez **Préférences...** , puis cliquez sur Ctrl et faites-le glisser vers la nouvelle fenêtre : 

    [![](window-images/new05.png "Creating a segue")](window-images/new05.png#lightbox)
6. Sélectionnez **Afficher** dans le menu contextuel.
7. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Si nous exécutons le code et que vous sélectionnez les **Préférences...** dans le menu de l' **application**, la fenêtre s’affiche :

[![](window-images/new04.png "A sample preferences menu")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Utilisation des panneaux

Comme indiqué au début de cet article, un panneau flotte au-dessus des autres fenêtres et fournit des outils ou des contrôles avec lesquels les utilisateurs peuvent travailler pendant que des documents sont ouverts. 

Comme tout autre type de fenêtre que vous créez et utilisez dans votre application Xamarin. Mac, le processus est fondamentalement le même :

1. Ajoutez une nouvelle définition de fenêtre au projet.
2. Double-cliquez sur le fichier `.xib` pour ouvrir la conception de la fenêtre et la modifier dans le Interface Builder de Xcode.
3. Définissez les propriétés de fenêtre requises dans l' **inspecteur d’attribut** et l’inspecteur de **taille**.
4. Faites glisser les contrôles requis pour générer votre interface et configurez-les dans l' **inspecteur d’attribut**.
5. Utilisez l' **inspecteur de taille** pour gérer le redimensionnement de vos éléments d’interface utilisateur.
6. Exposez les éléments d’interface C# utilisateur de la fenêtre au code via des **prises** et des **actions**.
7. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Dans l' **inspecteur d’attributs**, vous disposez des options suivantes spécifiques aux panneaux :

[![](window-images/panel03.png "The Attribute Inspector")](window-images/panel03.png#lightbox)

- **Style** : vous permet d’ajuster le style du panneau à partir du panneau normal (à l’instar d’une fenêtre standard), du panneau utilitaire (avec une barre de titre plus petite), du panneau HUD (translucide et la barre de titre fait partie de l’arrière-plan).
- **Non activé** : détermine dans le panneau qui devient la fenêtre clé.
- **Document modal** : si le document est modal, le panneau flotte uniquement au-dessus des fenêtres de l’application, sinon il flotte au-dessus de tout.

Pour ajouter un nouveau panneau, procédez comme suit :

1. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **nouveau fichier.** ..
2. Dans la boîte de dialogue nouveau fichier, sélectionnez **Xamarin. Mac**  > **fenêtre de cacao avec le contrôleur**:

    [![](window-images/panels00.png "Adding a new window controller")](window-images/panels00.png#lightbox)
3. Entrez `DocumentPanel` comme **Nom**, puis cliquez sur le bouton **Nouveau**.
4. Double-cliquez sur le fichier `DocumentPanel.xib` pour l’ouvrir et le modifier dans Interface Builder : 

    [![](window-images/new02.png "Editing the panel")](window-images/new02.png#lightbox)
5. Supprimez la fenêtre existante et faites glisser un panneau à partir de l' **inspecteur de bibliothèque** dans l' **éditeur d’interface**: 

    [![](window-images/panels01.png "Deleting the existing window")](window-images/panels01.png#lightbox)
6. Raccordez le panneau au **propriétaire du fichier** - **fenêtre** - **orifice**: 

    [![](window-images/panels02.png "Dragging to wire up the panel")](window-images/panels02.png#lightbox)
7. Basculez vers l' **inspecteur d’identité** et définissez la classe du panneau sur `DocumentPanel`: 

    [![](window-images/panels03.png "Setting the panel's class")](window-images/panels03.png#lightbox)
8. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.
9. Modifiez le fichier `DocumentPanel.cs` et modifiez la définition de classe comme suit : 

    `public partial class DocumentPanel : NSPanel`
10. Enregistrez les modifications dans le fichier.

Modifiez le fichier `AppDelegate.cs` et faites en sorte que la méthode `DidFinishLaunching` ressemble à ce qui suit :

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Si nous exécutons notre application, le panneau s’affiche :

[![](window-images/panels04.png "The panel in a running app")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Les fenêtres de panneau ont été dépréciées par Apple et doivent être remplacées par des **interfaces d’inspecteur**. Pour obtenir un exemple complet de création d’un **Inspector** dans une application Xamarin. Mac, consultez notre exemple d’application [MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector) .

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des fenêtres et des panneaux dans une application Xamarin. Mac. Nous avons vu les différents types et utilisations des fenêtres et des panneaux, comment créer et gérer des fenêtres et des panneaux dans le Interface Builder de Xcode et comment travailler avec des C# fenêtres et des panneaux dans le code.

## <a name="related-links"></a>Liens associés

- [MacWindows (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [MacInspector (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation des menus](~/mac/user-interface/menu.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation de Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
