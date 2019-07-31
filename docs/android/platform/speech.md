---
title: Reconnaissance vocale Android
description: Cet article décrit les principes fondamentaux de l’utilisation de l’espace de noms Android. Speech très puissant. Depuis son lancement, Android a pu reconnaître la parole et la sortir sous forme de texte. Il s’agit d’un processus relativement simple. Toutefois, pour la conversion de texte par synthèse vocale, le processus est plus complexe, car non seulement le moteur de reconnaissance vocale doit être pris en compte, mais également les langues disponibles et installées à partir du système TTS (Text-Speech).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: 2a6de91772b1b6135a5b3567f691b8cd3522a924
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643250"
---
# <a name="android-speech"></a>Reconnaissance vocale Android

_Cet article décrit les principes fondamentaux de l’utilisation de l’espace de noms Android. Speech très puissant. Depuis son lancement, Android a pu reconnaître la parole et la sortir sous forme de texte. Il s’agit d’un processus relativement simple. Toutefois, pour la conversion de texte par synthèse vocale, le processus est plus complexe, car non seulement le moteur de reconnaissance vocale doit être pris en compte, mais également les langues disponibles et installées à partir du système TTS (Text-Speech)._

## <a name="speech-overview"></a>Vue d’ensemble de la reconnaissance vocale

Le fait de disposer d’un système, qui «comprend» la voix humaine et dicte ce qui est tapé (discours à texte et Synthèse vocale), est une zone toujours croissante au sein du développement mobile, car la demande de communication naturelle avec nos appareils augmente. Il existe de nombreuses instances où une fonctionnalité qui convertit le texte en discours, ou vice versa, est un outil très utile pour incorporer dans votre application Android.

Par exemple, avec la désactivation de l’utilisation du téléphone mobile pendant la conduite, les utilisateurs veulent un moyen gratuit de faire fonctionner leurs appareils. La multitude de facteurs de forme Android différents, tels que l’usure Android, et l’inclusion toujours étendue de ceux qui peuvent utiliser des appareils Android (tels que les tablettes et les tablettes de notes), ont créé une plus grande focalisation sur les applications de synthèse vocale.

Google offre au développeur un ensemble complet d’API dans l’espace de noms Android. Speech pour couvrir la plupart des instances de prise en charge vocale d’un appareil (par exemple, logiciel conçu pour les aveugles).  L’espace de noms comprend la fonctionnalité qui permet de traduire le texte en `Android.Speech.Tts`discours, le contrôle du moteur utilisé pour effectuer la traduction, ainsi qu’un certain nombre `RecognizerIntent`de s qui autorisent la conversion de la parole en texte.

Bien que les fonctionnalités de reconnaissance vocale soient compréhensibles, il existe des limitations en fonction du matériel utilisé. Il est peu probable que l’appareil interprète correctement tout ce qui est parlé dans chaque langue disponible.

## <a name="requirements"></a>Configuration requise

Il n’existe aucune exigence particulière pour ce guide, autre que votre appareil avec un microphone et un orateur.

Le cœur d’un appareil Android qui interprète la parole est l’utilisation d' `Intent` un avec un `OnActivityResult`correspondant.
Toutefois, il est important de reconnaître que la parole n’est pas comprise, mais interprétée comme du texte. La différence est importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>Différence entre la compréhension et l’interprétation

Une définition simple de la compréhension est que vous êtes en mesure de déterminer par ton et contexte la signification réelle de ce qui est dit. Pour interpréter simplement, il suffit de prendre les mots et de les sortir dans un autre formulaire.

Prenons l’exemple simple suivant qui est utilisé dans une conversation quotidienne:

<kbd>Bonjour, comment vas-tu?</kbd>

Sans inflexion (mise en relief sur des mots spécifiques ou des parties de mots), il s’agit d’une question simple. Toutefois, si un rythme lent est appliqué à la ligne, la personne à l’écoute détecte que la personne n’est pas trop heureuse et peut avoir besoin de faire face à la demande ou que la personne n’est pas bien. Si l’accent est mis sur «est», la personne qui demande est généralement plus intéressée par la réponse.

