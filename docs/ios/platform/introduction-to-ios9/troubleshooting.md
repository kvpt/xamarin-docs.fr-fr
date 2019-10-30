---
title: Xamarin. iOS 9 – résolution des problèmes
description: Cet article fournit différents conseils de dépannage pour l’utilisation d’iOS 9 dans Xamarin. iOS. Des conseils couvrent l’analyse XML, les simulateurs, les contraintes de disposition, les problèmes réseau et beaucoup d’autres sujets.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 437698fcda6e85090cd7bdce90959300436e0bc2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031761"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin. iOS 9 – résolution des problèmes

_Cet article fournit plusieurs conseils de dépannage pour l’utilisation d’iOS 9 dans les applications Xamarin. iOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Un problème est survenu lors de l’analyse du code XML

Le concepteur iOS Xamarin ne prend pas encore en charge les fonctionnalités de Xcode 7. Le chargement des storyboards échoue dans le concepteur avec _« un problème est survenu lors de l’analyse du code XML »_ lors de la tentative d’utilisation de nouveaux éléments du concepteur iOS 9 (Xcode 7) tels que StackView.

la prise en charge du concepteur iOS pour les fonctionnalités de Xcode 7 est ciblée pour la prochaine version de la fonctionnalité cycle 6. La version préliminaire du cycle 6 est actuellement disponible dans le canal alpha et prend en charge les nouvelles fonctionnalités de Xcode 7 de façon limitée.

Solution de contournement partielle pour Visual Studio pour Mac : cliquez avec le bouton droit sur la table de montage séquentiel et choisissez **Ouvrir avec** > **Interface Builder Xcode**.

## <a name="where-are-the-ios-8-simulators"></a>Où se trouvent les simulateurs iOS 8 ?

Si vous avez installé Xcode 7 (ou une version ultérieure), il remplacera automatiquement tous les simulateurs iOS 8 par des simulateurs iOS 9 par défaut. Si vous avez encore besoin d’effectuer un test sur iOS 8, vous pouvez démarrer Xcode, puis télécharger et installer les simulateurs iOS 8.

Dans Xcode, sélectionnez le menu **Xcode** , puis **Préférences...**  > **Downloads (téléchargements**) :

