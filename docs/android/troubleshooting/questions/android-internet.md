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
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027007"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Pourquoi la build de ma version Android ne peut pas se connecter à Internet ?

## <a name="cause"></a>Cause :

La cause la plus courante de ce problème est que l’autorisation **Internet** est automatiquement incluse dans une version Debug, mais qu’elle doit être définie manuellement pour une version Release. Cela est dû au fait que l’autorisation Internet est utilisée pour permettre à un débogueur de s’attacher au processus, comme décrit [ici](~/android/deploy-test/building-apps/build-process.md)« DebugSymbols ».

## <a name="fix"></a>Correction

Pour résoudre le problème, vous pouvez avoir besoin de l’autorisation Internet dans le manifeste Android. Pour ce faire, vous pouvez utiliser l’éditeur de manifeste ou le Sourcecode du manifeste :

- Correction dans l’éditeur : dans votre projet Android, accédez à **Propriétés-> fichier AndroidManifest. xml-> autorisations requises** et cochez **Internet**

- Correction dans Sourcecode : Ouvrez le fichier AndroidManifest dans un éditeur de code source et ajoutez la balise d’autorisation à l’intérieur des balises `<Manifest>` :

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
