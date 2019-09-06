---
title: Utilisation de iCloud avec Xamarin. iOS
description: Ce document décrit iCloud et son utilisation dans les applications Xamarin. iOS. Il aborde le stockage clé-valeur, le stockage de documents et la sauvegarde iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/09/2016
ms.openlocfilehash: 364775ae8e8874d87022b5e45bd23ea29e82382d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292420"
---
# <a name="using-icloud-with-xamarinios"></a>Utilisation de iCloud avec Xamarin. iOS

L’API de stockage iCloud dans iOS 5 permet aux applications d’enregistrer des documents utilisateur et des données spécifiques à l’application dans un emplacement central et d’accéder à ces éléments à partir de tous les appareils de l’utilisateur.

Quatre types de stockage sont disponibles :

- **Stockage clé-valeur** : pour partager de petites quantités de données avec votre application sur les autres appareils d’un utilisateur.

- **Stockage UIDocument** : pour stocker des documents et d’autres données dans le compte icloud de l’utilisateur à l’aide d’une sous-classe de UIDocument.

- Stockage de base de données **CoreData** -sqlite.

- **Fichiers et répertoires individuels** : pour gérer un grand nombre de fichiers différents directement dans le système de fichiers.

Ce document aborde les deux premiers types-paires clé-valeur et sous-classes UIDocument, et comment utiliser ces fonctionnalités dans Xamarin. iOS.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

## <a name="requirements"></a>Configuration requise

- La dernière version stable de Xamarin. iOS
- Xcode 10
- Visual Studio pour Mac ou Visual Studio 2019.

## <a name="preparing-for-icloud-development"></a>Préparation au développement iCloud

