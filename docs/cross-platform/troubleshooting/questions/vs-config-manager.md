---
title: Pourquoi Visual Studio ne contient pas mon projet de bibliothèque référencée dans ma build ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 37fa93ef7377456d61d1a5f5de56d5de6b0f3c7f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282909"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>Pourquoi Visual Studio ne contient pas mon projet de bibliothèque référencée dans ma build ?

Visual Studio utilise le **Configuration Manager** pour déterminer quels projets dans une solution sont automatiquement inclus dans une build ou une configuration de déploiement donnée.

Certains modèles qui sont générés avec un projet de bibliothèque référencé auront déjà la bibliothèque référencée incluse dans la configuration ; dans le cas contraire, il devra être défini manuellement.

## <a name="how-to-use-the-configuration-manager"></a>Comment utiliser les Configuration Manager

1. Ouvrir la **> de Build Configuration Manager**
2. Sélectionnez la configuration à personnaliser, par exemple **Debug | iPhone**
3. Sélectionnez les cases à cocher pour les projets que vous souhaitez inclure.

> [!NOTE]
> Les zones grisées sont gérées automatiquement et ne doivent pas être modifiées.

Capture vidéo de ces étapes :[http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
