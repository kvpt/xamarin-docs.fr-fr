---
title: Sélecteur de documents dans Xamarin. iOS
description: Ce document décrit le sélecteur de documents iOS et explique comment l’utiliser dans Xamarin. iOS. Il examine iCloud, les documents, le code d’installation commun, les extensions de fournisseur de documents, etc.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: cd38facb62c5864f1c933611d8d9dcda94589066
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528227"
---
# <a name="document-picker-in-xamarinios"></a>Sélecteur de documents dans Xamarin. iOS

Le sélecteur de documents permet de partager des documents entre les applications. Ces documents peuvent être stockés dans iCloud ou dans un répertoire d’application différent. Les documents sont partagés via l’ensemble des [extensions de fournisseur de documents](~/ios/platform/extensions.md) que l’utilisateur a installées sur leur appareil. 

En raison de la difficulté de conserver les documents synchronisés entre les applications et le Cloud, ils introduisent une certaine complexité nécessaire.

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour effectuer les étapes présentées dans cet article:

- **Xcode 7 et iOS 8 ou versions ultérieures** : les API Xcode 7 et iOS 8 d’Apple et les API plus récentes doivent être installées et configurées sur l’ordinateur du développeur.
- **Visual Studio ou Visual Studio pour Mac** : la dernière version de Visual Studio pour Mac doit être installée.
- **appareil iOS** : un appareil iOS exécutant iOS 8 ou version ultérieure.

## <a name="changes-to-icloud"></a>Modifications apportées à iCloud

Pour implémenter les nouvelles fonctionnalités du sélecteur de documents, les modifications suivantes ont été apportées au service iCloud d’Apple:

- Le démon iCloud a été entièrement réécrit à l’aide de CloudKit.
- Les fonctionnalités iCloud existantes ont été renommées iCloud Drive.
- La prise en charge du système d’exploitation Microsoft Windows a été ajoutée à iCloud.
- Un dossier iCloud a été ajouté dans le Finder Mac OS.
- les appareils iOS peuvent accéder au contenu du dossier Mac OS iCloud.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

## <a name="what-is-a-document"></a>Qu’est-ce qu’un document?

Lorsque vous faites référence à un document dans iCloud, il s’agit d’une entité unique et autonome qui doit être perçue comme telle par l’utilisateur. Un utilisateur peut souhaiter modifier le document ou le partager avec d’autres utilisateurs (par exemple, à l’aide de la messagerie électronique).

Il existe plusieurs types de fichiers que l’utilisateur reconnaît immédiatement comme documents, tels que les pages, les keynotes ou les fichiers de nombres. Toutefois, iCloud n’est pas limité à ce concept. Par exemple, l’état d’un jeu (par exemple, une correspondance d’échecs) peut être traité comme un document et stocké dans iCloud. Ce fichier peut être transmis entre les appareils d’un utilisateur et lui permettre de récupérer un jeu là où il s’était arrêté sur un autre appareil.

## <a name="dealing-with-documents"></a>Gestion des documents

Avant de plonger dans le code requis pour utiliser le sélecteur de documents avec Xamarin, cet article va aborder les meilleures pratiques pour l’utilisation des documents iCloud, ainsi que plusieurs des modifications apportées aux API existantes requises pour prendre en charge le sélecteur de documents.

### <a name="using-file-coordination"></a>Utilisation de la coordination des fichiers

