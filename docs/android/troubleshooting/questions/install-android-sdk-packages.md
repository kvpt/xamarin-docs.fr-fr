---
title: Quels packages Android SDK installer ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: 24c70c2e869f59091a1519af6d1165dbea9cc467
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291572"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quels packages Android SDK installer ?

L’installation de l’Android SDK n’inclut pas automatiquement tous les paquets minimums requis pour le développement. Bien que les besoins individuels des développeurs varient, les paquets suivants seront généralement nécessaires pour le développement avec Xamarin.Android:

## <a name="tools"></a>Outils

Installez les derniers outils du dossier Tools dans le gestionnaire SDK :

- Outils Android SDK
- Android SDK Platform-Tools
- Android SDK Build-Tools

## <a name="android-platforms"></a>Plate-forme Android(s)

Installez la « plate-forme SDK » pour les versions Android que vous avez définies comme cible & minimale.

Exemples :

- Cible API 23
- API minimum 23

Seulement besoin d’installer la plate-forme SDK pour API 23

- Cible API 23
- API minimum 15

Besoin d’installer des plates-formes SDK pour API 15 et 23. Notez que vous n’avez pas besoin d’installer les niveaux d’API entre le minimum et la cible (même si vous êtes backporting à ces niveaux d’API).

## <a name="system-images"></a>Images du système

Ceux-ci ne sont nécessaires que si vous voulez utiliser les émulateurs Android out-of-the-box de Google. Pour plus d’informations, voir [Android Emulator Setup](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Les Extras Android SDK ne sont généralement pas nécessaires; mais il est utile d’être au courant d’eux car ils peuvent être nécessaires en fonction de votre cas d’utilisation.
