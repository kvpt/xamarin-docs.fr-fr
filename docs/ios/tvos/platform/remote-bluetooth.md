---
title: Contrôleurs distants et Bluetooth Siri pour tvOS dans Xamarin
description: Cet article explique comment utiliser les contrôleurs de jeu Siri distants et Bluetooth dans des applications tvOS écrites avec Xamarin.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 71a74d8f9046cd978a40b03da1921cd1fac9405f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769178"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Contrôleurs distants et Bluetooth Siri pour tvOS dans Xamarin

Les utilisateurs de votre application Xamarin. tvOS n’interagissent pas directement avec son interface, comme avec iOS, où ils appuient sur des images sur l’écran de l’appareil, mais indirectement à travers la salle à l’aide de la [télécommande Siri](#The-Siri-Remote).

Si votre application est un jeu, vous pouvez éventuellement créer une prise en charge pour les [contrôleurs de jeu Bluetooth](#Bluetooth-Game-Controllers) tiers, effectués pour iOS (IFM) dans votre application.

[![](remote-bluetooth-images/intro01.png "Le contrôleur de jeu et distant Bluetooth")](remote-bluetooth-images/intro01.png#lightbox)

Cet article décrit les [Siri Remote](#The-Siri-Remote), [touch surface](#Touch-Surface-Gestures) et les [boutons distants Siri](#Siri-Remote-Buttons) et montre comment les utiliser via des [mouvements et des storyboards](#Gestures-and-Storyboards), des [gestes et du code](#Gestures-and-Code) et [une gestion des événements de bas niveau](#Low-Level-Event-Handling). Enfin, il aborde l' [utilisation des contrôleurs de jeu](#Working-with-Game-Controllers) dans une application Xamarin. tvOS.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri à distance

La principale façon dont les utilisateurs interagissent avec Apple TV, et votre application Xamarin. tvOS, s’effectue via la télécommande Siri incluse. Apple a conçu le à distance pour relier la distance entre l’utilisateur assis sur le canapé et l’interface utilisateur d’Apple TV affichée dans la pièce sur l’écran de télévision.

Votre défi en tant que développeur d’applications tvOS est la création d’une interface utilisateur rapide, facile à utiliser et visuellement attrayante qui tire parti de la surface tactile, de l’accéléromètre, du gyroscope et des boutons à distance de Siri.

[![](remote-bluetooth-images/remote01.png "Siri à distance")](remote-bluetooth-images/remote01.png#lightbox)

Le Siri distant présente les fonctionnalités suivantes et les utilisations attendues dans votre application tvOS :

|Fonctionnalité|Utilisation générale de l’application|Utilisation de l’application de jeu|
|---|---|---|
|**Surface tactile**<br />Faites glisser pour naviguer, puis appuyez sur pour sélectionner et maintenir les menus contextuels.|**TAP/balayer**<br />Navigation dans l’interface utilisateur entre les éléments pouvant être activés.<br /><br />**Cliquez sur**<br />Active l’élément sélectionné (en focus).|**TAP/balayer**<br />Dépend de la conception du jeu et peut être utilisé comme un BMD en appuyant sur les bords.<br /><br />**Cliquez sur**<br />Exécutez la fonction de bouton principal.|
|**Menu**<br />Appuyez pour revenir à l’écran ou au menu précédent.|Revient à l’écran précédent et quitte l’écran d’accueil Apple TV à partir de l’écran principal de l’application.|Suspendre et reprendre le jeu, revient à l’écran précédent et quitte l’écran d’accueil Apple TV à partir de l’écran principal de l’application.|
|**Siri/Rechercher**<br />Dans les pays avec Siri, maintenez la touche enfoncée pour le contrôle vocal, dans tous les autres pays, affiche l’écran de recherche.|N/A|N/A|
|**Lecture/pause**<br />Lire et suspendre un support ou fournit une fonction secondaire dans les applications.|Démarre la lecture du média et interrompt/reprend la lecture.|Exécute la fonction de bouton secondaire ou ignore la vidéo d’introduction (si elle existe).|
|**Accueil**<br />Appuyez sur pour revenir à l’écran d’accueil, double-cliquez pour afficher les applications en cours d’exécution, appuyez sur la touche et maintenez-la enfoncée.|N/A|N/A|
|**Volume**<br />Contrôle le volume de l’équipement audio/vidéo attaché.|N/A|N/A|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Mouvements de la surface tactile

La surface tactile de la Siri distante est en mesure de détecter une variété de mouvements à un seul doigt auxquels vous pouvez répondre dans votre application Xamarin. tvOS :

|Balayer|Clic|Taper|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Déplace la sélection (Focus) entre les éléments de l’interface utilisateur à l’écran (vers le haut, vers la gauche, à droite). Le balayage peut être utilisé pour faire défiler rapidement des listes volumineuses de contenu à l’aide de l’inertie.|Active l’élément sélectionné (en focus) ou agit comme le bouton principal dans un jeu. Cliquer et maintenir peut activer des menus contextuels ou des fonctions secondaires.|Appuyer légèrement sur la surface tactile sur les bords agit comme des boutons directionnels sur un BMD, en déplaçant le focus vers le haut, vers le haut, vers la gauche ou vers la droite en fonction de la zone. En fonction de l’application, peut être utilisé pour révéler des contrôles masqués.|

Apple fournit les suggestions suivantes pour l’utilisation des gestes de surface tactile :

- La **distinction entre les clics et** les clics est une action intentionnelle de l’utilisateur et est bien adaptée à la sélection, à l’activation et au bouton principal d’un jeu. Le taraudage est plus subtil et doit être utilisé avec modération, car l’utilisateur maintient souvent le Siri à distance et peut facilement activer un événement TAP.
- **Ne pas redéfinir les gestes standard** : l’utilisateur a l’attente que des mouvements spécifiques effectuent des actions spécifiques, vous ne devez pas redéfinir la signification ou la fonction de ces mouvements dans votre application. La seule exception est une application de jeu pendant le jeu actif.
- **Définir de nouveaux gestes avec modération** , l’utilisateur a l’attente que des mouvements spécifiques effectuent des actions spécifiques. Vous devez éviter de définir des mouvements personnalisés pour effectuer des actions standard. Là encore, les jeux sont l’exception la plus courante dans laquelle les gestes personnalisés peuvent ajouter un jeu amusant et immersif au jeu.
- Le **cas échéant, répondez aux pressions d-pad** -l’appui faible sur les bords de la surface tactile réagira comme un BMD sur un contrôleur de jeu en déplaçant le focus ou la direction vers le haut, vers le bas, vers la gauche ou vers la droite. Le cas échéant, vous devez répondre à ces gestes dans votre application ou votre jeu.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Boutons distants de Siri

En plus des mouvements sur la surface tactile, votre application peut répondre à l’utilisateur en cliquant sur la surface tactile ou en appuyant sur le bouton de lecture/pause. Si vous accédez au Siri à distance à l’aide de l’infrastructure du contrôleur de jeu, vous pouvez également détecter le bouton de menu enfoncé.

En outre, les enfonceurs de bouton de menu peuvent être détectés à l’aide `UIKit` d’un module de reconnaissance de mouvement avec des éléments standard. Si vous interceptez le bouton de menu enfoncé, vous êtes chargé de fermer l’affichage et le contrôleur d’affichage en cours et de revenir au précédent.

> [!IMPORTANT]
> Vous devez **toujours** attribuer une fonction au bouton de lecture/pause sur l’accès à distance. Le fait de disposer d’un bouton non fonctionnel peut rendre votre application ininterrompue par l’utilisateur final. Si vous n’avez pas de fonction valide pour ce bouton, assignez la même fonction que le bouton principal (clic tactile).

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Mouvements et storyboards

Le moyen le plus simple de travailler avec la Siri distante dans votre application Xamarin. tvOS consiste à ajouter des modules de reconnaissance de mouvement à vos vues dans le concepteur d’interface.

Pour ajouter un module de reconnaissance des mouvements, procédez comme suit :

1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour modifier le concepteur d’interface.
2. Faites glisser un module de **reconnaissance de mouvement TAP** de la **bibliothèque** et déposez-le sur la vue :

    [![](remote-bluetooth-images/storyboard01.png "Module de reconnaissance de mouvement TAP")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Cochez la case **Sélectionner** dans la section du **bouton** de l' **inspecteur d’attribut**:

    [![](remote-bluetooth-images/storyboard02.png "Cocher sélectionner")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Select** signifie que le geste répond à l’utilisateur en cliquant sur la **surface tactile** sur le Siri distant. Vous avez également la possibilité de répondre aux boutons **menu**, **lire/suspendre**, **monter**, **descendre**, à **gauche** et à **droite** .
5. Ensuite, associez une **action** à partir du module de **reconnaissance de mouvement TAP** et appelez- `TouchSurfaceClicked`la :

    [![](remote-bluetooth-images/storyboard03.png "Action du module de reconnaissance de mouvement TAP")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac.

Modifiez le fichier de votre contrôleur `FirstViewController.cs`d’affichage (exemple) et ajoutez le code suivant pour gérer le mouvement déclenché :

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). En particulier, la [création de l’interface utilisateur](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) et [l’écriture du code à l’aide des sorties et](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) des sections actions.

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Gestes et code

Si vous le souhaitez, vous pouvez créer des mouvements C# directement dans le code et les ajouter aux vues dans votre interface utilisateur. Par exemple, pour ajouter une série de modules de reconnaissance de mouvement de balayage, modifiez votre contrôleur d’affichage et ajoutez le code suivant :

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>Gestion des événements de bas niveau

Si vous créez un type personnalisé basé sur `UIKit` dans votre application Xamarin. tvOS (par exemple `UIView`), vous avez également la possibilité de fournir une gestion de bas niveau de l’appui sur `UIPress` les boutons via des événements.

Un `UIPress` événement consiste à tvOS ce qu' `UITouch` est un événement iOS, à `UIPress` l’exception des informations relatives aux presses sur les Siri distants ou autres périphériques Bluetooth attachés (comme un contrôleur de jeu). `UIPress`les événements décrivent le bouton enfoncé et son état (commencé, annulé, modifié ou terminé).

Pour les boutons analogiques sur des appareils tels que `UIPress` les contrôleurs de jeu Bluetooth, retourne également la quantité de force appliquée au bouton. La `Type` propriété de l' `UIPress` événement définit le bouton physique qui a changé d’État, tandis que les autres propriétés décrivent la modification qui s’est produite.

Le code suivant montre un exemple de gestion des événements de `UIPress` bas niveau pour `UIView`un :

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

Comme pour les `UIPress` événements,sivousdevezimplémenterl’undesremplacementsd’événements,vousdevezimplémenter`UITouch` les quatre.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Contrôleurs de jeu Bluetooth

En plus de la télécommande standard Siri fournie avec les contrôleurs Apple TV, tiers, effectués pour iOS (IFM), les contrôleurs de jeu Bluetooth peuvent être associés au téléviseur Apple et utilisés pour contrôler votre application Xamarin. tvOS.

[![](remote-bluetooth-images/game01.png "Contrôleurs de jeu Bluetooth")](remote-bluetooth-images/game01.png#lightbox)

Les contrôleurs de jeu peuvent être utilisés pour améliorer le jeu et donner une idée de l’immersion dans un jeu. Elles peuvent également être utilisées pour contrôler l’interface standard Apple TV, de sorte que l’utilisation ne doit pas basculer entre le contrôleur distant et le contrôleur.

> [!IMPORTANT]
> Les contrôleurs de jeu Bluetooth sont un achat facultatif que les utilisateurs finaux peuvent effectuer, votre application ne peut pas forcer l’utilisateur à en acheter un. Si votre application prend en charge les contrôleurs de jeu, elle doit également prendre en charge la Siri distante afin que le jeu soit utilisable par tous les utilisateurs d’Apple TV.

Un contrôleur de jeu présente les fonctionnalités suivantes et les utilisations attendues au sein de votre application tvOS :

|Fonctionnalité|Utilisation générale de l’application|Utilisation de l’application de jeu|
|---|---|---|
|**D-Pad**|Parcourt les éléments d’interface utilisateur (modifie le focus).|Dépend du jeu.|
|**A**|Active l’élément sélectionné (en focus).|Exécute la fonction de bouton principal et confirme les actions de la boîte de dialogue.|
|**B**|Revient à l’écran précédent ou quitte l’écran d’accueil si l’écran principal de l’application s’affiche.|Exécute la fonction de bouton secondaire ou retourne à l’écran précédent.|
|**X**|Démarre la lecture du média ou suspend/reprend la lecture.|Dépend du jeu.|
|**Y**|N/A|Dépend du jeu.|
|**Menu**|Revient à l’écran précédent ou quitte l’écran d’accueil si l’écran principal de l’application s’affiche.|Suspendre/reprendre le jeu, revenir à l’écran précédent ou quitter l’écran d’accueil si l’écran principal de l’application s’affiche.|
|**Bouton gauche de l’épaule**|Navigue vers la gauche.|Dépend du jeu.|
|**Déclencheur gauche**|Navigue vers la gauche.|Dépend du jeu.|
|**Bouton de droite**|Navigue vers la droite.|Dépend du jeu.|
|**Déclencheur droit**|Navigue vers la droite|Dépend du jeu.|
|**Joystick gauche**|Parcourt les éléments d’interface utilisateur (modifie le focus).|Dépend du jeu.|
|**Joystick droit**|N/A|Dépend du jeu.|

Apple fournit les suggestions suivantes pour l’utilisation des contrôleurs de jeu :

- **Confirmer les connexions du contrôleur de jeu** : votre application tvOS peut être démarrée et arrêtée à tout moment par l’utilisateur final. Vous devez toujours vérifier la présence d’un contrôleur de jeu au moment du démarrage de l’application ou en éveil et prendre les mesures nécessaires.
- **Assurez-vous que votre application fonctionne sur les contrôleurs distants et de jeux Siri** . les utilisateurs ne doivent pas basculer entre le Siri distant et un contrôleur de jeu pour utiliser votre application. Testez fréquemment votre application avec les deux types de contrôleurs garantissant que tout est facile à parcourir et à fonctionner comme prévu.
- **Fournir un moyen** d’appuyer sur le bouton de menu doit toujours revenir à l’écran précédent. Si l’utilisateur se trouve sur l’écran de l’application principale, le bouton de menu doit les retourner à l’écran d’accueil Apple TV. Pendant le jeu, le bouton de menu doit afficher une alerte qui donne à l’utilisateur la possibilité de suspendre/reprendre le jeu ou de revenir au menu principal.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Utilisation des contrôleurs de jeu

Comme indiqué ci-dessus, en plus de la Siri distante standard fournie avec Apple TV, l’utilisateur peut éventuellement joindre un tiers, créé pour les contrôleurs de jeu Bluetooth iOS (IFM) et l’utiliser pour contrôler votre application Xamarin. tvOS.

Si votre application exigeait une entrée de contrôleur de bas niveau, vous pouvez utiliser l' [infrastructure de contrôleur de jeu](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) d’Apple, qui présente les modifications suivantes pour tvOS :

- Le profil de contrôleur de jeu`GCMicroGamepad`micro () a été ajouté pour cibler le Siri distant.
- La nouvelle `GCEventViewController` classe peut être utilisée pour acheminer des événements de contrôleur de jeu par le biais de votre application. Pour plus d’informations, consultez la section détermination de l' [entrée du contrôleur de jeu](#determining-game-controller-input) ci-dessous.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Configuration requise pour le contrôleur de jeu

Apple a plusieurs exigences spécifiques qui doivent être satisfaites si votre application Xamarin. tvOS prend en charge les contrôleurs de jeu :

- **Vous devez prendre en charge l’Siri à distance** . vous devez toujours prendre en charge le Siri distant. Votre jeu ne peut pas exiger que le contrôleur de jeu tiers soit lisible.
- **Vous devez prendre en charge la disposition des contrôles étendus** . tous les contrôleurs de jeu tvOS sont des contrôleurs étendus non Formfitting.
- Les **jeux doivent être lisibles à l’aide de contrôleurs autonomes** : Si votre application prend en charge un contrôleur de jeu étendu, elle doit être prise en charge uniquement avec ce contrôleur de jeu.
- **Vous devez prendre en charge le bouton de lecture/pause** -pendant le jeu, si l’utilisateur appuie sur le bouton de lecture/pause, vous devez afficher une alerte qui donne à l’utilisateur la possibilité de suspendre/reprendre le jeu ou de revenir au menu principal.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Activation de la prise en charge des contrôleurs de jeu

Pour activer la prise en charge du contrôleur de jeu dans votre application Xamarin. tvOS `Info.plist` , double-cliquez sur le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier :

[![](remote-bluetooth-images/game02.png "Éditeur info. plist")](remote-bluetooth-images/game02.png#lightbox)

Dans la section **contrôleur de jeu** , cochez la case **activer les contrôleurs**de jeu, puis cochez tous les types de contrôleurs de jeu qui seront pris en charge par l’application.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Utilisation du Siri à distance en tant que contrôleur de jeu

La télécommande Siri fournie avec Apple TV peut être utilisée en tant que contrôleur de jeu limité. Comme les autres contrôleurs de jeu, il s’affiche dans la structure du `GCController` contrôleur de jeu en tant `GCMotion` qu’objet `GCMicroGamepad` et prend en charge à la fois les profils et.

Le Siri distant présente les caractéristiques suivantes lors de son utilisation en tant que contrôleur de jeu :

- La surface tactile peut être utilisée comme un BMD qui fournit des données d’entrée analogiques.
- La télécommande peut être utilisée dans une orientation portrait ou paysage et votre application décide si l’objet de profil doit inverser automatiquement les données d’entrée.
- Le fait de cliquer sur la surface tactile agit comme appuyer sur le bouton **a** sur un contrôleur de jeu.
- Le bouton de lecture/pause agit comme le bouton **X** sur un contrôleur de jeu.
- Le bouton de menu doit afficher une alerte qui donne à l’utilisateur la possibilité de suspendre/reprendre le jeu ou de revenir au menu principal.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Détermination de l’entrée du contrôleur de jeu

Contrairement à IOS où les événements de contrôleur de jeu peuvent être reçus en parallèle avec les événements tactiles, tvOS traite tous les événements de `UIKit` bas niveau pour fournir des événements de haut niveau. Par conséquent, si vous avez besoin d’accéder aux événements du contrôleur de jeu de bas niveau, vous devez désactiver `UIKit`le comportement par défaut de.

Sur tvOS, lorsque vous souhaitez traiter directement une entrée de contrôleur de jeu, vous devez `GCEventViewController` utiliser une (ou une sous-classe) pour afficher l’interface utilisateur du jeu. Chaque fois `GCEventViewController` qu’un est le *premier répondeur*, l’entrée du contrôleur de jeu est capturée et remise à votre application par le biais de l’infrastructure du contrôleur de jeu.

Vous pouvez utiliser la `UserInteractionEnabled` propriété de la `GCEventViewController` classe pour basculer entre le traitement et le traitement des événements.

Pour plus d’informations sur l’implémentation de la prise en charge des contrôleurs de jeu, consultez la section [utilisation des contrôleurs de jeu](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) d’Apple dans le [Guide de programmation d’applications pour TvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) et le [Guide de programmation du contrôleur de jeu](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la nouvelle télécommande Siri fournie avec la TV Apple, les gestes de surface tactile et les boutons distants Siri. Ensuite, il a abordé l’utilisation des mouvements et des storyboards, des gestes et du code et des événements de bas niveau. Enfin, si vous avez abordé l’utilisation des contrôleurs de jeu.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
