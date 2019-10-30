---
title: Forum aux questions sur Xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 35df724850e1fc945c096aebc91b7aa84936bdc1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026976"
---
# <a name="android-frequently-asked-questions"></a>Forum aux questions sur Android

## <a name="installation--setup"></a>Installation du & d’installation

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Quels packages Android SDK dois-je installer ?](install-android-sdk-packages.md)

L’installation du Android SDK n’inclut pas automatiquement tous les packages minimaux requis pour le développement. Bien que les besoins individuels des développeurs varient, ce guide aborde les packages qui sont généralement requis pour le développement avec Xamarin. Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Où puis-je définir les emplacements de mon Kit SDK Android ?](android-sdk-location.md)

Ce guide décrit à la fois les paramètres par défaut de la Android SDK, qui doivent fonctionner pour la plupart des configurations. et comment modifier ces valeurs par défaut dans Visual Studio pour Mac ou Visual Studio si nécessaire.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Comment mettre à jour la version du Kit JDK Java ?](update-jdk.md)

Cet article explique comment mettre à jour la version du kit de développement Java (JDK) sur Windows et Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[Puis-je utiliser le kit de développement Java (JDK) version 9 ou ultérieure ?](jdk9-errors.md)

Xamarin. Android requiert JDK 8 ou Microsoft Mobile OpenJDK. Cet article répertorie certains messages d’erreur courants que vous pouvez voir si JDK 9 ou version ultérieure est installé, ainsi que des instructions pour vérifier la version de JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Comment puis-je installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?](install-android-support-library.md)

Ce guide fournit des exemples d’étapes pour installer la bibliothèque de prise en charge `Xamarin.Android.Support.v4` sur Windows & Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[De quels pilotes USB ai-je besoin pour déboguer Android sur Windows ?](android-drivers-debug-windows.md)

Pour déboguer sur un appareil Android lors du développement dans Windows ; vous devez installer un pilote USB compatible. Le gestionnaire de Android SDK inclut par défaut le « pilote USB Google », qui ajoute la prise en charge des appareils de câble.
D’autres appareils nécessitent des pilotes USB publiés par le fabricant de l’appareil. Ce guide fournit des informations sur la recherche de ces pilotes, ainsi que sur les autres méthodes de test.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[Est-il possible de se connecter à des émulateurs Android exécutés sur un Mac à partir d’une machine virtuelle Windows ?](connect-android-emulator-mac-windows.md)

Ce guide aborde les méthodes d’utilisation de l’émulateur Android.

## <a name="general-questions"></a>Questions générales

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Comment automatiser un projet de test Android NUnit ?](automate-android-nunit-test.md)

Ce guide décrit les étapes de configuration d’un projet de test Android NUnit, et _non_ un projet Xamarin. UITest. Vous trouverez les guides Xamarin. UITest [ici](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Pourquoi ma version mise en production Android ne peut-elle pas se connecter à Internet ?](android-internet.md)

La cause la plus courante de ce problème est que l’autorisation **Internet** est automatiquement incluse dans une version Debug, mais qu’elle doit être définie manuellement pour une version Release. Ce guide décrit comment activer l’autorisation sur les versions release.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Packages NuGet v4 / v13 de prise en charge Xamarin Android plus intelligents](android-support-v4v13-libraries.md)

`Support-v4` et `Support-v13` ne peuvent pas être utilisés ensemble dans la même application, autrement dit, ils s’excluent mutuellement. Cela est dû au fait que `Support-v13` contient en fait tous les types et l’implémentation de `Support-v4`. Si vous essayez de faire référence à la fois dans le même projet, vous rencontrerez des erreurs de type en double.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Comment faire résoudre une erreur PathTooLongException ?](path-too-long-exception.md)

Cet article explique comment résoudre une erreur **PathTooLongException** qui peut se produire lors de la génération d’un projet Xamarin. Android.

## <a name="deprecated"></a>Déconseillé

> [!NOTE]
> Les articles ci-dessous s’appliquent aux problèmes qui ont été résolus dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?](xa-lollipop.md)

Ce guide a été écrit à l’origine pour Android L preview. Xamarin. Android 4,17 a ajouté la prise en charge d’Android L Preview & Xamarin. Android 4,20 a ajouté la prise en charge d’interface Lollipop Android.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - Aucune ressource trouvée correspondant au nom donné : attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Cette erreur peut se produire dans les versions antérieures de Xamarin si certains packages de Android SDK requis sont manquants.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Réglage des paramètres de mémoire Java pour le concepteur Android](android-designer-java-memory.md)

Les paramètres de mémoire par défaut utilisés lors du démarrage du processus de `java` pour le concepteur Android peuvent être incompatibles avec certaines configurations système. À compter de Xamarin Studio 5.7.2.7 et Xamarin pour Visual Studio 3.9.344 ces paramètres peuvent être personnalisés pour chaque projet.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Mon fichier Android Resource.designer.cs ne se met pas à jour](resource-designer-wont-update.md)

Un bogue dans Xamarin. Studio 5,1 A précédemment endommagé des fichiers. csproj en supprimant partiellement ou complètement le code XML dans le fichier. csproj. Cela entraînerait l’échec des parties importantes du système de génération Android (telles que la mise à jour du Resource.designer.cs Android). À compter de la publication stable de 5.1.4 le 15 juillet, ce bogue a été corrigé. Toutefois, dans de nombreux cas, le fichier projet doit être réparé manuellement, comme décrit dans ce guide.
