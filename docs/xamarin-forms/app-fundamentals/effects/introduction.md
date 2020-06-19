---
title: Présentation des effets
description: Les effets permettent de personnaliser les contrôles natifs de chaque plateforme et sont généralement utilisés pour les petits changements de style. Cet article présente les effets, décrit la limite entre les effets et les renderers personnalisés, et décrit la classe PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a891ec70f6f83984ed463fe914442758bdf57a2e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139306"
---
# <a name="introduction-to-effects"></a>Présentation des effets

_Les effets permettent de personnaliser les contrôles natifs sur chaque plateforme et sont généralement utilisés pour les petites modifications de style. Cet article fournit une introduction aux effets, présente la limite entre les effets et les convertisseurs personnalisés, et décrit la classe PlatformEffect._

Xamarin.Forms[Les pages, les dispositions et les contrôles](~/xamarin-forms/user-interface/controls/index.md) présentent une API commune pour décrire les interfaces utilisateur mobiles multiplateforme. Chaque page, disposition et contrôle est rendu différemment sur chaque plateforme à l’aide d’une `Renderer` classe qui crée à son tour un contrôle natif (correspondant à la Xamarin.Forms représentation), le réorganise à l’écran et ajoute le comportement spécifié dans le code partagé.

Les développeurs peuvent implémenter leurs propres classes `Renderer` pour personnaliser l’apparence et/ou le comportement d’un contrôle. Cependant, l’implémentation d’une classe de renderer personnalisée pour effectuer une simple personnalisation d’un contrôle est souvent une réponse trop lourde. Les effets simplifient ce processus, en permettant une personnalisation plus facile des contrôles natifs sur chaque plateforme.

Les effets sont créés dans des projets spécifiques à la plateforme en sous-classant le `PlatformEffect` contrôle, puis les effets sont consommés en les joignant à un contrôle approprié dans une Xamarin.Forms bibliothèque .NET standard ou un projet de bibliothèque partagée.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Pourquoi utiliser un effet sur un renderer personnalisé ?

Les effets simplifient la personnalisation d’un contrôle sont réutilisables et peuvent être paramétrés de façon à renforcer leur réutilisabilité.

Tout ce qui peut être obtenu avec un effet peut également être obtenu avec un renderer personnalisé. Cependant , les renderers personnalisés offrent davantage de flexibilité et de possibilités de personnalisation que les effets. Les instructions suivantes listent les circonstances dans lesquelles il faut choisir un effet plutôt qu’un renderer personnalisé :

- Un effet est recommandé quand la modification des propriétés d’un contrôle spécifique à une plateforme permet d’obtenir le résultat souhaité.
- Il faut un renderer personnalisé quand il est nécessaire de remplacer les méthodes d’un contrôle spécifique à une plateforme.
- Un convertisseur personnalisé est nécessaire lorsque vous devez remplacer le contrôle spécifique à la plateforme qui implémente un Xamarin.Forms contrôle.

## <a name="subclassing-the-platformeffect-class"></a>Sous-classement de la classe PlatformEffect

Le tableau suivant liste l’espace de noms pour la classe `PlatformEffect` sur chaque plateforme et les types de ses propriétés :

|Plateforme|Espace de noms|Conteneur|Control|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms. Platform. iOS|UIView|UIView|
|Android|Xamarin.Forms. Platform. Android|ViewGroup|Affichage|
|Plateforme Windows universelle (UWP)|Xamarin.Forms. Platform. UWP|FrameworkElement|FrameworkElement|

Chaque classe `PlatformEffect` spécifique à une plateforme expose les propriétés suivantes :

- `Container` : référence le contrôle spécifique à une plateforme qui est utilisé pour implémenter la disposition.
- `Control`: référence le contrôle spécifique à la plateforme utilisé pour implémenter le Xamarin.Forms contrôle.
- `Element`: référence le Xamarin.Forms contrôle en cours de rendu.

Les effets n’ont pas d’informations de type sur le conteneur, le contrôle ou l’élément auquel ils sont attachés, car ils peuvent être attachés à n’importe quel élément. Ainsi, quand un effet est attaché à un élément qu’il ne prend pas en charge, il doit passer automatiquement en mode dégradé ou lever une exception. Cependant, les propriétés `Container`, `Control` et `Element` peuvent être castées dans leur type d’implémentation. Pour plus d’informations sur ces types, consultez [Classes de base de renderer et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Chaque classe `PlatformEffect` spécifique à une plateforme expose les méthodes suivantes, qui doivent être remplacées pour implémenter un effet :

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached): appelée lorsqu’un effet est attaché à un Xamarin.Forms contrôle. Une version substituée de cette méthode, dans chaque classe d’effet spécifique à la plateforme, est l’emplacement où effectuer la personnalisation du contrôle, ainsi que la gestion des exceptions dans le cas où l’effet ne peut pas être appliqué au Xamarin.Forms contrôle spécifié.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached): appelée lorsqu’un effet est détaché d’un Xamarin.Forms contrôle. Une version remplacée de cette méthode, dans la classe d’effet spécifique à chaque plateforme, est l’endroit où effectuer le nettoyage de l’effet, comme désinscrire un gestionnaire d’événements.

En outre, le `PlatformEffect` expose la [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) méthode, qui peut également être substituée. Cette méthode est appelée quand une propriété de l’élément a changé. Une version substituée de cette méthode, dans chaque classe d’effet spécifique à la plateforme, est l’endroit où répondre aux modifications de propriétés pouvant être liées sur le Xamarin.Forms contrôle. Une vérification de la propriété changée doit toujours être effectuée, car cette substitution peut être appelée plusieurs fois.

## <a name="related-links"></a>Liens connexes

- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
