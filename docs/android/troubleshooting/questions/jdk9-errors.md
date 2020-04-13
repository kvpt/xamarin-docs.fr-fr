---
title: Kit de développement Xamarin.Android et Java 9
description: Cet article explique comment résoudre Java Development Kit (JDK) 9 ou plus tard des erreurs dans Xamarin.Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 2ea7c9b9f900bc339d183c2f5b317792ebec5232
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026834"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin.Android et Java Development Kit 9 ou plus tard

_Cet article explique comment résoudre Java Development Kit (JDK) 9 ou plus tard des erreurs dans Xamarin.Android._

## <a name="overview"></a>Vue d’ensemble

Xamarin.Android utilise le kit de développement Java (JDK) pour s’intégrer à l’Android SDK pour la construction d’applications Android et l’exécution du concepteur Android. Les dernières versions de l’Android SDK (API 24 et plus) nécessitent JDK 8 (1.8) ou le Microsoft Mobile OpenJDK Preview. **Parce que les outils Android SDK disponibles sur Google ne sont pas encore compatibles avec JDK 9, Xamarin.Android ne fonctionne pas avec JDK 9 ou plus tard.**

## <a name="jdk-errors"></a>Erreurs JDK

Si vous essayez de construire un projet Xamarin.Android avec une version du JDK plus tard que JDK 8, vous obtiendrez une erreur explicite indiquant que cette version de JDK n’est pas pris en charge. Par exemple :

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Pour résoudre ces erreurs, vous devez installer JDK 8 (1.8) comme expliqué dans [Comment puis-je mettre à jour la version Java Development Kit (JDK) ?](~/android/troubleshooting/questions/update-jdk.md).
Alternativement, vous pouvez installer le [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) Le Microsoft Mobile OpenJDK finira par remplacer JDK 8 pour le développement Xamarin.Android.

## <a name="checking-the-jdk-version"></a>Vérification de la version JDK

Vous pouvez vérifier quelle version de Java vous avez installé en `bin` entrant la commande `PATH`suivante (l’annuaire JDK doit être dans votre ):

```shell
java -version
```

Si JDK 9 installé, vous verrez un message comme suit:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Si JDK 9 ou plus tard est installé, vous devez installer Java JDK 8 (1.8) ou le Microsoft Mobile OpenJDK Preview. Pour plus d’informations sur la façon d’installer JDK 8, voir [Comment puis-je mettre à jour la version Java Development Kit (JDK) ?](~/android/troubleshooting/questions/update-jdk.md). Pour plus d’informations sur la façon d’installer le Microsoft Mobile OpenJDK, voir [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md).

Notez que vous n’avez pas à désinstaller une version ultérieure de la JDK; cependant, vous devez vous assurer que Xamarin utilise JDK 8 plutôt qu’une version JDK ultérieure. Dans Visual Studio, cliquez sur **Outils > Options > Xamarin > Paramètres Android**. Si **Java Development Kit Location** n’est pas réglé à un emplacement JDK 8 (comme **C:\\Fichiers de programme\\Java\\jdk1.8.0 -111**), cliquez sur **Change** et définissez-le à l’endroit où JDK 8 est installé. Dans Visual Studio for Mac, naviguez vers **Préférences > Projets > SDK Locations > Android > Java SDK (JDK)** et cliquez sur **Parcourir** pour mettre à jour ce chemin.

## <a name="known-issues-with-jdk-9"></a>Problèmes connus avec JDK 9

### <a name="apksigner"></a>apksigner

Il ya un problème connu avec apksigner et `apksigner.bat` JDK `apksigner.jar` 9 `-classpath` dans lequel le fichier invoque le lieu de `-Djava.ext.dirs` ce que JDK 9 attend. Il est recommandé d’utiliser JDK 8 (1.8). Pour plus d’informations sur la façon d’installer JDK 8, voir [Comment mettre à jour la version Java Development Kit (JDK) ?](~/android/troubleshooting/questions/update-jdk.md)

Si vous avez installé JDK 9, assurez-vous `PATH` que le chemin suivant n’est pas `C:\ProgramData\Oracle\Java\javapath`fixé sur votre variable d’environnement car il sera toujours point à JDK 9: . Après l’avoir `java-version` enlevé, à une ligne de commande doit montrer JDK 8.
