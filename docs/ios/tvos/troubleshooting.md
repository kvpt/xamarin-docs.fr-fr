---
title: Résolution des problèmes d’applications tvOS générées avec Xamarin
description: Cet article fournit divers conseils pour aider à résoudre les problèmes au cours du développement d’une application tvOS générée avec Xamarin. Il décrit le problème connu et des erreurs spécifiques.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 6830df267aa0b9c4f12fbd53520206ea94fc8a38
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831896"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Résolution des problèmes d’applications tvOS générées avec Xamarin

_Cet article couvre connaître des problèmes que vous pouvez rencontrer lorsque vous travaillez avec prise en charge de tvOS de Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problèmes connus

La version actuelle de prise en charge de tvOS de Xamarin présente les problèmes connus suivants :

- **Framework mono** – Mono 4.3 Cryptography.ProtectedData ne parvient pas à déchiffrer les données à partir de Mono 4.2. Par conséquent, les packages NuGet échoue avec l’erreur `Data unprotection failed` quand une source NuGet protégée est configurée.
    - **Solution de contournement** : dans Visual Studio pour Mac, vous devez ajouter à nouveau ce mot de passe utiliser l’authentification avant d’effectuer une nouvelle tentative restaurer les packages sources de n’importe quel package NuGet.
- **Visual Studio pour Mac avec F# Add-in** : erreur lors de la création un F# modèle Android sur Windows. Cela doit toujours fonctionner correctement sur Mac.
- **Xamarin.Mac** : lors de l’exécution du projet de modèle unifié de Xamarin.Mac avec le Framework cible défini sur `Unsupported`, la fenêtre contextuelle `Could not connect to the debugger` peuvent apparaître.
    - **Solution de contournement potentielle** – rétrograder la version du framework Mono disponible dans notre canal Stable.
- **Xamarin Visual Studio & Xamarin.iOS** : lors du déploiement d’applications WatchKit dans Visual studio, l’erreur `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` peut apparaître.

Veuillez rapport les bogues que vous trouvez sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>Résolution de problèmes

Les sections suivantes répertorient certains problèmes connus qui peuvent se produire lors de l’utilisation de tvOS 9 avec Xamarin.tvOS et la solution à ces problèmes :

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Fichier exécutable non valide - le fichier exécutable ne contient-elle pas de bitcode

Lorsque vous tentez de soumettre une application Xamarin.tvOS vers le Store d’application Apple TV, vous pouvez obtenir un message d’erreur sous la forme _« Exécutable non valide - le fichier exécutable ne contient-elle pas bitcode »_ .

Pour résoudre ce problème, procédez comme suit :

