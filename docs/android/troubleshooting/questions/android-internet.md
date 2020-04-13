---
title: Pourquoi la build de ma version Android ne peut pas se connecter à Internet ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 5996cfa3c0a18fc186ea862a2b3d7910594e1281
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027007"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Pourquoi la build de ma version Android ne peut pas se connecter à Internet ?

## <a name="cause"></a>Cause

La cause la plus fréquente de ce problème est que l’autorisation **INTERNET** est automatiquement incluse dans une version de débogé, mais doit être réglée manuellement pour une version. C’est parce que l’autorisation d’Internet est utilisée pour permettre à un débbuggeur de s’attacher au processus, tel que décrit pour "DebugSymbols" [ici](~/android/deploy-test/building-apps/build-process.md).

## <a name="fix"></a>Fix

Pour résoudre le problème, vous pouvez exiger la permission d’Internet dans le manifeste Android. Cela peut être fait soit par l’éditeur manifeste ou le code source du manifeste:

- Correction dans l’éditeur: Dans votre projet Android, aller à **Properties -> AndroidManifest.xml -> autorisations requises** et vérifier **Internet**

- Fixez-vous dans Sourcecode: Ouvrez l’AndroidManifest dans `<Manifest>` un éditeur source et ajoutez l’étiquette d’autorisation à l’intérieur des balises:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
