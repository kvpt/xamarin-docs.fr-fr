---
title: Conseils de dépannage pour Xamarin.iOS
description: Ce document fournit différents conseils utiles pour la résolution des problèmes pendant le développement d’applications Xamarin.iOS. Il décrit les messages d’erreur spécifiques ainsi que d’autres problèmes potentiels.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2018
ms.openlocfilehash: 38c0ece3e8f0361f3c891713e53b033351512f94
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829922"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Conseils de dépannage pour Xamarin.iOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS ne peut pas résoudre System.ValueTuple

Cette erreur se produit en raison d’une incompatibilité avec Visual Studio.

- **Visual Studio 2017 Update 1** (version 15.1 ou une version antérieure) est compatible uniquement avec **System.ValueTuple NuGet 4.3.0** (ou une version antérieure).

- **Visual Studio 2017 Update 2** (version 15.2 ou une version ultérieure) est compatible uniquement avec les **System.ValueTuple NuGet 4.3.1** ou une version ultérieure.

Choisissez le package System.ValueTuple NuGet correct qui correspond à votre installation de Visual Studio 2017.


## <a name="receiving-error-retrieving-update-information-error-message"></a>Réception de Message d’erreur « Erreur de récupération des informations de mise à jour »

Lorsque vous tentez de mettre à jour le logiciel et ce message d’erreur s’affiche, essayez de redémarrer votre IDE et la déconnexion, puis dans votre compte dans l’IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Comment créer des prises de courant ou des actions avec Interface Builder ?

Avec l’Introduction du Concepteur de Xamarin pour iOS dans Visual Studio pour Mac et Visual studio, les développeurs de Xamarin.iOS peuvent désormais tirer parti de la création d’une interface utilisateur par le biais des Storyboards et .xibs. Reportez-vous à la [Hello, iOS](~/ios/get-started/hello-ios/index.md) guides pour plus d’informations sur l’utilisation du concepteur.

Vous pouvez également faire référence à d’Apple [Outlet](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) et [Actions](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) guides pour plus d’informations sur l’utilisation des Outlets et Actions dans IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding lève l’exception NotSupportedException

Vous utilisez peut-être un encodage n’est pas ajouté par défaut. Vérifier le [internationalisation](~/ios/app-fundamentals/localization/index.md) page pour savoir comment ajouter la prise en charge pour l’encodage plus.

## <a name="systemmissingmethodexception-anything-else"></a>System.MissingMethodException (quoi)

Le membre a été probablement supprimé par l’éditeur de liens et par conséquent n’existe pas dans l’assembly lors de l’exécution.  Il existe plusieurs solutions à cela :

- Ajouter le [ `[Preserve]` ](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) au membre d’attribut.  Cela empêchera l’éditeur de liens de le supprimer.
- Lors de l’appel [ **mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29), utilisez le **- nolink** ou **- linksdkonly** options :
  - **-nolink** désactive toutes les liaisons.
  - **-linksdkonly** sera uniquement lier des assemblys Xamarin.iOS fourni, tel que **xamarin.ios.dll**, tout en conservant tous les types dans les assemblys créés par l’utilisateur (ie. vos projets d’application).

Notez que les assemblys sont liées afin que le fichier exécutable obtenu est plus petit ; Par conséquent, la désactivation de la liaison peut entraîner un fichier exécutable plus volumineux qu’est souhaitable.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Vous obtenez une ModelNotImplementedException

