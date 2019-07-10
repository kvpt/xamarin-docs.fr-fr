---
title: À l’aide d’iCloud avec Xamarin.iOS
description: Ce document décrit iCloud et son utilisation dans les applications Xamarin.iOS. Il aborde le stockage clé-valeur, de stockage de documents et de sauvegarde iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/09/2016
ms.openlocfilehash: c30b5d0b3a02223a8808e57f5d0cdc5e7000e156
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674880"
---
# <a name="using-icloud-with-xamarinios"></a>À l’aide d’iCloud avec Xamarin.iOS

L’API de stockage iCloud dans iOS 5 permet aux applications d’enregistrer des documents d’utilisateur et les données spécifiques à l’application dans un emplacement central et accèdent à ces éléments à partir de tous les appareils de l’utilisateur.

Il existe quatre types de stockage disponibles :

- **Stockage clé-valeur** - partage de petites quantités de données avec votre application sur d’autres dispositifs d’un utilisateur.

- **Le stockage UIDocument** - pour stocker des documents et autres données dans le compte d’utilisateur iCloud à l’aide d’une sous-classe de UIDocument.

- **CoreData** -stockage de base de données SQLite.

- **Les fichiers et répertoires** : pour la gestion d’un grand nombre de différents fichiers directement dans le système de fichiers.

Ce document aborde les deux premiers types - paires clé-valeur et les sous-classes UIDocument - et comment utiliser ces fonctionnalités dans Xamarin.iOS.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

## <a name="requirements"></a>Configuration requise

- La dernière version stable de Xamarin.iOS
- Xcode 10
- Visual Studio pour Mac ou Visual Studio 2019.

## <a name="preparing-for-icloud-development"></a>Préparation pour le développement d’iCloud

