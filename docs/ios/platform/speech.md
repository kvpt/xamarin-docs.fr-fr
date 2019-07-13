---
title: Reconnaissance vocale dans Xamarin.iOS
description: Cet article présente la nouvelle API de reconnaissance vocale et montre comment l’implémenter dans une application Xamarin.iOS pour prendre en charge la reconnaissance vocale continue et transcription vocale (à partir de flux audio en direct ou enregistrés) dans le texte.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 8af7474036eb0fd6e2236cf52e96b8d12c8bc44e
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865708"
---
# <a name="speech-recognition-in-xamarinios"></a>Reconnaissance vocale dans Xamarin.iOS

_Cet article présente la nouvelle API de reconnaissance vocale et montre comment l’implémenter dans une application Xamarin.iOS pour prendre en charge la reconnaissance vocale continue et transcription vocale (à partir de flux audio en direct ou enregistrés) dans le texte._

Nouveau à iOS 10, Apple a release de l’API de reconnaissance vocale qui permet à une application iOS prendre en charge la reconnaissance vocale continue et transcription vocale (à partir de flux audio en direct ou enregistrés) dans le texte.

En fonction d’Apple, l’API de reconnaissance vocale a les fonctionnalités et les avantages suivants :

- Très précis
- État de l’Art
- Facile à utiliser
- Rapide
- Prend en charge plusieurs langues
- Respecte la confidentialité des utilisateurs

## <a name="how-speech-recognition-works"></a>Comment fonctionne la reconnaissance vocale

La reconnaissance vocale est implémentée dans une application iOS par l’acquisition de l’audio en direct ou préenregistré (dans les langues vocales qui prend en charge de l’API) et en le passant à un module de reconnaissance vocale qui retourne une transcription en texte brut des mots prononcés.

