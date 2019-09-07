---
title: Quels packages Android SDK installer ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: bd0f2a7704e5d666f6b32d4ccc489e069ec6ade6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757250"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quels packages Android SDK installer ?

L’installation du Android SDK n’inclut pas automatiquement tous les packages minimaux requis pour le développement. Bien que les besoins individuels des développeurs varient, les packages suivants sont généralement requis pour le développement avec Xamarin. Android :

## <a name="tools"></a>Outils

Installez les outils les plus récents à partir du dossier Tools du gestionnaire du kit de développement logiciel (SDK) :

- Android SDK Tools
- Plateforme Android SDK-outils
- Outils de génération Android SDK

## <a name="android-platforms"></a>Plateforme (s) Android

Installez la « plateforme SDK » pour les versions Android que vous avez définies au minimum & cible. 

Exemples :

- API cible 23
- API minimale 23

Vous devez uniquement installer la plateforme SDK pour l’API 23

- API cible 23
- API 15 minimum

Vous devez installer les plateformes du kit de développement logiciel pour les API 15 et 23. Notez que vous n’avez pas besoin d’installer les niveaux d’API entre la valeur minimale et la cible (même si vous effectuez un portage vers ces niveaux d’API).

## <a name="system-images"></a>Images système

Ces derniers sont requis uniquement si vous souhaitez utiliser les émulateurs Android prêts à l’emploi de Google. Pour plus d’informations, consultez [configuration de émulateur Android](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Suppléments
Les Android SDK supplémentaires ne sont généralement pas requis. Toutefois, il est utile de les connaître, car ils peuvent être nécessaires en fonction de votre cas d’utilisation.

## <a name="further-reading"></a>informations supplémentaires
Le guide suivant présente ces options et décrit plus en détail les différents packages que le gestionnaire du kit de développement logiciel (SDK) est disponible : [Guide d’installation de Android SDK Manager](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)
