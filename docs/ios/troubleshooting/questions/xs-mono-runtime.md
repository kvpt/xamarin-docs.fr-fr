---
title: Comment définir des variables d’environnement Mono Runtime pour des projets iOS dans Xamarin Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/31/2017
ms.openlocfilehash: fdd208122934b6fa8194a644592d1e23c4000d57
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030886"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Comment définir des variables d’environnement Mono Runtime pour des projets iOS dans Xamarin Studio ?

Si vous devez définir des variables d’environnement d’exécution pour mono, vous pouvez les définir dans les **options de projet > page exécuter > général** .

Remarque : les variables d’environnement de nettoyage de la mémoire pour SGen (MONO\_GC\_params) définies de cette façon seront uniquement utilisées lors du lancement à partir de Xamarin Studio. Si vous lancez l’application à partir de l’appareil, les paramètres de SGen seront ignorés. 

Pour définir définitivement une variable d’environnement pour une application, vous devez l’ajouter en tant qu’argument mTouch supplémentaire (pour toutes les configurations pertinentes) :

```csharp
   --setenv=NAME=VALUE
```

Pour afficher les variables d’environnement qui peuvent être définies, reportez-vous à la page man mono : [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1)) consultez la section intitulée : `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Setting environment variables for a project")