Étant donné qu’un fichier peut être modifié à partir de différents emplacements, la coordination doit être utilisée pour éviter la perte de données.

 [![](document-picker-images/image1.png "Utilisation de la coordination des fichiers")](document-picker-images/image1.png#lightbox)

Jetons un coup d’œil à l’illustration ci-dessus:

1. Un appareil iOS à l’aide de la coordination de fichiers crée un document et l’enregistre dans le dossier iCloud.
2. iCloud enregistre le fichier modifié dans le Cloud pour la distribution à tous les appareils.
3. Un Mac attaché voit le fichier modifié dans le dossier iCloud et utilise la coordination de fichiers pour copier les modifications dans le fichier.
4. Un appareil qui n’utilise pas la coordination des fichiers apporte une modification au fichier et l’enregistre dans le dossier iCloud Ces modifications sont immédiatement répliquées sur les autres appareils.

Supposons que l’appareil iOS d’origine était en train de modifier le fichier, mais que ses modifications soient perdues et remplacées par la version du fichier de l’appareil non coordonné. Pour éviter la perte de données, la coordination des fichiers est une fonction obligatoire lors de l’utilisation de documents basés sur le Cloud.

### <a name="using-uidocument"></a>Utilisation de UIDocument

 `UIDocument`simplifie les choses ( `NSDocument` ou sur MacOS) en faisant tout le gros du levage pour le développeur. Il fournit une coordination de fichiers intégrée avec des files d’attente d’arrière-plan pour éviter de bloquer l’interface utilisateur de l’application.

 `UIDocument`expose plusieurs API de haut niveau qui facilitent l’effort de développement d’une application Xamarin pour les besoins du développeur.

Le code suivant crée une sous-classe `UIDocument` de pour implémenter un document de type texte générique qui peut être utilisé pour stocker et récupérer du texte à partir de icloud:

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

La `GenericTextDocument` classe présentée ci-dessus sera utilisée tout au long de cet article lorsque vous travaillerez avec le sélecteur de documents et des documents externes dans une application Xamarin. iOS 8.

## <a name="asynchronous-file-coordination"></a>Coordination asynchrone des fichiers

iOS 8 fournit plusieurs nouvelles fonctionnalités de coordination de fichiers asynchrones via les nouvelles API de coordination de fichiers. Avant iOS 8, toutes les API de coordination de fichiers existantes étaient entièrement synchrones. Cela signifiait que le développeur était responsable de l’implémentation de sa propre mise en file d’attente en arrière-plan pour empêcher la coordination de fichiers de bloquer l’interface utilisateur de l’application.

La nouvelle `NSFileAccessIntent` classe contient une URL pointant vers le fichier et plusieurs options pour contrôler le type de coordination requis. Le code suivant illustre le déplacement d’un fichier d’un emplacement à un autre à l’aide d’intentions:

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>Découverte et liste de documents

La façon de découvrir et de répertorier les documents consiste à `NSMetadataQuery` utiliser les API existantes. Cette section aborde les nouvelles fonctionnalités ajoutées `NSMetadataQuery` à qui facilitent l’utilisation de documents.

### <a name="existing-behavior"></a>Comportement existant

Avant iOS 8, `NSMetadataQuery` était lent à récupérer les modifications des fichiers locaux, par exemple: suppressions, créations et renommages.

 [![](document-picker-images/image2.png "Vue d’ensemble des modifications de fichiers locaux NSMetadataQuery")](document-picker-images/image2.png#lightbox)

Dans le diagramme ci-dessus:

1. Pour les fichiers qui existent déjà dans le conteneur d' `NSMetadataQuery` application, `NSMetadata` contient des enregistrements existants précréés et mis en file d’attente afin qu’ils soient instantanément accessibles à l’application.
1. L’application crée un nouveau fichier dans le conteneur d’application.
1. Il y a un délai `NSMetadataQuery` avant de voir la modification du conteneur d’application et de `NSMetadata` créer l’enregistrement requis.


En raison du délai de création de l' `NSMetadata` enregistrement, deux sources de données doivent être ouvertes pour l’application: une pour les modifications de fichiers locaux et une pour les modifications basées sur le Cloud.

### <a name="stitching"></a>Assemblage

Dans iOS 8, `NSMetadataQuery` est plus facile à utiliser directement avec une nouvelle fonctionnalité appelée «assemblage»:

 [![](document-picker-images/image3.png "NSMetadataQuery avec une nouvelle fonctionnalité appelée assemblage")](document-picker-images/image3.png#lightbox)

À l’aide de la combinaison dans le diagramme ci-dessus:

1. Comme précédemment, pour les fichiers qui existent déjà dans le conteneur d' `NSMetadataQuery` application, `NSMetadata` contient des enregistrements existants précréés et mis en attente.
1. L’application crée un nouveau fichier dans le conteneur d’application à l’aide de la coordination de fichiers.
1. Un hook dans le conteneur d’application voit la modification et `NSMetadataQuery` les appels pour créer `NSMetadata` l’enregistrement requis.
1. L' `NSMetadata` enregistrement est créé directement après le fichier et mis à la disposition de l’application.


En utilisant la fonction de combinaison, l’application n’a plus à ouvrir de source de données pour surveiller les modifications de fichiers locales et sur le Cloud. Désormais, l’application peut s' `NSMetadataQuery` appuyer directement sur.

> [!IMPORTANT]
> La combinaison fonctionne uniquement si l’application utilise la coordination de fichiers comme indiqué dans la section ci-dessus. Si la coordination de fichiers n’est pas utilisée, les API utilisent par défaut le comportement pré-iOS 8 existant.




### <a name="new-ios-8-metadata-features"></a>Nouvelles fonctionnalités de métadonnées iOS 8

Les nouvelles fonctionnalités suivantes ont été ajoutées à `NSMetadataQuery` dans iOS 8:

- `NSMetatadataQuery`peut maintenant répertorier les documents non locaux stockés dans le Cloud.
- De nouvelles API ont été ajoutées pour accéder aux informations de métadonnées sur les documents basés sur le Cloud. 
- Il existe une nouvelle `NSUrl_PromisedItems` API qui permet d’accéder aux attributs de fichier des fichiers dont le contenu peut être disponible localement ou non.
- Utilisez la `GetPromisedItemResourceValue` méthode pour obtenir des informations sur un fichier donné ou utilisez `GetPromisedItemResourceValues` la méthode pour obtenir des informations sur plusieurs fichiers à la fois.


Deux nouveaux indicateurs de coordination de fichiers ont été ajoutés pour traiter les métadonnées:

- `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
- `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Avec les indicateurs ci-dessus, le contenu du fichier de document n’a pas besoin d’être disponible localement pour pouvoir être utilisé.

Le segment de code suivant montre comment utiliser `NSMetadataQuery` pour interroger l’existence d’un fichier spécifique et générer le fichier s’il n’existe pas:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;


#region Static Properties
public const string TestFilename = "test.txt"; 
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),           NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path 
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>Miniatures de document

Apple pense que la meilleure expérience utilisateur lors de la liste de documents pour une application est d’utiliser les aperçus. Le contexte de l’utilisateur final est ainsi en mesure d’identifier rapidement le document avec lequel il souhaite travailler.

Avant iOS 8, l’affichage des aperçus de document nécessitait une implémentation personnalisée. Dans iOS 8, les attributs du système de fichiers permettent au développeur de travailler rapidement avec des miniatures de documents.

#### <a name="retrieving-document-thumbnails"></a>Récupération des miniatures de document 

En appelant les `GetPromisedItemResourceValue` `GetPromisedItemResourceValues` méthodesou`NSUrl_PromisedItems` , l’API aestretournée.`NSUrlThumbnailDictionary` La seule clé actuellement dans ce dictionnaire est le `NSThumbnial1024X1024SizeKey` et son correspondant `UIImage`.

#### <a name="saving-document-thumbnails"></a>Enregistrement des miniatures de documents

Le moyen le plus simple d’enregistrer une miniature consiste `UIDocument`à utiliser. En appelant la `GetFileAttributesToWrite` méthode `UIDocument` du et en définissant la miniature, elle est automatiquement enregistrée lorsque le fichier du document est. Le démon iCloud verra cette modification et la propage à iCloud. Sur Mac OS X, les miniatures sont générées automatiquement pour le développeur par le plug-in Quick Look.

Avec les bases de l’utilisation des documents iCloud en place, ainsi que les modifications apportées à l’API existante, nous sommes prêts à implémenter le contrôleur d’affichage du sélecteur de documents dans une application mobile Xamarin iOS 8.


## <a name="enabling-icloud-in-xamarin"></a>Activation de iCloud dans Xamarin

Avant de pouvoir utiliser le sélecteur de document dans une application Xamarin. iOS, vous devez activer la prise en charge de iCloud dans votre application et via Apple. 

Les étapes suivantes guident pas à pas le processus de configuration de iCloud.

1. Créez un conteneur iCloud.
2. Créez un ID d’application qui contient le App Service iCloud.
3. Créez un profil de provisionnement qui comprend cet ID d’application.

Le guide [utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) vous guide dans les deux premières étapes. Pour créer un profil de provisionnement, suivez les étapes décrites dans le Guide de [Profil](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) de provisionnement.



Les étapes suivantes déprocédurent le processus de configuration de votre application pour iCloud:

Effectuez ce qui suit :

1. Ouvrez le projet dans Visual Studio pour Mac ou Visual Studio.
2. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez Options.
3. Dans la boîte de dialogue Options, sélectionnez **application iOS**, assurez-vous que l' **identificateur de Bundle** correspond à celui défini dans **ID d’application** créé ci-dessus pour l’application. 
4. Sélectionnez **signature du bundle iOS**, sélectionnez l' **identité du développeur** et le profil de provisionnement créé ci-dessus.
5. Cliquez sur le bouton **OK** pour enregistrer les modifications et fermer la boîte de dialogue.
6. Cliquez `Entitlements.plist` avec le bouton droit sur dans le **Explorateur de solutions** pour l’ouvrir dans l’éditeur.

    > [!IMPORTANT]
    > Dans Visual Studio, vous devrez peut-être ouvrir l’éditeur de droits en cliquant dessus avec le bouton droit, puis en sélectionnant **Ouvrir avec...** et sélection de l’éditeur de liste de propriétés

7. Cochez la case **activer les icloud** , les **documents icloud** , **le stockage clé-valeur** et **CloudKit** .
8. Assurez-vous que le **conteneur** existe pour l’application (tel qu’il a été créé ci-dessus). Exemple : `iCloud.com.your-company.AppName`
9. Enregistrez les modifications dans le fichier.

Pour plus d’informations sur les droits, consultez le guide [utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md) .

Une fois le programme d’installation ci-dessus en place, l’application peut désormais utiliser des documents basés sur le Cloud et le contrôleur d’affichage du sélecteur de documents.

## <a name="common-setup-code"></a>Code d’installation commun

Avant de commencer à utiliser le contrôleur d’affichage du sélecteur de document, un code d’installation standard est requis. Commencez par modifier le fichier de `AppDelegate.cs` l’application et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt"; 
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }


        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");  

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path 
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {   
                    // Yes, inform caller and save location the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();
                
        }
        
        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }
        
        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }
        
        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }
        
        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> Le code ci-dessus contient le code de la section découverte et Listing des documents ci-dessus. Il est présenté ici dans son intégralité, tel qu’il apparaîtrait dans une application réelle. Pour plus de simplicité, cet exemple fonctionne avec un seul fichier codé en dur`test.txt`().

