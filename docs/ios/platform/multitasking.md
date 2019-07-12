---
title: Multitâche pour iPad dans Xamarin.iOS
description: iOS 9 prend en charge les deux applications qui s’exécutent en même temps, à l’aide de la diapositive sur ou mode fractionné. Il prend également en charge la lecture d’image vidéo.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 888e00fbdbf30b5b2842bc30822a55f57372eb34
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831811"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitâche pour iPad dans Xamarin.iOS

_iOS 9 prend en charge les deux applications qui s’exécutent en même temps, à l’aide de la diapositive sur ou mode fractionné. Il prend également en charge la lecture d’image vidéo._

![](multitasking-images/about02-sml.png "Exemple d’écran de fractionner") ![](multitasking-images/about03-sml.png "d’image exemple")

iOS 9 prend en charge de multitâche pour l’exécution des deux applications en même temps sur un matériel spécifique iPad. Multitâche pour iPad est pris en charge via les fonctionnalités suivantes :

- [**Sur la diapositive** ](#Slide-Over) -permet à l’utilisateur exécuter temporairement une deuxième application iOS dans une diapositive panneau (soit sur le côté droit ou gauche de l’écran en fonction de l’orientation de la langue) qui couvre environ 25 % de l’application principale en cours d’exécution. Faites glisser sur est disponible uniquement sur un iPad Pro, iPad Air, iPad Air 2, iPad Mini 2, iPad Mini 3 ou iPad Mini 4.
- [**Mode fractionné** ](#Split-View) -sur du matériel pris en charge iPad (iPad Air 2, iPad Mini 4 et iPad Pro uniquement), l’utilisateur peut choisir une deuxième application et l’exécuter côte à côte avec l’application en cours d’exécution dans un mode d’écran de fractionnement. L’utilisateur peut contrôler le pourcentage de l’écran principal qui occupe de chaque application.
- [**Image dans l’image** ](#Picture-in-Picture) : pour les applications que vous lire du contenu vidéo, la vidéo peut maintenant être lues dans une fenêtre déplaçable et redimensionnable qui flotte au-dessus des autres applications en cours d’exécution sur l’appareil iOS. L’utilisateur a un contrôle total sur la taille et la position de cette fenêtre. Image dans l’image est disponible uniquement sur un iPad Pro, iPad Air, iPad Air 2, iPad Mini 2, iPad Mini 3 ou iPad Mini 4.

Il existe un certain nombre de choses à prendre en compte lorsque [prenant en charge le multitâche dans votre application](#Supporting-Multitasking-in-your-App), y compris :

- [Taille de l’écran et l’Orientation](#Screen-Size-Considerations)
- [Raccourcis clavier de matériel personnalisé](#Custom-Hardware-Keyboard-Shortcuts)
- [Gestion des ressources](#Resource-Management-Considerations)

En tant que développeur d’applications que vous pouvez également [refuser multitâche](#Opting-Out-of-Multitasking), y compris [la désactivation de la lecture de vidéo PIP](#Disabling-PIP-Video-Playback).

Cet article décrit les étapes requises pour vous assurer que votre application Xamarin.iOS s’exécute correctement dans un environnement multitâche ou comment se désabonner multitâche si elle n’est pas une bonne adapté à votre application.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Multitâche pour iPad vidéo**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Guide de démarrage rapide multitâche

Pour prendre en charge **diapositive sur** ou **mode fractionné** votre application doit effectuer les opérations suivantes :

- Généré à partir d’iOS 9 (ou version ultérieure).
- Utiliser une table de montage séquentiel pour son écran de lancement (et pas les composants image).
- Utiliser une table de montage séquentiel avec mise en forme automatique et les Classes de taille pour son interface utilisateur.
- Prend en charge toutes les 4 iOS orientations de l’appareil (Portrait, envers Portrait, paysage gauche et droite du paysage).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Sur le multitâche pour iPad

iOS 9 offre des capacités multitâche nouvelles sur iPad avec l’introduction de _diapositive sur_, _mode fractionné_ (iPad Air 2, iPad Mini 4 et iPad Pro) et _Picture in Picture_. Nous allons examiner ces fonctionnalités plus en détail dans les sections suivantes.

<a name="Slide-Over" />

### <a name="slide-over"></a>Diapositive sur

La fonctionnalité des diapositives sur permet à l’utilisateur choisir une deuxième application et les afficher dans le panneau glissant petit pour fournir une interaction rapide. Le panneau des diapositives sur est temporaire et se ferme lorsque l’utilisateur revient à l’utilisation de l’application principale à nouveau.

[![](multitasking-images/about01.png "Le panneau des diapositives sur")](multitasking-images/about01.png#lightbox)

Le principal point à retenir est que l’utilisateur décide de quelles deux applications exécutent côte à côte et que le développeur n’a aucun contrôle sur ce processus. Par conséquent, il existe quelques points que vous devez faire pour que votre application Xamarin.iOS s’exécute correctement dans le panneau des diapositives sur :

- **Utilisez les Classes de taille et de mise en forme automatique** , parce que votre application Xamarin.iOS peut maintenant être exécutée dans le panneau latéral de COULISSANTS, vous ne pouvez plus compter sur l’appareil, sa taille d’écran ou son orientation à disposition votre interface utilisateur. Pour vous assurer que votre application s’adapte son interface correctement, vous devez utiliser les Classes de taille et de mise en forme automatique. Pour plus d’informations, consultez notre [Introduction aux Storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) documentation.
- **Utiliser efficacement les ressources** , étant donné que votre application peut désormais partager le système avec une autre application en cours d’exécution, il est essentiel que votre application utilise efficacement les ressources système. Lorsque la mémoire devient éparse, le système s’arrête automatiquement l’application qui consomme le plus de mémoire. Consultez d’Apple [l’efficacité énergétique : Guide pour les applications iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) pour plus d’informations.

Faites glisser sur est disponible uniquement sur un iPad Pro, iPad Air, iPad Air 2, iPad Mini 2, iPad Mini 3 ou iPad Mini 4. Pour en savoir plus sur la préparation de votre application pour des diapositives sur, consultez le site d’Apple [adopter les améliorations de multitâche sur iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentation.

<a name="Split-View" />

### <a name="split-view"></a>Mode Fractionné

Sur le matériel pris en charge iPad (iPad Air 2, iPad Mini 4 et iPad Pro uniquement), l’utilisateur peut choisir une deuxième application et exécuter côte à côte avec l’application en cours d’exécution dans un mode d’écran de fractionnement. L’utilisateur peut contrôler le pourcentage de l’écran principal qui occupe de chaque application en faisant glisser un à l’écran séparateur.

[![](multitasking-images/about02.png "Le mode fractionné")](multitasking-images/about02.png#lightbox)

Comme la diapositive sur l’utilisateur décide de quelles deux applications être s’exécuteront côte à côte et là encore, le développeur n’a aucun contrôle sur ce processus. Par conséquent, le mode fractionné place des exigences similaires sur une application Xamarin.iOS :

- **Utilisez les Classes de taille et de mise en forme automatique** — comme votre application Xamarin.iOS permettre maintenant être exécutée dans un mode d’écran de fractionnement à une taille spécifiée de l’utilisateur, vous ne pouvez plus compter sur l’appareil, sa taille d’écran ou son orientation à disposition de votre interface utilisateur. Pour vous assurer que votre application s’adapte son interface correctement, vous devez utiliser les Classes de taille et de mise en forme automatique. Pour plus d’informations, consultez notre [Introduction aux Storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) documentation.
- **Utiliser efficacement les ressources** , étant donné que votre application peut désormais partager le système avec une autre application en cours d’exécution, il est essentiel que votre application utilise efficacement les ressources système. Lorsque la mémoire devient éparse, le système s’arrête automatiquement l’application qui consomme le plus de mémoire. Consultez d’Apple [l’efficacité énergétique : Guide pour les applications iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) pour plus d’informations.

Pour en savoir plus sur la préparation de votre application pour le mode fractionné, consultez le site d’Apple [adopter les améliorations de multitâche sur iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentation.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Picture in Picture

La nouvelle image dans la fonctionnalité de l’image (également appelé _PIP_) permet à l’utilisateur à regarder une vidéo dans une petite fenêtre flottante que l’utilisateur peut placer n’importe où sur l’écran au-dessus des autres applications en cours d’exécution.

[![](multitasking-images/about03.png "Un exemple de l’image dans la fenêtre flottante d’image")](multitasking-images/about03.png#lightbox)

Comme avec la diapositive sur et mode fractionné, l’utilisateur a un contrôle total sur regarder une vidéo dans l’image en mode de l’image. Si la fonction principale de votre application consiste à regarder la vidéo, il doit subir des modifications pour se comporter correctement en mode PIP. Sinon, aucune modification est nécessaire pour prendre en charge des PIP.

Pour votre application afficher la vidéo PIP à la demande de l’utilisateur, vous devez être à l’aide _AVKit_ ou _AV Foundation API_. Media Player framework a été supprimée dans iOS 9 et ne prend pas en charge le PIP.

Image dans l’image est disponible uniquement sur un iPad Pro, iPad Air, iPad Air 2, iPad Mini 2, iPad Mini 3 ou iPad Mini 4. Pour plus d’informations, consultez notre [PictureInPicture, exemple d’application](https://developer.xamarin.com/samples/ios/iOS9/) et d’Apple [image dans l’image de démarrage rapide](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentation.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Prise en charge de multitâche dans votre application

Pour n’importe quelle application Xamarin.iOS existante, prenant en charge le multitâche est une tâche transparente tant que votre application suit déjà les guides de conception d’Apple et les meilleures pratiques pour iOS 8. Cela signifie que l’application doit être à l’aide de storyboards avec mise en forme automatique et les Classes de taille pour les dispositions de son Interface utilisateur (voir notre [Introduction aux Storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) pour plus d’informations).

Pour ces applications, peu ou pas de modifications sont nécessaires pour prendre en charge le multitâche et se comporte bien qu’il contient. Si l’interface utilisateur de le de votre application, a été créé à l’aide d’autres méthodes telles que directement de positionnement et dimensionnement des éléments d’interface utilisateur dans C# code ou si elle s’appuie sur les tailles d’écran de périphérique spécifique ou les orientations, il doit modification significative pour prendre en charge d’iOS 9 multitâche correctement.

Pour prendre en charge iOS 9 multitâche sur n’importe quelle nouvelle application Xamarin.iOS, là encore utiliser les storyboards avec mise en forme automatique et les Classes de taille pour toutes les dispositions d’Interface utilisateur de l’application et implémenter les instructions fournies dans les sections suivantes.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Taille de l’écran et les considérations relatives à l’Orientation

Avant d’iOS 9, vous pouvez concevoir votre application contre les orientations et les tailles d’écran de périphérique spécifique. Car une application peut désormais être exécutée dans le panneau des diapositives Out ou en mode d’affichage fractionné, il peut trouver lui-même en cours d’exécution dans soit une classe de la taille horizontale compact ou régulière sur iPad, quelle que soit la taille physique de l’orientation ou de l’écran de l’appareil.

[![](multitasking-images/sizeclasses01.png "Taille de l’écran et les considérations relatives à l’Orientation")](multitasking-images/sizeclasses01.png#lightbox)

Sur un iPad, une application en plein écran comporte les Classes de taille horizontale et verticale régulière. Tous les iPhone, mais l’iPhone 6 Plus et iPhone 6 s en outre, avoir de classes de taille réduite dans les deux sens dans n’importe quelle orientation. L’iPhone 6 Plus et iPhone 6 s Plus en mode paysage disposez d’une classe de taille horizontale normale et une classe de taille verticale Compact (un peu comme un Mini iPad).

Sur des iPad qui prennent en charge de la diapositive sur et mode fractionné, vous pouvez vous retrouver avec les combinaisons suivantes :

| **Orientation** | **Application principale** | **Application secondaire** |
|--- |--- |--- |
| **Portrait** |75 % de l’écran<br />Horizontal Compact<br />Vertical régulière|25 % de l’écran<br />Horizontal Compact<br />Vertical régulière|
| **Paysage** |75 % de l’écran<br />Horizontal régulière<br />Vertical régulière|25 % de l’écran<br />Horizontal Compact<br />Vertical régulière|
| **Paysage** |50 % de l’écran<br />Horizontal Compact<br />Vertical régulière|50 % de l’écran<br />Horizontal Compact<br />Vertical régulière|

Dans l’exemple [MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) application, si elle est exécutée à plein écran sur un iPad en mode paysage, il présente la liste et la vue de détail en même temps :

[![](multitasking-images/sizeclasses03.png "La liste et la vue de détail présentée en même temps")](multitasking-images/sizeclasses03.png#lightbox)

Si la même application est exécutée dans le panneau des diapositives sur, il est disposé en tant qu’une classe de taille horizontale Compact et affiche uniquement la liste :

[![](multitasking-images/sizeclasses04.png "Uniquement dans la liste présentée lors de l’appareil est horizontale")](multitasking-images/sizeclasses04.png#lightbox)

Pour vous assurer que votre application se comporte correctement dans ces situations, vous devez adopter des Collections de caractéristique, ainsi que les Classes de taille et sont conformes à la `IUIContentContainer` et `IUITraitEnvironment` interfaces. Consultez d’Apple [référence de classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) et notre [Introduction aux Storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) guide pour plus d’informations.

En outre, vous ne pouvez plus compter sur les limites d’écran des appareils pour définir la zone visible de l’application, vous devez utiliser à la place des limites de fenêtre de votre application. Étant donné que les limites de fenêtre sont entièrement sous le contrôle de l’utilisateur, il est impossible par programmation les ajuster ou empêcher l’utilisateur de modifier ces limites.

Enfin, votre application doit utiliser un fichier de storyboard à présenter son écran de lancement au lieu d’utiliser un ensemble de **.png** fichiers image et de prendre en charge toutes les orientations de l’interface quatre (Portrait, envers Portrait, paysage gauche et droite du paysage) à prendre en compte pour l’exécution dans le panneau des diapositives sur ou en mode d’affichage fractionné.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Raccourcis clavier de matériel personnalisé

Dans iOS 9 s’exécutant sur un iPad, Apple a prise en charge étendue pour les claviers de matériel. iPad incluent toujours prise en charge de base du clavier externe via Bluetooth et certains claviers fabricants créés clavier incluant des clés spécifiques à iOS codé en dur.

Maintenant, avec iOS 9, les applications peuvent créer leurs propres raccourcis clavier personnalisés. En outre, certains raccourcis clavier de base sont disponibles comme **commande-C** (copie), **commande-X** (Couper), **commande-v** (coller) et **commande-MAJ-H**  (accueil), sans une application qui est spécifiquement écrit y répondre.

**Onglet de commandes** fera apparaître un sélecteur d’application qui permet à l’utilisateur de basculer rapidement entre les applications à partir du clavier, comme le système d’exploitation Mac :

[![](multitasking-images/keyboard01.png "Le sélecteur d’application")](multitasking-images/keyboard01.png#lightbox)

Si une application iOS 9 inclut des raccourcis clavier, l’utilisateur peut maintenir enfoncée le **commande**, **Option** ou **contrôle** clés pour les afficher dans une fenêtre contextuelle :

[![](multitasking-images/keyboard02.png "La fenêtre contextuelle des raccourcis clavier")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Définir des raccourcis clavier personnalisés

Si nous ajoutons le code suivant à un contrôleur d’affichage ou un affichage dans notre application, lorsque cette vue ou un contrôleur est visible, un raccourci clavier personnalisés seront disponible :

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

Tout d’abord, nous remplaçons le `CanBecomeFirstResponder` propriété et retour `true` afin que la vue ou un contrôleur d’affichage peut recevoir des entrées au clavier. 

Ensuite, nous remplaçons le `KeyCommands` propriété et créez un nouveau `UIKeyCommand` pour le **commande-N** frappe au clavier. Quand la séquence de touches est activé, nous appelons le `NewEntry` (méthode) (qui nous exposons à iOS 9 à l’aide du `Export` commande) pour effectuer l’action demandée.

Si cette application est exécutée sur un iPad avec un clavier matériel connecté et l’utilisateur tape **commande-N**, une nouvelle entrée sera être ajoutée à la liste. Si l’utilisateur appuie le **commande** clé, la liste de raccourcis s’affichera :

[![](multitasking-images/keyboard03.png "La fenêtre contextuelle des raccourcis clavier")](multitasking-images/keyboard03.png#lightbox)

Consultez l’exemple [multi-tâches application](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) pour un exemple d’implémentation.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Considérations sur la gestion des ressources

Même pour les applications qui utilisent déjà les guides de conception iOS 8 et les meilleures pratiques, gestion efficace des ressources peut toujours être un problème. Dans iOS 9, les applications n’ont plus une utilisation exclusive de mémoire, processeur ou autres ressources système.

Par conséquent, vous devez ajuster votre application Xamarin.iOS pour utiliser efficacement les ressources système ou qu’il rencontre un arrêt dans les situations de mémoire insuffisante. Cela vaut également d’applications refuser multitâche, depuis une seconde application peut toujours être exécutée dans un panneau diapositive sur ou une image dans la fenêtre image nécessitant des ressources supplémentaires ou à l’origine de la fréquence de rafraîchissement passe en dessous de 60 images par seconde.

Prenez en compte les actions utilisateur suivantes et leurs implications :

- **Saisie de texte dans le panneau des diapositives sur** -même si votre application ne comporte aucune entrée de texte, le clavier système peut désormais être affiché via son interface utilisateur. Par conséquent, l’application peut avoir besoin répondre à afficher les notifications de clavier (par exemple, l’affichage et le masquage du clavier).
- **Une deuxième application en cours d’exécution dans le panneau des diapositives sur** -la nouvelle application est maintenant en cours d’exécution au premier plan et en concurrence avec l’application existante pour les ressources système telles que les cycles de processeur et mémoire.
- **Lecture d’une vidéo dans une fenêtre PIP** - pas uniquement cette fenêtre peut couvrir une partie de l’interface de votre application, mais l’application qui a lancé la vidéo est toujours en cours d’exécution en arrière-plan et consomme des ressources processeur et mémoire.

Pour vous assurer que votre application utilise des ressources efficacement, vous devez procédez comme suit :

- **Profiler l’application avec Instruments** -recherchez les fuites de mémoire, sérieusement l’utilisation du processeur et les zones où l’application peut bloquer le thread principal.
- **Répondre aux méthodes de Transitions d’état** : dans votre **AppDelegate.cs** substitution de fichiers et de réponse à l’état changent les méthodes telles que l’application de saisie ou de revenir de l’arrière-plan. Libère les ressources non requis, tels que des images, de données ou de vues et de contrôleur d’affichage.
- **Tester côte à côte avec les applications gourmandes en mémoire** : exécuter votre application à l’aide de la diapositive Out et mode fractionné sur un matériel physique iOS avec une application gourmandes en mémoire, tels que des cartes (en mode d’affichage par Satellite) et de test que les deux applications restent réactives et se bloque pas.

Consultez d’Apple [l’efficacité énergétique : Guide pour les applications iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) pour plus d’informations sur la gestion des ressources.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Refus du multitâche

Alors que Apple suggère que toutes les applications iOS 9 prend en charge le multitâche, il peut y avoir des raisons très spécifiques pour une application pas trop, comme les jeux ou applications d’appareil photo qui nécessitent le mode plein écran fonctionne correctement.

Pour votre application Xamarin.iOS pour ne pas en cours d’exécution soit une diapositive Out panneau ou en mode d’affichage fractionné, modifier le projet **Info.plist** de fichiers et de vérifier **nécessite un plein écran**:

[![](multitasking-images/fullscreen01.png "Refus du multitâche")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Tandis que le refus du multitâche empêche votre application dans la diapositive Out ou la vue de fractionnement, il n’empêche pas une autre application en cours d’exécution dans la diapositive Out ou une image dans l’image vidéo de s’afficher en même temps que votre application.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>La désactivation de la lecture vidéo PIP

Dans la plupart des cas, votre application doit autoriser l’utilisateur à lire n’importe quel contenu vidéo, qu'il s’affiche dans une image dans la fenêtre flottante d’image. Toutefois, il peut arriver là où cela ne peut pas être souhaitée, tels que les vidéos de scènes coupées jeu.

Pour refuser la lecture vidéo PIP, procédez comme suit dans votre application :

- Si vous utilisez un `AVPlayerViewController` pour afficher la vidéo, définissez le `AllowsPictureInPicturePlayback` propriété `false`.
- Si vous utilisez le `AVPlayerLayer` pour afficher la vidéo, n’instanciez pas un `AVPictureInPictureController`.
- Si vous utilisez un `WKWebView` pour afficher la vidéo, définissez le `AllowsPictureInPictureMediaPlayback` propriété `false`.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a présenté les étapes requises pour vous assurer qu’une application Xamarin.iOS s’exécuter et se comportent correctement dans iOS 9 nouvelle capacité de multitâche pour iPad. En outre, elle couvrait opt-out du mode multitâche pour les applications où il n’est pas une solution parfaitement adaptée.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Plusieurs tâches (exemple)](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [Introduction aux Storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adopter les améliorations de multitâche sur iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Billet de blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
