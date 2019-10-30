---
title: Conseils de dépannage pour Xamarin. iOS
description: Ce document fournit différents conseils utiles pour la résolution des problèmes pendant le développement d’applications Xamarin. iOS. Il décrit des messages d’erreur spécifiques, ainsi que d’autres problèmes potentiels.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 21b1f0c29962b7aeb45a836c976ec2635a39622e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030880"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Conseils de dépannage pour Xamarin. iOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin. iOS ne peut pas résoudre System. ValueTuple

Cette erreur se produit en raison d’une incompatibilité avec Visual Studio.

- **Visual Studio 2017 Update 1** (version 15,1 ou antérieure) est compatible uniquement avec **System. ValueTuple NuGet 4.3.0** (ou antérieur).

- **Visual Studio 2017 Update 2** (version 15,2 ou ultérieure) est compatible uniquement avec le **System. ValueTuple NuGet 4.3.1** ou ultérieur.

Veuillez choisir le système. ValueTuple NuGet approprié qui correspond à votre installation de Visual Studio 2017.

## <a name="receiving-error-retrieving-update-information-error-message"></a>Réception du message d’erreur « erreur lors de la récupération des informations de mise à jour »

Lorsque vous tentez de mettre à jour le logiciel et que ce message d’erreur s’affiche, essayez de redémarrer votre IDE et de vous déconnecter, puis de revenir à votre compte dans l’IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Comment faire créer des sorties ou des actions avec Interface Builder ?

Avec l’introduction de l’Xamarin Designer pour iOS dans Visual Studio pour Mac et Visual Studio, les développeurs Xamarin. iOS peuvent désormais tirer parti de la création d’une interface utilisateur via les storyboards et. XIB. Pour plus d’informations sur l’utilisation du concepteur, reportez-vous aux guides [Hello, iOS](~/ios/get-started/hello-ios/index.md) .

Vous pouvez également consulter les guides d' [action](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) et de [sortie](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) d’Apple pour plus d’informations sur l’utilisation des sorties et des actions en IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System. Text. Encoding. GetEncoding lève une exception NotSupportedException

Vous utilisez peut-être un encodage qui n’est pas ajouté par défaut. Consultez la page d' [internationalisation](~/ios/app-fundamentals/localization/index.md) pour savoir comment ajouter la prise en charge d’un plus grand codage.

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (autre chose)

Le membre a probablement été supprimé par l’éditeur de liens et, par conséquent, il n’existe pas dans l’assembly au moment de l’exécution.  Il existe plusieurs solutions à ce qui suit :

- Ajoutez l’attribut [`[Preserve]`](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) au membre.  Cela empêchera l’éditeur de liens de le supprimer.
- Quand vous appelez [**mTouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29), utilisez les options **-nolink** ou **-linksdkonly** :
  - **-nolink** désactive toutes les liaisons.
  - **-linksdkonly** lie uniquement les assemblys fournis par Xamarin. iOS, tels que **Xamarin. iOS. dll**, tout en conservant tous les types dans les assemblys créés par l’utilisateur (IE. vos projets d’application).

Notez que les assemblys sont liés de sorte que l’exécutable résultant soit plus petit ; ainsi, la désactivation de la liaison peut entraîner un plus grand nombre de fichiers exécutables.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Vous obtenez un ModelNotImplementedException

