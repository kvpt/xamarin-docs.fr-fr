---
title: Liaison d’un fichier .AAR
description: Ce passage à pas fournit des instructions étape par étape pour créer une bibliothèque de liaisons Java Xamarin.Android à partir d’un Android . Fichier AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 103720c8cb47b1ac4cfe5cfadeb6b18828318ad3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73618546"
---
# <a name="binding-an-aar"></a>Liaison d’un fichier .AAR

_Ce passage à pas fournit des instructions étape par étape pour créer une bibliothèque de liaisons Java Xamarin.Android à partir d’un Android . Fichier AAR._

## <a name="overview"></a>Vue d’ensemble

Les *Archives Android (. AAR)* fichier est le format de fichier pour les bibliothèques Android.
Un. Fichier AAR est un . Archive ZIP qui contient ce qui suit:

- Code Java compilé
- ID de ressource
- Ressources
- Métadonnées (par exemple, Déclarations d’activité, autorisations)

Dans ce guide, nous allons passer à travers les bases de la création d’une bibliothèque de liaisons pour un seul . Fichier AAR. Pour un aperçu de la liaison de bibliothèque Java en général (avec un exemple de code de base), voir [Binding a Java Library](~/android/platform/binding-java-library/index.md).

> [!IMPORTANT]
> Un projet contraignant ne peut inclure qu’un seul . Fichier AAR. Si le . AAR dépend d’autres . AAR, alors ces dépendances devraient être contenues dans leur propre projet contraignant et ensuite référencées. Voir [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).

## <a name="walkthrough"></a>Procédure pas à pas

