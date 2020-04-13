---
title: Liaison d’un fichier .JAR
description: Ce passage à pas fournit des instructions étape par étape pour créer une bibliothèque de liaisons Java Xamarin.Android à partir d’un Android . Fichier JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 59969abae739db1d9035ec31738c39a3912f47ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027777"
---
# <a name="binding-a-jar"></a>Liaison d’un fichier .JAR

_Ce passage à pas fournit des instructions étape par étape pour créer une bibliothèque de liaisons Java Xamarin.Android à partir d’un Android . Fichier JAR._

## <a name="overview"></a>Vue d’ensemble

La communauté Android offre de nombreuses bibliothèques Java que vous pouvez utiliser dans votre application. Ces bibliothèques Java sont souvent emballés dans . Format JAR (Java Archive), mais vous pouvez emballer un . JAR il dans une *bibliothèque java Bindings* de sorte que sa fonctionnalité est disponible pour les applications Xamarin.Android. Le but de la bibliothèque Java Bindings est de faire les API dans le . Fichier JAR disponible au code C par l’intermédiaire d’emballages de code générés automatiquement.

L’outillage Xamarin peut générer une bibliothèque de liaisons à partir d’une ou plusieurs entrées. Fichiers JAR. La Bibliothèque des Liaisons (. L’assemblage DLL) contient ce qui suit : 

- Le contenu de l’original . Fichier JAR(s).

- Emballages Callable gérés (MCW), qui sont des types C ' qui enveloppent les types Java correspondants dans le . Fichier JAR(s).

Le code MCW généré utilise JNI (Java Native Interface) pour transmettre vos appels API vers le sous-jacent . Fichier JAR. Vous pouvez créer des bibliothèques de liaisons pour n’importe quel . Fichier JAR qui était initialement destiné à être utilisé avec Android (notez que l’outillage Xamarin ne prend pas actuellement en charge la liaison des bibliothèques Java non-Android). Vous pouvez également choisir de construire la Bibliothèque des liaisons sans inclure le contenu de la . FICHIER JAR de sorte que le DLL a une dépendance sur le . JAR à l’heure de course.

Dans ce guide, nous allons passer à travers les bases de la création d’une bibliothèque de liaisons pour un seul . Fichier JAR. Nous illustrerons avec un exemple &ndash; où tout va bien qui est, où aucune personnalisation ou débogage de fixations n’est nécessaire. 
[La création de liaisons à l’aide de métadonnées](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) offre un exemple de scénario plus avancé où le processus de liaison n’est pas entièrement automatique et qu’une certaine quantité d’intervention manuelle est nécessaire. Pour un aperçu de la liaison de bibliothèque Java en général (avec un exemple de code de base), voir [Binding a Java Library](~/android/platform/binding-java-library/index.md). 

## <a name="walkthrough"></a>Procédure pas à pas