Le code ci-dessus expose plusieurs raccourcis de lecteurs iCloud pour faciliter leur utilisation dans le reste de l’application.

Ensuite, ajoutez le code suivant à n’importe quel conteneur d’affichage ou de vue qui utilisera le sélecteur de document ou utilisera des documents basés sur le Cloud:

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Vous ajoutez `AppDelegate` ainsi un raccourci pour accéder au et vous accédez aux raccourcis icloud créés ci-dessus.

Une fois ce code en place, voyons comment implémenter le contrôleur d’affichage du sélecteur de documents dans une application Xamarin iOS 8.

## <a name="using-the-document-picker-view-controller"></a>Utilisation du contrôleur d’affichage du sélecteur de document

Avant iOS 8, il était très difficile d’accéder aux documents à partir d’une autre application, car il n’existait aucun moyen de découvrir des documents en dehors de l’application à partir de l’application.

### <a name="existing-behavior"></a>Comportement existant

 [![](document-picker-images/image31.png "Présentation des comportements existants")](document-picker-images/image31.png#lightbox)

Jetons un coup d’œil à l’accès à un document externe avant iOS 8:

1. Tout d’abord, l’utilisateur doit ouvrir l’application qui a créé le document à l’origine.
1. Le document est sélectionné et le `UIDocumentInteractionController` est utilisé pour envoyer le document à la nouvelle application.
1. Enfin, une copie du document d’origine est placée dans le conteneur de la nouvelle application.


À partir de là, le document est disponible pour la deuxième application et l’ouvrir et le modifier.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Découverte de documents en dehors du conteneur d’une application

Dans iOS 8, une application peut facilement accéder aux documents en dehors de son propre conteneur d’applications:

 [![](document-picker-images/image32.png "Découverte de documents en dehors du conteneur d’une application")](document-picker-images/image32.png#lightbox)

À l’aide du nouveau sélecteur de `UIDocumentPickerViewController`document icloud (), une application iOS peut découvrir et accéder directement en dehors de son conteneur d’application. Le `UIDocumentPickerViewController` fournit un mécanisme permettant à l’utilisateur d’accorder l’accès et de modifier les documents découverts via des autorisations.

Une application doit s’inscrire pour que ses documents s’affichent dans le sélecteur de documents iCloud et soient disponibles pour permettre à d’autres applications de les découvrir et de les utiliser. Pour qu’une application Xamarin iOS 8 partage son conteneur d’application, modifiez `Info.plist` -la dans un éditeur de texte standard et ajoutez les deux lignes suivantes au bas du dictionnaire (entre les `<dict>...</dict>` balises):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController` Fournit une nouvelle interface utilisateur qui permet à l’utilisateur de choisir des documents. Pour afficher le contrôleur d’affichage du sélecteur de documents dans une application Xamarin iOS 8, procédez comme suit:

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> Le développeur doit appeler la `StartAccessingSecurityScopedResource` méthode `NSUrl` de pour que l’accès à un document externe soit possible. La `StopAccessingSecurityScopedResource` méthode doit être appelée pour libérer le verrou de sécurité dès que le document a été chargé.

### <a name="sample-output"></a>Résultat de l'exemple

Voici un exemple de la façon dont le code ci-dessus affiche un sélecteur de document lorsqu’il est exécuté sur un appareil iPhone:

1. L’utilisateur démarre l’application et l’interface principale s’affiche:   
 
    [![](document-picker-images/image33.png "L’interface principale est affichée.")](document-picker-images/image33.png#lightbox)
1. L’utilisateur clique sur le bouton d' **action** en haut de l’écran et est invité à sélectionner un **fournisseur de documents** dans la liste des fournisseurs disponibles:   
 
    [![](document-picker-images/image34.png "Sélectionner un fournisseur de documents dans la liste des fournisseurs disponibles")](document-picker-images/image34.png#lightbox)
1. Le **contrôleur d’affichage du sélecteur de documents** s’affiche pour le **fournisseur de documents**sélectionné:   
 
    [![](document-picker-images/image35.png "Le contrôleur d’affichage du sélecteur de documents s’affiche")](document-picker-images/image35.png#lightbox)
1. L’utilisateur appuie sur un **dossier de documents** pour afficher son contenu:   
 
    [![](document-picker-images/image36.png "Contenu du dossier du document")](document-picker-images/image36.png#lightbox)
1. L’utilisateur sélectionne un **document** et le **Sélecteur de document** est fermé.
1. L’interface principale est réaffichée, le **document** est chargé à partir du conteneur externe et son contenu est affiché.


L’affichage réel du contrôleur d’affichage du sélecteur de documents dépend des fournisseurs de documents que l’utilisateur a installés sur l’appareil et du mode de sélection de document qui a été implémenté. L’exemple ci-dessus utilise le mode Open, les autres types de mode sont présentés en détail ci-dessous.

## <a name="managing-external-documents"></a>Gestion des documents externes

Comme indiqué ci-dessus, avant iOS 8, une application pouvait uniquement accéder aux documents qui faisaient partie de son conteneur d’applications. Dans iOS 8, une application peut accéder à des documents provenant de sources externes:

 [![](document-picker-images/image37.png "Vue d’ensemble de la gestion des documents externes")](document-picker-images/image37.png#lightbox)

Lorsque l’utilisateur sélectionne un document à partir d’une source externe, un document de référence est écrit dans le conteneur d’application qui pointe vers le document d’origine.

Pour faciliter l’ajout de cette nouvelle fonctionnalité dans les applications existantes, plusieurs nouvelles fonctionnalités ont été ajoutées `NSMetadataQuery` à l’API. En règle générale, une application utilise l’étendue de document omniprésente pour répertorier les documents qui résident dans son conteneur d’application. À l’aide de cette portée, seuls les documents du conteneur d’application continuent de s’afficher.

L’utilisation de la nouvelle étendue de document externe omniprésente retourne des documents qui se trouvent en dehors du conteneur d’application et retournent les métadonnées correspondantes. Le `NSMetadataItemUrlKey` pointera vers l’URL où le document se trouve réellement.

Parfois, une application ne souhaite pas utiliser les documents référencés par la référence. Au lieu de cela, l’application souhaite travailler directement avec le document de référence. Par exemple, l’application peut souhaiter afficher le document dans le dossier de l’application dans l’interface utilisateur, ou pour permettre à l’utilisateur de déplacer les références à l’intérieur d’un dossier.

Dans iOS 8, un nouveau `NSMetadataItemUrlInLocalContainerKey` a été fourni pour accéder directement au document de référence. Cette clé pointe vers la référence réelle au document externe dans un conteneur d’application.

Le `NSMetadataUbiquitousItemIsExternalDocumentKey` est utilisé pour tester si un document est externe au conteneur d’une application. Le `NSMetadataUbiquitousItemContainerDisplayNameKey` est utilisé pour accéder au nom du conteneur qui héberge la copie d’origine d’un document externe.

### <a name="why-document-references-are-required"></a>Pourquoi des références de document sont-elles requises?

La principale raison pour laquelle iOS 8 utilise des références pour accéder aux documents externes est la sécurité. Aucune application n’a accès au conteneur d’une autre application. Seul le sélecteur de documents peut le faire, car s’exécute hors processus et a accès à l’ensemble du système.

La seule façon d’accéder à un document en dehors du conteneur d’application consiste à utiliser le sélecteur de documents, et si l’URL retournée par le sélecteur est étendue à la sécurité. L’URL étendue de sécurité contient juste assez d’informations pour sélectionner le document, ainsi que les droits délimités requis pour accorder à une application l’accès au document.

Il est important de noter que si l’URL d’étendue de sécurité a été sérialisée dans une chaîne, puis désérialisée, les informations de sécurité seraient perdues et le fichier serait inaccessible à partir de l’URL. La fonctionnalité de référence de document fournit un mécanisme pour revenir aux fichiers pointés par ces URL.

Par conséquent, si l’application acquiert un `NSUrl` à partir de l’un des documents de référence, elle a déjà l’étendue de sécurité attachée et peut être utilisée pour accéder au fichier. Pour cette raison, il est fortement recommandé que le développeur utilise `UIDocument` , car il gère toutes ces informations et processus pour eux.

### <a name="using-bookmarks"></a>Utilisation des signets

Il n’est pas toujours possible d’énumérer les documents d’une application pour revenir à un document spécifique, par exemple, lors de la restauration de l’État. iOS 8 fournit un mécanisme permettant de créer des signets qui ciblent directement un document donné.

Le code suivant crée un signet à partir de `UIDocument`la `FileUrl` propriété de:

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

L’API Bookmark existante est utilisée pour créer un signet sur un existant `NSUrl` qui peut être enregistré et chargé pour fournir un accès direct à un fichier externe. Le code suivant restaure un signet créé ci-dessus:

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>Ouvrir et Mode importation et sélecteur de documents

Le contrôleur d’affichage du sélecteur de documents comporte deux modes de fonctionnement:

1. **Mode d’ouverture** : dans ce mode, lorsque l’utilisateur sélectionne un document externe, le sélecteur de document crée un signet d’étendue de sécurité dans le conteneur d’application.   
 
    [![](document-picker-images/image37.png "Signet d’étendue de sécurité dans le conteneur d’application")](document-picker-images/image37.png#lightbox)
1. **Mode d’importation** : dans ce mode, lorsque l’utilisateur sélectionne un document externe, le sélecteur de document ne crée pas de signet, mais copie le fichier dans un emplacement temporaire et fournit à l’application l’accès au document à l’emplacement suivant:   
 
    [![](document-picker-images/image38.png "Le sélecteur de documents copie le fichier dans un emplacement temporaire et fournit à l’application l’accès au document à cet emplacement.")](document-picker-images/image38.png#lightbox)   
 Une fois que l’application s’arrête pour une raison quelconque, l’emplacement temporaire est vidé et le fichier est supprimé. Si l’application doit conserver l’accès au fichier, elle doit en faire une copie et la placer dans son conteneur d’application.


Le mode d’ouverture est utile lorsque l’application souhaite collaborer avec une autre application et partager toutes les modifications apportées au document avec cette application. Le mode d’importation est utilisé lorsque l’application ne souhaite pas partager ses modifications dans un document avec d’autres applications.

## <a name="making-a-document-external"></a>Création d’un document externe

Comme indiqué ci-dessus, une application iOS 8 n’a pas accès aux conteneurs en dehors de son propre conteneur d’application. L’application peut écrire dans son propre conteneur localement ou dans un emplacement temporaire, puis utiliser un mode de document spécial pour déplacer le document résultant en dehors du conteneur d’application vers un emplacement choisi par l’utilisateur.

Pour déplacer un document vers un emplacement externe, procédez comme suit:

1. Commencez par créer un nouveau document dans un emplacement local ou temporaire.
1. Créez un `NSUrl` qui pointe vers le nouveau document.
1. Ouvrez un nouveau contrôleur d’affichage du sélecteur de documents et `NSUrl` transmettez-lui `MoveToService` le avec le mode. 
1. Une fois que l’utilisateur choisit un nouvel emplacement, le document est déplacé de son emplacement actuel vers le nouvel emplacement.
1. Un document de référence est écrit dans le conteneur d’application de l’application afin que l’application de création puisse toujours y accéder.


Vous pouvez utiliser le code suivant pour déplacer un document vers un emplacement externe:`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

Le document de référence retourné par le processus ci-dessus est exactement le même que celui créé par le mode d’ouverture du sélecteur de documents. Toutefois, il arrive que l’application souhaite déplacer un document sans en conserver une référence.

Pour déplacer un document sans générer de référence, utilisez le `ExportToService` mode. Exemple : `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Lorsque vous utilisez `ExportToService` le mode, le document est copié dans le conteneur externe et la copie existante est conservée à son emplacement d’origine.

## <a name="document-provider-extensions"></a>Extensions du fournisseur de documents

Avec iOS 8, Apple souhaite que l’utilisateur final puisse accéder à n’importe quel document basé sur le Cloud, quel que soit l’endroit où ils existent réellement. Pour atteindre cet objectif, iOS 8 fournit un nouveau mécanisme d’extension de fournisseur de documents.

### <a name="what-is-a-document-provider-extension"></a>Qu’est-ce qu’une extension de fournisseur de documents?

Simplement, une extension de fournisseur de documents est un moyen pour un développeur, ou un tiers, de fournir à un stockage de document alternatif d’application accessible de la même façon que l’emplacement de stockage iCloud existant.

L’utilisateur peut sélectionner l’un de ces emplacements de stockage alternatifs dans le sélecteur de documents et utiliser exactement les mêmes modes d’accès (ouvrir, importer, déplacer ou exporter) pour travailler avec les fichiers de cet emplacement.

Elle est implémentée à l’aide de deux extensions différentes:

- **Extension de sélecteur de documents** : `UIViewController` fournit une sous-classe qui fournit une interface graphique permettant à l’utilisateur de choisir un document à partir d’un autre emplacement de stockage. Cette sous-classe sera affichée dans le cadre du contrôleur d’affichage du sélecteur de documents.
- **Extension de fichier fournie** : il s’agit d’une extension non-interface utilisateur qui traite réellement le contenu des fichiers. Ces extensions sont fournies par le biais de `NSFileCoordinator` la coordination des fichiers (). Il s’agit d’un autre cas important où la coordination des fichiers est requise.


Le diagramme suivant illustre le workflow de données classique lors de l’utilisation des extensions de fournisseur de documents:

 [![](document-picker-images/image39.png "Ce diagramme illustre le workflow type de données lors de l’utilisation des extensions de fournisseur de documents")](document-picker-images/image39.png#lightbox)

Le processus suivant se produit:

1. L’application présente un contrôleur de sélecteur de documents pour permettre à l’utilisateur de sélectionner un fichier à utiliser.
1. L’utilisateur sélectionne un autre emplacement de fichier et l' `UIViewController` extension personnalisée est appelée pour afficher l’interface utilisateur.
1. L’utilisateur sélectionne un fichier à partir de cet emplacement et l’URL est renvoyée au sélecteur de documents.
1. Le sélecteur de documents sélectionne l’URL du fichier et le renvoie à l’application pour que l’utilisateur travaille sur.
1. L’URL est passée au coordinateur de fichiers pour retourner le contenu des fichiers à l’application.
1. Le coordinateur de fichiers appelle l’extension de fournisseur de fichiers personnalisée pour récupérer le fichier.
1. Le contenu du fichier est retourné au coordinateur de fichiers.
1. Le contenu du fichier est renvoyé à l’application.


### <a name="security-and-bookmarks"></a>Sécurité et signets

Cette section présente rapidement comment la sécurité et l’accès permanent aux fichiers par le biais de signets fonctionnent avec les extensions de fournisseur de documents. Contrairement au fournisseur de documents iCloud, qui enregistre automatiquement la sécurité et les signets dans le conteneur d’application, les extensions de fournisseur de document ne sont pas parce qu’elles ne font pas partie du système de référence de document.

Par exemple: dans un paramètre d’entreprise qui fournit sa propre banque de données sécurisée à l’ensemble de l’entreprise, les administrateurs ne souhaitent pas que les informations d’entreprise confidentielles soient consultées ou traitées par les serveurs iCloud publics. Par conséquent, le système de référence de document intégré ne peut pas être utilisé.

Le système de signets peut toujours être utilisé et il est de la responsabilité de l’extension de fournisseur de fichiers de traiter correctement une URL de signet et de retourner le contenu du document vers lequel elle pointe.

Pour des raisons de sécurité, iOS 8 possède une couche d’isolation qui rend persistantes les informations relatives à l’application qui a accès à l’identificateur à l’intérieur du fournisseur de fichiers. Il convient de noter que tous les accès aux fichiers sont contrôlés par cette couche d’isolation.

Le diagramme suivant illustre le déroulement des données lorsque vous travaillez avec des signets et une extension de fournisseur de documents:

 [![](document-picker-images/image40.png "Ce diagramme montre le workflow lorsque vous travaillez avec des signets et une extension de fournisseur de documents")](document-picker-images/image40.png#lightbox)

Le processus suivant se produit:

1. L’application est sur le paragraphe d’entrer en arrière-plan et doit conserver son état. Elle appelle `NSUrl` pour créer un signet vers un fichier dans un autre stockage.
1. `NSUrl`appelle l’extension du fournisseur de fichiers pour obtenir une URL permanente vers le document. 
1. L’extension du fournisseur de fichiers retourne l’URL sous la `NSUrl` forme d’une chaîne à.
1. Le `NSUrl` regroupe l’URL dans un signet et le retourne à l’application.
1. Lorsque l’application se rend en arrière-plan et doit restaurer l’État, elle passe le signet à `NSUrl` .
1. `NSUrl`appelle l’extension du fournisseur de fichiers avec l’URL du fichier.
1. Le fournisseur d’extension de fichier accède au fichier et retourne l’emplacement du fichier à `NSUrl` .
1. L’emplacement du fichier est fourni avec les informations de sécurité et retourné à l’application.


À partir de là, l’application peut accéder au fichier et l’utiliser comme d’habitude.

### <a name="writing-files"></a>Écriture de fichiers

Cette section présente rapidement comment fonctionne l’écriture de fichiers dans un autre emplacement avec une extension de fournisseur de documents. L’application iOS utilise la coordination de fichiers pour enregistrer les informations sur le disque à l’intérieur du conteneur d’application. Peu après l’écriture du fichier, l’extension du fournisseur de fichiers est notifiée de la modification.

À ce stade, l’extension du fournisseur de fichiers peut démarrer le chargement du fichier à l’emplacement de remplacement (ou marquer le fichier comme étant modifié et nécessiter le chargement).

### <a name="creating-new-document-provider-extensions"></a>Création d’extensions de fournisseur de documents

La création d’extensions de fournisseur de documents n’entre pas dans le cadre de cet article de présentation. Ces informations sont fournies ici pour montrer que, en fonction des extensions qu’un utilisateur a chargées dans son appareil iOS, une application peut avoir accès aux emplacements de stockage de documents en dehors de l’emplacement iCloud fourni par Apple.

Le développeur doit être conscient de ce fait lors de l’utilisation du sélecteur de documents et de l’utilisation de documents externes. Ils ne doivent pas supposer que ces documents sont hébergés dans iCloud.

Pour plus d’informations sur la création d’un fournisseur de stockage ou d’une extension de sélecteur de documents, consultez le document [Présentation des extensions d’application](~/ios/platform/extensions.md) .

## <a name="migrating-to-icloud-drive"></a>Migration vers le lecteur iCloud

Sur iOS 8, les utilisateurs peuvent choisir de continuer à utiliser le système de documents iCloud existant utilisé dans iOS 7 (et les systèmes précédents), ou ils peuvent choisir de migrer les documents existants vers le nouveau mécanisme de lecteur iCloud.

Sur Mac OS X Yosemite, Apple n’offre pas la compatibilité descendante, de sorte que tous les documents doivent être migrés vers le lecteur iCloud ou qu’ils ne soient plus mis à jour sur les appareils.

Une fois qu’un compte d’utilisateur a été migré vers le lecteur iCloud, seuls les appareils utilisant le lecteur iCloud peuvent propager les modifications apportées aux documents sur ces appareils.

> [!IMPORTANT]
> Les développeurs doivent savoir que les nouvelles fonctionnalités décrites dans cet article sont disponibles uniquement si le compte de l’utilisateur a été migré vers le lecteur iCloud. 

## <a name="summary"></a>Récapitulatif

Cet article a abordé les modifications apportées aux API iCloud existantes requises pour prendre en charge le lecteur iCloud et le nouveau contrôleur d’affichage du sélecteur de documents. Il a traité la coordination des fichiers et explique pourquoi il est important de travailler avec des documents basés sur le Cloud. Il a abordé le programme d’installation requis pour activer les documents basés sur le Cloud dans une application Xamarin. iOS et a présenté un aperçu de l’utilisation de documents en dehors du conteneur d’applications d’une application à l’aide du contrôleur d’affichage du sélecteur de documents.

En outre, cet article présente brièvement les extensions du fournisseur de documents et explique pourquoi le développeur doit en être conscient lors de l’écriture d’applications capables de gérer des documents basés sur le Cloud.

## <a name="related-links"></a>Liens associés

- [DocPicker (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-docpicker)
- [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Présentation des extensions d’application](~/ios/platform/extensions.md)
