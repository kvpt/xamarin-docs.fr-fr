---
title: 'Procédure pas à pas: Bind une bibliothèque Android Kotlin'
description: Cet article fournit une procédure pratique de la création d’une liaison Xamarin.Android pour une bibliothèque Kotlin existante, BubblePicker. Il couvre des sujets tels que la compilation d’une bibliothèque Kotlin, la lier, et l’utilisation de la liaison dans une application Xamarin.Android.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291708"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Procédure pas à pas: Bind une bibliothèque Android Kotlin

Xamarin permet aux développeurs mobiles de créer des applications mobiles natives multiplateformes à l’aide de Visual Studio et de C. Vous pouvez utiliser la plate-forme Android composants SDK hors de la boîte, mais dans de nombreux cas, vous souhaitez également utiliser des SDK tiers écrit pour cette plate-forme et Xamarin vous permet de le faire via des liaisons. Afin d’intégrer un cadre Android tiers dans votre application Xamarin.Android, vous devez créer une liaison Xamarin.Android pour elle avant que vous puissiez l’utiliser dans vos applications.

La plate-forme Android, ainsi que ses langues maternelles et l’outillage, sont en constante évolution, y compris l’introduction récente de la langue Kotlin, qui est fixé à terme pour remplacer Java. Il ya un certain nombre de SDKs parti 3d, qui ont déjà été migrés de Java à Kotlin et il nous présente de nouveaux défis. Même si le processus de liaison Kotlin est similaire à Java, il nécessite des étapes supplémentaires et des paramètres de configuration pour construire et exécuter avec succès dans le cadre d’une application Xamarin.Android.  

L’objectif de ce document est d’esquisser une approche de haut niveau pour aborder ce scénario et de fournir un guide détaillé étape par étape avec un exemple simple.

## <a name="background"></a>Arrière-plan

Kotlin est sorti en février 2016 et a été positionné comme une alternative au compilateur Java standard dans Android Studio d’ici 2017. Plus tard en 2019, Google a annoncé que le langage de programmation Kotlin deviendrait son langage préféré pour les développeurs d’applications Android. L’approche contraignante de haut niveau est similaire [au processus contraignant des bibliothèques Java régulières](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) avec quelques étapes importantes spécifiques Kotlin.

## <a name="prerequisites"></a>Prérequis