[![](troubleshooting-images/ios8.png "iOS 8 Simulators Downloads")](troubleshooting-images/ios8.png#lightbox)

Cliquez sur le bouton **vérifier et installer maintenant** pour réinstaller les simulateurs iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Contrainte de disposition avec les erreurs d’attribut Left/Right

Dans iOS 8 (et les versions antérieures) **, les éléments** d’interface utilisateur dans les storyboards peuvent utiliser une combinaison des attributs & **gauche** (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) et des attributs de **fin** de **début** & (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) dans le même disposition.

Si le même Storyboard dans s’exécute dans iOS 9, une exception se produit sous la forme suivante :

> Arrêt de l’application en raison d’une exception non interceptée’NSInvalidArgumentException', raison : ' * * * + [NSLayoutConstraint constraintWithItem : attribute : relatedBy : toItem : attribute : multiplicateur : constant :]: une contrainte ne peut pas être effectuée entre un début ou une fin attribut et un attribut right/left. Utilisez le début/la fin pour ou ni l’un ni l’autre.

iOS 9 applique les dispositions pour qu’elles utilisent des attributs de **fin** de **droite** & **gauche** _ou_ de **début** & , mais *pas* les deux. Pour résoudre ce problème, modifiez toutes les contraintes de disposition pour qu’elles utilisent le même jeu d’attributs dans votre fichier de table de montage séquentiel.

Pour plus d’informations, consultez la discussion sur l' [erreur de contrainte iOS 9](https://stackoverflow.com/questions/32692841/ios-9-constraint-error) Stack overflow.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>ERREUR ITMS-90535 : clé CFBundleExecutable inattendue

Après avoir basculé vers iOS 9, à partir d’une application utilise des composants tiers (en particulier notre composant Google Maps existant) qui ont été compilés et exécutés sur iOS 8 (ou version antérieure), lors de la tentative d’envoi de la nouvelle build à iTunes Connect, vous pouvez recevoir une erreur au format suivant :

> ERREUR ITMS-90535 : clé CFBundleExecutable inattendue. Le bundle à « Payload/App-Name. app/Component. Bundle » ne contient pas d’exécutable de bundle...

Ces problèmes peuvent généralement être résolus en recherchant le bundle nommé dans le projet, de même que le message d’erreur suggéré-modifier le `Info.plist` qui se trouve dans le regroupement en supprimant la clé de `CFBundleExecutable`. La clé de `CFBundlePackageType` doit également être définie sur `BNDL`.

Après avoir apporté ces modifications, effectuez un nettoyage et régénérez le projet entier. Vous devez être en mesure de soumettre à iTunes Connect sans problème après avoir apporté ces modifications.

Pour plus d’informations, consultez cette discussion [Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) .

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Échec de CFNetwork SSLHandshake (-9824). erreur

Lorsque vous tentez de vous connecter à Internet, soit directement, soit à partir d’une vue Web dans iOS 9, vous pouvez obtenir une erreur au format suivant :

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

Ou sous la forme :

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

Dans iOS9, la sécurité d’application transport (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et votre application. En outre, ATS requiert la communication à l’aide du protocole `HTTPS` et la communication de l’API de haut niveau à chiffrer à l’aide de TLS version 1,2 avec confidentialité ascendante.

Dans la mesure où ATS est activé par défaut dans les applications conçues pour iOS 9 et OS X 10,11 (El Capitan), toutes les connexions utilisant `NSURLConnection`, `CFURL` ou `NSURLSession` sont soumises aux exigences de sécurité ATS. Si vos connexions ne répondent pas à ces exigences, elles échouent avec une exception.

Pour plus d’informations sur la résolution de ce problème, consultez la section [opt-out de l’ATS](~/ios/app-fundamentals/ats.md) de notre guide de [sécurité de transport d’application](~/ios/app-fundamentals/ats.md) .

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Mes applications existantes ne s’exécutent pas sur iOS 9

Consultez nos [informations de compatibilité iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) pour obtenir des instructions sur la reconstruction et le redéploiement de vos applications existantes pour qu’elles s’exécutent sur iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView a la valeur null dans les constructeurs

**Raison :** Dans iOS 9, le constructeur `initWithFrame:` est désormais requis, en raison des changements de comportement dans iOS 9, comme l' [indique la documentation UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si vous avez inscrit une classe pour l’identificateur spécifié et qu’une nouvelle cellule doit être créée, la cellule est maintenant initialisée en appelant sa méthode `initWithFrame:`.

**Correctif :** Ajoutez le constructeur `initWithFrame:` comme suit :

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemples connexes : [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView ne parvient pas à initialiser avec le codeur lors du chargement d’une vue à partir d’un XIB/d’un bec

**Raison :** Le constructeur `initWithCoder:` est celui appelé lors du chargement d’une vue à partir d’un fichier de XIB Interface Builder. Si ce constructeur n’est pas exporté, le code non managé ne peut pas appeler notre version managée de celui-ci. Précédemment (par exemple, dans iOS 8), le constructeur `IntPtr` a été appelé pour initialiser la vue.

**Correctif :** Créez et exportez le constructeur `initWithCoder:` comme suit :

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemple connexe : [conversation](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Message dyld : aucune image du cache avec le nom...

Vous pouvez rencontrer un incident avec les informations suivantes dans le journal :

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Raison :** Il s’agit d’un bogue dans l’éditeur de liens natif d’Apple, qui se produit quand un Framework privé est public (JavaScriptCore a été rendu public dans iOS 7, avant qu’il ne s’agisse d’une infrastructure privée) et que la cible de déploiement de l’application soit pour une version iOS lorsque l’infrastructure était privée. Dans ce cas, l’éditeur de liens d’Apple crée un lien avec la version privée du Framework au lieu de la version publique.

**Correctif :** Ce problème sera résolu pour iOS 9, mais il existe une solution de contournement simple que vous pouvez vous appliquer en attendant : ciblez une version iOS ultérieure dans votre projet (vous pouvez essayer iOS 7 dans ce cas). D’autres infrastructures peuvent présenter des problèmes similaires, par exemple l’infrastructure WebKit a été rendue publique dans iOS 8 (et, par conséquent, le ciblage d’iOS 7 entraînera cette erreur ; vous devez cibler iOS 8 pour utiliser WebKit dans votre application).

## <a name="untrusted-enterprise-developer"></a>Développeur d’entreprise non fiable

Lorsque vous tentez d’exécuter la version iOS 9 de votre application Xamarin. iOS sur du matériel iOS réel, vous pouvez recevoir un message indiquant que votre compte de développeur n’a pas été approuvé sur l’appareil. Exemple :

[![](troubleshooting-images/untrusted01.png "Untrusted Enterprise Developer alert")](troubleshooting-images/untrusted01.png#lightbox)

Pour résoudre ce problème, procédez comme suit :

1. Démarrez Xcode (la dernière version bêta) sur le Mac de développement.
2. Sélectionnez **appareils** dans le menu **fenêtre** pour ouvrir la fenêtre appareils : 

    [![](troubleshooting-images/untrusted02.png "The Devices Window")](troubleshooting-images/untrusted02.png#lightbox)
3. Dans le panneau latéral **appareils** , sélectionnez votre appareil, cliquez avec le bouton droit et sélectionnez **afficher les profils de provisionnement...** : 

    [![](troubleshooting-images/untrusted03.png "SShow Provisioning Profiles")](troubleshooting-images/untrusted03.png#lightbox)
4. Sélectionnez chaque profil de provisionnement sur l’appareil, puis cliquez sur le bouton **-** pour le supprimer : 

    [![](troubleshooting-images/untrusted04.png "Deleting a provisioning profile")](troubleshooting-images/untrusted04.png#lightbox)
5. Dans le menu **Xcode** , sélectionnez **Preferences...** et **comptes**: 

    [![](troubleshooting-images/untrusted05.png "Xcode account preferences")](troubleshooting-images/untrusted05.png#lightbox)
6. Cliquez sur le bouton **afficher les détails.** .., puis cliquez sur le bouton **Télécharger tout** : 

    [![](troubleshooting-images/untrusted06.png "Download all profiles")](troubleshooting-images/untrusted06.png#lightbox)
7. Une fois la mise à jour de la liste terminée, cliquez sur le bouton **terminé** et fermez la fenêtre Préférences.
8. Supprimez la version existante de l’application Xamarin. iOS que vous essayez de tester à partir de l’appareil iOS.
9. Revenez à Visual Studio pour Mac, effectuez une génération propre et essayez à nouveau d’exécuter l’application sur l’appareil.

Il se peut que vous deviez arrêter et redémarrer Visual Studio pour Mac avant que les nouveaux profils de provisionnement chargés par Xcode ne s’affichent. Vous devrez peut-être également ajuster les options de **signature d’offre groupée iOS** pour votre application Xamarin. iOS afin de sélectionner les nouveaux profils de provisionnement.

## <a name="launch-screen-issues"></a>Problèmes d’écran de lancement

iOS 9 applique désormais les spécifications d’écran de lancement afin que la même image de lancement ne puisse plus être réutilisée pour prendre en charge différentes orientations d’interface. Pour plus d’informations, consultez [référence UILanchImage](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) d’Apple.

Si vous le souhaitez, vous pouvez utiliser un fichier de table de montage séquentiel pour présenter l’écran de lancement de votre application, par opposition à l’utilisation d’un jeu de fichiers image **. png** . Il s’agit à présent de la manière préférée d’Apple pour présenter des écrans de lancement. Pour plus d’informations, consultez notre guide de [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .

Enfin, votre application doit utiliser un fichier de table de montage séquentiel pour son écran de lancement et prendre en charge les quatre orientations d’interface (portrait, portrait vers le haut, paysage gauche et paysage droit) à prendre en compte pour l’exécution dans une diapositive sur un panneau ou en mode fractionné. Pour en savoir plus sur les nouvelles capacités multitâche d’iOS 9, consultez notre guide sur l’emploi [de la multitâche pour iPad](~/ios/platform/multitasking.md) .

## <a name="nsinternalinconsistencyexception-exception"></a>Exception NSInternalInconsistencyException

Lors de la compilation et de l’exécution d’une application Xamarin. iOS existante pour iOS 9, vous pouvez obtenir une erreur au format suivant :

> Exception objective-C levée.  Nom : NSInternalInconsistencyException raison : les fenêtres d’application sont supposées avoir un contrôleur d’affichage racine à la fin du lancement de l’application

Cela est dû au fait que les fenêtres d’application sont supposées avoir un contrôleur d’affichage racine à la fin du lancement de l’application et que votre application existante ne l’est pas.

Il existe au moins deux solutions possibles pour ce problème :

1. Mettez à jour l’application pour utiliser le fichier de Storyboard au lieu de `xib` fichiers pour définir son interface utilisateur. Celui-ci nécessite beaucoup de temps en fonction de la taille de votre application et de la connaissance de l’utilisation du concepteur iOS (ou Interface Builder de Xcode) pour la mise en page des storyboards. Pour plus d’informations, consultez notre [Introduction à la documentation sur les storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .
2. Le programme d’installation `RootViewController` propriété de la fenêtre d’application dans `FinishedLaunching` méthode de `AppDelegate` classe pour pointer vers un contrôleur d’affichage dans l’interface utilisateur de votre application.

## <a name="when-to-initialize-views-and-view-controllers"></a>Quand initialiser des vues et des contrôleurs d’affichage

Avec Xamarin. iOS, il est possible d’effectuer l’initialisation d’une vue ou d’un contrôleur d’affichage à l’intérieur de constructeurs appelés quand un contenu est exposé dans du code managé, mais il interrompt la conception d’iOS.

En général, vous ne devez pas initialiser tout ce qui peut rappeler le code Objective-C à partir du constructeur, car vous ne pouvez pas être sûr de l’appel de. Cela signifie également qu’il existe un meilleur emplacement (autre. ctor) ou des appels à override (car objective-C n’a pas d’événement) où cette initialisation doit être effectuée.

## <a name="related-links"></a>Liens associés

- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Nouveautés d’iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Mise à jour de vos applications Xamarin. iOS vers iOS9 (vidéo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
