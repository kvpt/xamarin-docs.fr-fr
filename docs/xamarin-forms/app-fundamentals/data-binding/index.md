---
title: Xamarin.FormsLiaison de données
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4b99877342ddbaf9ad5606ec5dc284818fcfeffd
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139696"
---
# <a name="xamarinforms-data-binding"></a>Xamarin.FormsLiaison de données

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_La liaison de données est la technique de liaison des propriétés de deux objets afin que les modifications apportées à une propriété soient répercutées automatiquement dans l’autre propriété. La liaison de données fait partie intégrante de l’architecture d’application MVVM (Model-View-ViewModel)._

## <a name="the-data-linking-problem"></a>Problème de la liaison de données

Une Xamarin.Forms application se compose d’une ou plusieurs pages, chacune contenant généralement plusieurs objets d’interface utilisateur appelés *vues*. Une des principales tâches du programme est de synchroniser ces vues, et d’effectuer le suivi des différentes valeurs ou sélections qu’elles représentent. Souvent, les vues représentent les valeurs d’une source de données sous-jacente et elles sont manipulées par l’utilisateur pour changer ces données. Quand la vue change, les données sous-jacentes doivent refléter ce changement et, inversement, quand les données sous-jacentes changent, le changement doit être reflété dans la vue.

Pour gérer correctement cette tâche, le programme doit être averti des changements des vues ou des données sous-jacentes. La solution courante est de définir des événements qui indiquent quand un changement se produit. Vous pouvez installer un gestionnaire d’événements auquel sont signalés ces changements. Il répond en transférant les données d’un objet à un autre. Toutefois, plusieurs vues impliquent plusieurs gestionnaires d’événements et, par extension, beaucoup de code.

## <a name="the-data-binding-solution"></a>Solution de la liaison de données

La liaison de données automatise ce travail et affiche les gestionnaires d’événements inutiles. Les liaisons de données peuvent être implémentées dans du code ou en XAML, mais elles sont beaucoup plus courantes en XAML, car elles permettent ainsi de réduire la taille du fichier code-behind. En remplaçant le code procédural dans les gestionnaires d’événements par du code déclaratif ou des balises, l’application est simplifiée et clarifiée.

Un des deux objets impliqués dans une liaison de données est presque toujours un élément qui dérive de `View` et constitue une partie de l’interface visuelle d’une page. L’autre objet peut être :

- Un autre `View` dérivé, généralement dans la même page.
- Un objet dans un fichier de code.

Dans les programmes de démonstration comme ceux de l’exemple [**DataBindingDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos), les liaisons de données entre deux `View` dérivées sont souvent montrées pour plus de clarté et de simplicité. Toutefois, les mêmes principes peuvent être appliqués aux liaisons de données entre un `View` et d’autres objets. Quand une application est créée à l’aide de l’architecture MVVM (Model-View-ViewModel), la classe avec les données sous-jacentes est souvent appelée ViewModel.

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

## <a name="the-command-interface"></a>[Interface de commande](commanding.md)

Implémentez la propriété `Command` avec des liaisons de données.

## <a name="compiled-bindings"></a>[Liaisons compilées](compiled-bindings.md)

Utilisez des liaisons compilées pour améliorer les performances de liaison de données.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur la liaison de données à partir de Xamarin.Forms Book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
