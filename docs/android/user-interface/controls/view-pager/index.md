---
title: ViewPager
description: ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter la navigation Gestural avec ViewPager, avec et sans fragments. Elle explique également comment ajouter des indicateurs de page à l’aide de PagerTitleStrip et PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 427ace2043f966b617a258b5f50fa42f943e707e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457651"
---
# <a name="viewpager"></a>ViewPager

_ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter la navigation Gestural avec ViewPager, avec et sans fragments. Elle explique également comment ajouter des indicateurs de page à l’aide de PagerTitleStrip et PagerTabStrip._

## <a name="overview"></a>Vue d’ensemble

Un scénario courant dans le développement d’applications est la nécessité de fournir aux utilisateurs une navigation Gestural entre les vues frères. Dans cette approche, l’utilisateur glisse vers la gauche ou vers la droite pour accéder aux pages de contenu (par exemple, dans un assistant d’installation ou un diaporama). Vous pouvez créer ces vues balayées à l’aide du `ViewPager` widget, disponible dans la [bibliothèque de prise en charge Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Le `ViewPager` est un widget de disposition constitué de plusieurs vues enfants où chaque vue enfant constitue une page de la disposition : 

[![Captures d’écran de l’application TreePager avec un exemple de balayage horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

En général, `ViewPager` est utilisé conjointement avec les [fragments](~/android/platform/fragments/index.md); Toutefois, il existe des situations où vous pouvez souhaiter utiliser `ViewPager` sans la complexité supplémentaire de `Fragment` s.

`ViewPager` utilise un modèle d’adaptateur pour lui fournir les vues à afficher. L’adaptateur utilisé ici est conceptuellement similaire à celui utilisé par [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; vous fournissez une implémentation de `PagerAdapter` pour générer les pages que le `ViewPager` affiche à l’utilisateur. Les pages affichées par `ViewPager` peuvent être `View` s ou `Fragment` s. Quand `View` les s sont affichés, l’adaptateur sous-classe la `PagerAdapter` classe de base d’Android. Si `Fragment` des s sont affichés, l’adaptateur sous-classe les Android `FragmentPagerAdapter` . La bibliothèque de prise en charge Android comprend également `FragmentPagerAdapter` (une sous-classe de `PagerAdapter` ) pour vous aider à obtenir des informations détaillées sur la connexion des `Fragment` s aux données. 

Ce guide présente les deux approches : 

- Dans [Viewpager avec views](~/android/user-interface/controls/view-pager/viewpager-and-views.md), une application [TreePager](/samples/xamarin/monodroid-samples/userinterface-treepager) est développée pour montrer comment utiliser `ViewPager` pour afficher les vues d’un catalogue d’arborescence (une galerie d’images d’arbres à feuilles de feuilles et de feuilles de route). 
    `PagerTabStrip`  et `PagerTitleStrip` sont utilisés pour afficher des titres qui facilitent la navigation entre les pages.

- Dans [Viewpager avec des fragments](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), une application [FlashCardPager](/samples/xamarin/monodroid-samples/userinterface-flashcardpager) légèrement plus complexe est développée pour montrer comment utiliser `ViewPager` avec `Fragment` pour créer une application qui présente des problèmes mathématiques sous forme de cartes Flash et répond aux entrées d’utilisateur. 

## <a name="requirements"></a>Configuration requise

Pour utiliser `ViewPager` dans votre projet d’application, vous devez installer le package v4 de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : inclusion d’un NuGet dans votre projet](/visualstudio/mac/nuget-walkthrough). 

## <a name="architecture"></a>Architecture

Trois composants sont utilisés pour implémenter la navigation Gestural avec `ViewPager` :

- ViewPager
- Adaptateur
- Indicateur de radiomessagerie

Chacun de ces composants est résumé ci-dessous.

### <a name="viewpager"></a>ViewPager

`ViewPager` est un gestionnaire de disposition qui affiche une collection d’un `View` à la fois. Son travail consiste à détecter le mouvement de balayage de l’utilisateur et à accéder à l’affichage suivant ou précédent, le cas échéant. Par exemple, la capture d’écran ci-dessous illustre une `ViewPager` transition d’une image à la suivante en réponse à un mouvement utilisateur : 

[![Gros plan de l’application TreePager affichant une transition entre les vues](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>Adaptateur

`ViewPager` extrait ses données d’un *adaptateur*. La tâche de l’adaptateur consiste à créer les `View` s affichés par le `ViewPager` , en les fournissant en fonction des besoins. Le diagramme ci-dessous illustre ce concept &ndash; que l’adaptateur crée et remplit les `View` et les fournit au `ViewPager` . Étant donné que `ViewPager` détecte les gestes de balayage de l’utilisateur, il demande à l’adaptateur de fournir le approprié `View` pour afficher : 

[![Diagramme illustrant la manière dont l’adaptateur connecte les images et les noms à ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

Dans cet exemple, chaque `View` est construit à partir d’une image d’arborescence et d’un nom d’arborescence avant d’être passé à l' `ViewPager` . 

### <a name="pager-indicator"></a>Indicateur de radiomessagerie

`ViewPager` peut être utilisé pour afficher un grand jeu de données (par exemple, une galerie d’images peut contenir des centaines d’images). Pour aider l’utilisateur à naviguer dans de grands jeux de données, `ViewPager` est souvent accompagné d’un *indicateur de radiomessagerie* qui affiche une chaîne. La chaîne peut être le titre de l’image, une légende ou simplement la position actuelle de l’affichage dans le jeu de données. 

Il existe deux vues qui peuvent produire ces informations de navigation pour vous : `PagerTabStrip` et `PagerTitleStrip.` chacune affiche une chaîne en haut d’un `ViewPager` , et chacune extrait ses données de la `ViewPager` carte de pour qu’elles restent synchronisées avec l’affichage en cours `View` . La différence réside dans le fait que `PagerTabStrip` comprend un indicateur visuel pour la chaîne « actuelle » alors que `PagerTitleStrip` ne le fait pas (comme indiqué dans ces captures d’écran) : 

[![Captures d’écran de l’application TreePager avec PagerTitleStrip et PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Ce guide montre comment effectuer `ViewPager` des immplement, des adaptateurs et des composants d’application indicateur et les intégrer pour prendre en charge la navigation Gestural. 

## <a name="related-links"></a>Liens associés

- [TreePager (exemple)](/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (exemple)](/samples/xamarin/monodroid-samples/userinterface-flashcardpager)