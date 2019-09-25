---
title: Présentation des effets
description: Les effets permettent de personnaliser les contrôles natifs de chaque plateforme et sont généralement utilisés pour les petits changements de style. Cet article présente les effets, décrit la limite entre les effets et les renderers personnalisés, et décrit la classe PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9325c34c645b75f28c7e2070f6bb095780ddb02
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70771459"
---
# <a name="introduction-to-effects"></a>Présentation des effets

_Les effets permettent de personnaliser les contrôles natifs de chaque plateforme et sont généralement utilisés pour les petits changements de style. Cet article présente les effets, décrit la limite entre les effets et les renderers personnalisés, et décrit la classe PlatformEffect._

[Pages, dispositions et contrôles](~/xamarin-forms/user-interface/controls/index.md) Xamarin.Forms présente une API commune pour décrire des interfaces utilisateur mobiles multiplateformes. Chaque page, disposition et contrôle est restitué différemment sur chaque plateforme avec une classe `Renderer` qui crée à son tour un contrôle natif (correspondant à la représentation de Xamarin.Forms), le dispose sur l’écran et ajoute le comportement spécifié dans le code partagé.

Les développeurs peuvent implémenter leurs propres classes `Renderer` pour personnaliser l’apparence et/ou le comportement d’un contrôle. Cependant, l’implémentation d’une classe de renderer personnalisée pour effectuer une simple personnalisation d’un contrôle est souvent une réponse trop lourde. Les effets simplifient ce processus, en permettant une personnalisation plus facile des contrôles natifs sur chaque plateforme.

Les effets sont créés dans des projets spécifiques à la plateforme en sous-classant le contrôle `PlatformEffect`, puis les effets sont consommés en les attachant à un contrôle approprié dans une bibliothèque .NET Standard Xamarin.Forms ou dans un projet de bibliothèque partagée.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Pourquoi utiliser un effet sur un renderer personnalisé ?

Les effets simplifient la personnalisation d’un contrôle sont réutilisables et peuvent être paramétrés de façon à renforcer leur réutilisabilité.

Tout ce qui peut être obtenu avec un effet peut également être obtenu avec un renderer personnalisé. Cependant , les renderers personnalisés offrent davantage de flexibilité et de possibilités de personnalisation que les effets. Les instructions suivantes listent les circonstances dans lesquelles il faut choisir un effet plutôt qu’un renderer personnalisé :

- Un effet est recommandé quand la modification des propriétés d’un contrôle spécifique à une plateforme permet d’obtenir le résultat souhaité.
- Il faut un renderer personnalisé quand il est nécessaire de remplacer les méthodes d’un contrôle spécifique à une plateforme.
- Il faut un renderer personnalisé quand il est nécessaire de remplacer le contrôle spécifique à une plateforme qui implémente un contrôle Xamarin.Forms.

## <a name="subclassing-the-platformeffect-class"></a>Sous-classement de la classe PlatformEffect

Le tableau suivant liste l’espace de noms pour la classe `PlatformEffect` sur chaque plateforme et les types de ses propriétés :

|Plateforme|Espace de noms|Conteneur|Contrôle|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|Vue|
|Plateforme Windows universelle (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Chaque classe `PlatformEffect` spécifique à une plateforme expose les propriétés suivantes :

- `Container` : référence le contrôle spécifique à une plateforme qui est utilisé pour implémenter la disposition.
- `Control` : référence le contrôle spécifique à une plateforme qui est utilisé pour implémenter le contrôle Xamarin.Forms.
- `Element` : référence le contrôle Xamarin.Forms qui est rendu.

Les effets n’ont pas d’informations de type sur le conteneur, le contrôle ou l’élément auquel ils sont attachés, car ils peuvent être attachés à n’importe quel élément. Ainsi, quand un effet est attaché à un élément qu’il ne prend pas en charge, il doit passer automatiquement en mode dégradé ou lever une exception. Cependant, les propriétés `Container`, `Control` et `Element` peuvent être castées dans leur type d’implémentation. Pour plus d’informations sur ces types, consultez [Classes de base de renderer et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Chaque classe `PlatformEffect` spécifique à une plateforme expose les méthodes suivantes, qui doivent être remplacées pour implémenter un effet :

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) : appelée quand un effet est attaché à un contrôle Xamarin.Forms. Une version remplacée de cette méthode, dans la classe d’effet spécifique à chaque plateforme, est l’endroit où effectuer la personnalisation du contrôle, ainsi que la gestion des exceptions dans le cas où l’effet ne peut pas être appliqué au contrôle Xamarin.Forms spécifié.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) : appelée quand un effet est détaché d’un contrôle Xamarin.Forms. Une version remplacée de cette méthode, dans la classe d’effet spécifique à chaque plateforme, est l’endroit où effectuer le nettoyage de l’effet, comme désinscrire un gestionnaire d’événements.

En outre, `PlatformEffect` expose la méthode [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)), qui peut également être remplacée. Cette méthode est appelée quand une propriété de l’élément a changé. Une version remplacée de cette méthode, dans la classe d’effet spécifique à chaque plateforme, est l’endroit où répondre aux changements des propriétés pouvant être liées sur le contrôle Xamarin.Forms. Une vérification de la propriété changée doit toujours être effectuée, car ce remplacement peut être appelé plusieurs fois.

## <a name="related-links"></a>Liens associés

- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
