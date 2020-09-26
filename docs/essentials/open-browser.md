---
title: Xamarin.Essentials Ouvrir le navigateur
description: La classe browser dans Xamarin.Essentials permet à une application d’ouvrir un lien Web dans le navigateur préféré du système optimisé ou dans le navigateur externe.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 09/24/2020
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c38949e9c8c0a957a7afa37206683588ffbb4cf
ms.sourcegitcommit: 3a15d9b29d65139b18dcf0871fe00cffb2a56357
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91353406"
---
# <a name="no-locxamarinessentials-browser"></a>Xamarin.Essentials: Navigateur

La classe **Navigateur** permet à une application d’ouvrir un lien web dans le navigateur préféré du système optimisé ou le navigateur externe.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité du **navigateur** , la configuration spécifique à la plateforme suivante est requise.

# <a name="android"></a>[Android](#tab/android)

Si la version d’Android cible de votre projet est définie sur **Android 11 (API R 30),** vous devez mettre à jour votre manifeste Android avec les requêtes utilisées avec les nouvelles [exigences de visibilité du package](https://developer.android.com/preview/privacy/package-visibility).

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés**, puis ajoutez ce qui suit dans le nœud **manifeste** :

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="http"/>
  </intent>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="https"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

-----

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

- [Code source de Navigateur](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Browser)
- [Documentation de l’API Browser](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
