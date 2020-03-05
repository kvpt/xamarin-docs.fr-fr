---
title: 'Procédure pas à pas : liaison d’une bibliothèque Kotlin Android'
description: Cet article fournit une procédure pas à pas de création d’une liaison Xamarin. Android pour une bibliothèque Kotlin existante, BubblePicker. Il aborde des sujets tels que la compilation d’une bibliothèque Kotlin, sa liaison et l’utilisation de la liaison dans une application Xamarin. Android.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78291708"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Procédure pas à pas : liaison d’une bibliothèque Kotlin Android

Xamarin permet aux développeurs mobiles de créer des applications mobiles natives multiplateformes à C#l’aide de Visual Studio et. Vous pouvez utiliser les composants du kit de développement logiciel (SDK) de la plateforme Android prêts à l’emploi, mais dans de nombreux cas, vous souhaitez également utiliser des kits de développement logiciel tiers écrits pour cette plateforme et Xamarin vous permet de le faire via des liaisons. Pour intégrer une infrastructure Android tierce à votre application Xamarin. Android, vous devez créer une liaison Xamarin. Android pour celle-ci avant de pouvoir l’utiliser dans vos applications.

La plateforme Android, ainsi que ses langages et outils natifs, évoluent en permanence, y compris la dernière présentation du langage Kotlin, qui est finalement définie pour remplacer Java. Il existe plusieurs kits de développement logiciel (SDK) tiers en 3D, qui ont déjà été migrés de Java vers Kotlin et nous présentent de nouveaux défis. Bien que le processus de liaison Kotlin soit similaire à Java, il requiert des étapes supplémentaires et des paramètres de configuration pour générer et s’exécuter avec succès dans le cadre d’une application Xamarin. Android.  

L’objectif de ce document est de présenter une approche de haut niveau pour traiter ce scénario et de fournir un guide pas à pas détaillé avec un exemple simple.

## <a name="background"></a>Arrière-plan

Kotlin a été publié en février 2016 et était positionné comme alternative au compilateur Java standard dans Android Studio par 2017. Plus tard dans 2019, Google a annoncé que le langage de programmation Kotlin devenait le langage préféré pour les développeurs d’applications Android. L’approche de la liaison de haut niveau est similaire au [processus de liaison des bibliothèques Java normales](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) , avec quelques étapes importantes spécifiques Kotlin.

## <a name="prerequisites"></a>Conditions préalables requises

