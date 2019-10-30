---
title: Multitâche pour iPad dans Xamarin. iOS
description: iOS 9 prend en charge deux applications qui s’exécutent en même temps, en utilisant le mode Diapositive sur ou fractionné. Il prend également en charge la vidéo en cours d’image.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: aeb3d01a3d0f7edbe92c9959073d859fc63486a6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031653"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitâche pour iPad dans Xamarin. iOS

_iOS 9 prend en charge deux applications qui s’exécutent en même temps, en utilisant le mode Diapositive sur ou fractionné. Il prend également en charge la vidéo en cours d’image._

![](multitasking-images/about02-sml.png "Exemple de fractionnement d’écran") ![](multitasking-images/about03-sml.png "Exemple d’image en image")

iOS 9 ajoute la prise en charge de la multitâche pour l’exécution de deux applications en même temps sur un matériel iPad spécifique. Le multitâche pour iPad est pris en charge via les fonctionnalités suivantes :

- [**Glisser-déplacer**](#Slide-Over) : permet à l’utilisateur d’exécuter temporairement une deuxième application iOS dans un panneau coulissant (à droite ou à gauche de l’écran en fonction de la direction de la langue) qui couvre approximativement 25% de l’application principale en cours d’exécution. La glissement est disponible uniquement sur un iPad Pro, iPad air, iPad air 2, iPad mini 2, iPad Mini 3 ou iPad mini 4.
- [**Mode fractionné**](#Split-View) : sur le matériel iPad pris en charge (iPad air 2, iPad mini 4 et iPad Pro uniquement), l’utilisateur peut choisir une deuxième application et l’exécuter côte à côte avec l’application en cours d’exécution en mode fractionné. L’utilisateur peut contrôler le pourcentage de l’écran principal occupé par chaque application.
- [**Image dans Picture**](#Picture-in-Picture) : pour les applications qui retournent du contenu vidéo, la vidéo peut maintenant être lue dans une fenêtre déplaçable et redimensionnable qui flotte sur les autres applications en cours d’exécution sur l’appareil iOS. L’utilisateur dispose d’un contrôle total sur la taille et la position de cette fenêtre. Picture in Picture est disponible uniquement sur un iPad Pro, iPad air, iPad air 2, iPad mini 2, iPad Mini 3 ou iPad mini 4.

Il y a plusieurs points à prendre en compte lors [de la prise en charge de la multitâche dans votre application](#Supporting-Multitasking-in-your-App), notamment :

- [Taille et orientation de l’écran](#Screen-Size-Considerations)
- [Raccourcis clavier du matériel personnalisé](#Custom-Hardware-Keyboard-Shortcuts)
- [Gestion des ressources](#Resource-Management-Considerations)

En tant que développeur d’applications, vous pouvez également refuser l’exécution [de tâches multitâches](#Opting-Out-of-Multitasking), notamment [la désactivation de la lecture vidéo PIP](#Disabling-PIP-Video-Playback).

Cet article décrit les étapes requises pour s’assurer que votre application Xamarin. iOS s’exécute correctement dans un environnement multitâche ou comment refuser l’exécution de tâches multitâches si elle n’est pas adaptée à votre application.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Vidéo multitâche pour iPad**

<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Démarrage rapide multitâche

Pour prendre en charge le mode **glisser-déplacer** ou **fractionner** , votre application doit effectuer les opérations suivantes :

- Être généré sur iOS 9 (ou version ultérieure).
- Utilisez une table de montage séquentiel pour l’écran de lancement (et non des ressources d’image).
- Utilisez une table de montage séquentiel avec des classes de taille automatique et de taille pour son interface utilisateur.
- Prendre en charge les 4 orientations d’appareils iOS (portrait, portrait vers le haut, paysage gauche & paysage).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>À propos du multitâche pour iPad

iOS 9 offre de nouvelles capacités multitâches sur iPad avec l’introduction de la _glissière_, du _mode fractionné_ (iPad air 2, iPad mini 4 et iPad Pro uniquement) et _de l’image_. Nous examinerons plus en détail ces fonctionnalités dans les sections suivantes.

<a name="Slide-Over" />

### <a name="slide-over"></a>Glisser-déplacer

La diapositive sur la fonctionnalité permet à l’utilisateur de choisir une deuxième application et de l’afficher dans un petit panneau coulissant pour fournir une interaction rapide. Le panneau déroulant est temporaire et se ferme lorsque l’utilisateur revient à nouveau avec l’application principale.

[![](multitasking-images/about01.png "The Slide Over panel")](multitasking-images/about01.png#lightbox)

L’élément principal à retenir est que l’utilisateur décide quelles applications vont s’exécuter côte à côte et que le développeur n’a aucun contrôle sur ce processus. Par conséquent, vous devrez effectuer quelques opérations pour vous assurer que votre application Xamarin. iOS s’exécute correctement dans une diapositive sur le panneau :

- **Utiliser les classes de mise en page automatique et de taille** : étant donné que votre application Xamarin. iOS peut maintenant être exécutée dans le panneau latéral coulissant, vous ne pouvez plus vous fier à l’appareil, à sa taille d’écran ou à son orientation pour mettre en page votre interface utilisateur. Pour vous assurer que votre application met correctement à l’échelle son interface, vous devez utiliser les classes de mise en page automatique et de taille. Pour plus d’informations, consultez notre [Introduction à la documentation sur les storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Utilisation efficace des ressources** : étant donné que votre application peut désormais partager le système avec une autre application en cours d’exécution, il est essentiel que votre application utilise efficacement les ressources système. Lorsque la mémoire devient incomplète, le système arrête automatiquement l’application qui consomme la plus grande partie de la mémoire. Pour plus d’informations, consultez le Guide de l' [efficacité énergétique d’Apple pour les applications iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) .

La glissement est disponible uniquement sur un iPad Pro, iPad air, iPad air 2, iPad mini 2, iPad Mini 3 ou iPad mini 4. Pour en savoir plus sur la préparation de votre application pour le glisser-déplacer, consultez la documentation [sur l’iPad adopter les améliorations multitâches](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) d’Apple.

<a name="Split-View" />

### <a name="split-view"></a>Mode Fractionné

Sur le matériel iPad pris en charge (iPad air 2, iPad mini 4 et iPad Pro uniquement), l’utilisateur peut choisir une deuxième application et l’exécuter côte à côte avec l’application en cours d’exécution en mode fractionné. L’utilisateur peut contrôler le pourcentage de l’écran principal occupé par chaque application en faisant glisser un séparateur sur l’écran.

[![](multitasking-images/about02.png "The Split View")](multitasking-images/about02.png#lightbox)

Comme glisser-déplacer, l’utilisateur décide quelles applications vont s’exécuter côte à côte et de nouveau, le développeur n’a aucun contrôle sur ce processus. En conséquence, le mode fractionné place des exigences similaires sur une application Xamarin. iOS :

- **Utiliser les classes de mise en page automatique et de taille** : étant donné que votre application Xamarin. iOS peut maintenant être exécutée en mode fractionné à la taille spécifiée de l’utilisateur, vous ne pouvez plus vous fier à l’appareil, à sa taille d’écran ou à son orientation pour mettre en page votre interface utilisateur. Pour vous assurer que votre application met correctement à l’échelle son interface, vous devez utiliser les classes de mise en page automatique et de taille. Pour plus d’informations, consultez notre [Introduction à la documentation sur les storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Utilisation efficace des ressources** : étant donné que votre application peut désormais partager le système avec une autre application en cours d’exécution, il est essentiel que votre application utilise efficacement les ressources système. Lorsque la mémoire devient incomplète, le système arrête automatiquement l’application qui consomme la plus grande partie de la mémoire. Pour plus d’informations, consultez le Guide de l' [efficacité énergétique d’Apple pour les applications iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) .

Pour en savoir plus sur la préparation de votre application pour le mode fractionné, consultez la documentation [sur l’iPad adopter les améliorations multitâches](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) d’Apple.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Image dans l’image

La fonctionnalité nouvelle image dans l’image (également appelée _PIP_) permet à l’utilisateur de regarder une vidéo dans une petite fenêtre flottante que l’utilisateur peut positionner n’importe où sur l’écran au-dessus des autres applications en cours d’exécution.

[![](multitasking-images/about03.png "An example Picture in Picture floating window")](multitasking-images/about03.png#lightbox)

Comme pour le mode glisser-déplacer et le mode fractionné, l’utilisateur dispose d’un contrôle total sur la visualisation d’une vidéo dans l’image en mode image. Si la fonction principale de votre application est de regarder la vidéo, elle devra être modifiée pour se comporter correctement en mode PIP. Dans le cas contraire, aucune modification n’est requise pour prendre en charge PIP.

Pour que votre application affiche la vidéo PIP à la demande de l’utilisateur, vous devez utiliser soit _AVKit_ , soit les _API AV Foundation_. Media Player Framework a été amorti dans iOS 9 et ne prend pas en charge PIP.

Picture in Picture est disponible uniquement sur un iPad Pro, iPad air, iPad air 2, iPad mini 2, iPad Mini 3 ou iPad mini 4. Pour plus d’informations, consultez notre [exemple d’application PictureInPicture](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) et la documentation d’Apple [sur Picture démarrage rapide](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) .

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Prise en charge des tâches multitâches dans votre application

Pour toute application Xamarin. iOS existante, la prise en charge de la multitâche est une tâche transparente tant que votre application suit déjà les guides de conception d’Apple et les meilleures pratiques pour iOS 8. Cela signifie que l’application doit utiliser des storyboards avec des classes de mise en page automatique et de taille pour ses dispositions d’interface utilisateur (pour plus d’informations, consultez notre [Introduction aux storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) ).

Pour ces applications, peu ou aucune modification n’est requise pour prendre en charge les tâches multitâches et pour se comporter de façon efficace. Si l’interface utilisateur de votre application a été créée à l’aide d’autres méthodes, telles que le C# positionnement et le redimensionnement des éléments d’interface utilisateur dans le code, ou si elle s’appuie sur des orientations ou des tailles d’écran d’appareil spécifiques, une modification importante est nécessaire pour prendre en charge le multitâche iOS 9 correctement.

Pour prendre en charge le multitâche iOS 9 sur toute nouvelle application Xamarin. iOS, utilisez de nouveau les storyboards avec les classes de mise en page automatique et de taille pour toutes les dispositions de l’interface utilisateur de l’application et implémentez les instructions dans les sections suivantes.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Considérations relatives à la taille et à l’orientation de l’écran

Avant iOS 9, vous pouviez concevoir votre application sur des tailles et des orientations d’écran d’appareil spécifiques. Comme une application peut maintenant être exécutée dans un panneau déroulant ou en mode fractionné, elle peut s’exécuter dans une classe de taille horizontale compacte ou normale sur iPad, indépendamment de l’orientation physique ou de la taille de l’écran de l’appareil.

[![](multitasking-images/sizeclasses01.png "Screen Size and Orientation Considerations")](multitasking-images/sizeclasses01.png#lightbox)

Sur un iPad, une application en plein écran contient des classes de taille horizontale et verticale standard. Tous les iPhone, à l’exception de iPhone 6 plus et iPhone 6S plus, ont des classes de taille compacte dans les deux sens, quelle que soit l’orientation. L’iPhone 6 plus et iPhone 6S plus en mode paysage ont une classe de taille horizontale normale et une classe de taille verticale compacte (similaire à un iPad mini).

Sur iPad qui prennent en charge le mode glisser-déplacer et fractionner, vous pouvez vous retrouver avec les combinaisons suivantes :

| **Orientation** | **Application principale** | **Application secondaire** |
|--- |--- |--- |
| **Aperçu** |75% de l’écran<br />Compact horizontal<br />Vertical normal|25% de l’écran<br />Compact horizontal<br />Vertical normal|
| **Jardin** |75% de l’écran<br />Horizontal normal<br />Vertical normal|25% de l’écran<br />Compact horizontal<br />Vertical normal|
| **Jardin** |50% de l’écran<br />Compact horizontal<br />Vertical normal|50% de l’écran<br />Compact horizontal<br />Vertical normal|

Dans l’exemple d’application [MuliTask](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) , si elle est exécutée en mode plein écran sur un iPad en mode paysage, elle présente la liste et la vue détaillée en même temps :

[![](multitasking-images/sizeclasses03.png "The list and the detail view presented at the same time")](multitasking-images/sizeclasses03.png#lightbox)

Si la même application est exécutée dans une diapositive sur le panneau, elle est présentée sous la forme d’une classe de taille horizontale compacte et affiche uniquement la liste :

[![](multitasking-images/sizeclasses04.png "Only the list presented when the device is horizontal")](multitasking-images/sizeclasses04.png#lightbox)

Pour vous assurer que votre application se comporte correctement dans ces situations, vous devez adopter des collections de traits avec les classes de taille et respecter les interfaces `IUIContentContainer` et `IUITraitEnvironment`. Pour plus d’informations, consultez Référence de la [classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) d’Apple et notre guide de [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .

En outre, vous ne pouvez plus vous fier aux limites de l’écran appareils pour définir la zone visible de l’application, mais vous devez utiliser les limites de fenêtre de votre application à la place. Étant donné que les limites de la fenêtre sont entièrement sous le contrôle de l’utilisateur, vous ne pouvez pas les ajuster par programmation ou empêcher l’utilisateur de modifier ces limites.

Enfin, votre application doit utiliser un fichier de table de montage séquentiel pour présenter son écran de lancement, par opposition à l’utilisation d’un ensemble de fichiers image **. png** et prendre en charge les quatre orientations d’interface (portrait, portrait vers le haut, paysage gauche et paysage droit) à prendre en compte. en cours d’exécution dans une diapositive sur le panneau ou en mode fractionné.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Raccourcis clavier du matériel personnalisé

Dans iOS 9 qui s’exécute sur un iPad, Apple offre une prise en charge étendue des claviers matériels. les iPad ont toujours inclus la prise en charge du clavier externe de base via Bluetooth et certains fabricants de claviers ont créé des claviers qui comprenaient des clés câblées propres à iOS.

Désormais, avec iOS 9, les applications peuvent créer leurs propres raccourcis clavier personnalisés. En outre, certains raccourcis clavier de base sont disponibles, tels que **commande-C** (copier), **commande-X** (couper), **commande-V** (coller) et **commande-Shift-H** (début), sans qu’une application écrite spécifiquement y soit résolue.

L' **onglet de commande** affiche un sélecteur d’application qui permet à l’utilisateur de basculer rapidement entre les applications à partir du clavier, de la même façon que la Mac OS :

[![](multitasking-images/keyboard01.png "The app switcher")](multitasking-images/keyboard01.png#lightbox)

Si une application iOS 9 comprend des raccourcis clavier, l’utilisateur peut maintenir la touche enfoncée sur la **commande**, l' **option** ou les touches de **contrôle** pour les afficher dans une fenêtre contextuelle :

[![](multitasking-images/keyboard02.png "The keyboard shortcuts popup")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Définition de raccourcis clavier personnalisés

Si nous ajoutons le code suivant à un contrôleur d’affichage ou de vue dans notre application, quand cette vue ou ce contrôleur est visible, un raccourci clavier personnalisé est disponible :

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

Tout d’abord, nous remplaçons la propriété `CanBecomeFirstResponder` et retournons `true` afin que le contrôleur de vue ou de vue puisse recevoir une entrée au clavier. 

Ensuite, nous remplaçons la propriété `KeyCommands` et créons une nouvelle `UIKeyCommand` pour la séquence de touches **commande-N** . Lorsque la frappe de touche est activée, nous appelons la méthode `NewEntry` (que nous exposant à iOS 9 à l’aide de la commande `Export`) pour effectuer l’action demandée.

Si nous exécutons cette application sur un iPad avec un clavier matériel attaché et que l’utilisateur tape **-N**, une nouvelle entrée est ajoutée à la liste. Si l’utilisateur maintient la touche de **commande** enfoncée, la liste des raccourcis s’affiche :

[![](multitasking-images/keyboard03.png "The keyboard shortcuts popup")](multitasking-images/keyboard03.png#lightbox)

Pour obtenir un exemple d’implémentation, consultez l’exemple d' [application multitâche](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) .

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Considérations relatives à la gestion des ressources

Même pour les applications qui utilisent déjà les guides de conception et les meilleures pratiques iOS 8, une gestion efficace des ressources peut toujours être un problème. Dans iOS 9, les applications n’ont plus d’utilisation exclusive de la mémoire, du processeur ou d’autres ressources système.

Par conséquent, vous devez affiner votre application Xamarin. iOS afin d’utiliser les ressources système de manière efficace, sinon elle est confrontée à l’arrêt en cas de mémoire insuffisante. Cela est également vrai pour les applications qui refusent les tâches multitâches, car une deuxième application peut toujours être exécutée dans une diapositive sur un panneau ou une image dans une fenêtre d’image nécessitant des ressources supplémentaires ou entraînant une fréquence d’actualisation inférieure à 60 images par seconde.

Tenez compte des actions utilisateur suivantes et de leurs implications :

- **Saisie de texte dans une diapositive** à l’écran-même si votre application n’a pas d’entrée de texte, le clavier système peut désormais être affiché sur son interface utilisateur. Par conséquent, l’application peut avoir besoin de répondre aux notifications d’affichage du clavier (telles que l’affichage et le masquage du clavier).
- **Exécution d’une deuxième application dans une diapositive** à l’aide du panneau : la nouvelle application s’exécute maintenant au premier plan et en concurrence avec l’application existante pour les ressources système telles que la mémoire et les cycles de processeur.
- **Lire une vidéo dans une fenêtre PIP** -non seulement cette fenêtre recouvre une partie de l’interface de votre application, mais l’application qui a lancé la vidéo est toujours en cours d’exécution en arrière-plan et consomme des ressources processeur et mémoire.

Pour vous assurer que votre application utilise les ressources efficacement, vous devez effectuer les opérations suivantes :

- **Profilez l’application avec les instruments** : Vérifiez les fuites de mémoire, l’utilisation de l’UC et les zones où l’application peut bloquer le thread principal.
- **Répondre aux méthodes de transitions d’État** : dans votre fichier **AppDelegate.cs** , remplacez et répondez aux méthodes de modification d’État telles que l’application qui entre ou retourne à partir de l’arrière-plan. Libérez les ressources inrequises, telles que les images, les données ou les vues et le contrôleur d’affichage.
- **Tester côte à côte avec des applications gourmandes en mémoire** : exécutez votre application à l’aide de l’affichage glisser-déplacer et du mode fractionné sur le matériel iOS physique avec une application gourmande en mémoire, telle que Maps (en mode d’affichage satellite) et vérifiez que les deux applications restent réactives et ne se bloquent pas.

Pour plus d’informations sur la gestion des ressources, consultez Guide de l' [efficacité énergétique d’Apple pour les applications iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) .

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Désactivation du multitâche

Alors qu’Apple suggère que toutes les applications iOS 9 prennent en charge la multitâche, il peut y avoir des raisons très spécifiques pour une application, comme les jeux ou les applications de caméra qui nécessitent que le plein écran fonctionne correctement.

Pour que votre application Xamarin. iOS ne soit pas exécutée dans un panneau déroulant ou en mode fractionné, modifiez le fichier **info. plist** du projet et cochez la case requiert l’affichage **plein écran**:

[![](multitasking-images/fullscreen01.png "Opting Out of Multitasking")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Si vous ne le désabonnez pas, vous empêchez l’exécution de votre application en mode glisser-déplacer ou fractionner, mais n’empêche pas une autre application d’être exécutée dans la diapositive vidéo ou une image de la vidéo image de s’afficher avec votre application.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Désactivation de la lecture vidéo PIP

Dans la plupart des cas, votre application doit permettre à l’utilisateur de lire tout contenu vidéo qu’il affiche dans une image de la fenêtre flottante de l’image. Toutefois, il peut y avoir des situations où cela n’est peut-être pas souhaité, par exemple des vidéos de séquences Cut.

Pour désactiver la lecture vidéo PIP, procédez comme suit dans votre application :

- Si vous utilisez un `AVPlayerViewController` pour afficher la vidéo, affectez à la propriété `AllowsPictureInPicturePlayback` la valeur `false`.
- Si vous utilisez le `AVPlayerLayer` pour afficher la vidéo, n’instanciez pas une `AVPictureInPictureController`.
- Si vous utilisez un `WKWebView` pour afficher la vidéo, affectez à la propriété `AllowsPictureInPictureMediaPlayback` la valeur `false`.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé les étapes requises pour s’assurer qu’une application Xamarin. iOS s’exécutera et se comportera correctement dans les nouvelles capacités multitâches d’iOS 9 pour iPad. En outre, le fait de ne pas être un bon choix est de ne pas convenir du multitâche pour les applications.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Multitâche (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask)
- [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adoption des améliorations de multitâche sur iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Billet de blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
