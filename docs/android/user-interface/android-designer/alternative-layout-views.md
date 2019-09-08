---
title: Autres vues de disposition
description: Cette rubrique explique comment une version peut être gérée à l’aide de qualificateurs de ressources. Par exemple, il peut y avoir une version d’une disposition qui est utilisée uniquement lorsque l’appareil est en mode paysage et une version de disposition qui est uniquement en mode portrait.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: c872baa99496352a1934d10356a1001b309aa63e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757401"
---
# <a name="alternative-layout-views"></a>Autres modes de disposition

_Cette rubrique explique comment vous pouvez mettre en forme les versions à l’aide de qualificateurs de ressources. Par exemple, la création d’une version d’une disposition qui est utilisée uniquement quand l’appareil est en mode paysage et une version de disposition qui est uniquement pour le mode portrait._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>Création d’autres dispositions

Lorsque vous cliquez sur l’icône de **mode disposition alternative** (à gauche de l' **appareil**), un volet de visualisation affiche la liste des autres dispositions disponibles dans votre projet. S’il n’existe aucune autre disposition, la vue **par défaut** est présentée : 

[![Volet d’affichage de la disposition secondaire](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "Volet d’affichage de la disposition secondaire")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

Lorsque vous cliquez sur le signe plus vert en regard de **nouvelle version**, la boîte de dialogue **créer une variation de disposition** s’ouvre et vous permet de sélectionner les qualificateurs de ressources pour cette variation de disposition : 

[![Créer une variation de disposition](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "Créer une variation de disposition")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

Dans l’exemple suivant, le qualificateur de ressource pour **l’orientation** de l’écran est défini sur **paysage**et la taille de l' **écran** devient **grande**. Cela crée une nouvelle version de disposition nommée **large-Land**:

[![Variation à grande échelle](alternative-layout-views-images/vs/03-large-land-sml.png "Variation à grande échelle")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

Notez que le volet de visualisation sur la gauche affiche les effets des sélections de qualificateurs de ressources. Cliquez sur **Ajouter** pour créer une disposition alternative et basculer le concepteur vers cette disposition. Le volet de visualisation de l' **autre mode page** indique la disposition qui est chargée dans le concepteur via un petit pointeur vers la droite, comme indiqué dans la capture d’écran suivante : 

[![Indicateur de disposition chargé](alternative-layout-views-images/vs/04-new-layout-sml.png "Indicateur de disposition chargé")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Modification de dispositions alternatives

Quand vous créez d’autres dispositions, il est souvent préférable d’effectuer une seule modification qui s’applique à toutes les versions dupliquées d’une disposition. Par exemple, vous souhaiterez peut-être modifier le texte du bouton en jaune dans toutes les dispositions. Si vous avez un grand nombre de mises en page et que vous devez propager une seule modification à toutes les versions, la maintenance peut rapidement devenir fastidieuse et sujette aux erreurs.

Pour simplifier la maintenance de plusieurs versions de disposition, le concepteur fournit un mode d' **édition multiple** qui propage vos modifications sur une ou plusieurs dispositions. Lorsque plusieurs dispositions sont présentes, l’icône de **multi-édition** s’affiche : 

[![Icône à plusieurs modifications](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "Icône à plusieurs modifications")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

Lorsque vous cliquez sur l’icône de **multi-édition** , des lignes s’affichent pour indiquer que les dispositions sont liées (comme indiqué ci-dessous); autrement dit, lorsque vous apportez une modification à une disposition, cette modification est propagée à toutes les dispositions liées. Vous pouvez dissocier toutes les dispositions en cliquant sur l’icône cerclée indiquée dans la capture d’écran suivante : 

[![Dissocier toutes les dispositions](alternative-layout-views-images/vs/06-multi-linked-sml.png "Dissocier toutes les dispositions")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

Si vous avez plus de deux dispositions, vous pouvez basculer de manière sélective le bouton modifier à gauche de chaque aperçu de la disposition pour déterminer les dispositions qui sont liées entre elles. Par exemple, si vous souhaitez effectuer une seule modification qui se propage sur la première et la dernière des trois dispositions, vous devez d’abord dissocier la disposition centrale comme indiqué ici : 

[![Dissocier la disposition centrale](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "Dissocier la disposition centrale")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

Dans cet exemple, une modification apportée à la disposition **par défaut** ou **longue** est propagée à l’autre disposition, mais pas à la **grande** disposition.

### <a name="multi-edit-example"></a>Exemple de modification multiple 

En général, lorsque vous apportez une modification à une disposition, cette même modification est propagée à toutes les autres dispositions liées. Par exemple, l’ajout d' `TextView` un nouveau widget à la disposition **par défaut** et la modification `Portrait` de sa chaîne de texte en entraînent la modification de toutes les dispositions liées. Voici comment il apparaît dans la disposition **par défaut** : 

[![Ajouter TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "Ajouter TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)

Le `TextView` est également ajouté à la vue de mise en page **grand sol** , car il est lié à la disposition **par défaut** : 

[![TextView paysage](alternative-layout-views-images/vs/09-landscape-textview-sml.png "TextView paysage")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)

Mais que se passe-t-il si vous souhaitez apporter une modification locale à une seule disposition (autrement dit, vous ne souhaitez pas que la modification soit propagée aux autres dispositions) ? Pour ce faire, vous devez dissocier la disposition que vous souhaitez modifier avant de la modifier, comme expliqué ci-dessous. 

### <a name="making-local-changes"></a>Apporter des modifications locales 

Supposons que vous souhaitiez que les deux dispositions `TextView`aient été ajoutées, mais que vous souhaitiez également modifier la chaîne de texte dans la `Landscape` `Portrait` **grande** disposition à la place de. Si cette modification est apportée à **un grand terrain** alors que les deux dispositions sont liées, la modification est répercutée sur la disposition **par défaut** . Par conséquent, nous devons d’abord dissocier les deux dispositions avant d’apporter la modification. Lorsque nous modifions le texte du **grand terrains** en `Landscape`, le concepteur marque cette modification à l’aide d’un cadre rouge pour indiquer que la modification est locale à la mise en page à **grande échelle** et n’est *pas* propagée à la disposition **par défaut** : 

[![Modification locale](alternative-layout-views-images/vs/10-local-change-sml.png "Modification locale")](alternative-layout-views-images/vs/10-local-change.png#lightbox)

Lorsque vous cliquez sur la disposition **par défaut** pour l’afficher `TextView` , la chaîne de texte est `Portrait`toujours définie sur. 

## <a name="handling-conflicts"></a>Gestion des conflits 

Si vous décidez de modifier la couleur du texte de la disposition **par défaut** en vert, une icône d’avertissement s’affiche sur la disposition liée. Cliquer sur cette disposition ouvre la disposition pour révéler le conflit. Le widget qui a provoqué le conflit est mis en surbrillance avec un cadre rouge et le message suivant s’affiche : *Les modifications récentes ont provoqué des conflits dans cette disposition alternative*. 

[![Modification en conflit](alternative-layout-views-images/vs/11-conflicting-change-sml.png "Modification en conflit")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)

Une *zone conflictuelle* s’affiche à droite du widget pour expliquer le conflit : 

[![Avertissement de conflit](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

La zone conflit affiche la liste des propriétés qui ont changé et répertorie leurs valeurs. Si vous cliquez sur **ignorer le conflit** , le changement de propriété ne s’applique qu’à ce widget. Cliquez sur **appliquer pour appliquer** la modification de la propriété à ce widget et au widget équivalent dans la disposition **par défaut** liée. Si toutes les modifications de propriété sont appliquées, le conflit est automatiquement supprimé. 

### <a name="view-group-conflicts"></a>Afficher les conflits de groupes 

Les modifications de propriété ne sont pas la seule source de conflits. Des conflits peuvent être détectés lors de l’insertion ou de la suppression de widgets. Par exemple, lorsque la mise en page à **grande échelle** est dissociée de la disposition **par défaut** `TextView` , et que, dans la **grande** disposition, vous faites glisser-déplacer `Button`au-dessus du, le concepteur marque le widget déplacé pour indiquer le conflits

[![Afficher le conflit de groupes](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Afficher le conflit de groupes")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)

Toutefois, il n’existe aucun marqueur `Button`sur le. Bien que la position de `Button` ait changé, le `Button` n’affiche aucune modification appliquée spécifique à la configuration de **grande superficie** . 

Si un `CheckBox` est ajouté à la disposition **par défaut** , un autre conflit est généré et une icône d’avertissement s’affiche au-dessus de la disposition de **grande superficie** : 

[![Conflit de cases à cocher](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "Conflit de cases à cocher")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)

Le fait de cliquer sur la **grande** disposition révèle le conflit. Le message suivant s'affiche : *Les modifications récentes ont provoqué des conflits dans cette disposition alternative*: 

[![Conflit de disposition Alt](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Conflit de disposition Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

En outre, la zone conflit affiche le message suivant :

[![Message de conflit](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

L’ajout de `LinearLayout` provoqueunconflit,carladispositiondegrandesuperficieachangé`CheckBox` dans le qui le contient. Toutefois, dans ce cas, la zone conflit affiche le widget qui vient d’être inséré dans la disposition **par défaut** (le `CheckBox`).

Si vous cliquez sur **ignorer le conflit**, le concepteur résout le conflit, ce qui permet de faire glisser et de déplacer le widget affiché dans la zone conflit dans la disposition où le widget est manquant (dans ce cas, la disposition de **grande superficie** ) : 

[![Conflit de groupes résolus](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "Conflit de groupes résolus")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

Comme indiqué dans l' `Button`exemple précédent avec, le `CheckBox` n’a pas de marqueur de modification rouge, car `LinearLayout` seuls les ont des modifications qui ont été appliquées dans la disposition de **grande superficie** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>Création d’autres dispositions

Lorsque vous cliquez sur l’icône de **mode disposition alternative** (à gauche de l' **appareil**), un volet de visualisation affiche la liste des autres dispositions disponibles dans votre projet. S’il n’existe aucune autre disposition, la vue **par défaut** est présentée : 

[![Volet d’affichage de la disposition secondaire](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

Lorsque vous cliquez sur le signe plus vert en regard de **nouvelle version**, la boîte de dialogue **créer une variation de disposition** s’ouvre et vous permet de sélectionner les qualificateurs de ressources pour cette variation de disposition : 

[![Créer une variation de disposition](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

Dans l’exemple suivant, le qualificateur de ressource pour **l’orientation** de l’écran est défini sur **paysage**et la taille de l' **écran** devient **grande**. Cela crée une nouvelle version de disposition nommée **large-Land**:

[![Variation à grande échelle](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

Notez que le volet de visualisation sur la gauche affiche les effets des sélections de qualificateurs de ressources. Cliquez sur **Ajouter** pour créer une disposition alternative et basculer le concepteur vers cette disposition. Le volet de visualisation de l' **autre mode page** indique la disposition qui est chargée dans le concepteur via un petit pointeur vers la droite, comme indiqué dans la capture d’écran suivante : 

[![Indicateur de disposition chargé](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Modification de dispositions alternatives

Quand vous créez d’autres dispositions, il est souvent préférable d’effectuer une seule modification qui s’applique à toutes les versions dupliquées d’une disposition. Par exemple, vous souhaiterez peut-être modifier le texte du bouton en jaune dans toutes les dispositions. Si vous avez un grand nombre de mises en page et que vous devez propager une seule modification à toutes les versions, la maintenance peut rapidement devenir fastidieuse et sujette aux erreurs.

Pour simplifier la maintenance de plusieurs versions de disposition, le concepteur fournit un mode d' **édition multiple** qui propage vos modifications sur une ou plusieurs dispositions. Lorsque plusieurs dispositions sont présentes, l’icône de **multi-édition** s’affiche : 

[![Icône à plusieurs modifications](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

Lorsque vous cliquez sur l’icône de **multi-édition** , des lignes s’affichent pour indiquer que les dispositions sont liées (comme indiqué ci-dessous); autrement dit, lorsque vous apportez une modification à une disposition, cette modification est propagée à toutes les dispositions liées. Vous pouvez dissocier toutes les dispositions en cliquant sur l’icône cerclée indiquée dans la capture d’écran suivante : 

[![Dissocier toutes les dispositions](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

Si vous avez plus de deux dispositions, vous pouvez basculer de manière sélective le bouton modifier à gauche de chaque aperçu de la disposition pour déterminer les dispositions qui sont liées entre elles. Par exemple, si vous souhaitez effectuer une seule modification qui se propage sur la première et la dernière des trois dispositions, vous devez d’abord dissocier la disposition centrale comme indiqué ici : 

[![Dissocier la disposition centrale](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

Dans cet exemple, une modification apportée à la disposition **par défaut** ou **longue** est propagée à d’autres dispositions, mais pas à la **grande** disposition. 

### <a name="multi-edit-example"></a>Exemple de modification multiple 

En général, lorsque vous apportez une modification à une disposition, cette même modification est propagée à toutes les autres dispositions liées. Par exemple, l’ajout d' `TextView` un nouveau widget à la disposition **par défaut** et la modification `Portrait` de sa chaîne de texte en entraînent la modification de toutes les dispositions liées. Voici comment il apparaît dans la disposition **par défaut** : 

[![Ajouter TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

Le `TextView` est également ajouté à la vue de mise en page **grand sol** , car il est lié à la disposition **par défaut** : 

[![TextView paysage](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)

Mais que se passe-t-il si vous souhaitez apporter une modification locale à une seule disposition (autrement dit, vous ne souhaitez pas que la modification soit propagée aux autres dispositions) ? Pour ce faire, vous devez dissocier la disposition que vous souhaitez modifier avant de la modifier, comme expliqué ci-dessous. 

### <a name="making-local-changes"></a>Apporter des modifications locales 

Supposons que vous souhaitiez que les deux dispositions `TextView`aient été ajoutées, mais que vous souhaitiez également modifier la chaîne de texte dans la `Landscape` `Portrait` **grande** disposition à la place de. Si cette modification est apportée à **un grand terrain** alors que les deux dispositions sont liées, la modification est répercutée sur la disposition **par défaut** . Par conséquent, nous devons d’abord dissocier les deux dispositions avant d’apporter la modification. Lorsque nous modifions le texte du **grand terrains** en `Landscape`, le concepteur marque cette modification à l’aide d’un cadre rouge pour indiquer que la modification est locale à la mise en page à **grande échelle** et n’est *pas* propagée à la disposition **par défaut** : 

[![Modification locale](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

Lorsque vous cliquez sur la disposition **par défaut** pour l’afficher `TextView` , la chaîne de texte est `Portrait`toujours définie sur. 

## <a name="handling-conflicts"></a>Gestion des conflits 

Si vous décidez de modifier la couleur du texte de la disposition **par défaut** en vert, une icône d’avertissement s’affiche sur la disposition liée. Cliquer sur cette disposition ouvre la disposition pour révéler le conflit. Le widget qui a provoqué le conflit est mis en surbrillance avec un cadre rouge et le message suivant s’affiche : *Les modifications récentes ont provoqué des conflits dans cette disposition alternative*. 

[![Modification en conflit](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

Une *zone conflictuelle* s’affiche à droite du widget pour expliquer le conflit : 

[![Avertissement de conflit](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

La zone conflit affiche la liste des propriétés qui ont changé et répertorie leurs valeurs. Si vous cliquez sur **ignorer le conflit** , le changement de propriété ne s’applique qu’à ce widget. Cliquez sur **appliquer pour appliquer** la modification de la propriété à ce widget et au widget équivalent dans la disposition **par défaut** liée. Si toutes les modifications de propriété sont appliquées, le conflit est automatiquement supprimé. 

### <a name="view-group-conflicts"></a>Afficher les conflits de groupes 

Les modifications de propriété ne sont pas la seule source de conflits. Des conflits peuvent être détectés lors de l’insertion ou de la suppression de widgets. Par exemple, lorsque la mise en page à **grande échelle** est dissociée de la disposition **par défaut** `TextView` , et que, dans la **grande** disposition, vous faites glisser-déplacer `Button`au-dessus du, le concepteur marque le widget déplacé pour indiquer le conflits

[![Afficher le conflit de groupes](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)

Toutefois, il n’existe aucun marqueur `Button`sur le. Bien que la position de `Button` ait changé, le `Button` n’affiche aucune modification appliquée spécifique à la configuration de **grande superficie** . 

Si un `CheckBox` est ajouté à la disposition **par défaut** , un autre conflit est généré et une icône d’avertissement s’affiche au-dessus de la disposition de **grande superficie** : 

[![Conflit de cases à cocher](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)

Le fait de cliquer sur la **grande** disposition révèle le conflit. Le message suivant s'affiche : *Les modifications récentes ont provoqué des conflits dans cette disposition alternative*. 

[![Conflit de disposition Alt](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)

En outre, la zone conflit affiche le message suivant :

[![Message de conflit](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

L’ajout de `LinearLayout` provoqueunconflit,carladispositiondegrandesuperficieachangé`CheckBox` dans le qui le contient. Toutefois, dans ce cas, la zone conflit affiche le widget qui vient d’être inséré dans la disposition **par défaut** (le `CheckBox`).

Si vous cliquez sur **ignorer le conflit**, le concepteur résout le conflit, ce qui permet de faire glisser et de déplacer le widget affiché dans la zone conflit dans la disposition où le widget est manquant (dans ce cas, la disposition de **grande superficie** ) : 

[![Conflit de groupes résolus](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)

Comme indiqué dans l' `Button`exemple précédent avec, le `CheckBox` n’a pas de marqueur de modification rouge, car `LinearLayout` seuls les ont des modifications qui ont été appliquées dans la disposition de **grande superficie** .

-----

### <a name="conflict-persistence"></a>Persistance des conflits

Les conflits sont conservés dans le fichier de disposition en tant que commentaires XML, comme illustré ici :

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Par conséquent, lorsqu’un projet est fermé et rouvert, tous les conflits sont toujours présents &ndash; , même ceux qui ont été ignorés.
