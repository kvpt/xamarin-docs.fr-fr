---
title: Prise en main d’Android
description: Ce document explique comment prendre en main l’intégration de .NET à Android. Il traite de l’installation de l’incorporation .NET, de la création d’un projet de bibliothèque Android, de l’utilisation de la sortie générée dans un projet de Android Studio et d’autres considérations.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: df29a7f595973a0447899666838e21418ff69330
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650074"
---
# <a name="getting-started-with-android"></a>Prise en main d’Android

Outre les spécifications du Guide de [prise en](~/tools/dotnet-embedding/get-started/java/index.md) main de Java, vous aurez également besoin des éléments suivants:

* [Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou version ultérieure
* [Android Studio 3. x](https://developer.android.com/studio/index.html) avec Java 1,8

En guise de vue d’ensemble, nous allons:

* Créer un C# projet de bibliothèque Android
* Installer l’incorporation .NET via NuGet
* Exécuter l’incorporation .NET sur l’assembly de bibliothèque Android
* Utiliser le fichier AAR généré dans un projet Java dans Android Studio

## <a name="create-an-android-library-project"></a>Créer un projet de bibliothèque Android

Ouvrez Visual Studio pour Windows ou Mac, créez un projet de bibliothèque de classes Android, nommez-le **Hello-from-CSharp**, puis enregistrez-le dans **~/projects/hello-from-CSharp** ou **%UserProfile%\Projects\hello-from-CSharp**.

Ajoutez une nouvelle activité Android nommée **HelloActivity.cs**, suivie d’une disposition Android sur **Resource/layout/Hello. AXML**.

Ajoutez un nouveau `TextView` à votre disposition et remplacez le texte par un texte agréable.

Votre source de disposition doit ressembler à ceci:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

Dans votre activité, assurez-vous que `SetContentView` vous appelez avec votre nouvelle disposition:

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> N’oubliez pas `[Register]` l’attribut. Pour plus d’informations, consultez [limitations](#current-limitations-on-android).

Générez le projet. L’assembly résultant sera enregistré dans `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Installation de l’incorporation .NET à partir de NuGet

Suivez ces [instructions](~/tools/dotnet-embedding/get-started/install/install.md) pour installer et configurer l’incorporation .net pour votre projet.

L’appel de commande que vous devez configurer doit ressembler à ceci:

### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Utiliser la sortie générée dans un projet Android Studio

1. Ouvrez Android Studio et créez un projet avec une **activité vide**.
2. Cliquez avec le bouton droit sur le module de votre **application** , puis choisissez **Nouveau > module**.
3. Sélectionnez **Importer. JAR/. Package AAR**.
4. Utilisez le navigateur de répertoires pour localiser **~/projects/hello-from-CSharp/output/hello_from_csharp.AAR** , puis cliquez sur **Terminer**.

![Importer des AAR dans Android Studio](android-images/androidstudioimport.png)

Cette opération copie le fichier AAR dans un nouveau module nommé **hello_from_csharp**.

![Projet Android Studio](android-images/androidstudioproject.png)

Pour utiliser le nouveau module à partir de votre **application**, cliquez avec le bouton droit et choisissez **ouvrir les paramètres du module**. Sous l' onglet dépendances, ajoutez une nouvelle **dépendance de module** et choisissez **: hello_from_csharp**.

![Dépendances de Android Studio](android-images/androidstudiodependencies.png)

Dans votre activité, ajoutez une nouvelle `onResume` méthode et utilisez le code suivant pour lancer l' C# activité:

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>Compression d’assembly (*important*)

Une autre modification est nécessaire pour l’incorporation .NET dans votre projet de Android Studio.

Ouvrez le fichier **Build. gradle** de votre application et ajoutez la modification suivante:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin. Android charge actuellement les assemblys .NET directement à partir du APK, mais il n’est pas nécessaire de compresser les assemblys.

Si vous ne disposez pas de ce programme d’installation, l’application se bloque lors du lancement et affiche ce qui suit dans la console:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Exécution de l'application

Lors du lancement de votre application:

![Bonjour de C# l’exemple en cours d’exécution dans l’émulateur](android-images/hello-from-csharp-android.png)

Notez ce qui s’est produit ici:

* Nous avons une C# classe, `HelloActivity`, qui sous-classe Java
* Nous avons des fichiers de ressources Android
* Nous les avons utilisées à partir de Java dans Android Studio

Pour que cet exemple fonctionne, tous les éléments suivants sont configurés dans le APK final:

* Xamarin. Android est configuré au démarrage de l’application
* Assemblys .NET inclus dans les **ressources/assemblys**
* Modifications apportées à **fichier AndroidManifest. xml** pour vos C# activités, etc.
* Ressources et ressources Android à partir des bibliothèques .NET
* [Wrappers Android pouvant être appelés](~/android/platform/java-integration/android-callable-wrappers.md) pour `Java.Lang.Object` une sous-classe

Si vous recherchez une procédure pas à pas supplémentaire, consultez la vidéo suivante, qui illustre l’incorporation de la [démonstration FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) de Charles Petzold dans un projet Android Studio:

[![Embeddinator-4000 pour Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Utilisation de Java 1,8

À l’écriture de cela, la meilleure option consiste à utiliser Android Studio 3,0 ([Téléchargez ici](https://developer.android.com/studio/index.html)).

Pour activer Java 1,8 dans le fichier **Build. gradle** du module de votre application:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

Pour plus d’informations, vous pouvez également consulter un [projet de test Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) . 

Si vous souhaitez utiliser Android Studio 2.3. x stable, vous devez activer la chaîne d’outils Jack déconseillée:

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Limitations actuelles sur Android

Pour le moment, si vous sous `Java.Lang.Object`-classez, Xamarin. Android génère le stub Java (wrapper Android pouvant être appelé) au lieu de l’incorporation .net. Pour cette raison, vous devez suivre les mêmes règles pour l' C# exportation vers Java en tant que Xamarin. Android. Par exemple :

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

* `[Register]`est requis pour mapper à un nom de package Java souhaité
* `[Export]`est requis pour rendre une méthode visible pour Java

Nous pouvons utiliser `ViewSubclass` dans Java comme suit:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

En savoir plus sur l' [intégration de Java à Xamarin. Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Assemblys multiples

L’incorporation d’un assembly unique est simple; Toutefois, il est bien plus probable que vous ayez plus d’un C# assembly. Souvent, vous aurez des dépendances avec les packages NuGet, tels que les bibliothèques de support Android ou les Google Play Services qui compliquent encore davantage les choses.

Cela provoque un dilemme, puisque l’incorporation .NET doit inclure de nombreux types de fichiers dans le AAR final, par exemple:

* Ressources Android
* Ressources Android
* Bibliothèques natives Android
* Source Android Java

Il est très probable que vous ne souhaitiez pas inclure ces fichiers à partir de la bibliothèque de support Android ou Google Play Services dans votre AAR, mais plutôt utiliser la version officielle de Google dans Android Studio.

Voici l’approche recommandée:

* Passons à l’incorporation .NET de tout assembly dont vous êtes propriétaire (source pour) et que vous souhaitez appeler à partir de Java
* Passer .NET incorporer tout assembly dont vous avez besoin des ressources Android, des bibliothèques natives ou des ressources à partir de
* Ajoutez des dépendances Java comme la bibliothèque de prise en charge Android ou Google Play Services dans Android Studio

Par conséquent, votre commande peut être:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

Vous devez exclure quoi que ce soit de NuGet, à moins que vous ne l’ayez trouvé contient des ressources Android, des ressources, etc. dont vous aurez besoin dans votre projet Android Studio. Vous pouvez également omettre les dépendances que vous n’avez pas besoin d’appeler à partir de Java, et l’éditeur de liens _doit_ inclure les parties de votre bibliothèque qui sont nécessaires.

Pour ajouter des dépendances Java nécessaires dans Android Studio, votre fichier **Build. gradle** peut se présenter comme suit:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Informations supplémentaires

* [Rappels sur Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Recherche Android préliminaire](~/tools/dotnet-embedding/android/index.md)
* [Limitations de l’incorporation .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribution au projet open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codes d’erreur et descriptions](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Liens connexes

- [Exemple Weather (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
