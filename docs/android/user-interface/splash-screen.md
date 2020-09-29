---
title: Écran de démarrage
description: Une application Android prend un certain temps pour démarrer, en particulier lorsque l’application est lancée pour la première fois sur un appareil. Un écran de démarrage peut afficher la progression du démarrage pour l’utilisateur ou pour indiquer la personnalisation.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/02/2019
ms.openlocfilehash: 2b0b68be5cd36d908c7e152137b14b41a7a0b947
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458171"
---
# <a name="splash-screen"></a>Écran de démarrage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)

_Une application Android prend un certain temps pour démarrer, en particulier lorsque l’application est lancée pour la première fois sur un appareil. Un écran de démarrage peut afficher la progression du démarrage pour l’utilisateur ou pour indiquer la personnalisation._

## <a name="overview"></a>Vue d’ensemble

Une application Android prend un certain temps pour démarrer, en particulier lors de la première exécution de l’application sur un appareil (parfois, on parle de _démarrage à froid_). L’écran de démarrage peut afficher la progression du démarrage pour l’utilisateur, ou il peut afficher des informations de personnalisation pour identifier et promouvoir l’application.

Ce guide décrit une technique permettant d’implémenter un écran de démarrage dans une application Android. Il couvre les étapes suivantes :

1. Création d’une ressource dessinable pour l’écran de démarrage.

2. Définition d’un nouveau thème qui affichera la ressource pouvant être dessinée.

3. Ajout d’une nouvelle activité à l’application qui sera utilisée comme écran de démarrage défini par le thème créé à l’étape précédente.

