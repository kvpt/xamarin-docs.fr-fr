---
title: Barres d’outils dans Xamarin. Mac
description: Cet article décrit l’utilisation des barres d’outils dans une application Xamarin. Mac. Il aborde la création et la gestion des barres d’outils dans Xcode et Interface Builder, leur exposition au code et leur utilisation par programmation.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: cd2490bfad880d128f5eaeebd4aac58ad3a4d8fa
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70772720"
---
# <a name="toolbars-in-xamarinmac"></a>Barres d’outils dans Xamarin. Mac

_Cet article décrit l’utilisation des barres d’outils dans une application Xamarin. Mac. Il aborde la création et la gestion des barres d’outils dans Xcode et Interface Builder, leur exposition au code et leur utilisation par programmation._

Les développeurs Xamarin. Mac qui travaillent avec Visual Studio pour Mac ont accès aux mêmes contrôles d’interface utilisateur que ceux disponibles pour les développeurs macOS qui travaillent avec Xcode, y compris le contrôle ToolBar. Comme Xamarin. Mac s’intègre directement à Xcode, il est possible d’utiliser la Interface Builder de Xcode pour créer et gérer des éléments de barre d’outils. Ces éléments de barre d’outils peuvent également C#être créés dans.

Les barres d’outils dans macOS sont ajoutées à la partie supérieure d’une fenêtre et offrent un accès facile aux commandes associées à ses fonctionnalités. Les barres d’outils peuvent être masquées, affichées ou personnalisées par les utilisateurs d’une application, et elles peuvent présenter des éléments de barre d’outils de différentes façons.

Cet article présente les principes de base de l’utilisation des barres d’outils et des éléments de barre d’outils dans une application Xamarin. Mac. 

Avant de continuer, lisez l’article sur la [salutation, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et de Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et des sections d' [actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , en ce qui concerne les concepts et les techniques clés qui seront utilisés dans ce guide.

Jetez également un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac](~/mac/internals/how-it-works.md) . Elle explique les `Register` attributs `Export` et utilisés pour connecter C# des classes à des classes objective-C.

## <a name="introduction-to-toolbars"></a>Présentation des barres d’outils

Toute fenêtre dans une application macOS peut inclure une barre d’outils :

![Une fenêtre d’exemple avec une barre d’outils](toolbar-images/info01.png "Une fenêtre d’exemple avec une barre d’outils")

Les barres d’outils offrent un moyen simple aux utilisateurs de votre application d’accéder rapidement aux fonctionnalités importantes ou couramment utilisées. Par exemple, une application de modification de document peut fournir des éléments de barre d’outils pour définir la couleur du texte, modifier la police ou imprimer le document actif.

Les barres d’outils peuvent afficher des éléments de trois façons :

1. **Icône et texte** 

     ![Une barre d’outils avec des icônes et du texte](toolbar-images/info02.png "Une barre d’outils avec des icônes et du texte")

2. **Icône uniquement** 

     ![Barre d’outils icône uniquement](toolbar-images/info03.png "Barre d’outils icône uniquement")

3. **Texte uniquement** 

     ![Barre d’outils de texte uniquement](toolbar-images/info04.png "Barre d’outils de texte uniquement")

Pour passer d’un mode à un autre, cliquez avec le bouton droit sur la barre d’outils et sélectionnez un mode d’affichage dans le menu contextuel :

![Menu contextuel d’une barre d’outils](toolbar-images/info05.png "Menu contextuel d’une barre d’outils")

Utilisez le même menu pour afficher la barre d’outils à une taille plus petite :

![Barre d’outils avec petites icônes](toolbar-images/info06.png "Barre d’outils avec petites icônes")

Le menu permet également de personnaliser la barre d’outils :

![Boîte de dialogue utilisée pour personnaliser une barre d’outils](toolbar-images/info07.png "Boîte de dialogue utilisée pour personnaliser une barre d’outils")

