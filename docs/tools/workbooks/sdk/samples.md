---
title: Exemples d’intégrations
description: Ce document contient des liens vers des exemples qui illustrent les intégrations de Xamarin Workbooks. Les exemples liés fonctionnent avec le rendu de représentation et SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: fbe471aa7f08d85a870d68505cf2c983b7e442e9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292817"
---
# <a name="sample-integrations"></a>Exemples d’intégrations

Consultez l’exemple de [récepteur de cuisine][KitchenSink] pour obtenir un exemple fonctionnel d’une intégration. Générez `KitchenSink.sln` simplement dans Visual Studio pour Mac ou Visual Studio, puis `KitchenSink.workbook`ouvrez.

[![Capture d’écran d’intégration du récepteur de cuisine](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

L’exemple de récepteur de cuisine illustre les deux ensembles de concepts :

* Les éléments de représentation montrent comment utiliser `RepresentationManager` pour améliorer le rendu à l’aide des représentations intégrées.
* L' `Person` objet et son convertisseur JavaScript associé illustrent l' `ISerializableObject` utilisation sans passer par un fournisseur de représentation.

Consultez également [SkiaSharp][skiasharp] pour obtenir un exemple concret d’une intégration qui utilise les [représentations](~/tools/workbooks/sdk/representations.md) existantes fournies par Xamarin Workbooks pour restituer ses types.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
