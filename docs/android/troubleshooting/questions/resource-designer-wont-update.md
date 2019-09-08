---
title: Mon fichier Android Resource.designer.cs ne se met pas à jour
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: c175c533e437736849eac6be1f4a90a783123812
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757148"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Mon fichier Android Resource.designer.cs ne se met pas à jour

> [!NOTE]
> Ce problème a été résolu dans Xamarin Studio 5.1.4 et versions ultérieures. Toutefois, si le problème se produit dans Visual Studio pour Mac, veuillez envoyer un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.

Un bogue dans Xamarin. Studio 5,1 A précédemment endommagé des fichiers. csproj en supprimant partiellement ou complètement le code XML dans le fichier. csproj. Cela entraînerait l’échec des parties importantes du système de génération Android (telles que la mise à jour du Resource.designer.cs Android). À compter de la publication stable de 5.1.4 le 15 juillet, ce bogue a été corrigé. Toutefois, dans de nombreux cas, le fichier projet doit être réparé manuellement, comme décrit ci-dessous.

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Deux approches possibles pour corriger le fichier projet

**Mode**

1. Créez un nouveau projet d’application Xamarin. Android, définissez toutes les propriétés du projet pour qu’elles correspondent à votre ancien projet et ajoutez toutes vos ressources, fichiers sources, etc. dans le projet.

   **OU**

2. Effectuez une copie de sauvegarde du fichier. csproj du projet d’origine, ouvrez-le dans un éditeur de texte, puis rajoutez les éléments manquants à partir d’un fichier. csproj correctement généré.

### <a name="if-this-does-not-solve-the-problem"></a>Si cela ne résout pas le problème

Après avoir expérimenté ces éléments, vous pouvez remarquer qu’après avoir ajouté les éléments et reconstruit le projet, le fichier Resource.designer.cs est mis à jour, mais vous devrez peut-être encore fermer et rouvrir la solution pour que le code se termine. nouveaux types contenus dans Resource.designer.cs. 