Si vous obtenez cette exception, cela signifie que vous appelez base. Méthode () sur une classe qui substitue un modèle. Vous n’avez pas besoin d’appeler la méthode de base dans une classe pour les modèles (il s’agit de classes qui sont marquées avec l’attribut [Model]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Cette classe n’est pas conforme au codage de valeur de clé pour la clé XXXX

Si vous recevez cette erreur lors du chargement d’un fichier de plume, cela signifie que la valeur XXXX est introuvable dans votre classe managée. Cela signifie que vous ne disposez pas d’une déclaration similaire à celle-ci :

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

La définition ci-dessus est générée automatiquement par Visual Studio pour Mac pour tous les fichiers XIB que vous ajoutez à Visual Studio pour Mac dans le fichier `NAME_OF_YOUR_XIB_FILE.designer.xib.cs`.

En outre, les types contenant le code ci-dessus doivent être une sous-classe de [NSObject](xref:Foundation.NSObject).  Si le type conteneur se trouve dans un espace de noms, il doit également avoir un attribut [[register]](xref:Foundation.RegisterAttribute) qui fournit un nom de type sans espace de noms (comme interface Builder ne prend pas en charge les espaces de noms dans les types) :

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe inconnue XXXX dans le fichier Interface Builder

Cette erreur est générée si vous définissez une classe dans vos fichiers de générateur d’interface, mais que vous ne fournissez pas l’implémentation C# réelle de celle-ci dans votre code.

Vous devez ajouter du code tel que celui-ci :

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```

## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System. MissingMethodException : aucun constructeur trouvé pour foo. bar :: ctor (System. IntPtr)

Cette erreur se produit au moment de l’exécution lorsque le code essaie d’instancier une instance des classes que vous avez référencées à partir de votre fichier Interface Builder. Cela signifie que vous avez oublié d’ajouter un constructeur qui accepte un IntPtr unique comme paramètre.

Le constructeur avec un handle IntPtr est utilisé pour lier des objets managés avec leurs représentations non managées.

Pour résoudre ce problème, ajoutez la ligne de code suivante à la classe foo. bar :

```csharp
public Bar (IntPtr handle) : base (handle) { }
```

## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Le type {foo} ne contient pas de définition pour `GetNativeField` et aucune méthode d’extension `GetNativeField` de type {foo} n’a été trouvée

Si vous recevez cette erreur dans les fichiers générés par le concepteur (*. xib.designer.cs), cela signifie l’un des deux éléments suivants :

 **1) la classe partielle ou la classe de base est manquante**

Les classes partielles générées par le concepteur doivent avoir des classes partielles correspondantes dans le code utilisateur qui héritent d’une sous-classe de `NSObject`, souvent `UIViewController`. Vérifiez que vous disposez d’une telle classe pour le type qui donne l’erreur.

 **2) les espaces de noms par défaut ont été modifiés**

Les fichiers de concepteur sont générés à l’aide des paramètres d’espace de noms par défaut de votre projet. Si vous avez modifié ces paramètres ou renommé le projet, les classes partielles générées peuvent ne plus figurer dans le même espace de noms que leurs équivalents de code utilisateur.

Les paramètres d’espace de noms se trouvent dans la boîte de dialogue Options du projet. L’espace de noms par défaut se trouve dans la section **général-> paramètres principaux** . Si elle est vide, le nom de votre projet est utilisé comme valeur par défaut. Vous trouverez des paramètres d’espace de noms plus avancés dans la section **code source-> les stratégies de nommage .net** .

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Avertissement pour les actions : la méthode privée’foo’n’est jamais utilisée. CS0169

Les actions des fichiers d’interface Builder sont connectées aux widgets par réflexion au moment de l’exécution, ce qui signifie que cet avertissement est attendu.

Vous pouvez utiliser « #pragma warning Disable 0169 » #pragma warning Enable 0169» autour de vos actions si vous souhaitez supprimer cet avertissement uniquement pour ces méthodes, ou ajouter 0169 au champ « ignorer les avertissements » dans les options du compilateur si vous souhaitez le désactiver pour l’ensemble de votre projet (et non pas recommandé).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>échec de mTouch avec le message suivant : impossible d’ouvrir l’assembly'/path/to/yourproject.exe'

Si vous voyez ce message d’erreur, le problème est généralement que le chemin d’accès absolu à votre projet contient un espace. Cela sera résolu dans une future version de Xamarin. iOS, mais vous pouvez contourner le problème en déplaçant le projet vers un dossier sans espaces.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Votre version de sqlite3 est ancienne-veuillez effectuer une mise à niveau vers au moins v 3.5.0 !

Cela se produit lorsque vous effectuez toutes les opérations suivantes :

1. Utilisez mono. Data. sqlite
1. Utiliser Mac OS X Leopard (10,5)
1. Exécutez votre application dans le simulateur.

Le problème est que mono sélectionne le système d’exploitation OS X `libsqlite3.dylib`, et non le fichier de `libsqlite3.dylib` du Iphonesimulator dans. Votre application fonctionnera sur l’appareil, mais ce n' *est* pas votre simulateur.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Le déploiement sur l’appareil échoue avec System. exception : AMDeviceInstallApplication a retourné 3892346901

Cette erreur signifie que la configuration de la signature de code de votre certificat/ID de lot ne correspond pas au profil d’approvisionnement installé sur votre appareil.  Vérifiez que vous avez sélectionné le certificat approprié dans options du projet-> signature de l’offre groupée et l’ID d’ensemble correct spécifié dans options du projet-> application iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>La saisie semi-automatique du code ne fonctionne pas dans Visual Studio pour Mac

Vérifiez que vous utilisez la dernière version de Visual Studio pour Mac et Xamarin. iOS

Si le problème est encore présent, veuillez signaler [un bogue](https://monodevelop.com/Developers#Reporting_Bugs), en joignant les fichiers journaux **~/Library/logs/XamarinStudio-{version}/IDE-{timestamp}.log**, **AndroidTools-{timestamp}. log**et **Components-{timestamp}. log** .

Si tout le reste échoue, vous pouvez essayer de supprimer le cache de saisie semi-automatique du code afin qu’il soit régénéré :

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Veillez à taper cette commande correctement, sinon vous risquez de supprimer accidentellement des fichiers importants.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio pour Mac se bloque lorsque vous copiez du texte

Les outils Mac populaires QuickSilver, la barre d’outils Google et la barre de lancement contiennent des fonctionnalités du presse-papiers qui corrompent la mémoire de Visual Studio pour Mac. Dans leurs options, vous pouvez répertorier Visual Studio pour Mac comme un processus avec lequel elles ne doivent pas interférer.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio pour Mac se plaint de mono 2,4 obligatoire

Si vous avez mis à jour Visual Studio pour Mac en raison d’une mise à jour récente, et que vous tentez de la redémarrer, 2,4 il vous suffit de [mettre à niveau l’installation de mono 2,4](http://www.go-mono.com/mono-downloads/download.html).  

Mono 2.4.2.3 _6 résout certains problèmes importants qui empêchaient Visual Studio pour Mac de s’exécuter de manière fiable, parfois bloqués Visual Studio pour Mac au démarrage ou empêchaient la génération de la base de données de saisie semi-automatique de code.

Une fois que vous avez installé le nouveau mono, Visual Studio pour Mac démarrera comme prévu.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Assertion à.. /.. /.. /.. /mono/Metadata/Generic-Sharing.c : 704, condition « OTI » non remplie

Si vous recevez la trace de la pile suivante :

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Cela signifie que vous liez une bibliothèque statique compilée avec du code Thumb dans votre projet. Depuis la version 3,1 du kit de développement logiciel (SDK) iPhone (ou une version ultérieure au moment de la rédaction de cet article), Apple a introduit un bogue dans son éditeur de liens lors de la liaison de code non-Thumb (Xamarin. iOS) avec du code Thumb (votre bibliothèque statique). Vous devrez établir un lien avec une version non-Thumb de votre bibliothèque statique pour atténuer ce problème.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1get_count-"></a>System. ExecutionEngineException : tentative d’une méthode de compilation JIT (wrapper managé à managé) foo [] : System. Collections. Generic. ICollection'1. get_Count ()

Le suffixe [] indique que vous ou la bibliothèque de classes appelez une méthode sur un tableau par le biais d’une collection générique, telle que IEnumerable < >, ICollection < > ou IList < >. En guise de solution de contournement, vous pouvez forcer explicitement le compilateur AOA à inclure une telle méthode en appelant la méthode vous-même et en vous assurant que ce code est exécuté avant l’appel qui a déclenché l’exception. Dans ce cas, vous pouvez écrire :

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Cela forcera le compilateur AOA à inclure la méthode get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio pour Mac éditeur de source est extrêmement lent

Parfois, l’éditeur de source de Visual Studio pour Mac devient extrêmement lent, s’interrompt pendant plusieurs secondes entre les caractères de frappe.

Ce problème est très rare et extrêmement difficile à reproduire. il ne peut généralement pas être reproduit sur le même ordinateur après le redémarrage de Visual Studio pour Mac. Pour cette raison, nous vous prions de l’apprécier si vous pouviez effectuer plusieurs étapes de débogage avant de redémarrer Visual Studio pour Mac, puis nous envoyer les résultats.

1. Essayez de fermer l’onglet Éditeur, puis de le rouvrir. Est-il nécessaire de modifier ou de déplacer le signe insertion jusqu’à ce que le ralentissement se reproduise ?
1. Désactivez « Sync Beam » à l’aide de l’outil de développement « débogage quartz » (que vous pouvez trouver en vedette) et vérifiez si les performances de l’éditeur source sont restaurées à la normale.
1. Essayez de répéter l’étape (1) avec la synchronisation de poutre toujours désactivée.
1. Si l’éditeur se bloque pendant plus de quelques secondes, essayez d’exécuter « killall-QUIT [Visual Studio pour Mac] » dans un terminal pendant qu’il est bloqué. Il peut être difficile de faire en sorte que la commande kill se produise lorsque l’éditeur est bloqué, mais il est essentiel de le faire, car la commande force mono à écrire des traces de pile de tous les threads dans le journal MD, que nous pouvons utiliser pour découvrir l’état des threads pendant que le XS est suspendu.

Attachez les journaux XS, **~/Library/logs/XamarinStudio-{version}/IDE-{timestamp}.log**, **ANDROIDTOOLS-{timestamp}. log**et **Components-{timestamp}. log** (dans les versions antérieures d’XS/MonoDevelop, envoyez-les simplement **~/Library/logs /MonoDevelop-(3.0 | 2.8 | 2.6)/MonoDevelop.log**).

> [!NOTE]
> Le problème ci-dessus a été corrigé dans XS 2,2 final * *

## <a name="compiled-application-is-very-large"></a>L’application compilée est très volumineuse

Pour prendre en charge le débogage, les versions Debug contiennent du code supplémentaire. Les projets intégrés en mode version sont une fraction de la taille.

À partir de Xamarin. iOS 1,3, les versions de débogage comprenaient la prise en charge du débogage pour chaque composant unique de mono (chaque méthode dans chaque classe des frameworks).  

Avec Xamarin. iOS 1,4, nous allons introduire une méthode plus fine pour le débogage, la valeur par défaut est de fournir uniquement l’instrumentation de débogage pour votre code et vos bibliothèques, et non pas pour tous les [assemblys mono](~/cross-platform/internals/available-assemblies.md) (cela restera possible, mais vous devra choisir de déboguer ces assemblys).

## <a name="installation-hangs"></a>Blocages d’installation

Les programmes d’installation mono et Xamarin. iOS se bloquent si le simulateur iPhone est en cours d’exécution. Ce problème n’est pas limité à mono ou à Xamarin. iOS. il s’agit d’un problème cohérent sur tout logiciel qui tente d’installer un logiciel sur MacOS Snow Leopard si le simulateur iPhone s’exécute au moment de l’installation.

Veillez à quitter le simulateur iPhone, puis recommencez l’installation.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Trampolines de type 0 insuffisant

Si vous recevez ce message lors de l’exécution de l’appareil, vous pouvez créer plus de type 0 trampolines (spécifique au type) en modifiant la section Options de projet « Build iPhone ».  Vous souhaitez ajouter des arguments supplémentaires pour les cibles de génération d’appareil :

 `-aot "ntrampolines=2048"`

Le nombre par défaut de trampolines est 1024. Essayez d’améliorer ce nombre jusqu’à ce que vous disposiez de suffisamment pour votre application.

## <a name="ran-out-of-trampolines-of-type-1"></a>Trampolines de type 1 insuffisant

Si vous utilisez intensivement des génériques récursifs, vous pouvez recevoir ce message sur l’appareil.  Vous pouvez créer plus de types 1 trampolines (type RGCTX) en modifiant la section Options de projet « Build iPhone ».  Vous souhaitez ajouter des arguments supplémentaires pour les cibles de génération d’appareil :

 `-aot "nrgctx-trampolines=2048"`

Le nombre par défaut de trampolines est 1024. Essayez d’améliorer ce nombre jusqu’à ce que vous ayez suffisamment d’espace pour votre utilisation des génériques.

## <a name="ran-out-of-trampolines-of-type-2"></a>Trampolines de type 2 insuffisant

Si vous créez des interfaces intensives, vous pouvez recevoir ce message sur l’appareil.
Vous pouvez créer plusieurs trampolines de type 2 (type IMT thunks) en modifiant les options de votre projet section « Build iPhone ».  Vous souhaitez ajouter des arguments supplémentaires pour les cibles de génération d’appareil :

 `-aot "nimt-trampolines=512"`

Le nombre par défaut de IMT thunk trampolines est 128. Essayez d’améliorer ce nombre jusqu’à ce que vous ayez suffisamment d’espace pour votre utilisation des interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Le débogueur ne parvient pas à se connecter à l’appareil

Lorsque vous commencez à déboguer une configuration d’appareil, vous voyez que le débogueur affiche une boîte de dialogue indiquant qu’il tente de se connecter à l’application. Il existe plusieurs raisons pour lesquelles le débogueur ne peut pas se connecter à l’application, selon le mode que vous utilisez pour vous connecter (USB ou Wi-Fi).

 **Si l’appareil et l’hôte du débogueur se trouvent sur des réseaux différents**, un pare-feu ou un réseau privé peut empêcher l’application de se connecter à l’hôte du débogueur en mode Wi-Fi.

 **Visual Studio pour Mac peut ne pas être en mesure d’interroger l’adresse IP correcte de l’hôte**. En mode Wi-Fi Visual Studio pour Mac donne à l’application toutes les adresses IP qu’il peut trouver sur l’hôte, et l’application les essaie toutes pour voir si elle peut utiliser l’une d’entre elles pour se connecter à Visual Studio pour Mac.

 **Un autre appareil est connecté à un port USB sur l’ordinateur hôte.** Dans certains cas, d’autres appareils connectés aux ports USB de l’ordinateur hôte ont été connus pour interférer d’une certaine manière avec le débogage en mode USB.

Si le mode WiFi ou USB ne fonctionne pas, vous pouvez facilement essayer l’autre : dans Visual Studio pour Mac, ouvrez les préférences, accédez à la page préférences/débogueur/débogueur iPhone, puis activez la case à cocher « déboguer les appareils iOS sur WiFi plutôt que sur USB ».   Si ce n’est pas le cas, vous pouvez obtenir plus d’informations sur l’échec dans la console de l’appareil en mode détaillé (qui est activé en ajoutant « -v-v-v » aux arguments mTouch supplémentaires dans les options du projet).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Erreur 134 : échec de mTouch avec le message suivant :

Cette erreur peut être déclenchée si vous essayez de créer avec-nolink sur le style Xamarin. iOS 1,4 des mises en production. Vous pouvez contourner cette erreur en spécifiant des arguments supplémentaires dans votre configuration de projet MonoDevelop.

Ajouter l’argument

 `-nosymbolstrip`

le problème doit être résolu.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>L’identité de distribution n’est pas affichée dans Visual Studio pour Mac options de signature de projet

Visual Studio pour Mac 2,2 a un bogue qui empêche la détection de certificats de distribution contenant une virgule. Veuillez mettre à jour vers Visual Studio pour Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Erreur « AFCFileRefWrite retournée : 1 » pendant le téléchargement

Lors du chargement d’une application sur votre appareil, vous pouvez recevoir une erreur « AFCFileRefWrite retournée : 1 ». Cela peut se produire si vous avez un fichier de longueur nulle.

## <a name="error-mtouch-failed-with-no-output"></a>Erreur « Échec de mTouch sans sortie »

La version actuelle de Xamarin. iOS et Visual Studio pour Mac échoue lorsque le nom du projet ou le répertoire où la solution ou le projet sont stockés contient des espaces.
Pour résoudre ce problème :

- Assurez-vous que ni votre projet ni le répertoire où il est stocké ne contient un espace.
- Dans le projet « principaux paramètres », vérifiez que le nom du projet ne contient pas d’espaces.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Erreur «le binaire que vous avez téléchargé n’était pas valide. Une version bêta préliminaire du kit de développement logiciel (SDK) a été utilisée pour générer l’application «

Cette erreur est généralement due à un projet qui a été démarré dans le développement iPad avant la sortie de Xamarin. iOS 2.0.0, vous avez probablement des clés dans vos informations. plist, par exemple :

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Cette paire de paires doit être supprimée, car Visual Studio pour Mac la gère automatiquement.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Erreur « une version bêta préliminaire du kit de développement logiciel (SDK) a été utilisée pour générer l’application »

(Fourni par Ed Anuff)

Procédez comme suit :

- Remplacez la version du kit de développement logiciel (SDK) de la build iPhone par 3,2 ou iTunes Connect pour la refuser lors du chargement, car une application compatible iPad créée à l’aide d’une version du kit de développement logiciel (SDK) est inférieure à 3,2
- Créez un fichier info. plist personnalisé pour le projet et définissez explicitement MinimumOSVersion sur 3,0.   Cette opération remplace la valeur MinimumOSVersion 3,2 définie par Xamarin. iOS.   Si vous ne le faites pas, l’application ne sera pas en mesure de s’exécuter sur un iPhone.
- Reconstruisez, compressez et chargez-les dans iTunes Connect.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Exception non gérée : System. exception : impossible de trouver le sélecteur someSelector : on {type}

Cette exception est due à l’un des trois éléments suivants :

1. Vous avez fourni un sélecteur au runtime objective-C sans appliquer l’attribut [Export] correspondant à une méthode
1. Vous avez activé la liaison complète et vous n’avez pas appliqué l’attribut [Preserve] à la méthode [Export] ed.
1. Vous avez appliqué l’attribut [Export] à une méthode privée dans un type hérité.

## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>Le fichier MainWindow.xib.designer.cs n’est pas mis à jour

Il y avait un bogue dans Xamarin Studio 2,4 qui a provoqué la non-regroupement du fichier MainWindow. XIB avec le fichier MainWindow. XIB. Designer dans les nouveaux projets. Cela signifiait ne pas mettre à jour le code du concepteur pour ce fichier particulier.

Ce problème est résolu dans la version de Visual Studio pour Mac disponible dans son programme de mise à jour intégré. par conséquent, assurez-vous d’utiliser la version la plus récente.

Vous pouvez corriger des projets existants en supprimant (et non en supprimant) le XIB et son fichier de concepteur, puis en l’ajoutant de nouveau. Cela doit regrouper correctement les fichiers.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView ou UIActionSheet disparaît après sa création

Si vous disposez d’un code similaire à celui-ci :

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

l’objet « actionSheet » vit comme une variable temporaire dans la fonction et dès que la fonction se termine, l’objet est éligible pour garbage collection, donc il finit par être récupéré par le garbage collector.

Pour résoudre ce problème, vous devez conserver une référence à « actionSheet » en dehors de votre méthode, quelque part qui se trouvera au-delà de votre méthode.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Le projet s’exécute toujours dans le simulateur iPad

Le programme d’installation de iPhone SDK 4,0 installe 2 SDK, le kit de développement logiciel (SDK) 3,2, pour la création d’applications iPad uniquement et le kit de développement logiciel (SDK) 4,0, utilisé pour générer iPhone et des applications universelles. Il installe également un simulateur 3,2, qui simule uniquement un iPad et un simulateur 4,0 qui simule iPhone ou iPhone 4. Tous les anciens SDK et simulateurs sont supprimés.

Visual Studio pour Mac options de génération de projet iPhone incluent un paramètre pour la version du kit de développement logiciel (SDK) qui sera utilisé dans la génération de votre application. Ce paramètre se trouve dans **Options du projet-> Build-> iPhone Build**.

Les nouveaux projets dans Visual Studio pour Mac utilisent le kit de développement logiciel (SDK) installé le plus ancien comme paramètre du kit de développement logiciel (SDK) par défaut, et si le kit de développement logiciel (SDK) spécifié n’existe pas, Visual Studio pour Mac utilise le plus proche qu’il peut trouver pour générer votre application. Cela a été fait afin que les projets ne nécessitent pas toujours le dernier Kit de développement logiciel (SDK). Toutefois, cela entraîne actuellement l’utilisation du kit de développement logiciel (SDK) 3,2, ce qui entraîne l’utilisation du simulateur iPad.

Pour résoudre ce problème à l’aide du kit de développement logiciel (SDK) 4,0, accédez à **Project options-> Build-> iPhone build**> et remplacez la valeur du kit de développement logiciel par « 4,0 » à l’aide de la zone de liste déroulante. Vous devez effectuer cette opération pour chaque combinaison de configuration et de plateforme, accessible à l’aide des listes déroulantes en haut du panneau.

La version du kit de développement logiciel (SDK) ne doit pas être confondue avec le paramètre « version minimale du système d’exploitation ».
Cette valeur ne doit pas nécessairement correspondre à la valeur de la version du kit de développement logiciel (SDK). elle affecte la version minimale du système d’exploitation sur lequel votre application sera installée, qui peut être antérieure à celle du kit de développement logiciel (SDK), tant que vous utilisez uniquement des API qui existent dans l’ancien système d’exploitation, ou que vous protégez l’utilisation des nouvelles ques. Vous devez la définir sur la version du système d’exploitation la plus ancienne sur laquelle vous testez votre application.

Notez également que le menu > **cible du simulateur > iPhone du projet**peut être utilisé pour sélectionner le simulateur qui est utilisé par défaut lors de l’exécution/du débogage d’un projet. En outre, le **> exécuter avec**> menu peut être utilisé pour choisir un simulateur spécifique avec lequel exécuter

## <a name="ibtool-returns-error-133"></a>ibtool retourne l’erreur 133

Cela signifie que XCode 4 est installé.   Dans XCode 4, l’outil ibtool a été supprimé, il n’est plus possible de modifier vos fichiers XIB avec un outil autonome.

Si vous souhaitez utiliser Interface Builder, installez la [série Xcode 3](https://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponible sur le site Web d’Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-interface-builder"></a>« Impossible de créer une liaison d’affichage pour le type MIME : application/vnd. Apple-Interface-Builder »

Cette erreur se produit si vous essayez de créer une interface utilisateur iPhone à partir d’un projet non iPhone. Assurez-vous de commencer avec une solution iPhone/iPad. il n’est pas possible d’ajouter simplement des éléments d’interface utilisateur iPhone à un projet non-iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Incident de démarrage lors de l’exécution dans le simulateur iOS

Si vous obtenez un blocage au moment de l’exécution (SIGSEGV) dans le simulateur, ainsi qu’une trace de la pile qui ressemble à ceci :

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```

... vous avez probablement un ou plusieurs assemblys obsolètes dans le répertoire de votre application de simulateur. De tels assemblys peuvent exister, car Apple iOS Simulator ajoute et met à jour les fichiers, mais ne les supprime jamais. Dans ce cas, la solution la plus simple consiste à sélectionner « réinitialiser et contenu et paramètres... ». dans le menu du simulateur.   

> [!WARNING]
> Cette opération supprime tous les fichiers, applications et données du simulateur.   La prochaine fois que vous exécuterez votre application, Visual Studio pour Mac le déploiera dans le simulateur et il n’y aura aucun ancien assembly obsolète pour provoquer l’incident.

## <a name="simulator-hangs-during-application-installation"></a>Le simulateur se bloque lors de l’installation de l’application

Cela peut se produire lorsque les noms d’application incluent un « . » (point) dans leur nom.
Cela est interdit en tant que nom d’exécutable dans CFBundleExecutable, même s’il peut fonctionner dans de nombreux autres cas (tels que les appareils).

 \* « La valeur ne doit pas inclure d’extension sur le nom ». - [https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Erreur : « le type d’attribut personnalisé 0x43 n’est pas pris en charge » lorsque vous double-cliquez sur des fichiers. XIB

Cela est dû à la tentative d’ouverture des fichiers. XIB lorsque les variables d’environnement ne sont pas définies correctement. Cela ne doit pas se produire avec l’utilisation normale de Visual Studio pour Mac/Xamarin. iOS, et la réouverture Visual Studio pour Mac à partir de/applications doit résoudre le problème.

Lorsque vous tentez de mettre à jour le logiciel et que ce message d’erreur s’affiche, envoyez un courrier électronique *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>L’application s’exécute sur le simulateur mais échoue sur l’appareil

Ce problème peut se manifester sous plusieurs formes et ne produit pas toujours une erreur cohérente. Si l’application contient un. XIB, vérifiez que l’action de **génération** sur le. XIB est définie sur **InterfaceDefinition**. Il s’agit de l’action de génération par défaut pour. XIB.

Pour vérifier l’action de génération, cliquez avec le bouton droit sur le fichier. XIB et choisissez **action de génération**.

## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException : aucune donnée n’est disponible pour l’encodage 437

Lorsque vous incluez des bibliothèques tierces dans votre application Xamarin. iOS, vous pouvez obtenir une erreur au format « System. NotSupportedException : aucune donnée n’est disponible pour l’encodage 437 » lors de la tentative de compilation et d’exécution de l’application. Par exemple, les bibliothèques, telles que `Ionic.Zip.ZipFile`, peuvent lever cette exception pendant l’opération.

Cela peut être résolu en ouvrant les options du projet Xamarin. iOS, en accédant à **iOS Build** > **internationalisation** et en vérifiant l’internationalisation de l' **Ouest** .