Quand vous configurez une barre d’outils dans le Interface Builder de Xcode, un développeur peut fournir des éléments de barre d’outils supplémentaires qui ne font pas partie de sa configuration par défaut. Les utilisateurs de l’application peuvent alors personnaliser la barre d’outils, en ajoutant et en supprimant ces éléments prédéfinis, si nécessaire. Bien entendu, la barre d’outils peut être réinitialisée à sa configuration par défaut.

La barre d’outils se connecte automatiquement au menu **affichage** , qui permet aux utilisateurs de les masquer, de les afficher et de les personnaliser :

![Éléments associés à une barre d’outils dans le menu Affichage](toolbar-images/info08.png "Éléments associés à une barre d’outils dans le menu Affichage")

Pour plus d’informations, consultez la documentation sur les [fonctionnalités de menu intégrées](~/mac/user-interface/menu.md) .

En outre, si la barre d’outils est correctement configurée dans Interface Builder, l’application conserve automatiquement les personnalisations de la barre d’outils sur plusieurs lancements de l’application.

Les sections suivantes de ce guide décrivent comment créer et gérer des barres d’outils avec l’Interface Builder de Xcode et comment les utiliser dans le code.

## <a name="setting-a-custom-main-window-controller"></a>Définition d’un contrôleur de fenêtre principale personnalisé

Pour exposer des éléments d' C# interface utilisateur au code via des prises et des actions, l’application Xamarin. Mac doit utiliser un contrôleur de fenêtre personnalisé :

