---
title: Installation de Xamarin dans Visual Studio 2019
description: Ce document explique comment installer Xamarin dans Visual Studio 2019. Il décrit la configuration requise, le processus d’installation et la vérification de l’installation.
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 970985e419c5710699b6bdda49da8b59ef3c6cb6
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "71119257"
---
# <a name="installing-xamarin-in-visual-studio-2019"></a>Installation de Xamarin dans Visual Studio 2019

<a name="requirements" />

Vérifiez la [configuration requise](~/cross-platform/get-started/requirements.md) avant de commencer.

## <a name="installation"></a>Installation

[!include[](~/cross-platform/includes/install-xamarin-windows-2019.md)]

Dans Visual Studio 2019, vérifiez que Xamarin est installé en cliquant sur le menu **Aide**. Si Xamarin est installé, vous devriez voir l’élément de menu **Xamarin**, comme dans cette capture d’écran :

![Élément de menu Xamarin dans le menu Aide](windows-images/12-xamarin-menu-item.png "Élément de menu Xamarin dans le menu Aide")

Vous pouvez cliquer sur **Aide > À propos de Microsoft Visual Studio**, puis faire défiler la liste des produits installés pour voir si Xamarin y figure :

![Écran des produits Visual Studio 2019 installés](windows-images/13-xamarin-is-installed.png "Écran des produits Visual Studio 2019 installés")

Pour savoir où trouver les informations de version, consultez [Où trouver les informations de version et les journaux ?](~/cross-platform/troubleshooting/questions/version-logs.md).

## <a name="next-steps"></a>Étapes suivantes

L’installation de Xamarin dans Visual Studio 2019 vous permet d’écrire du code pour vos applications. Toutefois, vous devez procéder à une configuration supplémentaire pour générer et déployer vos applications sur un simulateur, un émulateur ou un appareil. Consultez les guides suivants pour terminer l’installation et commencer la création d’applications multiplateformes.

### <a name="ios"></a>iOS

Pour plus d’informations, consultez le guide [Installation de Xamarin.iOS sur un système Windows](~/ios/get-started/installation/windows/index.md). 

1. [Installer Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation)
2. [Connecter Visual Studio à votre hôte de build Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
3. [Configuration développeur iOS](~/ios/get-started/installation/device-provisioning/index.md) (nécessaire pour exécuter votre application sur l’appareil)
4. [Remoted iOS Simulator](~/tools/ios-simulator/index.md)
5. [Introduction à Xamarin.iOS pour Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

### <a name="android"></a>Android

Pour plus d’informations, consultez le guide [Installation de Xamarin.Android sur un système Windows](~/android/get-started/installation/windows.md).

1. [Configuration de Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Utilisation de Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Émulateur du kit Android SDK](~/android/get-started/installation/android-emulator/index.md)
4. [Mettre en place un dispositif de développement](~/android/get-started/installation/set-up-device-for-development.md)
