---
title: Storyboards dans Xamarin. Mac – Démarrage rapide
description: Ce document fournit une introduction rapide à la création d’interfaces utilisateur macOS avec des storyboards dans Xamarin. Mac. Il décrit comment créer un segue et créer une fenêtre de préférences.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: f6dbbd85a11c492227f0e19ca1a561595660bf20
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937577"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Storyboards dans Xamarin. Mac – Démarrage rapide

En guise d’introduction rapide à l’utilisation des storyboards pour définir l’interface utilisateur d’une application Xamarin. Mac, commençons un nouveau projet Xamarin. Mac. Sélectionnez **Mac**  >  **App**  >  l'**application de cacao** de l’application Mac, puis cliquez sur le bouton **suivant** :

[![Ajout d’une nouvelle application de cacao](quickstart-images/qs01.png)](quickstart-images/qs01.png#lightbox)

Utilisez le **nom** de l’application `MacStoryboard` et cliquez sur le bouton **suivant** :

[![Définition du nom de l’application](quickstart-images/qs02.png)](quickstart-images/qs02.png#lightbox)

Utilisez le nom de **projet** et le nom de **solution** par défaut, puis cliquez sur le bouton **créer** :

[![Le nom du projet et de la solution](quickstart-images/qs03.png)](quickstart-images/qs03.png#lightbox)

Dans la **Explorateur de solutions**, double-cliquez sur le fichier pour l' `Main.storyboard` ouvrir et le modifier dans l’interface Builder de Xcode :

[![Modification de la table de montage séquentiel dans Xcode](quickstart-images/qs04.png)](quickstart-images/qs04.png#lightbox)

Comme vous pouvez le voir ci-dessus, la table de montage séquentiel par défaut définit à la fois la barre de menus de l’application et sa fenêtre principale avec l’affichage et le contrôleur d’affichage. Pour notre exemple d’application, nous allons créer une interface utilisateur qui dispose d’une _vue de contenu_ principale d’un côté et d’une _vue Inspector_ dans la seconde.

Pour ce faire, nous devons d’abord supprimer le contrôleur d’affichage et la vue par défaut qui sont fournis avec le Storyboard en le sélectionnant dans Interface Builder et en appuyant sur la touche **Suppr** :

[![Suppression du contrôleur d’affichage par défaut](quickstart-images/qs05.png)](quickstart-images/qs05.png#lightbox)

Ensuite, tapez `split` dans la zone de **filtre** , sélectionnez le contrôleur d’affichage fractionné vertical et faites-le glisser sur le _aire de conception_:

[![Recherche du contrôleur d’affichage fractionné](quickstart-images/qs06.png)](quickstart-images/qs06.png#lightbox)

Notez que le contrôleur incluait automatiquement deux contrôleurs d’affichage enfants (et leurs vues associées), câblés vers les côtés gauche et droit de l’affichage fractionné. Pour lier le mode fractionné à sa fenêtre parente, appuyez sur la touche **contrôle** , cliquez sur le contrôleur de fenêtre (le cercle bleu dans le cadre du contrôleur de fenêtre) et faites glisser une ligne vers le contrôleur d’affichage fractionné. Sélectionnez **contenu** de la fenêtre dans le menu contextuel :

[![Définition de l’affichage du contenu Windows](quickstart-images/qs07.png)](quickstart-images/qs07.png#lightbox)

Cela liera les deux éléments d’interface à l’aide d’un segue :

[![Segue entre la fenêtre et le contenu](quickstart-images/qs08.png)](quickstart-images/qs08.png#lightbox)

Nous voulons placer un affichage de texte sur le côté gauche de l’affichage fractionné et le faire remplir automatiquement dans la zone disponible lorsque la fenêtre ou le mode fractionné est redimensionné. Faites glisser un affichage de texte sur le contrôleur d’affichage supérieur associé à l’affichage fractionné, puis cliquez sur la contrainte de disposition automatique de **code confidentiel** (la deuxième icône à partir de la droite en bas de la aire de conception).

[![Configuration des contraintes](quickstart-images/qs09.png)](quickstart-images/qs09.png#lightbox)

À partir de là, nous allons cliquer sur les quatre icônes de **poutre** dans le cadre englobant en haut du menu segue de contraintes, puis cliquer sur le bouton **Ajouter des contraintes 4** en bas pour ajouter les contraintes requises.

Si nous revenons à Visual Studio pour Mac et exécutons le projet, vous remarquerez que l’affichage de texte se redimensionne automatiquement pour remplir le côté gauche de l’affichage fractionné en tant que fenêtre ou si le fractionnement est redimensionné :

[![Exemple de l’application en cours d’exécution](quickstart-images/qs10.png)](quickstart-images/qs10.png#lightbox)

Étant donné que nous allons utiliser le côté droit de l’affichage fractionné comme zone d’inspecteur, nous voulons qu’il ait une taille inférieure et l’autorise à être réduit. Revenez à Xcode et modifiez l’affichage du côté droit en le sélectionnant dans la Aire de conception et en cliquant sur l' **inspecteur de taille**. À partir de là, entrez une **largeur** de `250` :

[![Définition de la largeur](quickstart-images/qs11.png)](quickstart-images/qs11.png#lightbox)

Sélectionnez ensuite l’élément fractionné qui représente le côté droit, définissez une **priorité** plus élevée, puis cliquez sur la case à cocher l' **utilisateur peut réduire** :

[![Modification de la priorité de maintien](quickstart-images/qs12.png)](quickstart-images/qs12.png#lightbox)

Si nous revenons à Visual Studio pour Mac et exécutons le projet maintenant, vous remarquerez que le côté droit conserve sa taille réduite et que la fenêtre est redimensionnée :

[![Exemple de l’application en cours d’exécution](quickstart-images/qs13.png)](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue"></a>

## <a name="defining-a-presentation-segue"></a>Définition d’un segue de présentation

Nous allons mettre en page le côté droit de l’affichage fractionné pour qu’il agisse comme un inspecteur pour les propriétés du texte sélectionné. Nous allons faire glisser des contrôles dans la vue inférieure pour mettre en page l’interface utilisateur de l’inspecteur. Pour le dernier contrôle, nous souhaitons afficher un menu segue qui permet à l’utilisateur de sélectionner parmi quatre styles de caractères prédéfinis.

Nous allons ajouter un bouton à l’inspecteur et un contrôleur d’affichage au Aire de conception. Nous allons redimensionner le contrôleur d’affichage afin qu’il ait la même taille que notre menu segue et y ajouter quatre boutons. Ensuite, nous allons **contrôler** clé-cliquer sur le bouton dans la vue Inspector et faire glisser vers le contrôleur d’affichage qui représente notre menu segue :

[![Glissement pour créer un nouveau segue](quickstart-images/qs14.png)](quickstart-images/qs14.png#lightbox)

Dans le menu contextuel, nous allons sélectionner **menu segue**: 

[![Sélection du type de Segue](quickstart-images/qs15.png)](quickstart-images/qs15.png#lightbox)

Enfin, nous allons sélectionner segue dans le Aire de conception et définir le **bord par défaut** sur **Left**. Ensuite, nous allons faire glisser une ligne de la **vue d’ancrage** vers le bouton auquel nous voulons attacher le menu segue :

[![Glissement pour créer un nouveau segue](quickstart-images/qs16.png)](quickstart-images/qs16.png#lightbox)

Si nous revenons à Visual Studio pour Mac, exécutez l’application et cliquez sur le bouton **aucun** dans l’inspecteur, menu segue s’affiche :

[![Exemple de Segue en cours d’exécution](quickstart-images/qs17.png)](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences"></a>

## <a name="creating-app-preferences"></a>Création des préférences de l’application

La plupart des applications macOS standard proposent une _boîte de dialogue de préférence_ qui permet à l’utilisateur de définir plusieurs options qui contrôlent divers aspects de l’application, tels que l’apparence ou les comptes d’utilisateur.

Pour définir une fenêtre de boîte de dialogue des préférences standard, commencez par faire glisser un contrôleur d’affichage d’onglet sur le Aire de conception :

[![Modification de la table de montage séquentiel dans Xcode](quickstart-images/qs18.png)](quickstart-images/qs18.png#lightbox)

Là encore, les deux contrôleurs d’affichage enfants sont automatiquement attachés. Par exemple, nous ajouterons une étiquette à chaque vue qui sera centrée à l’intérieur de celle-ci :

[![Définition des contraintes](quickstart-images/qs19.png)](quickstart-images/qs19.png#lightbox)

Ensuite, nous souhaitons afficher la fenêtre Préférences lorsque l’utilisateur sélectionne l’élément de menu **Préférences...** . Dans la barre de menus, sélectionnez l’élément de menu Préférences, touche **CTRL** + clic, puis faites glisser une ligne vers le contrôleur d’affichage de l’onglet :

[![Glissement pour créer un segue](quickstart-images/qs20.png)](quickstart-images/qs20.png#lightbox)

Dans le menu contextuel, nous allons sélectionner **modal** pour afficher cette fenêtre en tant que boîte de dialogue modale :

[![Sélection du type de Segue](quickstart-images/qs21.png)](quickstart-images/qs21.png#lightbox)

Si nous enregistrons nos modifications, revenez à Visual Studio pour Mac, exécutez l’application et sélectionnez l’élément de menu **Préférences...** , la boîte de dialogue nouvelles préférences s’affichera :

[![Exemple de Segue en cours d’exécution](quickstart-images/qs22.png)](quickstart-images/qs22.png#lightbox)

Vous remarquerez peut-être que cela ne ressemble pas à une fenêtre de boîte de dialogue Préférences d’application macOS standard. Pour résoudre ce problème, incluez deux fichiers image dans le dossier de l’application Xamarin. Mac `Resources` dans le **Explorateur de solutions** et revenez au Interface Builder de Xcode.

Sélectionnez le contrôleur d’affichage d’onglets et changez son **style** en **barre d’outils**: 

[![Définition du style de barre d’onglets](quickstart-images/qs23.png)](quickstart-images/qs23.png#lightbox)

Sélectionnez chaque onglet et donnez-lui une **étiquette** et sélectionnez l’une des images pour la représenter :

[![Configuration de chaque onglet dans Xcode](quickstart-images/qs24.png)](quickstart-images/qs24.png#lightbox)

Si nous enregistrons nos modifications, revenons à Visual Studio pour Mac, exécutez l’application et sélectionnez l’élément de menu **Préférences...** , la boîte de dialogue s’affichera désormais comme une application MacOS standard :

[![Exemple de la fenêtre des préférences en cours d’exécution](quickstart-images/qs25.png)](quickstart-images/qs25.png#lightbox)

Pour plus d’informations, consultez notre documentation [sur l’utilisation des images](~/mac/app-fundamentals/image.md), des [menus](~/mac/user-interface/menu.md), des [fenêtres](~/mac/user-interface/window.md) et des [boîtes de dialogue](~/mac/user-interface/dialog.md) .

## <a name="related-links"></a>Liens associés

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [Présentation de Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
