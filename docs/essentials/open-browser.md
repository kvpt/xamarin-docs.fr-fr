---
title: 'Xamarin.Essentials : Ouvrir le navigateur'
description: La classe Navigateur dans Xamarin.Essentials permet à une application d’ouvrir un lien web dans le navigateur préféré du système optimisé ou le navigateur externe.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: 715b9872642dfa8d5426c2967c62e82643def34d
ms.sourcegitcommit: 9ca07644710e5919c7bc684c1ce144471611d5df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82974478"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials : Navigateur

La classe **Navigateur** permet à une application d’ouvrir un lien web dans le navigateur préféré du système optimisé ou le navigateur externe.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-browser"></a>Utilisation de Navigateur

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité Navigateur fonctionne en appelant la méthode `OpenAsync` avec `Uri` et `BrowserLaunchMode`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

Cette méthode retourne des résultats une fois que le navigateur a été _lancé_ et pas nécessairement quand il est _fermé_ par l’utilisateur.  Le résultat `bool` indique si le lancement a réussi ou non.

## <a name="customization"></a>Personnalisation

Lorsque vous utilisez le navigateur préféré du système, plusieurs options de personnalisation sont disponibles pour iOS et Android. Celles-ci incluent un `TitleMode` (Android uniquement) et des options de couleur par défaut pour les `Toolbar` (iOS et Android) et `Controls` (iOS uniquement) qui s’affichent. 

Ces options sont spécifiées à l’aide de `BrowserLaunchOptions` lors de l’appel à `OpenAsync`.

```csharp
await Browser.OpenAsync(uri, new BrowserLaunchOptions
                {
                    LaunchMode = BrowserLaunchMode.SystemPreferred,
                    TitleMode = BrowserTitleMode.Show,
                    PreferredToolbarColor = Color.AliceBlue,
                    PreferredControlColor = Color.Violet
                });
```

![Options du navigateur](images/browser-options.png)

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Le Mode de lancement détermine la façon dont le navigateur est lancé :

## <a name="system-preferred"></a>Système par défaut

Les [onglets personnalisés](https://developer.chrome.com/multidevice/android/customtabs) vont tenter d’être utilisés pour charger l’URI et garder conscience de la navigation.

## <a name="external"></a>Externe

Un `Intent` sera utilisé pour demander l’ouverture de l’URI via le navigateur normal du système.

# <a name="ios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Système par défaut

[SFSafariViewController](xref:SafariServices.SFSafariViewController) est utilisé pour charger l’URI et conserver la sensibilisation à la navigation.

## <a name="external"></a>Externe

`OpenUrl` standard sur l’application principale est utilisé pour lancer le navigateur par défaut en dehors de l’application.

# <a name="uwp"></a>[UWP](#tab/uwp)

Le navigateur par défaut de l’utilisateur sera toujours exécuté, quel que soit `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Code source de Navigateur](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentation de l’API Browser](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
