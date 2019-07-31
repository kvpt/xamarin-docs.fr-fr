---
title: Procédure pas à pas-emplacement en arrière-plan dans Xamarin. iOS
description: Ce document fournit une procédure pas à pas d’utilisation des informations d’emplacement dans une application Xamarin. iOS en arrière-plan. Il décrit l’installation, l’interface utilisateur et les États d’application nécessaires.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 20f49f3f0c103791064545311d9f66d409cff357
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656539"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Procédure pas à pas-emplacement en arrière-plan dans Xamarin. iOS

Dans cet exemple, nous allons créer une application d’emplacement iOS qui imprime des informations sur notre emplacement actuel: Latitude, longitude et d’autres paramètres à l’écran. Cette application montre comment effectuer correctement les mises à jour de l’emplacement lorsque l’application est active ou en arrière-plan.

Cette procédure pas à pas explique certains concepts fondamentaux de l’arrière-plan, notamment l’inscription d’une application en tant qu’application requise en arrière-plan, la suspension des mises à jour `WillEnterBackground` de `WillEnterForeground` l’interface utilisateur quand l’application est en arrière-plan et l’utilisation des méthodes et `AppDelegate` . .

## <a name="application-set-up"></a>Configuration de l’application


1. Tout d’abord, créez une **application de > iOS > application avecC#affichage unique ()** . Appelez- _le,_ puis vérifiez que iPad et iPhone ont été sélectionnés.

1. Une application d’emplacement qualifie comme une application requise en arrière-plan dans iOS. Inscrivez l’application en tant qu’application d’emplacement en modifiant le fichier **info. plist** pour le projet.

    Sous Explorateur de solutions, double-cliquez sur le fichier **info. plist** pour l’ouvrir, puis faites défiler la liste jusqu’en bas. Activez les cases à cocher **activer les modes d’arrière-plan** et **mises à jour** de l’emplacement.

    Dans Visual Studio pour Mac, elle ressemble à ce qui suit:

    [![](location-walkthrough-images/image7.png "Activez les cases à cocher Activer les modes d’arrière-plan et mises à jour de l’emplacement.")](location-walkthrough-images/image7.png#lightbox)

    Dans Visual Studio, **info. plist** doit être mis à jour manuellement en ajoutant la paire clé/valeur suivante:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Maintenant que l’application est inscrite, elle peut récupérer les données d’emplacement à partir de l’appareil. Dans iOS, la `CLLocationManager` classe est utilisée pour accéder aux informations d’emplacement et peut déclencher des événements qui fournissent des mises à jour d’emplacement.

1. Dans le code, créez une classe appelée `LocationManager` qui fournit un emplacement unique pour différents écrans et code pour vous abonner aux mises à jour de l’emplacement. Dans la `LocationManager` classe, créez une instance `CLLocationManager` du appelé `LocMgr`:

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    Le code ci-dessus définit un certain nombre de propriétés et d’autorisations sur la classe [CLLocationManager](xref:CoreLocation.CLLocationManager) :

    - `PausesLocationUpdatesAutomatically`: Il s’agit d’une valeur booléenne qui peut être définie selon que le système est autorisé ou non à suspendre les mises à jour de l’emplacement. Sur un appareil `true`, la valeur par défaut est, ce qui peut amener l’appareil à cesser d’obtenir des mises à jour de l’emplacement d’arrière-plan après environ 15 minutes.
    - `RequestAlwaysAuthorization`-Vous devez passer cette méthode pour donner à l’utilisateur de l’application la possibilité d’autoriser l’accès à l’emplacement en arrière-plan. `RequestWhenInUseAuthorization`peut également être passé si vous souhaitez donner à l’utilisateur la possibilité d’autoriser l’accès à l’emplacement uniquement lorsque l’application est au premier plan.
    - `AllowsBackgroundLocationUpdates`: Il s’agit d’une propriété booléenne, introduite dans iOS 9, qui peut être définie pour permettre à une application de recevoir des mises à jour d’emplacement lorsqu’elle est suspendue.

    > [!IMPORTANT]
    > iOS 8 (et versions ultérieures) nécessite également une entrée dans le fichier **info. plist** pour afficher l’utilisateur dans le cadre de la demande d’autorisation.

1. Ajoutez une clé `NSLocationAlwaysUsageDescription` ou `NSLocationWhenInUseUsageDescription` une chaîne qui sera affichée à l’utilisateur dans l’alerte qui demande l’accès aux données d’emplacement.

1. iOS 9 requiert que, lors `AllowsBackgroundLocationUpdates` de l’utilisation du fichier **info. plist** , `location`la clé `UIBackgroundModes` avec la valeur est incluse. Si vous avez terminé l’étape 2 de cette procédure pas à pas, vous devez déjà avoir dans votre fichier info. plist.


1. À l' `LocationManager` intérieur de la classe, créez `StartLocationUpdates` une méthode appelée avec le code suivant. Ce code montre comment démarrer la réception des mises à jour de l’emplacement `CLLocationManager`à partir du:

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    Il existe plusieurs éléments importants qui se produisent dans cette méthode. Tout d’abord, nous procédons à une vérification pour voir si l’application a accès aux données d’emplacement sur l’appareil. Nous vérifions cela en `LocationServicesEnabled` appelant `CLLocationManager`sur. Cette méthode retourne la **valeur false** si l’utilisateur a refusé l’accès à l’application aux informations relatives à l’emplacement.

1. Ensuite, indiquez au gestionnaire d’emplacement la fréquence de mise à jour. `CLLocationManager`fournit de nombreuses options pour filtrer et configurer les données d’emplacement, y compris la fréquence des mises à jour. Dans cet exemple, définissez le `DesiredAccuracy` à mettre à jour chaque fois que l’emplacement change d’un compteur. Pour plus d’informations sur la configuration de la fréquence de mise à jour de l’emplacement et d’autres préférences, reportez-vous à la référence de la [classe CLLocationManager](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) dans la documentation Apple.

1. Enfin, appelez `StartUpdatingLocation` sur l' `CLLocationManager` instance. Cela indique au gestionnaire d’emplacement d’avoir un correctif initial à l’emplacement actuel et de commencer à envoyer des mises à jour

Jusqu’à présent, le gestionnaire d’emplacement a été créé, configuré avec les types de données que vous souhaitez recevoir et a déterminé l’emplacement initial. À présent, le code doit restituer les données d’emplacement dans l’interface utilisateur. Nous pouvons le faire avec un événement personnalisé qui prend `CLLocation` comme argument:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

L’étape suivante consiste à s’abonner à des mises à jour `CLLocationManager`de localisation à partir du `LocationUpdated` et à déclencher l’événement personnalisé quand de nouvelles données d’emplacement deviennent disponibles, en passant l’emplacement en tant qu’argument. Pour ce faire, créez une nouvelle classe **LocationUpdateEventArgs.cs**. Ce code est accessible dans l’application principale et retourne l’emplacement de l’appareil lorsque l’événement est déclenché:

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>Interface utilisateur

1. Utilisez le concepteur iOS pour créer l’écran qui affichera les informations relatives à l’emplacement. Double-cliquez sur le fichier **main. Storyboard** pour commencer.

    Sur le Storyboard, faites glisser plusieurs étiquettes sur l’écran pour servir d’espaces réservés pour les informations d’emplacement. Dans cet exemple, il existe des étiquettes pour la latitude, la longitude, l’altitude, le cours et la vitesse.

    La disposition doit ressembler à ce qui suit:

    ![](location-walkthrough-images/image8.png "Exemple de disposition d’interface utilisateur dans le concepteur iOS")

1. Dans la panneau solutions, double-cliquez sur `ViewController.cs` le fichier et modifiez-le pour créer une nouvelle instance du LocationManager et `StartLocationUpdates`appeler dessus.
  Modifiez le code pour qu’il ressemble à ce qui suit:

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
                get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
            }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    Cette opération démarre les mises à jour de l’emplacement au démarrage de l’application, bien qu’aucune donnée ne s’affiche.

