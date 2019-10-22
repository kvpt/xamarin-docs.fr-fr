---
title: Extensions iOS dans Xamarin. iOS
description: Ce document décrit les extensions, qui sont des widgets présentés par iOS dans un contexte standard, par exemple dans le centre de notification. Il explique comment créer une extension et communiquer avec elle à partir de l’application parente.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 5995ba06873b2fb5f75c593fbc7136806e50d982
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70290600"
---
# <a name="ios-extensions-in-xamarinios"></a>extensions iOS dans Xamarin. iOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Création d’extensions dans une vidéo iOS**

Les extensions, telles qu’introduites dans iOS 8, sont des `UIViewControllers` spécialisées présentées par iOS dans des contextes standard tels que dans le **Centre de notifications**, lorsque les types de clavier personnalisés demandés par l’utilisateur exécutent une entrée spécialisée ou d’autres contextes tels que modification d’une photo dans laquelle l’extension peut fournir des filtres d’effet spéciaux.

Toutes les extensions sont installées conjointement avec une application conteneur (avec les deux éléments écrits à l’aide des API unifiées 64 bits) et sont activées à partir d’un point d’extension particulier dans une application hôte. Et dans la mesure où elles seront utilisées comme suppléments aux fonctions système existantes, elles doivent être hautes performances, épurées et robustes. 

## <a name="extension-points"></a>Points d’extension