Pour réaliser cette procédure pas à pas, vous aurez besoin des éléments suivants :

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Décompiler Java](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Construire une bibliothèque autochtone

La première étape consiste à construire une bibliothèque Kotlin indigène en utilisant Android Studio. La bibliothèque est généralement fournie par un développeur tiers ou disponible [au référentiel Maven de Google](https://maven.google.com/web/index.html) et à d’autres dépôts distants. À titre d’exemple, dans ce tutoriel, une liaison pour la bibliothèque Bubble Picker Kotlin est créée :

![Démo de GitHub BubblePicker](walkthrough-images/github-bubblepicker-demo.png)

1. Téléchargez [le code source](https://github.com/igalata/Bubble-Picker/archive/develop.zip) de GitHub pour la bibliothèque et déballez-le dans un dossier local **Bubble-Picker**.

1. Lancez l’Android Studio et **sélectionnez Open une** option de menu de projet Android Studio existante choisissant le dossier local Bubble-Picker :

    ![Projet Android Studio Open](walkthrough-images/android-studio-open-project.png)

1. Vérifiez que l’Android Studio est à jour, y compris Gradle. Le code source peut être construit avec succès sur Android Studio v3.5.3, Gradle v5.4.1. Instructions sur la façon de mettre à jour Gradle à la dernière version Gradle [pourrait être trouvé ici](https://gradle.org/install/).

1. Vérifiez que le SDK Android requis est installé. Le code source nécessite Android SDK v25. Open Tools > option de menu **SDK Manager** pour installer des composants SDK.

1. Mettre à jour et synchroniser le fichier de configuration **build.gradle** principal situé à la racine du dossier du projet :

    - Définir la version Kotlin à 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Enregistrez le référentiel Maven par défaut de Google afin que la dépendance de la bibliothèque de support puisse être résolue :

        ```gradle
        allprojects {
            repositories {
                jcenter()
                maven {
                    url "https://maven.google.com"
                }
            }
        }
        ```

    - Une fois que le fichier de configuration est mis à jour, il est désynchronisé et Gradle affiche le bouton **Sync Now,** appuyez-le et attendez que le processus de synchronisation soit terminé :

        ![Android Studio Gradle Sync Maintenant](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > Le cache de dépendance de Gradle peut être corrompu, cela se produit parfois après un délai de connexion réseau. Recharger les dépendances et synchroniser le projet (nécessite un réseau).

        > [!TIP]
        > L’état d’un processus de construction de Gradle (daemon) peut être corrompu. Arrêter tous les daemons Gradle peut résoudre ce problème. Arrêter les processus de construction Gradle (nécessite un redémarrage). Dans le cas des processus corrompus Gradle, vous pouvez également essayer de fermer l’IDE, puis tuer tous les processus Java.

        > [!TIP]
        > Votre projet peut être l’utilisation d’un plugin tiers, qui n’est pas compatible avec les autres plugins dans le projet ou la version de Gradle demandée par le projet.

1. Ouvrez le menu Gradle sur la droite, naviguez vers le menu **bubblepicker > Tasks,** exécutez la tâche **de construction** en tapant double sur elle, et attendez que le processus de construction pour terminer :

    ![Android Studio Gradle Exécuter La tâche](walkthrough-images/android-studio-gradle-execute-task.png)

1. Ouvrez le navigateur de fichiers de dossiers de racine et naviguez vers le dossier de construction : **Bubble-Picker -> bubblepicker -> build -> outputs -> aar**, save the **bubblepicker-release.aar** file as **bubblepicker-v1.0.aar**, this file will be used later in the binding process:

    ![Android Studio AAR Sortie](walkthrough-images/android-studio-aar-output.png)

Le fichier AAR est une archive Android, qui contient le code source Kotlin compilé et les actifs, requis par Android pour exécuter une application à l’aide de ce SDK.  

## <a name="prepare-metadata"></a>Préparer les métadonnées

La deuxième étape consiste à préparer le fichier de transformation des métadonnées, qui est utilisé par Xamarin.Android pour générer des classes C respectives. Un projet de liaison Xamarin.Android découvrira toutes les classes et les membres natifs à partir d’une archive Android donnée générant par la suite un fichier XML avec les métadonnées appropriées. Le fichier de transformation des métadonnées créé manuellement est ensuite appliqué à la ligne de base précédemment générée pour créer le fichier de définition XML final utilisé pour générer le code C.

Les métadonnées utilisent la syntaxe [XPath](https://www.w3.org/TR/xpath/) et sont utilisées par le générateur de liaisons pour influencer la création de l’assemblage de liaison. [L’article Java Binding Metadata](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata) fournit plus d’informations sur les transformations, qui pourraient être appliquées:

1. Créez un fichier **De Métadonnées.xml** vide :

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Décrivez les transformations xml :

- La bibliothèque natale de Kotlin a deux dépendances, que vous ne voulez pas exposer au monde de C, définissent deux transformations pour les ignorer complètement. Important de dire que les membres autochtones ne seront pas dépouillés du binaire résultant, seules les classes de C ne seront pas générées. [Java Decompiler](http://java-decompiler.github.io/) peut être utilisé pour identifier les dépendances. Exécutez l’outil et ouvrez le fichier AAR créé plus tôt, en conséquence la structure de l’archive Android sera montré, reflétant toutes les dépendances, valeurs, ressources, manifeste, et les classes:  

    ![Dépendances Java Decompiler](walkthrough-images/java-decompiler-dependencies.png)

    Les transformations pour sauter le traitement de ces paquets sont définies à l’aide d’instructions XPath :

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- La `BubblePicker` classe autochtone `getBackgroundColor` a `setBackgroundColor` deux méthodes et la transformation `BackgroundColor` suivante la transformera en propriété CMD :

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- Les types `UInt, UShort, ULong, UByte` non signés nécessitent une manipulation spéciale. Pour ces types Kotlin change les noms de la méthode et les types de paramètres automatiquement, qui est reflété dans le code généré:

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    Ce code est compilé dans le code d’ente Java suivant :

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    En outre, les `UIntArray, UShortArray, ULongArray, UByteArray` types connexes tels que sont également touchés par Kotlin. Le nom de la méthode est changé pour inclure un suffixe supplémentaire et les paramètres sont modifiés en un éventail d’éléments de versions signées des mêmes types. Dans l’exemple ci-dessous un paramètre de `UIntArray` type est converti automatiquement en `int[]` et le nom de la méthode est changé de `fooUIntArrayMethod` . `fooUIntArrayMethod--ajY-9A` Ce dernier est découvert par les outils Xamarin.Android et généré comme un nom de méthode valide:

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    Ce code est compilé dans le code d’ente Java suivant :

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    Afin de lui donner un nom significatif, les métadonnées suivantes peuvent être ajoutées à la **Métaadata.xml**, qui mettra à jour le nom de nouveau à initialement défini dans le code Kotlin:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    Dans l’échantillon BubblePicker, aucun membre n’utilise des types non signés, donc aucun changement supplémentaire n’est nécessaire.

- Membres Kotlin avec des paramètres génériques par défaut transformés en paramètres de Java.`Lang.Object` Type. Par exemple, une méthode Kotlin \<a un paramètre générique T> :

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Une fois qu’une liaison Xamarin.Android est générée, la méthode est exposée à C ' comme ci-dessous:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Les génériques Java et Kotlin ne sont pas pris en charge par les liaisons Xamarin.Android, donc une méthode généralisée de C pour accéder à l’API générique est créée. En tant que work-around, vous pouvez créer une bibliothèque Kotlin wrapper et exposer les API nécessaires d’une manière forte typée sans génériques. Vous pouvez également créer des aides du côté de Cmd pour aborder le problème de la même manière par le biais d’API de type solide.

    > [!TIP]
    > En transformant les métadonnées, tout changement pourrait être appliqué à la liaison générée. [L’article de la Bibliothèque Java binding](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) explique en détail comment les métadonnées sont générées et traitées.

## <a name="build-a-binding-library"></a>Construire une bibliothèque contraignante

L’étape suivante consiste à créer un projet de liaison Xamarin.Android à l’aide du modèle de liaison Visual Studio, ajouter les métadonnées requises, références natives, puis construire le projet pour produire une bibliothèque consommable :

1. Ouvrez Visual Studio pour Mac et créez un nouveau projet Xamarin.Android Binding Library, donnez-lui un nom, dans ce cas **testBubblePicker.Binding** et complétez l’assistant. Le modèle de liaison Xamarin.Android est situé par le chemin suivant: **Android > Library > Binding Library:**

    ![Studio visuel créer la liaison](walkthrough-images/visual-studio-create-binding.png)

    Dans le dossier Transformations il y a trois fichiers de transformation principaux :

    - **Metadata.xml** - Permet d’apporter des modifications à l’API finale, comme la modification de l’espace nom de la liaison générée.
    - **EnumFields.xml** - Contient la cartographie entre les constantes Java int et enums C.
    - **EnumMethods.xml** - Permet de changer les paramètres de la méthode et les types de retour de Java int constants à C 'enums.

    Gardez vider les fichiers **EnumFields.xml** et **EnumMethods.xml** et mettez à jour les **fichiers Metadata.xml** pour définir vos transformations.

1. Remplacez le fichier **Transformations/Metadata.xml** existant avec le fichier **Metadata.xml** créé à l’étape précédente. Dans la fenêtre des propriétés, vérifiez que le fichier **Build Action** est réglé sur **TransformationFile**:

    ![Métadonnées visualiseres](walkthrough-images/visual-studio-metadata.png)

1. Ajoutez le fichier **bubblepicker-v1.0.aar** que vous avez construit à l’étape 1 au projet de liaison en tant que référence native. Pour ajouter des références de bibliothèque natives, ouvrez le finder et naviguez vers le dossier avec les archives Android. Faites glisser et déposez les archives dans le dossier Jars dans Solution Explorer. Alternativement, vous pouvez utiliser l’option de menu contexte **Ajouter** sur le dossier Jars et choisir **les fichiers existants...**. Choisissez de copier le fichier à l’annuaire aux fins de cette procédure pas à pas. Assurez-vous de vérifier que **l’action build** est définie à **LibraryProjectZip**:

    ![Référence native Visual Studio](walkthrough-images/visual-studio-native-reference.png)

1. Ajoutez une référence au forfait [Xamarin.Kotlin.StdLib NuGet.](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) Ce forfait est une reliure pour la bibliothèque standard Kotlin. Sans ce paquet, la liaison ne fonctionnera que si la bibliothèque Kotlin n’utilise pas de types spécifiques Kotlin, sinon tous ces membres ne seront pas exposés à C et toute application qui tente de consommer la liaison se plantera à l’heure de l’exécution.

    > [!TIP]
    > En raison d’une limitation de la Xamarin.Android, les outils de liaison seulement une seule archive Android (AAR) peut être ajouté par projet de liaison. Si plusieurs fichiers AAR doivent être inclus, plusieurs projets Xamarin.Android sont nécessaires, un par chaque AAR. Si c’était le cas pour cette procédure pas à pas, alors les quatre actions précédentes de cette étape devraient être répétées pour chaque archive. Comme une option alternative, il est possible de fusionner manuellement plusieurs archives Android comme une seule archive et, par conséquent, vous pouvez utiliser un seul projet de liaison Xamarin.Android.

1. L’action finale est de construire la bibliothèque et faire n’ont pas d’erreurs de compilation. En cas d’erreurs de compilation, elles peuvent être traitées et traitées à l’aide du fichier Metadata.xml, que vous avez créé plus tôt en ajoutant des métadonnées de transformation xml, qui ajouteront, supprimeront ou renommeront les membres de la bibliothèque.

## <a name="consume-the-binding-library"></a>Consommer la bibliothèque de liaison

La dernière étape est de consommer la bibliothèque de liaison Xamarin.Android dans une application Xamarin.Android. Créez un nouveau projet Xamarin.Android, ajoutez une référence à la bibliothèque de liaison et rendre l’interface utilisateur Bubble Picker :

1. Créez le projet Xamarin.Android. Utilisez **l’application Android > >'application Android** comme point de départ et sélectionnez **dernières et plus grandes** que vous ciblez l’option Plates-formes pour éviter les problèmes de compatibilité. Toutes les étapes suivantes ciblent ce projet :

    ![Visual Studio Créer App](walkthrough-images/visual-studio-create-app.png)

1. Ajoutez une référence de projet au projet contraignant ou ajoutez une référence que le DLL a créée précédemment :

    ![Studio visuel Ajouter Binding Reference.png](walkthrough-images/visual-studio-add-binding-reference.png)

1. Ajoutez une référence au forfait [Xamarin.Kotlin.StdLib NuGet,](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) que vous avez ajouté au projet de liaison Xamarin.Android plus tôt. Il ajoute un soutien à tous les types spécifiques Kotlin qui ont besoin de remise dans le temps d’exécution.  Sans ce paquet, l’application peut être compilée mais se plantera au moment de l’exécution :

    ![Studio visuel Ajouter StdLib NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Ajouter `BubblePicker` le contrôle à `MainActivity`la mise en page Android pour . Ouvrez **testBubblePicker/Resources/layout/content_main.xml** fichier et appendice le nœud de contrôle BubblePicker comme le dernier élément de la racine RelativeLayout contrôle:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout …>
        …
        <com.igalata.bubblepicker.rendering.BubblePicker
            android:id="@+id/picker"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:backgroundColor="@android:color/white" />
    </RelativeLayout>
    ```

1. Mettre à jour le code source de l’application et ajouter la logique d’initialisation à la `MainActivity`, qui active le Bubble Picker SDK:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState)
    {
        ...
        var picker = FindViewById<BubblePicker>(Resource.Id.picker);
        picker.BubbleSize = 20;
        picker.Adapter = new BubblePickerAdapter();
        picker.Listener = new BubblePickerListener(picker);
        ...
    }
    ```

    `BubblePickerAdapter`et `BubblePickerListener` sont deux classes à créer à partir de zéro, qui traitent les bulles de données et d’interaction de contrôle:

    ```csharp
    public class BubblePickerAdapter : Java.Lang.Object, IBubblePickerAdapter
    {
        private List<string> _bubbles = new List<string>();
        public int TotalCount => _bubbles.Count;
        public BubblePickerAdapter()
        {
            for (int i = 0; i < 10; i++)
            {
                _bubbles.Add($"Item {i}");
            }
        }

        public PickerItem GetItem(int itemIndex)
        {
            if (itemIndex < 0 || itemIndex >= _bubbles.Count)
                return null;

            var result = _bubbles[itemIndex];
            var item = new PickerItem(result);
            return item;
        }
    }

    public class BubblePickerListener : Java.Lang.Object, IBubblePickerListener
    {
        public View Picker { get; }
        public BubblePickerListener(View picker)
        {
            Picker = picker;
        }

        public void OnBubbleDeselected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Deselected: {item.Title}", Snackbar.LengthLong)
                .SetAction("Action", (Android.Views.View.IOnClickListener)null)
                .Show();
        }

        public void OnBubbleSelected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Selected: {item.Title}", Snackbar.LengthLong)
            .SetAction("Action", (Android.Views.View.IOnClickListener)null)
            .Show();
        }
    }
    ```

1. Exécutez l’application, qui devrait rendre l’interface utilisateur Bubble Picker:

    ![Démonstration de BubblePicker](walkthrough-images/bubble-picker-demo.png)

    L’échantillon nécessite un code supplémentaire pour `BubblePicker` rendre les éléments de style et de gérer les interactions, mais le contrôle a été créé et activé avec succès.

Félicitations ! Vous avez créé avec succès une application Xamarin.Android et une bibliothèque de liaison, qui consomme une bibliothèque Kotlin.  

Vous devriez maintenant avoir une application de base Xamarin.Android qui utilise une bibliothèque Kotlin indigène via une bibliothèque de liaison Xamarin.Android. Ce pas-là utilise intentionnellement un exemple de base pour mieux mettre l’accent sur les concepts clés introduits. Dans le monde réel, vous serez probablement tenu d’exposer un plus grand nombre d’API et d’appliquer des transformations de métadonnées pour eux.

## <a name="related-links"></a>Liens connexes

- [Android Studio](https://developer.android.com/studio)
- [Gradle Installation](https://gradle.org/install/)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Décompiler Java](http://java-decompiler.github.io/)
- [Bibliothèque BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Bibliothèque Java contraignante](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [Xpath](https://www.w3.org/TR/xpath/)
- [Java Binding Metadata](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Exemple de dépôt de projet](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
