---
title: CloudKit in Xamarin.iOS
description: Ce document décrit comment utiliser des CloudKit dans Xamarin.iOS. Il fournit une vue d’ensemble de CloudKit et explique comment activer, le CloudKit commodité API, l’évolutivité, des comptes d’utilisateur et les environnements de développement et de production.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: 8ef12c8b0822f3d0486f584878f572a266b0d44e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831862"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit in Xamarin.iOS

Le framework de CloudKit simplifie le développement d’applications qu’iCloud de l’accès. Cela inclut la récupération des données d’application et les droits de ressource ainsi que la possibilité de stocker en toute sécurité les informations de l’application. Ce kit permet aux utilisateurs d’une couche d’anonymat en autorisant l’accès aux applications avec leur ID iCloud sans partager les informations personnelles.

Les développeurs peuvent se concentrer sur leurs applications côté client et laisser iCloud élimine la nécessité d’écrire une logique d’application côté serveur. CloudKit fournit l’authentification, les bases de données privés et publics et des données structurées et les services de stockage actif.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour effectuer les étapes présentées dans cet article :

-  **Xcode et le Kit de développement logiciel iOS** – Apple Xcode et iOS 8 API doivent être installés et configurés sur l’ordinateur du développeur.
-  **Visual Studio pour Mac** – la dernière version de Visual Studio pour Mac doit être installée et configurée sur l’appareil de l’utilisateur.
-  **iOS 8 appareil** : un appareil iOS la dernière version d’iOS 8 pour le test en cours d’exécution.

## <a name="what-is-cloudkit"></a>Nouveautés CloudKit

