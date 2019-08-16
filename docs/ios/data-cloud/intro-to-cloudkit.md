---
title: CloudKit dans Xamarin. iOS
description: Ce document explique comment utiliser CloudKit dans Xamarin. iOS. Il fournit une vue d’ensemble de CloudKit et explique comment l’activer, l’API de commodité CloudKit, l’extensibilité, les comptes d’utilisateur et les environnements de développement et de production.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: 29e737e5a6cb6abdae099c0224a2da058c2ea025
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527737"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit dans Xamarin. iOS

L’infrastructure CloudKit simplifie le développement d’applications qui accèdent à iCloud. Cela comprend la récupération des données d’application et des droits d’actifs, ainsi que la possibilité de stocker des informations d’application en toute sécurité. Ce kit offre aux utilisateurs une couche d’anonymat en autorisant l’accès aux applications avec leurs ID iCloud sans partager les informations personnelles.

Les développeurs peuvent se concentrer sur leurs applications côté client et laisser iCloud éliminer la nécessité d’écrire une logique d’application côté serveur. CloudKit fournit des bases de données d’authentification, privées et publiques, ainsi que des services de données structurées et de stockage des ressources.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour effectuer les étapes présentées dans cet article:

- **Xcode et le kit de développement logiciel (SDK) iOS** – les API Xcode et iOS 8 d’Apple doivent être installées et configurées sur l’ordinateur du développeur.
- **Visual Studio pour Mac** : la dernière version de Visual Studio pour Mac doit être installée et configurée sur l’appareil de l’utilisateur.
- **appareil iOS 8** : un appareil iOS exécutant la dernière version d’iOS 8 à des fins de test.

## <a name="what-is-cloudkit"></a>Qu’est-ce que CloudKit?

