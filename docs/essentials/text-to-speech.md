---
title: 'Xamarin.Essentials: Synthèse vocale'
description: La classe Synthèse vocale dans Xamarin.Essentials permet à une application d’utiliser les moteurs de synthèse vocale intégrés pour énoncer le texte à partir de l’appareil et également pour interroger les langages disponibles pris en charge par le moteur.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 6d89f70a8b7f6229297f86deb3cb83cf940fa7df
ms.sourcegitcommit: 4f8dc5298a95d591a59e97cdd347fd82858a1019
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66469474"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: Synthèse vocale

La classe **Synthèse vocale** permet à une application d’utiliser les moteurs de synthèse vocale intégrés pour énoncer le texte à partir de l’appareil et également pour interroger les langages disponibles pris en charge par le moteur.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-text-to-speech"></a>Utilisation de Synthèse vocale

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La Synthèse vocale fonctionne en appelant la méthode `SpeakAsync` avec du texte et des paramètres facultatifs, et retourne à la fin de l’énoncé.

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) =>
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

Cette méthode prend un `CancellationToken` facultatif pour arrêter l’énoncé lors de son démarrage.

```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

// Cancel speech if a cancellation token exists & hasn't been already requested.
public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? true)
        return;

    cts.Cancel();
}
```

La Synthèse vocale met automatiquement en file d’attente les requêtes de parole à partir du même thread.

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>Paramètres de la parole

Pour mieux contrôler la façon dont l’audio est prononcé avec `SpeechOptions` qui permet de définir le volume, la tonalité et les paramètres régionaux.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Les valeurs prises en charge pour ces paramètres sont les suivantes :

| Paramètre | Minimum | Maximum |
| --- | :---: | :---: |
| Tonalité | 0 | 2.0 |
| Volume | 0 | 1.0 |

### <a name="speech-locales"></a>Paramètres régionaux de la parole

Chaque plateforme prend en charge des paramètres régionaux différents, pour énoncer du texte dans différentes langues et avec différents accents. Les plateformes ont des codes différents et des façons variées de spécifier les paramètres régionaux, c’est pourquoi Xamarin.Essentials fournit une classe `Locale` inter-plateformes et un moyen de les interroger avec `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Limitations

- La file d’attente de l’énoncé n’est pas garantie si elle est appelée sur plusieurs threads.
- La lecture audio en arrière-plan n’est pas officiellement prise en charge.

## <a name="api"></a>API

- [Code source de Synthèse vocale](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentation de l’API Synthèse vocale](xref:Xamarin.Essentials.TextToSpeech)