1. Maintenant que les mises à jour de l’emplacement sont reçues, mettez à jour l’écran avec les informations relatives à l’emplacement. La méthode suivante obtient l’emplacement à partir `LocationUpdated` de notre événement et l’affiche dans l’interface utilisateur:

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

Nous devons encore vous abonner à `LocationUpdated` l’événement dans notre AppDelegate et appeler la nouvelle méthode pour mettre à jour l’interface utilisateur. Ajoutez le code `ViewDidLoad,` suivant juste après l' `StartLocationUpdates` appel:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


Désormais, lorsque l’application est exécutée, elle doit ressembler à ceci:

[![](location-walkthrough-images/image5.png "Exemple d’exécution d’application")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Gestion des États actifs et d’arrière-plan

1. L’application expose les mises à jour de l’emplacement alors qu’elle se trouve au premier plan et active. Pour illustrer ce qui se produit lorsque l’application entre en arrière- `AppDelegate` plan, substituez les méthodes qui effectuent le suivi des modifications de l’état de l’application afin que l’application écrit dans la console lors de la transition entre le premier plan et l’arrière-plan:

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    Ajoutez le code suivant dans la `LocationManager` pour imprimer en continu les données d’emplacement mises à jour vers la sortie de l’application, afin de vérifier que les informations d’emplacement sont toujours disponibles en arrière-plan:

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. Il reste un problème avec le code: Si vous tentez de mettre à jour l’interface utilisateur lorsque l’application est en arrière-plan, iOS l’arrête. Lorsque l’application passe en arrière-plan, le code doit se désabonner des mises à jour de l’emplacement et arrêter la mise à jour de l’interface utilisateur.

    iOS fournit des notifications lorsque l’application est sur le paragraphe de la transition vers un état d’application différent. Dans ce cas, nous pouvons m’abonner `ObserveDidEnterBackground` à la notification.

    L’extrait de code suivant montre comment utiliser une notification pour permettre à la vue de savoir quand arrêter les mises à jour de l’interface utilisateur. Cela va dans `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Quand l’application est en cours d’exécution, la sortie se présente comme suit:

    ![](location-walkthrough-images/image6.png "Exemple de sortie d’emplacement dans la console")

1. L’application imprime les mises à jour de l’emplacement à l’écran lors de son exécution au premier plan, puis continue à imprimer les données dans la fenêtre de sortie de l’application tout en opérant en arrière-plan.

Un seul problème en suspens subsiste: l’écran démarre les mises à jour de l’interface utilisateur lorsque l’application est chargée pour la première fois, mais il n’a aucun moyen de savoir quand l’application est à nouveau entrée au premier plan. Si l’application en arrière-plan est replacée au premier plan, les mises à jour de l’interface utilisateur ne sont pas reprises.

Pour résoudre ce problème, imbriquez un appel pour démarrer les mises à jour de l’interface utilisateur dans une autre notification, qui se déclenche lorsque l’application passe à l’état actif:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Désormais, l’interface utilisateur commence à se mettre à jour lorsque l’application est démarrée pour la première fois et reprend la mise à jour à chaque fois que l’application revient au premier plan.

Dans cette procédure pas à pas, nous avons créé une application iOS avec prise en charge bien comprise qui imprime les données de localisation à la fois sur l’écran et dans la fenêtre de sortie de l’application.


## <a name="related-links"></a>Liens associés

- [Emplacement (partie 4) (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [Référence de l’infrastructure d’emplacement principal](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
