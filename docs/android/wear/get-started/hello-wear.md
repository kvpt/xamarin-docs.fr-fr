---
title: Hello, Wear
description: Créez votre première application Android usure et exécutez-la sur un émulateur ou un appareil d’usure. Cette procédure pas à pas fournit des instructions détaillées sur la création d’un petit projet d’usure Android qui gère les clics de bouton et affiche un compteur de clic sur le périphérique d’usure. Il explique comment déboguer l’application à l’aide d’un émulateur d’usure ou d’un appareil d’usure connecté via Bluetooth à un téléphone Android. Il fournit également un ensemble de conseils de débogage pour l’usure Android.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/10/2018
ms.openlocfilehash: 056ab7a9fe4bcb7f07a9a7cd7c841a3d9f7574b6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648023"
---
# <a name="hello-wear"></a>Hello, Wear

_Créez votre première application Android usure et exécutez-la sur un émulateur ou un appareil d’usure. Cette procédure pas à pas fournit des instructions détaillées sur la création d’un petit projet d’usure Android qui gère les clics de bouton et affiche un compteur de clic sur le périphérique d’usure. Il explique comment déboguer l’application à l’aide d’un émulateur d’usure ou d’un appareil d’usure connecté via Bluetooth à un téléphone Android. Il fournit également un ensemble de conseils de débogage pour l’usure Android._

![Capture d’écran de l’application d’usure à effectuer dans ce didacticiel](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>Votre première application d’usure

Procédez comme suit pour créer votre première application Xamarin. Android usure:

### <a name="1-create-a-new-android-project"></a>1. Créer un nouveau projet Android

Créer une **application Android usure**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Création d’une application Android usure dans la boîte de dialogue Nouveau projet](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Création d’une application Android usure dans la boîte de dialogue nouvelle solution](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Ce modèle comprend automatiquement la **bibliothèque Xamarin Android portable** NuGet (et les dépendances) afin que vous ayez accès aux widgets spécifiques à l’usure. Si vous ne voyez pas le modèle d’usure, consultez le guide [d’installation et de configuration](~/android/wear/get-started/installation.md) pour vérifier que vous avez installé un Android SDK pris en charge. 

### <a name="2-choose-the-correct-target-framework"></a>2. Choisir le **Framework cible** approprié

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Assurez-vous que le **nombre minimal d’Android à cibler** est défini sur **Android 5,0 (lollipop)** ou version ultérieure: 

[![Définition de la version cible de .NET Framework sur Android 5,0 dans Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Vérifiez que la version cible de .NET Framework est définie sur **Android 5,0 (lollipop)** ou version ultérieure:

[![Définition de la version cible de .NET Framework sur Android 5,0 dans Visual Studio pour Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Pour plus d’informations sur la définition de la version cible du .NET Framework, consultez [fonctionnement des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Modifier la disposition **main. AXML**

Configurez la disposition pour `TextView` qu’elle `Button` contienne un et un pour l’exemple: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. Modifier la source **MainActivity.cs**

Ajoutez le code pour incrémenter un compteur et l’afficher à chaque clic sur le bouton: 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. Configurer un émulateur ou un appareil

L’étape suivante consiste à configurer un émulateur ou un appareil pour déployer et exécuter l’application. Si vous n’êtes pas encore familiarisé avec le processus de déploiement et d’exécution d’applications Xamarin. Android en général, consultez le Guide de [démarrage rapide de Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

Si vous n’avez pas d’appareil Android, tel qu’un SmartWatch d’usure Android, vous pouvez exécuter l’application sur un émulateur. Pour plus d’informations sur le débogage des applications d’usure sur un émulateur, consultez déboguer [l’usure Android sur un émulateur](~/android/wear/deploy-test/debug-on-emulator.md).

Si vous avez un appareil Android d’usure comme un SmartWatch Android, vous pouvez exécuter l’application sur l’appareil au lieu d’utiliser un émulateur. Pour plus d’informations sur le débogage sur un appareil d’usure, consultez déboguer [sur un appareil d’usure](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Exécuter l’application Android usure

L’appareil Android usure doit apparaître dans le menu déroulant de l’appareil. Veillez à choisir le bon appareil Android ou AVD avant de commencer le débogage. Après avoir sélectionné l’appareil, cliquez sur le bouton de lecture pour déployer l’application sur l’émulateur ou l’appareil.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Choix d’une usure dans le menu de périphérique Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Choix d’un menu d’usure dans Visual Studio pour Mac périphérique](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Vous pouvez voir un message **juste un minute.** .. (ou un autre écran interstitiel) au début: 

![L’émulateur Watch affiche une minute...](hello-wear-images/please-wait.png)

Si vous utilisez un émulateur Watch, le démarrage de l’application peut prendre un certain temps. Lorsque vous utilisez Bluetooth, il faut plus de temps pour déployer l’application que sur USB. (Par exemple, il faut environ 5 minutes pour déployer cette application sur une montre LG G qui est connectée à Bluetooth à un téléphone de connexion 5.)

Une fois l’application déployée, l’écran de l’appareil d’usure doit afficher un écran semblable à ce qui suit:

[![Écran initial de l’application d’usure](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Appuyez **sur le clic!** sur la face du dispositif d’usure et regardez l’incrément de nombre de chaque pression:

[![Capture d’écran de l’application d’usure après 3 clics](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Étapes suivantes

Découvrez les [exemples d’usure](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear) , notamment les applications Android avec des applications de téléphone auxiliaires.

Lorsque vous êtes prêt à distribuer votre application, consultez [utilisation de](~/android/wear/deploy-test/packaging.md)l’empaquetage.


## <a name="related-links"></a>Liens associés

- [Cliquer sur l’application (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-weartest)
