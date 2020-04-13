---
title: Discours Android
description: Cet article couvre les bases de l’utilisation de l’espace nom Android.Speech très puissant. Depuis sa création, Android a été en mesure de reconnaître la parole et la sortie comme texte. C’est un processus relativement simple. Pour le texte à la parole, cependant, le processus est plus impliqué, car non seulement le moteur de la parole doit être pris en compte, mais aussi les langues disponibles et installées à partir du système Text To Speech (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019765"
---
# <a name="android-speech"></a>Discours Android

_Cet article couvre les bases de l’utilisation de l’espace nom Android.Speech très puissant. Depuis sa création, Android a été en mesure de reconnaître la parole et la sortie comme texte. C’est un processus relativement simple. Pour le texte à la parole, cependant, le processus est plus impliqué, car non seulement le moteur de la parole doit être pris en compte, mais aussi les langues disponibles et installées à partir du système Text To Speech (TTS)._

## <a name="speech-overview"></a>Aperçu de la parole

Avoir un système, qui « comprend » la parole humaine et énonce ce qui est tapé — Discours au texte et Texte à la parole — est un domaine toujours croissant dans le développement mobile à mesure que la demande de communication naturelle avec nos appareils augmente. Il ya de nombreux cas où avoir une fonctionnalité qui convertit le texte en parole, ou vice versa, est un outil très utile à intégrer dans votre application Android.

Par exemple, avec la répression de l’utilisation du téléphone mobile pendant la conduite, les utilisateurs veulent un moyen mains libres de faire fonctionner leurs appareils. La pléthore de différents facteurs de forme Android, tels qu’Android Wear, et l’inclusion sans cesse croissante de ceux qui sont capables d’utiliser des appareils Android (tels que les tablettes et les plaquettes de notes), a créé un accent plus important sur les applications TTS.

Google fournit au développeur un riche ensemble d’API dans l’espace de nom Android.Speech pour couvrir la plupart des cas de faire un appareil "sens de la parole" (comme un logiciel conçu pour les aveugles).  L’espace de nom comprend l’installation pour `Android.Speech.Tts`permettre le texte d’être traduit en texte à `RecognizerIntent`travers , le contrôle sur le moteur utilisé pour effectuer la traduction, ainsi qu’un certain nombre de s qui permettent de convertir la parole en texte.

Bien que les installations soient là pour que la parole soit comprise, il y a des limites basées sur le matériel utilisé. Il est peu probable que l’appareil interprète avec succès tout ce qui lui est parlé dans toutes les langues disponibles.

## <a name="requirements"></a>Spécifications

Il n’y a pas d’exigences particulières pour ce guide, à l’indicatif autre que votre appareil ayant un microphone et un haut-parleur.

Le noyau d’un appareil Android interprétant la parole est l’utilisation d’un `Intent` avec un correspondant `OnActivityResult`.
Il est important, cependant, de reconnaître que le discours n’est pas compris, mais interprété au texte. La différence est importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La différence entre la compréhension et l’interprétation

Une définition simple de la compréhension est que vous êtes en mesure de déterminer par ton et contexte le sens réel de ce qui est dit. Interpréter signifie simplement prendre les mots et les produire sous une autre forme.

Considérez l’exemple simple suivant qui est utilisé dans la conversation quotidienne:

<kbd>Bonjour, comment vas-tu?</kbd>

Sans inflexion (accent mis sur des mots spécifiques ou des parties de mots), c’est une question simple. Cependant, si un rythme lent est appliqué à la ligne, la personne qui écoute détectera que l’asker n’est pas trop heureux et peut-être besoin de remonter le moral ou que l’asker est malade. Si l’accent est mis sur «sont», la personne demandant est généralement plus intéressé par la réponse.

Sans traitement audio assez puissant pour faire usage de l’inflexion, et un degré d’intelligence artificielle (IA) pour comprendre le contexte, le logiciel ne peut même pas commencer à comprendre ce qui a été dit, le mieux qu’un simple téléphone peut faire est de convertir le discours en texte.

## <a name="setting-up"></a>Configuration

Avant d’utiliser le système de parole, il est toujours sage de vérifier pour s’assurer que l’appareil a un microphone. Il serait inutile d’essayer d’exécuter votre application sur un Kindle ou Google note pad sans un microphone installé.

L’échantillon de code ci-dessous montre la requête si un microphone est disponible et, dans le cas non, pour créer une alerte. Si aucun microphone n’est disponible à ce stade, vous quitteriez l’activité ou désactiveriez la possibilité d’enregistrer le discours.

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

### <a name="creating-the-intent"></a>Créer l’intention

L’intention pour le système de la `RecognizerIntent`parole utilise un type particulier d’intention appelée le . Cette intention contrôle un grand nombre de paramètres, y compris combien de temps attendre avec le silence jusqu’à `Intent`ce que l’enregistrement est considéré comme terminé, toutes les langues supplémentaires à reconnaître et la sortie, et tout texte à inclure sur le dialogue modal de l’est comme moyen d’instruction. Dans cet extrait, `VOICE` est `readonly int` un utilisé `OnActivityResult`pour la reconnaissance dans .

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

### <a name="conversion-of-the-speech"></a>Conversion du discours

Le texte interprété à partir du `Intent`discours sera livré dans le , qui `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`est retourné lorsque l’activité a été achevée et est accessible via . Cela retournera `IList<string>`un , dont l’index peut être utilisé et affiché, en fonction du `RecognizerIntent.ExtraMaxResults`nombre de langues demandées dans l’intention de l’appelant (et spécifié dans le ). Comme avec n’importe quelle liste cependant, il est utile de vérifier pour s’assurer qu’il ya des données à afficher.

Lors de l’écoute `StartActivityForResult`de `OnActivityResult` la valeur de retour d’un , la méthode doit être fournie.

Dans l’exemple `textBox` ci-dessous, est un `TextBox` utilisé pour la sortie de ce qui a été dicté. Il pourrait également être utilisé pour transmettre le texte à une certaine forme d’interprète et à partir de là, l’application peut comparer le texte et la branche à une autre partie de l’application.

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

Le texte à la parole n’est pas tout à fait l’inverse de la parole au texte et s’appuie sur deux éléments clés; un moteur text-to-speech en cours d’installation sur l’appareil et une langue en cours d’installation.

En grande partie, les appareils Android sont livrés avec le service par défaut Google TTS installé et au moins une langue. Ceci est établi lorsque l’appareil est mis en place et sera basé sur l’endroit où l’appareil est à l’époque (par exemple, un téléphone mis en place en Allemagne installera la langue allemande, tandis que l’un en Amérique aura l’anglais américain).

### <a name="step-1---instantiating-texttospeech"></a>Étape 1 - Instantiating TextToSpeech

`TextToSpeech`peut prendre jusqu’à 3 paramètres, les deux premiers`AppContext`sont `IOnInitListener` `engine`nécessaires avec le troisième étant facultatif ( , , ). L’auditeur est utilisé pour se lier au service et tester pour la défaillance avec le moteur étant un certain nombre de texte Android disponible aux moteurs de la parole. À tout le moins, l’appareil aura le propre moteur de Google.

### <a name="step-2---finding-the-languages-available"></a>Étape 2 - Trouver les langues disponibles

La `Java.Util.Locale` classe contient une `GetAvailableLocales()`méthode utile appelée . Cette liste de langues supportées par le moteur de la parole peut ensuite être testée contre les langues installées.

C’est une question triviale de générer la liste des langues « comprises ». Il y aura toujours une langue par défaut (la langue que l’utilisateur `List<string>` a définie lors de la première mise en place de son `textToSpeech.IsLanguageAvailable(locale)`appareil), de sorte que dans cet exemple le a "Default" comme le premier paramètre, le reste de la liste sera rempli en fonction du résultat de la .

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

Ce code appelle [TextToSpeech.IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) pour tester si le paquet de langue pour un local donné est déjà présent sur l’appareil.
Cette méthode renvoie un [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), qui indique si la langue pour le lieu passé est disponible. Si `LanguageAvailableResult` indique que `NotSupported`la langue est, alors il n’y a pas de paquet vocal disponible (même pour le téléchargement) pour cette langue. Si `LanguageAvailableResult` est `MissingData`configuré à , alors il est possible de télécharger un nouveau paquet de langue comme expliqué ci-dessous dans l’étape 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Étape 3 - Réglage de la vitesse et du pas

Android permet à l’utilisateur de modifier le `SpeechRate` `Pitch` son du discours en modifiant le et (le taux de vitesse et le ton de la parole). Cela va de 0 à 1, avec "normal" discours étant 1 pour les deux.

### <a name="step-4---testing-and-loading-new-languages"></a>Étape 4 - Test et chargement de nouvelles langues

Le téléchargement d’une nouvelle `Intent`langue est effectué à l’aide d’un . Le résultat de cette intention provoque l’invoquable méthode [OnActivityResult.](xref:Android.App.Activity.OnActivityResult*) Contrairement à l’exemple de discours-à-texte (qui `PutExtra` a `Intent`utilisé le [RecognizerIntent](xref:Android.Speech.RecognizerIntent) comme un paramètre à la ), les tests et le chargement `Intent`s sont `Action`-basés:

- [TextToSpeech.Engine.ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; démarre une activité `TextToSpeech` à partir du moteur de la plate-forme pour vérifier l’installation et la disponibilité appropriées des ressources linguistiques sur l’appareil.

- [TextToSpeech.Engine.ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; démarre une activité qui incite l’utilisateur à télécharger les langues nécessaires.

L’exemple de code suivant illustre comment utiliser ces actions pour tester les ressources linguistiques et télécharger une nouvelle langue :

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

`TextToSpeech.Engine.ActionCheckTtsData`tests de disponibilité des ressources linguistiques. `OnActivityResult`est invoqué lorsque ce test se termine. Si les ressources linguistiques `OnActivityResult` doivent `TextToSpeech.Engine.ActionInstallTtsData` être téléchargées, les feux hors de l’action pour démarrer une activité qui permet à l’utilisateur de télécharger les langues nécessaires. Notez `OnActivityResult` que cette implémentation ne vérifie pas le `Result` code car, dans cet exemple simplifié, la détermination a déjà été faite que le paquet de langue doit être téléchargé.

L’action `TextToSpeech.Engine.ActionInstallTtsData` provoque l’activité **de données vocales Google TTS** à être présenté à l’utilisateur pour le choix des langues à télécharger:

![Activité de données vocales Google TTS](speech-images/01-google-tts-voice-data.png)

À titre d’exemple, l’utilisateur peut choisir Français et cliquer sur l’icône de téléchargement pour télécharger Français données vocales :

![Exemple de téléchargement de Français langue](speech-images/02-selecting-french.png)

L’installation de ces données se fait automatiquement après la fin du téléchargement.

### <a name="step-5---the-ioninitlistener"></a>Étape 5 - L’IOnInitListener

Pour qu’une activité puisse convertir le texte `OnInit` en parole, la méthode de l’interface doit être `TextToSpeech` mise en œuvre (c’est le deuxième paramètre spécifié pour l’instantanéisation de la classe). Cela initialise l’auditeur et teste le résultat.

L’auditeur doit tester `OperationResult.Success` `OperationResult.Failure` pour les deux et au minimum.
L’exemple suivant montre exactement que :

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

Dans ce guide, nous avons examiné les bases de la conversion du texte en parole et la parole au texte et les méthodes possibles de la façon de les inclure dans vos propres applications. Bien qu’ils ne couvrent pas tous les cas particuliers, vous devriez maintenant avoir une compréhension de base de la façon dont la parole est interprétée, comment installer de nouvelles langues, et comment augmenter l’inclusivité de vos applications.

## <a name="related-links"></a>Liens connexes

- [Xamarin.Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Texte à la parole (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Discours au texte (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Android.Speech namespace](xref:Android.Speech)
- [Android.Speech.Tts namespace](xref:Android.Speech.Tts)