|Tapez|Description|Point d’extension|Application hôte|
|--- |--- |--- |--- |
|Action|Éditeur ou visionneuse spécialisé pour un type de média particulier|`com.apple.ui-services`|Any|
|Fournisseur de documents|Permet à l’application d’utiliser un magasin de documents distant|`com.apple.fileprovider-ui`|Applications utilisant un [UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|Clavier|Autres claviers|`com.apple.keyboard-service`|Any|
|Édition photo|Manipulation et modification de photos|`com.apple.photo-editing`|Éditeur de photos. app|
|Partager|Partage des données avec les réseaux sociaux, les services de messagerie, etc.|`com.apple.share-services`|Any|
|Aujourd'hui|« Widgets » apparaissant sur l’écran Today ou le centre de notifications|`com.apple.widget-extensions`|Aujourd’hui et le centre de notifications|

[Des points d’extension supplémentaires](~/ios/platform/introduction-to-ios10/index.md#app-extensions) ont été ajoutés dans iOS 10.

## <a name="limitations"></a>Limitations

Les extensions présentent un certain nombre de limitations, dont certaines sont universelles pour tous les types (par exemple, aucun type d’extension ne peut accéder aux appareils photo ou aux micros), tandis que les autres types d’extension peuvent avoir des limitations spécifiques à leur utilisation (par exemple, les claviers personnalisés ne peut pas être utilisé pour les champs d’entrée de données sécurisés, tels que pour les mots de passe). 

Les limitations universelles sont les suivantes :

- Les infrastructures d' [interface utilisateur](~/ios/platform/eventkit.md) du kit d' [intégrité](~/ios/platform/healthkit.md) et du kit d’événements ne sont pas disponibles
- Les extensions ne peuvent pas utiliser les [modes d’arrière-plan étendus](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- Les extensions ne peuvent pas accéder aux appareils photo ou aux microphones de l’appareil (même s’ils peuvent accéder à des fichiers multimédias existants)
- Les extensions ne peuvent pas recevoir de données de dépôt d’air (bien qu’elles puissent transmettre des données via l’air goutte)
- [UIActionSheet](xref:UIKit.UIActionSheet) et [UIAlertView](xref:UIKit.UIAlertView) ne sont pas disponibles ; les extensions doivent utiliser [UIAlertController](xref:UIKit.UIAlertController)
- Plusieurs membres de [UIApplication](xref:UIKit.UIApplication) sont indisponibles : [UIApplication. SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication. OpenURL](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication. BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) et [ UIApplication. EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS applique une limite d’utilisation de la mémoire de 16 Mo sur les extensions actuelles.
- Par défaut, les extensions du clavier n’ont pas accès au réseau. Cela affecte le débogage sur l’appareil (la restriction n’est pas appliquée dans le simulateur), car Xamarin. iOS nécessite un accès réseau pour que le débogage fonctionne. Il est possible de demander l’accès au réseau en définissant la valeur `Requests Open Access` dans le fichier info. plist du projet sur `Yes`. Pour plus d’informations sur les limitations d’extension du clavier, consultez le [Guide du clavier personnalisé](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) d’Apple.

Pour les limitations individuelles, consultez le Guide de programmation de l' [extension d’application](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)Apple.

## <a name="distributing-installing-and-running-extensions"></a>Distribution, installation et exécution des extensions

Les extensions sont distribuées à partir d’une application conteneur, qui, à son tour, est envoyée et distribuée via l’App Store. Les extensions distribuées avec l’application sont installées à ce stade, mais l’utilisateur doit activer explicitement chaque extension. Les différents types d’extensions sont activés de différentes façons ; plusieurs requièrent que l’utilisateur accède à l’application **paramètres** et les active à partir de là. Tandis que d’autres sont activées au point d’utilisation, telles que l’activation d’une extension de partage lors de l’envoi d’une photo. 

L’application dans laquelle l’extension est utilisée (où l’utilisateur rencontre le point d’extension) est appelée **application hôte**, car il s’agit de l’application qui héberge l’extension lors de son exécution. L’application qui installe l’extension est l' **application conteneur**, car il s’agit de l’application qui contenait l’extension lors de son installation.  

En règle générale, l’application conteneur décrit l’extension et guide l’utilisateur tout au long du processus d’activation.

## <a name="extension-lifecycle"></a>Cycle de vie des extensions

Une extension peut être aussi simple qu’une [UIViewController](xref:UIKit.UIViewController) unique ou des extensions plus complexes qui présentent plusieurs écrans d’interface utilisateur. Lorsque l’utilisateur rencontre des points d' _extension_ (par exemple, lors du partage d’une image), il peut choisir parmi les extensions inscrites pour ce point d’extension. 

S’ils choisissent l’une des extensions de votre application, son `UIViewController` est instancié et commence le cycle de vie normal du contrôleur d’affichage. Toutefois, contrairement à une application normale, qui sont suspendues, mais qui ne se terminent généralement pas lorsque l’utilisateur termine l’interaction avec eux, les extensions sont chargées, exécutées, puis arrêtées à plusieurs reprises.

Les extensions peuvent communiquer avec leurs applications hôtes via un objet [NSExtensionContext](xref:Foundation.NSExtensionContext) . Certaines extensions ont des opérations qui reçoivent des rappels asynchrones avec les résultats. Ces rappels seront exécutés sur les threads d’arrière-plan et l’extension doit être prise en compte. par exemple, en utilisant [NSObject. InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*) s’il souhaite mettre à jour l’interface utilisateur. Pour plus d’informations, consultez la section [communication avec l’application hôte](#communicating-with-the-host-app) ci-dessous.

Par défaut, les extensions et leurs applications de conteneur ne peuvent pas communiquer, bien qu’elles soient installées ensemble. Dans certains cas, l’application conteneur est essentiellement un conteneur « expédition » vide dont l’objectif est servi une fois l’extension installée. Toutefois, si des circonstances sont dictées, l’application conteneur et l’extension peuvent partager des ressources à partir d’une zone commune. En outre, une **extension aujourd’hui** peut demander à son application conteneur d’ouvrir une URL. Ce comportement est indiqué dans le [widget du compte à rebours des événements](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions).

## <a name="creating-an-extension"></a>Création d’une extension

Les extensions (et leurs applications conteneur) doivent être des binaires 64 bits et être générées à l’aide des [API unifiées](~/cross-platform/macios/unified/index.md)Xamarin. iOS. Lorsque vous développez une extension, vos solutions contiennent au moins deux projets : l’application conteneur et un projet pour chaque extension fournie par le conteneur.

### <a name="container-app-project-requirements"></a>Spécifications du projet d’application conteneur

L’application conteneur utilisée pour installer l’extension présente les exigences suivantes :

- Elle doit conserver une référence au projet d’extension.   
- Il doit s’agir d’une application complète (doit pouvoir être lancée et exécutée avec succès), même si elle ne fait rien d’autre que fournir un moyen d’installer une extension. 
- Elle doit avoir un identificateur de bundle qui est la base de l’identificateur de Bundle du projet d’extension (pour plus d’informations, consultez la section ci-dessous).

### <a name="extension-project-requirements"></a>Spécifications du projet d’extension

En outre, le projet de l’extension présente les exigences suivantes :

- Elle doit avoir un identificateur de bundle qui commence par l’identificateur d’offre groupée de l’application conteneur. Par exemple, si l’application conteneur a un identificateur de Bundle de `com.myCompany.ContainerApp`, l’identificateur de l’extension peut être `com.myCompany.ContainerApp.MyExtension` : 

  ![](extensions-images/bundleidentifiers.png) 
- Il doit définir la clé `NSExtensionPointIdentifier`, avec une valeur appropriée (par exemple, `com.apple.widget-extension` pour un widget de centre de notification **aujourd’hui** ), dans son fichier de `Info.plist`.
- Elle doit également définir la clé de `NSExtensionMainStoryboard` *ou la clé* `NSExtensionPrincipalClass` dans son fichier `Info.plist` avec une valeur appropriée :
  - Utilisez la clé de `NSExtensionMainStoryboard` pour spécifier le nom de la table de montage séquentiel qui présente l’interface utilisateur principale de l’extension (moins `.storyboard`). Par exemple, `Main` pour le fichier `Main.storyboard`.
  - Utilisez la clé de `NSExtensionPrincipalClass` pour spécifier la classe qui sera initialisée au démarrage de l’extension. La valeur doit correspondre à la valeur de **Registre** de votre `UIViewController` : 

  ![](extensions-images/registerandprincipalclass.png)

Des spécifications supplémentaires peuvent être requises pour certains types d’extensions. Par exemple, la classe principale d’une extension de **Centre de notification** ou d' **aujourd’hui** doit implémenter [INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding).

> [!IMPORTANT]
> Si vous démarrez votre projet à l’aide d’un des modèles d’extensions fournis par Visual Studio pour Mac, la plupart (dans le cas contraire), ces exigences seront fournies et remplies automatiquement par le modèle.

## <a name="walkthrough"></a>Procédure pas à pas 

Dans la procédure pas à pas suivante, vous allez créer un exemple de widget **Today** qui calcule le jour et le nombre de jours restants dans l’année :

[![](extensions-images/carpediemscreenshot-sm.png "An example Today widget that calculates the day and number of days remaining in the year")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Création de la solution

Pour créer la solution requise, procédez comme suit :

1. Tout d’abord, créez un projet d' **application de vue unique** iOS, puis cliquez sur le bouton **suivant** : 

    [![](extensions-images/today01.png "First, create a new iOS, Single View App project and click the Next button")](extensions-images/today01.png#lightbox)
2. Appelez le projet `TodayContainer`, puis cliquez sur le bouton **suivant** : 

    [![](extensions-images/today02.png "Call the project TodayContainer and click the Next button")](extensions-images/today02.png#lightbox)
3. Vérifiez le **nom du projet** et le **NomSolution** , puis cliquez sur le bouton **créer** pour créer la solution : 

    [![](extensions-images/today03.png "Verify the Project Name and SolutionName and click the Create button to create the solution")](extensions-images/today03.png#lightbox)
4. Ensuite, dans la **Explorateur de solutions**, cliquez avec le bouton droit sur la solution et ajoutez un nouveau projet d' **extension iOS** à partir du modèle d' **extension aujourd’hui** : 

    [![](extensions-images/today04.png "Next, in the Solution Explorer, right-click on the Solution and add a new iOS Extension project from the Today Extension template")](extensions-images/today04.png#lightbox)
5. Appelez le projet `DaysRemaining`, puis cliquez sur le bouton **suivant** : 

    [![](extensions-images/today05.png "Call the project DaysRemaining and click the Next button")](extensions-images/today05.png#lightbox)
6. Examinez le projet et cliquez sur le bouton **créer** pour le créer : 

    [![](extensions-images/today06.png "Review the project and click the Create button to create it")](extensions-images/today06.png#lightbox)

La solution obtenue doit maintenant avoir deux projets, comme illustré ici :

[![](extensions-images/today07.png "The resulting Solution should now have two projects, as shown here")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Création de l’interface utilisateur de l’extension

Vous devez ensuite concevoir l’interface pour votre widget **Today** . Pour ce faire, vous pouvez utiliser un storyboard ou créer l’interface utilisateur dans le code. Les deux méthodes sont traitées en détail ci-dessous.

#### <a name="using-storyboards"></a>Utilisation des storyboards

Pour générer l’interface utilisateur avec une table de montage séquentiel, procédez comme suit :

1. Dans la **Explorateur de solutions**, double-cliquez sur le fichier `Main.storyboard` du projet d’extension pour l’ouvrir et le modifier : 

    [![](extensions-images/today08.png "Double-click the Extension projects Main.storyboard file to open it for editing")](extensions-images/today08.png#lightbox)
2. Sélectionnez l’étiquette qui a été automatiquement ajoutée à l’interface utilisateur par modèle et donnez-lui le **nom** `TodayMessage` sous l’onglet **widget** de l' **Explorateur de propriétés**: 

    [![](extensions-images/today09.png "Select the Label that was automatically added to the UI by template and give it the Name TodayMessage in the Widget tab of the Properties Explorer")](extensions-images/today09.png#lightbox)
3. Enregistrez les modifications apportées à la table de montage séquentiel.

#### <a name="using-code"></a>Utilisation du code

Pour générer l’interface utilisateur dans le code, procédez comme suit : 

1. Dans le **Explorateur de solutions**, sélectionnez le projet **DaysRemaining** , ajoutez une nouvelle classe et appelez-la `CodeBasedViewController` : 

    [![](extensions-images/code01.png "Aelect the DaysRemaining project, add a new class and call it CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Là encore, dans le **Explorateur de solutions**, double-cliquez sur le fichier `Info.plist` de l’extension pour l’ouvrir et le modifier : 

    [![](extensions-images/code02.png "Double-click Extensions Info.plist file to open it for editing")](extensions-images/code02.png#lightbox)
3. Sélectionnez la **vue source** (en bas de l’écran) et ouvrez le nœud `NSExtension` : 

    [![](extensions-images/code03.png "Select the Source View from the bottom of the screen and open the NSExtension node")](extensions-images/code03.png#lightbox)
4. Supprimez la clé de `NSExtensionMainStoryboard` et ajoutez une `NSExtensionPrincipalClass` avec la valeur `CodeBasedViewController` : 

    [![](extensions-images/code04.png "Remove the NSExtensionMainStoryboard key and add a NSExtensionPrincipalClass with the value CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Enregistrez les modifications apportées.

Modifiez ensuite le fichier `CodeBasedViewController.cs` et faites-le ressembler à ce qui suit :

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
  [Register("CodeBasedViewController")]
  public class CodeBasedViewController : UIViewController, INCWidgetProviding
  {
    public CodeBasedViewController ()
    {
    }

    public override void ViewDidLoad ()
    {
      base.ViewDidLoad ();

      // Add label to view
      var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
        TextAlignment = UITextAlignment.Center
      };

      View.AddSubview (TodayMessage);

      // Insert code to power extension here...

    }
  }
}
```

Notez que le `[Register("CodeBasedViewController")]` correspond à la valeur que vous avez spécifiée pour la `NSExtensionPrincipalClass` ci-dessus.

### <a name="coding-the-extension"></a>Codage de l’extension

Une fois l’interface utilisateur créée, ouvrez le `TodayViewController.cs` ou le fichier `CodeBasedViewController.cs` (à partir de la méthode utilisée pour créer l’interface utilisateur ci-dessus), modifiez la méthode **ViewDidLoad** et faites-lui ressembler à ceci :

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  // Calculate the values
  var dayOfYear = DateTime.Now.DayOfYear;
  var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
  var daysRemaining = 365 + leapYearExtra - dayOfYear;

  // Display the message
  if (daysRemaining == 1) {
    TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
  } else {
    TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
  }
}
```

Si vous utilisez la méthode d’interface utilisateur basée sur le code, remplacez le `// Insert code to power extension here...` Commentaire par le nouveau code ci-dessus. Après l’appel de l’implémentation de base (et l’insertion d’une étiquette pour la version basée sur du code), ce code effectue un calcul simple pour obtenir le jour de l’année et le nombre de jours restants. Ensuite, il affiche le message dans l’étiquette (`TodayMessage`) que vous avez créée dans la conception de l’interface utilisateur.

Notez que ce processus est similaire au processus normal d’écriture d’une application. Le `UIViewController` d’une extension a le même cycle de vie qu’un contrôleur d’affichage dans une application, sauf que les extensions n’ont pas de modes d’arrière-plan et ne sont pas interrompues lorsque l’utilisateur a fini de les utiliser. Au lieu de cela, les extensions sont initialisées et désallouées de façon répétée selon les besoins.

### <a name="creating-the-container-app-user-interface"></a>Création de l’interface utilisateur de l’application conteneur

Pour cette procédure pas à pas, l’application conteneur est simplement utilisée comme méthode pour livrer et installer l’extension et ne fournit aucune fonctionnalité propre. Modifiez le fichier `Main.storyboard` de TodayContainer et ajoutez du texte qui définit la fonction de l’extension et comment l’installer :

[![](extensions-images/today10.png "Edit the TodayContainers Main.storyboard file and add some text defining the Extensions function and how to install it")](extensions-images/today10.png#lightbox)

Enregistrez les modifications apportées à la table de montage séquentiel.

### <a name="testing-the-extension"></a>Test de l’extension

Pour tester votre extension dans le simulateur iOS, exécutez l’application **TodayContainer** . La vue principale du conteneur s’affiche :

[![](extensions-images/run01.png "The containers main view will be displayed")](extensions-images/run01.png#lightbox)

Ensuite, appuyez sur le bouton **Accueil** dans le simulateur, faites défiler vers le haut de l’écran pour ouvrir le **Centre de notification**, sélectionnez l’onglet **aujourd’hui** , puis cliquez sur le bouton **modifier** :

[![](extensions-images/run02.png "Hit the Home button in the Simulator, swipe down from the top of the screen to open the Notification Center, select the Today tab and click the Edit button")](extensions-images/run02.png#lightbox)

Ajoutez l’extension **DaysRemaining** à l’affichage **aujourd’hui** , puis cliquez sur le bouton **terminé** :

[![](extensions-images/run03.png "Add the DaysRemaining Extension to the Today view and click the Done button")](extensions-images/run03.png#lightbox)

Le nouveau widget est ajouté à l’affichage **aujourd’hui** et les résultats s’affichent :

[![](extensions-images/run04.png "The new widget will be added to the Today view and the results will be displayed")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Communication avec l’application hôte

L’exemple d’extension aujourd’hui que vous avez créé ci-dessus ne communique pas avec son application hôte (l’écran **aujourd’hui** ). Si c’est le cas, elle utilise la propriété [ExtensionContext](xref:Foundation.NSExtensionContext) des classes `TodayViewController` ou `CodeBasedViewController`. 

Pour les extensions qui recevront des données de leurs applications hôtes, les données se présenteront sous la forme d’un tableau d’objets [NSExtensionItem](xref:Foundation.NSExtensionItem) stockés dans la propriété [InputItems](xref:Foundation.NSExtensionContext.InputItems) du [ExtensionContext](xref:Foundation.NSExtensionContext) de la `UIViewController` de l’extension.

D’autres extensions, telles que les extensions de modification de photos, peuvent faire la distinction entre l’utilisateur qui termine ou annule l’utilisation. Cela sera renvoyé à l’application hôte via les méthodes [CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*) et [CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*) de la propriété [ExtensionContext](xref:Foundation.NSExtensionContext) .

Pour plus d’informations, consultez le Guide de programmation de l' [extension d’application](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)Apple.

## <a name="communicating-with-the-parent-app"></a>Communication avec l’application parente

Un App Group est un groupe qui permet à différentes applications (ou à une application et ses extensions) d’accéder à un emplacement partagé du stockage de fichiers. Vous pouvez utiliser des App Groups pour les données suivantes, par exemple :

- [Paramètres de Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults partagé](~/ios/app-fundamentals/user-defaults.md).
- [Fichiers partagés](~/ios/watchos/app-fundamentals/parent-app.md#files).

Pour plus d’informations, consultez la section [app Groups](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) de notre documentation **sur les fonctionnalités** .

## <a name="mobilecoreservices"></a>MobileCoreServices

Lorsque vous utilisez des extensions, utilisez un identificateur de type uniforme (UTI) pour créer et manipuler des données échangées entre l’application, d’autres applications et/ou services.

La classe statique `MobileCoreServices.UTType` définit les propriétés d’assistance suivantes qui sont en rapport avec les définitions d’Apple `kUTType...` :

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

Consultez l’exemple suivant :

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

Pour plus d’informations, consultez la section [app Groups](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) de notre documentation **sur les fonctionnalités** .

## <a name="precautions-and-considerations"></a>Précautions et considérations

Les extensions ont beaucoup moins de mémoire disponible que les applications. Ils sont censés fonctionner rapidement et avec une intrusion minimale pour l’utilisateur et l’application dans laquelle ils sont hébergés. Toutefois, une extension doit également fournir une fonction propre et utile à l’application consommatrice avec une interface utilisateur personnalisée qui permet à l’utilisateur d’identifier l’application de développeur ou de conteneur de l’extension à laquelle il appartient.

Compte tenu de ces exigences strictes, vous ne devez déployer que des extensions qui ont été rigoureusement testées et optimisées pour les performances et la consommation de mémoire. 

## <a name="summary"></a>Récapitulatif

Ce document contient des extensions couvertes, ce qu’elles sont, le type de points d’extension et les limitations connues imposées à une extension par iOS. Il a abordé la création, la distribution, l’installation et l’exécution des extensions et du cycle de vie de l’extension. Il a fourni une procédure pas à pas de création d’un widget simple **Today** qui montre deux façons de créer l’interface utilisateur du widget à l’aide des storyboards ou du code. Il a montré comment tester une extension dans le simulateur iOS. Enfin, il a brièvement abordé la communication avec l’application hôte et quelques précautions et considérations qui doivent être prises lors du développement d’une extension. 

## <a name="related-links"></a>Liens connexes

- [ContainerApp (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/intro-to-extensions)
- [Création d’extensions dans Xamarin. iOS (vidéo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
