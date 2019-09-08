---
title: Liaison d’un fichier .JAR
description: Cette procédure pas à pas fournit des instructions détaillées sur la création d’une bibliothèque de liaisons Java Xamarin. Android à partir d’un Android. Fichier JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 9a9e7e9c5d189527d4fbdcc2001d6f003fa63dd7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757876"
---
# <a name="binding-a-jar"></a>Liaison d’un fichier .JAR

_Cette procédure pas à pas fournit des instructions détaillées sur la création d’une bibliothèque de liaisons Java Xamarin. Android à partir d’un Android. Fichier JAR._

## <a name="overview"></a>Présentation

La communauté Android propose de nombreuses bibliothèques Java que vous pouvez utiliser dans votre application. Ces bibliothèques Java sont souvent empaquetées dans. Format JAR (Java Archive), mais vous pouvez empaqueter un. Collez-la dans une *bibliothèque de liaisons Java* afin que ses fonctionnalités soient disponibles pour les applications Xamarin. Android. L’objectif de la bibliothèque de liaisons Java est de rendre les API dans le. Fichier JAR disponible pour C# coder les wrappers de code générés automatiquement.

Les outils Xamarin peuvent générer une bibliothèque de liaisons à partir d’une ou plusieurs entrées. Fichiers JAR. La bibliothèque de liaisons (. DLL) contient les éléments suivants : 

- Contenu de l’original. Fichier (s) JAR.

- Les wrappers pouvant être appelés par Managed Callable (MCW), qui sont des C# types qui encapsulent les types Java correspondants dans le. Fichier (s) JAR.

Le code MCW généré utilise JNI (Java Native Interface) pour transférer vos appels d’API vers le sous-jacent. Fichier JAR. Vous pouvez créer des bibliothèques de liaisons pour tout. Fichier JAR ciblé à l’origine pour être utilisé avec Android (Notez que les outils Xamarin ne prennent pas actuellement en charge la liaison de bibliothèques Java non Android). Vous pouvez également choisir de générer la bibliothèque de liaisons sans inclure le contenu du. Fichier JAR afin que la DLL ait une dépendance sur le. JAR au moment de l’exécution.

Dans ce guide, nous allons parcourir les bases de la création d’une bibliothèque de liaisons pour une seule. Fichier JAR. Nous illustrerons ici un exemple où tout se passe &ndash; bien, où aucune personnalisation ou débogage de liaisons n’est nécessaire. 
La [création de liaisons à l’aide de métadonnées](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) offre un exemple de scénario plus avancé dans lequel le processus de liaison n’est pas entièrement automatique et un certain nombre d’interventions manuelles sont nécessaires. Pour obtenir une vue d’ensemble de la liaison de bibliothèque Java en général (à l’aide d’un exemple de code de base), consultez [liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md). 

## <a name="walkthrough"></a>Procédure pas à pas

