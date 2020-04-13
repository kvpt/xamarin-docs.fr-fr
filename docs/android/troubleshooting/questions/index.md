---
title: Xamarin.Android Questions fréquemment posées
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 28b13951a0d681ffdb8e643667e496e0b3606628
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291972"
---
# <a name="android-frequently-asked-questions"></a>Questions fréquemment posées par Android

## <a name="installation--setup"></a>Installation & Configuration

### <a name="which-android-sdk-packages-should-i-install"></a>[Quels packages Android SDK installer ?](install-android-sdk-packages.md)

L’installation de l’Android SDK n’inclut pas automatiquement tous les paquets minimums requis pour le développement. Bien que les besoins individuels des développeurs varient, ce guide discute des paquets qui seront généralement nécessaires pour le développement avec Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locations"></a>[Où définir les emplacements de mon kit Android SDK ?](android-sdk-location.md)

Ce guide décrit à la fois les paramètres par défaut de l’Android SDK, qui devrait fonctionner pour la plupart des configurations; et comment modifier ces défauts dans Visual Studio pour Mac ou Visual Studio si nécessaire.

### <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>[Comment mettre à jour la version du kit JDK Java ?](update-jdk.md)

Cet article illustre comment mettre à jour la version Java Development Kit (JDK) sur Windows et Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-later"></a>[Puis-je utiliser le kit JDK Java 9 ou version ultérieure ?](jdk9-errors.md)

Xamarin.Android nécessite JDK 8 ou le Microsoft Mobile OpenJDK. Cet article répertorie certains messages d’erreur courants que vous pouvez voir si JDK 9 ou plus tard est installé, ainsi que des instructions pour vérifier la version JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>[Comment installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?](install-android-support-library.md)

Ce guide fournit des exemples d’étapes pour installer la `Xamarin.Android.Support.v4` bibliothèque de support sur Windows & Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>[Quels pilotes USB sont nécessaires pour déboguer Android sur Windows ?](android-drivers-debug-windows.md)

Débogé d’un appareil Android lors du développement de Windows; vous devez installer un pilote USB compatible. Le gestionnaire Android SDK inclut par défaut le "Google USB Driver", ce qui ajoute une prise en charge des appareils Nexus.
D’autres appareils nécessitent des pilotes USB publiés par le fabricant de l’appareil. Ce guide fournit de l’information sur la recherche de ces pilotes ainsi que d’autres méthodes d’essai.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>[Est-il possible de se connecter à des émulateurs Android exécutés sur un Mac à partir d’une machine virtuelle Windows ?](connect-android-emulator-mac-windows.md)

Ce guide couvre les méthodes lors de l’utilisation de l’émulateur Android.

## <a name="general-questions"></a>Questions générales

### <a name="how-do-i-automate-an-android-nunit-test-project"></a>[Comment automatiser un projet de test Android NUnit ?](automate-android-nunit-test.md)

Ce guide couvre les étapes pour la mise en place d’un projet de test Android NUnit, _et non_ un projet Xamarin.UITest. Xamarin.UITest guides peuvent être trouvés [ici](/appcenter/test-cloud/preparing-for-upload).

### <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>[Pourquoi la build de ma version Android ne peut pas se connecter à Internet ?](android-internet.md)

La cause la plus fréquente de ce problème est que l’autorisation **INTERNET** est automatiquement incluse dans une version de débogé, mais doit être réglée manuellement pour une version. Ce guide décrit comment permettre la permission sur la version construit.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>[Packages NuGet v4 / v13 de prise en charge Xamarin Android plus intelligents](android-support-v4v13-libraries.md)

`Support-v4`et `Support-v13` ne peut pas être utilisé ensemble dans la même application, c’est-à-dire, ils sont mutuellement exclusifs. C’est `Support-v13` parce que contient réellement `Support-v4`tous les types et la mise en œuvre de . Si vous essayez de faire référence à la fois dans le même projet, vous rencontrerez des erreurs de type en double.

### <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>[Comment résoudre une erreur PathTooLongException ?](path-too-long-exception.md)

Cet article explique comment résoudre une erreur **PathTooLongException** qui peut se produire lors de la construction d’un projet Xamarin.Android.

## <a name="deprecated"></a>Déprécié

> [!NOTE]
> Les articles ci-dessous s’appliquent aux questions qui ont été résolues dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la dernière version du logiciel, veuillez déposer un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) avec vos informations de version complète et la sortie complète du journal de construction.

### <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>[Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?](xa-lollipop.md)

Ce guide a été écrit à l’origine pour l’aperçu Android L. Xamarin.Android 4.17 a ajouté Android L Preview Support & Xamarin.Android 4.20 ajouté Android Lollipop Support.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>[Android.Support.v7.AppCompat - Aucune ressource trouvée correspondant au nom donné : attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Cette erreur peut se produire dans les anciennes versions de Xamarin si certains des paquets Android SDK requis sont manquants.

### <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>[Réglage des paramètres de mémoire Java pour le concepteur Android](android-designer-java-memory.md)

Les paramètres de mémoire par défaut `java` qui sont utilisés lors du démarrage du processus pour le concepteur Android peuvent être incompatibles avec certaines configurations du système. En commençant par Xamarin Studio 5.7.2.7 et Xamarin pour Visual Studio 3.9.344 ces paramètres peuvent être personnalisés sur une base par projet.

### <a name="my-android-resourcedesignercs-file-will-not-update"></a>[Mon fichier Android Resource.designer.cs ne se met pas à jour](resource-designer-wont-update.md)

Un bug dans Xamarin.Studio 5.1 fichiers précédemment corrompus .csproj en supprimant partiellement ou complètement le code xml dans le fichier .csproj. Cela entraînerait l’échec d’importantes parties du système de construction Android (comme la mise à jour de l’Resource.designer.cs Android). Depuis la sortie stable de 5.1.4 le 15 juillet, ce bogue a été corrigé; mais dans de nombreux cas, le dossier du projet doit être réparé manuellement, tel que décrit dans ce guide.
