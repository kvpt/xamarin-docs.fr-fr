---
title: Entrées tactiles dans Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: e9810eed3affb15f581b95aec1aff9ae560ff63c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754752"
---
# <a name="touch-in-android"></a>Entrées tactiles dans Android

À l’instar d’iOS, Android crée un objet qui contient des données sur l’interaction physique de &ndash; l' `Android.View.MotionEvent` utilisateur avec l’écran d’un objet. Cet objet contient des données telles que l’action effectuée, l’endroit où la pression tactile a eu lieu, la pression appliquée, etc. Un `MotionEvent` objet décompose le déplacement vers jusqu’aux valeurs suivantes :

- Code d’action qui décrit le type de mouvement, tel que le toucher initial, le toucher qui se déplace sur l’écran ou la fin de la touche tactile.

- Ensemble de valeurs d’axe qui décrivent la position du `MotionEvent` et des autres propriétés de mouvement telles que l’endroit où le toucher a lieu, le moment où le toucher a eu lieu et la pression utilisée.
   Les valeurs de l’axe peuvent être différentes en fonction de l’appareil, de sorte que la liste précédente ne décrit pas toutes les valeurs d’axe.

L' `MotionEvent` objet est passé à une méthode appropriée dans une application. Il existe trois façons pour une application Xamarin. Android de répondre à un événement tactile :

- *Assigner un gestionnaire `View.Touch` d’événements à* : la `EventHandler<View.TouchEventArgs>` `Android.Views.View` classe a un auquel les applications peuvent assigner un gestionnaire. Il s’agit d’un comportement .NET classique.

- *Implémentation`View.IOnTouchListener`* -les instances de cette interface peuvent être assignées à un objet de vue à l’aide de la vue. `SetOnListener`méthode. Cela équivaut de façon fonctionnelle à l’affectation d’un gestionnaire d’événements `View.Touch` à l’événement. S’il existe une logique commune ou partagée qui peut nécessiter de nombreuses vues différentes quand elles sont touchées, il est plus efficace de créer une classe et d’implémenter cette méthode que d’assigner à chaque vue son propre gestionnaire d’événements.

- *Remplacer`View.OnTouchEvent`* toutes les vues dans la sous-classe `Android.Views.View`Android. Quand une vue est touchée, Android appelle le `OnTouchEvent` et lui passe un `MotionEvent` objet en tant que paramètre.

> [!NOTE]
> Tous les appareils Android ne prennent pas en charge les écrans tactiles. 

Si vous ajoutez la balise suivante à votre fichier manifeste, Google Play n’affichera que votre application sur les appareils tactiles activés :

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Mouvements

Un geste est une forme dessinée à la main sur l’écran tactile. Un mouvement peut comporter un ou plusieurs traits, chaque trait constitué d’une séquence de points créée par un point de contact différent avec l’écran. Android peut prendre en charge de nombreux types différents de gestes, d’un simple Fling à l’écran jusqu’à des gestes complexes qui impliquent une interaction multipoint.

Android fournit l' `Android.Gestures` espace de noms spécifiquement pour la gestion et la réponse aux gestes. Au cœur de tous les gestes se trouve une classe spéciale appelée `Android.Gestures.GestureDetector`. Comme son nom l’indique, cette classe écoute les gestes et les événements basés sur `MotionEvents` fournis par le système d’exploitation.

Pour implémenter un détecteur de mouvements, une activité doit `GestureDetector` instancier une classe et fournir `IOnGestureListener`une instance de, comme l’illustre l’extrait de code suivant :

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Une activité doit également implémenter OnTouchEvent et transmettre le MotionEvent au détecteur de mouvements. L’extrait de code suivant en montre un exemple :

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Lorsqu’une instance de `GestureDetector` identifie un mouvement d’intérêt, elle avertit l’activité ou l’application en déclenchant un événement ou via un rappel fourni `GestureDetector.IOnGestureListener`par.
Cette interface fournit six méthodes pour les différents mouvements :

- *OnDown* : appelée lorsqu’un TAP se produit, mais n’est pas libéré.

- *OnFling* : appelée lorsqu’un Fling se produit et fournit des données sur les fonctions tactiles de début et de fin qui ont déclenché l’événement.

- *OnLongPress* : appelée lorsqu’une pression longue s’est produite.

- *OnScroll* : appelé lorsqu’un événement de défilement se produit.

