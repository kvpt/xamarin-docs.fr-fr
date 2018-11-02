---
title: Hello, Android - Démarrage rapide
description: Dans ce guide en deux parties, vous allez créer votre première application Xamarin.Android (à l’aide de Visual Studio ou de Visual Studio pour Mac), et approfondir votre compréhension des principes fondamentaux du développement d’applications Android avec Xamarin. Vous y découvrirez les différents outils, concepts et étapes qui sont nécessaires à la création et au déploiement d’une application Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: e359defee98ada74a76726673f6d3db21c0f4db3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121579"
---
# <a name="hello-android-quickstart"></a>Hello, Android - Démarrage rapide

_Dans ce guide en deux parties, vous allez générer votre première application Xamarin.Android avec Visual Studio, et acquérir les fondements du développement d’applications Android avec Xamarin._

Vous allez créer une application qui permet de convertir un numéro de téléphone alphanumérique (entré par l’utilisateur) en un numéro de téléphone numérique, puis de l’afficher à l’écran de l’utilisateur. L’application finale ressemble à ceci :

[![Capture d’écran de l’application une fois terminée](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

::: zone pivot="windows"

## <a name="windows-requirements"></a>Configuration requise pour Windows

Pour effectuer cette procédure pas à pas, vous avez besoin des éléments suivants :

- Windows 10.

- Visual Studio 2017 Community, Professional ou Enterprise (version 15.8 ou ultérieure).

::: zone-end
::: zone pivot="macos"

## <a name="macos-requirements"></a>Configuration requise pour macOS

Pour effectuer cette procédure pas à pas, vous avez besoin des éléments suivants :

- La dernière version de Visual Studio pour Mac

- Un Mac exécutant macOS High Sierra (10.13) ou une version ultérieure

::: zone-end

Cette procédure pas à pas part du principe que la dernière version de Xamarin.Android est installée, et qu’elle est actuellement exécutée sur la plateforme que vous avez choisie. Pour savoir comment installer Xamarin.Android, reportez-vous aux [Guides d’installation de Xamarin.Android](~/android/get-started/installation/index.md).

## <a name="configuring-emulators"></a>Configuration des émulateurs

Si vous utilisez l’émulateur Android, nous vous recommandons de le configurer de manière à utiliser l’accélération matérielle. Pour obtenir des instructions concernant la configuration de l’accélération matérielle, consultez [Accélération matérielle de l’émulateur Android](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

## <a name="create-the-project"></a>Créer le projet

::: zone pivot="windows"

Démarrez Visual Studio. Pour créer un projet, cliquez sur **Fichier >Nouveau > Projet**.

Dans la boîte de dialogue **Nouveau projet**, cliquez sur le modèle **Application Android**.
Nommez le nouveau projet `Phoneword`, puis cliquez sur **OK** :

[![Le nouveau projet se nomme Phoneword](hello-android-quickstart-images/vs/01-new-project-name-w158-sml.png)](hello-android-quickstart-images/vs/01-new-project-name-w158.png#lightbox)

Dans la boîte de dialogue **Application Android**, cliquez sur **Application vide** et sur **OK** pour créer le projet :

[![Sélectionnez le modèle d’application vide](hello-android-quickstart-images/vs/02-blank-app-w158-sml.png)](hello-android-quickstart-images/vs/02-blank-app-w158.png#lightbox)

## <a name="create-a-layout"></a>Créer une disposition

Une fois le projet créé, développez le dossier **Ressources**, puis le dossier **Disposition** dans l’**Explorateur de solutions**.
Double-cliquez sur **activity_main.axml** pour l’ouvrir dans Android Designer. Voici le fichier de disposition pour l’écran de l’application :

[![Ouvrir le fichier axml d’activité](hello-android-quickstart-images/vs/03-open-layout-w158-sml.png)](hello-android-quickstart-images/vs/03-open-layout-w158.png#lightbox)

Dans la **boîte à outils** (la zone située à gauche), entrez `text` dans le champ de recherche, puis faites glisser un widget **Text (Large)** dans l’aire de conception (la zone située au centre) :

[![Ajout du widget Text (Large)](hello-android-quickstart-images/vs/04-large-text-w158-sml.png)](hello-android-quickstart-images/vs/04-large-text-w158.png#lightbox)

Après avoir sélectionné le contrôle **Text (Large)** dans l’aire de conception, accédez au volet **Propriétés** pour remplacer la propriété `Text` du widget **Text (Large)** par la propriété `Enter a Phoneword:` :

[![Définition des propriétés du widget Text (Large)](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158.png#lightbox)

Faites glisser un widget **Plain Text** de la **boîte à outils** vers l’aire de conception, puis placez-le sous le widget **Text (Large)**. Vous ne pouvez pas placer le widget tant que vous n’avez pas déplacé le pointeur de souris vers un emplacement pouvant accepter le widget. Dans les captures d’écran ci-dessous, vous ne pouvez pas placer le widget (comme illustré à gauche) tant que vous n’avez pas déplacé le pointeur de souris juste en dessous du `TextView` précédent (comme illustré à droite) :

[![La souris indique où vous pouvez placer le widget](hello-android-quickstart-images/vs/06a-cant-drop-w158-sml.png)](hello-android-quickstart-images/vs/06a-cant-drop-w158.png#lightbox)

Quand le widget **Plain Text** (un widget `EditText`) est correctement placé, il s’affiche comme illustré dans la capture d’écran suivante :

[![Ajout du widget Plain Text](hello-android-quickstart-images/vs/06b-plain-text-w158-sml.png)](hello-android-quickstart-images/vs/06b-plain-text-w158.png#lightbox)

Après avoir sélectionné le widget **Plain Text** dans l’aire de conception, accédez au volet **Propriétés** pour remplacer la propriété `Id` du widget **Plain Text** par la propriété `@+id/PhoneNumberText`, et la propriété `Text` par la propriété `1-855-XAMARIN` :

[![Définition des propriétés du widget Plain Text](hello-android-quickstart-images/vs/07-add-properties-w158-sml.png)](hello-android-quickstart-images/vs/07-add-properties-w158.png#lightbox)

Faites glisser un **bouton** de la **boîte à outils** vers l’aire de conception, puis placez-le sous le widget **Plain Text** :

[![Glissement du widget Button vers l’aire de conception](hello-android-quickstart-images/vs/08-drag-button-w158-sml.png)](hello-android-quickstart-images/vs/08-drag-button-w158.png#lightbox)

Après avoir sélectionné **Button** dans l’aire de conception, accédez au volet **Propriétés** pour remplacer la propriété `Text` par la propriété `Translate`, et la propriété `Id` par la propriété `@+id/TranslateButton` :

[![Définition des propriétés du widget Button](hello-android-quickstart-images/vs/09-translate-button-w158-sml.png)](hello-android-quickstart-images/vs/09-translate-button-w158.png#lightbox)

Faites glisser le widget **TextView** de la **boîte à outils** vers l’aire de conception, puis placez-le sous le widget **Button**. Remplacez la propriété `Text` de **TextView** par une chaîne vide, puis affectez à sa propriété `Id` la valeur `@+id/TranslatedPhoneword` :

[![Définition des propriétés du widget TextView](hello-android-quickstart-images/vs/10-textview-properties-w158-sml.png)](hello-android-quickstart-images/vs/10-textview-properties-w158.png#lightbox)

Enregistrez votre travail en appuyant sur **CTRL + S**.

## <a name="write-some-code"></a>Écrire du code

L’étape suivante consiste à ajouter du code pour convertir des numéros de téléphone alphanumériques en numéros de téléphone numériques. Ajoutez un fichier au projet en cliquant sur le projet **Phoneword** dans l**l’Explorateur de solutions**, puis en sélectionnant **Ajouter > Nouvel élément...** , comme indiqué ci-dessous :

[![Ajout d’un nouvel élément](hello-android-quickstart-images/vs/12-add-new-item-w158-sml.png)](hello-android-quickstart-images/vs/12-add-new-item-w158.png#lightbox)

Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Visual C# > Code > Fichier de code**, puis nommez le nouveau fichier de code **PhoneTranslator.cs** :

[![Ajout du fichier PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-w158-sml.png)](hello-android-quickstart-images/vs/14-add-class-w158.png#lightbox)

Cette opération crée une nouvelle classe C# vide. Insérez le code suivant dans ce fichier :

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Enregistrez les modifications apportées au fichier **PhoneTranslator.cs** en cliquant sur **Fichier > Enregistrer** (ou en appuyant sur **CTRL + S**), puis fermez le fichier.

## <a name="wire-up-the-user-interface"></a>Structuration de l’interface utilisateur

L’étape suivante consiste à ajouter du code pour structurer l’interface utilisateur en insérant du code de stockage dans la classe `MainActivity`. Commencez par configurer le bouton **Translate**. Dans la classe `MainActivity`, recherchez la méthode `OnCreate`. L’étape suivante consiste à ajouter le code du bouton à l’intérieur de `OnCreate`, sous les appels `base.OnCreate(savedInstanceState)` et `SetContentView(Resource.Layout.activity_main)`. Tout d’abord, modifiez le code du modèle pour que la méthode `OnCreate` ressemble à ceci :

```csharp
using Android.App;
using Android.OS;
using Android.Support.V7.App;
using Android.Runtime;
using Android.Widget;

namespace Phoneword
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);

            // New code will go here
        }
    }
}
```

Récupérez une référence aux contrôles qui ont été créés dans le fichier de disposition via Android Designer. Ajoutez le code suivant dans la méthode `OnCreate`, immédiatement après l’appel à `SetContentView` :

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneword);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Ajoutez le code de réponse à l’actionnement du bouton **Translate**.
Ajoutez le code suivant à la méthode `OnCreate` (après les lignes ajoutées à l’étape précédente) :

```csharp
// Add code to translate number
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    string translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Enregistrez votre travail en sélectionnant **Fichier > Enregistrer tout** (ou en appuyant sur **CTRL-MAJ- S**). Ensuite, générez l’application en sélectionnant **Générer > Regénérer la solution** (ou en appuyant sur **CTRL-MAJ-B**). 

En cas d’erreurs, examinez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que l’application soit générée. S’il s’agit d’une erreur de génération, telle que _(Ressource) n’existe pas dans le contexte actuel_, vérifiez que le nom de l’espace de noms dans **MainActivity.cs** correspond bien au nom du projet (`Phoneword`), puis regénérez entièrement la solution. Si des erreurs de build continuent de se produire, vérifiez que vous avez installé les dernières mises à jour de Visual Studio.

## <a name="set-the-app-name"></a>Définir le nom de l’application

Votre application doit maintenant être opérationnelle &ndash; Il est temps de définir son nom. Développez le dossier **valeur** (dans le dossier **Ressources**), puis ouvrez le fichier **strings.xml**. Remplacez la chaîne du nom de l’application par `Phone Word`, comme indiqué ici :

```xml
<resources>
    <string name="app_name">Phone Word</string>
    <string name="action_settings">Settings</string>
</resources>
```

## <a name="run-the-app"></a>Exécuter l'application

Testez l’application en l’exécutant sur un appareil ou un émulateur Android.
Appuyez sur le bouton **TRANSLATE** pour convertir **1-855-XAMARIN** en numéro de téléphone :

[![Capture d’écran de l’application en cours d’exécution](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

Pour exécuter l’application sur un appareil Android, consultez les informations relatives à la [configuration de votre appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end
::: zone pivot="macos"

Lancez Visual Studio pour Mac à partir du dossier **Applications** ou à partir de **Spotlight**.

Cliquez sur **Nouveau projet** pour créer un projet.

Dans la boîte de dialogue **Choisir un modèle pour votre nouveau projet**, cliquez sur **Android > Application**, puis sélectionnez le modèle **Application Android**. Cliquez sur **Suivant**.

[![Sélection du modèle Application Android](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

Dans la boîte de dialogue **Configurer votre application Android**, attribuez à la nouvelle application le nom de `Phoneword`, puis cliquez sur **Suivant**.

[![Configuration de l’application Android](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

Dans la boîte de dialogue **Configurer votre application Android**, conservez le nom de la solution et du projet (`Phoneword`), puis cliquez sur **Créer** pour créer le projet.

## <a name="create-a-layout"></a>Créer une disposition

Une fois le projet créé, développez le dossier **Ressources**, puis le dossier **Disposition** dans le **Panneau Solutions**.
Double-cliquez sur **Main.axml** pour l’ouvrir dans Android Designer. Voici le fichier de disposition de l’écran lorsqu’il est affiché dans Android Designer :

[![Ouverture du fichier Main.axml](hello-android-quickstart-images/xs/05-open-layout-sml.png)](hello-android-quickstart-images/xs/05-open-layout.png#lightbox)

Sélectionnez le **bouton** **Hello World, Click Me!** dans l’aire de conception, puis supprimez-le en appuyant sur la touche **Suppr**. 

Dans la **boîte à outils** (la zone située à droite), entrez `text` dans le champ de recherche, puis faites glisser un widget **Text (Large)** dans l’aire de conception (la zone située au centre) :

[![Ajout du widget Text (Large)](hello-android-quickstart-images/xs/06-large-text-sml.png)](hello-android-quickstart-images/xs/06-large-text.png#lightbox)

Après avoir sélectionné le widget **Text (Large)** dans l’aire de conception, accédez au panneau **Propriétés** pour remplacer la propriété `Text` du widget **Text (Large)** par la propriété `Enter a Phoneword:`, comme indiqué ci-dessous :

[![Définition des propriétés du widget Text (Large)](hello-android-quickstart-images/xs/07-enter-a-phoneword-sml.png)](hello-android-quickstart-images/xs/07-enter-a-phoneword.png#lightbox)

Ensuite, faites glisser un widget **Plain Text** de la **boîte à outils** vers l’aire de conception, puis placez-le sous le widget **Text (Large)**. Notez que vous pouvez utiliser le champ de recherche pour localiser les widgets plus facilement à l’aide de leur nom :

[![Ajout du widget Plain Text](hello-android-quickstart-images/xs/08-plain-text-sml.png)](hello-android-quickstart-images/xs/08-plain-text.png#lightbox)

Après avoir sélectionné le widget **Plain Text** dans l’aire de conception, accédez au panneau **Propriétés** pour remplacer la propriété `Id` du widget **Plain Text** par la propriété `@+id/PhoneNumberText`, et la propriété `Text` par la propriété `1-855-XAMARIN` :

[![Définition des propriétés du widget Plain Text](hello-android-quickstart-images/xs/09-add-properties-sml.png)](hello-android-quickstart-images/xs/09-add-properties.png#lightbox)

Faites glisser un **bouton** de la **boîte à outils** vers l’aire de conception, puis placez-le sous le widget **Plain Text** :

[![Ajout d’un bouton](hello-android-quickstart-images/xs/10-drag-button-sml.png)](hello-android-quickstart-images/xs/10-drag-button.png#lightbox)

Après avoir sélectionné le widget **Button** dans l’aire de conception, accédez au panneau **Propriétés** pour remplacer la propriété `Id` du widget **Button** par la propriété `@+id/TranslateButton`, et la propriété `Text` par la propriété `Translate` :

[![Configuration du bouton Translate](hello-android-quickstart-images/xs/11-translate-button-sml.png)](hello-android-quickstart-images/xs/11-translate-button.png#lightbox)

Faites glisser le widget **TextView** de la **boîte à outils** vers l’aire de conception, puis placez-le sous le widget **Button**. Après avoir sélectionné **TextView**, définissez la propriété `id` du widget **TextView** sur `@+id/TranslatedPhoneWord`, et remplacez la valeur `text` par une chaîne vide :

[![Définition des propriétés du widget TextView](hello-android-quickstart-images/xs/12-textview-properties-sml.png)](hello-android-quickstart-images/xs/12-textview-properties.png#lightbox)    

Enregistrez votre travail en appuyant sur **&#8984; + S**.

## <a name="write-some-code"></a>Écrire du code

À présent, ajoutez du code pour convertir des numéros de téléphone alphanumériques en numéros de téléphone numériques. Pour ajouter un fichier au projet, cliquez sur l’icône représentant une roue dentée en regard du projet **Phoneword** dans le **Panneau Solutions**, puis sélectionnez **Ajouter > Nouveau fichier...**  :

[![Ajouter un nouveau fichier au projet](hello-android-quickstart-images/xs/14-add-new-file-sml.png)](hello-android-quickstart-images/xs/14-add-new-file.png#lightbox)

Dans la boîte de dialogue **Nouveau fichier**, sélectionnez **Général > Classe vide**, nommez le nouveau fichier **PhoneTranslator**, puis cliquez sur **Nouveau**. Cette opération crée une nouvelle classe C# vide.

Supprimez tout le code de modèle qui se trouve dans la nouvelle classe, puis remplacez-le par le code suivant :

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Enregistrez les modifications apportées au fichier **PhoneTranslator.cs** en sélectionnant **Fichier > Enregistrer** (ou en appuyant sur **&#8984; + S**), puis fermez le fichier. Vérifiez qu’aucune erreur de compilation ne s’est produite lorsque vous avez regénéré la solution.

## <a name="wire-up-the-user-interface"></a>Structuration de l’interface utilisateur

L’étape suivante consiste à ajouter du code pour structurer l’interface utilisateur en ajoutant du code de stockage dans la classe `MainActivity`.
Dans le **Panneau Solutions**, double-cliquez sur **MainActivity.cs** pour l’ouvrir.

Commencez par ajouter un gestionnaire d’événements au bouton **Translate**. Dans la classe `MainActivity`, recherchez la méthode `OnCreate`. Ajoutez le code du bouton à l’intérieur de `OnCreate`, sous les appels `base.OnCreate(bundle)` et `SetContentView (Resource.Layout.Main)`. Supprimez tous les boutons de gestion de code existants (par exemple, le code qui référence `Resource.Id.myButton` et crée un gestionnaire de clic pour cela) de sorte que la méthode `OnCreate` se présente ainsi :

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Our code will go here
        }
    }
}
```

Ensuite, vous avez besoin d’une référence aux contrôles qui ont été créés dans le fichier de disposition via Android Designer. Ajoutez le code suivant dans la méthode `OnCreate`, immédiatement après l’appel à `SetContentView` :

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Ajoutez le code de réponse à l’actionnement du bouton **Translate** en insérant le code suivant dans la méthode `OnCreate` (après les lignes ajoutées à l’étape précédente) :

```csharp
// Add code to translate number
string translatedNumber = string.Empty;

translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Enregistrez votre travail, puis générez l’application en sélectionnant **Générer > Générer tout** (ou en appuyant sur **&#8984; + B**). Si l’application est compilée, vous recevez un message de confirmation en haut de la fenêtre Visual Studio pour Mac :

En cas d’erreurs, examinez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que l’application soit générée. S’il s’agit d’une erreur de génération, telle que _(Ressource) n’existe pas dans le contexte actuel_, vérifiez que le nom de l’espace de noms dans **MainActivity.cs** correspond bien au nom du projet (`Phoneword`), puis regénérez entièrement la solution. Si vous obtenez encore des erreurs de génération, vérifiez que vous avez installé les dernières mises à jour Xamarin.Android et Visual Studio pour Mac.

## <a name="set-the-label-and-app-icon"></a>Définir l’étiquette et l’icône d’application

Votre application doit maintenant être opérationnelle : il est temps de lui ajouter la touche finale ! Commencez par modifier le `Label` pour `MainActivity`.
Le `Label` est ce qu’Android affiche en haut de l’écran pour permettre aux utilisateurs de savoir où ils se trouvent dans l’application. En haut de la classe `MainActivity`, remplacez `Label` par `Phone Word`, comme indiqué ici :

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

À présent, nous allons définir l’icône de l’application. Par défaut, Visual Studio pour Mac fournit une icône pour le projet. Supprimez ces fichiers de la solution, et remplacez-les par une autre icône. Développez le dossier **Ressources** dans le **Panneau Solutions**. Notez qu’il y a cinq dossiers dont le nom est précédé de **mipmap-**, et que chacun de ces dossiers contient un fichier **Icon.png** :

[![Dossiers mipmap- et fichiers Icon.png](hello-android-quickstart-images/xs/23-mipmap-folders-sml.png)](hello-android-quickstart-images/xs/23-mipmap-folders.png#lightbox)

Vous devez supprimer tous les fichiers d’icône du projet. Cliquez avec le bouton droit sur chaque fichier **Icon.png**, puis sélectionnez **Supprimer** dans le menu contextuel :

[![Suppression des fichiers Icon.png par défaut](hello-android-quickstart-images/xs/23-delete-icon-sml.png)](hello-android-quickstart-images/xs/23-delete-icon.png#lightbox)

Dans la boîte de dialogue, cliquez sur le bouton **Supprimer**.

Ensuite, téléchargez et décompressez les [icônes de l’application Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Ce fichier .zip contient les icônes de l’application. Toutes les icônes sont visuellement identiques. Toutefois, chacune a une résolution différente, adaptée à la densité d’écran des différents appareils sur lesquels l’application peut être utilisée.  Ces fichiers d’icône doivent être copiés dans le projet Xamarin.Android. Dans Visual Studio pour Mac, dans le **Panneau Solutions**, cliquez avec le bouton droit sur le dossier **mipmap-hdpi**, puis sélectionnez **Ajouter > Ajouter des fichiers** :

[![Ajout de fichiers](hello-android-quickstart-images/xs/24-add-files-sml.png)](hello-android-quickstart-images/xs/24-add-files.png#lightbox)

Dans la boîte de dialogue de sélection, accédez au dossier non décompressé qui contient les icônes d’application Xamarin, puis ouvrez le dossier **mipmap-hdpi**. Sélectionnez **Icon.png**, puis cliquez sur **Ouvrir**.

Dans la boîte de dialogue **Ajouter un fichier à un dossier**, sélectionnez **Copier le fichier dans le répertoire**, puis cliquez sur **OK** :

[![Boîte de dialogue Copier le fichier dans le répertoire](hello-android-quickstart-images/xs/26-copy-to-directory-sml.png)](hello-android-quickstart-images/xs/26-copy-to-directory.png#lightbox)

Répétez ces étapes pour chaque dossier **mipmap-**, jusqu’à ce que le contenu des dossiers d’icônes d’application Xamarin **mipmap-** soit copié vers les dossiers **mipmap-** correspondants dans le projet **Phoneword**.

Une fois que toutes les icônes sont copiées dans le projet Xamarin.Android, ouvrez la boîte de dialogue **Options du projet** en cliquant avec le bouton droit sur le projet dans le **Panneau Solutions**. Sélectionnez **Générer > Application Android**, puis sélectionnez `@mipmap/icon` dans la zone de liste modifiable **Icône d’application** :

[![Définition de l’icône du projet](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

## <a name="run-the-app"></a>Exécuter l'application

Enfin, testez l’application en l’exécutant sur un émulateur ou un appareil Android, et en convertissant un numéro de téléphone alphanumérique :

[![Capture d’écran de l’application une fois terminée](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

Pour exécuter l’application sur un appareil Android, consultez les informations relatives à la [configuration de votre appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end

Félicitations, vous avez terminé votre première application Xamarin.Android !
Nous allons maintenant étudier en détail les outils et les compétences que nous venons d’utiliser. À suivre : [Hello, Android - En profondeur](~/android/get-started/hello-android/hello-android-deepdive.md).

## <a name="related-links"></a>Liens connexes

- [Icônes d’application Xamarin Android (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (exemple)](https://developer.xamarin.com/samples/monodroid/Phoneword)
