---
title: Reconnaissance vocale dans Xamarin. iOS
description: Cet article présente la nouvelle API de reconnaissance vocale et montre comment l’implémenter dans une application Xamarin. iOS pour prendre en charge la reconnaissance vocale continue et transcrire la parole (à partir de flux audio en direct ou enregistrés) en texte.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: c4b818bcf3c4a5280c0280a2e28e2f59c65c8c81
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930219"
---
# <a name="speech-recognition-in-xamarinios"></a>Reconnaissance vocale dans Xamarin. iOS

_Cet article présente la nouvelle API de reconnaissance vocale et montre comment l’implémenter dans une application Xamarin. iOS pour prendre en charge la reconnaissance vocale continue et transcrire la parole (à partir de flux audio en direct ou enregistrés) en texte._

Nouveauté d’iOS 10, Apple a publié l’API de reconnaissance vocale qui permet à une application iOS de prendre en charge la reconnaissance vocale continue et de transcrire la parole (des flux audio en direct ou enregistrés) en texte.

D’après Apple, l’API de reconnaissance vocale offre les avantages et les fonctionnalités suivantes :

- Très précis
- État de l’art
- Facile à utiliser
- Rapide
- Prend en charge plusieurs langues
- Respecte la confidentialité de l’utilisateur

## <a name="how-speech-recognition-works"></a>Fonctionnement de la reconnaissance vocale

La reconnaissance vocale est implémentée dans une application iOS en obtenant l’audio en temps réel ou pré-enregistré (dans l’un des langages parlés pris en charge par l’API) et en la transmettant à un module de reconnaissance vocale qui retourne une transcription de texte brut des mots prononcés.

