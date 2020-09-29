---
title: Notions de base de Xamarin. Android Designer
description: Cette rubrique présente les fonctionnalités de Xamarin. Android Designer, explique comment lancer le concepteur, décrit les Aire de conception et explique comment utiliser le volet Propriétés pour modifier les propriétés d’un widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/05/2018
ms.openlocfilehash: e4d4ffe70db8f79ddf5e8f7a8ca1bda0fdc5aa11
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458518"
---
# <a name="xamarinandroid-designer-basics"></a>Notions de base de Xamarin. Android Designer

_Cette rubrique présente les fonctionnalités de Xamarin. Android Designer, explique comment lancer le concepteur, décrit les Aire de conception et explique comment utiliser le volet Propriétés pour modifier les propriétés d’un widget._

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>Lancement du concepteur

Le concepteur est lancé automatiquement quand une disposition est créée, ou il peut être lancé en double-cliquant sur un fichier de disposition existant. Par exemple, si vous double-cliquez sur **activity_main. AXML** dans le dossier **ressources > layout** , le concepteur est chargé comme indiqué dans cette capture d’écran :

[![Écran concepteur dans Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

De même, vous pouvez ajouter une nouvelle disposition en cliquant avec le bouton droit sur le dossier de **disposition** dans le **Explorateur de solutions** et en sélectionnant **Ajouter > nouvel élément... > disposition Android**:

[![Boîte de dialogue Ajouter un nouvel élément](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

Cela crée un fichier de disposition **. AXML** et le charge dans le concepteur.

> [!TIP]
> Les nouvelles versions de Visual Studio prennent en charge l’ouverture de fichiers .xml dans Android Designer.
>
> Les fichiers .axml et .xml sont pris en charge dans Android Designer.

## <a name="designer-features"></a>Fonctionnalités du concepteur

Le concepteur est composé de plusieurs sections qui prennent en charge ses diverses fonctionnalités, comme illustré dans la capture d’écran suivante :

[![Diagramme des volets du concepteur](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

Lorsque vous modifiez une disposition dans le concepteur, vous utilisez les fonctionnalités suivantes pour créer et mettre en forme votre conception :

- **Aire de conception** &ndash; Facilite la construction visuelle de l’interface utilisateur en vous donnant une représentation modifiable de la façon dont la disposition s’affichera sur l’appareil. Le **aire de conception** s’affiche dans le **volet de conception** (avec la **barre d’outils du concepteur** positionnée au-dessus).

- **Volet source** &ndash; Fournit une vue de la source XML sous-jacente qui correspond à la conception présentée sur la **aire de conception**.

- **Barre d’outils** &ndash; du concepteur Affiche la liste des sélecteurs : l' **appareil**, la **version**, le **thème**, la configuration de la disposition et les paramètres de barre d’action. La **barre d’outils du concepteur** comprend également des icônes pour lancer l’éditeur de thème et pour activer la grille de conception de matériau.

- **Boîte à outils** &ndash; Fournit la liste des widgets et des mises en page que vous pouvez glisser-déplacer sur le **aire de conception**.

- **Fenêtre Propriétés** &ndash; Répertorie les propriétés du widget sélectionné pour l’affichage et la modification.

- **Structure** &ndash; du document Affiche l’arborescence des widgets qui composent la disposition. Vous pouvez cliquer sur un élément dans l’arborescence pour qu’il soit sélectionné sur la **aire de conception**. En outre, le fait de cliquer sur un élément de l’arborescence charge les propriétés de l’élément dans la fenêtre **Propriétés** .

## <a name="design-surface"></a>Aire de conception

Le concepteur vous permet de glisser-déplacer des widgets de la boîte à outils vers le **aire de conception**. Lorsque vous interagissez avec des widgets dans le concepteur (soit en ajoutant de nouveaux widgets, soit en repositionnant des widgets existants), des lignes verticales et horizontales sont affichées pour marquer les points d’insertion disponibles. Dans l’exemple suivant, un nouveau `Button` widget est glissé vers le **aire de conception**:

[![Exemples de lignes d’insertion sur Aire de conception](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

En outre, les widgets peuvent être copiés : vous pouvez utiliser copier et coller pour copier un widget, ou vous pouvez glisser-déplacer un widget existant tout en appuyant sur la touche <kbd>CTRL</kbd> .

### <a name="designer-toolbar"></a>Barre d’outils du concepteur

La **barre d’outils du concepteur** (positionnée au-dessus du **aire de conception**) présente les sélecteurs de configuration et les menus outils :

[![Diagramme de la barre d’outils du concepteur](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

La **barre d’outils du concepteur** permet d’accéder aux fonctionnalités suivantes :

- **Autre sélecteur** &ndash; de disposition Vous permet de sélectionner différentes versions de la disposition.

- **Sélecteur** &ndash; d’appareils Définit un ensemble de qualificateurs (tels que la taille de l’écran, la résolution et la disponibilité du clavier) associés à un appareil particulier. Vous pouvez également ajouter et supprimer de nouveaux appareils.

- Sélecteur de version **Android** &ndash; Version d’Android ciblée par la disposition. Le concepteur affiche la disposition en fonction de la version d’Android sélectionnée.

- **Sélecteur** &ndash; de thème Sélectionne le thème de l’interface utilisateur pour la disposition.

- **Sélecteur** &ndash; de configuration Sélectionne la configuration de l’appareil, par exemple *portrait* ou *paysage*.

- Options du qualificateur de **ressource** &ndash; Ouvre une boîte de dialogue qui présente des menus déroulants permettant de sélectionner la *langue*, le *mode interface utilisateur*, le *mode nuit*et les options d' *écran rond* .

- **Paramètres** &ndash; de barre d’action Configure les paramètres de Barre d’action pour la disposition.

- **Éditeur** &ndash; de thème Ouvre l' *éditeur de thème*, qui vous permet de personnaliser les éléments du thème sélectionné.

- Grille de conception de **matériau** &ndash; Active ou désactive la grille de *conception de matériau*. L’élément de menu déroulant adjacent à la grille de création de matériau ouvre une boîte de dialogue qui vous permet de personnaliser la grille.

Chacune de ces fonctionnalités est expliquée plus en détail dans les rubriques suivantes :

- Les [options qualificateurs de ressources et visualisation](~/android/user-interface/android-designer/resource-qualifiers.md) fournissent des informations détaillées sur le **Sélecteur d’appareil**, le sélecteur de **version Android**, le **Sélecteur de thème**, le sélecteur de **configuration**, les options de **compétences de ressource**et les **paramètres de barre d’action**.

- D' [autres modes de disposition](~/android/user-interface/android-designer/alternative-layout-views.md) expliquent comment utiliser l' **autre sélecteur de disposition**.

- Les [fonctionnalités de conception de matériaux Xamarin. Android designer](~/android/user-interface/android-designer/material-design-features.md) fournissent une vue d’ensemble complète de l' **éditeur de thème** et de la grille de **conception de matériau**.

### <a name="context-menu-commands"></a>Commandes du menu contextuel

Un menu contextuel est disponible à la fois dans le **aire de conception** et dans la **structure du document**. Ce menu affiche les commandes disponibles pour le widget sélectionné et son conteneur, ce qui facilite l’exécution d’opérations sur les conteneurs (qui ne sont pas toujours faciles à sélectionner sur le **aire de conception**). Voici un exemple de menu contextuel :

[![Exemple de menu contextuel quand vous cliquez avec le bouton droit sur le Aire de conception](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

Dans cet exemple, cliquez avec le bouton droit sur un `TextView` menu contextuel qui propose plusieurs options :

- **Élément LinearLayout** &ndash; ouvre un sous-menu permettant de modifier le `LinearLayout` parent du `TextView` .

- **Supprimer**, **copier**et **couper** &ndash; les opérations qui s’appliquent au clic droit `TextView` .

### <a name="zoom-controls"></a>Contrôles de zoom

Le **aire de conception** prend en charge le zoom à l’aide de plusieurs contrôles, comme indiqué ci-dessous :

[![Diagramme des contrôles Aire de conception zoom](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

Ces contrôles permettent de voir plus facilement certaines zones de l’interface utilisateur dans le concepteur :

- **Mettre en surbrillance les conteneurs** &ndash; Met en surbrillance les conteneurs sur le   **aire de conception** afin qu’ils soient plus faciles à localiser lors du zoom avant et arrière.

- **Taille normale** &ndash; Génère le rendu de la disposition Pixel-pour-pixel afin que vous puissiez voir comment la disposition examine la résolution de l’appareil sélectionné.

- **Ajuster à la fenêtre** &ndash; Définit le niveau de zoom afin que la disposition entière soit visible sur la Aire de conception.

- **Zoom avant** &ndash; Effectue un zoom avant de chaque clic, en agrandissant la disposition.

- **Zoom arrière** &ndash; Effectue un zoom arrière de manière incrémentielle de chaque clic, ce qui rend la disposition plus petite sur le Aire de conception.

Notez que le paramètre de zoom choisi n’affecte pas l’interface utilisateur de l’application au moment de l’exécution.

## <a name="switching-between-design-and-source-panes"></a>Basculement entre les volets conception et source

Dans la bande centrale entre les volets **conception** et **source** , plusieurs boutons sont utilisés pour modifier l’affichage des volets **conception** et **source** :

[![Emplacements des boutons d’affichage du volet](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

Ces boutons effectuent les opérations suivantes :

- **Conception** &ndash; Ce bouton supérieur, **conception**, sélectionne le volet **conception** . Lorsque vous cliquez sur ce bouton, les volets **boîte à outils** et **Propriétés** sont activés et la **barre d’outils Éditeur de texte** n’est pas affichée. Quand l’utilisateur clique sur le bouton **réduire** (voir ci-dessous), le volet de **conception** est présenté seul sans le volet **source** .

- **Permuter les volets** &ndash; Ce bouton (qui ressemble à deux flèches opposées) échange les volets de **conception** et **source** afin que le volet **source** se trouve à gauche et que le volet de **conception** se trouve à droite. Le fait de cliquer à nouveau Replace ces volets dans leurs emplacements d’origine.

- **Source** &ndash; Ce bouton (qui ressemble à deux chevrons opposés) sélectionne le volet **source** . Lorsque vous cliquez sur ce bouton, les volets **boîte à outils** et **Propriétés** sont désactivés et la **barre d’outils Éditeur de texte** est visible en haut de Visual Studio. Quand l’utilisateur clique sur le bouton **réduire** (voir ci-dessous), le fait de cliquer sur le bouton **source** permet d’afficher le volet **source** à la place du volet de **conception** .

- **Fractionnement vertical** &ndash; Ce bouton (qui ressemble à une barre verticale) affiche les volets **conception** et **source** côte à côte. Il s’agit de la disposition par défaut.

- **Fractionnement horizontal** &ndash; Ce bouton (qui ressemble à une barre horizontale) affiche le volet de **conception** au-dessus du volet **source** . Vous pouvez cliquer sur les **volets d’échange** pour placer le volet **source** au-dessus du volet de **conception** .

- **Réduire le volet** &ndash; Ce bouton (qui ressemble à deux crochets pointant vers la droite) « réduit » l’affichage à deux volets de la **conception** et de la **source** dans une vue unique de l’un de ces volets.
    Ce bouton devient le bouton du **volet de développement** (qui ressemble à deux crochets pointant vers la gauche), sur lequel vous pouvez cliquer pour revenir au mode d’affichage à deux volets (**conception** et **source**).

Lorsque l’utilisateur clique sur le **volet réduire** , seul le volet de **conception** s’affiche. Toutefois, vous pouvez cliquer sur le bouton **source** pour afficher uniquement le volet **source** . Cliquez à nouveau sur le bouton **conception** pour revenir au volet de **conception** .

## <a name="source-pane"></a>Volet source

Le volet **source** affiche la source XML sous-jacente de la conception affichée sur la **aire de conception**. Étant donné que les deux vues sont disponibles en même temps, il est possible de créer une conception d’interface utilisateur en passant d’une représentation visuelle de la conception à la source XML sous-jacente pour la conception :

[![Exemple de source XML dans le volet source](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

Les modifications apportées à la source XML sont immédiatement rendues sur le **aire de conception**; les modifications apportées à la **aire de conception** provoquent la mise à jour de la source XML affichée dans le volet **source** en conséquence. Lorsque vous apportez des modifications au code XML dans le volet **source** , les fonctionnalités de saisie semi-automatique et IntelliSense sont disponibles pour accélérer le développement de l’interface utilisateur XML, comme expliqué ci-après.

Pour une plus grande facilité de navigation lors de l’utilisation de longs fichiers XML, le volet **source** prend en charge la barre de défilement de Visual Studio (comme indiqué sur la droite dans la capture d’écran précédente). Pour plus d’informations sur la barre de défilement, consultez [Comment suivre votre code en personnalisant la barre de défilement](/visualstudio/ide/how-to-track-your-code-by-customizing-the-scrollbar).

### <a name="autocompletion"></a>Autocomplétion

Lorsque vous commencez à taper le nom d’un attribut pour un widget, vous pouvez appuyer sur <kbd>Ctrl + Espace</kbd> pour afficher une liste des saisies semi-automatiques possibles. Par exemple, après avoir entré `android:lay` dans l’exemple suivant (suivi de l’entrée <kbd>Ctrl + Espace</kbd>), la liste suivante s’affiche :

[![Saisie semi-automatique de l’attribut Layout](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

Appuyez sur <kbd>entrée</kbd> pour accepter la première saisie semi-automatique, ou utilisez les touches de direction pour faire défiler le contenu jusqu’à la fin souhaitée, puis appuyez sur <kbd>entrée</kbd>. Vous pouvez également utiliser la souris pour faire défiler vers et cliquer sur l’achèvement souhaité.

### <a name="intellisense"></a>IntelliSense

Une fois que vous avez entré un nouvel attribut pour un widget et que vous commencez à lui assigner une valeur, IntelliSense s’affiche après la saisie d’un caractère de déclenchement et fournit une liste de valeurs valides à utiliser pour cet attribut. Par exemple, une fois que le premier guillemet double est entré pour `android:layout_width` dans l’exemple suivant, un sélecteur de saisie semi-automatique s’affiche pour fournir la liste des choix valides pour cette largeur :

[![Exemple IntelliSense pour la largeur de disposition](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

En bas de cette fenêtre contextuelle se trouvent deux boutons (comme indiqué en rouge dans la capture d’écran ci-dessus). Le fait de cliquer sur le bouton **ressources du projet** sur la gauche restreint la liste aux ressources qui font partie du projet d’application, tandis que le fait de cliquer sur le bouton ressources de l' **infrastructure** à droite restreint la liste pour afficher les ressources disponibles dans l’infrastructure.
Ces boutons sont activés ou désactivés : vous pouvez les cliquer de nouveau pour désactiver l’action de filtrage fournie par chacun.

## <a name="properties-pane"></a>Propriétés, volet

Le concepteur prend en charge la modification des propriétés des widgets via le volet **Propriétés** : 

![Capture d’écran de l’Fenêtre Propriétés](designer-basics-images/vs/08-property-pad.png)

Les propriétés figurant dans le volet **Propriétés** varient en fonction du widget sélectionné sur la **aire de conception**.

### <a name="default-values"></a>Valeurs par défaut

Les propriétés de la plupart des widgets sont vides dans la fenêtre **Propriétés** , car leurs valeurs héritent du thème Android sélectionné.
La fenêtre **Propriétés** affiche uniquement les valeurs qui sont définies explicitement pour le widget sélectionné ; elle n’affiche pas les valeurs héritées du thème.

### <a name="referencing-resources"></a>Référencement des ressources

Certaines propriétés peuvent faire référence à des ressources qui sont définies dans des fichiers autres que le fichier Layout **. AXML** . Les cas les plus courants de ce type sont les `string` `drawable` ressources et. Toutefois, les références peuvent également être utilisées pour d’autres ressources, telles que les `Boolean` valeurs et les dimensions. Quand une propriété prend en charge les références de ressources, une icône de navigation (un carré) s’affiche en regard de l’entrée de texte correspondant à la propriété. Ce bouton ouvre un sélecteur de ressource lorsque vous cliquez dessus.

Par exemple, la capture d’écran suivante montre les options disponibles lorsque vous cliquez sur le carré assombri à droite du champ de texte d’un `Text` widget dans la fenêtre **Propriétés** :

[![Exemple de liste d’options de texte](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

Quand l’utilisateur clique sur la **ressource...** , la boîte de dialogue **Sélectionner une ressource** s’affiche :

[![Capture d’écran des ressources d’exemple avec plusieurs ressources listées](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

Dans cette liste, vous pouvez sélectionner une ressource texte à utiliser pour ce widget au lieu de coder en dur le texte dans le volet **Propriétés** . L’exemple suivant illustre le sélecteur de ressources pour la `Src` propriété d’un `ImageView` :

[![Icône de liste de sélecteur de ressources pour une ressource ImageView](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

En cliquant sur le carré vide à droite de la propriété, vous `Src` Ouvrez la boîte de dialogue **Sélectionner une ressource** avec une liste de ressources allant des couleurs (comme indiqué ci-dessus) à drawables.

### <a name="boolean-property-references"></a>Références de propriété booléennes

Les propriétés *booléennes* sont normalement sélectionnées en tant que cases à cocher en regard d’une propriété dans la fenêtre Propriétés. Vous pouvez désigner une `true` `false` valeur ou en activant ou désactivant cette case à cocher, ou vous pouvez sélectionner une référence de propriété en cliquant sur le carré à droite de la propriété. Dans l’exemple suivant, le texte est changé en majuscules en cliquant sur la référence de propriété booléenne de **texte tout en majuscules** associée à l’option sélectionnée `TextView` :

![Exemple de définition de propriétés booléennes](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>Modification des propriétés Inline

Le Android Designer prend en charge la modification directe de certaines propriétés sur le **aire de conception** (vous n’avez donc pas besoin de rechercher ces propriétés dans la liste de propriétés). Les propriétés qui peuvent être modifiées directement incluent le texte, la marge et la taille.

### <a name="text"></a>Texte

Les propriétés de texte de certains widgets (tels que `Button` et `TextView` ) peuvent être modifiées directement sur le **aire de conception**. Le fait de double-cliquer sur un widget le met en mode édition, comme indiqué ci-dessous :

![Ressource texte pour la chaîne Hello](designer-basics-images/vs/12-text-resource.png "Ressource texte")

Vous pouvez entrer une nouvelle valeur de texte ou vous pouvez entrer une nouvelle chaîne de ressource. Dans l’exemple suivant, la `@string/hello` ressource est remplacée par le texte suivant `CLICK THIS BUTTON` :

![Maj + Entrée pour lier automatiquement le texte à une nouvelle ressource](designer-basics-images/vs/13-shift-enter-resource.png)

Cette modification est stockée dans la propriété du widget `text` ; elle ne modifie pas la valeur assignée à la `@string/hello` ressource.
Lorsque vous entrez une nouvelle chaîne de texte, vous pouvez appuyer sur <kbd>MAJ</kbd>  +
 <kbd>entrée</kbd> pour lier automatiquement le texte entré à une nouvelle ressource.

### <a name="margin"></a>Margin

Lorsque vous sélectionnez un widget, le concepteur affiche des poignées qui vous permettent de modifier la taille ou la marge du widget de manière interactive. Le fait de cliquer sur le widget pendant qu’il est sélectionné bascule entre le mode de modification de la marge et le mode d’édition de la taille.

Lorsque vous cliquez sur un widget pour la première fois, des poignées de marge sont affichées. Si vous déplacez la souris sur l’une des poignées, le concepteur affiche la propriété que le handle modifiera (comme indiqué ci-dessous pour la `layout_marginLeft` propriété) :

![Capture d’écran montrant des poignées de marge dans le concepteur](designer-basics-images/vs/15-margin-handles.png)

Si une marge a déjà été définie, des lignes en pointillés sont affichées, ce qui indique l’espace occupé par la marge :

![Exemple de lignes en pointillés marquant un espace autour d’un bouton](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>Taille

Comme mentionné précédemment, vous pouvez basculer en mode d’édition de taille en cliquant sur un widget alors qu’il est déjà sélectionné. Cliquez sur la poignée triangulaire pour définir la taille de la dimension indiquée sur `wrap_content` :

![Encapsuler le contenu et les poignées de redimensionnement](designer-basics-images/vs/17-wrap-content.png)

Le fait de cliquer sur le handle de **contenu de retour** à la ligne réduit le widget dans cette dimension afin qu’il ne soit pas plus grand que nécessaire pour encapsuler le contenu délimité. Dans cet exemple, le texte du bouton est réduit horizontalement, comme indiqué dans la capture d’écran suivante.

Lorsque la valeur size est définie sur **Wrap content**, le concepteur affiche une poignée triangulaire qui pointe dans la direction opposée pour remplacer la taille par `match_parent` :

![Trouver le handle parent](designer-basics-images/vs/18-match-parent.png)

Le fait de cliquer sur le handle de **correspondance parent** restaure la taille de cette dimension pour qu’elle soit identique au widget parent.

Vous pouvez également faire glisser la poignée de redimensionnement circulaire (comme indiqué dans les captures d’écran ci-dessus) pour redimensionner le widget à une `dp` valeur arbitraire. Lorsque vous procédez ainsi, le **contenu du wrapper et la** correspondance des handles **parents** sont présentés pour cette dimension :

![Poignées de redimensionnement circulaires](designer-basics-images/vs/19-resize-dp.png)

Tous les conteneurs n’autorisent pas la modification du `Size` d’un widget. Par exemple, remarquez que dans la capture d’écran ci-dessous avec l' `LinearLayout` option sélectionnée, les poignées de redimensionnement n’apparaissent pas :

![Aucune poignée de redimensionnement](designer-basics-images/vs/20-no-resize-handles.png)

## <a name="document-outline"></a>Structure du document

La **structure du document** affiche la hiérarchie des widgets de la disposition.
Dans l’exemple suivant, le `LinearLayout` widget conteneur est sélectionné :

![Exemple de structure du document](designer-basics-images/vs/21-document-outline.png)

Le contour du widget sélectionné (dans ce cas, a `LinearLayout` ) est également mis en surbrillance sur la **aire de conception**. Le widget sélectionné dans la structure du document reste synchronisé avec son homologue sur le **aire de conception**. Cela est utile pour sélectionner des groupes de vues, qui ne sont pas toujours faciles à sélectionner sur le **aire de conception**.

La **structure du document** prend en charge le copier-coller, ou vous pouvez utiliser le glisser-déplacer. Le glisser-déplacer est pris en charge à partir de la **structure du document** jusqu’au **aire de conception** , ainsi que du **aire de conception** à la **structure du document**. En outre, cliquez avec le bouton droit sur un élément dans la **structure du document** pour afficher le menu contextuel de cet élément (le même menu contextuel qui apparaît lorsque vous cliquez avec le bouton droit sur le même widget sur le **aire de conception**).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="launching-the-designer"></a>Lancement du concepteur

Le concepteur est lancé automatiquement quand une disposition est créée, ou il peut être lancé en double-cliquant sur un fichier. AXML existant. Par exemple, le fait de double-cliquer sur **main. AXML** dans le dossier **resources > layout** charge le concepteur comme indiqué ci-dessous :

[![Écran concepteur dans Visual Studio pour Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

De même, vous pouvez ajouter une nouvelle disposition en cliquant avec le bouton droit sur le dossier de **disposition** dans le **panneau solutions** et en sélectionnant **Ajouter > nouveau fichier > disposition de > Android**:

[![Boîte de dialogue Ajouter un nouveau fichier](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

Cela crée un nouveau fichier. AXML et le charge sur le Aire de conception.

> [!TIP]
> Les nouvelles versions de Visual Studio prennent en charge l’ouverture de fichiers .xml dans Android Designer.
>
> Les fichiers .axml et .xml sont pris en charge dans Android Designer.

## <a name="designer-features"></a>Fonctionnalités du concepteur

Le concepteur est composé de plusieurs sections qui prennent en charge ses diverses fonctionnalités, comme illustré dans la capture d’écran suivante :

[![Diagramme des volets du concepteur](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

Lorsque vous modifiez une disposition dans le concepteur, vous utilisez les fonctionnalités suivantes pour créer et mettre en forme votre conception :

- **Aire de conception** &ndash; Facilite la construction visuelle de l’interface utilisateur en vous donnant une représentation modifiable de la façon dont la disposition s’affichera sur l’appareil.

- **Barre d’outils** &ndash; Affiche la liste des sélecteurs : l' **appareil**, la **version**, le **thème**, la configuration de la disposition et les paramètres de barre d’action. La barre d’outils comprend également des icônes pour lancer l’éditeur de thème et pour activer la grille de conception de matériau.

- **Boîte à outils** &ndash; Fournit la liste des widgets et des mises en page que vous pouvez glisser-déplacer sur le Aire de conception.

- Panneau des **Propriétés** &ndash; Répertorie les propriétés du widget sélectionné pour l’affichage et la modification.

- **Structure** &ndash; du document Affiche l’arborescence des widgets qui composent la disposition. Vous pouvez cliquer sur un élément dans l’arborescence pour le faire sélectionner dans le concepteur. En outre, le fait de cliquer sur un élément de l’arborescence charge les propriétés de l’élément dans le panneau des propriétés.

## <a name="toolbar"></a>Barre d’outils

La barre d’outils (positionnée au-dessus du Aire de conception) présente les sélecteurs de configuration et les menus outils :

[![Diagramme de la barre d’outils du concepteur](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

La barre d’outils permet d’accéder aux fonctionnalités suivantes :

- **Autre sélecteur** &ndash; de disposition Vous permet de sélectionner différentes versions de la disposition.

- **Sélecteur** &ndash; d’appareils Définit un ensemble de qualificateurs associés à un appareil particulier, tels que la taille de l’écran, la résolution et la disponibilité du clavier. Vous pouvez également ajouter et supprimer de nouveaux appareils.

- Sélecteur de version **Android** &ndash; Version d’Android ciblée par la disposition. Le concepteur affiche la disposition en fonction de la version d’Android sélectionnée.

- **Sélecteur** &ndash; de thème Sélectionne le thème de l’interface utilisateur pour la disposition.

- **Sélecteur** &ndash; de configuration Sélectionne la configuration de l’appareil, par exemple *portrait* ou *paysage*.

- Options du qualificateur de **ressource** &ndash; Ouvre une boîte de dialogue qui présente des menus déroulants permettant de sélectionner la *langue*, le *mode interface utilisateur*, le *mode nuit*et les options d' *écran rond* .

- **Paramètres** &ndash; de barre d’action Configure les paramètres de Barre d’action pour la disposition.

- **Éditeur** &ndash; de thème Ouvre l' *éditeur de thème*, qui vous permet de personnaliser les éléments du thème sélectionné.

- Grille de conception de **matériau** &ndash; Active ou désactive la grille de *conception de matériau*. L’élément de menu déroulant adjacent à la grille de création de matériau ouvre une boîte de dialogue qui vous permet de personnaliser la grille.

Chacune de ces fonctionnalités est expliquée plus en détail dans les rubriques suivantes :

Les [options qualificateurs de ressources et visualisation](~/android/user-interface/android-designer/resource-qualifiers.md) fournissent des informations détaillées sur le **Sélecteur d’appareil**, le sélecteur de **version Android**, le **Sélecteur de thème**, le sélecteur de **configuration**, les options de **compétences de ressource**et les **paramètres de barre d’action**.

D' [autres modes de disposition](~/android/user-interface/android-designer/alternative-layout-views.md) expliquent comment utiliser l' **autre sélecteur de disposition**.

Les [fonctionnalités de conception de matériau](~/android/user-interface/android-designer/material-design-features.md) fournissent une vue d’ensemble complète de l’éditeur de **thème** et de la grille de **conception de matériau**.

## <a name="design-surface"></a>Aire de conception

Le concepteur vous permet de glisser-déplacer des widgets de la boîte à outils vers le Aire de conception. Lorsque vous interagissez avec des widgets dans le concepteur (soit en ajoutant de nouveaux widgets, soit en repositionnant des widgets existants), des lignes verticales et horizontales sont affichées pour marquer les points d’insertion disponibles. Dans l’exemple suivant, un nouveau `Button` widget est glissé vers le aire de conception :

[![Exemples de lignes d’insertion sur Aire de conception](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

En outre, les widgets peuvent être copiés : vous pouvez utiliser copier et coller pour copier un widget, ou vous pouvez glisser-déplacer un widget existant tout en appuyant sur la touche <kbd>CTRL</kbd> .

### <a name="context-menu-commands"></a>Commandes du menu contextuel

Un menu contextuel est disponible à la fois dans le Aire de conception et dans la structure du document. Ce menu affiche les commandes disponibles pour le widget sélectionné et son conteneur, ce qui facilite l’exécution d’opérations sur les conteneurs (qui ne sont pas toujours faciles à sélectionner sur le Aire de conception). Voici un exemple de menu contextuel :

[![Exemple de menu contextuel quand vous cliquez avec le bouton droit sur le Aire de conception](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

Dans cet exemple, cliquez avec le bouton droit sur un `Button` menu contextuel qui propose plusieurs options :

- **Élément LinearLayout** &ndash; ouvre un sous-menu permettant de modifier le `LinearLayout` parent du `Button` .

- Opérations de **couper**, **copier**et **supprimer** &ndash; qui s’appliquent au clic droit `Button` .

### <a name="zoom-controls"></a>Contrôles de zoom

Le Aire de conception prend en charge le zoom à l’aide de plusieurs contrôles, comme indiqué ci-dessous :

[![Diagramme des contrôles Aire de conception zoom](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

Ces contrôles permettent de voir plus facilement certaines zones de l’interface utilisateur dans le concepteur :

- **Mettre en surbrillance les conteneurs** &ndash; Met en surbrillance les conteneurs sur le Aire de conception afin qu’ils soient plus faciles à localiser lors du zoom avant et arrière.

- **Taille normale** &ndash; Génère le rendu de la disposition Pixel-pour-pixel afin que vous puissiez voir comment la disposition examine la résolution de l’appareil sélectionné.

- **Ajuster à la fenêtre** &ndash; Définit le niveau de zoom afin que la disposition entière soit visible sur la Aire de conception.

- **Zoom avant** &ndash; Effectue un zoom avant de chaque clic, en agrandissant la disposition.

- **Zoom arrière** &ndash; Effectue un zoom arrière de manière incrémentielle de chaque clic, ce qui rend la disposition plus petite sur le Aire de conception.

Notez que le paramètre de zoom choisi n’affecte pas l’interface utilisateur de l’application au moment de l’exécution.

## <a name="property-pad"></a>Panneau des propriétés

Le concepteur prend en charge la modification des propriétés des widgets via le panneau des **Propriétés**. Les propriétés figurant dans le panneau des propriétés changent en fonction du widget sélectionné dans l’aire du concepteur. Lorsque l' `Button` exemple précédent est sélectionné, les propriétés de ce `Button` widget sont affichées :

[![Capture d’écran du panneau des propriétés](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>Sections du panneau des propriétés

Le panneau des propriétés est divisé en plusieurs sections qui regroupent des propriétés similaires, &ndash; ce qui facilite la localisation des propriétés intéressantes :

- **Widget** &ndash; Propriétés principales du widget, telles que `id` ,,   `visibility` `text` , etc. Les propriétés pour gérer le contenu du widget sont généralement placées ici.

- **Style** &ndash; Propriétés qui modifient l’apparence visuelle du widget, telles que `font` , `text color` , `background` , etc.

- **Disposition** &ndash; Propriétés qui définissent l’emplacement et la taille du widget.

- **Défilement** &ndash; Propriétés de défilement.

- **Comportement** &ndash; Indicateurs qui définissent le comportement du widget.

### <a name="default-values"></a>Valeurs par défaut

Les propriétés de la plupart des widgets sont vides dans le panneau des **Propriétés** , car leurs valeurs héritent du thème Android sélectionné. Le panneau des **Propriétés** affiche uniquement les valeurs qui sont définies explicitement pour le widget sélectionné ; elle n’affiche pas les valeurs héritées du thème.

### <a name="referencing-resources"></a>Référencement des ressources

Certaines propriétés peuvent faire référence à des ressources qui sont définies dans des fichiers autres que le fichier Layout **. AXML** . Les cas les plus courants de ce type sont les `string` `drawable` ressources et. Toutefois, les références peuvent également être utilisées pour d’autres ressources, telles que les `Boolean` valeurs et les dimensions.
Quand une propriété prend en charge les références de ressources, une icône parcourir (points de suspension &hellip; ) s’affiche en regard de l’entrée de texte correspondant à la propriété.
Lorsque vous cliquez dessus, ce bouton ouvre un sélecteur de ressources.

Par exemple, la capture d’écran suivante montre les ressources disponibles lorsque vous cliquez sur les points de suspension à droite du champ de texte d’un `Button` widget dans le panneau des **Propriétés**:

[![Capture d’écran des exemples de ressources avec deux ressources listées](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

L’exemple suivant illustre le sélecteur de ressources pour la `Src` propriété d’un `ImageView` :

[![Icône de liste de sélecteur de ressources pour une ressource ImageView](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>Références de propriété booléennes

Les propriétés *booléennes* sont normalement affichées sous la forme d’une case à cocher dans le panneau des propriétés. Quand une `Boolean` propriété prend en charge les références de ressources, une petite case à cocher s’affiche en regard de la propriété. Une case à cocher activée signifie `true` et une zone vide signifie `false` . Vous pouvez également entrer directement une valeur telle que `true` ou `false` . Placez le curseur de la souris sur l’entrée pour faire apparaître une petite icône de champ de texte. Vous pouvez cliquer dessus si vous souhaitez entrer la valeur manuellement.

[![Exemple de définition de propriétés booléennes](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)

## <a name="grouped-properties"></a>Propriétés groupées

Certains widgets ont des propriétés à valeurs multiples regroupées (par `Padding` exemple, par exemple). Ces valeurs de propriété sont répertoriées dans le **tableau de propriétés** dans une ligne unique et extensible. Certaines de ces propriétés peuvent être modifiées directement dans la ligne groupée, par exemple la `Padding` propriété illustrée ci-dessous :

[![Exemples de paramètres pour la propriété Padding](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>Modification des propriétés Inline

Le Android Designer prend en charge la modification directe de certaines propriétés sur le Aire de conception (vous n’avez donc pas besoin de rechercher ces propriétés dans la liste de propriétés). Les propriétés qui peuvent être modifiées directement incluent le texte, la marge et la taille.

### <a name="text"></a>Texte

Les propriétés de texte de certains widgets (tels que `Button` et `TextView` ) peuvent être modifiées directement sur le aire de conception. Le fait de double-cliquer sur un widget le met en mode édition, comme indiqué ci-dessous :

[![Ressource texte pour la chaîne Hello](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

Vous pouvez entrer une nouvelle valeur de texte ou vous pouvez entrer une nouvelle chaîne de ressource. Dans l’exemple suivant, la `@string/hello` ressource est remplacée par le texte suivant `CLICK THIS BUTTON` :

[![Maj + Entrée pour lier automatiquement le texte à une nouvelle ressource](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

Cette modification est stockée dans la propriété du widget `text` ; elle ne modifie pas la valeur assignée à la `@string/hello` ressource.
Lorsque vous entrez une nouvelle chaîne de texte, vous pouvez appuyer sur <kbd>MAJ</kbd>  +
 <kbd>entrée</kbd> pour lier automatiquement le texte entré à une nouvelle ressource.

### <a name="margin"></a>Margin

Lorsque vous sélectionnez un widget, le concepteur affiche des poignées qui vous permettent de modifier la taille ou la marge du widget de manière interactive. Le fait de cliquer sur le widget pendant qu’il est sélectionné bascule entre le mode de modification de la marge et le mode d’édition de la taille.

Lorsque vous cliquez sur un widget pour la première fois, des poignées de marge sont affichées. Si vous déplacez la souris sur l’une des poignées, le concepteur affiche la propriété que le handle modifiera (comme indiqué ci-dessous pour la `layout_marginLeft` propriété) :

[![Capture d’écran montrant des poignées de marge dans le concepteur](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

Si une marge a déjà été définie, des lignes en pointillés sont affichées, ce qui indique l’espace occupé par la marge :

[![Exemple de lignes en pointillés marquant un espace autour d’un bouton](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>Taille

Comme mentionné précédemment, vous pouvez basculer en mode d’édition de taille en cliquant sur un widget alors qu’il est déjà sélectionné. Cliquez sur la poignée triangulaire pour définir la taille de la dimension indiquée sur `wrap_content` :

[![Encapsuler le contenu et les poignées de redimensionnement](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

Le fait de cliquer sur le descripteur de **contenu encapsulé** réduit le widget dans cette dimension afin que ne soit pas plus grand que nécessaire pour encapsuler le contenu délimité. Dans cet exemple, le texte du bouton est réduit horizontalement, comme indiqué dans la capture d’écran suivante.

Lorsque la valeur size est définie sur **Wrap content**, le concepteur affiche une poignée triangulaire qui pointe dans la direction opposée pour remplacer la taille par `match_parent` :

[![Trouver le handle parent](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

Le fait de cliquer sur le handle de **correspondance parent** restaure la taille de cette dimension pour qu’elle soit identique au widget parent.

Vous pouvez également faire glisser la poignée de redimensionnement circulaire (comme indiqué dans les captures d’écran ci-dessus) pour redimensionner le widget à une `dp` valeur arbitraire. Lorsque vous procédez ainsi, le **contenu du wrapper et la** correspondance des handles **parents** sont présentés pour cette dimension :

[![Poignées de redimensionnement circulaires](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

Tous les conteneurs n’autorisent pas la modification du `Size` d’un widget. Par exemple, remarquez que dans la capture d’écran ci-dessous avec l' `LinearLayout` option sélectionnée, les poignées de redimensionnement n’apparaissent pas :

[![Aucune poignée de redimensionnement](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>Structure du document

La **structure du document** affiche la hiérarchie des widgets de la disposition.
Dans l’exemple suivant, le `LinearLayout` widget conteneur est sélectionné :

[![Structure du document](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

Le contour du widget sélectionné (dans ce cas, a `LinearLayout` ) est également mis en surbrillance sur la aire de conception. Le widget sélectionné dans la structure du document reste synchronisé avec son homologue sur le Aire de conception. Cela est utile pour sélectionner des groupes de vues, qui ne sont pas toujours faciles à sélectionner sur le Aire de conception.

La structure du document prend en charge le copier-coller, ou vous pouvez utiliser le glisser-déplacer. Le glisser-déplacer est pris en charge à partir de la structure du document jusqu’au Aire de conception, ainsi que du Aire de conception à la structure du document. En outre, cliquez avec le bouton droit sur un élément dans la structure du document pour afficher le menu contextuel de cet élément (le même menu contextuel qui apparaît lorsque vous cliquez avec le bouton droit sur le même widget sur le Aire de conception).

-----