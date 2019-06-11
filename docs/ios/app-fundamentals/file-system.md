---
title: Accès au système de fichiers dans Xamarin.iOS
description: Ce document décrit comment travailler avec le système de fichiers dans Xamarin.iOS. Il aborde les répertoires, la lecture de fichiers, de sérialisation XML et JSON, le bac à sable application, partage de fichiers via iTunes et bien plus encore.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/12/2018
ms.openlocfilehash: 10f4f53e71a47521076538bf9eb12b86c1e478a6
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827518"
---
# <a name="file-system-access-in-xamarinios"></a>Accès au système de fichiers dans Xamarin.iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/monotouch/FileSystemSampleCode/)

Vous pouvez utiliser Xamarin.iOS et `System.IO` classes dans le *bibliothèque de classes de Base (BCL) .NET* pour accéder au système de fichier iOS. La classe `File` permet de créer, supprimer et lire des fichiers, et la classe `Directory` permet de créer, supprimer ou énumérer le contenu des répertoires. Vous pouvez également utiliser `Stream` sous-classes, ce qui peuvent fournir un degré plus élevé de contrôle sur les opérations de fichier (par exemple, la compression ou la position de recherche au sein d’un fichier).

iOS impose certaines restrictions sur une application peut faire avec le système de fichiers pour préserver la sécurité des données d’une application et de protéger les utilisateurs à partir d’applications MALIGNES. Ces restrictions font partie de la *Application Sandbox* – un ensemble de règles qui limite l’accès d’une application pour les fichiers, les préférences, les ressources réseau, matériel, etc. Une application est limitée à la lecture et écriture de fichiers dans son répertoire de base (emplacement d’installation) ; Il ne peut pas accéder aux fichiers de l’application à un autre.

iOS a également certaines fonctionnalités spécifiques du système de fichiers : certains répertoires nécessitent un traitement spécial en ce qui concerne les mises à niveau et les sauvegardes et les applications peuvent également partager des fichiers entre eux et le **fichiers** application (depuis iOS 11) et par le biais de iTunes.

Cet article aborde les fonctionnalités et les restrictions d’e/s du système de fichiers et inclut un exemple d’application qui illustre l’utilisation de Xamarin.iOS pour exécuter certaines opérations de système de fichiers simple :