[![](speech-images/speech01.png "Comment fonctionne la reconnaissance vocale")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Dictée de clavier

Lorsque la plupart des utilisateurs pensez de la reconnaissance vocale sur un appareil iOS, ils pensent de l’assistant vocal de Siri intégré, qui a été publié en même temps que le clavier dictée dans iOS 5, avec uniquement le chiffre 4 l’iPhone.

La dictée de clavier est pris en charge par n’importe quel élément d’interface qui prend en charge TextKit (tel que `UITextField` ou `UITextArea`) et est activé par l’utilisateur clique sur le bouton de dictée (directement à gauche de la barre d’espace) sur le clavier virtuel iOS.

Apple a publié les statistiques de la dictée de clavier suivantes (collectées depuis 2011) :

- La dictée de clavier a été largement utilisée dans la mesure où il a été publié dans iOS 5.
- Environ 65 000 applications l’utilisent quotidiennement.
- Sur un tiers de toutes les iOS dictée est effectuée dans une application tierce 3e.

Clavier dictée est extrêmement facile à utiliser comme il ne nécessite aucun effort de part du développeur, autre qu’à l’aide d’un élément d’interface TextKit dans la conception de l’interface utilisateur de l’application. La dictée de clavier a également l’avantage de ne pas nécessiter les demandes de privilège spécial de l’application avant de pouvoir être utilisé.

Applications qui utilisent les nouvelles API de reconnaissance vocale nécessite des autorisations spéciales pour être accordées par l’utilisateur, étant donné que la reconnaissance vocale nécessite la transmission et le stockage temporaire des données sur les serveurs d’Apple. Consultez notre [sécurité et confidentialité améliorations](~/ios/app-fundamentals/security-privacy.md) documentation pour plus d’informations.

Alors que la dictée de clavier est facile à implémenter, il n’est fourni avec plusieurs limitations et des inconvénients :

- Elle nécessite l’utilisation d’un champ d’entrée de texte et l’affichage d’un clavier.
- Il fonctionne avec live audio d’entrée uniquement, et l’application n’a aucun contrôle sur le processus d’enregistrement audio.
- Il ne fournit aucun contrôle sur le langage utilisé pour interpréter la voix de l’utilisateur.
- Il n’existe aucun moyen de l’application savoir si le bouton de dictée est encore disponible pour l’utilisateur.
- L’application ne peut pas personnaliser le processus d’enregistrement audio.
- Il fournit un jeu de résultats très superficiel qui ne dispose pas des informations telles que la synchronisation et la confiance.

### <a name="speech-recognition-api"></a>API de reconnaissance vocale

Nouveau à iOS 10, Apple a publié l’API de reconnaissance vocale qui offre un moyen plus puissant pour une application iOS implémenter la reconnaissance vocale. Cette API est le même que celui qui Apple utilise pour alimenter Siri et la dictée de clavier et il est capable de fournir une transcription rapide avec une précision de pointe.

Les résultats fournis par l’API de reconnaissance vocale sont personnalisés en toute transparence à des utilisateurs individuels, sans l’application d’avoir à collecter ou accéder aux données utilisateur privées.

L’API de reconnaissance vocale fournit des résultats à l’application appelante dans presque en temps réel comme parle de l’utilisateur et fournit plus d’informations sur les résultats de traduction qu’uniquement du texte. Elles incluent notamment :

- Interprétée de plusieurs façons de ce que l’utilisateur dit.
- Niveaux de confiance pour les traductions individuelles.
- Informations de minutage.

Comme indiqué ci-dessus, l’audio pour la traduction peut être fourni par un flux en direct, ou à partir d’une source préalablement enregistrée et dans les dialectes pris en charge par iOS 10 et plus de 50 langues.

L’API de reconnaissance vocale peut être utilisé sur n’importe quel appareil iOS exécutant iOS 10 et dans la plupart des cas, nécessite une connexion internet active dans la mesure où la majeure partie des traductions a lieu sur les serveurs d’Apple. Ceci dit, certains iOS plus récents appareils prennent toujours en charge sur, la traduction sur le périphérique de langues spécifiques.

Apple a inclus une API de disponibilité pour déterminer si une langue donnée est disponible pour la traduction au moment actuel. L’application doit utiliser cette API plutôt que de tester pour la connectivité internet lui-même directement.

Comme indiqué ci-dessus dans la section de la dictée de clavier, la reconnaissance vocale nécessite la transmission et le stockage temporaire des données sur les serveurs d’Apple sur internet et par conséquent, l’application _doit_ demander l’autorisation de l’utilisateur à effectuer reconnaissance en incluant le `NSSpeechRecognitionUsageDescription` clés dans son `Info.plist` fichier et en appelant le `SFSpeechRecognizer.RequestAuthorization` (méthode). 

Selon la source de l’audio utilisé pour la reconnaissance vocale, apporter d’autres modifications à l’application `Info.plist` fichier peut être nécessaire. Consultez notre [sécurité et confidentialité améliorations](~/ios/app-fundamentals/security-privacy.md) documentation pour plus d’informations.

## <a name="adopting-speech-recognition-in-an-app"></a>Adoption de la reconnaissance vocale dans une application

Il existe quatre étapes majeures que le développeur doit prendre pour adopter la reconnaissance vocale dans une application iOS :

- Fournissez une description de l’utilisation de l’application `Info.plist` de fichiers à l’aide de la `NSSpeechRecognitionUsageDescription` clé. Par exemple, une application d’appareil photo peut inclure la description suivante, _« Cela vous permet de prendre une photo simplement en indiquant que le mot « fromage ». »_
- Demander l’autorisation en appelant le `SFSpeechRecognizer.RequestAuthorization` méthode pour présenter une explication (fourni dans le `NSSpeechRecognitionUsageDescription` clé ci-dessus) de la raison pour laquelle l’application veut vocale reconnaissance de l’accès à l’utilisateur dans une boîte de dialogue et les autoriser à accepter ou refuser.
- Créez une demande de reconnaissance vocale :
    * Pour l’audio préenregistré sur le disque, utilisez la `SFSpeechURLRecognitionRequest` classe.
    * Pour l’audio (ou audio de la mémoire), utilisez la `SFSPeechAudioBufferRecognitionRequest` classe.
- Transmettre la demande de reconnaissance vocale dans un module de reconnaissance vocale (`SFSpeechRecognizer`) pour commencer la reconnaissance. L’application peut éventuellement contenir sur retourné `SFSpeechRecognitionTask` pour surveiller et suivre les résultats de reconnaissance.

Ces étapes seront abordées en détail ci-dessous.

### <a name="providing-a-usage-description"></a>Fournir une Description de l’utilisation

Pour fournir le texte requis `NSSpeechRecognitionUsageDescription` clé dans le `Info.plist` de fichiers, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Info.plist` fichier à ouvrir pour modification.
2. Basculez vers le **Source** vue : 

    [![](speech-images/speech02.png "La vue de Source")](speech-images/speech02.png#lightbox)
3. Cliquez sur **ajouter une nouvelle entrée**, entrez `NSSpeechRecognitionUsageDescription` pour le **propriété**, `String` pour le **Type** et un **Description de l’utilisation** comme le **valeur**. Par exemple : 

    [![](speech-images/speech03.png "Ajout de NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Si l’application doit gérer la transcription audio en direct, il nécessitera également une Description de l’utilisation du Microphone. Cliquez sur **ajouter une nouvelle entrée**, entrez `NSMicrophoneUsageDescription` pour le **propriété**, `String` pour le **Type** et un **Description de l’utilisation** comme le **valeur**. Par exemple : 

    [![](speech-images/speech04.png "Ajout de NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
5. Enregistrez les modifications dans le fichier.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le `Info.plist` fichier à ouvrir pour modification.
2. Cliquez sur **ajouter une nouvelle entrée**, entrez `NSSpeechRecognitionUsageDescription` pour le **propriété**, `String` pour le **Type** et un **Description de l’utilisation** comme le **valeur**. Par exemple : 

    [![](speech-images/speech03w.png "Ajout de NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
3. Si l’application doit gérer la transcription audio en direct, il nécessitera également une Description de l’utilisation du Microphone. Cliquez sur **ajouter une nouvelle entrée**, entrez `NSMicrophoneUsageDescription` pour le **propriété**, `String` pour le **Type** et un **Description de l’utilisation** comme le **valeur**. Par exemple : 

    [![](speech-images/speech04w.png "Ajout de NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Enregistrez les modifications dans le fichier.

-----

> [!IMPORTANT]
> Ne parvient pas à fournir un des éléments ci-dessus `Info.plist` clés (`NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription`) peut entraîner l’application échoue sans avertissement lorsque vous tentez d’accéder à la reconnaissance vocale ou le microphone pour l’audio en direct.




### <a name="requesting-authorization"></a>Demandez une autorisation

Pour demander l’autorisation d’utilisateur requis qui autorise l’application à accéder à la reconnaissance vocale, modifiez la classe de contrôleur d’affichage principale et ajoutez le code suivant :

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

Le `RequestAuthorization` méthode de la `SFSpeechRecognizer` classe sera demander l’autorisation de l’utilisateur pour la reconnaissance vocale accès à l’aide de la raison pour laquelle le développeur fourni dans le `NSSpeechRecognitionUsageDescription` clé de le `Info.plist` fichier.

Un `SFSpeechRecognizerAuthorizationStatus` résultat est retourné à la `RequestAuthorization` routine de rappel de la méthode qui peut être utilisé pour prendre des mesures en fonction de l’autorisation de l’utilisateur. 

> [!IMPORTANT]
> Apple suggère en attente jusqu'à ce que l’utilisateur a démarré une action dans l’application qui nécessite la reconnaissance vocale avant de demander cette autorisation.

### <a name="recognizing-pre-recorded-speech"></a>Reconnaissance vocale préenregistré

Si l’application veut voix à partir d’un fichier WAV ou MP3 préenregistré, il peut utiliser le code suivant :

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

En examinant ce code en détail, tout d’abord, il tente de créer un module de reconnaissance vocale (`SFSpeechRecognizer`). Si la langue par défaut n’est pas pris en charge pour la reconnaissance vocale, `null` est retourné et les sorties de fonctions.

Si le module de reconnaissance vocale est disponible pour la langue par défaut, l’application vérifie s’il est actuellement disponible pour la reconnaissance à l’aide du `Available` propriété. Par exemple, la reconnaissance peut-être pas disponible si l’appareil n’a pas une connexion internet active.

Un `SFSpeechUrlRecognitionRequest` est créé à partir de la `NSUrl` emplacement du fichier préenregistré sur l’appareil iOS et il est transmis au module de reconnaissance vocale pour traiter une routine de rappel.

Lorsque le rappel est appelé, si le `NSError` n’est pas `null` une erreur s’est produite qui doivent être gérés. Étant donné que la reconnaissance vocale est effectuée de façon incrémentielle, la routine de rappel peut être appelée plusieurs fois de sorte que la `SFSpeechRecognitionResult.Final` propriété est testée pour déterminer si la traduction est terminée et meilleure version de la traduction est écrit (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Reconnaissance vocale en direct

Si l’application veut voix en direct, le processus est très similaire à la reconnaissance vocale préenregistré. Par exemple :

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

Elle utilise AV Foundation pour enregistrer l’audio qui sera passé à un `SFSpeechAudioBufferRecognitionRequest` pour traiter la demande de reconnaissance :

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

L’application tente de démarrer l’enregistrement et toutes les erreurs sont gérées Si l’enregistrement ne peut pas être démarré :

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

La tâche de reconnaissance est démarrée et un handle est conservé à la tâche de reconnaissance (`SFSpeechRecognitionTask`) :

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

Le rappel est utilisé de manière similaire à celle utilisée ci-dessus sur voix préalablement enregistrée.

Si l’enregistrement est arrêté par l’utilisateur en cours, le moteur Audio et que la demande de reconnaissance vocale en soient informés :

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Si l’utilisateur annule la reconnaissance, le moteur Audio et la tâche de reconnaissance sont informés :

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

Il est important d’appeler `RecognitionTask.Cancel` si l’utilisateur annule la traduction afin de libérer de la mémoire et processeur de l’appareil.

> [!IMPORTANT]
> Ne parvient pas à fournir le `NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription` `Info.plist` clés peuvent entraîner l’application échoue sans avertissement lorsque vous tentez d’accéder à la reconnaissance vocale ou le microphone pour l’audio en direct (`var node = AudioEngine.InputNode;`). Veuillez consulter la **en fournissant une Description de l’utilisation** section ci-dessus pour plus d’informations.

## <a name="speech-recognition-limits"></a>Limites de reconnaissance vocale

Apple impose les limitations suivantes lorsque vous travaillez avec la reconnaissance vocale dans une application iOS :

- La reconnaissance vocale est gratuite à toutes les applications, mais son utilisation n’est pas un nombre illimitée :
    - Les appareils iOS individuelles ont un nombre limité de reconnaissances qui peuvent être effectuées par jour.
    - Les applications sont limitées dans le monde entier sur une base de la demande par jour.
- L’application doit être préparée à gérer la connexion de réseau de la reconnaissance vocale et les échecs de limite de taux de l’utilisation.
- La reconnaissance vocale peut avoir un coût élevé dans une batterie se décharge et un trafic réseau élevé sur un appareil iOS de l’utilisateur, pour cette raison, Apple impose une limite de durée audio strict d’environ une minute de la parole max.

Si une application sollicite régulièrement ses limites de limitation de la vitesse, Apple demande que le développeur de les contacter.

## <a name="privacy-and-usability-considerations"></a>Confidentialité et les considérations relatives à la facilité d’utilisation

Apple a la suggestion suivante pour transparent et en respectant la confidentialité lors de l’inclusion de la reconnaissance vocale dans une application iOS :

- Lorsque l’enregistrement vocal de l’utilisateur, veillez à indiquer clairement que l’enregistrement a lieu dans l’Interface utilisateur de l’application. Par exemple, l’application peut lire un son un « enregistrement » et afficher un indicateur d’enregistrement.
- N’utilisez pas la reconnaissance vocale pour des informations utilisateur sensibles telles que les mots de passe, les données d’intégrité ou les informations financières.
- Afficher les résultats de reconnaissance _avant_ agisse sur ceux-ci. Cela fournit non seulement le commentaire quant à ce que l’application effectue, mais permet à l’utilisateur gérer les erreurs de reconnaissance comme elles ont été effectuées.

## <a name="summary"></a>Récapitulatif

Cet article a présenté la nouvelle API de reconnaissance vocale et vous a montré comment l’implémenter dans une application Xamarin.iOS pour prendre en charge la reconnaissance vocale continue et transcription vocale (à partir de flux audio en direct ou enregistrés) dans le texte. 



## <a name="related-links"></a>Liens associés

- [SpeakToMe (exemple)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