1. Ouvrez le Storyboard de l’application dans le Interface Builder de Xcode.
2. Sélectionnez le contrôleur de fenêtre sur l’aire de conception.
3. Basculez vers l' **inspecteur d’identité** et entrez « WindowController » comme nom de **classe**: 

    [![Définition d’un nom de classe personnalisé pour le contrôleur de fenêtre](toolbar-images/windowcontroller01.png "Définition d’un nom de classe personnalisé pour le contrôleur de fenêtre")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser.
5. Un fichier **WindowController.cs** sera ajouté à votre projet dans le **panneau solutions** dans Visual Studio pour Mac : 

    ![Sélection de WindowController.cs dans le panneau solutions](toolbar-images/windowcontroller02.png "Sélection de WindowController.cs dans le panneau solutions")

6. Rouvrez le Storyboard dans le Interface Builder de Xcode.
7. Le fichier **WindowController. h** sera disponible en vue de son utilisation : 

    [![Le fichier WindowController. h](toolbar-images/windowcontroller03.png "Le fichier WindowController. h")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Création et gestion des barres d’outils dans Xcode

Les barres d’outils sont créées et gérées à l’aide de l’Interface Builder de Xcode. Pour ajouter une barre d’outils à une application, modifiez le Storyboard principal de l’application (dans ce cas **main. Storyboard**) en double-cliquant dessus dans la **panneau solutions**:

![Ouverture de main. Storyboard dans le panneau solutions](toolbar-images/edit01.png "Ouverture de main. Storyboard dans le panneau solutions")

Dans l' **inspecteur de bibliothèque**, entrez « outil » dans la **zone de recherche** pour afficher plus facilement tous les éléments de la barre d’outils disponibles :

![Inspecteur de bibliothèque, filtré pour afficher les éléments de barre d’outils](toolbar-images/edit02.png "Inspecteur de bibliothèque, filtré pour afficher les éléments de barre d’outils")

Faites glisser une barre d’outils dans la fenêtre de l' **éditeur d’interface**. Une fois la barre d’outils sélectionnée, configurez son comportement en définissant des propriétés dans l' **inspecteur d’attributs**:

![L’inspecteur d’attributs pour une barre d’outils](toolbar-images/edit04.png "L’inspecteur d’attributs pour une barre d’outils")

Les propriétés suivantes sont disponibles :

1. **Display** -contrôle si la barre d’outils affiche des icônes, du texte ou les deux
2. **Visible au lancement** -si cette option est sélectionnée, la barre d’outils est visible par défaut.
3. **Personnalisable** : si cette option est sélectionnée, les utilisateurs peuvent modifier et personnaliser la barre d’outils.
4. **Séparateur** -si cette option est sélectionnée, une ligne horizontale fine sépare la barre d’outils du contenu de la fenêtre.
5. **Size** -définit la taille de la barre d’outils
6. **Enregistrement automatique** -si cette option est sélectionnée, l’application conserve les modifications de configuration de la barre d’outils d’un utilisateur au fil des lancements de l’application.

Sélectionnez l’option **enregistrement automatique** et laissez les paramètres par défaut de toutes les autres propriétés. 

Après avoir ouvert la barre d’outils dans la hiérarchie de l' **interface**, affichez la boîte de dialogue de personnalisation en sélectionnant un élément de la barre d’outils :

![Personnalisation de la barre d’outils](toolbar-images/edit05.png "Personnalisation de la barre d’outils")

Utilisez cette boîte de dialogue pour définir les propriétés des éléments qui font déjà partie de la barre d’outils, pour concevoir la barre d’outils par défaut de l’application et pour fournir des éléments de barre d’outils supplémentaires à sélectionner lors de la personnalisation de la barre d’outils. Pour ajouter des éléments à la barre d’outils, faites-les glisser à partir de l' **inspecteur de bibliothèque**:

![L’inspecteur de bibliothèque](toolbar-images/edit06.png "L’inspecteur de bibliothèque")

Les éléments de barre d’outils suivants peuvent être ajoutés :

- **Image de la barre d’outils** : élément de barre d’outils avec une image personnalisée sous forme d’icône.
- **Élément de barre d’outils d’espace flexible** : espace flexible utilisé pour justifier les éléments de barre d’outils suivants. Par exemple, un ou plusieurs éléments de barre d’outils suivis d’un élément de barre d’outils d’espace flexible et d’un autre élément de barre d’outils épinglerait le dernier élément sur le côté droit de la barre d’outils.
- **Espace de barre d’outils espace** -espace fixe entre les éléments de la barre d’outils
- **Élément de barre d’outils séparateur** : un séparateur visible entre deux ou plusieurs éléments de barre d’outils, pour le regroupement
- **Personnaliser l’élément de barre d’outils** : permet aux utilisateurs de personnaliser la barre d’outils
- **Imprimer l’élément de la barre d’outils** -permet aux utilisateurs d’imprimer le document ouvert
- **Élément de barre d’outils afficher les couleurs** -affiche le sélecteur de couleurs système standard : 

     ![Sélecteur de couleurs système](toolbar-images/edit07.png "Sélecteur de couleurs système")

- **Afficher l’élément de la barre d’outils des polices** -affiche la boîte de dialogue police système standard : 

     ![Sélecteur de police](toolbar-images/edit08.png "Sélecteur de police")

> [!IMPORTANT]
> Comme vous le verrez plus tard, de nombreux contrôles d’interface utilisateur de cacao standard, tels que les champs de recherche, les contrôles segmentés et les curseurs horizontaux, peuvent également être ajoutés à une barre d’outils.

### <a name="adding-an-item-to-a-toolbar"></a>Ajout d’un élément à une barre d’outils

Pour ajouter un élément à une barre d’outils, sélectionnez la barre d’outils dans la hiérarchie de l' **interface** et cliquez sur l’un de ses éléments, ce qui entraîne l’affichage de la boîte de dialogue de personnalisation. Ensuite, faites glisser un nouvel élément de l' **inspecteur de bibliothèque** vers la zone éléments de **barre d’outils autorisés** :

![Les éléments de barre d’outils autorisés dans la boîte de dialogue de personnalisation de la barre d’outils](toolbar-images/add01.png "Les éléments de barre d’outils autorisés dans la boîte de dialogue de personnalisation de la barre d’outils")

Pour vous assurer qu’un nouvel élément fait partie de la barre d’outils par défaut, faites-le glisser vers la zone éléments de la **barre d’outils par défaut** : 

![Réorganisation d’un élément de barre d’outils en le faisant glisser](toolbar-images/add02.png "Réorganisation d’un élément de barre d’outils en le faisant glisser")

Pour réorganiser les éléments de la barre d’outils par défaut, faites-les glisser vers la gauche ou la droite.

Ensuite, utilisez l' **inspecteur d’attributs** pour définir les propriétés par défaut de l’élément :

![Personnalisation d’un élément de barre d’outils à l’aide de l’inspecteur d’attributs](toolbar-images/add03.png "Personnalisation d’un élément de barre d’outils à l’aide de l’inspecteur d’attributs")

Les propriétés suivantes sont disponibles :

- **Nom** de l’image-image à utiliser comme icône pour l’élément
- **Étiquette** -texte à afficher pour l’élément dans la barre d’outils
- **Étiquette** de la palette-texte à afficher pour l’élément dans la zone **éléments de barre d’outils autorisés**
- **Tag** : identificateur unique facultatif qui permet d’identifier l’élément dans le code.
- **Identificateur** : définit le type d’élément de barre d’outils. Une valeur personnalisée peut être utilisée pour sélectionner un élément de barre d’outils dans le code.
- **Sélectionnable** -si cette option est activée, l’élément agit comme un bouton activé/désactivé.

> [!IMPORTANT]
> Ajoutez un élément à la zone **éléments de barre d’outils autorisés** mais pas à la barre d’outils par défaut pour fournir des options de personnalisation pour les utilisateurs. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Ajout d’autres contrôles d’interface utilisateur à une barre d’outils

Plusieurs éléments d’interface utilisateur de cacao, tels que les champs de recherche et les contrôles segmentés, peuvent également être ajoutés à une barre d’outils.

Pour ce faire, ouvrez la barre d’outils dans la hiérarchie de l' **interface** et sélectionnez un élément de barre d’outils pour ouvrir la boîte de dialogue de personnalisation. Faites glisser un **champ de recherche** de l’inspecteur de **bibliothèque** vers la zone **éléments de barre d’outils autorisés** :

![Utilisation de la boîte de dialogue Personnalisation de la barre d’outils](toolbar-images/add05.png "Utilisation de la boîte de dialogue Personnalisation de la barre d’outils")

À partir de là, utilisez Interface Builder pour configurer le champ de recherche et l’exposer au code par le biais d’une action ou d’une sortie.

## <a name="built-in-toolbar-item-support"></a>Prise en charge des éléments de barre d’outils intégrés

Par défaut, plusieurs éléments d’interface utilisateur de cacao interagissent avec les éléments de barre d’outils standard. Par exemple, faites glisser un **affichage de texte** dans la fenêtre de l’application et positionnez-le pour remplir la zone de contenu :

[![Ajout d’un affichage de texte à l’application](toolbar-images/edit09.png "Ajout d’un affichage de texte à l’application")](toolbar-images/edit09-large.png#lightbox)

Enregistrez le document, revenez à Visual Studio pour Mac à synchroniser avec Xcode, exécutez l’application, entrez du texte, sélectionnez-le, puis cliquez sur l’élément de barre d’outils **couleurs** . Notez que l’affichage de texte fonctionne automatiquement avec le sélecteur de couleurs :

![Fonctionnalités intégrées de la barre d’outils avec un affichage de texte et un sélecteur de couleurs](toolbar-images/edit10.png "Fonctionnalités intégrées de la barre d’outils avec un affichage de texte et un sélecteur de couleurs")

## <a name="using-images-with-toolbar-items"></a>Utilisation d’images avec des éléments de barre d’outils

À l’aide d’un élément de la **barre d’outils image**, toute image bitmap ajoutée au dossier **ressources** (et avec une action de génération de **ressource Bundle**) peut être affichée dans la barre d’outils sous la forme d’une icône :

1. Dans Visual Studio pour Mac, dans la **panneau solutions**, cliquez avec le bouton droit sur le dossier **ressources** , puis sélectionnez **Ajouter** > **Ajouter des fichiers**.
2. Dans la boîte de dialogue **Ajouter des fichiers** , accédez aux images de votre choix, sélectionnez-les et cliquez sur le bouton **ouvrir** : 

    [![Sélection des images à ajouter](toolbar-images/edit11.png "Sélection des images à ajouter")](toolbar-images/edit11-large.png#lightbox)

3. Sélectionnez **copier**, cochez **la case utiliser la même action pour tous les fichiers sélectionnés**, puis cliquez sur **OK**:

    ![Sélection de l’action de copie pour les images ajoutées](toolbar-images/edit12.png "Sélection de l’action de copie pour les images ajoutées")

4. Dans la **panneau solutions**, double-cliquez sur **MainWindow. XIB** pour l’ouvrir dans Xcode.

5. Sélectionnez la barre d’outils dans la hiérarchie de l' **interface** et cliquez sur l’un de ses éléments pour ouvrir la boîte de dialogue de personnalisation.

6. Faites glisser un **élément de barre d’outils image** de l' **inspecteur de bibliothèque** vers la zone éléments de **barre d’outils autorisés** de la barre d’outils : 

    ![Élément de barre d’outils d’image ajouté à la zone éléments de barre d’outils autorisés](toolbar-images/edit14.png "Élément de barre d’outils d’image ajouté à la zone éléments de barre d’outils autorisés")

7. Dans l' **inspecteur d’attributs**, sélectionnez l’image qui vient d’être ajoutée dans Visual Studio pour Mac : 

    ![Définition d’une image personnalisée pour un élément de barre d’outils](toolbar-images/edit15.png "Définition d’une image personnalisée pour un élément de barre d’outils")

8. Définissez l' **étiquette** sur « Trash » et l' **étiquette de palette** sur « Effacer le document » : 

    ![Définition de l’étiquette de l’élément de barre d’outils et de l’étiquette de palette](toolbar-images/edit16.png "Définition de l’étiquette de l’élément de barre d’outils et de l’étiquette de palette")

9. Faites glisser un **élément de barre d’outils de séparateur** de l’inspecteur de **bibliothèque** vers la zone **éléments de barre d’outils autorisés** de la barre d’outils : 

    [![Élément de barre d’outils séparateur ajouté à la zone éléments de barre d’outils autorisés](toolbar-images/edit17.png "Élément de barre d’outils séparateur ajouté à la zone éléments de barre d’outils autorisés")](toolbar-images/edit17-large.png#lightbox)

10. Faites glisser l’élément séparateur et l’élément « corbeille » vers la zone éléments de la **barre d’outils par défaut** et définissez l’ordre des éléments de la barre d’outils de gauche à droite comme suit (couleurs, polices, séparateur, corbeille, espace flexible, imprimer) : 

    ![Éléments de la barre d’outils par défaut](toolbar-images/edit18.png "Éléments de la barre d’outils par défaut")

11. Enregistrez les modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Exécutez l’application pour vérifier que la nouvelle barre d’outils est affichée par défaut :

![Une barre d’outils avec des éléments par défaut personnalisés](toolbar-images/edit19.png "Une barre d’outils avec des éléments par défaut personnalisés")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Exposition d’éléments de barre d’outils avec des sorties et des actions

Pour accéder à une barre d’outils ou à un élément de barre d’outils dans le code, celui-ci doit être attaché à une prise ou à une action :

1. Dans la **panneau solutions**, double-cliquez sur **main. Storyboard** pour l’ouvrir dans Xcode.
2. Assurez-vous que la classe personnalisée « WindowController » a été assignée au contrôleur de fenêtre principale dans l' **inspecteur d’identité**:

    [![Utilisation de l’inspecteur d’identité pour définir une classe personnalisée pour le contrôleur de fenêtre](toolbar-images/edit20a.png "Utilisation de l’inspecteur d’identité pour définir une classe personnalisée pour le contrôleur de fenêtre")](toolbar-images/edit20a-large.png#lightbox)

3. Ensuite, sélectionnez l’élément de barre d’outils dans la hiérarchie de l' **interface**: 

    ![Sélection de l’élément de barre d’outils dans la hiérarchie d’interface](toolbar-images/edit20.png "Sélection de l’élément de barre d’outils dans la hiérarchie d’interface")  

4. Ouvrez la **vue Assistant**, sélectionnez le fichier **WindowController. h** et faites-le glisser à partir de l’élément de barre d’outils vers le fichier **WindowController. h** .
5. Définissez le type de **connexion** sur **action**, entrez « trashDocument » comme **nom**, puis cliquez sur le bouton **se connecter** : 

    [![Configuration d’une action pour un élément de barre d’outils](toolbar-images/edit23.png "Configuration d’une action pour un élément de barre d’outils")](toolbar-images/edit23-large.png#lightbox)

6. Exposez l' **affichage de texte** sous forme de sortie appelée « documentEditor » dans le fichier **ViewController. h** : 

    [![Configuration d’une prise pour l’affichage de texte](toolbar-images/edit24.png "Configuration d’une prise pour l’affichage de texte")](toolbar-images/edit24-large.png#lightbox)

7. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Dans Visual Studio pour Mac, modifiez le fichier **ViewController.cs** et ajoutez le code suivant :

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Modifiez ensuite le fichier **WindowController.cs** et ajoutez le code suivant au bas de la `WindowController` classe :

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Lors de l’exécution de l’application, l’élément de barre d’outils **Corbeille** est actif :

![Barre d’outils avec un élément de la corbeille Active](toolbar-images/edit25.png "Barre d’outils avec un élément de la corbeille Active")

Notez que l’élément de la barre d’outils **Corbeille** peut maintenant être utilisé pour supprimer du texte.

## <a name="disabling-toolbar-items"></a>Désactivation des éléments de barre d’outils

Pour désactiver un élément dans une barre d’outils, créez `NSToolbarItem` une classe personnalisée et substituez la `Validate` méthode. Ensuite, dans Interface Builder, assignez le type personnalisé à l’élément que vous souhaitez activer/désactiver.

Pour créer une classe `NSToolbarItem` personnalisée, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > un**nouveau fichier...** . Sélectionnez classe **générale** > **vide**, entrez « ActivatableItem » comme **nom**, puis cliquez sur le bouton **nouveau** : 

![Ajout d’une classe vide dans Visual Studio pour Mac](toolbar-images/custom01.png "Ajout d’une classe vide dans Visual Studio pour Mac")

Modifiez ensuite le fichier **ActivatableItem.cs** de façon à ce qu’il soit lu comme suit :

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

Double-cliquez sur **main. Storyboard** pour l’ouvrir dans Xcode. Sélectionnez l’élément de barre d’outils **Corbeille** créé ci-dessus et remplacez sa classe par « ActivatableItem » dans l' **inspecteur d’identité**:

![Définition d’une classe personnalisée pour un élément de barre d’outils](toolbar-images/custom02.png "Définition d’une classe personnalisée pour un élément de barre d’outils")

Créez une prise appelée `trashItem` pour l’élément de barre d’outils **Corbeille** . Enregistrez les modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode. Enfin, ouvrez **MainWindow.cs** et mettez à `AwakeFromNib` jour la méthode pour lire comme suit :

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Exécutez l’application et notez que l’élément **Corbeille** est maintenant désactivé dans la barre d’outils :

![Barre d’outils avec élément de poubelle inactif](toolbar-images/custom03.png "Barre d’outils avec élément de poubelle inactif")

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des barres d’outils et des éléments de barre d’outils dans une application Xamarin. Mac. Il a décrit comment créer et gérer des barres d’outils dans les Interface Builder de Xcode, comment certains contrôles d’interface utilisateur fonctionnent automatiquement avec des éléments de barre d' C# outils, comment utiliser des barres d’outils dans le code et comment activer et désactiver des éléments de barre d’outils.

## <a name="related-links"></a>Liens associés

- [MacToolbar (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactoolbar)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Instructions relatives à l’interface humaine pour les barres d’outils](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Présentation des barres d’outils](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