Nous allons créer une bibliothèque de liaisons pour un exemple fichier d’archives Android qui a été créé dans Android Studio, [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Ce. AAR contient `TextCounter` une classe avec des méthodes statiques qui comptent le nombre de voyelles et de consonnes dans une chaîne. En outre, **textanalyzer.aar** contient une ressource d’image pour aider à afficher les résultats de comptage.

Nous utiliserons les étapes suivantes pour créer une bibliothèque de liaisons à partir de la . Fichier AAR:

1. Créez un nouveau projet de bibliothèque de liaisons Java.

2. Ajouter un seul . Fichier AAR au projet. Un projet contraignant ne peut contenir qu’un seul . Aar.

3. Définir l’action de construction appropriée pour le . Fichier AAR.

4. Choisissez un cadre cible que le . Soutiens AAR.

5. Construire la bibliothèque de liaisons.

Une fois que nous avons créé la bibliothèque de liaisons, nous allons développer une petite application Android qui invite l’utilisateur pour une chaîne de texte, les appels . AAR méthodes pour analyser le texte, récupère l’image de la . AAR, et affiche les résultats avec l’image.

L’application d’échantillon accédera à la `TextCounter` classe de **textanalyzer.aar**:

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

En outre, cette application d’échantillon récupérera et affichera une ressource d’image qui est emballée dans **textanalyzer.aar**:

[![Image de singe de Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Cette ressource d’image réside à **res/drawable/monkey.png** dans **textanalyzer.aar**.

### <a name="creating-the-bindings-library"></a>Création de la Bibliothèque des liaisons

Avant de commencer avec les étapes ci-dessous, s’il vous plaît télécharger l’exemple [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) fichier d’archives Android:

1. Créez un nouveau projet de bibliothèque de liaisons en commençant par le modèle de la bibliothèque de liaisons Android. Vous pouvez utiliser visual Studio pour Mac ou Visual Studio (les captures d’écran ci-dessous montrent Visual Studio, mais Visual Studio pour Mac est très similaire). Nommez la solution **AarBinding**:

    [![Créer un projet AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. Le modèle comprend un dossier **Jars** où vous ajoutez votre . AAR(s) au projet de la Bibliothèque des liaisons. Cliquez à droite sur le dossier **Jars** et sélectionnez **Ajouter > élément existant**:

    [![Ajouter l’article existant](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. Naviguez vers le fichier **textanalyzer.aar** téléchargé plus tôt, sélectionnez-le et cliquez sur **Ajouter**:

    [![Ajouter textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. Vérifiez que le fichier **textanalyzer.aar** a été ajouté avec succès au projet :

    [![Le fichier textanalyzer.aar a été ajouté](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. Définir l’action build pour **textanalyzer.aar** à `LibraryProjectZip`. Dans Visual Studio for Mac, cliquez à droite **textanalyzer.aar** pour définir l’action Build. Dans Visual Studio, l’action Build peut être placée dans le volet **Propriétés** :

    [![Réglage du textanalyzer.aar construire l’action à LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. Ouvrez les propriétés du projet pour configurer le *cadre cible*. Si le . AAR utilise toutes les API Android, définir le cadre cible au niveau API que le . AAR attend. (Pour plus d’informations sur le paramètre du cadre cible et les niveaux d’API Android en général, voir [Comprendre les niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).)

    Définissez le niveau d’API cible pour votre bibliothèque de liaisons. Dans cet exemple, nous sommes libres d’utiliser le dernier niveau d’API plate-forme (niveau API 23) parce que notre **textanalyzer** n’a pas de dépendance aux API Android:

    [![Fixer le niveau cible à l’API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. Construire la bibliothèque de liaisons. Le projet de la Bibliothèque des liaisons devrait être construit avec succès et produire une production. DLL à l’endroit suivant: **AarBinding/bin/Debug/AarBinding.dll**

### <a name="using-the-bindings-library"></a>Utilisation de la bibliothèque de liaisons

Pour consommer cela . DLL dans votre application Xamarin.Android, vous devez d’abord ajouter une référence à la bibliothèque de liaisons. Utiliser les étapes suivantes :

1. Nous créons cette application dans la même solution que la bibliothèque de liaisons pour simplifier cette procédure pas à pas. (L’application qui consomme la bibliothèque de liaisons pourrait également résider dans une solution différente.) Créez une nouvelle application Xamarin.Android : cliquez à droite sur la solution et **sélectionnez Ajouter un nouveau projet**. Nommez le nouveau projet **BindingTest**:

    [![Créer un nouveau projet BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. Cliquez à droite sur le nœud **Références** du projet **BindingTest** et **sélectionnez Ajouter référence...**:

    [![Cliquez sur Ajouter la référence](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. Sélectionnez le projet **AarBinding** créé plus tôt et cliquez **sur OK**:

    [![Vérifier le projet de liaison AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. Ouvrez le nœud **Références** du projet **BindingTest** pour vérifier que la référence **AarBinding** est présente :

    [![AarBinding est répertorié sous Références](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

Si vous souhaitez consulter le contenu du projet Binding Library, vous pouvez cliquer à deux clics sur la référence pour l’ouvrir dans le **navigateur d’objets**. Vous pouvez voir le contenu `Com.Xamarin.Textcounter` cartographié de l’espace de nom (cartographié à partir du paquet Java) `com.xamarin.textanalyzezr` et vous pouvez voir les membres de la `TextCounter` classe:

[![Affichage du navigateur d’objets](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

La capture d’écran ci-dessus met en évidence les deux `TextAnalyzer` méthodes que l’application d’exemple appellera: `NumConsonants` (qui enveloppe la `numConsonants` méthode Java sous-jacente), et `NumVowels` (qui enveloppe la `numVowels` méthode Java sous-jacente).

### <a name="accessing-aar-types"></a>Accéder. AAR Types

Après avoir ajouté une référence à votre application qui pointe vers la bibliothèque de liaison, vous pouvez accéder aux types Java dans le . AAR comme vous accéderiez aux types C (grâce aux emballages CMD). Le code de `TextAnalyzer` l’application CMD peut appeler des méthodes telles qu’illustrées dans cet exemple :

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

Dans l’exemple ci-dessus, nous `TextCounter` appelons des méthodes statiques dans la classe. Cependant, vous pouvez également instantané les classes et les méthodes d’instance d’appel. Par exemple, si votre . AAR enveloppe une `Employee` classe appelée qui `buildFullName`a la méthode `MyClass` d’instance , vous pouvez instantané et l’utiliser comme on le voit ici:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

Les étapes suivantes ajoutent du code à l’application afin `TextCounter` qu’elle invite l’utilisateur pour le texte, utilise pour analyser le texte, puis affiche les résultats.

Remplacez la mise en page **BindingTest** **(Main.axml**) par le XML suivant. Cette mise `EditText` en page a une entrée de texte et deux boutons pour initier la voyelle et les comptes de consonne:

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

Remplacez le contenu de **MainActivity.cs** par le code suivant. Comme on le voit dans cet exemple, les gestionnaires d’événements bouton appeler les méthodes enveloppées `TextCounter` qui résident dans le . AAR et utiliser des toasts pour afficher les résultats. Notez `using` la déclaration pour l’espace nom de `Com.Xamarin.Textcounter`la bibliothèque liée (dans ce cas, ):

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

Compilez et exécutez le projet **BindingTest.** L’application va démarrer et présenter la `EditText` capture d’écran sur la gauche (le est para initialisé avec un certain texte, mais vous pouvez l’exploiter pour le changer). Lorsque vous appuyez sur **COUNT VOWELS**, un toast affiche le nombre de voyelles comme indiqué sur la droite:

[![Captures d’écran de l’exécution BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Essayez d’appuyer sur le bouton **COUNT CONSONANTS.** En outre, vous pouvez modifier la ligne de texte et appuyer à nouveau sur ces boutons pour tester les différents nombres de voyelles et de consonnes.

### <a name="accessing-aar-resources"></a>Accéder. Ressources AAR

L’outillage Xamarin fusionne les données **R** de la . AAR dans la classe **ressources** de votre application. En conséquence, vous pouvez accéder . Les ressources AAR de votre mise en page (et de code-derrière) de la même manière que vous accéderiez aux ressources qui sont dans la voie **ressources** de votre projet.

Pour accéder à une ressource d’image, vous utilisez le nom **Resource.Drawable** pour l’image emballée à l’intérieur de la . Aar. Par exemple, vous pouvez référence **image.png** dans le . Fichier AAR `@drawable/image`en utilisant :

```xml
<ImageView android:src="@drawable/image" ... />
```

Vous pouvez également accéder à des dispositions de ressources qui résident dans le . Aar. Pour ce faire, vous utilisez le nom **Resource.Layout** pour la mise en page emballée à l’intérieur de la . Aar. Par exemple :

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**L’exemple textanalyzer.aar** contient un fichier d’image qui réside à **res/drawable/monkey.png**. Accédons à cette ressource d’image et utilisons-la dans notre application d’exemple :

Modifier la disposition **BindingTest** **(Main.axml**) et ajouter un `ImageView` à l’extrémité du `LinearLayout` conteneur. Cela `ImageView` affiche l’image trouvée à ** \@drawable/singe**; cette image sera chargée à partir de la section ressources de **textanalyzer.aar**:

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

Compilez et exécutez le projet **BindingTest.** L’application démarre et présentera la &ndash; capture d’écran sur la gauche lorsque vous appuyez sur **COUNT CONSONANTS**, les résultats sont affichés comme indiqué sur la droite:

[![BindingTest affichant le nombre de consonnes](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

Félicitations ! Vous avez réussi à lier une bibliothèque Java . Aar!

## <a name="summary"></a>Récapitulatif

Dans cette procédure pas à pas, nous avons créé une bibliothèque de liaisons pour un . Fichier AAR, a ajouté la bibliothèque de liaisons à une application de test minimale, et a exécuté l’application pour vérifier que notre code C ' peut appeler le code Java résidant dans le . Fichier AAR.
En outre, nous avons étendu l’application pour accéder et afficher une ressource d’image qui réside dans le . Fichier AAR.

## <a name="related-links"></a>Liens connexes

- [Construction d’une bibliothèque de liaisons Java (vidéo)](https://university.xamarin.com/classes#10090)
- [Liaison d’un fichier .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573 - Un projet ne peut pas lier plusieurs fichiers .aar](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