Si vous obtenez cette exception, cela signifie que vous appelez base. Méthode () sur une classe qui remplace un modèle. Vous n’avez pas besoin d’appeler la méthode de base dans une classe pour les modèles (il s’agit des classes qui sont marqués avec l’attribut [modèle]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Cette classe n’est pas un codage compatible avec la clé XXXX de valeur de clé

Si vous obtenez cette erreur lors du chargement d’un fichier NIB cela signifie que la valeur que XXXX est introuvable sur votre classe managée. Cela signifie qu’il manque une déclaration, comme ceci :

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

La définition ci-dessus est générée automatiquement par Visual Studio pour Mac pour tous les fichiers XIB que vous ajoutez à Visual Studio pour Mac dans le `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` fichier.

En outre, les types contenant le code ci-dessus doivent être une sous-classe de [NSObject](xref:Foundation.NSObject).  Si le type conteneur est dans un espace de noms, il doit également avoir un [[inscrire]](xref:Foundation.RegisterAttribute) attribut qui fournit un nom de type sans espace de noms (comme Interface Builder ne prend pas en charge les espaces de noms de types) :

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe inconnue XXXX dans le fichier d’Interface Builder

Cette erreur est générée si vous définissez une classe dans vos fichiers de générateur d’interface, mais vous ne fournissez pas de l’implémentation réelle pour lui dans votre C# code.

Vous devez ajouter du code comme suit :

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException: Aucun constructeur trouvé pour Foo.Bar::ctor(System.IntPtr)

Cette erreur se produit lors de l’exécution lorsque le code tente d’instancier une instance des classes que vous avez référencé à partir de votre fichier Interface Builder. Cela signifie que vous avez oublié d’ajouter un constructeur qui accepte un IntPtr unique en tant que paramètre.

Le constructeur avec un handle IntPtr est utilisé pour lier des objets gérés avec leurs représentations non managées.

Pour résoudre ce problème, ajoutez la ligne de code suivante à la classe Foo.Bar :

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Le type {Foo} ne contient pas une définition pour `GetNativeField` et aucune méthode d’extension `GetNativeField` de type {Foo} est introuvable.

Si vous obtenez cette erreur dans les fichiers générés concepteurs (*. xib.designer.cs), cela signifie deux choses :

 **(1) manquant de la classe partielle ou la classe de base**

Les classes partielles généré par le concepteur doivent avoir des classes partielles correspondantes dans le code utilisateur qui héritent d’une sous-classe de `NSObject`, souvent `UIViewController`. Assurez-vous que vous disposez d’une telle classe pour le type qui est et affiche l’erreur.

 **(2) espaces de noms par défaut modifié**

Les fichiers de concepteur sont générés à l’aide des paramètres d’espace de noms de votre projet par défaut. Si vous avez modifié ces paramètres, ou de renommer le projet, les classes partielles générées peuvent ne plus être dans le même espace de noms que leurs équivalents de code utilisateur.

Namespace paramètres se trouvent dans la boîte de dialogue Options du projet. L’espace de noms par défaut se trouve dans le **général -> Paramètres principaux** section. Si elle est vide, le nom de votre projet est utilisé en tant que la valeur par défaut. Vous trouverez des paramètres d’espace de noms plus avancés dans le **Code Source -> stratégies de noms .NET** section.

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Avertissement pour les actions : La méthode privée « Foo » n’est jamais utilisée. (CS0169)

Actions pour les fichiers de générateur d’interface sont connectées pour les widgets par réflexion lors de l’exécution, cet avertissement est attendu.

Vous pouvez utiliser « #pragma warning désactiver 0169 » « #pragma warning activer 0169" autour de vos actions si vous souhaitez supprimer cet avertissement uniquement pour ces méthodes, ou ajouter 0169 au champ « Ignore les avertissements » dans les options du compilateur si vous souhaitez la désactiver pour votre projet entier (not recommandé).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch a échoué avec le message suivant : Impossible d’ouvrir l’assembly ' / path/to/yourproject.exe'

Si vous voyez ce message d’erreur, généralement le problème est que le chemin d’accès absolu à votre projet contient un espace. Ce problème sera résolu dans une future version de Xamarin.iOS, mais vous pouvez contourner le problème en déplaçant le projet vers un dossier sans espaces.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Votre version sqlite3 est ancienne - Veuillez mettre à niveau vers au moins v3.5.0 !

Cela se produit lorsque vous effectuez toutes les conditions suivantes :

1.  Utiliser Mono.Data.Sqlite
1.  Utilisez Mac OS X Leopard (10.5)
1.  Exécuter votre application dans le simulateur.


Le problème est que Mono est sélectionner le système d’exploitation X `libsqlite3.dylib`, de pas l’iPhoneSimulator `libsqlite3.dylib` fichier. Votre application *sera* Professionnel sur l’appareil, mais pas seulement votre simulateur.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Déployer à l’échec de l’appareil avec System.Exception : AMDeviceInstallApplication retourné 3892346901

Cette erreur signifie que la configuration de signature de code pour votre id de certificat/bundle ne correspond pas au profil de provisionnement installé sur votre appareil.  Vérifiez que le certificat approprié est sélectionné dans les Options du projet -> signature du Bundle d’iPhone et l’id de bundle correctes spécifiée dans les Options de projet -> iPhone Application

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Exécution de code ne fonctionne pas dans Visual Studio pour Mac

Vérifiez que vous utilisez la dernière version de Visual Studio pour Mac et Xamarin.iOS

Si le problème est toujours présent, veuillez [signaler un bogue](http://monodevelop.com/Developers#Reporting_Bugs), attachement le **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{horodatage} .log**, et **composants-{horodatage} .log** des fichiers journaux.

Si tout le reste échoue, vous pouvez essayer de supprimer le cache de saisie semi-automatique de code afin qu’il est régénéré :

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Veillez à ce que vous tapez cette commande correctement ou vous pouvez supprimer accidentellement des fichiers importants.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio pour Mac se bloque lorsque vous copiez du texte

Les utilitaires Mac populaires QuickSilver, barre d’outils Google et barre de lancement ont des fonctionnalités de Presse-papiers qui endommagé Visual Studio pour la mémoire du Mac. Dans leurs options, vous pouvez répertorier Visual Studio pour Mac en tant que processus qu’ils ne doivent pas interférer avec.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio pour Mac plaint 2.4 Mono requis

Si la mise à jour de Visual Studio pour Mac en raison d’une mise à jour récente, et lorsque vous tentez de le démarrer à nouveau il plaint 2.4 Mono n’est pas présent, il vous suffit est [mettre à niveau votre installation de Mono 2.4](http://www.go-mono.com/mono-downloads/download.html).  

Mono 2.4.2.3_6 résout certains problèmes importants qui ont empêché de Visual Studio pour Mac de l’exécution de façon fiable, parfois suspendu Visual Studio pour Mac au démarrage ou a empêché la base de données de saisie semi-automatique de code en cours de génération.

Une fois que vous installez le nouveau Mono, Visual Studio pour Mac démarre comme prévu.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Assertion au... /.. /.. /.. /mono/Metadata/Generic-Sharing.c:704, condition 'oti' non satisfaite

Si vous recevez la trace de pile suivant :

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Cela signifie que vous liez une bibliothèque statique compilée avec le code thumb dans votre projet. À compter de la version SDK iPhone 3.1 (ou version ultérieure au moment de la rédaction) Apple a introduit un bogue dans son éditeur de liens lors de la liaison du code non Thumb (Xamarin.iOS) avec le code Thumb (votre bibliothèque statique). Vous devrez effectuer une liaison avec une version non Thumb de votre bibliothèque statique pour atténuer ce problème.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException: Tentative de JIT compile (méthode) (un wrapper managé au code managé) Foo[]:System.Collections.Generic.ICollection'1.get_Count ()

Le suffixe [] indique que vous ou la bibliothèque de classes appelle une méthode sur un tableau dans une collection générique, tel que IEnumerable <>, ICollection <> ou <> de IList. Pour résoudre ce problème, vous pouvez forcer explicitement le compilateur AOT pour inclure la méthode de ce type en appelant la méthode vous-même, et en vérifiant que ce code est exécuté avant l’appel qui a déclenché l’exception. Dans ce cas, vous pouvez écrire :

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Ce qui force le compilateur AOT pour inclure la méthode get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio pour l’éditeur de source Mac est extrêmement lente

Parfois, l’éditeur Visual Studio pour Mac source devient extrêmement lent, semble se bloquer pendant plusieurs secondes entre la saisie de caractères.

Ce problème est très rare et extrêmement difficiles à reproduire - il généralement ne peut pas être reproduit sur l’ordinateur même après le redémarrage de Visual Studio pour Mac. C’est pourquoi nous vous prions si vous pouvez effectuer plusieurs étapes de débogage avant de redémarrer Visual Studio pour Mac et nous envoyer les résultats.

1.  Essayez de fermer l’onglet Éditeur et la réouverture. Faut-il un peu de modification ou de déplacer le signe insertion jusqu'à ce que le ralentissement se produit à nouveau ?
1.  Désactiver les « Faisceau Sync » à l’aide de l’outil de développement « Quartz Debug » (que vous pouvez trouver à l’aide de Spotlight) et vérifiez si les performances de l’éditeur de source sont restaurée à la normale.
1.  Répétez l’étape (1) avec la synchronisation de faisceau reste désactivé.
1.  Si l’éditeur se bloque pendant plus de quelques secondes, essayez d’exécuter « killall-quitter Visual Studio pour Mac] » dans un terminal pendant qu’il ne répond pas. Il peut être difficile à l’heure de la commande kill alors que l’éditeur ne répond pas, mais il est essentiel de le faire, car la commande force Mono à écrire des traces de pile de tous les threads dans le journal de MD, que nous pouvons utiliser pour découvrir quel état les threads sont dans tandis que le XS ne répond pas.



Veuillez joindre les journaux XS, **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{horodatage} .log**, et **composants-{horodatage} .log**(dans les versions antérieures de XS/MonoDevelop, envoyez simplement **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**).

 **REMARQUE : Le problème ci-dessus a été résolu dans XS 2.2 finale**

## <a name="compiled-application-is-very-large"></a>Application compilée est très volumineux

Pour prendre en charge le débogage, les versions debug contiennent du code supplémentaire. Projets créés en mode version finale sont une fraction de la taille.

Depuis Xamarin.iOS 1.3 le débogage builds inclus débogage prise en charge pour chaque composant unique de Mono (chaque méthode dans chaque classe des frameworks).  

Avec Xamarin.iOS 1.4, nous allons présenter une méthode plus précise précie pour le débogage, la valeur par défaut sera uniquement fournir l’instrumentation de débogage pour votre code et vos bibliothèques, et pas le faire pour tous les [assemblys Mono](~/cross-platform/internals/available-assemblies.md) (cela sera toujours est possible, mais vous devrez participer à ces assemblys de débogage).

## <a name="installation-hangs"></a>Blocages de l’installation

Programmes d’installation de Mono et Xamarin.iOS se bloquer si vous disposez de l’exécution du simulateur iPhone. Ce problème n’est pas limité à Mono ou Xamarin.iOS, il s’agit d’un problème persistent entre n’importe quel logiciel qui tente d’installer logiciel sur MacOS Snow Leopard si l’iPhone simulateur est en cours d’exécution au moment de l’installation.

Assurez-vous que vous quittez le simulateur iPhone et réessayez l’installation.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>A manqué de trampolines de type 0

Si vous obtenez ce message lors de l’exécution d’appareil, vous pouvez créer plus, tapez 0 trampolines (de type spécifique) en modifiant votre section de « iPhone Build » des options de projet.  Vous souhaitez ajouter des cibles de génération des arguments supplémentaires pour l’appareil :

 `-aot "ntrampolines=2048"`

Le nombre de trampolines par défaut est 1024.  Essayez d’augmenter ce nombre jusqu'à ce que vous devez en configurer suffisamment pour votre application.

## <a name="ran-out-of-trampolines-of-type-1"></a>A manqué de trampolines de type 1

Si vous apportez une utilisation intensive des génériques de récursive, vous pouvez obtenir ce message sur l’appareil.  Vous pouvez créer plusieurs type trampolines 1 (type RGCTX) en modifiant votre section de « iPhone Build » des options de projet.  Vous souhaitez ajouter des cibles de génération des arguments supplémentaires pour l’appareil :

 `-aot "nrgctx-trampolines=2048"`

Le nombre de trampolines par défaut est 1024.  Essayez d’augmenter ce nombre jusqu'à ce que vous disposez de suffisamment de votre utilisation de génériques.

## <a name="ran-out-of-trampolines-of-type-2"></a>A manqué de trampolines de type 2

Si vous apportez à utilisation intensive des interfaces, vous pouvez obtenir ce message sur l’appareil.
Vous pouvez créer plusieurs type 2 trampolines (type IMT Thunks) en modifiant votre section de « iPhone Build » des options de projet.  Vous souhaitez ajouter des cibles de génération des arguments supplémentaires pour l’appareil :

 `-aot "nimt-trampolines=512"`

Le nombre par défaut de trampolines de IMT Thunk est 128.  Essayez d’augmenter ce nombre jusqu'à ce que vous disposez de suffisamment de votre utilisation d’interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Débogueur est impossible de se connecter avec l’appareil

Lorsque vous démarrez le débogage d’une configuration de l’appareil, vous verrez le débogueur affiche une boîte de dialogue indiquant qu’il essaie de se connecter à l’application. Il existe plusieurs raisons, que le débogueur ne peut pas être en mesure de se connecter à l’application, selon le mode que vous utilisez pour vous connecter (USB ou Wi-Fi).

 **Si l’appareil et l’hôte du débogueur se trouvent sur différents réseaux**, un pare-feu ou un réseau privé empêchent l’application de se connecter à l’hôte du débogueur en mode de Wi-Fi.

 **Visual Studio pour Mac ne peut pas être en mesure de rechercher l’adresse IP correcte de l’hôte**. Dans le Wi-Fi mode Visual Studio pour Mac donne à l’application toutes les adresses IP qu’il peut trouver de l’hôte et l’application tente de toutes les pour voir si elle peut utiliser un d’eux pour se connecter à Visual Studio pour Mac.

 **Un autre appareil est connecté à un port USB sur l’ordinateur hôte.** Dans certains cas autres périphériques connectés au port USB ports sur l’ordinateur hôte sont connus d’une certaine manière interfère avec le débogage en mode USB.

Si le mode Wi-Fi ou USB ne fonctionne pas, vous pouvez essayer facilement l’autre : dans Visual Studio pour Mac, ouvrez les préférences, accédez à la page du débogueur de débogueur/préférences/iPhone et activer/désactiver la case à cocher « Debug des appareils iOS via le Wi-Fi au lieu d’USB ».   Si aucune ne fonctionne, vous pouvez voir plus d’informations sur l’échec dans la console de l’appareil en mode détaillé (qui est activé en ajoutant «-v - v - v » aux arguments mtouch supplémentaires dans les options du projet).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Erreur 134 : mtouch a échoué avec le message suivant :

Cette erreur peut se produire si vous essayez de générer avec - nolink sur le style de Xamarin.iOS 1.4 des versions. Vous pouvez contourner cette erreur en spécifiant des Arguments supplémentaires dans votre configuration de projet monodevelop.

Ajoutez l’argument

 `-nosymbolstrip`

et le problème doit être résolu.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Identité de distribution ne figure pas dans Visual Studio pour le projet Mac options de signature

Visual Studio pour Mac 2.2 comporte un bogue qui provoque son ne pas détecter les certificats de distribution qui contiennent une virgule. Mettez à jour pour Visual Studio pour Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Erreur « AFCFileRefWrite retourné : 1 » lors du chargement

Lors du téléchargement d’une application sur votre appareil, vous pouvez recevoir une erreur « AFCFileRefWrite retourné : 1". Cela peut se produire si vous avez un fichier de longueur nulle.

## <a name="error-mtouch-failed-with-no-output"></a>Erreur « échoué sans aucune sortie de mtouch »

La version actuelle de Xamarin.iOS et Visual Studio pour Mac échoue lorsque le nom du projet ou le répertoire où sont stockés la solution ou le projet contient des espaces.
Pour résoudre ce problème :


-  Assurez-vous que votre projet, ni le répertoire où il est stocké contient un espace.
-  Dans votre projet « Principaux paramètres » Assurez-vous que le nom du projet ne contient pas d’espaces.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Erreur « le fichier binaire que vous avez téléchargé n’était pas valide. Une version bêta de la version préliminaire du kit SDK a été utilisée pour générer l’application »

Cette erreur est généralement dû à un projet qui a été démarré dans le développement de l’iPad avant la parution de Xamarin.iOS 2.0.0, vous avez probablement certaines clés dans votre fichier Info.plist telles que :

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Cette paire de clés doit être supprimé, car Visual Studio pour Mac prend en charge pour vous automatiquement.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Erreur « une version bêta de la version préliminaire du kit SDK a été utilisée pour générer l’application »

(Envoyé par Ed Anuff)

Procédez comme suit :

-  Modification de la version SDK dans iPhone Build 3.2 ou iTunes connecter rejette lors du chargement, car il voit une application compatible iPad créée à l’aide d’une version du Kit de développement logiciel inférieur à 3.2
-  Créer un fichier Info.plist personnalisé pour le projet et définir explicitement MinimumOSVersion 3.0 qu’il contient.   Cela remplacera la valeur de MinimumOSVersion 3.2 définie par Xamarin.iOS.   Si vous ne le faites pas, il se peut que l’application ne sera pas en mesure d’exécuter sur un iPhone.
-  Reconstruction, zip et du téléchargement iTunes se connectent.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Exception non prise en charge : System.Exception : Impossible de trouver le sélecteur someSelector : sur le {type}

Cette exception est provoquée par une des trois choses :


1.  Vous avez fourni un sélecteur pour le runtime Objective-C sans appliquer l’attribut [Exporter] correspondant à une méthode
1.  Vous avez activé la liaison complète et ne pas appliqué l’attribut [Preserve] à la méthode de [Export] ed.
1.  Vous avez appliqué l’attribut [Export] à une méthode privée dans un type hérité.


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>Fichier de MainWindow.xib.designer.cs n’est pas mis à jour.

Il a été un bogue dans Xamarin Studio 2.4 entraîné ne pas le fichier MainWindow.xib avec le fichier MainWindow.xib.designer dans les nouveaux projets de groupe. Cela signifiait qu’il ne mettrait pas à jour le code du concepteur pour que le fichier.

Ce problème est résolu dans la version de Visual Studio pour Mac est disponible dans sa mise à jour intégrée, donc Vérifiez que vous utilisez une version plus récente.

Vous pouvez corriger des projets existants en supprimant (ne pas la suppression) le xib et son fichier de concepteur, puis rajouter. Cela doit ré-regrouper les fichiers correctement.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView ou UIActionSheet disparaître après avoir été créés

Si vous avez du code comme suit :

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

l’objet « actionSheet » fonctionne comme une variable temporaire dans la fonction et dès que la fonction se termine, l’objet est éligible pour le garbage collection, donc elle finit par le garbage collecté.

Pour résoudre ce problème, vous devez conserver une référence à « actionSheet » en dehors de votre méthode quelque part qui se trouvera au-delà de votre méthode.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Projet toujours s’exécute dans le simulateur iPad

Le programme d’installation de l’iPhone SDK 4.0 installe 2 kits de développement logiciel - le Kit de développement logiciel 3.2, pour créer des applications iPad uniquement et le SDK 4.0, utilisé pour créer des applications universelles et iPhone. Il installe également un simulateur 3.2, qui simule uniquement sur un iPad, et un simulateur 4.0 qui simule les iPhone ou iPhone 4. Tous les kits de développement logiciel et des simulateurs plus anciens sont supprimés.

Visual Studio pour les options de génération de projet iPhone Mac incluent un paramètre pour la version SDK qui sera utilisé dans la génération de votre application. Ce paramètre se trouve dans **Options du projet -> Build -> iPhone Build**.

Nouveaux projets dans Visual Studio pour Mac utilisent le plus ancien Kit de développement logiciel en tant que leur paramètre de SDK par défaut, et si le Kit de développement logiciel spécifié n’existe pas, Visual Studio pour Mac utilise la plus proche qu’il peut trouver pour générer votre application. Ceci afin que les projets ne nécessite pas toujours du dernier SDK. Toutefois, cela entraîne actuellement dans le Kit de développement logiciel de 3,2 en cours utilisé - ce qui se traduit dans le simulateur iPad utilisé.

Pour résoudre ce problème en utilisant le SDK 4.0, accédez à **Options du projet -> Build -> iPhone Build**> et modifiez la valeur SDK sur « 4.0 » à l’aide de la liste déroulante. Vous devez effectuer cette opération pour chaque combinaison de plateforme, accessibles via les listes déroulantes en haut du Panneau de configuration.

La version SDK ne faut pas confondre avec le paramètre « Version minimale du système d’exploitation ».
Cette valeur n’a pas à correspondre à la valeur de version du Kit de développement logiciel - il affecte la version minimale du système d’exploitation installe votre application, ce qui peut être plus ancienne que le Kit de développement, tant que vous utilisez uniquement les API qui existent dans le système d’exploitation antérieur, ou protéger l’utilisation des nouvelles fonctionnalités à l’aide de runtime du système d’exploitation version xtraire KS. Vous devez le définir la version la plus ancienne du système d’exploitation sur lequel vous testez votre application.

Notez également que le **projet -> cible du simulateur iPhone**> menu peut être utilisé pour sélectionner le simulateur est utilisé par défaut lors du débogage/en cours d’exécution d’un projet. En outre, le **exécuter -> exécuter avec**> menu peut être utilisé pour choisir un simulateur spécifique avec lequel exécuter

## <a name="ibtool-returns-error-133"></a>ibtool renvoie l’erreur 133

Cela signifie que vous avez XCode 4 est installé.   Dans XCode 4, l’outil ibtool a été supprimé, il n’est plus possible de modifier vos fichiers XIB avec un outil autonome.

Si vous souhaitez utiliser Interface Builder, installez [XCode série 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponible à partir du site web d’Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>« Impossible de créer la liaison complet du type mime : application/vnd.apple -<wbr/>Générateur d’interface »

Cette erreur se produit si vous essayez de créer un iPhone de l’interface utilisateur à partir d’un projet non iPhone. Assurez-vous que vous démarrez avec une solution iPhone/iPad, il n’est pas possible d’ajouter simplement des éléments d’interface utilisateur iPhone à un projet iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Incident de démarrage lors de l’exécution dans le simulateur iOS

Si vous obtenez un incident de runtime (SIGSEGV) dans le simulateur, ainsi que d’une trace de pile qui ressemble à ceci :

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
.. .then que vous avez probablement une (ou plus) assembly obsolète dans le répertoire de votre application de simulateur. Ces assemblys peuvent existe dans la mesure où Apple iOS simulator ajoute et met à jour des fichiers, mais jamais les supprime. Si cela produit, la solution la plus simple consiste à sélectionner « Réinitialiser et de contenu et paramètres... » dans le menu du simulateur.   

> [!WARNING]
> Cela supprimera tous les fichiers, applications et données à partir du simulateur.   Prochaine fois que vous exécutez votre application, Visual Studio pour Mac sera le déployer dans le simulateur et il n’y aura aucun assembly ancien, obsolète pour provoquer le blocage.

## <a name="simulator-hangs-during-application-installation"></a>Simulator se bloque lors de l’installation de l’application

Cela peut se produire lorsque les noms d’application incluent un «. » (point) dans leur nom.
Cela est interdit en tant que le nom d’exécutable dans CFBundleExecutable - même si elle peut fonctionne dans de nombreux autres cas (par exemple, des appareils).

 \* « La valeur ne doit pas inclure toute extension du nom de ». - [https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Erreur : « Le type d’attribut personnalisé 0 x 43 n’est pas pris en charge » lors de double-clic sur des fichiers .xib

Cela est dû à une tentative d’ouverture des fichiers .xib lorsque les variables d’environnement sont définies de manière incorrecte. Cela ne doit pas se produire à l’utilisation normale de Visual Studio pour Mac/Xamarin.iOS et rouvrir Visual Studio pour Mac à partir / applications doit résoudre le problème.

Lorsque vous tentez de mettre à jour le logiciel et ce message d’erreur s’affiche, veuillez courrier électronique *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>Application s’exécute sur le simulateur, mais échoue sur l’appareil

Ce problème peut se manifester sous plusieurs formes et ne produit pas toujours une erreur de cohérence. Si l’application contient un .xib, vérifiez que le **Action de génération** sur les fichiers .xib est définie sur **interface**. Il s’agit de l’action de génération par défaut pour .xibs.

Pour vérifier l’action de génération, cliquez avec le bouton droit sur le fichier .xib et choisissez **Action de génération**.


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: Aucune donnée n’est disponible pour l’encodage 437

Lorsque vous incluez des bibliothèques tierces 3e dans votre application Xamarin.iOS, vous pouvez obtenir une erreur sous la forme « System.NotSupportedException : Aucune donnée n’est disponible pour l’encodage 437" lorsque vous tentez de compiler et exécuter l’application. Par exemple, les bibliothèques, telles que `Ionic.Zip.ZipFile`, peut lever cette exception au cours de l’opération.

Cela peut être résolu en ouvrant les options pour le projet Xamarin.iOS, en accédant à **Build iOS** > **internationalisation** et en vérifiant la **ouest** internationalisation.
