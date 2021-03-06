---
title: 'Xamarin.Essentials: Thème d’application'
description: Ce document décrit l’API de thème d’application demandée dans Xamarin.Essentials , qui fournit des informations sur le style de thème demandé pour l’application en cours d’exécution.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 01/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c68f00b77f0b9f88d014334dc56e1e58ed057986
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436881"
---
# <a name="no-locxamarinessentials-app-theme"></a>Xamarin.Essentials: Thème d’application

L’API **RequestedTheme** fait partie de la [`AppInfo`](app-information.md) classe et fournit des informations sur le thème qui est demandé pour votre application en cours d’exécution par le système.

## <a name="get-started"></a>Prise en main

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

`RequestedTheme`L’appel de doit être appelé sur le thread d’interface utilisateur ou une exception sera levée.

Les applications UWP respectent votre paramètre dans le format UWP App. XAML sous **RequestedTheme**. S’il est défini sur un thème spécifique, Xamarin.Essentials retourne toujours ce paramètre. Pour utiliser le thème dynamique du système d’exploitation, supprimez ce nœud de votre application, puis, lorsque votre application est exécutée, elle retourne le thème défini par l’utilisateur dans paramètres Windows (**paramètres > personnalisation > couleurs > Choisissez votre mode d’application par défaut**).

Pour plus d’informations, consultez la [documentation relative au thème UWP](/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Code source d’AppInfo](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppInfo)
- [Documentation sur l’API d’AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Theme-Detection-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]