---
title: Cibles de génération
description: Ce document répertorie toutes les cibles prises en charge dans le processus de génération Xamarin. Android.
ms.prod: xamarin
ms.assetid: 17DE89FF-F316-4620-B865-EF6E0963A29C
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/17/2020
ms.openlocfilehash: 4482e6c4bfe2a6952d59d896b7c79ca82432b42b
ms.sourcegitcommit: 38496cfd4d30fd40a011011f303a31de639bd699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91247266"
---
# <a name="build-targets"></a>Cibles de génération

Les cibles de génération suivantes sont définies pour les projets Xamarin. Android.

## <a name="build"></a>Build

Génère le code source dans un projet et toutes les dépendances.

Cette cible *ne crée pas* de package Android ( `.apk` fichier).
Pour créer un package Android, utilisez la cible [SignAndroidPackage](#signandroidpackage) *ou* affectez la valeur true à la propriété [$ (AndroidBuildApplicationPackage)](~/android/deploy-test/building-apps/build-properties.md#androidbuildapplicationpackage) lors de la génération :

```shell
msbuild /p:AndroidBuildApplicationPackage=True App.sln
```

## <a name="buildandstartaotprofiling"></a>BuildAndStartAotProfiling

Génère l’application avec un profileur AOA incorporé, définit le port TCP du profileur sur [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport) et démarre l’activité par défaut.

Le port TCP par défaut est `9999` .

Ajouté dans Xamarin. Android 10,2.

## <a name="clean"></a>Clean

Supprime tous les fichiers générés par le processus de génération.

## <a name="finishaotprofiling"></a>FinishAotProfiling

Doit être appelé *après* la cible [BuildAndStartAotProfiling](#buildandstartaotprofiling) .

Collecte les données du profileur AOA à partir de l’appareil ou de l’émulateur via le port TCP [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport)
et les écrit dans [`$(AndroidAotCustomProfilePath)`](~/android/deploy-test/building-apps/build-properties.md#androidaotcustomprofilepath) .

Les valeurs par défaut pour les profils de port et personnalisé sont `9999` et `custom.aprof` .

Pour passer des options supplémentaires à `aprofutil` , définissez-les dans la [`$(AProfUtilExtraOptions)`](~/android/deploy-test/building-apps/build-properties.md#aprofutilextraoptions)
.

Ceci équivaut à :

```shell
aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
```

Ajouté dans Xamarin. Android 10,2.

## <a name="install"></a>Installer

[Crée, signe](#signandroidpackage)et installe le package Android sur l’appareil par défaut ou l’appareil virtuel.

La [`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) propriété spécifie l’appareil cible Android sur lequel le package Android peut être installé ou supprimé.

```bash
# Install package onto emulator via -e
# Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
```

## <a name="signandroidpackage"></a>SignAndroidPackage

Crée et signe le fichier de package Android ( `.apk` ).

À utiliser avec `/p:Configuration=Release` pour générer des packages « Release » autonomes.

## <a name="startandroidactivity"></a>StartAndroidActivity

Démarre l’activité par défaut sur l’appareil ou l’émulateur en cours d’exécution.

Pour démarrer une autre activité, définissez la valeur [`$(AndroidLaunchActivity)`](~/android/deploy-test/building-apps/build-properties.md#androidlaunchactivity)
pour le nom de l’activité.

Ceci équivaut à :

```shell
adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)
```

Ajouté dans Xamarin. Android 10,2.

## <a name="stopandroidpackage"></a>StopAndroidPackage

Arrête complètement le package d’application sur l’appareil ou l’émulateur en cours d’exécution.

Ceci équivaut à :

```shell
adb shell am force-stop @PACKAGE_NAME@
```

Ajouté dans Xamarin. Android 10,2.

## <a name="uninstall"></a>Désinstaller l’interface

Désinstalle le package Android de l’appareil par défaut ou de l’appareil virtuel.

La [`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) propriété spécifie l’appareil cible Android sur lequel le package Android peut être installé ou supprimé.

## <a name="updateandroidresources"></a>UpdateAndroidResources

Met à jour le `Resource.designer.cs` fichier.

Cette cible est généralement appelée par l’IDE quand de nouvelles ressources sont ajoutées au projet.