Sans traitement audio assez puissant pour tirer parti de l’inflexion et d’un degré d’intelligence artificielle (AI) pour comprendre le contexte, le logiciel ne peut même pas commencer à comprendre ce qui a été dit: le meilleur téléphone simple est de convertir la parole en texte.

## <a name="setting-up"></a>Configuration

Avant d’utiliser le système de reconnaissance vocale, il est toujours judicieux de vérifier que l’appareil dispose d’un microphone. Il y a peu de points à essayer d’exécuter votre application sur un Kindle ou un pavé de note Google sans microphone installé.

L’exemple de code ci-dessous illustre l’interrogation d’un microphone disponible et, dans le cas contraire, la création d’une alerte. Si aucun microphone n’est disponible à ce stade, vous pouvez soit quitter l’activité, soit désactiver la possibilité d’enregistrer la parole.

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>Création de l’intention

L’objectif du système vocal utilise un type particulier d’intention appelé `RecognizerIntent`. Cette intention contrôle un grand nombre de paramètres, y compris la durée d’attente avec un silence jusqu’à ce que l’enregistrement soit pris en compte, les autres langages à reconnaître et les sorties `Intent`, et tout texte à inclure dans la boîte de dialogue modale de l’instruction. Dans cet extrait de `VOICE` code, `readonly int` est utilisé pour la `OnActivityResult`reconnaissance dans.

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>Conversion de la reconnaissance vocale

Le texte interprété à partir de la reconnaissance vocale est remis `Intent`dans le, qui est retourné lorsque l’activité est terminée et est accessible `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`via. Cela retourne un `IList<string>`, dont l’index peut être utilisé et affiché, en fonction du nombre de langages demandés dans l’intention de l’appelant (et spécifiés `RecognizerIntent.ExtraMaxResults`dans le). Comme pour n’importe quelle liste, il est préférable de vérifier qu’il y a des données à afficher.

Lors de l’écoute de la valeur de `StartActivityForResult`retour d' `OnActivityResult` un, la méthode doit être fournie.

