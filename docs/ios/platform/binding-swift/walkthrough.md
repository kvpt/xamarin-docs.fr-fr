---
title: 'Procédure pas à pas : liaison d’une bibliothèque SWIFT iOS'
description: Cet article fournit une procédure pas à pas de création d’une liaison Xamarin. iOS pour une infrastructure SWIFT existante, Gigya. Il aborde des sujets tels que la compilation d’un Framework SWIFT, sa liaison et l’utilisation de la liaison dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: B563ADE9-D0E3-4BC3-A288-66D2296BE706
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 3c63b1a4ed58b0efcc510085934a5380e6049ae7
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853157"
---
# <a name="walkthrough-bind-an-ios-swift-library"></a>Procédure pas à pas : liaison d’une bibliothèque SWIFT iOS

> [!IMPORTANT]
> Nous étudions actuellement l’utilisation de la liaison personnalisée sur la plateforme Xamarin. Veuillez suivre [**ce questionnaire**](https://www.surveymonkey.com/r/KKBHNLT) pour informer les futurs efforts de développement.

Xamarin permet aux développeurs mobiles de créer des expériences mobiles natives multiplateformes à l’aide de Visual Studio et de C#. Vous pouvez utiliser les composants du kit de développement logiciel (SDK) de la plateforme iOS prêts à l’emploi. Toutefois, dans de nombreux cas, vous souhaitez également utiliser des kits de développement logiciel tiers développés pour cette plateforme, ce que Xamarin vous permet d’effectuer via des liaisons. Pour intégrer un framework Objective-C tiers à votre application Xamarin. iOS, vous devez créer une liaison Xamarin. iOS pour celle-ci avant de pouvoir l’utiliser dans vos applications.

La plateforme iOS, ainsi que ses langages et outils natifs, évoluent en permanence et SWIFT est l’une des zones les plus dynamiques dans le monde de développement iOS. Il existe un certain nombre de kits de développement logiciel tiers, qui ont déjà été migrés de Objective-C vers SWIFT et nous présentent de nouveaux défis. Même si le processus de liaison SWIFT est similaire à Objective-C, il requiert des étapes supplémentaires et des paramètres de configuration pour créer et exécuter une application Xamarin. iOS acceptable pour le AppStore.

L’objectif de ce document est de présenter une approche de haut niveau pour traiter ce scénario et de fournir un guide pas à pas détaillé avec un exemple simple.

## <a name="background"></a>Arrière-plan

SWIFT a été introduite initialement par Apple dans 2014 et est désormais à la version 5,1, avec l’adoption de frameworks tiers en constante évolution. Vous avez le choix entre plusieurs options pour lier un Framework SWIFT et ce document décrit l’approche à l’aide de l’en-tête d’interface généré par objective-C. L’en-tête est automatiquement créé par les outils Xcode lorsqu’une infrastructure est créée, et elle est utilisée pour communiquer du monde géré au monde SWIFT.

## <a name="prerequisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous avez besoin des éléments suivants :

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started#installing-objective-sharpie)
- [Interface CLI AppCenter](https://docs.microsoft.com/appcenter/test-cloud/) (facultatif)

## <a name="build-a-native-library"></a>Créer une bibliothèque Native

La première étape consiste à créer une infrastructure SWIFT native avec l’en-tête objective-C activé. L’infrastructure est généralement fournie par un développeur tiers et l’en-tête est incorporé dans le package dans le répertoire suivant : ** \<FrameworkName> . Framework/en-têtes/ \<FrameworkName> -SWIFT. h**.

Cet en-tête expose les interfaces publiques, qui seront utilisées pour créer des métadonnées de liaison Xamarin. iOS et générer des classes C# exposant les membres du Framework SWIFT. Si l’en-tête n’existe pas ou a une interface publique incomplète (par exemple, vous ne voyez pas classes/membres), vous avez deux options :

- Mettre à jour le code source SWIFT pour générer l’en-tête et marquer les membres requis avec l' `@objc` attribut
- Créer une infrastructure de proxy dans laquelle vous contrôlez l’interface publique et proxy de tous les appels à l’infrastructure sous-jacente

Dans ce didacticiel, la deuxième approche est décrite, car elle a moins de dépendances sur le code source tiers, ce qui n’est pas toujours disponible. Une autre raison d’éviter la première approche est l’effort supplémentaire requis pour prendre en charge les futures modifications de l’infrastructure. Une fois que vous avez commencé à ajouter des modifications au code source tiers, vous êtes responsable de la prise en charge de ces modifications et de leur fusion éventuelle avec chaque mise à jour ultérieure.

Par exemple, dans ce didacticiel, une liaison pour le [Kit de développement logiciel (SDK) SWIFT Gigya](https://developers.gigya.com/display/GD/Swift+SDK) est créée :

1. Ouvrez Xcode et créez un nouveau Framework SWIFT, qui sera un proxy entre le code Xamarin. iOS et un Framework SWIFT tiers. Cliquez sur **fichier > nouveau projet >** et suivez les étapes de l’Assistant :

    ![projet de création de Framework Xcode](walkthrough-images/xcode-create-framework-project.png)

    ![nom du projet d’infrastructure de noms Xcode](walkthrough-images/xcode-name-framework-project.png)

1. Téléchargez [Gigya Framework](https://developers.gigya.com/display/GD/Swift+SDK) à partir du site Web du développeur et Déballez-le. Au moment de l’écriture, la dernière version est [Gigya SWIFT SDK 1.0.9](https://downloads.gigya.com/predownload?fileName=Swift-Core-framework-1.0.9.zip)

1. Sélectionnez **SwiftFrameworkProxy** dans l’Explorateur de fichiers du projet, puis sélectionnez l’onglet général.

1. Glissez-déplacez le package **Gigya. Framework** vers la liste infrastructures et bibliothèques Xcode sous l’onglet général, activez l’option **copier les éléments si nécessaire** lors de l’ajout de l’infrastructure :

    ![infrastructure de copie Xcode](walkthrough-images/xcode-copy-framework.png)

    Vérifiez que l’infrastructure SWIFT a été ajoutée au projet, sinon, les options suivantes ne seront pas disponibles.

1. Assurez-vous que l’option **ne pas incorporer** est sélectionnée, qui sera ensuite contrôlée manuellement :

    [![Xcode donotembed (option)](walkthrough-images/xcode-donotembed-option.png)](walkthrough-images/xcode-donotembed-option.png#lightbox)

1. Assurez-vous que l’option paramètres de build **incorporer toujours les bibliothèques standard SWIFT**, qui incluent des bibliothèques SWIFT avec l’infrastructure est définie sur non. Il sera ensuite contrôlé manuellement, les dylibs SWIFT inclus dans le package final :

    [![Xcode toujours incorporer l’option false](walkthrough-images/xcode-alwaysembedfalse-option.png)](walkthrough-images/xcode-alwaysembedfalse-option.png#lightbox)

1. Assurez-vous que l’option **Enable Bitcode** a la valeur **no**. À partir de maintenant Xamarin. iOS n’inclut pas Bitcode alors qu’Apple exige que toutes les bibliothèques prennent en charge les mêmes architectures :

    [![Active Xcode Enable bitcode option false](walkthrough-images/xcode-enablebitcodefalse-option.png)](walkthrough-images/xcode-enablebitcodefalse-option.png#lightbox)

    Vous pouvez vérifier que l’option Bitcode de l’infrastructure résultante est désactivée en exécutant la commande de terminal suivante sur le Framework :

    ```bash
    otool -l SwiftFrameworkProxy.framework/SwiftFrameworkProxy | grep __LLVM
    ```

    La sortie doit être vide, sinon vous souhaitez vérifier les paramètres du projet pour votre configuration spécifique.

1. Assurez-vous que l’option **de nom d’en-tête d’interface générée par objective-C** est activée et spécifie un nom d’en-tête. Le nom par défaut est ** \<FrameworkName> -SWIFT. h**:

    [![option d’en-tête Xcode objectice-c activée](walkthrough-images/xcode-objcheaderenabled-option.png)](walkthrough-images/xcode-objcheaderenabled-option.png#lightbox)

1. Exposez les méthodes souhaitées et marquez-les avec l' `@objc` attribut et appliquez des règles supplémentaires définies ci-dessous. Si vous générez l’infrastructure sans cette étape, l’en-tête objective-C généré sera vide et Xamarin. iOS ne pourra pas accéder aux membres du Framework SWIFT. Exposez la logique d’initialisation du kit de développement logiciel (SDK) SWIFT Gigya sous-jacent en créant un fichier SWIFT **SwiftFrameworkProxy. Swift** et en définissant le code suivant :

    ```swift
    import Foundation
    import UIKit
    import Gigya

    @objc(SwiftFrameworkProxy)
    public class SwiftFrameworkProxy : NSObject {

        @objc
        public func initFor(apiKey: String) -> String {
            Gigya.sharedInstance().initFor(apiKey: apiKey)
            let gigyaDomain = Gigya.sharedInstance().config.apiDomain
            let result = "Gigya initialized with domain: \(gigyaDomain)"
            return result
        }
    }
    ```

    Voici quelques remarques importantes sur le code ci-dessus :

    - Importez le module Gigya ici à partir du kit de développement logiciel (SDK) Gigya tiers d’origine et pouvez maintenant accéder à n’importe quel membre du Framework.
    - Marquez la classe SwiftFrameworkProxy avec l' `@objc` attribut qui spécifie un nom ; sinon, un nom unique illisible sera généré, tel que `_TtC19SwiftFrameworkProxy19SwiftFrameworkProxy` . Le nom du type doit être clairement défini, car il sera utilisé ultérieurement par son nom.
    - Hériter de la classe proxy de `NSObject` ; sinon, elle ne sera pas générée dans le fichier d’en-tête objective-C.
    - Marquez tous les membres à exposer en tant que `public` .

1. Modifiez la configuration de build du schéma de **Debug** en **Release**. Pour ce faire, ouvrez la boîte de dialogue **> cible Xcode > modifier le schéma** , puis affectez à l’option de **configuration Build** la valeur **Release**:

    ![schéma de modification Xcode](walkthrough-images/xcode-edit-scheme.png)

    ![mise en version du schéma de modification Xcode](walkthrough-images/xcode-edit-scheme-release.png)

1. À ce stade, l’infrastructure est prête à être créée. Générez l’infrastructure pour les architectures de simulateur et d’appareil, puis combinez les sorties comme un package d’infrastructure unique. Identifier les versions du kit de développement logiciel (SDK) installées afin de générer le code source à l’aide de l’outil **xcodebuild** :

    ```bash
    xcodebuild -showsdks
    ```

    Vous devez obtenir un résultat semblable à ce qui suit :

    ```bash
    iOS SDKs:
            iOS 13.2                        -sdk iphoneos13.2
    iOS Simulator SDKs:
            Simulator - iOS 13.2            -sdk iphonesimulator13.2
    macOS SDKs:
            DriverKit 19.0                  -sdk driverkit.macosx19.0
            macOS 10.15                     -sdk macosx10.15
    tvOS SDKs:
            tvOS 13.2                       -sdk appletvos13.2
    tvOS Simulator SDKs:
            Simulator - tvOS 13.2           -sdk appletvsimulator13.2
    watchOS SDKs:
            watchOS 6.1                     -sdk watchos6.1
    watchOS Simulator SDKs:
            Simulator - watchOS 6.1         -sdk watchsimulator6.1
    ```

    Choisissez un kit de développement logiciel (SDK) iOS et une version du kit de développement logiciel (SDK) iOS souhaités, dans ce cas version 13,2 et exécutez la génération avec la commande suivante :

    ```bash
    xcodebuild -sdk iphonesimulator13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    xcodebuild -sdk iphoneos13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    ```

    > [!TIP]
    > Si vous disposez d’un espace de travail au lieu d’un projet, générez l’espace de travail et spécifiez la cible comme paramètre obligatoire. Vous souhaitez également spécifier un répertoire de sortie, car pour les espaces de travail, ce répertoire sera différent pour les builds de projet.

    > [!TIP]
    > Vous pouvez également utiliser [le script d’assistance](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L3-L14) pour créer l’infrastructure pour toutes les architectures applicables ou simplement la créer à partir du simulateur et du périphérique de commutation Xcode dans le sélecteur cible.

1. Il existe deux infrastructures SWIFT, une pour chaque plateforme, qui les combinent comme un package unique à incorporer dans un projet de liaison Xamarin. iOS ultérieurement. Pour créer une infrastructure FAT, qui combine les deux architectures, vous devez effectuer les étapes suivantes. Le package d’infrastructure est simplement un dossier, ce qui vous permet d’effectuer tous les types d’opérations, telles que l’ajout, la suppression et le remplacement de fichiers :

    - Accédez au dossier de sortie de la génération avec les sous-dossiers **Release-iPhoneOS** et **Release-iphonesimulator dans** et copiez l’un des frameworks en tant que version initiale de la sortie finale (Framework FAT).

        ```bash
        cp -R "Release-iphoneos" "Release-fat"
        ```

    - Combiner des modules d’une autre génération avec les modules d’infrastructure FAT

        ```bash
        cp -R "Release-iphonesimulator/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/" "Release-fat/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/"
        ```

    - Combiner iPhoneOS + configuration iphonesimulator dans comme Framework FAT

        ```bash
        lipo -create -output "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphoneos/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphonesimulator/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

    - Vérifier les résultats

        ```bash
        lipo -info "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

        La sortie doit restituer les éléments suivants, en reflétant le nom de l’infrastructure et les architectures incluses :

        ```bash
        Architectures in the fat file: Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy are: x86_64 arm64
        ```

    > [!TIP]
    > Si vous ne souhaitez prendre en charge qu’une seule plate-forme (par exemple, vous générez une application, qui peut être exécutée sur un appareil uniquement), vous pouvez ignorer l’étape pour créer la bibliothèque FAT et utiliser l’infrastructure de sortie à partir de la build de l’appareil précédemment.

    > [!TIP]
    > Vous pouvez également utiliser [le script d’assistance](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L16-L24) pour créer l’infrastructure FAT, qui automatise toutes les étapes ci-dessus.

## <a name="prepare-metadata"></a>Préparer les métadonnées

À ce stade, vous devez disposer de l’infrastructure avec l’en-tête d’interface généré par objective-C prêt à être consommé par une liaison Xamarin. iOS.  L’étape suivante consiste à préparer les interfaces de définition d’API, qui sont utilisées par un projet de liaison pour générer des classes C#. Ces définitions peuvent être créées manuellement ou automatiquement par l’outil [précision objective](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) et le fichier d’en-tête généré. Utilisez la métaie pour générer les métadonnées :

1. Téléchargez le dernier outil [Sharp objective](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) à partir du site Web des téléchargements officiels et installez-le en suivant les étapes de l’Assistant. Une fois l’installation terminée, vous pouvez la vérifier en exécutant la commande sharpy :

    ```bash
    sharpie -v
    ```

1. Générer des métadonnées à l’aide du sharpy et du fichier d’en-tête objective-C généré automatiquement :

    ```bash
    sharpie bind --sdk=iphoneos13.2 --output="XamarinApiDef" --namespace="Binding" --scope="Release-fat/SwiftFrameworkProxy.framework/Headers/" "Release-fat/SwiftFrameworkProxy.framework/Headers/SwiftFrameworkProxy-Swift.h"
    ```

    La sortie reflète les fichiers de métadonnées générés : **ApiDefinitions.cs** et **StructsAndEnums.cs**. Enregistrez ces fichiers pour l’étape suivante afin de les inclure dans un projet de liaison Xamarin. iOS avec les références natives :

    ```bash
    Parsing 1 header files...
    Binding...
        [write] ApiDefinitions.cs
        [write] StructsAndEnums.cs
    ```

    L’outil génère des métadonnées C# pour chaque membre objective-C exposé, qui ressemble au code suivant. Comme vous pouvez le voir, il peut être défini manuellement, car il a un format lisible par l’utilisateur et un mappage de membres simples :

    ```csharp
    [Export ("initForApiKey:")]
    string InitForApiKey (string apiKey);
    ```

    > [!TIP]
    > Le nom du fichier d’en-tête peut être différent si vous avez modifié les paramètres de Xcode par défaut pour le nom d’en-tête. Par défaut, il a le nom d’un projet avec le suffixe **-SWIFT** . Vous pouvez toujours vérifier le fichier et son nom en accédant au dossier en-têtes du package du Framework.

    > [!TIP]
    > Dans le cadre du processus d’automatisation, vous pouvez utiliser [le script d’assistance](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L35) pour générer automatiquement des métadonnées une fois l’infrastructure Fat créée.

## <a name="build-a-binding-library"></a>Créer une bibliothèque de liaisons

L’étape suivante consiste à créer un projet de liaison Xamarin. iOS à l’aide du modèle de liaison Visual Studio, à ajouter les métadonnées requises, aux références natives, puis à générer le projet pour générer une bibliothèque consommable :

1. Ouvrez Visual Studio pour Mac et créez un projet de bibliothèque de liaisons Xamarin. iOS, donnez-lui un nom, dans ce cas SwiftFrameworkProxy. Binding, puis terminez l’Assistant. Le modèle de liaison Xamarin. iOS se trouve à l’emplacement suivant : **iOS > library > bibliothèque de liaisons**:

    ![créer une bibliothèque de liaisons Visual Studio](walkthrough-images/visualstudio-create-binding-library.png)

1. Supprimez les fichiers de métadonnées existants **ApiDefinition.cs** et **structs.cs** , car ils seront entièrement remplacés par les métadonnées générées par l’outil sharpity objective.
1. Copiez les métadonnées générées par Sharpy au cours de l’une des étapes précédentes, sélectionnez l’action de génération suivante dans la fenêtre Propriétés : **ObjBindingApiDefinition** pour le fichier **ApiDefinitions.cs** et **ObjBindingCoreSource** pour le fichier **StructsAndEnums.cs** :

    ![métadonnées de structure de projet Visual Studio](walkthrough-images/visualstudio-project-structure-metadata.png)

    Les métadonnées elles-mêmes décrivent chaque classe et membre objective-C exposé à l’aide du langage C#. Vous pouvez voir la définition d’en-tête objective-C d’origine à côté de la déclaration C# :

    ```csharp
    // @interface SwiftFrameworkProxy : NSObject
    [BaseType (typeof(NSObject))]
    interface SwiftFrameworkProxy
    {
        // -(NSString * _Nonnull)initForApiKey:(NSString * _Nonnull)apiKey __attribute__((objc_method_family("none"))) __attribute__((warn_unused_result));
        [Export ("initForApiKey:")]
        string InitForApiKey (string apiKey);
    }
    ```

    Bien qu’il s’agisse d’un code C# valide, il n’est pas utilisé tel quel mais est utilisé par les outils Xamarin. iOS pour générer des classes C# basées sur cette définition de métadonnées. Par conséquent, au lieu de l’interface SwiftFrameworkProxy vous obtenez une classe C# portant le même nom, qui peut être instanciée par votre code Xamarin. iOS. Cette classe obtient des méthodes, des propriétés et d’autres membres définis par vos métadonnées, que vous appellerez en C#.

1. Ajoutez une référence native à l’infrastructure Fat antérieure générée, ainsi que chaque dépendance de cette infrastructure. Dans ce cas, ajoutez les références natives SwiftFrameworkProxy et Gigya Framework au projet de liaison :

    - Pour ajouter des références de Framework natives, Ouvrez Finder et accédez au dossier avec les frameworks. Glissez-déplacez les frameworks sous l’emplacement des références natives dans le Explorateur de solutions. Vous pouvez également utiliser l’option de menu contextuel dans le dossier Références natives, puis cliquer sur **Ajouter une référence Native** pour rechercher les frameworks et les ajouter :

    ![références natives de structure de projet Visual Studio](walkthrough-images/visualstudio-project-structure-nativerefs.png)

    - Mettez à jour les propriétés de chaque référence native et vérifiez les trois options importantes suivantes :

        - Définir la liaison intelligente = true
        - Set force Load = false
        - Définir la liste des infrastructures utilisées pour créer les infrastructures d’origine. Dans ce cas, chaque infrastructure n’a que deux dépendances : Foundation et UIKit. Définissez-le sur le champ frameworks :

        ![options de proxy nativeref Visual Studio](walkthrough-images/visualstudio-nativeref-proxy-options.png)

        Si vous avez d’autres indicateurs de l’éditeur de liens à spécifier, définissez-les dans le champ indicateurs de l’éditeur de liens. Dans ce cas, laissez-le vide.

    - Spécifiez d’autres indicateurs de l’éditeur de liens si nécessaire. Si la bibliothèque que vous liez expose uniquement des API objective-C, mais utilise SWIFT en interne, vous risquez de rencontrer des problèmes tels que :

        ```Console
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftQuartzCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCoreImage
        ```

        Dans les propriétés du projet de liaison pour la bibliothèque native, les valeurs suivantes doivent être ajoutées aux indicateurs de l’éditeur de liens :

        ```Linker
        L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphonesimulator/ -L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphoneos -Wl,-rpath -Wl,@executable_path/Frameworks
        ```

        Les deux premières options (celles-ci  `-L ...`   ) indiquent au compilateur natif où trouver les bibliothèques SWIFT. Le compilateur natif ignorera les bibliothèques qui n’ont pas l’architecture correcte, ce qui signifie qu’il est possible de passer l’emplacement pour les bibliothèques de simulateur et les bibliothèques d’appareils en même temps, afin qu’il fonctionne pour les builds du simulateur et de l’appareil (ces chemins d’accès sont corrects uniquement pour iOS. pour tvOS et Watchos, ils doivent être mis à jour L’un des inconvénients est que cette approche nécessite que le Xcode correct se trouve dans/Application/Xcode.app, si le consommateur de la bibliothèque de liaison possède Xcode à un autre emplacement, il ne fonctionne pas. L’autre solution consiste à ajouter ces options dans les arguments mTouch supplémentaires dans les options de build iOS du projet exécutable ( `--gcc_flags -L... -L...` ). La troisième option permet à l’éditeur de liens natif de stocker l’emplacement des bibliothèques SWIFT dans l’exécutable, afin que le système d’exploitation puisse les trouver.

1. La dernière action consiste à générer la bibliothèque et à vérifier que vous n’avez pas d’erreurs de compilation. Vous constaterez souvent que les métadonnées de liaisons produites par objective Sharp sont annotées avec l'  `[Verify]`   attribut. Ces attributs indiquent que vous devez vérifier que la finesse objective a fait la bonne chose en comparant la liaison avec la déclaration objective-C d’origine (qui sera fournie dans un commentaire au-dessus de la déclaration liée). Vous pouvez en savoir plus sur les membres marqués avec l’attribut à [l’aide du lien suivant](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify). Une fois le projet généré, il peut être consommé par une application Xamarin. iOS.

## <a name="consume-the-binding-library"></a>Utiliser la bibliothèque de liaisons

L’étape finale consiste à utiliser la bibliothèque de liaisons Xamarin. iOS dans une application Xamarin. iOS. Créez un nouveau projet Xamarin. iOS, ajoutez une référence à la bibliothèque de liaisons et activez le kit de développement logiciel (SDK) SWIFT Gigya :

1. Créez un projet Xamarin. iOS. Vous pouvez utiliser l' **application iOS > > application avec affichage unique** comme point de départ :

    ![application Visual Studio New](walkthrough-images/visualstudio-app-new.png)

1. Ajoutez une référence de projet de liaison au projet ou dll cible créé précédemment. Traitez la bibliothèque de liaisons comme une bibliothèque Xamarin. iOS normale :

    ![références de l’application Visual Studio](walkthrough-images/visualstudio-app-refs.png)

1. Mettre à jour le code source de l’application et ajouter la logique d’initialisation au ViewController principal, qui active le kit de développement logiciel (SDK) Gigya

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
        var proxy = new SwiftFrameworkProxy();
        var result = proxy.InitForApiKey("APIKey");
        System.Diagnostics.Debug.WriteLine(result);
    }
    ```

1. Créez un bouton avec un nom **btnLogin** et ajoutez le gestionnaire de clic de bouton suivant pour activer un workflow d’authentification :

    ```csharp
    private void btnLogin_Tap(object sender, EventArgs e)
    {
        _proxy.LoginWithProvider(GigyaSocialProvidersProxy.Instagram, this, (result, error) =>
        {
            // process your login result here
        });
    }
    ```

1. Exécutez l’application, dans la sortie de débogage, vous devriez voir la ligne suivante : `Gigya initialized with domain: us1.gigya.com` . Cliquez sur le bouton pour activer le workflow d’authentification :

    [![résultat du proxy SWIFT](walkthrough-images/swiftproxy-result.png)](walkthrough-images/swiftproxy-result.png#lightbox)

Félicitations ! Vous avez créé avec succès une application Xamarin. iOS et une bibliothèque de liaison, qui consomme un Framework SWIFT. L’application ci-dessus s’exécutera avec succès sur iOS 12.2 +, car à partir de cette version d’iOS, [Apple a introduit la stabilité Abi](https://swift.org/blog/swift-5-1-released/) et chaque iOS démarrant + + comprend des bibliothèques Runtime SWIFT qui pourraient être utilisées pour exécuter votre application compilée avec SWIFT 5.1 +. Si vous devez ajouter la prise en charge des versions antérieures d’iOS, vous devez effectuer quelques étapes supplémentaires :

1. Afin d’ajouter la prise en charge d’iOS 12,1 et versions antérieures, vous souhaitez expédier un dylibs SWIFT spécifique utilisé pour compiler votre infrastructure. Utilisez le package NuGet [Xamarin. iOS. SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) pour traiter et copier les bibliothèques nécessaires avec votre Loi. Ajoutez la référence NuGet à votre projet cible et régénérez l’application. Aucune étape supplémentaire n’est requise, le package NuGet installe des tâches spécifiques, qui sont exécutées avec le processus de génération, identifient les dylibs SWIFT requis et les empaquette avec la loi finale.

1. Pour soumettre l’application à l’App Store, vous souhaitez utiliser Xcode et l’option de distribution, qui mettra à jour le fichier **SwiftSupport** et le dossier dylibs pour qu’il soit accepté par le AppStore :

    ○ Archive l’application. Dans le menu Visual Studio pour Mac, sélectionnez **générer > Archive pour la publication**:

    ![Archive Visual Studio pour la publication](walkthrough-images/visualstudio-archiveforpublishing.png)

    Cette action génère le projet et l’atteint dans l’organisateur, qui est accessible par Xcode pour la distribution.

    ○ Distribue via Xcode. Ouvrez Xcode et accédez à la **fenêtre >** option de menu Organizer :

    ![Archives Visual Studio](walkthrough-images/visualstudio-archives.png)

    Sélectionnez l’archive créée à l’étape précédente, puis cliquez sur le bouton distribuer l’application. Suivez l’Assistant pour charger l’application dans le AppStore.

1. Cette étape est facultative, mais il est important de vérifier que votre application peut s’exécuter sur iOS 12,1 et versions antérieures, ainsi que sur 12,2. Vous pouvez le faire avec l’aide de Test Cloud et de l’infrastructure UITest. Créez un projet UITest et un test de l’interface utilisateur de base, qui exécute l’application :

    - Créez un projet UITest et configurez-le pour votre application Xamarin. iOS :

        ![Nouveautés de Visual Studio UITest](walkthrough-images/visualstudio-uitest-new.png)

        > [!TIP]
        > Vous trouverez plus d’informations sur la création d’un projet UITest et sa configuration pour votre application à [l’aide du lien suivant](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

    - Créez un test de base pour exécuter l’application et utilisez certaines des fonctionnalités du kit de développement logiciel (SDK) SWIFT. Ce test active l’application, tente de se connecter, puis appuie sur le bouton Annuler :

        ```csharp
        [Test]
        public void HappyPath()
        {
            app.WaitForElement(StatusLabel);
            app.WaitForElement(LoginButton);
            app.Screenshot("App loaded.");
            Assert.AreEqual(app.Query(StatusLabel).FirstOrDefault().Text, "Gigya initialized with domain: us1.gigya.com");

            app.Tap(LoginButton);
            app.WaitForElement(GigyaWebView);
            app.Screenshot("Login activated.");

            app.Tap(CancelButton);
            app.WaitForElement(LoginButton);
            app.Screenshot("Login cancelled.");
        }
        ```

        > [!TIP]
        > Pour plus d’informations sur Framework tests UITest et UI Automation, consultez [le lien suivant](https://docs.microsoft.com/appcenter/test-cloud/uitest/).

    - Créer une application iOS dans App Center, créer une nouvelle série de tests avec un nouvel ensemble d’appareils pour exécuter le test :

        ![Visual Studio App Center-nouveau](walkthrough-images/visualstudio-appcenter-new.png)

        > [!TIP]
        > Pour en savoir plus sur AppCenter Test Cloud, consultez [le lien suivant](https://docs.microsoft.com/appcenter/test-cloud/).

    - Installer la CLI AppCenter

        ```bash
        npm install -g appcenter-cli
        ```

        > [!IMPORTANT]
        > Vérifier que le nœud v 6.3 ou version ultérieure est installé

    - Exécutez le test à l’aide de la commande suivante. Assurez-vous également que votre ligne de commande AppCenter est actuellement connectée.

        ```bash
        appcenter test run uitest --app "Mobile-Customer-Advisory-Team/SwiftBinding.iOS" --devices a7e7cb50 --app-path "Xamarin.SingleView.ipa" --test-series "master" --locale "en_US" --build-dir "Xamarin/Xamarin.SingleView.UITests/bin/Debug/"
        ```

    - Vérifiez le résultat. Dans le portail AppCenter, accédez à l' **application > test > séries de tests**:

        ![résultat de Visual Studio AppCenter UITest](walkthrough-images/visualstudio-appcenter-uitest-result.png)

        Et sélectionnez la série de tests souhaitée et vérifiez le résultat :

        ![Visual Studio AppCenter UITest s’exécute](walkthrough-images/visualstudio-appcenter-uitest-runs.png)

Vous avez développé une application Xamarin. iOS de base qui utilise une infrastructure SWIFT Native via une bibliothèque de liaisons Xamarin. iOS. L’exemple fournit un moyen simple d’utiliser le Framework sélectionné et, dans une application réelle, vous devrez exposer davantage d’API et préparer les métadonnées pour ces API. Le script permettant de générer des métadonnées simplifiera les futures modifications apportées aux API du Framework.

## <a name="related-links"></a>Liens connexes

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio pour Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Vérification des métadonnées de la netteté](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [Liaison de l’infrastructure objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [Kit de développement logiciel (SDK) Gigya iOS (Framework SWIFT)](https://developers.gigya.com/display/GD/Swift+SDK)
- [SWIFT 5,1-stabilité ABI](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Automatisation Xamarin UITest](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [Configuration de Xamarin. iOS UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter Test Cloud](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Exemple de référentiel de projet](https://github.com/alexeystrakh/xamarin-binding-swift-framework)
