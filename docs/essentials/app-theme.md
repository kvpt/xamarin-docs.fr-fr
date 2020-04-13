---
title: 'Xamarin.Essentials: Thème de l’application'
description: Ce document décrit l’API thème de l’application demandée dans Xamarin.Essentials, qui fournit des informations sur le style de thème demandé pour l’application en cours d’exécution.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: e31cae6ff639dbe261599a7cf78ae31fc09318b3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79190317"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Thème de l’application

L’API **demandée** fait [`AppInfo`](app-information.md) partie de la classe et fournit des informations sur le thème demandé pour votre application en cours d’exécution par le système.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Utilisation de RequestedTheme

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Obtenir des informations thématiques

Le thème de l’application demandée peut être détecté avec l’API suivante :

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Cela fournira le thème actuellement demandé par le système pour votre application. La valeur de rendement sera l’une des suivantes :

* Non spécifié
* Léger
* Foncé

Non spécifié sera retourné lorsque le système d’exploitation n’a pas un style d’interface utilisateur spécifique à demander. Un exemple de ceci est sur les appareils exécutant des versions d’iOS plus vieux que 13.0.


## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Android utilise des modes de configuration pour spécifier le type de thème à demander de l’utilisateur. Basé sur la version d’Android, il peut être changé par l’utilisateur ou est changé lorsque le mode économiseur de batterie est activé.

Vous pouvez en savoir plus sur la documentation officielle [Android pour Dark Theme](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Non spécifié sera toujours retourné sur les versions d’iOS de plus de 13.0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Par défaut, votre application s’exécute à l’aide du thème défini par l’utilisateur dans les paramètres Windows **(Paramètres > Personnalisation > Couleurs > Choisissez votre mode application par défaut**). Vous pouvez définir la propriété RequestedTheme de l’application pour remplacer l’utilisateur par défaut et spécifier quel thème est utilisé.

Vous pouvez en savoir plus sur la [documentation thématique demandée par UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Code source d’AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentation sur l’API d’AppInfo](xref:Xamarin.Essentials.AppInfo)