Pour réaliser cette procédure pas à pas, vous aurez besoin des éléments suivants :

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Décompilateur Java](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Créer une bibliothèque Native

La première étape consiste à créer une bibliothèque Kotlin Native à l’aide de Android Studio. La bibliothèque est généralement fournie par un développeur tiers ou disponible dans [le référentiel maven de Google](https://maven.google.com/web/index.html) et dans d’autres référentiels distants. Par exemple, dans ce didacticiel, une liaison pour la bibliothèque Kotlin du sélecteur de bulles est créée :

![Démo GitHub BubblePicker](walkthrough-images/github-bubblepicker-demo.png)

1. Téléchargez [le code source](https://github.com/igalata/Bubble-Picker/archive/develop.zip) à partir de GitHub pour la bibliothèque et décompressez-le dans un dossier local **-Sélecteur de bulles**.

1. Lancez le Android Studio et sélectionnez l’option de menu **ouvrir un projet Android Studio existant** , en choisissant le dossier local de sélecteur de bulles :

    ![Android Studio ouvrir le projet](walkthrough-images/android-studio-open-project.png)

1. Vérifiez que la Android Studio est à jour, y compris Gradle. Le code source peut être généré avec succès sur Android Studio v 3.5.3, Gradle v 5.4.1. Vous trouverez des instructions sur la mise à jour de Gradle vers la dernière version de Gradle [ici](https://gradle.org/install/).

1. Vérifiez que la Android SDK requise est installée. Le code source requiert Android SDK V25. Ouvrez l’option de menu **outils > du gestionnaire du kit SDK** pour installer les composants du SDK.

1. Mettez à jour et synchronisez le fichier de configuration **Build. gradle** situé à la racine du dossier du projet :

    - Définir la version de Kotlin sur 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Inscrire le référentiel maven de Google par défaut afin que la dépendance de la bibliothèque de prise en charge puisse être résolue :

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

    - Une fois le fichier de configuration mis à jour, il n’est plus synchronisé et Gradle affiche le bouton **Synchroniser maintenant** , puis vous attendez que le processus de synchronisation se termine :

        ![Android Studio Gradle synchroniser maintenant](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > Le cache de dépendances de Gradle est peut-être endommagé, ce qui se produit parfois après un délai de connexion réseau. Retélécharger les dépendances et synchroniser le projet (nécessite un réseau).

        > [!TIP]
        > L’état d’un processus de génération Gradle (démon) est peut-être endommagé. L’arrêt de tous les démons Gradle peut résoudre ce problème. Arrêter les processus de génération Gradle (nécessite un redémarrage). Dans le cas de processus Gradle endommagés, vous pouvez également essayer de fermer l’IDE, puis de tuer tous les processus Java.

        > [!TIP]
        > Votre projet utilise peut-être un plug-in tiers, qui n’est pas compatible avec les autres plug-ins du projet ou la version de Gradle demandée par le projet.

1. Ouvrez le menu Gradle à droite, accédez au menu **bubblepicker > Tasks** , exécutez la tâche de **génération** en double-cliquant dessus et attendez que le processus de génération se termine :

    ![Android Studio la tâche d’exécution de Gradle](walkthrough-images/android-studio-gradle-execute-task.png)

1. Ouvrez le navigateur fichiers du dossier racine et accédez au dossier de génération : **Bubble-sélecteur-> bubblepicker-> Build-> sorties-> AAR**, enregistrez le fichier **bubblepicker-Release. AAR** en tant que **bubblepicker-v 1.0. AAR**, ce fichier sera utilisé ultérieurement dans le processus de liaison :

    ![Sortie de AAR Android Studio](walkthrough-images/android-studio-aar-output.png)

Le fichier AAR est une archive Android, qui contient le code source Kotlin compilé et les ressources requises par Android pour exécuter une application à l’aide de ce kit de développement logiciel (SDK).  

## <a name="prepare-metadata"></a>Préparer les métadonnées

La deuxième étape consiste à préparer le fichier de transformation des métadonnées, qui est utilisé par Xamarin. Android C# pour générer des classes respectives. Un projet de liaison Xamarin. Android détecte toutes les classes et les membres natifs d’une archive Android donnée, puis génère un fichier XML avec les métadonnées appropriées. Le fichier de transformation de métadonnées créé manuellement est ensuite appliqué à la ligne de base générée précédemment pour créer le fichier de définition C# XML final utilisé pour générer le code.

Les métadonnées utilisent la syntaxe de  [XPath](https://www.w3.org/TR/xpath/) et sont utilisées par le générateur de liaisons pour influencer la création de l’assembly de liaison. L’article sur les [métadonnées de liaison Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata) fournit plus d’informations sur les transformations, qui peuvent être appliquées :

1. Créez un fichier **Metadata. xml** vide :

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Définir les transformations XML :

- La bibliothèque Kotlin Native a deux dépendances, que vous ne souhaitez pas exposer C# au monde, définissez deux transformations pour les ignorer complètement. Il est important de préciser que les membres natifs ne seront pas supprimés du fichier binaire C# résultant, seules les classes ne seront pas générées. [Java décompilateur](http://java-decompiler.github.io/) peut être utilisé pour identifier les dépendances. Exécutez l’outil et ouvrez le fichier AAR créé précédemment. par conséquent, la structure de l’archive Android s’affichera, reflétant toutes les dépendances, valeurs, ressources, manifestes et classes :  

    ![Dépendances décompilateur Java](walkthrough-images/java-decompiler-dependencies.png)

    Les transformations pour ignorer le traitement de ces packages sont définies à l’aide des instructions XPath :

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- La classe `BubblePicker` native a deux méthodes `getBackgroundColor` et `setBackgroundColor`, et la transformation suivante la remplacera par C# une propriété `BackgroundColor` :

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- Les types non signés `UInt, UShort, ULong, UByte` nécessitent un traitement spécial. Pour ces types, Kotlin modifie automatiquement les types de noms de méthode et de paramètres, ce qui est reflété dans le code généré :

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    Ce code est compilé dans le code d’octet Java suivant :

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    En outre, les types associés tels que les `UIntArray, UShortArray, ULongArray, UByteArray` sont également affectés par Kotlin. Le nom de la méthode est modifié pour inclure un suffixe supplémentaire et les paramètres sont modifiés en un tableau d’éléments des versions signées des mêmes types. Dans l’exemple ci-dessous, un paramètre de type `UIntArray` est converti automatiquement en `int[]` et le nom de la méthode passe de `fooUIntArrayMethod` à `fooUIntArrayMethod--ajY-9A`. Ce dernier est découvert par les outils Xamarin. Android et généré en tant que nom de méthode valide :

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    Ce code est compilé dans le code d’octet Java suivant :

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    Afin de lui attribuer un nom explicite, les métadonnées suivantes peuvent être ajoutées au **fichier Metadata. xml**, ce qui permet de mettre à jour le nom à l’origine défini dans le code Kotlin :

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    Dans l’exemple BubblePicker, il n’y a aucun membre utilisant des types non signés, par conséquent, aucune autre modification n’est requise.

- Les membres Kotlin avec des paramètres génériques sont transformés par défaut en paramètres de Java.`Lang.Object` entrer. Par exemple, une méthode Kotlin a un paramètre générique \<T >:

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Une fois qu’une liaison Xamarin. Android est générée, la méthode est C# exposée à comme indiqué ci-dessous :

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Les génériques Java et Kotlin ne sont pas pris en charge par les liaisons Xamarin. Android. par C# conséquent, une méthode généralisée d’accès à l’API générique est créée. En guise de solution de contournement, vous pouvez créer une bibliothèque Kotlin Wrapper et exposer les API requises d’une manière fortement typée sans génériques. Vous pouvez également créer des assistances sur C# le côté pour résoudre le problème de la même façon via des API fortement typées.

    > [!TIP]
    > En transformant les métadonnées, toutes les modifications peuvent être appliquées à la liaison générée. L’article liaison de la [bibliothèque Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) de la bibliothèque explique en détail comment les métadonnées sont générées et traitées.

## <a name="build-a-binding-library"></a>Créer une bibliothèque de liaisons

L’étape suivante consiste à créer un projet de liaison Xamarin. Android à l’aide du modèle de liaison Visual Studio, à ajouter les métadonnées requises, aux références natives, puis à générer le projet pour générer une bibliothèque consommable :

1. Ouvrez Visual Studio pour Mac et créez un projet de bibliothèque de liaisons Xamarin. Android, donnez-lui un nom, dans ce cas **testBubblePicker. Binding** et terminez l’Assistant. Le modèle de liaison Xamarin. Android se trouve à l’emplacement suivant : bibliothèque de **> android > bibliothèque de liaisons**:

    ![Créer une liaison Visual Studio](walkthrough-images/visual-studio-create-binding.png)

    Dans le dossier transformations, il existe trois fichiers de transformation principaux :

    - **Metadata. xml** : autorise les modifications apportées à l’API finale, telles que la modification de l’espace de noms de la liaison générée.
    - **EnumFields. xml** : contient le mappage entre les constantes et C# les énumérations Java int.
    - **EnumMethods,. xml** : permet de modifier les paramètres de méthode et les types de retour des C# constantes Java int aux enums.

    Conservez les fichiers **EnumFields. xml** et **EnumMethods,. xml** vides et mettez à jour le fichier **Metadata. xml** pour définir vos transformations.

1. Remplacez le fichier **transformations/Metadata. xml** existant par le fichier **Metadata. xml** créé à l’étape précédente. Dans la fenêtre Propriétés, vérifiez que l' **action de génération** de fichier est définie sur **TransformationFile**:

    ![Métadonnées Visual Studio](walkthrough-images/visual-studio-metadata.png)

1. Ajoutez le fichier **bubblepicker-v 1.0. AAR** que vous avez créé à l’étape 1 au projet de liaison en tant que référence native. Pour ajouter des références de bibliothèque natives, Ouvrez Finder et accédez au dossier avec l’archive Android. Glissez-déposez l’archive dans le dossier jar dans Explorateur de solutions. Vous pouvez également utiliser l’option de menu **Ajouter** un contexte dans le dossier jar et choisir **fichiers existants...** . Choisissez de copier le fichier dans le répertoire pour les besoins de cette procédure pas à pas. Veillez à vérifier que l' **action de génération** est définie sur **LibraryProjectZip**:

    ![Référence native de Visual Studio](walkthrough-images/visual-studio-native-reference.png)

1. Ajoutez une référence au package [NuGet Xamarin. Kotlin. stdlib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) . Ce package est une liaison pour la bibliothèque standard Kotlin. Sans ce package, la liaison ne fonctionnera que si la bibliothèque Kotlin n’utilise pas de types spécifiques Kotlin. dans le cas contraire, tous ces C# membres ne seront pas exposés à et toute application qui tente d’utiliser la liaison se bloquera au moment de l’exécution.

    > [!TIP]
    > En raison d’une limitation de Xamarin. Android, les outils de liaison ne peuvent être ajoutés qu’à une seule archive Android (AAR) par projet de liaison. Si plusieurs fichiers AAR doivent être inclus, plusieurs projets Xamarin. Android sont requis, un pour chaque AAR. Si c’était le cas pour cette procédure pas à pas, les quatre actions précédentes de cette étape devaient être répétées pour chaque archive. Une autre option consiste à fusionner manuellement plusieurs archives Android en tant qu’archive unique. vous pouvez donc utiliser un seul projet de liaison Xamarin. Android.

1. La dernière action consiste à générer la bibliothèque et à ne pas avoir d’erreurs de compilation. En cas d’erreurs de compilation, elles peuvent être traitées et gérées à l’aide du fichier Metadata. xml, que vous avez créé précédemment en ajoutant des métadonnées de transformation XML, ce qui permet d’ajouter, de supprimer ou de renommer des membres de bibliothèque.

## <a name="consume-the-binding-library"></a>Utiliser la bibliothèque de liaisons

L’étape finale consiste à utiliser la bibliothèque de liaisons Xamarin. Android dans une application Xamarin. Android. Créez un nouveau projet Xamarin. Android, ajoutez une référence à la bibliothèque de liaison et l’interface utilisateur du sélecteur de bulles de rendu :

1. Créez un projet Xamarin. Android. Utilisez l' **application android > > application Android** comme point de départ et sélectionnez les **dernières et les plus** importantes lorsque vous ciblez les plateformes pour éviter les problèmes de compatibilité. Toutes les étapes suivantes ciblent ce projet :

    ![Créer une application Visual Studio](walkthrough-images/visual-studio-create-app.png)

1. Ajoutez une référence de projet au projet de liaison ou ajoutez une référence à la DLL créée précédemment :

    ![Ajouter une référence de liaison dans Visual Studio. png](walkthrough-images/visual-studio-add-binding-reference.png)

1. Ajoutez une référence au package [NuGet Xamarin. Kotlin. stdlib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) que vous avez ajouté précédemment au projet de liaison Xamarin. Android. Elle ajoute la prise en charge de tous les types spécifiques de Kotlin qui nécessitent une gestion dans le Runtime.  Sans ce package, l’application peut être compilée, mais se bloquera au moment de l’exécution :

    ![Ajout de StdLib NuGet dans Visual Studio](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Ajoutez le contrôle `BubblePicker` à la disposition Android pour `MainActivity`. Ouvrez le fichier **testBubblePicker/Resources/layout/content_main. xml** et ajoutez le nœud de contrôle BubblePicker en tant que dernier élément du contrôle RelativeLayout racine :

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

1. Mettez à jour le code source de l’application et ajoutez la logique d’initialisation au `MainActivity`, qui active le kit de développement logiciel (SDK) de sélecteur de bulles :

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

    `BubblePickerAdapter` et `BubblePickerListener` sont deux classes à créer à partir de zéro, qui gèrent les données des bulles et l’interaction des contrôles :

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

1. Exécutez l’application, qui doit afficher l’interface utilisateur du sélecteur de bulles :

    ![Démonstration BubblePicker](walkthrough-images/bubble-picker-demo.png)

    L’exemple requiert du code supplémentaire pour afficher le style des éléments et gérer les interactions, mais le contrôle de `BubblePicker` a été créé et activé avec succès.

Félicitations ! Vous avez créé avec succès une application Xamarin. Android et une bibliothèque de liaison, qui consomme une bibliothèque Kotlin.  

Vous devez maintenant disposer d’une application Xamarin. Android de base qui utilise une bibliothèque Kotlin Native via une bibliothèque de liaisons Xamarin. Android. Cette procédure pas à pas utilise intentionnellement un exemple de base pour mieux mettre l’accent sur les concepts clés introduits. Dans les scénarios réels, vous aurez probablement besoin d’exposer un plus grand nombre d’API et d’appliquer des transformations de métadonnées à celles-ci.

## <a name="related-links"></a>Liens connexes

- [Android Studio](https://developer.android.com/studio)
- [Installation de Gradle](https://gradle.org/install/)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Décompilateur Java](http://java-decompiler.github.io/)
- [Bibliothèque BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Liaison de la bibliothèque Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Métadonnées de liaison Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin. Kotlin. StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Exemple de référentiel de projet](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
