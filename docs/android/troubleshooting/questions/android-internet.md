---
title: Pourquoi la build de ma version Android ne peut pas se connecter à Internet ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 162378c00f3e20574d04dc373fcc492a9407b88d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761027"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Pourquoi la build de ma version Android ne peut pas se connecter à Internet ?

## <a name="cause"></a>Cause

La cause la plus courante de ce problème est que l’autorisation **Internet** est automatiquement incluse dans une version Debug, mais qu’elle doit être définie manuellement pour une version Release. Cela est dû au fait que l’autorisation Internet est utilisée pour permettre à un débogueur de s’attacher au processus, comme décrit [ici](~/android/deploy-test/building-apps/build-process.md)« DebugSymbols ».

## <a name="fix"></a>Corriger

Pour résoudre le problème, vous pouvez avoir besoin de l’autorisation Internet dans le manifeste Android. Pour ce faire, vous pouvez utiliser l’éditeur de manifeste ou le Sourcecode du manifeste :

- Corriger dans l’éditeur : Dans votre projet Android, accédez à **Propriétés-> fichier AndroidManifest. xml-> autorisations requises** et cochez **Internet**

- Correction dans Sourcecode : Ouvrez le fichier AndroidManifest dans un éditeur de code source et ajoutez la balise `<Manifest>` d’autorisation à l’intérieur des balises :

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
