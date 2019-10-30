---
title: Où sont stockés les composants sur ma machine ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 9447cf903c8789078e66082e720eeecfa7bb3e0d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014239"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Où sont stockés les composants sur ma machine ?

Chaque fois que vous installez un composant Xamarin dans un projet d’application, il est placé à deux emplacements :

1. Dans un dossier de composants au niveau racine de votre dossier de solution. Si vous supprimez le composant de tous les projets de la solution, il est également supprimé de ce dossier.

2. Une copie est également stockée aux emplacements suivants :
    - Windows : `%LocalAppData%\Xamarin\Cache\Components`
    - Mac : `~/Library/Caches/Xamarin/Components`

Pour supprimer complètement un composant de votre système, supprimez-le de vos projets/solutions et du dossier cache ci-dessus.