- *OnShowPress* : appelé après qu’un OnDown s’est produit et qu’un événement de déplacement ou de déplacement n’a pas été effectué.

- *OnSingleTapUp* : appelée lorsqu’un seul TAP se produit.

Dans de nombreux cas, les applications peuvent uniquement être intéressées par un sous-ensemble de gestes. Dans ce cas, les applications doivent étendre la classe GestureDetector. SimpleOnGestureListener et remplacer les méthodes qui correspondent aux événements qui les intéressent.

## <a name="custom-gestures"></a>Mouvements personnalisés

Les gestes sont un excellent moyen pour les utilisateurs d’interagir avec une application. Les API que nous avons vu jusqu’à présent suffisent pour des mouvements simples, mais elles peuvent prouver un peu coûteux pour des gestes plus compliqués. Pour faciliter l’utilisation de gestes plus compliqués, Android fournit un autre ensemble d’API dans l’espace de noms Android. gestes qui facilitera la tâche associée aux gestes personnalisés.

### <a name="creating-custom-gestures"></a>Création de mouvements personnalisés

Depuis Android 1,6, le Android SDK est fourni avec une application préinstallée sur l’émulateur appelé « générateur de mouvements ». Cette application permet à un développeur de créer des mouvements prédéfinis qui peuvent être incorporés dans une application. La capture d’écran suivante montre un exemple de générateur de mouvements :

[![Capture d’écran du générateur de mouvements avec des exemples de mouvements](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Une version améliorée de cette application appelée geste Tool se trouve Google Play. L’outil geste ressemble beaucoup à un générateur de mouvements, sauf qu’il vous permet de tester les gestes une fois qu’ils ont été créés. La capture d’écran suivante montre le générateur de mouvements :

[![Capture d’écran de l’outil geste avec des exemples de mouvements](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

L’outil geste est un peu plus utile pour créer des mouvements personnalisés, car il permet de tester les gestes au fur et à mesure qu’ils sont créés et est facilement disponible par le biais de Google Play.

L’outil geste vous permet de créer un mouvement en dessinant à l’écran et en attribuant un nom. Une fois les gestes créés, ils sont enregistrés dans un fichier binaire sur la carte SD de votre appareil. Ce fichier doit être récupéré à partir de l’appareil, puis empaqueté avec une application dans le dossier/Resources/RAW. Ce fichier peut être récupéré à partir de l’émulateur à l’aide de l’Android Debug Bridge. L’exemple suivant illustre la copie du fichier à partir d’un accès Galaxy vers le répertoire de ressources d’une application :

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Une fois que vous avez récupéré le fichier, il doit être empaqueté avec votre application dans le répertoire/Resources/RAW. Le moyen le plus simple d’utiliser ce fichier de mouvement consiste à charger le fichier dans un GestureLibrary, comme illustré dans l’extrait de code suivant :

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Utilisation de mouvements personnalisés

Pour reconnaître des mouvements personnalisés dans une activité, un objet Android. geste. GestureOverlay doit être ajouté à sa disposition. L’extrait de code suivant montre comment ajouter par programme un GestureOverlayView à une activité :

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

L’extrait de code XML suivant montre comment ajouter un GestureOverlayView de façon déclarative :

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

Le `GestureOverlayView` a plusieurs événements qui seront déclenchés pendant le processus de dessin d’un mouvement. L’événement le plus intéressant `GesturePerformed`est. Cet événement est déclenché lorsque l’utilisateur a terminé de dessiner son geste.

Lorsque cet événement est déclenché, l’activité demande à `GestureLibrary` un d’essayer et de faire correspondre le mouvement de l’utilisateur avec l’un des mouvements créés par l’outil geste. `GestureLibrary`retourne une liste d’objets de prédiction.

Chaque objet de prédiction contient un score et un nom de l’un des mouvements dans `GestureLibrary`le. Plus le score est élevé, plus le geste nommé dans la prédiction correspond au geste dessiné par l’utilisateur.
En règle générale, les scores inférieurs à 1,0 sont considérés comme des correspondances médiocres.

Le code suivant illustre un exemple de correspondance d’un mouvement :

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

Une fois cette opération terminée, vous devez comprendre comment utiliser les fonctions tactiles et les gestes dans une application Xamarin. Android. Passons maintenant à une procédure pas à pas et voyons tous les concepts d’un exemple d’application fonctionnel.

## <a name="related-links"></a>Liens associés

- [Android touch Start (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
