---
title: Modification des propriétés d’appareil virtuel Android
description: Cet article explique comment utiliser Android Device Manager pour modifier les propriétés de profil d’un appareil virtuel Android.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 3E33C136-8042-4184-A40C-3200D8CD99CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: ab4e0e79c39d36adcfb29e9659c2c6a924c80470
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020872"
---
# <a name="editing-android-virtual-device-properties"></a>Modification des propriétés d’appareil virtuel Android

_Cet article explique comment utiliser Android Device Manager pour modifier les propriétés de profil d’un appareil virtuel Android._

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Android Device Manager sur Windows

**Android Device Manager** prend en charge la modification des propriétés de profil d’un appareil virtuel Android. Les écrans **Nouvel appareil** et **Modifier l’appareil** répertorient les propriétés de l’appareil virtuel dans la première colonne et les valeurs correspondantes de chaque propriété dans la seconde colonne (comme illustré dans cet exemple) : 

[![Exemple Nouvel écran d’appareil](device-properties-images/win/01-new-device-editor-sml.png)](device-properties-images/win/01-new-device-editor.png#lightbox)

Lorsque vous sélectionnez une propriété, une description détaillée de cette propriété s’affiche à droite. Vous pouvez modifier les *propriétés de profil matériel* et les * propriétés d’AVD*. Les propriétés de profil matériel (comme `hw.ramSize` et `hw.accelerometer`) décrivent les caractéristiques physiques de l’appareil émulé. Ces caractéristiques comprennent la taille de l’écran, la quantité de mémoire vive disponible et la présence ou non d’un accéléromètre. Les propriétés d’AVD spécifient le fonctionnement de l’AVD lorsqu’il s’exécute. Par exemple, les propriétés d’AVD peuvent être configurées pour spécifier la manière dont l’AVD utilise la carte graphique de votre ordinateur de développement pour le rendu.

Vous pouvez modifier les propriétés en suivant les instructions ci-dessous :

- Pour changer une propriété booléenne, cliquez sur la coche à droite de la propriété booléenne :

    ![Modification d’une propriété booléenne](device-properties-images/win/02-boolean-value.png)

- Pour changer une propriété *enum* (avec liste de choix), cliquez sur la flèche vers le bas à droite de la propriété et choisissez une nouvelle valeur.

    ![Modification d’une propriété enum](device-properties-images/win/04-enum-value.png)

- Pour changer une propriété de chaîne ou d’entier, double-cliquez sur le paramètre de chaîne ou d’entier actuel dans la colonne de valeur et entrez une nouvelle valeur.

    ![Modification d’une propriété d’entier](device-properties-images/win/03-integer-value.png)

::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>Android Device Manager sur macOS

**Android Device Manager** prend en charge la modification des propriétés de profil d’un appareil virtuel Android. Les écrans **Nouvel appareil** et **Modifier l’appareil** répertorient les propriétés de l’appareil virtuel dans la première colonne et les valeurs correspondantes de chaque propriété dans la seconde colonne (comme illustré dans cet exemple) : 

[![Exemple Nouvel écran d’appareil](device-properties-images/mac/01-new-device-editor-sml.png)](device-properties-images/mac/01-new-device-editor.png#lightbox)

Lorsque vous sélectionnez une propriété, une description détaillée de cette propriété s’affiche à droite. Vous pouvez modifier les *propriétés de profil matériel* et les * propriétés d’AVD*. Les propriétés de profil matériel (comme `hw.ramSize` et `hw.accelerometer`) décrivent les caractéristiques physiques de l’appareil émulé. Ces caractéristiques comprennent la taille de l’écran, la quantité de mémoire vive disponible et la présence ou non d’un accéléromètre. Les propriétés d’AVD spécifient le fonctionnement de l’AVD lorsqu’il s’exécute. Par exemple, les propriétés d’AVD peuvent être configurées pour spécifier la manière dont l’AVD utilise la carte graphique de votre ordinateur de développement pour le rendu.

Vous pouvez modifier les propriétés en suivant les instructions ci-dessous :

- Pour changer une propriété booléenne, cliquez sur la coche à droite de la propriété booléenne :

    ![Modification d’une propriété booléenne](device-properties-images/mac/02-boolean-value.png)

- Pour changer une propriété *enum* (avec liste de choix), cliquez sur le menu déroulant à droite de la propriété et choisissez une nouvelle valeur.

    ![Modification d’une propriété enum](device-properties-images/mac/04-enum-value.png)

- Pour changer une propriété de chaîne ou d’entier, double-cliquez sur le paramètre de chaîne ou d’entier actuel dans la colonne de valeur et entrez une nouvelle valeur.

    ![Modification d’une propriété d’entier](device-properties-images/mac/03-integer-value.png)

::: zone-end

Le tableau suivant présente en détail les propriétés répertoriées dans les écrans **Nouvel appareil** et **Éditeur d’appareil** :

[!include[](~/android/includes/emulator-properties.md)]

Pour plus d’informations sur ces propriétés, consultez [Propriétés de profil matériel](https://developer.android.com/studio/run/managing-avds.html#hpproperties).
