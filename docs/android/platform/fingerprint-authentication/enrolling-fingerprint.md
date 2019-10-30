---
title: Inscription d’une empreinte digitale
description: Comment configurer un verrouillage d’écran et inscrire une empreinte digitale sur un appareil ou un émulateur Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027591"
---
# <a name="enrolling-a-fingerprint"></a>Inscription d’une empreinte digitale

## <a name="enrolling-a-fingerprint-overview"></a>Présentation de l’inscription d’une empreinte digitale

Il est uniquement possible pour une application Android de tirer parti de l’authentification par empreinte digitale si l’appareil a déjà été configuré avec l’authentification par empreinte digitale. Ce guide explique comment inscrire une empreinte digitale sur un appareil ou un émulateur Android. Les émulateurs n’ont pas le matériel réel pour effectuer une analyse d’empreintes digitales, mais il est possible de simuler une analyse d’empreintes digitales avec l’aide de la Android Debug Bridge (décrite ci-dessous).  Ce guide explique comment activer le verrouillage d’écran sur un appareil Android et inscrire une empreinte digitale pour l’authentification.

## <a name="requirements"></a>spécifications

Pour inscrire une empreinte digitale, vous devez avoir un appareil Android ou un émulateur exécutant l’API de niveau 23 (Android 6,0).

L’utilisation du Android Debug Bridge (ADB) requiert une connaissance de l’invite de commandes et l’exécutable **ADB** doit se trouver dans le chemin d’accès de votre environnement bash, PowerShell ou d’invite de commandes.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configuration d’un verrouillage d’écran et inscription d’une empreinte digitale 

Pour configurer un verrouillage d’écran, procédez comme suit :

1. Accédez à **paramètres > sécurité**, puis sélectionnez **verrouillage**de l’écran :

    ![Emplacement de la sélection du verrouillage de l’écran sur l’écran de sécurité](enrolling-fingerprint-images/testing-01.png)

2. L’écran suivant qui s’affiche vous permet de sélectionner et de configurer l’une des méthodes de sécurité de verrouillage d’écran : 

    ![Sélectionner le balayage, le modèle, le code confidentiel ou le mot de passe](enrolling-fingerprint-images/testing-02.png)

   Sélectionnez et effectuez l’une des méthodes de verrouillage d’écran disponibles.

3. Une fois le screenlock configuré, revenez à la page **paramètres > sécurité** et sélectionnez **empreinte digitale**:

    ![Emplacement de la sélection par empreinte digitale sur l’écran sécurité](enrolling-fingerprint-images/testing-03.png)

4. À partir de là, suivez la séquence pour ajouter une empreinte digitale à l’appareil :

    [![séquence de captures d’écran pour l’ajout d’une empreinte digitale à l’appareil](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Dans le dernier écran, vous êtes invité à placer votre doigt sur le scanneur d’empreintes digitales : 

    ![Écran qui vous invite à placer votre doigt sur le capteur](enrolling-fingerprint-images/testing-05.png)

    Si vous utilisez un appareil Android, terminez le processus en touchant un doigt au scanneur. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulation d’une analyse d’empreintes digitales sur l’émulateur

Sur un émulateur Android, il est possible de simuler une analyse d’empreintes digitales à l’aide de l’Android Debug Bridge. Sur OS X, démarrez une session Terminal Server alors que sur Windows, démarrez une invite de commandes ou une session PowerShell et exécutez `adb`:

```shell
$ adb -e emu finger touch 1
```

La valeur **1** est l' _ID de\_Finger_ pour le doigt qui a été « numérisé ». Il s’agit d’un entier unique que vous attribuez à chaque empreinte digitale virtuelle. À l’avenir, lorsque l’application s’exécute, vous pouvez exécuter cette même commande ADB chaque fois que l’émulateur vous invite à entrer une empreinte digitale, vous pouvez exécuter la commande `adb` et lui transmettre l' _ID de\_Finger_ pour simuler l’analyse par empreinte digitale.

Une fois l’analyse d’empreintes digitales terminée, Android vous avertit que l’empreinte digitale a été ajoutée :  

![Écran affichant l’empreinte digitale ajoutée !](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Récapitulatif 

Ce guide a abordé la configuration d’un verrouillage d’écran et l’inscription d’une empreinte digitale sur un appareil Android ou dans un émulateur Android. 
