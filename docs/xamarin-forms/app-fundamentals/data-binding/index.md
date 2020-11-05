---
title: Xamarin.Forms Liaison de données
description: La liaison de données est la technique qui permet de lier les propriétés de deux objets afin que les changements d’une propriété soient automatiquement répercutés sur l’autre propriété. La liaison de données fait partie intégrante de l’architecture d’application modèle-vue-vue modèle (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 819e139c412d08f37aee28251f37a86d341487de
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374574"
---
# <a name="no-locxamarinforms-data-binding"></a>Xamarin.Forms Liaison de données

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/databindingdemos)

_La liaison de données est la technique de liaison des propriétés de deux objets afin que les modifications apportées à une propriété soient répercutées automatiquement dans l’autre propriété. La liaison de données fait partie intégrante de l’architecture d’application MVVM (Model-View-ViewModel)._

## <a name="the-data-linking-problem"></a>Problème de la liaison de données

Une Xamarin.Forms application se compose d’une ou plusieurs pages, chacune contenant généralement plusieurs objets d’interface utilisateur appelés *vues*. Une des principales tâches du programme est de synchroniser ces vues, et d’effectuer le suivi des différentes valeurs ou sélections qu’elles représentent. Souvent, les vues représentent les valeurs d’une source de données sous-jacente et elles sont manipulées par l’utilisateur pour changer ces données. Quand la vue change, les données sous-jacentes doivent refléter ce changement et, inversement, quand les données sous-jacentes changent, le changement doit être reflété dans la vue.

Pour gérer correctement cette tâche, le programme doit être averti des changements des vues ou des données sous-jacentes. La solution courante est de définir des événements qui indiquent quand un changement se produit. Vous pouvez installer un gestionnaire d’événements auquel sont signalés ces changements. Il répond en transférant les données d’un objet à un autre. Toutefois, plusieurs vues impliquent plusieurs gestionnaires d’événements et, par extension, beaucoup de code.

## <a name="the-data-binding-solution"></a>Solution de la liaison de données

La liaison de données automatise ce travail et affiche les gestionnaires d’événements inutiles. Les liaisons de données peuvent être implémentées dans du code ou en XAML, mais elles sont beaucoup plus courantes en XAML, car elles permettent ainsi de réduire la taille du fichier code-behind. En remplaçant le code procédural dans les gestionnaires d’événements par du code déclaratif ou des balises, l’application est simplifiée et clarifiée.

Un des deux objets impliqués dans une liaison de données est presque toujours un élément qui dérive de `View` et constitue une partie de l’interface visuelle d’une page. L’autre objet peut être :

- Un autre `View` dérivé, généralement dans la même page.
- Un objet dans un fichier de code.

Dans les programmes de démonstration comme ceux de l’exemple [**DataBindingDemos**](/samples/xamarin/xamarin-forms-samples/databindingdemos), les liaisons de données entre deux `View` dérivées sont souvent montrées pour plus de clarté et de simplicité. Toutefois, les mêmes principes peuvent être appliqués aux liaisons de données entre un `View` et d’autres objets. Quand une application est créée à l’aide de l’architecture MVVM (Model-View-ViewModel), la classe avec les données sous-jacentes est souvent appelée ViewModel.

Les liaisons de données sont décrites dans la série d’articles suivante :

## <a name="basic-bindings"></a>[Liaisons de base](basic-bindings.md)

Découvrez la différence entre la cible et la source d’une liaison de données, et consultez des liaisons de données simples dans du code et en XAML.

## <a name="binding-mode"></a>[Mode de liaison](binding-mode.md)

Découvrez comment le mode de liaison peut contrôler le flux de données entre les deux objets.

## <a name="string-formatting"></a>[Formatage de chaîne](string-formatting.md)

Utilisez une liaison de données pour mettre en forme et afficher les objets sous forme de chaînes.

## <a name="binding-path"></a>[Chemin de liaison](binding-path.md)

Explorez plus en détail la propriété `Path` de la liaison de données pour accéder aux sous-propriétés et aux membres de la collection.

## <a name="binding-value-converters"></a>[Convertisseurs de valeur de liaison](converters.md)

Utilisez des convertisseurs de valeur de liaison pour modifier les valeurs dans la liaison de données.

## <a name="relative-bindings"></a>[Liaisons relatives](relative-bindings.md)

Utilisez des liaisons relatives pour définir la source de liaison par rapport à la position de la cible de liaison.

## <a name="binding-fallbacks"></a>[Valeurs de repli pour les liaisons](binding-fallbacks.md)

Renforcez les liaisons de données en définissant les valeurs de secours à utiliser en cas d’échec du processus de liaison.

## <a name="multi-bindings"></a>[Liaisons multiples](multibinding.md)

Attachez une collection d' [`Binding`](xref:Xamarin.Forms.Binding) objets à une propriété de cible de liaison unique.

## <a name="the-command-interface"></a>[Interface de commande](commanding.md)

Implémentez la propriété `Command` avec des liaisons de données.

## <a name="compiled-bindings"></a>[Liaisons compilées](compiled-bindings.md)

Utilisez des liaisons compilées pour améliorer les performances de liaison de données.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur la liaison de données à partir de Xamarin.Forms Book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)