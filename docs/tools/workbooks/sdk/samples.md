---
title: Exemples d’intégrations
description: Ce document contient des liens vers des exemples qui illustrent les intégrations de Xamarin Workbooks. Exemples liés fonctionnent avec le rendu de représentation et SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424598"
---
# <a name="sample-integrations"></a>Exemples d’intégrations

Consultez le [cuisine récepteur] [ KitchenSink] exemple pour obtenir un exemple d’une intégration. Il vous suffit de générer `KitchenSink.sln` dans Visual Studio pour Mac ou Visual Studio, puis ouvrez `KitchenSink.workbook`.

[![Capture d’écran de l’intégration de récepteur cuisine](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

Récepteur de cuisine illustre les deux ensembles de concepts :

* Les éléments de la représentation montrent comment utiliser `RepresentationManager` pour améliorer le rendu en utilisant les représentations sous forme intégrés.
* Le `Person` objet et son convertisseur de JavaScript associé illustrent l’utilisation `ISerializableObject` sans passer par un fournisseur de représentation.

Consultez également [SkiaSharp][skiasharp] pour obtenir un exemple réel d’une intégration qui utilise existant [représentations](~/tools/workbooks/sdk/representations.md) fournis par Xamarin Workbooks pour afficher ses types.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