[![Exemple d’écran de démarrage du logo Xamarin suivi d’un écran d’application](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)

## <a name="requirements"></a>Configuration requise

Ce guide part du principe que l’application cible le niveau d’API Android 21 ou ultérieur. L’application doit également disposer des packages NuGet **Xamarin. Android. support. v4** et **Xamarin. Android. support. v7. AppCompat** ajoutés au projet.

Tout le code et XML de ce guide est disponible dans l’exemple de projet [SplashScreen](/samples/xamarin/monodroid-samples/splashscreen) pour ce guide.

## <a name="implementing-a-splash-screen"></a>Implémentation d’un écran de démarrage

La façon la plus rapide d’afficher et d’afficher l’écran de démarrage consiste à créer un thème personnalisé et à l’appliquer à une activité qui présente l’écran de démarrage. Lorsque l’activité est rendue, elle charge le thème et applique la ressource dessinable (référencée par le thème) à l’arrière-plan de l’activité. Cette approche évite d’avoir à créer un fichier de disposition.

L’écran de démarrage est implémenté sous la forme d’une activité qui affiche l’image de dessin personnalisée, effectue toutes les initialisations et démarre toutes les tâches. Une fois l’application amorcée, l’activité de l’écran de démarrage démarre l’activité principale et se supprime de la pile de retour de l’application.

### <a name="creating-a-drawable-for-the-splash-screen"></a>Création d’un dessinable pour l’écran de démarrage

L’écran de démarrage affiche un fichier XML qui peut être dessiné à l’arrière-plan de l’activité de l’écran de démarrage. Il est nécessaire d’utiliser une image bitmap (par exemple, PNG ou JPG) pour que l’image s’affiche.

L’exemple d’application définit un dessinable appelé **splash_screen.xml**. Ce dessinable utilise une [liste de couches](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) pour centrer l’image de l’écran de démarrage dans l’application, comme illustré dans le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash_logo"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

Cela `layer-list` Centre l’image de démarrage sur une couleur d’arrière-plan spécifiée par la `@color/splash_background` ressource. L’exemple d’application définit cette couleur dans le fichier **Resources/values/colors.xml** :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  ...
  <color name="splash_background">#FFFFFF</color>
</resources>
```

Pour plus d’informations sur `Drawable` les objets, consultez la [documentation Google sur Android](https://developer.android.com/reference/android/graphics/drawable/Drawable).

### <a name="implementing-a-theme"></a>Implémentation d’un thème

Pour créer un thème personnalisé pour l’activité de l’écran de démarrage, modifiez (ou ajoutez) les **valeurs/styles.xml** du fichier et créez un nouvel `style` élément pour l’écran de démarrage. Un exemple de fichier **values/style.xml** est présenté ci-dessous avec un `style` **mytheme. Splash**nommé :

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>  
    <item name="android:windowFullscreen">true</item>  
    <item name="android:windowContentOverlay">@null</item>  
    <item name="android:windowActionBar">true</item>  
  </style>
</resources>
```

**Mytheme. Splash** est très Spartan. &ndash; il déclare l’arrière-plan de la fenêtre, supprime explicitement la barre de titre de la fenêtre et déclare qu’il s’agit d’un plein écran. Si vous souhaitez créer un écran de démarrage qui émule l’interface utilisateur de votre application avant que celle-ci n’augmente la première disposition, vous pouvez utiliser `windowContentOverlay` plutôt que `windowBackground` dans votre définition de style. Dans ce cas, vous devez également modifier le **splash_screen.xml** dessiner pour qu’il affiche une émulation de votre interface utilisateur.

### <a name="create-a-splash-activity"></a>Créer une activité de démarrage

Nous avons maintenant besoin d’une nouvelle activité pour le lancement d’Android qui possède notre image de démarrage et effectue toutes les tâches de démarrage. Le code suivant est un exemple d’implémentation complète de l’écran de démarrage :

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` utilise explicitement le thème qui a été créé dans la section précédente, remplaçant le thème par défaut de l’application.
Il n’est pas nécessaire de charger une disposition dans `OnCreate` , car le thème déclare un dessinable comme arrière-plan.

Il est important de définir l' `NoHistory=true` attribut afin que l’activité soit supprimée de la pile de retour. Pour empêcher le bouton précédent d’annuler le processus de démarrage, vous pouvez également remplacer `OnBackPressed` et l’avoir sans rien :

```csharp
public override void OnBackPressed() { }
```

Le travail de démarrage est exécuté de façon asynchrone dans `OnResume` . Cela est nécessaire pour que le travail de démarrage ne ralentisse pas ou ne retarde pas l’apparence de l’écran de lancement. Une fois le travail terminé, démarre `SplashActivity` `MainActivity` et l’utilisateur peut commencer à interagir avec l’application.

Ce nouveau `SplashActivity` est défini en tant qu’activité du lanceur pour l’application en affectant à l’attribut la valeur `MainLauncher` `true` . Étant donné que `SplashActivity` est maintenant l’activité du lanceur, vous devez modifier `MainActivity.cs` et supprimer l' `MainLauncher` attribut de `MainActivity` :

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Mode paysage

L’écran de démarrage implémenté dans les étapes précédentes s’affiche correctement en mode portrait et paysage. Toutefois, dans certains cas, il est nécessaire d’avoir des écrans de démarrage distincts pour les modes portrait et paysage (par exemple, si l’image de démarrage est pleine d’écran).

Pour ajouter un écran de démarrage en mode paysage, procédez comme suit :

1. Dans le dossier **ressources/dessinable** , ajoutez la version paysage de l’image de l’écran de démarrage que vous souhaitez utiliser. Dans cet exemple, **splash_logo_land.png** est la version paysage du logo qui a été utilisée dans les exemples ci-dessus (il utilise les lettres blanches au lieu du bleu).

2. Dans le dossier **ressources/dessinable** , créez une version paysage du `layer-list` dessinable qui a été défini précédemment (par exemple, **splash_screen_land.xml**). Dans ce fichier, définissez le chemin d’accès de l’image bitmap sur la version paysage de l’image de l’écran de démarrage. Dans l’exemple suivant, **splash_screen_land.xml** utilise **splash_logo_land.png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3. Créez le dossier **Resources/values-Land** s’il n’existe pas déjà.

4. Ajoutez les fichiers **colors.xml** et **style.xml** à **valeurs-Land** (ils peuvent être copiés et modifiés à partir des fichiers  **values/colors.xml** et **valeurs/style.xml** existants).

5. Modifiez **values-Land/style.xml** pour qu’il utilise la version paysage de `windowBackground` . Dans cet exemple, **splash_screen_land.xml** est utilisé :

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6. Modifiez **valeurs-Land/colors.xml** pour configurer les couleurs que vous souhaitez utiliser pour la version paysage de l’écran de démarrage. Dans cet exemple, la couleur d’arrière-plan de démarrage devient bleue en mode paysage :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7. Générez et exécutez à nouveau l’application. Faire pivoter l’appareil en mode paysage lorsque l’écran de démarrage est toujours affiché. L’écran de démarrage passe à la version paysage :

    [![Rotation de l’écran de démarrage en mode paysage](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)

Notez que l’utilisation d’un écran de démarrage en mode paysage ne fournit pas toujours une expérience transparente. Par défaut, Android lance l’application en mode portrait et la passe en mode paysage même si l’appareil est déjà en mode paysage. Par conséquent, si l’application est lancée alors que l’appareil est en mode paysage, l’appareil présente brièvement l’écran de démarrage portrait, puis anime la rotation du portrait vers l’écran de démarrage paysage. Malheureusement, cette transition initiale de portrait à paysage a lieu même si `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` est spécifié dans les indicateurs de l’activité de démarrage. La meilleure façon de contourner cette limitation consiste à créer une seule image d’écran de démarrage qui s’affiche correctement en mode portrait et paysage.

## <a name="summary"></a>Résumé

Ce guide a présenté une manière d’implémenter un écran de démarrage dans une application Xamarin. Android. à savoir, application d’un thème personnalisé à l’activité Launch.

## <a name="related-links"></a>Liens associés

- [SplashScreen (exemple)](/samples/xamarin/monodroid-samples/splashscreen)
- [liste de couches dessinable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [Modèles de conception de matériau-écrans de lancement](https://material.io/design/communication/launch-screen.html#usage)