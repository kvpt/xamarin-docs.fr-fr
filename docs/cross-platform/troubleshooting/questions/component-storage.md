---
title: Où sont stockés les composants sur ma machine ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 4152c8ef7eeba3748d9244e27e48f3f9a2c0019b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359096"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Où sont stockés les composants sur ma machine ?

Chaque fois que vous installez un composant de Xamarin dans un projet d’application, il est placé dans deux emplacements :

1. Dans un dossier de composants à la racine de votre dossier de Solution. Si vous supprimez le composant à partir de tous les projets dans la solution, il sera obtenir supprimé de ce dossier.

2. Une copie est également stockée dans les emplacements suivants :
    - Windows : `%LocalAppData%\Xamarin\Cache\Components`
    - Mac : `~/Library/Caches/Xamarin/Components`

Par conséquent, pour supprimer un composant à partir de votre système, supprimez-le à partir de vos projets et des solutions et à partir du dossier de cache ci-dessus.