CloudKit consiste à donner l’accès développeur sur le serveurs iCloud. Il fournit la base d’iCloud Drive et bibliothèque de photos iCloud. CloudKit est pris en charge sur les appareils Mac OS X et Apple iOS.

 [![](intro-to-cloudkit-images/image1.png "Comment CloudKit est pris en charge sur Mac OS X et des périphériques Apple iOS")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit utilise l’infrastructure de compte iCloud. En l’absence d’un utilisateur connecté à un compte sur l’appareil iCloud, CloudKit utilisera leur ID pour identifier l’utilisateur. Si aucun compte n’est disponible, puis un accès limité en lecture seule sera fourni.

CloudKit prend en charge les deux le concept de bases de données publiques et privées. Bases de données publiques fournissent un « a » de toutes les données que l’utilisateur a accès à. Bases de données privées sont conçus pour stocker les données privées liées à un utilisateur spécifique.

CloudKit prend en charge les données structurées et en bloc. Il est capable de gérer les transferts de fichiers volumineux en toute transparence. CloudKit s’occupe de transférer efficacement des fichiers volumineux vers et depuis le serveurs iCloud en arrière-plan, le développeur se concentrer sur d’autres tâches.

> [!NOTE]
> Il est important de noter que CloudKit est un _Transport technologie_. Il ne fournit aucun stockage persistant ; Il permet uniquement à une application envoyer et recevoir des informations à partir des serveurs efficacement.

À ce jour, Apple initialement fournit CloudKit gratuitement avec une limite haute sur la capacité de bande passante et de stockage. Pour les applications avec une base utilisateur importante ou projets plus volumineux, Apple a suggéré qu’une mise à l’échelle la tarification abordable est fourni.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>L’activation de CloudKit dans une Application Xamarin

Avant d’une application Xamarin peut utiliser le Framework de CloudKit, l’application doit être configurée correctement comme détaillé dans le [utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) et [utilisation de droits](~/ios/deploy-test/provisioning/entitlements.md) guides

1.  Ouvrez le projet dans Visual Studio pour Mac ou Visual Studio.
2.  Dans le **l’Explorateur de solutions**, ouvrez le **Info.plist** de fichiers et de garantir le **identificateur de Bundle** correspond à celui qui a été défini dans **ID d’application**créé comme partie de l’approvisionnement est configuré :
 
    [![](intro-to-cloudkit-images/image26a.png "Entrez l’identificateur de Bundle")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  Faites défiler jusqu’en bas de la **Info.plist** fichier et sélectionnez **activé les Modes d’arrière-plan**, **mises à jour de l’emplacement** et **des Notifications à distance**:

    [![](intro-to-cloudkit-images/image27a.png "Sélectionnez les Modes d’arrière-plan est activée, les mises à jour de l’emplacement et les Notifications à distance")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  Cliquez sur le projet iOS dans la solution et sélectionnez **Options**.
5.  Sélectionnez **signature du Bundle iOS**, sélectionnez le **Developer Identity** et **profil de provisionnement** créé ci-dessus.
6.  Vérifiez le **Entitlements.plist** inclut **activer iCloud** , **stockage clé-valeur** et **CloudKit** .
7.  Vérifiez le **omniprésence conteneur** existe pour l’application (créé ci-dessus). Exemple : `iCloud.com.your-company.CloudKitAtlas`
8.  Enregistrez les modifications dans le fichier.


Ces paramètres en place, l’application est maintenant prête à accéder aux API de Framework CloudKit.

## <a name="cloudkit-api-overview"></a>Vue d’ensemble des API de CloudKit

Avant d’implémenter CloudKit dans une application Xamarin iOS, cet article va pour couvrir les principes fondamentaux de l’infrastructure de CloudKit, qui inclut les rubriques suivantes :

1.  **Conteneurs** – isolé des silos de communications d’iCloud.
2.  **Bases de données** – publics et privés sont accessibles à l’application.
3.  **Enregistrements** – le mécanisme dans lequel les données structurées sont déplacées vers et à partir de CloudKit.
4.  **Zones de l’enregistrement** – sont des groupes d’enregistrements.
5.  **Enregistre les identificateurs** : sont entièrement normalisées et représentent l’emplacement spécifique de l’enregistrement.
6.  **Référence** : fournir des relations entre les enregistrements connexes dans une base de données parent-enfant.
7.  **Ressources** – autoriser pour le fichier de données volumineuses, non structurées d’être téléchargées sur iCloud et associé à un enregistrement donné.


### <a name="containers"></a>Containers

Une application donnée en cours d’exécution sur un appareil iOS est toujours en cours d’exécution côté ainsi que les autres applications et services sur cet appareil. Sur l’appareil client, l’application va être isolées ou bac à sable d’une certaine façon. Dans certains cas, il s’agit d’un bac à sable littéral, et dans d’autres cas, l’application est simplement en cours d’exécution qu’il contient est propre espace de mémoire.

Le concept de prendre une application cliente et son exécution séparés à partir d’autres clients est très puissant et offre les avantages suivants :

1.  **Sécurité** – une application ne peut pas interférer avec d’autres applications client ou le système d’exploitation lui-même.
1.  **Stabilité** : si l’application cliente bloque à elle ne peut pas prendre des autres applications du système d’exploitation.
1.  **Confidentialité** – chaque application cliente a un accès limité aux données personnelles stockées dans l’appareil.


CloudKit a été conçu pour fournir les mêmes avantages comme indiqué ci-dessus et les appliquer à l’utilisation des informations basées sur le cloud :

 [![](intro-to-cloudkit-images/image31.png "Applications de CloudKit communiquent à l’aide de conteneurs")](intro-to-cloudkit-images/image31.png#lightbox)

Tout comme l’application est en cours d’exécution sur l’appareil, un de plusieurs est donc les communications de l’application avec iCloud un de plusieurs. Chacun de ces silos de communication différents sont appelés des conteneurs.

Les conteneurs sont exposées dans le cadre de CloudKit via la `CKContainer` classe. Par défaut, communique avec une application d’un conteneur et ce conteneur sépare les données pour cette application. Cela signifie que plusieurs applications peuvent stocker des informations sur le même compte iCloud, mais ces informations ne seront jamais être mélangées.

La mise en conteneur de données d’iCloud permet également de CloudKit encapsuler les informations utilisateur. De cette façon, l’application aura un accès limité au compte iCloud et les informations utilisateur sont stockées dans tous les tout en protégeant la confidentialité et la sécurité de l’utilisateur.

Les conteneurs sont entièrement gérées par le développeur de l’application via le portail WWDR. L’espace de noms du conteneur est global sur tous les développeurs d’Apple, le conteneur doit donc pas uniquement être propre aux applications d’un développeur donné, mais à toutes les applications et les développeurs d’Apple.

Apple suggère à l’aide de la notation DNS inverse lors de la création de l’espace de noms pour les conteneurs d’applications. Exemple :

```csharp
iCloud.com.company-name.application-name
```

Tandis que les conteneurs sont, par défaut, lié à une application donnée, ils peuvent être partagés entre applications. Par conséquent, plusieurs applications peuvent coordonner sur un seul conteneur. Une seule application peut également communiquer avec plusieurs conteneurs.

### <a name="databases"></a>Bases de données

Une des principales fonctions de CloudKit consiste à prendre des modèle de données d’une application et la réplication ce modèle jusqu'à les serveurs iCloud. Certaines informations sont destinées à l’utilisateur qui l’a créé, autres informations sont des données publiques qui peuvent être créées par un utilisateur pour une utilisation publique (comme critique d’un restaurant), ou il peut s’agir des informations que le développeur a publié pour l’application. Dans les deux cas, l’audience n’est pas simplement un seul utilisateur, mais est une Communauté de personnes.

 [![](intro-to-cloudkit-images/image32.png "Diagramme du conteneur CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

À l’intérieur d’un conteneur, tout d’abord est la base de données public. Il s’agit où toutes les informations publiques réside et même emplacement mingles. En outre, il existe plusieurs bases de données privées individuelles pour chaque utilisateur de l’application.

Lors de l’exécution sur un appareil iOS, l’application aura uniquement accès aux informations de l’utilisateur actuellement connecté sur iCloud. Vue de l’application du conteneur est donc comme suit :

 [![](intro-to-cloudkit-images/image33.png "La vue des applications du conteneur")](intro-to-cloudkit-images/image33.png#lightbox)

Il peut voir uniquement les bases de données publique et privée associé à l’utilisateur actuellement connecté sur iCloud.

Bases de données sont exposées dans le cadre de CloudKit via la `CKDatabase` classe. Chaque application a accès à deux bases de données : la base de données public et celui qui est privé.

Le conteneur est le point d’entrée initial dans CloudKit. Le code suivant peut être utilisé pour accéder à la base de données publique et privée à partir du conteneur par défaut de l’application :

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

Voici les différences entre les types de base de données :

||Base de données public|Base de données privée|
|---|--- |--- |
|**Type de données**|Données partagées|Données de l’utilisateur actuel|
|**Quota**|Prises en compte pour Quota du développeur|Pris en compte dans le Quota de l’utilisateur|
|**Autorisations par défaut**|World lisible|Utilisateur lisible|
|**Autorisations de modification**|iCloud rôles du tableau de bord via un niveau de la classe d’enregistrement|N/A|

### <a name="records"></a>Enregistrements

Conteneurs renferment des bases de données et à l’intérieur des bases de données sont des enregistrements. Enregistrements constituent le mécanisme dans lequel les données structurées sont déplacées vers et à partir de CloudKit :

 [![](intro-to-cloudkit-images/image34.png "Conteneurs renferment des bases de données et à l’intérieur des bases de données sont des enregistrements")](intro-to-cloudkit-images/image34.png#lightbox)

Les enregistrements sont exposées dans le cadre de CloudKit via la `CKRecord` classe qui encapsule des paires clé-valeur. Une instance d’un objet dans une application est équivalente à une `CKRecord` dans CloudKit. En outre, chaque `CKRecord` possède un type d’enregistrement, ce qui équivaut à la classe d’un objet.

Enregistrements ont un schéma juste-à-temps, donc les données sont décrites de CloudKit avant d’être remis pour le traitement. À partir de là, CloudKit interprétera les informations et gérer la logistique de stockage et la récupération de l’enregistrement.

Le `CKRecord` classe prend également en charge un large éventail de métadonnées. Par exemple, un enregistrement contient des informations sur sa date de création et de l’utilisateur qui l’a créée. Un enregistrement contient également des informations sur quand sa dernière modification et l’utilisateur qui l’a modifié.

Les enregistrements contiennent la notion d’une balise de modification. Il s’agit d’une version précédente d’une révision d’un enregistrement donné. La balise de modification est utilisée comme un moyen léger de déterminer si le client et le serveur ont la même version d’un enregistrement donné.

Comme indiqué ci-dessus, `CKRecords` encapsuler des paires clé-valeur et par conséquent, les types de données suivants peuvent être stockées dans un enregistrement :

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


Outre les types de valeur unique, un enregistrement peut contenir un tableau homogène d’un des types répertoriés ci-dessus.

Le code suivant peut être utilisé pour créer un nouvel enregistrement et le stocker dans une base de données :

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Zones de l’enregistrement

Enregistrements n’existent pas par eux-mêmes au sein d’une base de données : groupes d’enregistrements existent entre eux à l’intérieur d’une Zone d’enregistrement. Zones de l’enregistrement peut être imaginées en tant que Tables dans une base de données relationnelle traditionnel :

 [![](intro-to-cloudkit-images/image35.png "Groupes d’enregistrements existent entre eux à l’intérieur d’une Zone d’enregistrement")](intro-to-cloudkit-images/image35.png#lightbox)

Il peut y avoir plusieurs enregistrements dans une Zone d’enregistrement donnée et plusieurs Zones d’enregistrement au sein d’une base de données. Chaque base de données contient une Zone d’enregistrement par défaut :

 [![](intro-to-cloudkit-images/image36.png "Chaque base de données contient une Zone d’enregistrement par défaut et une Zone personnalisée")](intro-to-cloudkit-images/image36.png#lightbox)

Il s’agit dans lequel les enregistrements sont stockés par défaut. En outre, les Zones d’enregistrement personnalisés peuvent être créés. Représentent les Zones d’enregistrement la granularité de base auxquelles les validations atomiques et le suivi des modifications est terminée.

## <a name="record-identifiers"></a>Identificateurs d’enregistrement

Identificateurs d’enregistrement sont représentées sous forme de tuple, qui contient à la fois un client fourni nom de l’enregistrement et la zone dans laquelle l’enregistrement existe. Identificateurs d’enregistrement présentent les caractéristiques suivantes :

-  Elles sont créées par l’application cliente.
-  Ils sont entièrement normalisées et représentent l’emplacement spécifique de l’enregistrement.
-  En affectant l’ID Unique d’un enregistrement dans une base de données externe pour le nom d’enregistrement, ils permettent de combler des bases de données locales qui ne sont pas stockées au sein de CloudKit.


Lorsque les développeurs créent de nouveaux enregistrements, ils peuvent choisir de passer un identificateur d’enregistrement. Si un identificateur d’enregistrement n’est pas spécifié, un UUID est automatiquement créé et affecté à l’enregistrement.

Lorsque les développeurs créent de nouveaux identificateurs d’enregistrement, il peuvent choisir de spécifier la Zone d’enregistrement auquel appartient chaque enregistrement. Si aucun n’est spécifié, la Zone d’enregistrement par défaut sera utilisée.

Identificateurs d’enregistrement sont exposés dans le cadre de CloudKit via la `CKRecordID` classe. Le code suivant peut être utilisé pour créer un nouvel identificateur d’enregistrement :

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Références

Références fournissent des relations entre les enregistrements connexes dans une base de données :

 [![](intro-to-cloudkit-images/image37.png "Références fournissent des relations entre les enregistrements connexes dans une base de données")](intro-to-cloudkit-images/image37.png#lightbox)

Dans l’exemple ci-dessus, les Parents propriétaire enfants afin que l’enfant est un enregistrement enfant de l’enregistrement parent. La relation va de l’enregistrement enfant à l’enregistrement parent et est appelée un *référence arrière*.

Les références sont exposées dans le cadre de CloudKit via la `CKReference` classe. Ils constituent un moyen de laisser le serveur d’iCloud à comprendre la relation entre les enregistrements.

Références fournissent le mécanisme de suppressions en cascade. Si un enregistrement parent est supprimé de la base de données, tous les enregistrements enfants (comme spécifié dans une relation) sont automatiquement supprimés à partir de la base de données.

> [!NOTE]
> Pointeurs non résolues sont possibles lors de l’utilisation de CloudKit. Par exemple, au moment où l’application a extrait une liste de pointeurs d’enregistrement, un enregistrement sélectionné et ensuite fournir l’enregistrement, l’enregistrement n’existe plus dans la base de données. Une application doit être codée pour gérer cette situation correctement.

Bien que non obligatoire, références arrière sont préférables lorsque vous travaillez avec l’infrastructure de CloudKit. Apple a affiné le système pour en faire le type de référence plus efficace.

Lorsque vous créez une référence, le développeur peut soit fournissent un enregistrement qui est déjà en mémoire, ou créer une référence à un identificateur d’enregistrement. Si à l’aide d’un identificateur d’enregistrement et la référence spécifiée n’existe pas dans la base de données, un pointeur non résolu est créé.

Voici un exemple de création d’une référence par rapport à un enregistrement connu :

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Assets

Autoriser les ressources pour un fichier de données non structurées volumineux d’être téléchargées sur iCloud et associé à un enregistrement donné :

 [![](intro-to-cloudkit-images/image38.png "Autoriser les ressources pour un fichier de données non structurées volumineux d’être téléchargées sur iCloud et associé à un enregistrement donné")](intro-to-cloudkit-images/image38.png#lightbox)

Sur le client, un `CKRecord` est créé qui décrit le fichier qui vont être téléchargées sur le serveur d’iCloud. Un `CKAsset` est créé pour contenir le fichier et est lié à l’enregistrement décrivant.

Lorsque le fichier est téléchargé sur le serveur, l’enregistrement est placé dans la base de données et le fichier est copié dans une base de données en bloc stockage spéciale. Un lien est créé entre le pointeur d’enregistrement et le fichier chargé.

Sont exposées dans le cadre de CloudKit via la `CKAsset` classe et sont utilisés pour stocker les données de grande taille, non structurées. Étant donné que le développeur ne souhaite jamais ont des données non structurées importants dans la mémoire, ressources sont implémentées à l’aide de fichiers sur le disque.

Actifs sont détenus par des enregistrements, ce qui permet les ressources à récupérer à partir d’iCloud à l’aide de l’enregistrement en tant que pointeur. De cette façon, le serveur peut Garbage collecter actifs lors de l’enregistrement qui possède l’élément multimédia est supprimé.

Étant donné que `CKAssets` visent gérer les fichiers de données volumineux, Apple conçu CloudKit efficacement charger et télécharger les ressources.

Le code suivant peut être utilisé pour créer un élément multimédia et l’associer à l’enregistrement :

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Nous avons maintenant couvert tous les objets fondamentaux dans CloudKit. Les conteneurs sont associés aux applications et contiennent des bases de données. Bases de données contiennent des enregistrements qui sont regroupées en Zones d’enregistrement et désignés par des identificateurs d’enregistrement. Relations parent-enfant sont définies entre les enregistrements à l’aide de références. Enfin, les fichiers volumineux peuvent être téléchargés et associés à des enregistrements à l’aide de ressources.

## <a name="cloudkit-convenience-api"></a>API pratique de CloudKit

Apple propose deux ensembles différents d’API pour travailler avec CloudKit :

-  **API Operational** – offre toutes les fonctionnalités de CloudKit. Pour les applications plus complexes, cette API fournit un contrôle affiné sur CloudKit.
-  **API pratique** – offre un sous-ensemble courants, préconfiguré de fonctionnalités de CloudKit. Il fournit une solution d’accès pratique et simple pour y compris les fonctionnalités de CloudKit dans une application iOS.


L’API de la commodité est généralement le meilleur choix pour la plupart des applications iOS et Apple suggère de démarrer avec lui. Le reste de cette section couvre les rubriques suivantes de la commodité API :

-  Sauvegarde d’un enregistrement.
-  Extraction d’un enregistrement.
-  La mise à jour un enregistrement.


### <a name="common-setup-code"></a>Code de programme d’installation commun

Avant la mise en route avec l’API de commodité CloudKit, se trouve du code de programme d’installation standard requis. Commencez par modifier l’application `AppDelegate.cs` de fichier et qu’elle ressemble à ce qui suit :

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

Le code ci-dessus expose les bases de données de CloudKit publics et privés en tant que raccourcis pour les rendre plus facile à utiliser dans le reste de l’application.

Ensuite, ajoutez le code suivant à la vue ou n’importe quel conteneur d’affichage qui utiliseront CloudKit :

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Cette opération ajoute un raccourci pour accéder à la `AppDelegate` et accéder aux raccourcis de base de données publiques et privées créés ci-dessus.

Avec ce code en place, jetons un œil sur l’implémentation de l’API de commodité CloudKit dans une application Xamarin iOS 8.

### <a name="saving-a-record"></a>Sauvegarde d’un enregistrement

À l’aide du modèle présenté ci-dessus lorsque vous présentez des enregistrements, le code suivant sera créer un nouvel enregistrement et utiliser l’API de commodité pour l’enregistrer dans la base de données publique :

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

Trois choses à noter concernant le code ci-dessus :

1.  En appelant le `SaveRecord` méthode de la `PublicDatabase`, le développeur n’a pas spécifier la façon dont les données sont envoyées, quel fuseau, il est en cours d’écriture, etc. L’API de commodité se chargent de tous ces détails lui-même.
1.  L’appel est asynchrone et fournit une routine de rappel lorsque l’appel se termine avec succès ou échec. Si l’appel échoue, un message d’erreur sera fourni.
1.  CloudKit ne fournit pas de stockage local/persistance ; Il est uniquement un support de transfert. Par conséquent, lorsqu’une demande est faite pour enregistrer un enregistrement, il est immédiatement envoyé aux serveurs iCloud.


> [!NOTE]
> En raison de la nature de communications de réseau mobile, où les connexions sont constamment en cours de suppression ou interrompue, une des considérations première le développeur doit faire lors de l’utilisation de CloudKit est la gestion des erreurs « perte ».

### <a name="fetching-a-record"></a>Extraction d’un enregistrement

Avec un enregistrement est créé et stocké avec succès sur le serveur iCloud, utilisez le code suivant pour récupérer l’enregistrement :

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

Tout comme dans l’enregistrement de l’enregistrement, le code ci-dessus est asynchrone, simple et nécessite la gestion des erreurs exceptionnelles.

### <a name="updating-a-record"></a>La mise à jour un enregistrement

Après avoir un enregistrement a été récupéré à partir des serveurs iCloud, le code suivant peut être utilisé pour modifier l’enregistrement et enregistrer les modifications dans la base de données :

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

Le `FetchRecord` méthode de la `PublicDatabase` retourne un `CKRecord` si l’appel a réussi. L’application puis modifie l’enregistrement et les appels `SaveRecord` à nouveau à écrire les modifications dans la base de données.

Cette section vous a montré le cycle classique qui utilise une application lorsque vous travaillez avec l’API de commodité CloudKit. L’application sauvegarder les enregistrements sur iCloud, récupérer les enregistrements à partir d’iCloud, modifier les enregistrements et enregistrer ces modifications dans iCloud.

## <a name="designing-for-scalability"></a>Conception pour l’évolutivité

Jusqu'à présent, cet article a examiné de stocker et récupérer le modèle d’objet complet d’une application à partir des serveurs iCloud, chaque fois qu’il va être manipulées. Bien que cette approche fonctionne bien avec une petite quantité de données et une base d’utilisateurs très petit, il n’évolue pas bien lorsque la quantité d’informations ou pour un utilisateur de base augmente.

### <a name="big-data-tiny-device"></a>Big Data, petit appareil

Devient le plus populaire une application, les données plus dans la base de données et le moins possible est d’avoir un cache de données entières sur l’appareil. Les techniques suivantes peuvent être utilisées pour résoudre ce problème :

-  **Conserver les données volumineuses dans le Cloud** – CloudKit a été conçu pour gérer efficacement les données volumineuses.
-  **Client doit uniquement afficher une tranche de ces données** – arrêter le strict minimum de données nécessaires pour gérer n’importe quelle tâche à un moment donné.
-  **Vues du client peuvent modifier** – étant donné que chaque utilisateur dispose des préférences différentes, la tranche de données affichées permettre modifier l’utilisateur et individuel de l’utilisateur, vue d’une tranche donnée peut être différent.
-  **Client utilise des requêtes pour vous concentrer le point de vue** – requêtes permettent à l’utilisateur afficher un petit sous-ensemble d’un plus grand jeu de données qui existe dans le Cloud.


### <a name="queries"></a>Requêtes

Comme indiqué ci-dessus, les requêtes permettent au développeur de sélectionner un petit sous-ensemble du jeu de données plus volumineux qui existe dans le Cloud. Les requêtes sont exposées dans le cadre de CloudKit via la `CKQuery` classe.

Une requête combine trois éléments différents : un Type d’enregistrement ( `RecordType`), un prédicat ( `NSPredicate`) et, éventuellement, un descripteur de tri ( `NSSortDescriptors`). CloudKit prend en charge la plupart des `NSPredicate`.

#### <a name="supported-predicates"></a>Prédicats pris en charge

CloudKit prend en charge les types suivants de `NSPredicates` lorsque vous travaillez avec des requêtes :


1. Correspondance des enregistrements dans lesquels le nom est égal à une valeur stockée dans une variable :
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Permet la mise en correspondance s’appuie sur une valeur de clé dynamique, afin que la clé n’a besoin d’être savoir au moment de la compilation :
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Correspondance des enregistrements dans lesquels la valeur de l’enregistrement est supérieure à la valeur donnée :
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Correspondance des enregistrements dans lesquels les emplacement de l’enregistrement sont de 100 mètres de l’emplacement donné :
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit prend en charge une recherche sous forme de jeton. Cet appel crée deux jetons, un pour `after` et l’autre pour `session`. Elle renvoie un enregistrement qui contient ces deux jetons :
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
6. Prend en charge de CloudKit composée prédicats joints à l’aide de la `AND` opérateur.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Création de requêtes

Le code suivant peut être utilisé pour créer un `CKQuery` dans une application Xamarin iOS 8 :

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

Tout d’abord, il crée un prédicat pour sélectionner uniquement les enregistrements qui correspondent à un nom donné. Il crée ensuite une requête qui sélectionne les enregistrements du Type d’enregistrement donné qui correspondent au prédicat.

#### <a name="performing-a-query"></a>Exécution d’une requête

Une fois qu’une requête a été créée, utilisez le code suivant pour exécuter la requête et de traiter les enregistrements retournés :

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

Le code ci-dessus prend la requête créée ci-dessus et l’exécute sur la base de données Public. Dans la mesure où aucune Zone d’enregistrement n’est spécifié, toutes les zones sont recherchés. Si aucune erreur ne s’est produite, un tableau de `CKRecords` sera retourné correspondant aux paramètres de la requête.

La façon de réfléchir à des requêtes est qu’ils sont des sondages et sont très utiles pour le découpage des jeux de données volumineux. Requêtes, toutefois, ne sont pas parfaitement aux jeux de données volumineux, généralement statique pour les raisons suivantes :

-  Elles sont incorrectes pour l’autonomie de l’appareil.
-  Elles sont incorrectes pour le trafic réseau.
-  Elles sont incorrectes pour l’expérience utilisateur, car les informations qu'ils voient sont limitées par la fréquence à laquelle l’application est d’interrogation à la base de données. Les utilisateurs attendent aujourd'hui des notifications push en cas de modification.


### <a name="subscriptions"></a>Abonnements

Lors du traitement de jeux de données volumineux, généralement statique, la requête ne doit pas être effectuée sur l’appareil client, il doit s’exécuter sur le serveur sur le client. La requête doit s’exécuter en arrière-plan et doit être exécutée après chaque enregistrement est enregistré, que ce soit par l’appareil en cours ou un autre appareil, appuyez sur la même base de données.

Enfin, une notification push doit être envoyée à tous les périphériques attachés à la base de données lors de l’exécution de la requête côté serveur.

Les abonnements sont exposées dans le cadre de CloudKit via la `CKSubscription` classe. Elles combinent un Type d’enregistrement ( `RecordType`), un prédicat ( `NSPredicate`) et une Notification Push d’Apple ( `Push`).

> [!NOTE]
> Notifications Push CloudKit est légèrement augmenté car ils contiennent une charge utile contenant des informations spécifiques de CloudKit tels que ce qui a provoqué le push de se produire.

#### <a name="how-subscriptions-work"></a>Fonctionnement des abonnements

Avant d’implémenter l’abonnement dans C# de code, nous allons prendre une rapide vue d’ensemble du fonctionnement des abonnements :

 [![](intro-to-cloudkit-images/image39.png "Une vue d’ensemble du fonctionnement des abonnements")](intro-to-cloudkit-images/image39.png#lightbox)

Le graphique ci-dessus illustre le processus d’abonnement classiques comme suit :

1.  L’appareil client crée un nouvel abonnement contenant l’ensemble de conditions qui déclencheront l’abonnement et une Notification Push qui sera envoyée lors de l’exécution du déclencheur.
2.  L’abonnement est envoyé à la base de données dans lequel elle est ajoutée à la collection des abonnements existants.
3.  Un deuxième appareil crée un nouvel enregistrement et enregistre cet enregistrement dans la base de données.
4.  La base de données effectue une recherche dans sa liste d’abonnements pour voir si le nouvel enregistrement correspond à une de leurs conditions.
5.  Si une correspondance est trouvée, la Notification Push est envoyée à l’appareil qui inscrit l’abonnement avec des informations sur l’enregistrement qui a entraîné son déclenchement.


Sachant cela en place, penchons-nous sur la création d’abonnements dans une Xamarin iOS 8 application.

#### <a name="creating-subscriptions"></a>Création d’abonnements

Le code suivant peut être utilisé pour créer un abonnement :

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

Tout d’abord, il crée un prédicat qui fournit la condition pour déclencher l’abonnement. Ensuite, il crée l’abonnement par rapport à un Type d’enregistrement spécifique et définit l’option de lorsque le déclencheur est testé. Enfin, il définit le type de notification qui se produit lorsque l’abonnement est déclenchée et l’attache l’abonnement.

### <a name="saving-subscriptions"></a>L’enregistrement des abonnements

Avec l’abonnement créé, le code suivant l’enregistrerez sur la base de données :

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

À l’aide de l’API de commodité, l’appel est asynchrone, simple et fournit la gestion des erreurs facile.

#### <a name="handling-push-notifications"></a>Gestion des Notifications de Push

Si le développeur a déjà utilisé Apple Push Notifications (APS), le processus de gestion des Notifications générées par CloudKit doit connaître.

Dans le `AppDelegate.cs`, remplacer le `ReceivedRemoteNotification` classe comme suit :

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

Le code ci-dessus demande CloudKit pour analyser l’userInfo dans une Notification de CloudKit. Ensuite, les informations sont extraites sur l’alerte. Enfin, le type de notification est testé et la notification est gérée en conséquence.

Cette section a montré comment répondre le Big Data, le problème petit appareil présentées ci-dessus à l’aide de requêtes et des abonnements. L’application laisser ses données de grande taille dans le cloud et utiliser ces technologies pour proposer des vues dans ce jeu de données.

## <a name="cloudkit-user-accounts"></a>Comptes d’utilisateur de CloudKit

Comme indiqué au début de cet article, CloudKit repose sur l’infrastructure existante d’iCloud. La section suivante couvre, de façon détaillée comment les comptes sont exposés à un développeur à l’aide de l’API de CloudKit.

### <a name="authentication"></a>Authentication

Lorsque vous traitez des comptes d’utilisateur, le premier point important est l’authentification. CloudKit prend en charge l’authentification par le biais de l’utilisateur actuellement connecté dans iCloud sur l’appareil. L’authentification a lieu en arrière-plan et est gérée par iOS. De cette façon, les développeurs ont jamais à vous soucier des détails d’implémentation de l’authentification. Ils testent uniquement pour voir si un utilisateur est connecté.

### <a name="user-account-information"></a>Informations de compte utilisateur

CloudKit fournit les informations utilisateur suivantes aux développeurs :

-  **Identité** – un moyen d’identifier l’utilisateur.
-  **Métadonnées** – la capacité d’enregistrer et récupérer des informations sur les utilisateurs.
-  **Confidentialité** – toutes les informations sont gérées dans un manor consciente de confidentialité. Rien n’est exposé, sauf si l’utilisateur a consenti à ce dernier.
-  **Découverte** – offre aux utilisateurs la possibilité de découvrir ses amis qui sont à l’aide de la même application.


Ensuite, nous allons examiner ces rubriques en détail.

#### <a name="identity"></a>Identité

Comme indiqué ci-dessus, CloudKit offre un moyen de l’application identifier de façon unique un utilisateur donné :

 [![](intro-to-cloudkit-images/image40.png "Identifie de façon unique identifier un utilisateur donné")](intro-to-cloudkit-images/image40.png#lightbox)

Il existe une application cliente en cours d’exécution sur des appareils d’un utilisateur et toutes les bases de données utilisateur privée spécifique au sein du conteneur de CloudKit. L’application cliente va être lié à un de ces utilisateurs spécifiques. Cela est basé sur l’utilisateur qui est connecté à iCloud localement sur l’appareil.

Cela provient d’iCloud, étant donné une riche de banque d’informations utilisateur de sauvegarde. Et étant donné qu’iCloud héberge réellement le conteneur, il peut faire correspondre des utilisateurs. Dans le graphique ci-dessus, l’utilisateur dont le compte iCloud `user@icloud.com` est lié au client actuel.

Chaque conteneur par conteneur, un ID d’utilisateur unique, généré de façon aléatoire, est créé et associé au compte iCloud de l’utilisateur (adresse e-mail). Cet ID utilisateur est renvoyé à l’application et peut être utilisé en aucune façon que le développeur gré.

> [!NOTE]
> Plusieurs applications s’exécutant sur le même appareil pour le même utilisateur iCloud aura différents ID d’utilisateur, car ils sont connectés à différents conteneurs de CloudKit.

Le suivant code obtient l’ID d’utilisateur de CloudKit pour actuellement connecté dans utilisateur iCloud sur l’appareil :

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

Le code ci-dessus est demandant le conteneur de CloudKit pour fournir l’ID de l’utilisateur actuellement connecté. Étant donné que ces informations proviennent d’iCloud serveur, l’appel est asynchrone et la gestion des erreurs est nécessaire.

#### <a name="metadata"></a>Métadonnées

Chaque utilisateur de CloudKit a des métadonnées spécifiques qui les décrit. Ces métadonnées sont représentée comme un enregistrement de CloudKit :

 [![](intro-to-cloudkit-images/image41.png "Chaque utilisateur de CloudKit a des métadonnées spécifiques qui décrivent les")](intro-to-cloudkit-images/image41.png#lightbox)

À l’intérieur de la base de données privée pour un utilisateur spécifique d’un conteneur, il est un enregistrement qui définit cet utilisateur. Il existe de nombreux enregistrements d’utilisateur à l’intérieur de la base de données publique, une pour chaque utilisateur du conteneur. Un d’eux a un ID d’enregistrement qui correspond à l’ID d’enregistrement de l’utilisateur actuellement connecté

Enregistrements d’utilisateur dans la base de données publique sont world lisible. Ils sont traités, la plupart du temps, qu’un enregistrement ordinaire et sont de type `CKRecordTypeUserRecord`. Ces enregistrements sont réservés par le système et ne sont pas disponibles pour les requêtes.

Utilisez le code suivant pour accéder à un enregistrement d’utilisateur :

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

Le code ci-dessus est demandé de la base de données publics pour retourner l’enregistrement d’utilisateur pour l’utilisateur qui est l’ID que nous avons accédé ci-dessus. Étant donné que ces informations proviennent d’iCloud serveur, l’appel est asynchrone et la gestion des erreurs est nécessaire.

#### <a name="privacy"></a>Confidentialité

CloudKit a été de conception, par défaut, pour protéger la confidentialité de l’utilisateur actuellement connecté. Aucune information d’identification personnelle sur l’utilisateur n’est exposée par défaut. Il existe certains cas où l’application nécessite des informations limitées sur l’utilisateur.

Dans ce cas, l’application peut demander que l’utilisateur divulguer ces informations. Une boîte de dialogue s’affiche à l’utilisateur demandant à participer à l’exposition de leurs informations de compte.

#### <a name="discovery"></a>découverte,

En supposant que l’utilisateur en tant que souscrites dans permettant à l’application un accès limité à leurs informations de compte d’utilisateur, ils peuvent être détectables à d’autres utilisateurs de l’application :

 [![](intro-to-cloudkit-images/image42.png "Un utilisateur peut être détectable à d’autres utilisateurs de l’application")](intro-to-cloudkit-images/image42.png#lightbox)

L’application cliente communique avec un conteneur, et le conteneur communique avec iCloud pour accéder aux informations d’utilisateur. L’utilisateur peut fournir une adresse de messagerie et de découverte peut être utilisée pour obtenir des informations relatives à l’utilisateur précédent. Si vous le souhaitez, l’ID d’utilisateur peut également être utilisé pour découvrir des informations sur l’utilisateur.

CloudKit offre également un moyen de découvrir des informations sur tous les utilisateurs qui peuvent être des amis d’actuellement connecté sur utilisateur iCloud en interrogeant le carnet d’adresses entière. Le processus de CloudKit extraire dans le livre de Contact de l’utilisateur et utiliser les adresses de messagerie pour voir s’il peut trouver d’un autre utilisateur de l’application qui correspondent à ces adresses.

Cela permet à l’application tirer parti du livre de Contact de l’utilisateur sans fournir l’accès à celui-ci ou demandant à l’utilisateur à approuver l’accès aux contacts. À aucun moment sont les informations de contact accessible à l’application, seul le processus de CloudKit a accès.

Pour résumer, il existe trois types différents d’entrées disponibles pour la découverte d’utilisateur :

-  **ID d’enregistrement utilisateur** – découverte peut être effectuée dans l’ID d’utilisateur d’actuellement connecté dans CloudKit utilisateur.
-  **Adresse de messagerie utilisateur** : l’utilisateur peut fournir une adresse de messagerie et il peut être utilisé pour la découverte.
-  **Contactez le livre** – carnet d’adresses de l’utilisateur peut être utilisé pour découvrir des utilisateurs de l’application qui ont la même adresse e-mail comme indiqué dans leurs contacts.


Découverte d’utilisateurs retournera les informations suivantes :

-  **ID d’enregistrement utilisateur** -l’ID unique d’un utilisateur dans la base de données Public.
-  **Premier et dernier nom** - tel que stocké dans la base de données Public.


Ces informations seront affichera uniquement pour les utilisateurs qui ont choisi dans la découverte.

Le code suivant permet de découvrir les informations relatives à l’utilisateur actuellement connecté à iCloud sur l’appareil :

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

Utilisez le code suivant pour interroger tous les utilisateurs dans le livre de Contact :

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

Dans cette section, nous avons abordé les quatre principaux domaines de l’accès à un compte d’utilisateur qui CloudKit peut fournir à une application. À partir de l’obtention des identités et les métadonnées de l’utilisateur, les stratégies de confidentialité intégrées CloudKit et enfin, la possibilité de découvrir d’autres utilisateurs de l’application.

## <a name="the-development-and-production-environments"></a>Environnements de développement et Production

CloudKit fournit des environnements de développement et de Production distincts pour les types d’enregistrements et les données d’une application. L’environnement de développement est un environnement plus flexible qui est disponible uniquement pour les membres d’une équipe de développement. Lorsqu’une application ajoute un nouveau champ à un enregistrement et enregistre cet enregistrement dans l’environnement de développement, le serveur met automatiquement à jour les informations de schéma.

Le développeur peut utiliser cette fonctionnalité pour apporter des modifications à un schéma pendant le développement, ce qui fait gagner du temps. L’inconvénient est qu’après qu’un champ a été ajouté à un enregistrement, le type de données associé à ce champ ne peut pas être modifié par programme. Pour modifier un type de champ, le développeur doit supprimer le champ dans [CloudKit le tableau de bord](https://icloud.developer.apple.com/dashboard/) et ajoutez-le à nouveau avec le nouveau type.

Avant de déployer l’application, le développeur peut migrer leur schéma et les données dans l’environnement de production avec **tableau de bord de CloudKit**. Lors de l’exécution par rapport à l’environnement de Production, le serveur empêche une application à partir de la modification du schéma par programme. Le développeur peut toujours apporter des modifications avec **CloudKit le tableau de bord** mais tente d’ajouter des champs à un enregistrement dans le résultat de l’environnement de Production des erreurs.

> [!NOTE]
> IOS Simulator fonctionne uniquement avec les **environnement de développement**. Lorsque le développeur est prêt à tester une application dans un **environnement de Production**, un appareil iOS physique est requis.


## <a name="shipping-a-cloudkit-enabled-app"></a>Application activée pour un CloudKit de livraison

Avant d’expédier une application qui utilise CloudKit, il devra être configuré pour cibler le **environnement de Production CloudKit** ou l’application va être rejetée par Apple.

Effectuez ce qui suit :

1. Dans Visual Studio pour Ma, compilez l’application pour **version** > **appareil iOS**: 

    [![](intro-to-cloudkit-images/shipping01.png "Compilez l’application pour la mise en production")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. À partir de la **Build** menu, sélectionnez **Archive**: 

    [![](intro-to-cloudkit-images/shipping02.png "Sélectionnez l’Archive")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. Le **Archive** sera créé et affiché dans Visual Studio pour Mac : 

    [![](intro-to-cloudkit-images/shipping03.png "L’Archive est créée et affichée")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Démarrez **Xcode**.
5. À partir de la **fenêtre** menu, sélectionnez **organisateur**: 

    [![](intro-to-cloudkit-images/shipping04.png "Sélectionnez l’organisateur")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Sélectionnez l’archive de l’application et cliquez sur le **exporter...**  bouton : 

    [![](intro-to-cloudkit-images/shipping05.png "Archive de l’application")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. Sélectionnez une méthode pour l’exportation, cliquez sur le **suivant** bouton : 

    [![](intro-to-cloudkit-images/shipping06.png "Sélectionner une méthode pour l’exportation")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Sélectionnez le **équipe de développement** dans la liste déroulante et cliquez sur le **choisir** bouton : 

    [![](intro-to-cloudkit-images/shipping07.png "Sélectionnez l’équipe de développement dans la liste déroulante")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Sélectionnez **Production** dans la liste déroulante et cliquez sur le **suivant** bouton : 

    [![](intro-to-cloudkit-images/shipping08.png "Sélectionnez Production dans la liste déroulante")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Passez en revue le paramètre et cliquez sur le **exporter** bouton : 

    [![](intro-to-cloudkit-images/shipping09.png "Vérifiez le paramètre")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Choisissez un emplacement pour générer l’application résultante `.ipa` fichier.

Le processus est similaire pour l’envoi de l’application directement sur iTunes Connect, cliquez simplement sur le **envoyer...**  bouton au lieu de l’exportation... après la sélection d’une Archive dans la fenêtre de la bibliothèque multimédia.

## <a name="when-to-use-cloudkit"></a>Quand utiliser CloudKit

Comme nous l’avons vu dans cet article, CloudKit fournit un moyen simple pour une application stocker et récupérer des informations à partir des serveurs iCloud. Cela étant dit, CloudKit ne pas obsolètes ou déprécier les outils existants ou des infrastructures.

### <a name="use-cases"></a>Cas d’usage

Les cas d’usage suivants doivent permettre au développeur de déterminer quand utiliser un framework d’iCloud spécifique ou une technologie :

-  **iCloud clé-valeur Store** – de façon asynchrone maintient une petite quantité de données à jour et est très utile pour l’utilisation des préférences de l’application. Toutefois, il est contraint pour une très petite quantité d’informations.
-  **iCloud Drive** : reposant sur iCloud existant Documents API et fournit une API simple pour synchroniser des données non structurées à partir du système de fichiers. Il fournit un cache hors connexion complet sur Mac OS X et est idéal pour des applications orientées document.
-  **iCloud Core Data** – permet aux données à répliquer entre tous les périphériques de l’utilisateur. Les données sont utilisateur unique et très utile pour keeping private données structurées synchronisées.
-  **CloudKit** : fournit des données publiques à la fois structure et en bloc et est capable de gérer le jeu de données volumineux et des fichiers volumineux non structurées. Son liée à l’utilisateur du compte iCloud et fournit client dirigé le transfert de données.


Conservation de ces cas d’usage à l’esprit, le développeur doit choisir la technologie iCloud correct pour fournir à la fois la fonctionnalité actuelle de l’application requise et de fournir une bonne évolutivité pour une croissance future.

## <a name="summary"></a>Récapitulatif

Cet article a présenté une introduction rapide à l’API de CloudKit. Il a montré comment approvisionner et configurer une application Xamarin iOS pour utiliser CloudKit. Il a couvert les fonctionnalités de l’API de commodité CloudKit. Il a montré comment concevoir un CloudKit activé l’application pour l’évolutivité à l’aide de requêtes et des abonnements. Et enfin, il a indiqué les informations de compte d’utilisateur qui sont exposées à une application en CloudKit.

## <a name="related-links"></a>Liens associés

- [CloudKitAtlas (sample)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Création d’un profil de provisionnement](~/ios/get-started/installation/device-provisioning/index.md)
