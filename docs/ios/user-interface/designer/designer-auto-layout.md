---
title: Disposition automatique avec le Xamarin Designer pour iOS
description: Ce guide présente la disposition automatique iOS et explique comment utiliser l’Xamarin Designer pour iOS pour créer et modifier des dispositions à l’aide de contraintes. Il traite également de la modification des contraintes dans le code, de l’animation des modifications des contraintes, etc.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: f931397f50b6b7aece099efb775a6dda560bf0eb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280002"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Disposition automatique avec le Xamarin Designer pour iOS

La disposition automatique (également appelée « disposition adaptative ») est une approche de conception réactive. Contrairement au système de disposition transitoire, où l’emplacement de chaque élément est codé en dur à un point de l’écran, la disposition automatique concerne les *relations* , c’est-à-dire les positions des éléments par rapport à d’autres éléments sur l’aire de conception. Au cœur de la disposition automatique se trouve l’idée de contraintes ou de règles qui définissent le positionnement d’un élément ou d’un ensemble d’éléments dans le contexte d’autres éléments à l’écran. Étant donné que les éléments ne sont pas liés à une position particulière sur l’écran, les contraintes aident à créer une disposition adaptative qui semble correcte sur différentes tailles d’écran et orientations de périphérique.

Dans ce guide, nous allons introduire des contraintes et savoir comment les utiliser dans le concepteur iOS Xamarin. Ce guide ne couvre pas l’utilisation des contraintes par programmation. Pour plus d’informations sur l’utilisation de la disposition automatique par programmation, reportez-vous à la [documentation Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Configuration requise

Le Xamarin Designer pour iOS est disponible dans Visual Studio pour Mac dans Visual Studio 2017 et versions ultérieures sur Windows.

Ce guide suppose que vous connaissez les composants du concepteur à partir du Guide de [Présentation du concepteur iOS](~/ios/user-interface/designer/introduction.md) .

## <a name="introduction-to-constraints"></a>Présentation des contraintes

Une contrainte est une représentation mathématique de la relation entre deux éléments à l’écran. Le fait de représenter la position d’un élément d’interface utilisateur en tant que relation mathématique résout plusieurs problèmes associés au codage en dur de l’emplacement d’un élément d’interface utilisateur. Par exemple, si nous devions placer un bouton 20px à partir du bas de l’écran en mode portrait, la position du bouton serait en dehors de l’écran en mode paysage. Pour éviter cela, nous pouvons définir une contrainte qui place le bord inférieur du bouton 20px à partir du bas de la vue. La position du bord du bouton est alors calculée en tant que *bouton. Bottom = View. Bottom-20px*, ce qui placerait le bouton 20px à partir du bas de la vue en mode portrait et paysage. La possibilité de calculer le positionnement en fonction d’une relation mathématique est de savoir ce qui rend les contraintes utiles dans la conception de l’interface utilisateur.

Quand nous définissons une contrainte, nous `NSLayoutConstraint` créons un objet qui prend comme arguments les objets à contraindre et les propriétés, ou *attributs*, sur lesquelles la contrainte agira. Dans le concepteur iOS, les attributs incluent des bords tels que les bords *gauche*, *droit*, *supérieur*et *inférieur* d’un élément. Elles incluent également des attributs de taille, tels que la *hauteur* et la *largeur*, et l’emplacement du point central *, centerus et* *CenterY*. Par exemple, lorsque nous ajoutons une contrainte sur la position de la limite gauche de deux boutons, le concepteur génère le code suivant sous les couvertures :

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

La section suivante traite de l’utilisation des contraintes à l’aide du concepteur iOS, notamment l’activation et la désactivation de la disposition automatique et l’utilisation de la barre d’outils contraintes.

## <a name="enable-auto-layout"></a>Activer la disposition automatique

Le mode de contrainte est activé pour la configuration du concepteur iOS par défaut. Toutefois, si vous devez l’activer ou la désactiver manuellement, vous pouvez le faire en deux étapes :

1. Cliquez sur un espace vide sur l’aire de conception. Cela Désélectionne tous les éléments et affiche les propriétés du document de la table de montage séquentiel.
1. Activez ou désactivez la case à cocher **utiliser la disposition automatique** dans le volet des propriétés :

    ![](designer-auto-layout-images/image01.png "La case à cocher utiliser la disposition automatique dans le volet des propriétés")


Par défaut, aucune contrainte n’est créée ou visible sur l’aire de conception. Au lieu de cela, ils sont déduits automatiquement à partir des informations de frame au moment de la compilation. Pour ajouter des contraintes, nous devons sélectionner un élément sur l’aire de conception et lui ajouter des contraintes. Nous pouvons le faire à l’aide de la **barre d’outils contrainte**.

## <a name="constraints-toolbar"></a>Barre d’outils contraintes

 [![](designer-auto-layout-images/toolbarnew.png "Commandes du menu contextuel")](designer-auto-layout-images/toolbarnew.png#lightbox)

La barre d’outils contraintes a été mise à jour et se compose maintenant de deux parties principales :

- **Bouton bascule en mode contraintes**: Précédemment, vous avez entré le mode contraintes en cliquant à nouveau sur une vue sélectionnée sur l’aire de conception. Vous devez maintenant utiliser ce bouton bascule dans la barre des contraintes :

  ![bascule entre les modes de contraintes](designer-auto-layout-images/constraints.png)

- **Un bouton « mettre à jour les contraintes » :** Il est important de noter que les modifications dépendent de si vous êtes en mode de modification des contraintes.
  - En mode de modification de contrainte, ce bouton ajuste les contraintes pour qu’elles correspondent au frame d’élément.
  - En mode d’édition de frame, ce bouton ajuste le frame d’élément pour qu’il corresponde à la position que les contraintes définissent.


## <a name="surface-based-constraint-editing"></a>Modification des contraintes basées sur une surface

Dans la section précédente, nous avons appris à ajouter des contraintes DEFAULT et à supprimer des contraintes à l’aide de la barre d’outils contraintes. Pour une modification plus fine des contraintes, nous pouvons interagir avec les contraintes directement sur l’aire de conception. Cette section présente les principes fondamentaux de la modification des contraintes basée sur la surface, y compris les contrôles d’espacement des épingles, les zones de dépôt et l’utilisation de différents types de contraintes.

### <a name="creating-constraints"></a>Création de contraintes

L’outil de concepteur iOS offre deux types de contrôles pour manipuler des éléments sur l’aire de conception. En *faisant glisser des contrôles* et des *contrôles d’espacement de code confidentiel*, comme illustré dans l’image suivante :

![contrôles d’affichage](designer-auto-layout-images/controls.png)

Elles sont basculées en sélectionnant le bouton mode contraintes dans la barre contraintes.

Les poignées en forme de 4 T de chaque côté de l’élément définissent les bords *supérieur*, *droit*, *inférieur*et *gauche* de l’élément pour une contrainte. Les deux poignées I en forme à droite et en bas de l’élément définissent respectivement la *hauteur* et la *largeur* de la contrainte. Le carré moyen gère à *la fois les* contraintes *CenterX et CenterY* .

Pour créer une contrainte, choisissez une poignée et faites-la glisser quelque part sur l’aire de conception. Lorsque vous démarrez l’opération glisser, une série de lignes/zones vertes apparaît sur l’aire indiquant ce que vous pouvez contraindre. Par exemple, dans la capture d’écran ci-dessous, nous allons contraindre le côté supérieur du bouton central :

 [![](designer-auto-layout-images/image07.png "Restriction du côté supérieur du bouton central")](designer-auto-layout-images/image07.png#lightbox)

Notez les trois lignes vertes en pointillés sur les deux autres boutons. Les lignes vertes indiquent les *zones de dépôt*, ou les attributs des autres éléments auxquels nous pouvons limiter. Dans la capture d’écran ci-dessus, les deux autres boutons offrent 3 zones de dépôt verticales ( *bas*, *Centre*, *haut*) pour contraindre notre bouton. La ligne verte en pointillés située en haut de la vue signifie que le contrôleur d’affichage offre une contrainte en haut de la vue, tandis que la zone verte unie signifie que le contrôleur d’affichage offre une contrainte sous le repère de disposition supérieur.

> [!IMPORTANT]
> Les repères de mise en page sont des types spéciaux de cibles de contrainte qui nous permettent de créer des contraintes Top et Bottom qui prennent en compte la présence de barres système, telles que les barres d’État ou les barres d’outils. L’une des principales utilisations consiste à avoir une application compatible entre iOS 6 et iOS 7, puisque la version la plus récente dispose d’une vue de conteneur qui s’étend sous la barre d’État. Pour plus d’informations sur le Guide de disposition supérieur, reportez-vous à la [documentation Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



Les trois sections suivantes présentent l’utilisation de différents types de contraintes.

### <a name="size-constraints"></a>Contraintes de taille

Avec des contraintes de taille ( *hauteur* et *largeur* ), vous avez deux options. La première option consiste à faire glisser la poignée pour limiter à une taille d’élément voisin, comme illustré dans l’exemple ci-dessus. L’autre option consiste à double-cliquer sur la poignée pour créer une contrainte réflexive. Cela nous permet de spécifier une valeur de taille constante, comme illustré dans la capture d’écran ci-dessous :

 [![](designer-auto-layout-images/sizec.png "Faites glisser la poignée pour limiter à une taille d’élément voisin, comme illustré ici")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Contraintes du centre

La poignée carrée *crée une contrainte* *CenterX ou CenterY* , selon le contexte. Si vous faites glisser la poignée carrée, les autres éléments s’affichent pour offrir des zones de dépôt verticales et horizontales, comme illustré dans la capture d’écran ci-dessous :

 [![](designer-auto-layout-images/centerc.png "Contraintes du centre")](designer-auto-layout-images/centerc.png#lightbox)

Si vous choisissez une zone de dépôt verticale, une contrainte *CenterY* est créée. Si vous choisissez une zone de dépôt horizontale, la contrainte sera basée sur *Centera*.

### <a name="combinational-constraints"></a>Contraintes de combinaison

Pour créer des contraintes d’alignement et d’égalité de taille entre deux éléments, vous pouvez sélectionner des éléments dans une barre d’outils supérieure pour spécifier-in Order-horizontal Alignment, Vertical Alignment et Size égalités, comme illustré dans la capture d’écran ci-dessous :

 [![](designer-auto-layout-images/image06.png "Contraintes de combinaison")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualisation et modification des contraintes

Lorsque vous ajoutez une contrainte, elle est affichée sur l’aire de conception sous la forme d’une ligne bleue lorsque vous sélectionnez un élément :

 [![](designer-auto-layout-images/image09.png "Visualisation des contraintes")](designer-auto-layout-images/image09.png#lightbox)

Vous pouvez sélectionner une contrainte en cliquant sur une ligne bleue et en modifiant les valeurs de contrainte directement dans le panneau des propriétés. Vous pouvez également double-cliquer sur une ligne bleue pour afficher un menu segue qui vous permet de modifier les valeurs directement sur l’aire de conception :

 [![](designer-auto-layout-images/image08.png "Modification des contraintes")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problèmes de contrainte

Plusieurs types de problèmes peuvent survenir lors de l’utilisation de contraintes :

- **Contraintes en conflit** : cela se produit lorsque plusieurs contraintes forcent l’élément à avoir des valeurs en conflit pour un attribut et que le moteur de contrainte ne peut pas les réconcilier.
- **Éléments sous-restreints** : les propriétés d’un élément (emplacement + taille) doivent être entièrement couvertes par son ensemble de contraintes et de tailles intrinsèques pour que les contraintes soient valides. Si ces valeurs sont ambiguës, l’élément est dit comme étant sous-contraint.
- Inversion de **trame** : cela se produit lorsque le frame d’un élément et son ensemble de contraintes définissent deux rectangles résultants différents.


Cette section décrit les trois problèmes listés ci-dessus et fournit des détails sur la façon de les gérer.

### <a name="conflicting-constraints"></a>Contraintes en conflit

Les contraintes en conflit sont marquées en rouge et ont un symbole d’avertissement. Le fait de pointer sur les symboles d’avertissement affiche un menu segue avec des informations sur le conflit :

 [![](designer-auto-layout-images/image11.png "Avertissement de contraintes conflictuelles")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Éléments sous-restreints

Les éléments sous-restreints s’affichent en orange et déclenchent l’apparence d’une icône de marqueur orange dans la barre d’objets du contrôleur d’affichage :

 [![](designer-auto-layout-images/image02.png "Les éléments sous-restreints s’affichent en orange")](designer-auto-layout-images/image02.png#lightbox)

Si vous cliquez sur cette icône de marqueur, vous pouvez obtenir des informations sur les éléments sous-restreints dans la scène et résoudre les problèmes en les contraignant entièrement ou en supprimant leurs contraintes, comme illustré dans la capture d’écran ci-dessous :

 [![](designer-auto-layout-images/image10.png "Correction des éléments sous-restreints")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Décalage de la trame

Le décalage de la trame utilise le même code de couleur que les éléments sous-restreints. L’élément est toujours rendu sur la surface à l’aide de son frame natif, mais dans le cas d’une inversion de frame, un rectangle rouge marque l’emplacement où l’élément se terminera lors de l’exécution de l’application, comme illustré dans la capture d’écran ci-dessous :

 [![](designer-auto-layout-images/image05.png "Exemple d’affichage de non-placement de frame")](designer-auto-layout-images/image05.png#lightbox)

Pour résoudre les erreurs de non-placement de l’image, sélectionnez le bouton **mettre à jour les frames en fonction des contraintes** dans la barre d’outils contraintes (bouton à droite) :

 [![](designer-auto-layout-images/image03.png "Bouton de barre d’outils mettre à jour les frames en fonction des contraintes")](designer-auto-layout-images/image03.png#lightbox)

Cela permet d’ajuster automatiquement le frame d’élément pour qu’il corresponde aux positions définies par les contrôles.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modification des contraintes dans le code

Selon les exigences de votre application, il peut arriver que vous deviez modifier une contrainte dans le code. Par exemple, pour redimensionner ou repositionner la vue à laquelle une contrainte est attachée, pour modifier la priorité d’une contrainte ou désactiver complètement une contrainte.

Pour accéder à une contrainte dans le code, vous devez d’abord l’exposer dans le concepteur iOS en procédant comme suit :

1. Créez la contrainte comme d’habitude (à l’aide de l’une des méthodes indiquées ci-dessus).
2. Dans l' **Explorateur de structure du document**, recherchez la contrainte souhaitée, puis sélectionnez-la :

    [![](designer-auto-layout-images/modify01.png "Explorateur du plan du document")](designer-auto-layout-images/modify01.png#lightbox)
3. Ensuite, attribuez un **nom** à la contrainte sous l’onglet **widget** de l' **Explorateur de propriétés**:

    [![](designer-auto-layout-images/modify02.png "Onglet widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Enregistrez les modifications apportées.

Une fois les modifications apportées ci-dessus effectuées, vous pouvez accéder à la contrainte dans le code et modifier ses propriétés. Par exemple, vous pouvez utiliser la commande suivante pour définir la hauteur de la vue attachée sur zéro :

```csharp
ViewInfoHeight.Constant = 0;
```

À partir du paramètre suivant pour la contrainte dans le concepteur iOS :

[![](designer-auto-layout-images/modify03.png "Modification d’une contrainte dans l’Explorateur de propriétés")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>Passe de disposition différée

Au lieu de mettre à jour instantanément la vue attachée en réponse à des modifications de contrainte, le moteur de disposition automatique planifie une _passe de disposition différée_ pour un avenir proche. Au cours de cette passe différée, non seulement la contrainte de la vue donnée est mise à jour, mais les contraintes pour chaque vue de la hiérarchie sont recalculées et mises à jour pour s’ajuster à la nouvelle disposition.

À tout moment, vous pouvez planifier votre propre passe de disposition différée en appelant `SetNeedsLayout` les `SetNeedsUpdateConstraints` méthodes ou de la vue parent. 

La passe de disposition différée se compose de deux passes uniques dans la hiérarchie d’affichage :

- **Le test de mise à jour réussit** , le moteur de disposition automatique traverse la hiérarchie d’affichage et appelle `UpdateViewConstraints` la méthode sur tous les contrôleurs `UpdateConstraints` d’affichage et la méthode sur toutes les vues.
- À nouveau, le **moteur de disposition** automatique parcourt la hiérarchie d’affichage, mais cette fois appelle la `ViewWillLayoutSubviews` méthode sur tous les contrôleurs d’affichage `LayoutSubviews` et la méthode sur toutes les vues. La `LayoutSubviews` méthode met à jour la `Frame` propriété de chaque sous-affichage avec le rectangle calculé par le moteur de disposition automatique.

### <a name="animating-constraint-changes"></a>Animer des modifications de contrainte

En plus de modifier les propriétés de contrainte, vous pouvez utiliser l’animation principale pour animer les modifications apportées aux contraintes d’une vue. Par exemple :

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

Ici, la clé appelle la `LayoutIfNeeded` méthode de la vue parente dans le bloc d’animation. Cela indique à la vue de dessiner chaque « frame » de l’emplacement animé ou de la modification de la taille. Sans cette ligne, la vue est simplement alignée sur la version finale sans animation.

## <a name="summary"></a>Récapitulatif

Ce guide a introduit la disposition iOS auto (ou « Adaptive ») et le concept de contraintes sous forme de représentations mathématiques des relations entre les éléments sur l’aire de conception. Il a décrit comment activer la disposition automatique dans le concepteur iOS, utiliser la **barre d’outils contraintes**et modifier des contraintes individuellement sur l’aire de conception. Ensuite, il a expliqué comment résoudre trois problèmes courants liés aux contraintes. Enfin, il a montré comment modifier des contraintes dans le code.

## <a name="related-links"></a>Liens associés

- [Introduction aux storyboards](~/ios/user-interface/storyboards/index.md)
- [Procédures pas à pas pour les contrôles iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Présentation de Android Designer](~/android/user-interface/android-designer/index.md)
- [Contraintes de programmation](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple-Guide de disposition automatique](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