CloudKit permet au développeur d’accéder aux serveurs iCloud. Il fournit la base pour le lecteur iCloud et la bibliothèque de photos iCloud. CloudKit est pris en charge sur les appareils Mac OS X et Apple iOS.

 [![](intro-to-cloudkit-images/image1.png "Comment CloudKit est pris en charge sur les appareils Mac OS X et Apple iOS")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit utilise l’infrastructure de compte iCloud. Si un utilisateur se connecte à un compte iCloud sur l’appareil, CloudKit utilise son ID pour identifier l’utilisateur. Si aucun compte n’est disponible, l’accès en lecture seule limité sera fourni.

CloudKit prend en charge le concept de bases de données publiques et privées. Les bases de données publiques fournissent une «soupe» de toutes les données auxquelles l’utilisateur a accès. Les bases de données privées sont conçues pour stocker les données privées liées à un utilisateur spécifique.

CloudKit prend en charge les données structurées et les données en bloc. Il est en charge de gérer les transferts de fichiers volumineux en toute transparence. CloudKit se charge de transférer efficacement des fichiers volumineux vers et depuis les serveurs iCloud en arrière-plan, ce qui permet au développeur de se concentrer sur d’autres tâches.

> [!NOTE]
> Il est important de noter que CloudKit est une _technologie de transport_. Elle ne fournit aucune persistance; Il permet uniquement à une application d’envoyer et de recevoir efficacement des informations des serveurs.

Au moment de la rédaction de cet article, Apple fournit initialement CloudKit gratuitement avec une limite élevée sur la bande passante et la capacité de stockage. Pour les projets ou les applications de grande taille avec une base d’utilisateurs importante, Apple a indiqué qu’une échelle de tarification abordable sera fournie.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Activation de CloudKit dans une application Xamarin

Avant qu’une application Xamarin puisse utiliser CloudKit Framework, l’application doit être correctement configurée comme indiqué dans les guides [utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) et [utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md) .

1. Ouvrez le projet dans Visual Studio pour Mac ou Visual Studio.
2. Dans le **Explorateur de solutions**, ouvrez le fichier **info. plist** et assurez-vous que l' **identificateur de Bundle** correspond à celui défini dans ID d' **application** créé dans le cadre de la configuration de l’approvisionnement:
 
    [![](intro-to-cloudkit-images/image26a.png "Entrer l’identificateur de Bundle")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3. Faites défiler le fichier **info. plist** jusqu’en bas et sélectionnez **activé les modes d’arrière-plan**, les **mises à jour** de l’emplacement et les notifications distantes:

    [![](intro-to-cloudkit-images/image27a.png "Sélectionner les modes d’arrière-plan activés, les mises à jour d’emplacement et les notifications distantes")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4. Cliquez avec le bouton droit sur le projet iOS dans la solution, puis sélectionnez **options**.
5. Sélectionnez **signature du bundle iOS**, sélectionnez l' **identité du développeur** et le profil de provisionnement créés ci-dessus.
6. Assurez-vous que les **droits. plist** incluent l' **activation de icloud** , le **stockage clé-valeur** et **CloudKit** .
7. Assurez-vous que le **conteneur ubiquité** existe pour l’application (tel qu’il a été créé ci-dessus). Exemple : `iCloud.com.your-company.CloudKitAtlas`
8. Enregistrez les modifications dans le fichier.


Lorsque ces paramètres sont en place, l’application est maintenant prête à accéder aux API CloudKit Framework.

## <a name="cloudkit-api-overview"></a>Vue d’ensemble de l’API CloudKit

Avant d’implémenter CloudKit dans une application Xamarin iOS, cet article va aborder les notions de base de l’infrastructure CloudKit, qui inclut les rubriques suivantes:

1. **Conteneurs** : silos isolés de communications iCloud.
2. Les **bases de données** (publiques et privées) sont accessibles à l’application.
3. **Enregistrements** : mécanisme dans lequel les données structurées sont déplacées vers et à partir de CloudKit.
4. Les **zones d’enregistrement** : sont des groupes d’enregistrements.
5. **Identificateurs d’enregistrement** : sont entièrement normalisés et représentent l’emplacement spécifique de l’enregistrement.
6. **Reference** : fournissez des relations parent-enfant entre des enregistrements associés dans une base de données donnée.
7. **Ressources** : autorise le chargement de fichiers de données volumineuses non structurées dans icloud et leur association à un enregistrement donné.


### <a name="containers"></a>Containers

Une application donnée s’exécutant sur un appareil iOS s’exécute toujours sur d’autres applications et services sur cet appareil. Sur l’appareil client, l’application va être transposée en silo ou en sandbox d’une certaine manière. Dans certains cas, il s’agit d’un bac à sable (sandbox) littéral, et dans d’autres, l’application s’exécute simplement dans son propre espace mémoire.

Le concept de création d’une application cliente et d’exécution séparée d’autres clients est très puissant et offre les avantages suivants:

1. **Sécurité** : une application ne peut pas interférer avec d’autres applications clientes ou le système d’exploitation lui-même.
1. **Stabilité** : si l’application cliente se bloque, elle ne peut pas prendre en charge d’autres applications du système d’exploitation.
1. **Confidentialité** : chaque application cliente a un accès limité aux informations personnelles stockées au sein de l’appareil.


CloudKit a été conçu pour offrir les mêmes avantages que ceux indiqués ci-dessus, et les appliquer à l’utilisation des informations basées sur le Cloud:

 [![](intro-to-cloudkit-images/image31.png "Les applications CloudKit communiquent à l’aide de conteneurs")](intro-to-cloudkit-images/image31.png#lightbox)

Tout comme l’application est un-sur-plusieurs s’exécutant sur l’appareil, les communications de l’application avec iCloud un-sur-plusieurs. Chacun de ces différents silos de communication est appelé conteneurs.

Les conteneurs sont exposés dans l’infrastructure CloudKit via `CKContainer` la classe. Par défaut, une application communique avec un conteneur et ce conteneur sépare les données de cette application. Cela signifie que plusieurs applications peuvent stocker des informations sur le même compte iCloud, mais ces informations ne seront jamais mélangées.

Le conteneur de données iCloud permet également à CloudKit d’encapsuler les informations utilisateur. De cette façon, l’application dispose d’un accès limité au compte iCloud et des informations utilisateur stockées dans, tout en continuant à protéger la confidentialité et la sécurité de l’utilisateur.

Les conteneurs sont entièrement gérés par le développeur de l’application via le portail WWDR. L’espace de noms du conteneur étant global pour tous les développeurs Apple, le conteneur ne doit pas être unique pour les applications d’un développeur donné, mais pour tous les développeurs et applications Apple.

Apple suggère d’utiliser la notation DNS inverse lors de la création de l’espace de noms pour les conteneurs d’applications. Exemple :

```csharp
iCloud.com.company-name.application-name
```

Alors que les conteneurs sont, par défaut, liés un-à-un à une application donnée, ils peuvent être partagés entre les applications. Plusieurs applications peuvent donc coordonner un seul conteneur. Une même application peut également communiquer avec plusieurs conteneurs.

### <a name="databases"></a>Bases de données

L’une des principales fonctions de CloudKit consiste à prendre le modèle de données et la réplication d’une application qui modélisent les serveurs iCloud. Certaines informations sont destinées à l’utilisateur qui l’a créée. d’autres informations sont des données publiques qui peuvent être créées par un utilisateur pour une utilisation publique (par exemple, une revue de restaurant) ou des informations que le développeur a publiées pour l’application. Dans les deux cas, il ne s’agit pas seulement d’un seul utilisateur, mais il s’agit d’une communauté de personnes.

 [![](intro-to-cloudkit-images/image32.png "Diagramme de conteneur CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

Dans un conteneur, tout d’abord est la base de données publique. C’est là que toutes les informations publiques résident et sont confrontées. En outre, il existe plusieurs bases de données privées individuelles pour chaque utilisateur de l’application.

Lors de l’exécution sur un appareil iOS, l’application n’aura accès qu’aux informations de l’utilisateur iCloud actuellement connecté. La vue de l’application du conteneur est donc la suivante:

 [![](intro-to-cloudkit-images/image33.png "Affichage des applications du conteneur")](intro-to-cloudkit-images/image33.png#lightbox)

Elle ne peut voir que la base de données publique et la base de données privée associées à l’utilisateur iCloud actuellement connecté.

Les bases de données sont exposées dans l’infrastructure CloudKit `CKDatabase` via la classe. Chaque application a accès à deux bases de données: la base de données publique et la base de données privée.

Le conteneur est le point d’entrée initial dans CloudKit. Le code suivant peut être utilisé pour accéder à la base de données publique et privée à partir du conteneur par défaut de l’application:

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

Voici les différences entre les types de bases de données:

||Base de données publique|Base de données privée|
|---|--- |--- |
|**Type de données**|Données partagées|Données de l’utilisateur actuel|
|**Quota**|Compte dans le quota du développeur|Compte dans le quota de l’utilisateur|
|**Autorisations par défaut**|Lecture mondiale|Lisible par l’utilisateur|
|**Modification des autorisations**|Rôles de tableau de bord iCloud via un niveau record-Class|N/A|

### <a name="records"></a>Enregistrements

Les conteneurs contiennent des bases de données, et les bases de données internes sont des enregistrements. Les enregistrements sont le mécanisme dans lequel les données structurées sont déplacées vers et à partir de CloudKit:

 [![](intro-to-cloudkit-images/image34.png "Les conteneurs contiennent des bases de données, et les bases de données sont des enregistrements")](intro-to-cloudkit-images/image34.png#lightbox)

Les enregistrements sont exposés dans l’infrastructure CloudKit via `CKRecord` la classe, qui encapsule les paires clé-valeur. Une instance d’un objet dans une application équivaut à une `CKRecord` dans CloudKit. En outre, chacun `CKRecord` possède un type d’enregistrement, qui est équivalent à la classe d’un objet.

Les enregistrements ont un schéma juste-à-temps, de sorte que les données sont décrites à CloudKit avant d’être remises pour traitement. À partir de ce point, CloudKit interprétera les informations et traitera la logistique liée au stockage et à la récupération de l’enregistrement.

La `CKRecord` classe prend également en charge un large éventail de métadonnées. Par exemple, un enregistrement contient des informations sur le moment où il a été créé et l’utilisateur qui l’a créé. Un enregistrement contient également des informations sur le moment où il a été modifié pour la dernière fois et sur l’utilisateur qui l’a modifié.

Les enregistrements contiennent la notion d’une balise de modification. Il s’agit d’une version précédente d’une révision d’un enregistrement donné. La balise de modification est utilisée comme un moyen léger de déterminer si le client et le serveur ont la même version d’un enregistrement donné.

Comme indiqué ci- `CKRecords` dessus, encapsulez les paires clé-valeur et, par conséquent, les types de données suivants peuvent être stockés dans un enregistrement:

1. `NSString`
1. `NSNumber`
1. `NSData`
1. `NSDate`
1. `CLLocation`
1. `CKReferences`
1. `CKAssets`


Outre les types à valeur unique, un enregistrement peut contenir un tableau homogène de l’un des types listés ci-dessus.

Le code suivant peut être utilisé pour créer un nouvel enregistrement et le stocker dans une base de données:

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Zones d’enregistrement

Les enregistrements n’existent pas eux-mêmes dans une base de données spécifique. des groupes d’enregistrements existent dans une zone d’enregistrement. Les zones d’enregistrement peuvent être considérées comme des tables dans des bases de données relationnelles classiques:

 [![](intro-to-cloudkit-images/image35.png "Des groupes d’enregistrements existent dans une zone d’enregistrement")](intro-to-cloudkit-images/image35.png#lightbox)

Il peut y avoir plusieurs enregistrements dans une zone d’enregistrement donnée et plusieurs zones d’enregistrement dans une base de données donnée. Chaque base de données contient une zone d’enregistrement par défaut:

 [![](intro-to-cloudkit-images/image36.png "Chaque base de données contient une zone d’enregistrement par défaut et une zone personnalisée")](intro-to-cloudkit-images/image36.png#lightbox)

C’est ici que les enregistrements sont stockés par défaut. En outre, des zones d’enregistrement personnalisées peuvent être créées. Les zones d’enregistrement représentent la granularité de base à laquelle s’effectuent les validations atomiques et les Change Tracking.

## <a name="record-identifiers"></a>Identificateurs d’enregistrement

Les identificateurs d’enregistrement sont représentés sous forme de tuple, contenant à la fois un nom d’enregistrement fourni par le client et la zone dans laquelle l’enregistrement existe. Les identificateurs d’enregistrement présentent les caractéristiques suivantes:

- Elles sont créées par l’application cliente.
- Ils sont entièrement normalisés et représentent l’emplacement spécifique de l’enregistrement.
- En affectant l’ID unique d’un enregistrement d’une base de données étrangère au nom de l’enregistrement, vous pouvez l’utiliser pour établir un pont entre des bases de données locales qui ne sont pas stockées dans CloudKit.


Lorsque les développeurs créent de nouveaux enregistrements, ils peuvent choisir de passer un identificateur d’enregistrement. Si aucun identificateur d’enregistrement n’est spécifié, un UUID est automatiquement créé et affecté à l’enregistrement.

Lorsque les développeurs créent des identificateurs d’enregistrement, ils peuvent choisir de spécifier la zone d’enregistrement à laquelle chaque enregistrement appartient. Si aucun n’est spécifié, la zone d’enregistrement par défaut est utilisée.

Les identificateurs d’enregistrement sont exposés dans l’infrastructure CloudKit `CKRecordID` via la classe. Le code suivant peut être utilisé pour créer un nouvel identificateur d’enregistrement:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Références

Les références fournissent des relations entre les enregistrements associés dans une base de données spécifique:

 [![](intro-to-cloudkit-images/image37.png "Les références fournissent des relations entre les enregistrements associés dans une base de données spécifique")](intro-to-cloudkit-images/image37.png#lightbox)

Dans l’exemple ci-dessus, les parents possèdent des enfants afin que l’enfant soit un enregistrement enfant de l’enregistrement parent. La relation va de l’enregistrement enfant à l’enregistrement parent et est appelée *référence arrière*.

Les références sont exposées dans l’infrastructure CloudKit via `CKReference` la classe. Ils permettent au serveur iCloud de comprendre la relation entre les enregistrements.

Les références fournissent le mécanisme sous-jacent aux suppressions en cascade. Si un enregistrement parent est supprimé de la base de données, tous les enregistrements enfants (tels qu’ils sont spécifiés dans une relation) sont également automatiquement supprimés de la base de données.

> [!NOTE]
> Les pointeurs non résolus sont susceptibles de se produire lors de l’utilisation de CloudKit. Par exemple, au moment où l’application a extrait une liste de pointeurs d’enregistrements, a sélectionné un enregistrement, puis a demandé l’enregistrement, il se peut que l’enregistrement n’existe plus dans la base de données. Une application doit être codée pour gérer cette situation de façon appropriée.

Bien que cela ne soit pas obligatoire, les références Back sont préférées lorsque vous utilisez l’infrastructure CloudKit. Apple a affiné le système pour que ce type de référence soit le plus efficace.

Lors de la création d’une référence, le développeur peut soit fournir un enregistrement qui est déjà en mémoire, soit créer une référence à un identificateur d’enregistrement. Si vous utilisez un identificateur d’enregistrement et que la référence spécifiée n’existe pas dans la base de données, un pointeur non résolu serait créé.

Voici un exemple de création d’une référence à un enregistrement connu:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Assets

Les ressources permettent de charger un fichier de données volumineuses non structurées dans iCloud et de les associer à un enregistrement donné:

 [![](intro-to-cloudkit-images/image38.png "Les ressources permettent de charger un fichier de données volumineuses non structurées dans iCloud et de les associer à un enregistrement donné.")](intro-to-cloudkit-images/image38.png#lightbox)

Sur le client, un `CKRecord` est créé et décrit le fichier qui va être téléchargé sur le serveur iCloud. Un `CKAsset` est créé pour contenir le fichier et est lié à l’enregistrement qui le décrit.

Lorsque le fichier est téléchargé sur le serveur, l’enregistrement est placé dans la base de données et le fichier est copié dans une base de données de stockage en bloc spéciale. Un lien est créé entre le pointeur d’enregistrement et le fichier téléchargé.

Les ressources sont exposées dans l’infrastructure CloudKit via `CKAsset` la classe et sont utilisées pour stocker des données volumineuses non structurées. Étant donné que le développeur ne souhaite jamais avoir de grandes données non structurées en mémoire, les ressources sont implémentées à l’aide de fichiers sur le disque.

Les ressources sont détenues par des enregistrements, ce qui permet de récupérer les ressources à partir de iCloud à l’aide de l’enregistrement en tant que pointeur. De cette façon, le serveur peut nettoyer les ressources lorsque l’enregistrement qui possède la ressource est supprimé.

Étant `CKAssets` donné que sont prévus pour gérer des fichiers de données volumineux, Apple a conçu CloudKit pour charger et télécharger efficacement les ressources.

Le code suivant peut être utilisé pour créer un élément multimédia et l’associer à l’enregistrement:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Nous avons maintenant abordé tous les objets fondamentaux dans CloudKit. Les conteneurs sont associés à des applications et contiennent des bases de données. Les bases de données contiennent des enregistrements qui sont regroupés dans des zones d’enregistrement et pointés par des identificateurs d’enregistrement. Les relations parent-enfant sont définies entre les enregistrements à l’aide de références. Enfin, les fichiers volumineux peuvent être téléchargés et associés à des enregistrements à l’aide de ressources.

## <a name="cloudkit-convenience-api"></a>API de commodité CloudKit

Apple offre deux ensembles d’API différents pour l’utilisation de CloudKit:

- **API opérationnelle** : offre toutes les fonctionnalités de CloudKit. Pour les applications plus complexes, cette API fournit un contrôle affiné sur CloudKit.
- **API pratique** : offre un sous-ensemble commun et préconfiguré de fonctionnalités CloudKit. Il fournit une solution pratique et facile d’accès pour inclure les fonctionnalités CloudKit dans une application iOS.


L’API pratique est généralement le meilleur choix pour la plupart des applications iOS et Apple suggère de commencer par celle-ci. Le reste de cette section traite des rubriques d’API pratiques suivantes:

- Enregistrement d’un enregistrement.
- Récupération d’un enregistrement.
- Mise à jour d’un enregistrement.


### <a name="common-setup-code"></a>Code d’installation commun

Avant de commencer à utiliser l’API de commodité CloudKit, un code d’installation standard est requis. Commencez par modifier le fichier de `AppDelegate.cs` l’application et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
        #endregion
    }
}
```

Le code ci-dessus expose les bases de données CloudKit publiques et privées en tant que raccourcis pour faciliter leur utilisation dans le reste de l’application.

Ensuite, ajoutez le code suivant à n’importe quel conteneur d’affichage ou de vue qui utilisera CloudKit:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Cela ajoute un raccourci pour `AppDelegate` accéder au et accéder aux raccourcis de base de données publique et privée créés ci-dessus.

Avec ce code en place, voyons comment implémenter l’API de commodité CloudKit dans une application Xamarin iOS 8.

### <a name="saving-a-record"></a>Enregistrement d’un enregistrement

À l’aide du modèle présenté ci-dessus lors de la discussion sur les enregistrements, le code suivant crée un nouvel enregistrement et utilise l’API de commodité pour l’enregistrer dans la base de données publique:

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Trois points à noter concernant le code ci-dessus:

1. En appelant la `SaveRecord` méthode `PublicDatabase`de, le développeur n’a pas à spécifier la façon dont les données sont envoyées, la zone dans laquelle elles sont écrites, etc. L’API de commodité prend en charge tous ces détails.
1. L’appel est asynchrone et fournit une routine de rappel lorsque l’appel est terminé, avec succès ou échec. Si l’appel échoue, un message d’erreur est fourni.
1. CloudKit ne fournit pas de stockage/persistance local; Il s’agit uniquement d’un support de transfert. Ainsi, lorsqu’une demande est faite pour enregistrer un enregistrement, elle est immédiatement envoyée aux serveurs iCloud.


> [!NOTE]
> En raison de la nature «perte» des communications sur le réseau mobile, où les connexions sont constamment abandonnées ou interrompues, l’un des premiers éléments à prendre en compte lors de l’utilisation de CloudKit est la gestion des erreurs.

### <a name="fetching-a-record"></a>Récupération d’un enregistrement

Avec un enregistrement créé et correctement stocké sur le serveur iCloud, utilisez le code suivant pour récupérer l’enregistrement:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Tout comme lors de l’enregistrement de l’enregistrement, le code ci-dessus est asynchrone, simple et nécessite une bonne gestion des erreurs.

### <a name="updating-a-record"></a>Mise à jour d’un enregistrement

Une fois qu’un enregistrement a été extrait à partir des serveurs iCloud, le code suivant peut être utilisé pour modifier l’enregistrement et enregistrer les modifications dans la base de données:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

La `FetchRecord` méthode `CKRecord` de retourne une valeur si l’appel a réussi. `PublicDatabase` L’application modifie ensuite l’enregistrement et appelle `SaveRecord` à nouveau pour réécrire les modifications dans la base de données.

Cette section décrit le cycle classique qu’une application utilisera lors de l’utilisation de l’API de commodité CloudKit. L’application enregistre les enregistrements dans iCloud, récupère ces enregistrements à partir de iCloud, modifie les enregistrements et enregistre ces modifications dans iCloud.

## <a name="designing-for-scalability"></a>Conception pour l’évolutivité

Jusqu’à présent, cet article a examiné le stockage et la récupération du modèle objet entier d’une application à partir des serveurs iCloud, à chaque fois qu’il sera utilisé. Bien que cette approche fonctionne bien avec une petite quantité de données et une base d’utilisateurs très petite, elle n’est pas adaptée lorsque la quantité d’informations et/ou la base d’utilisateurs augmente.

### <a name="big-data-tiny-device"></a>Big Data, un petit appareil

Plus une application est populaire, plus il y a de données dans la base de données et moins il est possible d’avoir un cache de ces données entières sur l’appareil. Les techniques suivantes peuvent être utilisées pour résoudre ce problème:

- **Conservez les données volumineuses dans le Cloud** : CloudKit a été conçu pour gérer efficacement les données volumineuses.
- Le **client doit uniquement afficher une tranche de ces données** , réduisez le minimum de données nécessaires pour gérer les tâches à un moment donné.
- Les **vues client peuvent changer** . comme chaque utilisateur possède des préférences différentes, la tranche de données affichée peut passer d’un utilisateur à l’autre et la vue individuelle de l’utilisateur d’une tranche donnée peut être différente.
- Le **client utilise des requêtes pour concentrer le point de vue** – les requêtes permettent à l’utilisateur d’afficher un petit sous-ensemble d’un jeu de données plus volumineux qui existe dans le Cloud.


### <a name="queries"></a>Requêtes

Comme indiqué ci-dessus, les requêtes permettent au développeur de sélectionner un petit sous-ensemble du jeu de données plus volumineux qui existe dans le Cloud. Les requêtes sont exposées dans l’infrastructure CloudKit via `CKQuery` la classe.

Une requête combine trois éléments différents: un type d’enregistrement `RecordType`(), un prédicat ( `NSPredicate`) et, éventuellement, un descripteur de `NSSortDescriptors`tri (). CloudKit prend en charge `NSPredicate`la plupart de.

#### <a name="supported-predicates"></a>Prédicats pris en charge

CloudKit prend en charge les types `NSPredicates` suivants de lorsque vous utilisez des requêtes:


1. Enregistrements correspondants où le nom est égal à une valeur stockée dans une variable:
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Permet à la correspondance d’être basée sur une valeur de clé dynamique, de sorte que la clé n’a pas besoin d’être connue au moment de la compilation:
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Enregistrements correspondants où la valeur de l’enregistrement est supérieure à la valeur donnée:
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Les enregistrements correspondants où l’emplacement de l’enregistrement se trouve dans 100 mètres de l’emplacement donné:
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit prend en charge une recherche avec jetons. Cet appel va créer deux jetons, un pour `after` et un autre pour. `session` Elle renverra un enregistrement qui contient ces deux jetons:
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
6. CloudKit prend en charge les prédicats composés `AND` joints à l’aide de l’opérateur.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Création de requêtes

Le code suivant peut être utilisé pour créer un `CKQuery` dans une application Xamarin iOS 8:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

Tout d’abord, elle crée un prédicat pour sélectionner uniquement les enregistrements qui correspondent à un nom donné. Ensuite, il crée une requête qui sélectionne les enregistrements du type d’enregistrement donné qui correspondent au prédicat.

#### <a name="performing-a-query"></a>Exécution d’une requête

Une fois qu’une requête a été créée, utilisez le code suivant pour effectuer la requête et traiter les enregistrements retournés:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

Le code ci-dessus prend la requête créée ci-dessus et l’exécute sur la base de données publique. Dans la mesure où aucune zone d’enregistrement n’est spécifiée, toutes les zones sont recherchées. Si aucune erreur ne s’est produite, `CKRecords` un tableau de sera retourné en correspondance avec les paramètres de la requête.

La façon de réfléchir aux requêtes est qu’il s’agit d’interrogations, et qu’il s’agit d’un excellent découpage des jeux de données volumineux. Toutefois, les requêtes ne sont pas adaptées aux jeux de données volumineux, principalement statiques pour les raisons suivantes:

- Ils sont incorrects pour la durée de vie de la batterie de l’appareil.
- Ils sont incorrects pour le trafic réseau.
- Ils sont incorrects pour l’expérience utilisateur, car les informations qu’ils voient sont limitées par la fréquence d’interrogation de la base de données par l’application. Les utilisateurs attendent aujourd’hui des notifications push en cas de changement.


### <a name="subscriptions"></a>Abonnements

Lorsque vous traitez des jeux de données volumineux, principalement statiques, la requête ne doit pas être exécutée sur l’appareil client, elle doit s’exécuter sur le serveur au nom du client. La requête doit s’exécuter en arrière-plan et doit être exécutée après chaque enregistrement d’un enregistrement, que ce soit par l’appareil en cours ou par un autre appareil touchant la même base de données.

Enfin, une notification push doit être envoyée à chaque appareil attaché à la base de données lors de l’exécution de la requête côté serveur.

Les abonnements sont exposés dans l’infrastructure CloudKit `CKSubscription` via la classe. Ils combinent un type d' `RecordType`enregistrement (), un prédicat `NSPredicate`() et une notification push Apple `Push`().

> [!NOTE]
> Les notifications push CloudKit sont légèrement augmentées car elles contiennent une charge utile contenant des informations spécifiques CloudKit, telles que la cause de l’envoi.

#### <a name="how-subscriptions-work"></a>Fonctionnement des abonnements

Avant d’implémenter C# l’abonnement dans le code, examinons rapidement le fonctionnement des abonnements:

 [![](intro-to-cloudkit-images/image39.png "Vue d’ensemble du fonctionnement des abonnements")](intro-to-cloudkit-images/image39.png#lightbox)

Le graphique ci-dessus montre le processus d’abonnement par défaut comme suit:

1. L’appareil client crée un nouvel abonnement contenant l’ensemble des conditions qui déclenchent l’abonnement et une notification push qui est envoyée lorsque le déclencheur se produit.
2. L’abonnement est envoyé à la base de données où il est ajouté à la collection d’abonnements existants.
3. Un deuxième appareil crée un nouvel enregistrement et enregistre cet enregistrement dans la base de données.
4. La base de données recherche dans sa liste d’abonnements pour voir si le nouvel enregistrement correspond à l’une de leurs conditions.
5. Si une correspondance est trouvée, la notification push est envoyée à l’appareil qui a inscrit l’abonnement avec les informations relatives à l’enregistrement qui l’a déclenché.


Avec ces connaissances en place, examinons la création d’abonnements dans une application Xamarin iOS 8.

#### <a name="creating-subscriptions"></a>Création d’abonnements

Le code suivant peut être utilisé pour créer un abonnement:

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

Tout d’abord, elle crée un prédicat qui fournit la condition pour déclencher l’abonnement. Ensuite, il crée l’abonnement par rapport à un type d’enregistrement spécifique et définit l’option de lors du test du déclencheur. Enfin, il définit le type de notification qui se produit lorsque l’abonnement est déclenché et l’associe à l’abonnement.

### <a name="saving-subscriptions"></a>Enregistrement des abonnements

Une fois l’abonnement créé, le code suivant l’enregistre dans la base de données:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

À l’aide de l’API de commodité, l’appel est asynchrone, simple et fournit une gestion aisée des erreurs.

#### <a name="handling-push-notifications"></a>Gestion des notifications push

Si le développeur a déjà utilisé des notifications push Apple (APS), le processus de traitement des notifications générées par CloudKit doit vous être familier.

Dans, remplacez la `ReceivedRemoteNotification` classe comme suit: `AppDelegate.cs`

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

Le code ci-dessus demande à CloudKit d’analyser le userInfo dans une notification CloudKit. Ensuite, les informations sont extraites de l’alerte. Enfin, le type de notification est testé et la notification est traitée en conséquence.

Cette section explique comment répondre au Big Data, un tout petit problème d’appareil présenté ci-dessus à l’aide de requêtes et d’abonnements. L’application laisse ses données volumineuses dans le Cloud et utilise ces technologies pour fournir des vues dans ce jeu de données.

## <a name="cloudkit-user-accounts"></a>Comptes d’utilisateur CloudKit

Comme indiqué au début de cet article, CloudKit est basé sur l’infrastructure iCloud existante. La section suivante traite en détail de la façon dont les comptes sont exposés à un développeur à l’aide de l’API CloudKit.

### <a name="authentication"></a>Authentication

Lorsque vous traitez des comptes d’utilisateur, la première considération est l’authentification. CloudKit prend en charge l’authentification via l’utilisateur iCloud actuellement connecté sur l’appareil. L’authentification s’effectue en arrière-plan et est gérée par iOS. De cette façon, les développeurs n’ont jamais à se soucier des détails de l’implémentation de l’authentification. Ils testent uniquement si un utilisateur a ouvert une session.

### <a name="user-account-information"></a>Informations sur le compte d’utilisateur

CloudKit fournit les informations utilisateur suivantes au développeur:

- **Identité** : un moyen d’identifier l’utilisateur de manière unique.
- **Métadonnées** : possibilité d’enregistrer et de récupérer des informations sur les utilisateurs.
- **Confidentialité** : toutes les informations sont traitées dans un Manor de confidentialité. Rien n’est exposé, sauf si l’utilisateur l’a convenu.
- **Découverte** : donne aux utilisateurs la possibilité de découvrir leurs amis qui utilisent la même application.


Ensuite, nous examinerons ces rubriques en détail.

#### <a name="identity"></a>Identité

Comme indiqué ci-dessus, CloudKit permet à l’application d’identifier de façon unique un utilisateur donné:

 [![](intro-to-cloudkit-images/image40.png "Identifier de manière unique un utilisateur donné")](intro-to-cloudkit-images/image40.png#lightbox)

Une application cliente est en cours d’exécution sur les appareils de l’utilisateur et sur toutes les bases de données privées de l’utilisateur dans le conteneur CloudKit. L’application cliente va être liée à l’un de ces utilisateurs spécifiques. Cela est basé sur l’utilisateur qui est connecté à iCloud localement sur l’appareil.

Dans la mesure où cette information provient de iCloud, il existe un magasin de stockage riche d’informations utilisateur. Et étant donné que iCloud héberge réellement le conteneur, il peut corréler les utilisateurs. Dans le graphique ci-dessus, l’utilisateur dont `user@icloud.com` le compte icloud est lié au client actuel.

Sur un conteneur par conteneur, un ID d’utilisateur unique généré de manière aléatoire est créé et associé au compte iCloud de l’utilisateur (adresse de messagerie). Cet ID d’utilisateur est renvoyé à l’application et peut être utilisé de la même façon que le développeur.

> [!NOTE]
> Différentes applications s’exécutant sur le même appareil pour le même utilisateur iCloud auront des ID d’utilisateur différents, car ils sont connectés à différents conteneurs CloudKit.

Le code suivant obtient l’ID utilisateur CloudKit pour l’utilisateur iCloud actuellement connecté sur l’appareil:

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

Le code ci-dessus demande au conteneur CloudKit de fournir l’ID de l’utilisateur actuellement connecté. Étant donné que ces informations proviennent du serveur iCloud, l’appel est asynchrone et la gestion des erreurs est requise.

#### <a name="metadata"></a>Métadonnées

Chaque utilisateur dans CloudKit a des métadonnées spécifiques qui le décrivent. Ces métadonnées sont représentées sous la forme d’un enregistrement CloudKit:

 [![](intro-to-cloudkit-images/image41.png "Chaque utilisateur dans CloudKit a des métadonnées spécifiques qui le décrivent")](intro-to-cloudkit-images/image41.png#lightbox)

En regardant dans la base de données privée pour un utilisateur spécifique d’un conteneur, il existe un enregistrement qui définit cet utilisateur. Il existe de nombreux enregistrements d’utilisateur dans la base de données publique, un pour chaque utilisateur du conteneur. L’une d’entre elles aura un ID d’enregistrement qui correspond à l’ID d’enregistrement de l’utilisateur actuellement connecté.

Les enregistrements utilisateur dans la base de données publique sont accessibles en lecture sur le monde. Elles sont traitées, en grande partie, comme un enregistrement ordinaire et ont un type de `CKRecordTypeUserRecord`. Ces enregistrements sont réservés par le système et ne sont pas disponibles pour les requêtes.

Utilisez le code suivant pour accéder à un enregistrement d’utilisateur:

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

Le code ci-dessus demande à la base de données publique de retourner l’enregistrement utilisateur pour l’utilisateur dont nous avons accédé plus haut. Étant donné que ces informations proviennent du serveur iCloud, l’appel est asynchrone et la gestion des erreurs est requise.

#### <a name="privacy"></a>Confidentialité

Par défaut, CloudKit a été conçu pour protéger la confidentialité de l’utilisateur actuellement connecté. Aucune information personnelle sur l’utilisateur n’est exposée par défaut. Dans certains cas, l’application requiert des informations limitées sur l’utilisateur.

Dans ce cas, l’application peut demander à l’utilisateur de divulguer ces informations. Une boîte de dialogue s’affiche pour demander à l’utilisateur de choisir d’exposer ses informations de compte.

#### <a name="discovery"></a>découverte,

En supposant que l’utilisateur a opté pour autoriser l’application à un accès limité à ses informations de compte d’utilisateur, il peut être détectable par d’autres utilisateurs de l’application:

 [![](intro-to-cloudkit-images/image42.png "Un utilisateur peut être détectable par d’autres utilisateurs de l’application")](intro-to-cloudkit-images/image42.png#lightbox)

L’application cliente communique avec un conteneur, et le conteneur parle de iCloud pour accéder aux informations utilisateur. L’utilisateur peut fournir une adresse e-mail et la découverte peut être utilisée pour obtenir des informations sur l’utilisateur. Si vous le souhaitez, l’ID d’utilisateur peut également être utilisé pour découvrir des informations sur l’utilisateur.

CloudKit fournit également un moyen de découvrir des informations sur les utilisateurs qui peuvent être des amis de l’utilisateur iCloud actuellement connecté en interrogeant l’ensemble du carnet d’adresses. Le processus CloudKit extraira le livre de contacts de l’utilisateur et utilisera les adresses de messagerie pour voir s’il peut trouver d’autres utilisateurs de l’application qui correspondent à ces adresses.

Cela permet à l’application de tirer parti de l’annuaire de contacts de l’utilisateur sans y avoir accès ou demander à l’utilisateur d’approuver l’accès aux contacts. À aucun moment les informations de contact mises à la disposition de l’application, seul le processus CloudKit a accès.

Pour résumer, il existe trois types différents d’entrées disponibles pour la découverte d’utilisateurs:

- **ID d’enregistrement utilisateur** : la découverte peut être effectuée par rapport à l’ID utilisateur de l’utilisateur CloudKit actuellement connecté.
- **Adresse e-mail** de l’utilisateur: l’utilisateur peut fournir une adresse e-mail et peut être utilisé pour la découverte.
- **Livre de contacts** : le carnet d’adresses de l’utilisateur peut être utilisé pour découvrir les utilisateurs de l’application qui ont la même adresse e-mail que celle figurant dans leurs contacts.


La découverte des utilisateurs renverra les informations suivantes:

- **ID d’enregistrement utilisateur** : ID unique d’un utilisateur dans la base de données publique.
- **Prénom et nom** : tels qu’ils sont stockés dans la base de données publique.


Ces informations sont renvoyées uniquement pour les utilisateurs qui ont opté pour la découverte.

Le code suivant permet de découvrir des informations sur l’utilisateur actuellement connecté à iCloud sur l’appareil:

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

Utilisez le code suivant pour interroger tous les utilisateurs de l’annuaire de contacts:

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

Dans cette section, nous avons abordé les quatre principaux aspects de l’accès au compte d’un utilisateur que CloudKit peut fournir à une application. De l’obtention de l’identité de l’utilisateur et des métadonnées, aux stratégies de confidentialité intégrées à CloudKit et enfin, à la possibilité de découvrir d’autres utilisateurs de l’application.

## <a name="the-development-and-production-environments"></a>Environnements de développement et de production

CloudKit fournit des environnements de développement et de production distincts pour les types d’enregistrements et les données d’une application. L’environnement de développement est un environnement plus souple qui est disponible uniquement pour les membres d’une équipe de développement. Quand une application ajoute un nouveau champ à un enregistrement et enregistre cet enregistrement dans l’environnement de développement, le serveur met automatiquement à jour les informations de schéma.

Le développeur peut utiliser cette fonctionnalité pour apporter des modifications à un schéma pendant le développement, ce qui permet de gagner du temps. L’inconvénient est qu’une fois qu’un champ a été ajouté à un enregistrement, le type de données associé à ce champ ne peut pas être modifié par programmation. Pour modifier le type d’un champ, le développeur doit supprimer le champ dans le [tableau de bord CloudKit](https://icloud.developer.apple.com/dashboard/) , puis l’ajouter à nouveau avec le nouveau type.

Avant de déployer l’application, le développeur peut migrer son schéma et ses données vers l’environnement de production à l’aide du **tableau de bord CloudKit**. En cas d’exécution sur l’environnement de production, le serveur empêche une application de modifier le schéma par programmation. Le développeur peut toujours apporter des modifications au **tableau de bord CloudKit** , mais les tentatives d’ajout de champs à un enregistrement dans l’environnement de production entraînent des erreurs.

> [!NOTE]
> Le simulateur iOS fonctionne uniquement avec l' **environnement de développement**. Lorsque le développeur est prêt à tester une application dans un **environnement de production**, un appareil iOS physique est requis.


## <a name="shipping-a-cloudkit-enabled-app"></a>Expédition d’une application activée pour CloudKit

Avant d’expédier une application qui utilise CloudKit, elle doit être configurée pour cibler l' **environnement de production CloudKit** ou l’application sera rejetée par Apple.

Effectuez ce qui suit :

1. Dans Visual Studio pour ma, compilez l’application pour l'**appareil iOS**de **mise en sortie** > : 

    [![](intro-to-cloudkit-images/shipping01.png "Compiler l’application pour la mise en version")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. Dans le menu **générer** , sélectionnez **Archive**: 

    [![](intro-to-cloudkit-images/shipping02.png "Sélectionner l’archive")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. L' **Archive** est créée et affichée dans Visual Studio pour Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "L’archive sera créée et affichée")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Démarrez **Xcode**.
5. Dans le menu **fenêtre** , sélectionnez **organisateur**: 

    [![](intro-to-cloudkit-images/shipping04.png "Sélectionner l’organisateur")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Sélectionnez l’archive de l’application, puis cliquez sur le bouton **Exporter...** : 

    [![](intro-to-cloudkit-images/shipping05.png "L’archive de l’application")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. Sélectionnez une méthode pour l’exportation, puis cliquez sur le bouton **suivant** : 

    [![](intro-to-cloudkit-images/shipping06.png "Sélectionner une méthode pour l’exportation")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Sélectionnez l' **équipe de développement** dans la liste déroulante, puis cliquez sur le bouton **choisir** : 

    [![](intro-to-cloudkit-images/shipping07.png "Sélectionner l’équipe de développement dans la liste déroulante")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Sélectionnez **production** dans la liste déroulante, puis cliquez sur le bouton **suivant** : 

    [![](intro-to-cloudkit-images/shipping08.png "Sélectionnez production dans la liste déroulante.")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Vérifiez le paramètre, puis cliquez sur le bouton **Exporter** : 

    [![](intro-to-cloudkit-images/shipping09.png "Vérifier le paramètre")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Choisissez un emplacement pour générer le fichier d' `.ipa` application résultant.

Le processus est similaire pour l’envoi de l’application directement à iTunes Connect, en cliquant simplement sur le bouton **Submit...** à la place de l’exportation... Après avoir sélectionné une archive dans la fenêtre organisateur.

## <a name="when-to-use-cloudkit"></a>Quand utiliser CloudKit

Comme nous l’avons vu dans cet article, CloudKit offre un moyen simple pour une application de stocker et de récupérer des informations à partir des serveurs iCloud. Cela dit, CloudKit n’a pas pour objet d’être obsolète ou déconseillé les outils ou Frameworks existants.

### <a name="use-cases"></a>Cas d’usage

Les cas d’usage suivants devraient aider le développeur à décider quand utiliser une infrastructure ou une technologie iCloud spécifique:

- **stockage clé-valeur icloud** : permet de conserver une petite quantité de données de façon asynchrone et est très utile pour utiliser les préférences de l’application. Toutefois, il est limité pour une très petite quantité d’informations.
- **lecteur icloud** : basé sur les API de documents icloud existantes et fournit une API simple pour synchroniser les données non structurées à partir du système de fichiers. Il fournit un cache hors connexion complet sur Mac OS X et est parfait pour les applications centrées sur les documents.
- **icloud Core Data** – permet la réplication des données entre tous les appareils de l’utilisateur. Les données sont uniques et idéales pour assurer la synchronisation des données privées et structurées.
- **CloudKit** : fournit des données publiques à la fois en structure et en bloc, et est en capacité à gérer à la fois un jeu de données volumineux et des fichiers non structurés volumineux. Son lié au compte iCloud de l’utilisateur et fournit un transfert de données dirigé par le client.


En gardant ces cas d’utilisation à l’esprit, le développeur doit choisir la technologie iCloud appropriée pour fournir les fonctionnalités d’application requises actuelles et offrir une bonne évolutivité en vue d’une croissance future.

## <a name="summary"></a>Récapitulatif

Cet article a abordé une présentation rapide de l’API CloudKit. Il a montré comment approvisionner et configurer une application Xamarin iOS pour utiliser CloudKit. Il a abordé les fonctionnalités de l’API de commodité CloudKit. Il explique comment concevoir une application CloudKit activée pour l’évolutivité à l’aide de requêtes et d’abonnements. Enfin, il a montré les informations du compte d’utilisateur qui sont exposées à une application par CloudKit.

## <a name="related-links"></a>Liens associés

- [CloudKitAtlas (sample)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-cloudkitatlas)
- [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Création d’un profil de provisionnement](~/ios/get-started/installation/device-provisioning/index.md)
