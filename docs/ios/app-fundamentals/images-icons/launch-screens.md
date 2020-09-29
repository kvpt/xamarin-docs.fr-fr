---
title: Écrans de lancement pour les applications Xamarin. iOS
description: Cet article explique comment créer un écran de lancement d’application pour tous les appareils iOS, quelle que soit la résolution et l’orientation, à l’aide d’un seul Storyboard unifié.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2018
ms.openlocfilehash: f4f24ee8bfc6bdde0becb9539ff9e2f532d06381
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432027"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Écrans de lancement pour les applications Xamarin. iOS

_Cet article explique comment créer un écran de lancement d’application pour tous les appareils iOS, quelle que soit la résolution et l’orientation, à l’aide d’un seul Storyboard unifié._

Avant iOS 8, la création d’un écran de lancement pour une application iOS nécessitait que le développeur fournisse une ressource d’image pour chacun des différents facteurs de forme de périphérique et résolution dans laquelle l’application peut s’exécuter. Depuis la publication d’iOS 8, il est toutefois possible d’utiliser un seul Storyboard unifié pour créer un écran de lancement qui semble correct dans tous les cas.

Cette brève procédure pas à pas explique comment créer un écran de lancement avec une table de montage séquentiel fournie par défaut dans un nouveau projet ou avec un Storyboard ajouté manuellement à un projet existant. Il montre ensuite comment utiliser le concepteur iOS pour ajouter une vue d’image et une étiquette à la table de montage séquentiel, pour définir des contraintes sur ces vues et pour vérifier que la table de montage séquentiel semble correcte pour différents appareils et orientations.

<a name="storyboard"></a>

## <a name="managing-launch-screens-with-storyboards"></a>Gestion des écrans de lancement avec les storyboards

Dans iOS 8 (et versions ultérieures), le développeur peut créer une table de montage séquentiel unifiée spéciale pour fournir l’écran de lancement au lieu d’utiliser une ou plusieurs images de lancement statiques. Lorsque vous créez une table de montage séquentiel de lancement dans le concepteur iOS, utilisez les classes de taille et la disposition automatique pour définir différentes dispositions pour différents environnements d’affichage. En utilisant des classes de taille et une disposition automatique, le développeur peut créer un écran de lancement unique qui s’affiche correctement sur tous les appareils et tous les environnements d’affichage.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans Visual Studio pour Mac, créez un nouveau projet en sélectionnant **fichier > nouvelle solution** , puis en choisissant **application avec affichage unique**: 

    ![Fenêtre nouveau projet, avec une application avec affichage unique sélectionnée](launch-screens-images/launch01.png)

    - Par défaut, un nouveau projet comprend un fichier **écran d’installation. Storyboard** qui définit l’interface d’écran de lancement. 
    - Pour ajouter à la place un plan conceptuel d’écran de lancement à un projet existant, cliquez avec le bouton droit sur le nom du projet dans le **panneau solutions** et choisissez **Ajouter > nouveau fichier...** , puis sélectionnez **lancer l’écran**:

    ![Fenêtre nouveau fichier, avec l’écran de lancement iOS sélectionné](launch-screens-images/launch01b.png)

    - Nommez le fichier **écran d’installation** ou un autre nom de votre choix.

2. Configurez le projet pour utiliser le Storyboard approprié pour son écran de lancement :

    - Double-cliquez sur le fichier **info. plist** dans le **panneau solutions** pour l’ouvrir et le modifier.
    - Dans la section **images de lancement** , assurez-vous que l’écran de **lancement** est défini sur le nom de la table de montage séquentiel appropriée :

    ![Sélecteur d’écran de lancement dans info. plist](launch-screens-images/launch02.png)

    - Par défaut, un nouveau projet est configuré pour utiliser **écran d’installation. Storyboard** comme écran de lancement.

