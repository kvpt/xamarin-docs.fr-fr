---
title: HomeKit dans Xamarin. iOS
description: HomeKit est l’infrastructure d’Apple pour le contrôle des appareils domotiques. Cet article présente HomeKit et traite de la configuration des accessoires de test dans le simulateur d’accessoire HomeKit et de l’écriture d’une application Xamarin. iOS simple pour interagir avec ces accessoires.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: b9b8d1dbaf7872ab629392e15d906540ae491db4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572596"
---
# <a name="homekit-in-xamarinios"></a>HomeKit dans Xamarin. iOS

_HomeKit est l’infrastructure d’Apple pour le contrôle des appareils domotiques. Cet article présente HomeKit et traite de la configuration des accessoires de test dans le simulateur d’accessoire HomeKit et de l’écriture d’une application Xamarin. iOS simple pour interagir avec ces accessoires._

[![](homekit-images/accessory01.png "An example HomeKit enabled App")](homekit-images/accessory01.png#lightbox)

Apple a introduit HomeKit dans iOS 8 comme un moyen d’intégrer en toute transparence plusieurs appareils domotiques à l’intérieur d’un grand nombre de fournisseurs dans une seule unité cohérente. En promouvant un protocole commun pour la découverte, la configuration et le contrôle des appareils domotiques, HomeKit permet aux appareils de fournisseurs non liés de travailler ensemble, sans que les fournisseurs individuels aient à coordonner les efforts.

Avec HomeKit, vous pouvez créer une application Xamarin. iOS qui contrôle tous les appareils compatibles HomeKit sans utiliser des API ou des applications fournies par le fournisseur. À l’aide de HomeKit, vous pouvez effectuer les opérations suivantes :

- Découvrez les nouveaux appareils domotiques HomeKit activés et ajoutez-les à une base de données qui sera conservée sur tous les appareils iOS de l’utilisateur.
- Installer, configurer, afficher et contrôler n’importe quel appareil dans la _base de données de configuration_de base HomeKit.
- Communiquez avec n’importe quel appareil HomeKit préconfiguré et commandez-les pour effectuer des actions individuelles ou travailler de concert, par exemple pour activer tous les éclairages de la cuisine.

En plus de servir des appareils dans la base de données de configuration d’accès à distance aux applications HomeKit, HomeKit permet d’accéder aux commandes vocales de Siri. Dans le cadre d’une configuration HomeKit correctement configurée, l’utilisateur peut émettre des commandes vocales telles que « Siri, allumer les voyants dans la salle de vie ».

<a name="Home-Configuration-Database"></a>

## <a name="the-home-configuration-database"></a>La base de données de configuration de base

HomeKit organise tous les appareils Automation d’un emplacement donné dans une collection d’hébergement. Ce regroupement permet à l’utilisateur de regrouper ses appareils domotiques dans des unités disposées de manière logique avec des étiquettes explicites explicites.

La collection d’hébergement est stockée dans une base de données de configuration de base qui sera automatiquement sauvegardée et synchronisée sur tous les appareils iOS de l’utilisateur. HomeKit fournit les classes suivantes pour l’utilisation de la base de données de configuration de base :

- `HMHome`-Il s’agit du conteneur de niveau supérieur qui contient toutes les informations et configurations de tous les appareils domotiques à un seul emplacement physique (par exemple, un séjour de famille unique). L’utilisateur peut avoir plusieurs résidences, telles que la maison principale et un bureau de vacances. Ils peuvent également avoir des « maisons » différents sur la même propriété, telles que la maison principale et une maison invitée sur le garage. Dans les deux cas, au moins un `HMHome` objet _doit_ être configuré et stocké avant que toute autre information HomeKit puisse être entrée.
- `HMRoom`-Si cette option est facultative, `HMRoom` elle permet à l’utilisateur de définir des pièces spécifiques à l’intérieur d’une page d’habitation ( `HMHome` ) telle que : cuisine, salle de bain, garage ou salon. L’utilisateur peut regrouper tous les appareils domotiques dans un emplacement spécifique de leur maison dans un `HMRoom` et les agit en tant qu’unité. Par exemple, demander à Siri de désactiver les lumières du garage.
- `HMAccessory`: Représente un périphérique d’automatisation HomeKit physique individuel qui a été installé dans la résidence de l’utilisateur (par exemple, un thermostat intelligent). Chaque `HMAccessory` est assigné à un `HMRoom` . Si l’utilisateur n’a configuré aucune salle, HomeKit affecte des accessoires à une salle par défaut spéciale.
- `HMService`-Représente un service fourni par un donné `HMAccessory` , tel que l’état activé/désactivé d’une lumière ou sa couleur (si le changement de couleur est pris en charge). Chacun `HMAccessory` peut avoir plusieurs services, tels qu’un ouvreur de porte de garage qui comprend également une lumière. En outre, un donné `HMAccessory` peut avoir des services, tels que la mise à jour du microprogramme, qui sont en dehors du contrôle utilisateur.
- `HMZone`-Permet à l’utilisateur de regrouper une collection d' `HMRoom` objets dans des zones logiques, telles que les escaliers, les Starbucks ou le sous-sol. S’il est facultatif, cela permet des interactions comme demander à Siri de désactiver l’ensemble du Starbucks clair.

<a name="Provisioning-a-HomeKit-App"></a>

## <a name="provisioning-a-homekit-app"></a>Configuration d’une application HomeKit

En raison des exigences de sécurité imposées par HomeKit, une application Xamarin. iOS qui utilise HomeKit Framework doit être correctement configurée dans le portail des développeurs Apple et dans le fichier de projet Xamarin. iOS.

Effectuez les actions suivantes :

1. Connectez-vous au [portail des développeurs Apple](https://developer.apple.com).
2. Cliquez sur **certificats, identificateurs & profils**.
3. Si vous ne l’avez pas déjà fait, cliquez sur **identificateurs** et créez un ID pour votre application (par exemple `com.company.appname` ,), ou modifiez votre ID existant.
4. Assurez-vous que le service **HomeKit** a été vérifié pour l’ID donné : 

    [![](homekit-images/provision01.png "Enable the HomeKit service for the given ID")](homekit-images/provision01.png#lightbox)
5. Enregistrez vos modifications.
6. Cliquez sur **Configuration des profils de provisionnement**  >  **Development** et créez un nouveau profil de provisionnement de développement pour votre application : 

    [![](homekit-images/provision02.png "Create a new development provisioning profile for the app")](homekit-images/provision02.png#lightbox)
7. Téléchargez et installez le nouveau profil de provisionnement ou utilisez Xcode pour télécharger et installer le profil.
8. Modifiez les options de votre projet Xamarin. iOS et assurez-vous d’utiliser le profil de provisionnement que vous venez de créer : 

    [![](homekit-images/provision03.png "Select provisioning profile just created")](homekit-images/provision03.png#lightbox)
9. Modifiez ensuite votre fichier **info. plist** et assurez-vous que vous utilisez l’ID d’application qui a été utilisé pour créer le profil de provisionnement : 

    [![](homekit-images/provision04.png "Set the App ID ")](homekit-images/provision04.png#lightbox)
10. Enfin, modifiez votre fichier **Entitlements. plist** et assurez-vous que le droit **HomeKit** a été sélectionné : 

    [![](homekit-images/provision05.png "Enable the HomeKit entitlement")](homekit-images/provision05.png#lightbox)
11. Enregistrez les modifications apportées à tous les fichiers.

Lorsque ces paramètres sont en place, l’application est maintenant prête à accéder aux API HomeKit Framework. Pour plus d’informations sur l’approvisionnement, consultez nos guides d’approvisionnement et d' [approvisionnement](~/ios/get-started/installation/device-provisioning/index.md) des [appareils](~/ios/get-started/installation/device-provisioning/index.md) .

> [!IMPORTANT]
> Le test d’une application HomeKit activée requiert un appareil iOS réel qui a été correctement approvisionné pour le développement. HomeKit ne peut pas être testé à partir du simulateur iOS.

## <a name="the-homekit-accessory-simulator"></a>Simulateur d’accessoires HomeKit

Pour fournir un moyen de tester tous les appareils et services domotiques, sans avoir besoin d’un appareil physique, Apple a créé le _simulateur d’accessoire HomeKit_. À l’aide de ce simulateur, vous pouvez configurer et configurer des appareils HomeKit virtuels.

### <a name="installing-the-simulator"></a>Installation du simulateur

Apple fournit le simulateur d’accessoire HomeKit comme un téléchargement séparé de Xcode. vous devez donc l’installer avant de continuer.

Effectuez les actions suivantes :

1. Dans un navigateur Web, visitez [téléchargements pour les développeurs Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Téléchargez les **outils supplémentaires pour XCode xxx** (où xxx est la version de Xcode que vous avez installée) : 

    [![](homekit-images/simulator01.png "Download the Additional Tools for Xcode")](homekit-images/simulator01.png#lightbox)
3. Ouvrez l’image de disque et installez les outils dans le répertoire de votre **application** .

Une fois le simulateur d’accessoires HomeKit installé, des accessoires virtuels peuvent être créés pour le test.

### <a name="creating-virtual-accessories"></a>Création d’accessoires virtuels

Pour démarrer le simulateur d’accessoires HomeKit et créer quelques accessoires virtuels, procédez comme suit :

1. À partir du dossier applications, démarrez le simulateur d’accessoires HomeKit : 

    [![](homekit-images/simulator02.png "The HomeKit Accessory Simulator")](homekit-images/simulator02.png#lightbox)
2. Cliquez sur le **+** bouton et sélectionnez **nouvel accessoire...**: 

    [![](homekit-images/simulator03.png "Add a new accessory")](homekit-images/simulator03.png#lightbox)
3. Renseignez les informations relatives au nouvel accessoire, puis cliquez sur le bouton **Terminer** : 

    [![](homekit-images/simulator04.png "Fill out the information about the new accessory")](homekit-images/simulator04.png#lightbox)
4. Cliquez sur **Ajouter un service.** et sélectionnez un type de service dans la liste déroulante : 

    [![](homekit-images/simulator05.png "Select a service type from the dropdown")](homekit-images/simulator05.png#lightbox)
5. Fournissez un **nom** pour le service, puis cliquez sur le bouton **Terminer** : 

    [![](homekit-images/simulator06.png "Enter a Name for the service")](homekit-images/simulator06.png#lightbox)
6. Vous pouvez fournir des caractéristiques facultatives pour un service en cliquant sur le bouton **Ajouter une caractéristique** et en configurant les paramètres requis : 

    [![](homekit-images/simulator07.png "Configuring the required settings")](homekit-images/simulator07.png#lightbox)
7. Répétez les étapes ci-dessus pour créer l’un des types d’appareils d’automatisation de la racine virtuelle pris en charge par HomeKit.

Avec quelques exemples d’accessoires HomeKit virtuels créés et configurés, vous pouvez désormais utiliser et contrôler ces appareils à partir de votre application Xamarin. iOS.

## <a name="configuring-the-infoplist-file"></a>Configuration du fichier info. plist

Nouveauté pour iOS 10 (et versions ultérieures), le développeur doit ajouter la `NSHomeKitUsageDescription` clé au fichier de l’application `Info.plist` et fournir une chaîne qui déclare la raison pour laquelle l’application souhaite accéder à la base de données HomeKit de l’utilisateur. Cette chaîne est présentée à l’utilisateur la première fois qu’il exécute l’application :

[![](homekit-images/info01.png "The HomeKit permission dialog")](homekit-images/info01.png#lightbox)

Pour définir cette clé, procédez comme suit :

1. Double-cliquez sur le `Info.plist` fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. En bas de l’écran, basculez en mode **source** .
3. Ajoutez une nouvelle **entrée** à la liste.
4. Dans la liste déroulante, sélectionnez **Privacy-HomeKit utilisation Description**: 

    [![](homekit-images/info02.png "Select Privacy - HomeKit Usage Description")](homekit-images/info02.png#lightbox)
5. Entrez une description de la raison pour laquelle l’application veut accéder à la base de données HomeKit de l’utilisateur : 

    [![](homekit-images/info03.png "Enter a description")](homekit-images/info03.png#lightbox)
6. Enregistrez les modifications dans le fichier.

> [!IMPORTANT]
> `NSHomeKitUsageDescription`Si vous ne définissez pas la clé dans le `Info.plist` fichier, l’application _échoue silencieusement_ (en cours de fermeture par le système lors de l’exécution) sans erreur lorsqu’elle est exécutée dans iOS 10 (ou version ultérieure).

## <a name="connecting-to-homekit"></a>Connexion à HomeKit

Pour communiquer avec HomeKit, votre application Xamarin. iOS doit d’abord instancier une instance de la `HMHomeManager` classe. Le responsable Home est le point d’entrée central dans HomeKit et est responsable de la fourniture d’une liste de maisons disponibles, de la mise à jour et de la maintenance de cette liste et du retour de la _maison principale_de l’utilisateur.

L' `HMHome` objet contient toutes les informations relatives à une page d’hébergement, y compris les salles, les groupes ou les zones qu’elle peut contenir, ainsi que tous les accessoires d’automatisation d’habitation qui ont été installés. Avant de pouvoir effectuer des opérations dans HomeKit, au moins une d’entre elles `HMHome` doit être créée et affectée en tant que principal d’hébergement.

Votre application est chargée de vérifier s’il existe une origine principale et de la créer et de l’affecter si ce n’est pas le cas.

### <a name="adding-a-home-manager"></a>Ajout d’un gestionnaire d’hébergement

Pour ajouter la reconnaissance HomeKit à une application Xamarin. iOS, modifiez le fichier **AppDelegate.cs** pour le modifier et faites-le ressembler à ce qui suit :

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

Lorsque l’application est exécutée pour la première fois, l’utilisateur est invité à indiquer s’il souhaite lui permettre d’accéder à ses informations HomeKit :

[![](homekit-images/home01.png "The user will be asked if they want to allow it to access their HomeKit information")](homekit-images/home01.png#lightbox)

Si l’utilisateur répond **OK**, l’application sera en mesure de fonctionner avec ses accessoires HomeKit. sinon, elle ne le sera pas et tout appel à HomeKit échouera avec une erreur.

Lorsque le responsable de la famille est en place, l’application doit déterminer si une racine principale a été configurée et, dans le cas contraire, fournir un moyen à l’utilisateur de créer et d’affecter un.

### <a name="accessing-the-primary-home"></a>Accès à la page d’hébergement principale

Comme indiqué ci-dessus, une racine principale doit être créée et configurée avant que HomeKit soit disponible et il incombe à l’application de fournir à l’utilisateur un moyen de créer et d’affecter une page d’hébergement principale s’il n’en existe pas déjà une.

Lorsque votre application démarre pour la première fois ou est retournée à partir de l’arrière-plan, elle doit surveiller l' `DidUpdateHomes` événement de la `HMHomeManager` classe pour vérifier l’existence d’une racine principale. S’il n’en existe pas, il doit fournir une interface permettant à l’utilisateur d’en créer un.

Le code suivant peut être ajouté à un contrôleur d’affichage pour vérifier la racine principale :

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

Lorsque le responsable Home établit une connexion à HomeKit, l' `DidUpdateHomes` événement est déclenché, toutes les maisons existantes sont chargées dans la collection de maisons du responsable et la maison principale est chargée, si elle est disponible.

### <a name="adding-a-primary-home"></a>Ajout d’une page d’hébergement principale

Si la `PrimaryHome` propriété du `HMHomeManager` est `null` après un `DidUpdateHomes` événement, vous devez fournir un moyen à l’utilisateur de créer et d’affecter un serveur d’hébergement principal avant de continuer.

En général, l’application présente un formulaire permettant à l’utilisateur de nommer une nouvelle page d’hébergement qui est ensuite transmise au responsable de l’installation principale afin d’être configurée comme page d’hébergement principale. Pour l’exemple d’application **HomeKitIntro** , une vue modale a été créée dans le concepteur iOS et appelée par `AddHomeSegue` segue à partir de l’interface principale de l’application.

Il fournit un champ de texte permettant à l’utilisateur d’entrer un nom pour la nouvelle page d’hébergement et un bouton pour ajouter la page d’hébergement. Quand l’utilisateur appuie sur le bouton **Ajouter** , le code suivant appelle le responsable pour ajouter la page d’hébergement :

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

La `AddHome` méthode tente de créer une nouvelle page d’hébergement et de la retourner à la routine de rappel donnée. Si la `error` propriété n’a pas la valeur `null` , une erreur s’est produite et elle doit être présentée à l’utilisateur. Les erreurs les plus courantes sont causées par un nom de famille non unique ou le responsable de la famille ne parvient pas à communiquer avec HomeKit.

Si la page d’hébergement a été créée avec succès, vous devez appeler la `UpdatePrimaryHome` méthode pour définir la nouvelle page d’hébergement comme racine principale. Là encore, si la `error` propriété n’a pas la valeur `null` , une erreur s’est produite et elle doit être présentée à l’utilisateur.

Vous devez également surveiller les événements et le gestionnaire d’hébergement `DidAddHome` `DidRemoveHome` et mettre à jour l’interface utilisateur de l’application selon vos besoins.

> [!IMPORTANT]
> La `AlertView.PresentOKAlert` méthode utilisée dans l’exemple de code ci-dessus est une classe d’assistance dans l’application HomeKitIntro qui facilite l’utilisation des alertes iOS.

## <a name="finding-new-accessories"></a>Recherche de nouveaux accessoires

Une fois qu’une page d’hébergement principale a été définie ou chargée à partir du gestionnaire d’hébergement, votre application Xamarin. iOS peut appeler `HMAccessoryBrowser` pour rechercher de nouveaux accessoires d’automatisation de la domotique et les ajouter à une page d’hébergement.

Appelez la `StartSearchingForNewAccessories` méthode pour commencer à rechercher de nouveaux accessoires et la `StopSearchingForNewAccessories` méthode lorsque vous avez terminé.

> [!IMPORTANT]
> `StartSearchingForNewAccessories`ne doit pas être exécuté pendant de longues périodes, car cela aura une incidence négative sur la durée de vie de la batterie et sur les performances de l’appareil iOS. Apple suggère `StopSearchingForNewAccessories` un appel après une minute ou uniquement lorsque l’interface utilisateur de recherche d’accessoire est présentée à l’utilisateur.

L' `DidFindNewAccessory` événement est appelé quand de nouveaux accessoires sont découverts et qu’ils sont ajoutés à `DiscoveredAccessories` la liste dans le navigateur d’accessoire.

La `DiscoveredAccessories` liste contient une collection d' `HMAccessory` objets qui définissent un appareil domotique HomeKit activé et ses services disponibles, tels que les lumières ou le contrôle de la porte de garage.

Une fois le nouvel accessoire trouvé, il doit être présenté à l’utilisateur et peut donc le sélectionner et l’ajouter à une page d’hébergement. Exemple :

[![](homekit-images/accessory01.png "Finding a new accessory")](homekit-images/accessory01.png#lightbox)

Appelez la `AddAccessory` méthode pour ajouter l’accessoire sélectionné à la collection de l’hébergement. Par exemple :

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

Si la `err` propriété n’a pas la valeur `null` , une erreur s’est produite et elle doit être présentée à l’utilisateur. Dans le cas contraire, l’utilisateur sera invité à entrer le code d’installation de l’appareil à ajouter :

[![](homekit-images/accessory02.png "Enter the setup code for the device to add")](homekit-images/accessory02.png#lightbox)

Dans le simulateur d’accessoire HomeKit, ce numéro se trouve sous le champ **code d’installation** :

[![](homekit-images/accessory03.png "The Setup Code field in the HomeKit Accessory Simulator")](homekit-images/accessory03.png#lightbox)

Pour les accessoires HomeKit réels, le code d’installation est imprimé sur une étiquette sur l’appareil lui-même, dans la zone produit ou dans le manuel de l’utilisateur de l’accessoire.

Vous devez surveiller l’événement du navigateur d’accessoires `DidRemoveNewAccessory` et mettre à jour l’interface utilisateur pour supprimer un accessoire de la liste disponible une fois que l’utilisateur l’a ajouté à sa collection personnelle.

## <a name="working-with-accessories"></a>Utilisation des accessoires

Une fois que vous avez ajouté une page d’hébergement principale et que des accessoires ont été ajoutés, vous pouvez présenter une liste d’accessoires (et éventuellement de salles) à utiliser par l’utilisateur.

L' `HMRoom` objet contient toutes les informations relatives à une salle donnée et à tous les accessoires qui lui appartiennent. Les salles peuvent éventuellement être organisées en une ou plusieurs zones. Un `HMZone` contient toutes les informations relatives à une zone donnée et à toutes les pièces qui lui appartiennent.

Dans le cadre de cet exemple, nous allons conserver des choses simples et travailler directement avec les accessoires d’une famille, au lieu de les organiser en salles ou en zones.

L' `HMHome` objet contient une liste d’accessoires affectés qui peuvent être présentés à l’utilisateur dans sa `Accessories` propriété. Par exemple :

[![](homekit-images/accessory04.png "An example accessory")](homekit-images/accessory04.png#lightbox)

Formulaire ici, l’utilisateur peut sélectionner un accessoire donné et utiliser les services qu’il fournit.

## <a name="working-with-services"></a>Utilisation des services

Lorsque l’utilisateur interagit avec un périphérique d’automatisation à la racine HomeKit donné, il s’agit généralement des services qu’il fournit. La `Services` propriété de la `HMAccessory` classe contient une collection d' `HMService` objets qui définissent les services offerts par un appareil.

Les services sont des éclairages, des thermostats, des appareils ouverts, des commutateurs ou des verrous. Certains appareils (comme un ouvreur de porte de garage) fournissent plusieurs services, tels qu’une lumière et la possibilité d’ouvrir ou de fermer une porte.

En plus des services spécifiques fournis par un accessoire donné, chaque accessoire contient un `Information Service` qui définit des propriétés telles que son nom, son fabricant, son modèle et son numéro de série.

### <a name="accessory-service-types"></a>Types de service accessoire

Les types de service suivants sont disponibles via l' `HMServiceType` énumération :

- **AccessoryInformation** -fournit des informations sur l’appareil Automation (accessoire).
- **AirQualitySensor** : définit un capteur de qualité de l’air.
- **Battery** -définit l’état de la batterie d’un accessoire.
- **CarbonDioxideSensor** : définit un capteur de dioxyde de carbone.
- **CarbonMonoxideSensor** : définit un capteur de monoxyde de carbone.
- **ContactSensor** : définit un capteur de contact (par exemple, une fenêtre ouverte ou fermée).
- **Porte** : définit un capteur d’état de la porte (tel qu’ouvert ou fermé).
- **Fan** -définit un ventilateur contrôlé à distance.
- **GarageDoorOpener** : définit un ouvreur de porte de garage.
- **HumiditySensor** : définit un capteur d’humidité.
- **LeakSensor** : définit un capteur de fuites (comme pour un chauffe-eau chaud ou une machine à laver).
- **Ampoule** -définit une lumière autonome ou une lumière qui fait partie d’un autre accessoire (comme un ouvreur de porte de garage).
- **LightSensor** : définit un capteur de lumière.
- **LockManagement** : définit un service qui gère un verrou de porte automatique.
- **LockMechanism** : définit un verrou contrôlé à distance (par exemple, un verrou de porte).
- **MotionSensor** : définit un capteur de mouvement.
- **OccupancySensor** : définit un capteur d’occupation.
- **Outlet** : définit une prise murale contrôlée à distance.
- **SecuritySystem** : définit un système de sécurité de l’ordinateur de bureau.
- **StatefulProgrammableSwitch** : définit un commutateur programmable qui reste dans un état d’octroi une fois déclenché (comme un commutateur Flip).
- **StatelessProgrammableSwitch** : définit un commutateur programmable qui retourne à son état initial après avoir été déclenché (comme un bouton de commande).
- **SmokeSensor** : définit un capteur de fumée.
- **Commutateur** -définit un commutateur on/off comme un commutateur mural standard.
- **Capteur** : définit un capteur de température.
- **Thermostat** : définit un thermostat intelligent utilisé pour contrôler un système HVAC.
- **Window** -définit une fenêtre automatisée à laquelle la canne est ouverte ou fermée à distance.
- **WindowCovering** : définit une couverture de fenêtre contrôlée à distance, comme les aveugles qui peuvent être ouvertes ou fermées.

### <a name="displaying-service-information"></a>Affichage des informations de service

Une fois que vous avez chargé un `HMAccessory` , vous pouvez interroger les `HNService` objets qu’il fournit et afficher ces informations à l’utilisateur :

[![](homekit-images/accessory05.png "Displaying Service Information")](homekit-images/accessory05.png#lightbox)

Vous devez toujours vérifier la `Reachable` propriété d’un `HMAccessory` avant de tenter de l’utiliser. Un accessoire peut être inaccessible. l’utilisateur n’est pas dans la plage de l’appareil ou s’il a été débranché.

Une fois qu’un service a été sélectionné, l’utilisateur peut afficher ou modifier une ou plusieurs caractéristiques de ce service afin de surveiller ou de contrôler un périphérique d’automatisation de l’hôte donné.

<a name="Working-with-Characteristics"></a>

## <a name="working-with-characteristics"></a>Utilisation des caractéristiques

Chaque `HMService` objet peut contenir une collection d' `HMCharacteristic` objets qui peuvent fournir des informations sur l’état du service (par exemple, une porte ouverte ou fermée) ou autoriser l’utilisateur à ajuster un État (par exemple, définir la couleur d’une lumière).

`HMCharacteristic`fournit non seulement des informations sur une caractéristique et son état, mais fournit également des méthodes pour l’utilisation de l’état via des _métadonnées caractéristiques_ ( `HMCharacteristisMetadata` ). Ces métadonnées peuvent fournir des propriétés (telles que des plages de valeurs minimales et maximales) qui sont utiles lors de l’affichage d’informations à l’utilisateur ou de leur autorisation de modifier les États.

L' `HMCharacteristicType` énumération fournit un ensemble de valeurs de métadonnées caractéristiques qui peuvent être définies ou modifiées comme suit :

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- Qualité de l’As
- AudioFeedback
- BatteryLevel
- Brightness (Luminosité)
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
- SerialNumber
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

### <a name="working-with-a-characteristics-value"></a>Utilisation de la valeur d’une caractéristique

Pour vous assurer que votre application possède l’état le plus récent d’une caractéristique donnée, appelez la `ReadValue` méthode de la `HMCharacteristic` classe. Si la `err` propriété n’a pas la valeur `null` , une erreur s’est produite et peut ne pas être présentée à l’utilisateur.

La propriété de la caractéristique `Value` contient l’état actuel de la caractéristique donnée en tant que `NSObject` , et par conséquent, ne peut pas être utilisé directement en C#.

Pour lire la valeur, la classe d’assistance suivante a été ajoutée à l’exemple d’application **HomeKitIntro** :

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

`NSObjectConverter`Est utilisé chaque fois que l’application doit lire l’état actuel d’une caractéristique. Par exemple :

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

La ligne ci-dessus convertit la valeur en une `float` qui peut ensuite être utilisée dans le code C# Xamarin.

Pour modifier un `HMCharacteristic` , appelez sa `WriteValue` méthode et encapsulez la nouvelle valeur dans un `NSObject.FromObject` appel. Par exemple :

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Si la `err` propriété n’a pas la valeur `null` , une erreur s’est produite et doit être présentée à l’utilisateur.

### <a name="testing-characteristic-value-changes"></a>Test des modifications de valeur de caractéristiques

Lorsque vous utilisez `HMCharacteristics` et simulez des accessoires, les modifications apportées à la `Value` propriété peuvent être surveillées dans le simulateur d’accessoire HomeKit.

Lorsque l’application **HomeKitIntro** s’exécute sur du matériel d’appareil iOS réel, les modifications apportées à la valeur d’une caractéristique doivent être affichées presque instantanément dans le simulateur d’accessoire HomeKit. Par exemple, la modification de l’état d’une lumière dans l’application iOS :

[![](homekit-images/test01.png "Changing the state of a light in an iOS app")](homekit-images/test01.png#lightbox)

Doit modifier l’état de la lumière dans le simulateur d’accessoires HomeKit. Si la valeur ne change pas, vérifiez l’état du message d’erreur lors de l’écriture de nouvelles valeurs de caractéristiques et assurez-vous que l’accessoire est toujours accessible.

## <a name="advanced-homekit-features"></a>Fonctionnalités avancées de HomeKit

Cet article a abordé les fonctionnalités de base requises pour l’utilisation des accessoires HomeKit dans une application Xamarin. iOS. Toutefois, il existe plusieurs fonctionnalités avancées de HomeKit qui ne sont pas abordées dans cette introduction :

- **Salles** -les accessoires compatibles HomeKit peuvent éventuellement être organisés en espaces par l’utilisateur final. Cela permet à HomeKit de présenter des accessoires de manière à ce que l’utilisateur comprenne et travaille facilement avec. Pour plus d’informations sur la création et la maintenance de salles, consultez la documentation [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) d’Apple.
- **Zones** -les salles peuvent éventuellement être organisées en zones par l’utilisateur final. Une zone fait référence à une collection de salles que l’utilisateur peut traiter en tant qu’unité unique. Par exemple : escaliers, Starbucks ou sous-sol. Là encore, cela permet à HomeKit de présenter et de travailler avec des accessoires d’une manière qui paraît logique pour l’utilisateur final. Pour plus d’informations sur la création et la gestion des zones, consultez la documentation [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) d’Apple.
- **Actions et jeux** d’actions : les actions modifient les caractéristiques du service accessoire et peuvent être regroupées dans des ensembles. Les jeux d’actions jouent le rôle de scripts pour contrôler un groupe d’accessoires et coordonner leurs actions. Par exemple, un script « Watch TV » peut fermer les aveugles, estomper les lumières et allumer la télévision et son système son. Pour plus d’informations sur la création et la gestion des actions et des jeux d’actions, consultez la documentation [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) et [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) d’Apple.
- **Déclencheurs** : un déclencheur peut activer un ou plusieurs jeux d’actions lorsqu’un ensemble de conditions donné a été respecté. Par exemple, activez la lumière portch et verrouillez toutes les portes externes quand elles sont sombres à l’extérieur. Pour plus d’informations sur la création et la gestion des déclencheurs, consultez la documentation [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) d’Apple.

Étant donné que ces fonctionnalités utilisent les mêmes techniques que celles présentées ci-dessus, elles doivent être faciles à implémenter en suivant le [Guide HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)d’Apple, [les instructions de l’interface utilisateur HomeKit](https://developer.apple.com/homekit/ui-guidelines/) et la [référence HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Instructions de révision de l’application HomeKit

Avant de soumettre une application Xamarin. iOS activée pour HomeKit à iTunes Connect pour la publication dans l’App Store iTunes, veillez à suivre les instructions d’Apple pour les applications prenant en charge HomeKit :

- L’objectif principal de l’application _doit_ être Automation en cas d’utilisation de l’infrastructure HomeKit.
- Le texte marketing de l’application doit avertir les utilisateurs qu’HomeKit est en cours d’utilisation et ils doivent fournir une politique de confidentialité.
- La collecte des informations utilisateur ou l’utilisation de HomeKit pour la publicité est strictement interdite.

Pour connaître les instructions de révision complète, consultez [les instructions d’examen de l’App Store](https://developer.apple.com/app-store/review/guidelines/)d’Apple.

## <a name="whats-new-in-ios-9"></a>Nouveautés d’iOS 9

Apple a apporté les modifications et ajouts suivants à HomeKit pour iOS 9 :

- **Conservation des objets existants** : si un accessoire existant est modifié, le responsable de la famille ( `HMHomeManager` ) vous informe de l’élément spécifique qui a été modifié.
- **Identificateurs persistants** : toutes les classes HomeKit pertinentes incluent désormais une `UniqueIdentifier` propriété permettant d’identifier de manière unique un élément donné sur les applications compatibles HomeKit (ou les instances de la même application).
- **Gestion des utilisateurs** : ajout d’un contrôleur d’affichage intégré pour assurer la gestion des utilisateurs sur les utilisateurs qui ont accès aux appareils HomeKit dans la base de l’utilisateur principal.
- **Fonctionnalités utilisateur** : les utilisateurs de HomeKit disposent désormais d’un ensemble de privilèges qui contrôlent les fonctions qu’ils sont en mesure d’utiliser dans les accessoires HomeKit et HomeKit. Votre application doit uniquement afficher les fonctionnalités pertinentes pour l’utilisateur actuel. Par exemple, seuls les administrateurs doivent être en mesure de gérer d’autres utilisateurs.
- **Séquences** prédéfinies : des scènes prédéfinies ont été créées pour quatre événements courants qui se produisent pour l’utilisateur HomeKit moyen : obtenir, conserver, retourner, aller à la vitre. Ces scènes prédéfinies ne peuvent pas être supprimées d’une page d’hébergement.
- **Scenes and Siri** -Siri offre une prise en charge plus poussée des scènes dans iOS 9 et peut reconnaître le nom d’une scène définie dans HomeKit. Un utilisateur peut exécuter une scène simplement en parlant son nom à Siri.
- **Catégories d’accessoires** : un ensemble de catégories prédéfinies a été ajouté à tous les accessoires et permet d’identifier le type d’accessoire ajouté à un foyer ou de travailler à partir de votre application. Ces nouvelles catégories sont disponibles lors de la configuration des accessoires.
- **Prise en charge Apple Watch** : HomeKit est désormais disponible pour Watchos et le Apple Watch peut contrôler les appareils activés HomeKit sans iPhone près de la montre. HomeKit pour Watchos prend en charge les fonctionnalités suivantes : affichage des maisons, contrôle des accessoires et exécution des scènes.
- **Nouveau type de déclencheur d’événement** -en plus des déclencheurs de type Timer pris en charge dans iOS 8, iOS 9 prend désormais en charge les déclencheurs d’événements en fonction de l’état des accessoires (tels que les données de capteur) ou de la géolocalisation. Les déclencheurs d’événements utilisent `NSPredicates` pour définir les conditions d’exécution.
- **Accès à distance** : avec l’accès à distance, l’utilisateur est maintenant en mesure de contrôler ses accessoires d’automatisation de la maison HomeKit lorsqu’ils sont éloignés de la maison sur un site distant. Dans iOS 8, ce n’était pris en charge que si l’utilisateur avait une troisième génération d’Apple TV à la page. Dans iOS 9, cette limitation est levée et l’accès à distance est pris en charge via iCloud et HAP (HomeKit accessoire Protocol).
- **Nouvelles capacités Bluetooth Low Energy (BLE)** -HomeKit prend désormais en charge davantage de types d’accessoires qui peuvent communiquer via le protocole Bluetooth Low Energy (BLE). À l’aide du tunneling sécurisé HAP, un accessoire HomeKit peut exposer un autre accessoire Bluetooth sur le Wi-Fi (s’il est hors de la plage Bluetooth). Dans iOS 9, les accessoires BLE prennent entièrement en charge les notifications et les métadonnées.
- **Nouvelles catégories d’accessoires** : Apple a ajouté les nouvelles catégories d’accessoires suivantes dans iOS 9 : couvertures de fenêtre, portes motorisées, fenêtres, systèmes d’alarme, capteurs et commutateurs programmables.

Pour plus d’informations sur les nouvelles fonctionnalités de HomeKit dans iOS 9, consultez [index HomeKit](https://developer.apple.com/homekit/) d’Apple et [Nouveautés de HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) Video.

## <a name="summary"></a>Résumé

Cet article a introduit l’infrastructure domotique HomeKit d’Apple. Il a montré comment configurer et configurer des appareils de test à l’aide du simulateur d’accessoires HomeKit et comment créer une application Xamarin. iOS simple pour découvrir, communiquer avec et contrôler des appareils domotiques à l’aide de HomeKit.

## <a name="related-links"></a>Liens connexes

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Nouveautés d’iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Instructions relatives à l’interface utilisateur HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Référence du Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
