---
title: HomeKit dans Xamarin.iOS
description: HomeKit est un framework d’Apple permettant de contrôler les appareils domotique. Cet article présente les HomeKit et couvre la configuration accessoires de test dans le simulateur d’accessoires HomeKit et écrivez une application Xamarin.iOS simple d’interagir avec ces Accessoires.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 9daedbe9bba5a2923a247104c4e69ae2e1b635aa
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865771"
---
# <a name="homekit-in-xamarinios"></a>HomeKit dans Xamarin.iOS

_HomeKit est un framework d’Apple permettant de contrôler les appareils domotique. Cet article présente les HomeKit et couvre la configuration accessoires de test dans le simulateur d’accessoires HomeKit et écrivez une application Xamarin.iOS simple d’interagir avec ces Accessoires._

[![](homekit-images/accessory01.png "Application activée pour un exemple HomeKit")](homekit-images/accessory01.png#lightbox)

Apple a introduit HomeKit dans iOS 8 comme un moyen d’intégrer en toute transparence plusieurs périphériques de domotique à partir d’un fournisseurs diverses en une unité unique et cohérente. Par la promotion d’un protocole commun pour la découverte, configuration et contrôle des appareils de la domotique, HomeKit permet aux appareils à partir de non lié à des fournisseurs de travailler ensemble, tout cela sans avoir à coordonner les efforts de fournisseurs individuels.

Avec HomeKit, vous pouvez créer une application Xamarin.iOS qui contrôle n’importe quel appareil HomeKit activé sans à l’aide du fournisseur fourni API ou des applications. L’utilisation d’HomeKit, vous pouvez procédez comme suit :

- Détecter les nouveaux appareils de domotique HomeKit activée et les ajouter à une base de données qui rend persistantes sur l’ensemble des appareils iOS de l’utilisateur.
- Le programme d’installation, configurer, afficher et contrôler n’importe quel appareil dans le HomeKit _base de données de Configuration accueil_.
- Communiquer avec n’importe quel appareil HomeKit préconfiguré et des commandes à effectuer des actions individuelles ou fonctionnent de concert, telles que l’allumage sur toutes les lumières dans la cuisine.

Outre son utilisation des appareils dans la base de Configuration accueil aux applications d’HomeKit activé, HomeKit fournit l’accès aux commandes vocales de Siri. Étant donné un programme d’installation HomeKit correctement configuré, l’utilisateur peut émettre des commandes vocales tel que « Siri, allumer l’éclairage dans le salon. »

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>La base de données de Configuration de base

HomeKit organise tous les appareils d’automation dans un emplacement donné dans une Collection d’accueil. Cette collection fournit un moyen de l’utilisateur regrouper leurs appareils domotique en unités logiquement organisées avec des étiquettes significatives, contrôlable de visu.

La Collection d’accueil est stockée dans une base de Configuration accueil sera automatiquement sauvegardée et synchronisés sur l’ensemble des appareils iOS de l’utilisateur. HomeKit fournit les classes suivantes pour travailler avec la base de données accueil Configuration :

- `HMHome` -Il s’agit du conteneur de niveau supérieur qui contient toutes les informations et les configurations pour tous les appareils domotique dans un seul emplacement physique (par exemple). une seule famille résidence). L’utilisateur peut avoir plusieurs résidence, telles que leur accueil principale et une maison de vacances. Ou ils peuvent avoir différentes « héberge » sur la même propriété, telles que la maison principale et une maison invité sur le garage. Dans les deux cas, au moins un `HMHome` objet _doit_ être configuré et stockées avant toutes les autres informations HomeKit peuvent être entrées.
- `HMRoom` Bien que facultatif, un `HMRoom` permet à l’utilisateur définir des salles spécifiques à l’intérieur d’une maison (`HMHome`) tel que : Cuisine, salle de bain, Garage ou salon. L’utilisateur peut grouper tous les appareils domotique à un emplacement spécifique dans leur maison dans un `HMRoom` et agir sur eux en tant qu’unité. Par exemple, vous demandant Siri à éteindre la lumière garage.
- `HMAccessory` -Il s’agit d’un individu, HomeKit physique activé appareil automation a été installé dans le lieu de résidence de l’utilisateur (par exemple, un thermostat intelligent). Chaque `HMAccessory` est affecté à un `HMRoom`. Si l’utilisateur n’a pas configuré les salles de conversation, HomeKit affecte accessoires à une salle par défaut spéciale.
- `HMService` : Représente un service fourni par une donnée `HMAccessory`, telles que l’état activé/désactivé d’une lumière ou sa couleur (si le changement de couleur est pris en charge). Chaque `HMAccessory` peut avoir plusieurs services, tels que d’une porte de garage qui inclut également une lumière. En outre, une donnée `HMAccessory` peut avoir des services, tels que de la mise à jour du microprogramme, qui sont trouvent en dehors du contrôle utilisateur.
- `HMZone` -Permet à l’utilisateur regrouper un ensemble de `HMRoom` des objets dans des zones logiques, tels qu’attirail, Downstairs ou sous-sol. Bien que facultative, ainsi, pour les interactions comme demander Siri pour activer la lumière downstairs désactivé.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Provisionnement d’une application HomeKit

