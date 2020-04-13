---
title: Mon fichier Android Resource.designer.cs ne se met pas à jour
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/19/2017
ms.openlocfilehash: 4683bbaa5aa48c7b5de5fb9a87a4cd3fbc0aeada
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019512"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Mon fichier Android Resource.designer.cs ne se met pas à jour

> [!NOTE]
> Ce problème a été résolu dans Xamarin Studio 5.1.4 et les versions ultérieures. Toutefois, si le problème se produit dans Visual Studio pour Mac, veuillez déposer un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) avec vos informations de version complète et la sortie complète du journal de construction.

Un bug dans Xamarin.Studio 5.1 fichiers précédemment corrompus .csproj en supprimant partiellement ou complètement le code xml dans le fichier .csproj. Cela entraînerait l’échec d’importantes parties du système de construction Android (comme la mise à jour de l’Resource.designer.cs Android). Depuis la sortie stable de 5.1.4 le 15 juillet, ce bogue a été corrigé; mais dans de nombreux cas, le dossier du projet doit être réparé manuellement, tel que décrit ci-dessous.

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Deux approches possibles pour fixer le dossier du projet

**Soit:**

1. Créez un tout nouveau projet d’application Xamarin.Android, définissez toutes les propriétés du projet pour correspondre à votre ancien projet, et ajoutez toutes vos ressources, fichiers sources, etc. de nouveau dans le projet.

   **OR**

2. Faites une copie de sauvegarde du fichier .csproj de votre projet d’origine, puis ouvrez-le dans un éditeur de texte, et ajoutez en arrière dans les éléments manquants à partir d’un fichier .csproj proprement généré.

### <a name="if-this-does-not-solve-the-problem"></a>Si cela ne résout pas le problème

Après avoir expérimenté avec ces éléments, vous pouvez remarquer qu’après avoir rajié les éléments et la reconstruction du projet, le fichier Resource.designer.cs serait mise à jour, mais alors vous pourriez encore avoir à fermer et rouvrir la solution pour obtenir l’achèvement du code pour reconnaître les nouveaux types contenus dans Resource.designer.cs. 
