---
title: Reconnaissance vocale Android
description: Cet article aborde les principes fondamentaux de l’espace de noms d’Android.Speech très puissante. Depuis son lancement, Android a été en mesure de reconnaître la reconnaissance vocale et de sortie sous forme de texte. C’est un processus relativement simple. Pour le texte par synthèse vocale, toutefois, le processus est plus complexe, car non seulement le moteur de reconnaissance vocale n’a à prendre en compte, mais également les langues disponibles et installés à partir du système de texte par synthèse vocale (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: 3d4c29a7d206b826046fd1f79e0513e85ea57898
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740669"
---
# <a name="android-speech"></a>Reconnaissance vocale Android

_Cet article aborde les principes fondamentaux de l’espace de noms d’Android.Speech très puissante. Depuis son lancement, Android a été en mesure de reconnaître la reconnaissance vocale et de sortie sous forme de texte. C’est un processus relativement simple. Pour le texte par synthèse vocale, toutefois, le processus est plus complexe, car non seulement le moteur de reconnaissance vocale n’a à prendre en compte, mais également les langues disponibles et installés à partir du système de texte par synthèse vocale (TTS)._

## <a name="speech-overview"></a>Vue d’ensemble de la reconnaissance vocale

Un système qui « comprend » voix humaine et enunciates ce qui est tapé, vocale et texte, conversion de texte par synthèse vocale, est une zone dont la croissance dans le développement mobile comme la demande pour une communication naturelle avec nos périphériques augmente. Il existe de nombreux cas où ayant une fonctionnalité qui convertit le texte en paroles, ou vice versa, est un outil très utile à intégrer à votre application android.

Par exemple, avec crochet vers le bas sur l’utilisation du téléphone mobile tout en conduisant, les utilisateurs veulent un moyen gratuit mains de l’utilisation de leurs appareils. L’abondance des différents Android facteurs de forme, telles que Android Wear, et l’inclusion d’élargissement jamais de ceux en mesure d’utiliser des appareils Android (par exemple, les tablettes et les panneaux de note), a créé un se concentre plus particulièrement sur des applications de synthèse vocale.

Google fournit au développeur un rich ensemble d’API dans l’espace de noms d’Android.Speech pour couvrir la plupart des instances de fabrication d’un dispositif « vocal prenant en charge » (par exemple un logiciel conçu for the blind).  L’espace de noms inclut la fonctionnalité pour autoriser le texte à traduire en voix via `Android.Speech.Tts`, contrôler le moteur utilisé pour effectuer la traduction, ainsi que d’un nombre de `RecognizerIntent`s qui permettent la reconnaissance vocale à convertir en texte.

Tandis que les installations existe-t-il pour la voix doit être compréhensible, il existe limitations basées sur le matériel utilisé. Il est peu probable que l’appareil correctement interprète tout parlée à ce dernier dans toutes les langues disponibles.

## <a name="requirements"></a>Configuration requise

Il n’existe aucune exigence particulière pour ce guide, autre que votre appareil ayant un microphone et les haut-parleurs.

Le cœur d’un appareil Android interprétation vocale est l’utilisation d’un `Intent` avec correspondante `OnActivityResult`.
Il est important, cependant, à reconnaître que la reconnaissance vocale n’est pas comprise — mais interprétées au texte. La différence est importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La différence entre la présentation et l’interprétation

Une simple définition de présentation est que vous êtes en mesure de déterminer par ton et le contexte de la vraie signification de ce que dit. Pour interpréter simplement signifie prendre les mots et les sortir dans une autre forme.

Prenons l’exemple simple suivant qui est utilisé dans une conversation de tous les jours :

<kbd>Bonjour, comment vas-tu ?</kbd>

Sans inflexion (accent mis sur des mots spécifiques ou des parties de mots), il est une question simple. Toutefois, si un rythme lent est appliqué à la ligne, la personne à l’écoute détecte que la personne n’est pas trop heureux et peut-être doit encouragements ou que la personne est car. Si l’accent est mis sur « are », la personne qui est généralement plus intéressée par la réponse.

Sans traitement pour rendre l’audio assez puissant permettent de comprendre le contexte de l’inflexion et un degré de l’intelligence artificielle (IA), le logiciel ne peut pas même de commencer à comprendre ce qui a été dit, le mieux à faire, un téléphone simple est convertir la parole en texte.

## <a name="setting-up"></a>Configuration

Avant d’utiliser le système de reconnaissance vocale, il est donc judicieux de vérifier pour vérifier que l’appareil a un microphone. Il serait peu point essaie d’exécuter votre application sur un bloc-notes Kindle ou Google sans microphone installé.

L’exemple de code ci-dessous montre une interrogation s’il existe un microphone et dans le cas contraire, pour créer une alerte. Si aucun microphone n’est disponible à ce stade vous serez quitter l’activité soit désactiver la capacité d’enregistrer la reconnaissance vocale.

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

L’objectif pour le système de reconnaissance vocale utilise un type particulier d’intention appelé le `RecognizerIntent`. Cette intention contrôle un grand nombre de paramètres, y compris la durée pendant laquelle attendre avec silence jusqu'à ce que l’enregistrement est considéré comme terminé, toutes les langues supplémentaires pour reconnaître et de sortie et le texte à inclure dans le `Intent`de boîte de dialogue modale comme moyen de l’instruction. Dans cet extrait, `VOICE` est un `readonly int` utilisé pour la reconnaissance dans `OnActivityResult`.

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

Le texte interprété à partir de la reconnaissance vocale sera remis dans le `Intent`, qui est retourné lorsque l’activité est terminée et est accessible via `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Ceci renverra un `IList<string>`, dont l’index peut être utilisé et affichée en fonction du nombre de langues demandée dans l’intention de l’appelant (et il est spécifié dans le `RecognizerIntent.ExtraMaxResults`). Comme avec n’importe quelle liste Cependant, cela vaut la peine pour vous assurer qu’il existe des données à afficher.

Lors de l’écoute pour la valeur de retour d’un `StartActivityForResult`, le `OnActivityResult` méthode doit être fourni.

Dans l’exemple ci-dessous, `textBox` est un `TextBox` utilisée pour la sortie de ce qui a été défini. Il peut également être utilisé pour transférer le texte en une forme de l’interpréteur et à partir de là, l’application peut comparer le texte et la branche vers une autre partie de l’application.

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

## <a name="text-to-speech"></a>Conversion de texte par synthèse vocale

Conversion de texte par synthèse vocale n’est pas tout à fait l’inverse de la parole au texte et s’appuie sur deux composants clés. un moteur de synthèse vocale en cours d’installation sur l’appareil et un langage en cours d’installation.

En grande partie, les appareils Android sont fournis avec la valeur par défaut service Google TTS installé et au moins une langue. Cela est établie lorsque l’appareil est tout d’abord configuré et doit reposer à partir de l’appareil au moment (par exemple, un téléphone paramétrer en Allemagne installera l’allemand, tandis qu’un en Amérique aura l’anglais américain).

### <a name="step-1---instantiating-texttospeech"></a>Étape 1 : lors de l’instanciation texttospeech avec le

`TextToSpeech` peut prendre jusqu'à 3 paramètres, les deux premières sont requis et le troisième étant facultative (`AppContext`, `IOnInitListener`, `engine`). L’écouteur est utilisé pour lier au service et au test d’échec avec le moteur en cours de n’importe quel nombre de moteurs de disponible Android texte par synthèse vocale. Au minimum, l’appareil aura du moteur de Google.

### <a name="step-2---finding-the-languages-available"></a>Étape 2 : recherche les langues disponibles

Le `Java.Util.Locale` classe contient une méthode utile appelée `GetAvailableLocales()`. Cette liste de langues prises en charge par le moteur de reconnaissance vocale peut être testée sur les langues installées.

Il est sans importance pour générer la liste des langues « compris ». Il y aura toujours une langue par défaut (la langue définie de l’utilisateur lorsqu’ils tout d’abord configurer son appareil), c’est le cas dans cet exemple le `List<string>` a « Default » comme premier paramètre, le reste de la liste est rempli en fonction du résultat de la `textToSpeech.IsLanguageAvailable(locale)`.

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

Ce code appelle [TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/) pour tester si le module linguistique pour les paramètres régionaux donnés est déjà présent sur l’appareil.
Cette méthode retourne un [LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/), ce qui indique si la langue pour les paramètres régionaux transmis est disponible. Si `LanguageAvailableResult` indique que la langue est `NotSupported`, alors il n’existe aucun package de la voix (même pour le téléchargement) pour cette langue. Si `LanguageAvailableResult` est défini sur `MissingData`, il est possible de télécharger un nouveau package de langage, comme expliqué ci-dessous à l’étape 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Étape 3 : définition de la vitesse et le pas

Android permet à l’utilisateur de modifier le son de la parole en modifiant le `SpeechRate` et `Pitch` (le taux de vitesse et le ton de la synthèse vocale). Cela va de 0 à 1, en parlant de « normal » en cours de 1 pour les deux.

### <a name="step-4---testing-and-loading-new-languages"></a>Étape 4 : test et le chargement de nouvelles langues

Téléchargement d’un nouveau langage est effectuée à l’aide un `Intent`. Le résultat de cette intention provoque la [OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/) méthode à appeler. Contrairement à l’exemple de la parole-texte (qui est utilisé le [RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/) comme un `PutExtra` paramètre à la `Intent`), le test et le chargement `Intent`s sont `Action`-en fonction :

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash; démarre une activité à partir de la plateforme `TextToSpeech` moteur pour vérifier l’installation correcte et la disponibilité des ressources de langue sur l’appareil.

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash; démarre une activité qui invite l’utilisateur à télécharger les langues nécessaires.

L’exemple de code suivant illustre l’utilisation de ces actions à tester des ressources de langue et de télécharger un nouveau langage :

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

`TextToSpeech.Engine.ActionCheckTtsData` vérifie la disponibilité des ressources de langue. `OnActivityResult` est appelé lorsque ce test est terminé. Si les ressources de langue doivent être téléchargés, `OnActivityResult` déclenche sans cesse la `TextToSpeech.Engine.ActionInstallTtsData` action pour démarrer une activité qui permet à l’utilisateur à télécharger les langues nécessaires. Notez que ce `OnActivityResult` implémentation ne vérifie pas la `Result` code car, dans cet exemple simplifié, la détermination a déjà été faite que le package de langue doit être téléchargé.

Le `TextToSpeech.Engine.ActionInstallTtsData` action entraîne le **données vocale de Google TTS** activité qui sera présenté à l’utilisateur pour le choix de langues à télécharger :

![Activité des données systématique de Google Voice](speech-images/01-google-tts-voice-data.png)

Par exemple, l’utilisateur peut choisir le Français et cliquez sur l’icône de téléchargement pour télécharger des données vocales Français :

![Exemple de téléchargement de langue Français](speech-images/02-selecting-french.png)

Installation de ces données s’effectue automatiquement une fois le téléchargement terminé.


### <a name="step-5---the-ioninitlistener"></a>Étape 5 : le IOnInitListener

Une activité peut être en mesure de convertir le texte par synthèse vocale, la méthode d’interface `OnInit` doit être implémentée (c’est le deuxième paramètre spécifié pour l’instanciation de la `TextToSpeech` classe). Initialise l’écouteur et le résultat des tests.

L’écouteur doit effectuer un test à la fois `OperationResult.Success` et `OperationResult.Failure` au minimum.
L’exemple suivant montre exactement cela :

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

Dans ce guide, nous avons examiné les principes fondamentaux de conversion de texte par synthèse vocale et reconnaissance vocale en texte et des méthodes possibles de les inclure dans vos propres applications. Pendant qu’ils ne couvrent pas tous les cas particulier, vous devez maintenant avoir une compréhension élémentaire de la reconnaissance vocale est interprétée, comment installer de nouvelles langues et comment augmenter l’inclusivity de vos applications.



## <a name="related-links"></a>Liens associés

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Texte par synthèse vocale (exemple)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Reconnaissance vocale (exemple)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Espace de noms d’Android.Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Espace de noms Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
