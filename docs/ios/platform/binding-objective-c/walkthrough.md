---
title: 'Procédure pas à pas : liaison d’une bibliothèque objective-C iOS'
description: Cet article fournit une procédure pas à pas de création d’une liaison Xamarin. iOS pour une bibliothèque objective-C existante, InfColorPicker. Il aborde des sujets tels que la compilation d’une bibliothèque objective-C statique, sa liaison et l’utilisation de la liaison dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 82367d63d1cc7b52d69de52ee635e380cd68c382
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435653"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Procédure pas à pas : liaison d’une bibliothèque objective-C iOS

> [!IMPORTANT]
> Nous étudions actuellement l’utilisation de la liaison personnalisée sur la plateforme Xamarin. Veuillez suivre [**ce questionnaire**](https://www.surveymonkey.com/r/KKBHNLT) pour informer les futurs efforts de développement.

_Cet article fournit une procédure pas à pas de création d’une liaison Xamarin. iOS pour une bibliothèque objective-C existante, InfColorPicker. Il aborde des sujets tels que la compilation d’une bibliothèque objective-C statique, sa liaison et l’utilisation de la liaison dans une application Xamarin. iOS._

Lorsque vous travaillez sur iOS, vous pouvez rencontrer des cas où vous souhaitez consommer une bibliothèque objective-C tierce. Dans ces situations, vous pouvez utiliser un _projet de liaison_ Xamarin. IOS pour créer une [liaison C#](~/cross-platform/macios/binding/overview.md) qui vous permettra d’utiliser la bibliothèque dans vos applications Xamarin. iOS.

En général, dans l’écosystème iOS, vous pouvez trouver des bibliothèques dans 3 versions :

- En tant que fichier de bibliothèque statique précompilé avec `.a` extension avec son ou ses en-têtes (fichiers. h). Par exemple, [la bibliothèque d’analyse de Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- Comme une infrastructure précompilée. Il s’agit simplement d’un dossier contenant la bibliothèque statique, les en-têtes et parfois des ressources supplémentaires avec l' `.framework` extension. Par exemple, [la bibliothèque AdMob de Google](https://developers.google.com/admob/ios/download).
- En tant que fichiers de code source uniquement. Par exemple, une bibliothèque contenant uniquement `.m` des `.h` fichiers C objectifs et.

Dans le premier et le deuxième scénario, il existe déjà une bibliothèque statique CocoaTouch précompilée. dans cet article, nous allons nous concentrer sur le troisième scénario. N’oubliez pas que, avant de commencer à créer une liaison, vérifiez toujours la licence fournie avec la bibliothèque pour vous assurer que vous êtes libre de la lier.

Cet article fournit une procédure pas à pas de création d’un projet de liaison à l’aide du projet open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) objective-c, mais toutes les informations contenues dans ce guide peuvent être adaptées pour une utilisation avec une bibliothèque objective-c tierce. La bibliothèque InfColorPicker fournit un contrôleur d’affichage réutilisable qui permet à l’utilisateur de sélectionner une couleur en fonction de sa représentation TSL, ce qui rend la sélection des couleurs plus conviviale.

[![Exemple de la bibliothèque InfColorPicker s’exécutant sur iOS](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

Nous allons aborder toutes les étapes nécessaires à l’utilisation de cette API objective-C particulière dans Xamarin. iOS :

- Tout d’abord, nous allons créer une bibliothèque statique objective-C à l’aide de Xcode.
- Nous allons ensuite lier cette bibliothèque statique à Xamarin. iOS.
- Ensuite, montrez de quelle manière objective Sharp peut réduire la charge de travail en générant automatiquement (mais pas toutes) les définitions d’API nécessaires à la liaison Xamarin. iOS.
- Enfin, nous allons créer une application Xamarin. iOS qui utilise la liaison.

L’exemple d’application montre comment utiliser un délégué renforcé pour la communication entre l’API InfColorPicker et notre code C#. Une fois que nous avons vu comment utiliser un délégué fort, nous allons aborder l’utilisation de délégués faibles pour effectuer les mêmes tâches.

## <a name="requirements"></a>Configuration requise

Cet article suppose que vous êtes familiarisé avec Xcode et le langage Objective-C et que vous avez lu notre documentation [objective-c de liaison](~/cross-platform/macios/binding/index.md) . En outre, les éléments suivants sont requis pour effectuer les étapes présentées :

- Le **Kit de développement logiciel (SDK) Xcode et iOS** -le Xcode de Apple et la dernière API iOS doivent être installés et configurés sur l’ordinateur du développeur.
- **[Outils en ligne de commande Xcode](#Installing_the_Xcode_Command_Line_Tools)** : les outils en ligne de commande Xcode doivent être installés pour la version actuellement installée de Xcode (voir ci-dessous pour plus d’informations sur l’installation).
- **Visual Studio pour Mac ou Visual Studio** -la dernière version de Visual Studio pour Mac ou Visual Studio doit être installée et configurée sur l’ordinateur de développement. Un Mac Apple est requis pour le développement d’une application Xamarin. iOS et, lors de l’utilisation de Visual Studio, vous devez être connecté à [un hôte de build Xamarin. iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **La dernière version de objective Sharp** -une copie en cours de l’outil Sharpship objective téléchargée [ici](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Si vous avez déjà installé Sharpy, vous pouvez le mettre à jour vers la version la plus récente à l’aide de la `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"></a>

## <a name="installing-the-xcode-command-line-tools"></a>Installation des outils en ligne de commande Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Comme indiqué ci-dessus, nous allons utiliser les outils en ligne de commande Xcode (en particulier `make` et `lipo` ) dans cette procédure pas à pas. La `make` commande est un utilitaire UNIX très courant qui automatise la compilation des programmes et des bibliothèques exécutables à l’aide d’un _Makefile_ qui spécifie la façon dont le programme doit être généré. La `lipo` commande est un utilitaire de ligne de commande OS X permettant de créer des fichiers à plusieurs architectures. elle combine plusieurs `.a` fichiers dans un seul fichier qui peut être utilisé par toutes les architectures matérielles.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Comme indiqué ci-dessus, nous allons utiliser les outils en ligne de commande XCode sur l' **hôte de build Mac** (et plus particulièrement `make` `lipo` ) dans cette procédure pas à pas. La `make` commande est un utilitaire UNIX très courant qui automatise la compilation des programmes et des bibliothèques exécutables à l’aide d’un _Makefile_ qui spécifie comment générer le programme. La `lipo` commande est un utilitaire de ligne de commande OS X permettant de créer des fichiers à plusieurs architectures. elle combine plusieurs `.a` fichiers dans un seul fichier qui peut être utilisé par toutes les architectures matérielles.

-----

Selon la génération d’Apple [à partir de la ligne de commande avec la documentation du FAQ Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) , dans OS X 10,9 et versions ultérieures, le volet de **Téléchargement** de la boîte de dialogue des **Préférences** de Xcode ne prend plus en charge les outils en ligne de commande de téléchargement.

Pour installer les outils, vous devez utiliser l’une des méthodes suivantes :

- **Installer Xcode** -quand vous installez Xcode, il est fourni avec tous vos outils en ligne de commande. Dans les shims OS X 10,9 (installés dans `/usr/bin` ), peut mapper n’importe quel outil inclus dans `/usr/bin` l’outil correspondant à l’intérieur de Xcode. Par exemple, la `xcrun` commande, qui vous permet de rechercher ou d’exécuter n’importe quel outil à l’intérieur de Xcode à partir de la ligne de commande.
- **Application Terminal** -à partir de l’application Terminal, vous pouvez installer les outils en ligne de commande en exécutant la `xcode-select --install` commande suivante :
  - Démarrez l’application Terminal.
  - Tapez `xcode-select --install` et appuyez sur **entrée**, par exemple :

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - Vous êtes invité à installer les outils en ligne de commande, cliquez sur le bouton **installer** : [ ![ installation des outils en ligne de commande](walkthrough-images/xcode01.png)](walkthrough-images/xcode01.png#lightbox)

  - Les outils sont téléchargés et installés à partir des serveurs Apple : [ ![ téléchargement des outils](walkthrough-images/xcode02.png)](walkthrough-images/xcode02.png#lightbox)

- **Téléchargements pour les développeurs Apple** : le package outils en ligne de commande est disponible dans la page Web [téléchargements pour les développeurs Apple](https://developer.apple.com/downloads/index.action) . Connectez-vous avec votre ID Apple, puis recherchez et téléchargez les outils en ligne de commande : [ ![ recherche des outils en ligne de commande](walkthrough-images/xcode03.png)](walkthrough-images/xcode03.png#lightbox)

Une fois les outils en ligne de commande installés, nous sommes prêts à continuer avec la procédure pas à pas.

## <a name="walkthrough"></a>Procédure pas à pas

Dans cette procédure pas à pas, nous allons aborder les étapes suivantes :

- **[Créer une bibliothèque statique](#Creating_A_Static_Library)** : cette étape implique la création d’une bibliothèque statique du code Objective-C **InfColorPicker** . La bibliothèque statique aura l' `.a` extension de fichier et sera incorporée dans l’assembly .net du projet de bibliothèque.
- **[Créer un projet de liaison Xamarin. iOS](#Create_a_Xamarin.iOS_Binding_Project)** : une fois que nous disposons d’une bibliothèque statique, nous l’utiliserons pour créer un projet de liaison Xamarin. iOS. Le projet de liaison se compose de la bibliothèque statique que nous venons de créer et de métadonnées sous la forme de code C# qui explique comment l’API objective-C peut être utilisée. Ces métadonnées sont communément appelées définitions d’API. Nous allons utiliser la **[précision objective](#Using_Objective_Sharpie)** pour nous aider à créer les définitions d’API.
- **[Normaliser les définitions d’API : l'](#Normalize_the_API_Definitions)** objectif de la finesse est de nous aider, mais cela ne peut pas faire tout. Nous allons aborder certaines modifications que nous devons apporter aux définitions d’API avant de pouvoir les utiliser.
- **[Utilisez la bibliothèque de liaisons](#Using_the_Binding)** -Enfin, nous allons créer une application Xamarin. IOS pour montrer comment utiliser notre projet de liaison nouvellement créé.

Maintenant que nous comprenons les étapes impliquées, passons au reste de la procédure pas à pas.

<a name="Creating_A_Static_Library"></a>

## <a name="creating-a-static-library"></a>Création d’une bibliothèque statique

Si nous examinons le code pour InfColorPicker dans GitHub :

[![Inspecter le code pour InfColorPicker dans GitHub](walkthrough-images/image02.png)](walkthrough-images/image02.png#lightbox)

Nous pouvons voir les trois répertoires suivants dans le projet :

- **InfColorPicker** : ce répertoire contient le code Objective-C pour le projet.
- **PickerSamplePad** : ce répertoire contient un exemple de projet iPad.
- **PickerSamplePhone** : ce répertoire contient un exemple de projet iPhone.

Nous allons télécharger le projet InfColorPicker à partir de [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) et le décompresser dans le répertoire de notre choix. En ouvrant la cible Xcode pour `PickerSamplePhone` Project, nous voyons la structure de projet suivante dans le navigateur Xcode :

[![Structure de projet dans le navigateur Xcode](walkthrough-images/image03.png)](walkthrough-images/image03.png#lightbox)

Ce projet permet de réutiliser le code en ajoutant directement le code source InfColorPicker (dans la zone rouge) dans chaque exemple de projet. Le code de l’exemple de projet se trouve à l’intérieur de la zone bleue. Étant donné que ce projet particulier ne nous fournit pas de bibliothèque statique, il est nécessaire de créer un projet XCode pour compiler la bibliothèque statique.

La première étape consiste à ajouter le code source InfoColorPicker à la bibliothèque statique. Pour ce faire, procédez comme suit :

1. Démarrez Xcode.
2. Dans le menu **fichier** , sélectionnez **nouveau**  >  **projet...**:

    [![Création d’un nouveau projet](walkthrough-images/image04.png)](walkthrough-images/image04.png#lightbox)
3. Sélectionnez **Framework & bibliothèque**, le modèle de **bibliothèque statique cacao** et cliquez sur le bouton **suivant** :

    [![Sélectionner le modèle de bibliothèque statique cacao tactile](walkthrough-images/image05.png)](walkthrough-images/image05.png#lightbox)

4. Entrez `InfColorPicker` pour le **nom du projet** et cliquez sur le bouton **suivant** :

    [![Entrez InfColorPicker pour le nom du projet](walkthrough-images/image06.png)](walkthrough-images/image06.png#lightbox)
5. Sélectionnez un emplacement pour enregistrer le projet et cliquez sur le bouton **OK** .
6. Nous devons à présent ajouter la source du projet InfColorPicker à notre projet de bibliothèque statique. Étant donné que le fichier **InfColorPicker. h** existe déjà dans notre bibliothèque statique (par défaut), Xcode ne nous autorisera pas à le remplacer. Dans le **Finder**, accédez au code source InfColorPicker dans le projet d’origine que nous avons décompressé à partir de GitHub, copiez tous les fichiers InfColorPicker et collez-les dans notre nouveau projet de bibliothèque statique :

    [![Copier tous les fichiers InfColorPicker](walkthrough-images/image12.png)](walkthrough-images/image12.png#lightbox)

7. Revenez à Xcode, cliquez avec le bouton droit sur le dossier **InfColorPicker** , puis sélectionnez **Ajouter des fichiers à « InfColorPicker... »**:

    [![Ajout de fichiers](walkthrough-images/image08.png)](walkthrough-images/image08.png#lightbox)

8. Dans la boîte de dialogue Ajouter des fichiers, accédez aux fichiers de code source InfColorPicker que nous venons de copier, sélectionnez-les tous, puis cliquez sur le bouton **Ajouter** :

    [![Sélectionnez tout, puis cliquez sur le bouton Ajouter](walkthrough-images/image09.png)](walkthrough-images/image09.png#lightbox)

9. Le code source sera copié dans notre projet :

    [![Le code source sera copié dans le projet](walkthrough-images/image10.png)](walkthrough-images/image10.png#lightbox)

10. Dans le navigateur de projet Xcode, sélectionnez le fichier **InfColorPicker. m** et commentez les deux dernières lignes (en raison de la façon dont cette bibliothèque a été écrite, ce fichier n’est pas utilisé) :

    [![Modification du fichier InfColorPicker. m](walkthrough-images/image14.png)](walkthrough-images/image14.png#lightbox)

11. Nous devons maintenant vérifier s’il existe des frameworks requis par la bibliothèque. Vous pouvez trouver ces informations dans le fichier Lisez-moi ou en ouvrant l’un des exemples de projets fournis. Cet exemple utilise `Foundation.framework` , `UIKit.framework` , et `CoreGraphics.framework` Ajoutons-y.

12. Sélectionnez la **cible InfColorPicker > phases de build** , puis développez la section **lier le fichier binaire avec les bibliothèques** :

    [![Développez la section lien binaire avec bibliothèques](walkthrough-images/image16b.png)](walkthrough-images/image16b.png#lightbox)

13. Utilisez le **+** bouton pour ouvrir la boîte de dialogue qui vous permet d’ajouter les infrastructures de frames requises listées ci-dessus :

    [![Ajoutez les frameworks de frames requis listés ci-dessus](walkthrough-images/image16c.png)](walkthrough-images/image16c.png#lightbox)

14. La section **Link Binary with Libraries** doit maintenant ressembler à l’image ci-dessous :

    [![Section de liaison Binary with Libraries](walkthrough-images/image16d.png)](walkthrough-images/image16d.png#lightbox)

À ce stade, nous sommes proches, mais ce n’est pas tout à fait. La bibliothèque statique a été créée, mais nous devons la créer pour créer un fichier binaire Fat qui inclut toutes les architectures requises pour l’appareil iOS et le simulateur iOS.

### <a name="creating-a-fat-binary"></a>Création d’un binaire FAT

Tous les appareils iOS ont des processeurs alimentés par une architecture ARM qui ont évolué au fil du temps. Chaque nouvelle architecture a ajouté de nouvelles instructions et d’autres améliorations tout en continuant à assurer la compatibilité descendante. les appareils iOS possèdent des jeux d’instructions ARMv6, ARMv7, armv7s, arm64, même si [ARMv6 n’est plus utilisé](~/ios/deploy-test/compiling-for-different-devices.md). Le simulateur iOS n’est pas alimenté par ARM et est à la place un simulateur Powered x86 et x86_64. Cela signifie que les bibliothèques doivent être fournies pour chaque ensemble d’instructions.

Une bibliothèque FAT est un `.a` fichier contenant toutes les architectures prises en charge.

La création d’un binaire FAT est un processus en trois étapes :

- Compilez une version ARM 7 & ARM64 de la bibliothèque statique.
- Compilez une version x86 et x84_64 de la bibliothèque statique.
- Utilisez l' `lipo` outil en ligne de commande pour combiner les deux bibliothèques statiques en une seule.

Bien que ces trois étapes soient assez simples, il peut être nécessaire de les répéter à l’avenir lorsque la bibliothèque objective-C reçoit des mises à jour ou que des correctifs de bogue sont requis. Si vous décidez d’automatiser ces étapes, Cela simplifiera la maintenance et la prise en charge futures du projet de liaison iOS.

De nombreux outils sont disponibles pour automatiser ces tâches : un script shell, [Rake](https://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)et Make. Lorsque les outils en ligne de commande Xcode sont installés, `make` est également installé, de sorte que est le système de génération qui sera utilisé pour cette procédure pas à pas. Voici un **Makefile** que vous pouvez utiliser pour créer une bibliothèque partagée multi-architecture qui fonctionnera sur un appareil iOS et le simulateur pour une bibliothèque :

<!--markdownlint-disable MD010 -->
```makefile
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
<!--markdownlint-enable MD010 -->

Entrez les commandes du **Makefile** dans l’éditeur de texte brut de votre choix et mettez à jour les sections avec **votre-Project-Name** avec le nom de votre projet. Il est également important de s’assurer que vous collez les instructions ci-dessus exactement, en préservant les onglets dans les instructions.

Enregistrez le fichier avec le nom **Makefile** au même emplacement que la bibliothèque statique Xcode InfColorPicker que nous avons créée ci-dessus :

[![Enregistrez le fichier avec le nom Makefile](walkthrough-images/lib00.png)](walkthrough-images/lib00.png#lightbox)

Ouvrez l’application Terminal sur votre Mac et accédez à l’emplacement de votre Makefile. Tapez `make` dans le terminal, appuyez sur **entrée** pour exécuter le **Makefile** :

[![Exemple de sortie de Makefile](walkthrough-images/lib01.png)](walkthrough-images/lib01.png#lightbox)

Lorsque vous exécutez make, vous verrez beaucoup de texte défilant. Si tout fonctionne correctement, vous verrez les mots **génération réussie** et le `libInfColorPicker-armv7.a` , `libInfColorPicker-i386.a` et les `libInfColorPickerSDK.a` fichiers seront copiés dans le même emplacement que le **Makefile**:

[![Les fichiers libInfColorPicker-ARMv7. a, libInfColorPicker-i386. a et libInfColorPickerSDK. a générés par le Makefile](walkthrough-images/lib02.png)](walkthrough-images/lib02.png#lightbox)

Vous pouvez confirmer les architectures dans votre fichier binaire Fat à l’aide de la commande suivante :

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Les éléments suivants doivent s’afficher :

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

À ce stade, nous avons terminé la première étape de notre liaison iOS en créant une bibliothèque statique à l’aide de Xcode et des outils en ligne de commande Xcode `make` et `lipo` . Passons à l’étape suivante et utilisons **objective-Sharp** pour automatiser la création des liaisons d’API pour nous.

<a name="Create_a_Xamarin.iOS_Binding_Project"></a>

## <a name="create-a-xamarinios-binding-project"></a>Créer un projet de liaison Xamarin. iOS

Avant de pouvoir utiliser **objective-Sharp** pour automatiser le processus de liaison, nous devons créer un projet de liaison Xamarin. IOS pour héberger les définitions d’API (nous allons utiliser **objective-sharpus** pour nous aider à générer) et créer la liaison C# pour nous.

Procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez Visual Studio pour Mac.
1. Dans le menu **fichier** , sélectionnez **nouvelle**  >  **solution...**:

    ![Démarrage d’une nouvelle solution](walkthrough-images/bind01.png)

1. Dans la boîte de dialogue nouvelle solution, sélectionnez **bibliothèque**  >  **iOS de liaison du projet**:

    ![Sélectionner un projet de liaison iOS](walkthrough-images/bind02.png)

1. Cliquez sur le bouton **Suivant**.

1. Entrez « InfColorPickerBinding » comme **nom de projet** , puis cliquez sur le bouton **créer** pour créer la solution :

    ![Entrez InfColorPickerBinding comme nom de projet](walkthrough-images/bind02a.png)

La solution est créée et deux fichiers par défaut sont inclus :

![Structure de la solution dans le Explorateur de solutions](walkthrough-images/bind03.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Démarrez Visual Studio.

1. Dans le menu **fichier** , sélectionnez **nouveau**  >  **projet...**:

    ![Création d’un nouveau projet](walkthrough-images/bind01vs.png "Création d’un nouveau projet")

1. Dans la boîte de dialogue Nouveau projet, sélectionnez **Visual C# > iPhone & iPad > bibliothèque de liaisons iOS (Xamarin)**:

    [![Sélectionner la bibliothèque de liaisons iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Entrez « InfColorPickerBinding » comme **nom** et cliquez sur le bouton **OK** pour créer la solution.

La solution est créée et deux fichiers par défaut sont inclus :

![Structure de la solution dans le Explorateur de solutions](walkthrough-images/bind03vs.png)

-----

- **ApiDefinition.cs** : ce fichier contient les contrats qui définissent la façon dont les API objective-C seront encapsulées en C#.
- **Structs.cs** : ce fichier contiendra toutes les structures ou valeurs d’énumération requises par les interfaces et les délégués.

Nous allons utiliser ces deux fichiers ultérieurement dans la procédure pas à pas. Tout d’abord, nous devons ajouter la bibliothèque InfColorPicker au projet de liaison.

### <a name="including-the-static-library-in-the-binding-project"></a>Inclusion de la bibliothèque statique dans le projet de liaison

Maintenant que notre projet de liaison de base est prêt, nous devons ajouter la bibliothèque binaire Fat que nous avons créée ci-dessus pour la bibliothèque **InfColorPicker** .

Pour ajouter la bibliothèque, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Cliquez avec le bouton droit sur le dossier **références natives** dans le panneau solutions, puis sélectionnez **Ajouter des références natives**:

    ![Ajouter des références natives](walkthrough-images/bind04a.png)

1. Accédez au fichier binaire Fat que nous avons créé précédemment ( `libInfColorPickerSDK.a` ) et appuyez sur le bouton **ouvrir** :

    ![Sélectionnez le fichier libInfColorPickerSDK. a](walkthrough-images/bind05.png)
1. Le fichier sera inclus dans le projet :

    ![Inclusion d’un fichier](walkthrough-images/bind04.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Copiez `libInfColorPickerSDK.a` à partir de votre **hôte de build Mac** et collez-le dans votre projet de liaison.

1. Cliquez avec le bouton droit sur le projet et choisissez **ajouter > élément existant...**:

    ![Ajout d’un fichier existant](walkthrough-images/bind04vs.png)

1. Accédez au `libInfColorPickerSDK.a` et appuyez sur le bouton **Ajouter** :

    ![Ajout de libInfColorPickerSDK. a](walkthrough-images/bind05vs.png)

1. Le fichier sera inclus dans le projet.

-----

Quand le fichier **. a** est ajouté au projet, Xamarin. iOS définit automatiquement l’action de **génération** du fichier sur **ObjcBindingNativeLibrary**et crée un fichier spécial appelé `libInfColorPickerSDK.linkwith.cs` .

Ce fichier contient l' `LinkWith` attribut qui indique à Xamarin. iOS comment gérer la bibliothèque statique que nous venons d’ajouter. Le contenu de ce fichier est illustré dans l’extrait de code suivant :

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

L' `LinkWith` attribut identifie la bibliothèque statique pour le projet et certains indicateurs de l’éditeur de liens importants.

Nous devons ensuite créer les définitions d’API pour le projet InfColorPicker. Dans le cadre de cette procédure pas à pas, nous allons utiliser objective Sharp pour générer le fichier **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"></a>

## <a name="using-objective-sharpie"></a>Utilisation de la finesse objective

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

La finesse d’objectif est un outil en ligne de commande (fourni par Xamarin) qui peut aider à créer les définitions requises pour lier une bibliothèque objective-C tierce à C#. Dans cette section, nous allons utiliser objective Sharp pour créer le **ApiDefinition.cs** initial pour le projet InfColorPicker.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

La finesse d’objectif est un outil en ligne de commande (fourni par Xamarin) qui peut aider à créer les définitions requises pour lier une bibliothèque objective-C tierce à C#. Dans cette section, nous allons utiliser objective Sharp sur notre **hôte de build Mac** pour créer le **ApiDefinition.cs** initial pour le projet InfColorPicker.

-----

Pour commencer, nous allons télécharger le fichier d’installation objective Sharpy comme détaillé dans ce [Guide](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Exécutez le programme d’installation et suivez toutes les invites à l’écran de l’Assistant Installation pour installer objective Sharp sur notre ordinateur de développement.

Une fois l’installation de l’application de la netteté objective terminée, commençons l’application Terminal et entrez la commande suivante pour obtenir de l’aide sur tous les outils qu’elle fournit pour faciliter la liaison :

```bash
sharpie -help
```

Si nous exécutons la commande ci-dessus, la sortie suivante sera générée :

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

Dans le cadre de cette procédure pas à pas, nous utiliserons les outils de finesse d’objectif suivants :

- **Xcode** : cet outil fournit des informations sur l’installation de Xcode actuelle et sur les versions des API iOS et Mac que nous avons installées. Nous utiliserons ces informations ultérieurement lors de la génération de nos liaisons.
- **Bind** : nous utiliserons cet outil pour analyser les fichiers **. h** du projet InfColorPicker dans les fichiers **ApiDefinition.cs** et **StructsAndEnums.cs** initiaux.

Pour obtenir de l’aide sur un outil précis, entrez le nom de l’outil et l' `-help` option. Par exemple, `sharpie xcode -help` retourne la sortie suivante :

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, -help           Show detailed help
  -v, -verbose        Be verbose with output

Xcode Options:
  -sdks               List all available Xcode SDKs. Pass -verbose for more
                        details.
  -sdkpath SDK        Output the path of the SDK
  -frameworks SDK     List all available framework directories in a given SDK.
```

Avant de pouvoir démarrer le processus de liaison, nous devons obtenir des informations sur nos kits de développement logiciel (SDK) actuellement installés en entrant la commande suivante dans le terminal `sharpie xcode -sdks` :

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

À partir de ce qui précède, nous pouvons voir que le `iphoneos9.3` Kit de développement logiciel (SDK) est installé sur notre machine. Une fois ces informations en place, nous sommes prêts à analyser les fichiers de projet InfColorPicker `.h` dans les **ApiDefinition.cs** initiaux et `StructsAndEnums.cs` pour le projet InfColorPicker.

Entrez la commande suivante dans l’application Terminal :

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] -scope [full-path-to-project]/InfColorPicker/InfColorPicker [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Où `[full-path-to-project]` est le chemin d’accès complet au répertoire où se trouve le fichier de projet XCode **InfColorPicker** sur notre ordinateur, et [iPhone-OS] est le kit de développement logiciel (SDK) iOS que nous avons installé, comme indiqué par la `sharpie xcode -sdks` commande. Notez que, dans cet exemple, nous avons passé ** \* . h** comme paramètre, qui comprend *tous* les fichiers d’en-tête dans ce répertoire. normalement, vous ne devez pas le faire, mais lisez attentivement les fichiers d’en-tête pour rechercher le fichier **. h** de niveau supérieur qui fait référence à tous les autres fichiers pertinents, et transmettez-le simplement à objective Sharp. 

> [!TIP] 
> Pour l' `-scope` argument, transmettez le dossier qui contient les en-têtes que vous souhaitez lier. Sans l' `-scope` argument, objective Sharp tente de générer des liaisons pour tous les en-têtes du kit de développement logiciel (SDK) iOS importés, par exemple, ce qui `#import <UIKit.h>` génère un fichier de définitions volumineux qui générera probablement des erreurs lors de la compilation du projet de liaison. Avec le `-scope` jeu d’arguments, objective Sharp ne génère pas de liaisons pour tous les en-têtes en dehors du dossier étendu. 

La [sortie](walkthrough-images/os05.png) suivante sera générée dans le terminal :

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

Les fichiers **InfColorPicker.enums.cs** et **InfColorPicker.cs** seront créés dans le répertoire suivant :

[![Fichiers InfColorPicker.enums.cs et InfColorPicker.cs](walkthrough-images/os06.png)](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Ouvrez ces deux fichiers dans le projet de liaison que nous avons créé ci-dessus. Copiez le contenu du fichier **InfColorPicker.cs** et collez-le dans le fichier **ApiDefinition.cs** , en remplaçant le `namespace ...` bloc de code existant par le contenu du fichier **InfColorPicker.cs** (en laissant `using` intactes les instructions) :

![Fichier InfColorPickerControllerDelegate](walkthrough-images/os07.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Ouvrez ces deux fichiers dans le projet de liaison que nous avons créé ci-dessus. Copiez le contenu du fichier **InfColorPicker.cs** (à partir de l' **hôte de build Mac**) et collez-le dans le fichier **ApiDefinition.cs** , en remplaçant le `namespace ...` bloc de code existant par le contenu du fichier **InfColorPicker.cs** (en laissant `using` intactes les instructions).

-----

<a name="Normalize_the_API_Definitions"></a>

## <a name="normalize-the-api-definitions"></a>Normaliser les définitions d’API

Bien qu’il y ait parfois un problème de traduction `Delegates` , nous devons modifier la définition de l' `InfColorPickerControllerDelegate` interface et remplacer la `[Protocol, Model]` ligne par ce qui suit :

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

Pour que la définition ressemble à ceci :

[![La définition](walkthrough-images/os11.png)](walkthrough-images/os11.png#lightbox)

Ensuite, nous faisons la même chose avec le contenu du `InfColorPicker.enums.cs` fichier, en le copiant et en le collant dans le `StructsAndEnums.cs` fichier en laissant les `using` instructions intactes :

[![Le contenu du fichier StructsAndEnums.cs ](walkthrough-images/os09.png)](walkthrough-images/os09.png#lightbox)

Vous pouvez également constater que objective Sharp a annoté la liaison avec des `[Verify]` attributs. Ces attributs indiquent que vous devez vérifier que la netteté objective a effectué la bonne chose en comparant la liaison avec la déclaration d’origine C/Objective-C (qui sera fournie dans un commentaire au-dessus de la déclaration liée). Une fois que vous avez vérifié les liaisons, vous devez supprimer l’attribut Verify. Pour plus d’informations, reportez-vous au Guide de [vérification](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

À ce stade, notre projet de liaison doit être terminé et prêt à être généré. Nous allons créer notre projet de liaison et nous assurer que nous sommes terminés sans erreur :

[Générez le projet de liaison et assurez-vous qu’il n’y a pas d’erreurs](walkthrough-images/os12.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

À ce stade, notre projet de liaison doit être terminé et prêt à être généré. Nous allons créer notre projet de liaison et nous assurer que nous sommes terminés sans erreurs.

-----

<a name="Using_the_Binding"></a>

## <a name="using-the-binding"></a>Utilisation de la liaison

Procédez comme suit pour créer un exemple d’application iPhone pour utiliser la bibliothèque de liaisons iOS créée ci-dessus :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. **Créer un projet Xamarin. iOS** : ajoutez un nouveau projet Xamarin. iOS nommé **InfColorPickerSample** à la solution, comme illustré dans les captures d’écran suivantes :

    ![Ajout d’une application de vue unique](walkthrough-images/use01.png)

    ![Définition de l’identificateur](walkthrough-images/use01a.png)

1. **Ajoutez une référence au** projet de liaison-mettez à jour le projet **InfColorPickerSample** afin qu’il dispose d’une référence au projet **InfColorPickerBinding** :

    ![Ajout d’une référence au projet de liaison](walkthrough-images/use02.png)

1. **Créez l’interface utilisateur iPhone** -double-cliquez sur le fichier **fichier mainstoryboard. Storyboard** dans le projet **InfColorPickerSample** pour le modifier dans le concepteur iOS. Ajoutez un **bouton** à la vue et appelez-le `ChangeColorButton` , comme illustré ci-dessous :

    ![Ajout d’un bouton à la vue](walkthrough-images/use03.png)

1. **Ajoutez InfColorPickerView. XIB** -la bibliothèque InfColorPicker objective-C contient un fichier **. XIB** . Xamarin. iOS n’inclura pas This **. XIB** dans le projet de liaison, ce qui entraînera des erreurs d’exécution dans notre exemple d’application. Pour contourner ce problème, ajoutez le fichier **. XIB** à notre projet Xamarin. iOS. Sélectionnez le projet Xamarin. iOS, cliquez avec le bouton droit et sélectionnez **ajouter > ajouter des fichiers**, puis ajoutez le fichier **. XIB** comme indiqué dans la capture d’écran suivante :

    ![Ajoutez InfColorPickerView. XIB](walkthrough-images/use04.png)

1. Lorsque vous y êtes invité, copiez le fichier **. XIB** dans le projet.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. **Créer un projet Xamarin. iOS** -ajoutez un nouveau projet Xamarin. iOS nommé **InfColorPickerSample** à l’aide du modèle d' **application vue unique** :

    [![projet d’application iOS (Xamarin)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Sélectionner un modèle](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Ajoutez une référence au** projet de liaison-mettez à jour le projet **InfColorPickerSample** afin qu’il dispose d’une référence au projet **InfColorPickerBinding** :

    ![Ajouter une référence au projet de liaison](walkthrough-images/use02vs.png)

1. **Créez l’interface utilisateur iPhone** -double-cliquez sur le fichier **fichier mainstoryboard. Storyboard** dans le projet **InfColorPickerSample** pour le modifier dans le concepteur iOS. Ajoutez un **bouton** à la vue et appelez-le `ChangeColorButton` , comme illustré ci-dessous :

    ![Créer l’interface utilisateur iPhone](walkthrough-images/use03vs.png)

1. **Ajoutez InfColorPickerView. XIB** -la bibliothèque InfColorPicker objective-C contient un fichier **. XIB** . Xamarin. iOS n’inclura pas This **. XIB** dans le projet de liaison, ce qui entraînera des erreurs d’exécution dans notre exemple d’application. Pour contourner ce problème, ajoutez le fichier **. XIB** à notre projet Xamarin. IOS à partir de notre **hôte de build Mac**. Sélectionnez le projet Xamarin. iOS, cliquez avec le bouton droit et sélectionnez **Ajouter**  >  un**élément existant...**, puis ajoutez le fichier **. XIB** .

-----

À présent, examinons rapidement les protocoles en Objective-C et comment nous les manipulons dans le code de liaison et C#.

### <a name="protocols-and-xamarinios"></a>Protocoles et Xamarin. iOS

En Objective-C, un protocole définit des méthodes (ou des messages) qui peuvent être utilisées dans certaines circonstances. Conceptuellement, elles sont très similaires aux interfaces en C#. L’une des principales différences entre un protocole objective-C et une interface C# est que les protocoles peuvent avoir des méthodes facultatives-méthodes qu’une classe n’a pas à implémenter. Objective-C utilise le @optional mot clé pour indiquer les méthodes facultatives. Pour plus d’informations sur les protocoles [, consultez événements, protocoles et délégués](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** possède un protocole de ce type, illustré dans l’extrait de code ci-dessous :

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Ce protocole est utilisé par **InfColorPickerController** pour informer les clients que l’utilisateur a choisi une nouvelle couleur et que le **InfColorPickerController** est terminé. La netteté objective a mappé ce protocole comme indiqué dans l’extrait de code suivant :

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

Lorsque la bibliothèque de liaisons est compilée, Xamarin. iOS crée une classe de base abstraite appelée `InfColorPickerControllerDelegate` , qui implémente cette interface avec des méthodes virtuelles.

Il existe deux façons d’implémenter cette interface dans une application Xamarin. iOS :

- **Délégué fort** : l’utilisation d’un délégué fort implique la création d’une classe C# qui sous-classe `InfColorPickerControllerDelegate` et substitue les méthodes appropriées. **InfColorPickerController** utilise une instance de cette classe pour communiquer avec ses clients.
- **Délégué faible** : un délégué faible est une technique légèrement différente qui implique la création d’une méthode publique sur une classe (telle que `InfColorPickerSampleViewController` ), puis l’exposition de cette méthode au `InfColorPickerDelegate` protocole via un `Export` attribut.

Les délégués forts fournissent IntelliSense, la sécurité de type et une meilleure encapsulation. Pour ces raisons, vous devez utiliser des délégués forts là où vous pouvez, au lieu d’un délégué faible.

Dans cette procédure pas à pas, nous allons aborder les deux techniques : en implémentant d’abord un délégué fort, puis en expliquant comment implémenter un délégué faible.

### <a name="implementing-a-strong-delegate"></a>Implémentation d’un délégué fort

Terminez l’application Xamarin. iOS à l’aide d’un délégué fort pour répondre au `colorPickerControllerDidFinish:` message :

**Sous-classe InfColorPickerControllerDelegate** -ajoutez une nouvelle classe au projet appelé `ColorSelectedDelegate` . Modifiez la classe afin qu’elle dispose du code suivant :

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

Xamarin. iOS lie le délégué objective-C en créant une classe de base abstraite appelée `InfColorPickerControllerDelegate` . Sous-classez ce type et substituez la `ColorPickerControllerDidFinish` méthode pour accéder à la valeur de la `ResultColor` propriété de `InfColorPickerController` .

**Créez une instance de ColorSelectedDelegate** -notre gestionnaire d’événements aura besoin d’une instance du `ColorSelectedDelegate` type que nous avons créé à l’étape précédente. Modifiez la classe `InfColorPickerSampleViewController` et ajoutez la variable d’instance suivante à la classe :

```csharp
ColorSelectedDelegate selector;
```

**Initialisez la variable ColorSelectedDelegate** -pour vous assurer qu' `selector` il s’agit d’une instance valide, mettez à jour la méthode `ViewDidLoad` dans `ViewController` afin qu’elle corresponde à l’extrait de code suivant :

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**Implémentez la méthode HandleTouchUpInsideWithStrongDelegate** . ensuite, implémentez le gestionnaire d’événements pour lorsque l’utilisateur touche **ColorChangeButton**. Modifiez `ViewController` et ajoutez la méthode suivante :

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

Nous obtenons d’abord une instance de `InfColorPickerController` par le biais d’une méthode statique et indiquons que cette instance connaît notre délégué fort via la propriété `InfColorPickerController.Delegate` . Cette propriété a été générée automatiquement pour nous par la netteté objective. Enfin, nous appelons `PresentModallyOverViewController` pour afficher la vue `InfColorPickerSampleViewController.xib` afin que l’utilisateur puisse sélectionner une couleur.

**Exécuter l’application** : à ce stade, nous avons terminé avec l’ensemble de notre code. Si vous exécutez l’application, vous devez être en mesure de modifier la couleur d’arrière-plan du `InfColorColorPickerSampleView` comme indiqué dans les captures d’écran suivantes :

[![Exécution de l’application](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

Félicitations ! À ce stade, vous avez créé et lié avec succès une bibliothèque objective-C à utiliser dans une application Xamarin. iOS. Voyons ensuite comment utiliser des délégués faibles.

### <a name="implementing-a-weak-delegate"></a>Implémentation d’un délégué faible

Au lieu de sous-classer une classe liée au protocole objective-C pour un délégué particulier, Xamarin. iOS vous permet également d’implémenter les méthodes de protocole dans toute classe qui dérive de `NSObject` , en décorant vos méthodes avec, puis en `ExportAttribute` fournissant les sélecteurs appropriés. Lorsque vous adoptez cette approche, vous assignez une instance de votre classe à la `WeakDelegate` propriété plutôt qu’à la `Delegate` propriété. Un délégué faible vous offre la possibilité de mettre votre classe déléguée dans une hiérarchie d’héritage différente. Voyons comment implémenter et utiliser un délégué faible dans notre application Xamarin. iOS.

**Créer un gestionnaire d’événements pour TouchUpInside** : créons un nouveau gestionnaire d’événements pour l' `TouchUpInside` événement du bouton modifier la couleur d’arrière-plan. Ce gestionnaire remplit le même rôle que le `HandleTouchUpInsideWithStrongDelegate` Gestionnaire que nous avons créé dans la section précédente, mais il utilise un délégué faible au lieu d’un délégué fort. Modifiez la classe `ViewController` et ajoutez la méthode suivante :

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**Mettez à jour ViewDidLoad** -nous devons changer `ViewDidLoad` afin qu’il utilise le gestionnaire d’événements que nous venons de créer. Modifiez `ViewController` et modifiez `ViewDidLoad` pour ressembler à l’extrait de code suivant :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Gérer le colorPickerControllerDidFinish : message :** lorsque la `ViewController` est terminée, iOS envoie le message `colorPickerControllerDidFinish:` au `WeakDelegate` . Nous devons créer une méthode C# capable de gérer ce message. Pour ce faire, nous créons une méthode C#, puis l’orneons avec `ExportAttribute` . Modifiez `ViewController` et ajoutez la méthode suivante à la classe :

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Exécutez l'application. Elle doit maintenant se comporter exactement comme avant, mais elle utilise un délégué faible au lieu du délégué fort. À ce stade, vous avez terminé cette procédure pas à pas. Vous devez maintenant comprendre comment créer et utiliser un projet de liaison Xamarin. iOS.

## <a name="summary"></a>Récapitulatif

Cet article a parcouru le processus de création et d’utilisation d’un projet de liaison Xamarin. iOS. Tout d’abord, nous avons abordé la compilation d’une bibliothèque objective-C existante dans une bibliothèque statique. Nous avons ensuite abordé la création d’un projet de liaison Xamarin. iOS et l’utilisation d’objective Sharp pour générer les définitions d’API pour la bibliothèque objective-C. Nous avons abordé la manière de mettre à jour et de modifier les définitions d’API générées afin qu’elles soient adaptées à la consommation publique. Une fois le projet de liaison Xamarin. iOS terminé, nous avons passé à consommer cette liaison dans une application Xamarin. iOS, en se concentrant sur l’utilisation de délégués forts et de délégués faibles.

## <a name="related-links"></a>Liens associés

- [Liaison de bibliothèques Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Détails de la liaison](~/cross-platform/macios/binding/overview.md)
- [Guide de référence des types de liaison](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin pour les développeurs objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Directives de conception d’infrastructure](/dotnet/standard/design-guidelines/)