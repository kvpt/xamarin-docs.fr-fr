---
title: Incorporation .NET
description: L’incorporation .net permet à votre code .net existantC#( F#,, et autres) d’être consommé par du code écrit dans d’autres langages de programmation.
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 5a0e7eeaee9b3189de63d0b82a3822cc68023505
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006949"
---
# <a name="net-embedding"></a>Incorporation .NET

![Aperçu](~/media/shared/preview.png)

L’incorporation .net permet à votre code .net existantC#( F#,, et autres) d’être consommé à partir d’autres langages de programmation et dans différents environnements.

Cela signifie que si vous avez une bibliothèque .NET que vous souhaitez utiliser à partir de votre application iOS existante, vous pouvez le faire.   Ou si vous souhaitez le lier à une bibliothèque native C++ , vous pouvez également le faire.   Ou utiliser du code .NET à partir de Java.

L’incorporation .NET est basée sur le projet open source [Embeddinator-4000](https://github.com/mono/Embeddinator-4000) .

## <a name="environments-and-languages"></a>Environnements et langues

L’outil est conscient de l’environnement qu’il utilise, ainsi que du langage qui le consomme.   Par exemple, la plateforme iOS n’autorise pas la compilation juste-à-temps (JIT). par conséquent, l’incorporation .NET compile de manière statique votre code .NET en code natif qui peut être utilisé dans iOS.  D’autres environnements autorisent la compilation JIT, et dans ces environnements, nous choisissons la compilation JIT.

Il prend en charge plusieurs consommateurs de langage. il est donc destiné au code .NET en tant que code idiomatique dans le langage cible.   Voici la liste des langues prises en charge à l’heure actuelle :

- [**Objective-c**](objective-c/index.md) : mappage de .net vers des API idiomatique objective-c
- [**Java**](android/index.md) : mappage de .net à des API Java idiomatique
- [**C**](get-started/c.md) -mappage de .net à des API orientées objet comme c

D’autres langues seront fournies plus tard.

## <a name="getting-started"></a>Commencer

Pour commencer, consultez l’un de nos guides pour chacune des langues actuellement prises en charge :

- [**Objective-C**](get-started/objective-c/index.md) – couvre MacOS et iOS
- [**Java**](get-started/java/index.md) : couvre MacOS et Android
- [**C**](get-started/c.md) – aborde le langage c sur les plateformes de bureau

## <a name="related-links"></a>Liens associés

- [Embeddinator-4000 sur GitHub](https://github.com/mono/Embeddinator-4000)
