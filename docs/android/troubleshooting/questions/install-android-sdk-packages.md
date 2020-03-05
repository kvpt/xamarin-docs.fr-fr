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
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291572"
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

Exemples :

- API cible 23
- API minimale 23

Vous devez uniquement installer la plateforme SDK pour l’API 23

- API cible 23
- API 15 minimum

Vous devez installer les plateformes du kit de développement logiciel pour les API 15 et 23. Notez que vous n’avez pas besoin d’installer les niveaux d’API entre la valeur minimale et la cible (même si vous effectuez un portage vers ces niveaux d’API).

## <a name="system-images"></a>Images système

Ces derniers sont requis uniquement si vous souhaitez utiliser les émulateurs Android prêts à l’emploi de Google. Pour plus d’informations, consultez [configuration de émulateur Android](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Les Android SDK supplémentaires ne sont généralement pas requis. Toutefois, il est utile de les connaître, car ils peuvent être nécessaires en fonction de votre cas d’utilisation.
