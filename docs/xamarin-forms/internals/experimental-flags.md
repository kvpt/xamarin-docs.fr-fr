---
title: Xamarin.Formsindicateurs expérimentaux
description: Xamarin.Formsles indicateurs expérimentaux permettent à l’équipe d’ingénierie de fournir de nouvelles fonctionnalités aux utilisateurs plus rapidement, tout en étant en mesure de modifier les API de fonctionnalités avant qu’elles ne passent à une version stable.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/31/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6dafdc547e4673134e8ff3badc85a3527dfc55c4
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917422"
---
# <a name="no-locxamarinforms-experimental-flags"></a>Xamarin.Formsindicateurs expérimentaux

Quand une nouvelle Xamarin.Forms fonctionnalité est implémentée, elle est parfois placée derrière un indicateur expérimental. Cela permet à l’équipe d’ingénierie de fournir de nouvelles fonctionnalités plus rapidement, tout en étant en mesure de modifier les API de fonctionnalités avant qu’elles ne passent à une version stable. L’indicateur expérimental est ensuite supprimé une fois la fonctionnalité déplacée vers une version stable.

Xamarin.Formscomprend les indicateurs expérimentaux suivants :

- `Brush_Experimental`
- `CarouselView_Experimental`
- `DragAndDrop_Experimental`
- `Expander_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `RadioButton_Experimental`
- `Shapes_Experimental`
- `Shell_UWP_Experimental`
- `SwipeView_Experimental`

L’utilisation de la fonctionnalité qui se trouve derrière un indicateur expérimental nécessite que vous activiez l’indicateur, ou indicateurs, dans votre application. Il existe deux approches pour activer les indicateurs expérimentaux :

- Activez l’indicateur expérimental, ou indicateurs, dans vos projets de plateforme.
- Activez l’indicateur expérimental, ou indicateurs, dans votre `App` classe.

> [!WARNING]
> Si vous consommez les fonctionnalités qui se trouvent derrière un indicateur expérimental, sans activer l’indicateur, votre application lève une exception qui indique quel indicateur doit être activé.

## <a name="enable-flags-in-platform-projects"></a>Activer les indicateurs dans les projets de plateforme

La `Xamarin.Forms.Forms.SetFlags` méthode peut être utilisée pour activer un indicateur expérimental dans vos projets de plateforme :

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

La `SetFlags` méthode doit être appelée dans votre `AppDelegate` classe sur iOS, dans votre `MainActivity` classe sur Android et dans votre `App` classe sur UWP.

> [!IMPORTANT]
> L’activation d’un indicateur expérimental dans vos projets de plateforme doit se produire avant que la `Forms.Init` méthode ne soit appelée.

La `Xamarin.Forms.Forms.SetFlags` méthode accepte un `string` argument de tableau, ce qui permet d’activer plusieurs indicateurs expérimentaux dans un appel de méthode unique :

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> N’appelez jamais la `SetFlags` méthode plusieurs fois, car les appels suivants remplaceront le résultat des appels précédents.

## <a name="enable-flags-in-your-app-class"></a>Activer les indicateurs dans la classe de votre application

La `Device.SetFlags` méthode peut être utilisée pour activer un indicateur expérimental dans la `App` classe de votre projet de code partagé :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

La `Device.SetFlags` méthode accepte un `IReadOnlyList<string>` argument, ce qui permet d’activer plusieurs indicateurs expérimentaux dans un appel de méthode unique :

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> N’appelez jamais la `SetFlags` méthode plusieurs fois, car les appels suivants remplaceront le résultat des appels précédents.
