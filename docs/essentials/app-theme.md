---
title: 'Xamarin.Essentials: Thème d’application'
description: Ce document décrit l’API Thème d’application demandée dans Xamarin.Essentials, qui fournit des informations sur le style de thème demandé pour l’application en cours d’exécution.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 39f650a73f03888e50c6259c1052e24dbea0a4f5
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555667"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Thème d’application

L’API **RequestedTheme** fait partie de la classe [AppInfo](/app-information.md) et fournit des informations sur le thème qui est demandé pour votre application en cours d’exécution par le système.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Utilisation de RequestedTheme

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Obtention d’informations sur le thème

Le thème d’application demandé peut être détecté avec l’API suivante :

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Cela fournit le thème actuel demandé par le système pour votre application. La valeur de retour sera l’une des suivantes :

* Non spécifié
* Clair
* Sombre

Non spécifié (Unspecified) est retourné lorsque le système d’exploitation n’a pas de style d’interface utilisateur spécifique à demander. C’est le cas, par exemple, sur les appareils exécutant des versions d’iOS antérieures à 13.0.


## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Android utilise des modes de configuration pour spécifier le type de thème à demander à l’utilisateur. En fonction de la version d’Android, il peut être modifié par l’utilisateur ou lorsque le mode économiseur de batterie est activé.

Pour plus d’informations, consultez la [documentation officielle Android sur le thème Sombre](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Non spécifié (Unspecified) est toujours retourné sur les versions d’iOS antérieures à 13.0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Par défaut, votre application s’exécute à l’aide du thème défini par l’utilisateur dans les paramètres Windows (**Paramètres > Personnalisation > Couleurs > Choisir votre mode d’application par défaut**). Vous pouvez définir la propriété RequestedTheme de l’application pour remplacer la valeur par défaut de l’utilisateur et spécifier le thème à utiliser.

Pour plus d’informations, consultez la [documentation relative au thème UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Code source d’AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentation sur l’API d’AppInfo](xref:Xamarin.Essentials.AppInfo)