3. Ajoutez une image au catalogue de ressources **. xcassets** Assets pour qu’il soit disponible pour une utilisation sur l’écran de lancement. Pour plus d’informations, consultez la section [Ajout d’images à un ensemble d’images du catalogue de composants dans](~/ios/app-fundamentals/images-icons/displaying-an-image.md) le guide [affichage d’une image](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

4. Ouvrez **écran d’installation. Storyboard** pour le modifier en double-cliquant dessus dans la **panneau solutions**.

5. Choisissez un appareil et une orientation sur lesquels afficher un aperçu de la table de montage séquentiel d’écran de lancement dans le concepteur iOS. Ouvrez le panneau de sélection de l’appareil dans la barre d’outils inférieure, puis sélectionnez **iPhone** et **portrait**.

    ![Barre d’outils sélection d’appareil](launch-screens-images/launch05.png)

    - Notez que la sélection d’un appareil et de l’orientation modifie uniquement la manière dont le concepteur iOS affiche un aperçu de la conception. Quelle que soit la sélection effectuée ici, les contraintes nouvellement ajoutées sont appliquées sur l’ensemble des appareils et des orientations, sauf si le bouton **modifier les traits** a été utilisé pour spécifier la valeur dans le cas contraire. 

6. Définissez la couleur d' **arrière-plan** de la vue principale du contrôleur d’affichage. Sélectionnez la vue en cliquant au milieu du contrôleur d’affichage et ajustez la couleur d’arrière-plan à l’aide de la **panneau Propriétés**:

    ![Vue unique avec une couleur d’arrière-plan violette](launch-screens-images/launch06.png)

7. Ajoutez une **vue d’image** à l’écran de lancement et définissez son **image**source :

    - Faites glisser une **vue image** du **panneau boîte à outils** vers le centre de la vue.
    - Une fois **la vue d’image** sélectionnée, dans la section **widget** du **panneau Propriétés** définissez la propriété **image** sur l’ensemble d’images déjà ajouté au catalogue de ressources **. xcassets** . Repositionnez et redimensionnez la **vue d’image** en fonction des besoins :
    
    ![Affichage des images avec le jeu de propriétés image](launch-screens-images/launch07.png)

8. Ajoutez une **étiquette** en dessous de la **vue image** et utilisez la **panneau Propriétés** pour définir ses attributs : 

    ![Étiquette avec le texte et la couleur définis](launch-screens-images/launch08.png)

9. Basculez en mode de modification des contraintes en utilisant le bouton de droite dans la **barre d’outils contraintes**:
    
    ![Bouton du mode de modification des contraintes](launch-screens-images/launch09.png)

10. Ajoutez des contraintes à la **vue image**, en définissant sa hauteur et sa largeur et en la recentrant horizontalement et verticalement :

    ![Affichage des images avec contraintes de disposition](launch-screens-images/launch10.png)

    - Pour plus d’informations sur l’ajout de contraintes, consultez [disposition automatique avec l’Xamarin Designer pour iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Ajoutez des contraintes à l' **étiquette**, centrez-la horizontalement, en lui donnant une hauteur et une largeur, et en la positionnant une distance fixe verticalement à partir de la **vue d’image**:

    ![Étiquette avec contraintes de disposition](launch-screens-images/launch11.png)

12. Testez les autres périphériques et orientations pour vérifier que la conception est similaire à celle prévue dans tous les scénarios. Dans les cas où des ajustements doivent être effectués pour un appareil ou une orientation spécifique, utilisez le bouton **modifier les traits** pour ajouter des contraintes pour des classes de taille spécifiques :

    ![Écran de lancement rendu sous la forme d’un iPhone X utilisant l’orientation paysage](launch-screens-images/launch12.png)

13. Enregistrez les modifications apportées à la table de montage séquentiel. Exécutez l’application sur un simulateur ou un appareil, et l’écran de lancement sera visible lors du lancement de l’application.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Créez un projet. Dans Visual Studio, sélectionnez **fichier > nouveau > projet > Visual C# > iPhone & iPad > application iOS (Xamarin)**:

    ![La fenêtre nouveau projet, avec application iOS (Xamarin) sélectionnée](launch-screens-images/launch01.w157.png)

    Sélectionnez le modèle **application avec affichage unique** , puis cliquez sur **OK**:

    ![Modèle d’application avec affichage unique](launch-screens-images/launch01-2.w157.png)

2. Si les **ressources > écran d’installation. XIB** existent dans le **Explorateur de solutions**, supprimez-la en cliquant avec le bouton droit sur le fichier et en choisissant **supprimer**. Ce fichier sera remplacé par un Storyboard à l’étape suivante.

3. Créez un Storyboard à utiliser comme écran de lancement. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet et choisissez **Ajouter > nouvel élément...** suivi d’un **Storyboard vide**. Nommez ce Storyboard **écran d’installation. Storyboard** , puis cliquez sur **Ajouter**:

    ![La fenêtre Ajouter un nouvel élément, avec une table de montage séquentiel vide sélectionnée](launch-screens-images/launch03.w157.png)

4. Configurez le projet pour utiliser **écran d’installation. Storyboard** comme Storyboard d’écran de lancement :

    - Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Info.plist** pour l’ouvrir et le modifier. 
    - Sous l’onglet **ressources visuelles** , définissez **écran de lancement** sur **écran d’installation**.

    ![Sélecteur d’écran de lancement dans info. plist](launch-screens-images/launch04-vs.png)

5. Ajoutez une image à un catalogue de composants dans le projet afin qu’elle soit disponible sur l’écran de lancement :

    - Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur **catalogues de ressources** , puis sélectionnez Ajouter un **catalogue de composants**. Nommez ces nouveaux **actifs**du catalogue de composants :

    ![La fenêtre Ajouter un nouvel élément, avec le catalogue de ressources sélectionné](launch-screens-images/launch05.w157.png)

    - Ajoutez un nouvel ensemble d’images au catalogue d’actifs des **ressources** , comme décrit dans la section [Ajout d’images à un ensemble d’images du catalogue de composants](~/ios/app-fundamentals/images-icons/displaying-an-image.md) du guide [affichage d’une image](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

6. Ouvrez **écran d’installation. Storyboard** pour le modifier en double-cliquant dessus dans la **Explorateur de solutions**.

    - Pour modifier un fichier de table de montage séquentiel, Visual Studio a besoin d’une connexion active à un hôte de build Mac. Pour plus d’informations, consultez le guide [connexion au Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) .

7. Choisissez un appareil et une orientation sur lesquels afficher un aperçu de la table de montage séquentiel d’écran de lancement dans le concepteur iOS. Ouvrez le panneau de sélection de l’appareil dans la barre d’outils inférieure, puis sélectionnez **iPhone** et **portrait**: 

    ![Barre d’outils sélection d’appareil](launch-screens-images/launch07-vs.png)

    - Notez que la sélection d’un appareil et de l’orientation modifie uniquement la manière dont le concepteur iOS affiche un aperçu de la conception. Quelle que soit la sélection effectuée ici, les contraintes nouvellement ajoutées sont appliquées sur l’ensemble des appareils et des orientations, sauf si le bouton **modifier les traits** a été utilisé pour spécifier la valeur dans le cas contraire. 

8. Ajoutez un **contrôleur d’affichage** à la table de montage séquentiel en faisant glisser l’un de la **boîte à outils** vers l’aire de conception : 

    ![Un contrôleur d’affichage vide a été ajouté à l’aire de conception](launch-screens-images/launch08-vs.png)

9. Définissez la couleur d' **arrière-plan** de la vue principale du contrôleur d’affichage. Sélectionnez la vue en cliquant au milieu du contrôleur d’affichage et ajustez la couleur d’arrière-plan à l’aide de la **fenêtre Propriétés**:
    
    ![Vue unique avec une couleur d’arrière-plan violette](launch-screens-images/launch09-vs.png)

10. Ajoutez une **vue d’image** à l’écran de lancement et définissez son **image**source :

    - Faites glisser une **vue image** de la **boîte à outils** vers le centre de la vue.
    - Avec la **vue d’image** toujours sélectionnée, dans la section **widget** de la **fenêtre Propriétés** , définissez la propriété **image** sur l’ensemble d’images déjà ajouté au catalogue d’actifs des **ressources** . Repositionnez et redimensionnez la **vue d’image** en fonction des besoins :
    
    ![Affichage des images avec le jeu de propriétés image](launch-screens-images/launch10-vs.png)

11. Ajoutez une **étiquette** en dessous de la **vue d’image**:

    - Faites glisser une **étiquette** de la **boîte à outils** vers l’aire de conception, en la plaçant sous la **vue d’image**.
    - Définissez les attributs de l' **étiquette** à l’aide de la **fenêtre Propriétés**:

    ![Étiquette avec le texte et la couleur définis](launch-screens-images/launch11-vs.png) 

12. Basculez en mode de modification des contraintes en utilisant le bouton de droite dans la **barre d’outils contraintes**:
    
    ![Bouton du mode de modification des contraintes](launch-screens-images/launch12-vs.png) 

13. Ajoutez des contraintes à la **vue image**, en définissant sa hauteur et sa largeur et en la recentrant horizontalement et verticalement :

    ![Affichage des images avec contraintes de disposition](launch-screens-images/launch13-vs.png) 

    - Pour plus d’informations sur l’ajout de contraintes, consultez [disposition automatique avec le Xamarin Designer pour iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Ajoutez des contraintes à l' **étiquette**, centrez-la horizontalement, en lui donnant une hauteur et une largeur, et en la positionnant une distance fixe verticalement à partir de la **vue d’image**:
    
    ![Étiquette avec contraintes de disposition](launch-screens-images/launch14-vs.png) 

15. Testez les autres périphériques et orientations pour vérifier que la conception est similaire à celle prévue dans tous les scénarios. Dans les cas où des ajustements doivent être effectués pour un appareil ou une orientation spécifique, utilisez le bouton **modifier les traits** pour ajouter des contraintes pour des classes de taille spécifiques :

    ![Écran de lancement rendu sous la forme d’un iPhone X utilisant l’orientation paysage](launch-screens-images/launch15-vs.png) 

16. Enregistrez les modifications apportées à la table de montage séquentiel. Exécutez l’application sur un simulateur ou un appareil, et l’écran de lancement sera visible lors du lancement de l’application.

-----

> [!NOTE]
> Une table de montage séquentiel utilisée comme écran de lancement _doit_ inclure uniquement des éléments d’interface utilisateur simples et intégrés, et **ne peut** effectuer aucun calcul ni dériver d’une classe personnalisée.

Pour plus d’informations sur la création d’un écran de lancement avec une table de montage séquentiel unifiée, consultez la section [écrans de lancement dynamique](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) du guide [Unified storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="migrating-to-launch-screen-storyboards"></a>Migration vers des storyboards d’écran de lancement

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Quand vous mettez à jour une application existante pour utiliser des storyboards pour ses écrans de lancement, cliquez avec le bouton droit sur le **nom du projet** dans le **Explorateur de solutions** puis sélectionnez **Ajouter**  >  **un nouveau fichier...**. Sélectionnez **iOS**  >  **écran de lancement** iOS, puis cliquez sur le bouton **nouveau** :

![Sélectionner un écran de lancement iOS](launch-screens-images/storyboard02.png)

Ensuite, double-cliquez sur le `Info.plist` fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Sous **écran de lancement**, sélectionnez le nouveau fichier de Storyboard créé ci-dessus.

![Sélectionner le nouveau fichier de Storyboard créé ci-dessus](launch-screens-images/storyboard09.png)

Pour utiliser le nouvel Storyboard comme écran de lancement, procédez comme suit :

1. Double-cliquez sur le `Info.plist` fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Faites défiler jusqu’à la section **images de lancement universel** de l’éditeur, ouvrez la liste déroulante **lancer l’écran** et sélectionnez le nom de la table de montage séquentiel créée ci-dessus : 

    ![Définition de l’écran de lancement sur le Storyboard](launch-screens-images/storyboard08.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Cliquez avec le bouton droit sur le nom du projet dans le **Explorateur de solutions** puis sélectionnez **Ajouter**  >  **un nouveau fichier...**: 

    ![Ajouter un nouveau fichier](launch-screens-images/image012.png)
2. Entrez un nom pour l’écran de lancement, puis cliquez sur le bouton **Ajouter** : 

    ![Entrer un nom pour l’écran de lancement](launch-screens-images/image013.png)
3. Dans la **Explorateur de solutions**, double-cliquez sur le fichier de Storyboard nouvellement créé pour l’ouvrir et le modifier.
4. Assurez-vous que la **classe Size** a la valeur **any** , et **que la vue** est **générique**: 

    ![Assurez-vous que la classe Size a la valeur any, any et la vue Generic.](launch-screens-images/image016.png)
5. Assembly l’écran de lancement à partir de classes de taille, d’éléments d’interface utilisateur simples (tels que `UIImageView` ) et d’images que vous avez inclus dans l’offre groupée de l’application : 

    ![L’écran de lancement de l’assembly dans le concepteur iOS](launch-screens-images/image017.png)
6. Enregistrez les modifications apportées à la table de montage séquentiel.

-----

## <a name="related-links"></a>Liens associés

- [Écrans de lancement dynamique (exemple)](/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Principes de base du concepteur iOS](~/ios/user-interface/designer/index.md)
- [Ajout d’images à un ensemble d’images du catalogue de composants](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Disposition automatique avec le Xamarin Designer pour iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Instructions relatives à l’interface humaine : écran de lancement](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)