Applications doivent être configurées pour utiliser iCloud dans les [portail de provisionnement Apple](https://developer.apple.com/account/ios/overview.action) et le projet lui-même. Avant de développer pour iCloud (ou essayer les exemples) suivez les étapes ci-dessous.

Pour configurer correctement une application à accéder à iCloud :

-   **Trouver votre ID d’équipe** -connexion à [developer.apple.com](https://developer.apple.com) et visitez le **Member Center > Your Account > Developer Account Summary** pour obtenir votre ID d’équipe (ou ID individuel pour les développeurs uniques ). Il sera une chaîne de 10 caractères ( **A93A5CM278** par exemple)-cela fait partie de l’identificateur « conteneur ».

-   **Créer un nouvel ID d’application** - pour créer un ID d’application, suivez les étapes décrites dans le [d’approvisionnement pour la section de Store Technologies du guide de provisionnement des appareils](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)et veillez à consulter **iCloud** comme un service autorisées :

 [![](introduction-to-icloud-images/icloud-sml.png "Vérifiez iCloud comme un service autorisé")](introduction-to-icloud-images/icloud.png#lightbox)

- **Créer un nouveau profil de provisionnement** - pour créer un profil de provisionnement, suivez les étapes décrites dans le [guide de provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Ajouter l’identificateur du conteneur à Entitlements.plist** -le format d’identificateur de conteneur est `TeamID.BundleID`. Pour plus d’informations, reportez-vous à la [utilisation de droits](~/ios/deploy-test/provisioning/entitlements.md) guide.

- **Configurer les propriétés du projet** : dans le fichier Info.plist fichier garantir la **identificateur de Bundle** correspond à la **ID d’offre groupée** définies lorsque [création d’un ID d’application](~/ios/deploy-test/provisioning/capabilities/index.md); IOS Bundle Signing utilise un **profil de provisionnement** qui contiennent un ID d’application avec App Service, iCloud et **droits personnalisés** fichier sélectionné. Cela tout est possible dans Visual Studio sous le volet de propriétés de projet.

- **Activer iCloud sur votre appareil** - accédez à **Paramètres > iCloud** et assurez-vous que l’appareil est connecté.
Sélectionnez et activez le **Documents et données** option.

- **Vous devez utiliser un appareil pour tester iCloud** -il ne fonctionnera pas sur le simulateur.
En fait, vous devez vraiment deux ou plusieurs des appareils connectés avec le même ID Apple pour voir iCloud en action.


## <a name="key-value-storage"></a>Stockage clé-valeur

Stockage clé-valeur est destiné aux petites quantités de données un utilisateur intéresser être persistantes entre les appareils - telles que la dernière page que ceux-ci affichés dans un livre ou un magazine. Stockage de clé-valeur ne doit pas être utilisé pour les données de sauvegarde.

Il existe certaines limites à connaître lors de l’utilisation du stockage de clé-valeur :

- **Taille de clé maximale** -noms de clé ne peut pas comporter plus de 64 octets.

- **Taille de la valeur maximale** -vous ne pouvez pas stocker plus de 64 kilo-octets dans une seule valeur.

- **Taille de stockage de clé / valeur maximale pour une application** -Applications ne peuvent stocker jusqu'à 64 Ko de données clé-valeur au total. Tente de définir les clés au-delà de cette limite échoue et la valeur précédente est conservé.

- **Types de données** : seuls les types de base telles que des chaînes, nombres et valeurs booléennes peuvent être stockées.

Le **iCloudKeyValue** exemple montre comment il fonctionne. L’exemple de code crée une clé nommée pour chaque appareil : vous pouvez définir cette clé sur un périphérique et regardez la valeur est propagée à d’autres utilisateurs. Il crée également une clé appelée « Partagés », qui peuvent être modifiés sur n’importe quel appareil - si vous modifiez à la fois sur de nombreux appareils, iCloud décidera dont la valeur « wins » (à l’aide d’un horodatage sur la modification) et sont propagées.

Cette capture d’écran montre l’exemple en cours d’utilisation. Lors de la réception des notifications de modification à partir d’iCloud, elles sont imprimées dans l’affichage de texte de défilement en bas de l’écran et mis à jour dans les champs d’entrée.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "Le flux de messages entre les appareils")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Définition et récupération de données

Ce code montre comment définir une valeur de chaîne.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

L’appel de synchronisation garantit que la valeur est rendue persistante dans le stockage sur disque local uniquement. La synchronisation avec iCloud se produit en arrière-plan et ne peut pas être « forcée » par code d’application. Avec une connectivité réseau fonctionne bien la synchronisation souvent se produira dans les 5 secondes, toutefois, si le réseau est médiocre (ou déconnecté) une mise à jour peut prendre beaucoup plus de temps.

Vous pouvez récupérer une valeur avec ce code :

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

La valeur est récupérée à partir de la banque de données locale : cette méthode ne tente pas de contacter des serveurs iCloud pour obtenir la valeur « latest ». iCloud met à jour la banque de données locale en fonction de son propre calendrier.

### <a name="deleting-data"></a>Suppression de données

Pour supprimer complètement une paire clé-valeur, utilisez la méthode Remove comme suit :

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>En observant les modifications

Une application peut également recevoir des notifications lorsque les valeurs sont modifiées par iCloud en ajoutant un observateur à la `NSNotificationCenter.DefaultCenter`.
Le code suivant à partir de **KeyValueViewController.cs** `ViewWillAppear` méthode montre comment écouter les notifications et créer une liste dont les clés ont été modifiés :

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

Votre code peut alors prendre une action avec la liste de clés modifiées, telles que la mise à jour une copie locale ou l’interface utilisateur avec les nouvelles valeurs.

Les causes possibles de modification sont : ServerChange (0), InitialSyncChange (1) ou QuotaViolationChange (2). Vous pouvez accéder à la raison et effectuer un traitement différent si nécessaire (par exemple, vous devrez peut-être supprimer certaines clés à la suite d’un *QuotaViolationChange*).

## <a name="document-storage"></a>Stockage de documents

iCloud stockage de documents est conçu pour gérer les données importantes à votre application (et à l’utilisateur). Il peut être utilisé pour gérer des fichiers et autres données que votre application doit s’exécuter, tout en fournissant la sauvegarde sur iCloud et de partager des fonctionnalités entre tous les appareils de l’utilisateur.

Ce diagramme illustre comment tout s’ajuste. Chaque périphérique possède les données enregistrées sur le stockage local (la UbiquityContainer) et iCloud du système d’exploitation que démon s’occupe de l’envoi et la réception des données dans le cloud. Tous les accès de fichier à la UbiquityContainer doivent être effectuée par le biais de FilePresenter/FileCoordinator pour empêcher les accès simultanés. Le `UIDocument` classe implémente pour vous ; cet exemple montre comment utiliser UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "La vue d’ensemble du stockage de document")](introduction-to-icloud-images/icloud-overview.png#lightbox)

L’exemple iCloudUIDoc implémente une simple `UIDocument` sous-classe qui contient un champ de texte unique. Le texte est rendu dans un `UITextView` et modifications sont propagées par iCloud à d’autres périphériques avec un message de notification affiché en rouge. L’exemple de code ne traite pas les fonctionnalités iCloud plus avancées telles que la résolution des conflits.

Cette capture d’écran montre l’exemple d’application - après la modification du texte et en appuyant sur **UpdateChangeCount** le document est synchronisé via iCloud à d’autres appareils.

 [![](introduction-to-icloud-images/iclouduidoc.png "Cette capture d’écran montre l’exemple d’application après la modification du texte et en appuyant sur UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Il existe cinq parties à l’exemple iCloudUIDoc :

1. **L’accès à la UbiquityContainer** -déterminer si iCloud est activé et si tel est le cas le chemin d’accès à la zone de stockage iCloud de votre application.

1. **Création d’une sous-classe de UIDocument** -créer une classe comme intermédiaire entre le stockage d’iCloud et de vos objets de modèle.

1. **Recherche et l’ouverture de documents iCloud** -utiliser `NSFileManager` et `NSPredicate` pour rechercher des documents iCloud et les ouvrir.

1. **Affichage de documents iCloud** -exposent des propriétés à partir de votre `UIDocument` afin que vous pouvez interagir avec les contrôles d’interface utilisateur.

1. **L’enregistrement de documents iCloud** -Assurez-vous que les modifications apportées dans l’interface utilisateur sont conservées sur disque et iCloud.

Toutes les opérations d’iCloud exécuter (ou doit s’exécuter) en mode asynchrone afin qu’ils ne bloquent en attendant que quelque chose se produise. Vous verrez trois façons différentes d’accomplir cela dans l’exemple :

 **Threads** - dans `AppDelegate.FinishedLaunching` l’appel initial à `GetUrlForUbiquityContainer` est effectuée sur un autre thread afin d’éviter de bloquer le thread principal.

 **NotificationCenter** - l’enregistrement pour les notifications lorsque asynchrone opérations telles que `NSMetadataQuery.StartQuery` terminée.

 **Gestionnaires de saisie semi-automatique** - en passant dans les méthodes pour s’exécuter sur la réalisation des opérations asynchrones comme `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>L’accès à la UbiquityContainer

La première étape dans l’aide d’iCloud stockage de documents consiste à déterminer si iCloud est activé et cas dans ce l’emplacement du « conteneur omniprésence » (le répertoire où les fichiers compatibles avec iCloud sont stockés sur l’appareil).

Ce code se trouve dans le `AppDelegate.FinishedLaunching` méthode de l’exemple.

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

Bien que l’exemple ne le faites pas, Apple recommande appelant GetUrlForUbiquityContainer chaque fois qu’une application s’affiche au premier plan.

### <a name="creating-a-uidocument-subclass"></a>Création d’une sous-classe de UIDocument

Tous les fichiers d’iCloud et les répertoires (ie. quoi que ce soit stocké dans le répertoire UbiquityContainer) doit être géré à l’aide de méthodes NSFileManager, implémentant le protocole NSFilePresenter et l’écriture via un NSFileCoordinator.
Le plus simple pour faire tout cela est ne pas pour l’écrire vous-même, mais la sous-classe UIDocument qui fait tout pour vous.

Il existe seulement deux méthodes que vous devez implémenter dans une sous-classe UIDocument pour travailler avec iCloud :

- **LoadFromContents** -transmet le NSData de contenu du fichier pour vous décompressez dans votre classe de modèle/es.

- **ContentsForType** -demande vous permet de fournir la représentation sous forme de NSData de votre classe de modèle/es à enregistrer sur disque (et le Cloud).

Cet exemple de code à partir de **iCloudUIDoc\MonkeyDocument.cs** montre comment implémenter UIDocument.

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

Le modèle de données dans ce cas est très simple : un champ de texte unique. Votre modèle de données peut être aussi complexe que nécessaire, tel qu’un document Xml ou les données binaires. Le rôle principal de l’implémentation de UIDocument consiste à traduire entre vos classes de modèle et une représentation NSData qui peut être enregistrée et chargée sur le disque.

### <a name="finding-and-opening-icloud-documents"></a>Recherche et ouverture de Documents iCloud

L’exemple d’application ne traite qu’un seul fichier - test.txt - donc le code dans **AppDelegate.cs** crée un `NSPredicate` et `NSMetadataQuery` pour rechercher plus précisément pour ce nom de fichier. Le `NSMetadataQuery` s’exécute de façon asynchrone et envoie une notification lorsqu’elle est terminée. `DidFinishGathering` appelé par l’Observateur de notification, arrête la requête et appelle LoadDocument, qui utilise le `UIDocument.Open` méthode avec un gestionnaire d’achèvement pour tenter de charger le fichier et l’afficher dans un `MonkeyDocumentViewController`.

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>Affichage de Documents iCloud

Affichage d’un UIDocument ne doit pas être différente à une autre classe de modèle
- propriétés sont affichées dans les contrôles d’interface utilisateur, éventuellement modifié par l’utilisateur et ensuite écrit dans le modèle.

Dans l’exemple **iCloudUIDoc\MonkeyDocumentViewController.cs** affiche le texte MonkeyDocument dans un `UITextView`. `ViewDidLoad` écoute de la notification envoyée le `MonkeyDocument.LoadFromContents` (méthode). `LoadFromContents` est appelé lorsque iCloud a de nouvelles données pour le fichier, afin que la notification indique que le document a été mis à jour.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

L’exemple de gestionnaire de notification de code appelle une méthode pour mettre à jour de l’interface utilisateur - dans ce cas sans détection de conflit ou résolution.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Enregistrement des Documents iCloud

Pour ajouter un UIDocument à iCloud, vous pouvez appeler `UIDocument.Save` directement (pour les nouveaux documents uniquement) ou déplacer un fichier existant à l’aide `NSFileManager.DefaultManager.SetUbiquitious`. L’exemple de code crée un nouveau document directement dans le conteneur de l’omniprésence avec ce code (il existe deux achèvement gestionnaires ici, un pour le `Save` opération et l’autre pour l’ouvrir) :

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

Les modifications ultérieures apportées au document ne sont pas « enregistrées » directement, au lieu de cela nous indiquons à la `UIDocument` qui il a changé avec `UpdateChangeCount`, et il sera automatiquement planifier une sauvegarde pour l’opération de disque :

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gestion des Documents iCloud

Les utilisateurs peuvent gérer des documents iCloud dans les **Documents** répertoire du « conteneur omniprésence » en dehors de votre application par le biais de paramètres ; ils peuvent afficher la liste des fichiers et balayage à supprimer. Code d’application doit être en mesure de gérer la situation où les documents sont supprimés par l’utilisateur. Ne stockez pas de données d’application interne dans le **Documents** directory.

 [![](introduction-to-icloud-images/icloudstorage.png "La gestion des flux de travail de Documents iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Les utilisateurs recevront également différents avertissements lorsqu’ils tentent de supprimer une application prenant en charge iCloud à partir de leur appareil, pour les informer de l’état de documents iCloud relatifs à cette application.

 [![](introduction-to-icloud-images/icloud-delete1.png "Exemple de dialogue lorsque l’utilisateur tente de supprimer une application prenant en charge iCloud à partir de leur appareil")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Exemple de dialogue lorsque l’utilisateur tente de supprimer une application prenant en charge iCloud à partir de leur appareil")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Sauvegarde iCloud

Bien que la sauvegarde sur iCloud n’est pas une fonctionnalité qui est accessible directement par les développeurs, la manière dont vous concevez votre application peut affecter l’expérience utilisateur.
Apple fournit [iOS recommandations de stockage de données](https://developer.apple.com/icloud/documentation/data-storage/) pour les développeurs à suivre dans leurs applications iOS.

Le plus important est que votre application stocke des fichiers volumineux qui ne sont pas générées par l’utilisateur (par exemple, une application de lecteurs du magazine qui stocke hundred-plus mégaoctets de contenu par problème). Apple préfère que vous ne stockez pas de ce type de données pour lesquelles il sera être sauvegardé sur iCloud et remplir inutilement le quota d’utilisateur iCloud.

Applications qui stockent des grandes quantités de données, telles que cela doivent soit stocker dans un des répertoires utilisateur qui n’est pas sauvegardé (par ex. Caches ou tmp) ou utilisez `NSFileManager.SetSkipBackupAttribute` pour appliquer un indicateur à ces fichiers afin qu’iCloud les ignore pendant les opérations de sauvegarde.

## <a name="summary"></a>Récapitulatif

Cet article a présenté la nouvelle fonctionnalité iCloud incluse dans iOS 5. Il a examiné les étapes requises pour configurer votre projet pour utiliser iCloud et puis fourni des exemples montrant comment implémenter les fonctionnalités iCloud.

L’exemple de stockage de clé-valeur a montré comment iCloud peut être utilisé pour stocker une petite quantité de données de la même manière que nsuserpreferences sont stockés. L’exemple UIDocument vous a montré comment plus de données complexes peuvent être stockés et synchronisés sur plusieurs appareils via iCloud.

Enfin, il inclus une brève discussion sur la façon dont l’ajout de la sauvegarde iCloud doit influencer la conception de votre application.



## <a name="related-links"></a>Liens associés

- [Introduction à iCloud (exemple)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [Exemple de Code séminaire d’iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud séminaire diapositives](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Stockage iCloud](https://support.apple.com/kb/HT4847)
