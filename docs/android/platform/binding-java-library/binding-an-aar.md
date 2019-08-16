---
title: Liaison d’un fichier .AAR
description: Cette procédure pas à pas fournit des instructions détaillées sur la création d’une bibliothèque de liaisons Java Xamarin. Android à partir d’un Android. Fichier AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 4c612faca90d4b2b4e44cfbb53ff65ec07fcdfd1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524839"
---
# <a name="binding-an-aar"></a>Liaison d’un fichier .AAR

_Cette procédure pas à pas fournit des instructions détaillées sur la création d’une bibliothèque de liaisons Java Xamarin. Android à partir d’un Android. Fichier AAR._


## <a name="overview"></a>Présentation

L' *Archive Android (. AAR)* est le format de fichier pour les bibliothèques Android.
Pièce. Le fichier AAR est un. Archive ZIP qui contient les éléments suivants:

- Code Java compilé
- ID de ressource
- Ressources
- Métadonnées (par exemple, déclarations d’activité, autorisations)

Dans ce guide, nous allons parcourir les bases de la création d’une bibliothèque de liaisons pour une seule. Fichier AAR. Pour obtenir une vue d’ensemble de la liaison de bibliothèque Java en général (à l’aide d’un exemple de code de base), consultez [liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Un projet de liaison ne peut inclure qu’un seul. Fichier AAR. Si. AAR les dépendances sur d’autres. AAR, ces dépendances doivent être contenues dans leur propre projet de liaison, puis référencées. Consultez le [bogue 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Procédure pas à pas

Nous allons créer une bibliothèque de liaisons pour un exemple de fichier d’archive Android créé dans Android Studio, [textanalyzer. AAR](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Voici. AAR contient une `TextCounter` classe avec des méthodes statiques qui comptent le nombre de voyelles et de consonnes dans une chaîne. En outre, **textanalyzer. AAR** contient une ressource image permettant d’afficher les résultats du comptage.

Nous allons utiliser les étapes suivantes pour créer une bibliothèque de liaisons à partir du. Fichier AAR:

1. Créez un projet de bibliothèque de liaisons Java.

2. Ajoutez un seul. Fichier AAR dans le projet. Un projet de liaison ne peut contenir qu’un seul. AAR.

3. Définissez l’action de génération appropriée pour le. Fichier AAR.

4. Choisissez une version cible de .NET Framework que le. AAR prend en charge.

5. Générez la bibliothèque de liaisons.

Une fois la bibliothèque de liaisons créée, nous allons développer une petite application Android qui invite l’utilisateur à entrer une chaîne de texte, appelle. Méthodes AAR pour analyser le texte, récupère l’image à partir de. AAR et affiche les résultats avec l’image.

L’exemple d’application accède à `TextCounter` la classe de **textanalyzer. AAR**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

En outre, cet exemple d’application va récupérer et afficher une ressource d’image empaquetée dans **textanalyzer. AAR**:

[![Image de singe Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Cette ressource image se trouve dans **textanalyzer. AAR**, à l’adresse **res/Drawable/singe. png** .



### <a name="creating-the-bindings-library"></a>Création de la bibliothèque de liaisons

Avant de commencer à suivre les étapes ci-dessous, téléchargez l’exemple de fichier d’archive [textanalyzer. AAR](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android:

1. Créez un projet de bibliothèque de liaisons en commençant par le modèle Bibliothèque de liaisons Android. Vous pouvez utiliser Visual Studio pour Mac ou Visual Studio (les captures d’écran ci-dessous illustrent Visual Studio, mais Visual Studio pour Mac sont très similaires). Nommez la solution **AarBinding**:

    [![Créer un projet AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w157.png)](binding-an-aar-images/01-new-bindings-library-vs.w157.png#lightbox)

2. Le modèle comprend un dossier **jar** dans lequel vous ajoutez votre. AAR (s) au projet de bibliothèque de liaisons. Cliquez avec le bouton droit sur le dossier **jar** et sélectionnez **Ajouter > élément existant**:

    [![Ajouter un élément existant](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3. Accédez au fichier **textanalyzer. AAR** téléchargé précédemment, sélectionnez-le, puis cliquez sur **Ajouter**:

    [![Ajouter textanalayzer. AAR](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4. Vérifiez que le fichier **textanalyzer. AAR** a été correctement ajouté au projet:

    [![Le fichier textanalyzer. AAR a été ajouté](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. Définissez l’action de génération pour **textanalyzer. AAR** sur `LibraryProjectZip`. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur **textanalyzer. AAR** pour définir l’action de génération. Dans Visual Studio, l’action de génération peut être définie dans le volet **Propriétés** .):

    [![Définition de l’action de génération textanalyzer. AAR sur LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. Ouvrez les propriétés du projet pour configurer la version *cible de .NET Framework*. Si. AAR utilise des API Android, définit le Framework cible au niveau de l’API que le. AAR attend. (Pour plus d’informations sur le paramètre Framework cible et les niveaux d’API Android en général, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).)

    Définissez le niveau d’API cible pour votre bibliothèque de liaisons. Dans cet exemple, nous sommes libres d’utiliser le niveau d’API de plateforme le plus récent (niveau d’API 23), car notre **textanalyzer** n’a pas de dépendance sur les API Android:

    [![Définition du niveau cible sur l’API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. Générez la bibliothèque de liaisons. Le projet de bibliothèque de liaisons doit être généré correctement et produire une sortie. DLL à l’emplacement suivant: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>Utilisation de la bibliothèque de liaisons

Pour utiliser ce. DLL dans votre application Xamarin. Android, vous devez d’abord ajouter une référence à la bibliothèque de liaisons. Procédez comme suit:

1. Nous créons cette application dans la même solution que la bibliothèque de liaisons pour simplifier cette procédure pas à pas. (L’application qui utilise la bibliothèque de liaisons peut également résider dans une autre solution.) Créer une application Xamarin. Android: cliquez avec le bouton droit sur la solution et sélectionnez **Ajouter nouveau projet**. Nommez le nouveau projet **BindingTest**:

    [![Créer un projet BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. Cliquez avec le bouton droit sur le nœud **références** du projet **BindingTest** et sélectionnez **Ajouter une référence...** :

    [![Cliquez sur Ajouter une référence.](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. Sélectionnez le projet **AarBinding** créé précédemment, puis cliquez sur **OK**:

    [![Vérifier le projet de liaison AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. Ouvrez le nœud **références** du projet **BindingTest** pour vérifier que la référence **AarBinding** est présente:

    [![AarBinding est listé sous Références](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Si vous souhaitez afficher le contenu du projet de bibliothèque de liaisons, vous pouvez double-cliquer sur la référence pour l’ouvrir dans l' **Explorateur d’objets**. Vous pouvez voir le contenu mappé de l' `Com.Xamarin.Textcounter` espace de noms (mappé à partir du package java `com.xamarin.textanalyzezr` ) et vous pouvez afficher les membres de `TextCounter` la classe:

[![Affichage de l’Explorateur d’objets](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

La capture d’écran ci- `TextAnalyzer` dessus met en évidence les deux méthodes que `NumConsonants` l’exemple d’application appellera: ( `numConsonants` qui encapsule la `NumVowels` méthode Java sous-jacente) et `numVowels` (qui encapsule la méthode Java sous-jacente).



### <a name="accessing-aar-types"></a>L’accès à. Types AAR

Une fois que vous avez ajouté une référence à votre application qui pointe vers la bibliothèque de liaisons, vous pouvez accéder aux types Java dans le. AAR comme vous le C# C# feriez pour accéder aux types (grâce aux wrappers). C#le code d’application `TextAnalyzer` peut appeler des méthodes, comme illustré dans cet exemple:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

Dans l’exemple ci-dessus, nous appelons des méthodes statiques dans la `TextCounter` classe. Toutefois, vous pouvez également instancier des classes et appeler des méthodes d’instance. Par exemple, si votre. AAR encapsule une classe appelée `Employee` qui a la méthode `buildFullName`d’instance, vous pouvez `MyClass` l’instancier et l’utiliser comme indiqué ici:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

Les étapes suivantes ajoutent du code à l’application afin qu’il invite l’utilisateur à entrer du `TextCounter` texte, utilise pour analyser le texte, puis affiche les résultats.

Remplacez la disposition **BindingTest** (**main. AXML**) par le code XML suivant. Cette disposition a un `EditText` pour l’entrée de texte et deux boutons pour initier des nombres de voyelles et de consonnes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

Remplacez le contenu de **MainActivity.cs** par le code suivant. Comme illustré dans cet exemple, les gestionnaires d’événements de bouton appellent `TextCounter` des méthodes encapsulées qui résident dans le. AAR et utilisent des toasts pour afficher les résultats. Notez l' `using` instruction pour l’espace de noms de la bibliothèque liée (dans ce `Com.Xamarin.Textcounter`cas,):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

Compilez et exécutez le projet **BindingTest** . L’application démarre et présente la capture d’écran à gauche (le `EditText` est initialisé avec du texte, mais vous pouvez cliquer dessus pour le modifier). Quand vous appuyez sur **Count voyelles**, un toast affiche le nombre de voyelles comme indiqué à droite:

[![Captures d’écran de l’exécution de BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Essayez de cliquer sur le bouton consonnes de **nombre** . En outre, vous pouvez modifier la ligne de texte et appuyer à nouveau sur ces boutons pour tester différents nombres de voyelles et de consonnes.



### <a name="accessing-aar-resources"></a>L’accès à. Ressources AAR

L’outil Xamarin fusionne les données **R** à partir du. AAR dans la classe de **ressources** de votre application. Par conséquent, vous pouvez accéder à. AAR des ressources de votre disposition (et du code-behind) de la même façon que vous accédez aux ressources qui se trouvent dans le chemin d’accès aux **ressources** de votre projet.

Pour accéder à une ressource d’image, vous utilisez le nom de la **ressource. dessinable** pour l’image compressée dans le. AAR. Par exemple, vous pouvez référencer **image. png** dans le. Fichier AAR à l' `@drawable/image`aide de:

```xml
<ImageView android:src="@drawable/image" ... />
```

Vous pouvez également accéder aux mises en page des ressources qui résident dans le. AAR. Pour ce faire, vous utilisez le nom **Resource. Layout** pour la disposition compressée à l’intérieur du. AAR. Par exemple :

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

L’exemple **textanalyzer. AAR** contient un fichier image qui réside dans **res/Drawable/singe. png**. Nous allons accéder à cette ressource d’image et l’utiliser dans notre exemple d’application:

Modifiez la disposition de **BindingTest** (**main. AXML**) et ajoutez `ImageView` un à la fin du `LinearLayout` conteneur. Cela `ImageView` affiche l’image trouvée dans **@drawable/monkey** ; cette image sera chargée à partir de la section des ressources de **textanalyzer. AAR**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

Compilez et exécutez le projet **BindingTest** . L’application démarre et présente la capture d’écran sur la &ndash; gauche lorsque vous appuyez sur **compter**les consonnes, les résultats s’affichent comme indiqué à droite:

[![BindingTest affichant le nombre de consonnes](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


Félicitations ! Vous avez correctement lié une bibliothèque Java. AAR!



## <a name="summary"></a>Récapitulatif

Dans cette procédure pas à pas, nous avons créé une bibliothèque de liaisons pour un. Fichier AAR, ajout de la bibliothèque de liaisons à une application de test minimale et exécution de l’application pour vérifier C# que notre code peut appeler du code Java résidant dans le. Fichier AAR.
En outre, nous avons étendu l’application pour accéder et afficher une ressource d’image qui réside dans le. Fichier AAR.


## <a name="related-links"></a>Liens associés

- [Création d’une bibliothèque de liaisons Java (vidéo)](https://university.xamarin.com/classes#10090)
- [Liaison d’un fichier .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bogue 44573-un projet ne peut pas lier plusieurs fichiers. AAR](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
