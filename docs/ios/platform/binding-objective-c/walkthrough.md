---
title: 'Procédure pas à pas : liaison d’une bibliothèque Objective-C iOS'
description: Cet article fournit une procédure pas à pas pratique de création d’une liaison Xamarin.iOS pour une bibliothèque Objective-C existante, InfColorPicker. Elle couvre des sujets tels que la compilation d’une bibliothèque Objective-C statique et en le liant à l’aide de la liaison dans une application Xamarin.iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 2897129779b698eae60338d44f9af19b6a2761bc
ms.sourcegitcommit: 10b4ccbfcf182be940899c00fc0fecae1e199c5b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252356"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Procédure pas à pas : liaison d’une bibliothèque Objective-C iOS

_Cet article fournit une procédure pas à pas pratique de création d’une liaison Xamarin.iOS pour une bibliothèque Objective-C existante, InfColorPicker. Elle couvre des sujets tels que la compilation d’une bibliothèque Objective-C statique et en le liant à l’aide de la liaison dans une application Xamarin.iOS._

Lorsque vous travaillez sur iOS, vous pouvez rencontrer des cas où vous souhaitez utiliser une bibliothèque Objective-C de fournisseurs tiers. Dans ces situations, vous pouvez utiliser un Xamarin.iOS _projet de liaison_ pour créer un [ C# liaison](~/cross-platform/macios/binding/overview.md) qui vous permettent de consommer la bibliothèque dans vos applications Xamarin.iOS.

En règle générale, dans l’écosystème iOS, vous trouverez bibliothèques dans 3 versions :

* Comme un fichier de bibliothèque statique précompilé avec `.a` extension ainsi que ses en-têtes (fichiers .h). Par exemple, [bibliothèque d’Analytique de Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Comme une infrastructure précompilée. Il s’agit simplement un dossier contenant la bibliothèque statique, des en-têtes et parfois, d’autres ressources avec `.framework` extension. Par exemple, [bibliothèque de AdMob de Google](https://developers.google.com/admob/ios/download).
* Tout simplement les fichiers de code source. Par exemple, une bibliothèque contenant simplement `.m` et `.h` Objective C fichiers.

Dans le premier et le deuxième scénario déjà sera une bibliothèque statique CocoaTouch précompilé donc dans cet article nous allons nous concentrer sur le troisième scénario. N’oubliez pas, avant de commencer à créer une liaison, vérifiez toujours la licence fournie avec la bibliothèque pour vous assurer que vous êtes libre de la lier.

Cet article fournit une procédure de création d’un projet de liaison à l’aide de l’open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C de projet par exemple, cependant, toutes les informations dans ce guide peuvent être adaptées pour une utilisation avec n’importe quel bibliothèque d’Objective-C par des tiers. La bibliothèque InfColorPicker fournit un contrôleur d’affichage réutilisables qui permet à l’utilisateur de sélectionner une couleur en fonction de sa représentation sous forme de TSL, effectuer une sélection de couleur plus conviviales.

[![](walkthrough-images/run01.png "Exemple de la bibliothèque InfColorPicker en cours d’exécution sur iOS")](walkthrough-images/run01.png#lightbox)

Nous aborderons toutes les étapes nécessaires pour utiliser cette API Objective-C particulier dans Xamarin.iOS :

- Tout d’abord, nous allons créer une bibliothèque statique Objective-C à l’aide de Xcode.
- Ensuite, nous allons lier cette bibliothèque statique avec Xamarin.iOS.
- Ensuite, montrent comment objectif Sharpie peut réduire la charge de travail en générant automatiquement certaines (mais pas tous) des définitions d’API nécessaires requises par la liaison Xamarin.iOS.
- Enfin, nous allons créer une application Xamarin.iOS qui utilise la liaison.

L’exemple d’application va vous montrer comment utiliser un délégué fort pour la communication entre l’API InfColorPicker et notre C# code. Une fois que nous avons vu comment utiliser un délégué fort, nous aborderons l’utilisation de délégués faibles pour effectuer les mêmes tâches.

## <a name="requirements"></a>Configuration requise

Cet article suppose que vous êtes familiarisé avec Xcode et le langage d’Objective-C et que vous avez lu nos [liaison Objective-C](~/cross-platform/macios/binding/index.md) documentation. En outre, ce qui suit est nécessaire pour terminer les étapes suivantes :

-  **Xcode et SDK iOS** -Xcode d’Apple et l’API iOS plus récentes doivent être installés et configurés sur l’ordinateur du développeur.
-  **[Outils de ligne de commande Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -les outils de ligne de commande Xcode doit être installés pour la version actuellement installée de Xcode (voir ci-dessous pour les détails de l’installation).
-  **Visual Studio pour Mac ou Visual Studio** -la dernière version de Visual Studio pour Mac ou Visual Studio doit être installée et configurée sur l’ordinateur de développement. Un Apple Mac est requise pour développer une application Xamarin.iOS, et lors de l’utilisation de Visual Studio vous devez être connecté à [un Xamarin.iOS hôte de build](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **La dernière version de l’objectif Sharpie** -une copie actuelle de l’outil objectif Sharpie téléchargé à partir de [ici](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Si vous avez déjà Sharpie objectif installé, vous pouvez mettre à jour vers la dernière version à l’aide de la `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Installer les outils de ligne de commande de Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)


Comme indiqué ci-dessus, nous allons utiliser les outils de ligne de commande Xcode (en particulier `make` et `lipo`) dans cette procédure pas à pas. Le `make` commande est un utilitaire Unix très courant qui automatise la compilation des programmes exécutables et des bibliothèques à l’aide un _makefile_ qui spécifie comment le programme doit être généré. Le `lipo` commande est un utilitaire de ligne de commande du système d’exploitation X pour la création de fichiers de l’architecture multi ; il combinera plusieurs `.a` fichiers dans un fichier qui peut être utilisé par toutes les architectures matérielles.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Comme indiqué ci-dessus, nous allons utiliser les outils de ligne de commande Xcode sur le **hôte de Build Mac** (spécifiquement `make` et `lipo`) dans cette procédure pas à pas. Le `make` commande est un utilitaire Unix très courant qui automatise la compilation des programmes exécutables et des bibliothèques à l’aide un _makefile_ spécifie comment générer le programme. Le `lipo` commande est un utilitaire de ligne de commande du système d’exploitation X pour la création de fichiers de l’architecture multi ; il combinera plusieurs `.a` fichiers dans un fichier qui peut être utilisé par toutes les architectures matérielles.


-----

En fonction d’Apple [génération à partir de la ligne de commande avec Xcode FAQ](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) documentation, dans OS X 10.9 et supérieur, le **télécharge** volet de Xcode **préférences** dialogue pas prend plus en charge les outils de ligne de commande de téléchargement.

Vous devez utiliser une des méthodes suivantes pour installer les outils :

- **Installer Xcode** : quand vous installez Xcode, il est fourni avec tous vos outils de ligne de commande. Dans OS X 10.9 les shims (installé dans `/usr/bin`), peut mapper à n’importe quel outil inclus dans `/usr/bin` à l’outil correspondant à l’intérieur de Xcode. Par exemple, le `xcrun` commande, qui vous permet de trouver ou d’exécuter n’importe quel outil à l’intérieur de Xcode à partir de la ligne de commande.
- **L’Application Terminal** -à partir de l’application Terminal, vous pouvez installer les outils de ligne de commande en exécutant la `xcode-select --install` commande :
    - Démarrez l’Application Terminal.
    - Type `xcode-select --install` et appuyez sur **entrée**, par exemple :

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Vous devrez installer les outils de ligne de commande, cliquez sur le **installer** bouton :   [![](walkthrough-images/xcode01.png "Installation des outils de ligne de commande")](walkthrough-images/xcode01.png#lightbox)

    - Les outils seront téléchargés et installés à partir de serveurs Apple :   [![](walkthrough-images/xcode02.png "Téléchargement des outils")](walkthrough-images/xcode02.png#lightbox)

- **Téléchargements pour les développeurs d’Apple** -outils de ligne de commande le package est disponible le [téléchargements pour les développeurs d’Apple](https://developer.apple.com/downloads/index.action) page web. Connectez-vous avec votre ID Apple, puis recherchez et téléchargez les outils de ligne de commande : [![](walkthrough-images/xcode03.png "Recherche les outils de ligne de commande")](walkthrough-images/xcode03.png#lightbox)

Avec les outils de ligne de commande installé, nous sommes prêts poursuivre la procédure pas à pas.

## <a name="walkthrough"></a>Procédure pas à pas

Dans cette procédure pas à pas, nous aborderons les étapes suivantes :

- **[Créer une bibliothèque statique](#Creating_A_Static_Library)**  -cette étape implique la création d’une bibliothèque statique de la **InfColorPicker** code Objective-C. La bibliothèque statique aura le `.a` extension de fichier et seront incorporées dans l’assembly .NET du projet de bibliothèque.
- **[Créer un projet de liaison Xamarin.iOS](#Create_a_Xamarin.iOS_Binding_Project)**  -une fois que nous disposons d’une bibliothèque statique, nous allons l’utiliser pour créer un projet de liaison Xamarin.iOS. Le projet de liaison se compose de la bibliothèque statique que nous venons de créer et de métadonnées sous la forme de C# code qui explique comment l’API Objective-C peut être utilisé. Ces métadonnées sont communément appelé les définitions d’API. Nous allons utiliser **[objectif Sharpie](#Using_Objective_Sharpie)** pour nous aider à créer les définitions d’API.
- **[Normaliser les définitions API](#Normalize_the_API_Definitions)**  - objectif Sharpie fait un travail remarquable pour nous aider à, mais il ne peut pas effectuer toutes les opérations. Nous aborderons certaines modifications que nous devons apporter aux définitions d’API avant de pouvoir être utilisés.
- **[Utiliser la bibliothèque de liaison](#Using_the_Binding)**  -Enfin, nous allons créer une application Xamarin.iOS pour montrer comment utiliser notre projet de liaison qui vient d’être créée.

Maintenant que nous comprenons quelles étapes sont impliquées, passons au reste de la procédure pas à pas.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Création d’une bibliothèque statique

Si nous observons le code pour InfColorPicker dans Github :

[![](walkthrough-images/image02.png "Inspecter le code pour InfColorPicker dans Github")](walkthrough-images/image02.png#lightbox)

Nous pouvons voir les trois répertoires suivants dans le projet :

- **InfColorPicker** -ce répertoire contient le code Objective-C pour le projet.
- **PickerSamplePad** -ce répertoire contient un exemple de projet iPad.
- **PickerSamplePhone** -ce répertoire contient un exemple de projet iPhone.

Nous allons télécharger le projet InfColorPicker [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) et décompressez-le dans le répertoire de notre choix. Ouverture de la cible de Xcode pour `PickerSamplePhone` projet, nous voyons la structure de projet suivante dans le navigateur de Xcode :

[![](walkthrough-images/image03.png "La structure de projet dans le navigateur de Xcode")](walkthrough-images/image03.png#lightbox)

Ce projet permet d’obtenir la réutilisation de code en ajoutant directement le code source InfColorPicker (en rouge) dans chaque exemple de projet. Le code de l’exemple de projet est à l’intérieur de la zone bleue. Étant donné que ce projet ne pas nous fournisse une bibliothèque statique, il est nécessaire pour nous créer un projet Xcode pour compiler la bibliothèque statique.

La première étape est pour nous permet d’ajouter le code source InfoColorPicker dans la bibliothèque statique. Pour y parvenir nous allons effectuer les opérations suivantes :

1. Démarrez Xcode.
2. À partir de la **fichier** menu, sélectionnez **New** > **projet...** :

    [![](walkthrough-images/image04.png "Démarrez un nouveau projet")](walkthrough-images/image04.png#lightbox)
3. Sélectionnez **Framework & bibliothèque**, le **Cocoa Touch une bibliothèque statique** modèle et cliquez sur le **suivant** bouton :

    [![](walkthrough-images/image05.png "Sélectionnez le modèle Cocoa Touch une bibliothèque statique")](walkthrough-images/image05.png#lightbox)

4. Entrez `InfColorPicker` pour le **nom_projet** et cliquez sur le **suivant** bouton :

    [![](walkthrough-images/image06.png "Entrez InfColorPicker pour le nom du projet")](walkthrough-images/image06.png#lightbox)
5. Sélectionnez un emplacement pour enregistrer le projet et cliquez sur le **OK** bouton.
6. Nous devons maintenant ajouter la source du projet InfColorPicker à notre projet de bibliothèque statique. Étant donné que le **InfColorPicker.h** fichier existe déjà dans notre bibliothèque statique (par défaut), Xcode nous autorisera pas à le remplacer. À partir de la **Finder**, accéder au code source InfColorPicker dans le projet d’origine que nous avons décompressés à partir de GitHub, copiez tous les fichiers InfColorPicker et collez-les dans notre nouveau projet de bibliothèque statique :

    [![](walkthrough-images/image12.png "Copiez tous les fichiers InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Revenez à Xcode, cliquez avec le bouton droit sur le **InfColorPicker** dossier et sélectionnez **ajouter des fichiers à « InfColorPicker... »** :

    [![](walkthrough-images/image08.png "Ajout de fichiers")](walkthrough-images/image08.png#lightbox)

8. À partir de la boîte de dialogue Ajouter des fichiers, accédez aux fichiers de code source InfColorPicker que nous venez de copier, sélectionnez-les tous, puis cliquez sur le **ajouter** bouton :

    [![](walkthrough-images/image09.png "Sélectionner tout et cliquez sur le bouton Ajouter")](walkthrough-images/image09.png#lightbox)

9. Le code source sera copié dans notre projet :

    [![](walkthrough-images/image10.png "Le code source sera copié dans le projet")](walkthrough-images/image10.png#lightbox)

10. Dans le navigateur de projet Xcode, sélectionnez le **InfColorPicker.m** de fichiers et commentez les deux dernières lignes (en raison de la façon de cette bibliothèque a été écrit, ce fichier n’est pas utilisé) :

    [![](walkthrough-images/image14.png "Modification du fichier InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. Nous devons maintenant vérifier s’il existe des infrastructures requises par la bibliothèque. Vous pouvez trouver ces informations dans le fichier Lisezmoi, soit en ouvrant un des exemples de projet fournis. Cet exemple utilise `Foundation.framework`, `UIKit.framework`, et `CoreGraphics.framework` nous allons donc les ajouter.

12. Sélectionnez le **InfColorPicker cible > génération de Phases** et développez le **Link Binary With Libraries** section :

    [![](walkthrough-images/image16b.png "Développez la section binaire avec des bibliothèques de liens")](walkthrough-images/image16b.png#lightbox)

13. Utilisez le **+** bouton pour ouvrir la boîte de dialogue que vous puissiez ajouter les infrastructures de frames requis répertoriés ci-dessus :

    [![](walkthrough-images/image16c.png "Ajoutez que les infrastructures de frames requis répertoriés ci-dessus")](walkthrough-images/image16c.png#lightbox)

14. Le **Link Binary With Libraries** section doit maintenant ressembler à l’image ci-dessous :

    [![](walkthrough-images/image16d.png "La section binaire avec des bibliothèques de liens")](walkthrough-images/image16d.png#lightbox)

À ce stade, nous sommes fermer, mais nous n’avons pas tout à fait terminé. La bibliothèque statique a été créée, mais nous devons générer afin de créer un Fat binaire qui inclut toutes les architectures requis pour les appareils iOS et le simulateur iOS.

### <a name="creating-a-fat-binary"></a>Création d’un fichier Fat binaire

Tous les appareils iOS dotés de processeurs alimentées par l’architecture ARM ont développé au fil du temps. Chaque nouvelle architecture ajouté de nouvelles instructions et autres améliorations tout en conservant une compatibilité descendante. les appareils iOS ont armv6, armv7, armv7s, jeux d’instructions arm64 – même si [armv6 ne pas utilisé plus](~/ios/deploy-test/compiling-for-different-devices.md). Le simulateur iOS n’est pas alimenté par ARM et est à la place un x86 et simulateur de x86_64 sous tension. Cela signifie que les bibliothèques doivent être fournis pour chaque jeu d’instructions.

Est une bibliothèque Fat `.a` fichier contenant toutes les architectures prises en charge.

Création d’un fat binaire est un processus en trois étapes :

- Compiler une version 7 de ARM et ARM64 de la bibliothèque statique.
- Compiler une version x86 et x84_64 de la bibliothèque statique.
- Utilisez le `lipo` outil en ligne de commande pour combiner les deux bibliothèques statiques en une seule.

Bien que ces trois étapes sont plutôt simples, il peut être nécessaire de les répéter à l’avenir lors de la bibliothèque Objective-C reçoit des mises à jour ou si nous avons besoin de correctifs de bogues. Si vous décidez d’automatiser ces étapes, il permettra de simplifier la maintenance future et la prise en charge du projet de liaison iOS.

Il existe de nombreux outils disponibles pour automatiser des tâches - un script shell, [rake](http://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/), et [rendre](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Lorsque les outils de ligne de commande Xcode sont installés, `make` est également installé, c’est le système de génération qui sera utilisé pour cette procédure pas à pas. Voici un **Makefile** que vous pouvez utiliser pour créer une bibliothèque partagée architecture multi qui fonctionne sur un appareil iOS et le simulateur pour n’importe quelle bibliothèque :

```bash
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```

Entrez le **Makefile** commandes dans l’éditeur de texte brut de votre choix et mettre à jour les sections avec **votre nom de projet** par le nom de votre projet. Il est également important de s’assurer que vous collez les instructions ci-dessus exactement, avec les onglets dans les instructions conservées.

Enregistrez le fichier sous le nom **Makefile** au même emplacement que la bibliothèque statique de InfColorPicker Xcode, nous avons créé ci-dessus :

[![](walkthrough-images/lib00.png "Enregistrez le fichier avec le nom de Makefile")](walkthrough-images/lib00.png#lightbox)

Ouvrez l’Application Terminal sur votre Mac et accédez à l’emplacement de votre Makefile. Type `make` dans le Terminal, appuyez sur **entrée** et **Makefile** seront exécutées :

[![](walkthrough-images/lib01.png "Exemple de sortie de makefile")](walkthrough-images/lib01.png#lightbox)

Lorsque vous exécutez marque, vous verrez un grand nombre de défilement par du texte. Si tout fonctionne correctement, vous verrez les mots **génération a réussi** et `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` et `libInfColorPickerSDK.a` fichiers seront copiés dans le même emplacement que le **Makefile**:

[![](walkthrough-images/lib02.png "Les fichiers de libInfColorPicker-armv7.a, libInfColorPicker-i386.a et libInfColorPickerSDK.a générés par le Makefile")](walkthrough-images/lib02.png#lightbox)

Vous pouvez vérifier les architectures dans votre fichier Fat binaire à l’aide de la commande suivante :

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Cela doit afficher les éléments suivants :

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

À ce stade, nous avons terminé la première étape de notre liaison iOS en créant une bibliothèque statique à l’aide de Xcode et les outils de ligne de commande Xcode `make` et `lipo`. Nous allons passer à l’étape suivante et utilisez **objectif-Sharpie** pour automatiser la création des liaisons d’API pour nous.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Créer un projet de liaison de Xamarin.iOS

Avant que nous pouvons utiliser **objectif-Sharpie** pour automatiser le processus de liaison, nous devons créer un projet de liaison Xamarin.iOS pour héberger les définitions d’API (que nous allons utiliser **objectif-Sharpie** pour nous aider à build) et créer le C# de liaison pour nous.

Nous allons effectuer les opérations suivantes :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez Visual Studio pour Mac.
1. À partir de la **fichier** menu, sélectionnez **New** > **Solution...** :

    ![](walkthrough-images/bind01.png "Démarrage d’une nouvelle solution")

1. Dans la boîte de dialogue Nouvelle Solution, sélectionnez **bibliothèque** > **iOS projet Binding**:

    ![](walkthrough-images/bind02.png "Sélectionnez la liaison de projet iOS")

1. Cliquez sur le **suivant** bouton.

1. Entrez « InfColorPickerBinding » en tant que le **nom_projet** et cliquez sur le **créer** bouton pour créer la solution :

    ![](walkthrough-images/bind02a.png "Entrez InfColorPickerBinding comme nom de projet")

La solution va être créée et deux fichiers par défaut seront incluses :

![](walkthrough-images/bind03.png "La structure de solution dans l’Explorateur de solutions")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Démarrez Visual Studio.

1. À partir de la **fichier** menu, sélectionnez **New** > **projet...** :

    ![Démarrez un nouveau projet](walkthrough-images/bind01vs.png "démarrez un nouveau projet")

1. Dans la boîte de dialogue Nouveau projet, sélectionnez **Visual C# > iPhone & iPad > liaisons bibliothèque iOS (Xamarin)** :

    [![Sélectionnez la bibliothèque de liaisons iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Entrez « InfColorPickerBinding » en tant que le **nom** et cliquez sur le **OK** bouton pour créer la solution.

La solution va être créée et deux fichiers par défaut seront incluses :

![](walkthrough-images/bind03vs.png "La structure de solution dans l’Explorateur de solutions")

-----

- **ApiDefinition.cs** -ce fichier contient les contrats qui définissent comment les API Objective-C est encapsulé dans C#.
- **Structs.cs** : ce fichier contiendra toutes les structures ou les valeurs d’énumération qui sont requis par les interfaces et délégués.

Nous allons travailler avec ces deux fichiers plus loin dans la procédure pas à pas. Tout d’abord, nous devons ajouter la bibliothèque InfColorPicker au projet de liaison.

### <a name="including-the-static-library-in-the-binding-project"></a>Ajout de la bibliothèque statique dans le projet de liaison

Nous avons notre liaison projet de base prêt, nous devons ajouter la bibliothèque Fat binaire que nous avons créé ci-dessus pour le **InfColorPicker** bibliothèque.

Suivez ces étapes pour ajouter la bibliothèque :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Avec le bouton droit sur le **références natives** dossier dans le panneau solutions, puis sélectionnez **ajouter des références natives**:

    ![](walkthrough-images/bind04a.png "Ajouter des références natives")

1. Accédez à la Fat binaire nous apportées précédemment (`libInfColorPickerSDK.a`) et appuyez sur la **Open** bouton :

    ![](walkthrough-images/bind05.png "Sélectionnez le fichier libInfColorPickerSDK.a")
1. Le fichier sera inclus dans le projet :

    ![](walkthrough-images/bind04.png "Y compris un fichier")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copie le `libInfColorPickerSDK.a` à partir de votre **hôte de Build Mac** et collez-le dans votre projet de liaison.

1. Avec le bouton droit sur le projet et choisissez **Ajouter > élément existant...** :

    ![](walkthrough-images/bind04vs.png "Ajout d’un fichier existant")

1. Accédez à la `libInfColorPickerSDK.a` et appuyez sur la **ajouter** bouton :

    ![](walkthrough-images/bind05vs.png "Ajout de libInfColorPickerSDK.a")

1. Le fichier sera inclus dans le projet.

-----

Lorsque le **.a** fichier est ajouté au projet, Xamarin.iOS définira automatiquement le **Action de génération** du fichier à **ObjcBindingNativeLibrary**et créer un fichier spécial appelé `libInfColorPickerSDK.linkwith.cs`.


Ce fichier contient le `LinkWith` attribut qui indique à Xamarin.iOS comment le handle de la bibliothèque statique que nous vous avons ajouté. Le contenu de ce fichier est présenté dans l’extrait de code suivant :

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

Le `LinkWith` attribut identifie la bibliothèque statique pour le projet et certains indicateurs de l’éditeur de liens importants.


La prochaine chose que nous devons faire consiste à créer les définitions d’API pour le projet InfColorPicker. Dans le cadre de cette procédure pas à pas, nous allons utiliser objectif Sharpie pour générer le fichier **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>À l’aide de Objective Sharpie

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)


Objectif Sharpie est une ligne de commande outil (fourni par Xamarin) qui vous permet de créer les définitions requises pour lier une bibliothèque de Objective-C 3ème partie à C#. Dans cette section, nous allons utiliser objectif Sharpie pour créer la première **ApiDefinition.cs** pour le projet InfColorPicker.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Objectif Sharpie est une ligne de commande outil (fourni par Xamarin) qui vous permet de créer les définitions requises pour lier une bibliothèque de Objective-C 3ème partie à C#. Dans cette section, nous allons utiliser objectif Sharpie sur notre **hôte de Build Mac** pour créer la première **ApiDefinition.cs** pour le projet InfColorPicker.


-----

Pour commencer, nous allons télécharger de fichier du programme d’installation objectif Sharpie détaillées dans cet [guide](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Exécutez le programme d’installation et suivez toutes les invites à l’écran à partir de l’Assistant Installation pour installer objectif Sharpie sur notre ordinateur de développement.

Une fois que nous avons objectif Sharpie correctement installé, nous allons démarrer l’application Terminal et entrez la commande suivante pour obtenir de l’aide sur tous les outils qu’il fournit pour aider à la liaison :

```bash
sharpie -help
```

Si nous exécutons la commande ci-dessus, la sortie suivante est générée :

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

Pour les besoins de cette procédure pas à pas, nous allons utiliser les outils d’objectif Sharpie suivants :

- **Xcode** - ce outils présente des informations sur notre installation Xcode en cours et les versions d’iOS et Mac API que nous avons installé. Nous utiliserons ces informations ultérieurement lorsque nous générons notre liaisons.
- **lier** -nous allons utiliser cet outil pour analyser le **.h** fichiers dans le projet InfColorPicker dans initial **ApiDefinition.cs** et **StructsAndEnums.cs** fichiers.

Pour obtenir une aide sur un outil Sharpie d’objectif spécifique, entrez le nom de l’outil et le `-help` option. Par exemple, `sharpie xcode -help` renvoie le résultat suivant :

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

Pour pouvoir commencer le processus de liaison, nous devons obtenir des informations sur nos kits SDK installés actuelle en entrant la commande suivante dans le Terminal `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

Dans l’exemple ci-dessus, nous constatons que nous avons le `iphoneos9.3` SDK installé sur votre ordinateur. Ces informations en place, nous sommes prêts à analyser le projet InfColorPicker `.h` des fichiers dans la première **ApiDefinition.cs** et `StructsAndEnums.cs` pour le projet InfColorPicker.

Entrez la commande suivante dans l’application Terminal :

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Où `[full-path-to-project]` est le chemin d’accès complet au répertoire où le **InfColorPicker** fichier projet Xcode se trouve sur notre ordinateur, et [iphone os] iOS SDK que nous avons installé, comme indiqué par la `sharpie xcode -sdks` commande. Notez que dans cet exemple nous avons passé  **\*.h** en tant que paramètre, qui inclut *tous les* les fichiers d’en-tête dans ce répertoire - normalement vous devraient pas le faire, mais au lieu de cela Lisez attentivement le fichiers d’en-tête pour trouver le niveau supérieur **.h** fichier qui fait référence à tous les autres fichiers appropriés et le passe simplement à objectif Sharpie.

Ce qui suit [sortie](walkthrough-images/os05.png) sera généré dans le terminal :

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

Et le **InfColorPicker.enums.cs** et **InfColorPicker.cs** fichiers seront créés dans notre annuaire :

[![](walkthrough-images/os06.png "Les fichiers InfColorPicker.enums.cs et InfColorPicker.cs")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)


Ouvrez ces deux fichiers dans le projet de liaison que nous avons créé ci-dessus. Copier le contenu de la **InfColorPicker.cs** de fichiers et collez-la dans la **ApiDefinition.cs** fichier, en remplaçant `namespace ...` bloc de code avec le contenu de la  **InfColorPicker.cs** fichier (en laissant le `using` instructions intactes) :

![](walkthrough-images/os07.png "Le fichier InfColorPickerControllerDelegate")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Ouvrez ces deux fichiers dans le projet de liaison que nous avons créé ci-dessus. Copiez le contenu de la **InfColorPicker.cs** fichier (à partir de la **hôte de Build Mac**) et collez-la dans la **ApiDefinition.cs** fichier, en remplaçant `namespace ...` bloc de code avec le contenu de la **InfColorPicker.cs** fichier (en laissant le `using` instructions intactes).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normaliser les définitions d’API

Objectif Sharpie a parfois un problème traduire `Delegates`, de sorte que nous devrons modifier la définition de la `InfColorPickerControllerDelegate` interface et remplacez le `[Protocol, Model]` ligne par le code suivant :

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
Pour que la définition de l’aspect suivant :

[![](walkthrough-images/os11.png "La définition")](walkthrough-images/os11.png#lightbox)

Ensuite, nous faisons la même chose avec le contenu de la `InfColorPicker.enums.cs` fichier, copie et collage dans le `StructsAndEnums.cs` fichier en laissant le `using` instructions intactes :

[![](walkthrough-images/os09.png "Le contenu du StructsAndEnums.cs fichier ")](walkthrough-images/os09.png#lightbox)

Vous pouvez également trouver qu’objectif Sharpie a annoté la liaison avec `[Verify]` attributs. Ces attributs indiquent que vous devez vérifier qu’objectif Sharpie fait la chose correcte en comparant la liaison avec la déclaration d’origine de C/Objective-C (qui vous sont fournie dans un commentaire au-dessus de la déclaration de liaison). Une fois que vous avez vérifié les liaisons, vous devez supprimer l’attribut vérifier. Pour plus d’informations, reportez-vous à la [Vérifiez](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) guide.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)


À ce stade, notre projet de liaison doit être terminé et prêt à générer. Nous allons créer notre projet de liaison et vous assurer que nous avons finalement sans erreurs :

[Générez le projet de liaison et ne vérifiez qu’aucune erreur](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


À ce stade, notre projet de liaison doit être terminé et prêt à générer. Nous allons créer notre projet de liaison et vous assurer que nous avons finalement sans erreurs.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>À l’aide de la liaison

Suivez ces étapes pour créer un exemple d’application iPhone à utiliser iOS que bibliothèque de liaison créé ci-dessus :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. **Créer le projet Xamarin.iOS** -ajouter un nouveau projet Xamarin.iOS appelé **InfColorPickerSample** à la solution, comme indiqué dans les captures d’écran suivante :

    ![](walkthrough-images/use01.png "Ajout d’une application avec affichage unique")

    ![](walkthrough-images/use01a.png "Définition de l’identificateur")

1. **Ajouter une référence au projet de liaison** -mise à jour le **InfColorPickerSample** projet afin qu’il dispose d’une référence à la **InfColorPickerBinding** projet :

    ![](walkthrough-images/use02.png "Ajout de référence au projet de liaison")

1. **Créer l’Interface utilisateur de l’iPhone** -Double cliquez sur le **MainStoryboard.storyboard** de fichiers dans le **InfColorPickerSample** projet pour le modifier dans le concepteur iOS. Ajouter un **bouton** à la vue et appelez-le `ChangeColorButton`, comme illustré dans l’exemple suivant :

    ![](walkthrough-images/use03.png "Ajout d’un bouton à la vue")

1. **Ajouter le InfColorPickerView.xib** -bibliothèque le InfColorPicker Objective-C inclut un **.xib** fichier. Xamarin.iOS n’inclura pas ce **.xib** dans le projet de liaison, ce qui provoquera des erreurs d’exécution dans notre exemple d’application. Pour cela, la solution de contournement consiste à ajouter le **.xib** fichier à notre projet Xamarin.iOS. Sélectionnez projet Xamarin.iOS, avec le bouton droit et sélectionnez **Ajouter > ajouter des fichiers**et ajoutez le **.xib** fichier comme indiqué dans la capture d’écran suivante :

    ![](walkthrough-images/use04.png "Ajouter le InfColorPickerView.xib")

1. Lorsque vous êtes invité, copiez la **.xib** fichier dans le projet.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **Créer le projet Xamarin.iOS** -ajouter un nouveau projet Xamarin.iOS nommé **InfColorPickerSample** à l’aide de la **application avec affichage unique** modèle :

    [![projet d’application (Xamarin) iOS](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Sélectionnez le modèle](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Ajouter une référence au projet de liaison** -mise à jour le **InfColorPickerSample** projet afin qu’il dispose d’une référence à la **InfColorPickerBinding** projet :

    ![](walkthrough-images/use02vs.png "Ajouter une référence au projet de liaison")

1. **Créer l’Interface utilisateur de l’iPhone** -Double cliquez sur le **MainStoryboard.storyboard** de fichiers dans le **InfColorPickerSample** projet pour le modifier dans le concepteur iOS. Ajouter un **bouton** à la vue et appelez-le `ChangeColorButton`, comme illustré dans l’exemple suivant :

    ![](walkthrough-images/use03vs.png "Créer l’Interface utilisateur de l’iPhone")

1. **Ajouter le InfColorPickerView.xib** -bibliothèque le InfColorPicker Objective-C inclut un **.xib** fichier. Xamarin.iOS n’inclura pas ce **.xib** dans le projet de liaison, ce qui provoquera des erreurs d’exécution dans notre exemple d’application. Pour cela, la solution de contournement consiste à ajouter le **.xib** fichier à notre projet Xamarin.iOS à partir de notre **hôte de Build Mac**. Sélectionnez projet Xamarin.iOS, avec le bouton droit et sélectionnez **ajouter** > **élément existant...** et ajoutez le **.xib** fichier.

-----

Ensuite, jetons un œil rapide à des protocoles dans Objective-C et comment les gérer dans la liaison et C# code.

### <a name="protocols-and-xamarinios"></a>Protocoles et Xamarin.iOS

En Objective-C, un protocole définit des méthodes (ou messages) qui peut être utilisé dans certaines circonstances. Conceptuellement, elles sont très similaires aux interfaces dans C#. Une différence majeure entre un protocole Objective-C et un C# interface est que les protocoles peuvent avoir des méthodes facultatives - méthodes une classe n’a pas à implémenter. Objective-C utilise la @optional mot clé est utilisé pour indiquer quelles méthodes sont facultatifs. Pour plus d’informations sur les protocoles, consultez [événements, protocoles et délégués](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** a un tel protocole indiqué dans l’extrait de code ci-dessous :

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Ce protocole est utilisé par **InfColorPickerController** pour informer les clients que l’utilisateur a sélectionné une nouvelle couleur et que le **InfColorPickerController** est terminé. Objectif Sharpie mappé ce protocole, comme indiqué dans l’extrait de code suivant :

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

Lors de la compilation, la bibliothèque de liaison Xamarin.iOS créera une classe de base abstraite appelée `InfColorPickerControllerDelegate`, qui implémente cette interface avec des méthodes virtuelles.

Il existe deux façons que nous pouvons implémenter cette interface dans une application Xamarin.iOS :

- **Fort déléguer** -utilisation d’un délégué fort implique la création d’un C# classe qui sous-classe `InfColorPickerControllerDelegate` et substitue les méthodes appropriées. **InfColorPickerController** utilisera une instance de cette classe pour communiquer avec ses clients.
- **Délégué faible** -un délégué faible est une technique légèrement différente qui implique la création d’une méthode publique sur une classe (tel que `InfColorPickerSampleViewController`) et ensuite exposant cette méthode pour le `InfColorPickerDelegate` de protocole un `Export` attribut.

Délégués forts fournissent Intellisense, la sécurité de type et une meilleure encapsulation. Pour ces raisons, vous devez utiliser des délégués forts dans laquelle vous pouvez, au lieu d’un délégué faible.

Dans cette procédure pas à pas, nous aborderons ces deux techniques : tout d’abord implémenter un délégué fort et puis expliquant comment implémenter un délégué faible.

### <a name="implementing-a-strong-delegate"></a>Implémentation d’un délégué fort

Terminer l’application Xamarin.iOS à l’aide d’un délégué fort pour répondre à la `colorPickerControllerDidFinish:` message :

**Sous-classe InfColorPickerControllerDelegate** -ajouter une nouvelle classe au projet nommé `ColorSelectedDelegate`. Modifiez la classe afin qu’il dispose le code suivant :

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
    public class ColorSelectedDelegate:InfColorPickerControllerDelegate
    {
        readonly UIViewController parent;

        public ColorSelectedDelegate (UIViewController parent)
        {
            this.parent = parent;
        }

        public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
        {
            parent.View.BackgroundColor = controller.ResultColor;
            parent.DismissViewController (false, null);
        }
    }
}
```

Xamarin.iOS liera le délégué Objective-C en créant une classe de base abstraite appelée `InfColorPickerControllerDelegate`. Sous-classe ce type et que vous remplacez le `ColorPickerControllerDidFinish` méthode pour accéder à la valeur de la `ResultColor` propriété du `InfColorPickerController`.

**Créer une instance de ColorSelectedDelegate** -notre gestionnaire d’événements sera besoin d’une instance de la `ColorSelectedDelegate` type que nous avons créé à l’étape précédente. Modifier la classe `InfColorPickerSampleViewController` et ajoutez la variable d’instance suivant à la classe :

```csharp
ColorSelectedDelegate selector;
```

**Initialisez la variable ColorSelectedDelegate** - pour vous assurer que `selector` n’est valide d’une instance, mettre à jour de la méthode `ViewDidLoad` dans `ViewController` pour correspondre à l’extrait de code suivant :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**Implémentez la méthode HandleTouchUpInsideWithStrongDelegate** -ensuite implémenter le Gestionnaire d’événements pour lorsque l’utilisateur touche **ColorChangeButton**. Modifier `ViewController`et ajoutez la méthode suivante :

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

Nous avons d’abord obtenir une instance de `InfColorPickerController` via une méthode statique et assurez-vous d’instance prenant en charge de notre délégué fort via la propriété `InfColorPickerController.Delegate`. Cette propriété a été automatiquement générée pour nous par objectif Sharpie. Enfin, nous appelons `PresentModallyOverViewController` pour afficher la vue `InfColorPickerSampleViewController.xib` afin que l’utilisateur peut sélectionner une couleur.

**Exécutez l’Application** : À ce stade, nous avons terminé avec l’ensemble de notre code. Si vous exécutez l’application, vous pourrez modifier la couleur d’arrière-plan de la `InfColorColorPickerSampleView` comme indiqué dans les captures d’écran suivante :

[![](walkthrough-images/run01.png "Exécution de l’Application")](walkthrough-images/run01.png#lightbox)

Félicitations ! À ce stade, vous avez correctement créé et lié une bibliothèque Objective-C pour une utilisation dans une application Xamarin.iOS. Ensuite, nous allons en savoir plus sur l’utilisation de délégués faibles.

### <a name="implementing-a-weak-delegate"></a>Implémentation d’un délégué faible

Au lieu de sous-classement d’une classe liée au protocole Objective-C pour un délégué particulier, Xamarin.iOS vous permet également d’implémenter les méthodes de protocole dans toute classe qui dérive de `NSObject`, décorant vos méthodes avec la `ExportAttribute`, puis en fournissant les sélecteurs appropriés. Lorsque vous adoptez cette approche, vous affectez une instance de votre classe à la `WeakDelegate` propriété plutôt qu’à la `Delegate` propriété. Un délégué faible vous offre la flexibilité nécessaire pour prendre votre classe de délégué vers le bas d’une hiérarchie d’héritage différents. Nous allons voir comment implémenter et utiliser un délégué faible dans notre application Xamarin.iOS.

**Créez un gestionnaire d’événements pour TouchUpInside** -nous allons créer un nouveau gestionnaire d’événements pour le `TouchUpInside` événements du bouton Modifier la couleur d’arrière-plan. Ce gestionnaire remplira le même rôle que le `HandleTouchUpInsideWithStrongDelegate` gestionnaire que nous avons créé dans la section précédente, mais utilise un délégué faible au lieu d’un délégué fort. Modifier la classe `ViewController`et ajoutez la méthode suivante :

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Mettre à jour ViewDidLoad** -nous devons modifier `ViewDidLoad` afin qu’il utilise le Gestionnaire d’événements que nous venons de créer. Modifier `ViewController` et modifiez `ViewDidLoad` pour ressembler à l’extrait de code suivant :


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Gérer le colorPickerControllerDidFinish : Message** - lorsque le `ViewController` est terminé, iOS enverra le message `colorPickerControllerDidFinish:` à la `WeakDelegate`. Nous devons créer un C# méthode capable de gérer ce message. Pour ce faire, nous créons un C# méthode et puis orner avec le `ExportAttribute`. Modifier `ViewController`et ajoutez la méthode suivante à la classe :

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Exécutez l'application. Il doit maintenant se comportent exactement comme auparavant, mais il est à l’aide d’un délégué faible au lieu du délégué fort. À ce stade vous avez terminé cette procédure pas à pas. Vous devez maintenant avoir une compréhension de la manière de créer et consommer un projet de liaison Xamarin.iOS.

## <a name="summary"></a>Récapitulatif

Cet article a présenté le processus de création et à l’aide d’un projet de liaison Xamarin.iOS. Tout d’abord, nous avons abordé comment compiler une bibliothèque Objective-C existante dans une bibliothèque statique. Ensuite, nous avons abordé comment créer un projet de liaison Xamarin.iOS et comment utiliser objectif Sharpie pour générer les définitions d’API pour la bibliothèque Objective-C. Nous avons abordé la mise à jour et ajuster les définitions API générées pour les adapter à une utilisation publique. Une fois le projet de liaison Xamarin.iOS a été terminé, nous sommes passés à l’utilisation de cette liaison dans une application Xamarin.iOS, en se concentrant sur l’utilisation de délégués forts et faibles délégués.

## <a name="related-links"></a>Liens associés

- [Exemple de liaison (exemple)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Liaison de bibliothèques Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Détails de liaison](~/cross-platform/macios/binding/overview.md)
- [Guide de référence de Types de liaison](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin pour les développeurs Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Règles de conception de .NET Framework](https://msdn.microsoft.com/library/ms229042.aspx)
