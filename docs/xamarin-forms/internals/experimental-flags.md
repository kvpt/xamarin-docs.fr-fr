---
title: Indicateurs expérimentaux Xamarin. Forms
description: Les indicateurs expérimentaux Xamarin. Forms permettent à l’équipe d’ingénierie de fournir de nouvelles fonctionnalités aux utilisateurs plus rapidement, tout en étant en mesure de modifier les API de fonctionnalités avant qu’elles ne passent à une version stable.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2020
ms.openlocfilehash: cebb996da992058616f9cf96ef3212c9ce27022a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80112588"
---
# <a name="xamarinforms-experimental-flags"></a>Indicateurs expérimentaux Xamarin. Forms

Quand une nouvelle fonctionnalité Xamarin. Forms est implémentée, elle est parfois placée derrière un indicateur expérimental. Cela permet à l’équipe d’ingénierie de fournir de nouvelles fonctionnalités plus rapidement, tout en étant en mesure de modifier les API de fonctionnalités avant qu’elles ne passent à une version stable. L’indicateur expérimental est ensuite supprimé une fois la fonctionnalité déplacée vers une version stable.

Xamarin. Forms comprend les indicateurs expérimentaux suivants :

- `CarouselView_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

L’utilisation de la fonctionnalité qui se trouve derrière un indicateur expérimental nécessite que vous activiez l’indicateur, ou indicateurs, dans votre application. Il existe deux approches pour activer les indicateurs expérimentaux :

- Activez l’indicateur expérimental, ou indicateurs, dans vos projets de plateforme.
- Activez l’indicateur expérimental, ou indicateurs, dans votre classe `App`.

> [!WARNING]
> Si vous consommez les fonctionnalités qui se trouvent derrière un indicateur expérimental, sans activer l’indicateur, votre application lève une exception qui indique quel indicateur doit être activé.

## <a name="enable-flags-in-platform-projects"></a>Activer les indicateurs dans les projets de plateforme

La méthode `Xamarin.Forms.Forms.SetFlags` peut être utilisée pour activer un indicateur expérimental dans vos projets de plateforme :

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

La méthode `SetFlags` doit être appelée dans votre classe `AppDelegate` sur iOS, dans votre classe `MainActivity` sur Android et dans votre classe `App` sur UWP.

> [!IMPORTANT]
> L’activation d’un indicateur expérimental dans vos projets de plateforme doit se produire avant l’appel de la méthode `Forms.Init`.

La méthode `Xamarin.Forms.Forms.SetFlags` accepte un argument de tableau `string`, ce qui permet d’activer plusieurs indicateurs expérimentaux dans un appel de méthode unique :

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> N’appelez jamais la méthode `SetFlags` plusieurs fois, car les appels suivants remplaceront le résultat des appels précédents.

## <a name="enable-flags-in-your-app-class"></a>Activer les indicateurs dans la classe de votre application

La méthode `Device.SetFlags` peut être utilisée pour activer un indicateur expérimental dans la classe `App` de votre projet de code partagé :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

La méthode `Device.SetFlags` accepte un argument `IReadOnlyList<string>`, ce qui permet d’activer plusieurs indicateurs expérimentaux dans un appel de méthode unique :

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> N’appelez jamais la méthode `SetFlags` plusieurs fois, car les appels suivants remplaceront le résultat des appels précédents.