Dans l’exemple ci- `textBox` dessous, `TextBox` est utilisé pour sortir ce qui a été dicté. Il peut également être utilisé pour transmettre le texte à une certaine forme d’interpréteur et à partir de là, l’application peut comparer le texte et la branche à une autre partie de l’application.

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
            if (matches.Count != 0)
            {
                string textInput = textBox.Text + matches[0];
                textBox.Text = textInput;
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>Synthèse vocale

La conversion de texte par synthèse vocale n’est pas tout à fait l’inverse de la reconnaissance vocale au texte et s’appuie sur deux composants clés. un moteur de conversion de texte par synthèse vocale installé sur l’appareil et une langue en cours d’installation.

En grande partie, les appareils Android sont fournis avec le service Google TTS par défaut installé et au moins une langue. Cela est établi lorsque l’appareil est configuré pour la première fois et est basé sur l’emplacement de l’appareil à l’heure (par exemple, un téléphone configuré en Allemagne installe la langue allemande, tandis que l’autre en Amérique est l’anglais américain).

### <a name="step-1---instantiating-texttospeech"></a>Étape 1: instanciation de TextToSpeech

`TextToSpeech`peut prendre jusqu’à 3 paramètres, les deux premiers sont requis avec le troisième étant facultatif (`AppContext`, `IOnInitListener`, `engine`). L’écouteur est utilisé pour effectuer une liaison au service et pour tester l’échec avec le moteur qui est un nombre quelconque de moteurs de reconnaissance vocale et de texte Android disponibles. Au minimum, l’appareil aura son propre moteur Google.

### <a name="step-2---finding-the-languages-available"></a>Étape 2: recherche des langues disponibles

La `Java.Util.Locale` classe contient une méthode utile appelée `GetAvailableLocales()`. Cette liste de langues prises en charge par le moteur de reconnaissance vocale peut ensuite être testée par rapport aux langues installées.

Il est important de générer la liste des langages «compriss». Il y aura toujours une langue par défaut (la langue définie par l’utilisateur lors de la première configuration de son appareil). par conséquent `List<string>` , dans cet exemple, le a la valeur «default» comme premier paramètre, le reste de la liste est rempli en `textToSpeech.IsLanguageAvailable(locale)`fonction du résultat de l’opération.

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

Ce code appelle [TextToSpeech. IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) pour vérifier si le package de langage pour les paramètres régionaux spécifiés est déjà présent sur l’appareil.
Cette méthode retourne un [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), qui indique si la langue des paramètres régionaux passés est disponible. Si `LanguageAvailableResult` indique que la langue est `NotSupported`, aucun package vocal n’est disponible (même pour le téléchargement) pour cette langue. Si `LanguageAvailableResult` a la `MissingData`valeur, il est possible de télécharger un nouveau package de langue, comme expliqué ci-dessous à l’étape 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Étape 3: définition de la vitesse et du pas

Android permet à l’utilisateur de modifier le son de la parole en modifiant le `SpeechRate` et `Pitch` (le taux de vitesse et le ton de la parole). Cela va de 0 à 1, la parole «normal» étant 1 pour les deux.

### <a name="step-4---testing-and-loading-new-languages"></a>Étape 4: test et chargement de nouvelles langues

Le téléchargement d’une nouvelle langue s’effectue à `Intent`l’aide d’un. Le résultat de cette intention entraîne l’appel de la méthode [OnActivityResult](xref:Android.App.Activity.OnActivityResult*) . Contrairement à l’exemple de reconnaissance vocale (qui utilisait le [RecognizerIntent](xref:Android.Speech.RecognizerIntent) en tant `PutExtra` que paramètre de `Intent`), les tests et les `Intent`chargements `Action`sont basés sur:

- [TextToSpeech. Engine. ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; démarre une activité à partir du `TextToSpeech` moteur de plateforme pour vérifier l’installation et la disponibilité des ressources de langue sur l’appareil.

- [TextToSpeech. Engine. ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; démarre une activité qui invite l’utilisateur à télécharger les langues nécessaires.

L’exemple de code suivant montre comment utiliser ces actions pour tester les ressources de langue et télécharger un nouveau langage:

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData`teste la disponibilité des ressources de langue. `OnActivityResult`est appelé lorsque ce test est terminé. Si les ressources de langue doivent être téléchargées, `OnActivityResult` déclenche l' `TextToSpeech.Engine.ActionInstallTtsData` action pour démarrer une activité qui permet à l’utilisateur de télécharger les langues nécessaires. Notez que cette `OnActivityResult` implémentation ne vérifie pas le `Result` code car, dans cet exemple simplifié, la détermination a déjà été effectuée pour que le package de langage doive être téléchargé.

L' `TextToSpeech.Engine.ActionInstallTtsData` action entraîne la présentation de l’activité **données vocales Google TTS** à l’utilisateur pour le choix des langues à télécharger:

![Activité de données vocales Google TTS](speech-images/01-google-tts-voice-data.png)

Par exemple, l’utilisateur peut choisir le français et cliquer sur l’icône de téléchargement pour télécharger les données vocales françaises:

![Exemple de téléchargement de la langue française](speech-images/02-selecting-french.png)

L’installation de ces données se produit automatiquement une fois le téléchargement terminé.


### <a name="step-5---the-ioninitlistener"></a>Étape 5-IOnInitListener

Pour qu’une activité puisse convertir le texte en parole, la méthode `OnInit` d’interface doit être implémentée (il s’agit du deuxième paramètre spécifié pour l’instanciation de la `TextToSpeech` classe). Cela initialise l’écouteur et teste le résultat.

L’écouteur doit tester à la `OperationResult.Success` fois `OperationResult.Failure` et au minimum.
L’exemple suivant montre simplement ce qui suit:

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons examiné les principes fondamentaux de la conversion de texte en discours et de synthèse vocale en texte, ainsi que les méthodes possibles pour les inclure dans vos propres applications. Bien qu’ils ne couvrent pas tous les cas particuliers, vous devez maintenant avoir une compréhension élémentaire de la façon dont la reconnaissance vocale est interprétée, de la façon d’installer de nouvelles langues et de l’augmentation de la capacité de votre application.



## <a name="related-links"></a>Liens associés

- [Xamarin. Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Synthèse vocale (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Parole en texte (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Espace de noms Android. Speech](xref:Android.Speech)
- [Espace de noms Android. Speech. TTS](xref:Android.Speech.Tts)
