---
title: Prise en charge de UrhoSharp Mac
description: Ce document aborde la prise en charge de macOS de UrhoSharp. Il décrit comment créer un projet et fournit un lien vers un exemple de code.
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 6d0a048020284319682c1bee0f9a1d7f9af00977
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386362"
---
# <a name="urhosharp-mac-support"></a>Prise en charge de UrhoSharp Mac

_Fonctionnalités et configuration du Mac spécifique_

Bien que Urho est une bibliothèque de classes portable, et permet à la même API pour être utilisé sur la plateforme différents pour votre logique de jeu, vous devez toujours initialiser Urho dans votre pilote de plateforme spécifique et dans certains cas, vous pouvez tirer parti des fonctionnalités spécifiques de plateforme .

Dans les pages ci-dessous, supposons que `MyGame` est une sous-classe de la `Application` classe.

## <a name="macos"></a>macOS

**Architectures prises en charge :** x86/x86-64 pour 32 bits et 64 bits.

## <a name="creating-a-project"></a>Création d'un projet

Créez un projet console, référencer le Urho NuGet et assurez-vous que vous pouvez localiser les ressources (les répertoires contenant le répertoire de données).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Exemple

[Exemple complet](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