Les applications doivent être configurées pour utiliser iCloud dans le [portail d’approvisionnement Apple](https://developer.apple.com/account/ios/overview.action) et le projet lui-même. Avant de développer pour iCloud (ou d’essayer les exemples), suivez les étapes ci-dessous.

Pour configurer correctement une application pour accéder à iCloud :

- **Recherchez votre TeamID** -Connectez-vous à [Developer.Apple.com](https://developer.apple.com) et visitez le **Centre des membres > votre compte > Résumé du compte de développeur** pour obtenir votre ID d’équipe (ou un ID individuel pour les développeurs uniques). Il s’agit d’une chaîne de 10 caractères ( **A93A5CM278** , par exemple), qui fait partie de l' « identificateur de conteneur ».

- **Créer un ID d’application** : pour créer un ID d’application, suivez les étapes décrites dans la [section Configuration des technologies du Windows Store du Guide de provisionnement des appareils](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)et veillez à vérifier **icloud** comme un service autorisé :

 [![](introduction-to-icloud-images/icloud-sml.png "Vérifier iCloud en tant que service autorisé")](introduction-to-icloud-images/icloud.png#lightbox)

- **Créer un profil de provisionnement** -pour créer un profil de provisionnement, suivez les étapes décrites dans le [Guide de provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Ajoutez l’identificateur de conteneur au fichier Entitlements. plist** : le format de l' `TeamID.BundleID`identificateur de conteneur est. Pour plus d’informations, consultez le guide [utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md) .

- **Configurer les propriétés du projet** -dans le fichier info. plist, vérifiez que l' **identificateur de Bundle** correspond à l' **ID de Bundle** défini lors de la [création d’un ID d’application](~/ios/deploy-test/provisioning/capabilities/index.md); La signature du bundle iOS utilise un **profil de provisionnement** qui contient un ID d’application avec l’app service icloud et le fichier de **droits personnalisé** sélectionné. Cela peut être fait dans Visual Studio sous le volet Propriétés du projet.

- **Activer icloud sur votre appareil** : accédez aux **Paramètres > icloud** et assurez-vous que l’appareil est connecté.
Sélectionnez et activez l’option **Documents & données** .

- **Vous devez utiliser un appareil pour tester icloud** ; il ne fonctionnera pas sur le simulateur.
En fait, vous avez vraiment besoin d’au moins deux appareils connectés avec le même ID Apple pour voir iCloud en action.


## <a name="key-value-storage"></a>Stockage clé-valeur

Le stockage clé-valeur est conçu pour les petites quantités de données qu’un utilisateur peut souhaiter conserver sur les appareils, comme la dernière page qu’il a consultée dans un livre ou un magazine. Le stockage clé-valeur ne doit pas être utilisé pour la sauvegarde des données.

Il existe certaines limitations à connaître lors de l’utilisation du stockage clé-valeur :

- **Taille de clé maximale** -les noms de clé ne peuvent pas dépasser 64 octets.

- **Taille maximale** de la valeur : vous ne pouvez pas stocker plus de 64 kilo-octets dans une seule valeur.

- **Taille maximale de la Banque de valeurs de clé pour une application** : les applications peuvent uniquement stocker jusqu’à 64 kilo-octets de données clé-valeur au total. Les tentatives de définition de clés au-delà de cette limite échouent et la valeur précédente est conservée.

- **Types de données** : seuls les types de base tels que les chaînes, les nombres et les valeurs booléennes peuvent être stockés.

L’exemple **iCloudKeyValue** illustre son fonctionnement. L’exemple de code crée une clé nommée pour chaque appareil : vous pouvez définir cette clé sur un appareil et observer la propagation de la valeur à d’autres utilisateurs. Il crée également une clé appelée « Shared » qui peut être modifiée sur n’importe quel appareil : Si vous modifiez sur plusieurs appareils à la fois, iCloud détermine la valeur « WINS » (à l’aide d’un horodateur sur la modification) et est propagée.

Cette capture d’écran montre l’exemple en cours d’utilisation. Lorsque des notifications de modification sont reçues de iCloud, elles sont imprimées dans l’affichage de texte défilant au bas de l’écran et mis à jour dans les champs d’entrée.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "Le flow des messages entre les appareils")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Définition et récupération de données

Ce code montre comment définir une valeur de chaîne.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

L’appel de Synchronize garantit que la valeur est conservée uniquement dans le stockage sur disque local. La synchronisation avec iCloud se produit en arrière-plan et ne peut pas être « forcée » par le code d’application. Avec une bonne connectivité réseau, la synchronisation se produit souvent dans un délai de 5 secondes. Toutefois, si le réseau est faible (ou déconnecté), une mise à jour peut prendre plus de temps.

Vous pouvez récupérer une valeur avec ce code :

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

La valeur est extraite du magasin de données local. cette méthode ne tente pas de contacter les serveurs iCloud pour connaître la valeur « latest ». iCloud met à jour la Banque de données locale en fonction de son propre calendrier.

### <a name="deleting-data"></a>Suppression de données

Pour supprimer complètement une paire clé-valeur, utilisez la méthode Remove comme suit :

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Observation des modifications

Une application peut également recevoir des notifications lorsque les `NSNotificationCenter.DefaultCenter`valeurs sont modifiées par icloud en ajoutant un observateur à.
Le code suivant de la méthode **KeyValueViewController.cs** `ViewWillAppear` montre comment écouter ces notifications et créer une liste des clés qui ont été modifiées :

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

Votre code peut ensuite prendre une mesure avec la liste des clés modifiées, telles que la mise à jour d’une copie locale ou la mise à jour de l’interface utilisateur avec les nouvelles valeurs.

Les raisons des modifications possibles sont les suivantes : ServerChange (0), InitialSyncChange (1) ou QuotaViolationChange (2). Vous pouvez accéder à la raison et effectuer un traitement différent si nécessaire (par exemple, vous devrez peut-être supprimer certaines clés à la suite d’un *QuotaViolationChange*).

## <a name="document-storage"></a>Stockage de documents

le stockage de documents iCloud est conçu pour gérer les données qui sont importantes pour votre application (et pour l’utilisateur). Il peut être utilisé pour gérer des fichiers et d’autres données que votre application doit exécuter, tout en fournissant des fonctionnalités de sauvegarde et de partage basées sur iCloud sur tous les appareils de l’utilisateur.

Ce diagramme montre comment tous les éléments s’imbriquent. Chaque appareil a des données enregistrées sur le stockage local (UbiquityContainer) et le démon iCloud du système d’exploitation se charge de l’envoi et de la réception des données dans le Cloud. Tous les accès aux fichiers UbiquityContainer doivent être effectués via FilePresenter/FileCoordinator pour empêcher l’accès simultané. La `UIDocument` classe les implémente pour vous ; cet exemple montre comment utiliser UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "Vue d’ensemble du stockage de documents")](introduction-to-icloud-images/icloud-overview.png#lightbox)

L’exemple iCloudUIDoc implémente une sous `UIDocument` -classe simple qui contient un champ de texte unique. Le texte est rendu dans un `UITextView` et les modifications sont propagées par icloud à d’autres appareils avec un message de notification affiché en rouge. L’exemple de code ne traite pas des fonctionnalités iCloud plus avancées, telles que la résolution des conflits.

Cette capture d’écran montre l’exemple d’application. après avoir modifié le texte et appuyé sur **UpdateChangeCount** , le document est synchronisé via icloud sur d’autres appareils.

 [![](introduction-to-icloud-images/iclouduidoc.png "Cette capture d’écran montre l’exemple d’application après avoir modifié le texte et appuyé sur UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

L’exemple iCloudUIDoc comprend cinq parties :

1. **Accès au UbiquityContainer** -Déterminez si icloud est activé et, si tel est le cas, le chemin d’accès à la zone de stockage icloud de votre application.

1. **Création d’une sous-classe UIDocument** : créez une classe à intermédiaire entre le stockage icloud et vos objets de modèle.

1. **Recherche et ouverture de documents icloud** - `NSFileManager` utilisez `NSPredicate` et pour rechercher des documents icloud et les ouvrir.

1. **Affichage des documents icloud** : exposez `UIDocument` les propriétés de votre pour pouvoir interagir avec les contrôles d’interface utilisateur.

1. **Enregistrement de documents icloud** -Assurez-vous que les modifications apportées à l’interface utilisateur sont conservées sur disque et iCloud.

Toutes les opérations iCloud s’exécutent (ou doivent être exécutées) de façon asynchrone afin qu’elles ne soient pas bloquées en attendant qu’un événement se produise. Vous verrez trois façons différentes d’effectuer cette opération dans l’exemple :

 **Threads** : `AppDelegate.FinishedLaunching` dans l’appel initial `GetUrlForUbiquityContainer` à est effectué sur un autre thread pour empêcher le blocage du thread principal.

 **NotificationCenter** : inscription pour les notifications lorsque des opérations `NSMetadataQuery.StartQuery` asynchrones telles que sont terminées.

 **Gestionnaires de saisie semi-automatique** -passage de méthodes à exécuter à la fin `UIDocument.Open`d’opérations asynchrones telles que.

### <a name="accessing-the-ubiquitycontainer"></a>Accès au UbiquityContainer

La première étape de l’utilisation du stockage de documents iCloud consiste à déterminer si iCloud est activé et, si tel est le cas, l’emplacement du « conteneur ubiquité » (le répertoire où sont stockés les fichiers compatibles iCloud sur l’appareil).

Ce code est dans la `AppDelegate.FinishedLaunching` méthode de l’exemple.

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

Bien que l’exemple ne le fasse pas, Apple recommande d’appeler GetUrlForUbiquityContainer chaque fois qu’une application passe au premier plan.

### <a name="creating-a-uidocument-subclass"></a>Création d’une sous-classe UIDocument

Tous les fichiers et répertoires iCloud (c’est-à-dire tout ce qui est stocké dans le répertoire UbiquityContainer) doivent être gérés à l’aide de méthodes NSFileManager, en implémentant le protocole NSFilePresenter et en écrivant via un NSFileCoordinator.
La façon la plus simple d’effectuer tout ce qui n’est pas de l’écrire vous-même, mais de sous-classe UIDocument qui fait tout cela pour vous.

Il n’existe que deux méthodes que vous devez implémenter dans une sous-classe UIDocument pour travailler avec iCloud :

- **LoadFromContents** : transmet la NSData du contenu du fichier pour que vous déballez dans votre classe de modèle/es.

- **ContentsForType** : demandez à vous de fournir la représentation NSData de votre classe de modèle/es à enregistrer sur le disque (et le Cloud).

Cet exemple de code de **iCloudUIDoc\MonkeyDocument.cs** montre comment implémenter UIDocument.

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

Dans ce cas, le modèle de données est très simple : il s’agit d’un champ de texte unique. Votre modèle de données peut être aussi complexe que nécessaire, par exemple un document XML ou des données binaires. Le rôle principal de l’implémentation UIDocument consiste à effectuer une conversion entre vos classes de modèle et une représentation NSData qui peut être enregistrée/chargée sur le disque.

### <a name="finding-and-opening-icloud-documents"></a>Recherche et ouverture de documents iCloud

L’exemple d’application ne traite qu’un seul fichier-test. txt. le code de **AppDelegate.cs** crée donc `NSPredicate` un `NSMetadataQuery` et pour rechercher spécifiquement ce nom de fichier. Le `NSMetadataQuery` s’exécute de façon asynchrone et envoie une notification lorsqu’il se termine. `DidFinishGathering`est appelé par l’observateur de notification, arrête la requête et appelle LoadDocument, qui utilise `UIDocument.Open` la méthode avec un gestionnaire d’achèvement pour tenter de charger le fichier et de l' `MonkeyDocumentViewController`afficher dans un.

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

### <a name="displaying-icloud-documents"></a>Affichage des documents iCloud

L’affichage d’un UIDocument ne doit pas être différent de toute autre classe de modèle
- les propriétés s’affichent dans les contrôles d’interface utilisateur, éventuellement modifiés par l’utilisateur, puis réécrits dans le modèle.

Dans l’exemple **iCloudUIDoc\MonkeyDocumentViewController.cs** affiche le texte MonkeyDocument dans un `UITextView`. `ViewDidLoad`écoute la notification envoyée dans la `MonkeyDocument.LoadFromContents` méthode. `LoadFromContents`est appelé lorsque iCloud a de nouvelles données pour le fichier, de sorte que la notification indique que le document a été mis à jour.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

L’exemple de gestionnaire de notification de code appelle une méthode pour mettre à jour l’interface utilisateur, dans ce cas sans détection ni résolution de conflit.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Enregistrement de documents iCloud

Pour ajouter un UIDocument à iCloud, vous pouvez `UIDocument.Save` appeler directement (pour les nouveaux documents uniquement) ou déplacer un fichier `NSFileManager.DefaultManager.SetUbiquitious`existant à l’aide de. L’exemple de code crée un nouveau document directement dans le conteneur ubiquité avec ce code (il y a deux gestionnaires de terminaison ici, un `Save` pour l’opération et un autre pour l’ouverture) :

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

Les modifications ultérieures apportées au document ne sont pas directement « enregistrées » `UIDocument` . au lieu de cela `UpdateChangeCount`, nous indiquons le qu’il a modifié, et il planifiera automatiquement une opération d’enregistrement sur disque :

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gestion des documents iCloud

Les utilisateurs peuvent gérer les documents iCloud dans le répertoire **documents** du « conteneur ubiquité » en dehors de votre application via les paramètres. ils peuvent afficher la liste des fichiers et les balayer pour les supprimer. Le code d’application doit être en mesure de gérer la situation dans laquelle les documents sont supprimés par l’utilisateur. Ne stockez pas de données d’application internes dans le répertoire **documents** .

 [![](introduction-to-icloud-images/icloudstorage.png "Gestion du flux de travail des documents iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Les utilisateurs recevront également différents avertissements lorsqu’ils tenteront de supprimer une application iCloud de leur appareil pour les informer de l’état des documents iCloud associés à cette application.

 [![](introduction-to-icloud-images/icloud-delete1.png "Exemple de boîte de dialogue lorsque l’utilisateur tente de supprimer une application iCloud de son appareil")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Exemple de boîte de dialogue lorsque l’utilisateur tente de supprimer une application iCloud de son appareil")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Sauvegarde iCloud

La sauvegarde sur iCloud n’est pas une fonctionnalité directement accessible par les développeurs, la façon dont vous concevez votre application peut avoir une incidence sur l’expérience utilisateur.
Apple fournit des [instructions de stockage de données iOS](https://developer.apple.com/icloud/documentation/data-storage/) que les développeurs peuvent suivre dans leurs applications iOS.

La considération la plus importante est de savoir si votre application stocke des fichiers volumineux qui ne sont pas générés par l’utilisateur (par exemple, une application de lecteur de magazines qui stocke des centaines de mégaoctets de contenu par problème). Apple préfère que vous ne stockiez pas ce type de données où elle sera sauvegardée sur iCloud et remplisse inutilement le quota iCloud de l’utilisateur.

Les applications qui stockent de grandes quantités de données comme celle-ci doivent soit les stocker dans l’un des répertoires utilisateur qui n’est pas sauvegardé (par exemple, Cache ou tmp) ou utilisez `NSFileManager.SetSkipBackupAttribute` pour appliquer un indicateur à ces fichiers afin que icloud les ignore pendant les opérations de sauvegarde.

## <a name="summary"></a>Récapitulatif

Cet article a introduit la nouvelle fonctionnalité iCloud incluse dans iOS 5. Il a examiné les étapes requises pour configurer votre projet pour utiliser iCloud, puis fourni des exemples d’implémentation des fonctionnalités de iCloud.

L’exemple de stockage clé-valeur démontre comment iCloud peut être utilisé pour stocker une petite quantité de données similaire à la façon dont les NSUserPreferences sont stockés. L’exemple UIDocument a montré comment des données plus complexes peuvent être stockées et synchronisées sur plusieurs appareils via iCloud.

Enfin, elle comprenait une brève discussion sur la façon dont l’ajout de la sauvegarde iCloud doit influencer la conception de votre application.



## <a name="related-links"></a>Liens associés

- [Présentation de iCloud (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/introductiontoicloud)
- [Exemple de code de séminaire iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Diapositives du séminaire iCloud](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Stockage iCloud](https://support.apple.com/kb/HT4847)
