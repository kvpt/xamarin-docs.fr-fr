---
title: Dépannage des applications tvOS générées avec Xamarin
description: Cet article fournit différents conseils pour vous aider à résoudre les problèmes liés au développement d’une application tvOS créée avec Xamarin. Il décrit un problème connu et des erreurs spécifiques.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: db630ba7db1532f24fb6e810653427d1678ad5e2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434649"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Dépannage des applications tvOS générées avec Xamarin

_Cet article aborde les problèmes que vous pouvez rencontrer lors de l’utilisation du support tvOS de Xamarin._

<a name="Known-Issues"></a>

## <a name="known-issues"></a>Problèmes connus

La version actuelle de la prise en charge tvOS de Xamarin présente les problèmes connus suivants :

- **Infrastructure mono** – chiffrement mono 4,3. ProtectedData ne parvient pas à déchiffrer les données de mono 4,2. Par conséquent, les packages NuGet ne pourront pas être restaurés avec l’erreur `Data unprotection failed` quand une source NuGet protégée est configurée.
  - **Solution de contournement** : dans Visual Studio pour Mac vous devrez rajouter toutes les sources de package NuGet qui utilisent l’authentification par mot de passe avant de retenter la restauration des packages.
- **Visual Studio pour Mac w/F # Add-in** : erreur lors de la création d’un modèle Android F # sur Windows. Cela doit continuer à fonctionner correctement sur Mac.
- **Xamarin. Mac** : lors de l’exécution du projet de modèle unifié Xamarin. Mac avec la version cible du .NET Framework définie sur `Unsupported` , la fenêtre contextuelle `Could not connect to the debugger` peut s’afficher.
  - **Solution de contournement potentielle** : rétrogradez la version du Framework mono disponible dans notre canal stable.
- **Xamarin Visual studio & Xamarin. iOS** : lors du déploiement d’applications WatchKit dans Visual Studio, l’erreur `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` peut s’afficher.

Signalez les bogues que vous trouverez sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>Résolution des problèmes

Les sections suivantes répertorient certains problèmes connus qui peuvent se produire lors de l’utilisation de tvOS 9 avec Xamarin. tvOS et la solution à ces problèmes :

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Exécutable non valide : l’exécutable ne contient pas de bitcode

Lors de la tentative d’envoi d’une application Xamarin. tvOS à Apple TV App Store, un message d’erreur peut s’afficher sous la forme _« exécutable non valide : l’exécutable ne contient pas de bitcode »_.

Pour résoudre ce problème, procédez comme suit :

1. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur votre fichier projet Xamarin. tvOS dans le **Explorateur de solutions** et sélectionnez **options**.
2. Sélectionnez **TvOS Build** et assurez-vous que vous êtes dans la configuration **Release** : 

    [![Sélectionner les options de build tvOS](troubleshooting-images/ts01.png)](troubleshooting-images/ts01.png#lightbox)
3. Ajoutez `--bitcode=asmonly` le champ **arguments mTouch supplémentaires** , puis cliquez sur le bouton **OK** .
4. Régénérez votre application dans la configuration **Release** .

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Vérification que votre application tvOS contient Bitcode

Pour vérifier que votre build d’application Xamarin. tvOS contient Bitcode, ouvrez l’application Terminal et entrez ce qui suit :

```csharp
otool -l /path/to/your/tv.app/tv
```

Dans la sortie, recherchez les éléments suivants :

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` et `size` seront différents, mais d’autres champs doivent être identiques.

Vous devez vous assurer que toutes les bibliothèques statiques () tierces `.a` que vous utilisez ont été créées sur des bibliothèques tvOS (et non des bibliothèques IOS) et qu’elles incluent également des informations bitcode.

Pour les applications ou les bibliothèques qui incluent des bitcode valides `size` , la valeur sera supérieure à un. Dans certaines situations, une bibliothèque peut avoir le marqueur bitcode, mais ne contient pas de bitcode valide. Par exemple :

**Bitcode non valide**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**Bitcode valide** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Notez que la différence `size` entre les deux bibliothèques de l’exemple indiqué ci-dessus est exécutée. La bibliothèque doit être générée à partir d’une build d’archivage Xcode avec bitcode activé (paramètre Xcode `ENABLE_BITCODE` ) comme solution à ce problème de taille.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Les applications qui contiennent uniquement la tranche arm64 doivent également avoir « arm64 » dans la liste des UIRequiredDeviceCapabilities dans info. plist

Lors de l’envoi d’une application à l’App Store d’Apple TV pour la publication, vous pouvez obtenir une erreur au format suivant :

_« Les applications qui contiennent uniquement la tranche arm64 doivent également avoir «arm64 » dans la liste des UIRequiredDeviceCapabilities dans info. plist»_

Si cela se produit, modifiez votre `Info.plist` fichier et assurez-vous qu’il possède les clés suivantes :

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
  <string>arm64</string>
</array>
```

Recompilez votre application pour qu’elle soit publiée et renvoyez-la à iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Exécution de la tâche « MTouch »--échec

Si vous utilisez une bibliothèque tierce (telle que l’utilisation de plusieurs jeux) et que la compilation de la version a échoué avec une longue série de messages d’erreur se terminant par `Task "MTouch" execution -- FAILED` , essayez `-gcc_flags="-framework OpenAL"` d’ajouter à vos **arguments tactiles supplémentaires**:

[![Exécution de la tâche MTouch](troubleshooting-images/mtouch01.png)](troubleshooting-images/mtouch01.png#lightbox)

Vous devez également inclure `--bitcode=asmonly` dans les **arguments tactiles supplémentaires**, faire en sorte que vos options de l’éditeur de liens aient la valeur **lier tout** et effectuer une compilation propre.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Erreur ITMS-90471. L’icône de grande taille est manquante

Si vous recevez un message au format «erreur ITMS-90471. La grande icône est manquante «lors de la tentative d’envoi d’une application Xamarin. tvOS à l’App Store d’Apple TV pour la version finale, veuillez vérifier les points suivants :

1. Vérifiez que vous avez inclus les ressources d’icône volumineuses dans votre `Assets.car` fichier que vous avez créé à l’aide de la documentation des [icônes](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) de l’application.
2. Veillez à inclure le `Assets.car` fichier dans la documentation [utilisation des icônes et des images](~/ios/tvos/app-fundamentals/icons-images.md) dans votre ensemble d’applications final.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Bundle non valide : une application qui prend en charge les contrôleurs de jeu doit également prendre en charge l’Apple TV à distance

ou 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Bundle non valide : les applications Apple TV avec l’infrastructure GameController doivent inclure la clé GCSupportedGameControllers dans le fichier info. plist de l’application

Les contrôleurs de jeu peuvent être utilisés pour améliorer le jeu et donner une idée de l’immersion dans un jeu. Elles peuvent également être utilisées pour contrôler l’interface standard Apple TV afin que l’utilisateur n’ait pas à basculer entre le contrôleur distant et le contrôleur.

Si vous soumettez une application Xamarin. tvOS avec prise en charge du contrôleur de jeu à Apple TV App Store et que vous recevez un message d’erreur sous la forme :

_Nous avons découvert un ou plusieurs problèmes liés à la livraison récente de « nom de l’application ». Votre remise a réussi, mais vous souhaiterez peut-être corriger les problèmes suivants dans votre prochaine livraison :_

_Bundle non valide : une application qui prend en charge les contrôleurs de jeu doit également prendre en charge la télécommande Apple TV._

ou 

_Bundle non valide : les applications Apple TV avec l’infrastructure GameController doivent inclure la clé GCSupportedGameControllers dans le fichier info. plist de l’application._

La solution consiste à ajouter la prise en charge de Siri Remote ( `GCMicroGamepad` ) au fichier de votre application `Info.plist` . Le profil de contrôleur de jeu a été ajouté par Apple pour cibler le Siri distant. Par exemple, incluez les clés suivantes :

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> Les contrôleurs de jeu Bluetooth sont un achat facultatif que les utilisateurs finaux peuvent effectuer, votre application ne peut pas forcer l’utilisateur à en acheter un. Si votre application prend en charge les contrôleurs de jeu, elle doit également prendre en charge la Siri distante afin que le jeu soit utilisable par tous les utilisateurs d’Apple TV.

Pour plus d’informations, consultez notre section [utilisation des contrôleurs de jeu](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) de notre documentation sur [les contrôleurs distants et Bluetooth Siri](~/ios/tvos/platform/remote-bluetooth.md) .

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Framework cible incompatible :. Netportable, version = v 4.5, Profile = Profil78

Lorsque vous tentez d’inclure une bibliothèque de classes portable (PCL) dans un projet Xamarin. tvOS, vous pouvez obtenir un message de la forme :

_Framework cible incompatible :. Netportable, version = v 4.5, Profile = Profil78_

Pour résoudre ce problème, ajoutez un fichier XML appelé `Xamarin.TVOS.xml` avec le contenu suivant :

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Au chemin d’accès suivant :

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Notez que le numéro de profil dans le chemin d’accès doit correspondre au numéro de profil de la bibliothèque de classes portable.

Ce fichier étant en place, vous devez être en mesure d’ajouter le fichier PCL au projet Xamarin. tvOS.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)