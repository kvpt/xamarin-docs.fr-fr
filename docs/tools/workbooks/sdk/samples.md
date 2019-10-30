---
title: Exemples d’intégrations
description: Ce document contient des liens vers des exemples qui illustrent les intégrations de Xamarin Workbooks. Les exemples liés fonctionnent avec le rendu de représentation et SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: fa66ee2b2b469900381e2d31dc5dec49eb42c4f6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018123"
---
# <a name="sample-integrations"></a>Exemples d’intégrations

Consultez l’exemple de [récepteur de cuisine][KitchenSink] pour obtenir un exemple fonctionnel d’une intégration. Générez simplement `KitchenSink.sln` dans Visual Studio pour Mac ou Visual Studio, puis ouvrez `KitchenSink.workbook`.

[Capture d’écran d’intégration du récepteur de cuisine ![](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

L’exemple de récepteur de cuisine illustre les deux ensembles de concepts :

* Les éléments de représentation montrent comment utiliser `RepresentationManager` pour améliorer le rendu à l’aide des représentations intégrées.
* L’objet `Person` et son convertisseur JavaScript associé illustrent l’utilisation de `ISerializableObject` sans passer par un fournisseur de représentation.

Consultez également [SkiaSharp][skiasharp] pour obtenir un exemple concret d’une intégration qui utilise les [représentations](~/tools/workbooks/sdk/representations.md) existantes fournies par Xamarin Workbooks pour restituer ses types.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
