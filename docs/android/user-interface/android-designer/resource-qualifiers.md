---
title: Qualificateurs de ressources et options de visualisation
description: Cette rubrique explique comment définir des ressources qui seront utilisées uniquement quand certaines valeurs de qualificateur sont mises en correspondance. Une ressource de type chaîne qualifiée du langage est un exemple simple. Une ressource de type chaîne peut être définie comme valeur par défaut, avec d’autres ressources de remplacement définies pour être utilisées pour d’autres langues. Tous les types de ressources peuvent être qualifiés, y compris la disposition proprement dite.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 6ae10e73c3f41226560beb8cf8c50608fc6adf3f
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70762623"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificateurs de ressources et options de visualisation

_Cette rubrique explique comment définir des ressources qui seront utilisées uniquement quand certaines valeurs de qualificateur sont mises en correspondance. Une ressource de type chaîne qualifiée du langage est un exemple simple. Une ressource de type chaîne peut être définie comme valeur par défaut, avec d’autres ressources de remplacement définies pour être utilisées pour d’autres langues. Tous les types de ressources peuvent être qualifiés, y compris la disposition proprement dite._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>Options du qualificateur de ressource

Vous pouvez accéder aux **Options du qualificateur de ressource** en cliquant sur l’icône représentant des points de suspension à droite du bouton en mode **paysage** :

