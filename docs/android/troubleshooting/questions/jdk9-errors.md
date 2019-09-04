---
title: Xamarin. Android et Java Development Kit 9
description: Cet article explique comment résoudre les erreurs du kit de développement Java (JDK) 9 ou versions ultérieures dans Xamarin. Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 3f1f49a5f63d8159644bd2bb247287a91c27023a
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225555"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin. Android et Java Development Kit 9 ou version ultérieure

_Cet article explique comment résoudre les erreurs du kit de développement Java (JDK) 9 ou versions ultérieures dans Xamarin. Android._


## <a name="overview"></a>Présentation

Xamarin. Android utilise le kit de développement Java (JDK) pour s’intégrer au Android SDK pour la création d’applications Android et l’exécution du concepteur Android. Les versions les plus récentes des Android SDK (API 24 et ultérieures) requièrent JDK 8 (1,8) ou la version préliminaire de Microsoft Mobile OpenJDK. **Étant donné que les outils de Android SDK disponibles à partir de Google ne sont pas encore compatibles avec JDK 9, Xamarin. Android ne fonctionne pas avec JDK 9 ou version ultérieure.**

## <a name="jdk-errors"></a>Erreurs JDK

Si vous essayez de générer un projet Xamarin. Android avec une version du JDK ultérieure à JDK 8, vous obtiendrez une erreur explicite indiquant que cette version de JDK n’est pas prise en charge. Par exemple :

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Pour résoudre ces erreurs, vous devez installer JDK 8 (1,8) comme expliqué dans [Comment faire mettre à jour la version du kit de développement Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
Vous pouvez également installer la version [préliminaire de Microsoft Mobile openjdk](~/android/get-started/installation/openjdk.md) Microsoft Mobile openjdk finira par remplacer JDK 8 pour le développement Xamarin. Android.


## <a name="checking-the-jdk-version"></a>Vérification de la version de JDK

Vous pouvez vérifier la version de Java que vous avez installée en entrant la commande suivante (le répertoire JDK `bin` doit être dans votre `PATH`):

```shell
java -version
```

Si JDK 9 est installé, un message semblable au suivant s’affiche:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Si JDK 9 ou version ultérieure est installé, vous devez installer Java JDK 8 (1,8) ou la version préliminaire de Microsoft Mobile OpenJDK. Pour plus d’informations sur l’installation de JDK 8, consultez [Comment faire mettre à jour la version du kit de développement Java (JDK)](~/android/troubleshooting/questions/update-jdk.md). Pour plus d’informations sur l’installation de Microsoft Mobile OpenJDK, consultez la version [préliminaire de Microsoft Mobile openjdk](~/android/get-started/installation/openjdk.md).

Notez que vous n’avez pas besoin de désinstaller une version ultérieure du JDK. Toutefois, vous devez vous assurer que Xamarin utilise JDK 8 plutôt qu’une version ultérieure de JDK. Dans Visual Studio, cliquez sur **outils > Options > les paramètres Xamarin > Android**. Si l' **emplacement du kit de développement Java** n’est pas défini sur un emplacement JDK 8 (par exemple, **\\C: Program Files\\\\Java JDK 1.8.0 _111**), cliquez sur **modifier** et affectez-lui l’emplacement où JDK 8 est installé. Dans Visual Studio pour Mac, accédez à **préférences > projets > emplacements du kit de développement logiciel (SDK) > Android > SDK Java (JDK)** , puis cliquez sur **Parcourir** pour mettre à jour ce chemin d’accès.

## <a name="known-issues-with-jdk-9"></a>Problèmes connus avec JDK 9

### <a name="apksigner"></a>apksigner

Il existe un problème connu avec apksigner et JDK 9 dans lequel le `apksigner.bat` fichier appelle le avec `apksigner.jar` `-Djava.ext.dirs` au lieu de `-classpath` recevoir le JDK 9. Nous vous recommandons d’utiliser JDK 8 (1,8). Pour plus d’informations sur l’installation de JDK 8, consultez [Comment faire mettre à jour la version du kit de développement Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Si vous avez installé JDK 9, assurez-vous que le chemin d’accès suivant `PATH` n’est pas défini sur votre variable d’environnement, car `C:\ProgramData\Oracle\Java\javapath`il doit toujours pointer sur JDK 9:. Après l’avoir supprimée, `java-version` une ligne de commande doit afficher JDK 8.