Dans la procédure pas à pas suivante, nous allons créer une bibliothèque de liaisons pour [Picasso](http://square.github.io/picasso/), un Android populaire. JAR qui fournit des fonctionnalités de chargement et de mise en cache d’image. Nous allons utiliser les étapes suivantes pour lier **Picasso-2. x. x. jar** afin de créer un nouvel assembly .net que nous pouvons utiliser dans un projet Xamarin. Android : 

1. Créez un projet de bibliothèque de liaisons Java.

2. Ajoutez le. Fichier JAR vers le projet.

3. Définissez l’action de génération appropriée pour le. Fichier JAR.

4. Choisissez une version cible de .NET Framework que le. JAR prend en charge.

5. Générez la bibliothèque de liaisons.

Une fois la bibliothèque de liaisons créée, nous allons développer une petite application Android qui démontre notre capacité à appeler des API dans la bibliothèque de liaisons. Dans cet exemple, nous souhaitons accéder aux méthodes de **Picasso-2. x. x. jar**:

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

Une fois que nous avons généré une bibliothèque de liaisons pour **Picasso-2. x. x. jar**, nous pouvons appeler C#ces méthodes à partir de. Par exemple :

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>Création de la bibliothèque de liaisons

Avant de commencer à suivre les étapes ci-dessous, veuillez télécharger [Picasso-2. x. x. jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Tout d’abord, créez un projet de bibliothèque de liaisons. Dans Visual Studio pour Mac ou Visual Studio, créez une nouvelle solution et sélectionnez le modèle *bibliothèque de liaisons Android* . (Les captures d’écran de cette procédure pas à pas utilisent Visual Studio, mais Visual Studio pour Mac est très similaire.) Nommez la solution **JarBinding**: 

[![Créer un projet de bibliothèque JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

Le modèle comprend un dossier **jar** dans lequel vous ajoutez votre. Fichier (s) JAR dans le projet de bibliothèque de liaisons. Cliquez avec le bouton droit sur le dossier **jar** et sélectionnez **Ajouter > élément existant**: 

[![Ajouter un élément existant](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Accédez au fichier **Picasso-2. x. x. jar** téléchargé précédemment, sélectionnez-le, puis cliquez sur **Ajouter**: 

[![Sélectionnez fichier jar, puis cliquez sur Ajouter.](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Vérifiez que le fichier **Picasso-2. x. x. jar** a été correctement ajouté au projet : 

[![Jar ajouté au projet](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Lorsque vous créez un projet de bibliothèque de liaisons Java, vous devez spécifier si le. JAR doit être incorporé dans la bibliothèque de liaisons ou empaqueté séparément. Pour ce faire, vous spécifiez l’une des *actions de génération*suivantes : 

- **EmbeddedJar** &ndash; . JAR sera incorporé dans la bibliothèque de liaisons.

- **InputJar** &ndash; . JAR sera conservé séparément de la bibliothèque de liaisons.

En général, vous utilisez l’action de génération **EmbeddedJar** pour que le. JAR est automatiquement empaqueté dans la bibliothèque de liaisons. Il s’agit de l’option &ndash; la plus simple pour le bytecode Java dans le. JAR est converti en bytecode DEX et est incorporé (avec les wrappers pouvant être appelés) à votre APK. Si vous souhaitez conserver le. JAR distinct de la bibliothèque de liaisons, vous pouvez utiliser l’option **InputJar** ; Toutefois, vous devez vous assurer que le. Le fichier JAR est disponible sur l’appareil qui exécute votre application. 

Définissez l’action de génération sur **EmbeddedJar**: 

[![Sélectionner l’action de génération EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Ensuite, ouvrez les propriétés du projet pour configurer la version *cible de .NET Framework*. Si. JAR utilise des API Android, définit le Framework cible au niveau de l’API que le. JAR attend. En général, le développeur du. Fichier JAR indique le ou les niveaux d’API que le. JAR est compatible avec. (Pour plus d’informations sur le paramètre Framework cible et les niveaux d’API Android en général, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).)

Définissez le niveau d’API cible pour votre bibliothèque de liaisons (dans cet exemple, nous utilisons l’API de niveau 19) : 

[![Niveau d’API cible défini sur API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

Enfin, générez la bibliothèque de liaisons. Bien qu’il soit possible d’afficher certains messages d’avertissement, le projet de bibliothèque de liaisons doit se générer correctement et produire une sortie. DLL à l’emplacement suivant : **JarBinding/bin/Debug/JarBinding.dll**

### <a name="using-the-bindings-library"></a>Utilisation de la bibliothèque de liaisons

Pour utiliser ce. DLL dans votre application Xamarin. Android, procédez comme suit :

1. Ajoutez une référence à la bibliothèque de liaisons.

2. Effectuez des appels dans le. Fichier JAR à l’aide des wrappers pouvant être appelés par Managed. 

Dans les étapes suivantes, nous allons créer une application minimale qui utilise la bibliothèque de liaisons pour télécharger et afficher une image dans un `ImageView`; le « gros levage » est effectué par le code qui réside dans le. Fichier JAR. 

Tout d’abord, créez une nouvelle application Xamarin. Android qui utilise la bibliothèque de liaisons. Cliquez avec le bouton droit sur la solution et sélectionnez **Ajouter nouveau projet**. Nommez le nouveau projet **BindingTest**. Nous créons cette application dans la même solution que la bibliothèque de liaisons afin de simplifier cette procédure pas à pas. Toutefois, l’application qui utilise la bibliothèque de liaisons peut, à la place, résider dans une autre solution : 

[![Ajouter un nouveau projet BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Cliquez avec le bouton droit sur le nœud **références** du projet **BindingTest** et sélectionnez **Ajouter une référence...** :

[![Ajouter une référence à droite](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Vérifiez le projet **JarBinding** créé précédemment et cliquez sur **OK**:

[![Sélectionner un projet JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Ouvrez le nœud **références** du projet **BindingTest** et vérifiez que la référence **JarBinding** est présente : 

[![JarBinding s’affiche sous Références](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modifiez la disposition de **BindingTest** (**main. AXML**) pour qu’elle dispose d' `ImageView`un seul :

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

Ajoutez l’instruction `using` suivante à **MainActivity.cs** &ndash; , ce qui permet d’accéder facilement aux méthodes de la classe basée `Picasso` sur Java qui réside dans la bibliothèque de liaisons :

```csharp
using Com.Squareup.Picasso;
```

Modifiez la `OnCreate` méthode afin qu’elle utilise la `Picasso` classe pour charger une image à partir d’une URL et l’afficher `ImageView`dans le : 

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

Compilez et exécutez le projet **BindingTest** . L’application démarre et, après un bref délai (en fonction des conditions du réseau), elle doit télécharger et afficher une image similaire à la capture d’écran suivante :

[![Capture d’écran de BindingTest en cours d’exécution](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Félicitations ! Vous avez correctement lié une bibliothèque Java. JAR et l’ai utilisé dans votre application Xamarin. Android.

## <a name="summary"></a>Récapitulatif

Dans cette procédure pas à pas, nous avons créé une bibliothèque de liaisons pour une tierce partie. Fichier JAR, ajout de la bibliothèque de liaisons à une application de test minimale, puis exécution de l’application pour vérifier C# que notre code peut appeler du code Java résidant dans le. Fichier JAR. 

## <a name="related-links"></a>Liens associés

- [Création d’une bibliothèque de liaisons Java (vidéo)](https://university.xamarin.com/classes#10090)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
