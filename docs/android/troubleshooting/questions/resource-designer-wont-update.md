---
title: Mon fichier Android Resource.designer.cs ne se met pas à jour
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: 65f7c6d8a573501ffec4aa1b8eaf28ff1e6479e8
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864220"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Mon fichier Android Resource.designer.cs ne se met pas à jour

> [!NOTE]
> Ce problème a été résolu dans Xamarin Studio 5.1.4 et versions ultérieures. Toutefois, si le problème se produit dans Visual Studio pour Mac, veuillez soumettre un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) avec votre contrôle de version complet intégral et les informations de générer la sortie de journal.

Un bogue dans Xamarin.Studio 5.1 endommagé précédemment fichiers .csproj en partiellement ou totalement supprimant le code xml dans le fichier .csproj. Cela entraînerait important système (par exemple, la mise à jour de l’Android Resource.designer.cs) de génération de parties de l’Android échec. À compter de la 5.1.4 stable libérer le 15 juillet, ce bogue a été résolu ; mais dans de nombreux cas le fichier projet doit être réparée manuellement, comme décrit ci-dessous.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Deux approches possibles pour résoudre le fichier de projet

**Soit :**

1. Créer un tout nouveau projet d’application Xamarin.Android, définir toutes les propriétés de projet pour faire correspondre votre ancien projet et ajouter toutes vos ressources, fichiers sources, etc. dans le projet.

   **OU**

2. Faites une copie de sauvegarde de fichier .csproj du votre projet d’origine, puis ouvrez-le dans un éditeur de texte et ajouter dans les éléments manquants à partir d’un fichier .csproj correctement généré.

### <a name="if-this-does-not-solve-the-problem"></a>Si cela ne résout pas le problème

Après avoir expérimenté avec ces éléments, vous pouvez remarquer qu’après l’ajout d’éléments et régénérer le projet, le fichier Resource.designer.cs voulez-vous mettre à jour, mais ensuite vous pourriez toujours devoir fermez et rouvrez la solution pour obtenir la saisie semi-automatique de code pour reconnaître les nouveaux types contenus dans Resource.designer.cs. 
