---
title: 'Xamarin. Essentials : thème de l’application'
description: Ce document décrit l’API de thème d’application demandée dans Xamarin. Essentials, qui fournit des informations sur le style de thème demandé pour l’application en cours d’exécution.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: e31cae6ff639dbe261599a7cf78ae31fc09318b3
ms.sourcegitcommit: c83b55f60ece20e9163b3e587130250fdf113a16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79190317"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin. Essentials : thème de l’application

L’API **RequestedTheme** fait partie de la classe [`AppInfo`](app-information.md) et fournit des informations sur le thème qui est demandé pour votre application en cours d’exécution par le système.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Utilisation de RequestedTheme

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Obtention d’informations sur les thèmes

Le thème d’application demandé peut être détecté avec l’API suivante :

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Cela fournira le thème actuel demandé par le système pour votre application. La valeur de retour sera l’une des suivantes :

* Non spécifié
* Léger
* Foncé

Non spécifié est retourné lorsque le système d’exploitation n’a pas de style d’interface utilisateur spécifique à demander. C’est le cas, par exemple, sur les appareils exécutant des versions d’iOS antérieures à 13,0.


## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Android utilise des modes de configuration pour spécifier le type de thème à demander à l’utilisateur. En fonction de la version d’Android, elle peut être modifiée par l’utilisateur ou modifiée lorsque le mode économiseur de batterie est activé.

Pour plus d’informations, consultez la [documentation Android](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme)officielle sur le thème foncé.


# <a name="ios"></a>[iOS](#tab/ios)

Unspecified sera toujours renvoyé sur les versions d’iOS antérieures à 13,0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Par défaut, votre application s’exécute en utilisant le thème défini par l’utilisateur dans les paramètres Windows (**paramètres > personnalisation > couleurs > Choisissez votre mode d’application par défaut**). Vous pouvez définir la propriété RequestedTheme de l’application pour remplacer la valeur par défaut de l’utilisateur et spécifier le thème à utiliser.

Pour plus d’informations, consultez la [documentation relative au thème UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Code source d’AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentation sur l’API d’AppInfo](xref:Xamarin.Essentials.AppInfo)
