---
title: ViewPager
description: ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter la navigation Gestural avec ViewPager, avec et sans fragments. Elle explique également comment ajouter des indicateurs de page à l’aide de PagerTitleStrip et PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 600a94a0ee9eb5bcf06dc19d95cf9e77132a2e81
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029064"
---
# <a name="viewpager"></a>ViewPager

_ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter la navigation Gestural avec ViewPager, avec et sans fragments. Elle explique également comment ajouter des indicateurs de page à l’aide de PagerTitleStrip et PagerTabStrip._

## <a name="overview"></a>Overview

Un scénario courant dans le développement d’applications est la nécessité de fournir aux utilisateurs une navigation Gestural entre les vues frères. Dans cette approche, l’utilisateur glisse vers la gauche ou vers la droite pour accéder aux pages de contenu (par exemple, dans un assistant d’installation ou un diaporama). Vous pouvez créer ces vues balayées à l’aide du widget `ViewPager`, disponible dans la [bibliothèque de prise en charge Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Le `ViewPager` est un widget de disposition constitué de plusieurs vues enfants où chaque vue enfant constitue une page dans la mise en page : 

[Captures d’écran ![de l’application TreePager avec un exemple de balayage horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

En général, `ViewPager` est utilisé conjointement avec les [fragments](~/android/platform/fragments/index.md); Toutefois, il existe des situations où vous pouvez souhaiter utiliser `ViewPager` sans la complexité supplémentaire de `Fragment`s.

`ViewPager` utilise un modèle d’adaptateur pour lui fournir les vues à afficher. L’adaptateur utilisé ici est conceptuellement similaire à celui utilisé par [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; vous fournissez une implémentation de `PagerAdapter` pour générer les pages que l' `ViewPager` affiche à l’utilisateur. Les pages affichées par `ViewPager` peuvent être `View`s ou `Fragment`s. Lorsque `View`s sont affichées, l’adaptateur sous-classe la classe de base d’Android `PagerAdapter`. Si `Fragment`s sont affichées, l’adaptateur sous-classe les `FragmentPagerAdapter`Android. La bibliothèque de prise en charge Android comprend également `FragmentPagerAdapter` (une sous-classe de `PagerAdapter`) pour faciliter les détails de la connexion de `Fragment`s aux données. 

Ce guide présente les deux approches : 

- Dans [Viewpager avec des vues](~/android/user-interface/controls/view-pager/viewpager-and-views.md), une application [TreePager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager) est développée pour montrer comment utiliser `ViewPager` pour afficher les vues d’un catalogue d’arborescence (une galerie d’images d’arbres à feuilles de route et de feuilles persistantes). 
    `PagerTabStrip` et `PagerTitleStrip` sont utilisés pour afficher des titres qui facilitent la navigation entre les pages.

- Dans [Viewpager avec des fragments](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), une application [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) légèrement plus complexe est développée pour montrer comment utiliser `ViewPager` avec `Fragment`s pour créer une application qui présente des problèmes mathématiques sous forme de cartes Flash et répond à une entrée d’utilisateur. 

## <a name="requirements"></a>Configuration requise

Pour utiliser `ViewPager` dans votre projet d’application, vous devez installer le package v4 de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : inclusion d’un NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

## <a name="architecture"></a>Architecture

Trois composants sont utilisés pour implémenter la navigation Gestural avec `ViewPager`:

- ViewPager
- Adaptateur
- Indicateur de radiomessagerie

Chacun de ces composants est résumé ci-dessous.

### <a name="viewpager"></a>ViewPager

`ViewPager` est un gestionnaire de disposition qui affiche une collection d' `View`s un à la fois. Son travail consiste à détecter le mouvement de balayage de l’utilisateur et à accéder à l’affichage suivant ou précédent, le cas échéant. Par exemple, la capture d’écran ci-dessous illustre une `ViewPager` effectuant la transition d’une image à la suivante en réponse à un mouvement utilisateur : 

[![gros plan de l’application TreePager affichant une transition entre des vues](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>Adaptateur

`ViewPager` extrait ses données d’un *adaptateur*. La tâche de l’adaptateur consiste à créer le `View`s affiché par le `ViewPager`, en le fournissant en fonction des besoins. Le diagramme ci-dessous illustre ce concept &ndash; l’adaptateur crée et remplit `View`s et les fournit au `ViewPager`. Comme le `ViewPager` détecte les gestes de balayage de l’utilisateur, il demande à l’adaptateur de fournir le `View` approprié à afficher : 

[Diagramme de ![illustrant la manière dont l’adaptateur connecte les images et les noms à ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

Dans cet exemple, chaque `View` est construit à partir d’une image d’arborescence et d’un nom d’arborescence avant d’être transmis à la `ViewPager`. 

### <a name="pager-indicator"></a>Indicateur de radiomessagerie

`ViewPager` peut être utilisé pour afficher un grand jeu de données (par exemple, une galerie d’images peut contenir des centaines d’images). Pour aider l’utilisateur à naviguer dans de grands jeux de données, `ViewPager` est souvent accompagné d’un *indicateur de radiomessagerie* qui affiche une chaîne. La chaîne peut être le titre de l’image, une légende ou simplement la position actuelle de l’affichage dans le jeu de données. 

Il existe deux vues qui peuvent produire ces informations de navigation : `PagerTabStrip` et `PagerTitleStrip.` affichent chacune une chaîne en haut d’un `ViewPager`, et chacune extrait ses données de l’adaptateur de l' `ViewPager`afin qu’elles restent toujours synchronisées avec la `View`actuellement affichée. La différence réside dans le fait que `PagerTabStrip` comprend un indicateur visuel pour la chaîne « actuelle » alors que `PagerTitleStrip` ne le fait pas (comme indiqué dans ces captures d’écran) : 

[![captures d’écran de l’application TreePager avec PagerTitleStrip et PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Ce guide montre comment immplement des composants d’application `ViewPager`, d’adaptateur et d’indicateur et les intégrer pour prendre en charge la navigation Gestural. 

## <a name="related-links"></a>Liens connexes

- [TreePager (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
