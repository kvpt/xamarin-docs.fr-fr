---
title: Xamarin.Forms indicateurs expérimentaux
description: Xamarin.Forms les indicateurs expérimentaux permettent à l’équipe d’ingénierie de fournir de nouvelles fonctionnalités aux utilisateurs plus rapidement, tout en étant en mesure de modifier les API de fonctionnalités avant qu’elles ne passent à une version stable.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9f6f5e2d27f04dadfa2b830703bb36355072c44
ms.sourcegitcommit: c9f62ed4bab516be18606e87e89e01f57b4ad17e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163489"
---
# <a name="no-locxamarinforms-experimental-flags"></a>Xamarin.Forms indicateurs expérimentaux

Quand une nouvelle Xamarin.Forms fonctionnalité est implémentée, elle est parfois placée derrière un indicateur expérimental. Cela permet à l’équipe d’ingénierie de fournir de nouvelles fonctionnalités plus rapidement, tout en étant en mesure de modifier les API de fonctionnalités avant qu’elles ne passent à une version stable. L’indicateur expérimental est ensuite supprimé une fois la fonctionnalité déplacée vers une version stable.

Xamarin.Forms comprend les indicateurs expérimentaux suivants :

- `Shell_UWP_Experimental`

L’utilisation de la fonctionnalité qui se trouve derrière un indicateur expérimental nécessite que vous activiez l’indicateur, ou indicateurs, dans votre application. Il existe deux approches pour activer les indicateurs expérimentaux :

- Activez l’indicateur expérimental dans vos projets de plateforme.
- Activez l’indicateur expérimental dans votre `App` classe.

> [!WARNING]
> Si vous consommez les fonctionnalités qui se trouvent derrière un indicateur expérimental, sans activer l’indicateur, votre application lève une exception qui indique quel indicateur doit être activé.

## <a name="enable-flags-in-platform-projects"></a>Activer les indicateurs dans les projets de plateforme

La `Xamarin.Forms.Forms.SetFlags` méthode peut être utilisée pour activer un indicateur expérimental dans vos projets de plateforme :

```csharp
Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

La `SetFlags` méthode doit être appelée dans votre `AppDelegate` classe sur iOS, dans votre `MainActivity` classe sur Android et dans votre `App` classe sur UWP.

> [!IMPORTANT]
> L’activation d’un indicateur expérimental dans vos projets de plateforme doit se produire avant que la `Forms.Init` méthode ne soit appelée.

La `Xamarin.Forms.Forms.SetFlags` méthode accepte un `string` argument de tableau, ce qui permet d’activer plusieurs indicateurs expérimentaux dans un appel de méthode unique :

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> N’appelez jamais la `SetFlags` méthode plusieurs fois, car les appels suivants remplaceront le résultat des appels précédents.

## <a name="enable-flags-in-your-app-class"></a>Activer les indicateurs dans la classe de votre application

La `Device.SetFlags` méthode peut être utilisée pour activer un indicateur expérimental dans la `App` classe de votre projet de code partagé :

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental" });
```

La `Device.SetFlags` méthode accepte un `IReadOnlyList<string>` argument, ce qui permet d’activer plusieurs indicateurs expérimentaux dans un appel de méthode unique :

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> N’appelez jamais la `SetFlags` méthode plusieurs fois, car les appels suivants remplaceront le résultat des appels précédents.

## <a name="old-experimental-flags"></a>Anciens indicateurs expérimentaux

Le tableau suivant répertorie les indicateurs expérimentaux pour les fonctionnalités qui sont désormais en disponibilité générale et la Xamarin.Forms version dans laquelle l’indicateur expérimental a été supprimé :

| Indicateur | Xamarin.Forms 3/05 |
| ---- | --------------------- |
| `AppTheme_Experimental` | 4.8 |
| `Brush_Experimental` | 5.0 |
| `CarouselView_Experimental` | 5.0 |
| `CollectionView_Experimental` | 4.3 |
| `DragAndDrop_Experimental` | 5.0 |
| `FastRenderers_Experimental` | 4.0 |
| `IndicatorView_Experimental` | 4,7 |
| `Markup_Experimental` | 5,0 (déplacé vers Xamarin Community Toolkit) |
| `MediaElement_Experimental` | 5,0 (déplacé vers Xamarin Community Toolkit) |
| `RadioButton_Experimental` | 5.0 |
| `Shapes_Experimental` | 5.0 |
| `Shell_Experimental` | 4.0  |
| `StateTriggers_Experimental` | 4,7 |
| `SwipeView_Experimental` | 5.0 |
| `Visual_Experimental` | 3.6 |