[![Fonctionnement de la reconnaissance vocale](speech-images/speech01.png)](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Dictée du clavier

Lorsque la plupart des utilisateurs pensent à la reconnaissance vocale sur un appareil iOS, ils pensent à l’Assistant vocal Siri intégré, qui a été publié en même temps que la dictée du clavier dans iOS 5 avec l’iPhone.

La dictée du clavier est prise en charge par tout élément d’interface qui prend en charge TextKit (tel que `UITextField` ou `UITextArea` ) et est activée par l’utilisateur en cliquant sur le bouton dictée (directement à gauche de la barre d’espace) dans le clavier virtuel iOS.

Apple a publié les statistiques de dictée de clavier suivantes (collectées depuis 2011) :

- La dictée du clavier est largement utilisée depuis sa sortie dans iOS 5.
- Environ 65 000 applications l’utilisent par jour.
- Environ une troisième de la dictée iOS est effectuée dans une application tierce.

La dictée du clavier est extrêmement facile à utiliser, car elle ne nécessite aucun effort de la part du développeur, à l’exception de l’utilisation d’un élément d’interface TextKit dans la conception de l’interface utilisateur de l’application. La dictée du clavier présente également l’avantage de ne pas nécessiter de demande de privilège spéciale de l’application avant de pouvoir être utilisée.

Les applications qui utilisent les nouvelles API de reconnaissance vocale requièrent des autorisations spéciales pour être accordées par l’utilisateur, car la reconnaissance vocale requiert la transmission et le stockage temporaire des données sur les serveurs Apple. Pour plus d’informations, consultez notre documentation sur les améliorations de la [sécurité et](~/ios/app-fundamentals/security-privacy.md) de la confidentialité.

Bien que la dictée au clavier soit facile à implémenter, elle est accompagnée de plusieurs inconvénients et limitations :

- Elle requiert l’utilisation d’un champ d’entrée de texte et l’affichage d’un clavier.
- Il fonctionne uniquement avec l’entrée audio en direct et l’application n’a aucun contrôle sur le processus d’enregistrement audio.
- Il n’offre aucun contrôle sur la langue utilisée pour interpréter la parole de l’utilisateur.
- L’application n’a aucun moyen de savoir si le bouton de dictée est même disponible pour l’utilisateur.
- L’application ne peut pas personnaliser le processus d’enregistrement audio.
- Il fournit un ensemble très superficiel de résultats qui manquent d’informations telles que le minutage et la confiance.

### <a name="speech-recognition-api"></a>API reconnaissance vocale

Nouveauté d’iOS 10, Apple a publié l’API de reconnaissance vocale qui offre un moyen plus puissant pour une application iOS d’implémenter la reconnaissance vocale. Cette API est la même que celle utilisée par Apple pour alimenter à la fois les Siri et la dictée du clavier. elle est capable de fournir une transcription rapide avec un état de précision artistique.

Les résultats fournis par l’API de reconnaissance vocale sont personnalisés en toute transparence pour les utilisateurs individuels, sans que l’application n’ait à collecter ou à accéder à des données utilisateur privées.

L’API de reconnaissance vocale fournit des résultats à l’application appelante quasiment en temps réel à mesure que l’utilisateur parle et fournit plus d’informations sur les résultats de la traduction que le simple texte. notamment :

- Interprétations multiples de ce que l’utilisateur a dit.
- Niveaux de confiance pour les différentes traductions.
- Informations de minutage.

Comme indiqué ci-dessus, l’audio pour la traduction peut être fourni par un flux en direct, ou à partir d’une source pré-enregistrée et dans l’un des plus de 50 langues et dialectes pris en charge par iOS 10.

L’API de reconnaissance vocale peut être utilisée sur n’importe quel appareil iOS exécutant iOS 10 et, dans la plupart des cas, requiert une connexion Internet en direct, car l’essentiel des traductions s’effectue sur les serveurs d’Apple. Cela dit, certains appareils iOS plus récents prennent en charge la traduction permanente sur l’appareil de langues spécifiques.

Apple a inclus une API de disponibilité pour déterminer si une langue donnée est disponible pour la traduction à l’heure actuelle. L’application doit utiliser cette API au lieu de tester directement la connectivité Internet.

Comme indiqué ci-dessus dans la section dictation du clavier, la reconnaissance vocale requiert la transmission et le stockage temporaire des données sur les serveurs Apple sur Internet. par conséquent, l’application _doit_ demander à l’utilisateur l’autorisation d’effectuer la reconnaissance en incluant la `NSSpeechRecognitionUsageDescription` clé dans son `Info.plist` fichier et en appelant la `SFSpeechRecognizer.RequestAuthorization` méthode. 

En fonction de la source du son utilisé pour la reconnaissance vocale, d’autres modifications du fichier de l’application `Info.plist` peuvent être nécessaires. Pour plus d’informations, consultez notre documentation sur les améliorations de la [sécurité et](~/ios/app-fundamentals/security-privacy.md) de la confidentialité.

## <a name="adopting-speech-recognition-in-an-app"></a>Adoption de la reconnaissance vocale dans une application

Le développeur doit suivre quatre étapes majeures pour adopter la reconnaissance vocale dans une application iOS :

- Fournissez une description d’utilisation dans le fichier de l’application `Info.plist` à l’aide de la `NSSpeechRecognitionUsageDescription` clé. Par exemple, une application de caméra peut inclure la description suivante : _« cela vous permet de prendre une photo simplement en disant le mot «fromage ».»_
- Demandez l’autorisation en appelant la `SFSpeechRecognizer.RequestAuthorization` méthode pour présenter une explication (fournie dans la `NSSpeechRecognitionUsageDescription` clé ci-dessus) de la raison pour laquelle l’application souhaite que la reconnaissance vocale accède à l’utilisateur dans une boîte de dialogue et lui permette d’accepter ou de refuser.
- Créer une demande de reconnaissance vocale :
  - Pour les données audio pré-enregistrées sur le disque, utilisez la `SFSpeechURLRecognitionRequest` classe.
  - Pour l’audio en direct (ou l’audio de la mémoire), utilisez la `SFSPeechAudioBufferRecognitionRequest` classe.
- Transmettez la demande de reconnaissance vocale à un module de reconnaissance vocale ( `SFSpeechRecognizer` ) pour commencer la reconnaissance. L’application peut éventuellement contenir le retourné `SFSpeechRecognitionTask` pour surveiller et suivre les résultats de la reconnaissance.

Ces étapes seront traitées en détail ci-dessous.

### <a name="providing-a-usage-description"></a>Fournir une description de l’utilisation

Pour fournir la `NSSpeechRecognitionUsageDescription` clé requise dans le `Info.plist` fichier, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le fichier pour l' `Info.plist` ouvrir et le modifier.
2. Basculez vers la vue **source** : 

    [![Vue source](speech-images/speech02.png)](speech-images/speech02.png#lightbox)
3. Cliquez sur **Ajouter une nouvelle entrée**, `NSSpeechRecognitionUsageDescription` Entrez pour la **propriété**, `String` pour le **type** et une description d' **utilisation** comme **valeur**. Par exemple : 

    [![Ajout de NSSpeechRecognitionUsageDescription](speech-images/speech03.png)](speech-images/speech03.png#lightbox)
4. Si l’application gère la transcription audio en direct, elle nécessitera également une description de l’utilisation du microphone. Cliquez sur **Ajouter une nouvelle entrée**, `NSMicrophoneUsageDescription` Entrez pour la **propriété**, `String` pour le **type** et une description d' **utilisation** comme **valeur**. Par exemple : 

    [![Ajout de NSMicrophoneUsageDescription](speech-images/speech04.png)](speech-images/speech04.png#lightbox)
5. Enregistrez les modifications du fichier.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le fichier pour l' `Info.plist` ouvrir et le modifier.
2. Cliquez sur **Ajouter une nouvelle entrée**, `NSSpeechRecognitionUsageDescription` Entrez pour la **propriété**, `String` pour le **type** et une description d' **utilisation** comme **valeur**. Par exemple : 

    [![Ajout de NSSpeechRecognitionUsageDescription](speech-images/speech03w.png)](speech-images/speech03w.png#lightbox)
3. Si l’application gère la transcription audio en direct, elle nécessitera également une description de l’utilisation du microphone. Cliquez sur **Ajouter une nouvelle entrée**, `NSMicrophoneUsageDescription` Entrez pour la **propriété**, `String` pour le **type** et une description d' **utilisation** comme **valeur**. Par exemple : 

    [![Ajout de NSMicrophoneUsageDescription](speech-images/speech04w.png)](speech-images/speech04w.png#lightbox)
4. Enregistrez les modifications du fichier.

-----

> [!IMPORTANT]
> Si vous ne fournissez pas l’une ou l’autre des clés ci-dessus `Info.plist` ( `NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription` ), l’application échoue sans avertissement quand vous tentez d’accéder à la reconnaissance vocale ou au microphone pour l’audio en direct.

### <a name="requesting-authorization"></a>Demande d’autorisation

Pour demander l’autorisation utilisateur requise qui permet à l’application d’accéder à la reconnaissance vocale, modifiez la classe du contrôleur d’affichage principal et ajoutez le code suivant :

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

La `RequestAuthorization` méthode de la `SFSpeechRecognizer` classe demande l’autorisation à l’utilisateur d’accéder à la reconnaissance vocale en utilisant la raison pour laquelle le développeur a fourni la `NSSpeechRecognitionUsageDescription` clé du `Info.plist` fichier.

Un `SFSpeechRecognizerAuthorizationStatus` résultat est retourné à la `RequestAuthorization` routine de rappel de la méthode qui peut être utilisée pour prendre une mesure en fonction de l’autorisation de l’utilisateur. 

> [!IMPORTANT]
> Apple suggère d’attendre que l’utilisateur ait démarré une action dans l’application qui nécessite la reconnaissance vocale avant de demander cette autorisation.

### <a name="recognizing-pre-recorded-speech"></a>Reconnaissance de la parole préenregistrée

Si l’application veut reconnaître la parole d’un fichier WAV ou MP3 pré-enregistré, elle peut utiliser le code suivant :

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

En examinant ce code en détail, tout d’abord, il tente de créer un module de reconnaissance vocale ( `SFSpeechRecognizer` ). Si la langue par défaut n’est pas prise en charge pour la reconnaissance vocale, `null` est retourné et les fonctions se ferment.

Si le module de reconnaissance vocale est disponible pour la langue par défaut, l’application vérifie s’il est actuellement disponible pour la reconnaissance à l’aide de la `Available` propriété. Par exemple, la reconnaissance peut ne pas être disponible si l’appareil ne dispose pas d’une connexion Internet active.

Un `SFSpeechUrlRecognitionRequest` est créé à partir de l' `NSUrl` emplacement du fichier pré-enregistré sur l’appareil iOS et il est transmis au module de reconnaissance vocale pour traiter une routine de rappel.

Lorsque le rappel est appelé, si le `NSError` n’est pas `null` une erreur qui doit être gérée. Étant donné que la reconnaissance vocale est effectuée de façon incrémentielle, la routine de rappel peut être appelée plusieurs fois. ainsi, la `SFSpeechRecognitionResult.Final` propriété est testée pour déterminer si la traduction est terminée et si la meilleure version de la traduction est écrite ( `BestTranscription` ).

### <a name="recognizing-live-speech"></a>Reconnaissance vocale en direct

Si l’application veut reconnaître la parole en direct, le processus est très similaire à la reconnaissance vocale préenregistrée. Par exemple :

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

En examinant ce code en détail, il crée plusieurs variables privées pour gérer le processus de reconnaissance :

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Elle utilise la Fondation AV pour enregistrer les données audio qui seront transmises à un `SFSpeechAudioBufferRecognitionRequest` pour gérer la demande de reconnaissance :

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

L’application tente de démarrer l’enregistrement et toutes les erreurs sont gérées si l’enregistrement ne peut pas être démarré :

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

La tâche de reconnaissance est démarrée et un descripteur est conservé dans la tâche de reconnaissance ( `SFSpeechRecognitionTask` ) :

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

Le rappel est utilisé de la même manière que celui utilisé précédemment pour la reconnaissance vocale préenregistrée.

Si l’enregistrement est arrêté par l’utilisateur, le moteur audio et la demande de reconnaissance vocale sont informés :

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Si l’utilisateur annule la reconnaissance, le moteur audio et la tâche de reconnaissance sont informés :

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

Il est important d’appeler `RecognitionTask.Cancel` si l’utilisateur annule la traduction pour libérer à la fois la mémoire et le processeur de l’appareil.

> [!IMPORTANT]
> L’échec de la fourniture des `NSSpeechRecognitionUsageDescription` `NSMicrophoneUsageDescription` `Info.plist` clés ou peut entraîner l’échec de l’application sans avertissement lorsque vous tentez d’accéder à la reconnaissance vocale ou au microphone pour l’audio en direct ( `var node = AudioEngine.InputNode;` ). Pour plus d’informations, consultez la section **fourniture d’une description d’utilisation** ci-dessus.

## <a name="speech-recognition-limits"></a>Limites de reconnaissance vocale

Apple impose les limitations suivantes lors de l’utilisation de la reconnaissance vocale dans une application iOS :

- La reconnaissance vocale est gratuite pour toutes les applications, mais son utilisation n’est pas illimitée :
  - Les appareils iOS individuels ont un nombre limité de reconnaissances qui peuvent être effectuées par jour.
  - Les applications seront limitées globalement sur une base de demande par jour.
- L’application doit être préparée à gérer la reconnaissance vocale et les défaillances de la limite du taux d’utilisation.
- La reconnaissance vocale peut avoir un coût élevé en cas de drainage de la batterie et de trafic réseau élevé sur l’appareil iOS de l’utilisateur. pour cette raison, Apple impose une limite de durée audio stricte d’environ une minute de voix max.

Si une application atteint régulièrement ses limites de limitation de débit, Apple demande au développeur de le contacter.

## <a name="privacy-and-usability-considerations"></a>Considérations sur la confidentialité et la convivialité

Apple a la suggestion suivante pour être transparente et respecter la confidentialité de l’utilisateur lors de l’inclusion de la reconnaissance vocale dans une application iOS :

- Lors de l’enregistrement de la voix de l’utilisateur, veillez à indiquer clairement que l’enregistrement a lieu dans l’interface utilisateur de l’application. Par exemple, l’application peut lire un son « enregistrement » et afficher un indicateur d’enregistrement.
- N’utilisez pas la reconnaissance vocale pour les informations confidentielles de l’utilisateur, telles que les mots de passe, les données d’intégrité ou les informations financières.
- Affichez les résultats de la reconnaissance _avant_ d’agir dessus. Cela fournit non seulement des commentaires sur ce que fait l’application, mais permet à l’utilisateur de gérer les erreurs de reconnaissance au fur et à mesure qu’elles sont effectuées.

## <a name="summary"></a>Résumé

Cet article a présenté la nouvelle API de reconnaissance vocale et a montré comment l’implémenter dans une application Xamarin. iOS pour prendre en charge la reconnaissance vocale continue et transcrire la parole (à partir de flux audio en direct ou enregistrés) en texte. 

## <a name="related-links"></a>Liens associés

- [SpeakToMe (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-speaktome)