[Options du qualificateur ![Resource](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

Cette boîte de dialogue présente des menus déroulants pour les qualificateurs de ressources suivants :

- **Langue** &ndash; affiche les ressources linguistiques disponibles et offre la possibilité d’ajouter de nouvelles ressources de langue/région.

- Le **mode iu** &ndash; répertorie les modes d’affichage (par exemple, **station d’accueil** et station d’accueil de **Bureau**), ainsi que les directions de disposition.

Chacun de ces menus déroulants ouvre de nouvelles boîtes de dialogue où vous pouvez sélectionner et configurer des qualificateurs de ressources (comme expliqué ci-dessous).

### <a name="language"></a>Langue

Le menu déroulant **langue** répertorie uniquement les langues dont les ressources sont définies (ou **toutes les langues**, qui est la valeur par défaut). Toutefois, il existe également une option **Ajouter une langue/région..** . qui vous permet d’ajouter une nouvelle langue à la liste :

[langue/région de ![Add](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

Lorsque vous cliquez sur **Ajouter une langue/région...** , la boîte de dialogue **Sélectionner une langue** s’ouvre et affiche les listes déroulantes des langues et régions disponibles :

![Liste des langues](resource-qualifiers-images/vs/10-languages.png "Liste des langues")

Dans cet exemple, nous avons choisi **fr (français)** comme langue et **être** (Belgique) pour le dialecte régional du français. Notez que le champ **région** est facultatif, car de nombreuses langues peuvent être spécifiées sans tenir compte des régions spécifiques. Lorsque le menu déroulant **langue** s’ouvre à nouveau, il affiche la ressource langue/région nouvellement ajoutée :

![Langue et région choisies](resource-qualifiers-images/vs/11-language-region-added.png "Langue et région choisies")

Notez que si vous ajoutez une nouvelle langue, mais que vous ne créez pas de ressources pour celle-ci, la langue ajoutée ne s’affichera plus la prochaine fois que vous ouvrirez le projet.

### <a name="ui-mode"></a>Mode interface utilisateur

Lorsque vous cliquez sur le menu déroulant mode de l' **interface utilisateur** , une liste de modes s’affiche, par exemple **normal**, **station d’accueil automobile**, **station d’accueil de bureau**, **télévision**, **appareil**et **Espion**:

[menu mode ![UI](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Sous cette liste se trouvent les modes nuit **non nocturne** et **nuit**, suivis des directions de disposition de **gauche à droite** et de **droite à gauche** (pour plus d’informations sur les options de **gauche à droite** et de droite **à gauche** , consultez [ LayoutDirection](xref:Android.Util.LayoutDirection)).
Les derniers éléments de la boîte de dialogue **Options du qualificateur de ressource** sont les **écrans ronds** (pour une utilisation avec Android usure) ou **pas les écrans arrondis**.
Pour plus d’informations sur les écrans ronds et non arrondis, consultez [dispositions](https://developer.android.com/training/wearables/ui/layouts.html).
Pour plus d’informations sur les modes d’interface utilisateur Android, consultez [UiModeManager](xref:Android.App.UiModeManager).

## <a name="action-bar-settings"></a>Paramètres de Barre d’action

L’icône Paramètres de la **barre d’action** est disponible à gauche de l’icône pinceau (éditeur de thème) :

![Paramètres de Barre d’action](resource-qualifiers-images/vs/14-action-bar.png "Paramètres de Barre d’action")

Cette icône ouvre un menu segue de dialogue qui permet de sélectionner l’un des trois modes d’Barre d’action :

- @No__t_1 **standard** est constitué soit d’un logo, soit d’une icône et d’un texte de titre avec un sous-titre facultatif.

- **Liste** &ndash; mode de navigation de la liste. Au lieu d’un texte de titre statique, ce mode présente un menu de liste pour la navigation au sein de l’activité (autrement dit, il peut être présenté à l’utilisateur sous la forme d’une liste déroulante).

- **Onglets** &ndash; mode de navigation par onglets. Au lieu d’un texte de titre statique, ce mode présente une série d’onglets pour la navigation au sein de l’activité.

## <a name="themes"></a>Thèmes

Le menu déroulant **thème** affiche tous les thèmes définis dans le projet. Le fait de sélectionner **plus de thèmes** ouvre une boîte de dialogue contenant la liste de tous les thèmes disponibles à partir de la Android SDK installée, comme indiqué ci-dessous :

[![Liste des thèmes supplémentaires](resource-qualifiers-images/vs/15-theme-menu-sml.png "Liste des thèmes supplémentaires")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

Quand un thème est sélectionné, le Aire de conception est mis à jour pour afficher l’effet du nouveau thème. Notez que cette modification est rendue permanente uniquement si l’utilisateur clique sur le bouton **OK** dans la boîte de dialogue **thème** . Une fois qu’un thème a été sélectionné, il est inclus dans le menu déroulant **thème** , comme indiqué ci-dessous :

![Le thème clair est désormais disponible](resource-qualifiers-images/vs/16-light-theme.png "Le thème clair est désormais disponible")

## <a name="android-version"></a>Version d’Android

Le sélecteur de **version** Android définit la version d’Android qui est utilisée pour afficher la disposition dans le concepteur. Le sélecteur affiche toutes les versions qui sont compatibles avec la version cible du .NET Framework du projet :

![Liste des versions d’Android](resource-qualifiers-images/vs/17-android-version.png "Liste des versions d’Android")

La version cible du .NET Framework peut être définie dans les paramètres du projet sous **propriétés > Application > compiler à l’aide de la version Android**. Pour plus d’informations sur la version cible de .NET Framework, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

L’ensemble des widgets disponibles dans la boîte à outils est déterminé par la version du Framework cible du projet. Cela est également vrai pour les propriétés disponibles dans la **fenêtre Propriétés**. La liste des widgets disponibles n’est *pas* déterminée par la valeur sélectionnée dans le sélecteur de **version** de la barre d’outils. Par exemple, si vous définissez la version cible du projet sur Android 4,4, vous pouvez toujours sélectionner Android 6,0 dans le sélecteur de version de la barre d’outils pour voir à quoi ressemble le projet dans Android 6,0, mais vous ne pouvez pas ajouter des widgets spécifiques à Android 6,0 &ndash; vous avez ne sont pas toujours limités aux widgets disponibles dans Android 4,4.

Pour plus d’informations sur les types de ressources, consultez [ressources Android](~/android/app-fundamentals/resources-in-android/index.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>Options du qualificateur de ressource

Vous pouvez accéder aux **Options du qualificateur de ressource** en cliquant sur l’icône représentant des points de suspension à droite du bouton en mode **paysage** :

[options du qualificateur ![Resource](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

Cette boîte de dialogue présente des menus déroulants pour les qualificateurs de ressources suivants :

- **Langue** &ndash; affiche les ressources linguistiques disponibles et offre la possibilité d’ajouter de nouvelles ressources de langue/région.

- Le **mode iu** &ndash; répertorie les modes d’affichage (par exemple, **station d’accueil** et station d’accueil de **Bureau**), ainsi que les directions de disposition.

Chacun de ces menus déroulants ouvre de nouvelles boîtes de dialogue où vous pouvez sélectionner et configurer des qualificateurs de ressources (comme expliqué ci-dessous).

### <a name="language"></a>Langue

Le menu déroulant **langue** répertorie uniquement les langues dont les ressources sont définies (ou **toutes les langues**, qui est la valeur par défaut). Toutefois, il existe également une option **Ajouter une langue/région..** . qui vous permet d’ajouter une nouvelle langue à la liste :

[langue/région de ![Add](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

Lorsque vous cliquez sur **Ajouter une langue/région...** , la boîte de dialogue **Sélectionner une langue** s’ouvre et affiche les listes déroulantes des langues et régions disponibles :

[![List de langues](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

Dans cet exemple, nous avons choisi **fr (français)** comme langue et **être** (Belgique) pour le dialecte régional du français. Notez que le champ **région** est facultatif, car de nombreuses langues peuvent être spécifiées sans tenir compte des régions spécifiques. Lorsque le menu déroulant **langue** s’ouvre à nouveau, il affiche la ressource langue/région nouvellement ajoutée :

[![Language et région choisies](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

Notez que si vous ajoutez une nouvelle langue, mais que vous ne créez pas de ressources pour celle-ci, la langue ajoutée ne s’affichera plus la prochaine fois que vous ouvrirez le projet.

### <a name="ui-mode"></a>Mode interface utilisateur

Lorsque vous cliquez sur le menu déroulant mode de l' **interface utilisateur** , une liste de modes s’affiche, par exemple **normal**, **station d’accueil automobile**, **station d’accueil de bureau**, **télévision**, **appareil**et **Espion**:

[menu mode ![UI](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

Sous cette liste se trouvent les modes nuit **non nocturne** et **nuit**, suivis des directions de disposition de **gauche à droite** et **de droite à gauche**. La dernière paire d’options vous permet de sélectionner des **écrans ronds** ou **rectangulaires** (utiles pour les appareils Android d’usure).

Pour plus d’informations sur les modes d’interface utilisateur Android, consultez [UiModeManager](xref:Android.App.UiModeManager).
Pour plus d’informations sur les options de **gauche à droite** et **de droite à gauche** , consultez [LayoutDirection](xref:Android.Util.LayoutDirection).

## <a name="action-bar-settings"></a>Paramètres de Barre d’action

L’icône Paramètres de la **barre d’action** est disponible à gauche de l’icône pinceau (éditeur de thème) :

[paramètres de la barre de ![Action](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

Cette icône ouvre un menu segue de dialogue qui permet de sélectionner l’un des trois modes d’Barre d’action :

- Le &ndash; **standard** est constitué d’un logo ou d’une icône et d’un texte de titre avec un sous-titre facultatif.

- **Liste** &ndash; mode de navigation de la liste. Au lieu d’un texte de titre statique, ce mode présente un menu de liste pour la navigation au sein de l’activité (autrement dit, il peut être présenté à l’utilisateur sous la forme d’une liste déroulante).

- **Onglets** &ndash; mode de navigation par onglets. Au lieu d’un texte de titre statique, ce mode présente une série d’onglets pour la navigation au sein de l’activité.

## <a name="themes"></a>Thèmes

Le menu déroulant **thème** affiche tous les thèmes définis dans le projet. Le fait de sélectionner **plus de thèmes** ouvre une boîte de dialogue contenant la liste de tous les thèmes disponibles à partir de la Android SDK installée, comme indiqué ci-dessous :

[Liste des thèmes ![More](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

Quand un thème est sélectionné, le Aire de conception est mis à jour pour afficher l’effet du nouveau thème. Notez que cette modification est rendue permanente uniquement si l’utilisateur clique sur le bouton **OK** dans la boîte de dialogue **thème** . Une fois qu’un thème a été sélectionné, il est inclus dans le menu déroulant **thème** , comme indiqué ci-dessous :

[![Light thème est désormais disponible](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Version d’Android

Le sélecteur de **version** Android définit la version d’Android qui est utilisée pour afficher la disposition dans le concepteur. Le sélecteur affiche toutes les versions qui sont compatibles avec la version cible du .NET Framework du projet :

[![List des versions d’Android](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

La version cible du .NET Framework peut être définie dans les paramètres du projet sous la section **Options du projet > générer > général** . Pour plus d’informations sur la version cible de .NET Framework, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

L’ensemble des widgets disponibles dans la boîte à outils est déterminé par la version du Framework cible du projet. Cela est également vrai pour les propriétés disponibles dans le panneau des **Propriétés**. La liste des widgets disponibles n’est *pas* déterminée par la valeur sélectionnée dans le sélecteur de **version** de la barre d’outils. Par exemple, si vous définissez la version cible du projet sur Android 4,4, vous pouvez toujours sélectionner Android 6,0 dans le sélecteur de version de la barre d’outils pour voir à quoi ressemble le projet dans Android 6,0, mais vous ne pouvez pas ajouter des widgets spécifiques à Android 6,0 &ndash; vous avez ne sont pas toujours limités aux widgets disponibles dans Android 4,4.

Pour plus d’informations sur les types de ressources, consultez [ressources Android](~/android/app-fundamentals/resources-in-android/index.md).

-----