[![Un exemple d’e/s l’exécution de certaines opérations de système de fichiers simple](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>Accès aux fichiers général

Xamarin.iOS vous permet d’utiliser le .NET `System.IO` classes pour les opérations de système de fichiers sur iOS.

Les extraits de code suivants illustrent certaines opérations de fichier courantes. Vous trouverez les toutes sous dans le **SampleCode.cs** fichier, dans l’exemple d’application pour cet article.

### <a name="working-with-directories"></a>Utilisation de répertoires

Ce code énumère les sous-répertoires dans le répertoire actif (spécifié par le «. / « paramètre), qui est l’emplacement de votre fichier exécutable d’application.
Votre sortie sera une liste de tous les fichiers et dossiers qui sont déployés avec votre application (affichée dans la fenêtre de console pendant le débogage).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>Lecture des fichiers

Pour lire un fichier texte, vous devez uniquement une seule ligne de code. Cet exemple affiche le contenu d’un fichier texte dans la fenêtre de sortie de l’Application.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>sérialisation XML

Bien que l’utilisation de l’ensemble `System.Xml` espace de noms est abordée dans cet article, vous pouvez facilement désérialiser un document XML à partir du système de fichiers en utilisant un StreamReader comme cet extrait de code :

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Pour plus d’informations, consultez la documentation de [System.Xml](xref:System.Xml) et [sérialisation](xref:System.Xml.Serialization). Consultez le [documentation Xamarin.iOS](~/ios/deploy-test/linker.md) sur l’éditeur de liens – souvent vous devrez ajouter la `[Preserve]` attribut aux classes que vous prévoyez de sérialiser.

### <a name="creating-files-and-directories"></a>Création de fichiers et répertoires

Cet exemple montre comment utiliser le `Environment` classe pour accéder au dossier de Documents où nous pouvons créer des fichiers et répertoires.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

Création d’un répertoire est un processus similaire :

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Pour plus d’informations, consultez le [référence de l’API de System.IO](xref:System.IO).

### <a name="serializing-json"></a>Sérialisation JSON

[Json.NET](http://www.newtonsoft.com/json) est une infrastructure JSON hautes performances qui fonctionne avec Xamarin.iOS et est disponible sur NuGet. Ajoutez le package NuGet à votre application de projet, à l’aide de **NuGet ajouter** dans Visual Studio pour Mac :

[![](file-system-images/json01.png "Ajout du package NuGet au projet d’applications")](file-system-images/json01.png#lightbox)

Ensuite, ajoutez une classe en tant que le modèle de données pour la sérialisation/désérialisation (dans ce cas `Account.cs`) :

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }

        public Account() {
        }
    }
}
```

Enfin, créez une instance de la `Account` classe, sa sérialisation en données json et l’écrire dans un fichier :

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```

Pour plus d’informations sur l’utilisation des données json dans une application .NET, consultez Json .NET [documentation](http://www.newtonsoft.com/json/help).

## <a name="special-considerations"></a>Considérations spéciales

En dépit des similarités entre les opérations de fichier Xamarin.iOS et .NET, iOS et Xamarin.iOS diffèrent des .NET certaines différences importantes.

### <a name="making-project-files-accessible-at-runtime"></a>Rendre les fichiers de projet accessibles lors de l’exécution

Par défaut, si vous ajoutez un fichier à votre projet, il ne seront pas incluse dans l’assembly final et donc pas disponible pour votre application. Pour inclure un fichier dans l’assembly, vous devez la marquer avec une action de génération spécial, appelée contenu.

Pour marquer un fichier pour l’inclusion, avec le bouton droit sur l’ou les fichiers et choisissez **Action de génération &gt; contenu** dans Visual Studio pour Mac. Vous pouvez également modifier le **Action de génération** dans le fichier **propriétés** feuille.

### <a name="case-sensitivity"></a>Respect de la casse

Il est important de comprendre que le système de fichiers iOS est *respect de la casse*. Respect de la casse signifie que vos noms de répertoires et de fichiers doivent correspondre exactement : **README.txt** et **readme.txt** est considéré comme noms de fichier différents.

Cela peut entraîner une confusion pour les développeurs .NET qui sont plus familiarisés avec le système de fichiers Windows, qui est *respectent pas la casse* – **fichiers**, **fichiers**, et  **fichiers** faites référence dans le même répertoire.

> [!WARNING]
> IOS Simulator n’est pas la casse.
> Si votre nom de fichier casse diffère entre le fichier proprement dit et les références à celui-ci dans le code, votre code peut toujours fonctionner dans le simulateur, mais il échouera sur un appareil réel. Il s’agit d’une des raisons pourquoi il est important de déployer et tester sur un appareil réel au plus tôt et souvent au cours du développement d’iOS.

### <a name="path-separator"></a>Séparateur de chemin d’accès

iOS utilise la barre oblique « / » comme séparateur de chemin d’accès (qui est différent de Windows, qui utilise la barre oblique inverse « \ »).

En raison de cette différence à confusion, il est recommandé d’utiliser le `System.IO.Path.Combine` (méthode), qui ajuste pour la plateforme actuelle, plutôt que de coder en dur un séparateur de chemin d’accès particulier. Il s’agit d’une étape simple qui rend votre code plus portable sur d’autres plateformes.

## <a name="application-sandbox"></a>Bac à sable d’application

Accès de votre application pour le système de fichiers (et autres ressources telles que les fonctionnalités réseau et du matériel) est limitée pour des raisons de sécurité. Cette restriction est appelée le *Application Sandbox*. En termes de système de fichiers, votre application est limitée à la création et la suppression de fichiers et répertoires dans son répertoire de base.

Le répertoire de base est un emplacement unique dans le système de fichiers où votre application et toutes ses données sont stockés. Vous ne pouvez pas choisir (ou modifier) l’emplacement du répertoire de base pour votre application ; Toutefois iOS et Xamarin.iOS fournissent des propriétés et méthodes pour gérer les fichiers et répertoires à l’intérieur.

## <a name="the-application-bundle"></a>Le bundle d’applications

Le *offre groupée d’applications* est le dossier qui contient votre application.
Il se distingue d’autres dossiers en ayant le suffixe de fichier .app ajouté au nom du répertoire. Votre offre groupée d’applications contient votre fichier exécutable et tout le contenu (fichiers, images, etc.) nécessaire pour votre projet.

Lorsque vous accédez à votre offre groupée d’application dans le système d’exploitation Mac, il apparaît avec une autre icône que vous consultez dans d’autres répertoires (et le **.app** suffixe est masqué) ; Toutefois, il est simplement un répertoire régulière qui affiche le système d’exploitation différemment.

Pour afficher le bundle d’applications pour l’exemple de code, cliquez sur le projet dans **Visual Studio pour Mac** et sélectionnez **afficher dans le Finder**. Puis accédez à la **bin /** directory où vous devriez trouver une icône d’application (semblable à la capture d’écran ci-dessous).

![Naviguer dans le répertoire bin pour trouver une icône d’application similaire à cette capture d’écran](file-system-images/40-bundle.png)

Avec le bouton droit sur cette icône, puis choisissez **afficher le contenu du Package** pour parcourir le contenu du répertoire du Bundle d’applications. Le contenu s’affiche comme le contenu d’un répertoire régulière, comme illustré ici :

[![Le contenu de l’offre groupée d’applications](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

Le bundle d’applications est ce qui est installé sur le simulateur ou sur votre appareil pendant le test, et au final, c’est ce qui est envoyé à Apple pour être inclus dans l’App Store.

## <a name="application-directories"></a>Annuaires d’application

Lorsque votre application est installée sur un appareil, le système d’exploitation crée un répertoire de base pour votre application et crée un certain nombre de répertoires dans le répertoire racine de l’application qui sont disponibles pour utilisation. Depuis iOS 8, les répertoires accessibles par l’utilisateur sont [introuvable](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) au sein de la racine de l’application, par conséquent, vous ne pouvez pas dériver les chemins d’accès pour le regroupement de l’application à partir de répertoires de l’utilisateur, ou vice versa.

Ces répertoires, comment déterminer leur chemin d’accès et leurs objectifs sont répertoriés ci-dessous :

&nbsp;

|Répertoire|Description|
|---|---|
|[ApplicationName].app/|**Dans iOS 7 et versions antérieures**, il s’agit du `ApplicationBundle` répertoire dans lequel se trouve votre fichier exécutable d’application. La structure de répertoires que vous créez dans votre application existe dans ce répertoire (par exemple, les images et les autres types de fichiers que vous avez marquées en tant que ressources dans votre Visual Studio pour le projet Mac).<br /><br />Si vous avez besoin accéder aux fichiers de contenu à l’intérieur de votre offre groupée d’applications, le chemin d’accès à ce répertoire est disponible via le `NSBundle.MainBundle.BundlePath` propriété.|
|Documents/|Utilisez ce répertoire pour stocker des documents d’utilisateur et les fichiers de données d’application.<br /><br />Le contenu de ce répertoire peut être mis à disposition l’utilisateur via (bien que cela est désactivée par défaut) de partage de fichiers iTunes. Ajouter un `UIFileSharingEnabled` clé booléenne dans le fichier Info.plist pour permettre aux utilisateurs d’accéder à ces fichiers.<br /><br />Même si une application ne permet pas immédiatement le partage de fichiers, vous devez éviter de placer des fichiers qui doivent être masqués à partir de vos utilisateurs dans ce répertoire (tels que les fichiers de base de données, sauf si vous avez l’intention de les partager). Tant que les fichiers sensibles restent masqués, ces fichiers ne seront pas exposées (et potentiellement déplacés, modifiés ou supprimés par iTunes) si le partage de fichiers est activé dans une future version.<br /><br /> Vous pouvez utiliser la `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` méthode pour obtenir le chemin d’accès au répertoire Documents de votre application.<br /><br />Le contenu de ce répertoire est sauvegardé par iTunes.|
|Bibliothèque /|Le répertoire de la bibliothèque est un bon emplacement pour stocker les fichiers qui ne sont pas créés directement par l’utilisateur, telles que les bases de données ou d’autres fichiers générés par l’application. Le contenu de ce répertoire n’est jamais exposé à l’utilisateur via iTunes.<br /><br />Vous pouvez créer vos propres sous-répertoires dans la bibliothèque ; Toutefois, il existe déjà certains créé par le système répertoires ici que vous devez connaître, y compris les préférences et les Caches.<br /><br />Le contenu de ce répertoire (à l’exception du sous-répertoire Caches) est sauvegardé par iTunes. Les répertoires personnalisés que vous créez dans la bibliothèque seront sauvegardés.|
|Bibliothèque/Préférences /|Fichiers de préférences spécifiques à l’application sont stockées dans ce répertoire. Ne créez pas directement ces fichiers. Au lieu de cela, utilisez la `NSUserDefaults` classe.<br /><br />Le contenu de ce répertoire est sauvegardé par iTunes.|
|Bibliothèque/Caches /|Le répertoire de Caches est un bon emplacement pour stocker les fichiers de données qui peuvent aider votre application s’exécuter, mais qui peut être facilement recréées. L’application doit créer et supprimer ces fichiers en fonction des besoins et être en mesure de recréer ces fichiers si nécessaire. iOS 5 peut également supprimer ces fichiers (dans les situations de stockage faible), mais elle ne sera pas le faire pendant l’exécution de l’application.<br /><br />Le contenu de ce répertoire n’est pas sauvegardé par iTunes, ce qui signifie qu’ils ne seront pas présents si l’utilisateur restaure un appareil, et ils ne soient pas présents après avoir installé une version mise à jour de votre application.<br /><br />Par exemple, au cas où votre application ne peut pas se connecter au réseau, vous pouvez utiliser le répertoire de Caches pour stocker les fichiers de données ou pour fournir une bonne expérience hors connexion. L’application peut enregistrer et récupérer ces données rapidement en attendant des réponses du réseau, mais il ne doit pas nécessairement être sauvegardées et peut facilement être récupéré ou recréé après une restauration ou une version mise à jour.|
|tmp/|Les applications peuvent stocker des fichiers temporaires qui sont nécessaires uniquement pour une courte période dans ce répertoire. Pour économiser l’espace, les fichiers doivent être supprimés lorsqu’ils ne sont plus nécessaires. Le système d’exploitation peut également supprimer des fichiers à partir de ce répertoire quand une application n’est pas en cours d’exécution.<br /><br />Le contenu de ce répertoire n’est pas sauvegardé par iTunes.<br /><br />Par exemple, le répertoire tmp peut servir à stocker les fichiers temporaires qui sont téléchargés pour l’affichage à l’utilisateur (par exemple, les avatars Twitter ou les pièces jointes), mais qui peut être supprimé une fois qu’ils ont été affichées (et téléchargées à nouveau s’ils sont nécessaires à l’avenir) .|

Cette capture d’écran montre la structure de répertoires dans une fenêtre Finder :

[![](file-system-images/08-library-directory.png "Cette capture d’écran montre la structure de répertoires dans une fenêtre Finder")](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>Accès par programme aux autres répertoires

Les exemples précédents de répertoires et les fichiers accédés les `Documents` directory. Pour écrire dans un autre répertoire, vous devez construire un chemin d’accès à l’aide de la «. « syntaxe comme illustré ici :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

Création d’un répertoire est similaire :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Chemins d’accès à la `Caches` et `tmp` répertoires peuvent être construites comme suit :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>Partage avec l’application de fichiers

iOS 11 a introduit le **fichiers** application - un Explorateur de fichiers pour iOS, qui permet à l’utilisateur voir et interagir avec leurs fichiers dans iCloud et également stockés par toute application qui prend en charge. Pour autoriser l’utilisateur à accéder directement aux fichiers dans votre application, créez une nouvelle clé booléenne dans la **Info.plist** fichier `LSSupportsOpeningDocumentsInPlace` et affectez-lui la valeur `true`, comme ici :

![Définissez LSSupportsOpeningDocumentsInPlace dans Info.plist](file-system-images/51-supports-opening.png)

L’application **Documents** répertoire seront désormais disponible pour la navigation dans les **fichiers** application. Dans le **fichiers** application, accédez à **sur mon iPhone** et chaque application avec fichiers partagés seront visible. Les captures d’écran ci-dessous montrent ce que le [exemple d’application du système de fichiers](https://developer.xamarin.com/samples/monotouch/FileSystemSampleCode/) ressemble à :

![application de fichiers iOS 11](file-system-images/50-files-app-1-sml.png) ![Parcourir les fichiers de mon iPhone](file-system-images/50-files-app-2-sml.png) ![Fichiers d’application exemple](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Partage de fichiers avec l’utilisateur via iTunes

Accessibles aux utilisateurs les fichiers dans le répertoire de Documents de votre application en modifiant `Info.plist` et la création d’un **Application qui prend en charge le partage iTunes** (`UIFileSharingEnabled`) entrée dans le **Source** afficher, comme illustré ici :

[![Ajout de l’Application prend en charge les iTunes partage la propriété](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Ces fichiers sont accessibles dans iTunes lorsque l’appareil est connecté et que l’utilisateur choisit le `Apps` onglet. Par exemple, la capture d’écran suivante montre les fichiers dans l’application sélectionnée partagée via iTunes :

[![Cette capture d’écran montre les fichiers dans l’application sélectionnée partagée via iTunes](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Les utilisateurs peuvent accéder uniquement les éléments de niveau supérieur dans ce répertoire via iTunes. Ils ne peuvent pas voir le contenu de tous les sous-répertoires (bien qu’ils peuvent les copier sur leur ordinateur ou les supprimer). Par exemple, avec GoodReader, PDF et EPUB peut être partagé avec l’application afin que les utilisateurs peuvent les lire sur leurs appareils iOS.

Les utilisateurs qui modifient le contenu du dossier des Documents peuvent entraîner des problèmes si elles ne sont pas prudents. Votre application doit prendre cela en compte et qu’elles soient résistantes aux mises à jour destructives du dossier Documents.

L’exemple de code pour cet article crée un fichier et un dossier dans le dossier Documents (dans **SampleCode.cs**) et permet le partage de fichiers dans le **Info.plist** fichier. Cette capture d’écran montre comment ceux-ci s’affichent dans iTunes :

[![Cette capture d’écran montre comment les fichiers apparaissent dans iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Reportez-vous à la [utilisation des Images](~/ios/app-fundamentals/images-icons/index.md) article pour plus d’informations sur la façon de définir des icônes d’application et pour tout type de document personnalisées que vous créez.

Si le `UIFileSharingEnabled` clé est false ou absent, puis le partage de fichiers est désactivée, par défaut et les utilisateurs ne seront pas en mesure d’interagir avec votre répertoire de Documents.

## <a name="backup-and-restore"></a>Sauvegarde et restauration

Lorsqu’un appareil est sauvegardé par iTunes, tous les répertoires créés dans le répertoire de base de votre application seront enregistrés, sauf les répertoires suivants :

- **[ApplicationName] .app** – ne pas écrire dans ce répertoire, dans la mesure où il est signé et donc de doit pas être modifiée après l’installation. Il peut contenir des ressources accessibles à partir de votre code, mais ils ne nécessitent pas de sauvegarde dans la mesure où il seraient restaurés en téléchargeant de nouveau l’application.
- **Bibliothèque/Caches** : le répertoire de cache est destiné aux fichiers de travail qui ne doivent pas être sauvegardées.
- **TMP** : ce répertoire est utilisé pour les fichiers temporaires qui sont créés et supprimés lorsque vous n’avez plus besoin, ou pour les fichiers que l’option iOS supprime lorsqu’il a besoin d’espace.

Sauvegarde d’une grande quantité de données peut prendre un certain temps. Si vous décidez que vous avez besoin sauvegarder n’importe quel document particulier ou les données, votre application doit utiliser les Documents et les dossiers de bibliothèque. Pour les données temporaires ou les fichiers qui peuvent être facilement récupérées à partir du réseau, utilisez les Caches ou le répertoire tmp.

> [!NOTE]
> iOS sera 'clean' le système de fichiers quand un appareil s’exécute peut-être dangereusement bas sur l’espace disque.
> Ce processus supprime tous les fichiers de la bibliothèque/Caches et tmp dossier des applications qui n’est pas en cours d’exécution.

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>Conforme aux restrictions de sauvegarde iCloud 5 iOS

> [!NOTE]
> Bien que cette stratégie a été le premier introduit avec iOS 5 (ce qui semble être il y a longtemps) les instructions sont toujours applicables aux applications dès aujourd'hui.

Apple a introduit *sauvegarde iCloud* fonctionnalité avec iOS 5. Lorsque la sauvegarde iCloud est activée, tous les fichiers du répertoire de base de votre application (à l’exclusion de répertoires qui ne sont pas normalement sauvegardés, par exemple, l’offre groupée d’applications, `Caches`, et `tmp`) sont sauvegardés sur les serveurs iCloud. Cette fonctionnalité fournit à l’utilisateur avec une sauvegarde complète au cas où leur appareil est perdu, volé ou endommagé.

Étant donné qu’iCloud offre uniquement 5 Go d’espace 'free' pour chaque utilisateur et d’éviter d’utiliser inutilement la bande passante, Apple attend applications uniquement sauvegarder essentielles généré par l’utilisateur des données. Pour respecter les instructions de stockage de données iOS, vous devez limiter la quantité de données sont sauvegardées en adhérant aux éléments suivants :

- Stocker uniquement les données générées par l’utilisateur ou données qui sinon ne peut pas être recréées, dans le répertoire Documents (qui est sauvegardé).
- Store toutes les autres données pouvant facilement être recréées ou nouveau téléchargées dans `Library/Caches` ou `tmp` (qui n’est pas sauvegardé et peut être « nettoyé »).
- Si vous disposez des fichiers peuvent être appropriées pour le `Library/Caches` ou `tmp` dossier, mais vous ne souhaitez pas être 'nettoyés' out, les stocker ailleurs (tel que `Library/YourData`) et appliquer le ' ne pas sauvegarder des ' attribut pour empêcher que les fichiers à l’aide de la sauvegarde iCloud espace de stockage et de la bande passante. Ces données utilisent toujours l’espace sur l’appareil, donc vous devez gérer soigneusement et supprimez-le lorsque cela est possible.

Le ' ne pas sauvegarder des ' attribut est défini à l’aide de la `NSFileManager` classe. Vérifiez que votre classe est `using Foundation` et appelez `SetSkipBackupAttribute` comme suit :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Lorsque `SetSkipBackupAttribute` est `true` le fichier ne sera pas sauvegardée, quel que soit le répertoire, il est stocké dans (même si le `Documents` directory). Vous pouvez interroger l’attribut à l’aide de la `GetSkipBackupAttribute` (méthode) et vous pouvez rétablir ses en appelant le `SetSkipBackupAttribute` méthode avec `false`, comme suit :

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Partage des données entre applications iOS et les extensions d’application

Dans la mesure où les Extensions d’application s’exécuter en tant que partie d’une application hôte (par opposition à leur application de conteneur), le partage des données n’est pas automatique inclus un travail supplémentaire est nécessaire. Groupes d’applications sont que le mécanisme iOS utilise pour autoriser les applications de partager des données différentes. Si les applications ont été correctement configurées avec les droits corrects et l’approvisionnement, accéder à un répertoire partagé en dehors de leur bac à sable iOS normal.

### <a name="configure-an-app-group"></a>Configurer un groupe de l’application

L’emplacement partagé est configuré à l’aide un [App Group](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), qui est configuré dans le **certificats, identificateurs et profils** section [iOS Dev Center](https://developer.apple.com/devcenter/ios/). Cette valeur doit également être référencée dans de chaque projet **Entitlements.plist**.

Pour plus d’informations sur la création et configuration d’un groupe de l’application, reportez-vous à la [application groupe fonctionnalités](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) guide.

### <a name="files"></a>Fichiers

L’application iOS et l’extension peuvent également partager des fichiers à l’aide d’un chemin d’accès de fichier commun (étant donné qu’ils ont été correctement configuré avec les droits corrects et d’approvisionnement) :

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> Si le chemin d’accès de groupe retourné est `null`, vérifiez la configuration des droits et le profil de provisionnement et assurez-vous qu’ils sont corrects.

## <a name="application-version-updates"></a>Application des mises à jour de version

Lorsqu’une nouvelle version de votre application est téléchargée, iOS crée un nouveau répertoire de base et stocke le nouveau package d’Application qu’elle contient. iOS déplace ensuite les dossiers suivants à partir de la version précédente de votre offre groupée d’Application vers votre nouveau répertoire de base :

- **Documents**
- **Bibliothèque**

Autres répertoires peuvent également être copiés entre et placés sous votre nouveau répertoire de base, mais qu’ils ne sont pas garantis à copier, donc votre application ne doit pas compter sur ce comportement du système.

## <a name="summary"></a>Récapitulatif

Cet article vous a montré que les opérations de système de fichiers avec Xamarin.iOS sont similaires à n’importe quel autre application .NET. Il introduit le bac à sable d’Application et examiné les implications de sécurité il provoque. Ensuite, il a expliqué le concept d’une offre groupée d’Application. Enfin, il énumérer les répertoires spécialisées disponibles pour votre application et expliqué leur rôle au cours des sauvegardes et des mises à niveau de l’application.

## <a name="related-links"></a>Liens connexes

- [Exemple de code du système de fichiers](https://developer.xamarin.com/samples/monotouch/FileSystemSampleCode/)
- [Guide de programmation de système de fichiers](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Inscrit le fichier de Types de votre application prend en charge](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