1. Dans Visual Studio pour Mac, cliquez sur votre fichier de projet Xamarin.tvOS dans le **l’Explorateur de solutions** et sélectionnez **Options**.
2. Sélectionnez **Build tvOS** et vérifiez que vous êtes sur le **version** configuration : 

    [![](troubleshooting-images/ts01.png "Sélectionnez les options de génération de tvOS")](troubleshooting-images/ts01.png#lightbox)
3. Ajouter `--bitcode=asmonly` à la **arguments mtouch supplémentaires** champ et cliquez sur le **OK** bouton.
4. Régénérez votre application dans le **version** configuration.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Vérifier que votre application contient Bitcode tvOS

Pour vérifier que votre version de l’application de Xamarin.tvOS contient Bitcode, ouvrez l’application Terminal et entrez les informations suivantes :

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

`addr` et `size` sera différente, mais les autres champs doivent être identiques.

Vous devez vous assurer que tout tiers statique (`.a`) que vous utilisez des bibliothèques ont été développés tvOS bibliothèques (pas les bibliothèques iOS) et qu’il inclut également des informations de bitcode.

Pour les applications ou bibliothèques qui incluent le bitcode valide le `size` sera supérieur à un. Il existe certaines situations où une bibliothèque peut avoir le marqueur bitcode, pas contient déjà bitcode valide. Par exemple :

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

Notez la différence de `size` entre les deux bibliothèques dans l’exemple répertorié s’exécute au-dessus. La bibliothèque doit être générée à partir d’une build d’archivage Xcode avec bitcode activé (paramètre de Xcode `ENABLE_BITCODE`) en tant que solution à ce problème de taille.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Les applications qui contiennent uniquement la tranche arm64 doivent également avoir des « arm64 » dans la liste des UIRequiredDeviceCapabilities dans Info.plist

Lorsque vous soumettez une application vers le Store d’applications TV Apple pour publication, vous pouvez obtenir une erreur dans le formulaire :

_« Applications qui contiennent uniquement la tranche arm64 doivent ont également « arm64 » dans la liste des UIRequiredDeviceCapabilities dans Info.plist »_

Si cela se produit, modifiez votre `Info.plist` de fichier et vérifiez qu’il possède les clés suivantes :

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Recompiler votre application pour la mise en production et soumettez à nouveau à iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Exécution de « MTouch »--échouée de la tâche

Si vous utilisez une bibliothèque tierce 3e (par exemple, MonoGame) et la compilation de votre version a échoué avec une longue série de messages d’erreur se terminant par `Task "MTouch" execution -- FAILED`, essayez d’ajouter `-gcc_flags="-framework OpenAL"` à votre **les arguments supplémentaires touch**:

[![](troubleshooting-images/mtouch01.png "Tâche d’exécution de MTouch")](troubleshooting-images/mtouch01.png#lightbox)

Vous devez également inclure `--bitcode=asmonly` dans le **les arguments supplémentaires touch**, avoir vos options de l’éditeur de liens **lier tout** et effectuer une compilation propre.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Erreur de ITMS-90471. Icône de grande taille est manquant

Si vous obtenez un message sous la forme « erreur ITMS-90471. Icône de grande taille est manquante » lorsque vous tentez de soumettre une application Xamarin.tvOS vers le Store d’application Apple TV pour la mise en production, vérifiez les éléments suivants :

1. Vérifiez que vous avez inclus les ressources de grandes icônes dans votre `Assets.car` fichier que vous avez créé à l’aide de la [icônes d’application](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) documentation.
2. Vous assurer que vous avez inclus le `Assets.car` de fichiers à partir de la [utilisation des icônes et Images](~/ios/tvos/app-fundamentals/icons-images.md) documentation dans votre offre groupée d’application finale.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Bundle non valide : une application qui prend en charge des contrôleurs de jeu doit prennent également en charge l’Apple TV à distance

ou Gestionnaire de configuration 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Bundle non valide – applications Apple TV avec le framework GameController doit inclure la clé GCSupportedGameControllers dans le fichier Info.plist de l’application

Contrôleurs de jeu peuvent être utilisés pour améliorer le jeu et le sentiment d’immersion dans un jeu. Ils peuvent également servir à contrôler l’interface de Apple TV standard afin de l’utilisateur n’a pas de basculer entre l’instance distante et le contrôleur.

Si vous soumettez une application Xamarin.tvOS avec prise en charge du contrôleur de jeu à l’Apple TV App store et vous obtenez un message d’erreur sous la forme de :


_Nous avons découvert un ou plusieurs problèmes avec votre remise récente pour « nom de l’application ». Votre remise a réussi, mais vous pouvez corriger les problèmes suivants dans votre prochaine livraison :_

_Bundle non valide : une application qui prend en charge des contrôleurs de jeu doit également en charge l’Apple TV à distance._

ou Gestionnaire de configuration 

_Bundle non valide – applications Apple TV avec le framework GameController doit inclure la clé GCSupportedGameControllers dans le fichier Info.plist de l’application._

La solution consiste à ajouter la prise en charge pour l’instance distante de Siri (`GCMicroGamepad`) à votre application `Info.plist` fichier. Le profil du contrôleur de jeu Micro a été ajouté par Apple pour cibler le Siri Remote. Par exemple, inclure les clés suivantes :

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
> Les contrôleurs de jeu Bluetooth sont un bon facultatif risquant de rendre les utilisateurs finaux, votre application ne peut pas forcer l’utilisateur à acheter un. Si votre application prend en charge les contrôleurs de jeu il doit également prendre en charge distant Siri afin que le jeu soit utilisable par tous les utilisateurs d’Apple TV.

Pour plus d’informations, consultez notre [utilisation de contrôleurs de jeu](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) section de notre [Siri Remote et Bluetooth contrôleurs](~/ios/tvos/platform/remote-bluetooth.md) documentation.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Framework cible incompatible :. NetPortable, Version = v4.5, profil = Profile78

Lorsque vous tentez d’inclure une bibliothèque de classes Portable (PCL) dans un projet Xamarin.tvOS, vous pouvez obtenir un message pour former :

_Framework cible incompatible :. NetPortable, Version = v4.5, profil = Profile78_

Pour résoudre ce problème, ajoutez un fichier XML appelé `Xamarin.TVOS.xml` avec le contenu suivant :

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Le chemin d’accès suivant :

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Notez que le nombre de profil dans le chemin d’accès doit correspondre au nombre de profil de la bibliothèque PCL.

Avec ce fichier en place, vous devez être en mesure d’ajouter correctement le fichier de bibliothèque de classes portable pour le projet Xamarin.tvOS.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
