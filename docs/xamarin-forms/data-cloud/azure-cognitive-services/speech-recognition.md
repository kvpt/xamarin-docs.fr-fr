---
title: ''
description: Cet article explique comment utiliser l’API Azure Speech service pour transcrire la parole en texte dans une Xamarin.Forms application.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 171ecc02fda304135e5f535c3e798067595d7047
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139319"
---
# <a name="speech-recognition-using-azure-speech-service"></a>Reconnaissance vocale à l’aide du service Azure Speech

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)

Azure Speech service est une API basée sur le Cloud qui offre les fonctionnalités suivantes :

- La **parole en texte** contranscrit des fichiers audio ou des flux dans du texte.
- La conversion **de texte par synthèse vocale** convertit le texte d’entrée en discours synthétisé de type humain.
- La **traduction vocale** active la traduction en temps réel et multilingue pour la parole en texte et la reconnaissance vocale.
- Les **assistants vocaux** peuvent créer des interfaces de conversation de type humain pour les applications.

Cet article explique comment la reconnaissance vocale en texte est implémentée dans l’exemple d' Xamarin.Forms application à l’aide du service Azure Speech. Les captures d’écran suivantes montrent l’exemple d’application sur iOS et Android :

[![Captures d’écran de l’exemple d’application sur iOS et Android](speech-recognition-images/speech-recognition-cropped.png)](speech-recognition-images/speech-recognition.png#lightbox "Captures d’écran de l’exemple d’application sur iOS et Android")

## <a name="create-an-azure-speech-service-resource"></a>Créer une ressource Azure Speech service

Azure Speech service fait partie d’Azure Cognitive Services, qui fournit des API basées sur le Cloud pour des tâches telles que la reconnaissance d’images, la reconnaissance vocale et la traduction, et la recherche Bing. Pour plus d’informations, voir [qu’est-ce qu’Azure cognitive services ?](https://docs.microsoft.com/azure/cognitive-services/welcome).

L’exemple de projet requiert la création d’une ressource Azure Cognitive Services dans votre Portail Azure. Une ressource de Cognitive Services peut être créée pour un service unique, tel que Speech service, ou en tant que ressource à plusieurs services. Les étapes de création d’une ressource de service vocal sont les suivantes :

1. Connectez-vous à votre [portail Azure](https://portal.azure.com).
1. Créer une ressource à service multiple ou à service unique.
1. Obtenez les informations sur la clé et la région de l’API pour votre ressource.
1. Mettez à jour l’exemple de fichier **constants.cs** .

Pour obtenir un guide pas à pas de la création d’une ressource, consultez [créer une ressource cognitive services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer. Une fois que vous avez un compte, vous pouvez créer une ressource de service unique au niveau gratuit pour tester le service.

## <a name="configure-your-app-with-the-speech-service"></a>Configurer votre application avec le service vocal

Après la création d’une ressource Cognitive Services, le fichier **constants.cs** peut être mis à jour avec la région et la clé API de votre ressource Azure :

```csharp
public static class Constants
{
    public static string CognitiveServicesApiKey = "YOUR_KEY_GOES_HERE";
    public static string CognitiveServicesRegion = "westus";
}
```

## <a name="install-nuget-speech-service-package"></a>Installer le package du service de reconnaissance vocale NuGet

L’exemple d’application utilise le package NuGet **Microsoft. cognitiveservices Account. Speech** pour se connecter au service Azure Speech. Installez ce package NuGet dans le projet partagé et chaque projet de plateforme.

## <a name="create-an-imicrophoneservice-interface"></a>Créer une interface IMicrophoneService

Chaque plateforme requiert une autorisation d’accès au microphone. L’exemple de projet fournit une `IMicrophoneService` interface dans le projet partagé et utilise Xamarin.Forms `DependencyService` pour obtenir des implémentations de plateforme de l’interface.

```csharp
public interface IMicrophoneService
{
    Task<bool> GetPermissionAsync();
    void OnRequestPermissionResult(bool isGranted);
}
```

## <a name="create-the-page-layout"></a>Créer la mise en page

L’exemple de projet définit une mise en page de base dans le fichier **MainPage. Xaml** . Les éléments de disposition de clé `Button` démarrent le processus de transcription, un `Label` pour contenir le texte transcrit et un `ActivityIndicator` à afficher lorsque la transcription est en cours :

```xaml
<ContentPage ...>
    <StackLayout>
        <Frame ...>
            <ScrollView x:Name="scroll"
                        ...>
                <Label x:Name="transcribedText"
                       ... />
            </ScrollView>
        </Frame>

        <ActivityIndicator x:Name="transcribingIndicator"
                           IsRunning="False" />
        <Button x:Name="transcribeButton"
                ...
                Clicked="TranscribeClicked"/>
    </StackLayout>
</ContentPage>
```

## <a name="implement-the-speech-service"></a>Implémenter le service vocal

Le fichier code-behind **MainPage.Xaml.cs** contient toute la logique permettant d’envoyer du son et de recevoir du texte transcrit à partir du service Azure Speech.

Le `MainPage` constructeur obtient une instance de l' `IMicrophoneService` interface à partir de `DependencyService` :

```csharp
public partial class MainPage : ContentPage
{
    SpeechRecognizer recognizer;
    IMicrophoneService micService;
    bool isTranscribing = false;

    public MainPage()
    {
        InitializeComponent();

        micService = DependencyService.Resolve<IMicrophoneService>();
    }

    // ...
}
```

La `TranscribeClicked` méthode est appelée lorsque l' `transcribeButton` instance est exploitée :

```csharp
async void TranscribeClicked(object sender, EventArgs e)
{
    bool isMicEnabled = await micService.GetPermissionAsync();

    // EARLY OUT: make sure mic is accessible
    if (!isMicEnabled)
    {
        UpdateTranscription("Please grant access to the microphone!");
        return;
    }

    // initialize speech recognizer 
    if (recognizer == null)
    {
        var config = SpeechConfig.FromSubscription(Constants.CognitiveServicesApiKey, Constants.CognitiveServicesRegion);
        recognizer = new SpeechRecognizer(config);
        recognizer.Recognized += (obj, args) =>
        {
            UpdateTranscription(args.Result.Text);
        };
    }

    // if already transcribing, stop speech recognizer
    if (isTranscribing)
    {
        try
        {
            await recognizer.StopContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = false;
    }

    // if not transcribing, start speech recognizer
    else
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            InsertDateTimeRecord();
        });
        try
        {
            await recognizer.StartContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = true;
    }
    UpdateDisplayState();
}
```

La méthode `TranscribeClicked` effectue les opérations suivantes :

1. Vérifie si l’application a accès au microphone et s’arrête tôt si ce n’est pas le cas.
1. Crée une instance de la `SpeechRecognizer` classe si elle n’existe pas déjà.
1. Arrête la transcription continue si elle est en cours.
1. Insère un horodateur et démarre la transcription continue si elle n’est pas en cours.
1. Indique à l’application de mettre à jour son apparence en fonction du nouvel état de l’application.

Les autres méthodes de `MainPage` classe sont des assistances pour l’affichage de l’état de l’application :

```csharp
void UpdateTranscription(string newText)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (!string.IsNullOrWhiteSpace(newText))
        {
            transcribedText.Text += $"{newText}\n";
        }
    });
}

void InsertDateTimeRecord()
{
    var msg = $"=================\n{DateTime.Now.ToString()}\n=================";
    UpdateTranscription(msg);
}

void UpdateDisplayState()
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (isTranscribing)
        {
            transcribeButton.Text = "Stop";
            transcribeButton.BackgroundColor = Color.Red;
            transcribingIndicator.IsRunning = true;
        }
        else
        {
            transcribeButton.Text = "Transcribe";
            transcribeButton.BackgroundColor = Color.Green;
            transcribingIndicator.IsRunning = false;
        }
    });
}
```

La `UpdateTranscription` méthode écrit le fourni `newText` `string` dans l' `Label` élément nommé `transcribedText` . Elle force cette mise à jour sur le thread d’interface utilisateur afin qu’elle puisse être appelée à partir de n’importe quel contexte sans provoquer d’exceptions. `InsertDateTimeRecord`Écrit la date et l’heure actuelles dans l' `transcribedText` instance pour marquer le début d’une nouvelle transcription. Enfin, la `UpdateDisplayState` méthode met à jour les `Button` `ActivityIndicator` éléments et pour indiquer si la transcription est en cours ou non.

## <a name="create-platform-microphone-services"></a>Créer des services de microphone de plateforme

L’application doit disposer d’un accès au microphone pour collecter des données vocales. L' `IMicrophoneService` interface doit être implémentée et inscrite auprès de `DependencyService` sur chaque plateforme pour que l’application fonctionne.

### <a name="android"></a>Téléphone

L’exemple de projet définit une `IMicrophoneService` implémentation pour Android appelée `AndroidMicrophoneService` :

```csharp
[assembly: Dependency(typeof(AndroidMicrophoneService))]
namespace CognitiveSpeechService.Droid.Services
{
    public class AndroidMicrophoneService : IMicrophoneService
    {
        public const int RecordAudioPermissionCode = 1;
        private TaskCompletionSource<bool> tcsPermissions;
        string[] permissions = new string[] { Manifest.Permission.RecordAudio };

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();

            if ((int)Build.VERSION.SdkInt < 23)
            {
                tcsPermissions.TrySetResult(true);
            }
            else
            {
                var currentActivity = MainActivity.Instance;
                if (ActivityCompat.CheckSelfPermission(currentActivity, Manifest.Permission.RecordAudio) != (int)Permission.Granted)
                {
                    RequestMicPermissions();
                }
                else
                {
                    tcsPermissions.TrySetResult(true);
                }

            }

            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermissions()
        {
            if (ActivityCompat.ShouldShowRequestPermissionRationale(MainActivity.Instance, Manifest.Permission.RecordAudio))
            {
                Snackbar.Make(MainActivity.Instance.FindViewById(Android.Resource.Id.Content),
                        "Microphone permissions are required for speech transcription!",
                        Snackbar.LengthIndefinite)
                        .SetAction("Ok", v =>
                        {
                            ((Activity)MainActivity.Instance).RequestPermissions(permissions, RecordAudioPermissionCode);
                        })
                        .Show();
            }
            else
            {
                ActivityCompat.RequestPermissions((Activity)MainActivity.Instance, permissions, RecordAudioPermissionCode);
            }
        }
    }
}
```

Le `AndroidMicrophoneService` offre les fonctionnalités suivantes :

1. L' `Dependency` attribut inscrit la classe avec `DependencyService` .
1. La `GetPermissionAsync` méthode vérifie si des autorisations sont requises en fonction de la version de Android SDK et appelle `RequestMicPermissions` si l’autorisation n’a pas déjà été accordée.
1. La `RequestMicPermissions` méthode utilise la `Snackbar` classe pour demander des autorisations à l’utilisateur si un raisonnement est requis, sinon il demande directement des autorisations d’enregistrement audio.
1. La `OnRequestPermissionResult` méthode est appelée avec un `bool` résultat une fois que l’utilisateur a répondu à la demande d’autorisations.

La `MainActivity` classe est personnalisée pour mettre à jour l' `AndroidMicrophoneService` instance lorsque les demandes d’autorisations sont terminées :

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    IMicrophoneService micService;
    internal static MainActivity Instance { get; private set; }
    
    protected override void OnCreate(Bundle savedInstanceState)
    {
        Instance = this;
        // ...
        micService = DependencyService.Resolve<IMicrophoneService>();
    }
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        // ...
        switch(requestCode)
        {
            case AndroidMicrophoneService.RecordAudioPermissionCode:
                if (grantResults[0] == Permission.Granted)
                {
                    micService.OnRequestPermissionResult(true);
                }
                else
                {
                    micService.OnRequestPermissionResult(false);
                }
                break;
        }
    }
}
```

La `MainActivity` classe définit une référence statique appelée `Instance` , qui est requise par l' `AndroidMicrophoneService` objet lors de la demande d’autorisations. Elle substitue la `OnRequestPermissionsResult` méthode pour mettre à jour l' `AndroidMicrophoneService` objet quand la demande d’autorisations est approuvée ou refusée par l’utilisateur.

Enfin, l’application Android doit inclure l’autorisation d’enregistrer l’audio dans le fichier **fichier AndroidManifest. xml** :

```xml
<manifest ...>
    ...
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
</manifest>
```

### <a name="ios"></a>iOS

L’exemple de projet définit une `IMicrophoneService` implémentation pour iOS appelée `iOSMicrophoneService` :

```csharp
[assembly: Dependency(typeof(iOSMicrophoneService))]
namespace CognitiveSpeechService.iOS.Services
{
    public class iOSMicrophoneService : IMicrophoneService
    {
        TaskCompletionSource<bool> tcsPermissions;

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();
            RequestMicPermission();
            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermission()
        {
            var session = AVAudioSession.SharedInstance();
            session.RequestRecordPermission((granted) =>
            {
                tcsPermissions.TrySetResult(granted);
            });
        }
    }
}
```

Le `iOSMicrophoneService` offre les fonctionnalités suivantes :

1. L' `Dependency` attribut inscrit la classe avec `DependencyService` .
1. La `GetPermissionAsync` méthode appelle `RequestMicPermissions` pour demander des autorisations à l’utilisateur de l’appareil.
1. La `RequestMicPermissions` méthode utilise l' `AVAudioSession` instance partagée pour demander des autorisations d’enregistrement.
1. La `OnRequestPermissionResult` méthode met à jour l' `TaskCompletionSource` instance avec la `bool` valeur fournie.

Enfin, l’application iOS **info. plist** doit inclure un message indiquant à l’utilisateur la raison pour laquelle l’application demande l’accès au microphone. Modifiez le fichier info. plist pour inclure les balises suivantes dans l' `<dict>` élément :

```xml
<plist>
    <dict>
        ...
        <key>NSMicrophoneUsageDescription</key>
        <string>Voice transcription requires microphone access</string>
    </dict>
</plist>
```

### <a name="uwp"></a>UWP

L’exemple de projet définit une `IMicrophoneService` implémentation pour UWP appelée `UWPMicrophoneService` :

```csharp
[assembly: Dependency(typeof(UWPMicrophoneService))]
namespace CognitiveSpeechService.UWP.Services
{
    public class UWPMicrophoneService : IMicrophoneService
    {
        public async Task<bool> GetPermissionAsync()
        {
            bool isMicAvailable = true;
            try
            {
                var mediaCapture = new MediaCapture();
                var settings = new MediaCaptureInitializationSettings();
                settings.StreamingCaptureMode = StreamingCaptureMode.Audio;
                await mediaCapture.InitializeAsync(settings);
            }
            catch(Exception ex)
            {
                isMicAvailable = false;
            }

            if(!isMicAvailable)
            {
                await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
            }

            return isMicAvailable;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            // intentionally does nothing
        }
    }
}
```

Le `UWPMicrophoneService` offre les fonctionnalités suivantes :

1. L' `Dependency` attribut inscrit la classe avec `DependencyService` .
1. La `GetPermissionAsync` méthode tente d’initialiser une `MediaCapture` instance. En cas d’échec, il lance une demande de l’utilisateur pour activer le microphone.
1. La `OnRequestPermissionResult` méthode existe pour satisfaire l’interface, mais elle n’est pas requise pour l’implémentation UWP.

Enfin, le **package UWP. appxmanifest** doit spécifier que l’application utilise le microphone. Double-cliquez sur le fichier Package. appxmanifest et sélectionnez l’option **microphone** sous l’onglet **fonctionnalités** dans Visual Studio 2019 :

[![Capture d’écran du manifeste dans Visual Studio 2019](speech-recognition-images/package-manifest-cropped.png)](speech-recognition-images/package-manifest.png#lightbox "Capture d’écran du manifeste dans Visual Studio 2019")

## <a name="test-the-application"></a>Test de l’application

Exécutez l’application et cliquez sur le bouton **transcrire** . L’application doit demander l’accès au microphone et commencer le processus de transcription. Le `ActivityIndicator` s’anime, indiquant que la transcription est active. À mesure que vous parlez, l’application diffuse en continu des données audio vers la ressource Azure Speech services, qui répondra avec du texte transcrit. Le texte transcrit s’affiche dans l' `Label` élément tel qu’il est reçu.

> [!NOTE]
> Les émulateurs Android ne parviennent pas à charger et à initialiser les bibliothèques de service vocal. Le test sur un appareil physique est recommandé pour la plateforme Android.

## <a name="related-links"></a>Liens connexes

- [Exemple de service Azure Speech](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)
- [Vue d’ensemble du service Azure Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)
- [Créer une ressource Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)
- [Démarrage rapide : Reconnaître la voix à partir d’un microphone](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone)