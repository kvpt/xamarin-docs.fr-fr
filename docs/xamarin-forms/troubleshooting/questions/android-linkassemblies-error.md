---
title: 'Erreur de build Android: échec inattendu de la tâche LinkAssemblies'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: 71305dd7287df56036d0298ebfcf8a8cb7c4d3b3
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528376"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Erreur de build Android: échec inattendu de la tâche LinkAssemblies

Vous pouvez voir un message `The "LinkAssemblies" task failed unexpectedly` d’erreur lors de la création d’un projet Xamarin. Android qui utilise des formulaires. Cela se produit lorsque l’éditeur de liens est actif (généralement sur une version *Release* pour réduire la taille du package d’application); Cela se produit parce que les cibles Android ne sont pas mises à jour avec le Framework le plus récent. (Plus d’informations: [Xamarin. Forms pour les conditions requises pour Android](~/get-started/requirements.md#android))

Pour résoudre ce problème, assurez-vous de disposer des dernières versions de Android SDK prises en charge et définissez la version **cible de .NET Framework** sur la dernière plateforme installée. Il est également recommandé de définir la **version Android cible** sur la dernière plateforme installée, et la **version Android minimale** sur API 19 ou supérieure. Cela est considéré comme une configuration prise en charge.

## <a name="setting-in-visual-studio-for-mac"></a>Définir dans Visual Studio pour Mac

1. Cliquez avec le bouton droit sur le projet Android, puis sélectionnez **options** dans le menu.
2. Dans la boîte de dialogue **Options du projet** , accédez à **Générer > général**.
3. Définir la **compilation avec la version Android: (Version cible du .NET Framework)**  sur la dernière plateforme installée.
4. Dans la boîte de dialogue **Options du projet** , accédez à **Générer > application Android**.
5. Définissez la **version d’Android minimale** sur l’API de niveau 19 ou version ultérieure, et la **version Android cible** sur la dernière plateforme installée que vous avez choisie dans (3).

## <a name="setting-in-visual-studio"></a>Définir dans Visual Studio

1. Cliquez avec le bouton droit sur le projet Android , puis sélectionnez corrections dans le menu.
2. Dans les propriétés du projet, accédez à **application**.
3. Définir la **compilation avec la version Android: (Version cible du .NET Framework)**  sur la dernière plateforme installée.
4. Dans les propriétés du projet, accédez à **Android manifest**.
5. Définissez la **version d’Android minimale** sur l’API de niveau 19 ou version ultérieure, et la **version Android cible** sur la dernière plateforme installée que vous avez choisie dans (3).

Une fois que vous avez mis à jour ces paramètres, nettoyez et régénérez votre projet pour vous assurer que vos modifications sont sélectionnées.
