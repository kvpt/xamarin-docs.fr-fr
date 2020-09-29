---
title: Création d’un visage de montre pour Android usure 1,0
description: Ce guide explique comment implémenter un service de visage de montre personnalisé pour Android usure 1,0. Des instructions pas à pas sont fournies pour créer un service de facette numérique supprimé, suivi de davantage de code pour créer un visage de type analogique.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/23/2018
ms.openlocfilehash: 79dcab73c379cecb5108a88cc8bbb2eab33af05c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457105"
---
# <a name="creating-a-watch-face"></a>Création d’un cadran de montre

_Ce guide explique comment implémenter un service de visage de montre personnalisé pour Android usure 1,0. Des instructions pas à pas sont fournies pour créer un service de facette numérique supprimé, suivi de davantage de code pour créer un visage de type analogique._

## <a name="overview"></a>Vue d’ensemble

Dans cette procédure pas à pas, un service de visage Watch de base est créé pour illustrer les bases de la création d’un visage Android personnalisé 1,0 Watch.
Le service de visage de la montre initiale affiche un simple espion numérique qui affiche l’heure actuelle en heures et minutes :

[![Visage de la montre numérique](creating-a-watchface-images/01-initial-face.png "Exemple de capture d’écran du visage de la montre numérique initiale")](creating-a-watchface-images/01-initial-face.png#lightbox)

Une fois ce visage de la montre numérique développé et testé, davantage de code est ajouté pour le mettre à niveau vers une facette de montre analogique plus sophistiquée, avec trois mains :

[![Visage de montre analogique](creating-a-watchface-images/02-example-watchface.png "Exemple de capture d’écran du visage de la montre analogique finale")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Les services de visage Watch sont regroupés et installés dans le cadre d’une application 1,0. Dans les exemples suivants, `MainActivity` contient rien de plus que le code du modèle d’application usure 1,0, de sorte que le service de visage de la montre peut être empaqueté et déployé dans le Smart Watch dans le cadre de l’application. En effet, cette application servira uniquement de véhicule pour obtenir le service de visage de la montre chargé dans le périphérique 1,0 (ou l’émulateur) à des fins de débogage et de test.

## <a name="requirements"></a>Configuration requise

Pour implémenter un service de visage Watch, les conditions suivantes sont requises :

- Android 5,0 (niveau d’API 21) ou supérieur sur l’appareil ou l’émulateur d’usure.

- Les [bibliothèques de support Xamarin Android usure](https://www.nuget.org/packages/Xamarin.Android.Wear) doivent être ajoutées au projet Xamarin. Android.

Même si Android 5,0 est le niveau d’API minimal pour l’implémentation d’un service Watch face, Android 5,1 ou version ultérieure est recommandé. Les appareils Android d’usure exécutant Android 5,1 (API 22) ou une version ultérieure permettent aux applications d’utiliser l’usure de contrôler ce qui est affiché à l’écran pendant que l’appareil est en mode *ambiant* à faible consommation d’énergie. Lorsque l’appareil quitte le mode *ambiant* de faible puissance, il est en mode *interactif* . Pour plus d’informations sur ces modes, consultez [maintien de l’affichage de votre application](https://developer.android.com/training/wearables/apps/always-on.html).

## <a name="start-an-app-project"></a>Démarrer un projet d’application

Créez un projet Android d’usure 1,0 appelé **WatchFace** (pour plus d’informations sur la création de projets Xamarin. Android, consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)) :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Boîte de dialogue Nouveau projet](creating-a-watchface-images/03-wear-project-vs-sml.png "Sélectionner l’application d’usure dans la boîte de dialogue Nouveau projet")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Boîte de dialogue Nouveau projet](creating-a-watchface-images/03-wear-project-xs-sml.png "Sélectionner l’application d’usure dans la boîte de dialogue Nouveau projet")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----

Définissez le nom du package sur `com.xamarin.watchface` :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Paramètre de nom de package](creating-a-watchface-images/04-package-name-vs.png "Définissez le nom du package sur com. xamarin. watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Paramètre de nom de package](creating-a-watchface-images/04-package-name-xs.png "Définissez le nom du package sur com. xamarin. watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En outre, faites défiler l’accès et activez les autorisations **Internet** et **WAKE_LOCK** :

[![Autorisations requises](creating-a-watchface-images/05-required-permissions-vs.png "Activer les autorisations INTERNET et WAKE_LOCK")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Définissez la version d’Android minimale sur **android 5,1 (niveau d’API 22)**.
Activez également les autorisations **Internet** et **WakeLock** :

[![Autorisations requises](creating-a-watchface-images/05-required-permissions-xs.png "Activer les autorisations Internet et WakeLock")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Ensuite, téléchargez [preview.png](creating-a-watchface-images/preview.png) &ndash; ce sera ajouté au dossier **drawables** plus loin dans cette procédure pas à pas.

## <a name="add-the-xamarinandroid-wear-package"></a>Ajouter le package d’usure Xamarin. Android

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Démarrez le gestionnaire de package NuGet (dans Visual Studio, cliquez avec le bouton droit sur **références** dans le **Explorateur de solutions** puis sélectionnez **gérer les packages NuGet...**). Mettez à jour le projet vers la dernière version stable de **Xamarin. Android. usure**:

[![Gestionnaire de package NuGet-ajouter](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "Ajouter le package Xamarin. Android. usure")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Ensuite, si **Xamarin. Android. support. v13** est installé, désinstallez-le :

[![Gestionnaire de package NuGet-supprimer](creating-a-watchface-images/07-uninstall-v13-sml.png "Supprimer Xamarin. support. v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Démarrez le gestionnaire de package NuGet (dans Visual Studio pour Mac, cliquez avec le bouton droit sur **packages** dans le **volet solution** , puis sélectionnez **Ajouter des packages...**). Mettez à jour le projet vers la dernière version stable de **Xamarin. Android. usure**:

[![Gestionnaire de package NuGet-ajouter](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "Ajouter le package Xamarin. Android. usure")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----

Créer et exécuter l’application sur un appareil ou un émulateur d’usure (pour plus d’informations sur la façon de procéder, consultez le guide [prise en main](~/android/wear/get-started/index.md) ). Vous devez voir l’écran d’application suivant sur le périphérique d’usure :

[![Capture d’écran de l’application](creating-a-watchface-images/08-app-screen.png "Écran d’application sur un appareil d’usure")](creating-a-watchface-images/08-app-screen.png#lightbox)

À ce stade, l’application de base ne dispose pas d’une fonctionnalité de visage de surveillance, car elle ne fournit pas encore d’implémentation de service de visage de montre. Ce service sera ajouté ensuite.

## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

L’usure d’Android met en œuvre regarder des visages via la `CanvasWatchFaceService` classe. `CanvasWatchFaceService` est dérivé de `WatchFaceService` , lequel est dérivé de, `WallpaperService` comme indiqué dans le diagramme suivant :

[![Diagramme d’héritage](creating-a-watchface-images/09-inheritance-diagram-sml.png "Diagramme d’héritage CanvasWatchFaceService")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` comprend un imbriqué `CanvasWatchFaceService.Engine` ; Il instancie un `CanvasWatchFaceService.Engine` objet qui effectue le travail réel de dessin de la facette de la montre. `CanvasWatchFaceService.Engine` est dérivée de `WallpaperService.Engine` , comme indiqué dans le diagramme ci-dessus.

Qui n’est pas illustré dans ce diagramme est un `Canvas` qui `CanvasWatchFaceService` utilise pour dessiner la facette de la montre, &ndash; elle `Canvas` est transmise via la `OnDraw` méthode, comme décrit ci-dessous.

Dans les sections suivantes, un service de visage de montre personnalisé est créé en procédant comme suit :

1. Définissez une classe appelée `MyWatchFaceService` qui est dérivée de  `CanvasWatchFaceService` .

2. Dans `MyWatchFaceService` , créez une classe imbriquée appelée  `MyWatchFaceEngine` qui est dérivée de  `CanvasWatchFaceService.Engine` .

3. Dans `MyWatchFaceService` , implémentez une `CreateEngine` méthode qui instancie `MyWatchFaceEngine` et la retourne.

4. Dans `MyWatchFaceEngine` , implémentez la `OnCreate` méthode pour créer le style de visage Watch et effectuer d’autres tâches d’initialisation.

5. Implémentez la `OnDraw` méthode de `MyWatchFaceEngine` . Cette méthode est appelée chaque fois que la face de la montre doit être redessinée (c’est-à-dire *invalidée*). `OnDraw` est la méthode qui dessine (et redessine) les éléments de face tels que les heures, les minutes et les secondes.

6. Implémentez la `OnTimeTick` méthode de `MyWatchFaceEngine` .
    `OnTimeTick` est appelé au moins une fois par minute (dans les modes ambiants et interactifs) ou lorsque la date/l’heure a changé.

Pour plus d’informations sur `CanvasWatchFaceService` , consultez la documentation de l’API [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) Android.
De même, [CanvasWatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) explique l’implémentation réelle du visage de la montre.

### <a name="add-the-canvaswatchfaceservice"></a>Ajouter CanvasWatchFaceService

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Ajoutez un nouveau fichier appelé **MyWatchFaceService.cs** (dans Visual Studio, cliquez avec le bouton droit sur **WatchFace** dans le **Explorateur de solutions**, cliquez sur **Ajouter > nouvel élément...**, puis sélectionnez **classe**).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Ajoutez un nouveau fichier appelé **MyWatchFaceService.cs** (dans Visual Studio pour Mac, cliquez avec le bouton droit sur le projet **WatchFace** , cliquez sur **Ajouter > nouveau fichier...**, puis sélectionnez **classe vide**).

-----

Remplacez le contenu de ce fichier par le code suivant :

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (dérivée de `CanvasWatchFaceService` ) est le « programme principal » du visage de la montre. `MyWatchFaceService` implémente une seule méthode, `OnCreateEngine` , qui instancie et retourne un `MyWatchFaceEngine` objet ( `MyWatchFaceEngine` est dérivé de `CanvasWatchFaceService.Engine` ). L’objet instancié `MyWatchFaceEngine` doit être retourné en tant que `WallpaperService.Engine` . L’objet d’encapsulation `MyWatchFaceService` est passé au constructeur.

`MyWatchFaceEngine` est l’implémentation réelle de la montre &ndash; , qui contient le code qui dessine le visage de la montre. Il gère également les événements système tels que les modifications d’écran (modes ambiants/interactifs, extinction de l’écran, etc.).

### <a name="implement-the-engine-oncreate-method"></a>Implémenter la méthode OnCreate du moteur

La `OnCreate` méthode initialise le visage de la montre. Ajoutez le champ suivant à `MyWatchFaceEngine` :

```csharp
Paint hoursPaint;
```

Cet `Paint` objet sera utilisé pour dessiner l’heure actuelle sur le cadran de la montre. Ensuite, ajoutez la méthode suivante à `MyWatchFaceEngine` :

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` est appelé peu après le `MyWatchFaceEngine` démarrage de. Il configure `WatchFaceStyle` (qui contrôle la façon dont l’appareil d’usure interagit avec l’utilisateur) et instancie l' `Paint` objet qui sera utilisé pour afficher l’heure.

L’appel à `SetWatchFaceStyle` effectue les opérations suivantes :

1. Définit le *mode aperçu* sur `PeekModeShort` , ce qui entraîne l’affichage des notifications sous forme de petites cartes de « lecture » à l’écran.

2. Définit la visibilité de l’arrière-plan sur `Interruptive` , ce qui entraîne l’affichage rapide de l’arrière-plan d’une carte d’aperçu si elle représente une notification ininterrompue.

3. Désactive l’heure par défaut de l’interface utilisateur du système sur le visage de la montre afin que la face personnalisée puisse afficher l’heure à la place.

Pour plus d’informations sur ces options de style de visage, consultez la documentation de l’API Android [WatchFaceStyle. Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) .

Une fois `SetWatchFaceStyle` terminé, `OnCreate` instancie l' `Paint` objet ( `hoursPaint` ) et définit sa couleur sur blanc et sa taille de texte sur 48 pixels ([TEXTSIZE](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) doit être spécifié en pixels).

### <a name="implement-the-engine-ondraw-method"></a>Implémenter la méthode OnDraw du moteur

La méthode `OnDraw` est peut-être la méthode la plus importante `CanvasWatchFaceService.Engine` &ndash; . il s’agit de la méthode qui dessine des éléments de face tels que des chiffres et des mains de la facette de l’horloge.
Dans l’exemple suivant, il dessine une chaîne de temps sur le visage de la montre.
Ajoutez la méthode suivante à `MyWatchFaceEngine` :

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

Quand Android appelle `OnDraw` , il transmet une `Canvas` instance et les limites dans lesquelles la face peut être dessinée. Dans l’exemple de code ci-dessus, `DateTime` est utilisé pour calculer l’heure actuelle en heures et minutes (au format 12 heures). La chaîne d’heure obtenue est dessinée sur le canevas à l’aide de la `Canvas.DrawText` méthode. La chaîne s’affiche à 70 pixels du bord gauche et 80 pixels en bas du bord supérieur.

Pour plus d’informations sur la `OnDraw` méthode, consultez la documentation de l’API [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) Android.

### <a name="implement-the-engine-ontimetick-method"></a>Implémenter la méthode OnTimeTick du moteur

Android appelle périodiquement la `OnTimeTick` méthode pour mettre à jour l’heure indiquée par le visage de la montre. Elle est appelée au moins une fois par minute (dans les modes ambiants et interactifs), ou lorsque la date/l’heure ou le fuseau horaire ont changé. Ajoutez la méthode suivante à `MyWatchFaceEngine` :

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Cette implémentation de `OnTimeTick` appelle simplement `Invalidate` . La `Invalidate` méthode planifie `OnDraw` le redessin de la facette de la montre.

Pour plus d’informations sur la `OnTimeTick` méthode, consultez la documentation de l’API [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) Android.

## <a name="register-the-canvaswatchfaceservice"></a>Inscrire le CanvasWatchFaceService

`MyWatchFaceService` doit être inscrite dans le **AndroidManifest.xml** de l’application d’usure associée. Pour ce faire, ajoutez le code XML suivant à la `<application>` section :

```xml
<service
    android:name="watchface.MyWatchFaceService"
    android:label="Xamarin Sample"
    android:allowEmbedded="true"
    android:taskAffinity=""
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data
        android:name="android.service.wallpaper"
        android:resource="@xml/watch_face" />
    <meta-data
        android:name="com.google.android.wearable.watchface.preview"
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

Ce code XML effectue les opérations suivantes :

1. Définit l' `android.permission.BIND_WALLPAPER` autorisation. Cette autorisation donne au service de visage de la montre l’autorisation de changer le papier peint du système sur l’appareil. Notez que cette autorisation doit être définie dans la `<service>` section plutôt que dans la `<application>` section externe.

2. Définit une `watch_face` ressource. Cette ressource est un bref fichier XML qui déclare une `wallpaper` ressource (ce fichier sera créé dans la section suivante).

3. Déclare une image dessinable appelée `preview` qui sera affichée par l’écran de sélection du sélecteur de espion.

4. Comprend un `intent-filter` pour informer Android qu'  `MyWatchFaceService` il affichera un visage de montre.

Cela termine le code de l’exemple de base `WatchFace` . L’étape suivante consiste à ajouter les ressources nécessaires.

## <a name="add-resource-files"></a>Ajouter des fichiers de ressources

Avant de pouvoir exécuter le service espion, vous devez ajouter la ressource **Watch_Face** et l’image d’aperçu. Tout d’abord, créez un nouveau fichier XML à **ressources/XML/watch_face.xml** et remplacez son contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Définissez l’action de génération de ce fichier sur **AndroidResource**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Action de génération](creating-a-watchface-images/10-android-resource-vs.png "Définir l’action de génération sur AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Action de génération](creating-a-watchface-images/10-android-resource-xs.png "Définir l’action de génération sur AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Ce fichier de ressources définit un `wallpaper` élément simple qui sera utilisé pour le visage de la montre.

Si vous ne l’avez pas encore fait, téléchargez [preview.png](creating-a-watchface-images/preview.png).
Installez-le à **ressources/dessinables/preview.png**. Veillez à ajouter ce fichier au `WatchFace` projet. Cette image d’aperçu est présentée à l’utilisateur dans le sélecteur de visage Watch sur l’appareil usure. Pour créer une image d’aperçu pour votre propre visage de montre, vous pouvez prendre une capture d’écran du visage de la montre pendant qu’il est en cours d’exécution. (Pour plus d’informations sur les captures d’écran des appareils d’usure, consultez [capture d’écrans](~/android/wear/deploy-test/debug-on-device.md#screenshots)).

## <a name="try-it"></a>Essayez !

Générez et déployez l’application sur l’appareil d’usure. L’écran de l’application d’usure doit s’afficher comme avant. Pour activer la nouvelle facette, procédez comme suit :

1. Faites défiler vers la droite jusqu’à ce que l’arrière-plan de l’écran Watch s’affiche.

2. Touchez et maintenez le contact n’importe où sur l’arrière-plan de l’écran pendant deux secondes.

3. Faites défiler de gauche à droite pour parcourir les différentes faces de la montre.

4. Sélectionnez la face de l' **exemple Xamarin** (illustrée à droite) :

    [![Sélecteur watchface](creating-a-watchface-images/11-watchface-picker.png "Balayer pour localiser Xamarin exemple de visage de montre")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5. Appuyez sur l’exemple de visage Watch **Xamarin** pour le sélectionner.

Cela modifie le visage de la montre de l’appareil d’usure pour utiliser le service de visage de montre personnalisé mis en œuvre jusqu’à présent :

[![Visage de la montre numérique](creating-a-watchface-images/12-digital-watchface.png "Espion numérique personnalisé s’exécutant sur un appareil d’usure")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Il s’agit d’un visage de montre relativement brut, car l’implémentation de l’application est tellement minime (par exemple, elle n’inclut pas d’arrière-plan de la facette et n’appelle pas `Paint` les méthodes anti-alias pour améliorer l’apparence).
Toutefois, il implémente la fonctionnalité complète qui est requise pour créer une facette de montre personnalisée.

Dans la section suivante, ce visage de la montre sera mis à niveau vers une implémentation plus sophistiquée.

## <a name="upgrading-the-watch-face"></a>Mise à niveau du visage de la montre

Dans le reste de cette procédure pas à pas, `MyWatchFaceService` est mis à niveau pour afficher un visage de type analogique et il est étendu pour prendre en charge davantage de fonctionnalités. Les fonctionnalités suivantes seront ajoutées pour créer la facette de la montre mise à niveau :

1. Indique l’heure avec des mains d’heures, de minutes et de secondes analogues.

2. Réagit aux modifications de visibilité.

3. Répond aux modifications entre le mode ambiant et le mode interactif.

4. Lit les propriétés de l’appareil sous-jacent usure.

5. Met à jour automatiquement l’heure à laquelle une modification de fuseau horaire a lieu.

Avant d’implémenter les modifications de code ci-dessous, téléchargez [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), décompressez-les, puis déplacez les fichiers. png décompressés vers **ressources/dessinables** (remplacez le **preview.png**précédent). Ajoutez les nouveaux fichiers. png au `WatchFace` projet.

### <a name="update-engine-features"></a>Fonctionnalités du moteur de mise à jour

L’étape suivante consiste à mettre à niveau **MyWatchFaceService.cs** vers une implémentation qui dessine un visage de montre analogique et qui prend en charge de nouvelles fonctionnalités. Remplacez le contenu de **MyWatchFaceService.cs** par la version analogique du code de la facette watch dans [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (vous pouvez couper et coller cette source dans le **MyWatchFaceService.cs**existant).

Cette version de **MyWatchFaceService.cs** ajoute du code aux méthodes existantes et inclut des méthodes substituées supplémentaires pour ajouter d’autres fonctionnalités. Les sections suivantes fournissent une visite guidée du code source.

#### <a name="oncreate"></a>OnCreate

La méthode **OnCreate** mise à jour configure le style de visage Watch comme précédemment, mais il comprend quelques étapes supplémentaires :

1. Définit l’image d’arrière-plan sur la ressource **xamarin_background** qui se trouve dans les **ressources/dessinables-HDPI/xamarin_background.png**.

2. Initialise `Paint` des objets pour dessiner la main, la main et la seconde.

3. Initialise un `Paint` objet pour dessiner les graduations de l’heure autour du bord du visage de la montre.

4. Crée un minuteur qui appelle la `Invalidate` méthode (redessiner) afin que la deuxième main soit redessinée chaque seconde. Notez que ce minuteur est nécessaire car n' `OnTimeTick` appelle qu' `Invalidate` une fois par minute.

Cet exemple comprend une seule **xamarin_background.png** image ; Toutefois, vous souhaiterez peut-être créer une image d’arrière-plan différente pour chaque densité d’écran que votre espion personnalisé prendra en charge.

#### <a name="ondraw"></a>OnDraw

La méthode **OnDraw** mise à jour dessine une face de type analogique à l’aide des étapes suivantes :

1. Obtient l’heure actuelle, qui est maintenant conservée dans un `time` objet.

2. Détermine les limites de la surface de dessin et de son centre.

3. Dessine l’arrière-plan, mis à l’échelle pour s’ajuster à l’appareil lors du dessin de l’arrière-plan.

4. Dessine douze *graduations* autour de l’horloge (correspondant aux heures sur la face de l’horloge).

5. Calcule l’angle, la rotation et la longueur de chaque main de suivi.

6. Dessine chaque main sur la surface de la montre. Notez que la deuxième main n’est pas dessinée si la montre est en mode ambiant.

#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Cette méthode est appelée pour informer `MyWatchFaceEngine` des propriétés de l’appareil d’usure (comme le mode ambiant et la protection en cas d’ambiance basse bits). Dans `MyWatchFaceEngine` , cette méthode vérifie uniquement le mode ambiant faible bit (en mode ambiant faible bit, l’écran prend en charge moins de bits pour chaque couleur).

Pour plus d’informations sur cette méthode, consultez la documentation de l’API [OnPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) Android.

#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Cette méthode est appelée lorsque l’appareil d’usure entre ou quitte le mode ambiant. Dans l' `MyWatchFaceEngine` implémentation, le visage Watch désactive l’anticrénelage lorsqu’il est en mode ambiant.

Pour plus d’informations sur cette méthode, consultez la documentation de l’API [OnAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) Android.

#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Cette méthode est appelée chaque fois que la surveillance devient visible ou masquée. Dans `MyWatchFaceEngine` , cette méthode enregistre/annule l’inscription du récepteur de fuseau horaire (décrit ci-dessous) en fonction de l’état de visibilité.

Pour plus d’informations sur cette méthode, consultez la documentation de l’API [OnVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) Android.

### <a name="time-zone-feature"></a>Fonctionnalité de fuseau horaire

Le nouveau **MyWatchFaceService.cs** comprend également des fonctionnalités permettant de mettre à jour l’heure actuelle à chaque modification du fuseau horaire (par exemple, lors d’un déplacement dans des fuseaux horaires). Près de la fin de **MyWatchFaceService.cs**, une modification de fuseau horaire `BroadcastReceiver` est définie et gère les objets de tentative de modification de fuseau horaire :

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

Les `RegisterTimezoneReceiver` `UnregisterTimezoneReceiver` méthodes et sont appelées par la `OnVisibilityChanged` méthode.
`UnregisterTimezoneReceiver` est appelé lorsque l’état de visibilité du visage de la montre est remplacé par masqué. Lorsque la face de montre est de nouveau visible, `RegisterTimezoneReceiver` est appelé (consultez la `OnVisibilityChanged` méthode).

La méthode de moteur `RegisterTimezoneReceiver` déclare un gestionnaire pour cet événement du destinataire du fuseau horaire `Receive` ; ce gestionnaire met à jour l' `time` objet avec la nouvelle heure à chaque fois qu’un fuseau horaire est franchi :

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Un filtre d’intention est créé et inscrit pour le récepteur de fuseau horaire :

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

La `UnregisterTimezoneReceiver` méthode annule l’inscription du récepteur de fuseau horaire :

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Exécuter le visage de montre amélioré

Générez et déployez à nouveau l’application sur le périphérique d’usure. Sélectionnez le visage Watch dans le sélecteur de visage Watch comme auparavant. L’aperçu dans le sélecteur de espions s’affiche à gauche, et la nouvelle face de montre s’affiche à droite :

[![Visage de montre analogique](creating-a-watchface-images/13-analog-watchface.png "Face analogique améliorée dans le sélecteur et sur l’appareil")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

Dans cette capture d’écran, la deuxième main est déplacée une fois par seconde. Lorsque vous exécutez ce code sur un appareil d’usure, la seconde main disparaît lorsque la montre passe en mode ambiant.

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, un Android personnalisé 1,0 watchface a été implémenté et testé. Les `CanvasWatchFaceService` `CanvasWatchFaceService.Engine` classes et ont été introduites, et les méthodes essentielles de la classe Engine ont été implémentées pour créer un simple visage de montre numérique. Cette implémentation a été mise à jour avec davantage de fonctionnalités pour créer une face de montre analogique, et des méthodes supplémentaires ont été implémentées pour gérer les modifications de visibilité, le mode ambiant et les différences dans les propriétés de l’appareil. Enfin, un récepteur de diffusion de fuseau horaire a été implémenté afin que la surveillance met automatiquement à jour l’heure à laquelle un fuseau horaire est franchi.

## <a name="related-links"></a>Liens associés

- [Création de visages de Watch](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Exemple WatchFace](/samples/xamarin/monodroid-samples/wear-watchface)
- [WatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)