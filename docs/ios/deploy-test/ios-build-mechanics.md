---
title: Mécanismes de génération d’iOS
description: Ce guide explique comment planifier des applications et comment utiliser des méthodes permettant d’accélérer les builds pour toutes les configurations de build.
ms.prod: xamarin
ms.assetid: 06FD3940-D666-4C9E-BC3E-BBE481EF8012
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: ec510d46bf1b46cf8fd70c8f4d43b3108f46a010
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284772"
---
# <a name="ios-build-mechanics"></a>Mécanismes de génération d’iOS

_Ce guide explique comment planifier des applications et comment utiliser des méthodes permettant d’accélérer les builds pour toutes les configurations de build._

Développer des applications performantes ne se limite pas à l’écriture d’un code qui fonctionne. Une application bien écrite doit contenir des optimisations qui accomplissent des builds plus rapides avec des applications plus petites et qui s’exécutent plus rapidement. Ces optimisations ne résulteront pas seulement en une meilleure expérience pour l’utilisateur, mais également pour vous ou les développeurs qui travaillent sur le projet. Il est primordial de s’assurer que lors du traitement de votre application tout est mesuré souvent. 

N’oubliez pas que les options par défaut sont fiables et rapides, mais ne sont pas optimales pour chaque situation. En outre, de nombreuses options peuvent ralentir ou accélérer le cycle de développement en fonction de chaque projet. Par exemple, la suppression native prend du temps, mais si la taille acquise est peu importante alors le temps consacré à la suppression ne pourra pas être récupéré via un déploiement plus rapide. En revanche, la suppression native peut réduire l’application de manière significative, auquel cas elle sera plus rapide à déployer. Cela varie entre les projets, et la seule façon de savoir consiste à tester chaque solution.

Les vitesses de génération Xamarin peuvent également être affectées par diverses capacités et fonctionnalités d’un ordinateur susceptibles d’avoir un impact sur les performances : fonctionnalités du processeur, vitesses du bus, quantité de mémoire physique, vitesse du disque, vitesse du réseau. Ces limitations des performances dépassent le cadre de ce document et sont sous la responsabilité du développeur.


## <a name="timing-apps"></a>Minutage des applications

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour activer la sortie MSBuild de diagnostic dans Visual Studio pour Mac :