En raison des exigences de sécurité imposées par HomeKit, une application Xamarin.iOS qui utilise le framework HomeKit doit être correctement configurée dans les deux le portail des développeurs Apple et dans le fichier projet Xamarin.iOS.

Effectuez ce qui suit :

1. Connectez-vous à la [portail des développeurs Apple](https://developer.apple.com).
2. Cliquez sur **certificats, identificateurs et profils**.
3. Si vous n’avez pas déjà fait, cliquez sur **identificateurs** et créer un ID pour votre application (par exemple, `com.company.appname`), sinon modifier votre code existant.
4. Vérifiez que le **HomeKit** service a été activée pour l’ID donné : 

    [![](homekit-images/provision01.png "Activer le service d’HomeKit pour l’ID donné")](homekit-images/provision01.png#lightbox)
5. Enregistrez les modifications apportées.
6. Cliquez sur **profils de provisionnement** > **développement** et créer un environnement de développement nouveau profil de configuration pour votre application : 

    [![](homekit-images/provision02.png "Créer un environnement de développement nouveau profil de configuration pour l’application")](homekit-images/provision02.png#lightbox)
7. Télécharger et installer le nouveau profil de provisionnement ou utiliser Xcode pour télécharger et installer le profil.
8. Modifier les options de votre projet Xamarin.iOS et vous assurer que vous utilisez le profil de configuration que vous venez de créer : 

    [![](homekit-images/provision03.png "Sélectionnez le profil de provisionnement venez de créer")](homekit-images/provision03.png#lightbox)
9. Ensuite, modifiez votre **Info.plist** de fichiers et de vous assurer que vous utilisez l’ID d’application qui a été utilisé pour créer le profil de provisionnement : 

    [![](homekit-images/provision04.png "Définir l’ID d’application ")](homekit-images/provision04.png#lightbox)
10. Enfin, modifiez votre **Entitlements.plist** de fichiers et vérifiez que le **HomeKit** droit a été sélectionné : 

    [![](homekit-images/provision05.png "Activer le droit d’HomeKit")](homekit-images/provision05.png#lightbox)
11. Enregistrez les modifications à tous les fichiers.

Ces paramètres en place, l’application est maintenant prête à accéder aux API de Framework HomeKit. Pour obtenir des informations détaillées sur l’approvisionnement, consultez notre [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) et [l’approvisionnement de votre application](~/ios/get-started/installation/device-provisioning/index.md) guides.

> [!IMPORTANT]
> Test d’une application HomeKit activée nécessite un appareil iOS réels qui a été configuré correctement pour le développement. HomeKit ne peut pas être testée dans le simulateur iOS.

## <a name="the-homekit-accessory-simulator"></a>Le simulateur d’accessoire de HomeKit

Pour fournir un moyen de tester tous les appareils de la domotique possibles et les services, sans devoir disposer d’un appareil physique, Apple créé le _HomeKit accessoire simulateur_. À l’aide de ce simulateur, vous pouvez configurer et configurer des appareils virtuels de HomeKit.

### <a name="installing-the-simulator"></a>Installez le simulateur

Apple fournit le simulateur d’accessoires HomeKit sous forme de téléchargement distinct à partir de Xcode, vous devrez installer avant de continuer.

Effectuez ce qui suit :

1. Dans un navigateur web, visitez [téléchargements pour les développeurs d’Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Téléchargez le **des outils supplémentaires pour Xcode xxx** (où xxx est la version de Xcode que vous avez installée) : 

    [![](homekit-images/simulator01.png "Téléchargez les outils supplémentaires pour Xcode")](homekit-images/simulator01.png#lightbox)
3. Ouvrir l’image de disque et installez les outils dans votre **Applications** directory.

Avec le simulateur d’accessoires HomeKit installé, Accessoires virtuels peuvent être créés pour le test.

### <a name="creating-virtual-accessories"></a>Création d’accessoires virtuels

Pour démarrer le simulateur d’accessoires HomeKit et créer quelques accessoires virtuels, procédez comme suit :

1. À partir du dossier d’Applications, démarrer le simulateur d’accessoires HomeKit : 

    [![](homekit-images/simulator02.png "Le simulateur d’accessoire de HomeKit")](homekit-images/simulator02.png#lightbox)
2. Cliquez sur le **+** bouton et sélectionnez **accessoire nouvelle...** : 

    [![](homekit-images/simulator03.png "Ajouter un nouvel accessoire")](homekit-images/simulator03.png#lightbox)
3. Remplissez les informations sur le nouvel accessoire et cliquez sur le **Terminer** bouton : 

    [![](homekit-images/simulator04.png "Remplissez les informations sur le nouvel accessoire")](homekit-images/simulator04.png#lightbox)
4. Cliquez sur le **ajouter Service...** bouton, puis sélectionnez un type de service dans la liste déroulante : 

    [![](homekit-images/simulator05.png "Sélectionnez un type de service dans la liste déroulante")](homekit-images/simulator05.png#lightbox)
5. Fournir un **nom** pour le service et cliquez sur le **Terminer** bouton : 

    [![](homekit-images/simulator06.png "Entrez un nom pour le service")](homekit-images/simulator06.png#lightbox)
6. Vous pouvez fournir les caractéristiques facultatives pour un service en cliquant sur le **ajouter une caractéristique** bouton et la configuration des paramètres requis : 

    [![](homekit-images/simulator07.png "Configuration des paramètres requis")](homekit-images/simulator07.png#lightbox)
7. Répétez les étapes ci-dessus pour créer l’un de chaque type d’appareil virtuel domotique qui prend en charge de HomeKit.

Avec certains exemples virtuel des accessoires HomeKit créé et configuré, vous pouvez maintenant utiliser et contrôler ces appareils à partir de votre application Xamarin.iOS.

## <a name="configuring-the-infoplist-file"></a>Configuration du fichier Info.plist

Nouveau dans iOS 10 (et version ultérieure), le développeur doit ajouter la `NSHomeKitUsageDescription` clé à l’application `Info.plist` de fichiers et de fournir une chaîne de déclaration pourquoi l’application souhaite accéder à la base de données de l’utilisateur HomeKit. Cette chaîne sera présentée à l’utilisateur la première exécution de l’application :

[![](homekit-images/info01.png "La boîte de dialogue autorisation HomeKit")](homekit-images/info01.png#lightbox)

Pour définir cette clé, procédez comme suit :

1. Double-cliquez sur le `Info.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. En bas de l’écran, basculez vers le **Source** vue.
3. Ajouter un nouveau **entrée** à la liste.
4. Dans la liste déroulante, sélectionnez **confidentialité - Description de l’utilisation de HomeKit**: 

    [![](homekit-images/info02.png "Sélectionnez confidentialité - Description de l’utilisation de HomeKit")](homekit-images/info02.png#lightbox)
5. Entrez une description pour la raison pour laquelle l’application souhaite accéder à la base de données de l’utilisateur HomeKit : 

    [![](homekit-images/info03.png "Entrez une description")](homekit-images/info03.png#lightbox)
6. Enregistrez les modifications dans le fichier.

> [!IMPORTANT]
> Échec de définition de la `NSHomeKitUsageDescription` clé dans le `Info.plist` fichier entraîne l’application _échouent silencieusement_ (en cours de fermeture par le système lors de l’exécution) sans erreur lorsque exécutez dans iOS 10 (ou version ultérieure).

## <a name="connecting-to-homekit"></a>Connexion à HomeKit

Pour communiquer avec HomeKit, votre application Xamarin.iOS doit tout d’abord instancier une instance de la `HMHomeManager` classe. Le gestionnaire accueil est le point d’entrée central dans HomeKit et est chargé de fournir une liste de biens immobiliers disponibles, la mise à jour et de maintenir cette liste, de retour de l’utilisateur _principal accueil_.

Le `HMHome` objet contient toutes les informations concernant un accueil donnent y compris les salles de conversation, les groupes ou les zones qu’elle peut contenir, ainsi que tous les accessoires domotique qui ont été installés. Avant de pouvoir effectuer des opérations dans HomeKit, au moins un `HMHome` doit être créé et affecté en tant que la page d’accueil principale.

Votre application est chargée de vérifier si un accueil principal existe et créer et affecter si elle n’est pas le cas.

### <a name="adding-a-home-manager"></a>Ajout d’un gestionnaire de base

Pour ajouter la reconnaissance d’HomeKit à une application Xamarin.iOS, modifiez le **AppDelegate.cs** fichier pour le modifier et qu’elle ressemble à ce qui suit :

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

Lors de la première exécution de l’application, l’utilisateur sera invité s’ils souhaitent pouvoir accéder à leurs informations d’HomeKit :

[![](homekit-images/home01.png "L’utilisateur sera invité s’ils souhaitent pouvoir accéder à leurs informations d’HomeKit")](homekit-images/home01.png#lightbox)

Si l’utilisateur répond **OK**, puis l’application sera en mesure d’utiliser leurs accessoires HomeKit sinon il ne peut pas et tous les appels à HomeKit échoue avec une erreur.

Avec le gestionnaire accueil en place, ensuite l’application devra voir si un accueil principal a été configuré, dans le cas contraire, offrent un moyen de l’utilisateur créer et affecter une.

### <a name="accessing-the-primary-home"></a>L’accès à la page d’accueil principale

Comme indiqué ci-dessus, un accueil principal doit être créé et configuré avant d’HomeKit est disponible et elle est la responsabilité de l’application de fournir un moyen de l’utilisateur créer et affecter un accueil principal s’il n’existe pas déjà.

Lorsque tout d’abord, votre application démarre ou retourne à partir de l’arrière-plan, il doit surveiller le `DidUpdateHomes` événements de la `HMHomeManager` class à vérifier l’existence d’une maison principal. S’il n’existe pas, il doit fournir une interface pour l’utilisateur à en créer un.

Le code suivant peut être ajouté à un contrôleur d’affichage à vérifier pour la page d’accueil principale :

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

Lorsque le Gestionnaire d’accueil établit une connexion avec HomeKit, le `DidUpdateHomes` événement est déclenché, les habitations existantes seront chargées dans la collection du Gestionnaire de biens immobiliers et la page d’accueil principal sera chargé, s’il est disponible.

### <a name="adding-a-primary-home"></a>Ajout d’une maison principal

Si le `PrimaryHome` propriété de la `HMHomeManager` est `null` après un `DidUpdateHomes` événement, vous devez fournir un moyen de l’utilisateur créer et affecter un accueil principal avant de continuer.

En général, l’application présente un formulaire pour l’utilisateur de nommer une nouvelle maison qui ensuite est passée au Gestionnaire de base pour le programme d’installation en tant que la page d’accueil principale. Pour le **HomeKitIntro** exemple d’application, une vue modale a été créée dans le concepteur IOS et appelée par le `AddHomeSegue` segue à partir de l’interface principale de l’application.

Il fournit un champ de texte pour l’utilisateur à entrer un nom pour la nouvelle page d’accueil et un bouton pour ajouter la page d’accueil. Lorsque l’utilisateur appuie sur le **ajouter accueil** bouton, le code suivant appelle le Gestionnaire de base pour ajouter la page d’accueil :

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

Le `AddHome` méthode tente de créer la nouvelle maison et le renvoyer à la routine de rappel donné. Si le `error` propriété n’est pas `null`, une erreur s’est produite et il doit être présenté à l’utilisateur. Les erreurs courantes sont provoquées par un nom de base non unique ou le Gestionnaire de base n’est pas en mesure de communiquer avec HomeKit.

Si la page d’accueil a été créé avec succès, vous devez appeler la `UpdatePrimaryHome` méthode pour définir la nouvelle page d’accueil comme la page d’accueil principale. Là encore, si le `error` propriété n’est pas `null`, une erreur s’est produite et il doit être présenté à l’utilisateur.

Vous devez également surveiller l’accueil du Gestionnaire de `DidAddHome` et `DidRemoveHome` événements et mise à jour de l’interface utilisateur de l’application en fonction des besoins.

> [!IMPORTANT]
> Le `AlertView.PresentOKAlert` méthode utilisée dans l’exemple de code ci-dessus est une classe d’assistance dans l’application HomeKitIntro qui rend l’utilisation de l’iOS alertes plus facile.


## <a name="finding-new-accessories"></a>Recherche de nouveaux accessoires

Une fois un accueil principal a été défini ou chargé à partir du Gestionnaire d’accueil, votre application Xamarin.iOS peut appeler le `HMAccessoryBrowser` pour trouver tous les accessoires domotique nouvelle et les ajouter à une page d’accueil.

Appelez le `StartSearchingForNewAccessories` méthode pour commencer la recherche de nouveaux accessoires et les `StopSearchingForNewAccessories` méthode une fois.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` doit pas être conservé en cours d’exécution pendant de longues périodes de temps, car elle va compromettre l’autonomie et performances de l’appareil iOS. Apple suggère d’appeler `StopSearchingForNewAccessories` après une minute ou effectuez une recherche uniquement lorsque l’interface utilisateur d’accessoire rechercher est présentée à l’utilisateur.

Le `DidFindNewAccessory` événement sera appelé lors de la nouvelle accessoires sont découverts et ils seront ajoutés à la `DiscoveredAccessories` liste dans le navigateur d’accessoire.

Le `DiscoveredAccessories` liste contiendra une collection de `HMAccessory` les objets qui définissent un donnent HomeKit activé appareil de la domotique et ses services disponibles, tels que les voyants ou le contrôle de la porte de garage.

Une fois le nouvel accessoire a été trouvé, il doit être présenté à l’utilisateur et par conséquent, ils peuvent sélectionner et l’ajouter à une maison. Exemple :

[![](homekit-images/accessory01.png "Recherche d’un nouvel accessoire")](homekit-images/accessory01.png#lightbox)

Appelez le `AddAccessory` méthode pour ajouter l’accessoire sélectionné à la collection de la page d’accueil. Par exemple :

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

Si le `err` propriété n’est pas `null`, une erreur s’est produite et il doit être présenté à l’utilisateur. Sinon, l’utilisateur sera invité à entrer le code de programme d’installation pour le périphérique à ajouter :

[![](homekit-images/accessory02.png "Entrez le code d’installation pour le périphérique à ajouter")](homekit-images/accessory02.png#lightbox)

Dans le simulateur d’accessoires HomeKit ce nombre peut être trouvé sous le **Code d’installation** champ :

[![](homekit-images/accessory03.png "Le champ Code d’installation dans le simulateur d’accessoires HomeKit")](homekit-images/accessory03.png#lightbox)

Pour les accessoires HomeKit réels, le code d’installation soit être imprimé sur une étiquette sur l’appareil lui-même, sur l’emballage du produit ou dans le manuel de l’utilisateur de l’accessoire.

Vous devez surveiller le navigateur accessoire `DidRemoveNewAccessory` événement et mise à jour de l’utilisateur de l’interface pour supprimer un accessoire dans la liste disponible une fois que l’utilisateur a ajouté à leur Collection d’accueil.

## <a name="working-with-accessories"></a>Utilisation des accessoires

Une fois un accueil principal et établi et accessoires ont été ajoutés à ce dernier, vous pouvez présenter une liste des accessoires (et éventuellement des salles de conversation) pour l’utilisateur travailler avec.

Le `HMRoom` objet contient toutes les informations concernant une salle donnée et tous les accessoires qui lui appartiennent. Salles de conversation peuvent éventuellement être organisés en une ou plusieurs zones. Un `HMZone` contient toutes les informations sur une zone donnée et toutes les pièces qui lui appartiennent.

Cet exemple, nous allons garder des données simples et fonctionnent avec les accessoires d’une maison directement, au lieu d’organisant dans des zones ou des salles de conversation.

Le `HMHome` objet contient une liste d’accessoire attribué qui peut être présentée à l’utilisateur dans son `Accessories` propriété. Par exemple :

[![](homekit-images/accessory04.png "Un accessoire exemple")](homekit-images/accessory04.png#lightbox)

Écran ici, l’utilisateur peut sélectionner un accessoire donné et travailler avec les services qu’il fournit.

## <a name="working-with-services"></a>Utilisation des Services

Quand l’utilisateur interagit avec un appareil HomeKit domotique est activée, il est généralement via les services qu’il fournit. Le `Services` propriété de la `HMAccessory` classe contient une collection de `HMService` propose des objets qui définissent les services un appareil.

Les services sont des éléments tels que les lumières, thermostats, porte de garage, commutateurs ou les verrous. Certains appareils (comme une porte de garage) fournit plusieurs services, par exemple une lumière et la capacité à ouvrir ou fermer une porte.

En plus des services spécifiques qui fournit un accessoire donné, chaque accessoire contient un `Information Service` qui définit des propriétés telles que son nom, le fabricant, le modèle et le numéro de série.

### <a name="accessory-service-types"></a>Types de Service accessoire

Les Types de Service suivants sont disponibles via le `HMServiceType` enum :

- **AccessoryInformation** -fournit des informations sur l’appareil domotique donné (accessoire).
- **AirQualitySensor** -définit un capteur de qualité d’air.
- **Batterie** -définit l’état de la batterie d’un accessoire.
- **CarbonDioxideSensor** -définit un capteur de dioxyde de carbone.
- **CarbonMonoxideSensor** -définit un capteur monoxyde.
- **ContactSensor** -définit un capteur de contact (par exemple, une fenêtre qui est ouvert ou fermé).
- **La porte** -définit un capteur d’état porte (tel qu’ouvert ou fermé).
- **Ventilateur** -définit un ventilateur contrôlé à distance.
- **GarageDoorOpener** -définit une porte de garage.
- **HumiditySensor** -définit un capteur d’humidité.
- **LeakSensor** -définit un capteur de fuite (comme pour une machine de lavage ou le chauffe-eau).
- **Appareil d’éclairage** -définit une lumière autonome ou une lumière qui fait partie d’un autre accessoire (par exemple, une porte de garage).
- **LightSensor** -définit un capteur d’éclairage.
- **LockManagement** -définit un service qui gère un verrouillage de porte automatisés.
- **LockMechanism** -définit un verrou contrôlé à distance (par exemple, un verrouillage de porte).
- **MotionSensor** -définit un capteur de mouvement.
- **OccupancySensor** -définit un capteur d’occupation.
- **Outlet** -définit une prise murale contrôlée à distance.
- **SecuritySystem** -définit un système de sécurité de base.
- **StatefulProgrammableSwitch** -définit un commutateur programmable qui reste dans un état donnent une fois déclenché (par exemple, un commutateur retournement).
- **StatelessProgrammableSwitch** -définit un commutateur programmable qui renvoie à son état initial après le déclenchement (par exemple, un bouton de commande).
- **SmokeSensor** -définit un capteur de détection de fumée.
- **Commutateur** -définit un commutateur activé/désactivé, par exemple un commutateur standard mur.
- **Capteur de température** -définit un capteur de température.
- **Thermostat** -définit un thermostat intelligent permet de contrôler un système HVAC.
- **Fenêtre** -définit une fenêtre automatisée que canne à sucre être ouvert ou fermé à distance.
- **WindowCovering** -définit une fenêtre contrôlée à distance couvrant comme Stores qui peuvent être ouvert ou fermés.

### <a name="displaying-service-information"></a>Affichage des informations de Service

Après avoir chargé un `HMAccessory` vous pouvez interroger l’individu `HNService` objets il fournit et afficher ces informations à l’utilisateur :

[![](homekit-images/accessory05.png "Affichage des informations de Service")](homekit-images/accessory05.png#lightbox)

Vous devez toujours doit vérifier la `Reachable` propriété d’un `HMAccessory` avant d’essayer de les utiliser. Un accessoire peut être inaccessible l’utilisateur n’est pas dans la plage de l’appareil ou si elle a été déconnecté.

Une fois qu’un service a été sélectionné, l’utilisateur peut afficher ou modifier une ou plusieurs caractéristiques de ce service pour surveiller et de contrôler un appareil domotique donné.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Utilisation de caractéristiques

Chaque `HMService` objet peut contenir une collection de `HMCharacteristic` des objets qui peuvent fournir des informations sur l’état du service (par exemple, une porte qui est ouvert ou fermé) ou autoriser l’utilisateur d’ajuster un état (par exemple, en définissant la couleur d’une lumière).

`HMCharacteristic` fournit non seulement des informations sur une caractéristique et son état, mais fournit également des méthodes pour travailler avec l’état via _caractéristique métadonnées_ (`HMCharacteristisMetadata`). Ces métadonnées peuvent fournir des propriétés (telles que les plages de valeur minimale et maximale) qui sont utiles lors de l’affichage des informations à l’utilisateur ou leur permettant de modifier des États.

Le `HMCharacteristicType` enum fournit un ensemble de valeurs de métadonnées de la caractéristique qui peut être défini ou modifié comme suit :

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- AirQuality
- AudioFeedback
- BatteryLevel
- Luminosité
- CarbonDioxideDetected
- CarbonDioxideLevel
- CarbonDioxidePeakLevel
- CarbonMonoxideDetected
- CarbonMonoxideLevel
- CarbonMonoxidePeakLevel
- ChargingState
- ContactState
- CoolingThreshold
- CurrentDoorState
- CurrentHeatingCooling
- CurrentHorizontalTilt
- CurrentLightLevel
- CurrentLockMechanismState
- CurrentPosition
- CurrentRelativeHumidity
- CurrentSecuritySystemState
- CurrentTemperature
- CurrentVerticalTilt
- FirmwareVersion
- HardwareVersion
- HeatingCoolingStatus
- HeatingThreshold
- HoldPosition
- Teinte
- Identifier
- InputEvent
- LeakDetected
- LockManagementAutoSecureTimeout
- LockManagementControlPoint
- LockMechanismLastKnownAction
- Journaux d’activité
- Fabricant
- Modèle
- MotionDetected
- Nom
- ObstructionDetected
- OccupancyDetected
- OutletInUse
- OutputState
- PositionState
- PowerState
- RotationDirection
- RotationSpeed
- Saturation
- SerialNumber (Numéro_série)
- SmokeDetected
- SoftwareVersion
- StatusActive
- StatusFault
- StatusJammed
- StatusLowBattery
- StatusTampered
- TargetDoorState
- TargetHeatingCooling
- TargetHorizontalTilt
- TargetLockMechanismState
- TargetPosition
- TargetRelativeHumidity
- TargetSecuritySystemState
- TargetTemperature
- TargetVerticalTilt
- TemperatureUnits
- Version

### <a name="working-with-a-characteristics-value"></a>Utilisation de valeur d’une caractéristique

Pour vous assurer que votre application a le dernier état d’une caractéristique donné, appelez le `ReadValue` méthode de la `HMCharacteristic` classe. Si le `err` propriété n’est pas `null`, une erreur s’est produite et il peut ou ne peut pas être présentée à l’utilisateur.

La caractéristique `Value` propriété contient l’état actuel de la caractéristique donnée comme un `NSObject`et par conséquent ne peut pas être manipulées directement dans C#.

Pour lire la valeur, la classe d’assistance suivante a été ajoutée à la **HomeKitIntro** exemple d’application :

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

Le `NSObjectConverter` est utilisé chaque fois que l’application doit lire l’état actuel d’une caractéristique. Par exemple :

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

La ligne ci-dessus convertit la valeur dans un `float` qui peut ensuite être utilisé dans le Xamarin C# code.

Pour modifier un `HMCharacteristic`, appelez sa `WriteValue` méthode et encapsuler la nouvelle valeur dans un `NSObject.FromObject` appeler. Par exemple :

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Si le `err` propriété n’est pas `null`, une erreur s’est produite et doit être présentée à l’utilisateur.

### <a name="testing-characteristic-value-changes"></a>Test des modifications de valeur caractéristique

Lorsque vous travaillez avec `HMCharacteristics` et accessoires simulés, les modifications apportées à la `Value` propriété peut être surveillée dans le simulateur d’accessoires HomeKit.

Avec le **HomeKitIntro** application s’exécutant sur iOS réels matériels des périphériques, modifications apportées à valeur d’une caractéristique doit être visible presque instantanément dans le simulateur d’accessoires HomeKit. Par exemple, modification de l’état d’une lumière dans l’application iOS :

[![](homekit-images/test01.png "Modification de l’état d’une lumière dans une application iOS")](homekit-images/test01.png#lightbox)

Devez modifier l’état de la lumière dans le simulateur d’accessoires HomeKit. Si la valeur ne change pas, vérifiez l’état du message d’erreur lors de l’écriture de nouvelles valeurs de caractéristiques et assurez-vous que l’accessoire est toujours accessible.

## <a name="advanced-homekit-features"></a>Fonctionnalités avancées d’HomeKit

Cet article a présenté les fonctionnalités de base requises pour travailler avec des accessoires HomeKit dans une application Xamarin.iOS. Toutefois, il existe plusieurs fonctionnalités avancées de HomeKit qui ne sont pas traitées dans cette introduction :

- **Salles** -des accessoires HomeKit activé peuvent éventuellement organisés en salles par l’utilisateur final. Ainsi, HomeKit accessoires présents dans un moyen simple pour l’utilisateur à comprendre et utiliser. Pour plus d’informations sur la création et la gestion des salles de conversation, consultez d’Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) documentation.
- **Zones** -salles peuvent éventuellement être organisés en zones par l’utilisateur final. Une zone fait référence à une collection de classes que l’utilisateur peut traiter comme une seule unité. Par exemple :  Attirail Downstairs ou sous-sol. Là encore, cela permet d’HomeKit présenter et utiliser des accessoires d’une manière qui a du sens pour l’utilisateur final. Pour plus d’informations sur la création et la gestion des zones, consultez d’Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) documentation.
- **Actions et jeux d’actions** -Actions modifier les caractéristiques des accessoires de service et peuvent être regroupées en ensembles. Jeux d’actions agisse en tant que scripts permettant de contrôler un groupe d’accessoires et coordonner leurs actions. Par exemple, un script de « Télévision » peut fermer les stores atténue l’éclairage et activer la télévision et son système audio. Pour plus d’informations sur la création et la gestion des Actions et des jeux d’actions, consultez d’Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) et [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) documentation.
- **Déclencheurs** : un déclencheur peut activer une ou plusieurs Action définie lorsqu’un ensemble donné de conditions ont été remplies. Par exemple, allumer le voyant portch et verrouiller toutes les portes externes lorsqu’il atteint foncé à l’extérieur. Pour plus d’informations sur la création et la gestion des déclencheurs, consultez d’Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) documentation.

Dans la mesure où ces fonctionnalités utilisent les mêmes techniques présentées ci-dessus, ils doivent être faciles à implémenter par Apple suivant [HomeKitDeveloper Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [indications de l’Interface utilisateur HomeKit](https://developer.apple.com/homekit/ui-guidelines/) et [ Référence de l’infrastructure d’HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Directives de révision d’application HomeKit

Avant de soumettre un HomeKit activée application Xamarin.iOS dans iTunes Connect pour la version de l’iTunes App Store, veillez à suivre les instructions d’Apple pour les applications d’HomeKit activé :

- Objectif principal de l’application _doit_ être domotique si vous utilisez le framework HomeKit.
- Texte marketing de l’application doit informer les utilisateurs que HomeKit est utilisé et qu’ils doivent fournir une politique de confidentialité.
- Collecte des informations sur l’utilisateur ou l’utilisation d’HomeKit à des fins publicitaires est strictement interdite.

Pour la version complète, consultez les instructions, consultez d’Apple [App Store passez en revue les instructions](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>Quelles sont les nouveautés dans iOS 9

Apple a effectué les modifications et additions suivantes à HomeKit pour iOS 9 :

- **Conservation d’objets existants** : si un accessoire existant est modifié, le Gestionnaire d’accueil (`HMHomeManager`) vous informe de l’élément spécifique qui a été modifié.
- **Identificateurs persistants** -toutes les classes HomeKit pertinentes incluent désormais un `UniqueIdentifier` propriété pour identifier de façon unique un élément donné sur HomeKit activée applications (ou instances de la même application).
- **Gestion des utilisateurs** -ajouter un contrôleur d’affichage intégré pour fournir la gestion des utilisateurs sur les utilisateurs qui ont accès aux appareils HomeKit à domicile de l’utilisateur principal.
- **Fonctionnalités utilisateur** - HomeKit utilisateurs ont désormais un ensemble de privilèges qui contrôlent ce que les fonctions qu’ils sont en mesure d’utiliser dans HomeKit et HomeKit activé accessoires. Votre application doit afficher uniquement les fonctionnalités applicables à l’utilisateur actuel. Par exemple, seuls les administrateurs doivent être en mesure de maintenir les autres utilisateurs.
- **Prédéfinis scènes** -arrière-plan prédéfinis ont été créés pour les quatre événements courants qui se produisent pour l’utilisateur HomeKit moyen : Opérationnel, laissez, retourner, accédez à la base de l’analyse. Impossible de supprimer ces séquences prédéfinis à partir d’une maison.
- **Arrière-plan et Siri** -Siri a prise en charge accrue pour les scènes dans iOS 9 et peut reconnaissent le nom de toutes les scènes défini dans HomeKit. Un utilisateur peut exécuter une scène simplement en parlant de son nom par Siri.
- **Catégories Accessoires** -un ensemble de catégories prédéfinies a été ajouté à tous les accessoires et vous aide à identifier le type d’accessoire ajouté à une page d’accueil ou a travaillé sur à partir de votre application. Ces nouvelles catégories sont disponibles pendant l’installation d’accessoire.
- **Prise en charge d’Apple Watch** - HomeKit est désormais disponible pour watchOS et l’Apple Watch pourront contrôle HomeKit des appareils compatibles sans un iPhone en cours près de la montre. HomeKit pour watchOS prend en charge les fonctionnalités suivantes : Affichage des habitations, contrôler les accessoires et l’exécution de séquences.
- **Nouveau Type de déclencheur d’événement** : outre le type de déclencheur du minuteur pris en charge dans iOS 8, iOS 9 maintenant prend en charge les déclencheurs d’événements en fonction d’état accessoire (telles que les données de capteur) ou la géolocalisation. Utilisent des déclencheurs d’événements `NSPredicates` pour définir des conditions pour leur exécution.
- **Accès à distance** -avec accès à distance, l’utilisateur est maintenant en mesure de contrôler leur HomeKit activé accueil Automation accessoires lorsqu’ils sont en dehors de la maison à un emplacement distant. Dans iOS 8 cela était uniquement pris en charge si l’utilisateur avait une génération 3e Apple TV dans la page d’accueil. Dans iOS 9, cette limitation a été supprimée et accès à distance est prise en charge par le biais d’iCloud et le protocole d’accessoires HomeKit (HAP).
- **Nouvelles fonctionnalités Bluetooth Low Energy (BLE)** -HomeKit prend désormais en charge plusieurs types accessoires capable de communiquer via le protocole Bluetooth Low Energy (BLE). À l’aide de HAP Secure Tunneling, un accessoire HomeKit peut exposer un autre accessoire Bluetooth via le Wi-Fi (s’il est hors limites de Bluetooth). Dans iOS 9, BLE accessoires ont une prise en charge complète pour les notifications et les métadonnées.
- **Nouvelles catégories accessoire** -Apple ajouté les nouvelles catégories accessoire suivant dans iOS 9 : Revêtements de fenêtre, trotinette portes et Windows, systèmes d’alarme, capteurs et des commutateurs programmables.

Pour plus d’informations sur les nouvelles fonctionnalités de HomeKit dans iOS 9, consultez d’Apple [HomeKit Index](https://developer.apple.com/homekit/) et [What ' s New in HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) vidéo.

## <a name="summary"></a>Récapitulatif

Cet article a présenté le framework d’Apple HomeKit domotique. Il vous a montré comment installer et configurer des appareils de test à l’aide du simulateur d’accessoires HomeKit et comment créer une simple application Xamarin.iOS pour découvrir, de communiquer avec et contrôler les appareils domotique à l’aide de HomeKit.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Quelles sont les nouveautés dans iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Recommandations pour l’Interface utilisateur de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Référence de l’infrastructure de HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