Dans la procédure pas à pas suivante, nous allons créer une bibliothèque de liaisons pour [Picasso](https://square.github.io/picasso/), un Android populaire . JAR qui fournit des fonctionnalités de chargement et de mise en cache d’images. Nous allons utiliser les étapes suivantes pour lier **picasso-2.x.x.jar** pour créer un nouvel assemblage .NET que nous pouvons utiliser dans un projet Xamarin.Android: 

1. Créez un nouveau projet de bibliothèque de liaisons Java.

2. Ajouter le . Fichier JAR au projet.

3. Définir l’action de construction appropriée pour le . Fichier JAR.

4. Choisissez un cadre cible que le . Jar soutient.

5. Construire la bibliothèque de liaisons.

Une fois que nous avons créé la bibliothèque de liaisons, nous développerons une petite application Android qui démontre notre capacité à appeler des API dans la bibliothèque de liaisons. Dans cet exemple, nous voulons accéder à des méthodes de **picasso-2.x.x.jar**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}
```

Après avoir généré une bibliothèque de liaisons pour **picasso-2.x.x.jar**, nous pouvons appeler ces méthodes à partir de C. Par exemple :

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>Création de la Bibliothèque des liaisons

Avant de commencer avec les étapes ci-dessous, s’il vous plaît télécharger [picasso-2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Tout d’abord, créez un nouveau projet de bibliothèque de liaisons. Dans Visual Studio pour Mac ou Visual Studio, créez une nouvelle solution et sélectionnez le modèle *Android Bindings Library.* (Les captures d’écran dans cette procédure pas à pas utilisent Visual Studio, mais Visual Studio pour Mac est très similaire.) Nommez la solution **JarBinding**: 

[![Créer un projet de bibliothèque JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

Le modèle comprend un dossier **Jars** où vous ajoutez votre . JAR(s) au projet de la Bibliothèque des liaisons. Cliquez à droite sur le dossier **Jars** et sélectionnez **Ajouter > élément existant**: 

[![Ajouter l’article existant](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Naviguez vers le fichier **picasso-2.x.x.jar** téléchargé plus tôt, sélectionnez-le et cliquez sur **Ajouter**: 

[![Sélectionnez le fichier de pot et cliquez sur Ajouter](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Vérifiez que le fichier **picasso-2.x.x.jar** a été ajouté avec succès au projet : 

[![Jar ajouté au projet](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Lorsque vous créez un projet de bibliothèque Java Bindings, vous devez spécifier si le . JAR doit être intégré dans la bibliothèque de liaisons ou emballé séparément. Pour ce faire, vous spécifiez l’une des *actions de construction*suivantes : 

- **EmbeddedJar** &ndash; le . JAR sera intégré dans la Bibliothèque des liaisons.

- **InputJar** &ndash; le . JAR sera tenu à l’écart de la Bibliothèque des liaisons.

Typiquement, vous utilisez l’action **EmbeddedJar** construire de sorte que le . JAR est automatiquement emballé dans la bibliothèque de fixations. C’est l’option &ndash; la plus simple Java bytecode dans le . JAR est converti en bytecode Dex et est intégré (avec les emballages callables gérés) dans votre APK. Si vous voulez garder le . JAR séparé de la bibliothèque de fixations, vous pouvez utiliser l’option **InputJar;** cependant, vous devez vous assurer que le . Le fichier JAR est disponible sur l’appareil qui exécute votre application. 

Définir l’action de construction à **EmbeddedJar**: 

[![Sélectionnez EmbeddedJar construire l’action](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Ensuite, ouvrez les propriétés du projet pour configurer le *cadre cible*. Si le . JAR utilise toutes les API Android, définir le cadre cible au niveau API que le . JAR s’attend à. Typiquement, le développeur de la . Le fichier JAR indiquera quel niveau d’API (ou niveaux) que le . JAR est compatible avec. (Pour plus d’informations sur le paramètre du cadre cible et les niveaux d’API Android en général, voir [Comprendre les niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).)

Définissez le niveau d’API cible pour votre bibliothèque de liaisons (dans cet exemple, nous utilisons le niveau API 19) : 

[![Niveau D’API cible fixé à l’API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

Enfin, construisez la Bibliothèque des Liaisons. Bien que certains messages d’avertissement puissent être affichés, le projet de la Bibliothèque des liaisons devrait être construit avec succès et produire une sortie. DLL à l’endroit suivant: **JarBinding/bin/Debug/JarBinding.dll**

### <a name="using-the-bindings-library"></a>Utilisation de la bibliothèque de liaisons

Pour consommer cela . DLL dans votre application Xamarin.Android, faites ce qui suit:

1. Ajoutez une référence à la Bibliothèque des liaisons.

2. Faites des appels dans le . JAR par l’intermédiaire des emballages callables gérés. 

Dans les étapes suivantes, nous allons créer une application minimale qui utilise la `ImageView`bibliothèque de liaisons pour télécharger et afficher une image dans un ; le "levage lourd" est fait par le code qui réside dans le . Fichier JAR. 

Tout d’abord, créer une nouvelle application Xamarin.Android qui consomme la bibliothèque de liaisons. Cliquez à droite sur la solution et **sélectionnez Ajouter un nouveau projet**; nom du nouveau projet **BindingTest**. Nous créons cette application dans la même solution que la Bibliothèque des liaisons afin de simplifier cette procédure pas à pas; cependant, l’application qui consomme la bibliothèque de liaisons pourrait, au lieu de cela, résider dans une solution différente : 

[![Ajouter un nouveau projet BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Cliquez à droite sur le nœud **Références** du projet **BindingTest** et **sélectionnez Ajouter référence...**:

[![Référence d’ajout à droite](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Consultez le projet **JarBinding** créé plus tôt et cliquez sur **OK**:

[![Sélectionnez le projet JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Ouvrez le nœud **Références** du projet **BindingTest** et vérifiez que la référence **JarBinding** est présente : 

[![JarBinding apparaît sous Références](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modifier la mise en page **BindingTest** (**Main.axml**) afin qu’il ait un seul `ImageView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

Ajoutez la `using` déclaration suivante à **MainActivity.cs** &ndash; cela permet d’accéder facilement aux `Picasso` méthodes de la classe java qui réside dans la bibliothèque de liaisons :

```csharp
using Com.Squareup.Picasso;
```

Modifier `OnCreate` la méthode afin `Picasso` qu’elle utilise la classe pour charger `ImageView`une image à partir d’une URL et l’afficher dans le : 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

Compilez et exécutez le projet **BindingTest.** L’application démarre, et après un court délai (selon les conditions du réseau), elle doit télécharger et afficher une image similaire à la capture d’écran suivante:

[![Capture d’écran de BindingTest en cours d’exécution](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Félicitations ! Vous avez réussi à lier une bibliothèque Java . JAR et l’a utilisé dans votre application Xamarin.Android.

## <a name="summary"></a>Récapitulatif

Dans cette procédure pas à pas, nous avons créé une bibliothèque de liaisons pour un tiers . Fichier JAR, a ajouté la bibliothèque de liaisons à une application de test minimale, puis a exécuté l’application pour vérifier que notre code C peut appeler le code Java résidant dans le . Fichier JAR. 

## <a name="related-links"></a>Liens connexes

- [Construction d’une bibliothèque de liaisons Java (vidéo)](https://university.xamarin.com/classes#10090)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
