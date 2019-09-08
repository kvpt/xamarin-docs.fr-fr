---
title: Fonctionnalités de conception de matériaux Xamarin. Android Designer
description: Cette rubrique décrit les fonctionnalités du concepteur qui permettent aux développeurs de créer plus facilement des dispositions matérielles conformes à la conception. Cette section présente et explique comment utiliser la grille de matériau, la palette de couleurs matérielles, l’échelle typographique et l’éditeur de thème.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 67755baf18ad5277e7c2fe8be705522a9b668ff3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756820"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Fonctionnalités de conception de matériaux Xamarin. Android Designer

_Cette rubrique décrit les fonctionnalités du concepteur qui permettent aux développeurs de créer plus facilement des dispositions matérielles conformes à la conception. Cette section présente et explique comment utiliser la grille de matériau, la palette de couleurs matérielles, l’échelle typographique et l’éditeur de thème._

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016 : Tout le monde peut créer de belles applications avec la conception de matériaux**

## <a name="overview"></a>Présentation

Xamarin. Android Designer comprend des fonctionnalités qui facilitent la création de dispositions conformes à la conception. Si vous n’êtes pas familiarisé avec la conception de matériaux, consultez Introduction à la [conception de matériaux](https://material.io/design/introduction).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dans ce guide, nous allons examiner les fonctionnalités de concepteur suivantes :

- *Grille de matériau* &ndash; Une superposition sur la aire de conception qui affiche une grille, l’espacement et les lignes de dessin pour vous aider à placer les widgets de mise en page en fonction des règles de conception de matériau.

- *Éditeur de thème* &ndash; Éditeur de ressources de petite couleur qui vous permet de définir des informations de couleur pour un sous-ensemble d’un thème. Par exemple, vous pouvez afficher un aperçu et modifier les couleurs `colorPrimary`matérielles, `colorAccent`telles que, `colorPrimaryDark`et.

Nous allons examiner chacune de ces fonctionnalités et fournir des exemples de leur utilisation.

## <a name="material-design-grid"></a>Grille de conception de matériau

Le menu de la grille de conception de matériau est disponible à partir de la barre d’outils en haut du concepteur :

[![Grille de conception de matériau](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

Lorsque vous cliquez sur l’icône de grille de conception de matériau, le concepteur affiche une superposition sur la Aire de conception qui comprend les éléments suivants :

- Traits de soulignement (orange)

- Espacement (zones vertes)

- Une grille (lignes bleues)

Ces éléments peuvent être affichés dans la capture d’écran précédente. Chacun de ces éléments de superposition est configurable. Lorsque vous cliquez sur les points de suspension en regard du menu de la grille de conception de matériau, une boîte de dialogue Menu segue s’ouvre pour vous permettre de désactiver/activer la grille, de configurer le placement des lignes de clés et de définir des espaces. Notez que toutes les valeurs sont exprimées en `dp` (pixels indépendants de la densité) :

[![Configuration de la grille, du KEYLINE et de l’espacement](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

Pour ajouter une nouvelle ligne de code, entrez une nouvelle valeur de décalage dans la zone **décalage** , sélectionnez un emplacement (**gauche**, **haut**, **droite**ou **bas**), puis cliquez sur l’icône + pour ajouter le nouveau KEYLINE. De même, pour ajouter un nouvel espacement, entrez respectivement la taille et le décalage (en DP) dans les zones **taille** et **décalage** . Sélectionnez un emplacement (**gauche**, **haut**, **droite**ou **bas**), puis cliquez sur l’icône + pour ajouter le nouvel espacement.

Lorsque vous modifiez ces valeurs de configuration, elles sont enregistrées dans le fichier XML de disposition et réutilisées lorsque vous rouvrez la disposition.

## <a name="theme-editor"></a>Éditeur de thème

L' **éditeur de thèmes** vous permet de personnaliser les informations de couleur pour un sous-ensemble d’attributs de thème. Pour ouvrir l' **éditeur de thème**, cliquez sur l’icône représentant un pinceau dans la barre d’outils :

[![Icône de l’éditeur de thème](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

Bien que l' **éditeur de thème** soit accessible à partir de la barre d’outils pour toutes les versions et tous les niveaux d’API Android cibles, seul un sous-ensemble des fonctionnalités décrites ci-dessous est disponible si le niveau d’API cible est antérieur à l’API 21 (Android 5,0 Lollipop).

Le panneau gauche de l’éditeur de **thème** affiche la liste des couleurs qui composent le thème actuellement sélectionné (dans cet exemple, nous utilisons le `Default Theme`) :

[![Éditeur de thème](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

Lorsque vous sélectionnez une couleur à gauche, le panneau de droite fournit les onglets suivants pour vous aider à modifier cette couleur :

- **Hériter** &ndash; Affiche un diagramme d’héritage de style pour la couleur sélectionnée et répertorie la couleur résolue et le code de couleur affectés à cette couleur de thème.

- **Sélecteur de couleurs** &ndash; Vous permet de remplacer la couleur sélectionnée par une valeur arbitraire.

- **Palette de matériaux** &ndash; Vous permet de remplacer la couleur sélectionnée par une valeur conforme à la conception de matériau.

- **Ressources** &ndash; Vous permet de remplacer la couleur sélectionnée par une des autres ressources de couleur existantes dans le thème.

Nous allons examiner en détail chacun de ces onglets.

### <a name="inherit-tab"></a>Onglet hériter

Comme indiqué dans l’exemple suivant, l’onglet **hériter** répertorie l’héritage de style pour la couleur d' **arrière-plan** du **thème par défaut**:

[![Onglet hériter](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

Dans cet exemple, le **thème par défaut** hérite d’un style qui `@color/background_material_light` utilise mais le remplace par `color/material_grey_50`, qui a une valeur de code de couleur `#fffafafa`de.
Pour plus d’informations sur l’héritage de style, consultez [styles et thèmes](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Sélecteur de couleurs

La capture d’écran suivante illustre le **Sélecteur de couleurs**:

[![Sélecteur de couleurs](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

Dans cet exemple, la couleur d' **arrière-plan** peut être remplacée par une valeur de différentes manières :

- En cliquant directement sur une couleur.
- Saisie des valeurs de teinte, de saturation et de luminosité.
- Saisie de valeurs RVB (rouge, vert, bleu) au format décimal.
- Définition de l’alpha (opacité) de la couleur sélectionnée.
- Saisie directe du code de couleur hexadécimal.

La couleur que vous choisissez dans le sélecteur de couleurs n’est *pas* limitée aux recommandations en matière de conception de matériau ou à l’ensemble des ressources de couleurs disponibles.

### <a name="resources"></a>Ressources

L’onglet **ressources** offre une liste des ressources de couleur déjà présentes dans le thème :

[![Situées](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

L’utilisation de l’onglet **ressources** limite vos choix à cette liste de couleurs. Gardez à l’esprit que si vous choisissez une ressource de couleur qui est déjà assignée à une autre partie du thème, deux éléments adjacents de l’interface utilisateur peuvent « s’exécuter ensemble » (car ils ont la même couleur) et deviennent difficiles à distinguer par l’utilisateur.

### <a name="material-palette"></a>Palette de matériaux

L’onglet **palette de matériaux** ouvre la palette de **couleurs conception de matériau**. Le choix d’une valeur de couleur de cette palette limite votre choix de couleurs pour qu’elle soit cohérente avec les règles de conception de matériau :

[![Palette de matériaux](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

La partie supérieure de la palette de couleurs affiche les couleurs de conception du matériau principal, tandis que le bas de la palette affiche une plage de teintes pour la couleur principale sélectionnée. Par exemple, lorsque vous sélectionnez **Indigo**, une collection de teintes **Indigo** s’affiche au bas de la boîte de dialogue.
Lorsque vous sélectionnez une teinte, la couleur de la propriété est remplacée par la teinte sélectionnée. Dans l’exemple suivant, le `Background Tint` du bouton est remplacé par *Indigo 500*:

![Sélectionner Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint`est défini sur le code de couleur pour *Indigo 500* (`#ff3f51b5`) et le concepteur met à jour la couleur d’arrière-plan pour refléter cette modification :

[![Teinte d’arrière-plan modifiée](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Pour plus d’informations sur la palette de couleurs de conception de matériau, consultez le Guide de la [palette de couleurs](https://material.io/design/color/)conception de matériau.

### <a name="creating-a-new-theme"></a>Création d’un nouveau thème

Dans l’exemple suivant, nous allons utiliser la palette de matériaux pour créer un nouveau thème personnalisé. Tout d’abord, nous allons modifier la couleur d' **arrière-plan** en *bleu 900*:

![Remplacez l’arrière-plan par Blue 900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

Lorsqu’une ressource de couleur est modifiée, un message s’affiche avec le message, *le thème actuel comporte des modifications non enregistrées*:

[![Avertissement sur les modifications non enregistrées](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

La couleur d' **arrière-plan** dans le concepteur a été remplacée par la nouvelle sélection de couleur, mais cette modification n’a pas encore été enregistrée. À ce stade, vous pouvez effectuer l’une des opérations suivantes :

- Cliquez sur **Ignorer les modifications** pour abandonner les nouveaux choix de couleurs (ou choix) et rétablir l’état d’origine du thème.

- Appuyez sur <kbd>CTRL + S</kbd> pour enregistrer les modifications apportées au thème actuellement.

Dans l’exemple suivant, l’utilisateur a appuyé sur <kbd>CTRL + S</kbd> pour que les modifications aient été enregistrées dans **AppTheme**:

[![Modifications enregistrées dans AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>Récapitulatif

Cette rubrique décrit les fonctionnalités de conception de matériau disponibles dans Xamarin. Android Designer. Il a expliqué comment activer et configurer la grille de conception de matériau et explique comment utiliser l’éditeur de thème pour créer des thèmes personnalisés conformes aux règles de conception de matériau.
Pour plus d’informations sur la prise en charge de Xamarin. Android pour la conception de matériaux, consultez [thème matériel](~/android/user-interface/material-theme.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Dans ce guide, nous allons examiner les fonctionnalités de concepteur suivantes :

- *Grille de conception de matériau* &ndash; Une superposition sur la aire de conception qui affiche une grille, l’espacement et les lignes de dessin pour vous aider à placer les widgets de mise en page en fonction des règles de conception de matériau.

- *Palette de couleurs de conception de matériau* &ndash; Boîte de dialogue de remplissage de propriétés qui vous aide à choisir une couleur dans la palette de conception de matériaux officielle.

- *Échelle typographique* Boîte de dialogue de la boîte de dialogue qui vous permet de choisir des paramètres conformes à la conception `textAppearance` pour la propriété des champs de texte. &ndash;

- *Éditeur de thème* &ndash; Éditeur de ressources de petite couleur qui vous permet de définir des informations de couleur pour un sous-ensemble d’un thème. Par exemple, vous pouvez afficher un aperçu et modifier les couleurs `colorPrimary`matérielles, `colorAccent`telles que, `colorPrimaryDark`et.

Nous allons examiner chacune de ces fonctionnalités et fournir des exemples de leur utilisation.

## <a name="material-design-grid"></a>Grille de conception de matériau

Le menu de la grille de conception de matériau est disponible à partir de la barre d’outils en haut du concepteur :

[![Grille de conception de matériau](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

Lorsque vous cliquez sur l’icône de grille de conception de matériau, le concepteur affiche une superposition sur la Aire de conception qui comprend les éléments suivants :

- Traits de soulignement (orange)

- Espacement (zones vertes)

- Une grille (lignes bleues)

Ces éléments peuvent être affichés dans la capture d’écran suivante :

[![KEYLINE, espacement et grille](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

Chacun de ces éléments de superposition est configurable. Quand vous cliquez sur les points&hellip;de suspension () en regard du menu de la grille de conception de matériau, une boîte de dialogue Menu segue s’ouvre pour vous permettre de désactiver/activer la grille, de configurer le placement des lignes de clés et de définir les espaces. Notez que toutes les valeurs sont exprimées en `dp` (pixels indépendants de la densité) :

[![Configuration de la grille, du KEYLINE et de l’espacement](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Pour ajouter une nouvelle ligne de base, entrez une nouvelle valeur de décalage dans la zone **décalage** , sélectionnez un emplacement (**gauche**, **haut**, **droite**ou **bas**), puis cliquez sur l’icône + (qui s’affiche à droite lorsqu’une valeur est entrée) pour ajouter la nouvelle ligne de code. De même, pour ajouter un nouvel espacement, entrez respectivement la taille et le décalage (en DP) dans les zones **taille** et **décalage** . Sélectionnez un emplacement (**gauche**, **haut**, **droite**ou **bas**), puis cliquez sur l’icône + pour ajouter le nouvel espacement.

Lorsque vous modifiez ces valeurs de configuration, elles sont enregistrées dans le fichier XML de disposition et réutilisées lorsque vous rouvrez la disposition.

## <a name="material-design-color-palette"></a>Palette de couleurs de conception de matériau

Chaque élément du panneau des propriétés qui accepte une couleur possède maintenant une icône de palette supplémentaire que vous pouvez utiliser pour ouvrir la palette de couleurs de conception de matériau, comme illustré dans cette capture d’écran :

[![Icône de couleur](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Lorsque vous cliquez sur cette icône, une boîte de dialogue Menu segue s’ouvre pour vous permettre de configurer la couleur de cette propriété à partir de la palette de couleurs de conception de matériau :

[![Palette de couleurs de conception de matériau](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

La partie supérieure de la palette de couleurs affiche les couleurs de conception du matériau principal, tandis que le bas de la palette affiche une plage de teintes pour la couleur principale sélectionnée. Par exemple, lorsque vous sélectionnez **Indigo**, une collection de teintes **Indigo** s’affiche au bas de la boîte de dialogue.
Lorsque vous sélectionnez une teinte, la couleur de la propriété est remplacée par la teinte sélectionnée. Dans l’exemple suivant, le `Background Tint` du bouton est remplacé par *Indigo 500*:

[![Choisir Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint`est défini sur le code de couleur pour *Indigo 500* (`#ff3f51b5`) et le concepteur met à jour la couleur d’arrière-plan du bouton pour refléter cette modification :

[![Modifications de teinte d’arrière-plan](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Pour plus d’informations sur la palette de couleurs de conception de matériau, consultez le Guide de la [palette de couleurs](https://material.io/design/color/)conception de matériau.

## <a name="typographic-scale"></a>Échelle typographique

La section **apparence du texte** de l’onglet **style** du tableau des **Propriétés** contient une icône qui vous permet `TextAppearance` de sélectionner un style conforme à la spécification de conception de matériau :

[![Onglet style](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Lorsque vous cliquez sur cette icône, la boîte de dialogue de l' **échelle typographique** menu segue s’ouvre, qui présente la liste des styles de texte préconfigurés que vous pouvez choisir :

[![Sélecteur de style de texte](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

Dans l’exemple suivant, le fait de cliquer sur **affichage 1** remplace le texte du bouton par la plus grande police d' **affichage 1**:

[![Style d’affichage 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

Le style de texte dans la boîte de dialogue **Echelle typographique** suit le paramètre **thème** . Par exemple, si le thème **clair** est choisi dans le concepteur, la liste des styles de texte disponibles reflète le thème **clair** :

[![Thème clair](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>Éditeur de thème

L' **éditeur de thèmes** vous permet de personnaliser les informations de couleur pour un sous-ensemble d’attributs de thème. Pour ouvrir l' **éditeur de thème**, cliquez sur l’icône représentant un pinceau dans la barre d’outils :

[![Icône de l’éditeur de thème](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

Bien que l' **éditeur de thème** soit accessible à partir de la barre d’outils pour toutes les versions et tous les niveaux d’API Android cibles, seul un sous-ensemble des fonctionnalités décrites ci-dessous est disponible si le niveau d’API cible est antérieur à l’API 21 (Android 5,0 Lollipop).

Le panneau gauche de l’éditeur de **thème** affiche la liste des couleurs qui composent le thème actuellement sélectionné (dans cet exemple, nous utilisons le `Default Theme`) :

[![Éditeur de thème](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

Lorsque vous sélectionnez une couleur à gauche, le panneau de droite fournit les onglets suivants pour vous aider à modifier cette couleur :

- **Hériter** &ndash; Affiche un diagramme d’héritage de style pour la couleur sélectionnée et répertorie la couleur résolue et le code de couleur affectés à cette couleur de thème.

- **Sélecteur de couleurs** &ndash; Vous permet de remplacer la couleur sélectionnée par une valeur arbitraire.

- **Palette de matériaux** &ndash; Vous permet de remplacer la couleur sélectionnée par une valeur conforme à la conception de matériau.

- **Ressources** &ndash; Vous permet de remplacer la couleur sélectionnée par une des autres ressources de couleur existantes dans le thème.

Nous allons examiner en détail chacun de ces onglets.

### <a name="inherit-tab"></a>Onglet hériter

Comme indiqué dans l’exemple suivant, l’onglet **hériter** répertorie l’héritage de style pour la couleur d' **arrière-plan** du **thème par défaut**:

[![Onglet hériter](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

Dans cet exemple, le **thème par défaut** hérite d’un style qui `@color/background_material_dark` utilise mais le remplace par `color/material_grey_850`, qui a une valeur de code de couleur `#ff303030`de.
Pour plus d’informations sur l’héritage de style, consultez [styles et thèmes](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Sélecteur de couleurs

La capture d’écran suivante illustre le **Sélecteur de couleurs**:

[![Sélecteur de couleurs](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

Dans cet exemple, la couleur d' **arrière-plan** peut être remplacée par une valeur de différentes manières :

- En cliquant directement sur une couleur.
- Saisie des valeurs de teinte, de saturation et de luminosité.
- Saisie de valeurs RVB (rouge, vert, bleu) au format décimal.
- Définition de l’alpha (opacité) de la couleur sélectionnée.
- Saisie directe du code de couleur hexadécimal.

La couleur que vous choisissez dans le sélecteur de couleurs n’est *pas* limitée aux recommandations en matière de conception de matériau ou à l’ensemble des ressources de couleurs disponibles.

### <a name="resources"></a>Ressources

L’onglet **ressources** offre une liste des ressources de couleur déjà présentes dans le thème :

[![Situées](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

L’utilisation de l’onglet **ressources** limite vos choix à cette liste de couleurs. Gardez à l’esprit que si vous choisissez une ressource de couleur qui est déjà assignée à une autre partie du thème, deux éléments adjacents de l’interface utilisateur peuvent « s’exécuter ensemble » (car ils ont la même couleur) et deviennent difficiles à distinguer par l’utilisateur.

### <a name="material-palette"></a>Palette de matériaux

L’onglet **palette de matériaux** ouvre la **palette couleur de conception de matériau** décrite [précédemment](#material-design-color-palette). Le choix d’une valeur de couleur de cette palette limite votre choix de couleurs pour qu’elle soit cohérente avec les règles de conception de matériau.

[![Palette de matériaux](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>Création d’un nouveau thème

Dans l’exemple suivant, nous allons utiliser la palette de matériaux pour créer un nouveau thème personnalisé. Tout d’abord, nous allons modifier la couleur d' **arrière-plan** en *bleu 900*:

[![Remplacez l’arrière-plan par Blue 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

Lorsqu’une ressource de couleur est modifiée, un message s’affiche avec le message, *le thème actuel comporte des modifications non enregistrées*:

[![Avertissement sur les modifications non enregistrées](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

La modification de couleur dans le concepteur a été effectuée, mais cette modification n’a pas encore été enregistrée. À ce stade, vous pouvez effectuer l’une des opérations suivantes :

- Cliquez sur **Ignorer les modifications** pour abandonner les nouveaux choix de couleurs (ou choix) et rétablir l’état d’origine du thème.

- Appuyez sur  **&#8984; + S** pour enregistrer les modifications apportées à un nouveau thème appelé **personnalisé**.

## <a name="summary"></a>Récapitulatif

Cette rubrique décrit les fonctionnalités de conception de matériau disponibles dans Xamarin. Android Designer. Il a expliqué comment activer et configurer la grille de conception de matériau, comment utiliser la palette de couleurs de conception de matériau pour modifier les propriétés de couleur et comment utiliser le sélecteur d’échelle typographique pour configurer des propriétés de texte. Il montre également comment utiliser l’éditeur de thème pour créer des thèmes personnalisés conformes aux règles de conception de matériaux. Pour plus d’informations sur la prise en charge de Xamarin. Android pour la conception de matériaux, consultez [thème matériel](~/android/user-interface/material-theme.md).

-----

## <a name="related-links"></a>Liens associés

- [Thème matériau](~/android/user-interface/material-theme.md)
- [Présentation de la conception de matériau](https://material.io/design/introduction)
