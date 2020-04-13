---
title: Inscription d’une empreinte digitale
description: Comment configurer un verrouillage d’écran et inscrire une empreinte digitale sur un appareil Android ou un émulateur.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027591"
---
# <a name="enrolling-a-fingerprint"></a>Inscription d’une empreinte digitale

## <a name="enrolling-a-fingerprint-overview"></a>Inscription d’une vue d’ensemble des empreintes digitales

Il est seulement possible pour une application Android de tirer parti de l’authentification des empreintes digitales si l’appareil a déjà été configuré avec l’authentification des empreintes digitales. Ce guide discutera de la façon d’inscrire une empreinte digitale sur un appareil Android ou un émulateur. Les émulateurs n’ont pas le matériel réel pour effectuer un balayage d’empreintes digitales, mais il est possible de simuler une analyse d’empreintes digitales à l’aide du pont Android Debug (décrit ci-dessous).  Ce guide discutera de la façon d’activer le verrouillage de l’écran sur un appareil Android et d’inscrire une empreinte digitale pour l’authentification.

## <a name="requirements"></a>Spécifications

Pour inscrire une empreinte digitale, vous devez avoir un appareil Android ou un émulateur exécutant le niveau 23 de l’API (Android 6.0).

L’utilisation de l’Android Debug Bridge (ADB) nécessite **une** familiarité avec l’invite de commande, et la bad exécutable doit être dans le PATH de votre Bash, PowerShell, ou Command Prompt environnement.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurer un verrouillage d’écran et inscrire une empreinte digitale 

Pour configurer un verrou d’écran, effectuez les étapes suivantes :

1. Accédez à **Paramètres > Sécurité**, et sélectionnez verrouillage **d’écran**:

    ![Emplacement de la sélection de verrouillage d’écran sur l’écran de sécurité](enrolling-fingerprint-images/testing-01.png)

2. L’écran suivant qui apparaît vous permettra de sélectionner et de configurer l’une des méthodes de sécurité de verrouillage de verrouillage d’écran : 

    ![Sélectionnez Swipe, Pattern, PIN ou Password](enrolling-fingerprint-images/testing-02.png)

   Sélectionnez et complétez l’une des méthodes de verrouillage d’écran disponibles.

3. Une fois que l’écran est configuré, retournez à la page **Paramètres > Sécurité** et sélectionnez **Fingerprint**:

    ![Emplacement de la sélection d’empreintes digitales sur l’écran de sécurité](enrolling-fingerprint-images/testing-03.png)

4. De là, suivez la séquence pour ajouter une empreinte digitale à l’appareil :

    [![Séquence de captures d’écran pour l’ajout d’une empreinte digitale à l’appareil](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Dans l’écran final, vous êtes invité à placer votre doigt sur le scanner d’empreintes digitales: 

    ![Écran qui vous invite à mettre le doigt sur le capteur](enrolling-fingerprint-images/testing-05.png)

    Si vous utilisez un appareil Android, complétez le processus en touchant un doigt au scanner. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simuler une analyse d’empreintes digitales sur l’émulateur

Sur un émulateur Android, il est possible de simuler un balayage des empreintes digitales en utilisant le pont Android Debug. Sur OS X, démarrez une session Terminal tandis que sur `adb`Windows démarrez une invite de commande ou une session Powershell et exécutez :

```shell
$ adb -e emu finger touch 1
```

La valeur de **1** est l’id du _doigt\__ pour le doigt qui a été "scanné". Il s’agit d’un intégré unique que vous attribuez pour chaque empreinte digitale virtuelle. À l’avenir, lorsque l’application est en cours d’exécution, vous pouvez exécuter cette même `adb` commande de la BAD chaque fois que l’émulateur vous invite pour une empreinte digitale, vous pouvez exécuter la commande et lui passer l’id _du doigt\__ pour simuler l’analyse de l’empreinte digitale.

Une fois l’analyse des empreintes digitales terminée, Android vous informera que l’empreinte digitale a été ajoutée :  

![Écran affichant l’empreinte digitale ajoutée!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Récapitulatif 

Ce guide a couvert la façon de configurer un verrouillage d’écran et d’inscrire une empreinte digitale sur un appareil Android ou dans un émulateur Android. 