1. Cliquez sur **Visual Studio pour Mac > Préférences...**
2. Dans l’arborescence à gauche, sélectionnez **Projets > Générer**
3. Dans le volet droit, définissez la liste déroulante Niveau de détail du journal sur **Diagnostic** :  [![](ios-build-mechanics-images/image2.png "Définition du niveau de détail du journal")](ios-build-mechanics-images/image2.png#lightbox)
4. Cliquez sur **OK**.
5. Redémarrer Visual Studio pour Mac
6. Nettoyer et regénérer votre package
7. Afficher la sortie de diagnostic dans le Panneau Erreurs (Afficher > Panneaux > Erreurs) en cliquant sur le bouton Sortie de génération


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour activer la sortie MSBuild de diagnostic dans Visual Studio :

1. Cliquez sur **Outils > Options...**
2. Dans l’arborescence à gauche, sélectionnez **Projets et solutions > Générer et exécuter**
3. Dans le volet droit, définissez la *liste déroulante Niveau de détail de la sortie de génération MSBuild* sur **Diagnostic** :  [![](ios-build-mechanics-images/image2-vs.png "Définition du niveau de détail de la sortie de génération MSBuild")](ios-build-mechanics-images/image2-vs.png#lightbox)
4. Cliquez sur **OK**.
5. Nettoyez et regénérez votre package.
6. La sortie de diagnostic est visible dans le panneau Sortie.

-----

## <a name="timing-mtouch"></a>Minutage Mtouch

Pour afficher des informations spécifiques au processus de génération mtouch, passez `--time --time` aux arguments mtouch dans vos **Options de projet**. Vous trouverez les résultats dans la sortie de génération, en recherchant la tâche `MTouch` :

```csharp
Setup: 36 ms
Resolve References: 982 ms
Extracted native link info: 987 ms
...
Total time: 1554 ms
```

## <a name="connecting-from-visual-studio-with-build-host"></a>Connexion à partir de Visual Studio avec l’hôte de build

Les outils Xamarin fonctionnent techniquement sur n’importe quel Mac pouvant exécuter OS X 10.10 Yosemite ou version ultérieure. Toutefois, les expériences de développement et les durées de génération peuvent être gênées par les performances du Mac.

À l’état déconnecté, Visual Studio pour Windows exécute uniquement la phase de compilation C#. Il n’essaie pas d’effectuer de liaison ou de compilation AOT, d’empaqueter l’application dans un bundle _.app_ , ou de signer le bundle d’applications. (La phase de compilation C# constitue rarement un goulot d’étranglement.) Essayez d’identifier où dans le pipeline la génération ralentit en effectuant la génération directement sur l’hôte de build Mac dans Visual Studio pour Mac.


En outre, la connexion réseau entre l’ordinateur Windows et l’hôte de build Mac constitue l’un des emplacements les plus courants à l’origine de la lenteur. Cela peut être dû à un obstacle physique sur le réseau, à l’utilisation d’une connexion sans fil ou au fait de devoir passer par un ordinateur saturé (par exemple, un service Mac dans le cloud).

## <a name="simulator-tricks"></a>Astuces de simulateur

 
Lorsque du déploiement d’applications mobiles, il est essentiel de déployer rapidement le code. Pour diverses raisons, notamment la vitesse et l’absence d’exigences de provisionnement de l’appareil, les développeurs choisissent souvent de déployer sur un émulateur ou un simulateur préinstallé. Pour les fabricants d’outils de développement, la décision de fournir un simulateur ou un émulateur revient à un compromis entre la vitesse et la compatibilité. 

Apple fournit un simulateur pour le développement iOS, favorisant la vitesse par rapport à la compatibilité en créant un environnement moins restrictif pour l’exécution de code. Cet environnement moins restrictif permet à Xamarin d’utiliser le compilateur JIT pour le simulateur (par opposition à [AOT](~/ios/internals/architecture.md) sur un appareil), ce qui signifie que la build est compilée en code natif au moment de l’exécution. Comme le Mac est beaucoup plus rapide qu’un appareil, ceci permet d’obtenir de meilleures performances.

Le simulateur utilise un lanceur d’applications partagé, ce qui permet au lanceur d’être réutilisé, au lieu d’être généré à chaque fois, comme cela est nécessaire sur l’appareil.

En tenant compte des informations ci-dessus, la liste ci-dessous donne des informations sur les étapes à suivre pour générer et déployer votre application sur le simulateur afin d’obtenir les meilleures performances.
 
### <a name="tips"></a>Conseils

- Pour les builds : 
  - Désélectionnez l’option **Optimiser les images PNG** dans les Options de projet. Cette optimisation n’est pas nécessaire pour les builds sur le simulateur.
  - Réglez l’éditeur de liens sur **Ne pas lier**. La désactivation de l’éditeur de liens permet d’être plus rapide, car son exécution prend beaucoup de temps.
  - La désactivation du lanceur d’applications partagé à l’aide de l’indicateur `--nofastsim` rend les builds du simulateur beaucoup plus lentes. Supprimez cet indicateur lorsqu’il n’est plus nécessaire.
  - L’utilisation de bibliothèques natives est plus lente, car le fichier exécutable principal de simlauncher partagé ne peut pas être réutilisé dans de tels cas et un fichier exécutable spécifique à l’application doit être compilé pour chaque build.
- Pour le déploiement
  - Si possible, faites-en sorte que le simulateur soit toujours en cours d’exécution. Le démarrage à froid du simulateur peut prendre jusqu'à 12 secondes.
- Conseils supplémentaires
  - Favorisez la génération (Build) par rapport à la régénération (Rebuild), car cette dernière exécute un nettoyage avant la génération. Le nettoyage peut prendre un certain temps car il supprime les références qui pourraient être utilisées.
  - Tirez parti du fait que le simulateur n’applique pas le bac à sable (sandbox). La présence de ressources volumineuses comme des vidéos ou d’autres éléments dans votre projet peut créer des opérations de copie de fichiers coûteuses à chaque fois que l’application est lancée dans le simulateur. Évitez de ces opérations coûteuses en plaçant ces fichiers dans le répertoire de base et les référençant dans votre application avec le chemin d’accès complet au fichier.  
  - En cas de doute, utilisez l’indicateur `--time --time` pour mesurer la modification

La capture d’écran ci-dessous illustre comment définir ces options pour le simulateur dans vos options iOS :

[![](ios-build-mechanics-images/image3.png "Définition des options")](ios-build-mechanics-images/image3.png#lightbox)

## <a name="device-tricks"></a>Astuces relatives à l’appareil

Le déploiement sur l’appareil est similaire au déploiement sur le simulateur, car le simulateur est un petit sous-ensemble de la build utilisée pour l’appareil iOS. La génération pour appareil nécessite davantage d’étapes, mais présente l’avantage d’offrir des possibilités supplémentaires d’optimiser votre application.

### <a name="build-configurations"></a>Configurations de build

Il existe plusieurs configurations de build fournies lors du déploiement d’applications iOS. Il est important de bien comprendre chaque configuration, pour savoir quand et pourquoi vous devez optimiser.

- Débogage
  - Il s’agit de la configuration principale qui doit être utilisée lorsqu’une application est en cours de développement et elle doit, par conséquent, être aussi rapide que possible.
- Libérer
  - Les builds de mise en production sont celles qui sont envoyées à vos utilisateurs et il est donc essentiel de se concentrer sur les performances. Lorsque vous utilisez la configuration Mise en production, vous souhaiterez peut-être utiliser le compilateur d’optimisation LLVM et optimiser les fichiers PNG.

 
Il est également important de comprendre la relation entre la génération et le déploiement. La durée de déploiement dépend de la taille de l’application. Une application plus volumineuse prend plus de temps à déployer. En réduisant la taille de l’application, vous pouvez réduire la durée de déploiement.

La réduction de la taille de l’application peut aussi réduire la durée de la génération. En effet, la suppression de code de l’application prend moins de temps que la compilation en mode natif de code qui ne sera pas utilisé. Les fichiers objets plus petits signifient une liaison plus rapide, ce qui crée un fichier exécutable plus petit avec moins de symboles à générer. Par conséquent, l’économie d’espace présente un double avantage, c’est pourquoi **Lier SDK** est la valeur par défaut pour toutes les builds d’appareil. 

> [!NOTE]
> L’option **Lier SDK** peut s’afficher en tant que Lier les SDK Framework uniquement ou Lier les assemblys du SDK uniquement, en fonction de l’IDE qui est utilisé.
 

### <a name="tips"></a>Conseils

- Build : 
  - La génération d’une architecture unique (par exemple, ARM64) est plus rapide qu’une architecture FAT binaire (par exemple, ARMv7 + ARM64)
  - Évitez d’optimiser les fichiers PNG lors du débogage
  - Envisagez de lier tous les assemblys. Optimisez chaque assembly. 
  - Désactivation de la création de symboles de débogage à l’aide de `--dsym=false`. Toutefois, vous devez être conscient que le fait de désactiver cette option signifie que les rapports d’incidents peuvent uniquement générer des symboles de débogage sur l’ordinateur qui a généré l’application et uniquement si l’application n’a pas été supprimée.

 
Voici certaines choses à éviter :

- Fichiers binaires FAT (déboguer) 
- Désactiver l’éditeur de liens `--nolink` 
- Désactiver la suppression 
  - Symboles `--nosymbolstrip` 
  - IL (mise en production) `--nostrip`.  
 
Conseils supplémentaires 

- Comme sur le simulateur, préférez la Build à la Rebuild 
  - Les assemblys AOT (fichiers objets) sont mis en cache 
- Les builds Débogage prennent plus de temps à cause des symboles, de l’exécution de dsymutil, et dans la mesure où elles finissent par être plus volumineuses, le temps supplémentaire nécessaire pour charger sur les appareils. 
- Par défaut, les builds Mise en production effectuent une suppression IL des assemblys. Cela prend peu de temps qui est probablement récupéré lors du déploiement d’une application plus petite sur l’appareil.
- Évitez de déployer des fichiers statiques volumineux avec chaque build (débogage) 
  - Utilisez UIFileSharingEnabled (info.plist) 
    - Les ressources peuvent être chargées une seule fois 
- En cas de doute, utilisez l’indicateur `--time --time` pour mesurer la modification

La capture d’écran ci-dessous illustre comment définir ces options pour le simulateur dans vos options iOS :

[![](ios-build-mechanics-images/image4.png "Définition des options")](ios-build-mechanics-images/image4.png#lightbox)

## <a name="using-the-linker"></a>Utilisation de l’éditeur de liens

Lors de la génération de votre application, mtouch utilise un éditeur de liens pour le code managé, ce qui supprime le code que l’application n’utilise pas. En théorie, cela fournit des builds plus petites et donc, plus rapides. Pour plus d’informations sur l’éditeur de liens, consultez le guide [Liaison sur iOS](~/ios/deploy-test/linker.md).

Lorsque vous utilisez l’éditeur de liens, considérez les points suivants :

- Le fait de sélectionner **Ne pas lier** pour une build Appareil prend plus de temps et génère également une application plus volumineuse. 
  - Apple rejette les applications dont la taille dépasse la limite définie. Selon `MinimumOSVersion`, cette taille peut être de 60 Mo seulement. 
  - Le fichier exécutable natif est inclus. 
  - L’utilisation de Ne pas lier est plus rapide pour les builds de simulateur, car la compilation JIT est utilisée (par opposition à AOT sur un appareil).
- Lier SDK est l’option par défaut.
- L’option Tout lier peut ne pas être sécurisée, en particulier si vous utilisez du code qui n’est pas le vôtre comme NuGets ou Composants. Si vous choisissez de ne pas lier des assemblys, tout le code de ces services est inclus avec votre application, créant potentiellement des applications plus volumineuses. 
  - Toutefois, si vous choisissez **Tout lier** l’application peut se bloquer, en particulier si des composants externes sont utilisés. Ceci est dû au fait que certains composants utilisent la Réflexion sur certains types.
  - La réflexion et l’analyse statique ne fonctionnent pas ensemble. 

Les outils peuvent être réglés pour conserver les choses à l’intérieur de l’application à l’aide de l’attribut [`[Preserve]` ](~/ios/deploy-test/linker.md). 

Si vous n’avez pas accès au code source, ou s’il est généré par un outil et que vous ne souhaitez pas le modifier, il peut toujours être lié via la création d’un fichier XML qui décrit tous les types et membres devant être conservés. Vous pouvez ensuite ajouter l’indicateur `--xml={file.name}.xml` à vos options de projet, qui a traité le code exactement comme si vous utilisiez des attributs.


### <a name="partially-linking-applications"></a>Liaison partielle d’applications 

Il est également possible de lier partiellement des applications, pour aider à optimiser le temps de génération de votre application :

- Utilisez `Link All` et ignorez certains assemblys 
  - Une partie de l’optimisation de la taille d’application est perdue.
  - Aucun accès au code source est requis.
  - Par exemple, `--linkall --linkskip=fieldserviceiOS`.
 
- Utilisez l’option `Link SDK` et utilisez l’attribut `[LinkerSafe]` sur les assemblys dont vous avez besoin 
  - L’accès au code source est requis.
  - Indique au système que l’assembly est sécurisé pour la liaison, et est traité comme s’il s’agissait d’un SDK Xamarin.
 
### <a name="objective-c-bindings"></a>Liaisons Objective-C 

- L’utilisation de l’attribut `[Assembly: LinkerSafe]` sur vos liaisons peut permettre d’économiser en temps et en taille.

- SmartLink 
  - Effectuée du côté natif 
  - Utilisez l’attribut `[LinkWith (SmartLink=true)]`
  - Cela permet à l’éditeur de liens natif d’éliminer le code natif de la bibliothèque avec laquelle vous effectuez la liaison. 
  - Notez que la recherche dynamique de symboles ne fonctionne pas avec ceci. 

## <a name="summary"></a>Récapitulatif

Ce guide a exploré comment minuter une application iOS et les options à prendre en compte qui sont dépendantes de la configuration et des options de la génération du projet. 

<!-----
# Benchmarks

## Layer 1: building again after making modifications, but _without_ cleaning should be faster 
 
The app should build a bit more quickly if you have only made changes to a subset of the libraries and you do not clean the build before re-deploying. 
 
 
 
### Clean build time 
178 seconds 
 
 
### Build again (without cleaning) after making _no changes_ 
12.5 seconds 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
3 trials: 45 seconds, 43 seconds, 43 seconds 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
 
3 trials: 45 seconds, 45 seconds, 45 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
- Sales.Native.Core.IOS.Ext/ServiceInterfaces/AlertDialog/Dialog.cs 
- Sales.Native.Core.Tools.IOS.Ext/BaseViews/BaseNavigationViewController.cs 
- View.Common/Services/DataTransferResult.cs 
 
45 seconds 
 
 
 
 
 
 
## Layer 2: "app thinning" aka "device specific builds" 
 
The idea of "app thinning" is that the IDE will only build the 1 architecture needed for the specific device that you're deploying to (rather than _both_ 32-bit and 64-bit architectures). 
 
As of the latest "Xamarin 4" builds, you can now enable "app thinning" in Visual Studio via the "Project Options -> iOS Build -> Enable device-specific builds" setting. 
 
Or if you prefer you can achieve a similar result by changing the "Project Options -> iOS Build -> Advanced [tab] -> Supported architectures" to select just _one_ architecture (for example ARM64 if you are developing on a 64-bit device). 
 
 
 
(Caveat: I ran the following builds in Visual Studio for Mac on the Mac rather than on the command line.) 
 
### Clean build time without "device specific builds" 
177 seconds 
 
 
 
### Clean build time _with_ "device specific builds"  
2 trials: 106 seconds, 98 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 31 seconds, 31 seconds 
 
 
* * * 
 
 
## Using the same strategy, but explicitly setting "Supported architectures" to select ARM64 _only_ (rather than using "device specific builds") 
 
(These builds were again run on the command line using `xbuild`.) 
 
 
 
### Clean build time with "Supported architectures" set to ARM64 _only_ 
2 trials: 80 seconds, 91 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 26 seconds, 26 seconds 
 
 
 
 
 
[1] Mac system used for testing: MacBookAir5,2 
 
- 2.0 GHz Core i7 (I7-3667U) 
 
2 Cores with hyper-threading 
 
L2 Cache (per Core): 256 KB 
L3 Cache: 4 MB 
 
- Standard MacBook soldered-in solid-state storage 
 
- 8 GB RAM 
---->


## <a name="related-links"></a>Liens associés

- [Billet de blog](https://blog.xamarin.com/xamarin-ios-build-improvements/)
- [Liaison sur iOS](~/ios/deploy-test/linker.md)
- [Configuration de l’éditeur de liens personnalisé](~/cross-platform/deploy-test/linker.md)
