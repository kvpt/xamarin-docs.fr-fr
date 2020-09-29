---
title: Accès au système de fichiers dans Xamarin. iOS
description: Ce document explique comment utiliser le système de fichiers dans Xamarin. iOS. Il traite des répertoires, de la lecture de fichiers, de la sérialisation XML et JSON, du bac à sable de l’application, du partage de fichiers via iTunes et bien plus encore.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/12/2018
ms.openlocfilehash: ef632b4bff3313de82c71cb5839ecdc24c9242ad
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431491"
---
# <a name="file-system-access-in-xamarinios"></a>Accès au système de fichiers dans Xamarin. iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)

Vous pouvez utiliser Xamarin. iOS et les `System.IO` classes de la *bibliothèque de classes de base .net (BCL)* pour accéder au système de fichiers iOS. La classe `File` permet de créer, supprimer et lire des fichiers, et la classe `Directory` permet de créer, supprimer ou énumérer le contenu des répertoires. Vous pouvez également utiliser `Stream` des sous-classes, qui permettent de mieux contrôler les opérations sur les fichiers (par exemple, la compression ou la recherche de position dans un fichier).

iOS impose des restrictions sur ce qu’une application peut faire avec le système de fichiers afin de préserver la sécurité des données d’une application et de protéger les utilisateurs des applications malveillantes. Ces restrictions font partie du *bac à sable (sandbox* ) de l’application, un ensemble de règles qui limite l’accès d’une application aux fichiers, préférences, ressources réseau, matériel, etc. Une application est limitée à la lecture et à l’écriture de fichiers dans son répertoire de départ (emplacement d’installation); il ne peut pas accéder aux fichiers d’une autre application.

iOS offre également des fonctionnalités spécifiques au système de fichiers : certains annuaires nécessitent un traitement spécial en ce qui concerne les sauvegardes et les mises à niveau, et les applications peuvent également partager des fichiers entre eux et l’application **fichiers** (depuis iOS 11) et via iTunes.

Cet article présente les fonctionnalités et les restrictions du système de fichiers iOS, ainsi qu’un exemple d’application qui montre comment utiliser Xamarin. iOS pour exécuter des opérations de système de fichiers simples :

[![Exemple d’iOS exécutant des opérations de système de fichiers simples](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>Accès général aux fichiers

Xamarin. iOS vous permet d’utiliser les `System.IO` classes .net pour les opérations du système de fichiers sur iOS.

Les extraits de code suivants illustrent des opérations de fichier courantes. Vous les trouverez ci-dessous dans le fichier **SampleCode.cs** , dans l’exemple d’application de cet article.

### <a name="working-with-directories"></a>Utilisation des répertoires

Ce code énumère les sous-répertoires du répertoire actif (spécifié par le paramètre « ./ »), qui est l’emplacement de l’exécutable de votre application.
Votre sortie sera une liste de tous les fichiers et dossiers déployés avec votre application (affichée dans la fenêtre de console pendant le débogage).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>Lecture des fichiers

Pour lire un fichier texte, vous n’avez besoin que d’une seule ligne de code. Cet exemple affiche le contenu d’un fichier texte dans la fenêtre sortie de l’application.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>sérialisation XML

Bien que l’utilisation `System.Xml` de l’espace de noms complet dépasse le cadre de cet article, vous pouvez facilement désérialiser un document XML à partir du système de fichiers à l’aide d’un StreamReader comme cet extrait de code :

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Pour plus d’informations, consultez la documentation relative à [System.Xml](xref:System.Xml) et à la [sérialisation](xref:System.Xml.Serialization). Consultez la [documentation Xamarin. iOS](~/ios/deploy-test/linker.md) sur l’éditeur de liens – vous devrez souvent ajouter l' `[Preserve]` attribut aux classes que vous souhaitez sérialiser.

### <a name="creating-files-and-directories"></a>Création de fichiers et de répertoires

Cet exemple montre comment utiliser la `Environment` classe pour accéder au dossier documents où nous pouvons créer des fichiers et des répertoires.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

La création d’un répertoire est un processus similaire :

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Pour plus d’informations, consultez la référence de l' [API System.IO](xref:System.IO).

### <a name="serializing-json"></a>Sérialisation de JSON

[JSON.net](http://www.newtonsoft.com/json) est une infrastructure JSON haute performance qui fonctionne avec Xamarin. iOS et est disponible sur NuGet. Ajoutez le package NuGet à votre projet d’application, en utilisant **Ajouter NuGet** dans Visual Studio pour Mac :

[![Ajout du package NuGet au projet d’applications](file-system-images/json01.png)](file-system-images/json01.png#lightbox)

Ensuite, ajoutez une classe pour agir en tant que modèle de données pour la sérialisation/désérialisation (dans ce cas `Account.cs` ) :

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

Enfin, créez une instance de la `Account` classe, sérialisez-la en données JSON et écrivez-la dans un fichier :

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

Pour plus d’informations sur l’utilisation des données JSON dans une application .NET, consultez la [documentation](https://www.newtonsoft.com/json/help)de JSON. net.

## <a name="special-considerations"></a>Considérations spéciales

En dépit des similarités entre les opérations de fichiers Xamarin. iOS et .NET, iOS et Xamarin. iOS diffèrent de .NET de manière importante.

### <a name="making-project-files-accessible-at-runtime"></a>Rendre les fichiers projet accessibles au moment de l’exécution

Par défaut, si vous ajoutez un fichier à votre projet, il n’est pas inclus dans l’assembly final et par conséquent, il n’est pas disponible pour votre application. Pour inclure un fichier dans l’assembly, vous devez le marquer avec une action de génération spéciale, appelée contenu.

Pour marquer un fichier à inclure, cliquez avec le bouton droit sur le ou les fichiers et choisissez **générer le &gt; contenu** de l’action dans Visual Studio pour Mac. Vous pouvez également modifier l' **action de génération** dans la feuille de **Propriétés** du fichier.

### <a name="case-sensitivity"></a>Respect de la casse

Il est important de comprendre que le système de fichiers iOS respecte la *casse*. Le respect de la casse signifie que vos noms de fichiers et de répertoires doivent correspondre exactement – **README.txt** et **readme.txt** seraient considérés comme des noms de fichiers différents.

Cela peut être confus pour les développeurs .NET qui sont plus familiarisés avec le système de fichiers Windows, qui ne respecte pas la *casse* : les **fichiers** **, les fichiers et**les **fichiers** font tous référence au même répertoire.

> [!WARNING]
> Le simulateur iOS ne respecte pas la casse.
> Si la casse du nom de fichier diffère entre le fichier lui-même et les références à celui-ci dans le code, votre code peut continuer à fonctionner dans le simulateur, mais il échouera sur un appareil réel. C’est l’une des raisons pour lesquelles il est important de déployer et de tester sur un appareil réel très tôt et souvent pendant le développement iOS.

### <a name="path-separator"></a>Séparateur de chemin

iOS utilise la barre oblique « / » comme séparateur de chemin d’accès (ce qui est différent de Windows, qui utilise la barre oblique inverse « \ »).

En raison de cette différence confuse, il est conseillé d’utiliser la `System.IO.Path.Combine` méthode, qui s’ajuste à la plateforme actuelle, plutôt que de coder en dur un séparateur de chemin d’accès particulier. Il s’agit d’une étape simple qui rend votre code plus portable pour d’autres plateformes.

## <a name="application-sandbox"></a>Bac à sable d’application

L’accès de votre application au système de fichiers (et à d’autres ressources telles que les fonctionnalités réseau et matérielles) est limité pour des raisons de sécurité. Cette restriction est appelée bac à *sable (sandbox) d’application*. En termes de système de fichiers, votre application est limitée à la création et à la suppression de fichiers et de répertoires dans son répertoire de départ.

Le répertoire de départ est un emplacement unique dans le système de fichiers dans lequel votre application et toutes ses données sont stockées. Vous ne pouvez pas choisir (ou modifier) l’emplacement du répertoire de départ pour votre application ; Toutefois, iOS et Xamarin. iOS fournissent des propriétés et des méthodes pour gérer les fichiers et les répertoires à l’intérieur de.

## <a name="the-application-bundle"></a>Le bundle d’applications

L' *offre groupée d’applications* est le dossier qui contient votre application.
Elle est distinguée des autres dossiers en faisant en sorte que le suffixe. app soit ajouté au nom du répertoire. Votre ensemble d’applications contient votre fichier exécutable et tout le contenu (fichiers, images, etc.) nécessaires à votre projet.

Lorsque vous accédez à votre offre groupée d’applications dans Mac OS, elle apparaît avec une icône différente de celle que vous voyez dans d’autres répertoires (et le suffixe **. app** est masqué); Toutefois, il s’agit simplement d’un répertoire standard que le système d’exploitation affiche différemment.

Pour afficher l’offre groupée d’applications pour l’exemple de code, cliquez avec le bouton droit sur le projet dans **Visual Studio pour Mac** et sélectionnez **révéler dans le Finder**. Accédez ensuite à l' **emplacement/** répertoire où vous devriez trouver une icône d’application (semblable à la capture d’écran ci-dessous).

![Parcourez le répertoire bin pour trouver une icône d’application semblable à cette capture d’écran.](file-system-images/40-bundle.png)

Cliquez avec le bouton droit sur cette icône et choisissez **afficher le contenu du package** pour parcourir le contenu du répertoire de l’ensemble d’applications. Le contenu s’affiche comme le contenu d’un répertoire normal, comme illustré ici :

[![Contenu de l’offre groupée d’applications](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

L’offre groupée d’applications est celle qui est installée sur le simulateur ou sur votre appareil pendant le test, et il s’agit finalement de ce qui est envoyé à Apple pour l’inclure dans l’App Store.

## <a name="application-directories"></a>Répertoires d’application

Lorsque votre application est installée sur un appareil, le système d’exploitation crée un répertoire de démarrage pour votre application et crée un certain nombre de répertoires dans le répertoire racine de l’application qui peuvent être utilisés. Depuis iOS 8, les répertoires accessibles à l’utilisateur ne sont [pas situés](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) dans la racine de l’application, vous ne pouvez donc pas dériver les chemins d’accès du bundle d’applications à partir des répertoires utilisateur, ou vice versa.

Ces répertoires, la façon de déterminer leur chemin d’accès et leurs objectifs sont répertoriés ci-dessous :

&nbsp;

|Répertoire|Description|
|---|---|
|[ApplicationName]. app/|**Dans iOS 7 et les versions antérieures**, il s’agit du `ApplicationBundle` répertoire dans lequel l’exécutable de votre application est stocké. La structure de répertoires que vous créez dans votre application existe dans ce répertoire (par exemple, les images et autres types de fichiers que vous avez marqués comme ressources dans votre projet Visual Studio pour Mac).<br /><br />Si vous avez besoin d’accéder aux fichiers de contenu à l’intérieur de votre offre groupée d’applications, le chemin d’accès à ce répertoire est disponible via la `NSBundle.MainBundle.BundlePath` propriété.|
|Évoqu|Utilisez ce répertoire pour stocker les documents utilisateur et les fichiers de données d’application.<br /><br />Le contenu de ce répertoire peut être mis à la disposition de l’utilisateur par le biais du partage de fichiers iTunes (bien que cela soit désactivé par défaut). Ajoutez une `UIFileSharingEnabled` clé booléenne au fichier info. plist pour permettre aux utilisateurs d’accéder à ces fichiers.<br /><br />Même si une application n’active pas immédiatement le partage de fichiers, vous devez éviter de placer les fichiers qui doivent être masqués par vos utilisateurs dans ce répertoire (tels que les fichiers de base de données, sauf si vous envisagez de les partager). Tant que les fichiers sensibles restent masqués, ces fichiers ne sont pas exposés (et éventuellement déplacés, modifiés ou supprimés par iTunes) si le partage de fichiers est activé dans une version ultérieure.<br /><br /> Vous pouvez utiliser la `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` méthode pour obtenir le chemin d’accès au répertoire documents de votre application.<br /><br />Le contenu de ce répertoire est sauvegardé par iTunes.|
|Bibliothèque|Le répertoire de bibliothèque est un bon emplacement pour stocker des fichiers qui ne sont pas créés directement par l’utilisateur, tels que des bases de données ou d’autres fichiers générés par l’application. Le contenu de ce répertoire n’est jamais exposé à l’utilisateur via iTunes.<br /><br />Vous pouvez créer vos propres sous-répertoires dans la bibliothèque. Toutefois, il existe déjà des répertoires créés par le système, dont vous devez être conscient, y compris les préférences et les caches.<br /><br />Le contenu de ce répertoire (à l’exception du sous-répertoire caches) est sauvegardé par iTunes. Les répertoires personnalisés que vous créez dans la bibliothèque seront sauvegardés.|
|Bibliothèque/Préférences/|Les fichiers de préférences spécifiques à l’application sont stockés dans ce répertoire. Ne créez pas ces fichiers directement. Utilisez plutôt la `NSUserDefaults` classe.<br /><br />Le contenu de ce répertoire est sauvegardé par iTunes.|
|Bibliothèque/caches/|Le répertoire caches est un bon emplacement pour stocker des fichiers de données qui peuvent aider votre application à s’exécuter, mais qui peuvent être facilement recréés. L’application doit créer et supprimer ces fichiers en fonction des besoins et être en mesure de recréer ces fichiers si nécessaire. iOS 5 peut également supprimer ces fichiers (dans le cas d’une mémoire insuffisante), mais il ne le fera pas tant que l’application est en cours d’exécution.<br /><br />Le contenu de ce répertoire n’est pas sauvegardé par iTunes, ce qui signifie qu’il ne sera pas présent si l’utilisateur restaure un appareil et qu’il n’est peut-être pas présent après l’installation d’une version mise à jour de votre application.<br /><br />Par exemple, si votre application ne peut pas se connecter au réseau, vous pouvez utiliser le répertoire de caches pour stocker des données ou des fichiers afin d’offrir une bonne expérience hors connexion. L’application peut enregistrer et récupérer ces données rapidement en attendant les réponses réseau, mais elle n’a pas besoin d’être sauvegardée et peut être facilement Récupérée ou recréée après une restauration ou une mise à jour de version.|
|tmp|Les applications peuvent stocker des fichiers temporaires qui sont uniquement nécessaires pendant une brève période dans ce répertoire. Pour économiser de l’espace, les fichiers doivent être supprimés lorsqu’ils ne sont plus nécessaires. Le système d’exploitation peut également supprimer des fichiers de ce répertoire lorsqu’une application n’est pas en cours d’exécution.<br /><br />Le contenu de ce répertoire n’est pas sauvegardé par iTunes.<br /><br />Par exemple, le répertoire tmp peut être utilisé pour stocker les fichiers temporaires qui sont téléchargés pour l’affichage à l’utilisateur (par exemple, avatars Twitter ou pièces jointes de courrier électronique), mais qui peuvent être supprimés une fois qu’ils ont été consultés (et téléchargés à nouveau s’ils sont requis dans le futur).|

Cette capture d’écran montre la structure de répertoires dans une fenêtre de recherche :

[![Cette capture d’écran montre la structure de répertoires dans une fenêtre de recherche](file-system-images/08-library-directory.png)](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>Accès à d’autres répertoires par programmation

Les exemples de répertoire et de fichier antérieurs ont accédé à l' `Documents` Annuaire. Pour écrire dans un autre répertoire, vous devez créer un chemin d’accès à l’aide de la syntaxe « .. » comme indiqué ici :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

La création d’un répertoire est similaire :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Les chemins d’accès aux `Caches` `tmp` répertoires et peuvent être construits comme suit :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>Partage avec l’application fichiers

iOS 11 a introduit l’application **fichiers** -un Explorateur de fichiers pour IOS qui permet à l’utilisateur d’afficher et d’interagir avec ses fichiers dans icloud et également stockée par n’importe quelle application qui le prend en charge. Pour permettre à l’utilisateur d’accéder directement aux fichiers de votre application, créez une clé booléenne dans le fichier **info. plist** `LSSupportsOpeningDocumentsInPlace` et affectez-lui la valeur `true` , comme suit :

![Définir LSSupportsOpeningDocumentsInPlace dans info. plist](file-system-images/51-supports-opening.png)

Le répertoire **documents** de l’application est maintenant disponible pour l’exploration dans l’application **fichiers** . Dans l’application **fichiers** , accédez à **mon iPhone** et chaque application avec des fichiers partagés est visible. Les captures d’écran ci-dessous montrent à quoi ressemble l' [exemple d’application de système de fichiers](/samples/xamarin/ios-samples/filesystemsamplecode) :

![application fichiers iOS 11](file-system-images/50-files-app-1-sml.png) ![Parcourir mes fichiers iPhone](file-system-images/50-files-app-2-sml.png) ![Exemples de fichiers d’application](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Partage de fichiers avec l’utilisateur via iTunes

Les utilisateurs peuvent accéder aux fichiers dans le répertoire documents de votre application en modifiant `Info.plist` et en créant une **application qui prend en charge** l’entrée de partage iTunes ( `UIFileSharingEnabled` ) dans la vue **source** , comme illustré ici :

[![L’ajout de l’application prend en charge la propriété de partage iTunes](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Ces fichiers sont accessibles dans iTunes lorsque l’appareil est connecté et que l’utilisateur choisit l' `Apps` onglet. Par exemple, la capture d’écran suivante montre les fichiers dans l’application sélectionnée partagée via iTunes :

[![Cette capture d’écran montre les fichiers de l’application sélectionnée, partagés par le biais d’iTunes.](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Les utilisateurs ne peuvent accéder aux éléments de niveau supérieur de ce répertoire qu’à l’aide d’iTunes. Ils ne peuvent pas voir le contenu de tous les sous-répertoires (même s’ils peuvent les copier sur leur ordinateur ou les supprimer). Par exemple, avec GoodReader, les fichiers PDF et EPUB peuvent être partagés avec l’application afin que les utilisateurs puissent les lire sur leurs appareils iOS.

Les utilisateurs qui modifient le contenu du dossier documents peuvent provoquer des problèmes s’ils ne sont pas vigilants. Votre application doit prendre cela en considération et être résiliente aux mises à jour destructrices du dossier Documents.

L’exemple de code de cet article crée un fichier et un dossier dans le dossier documents (dans **SampleCode.cs**) et active le partage de fichiers dans le fichier **info. plist** . Cette capture d’écran montre comment elles apparaissent dans iTunes :

[![Cette capture d’écran montre comment les fichiers apparaissent dans iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Reportez-vous à l’article [utilisation d’images](~/ios/app-fundamentals/images-icons/index.md) pour plus d’informations sur la façon de définir des icônes pour l’application et pour les types de documents personnalisés que vous créez.

Si la `UIFileSharingEnabled` clé est false ou absente, le partage de fichiers est, par défaut, désactivé, et les utilisateurs ne peuvent pas interagir avec votre répertoire de documents.

## <a name="backup-and-restore"></a>Sauvegarde et restauration

Quand un appareil est sauvegardé par iTunes, tous les répertoires créés dans le répertoire de départ de votre application sont enregistrés, à l’exception des répertoires suivants :

- **[ApplicationName]. app** : n’écrivez pas dans ce répertoire, car il est signé et doit donc rester inchangé après l’installation. Il peut contenir des ressources auxquelles vous accédez à partir de votre code, mais elles ne nécessitent pas de sauvegarde, car elles seraient restaurées en téléchargeant l’application.
- **Bibliothèque/caches** : le répertoire du cache est destiné aux fichiers de travail qui n’ont pas besoin d’être sauvegardés.
- **tmp** : ce répertoire est utilisé pour les fichiers temporaires qui sont créés et supprimés lorsque vous n’en avez plus besoin, ou pour les fichiers que IOS supprime lorsqu’il a besoin d’espace.

La sauvegarde d’une grande quantité de données peut prendre beaucoup de temps. Si vous décidez de sauvegarder un document ou des données spécifiques, votre application doit utiliser les dossiers documents et bibliothèque. Pour les données temporaires ou les fichiers qui peuvent être facilement récupérés à partir du réseau, utilisez les caches ou le répertoire tmp.

> [!NOTE]
> iOS « nettoie » le système de fichiers lorsqu’un appareil ne dispose pas de suffisamment d’espace disque.
> Ce processus va supprimer tous les fichiers du dossier Library/caches et tmp des applications qui ne sont pas en cours d’exécution.

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>Respect des restrictions de sauvegarde iOS 5 iCloud

> [!NOTE]
> Bien que cette stratégie ait été introduite pour la première fois avec iOS 5 (il y a un peu plus longtemps), les conseils s’appliquent toujours aux applications dès aujourd’hui.

Apple a introduit la fonctionnalité de *sauvegarde icloud* avec iOS 5. Lorsque la sauvegarde iCloud est activée, tous les fichiers dans le répertoire de démarrage de votre application (à l’exclusion des répertoires qui ne sont normalement pas sauvegardés, par exemple, l’ensemble d’applications, `Caches` et `tmp` ) sont sauvegardés sur les serveurs iCloud. Cette fonctionnalité permet à l’utilisateur d’effectuer une sauvegarde complète en cas de perte, de vol ou d’endommagement de son appareil.

Étant donné que iCloud fournit uniquement 5 Go d’espace libre à chaque utilisateur et pour éviter d’utiliser inutilement la bande passante, Apple s’attend à ce que les applications ne sauvegardent que les données essentielles générées par l’utilisateur. Pour se conformer aux instructions de stockage de données iOS, vous devez limiter la quantité de données sauvegardées en adhérant aux éléments suivants :

- Stockez uniquement les données générées par l’utilisateur ou les données qui ne peuvent pas être recréées dans le répertoire documents (qui est sauvegardé).
- Stockez toutes les autres données qui peuvent être facilement recréées ou téléchargées dans `Library/Caches` ou `tmp` (qui ne sont pas sauvegardées et qui peuvent être « nettoyées »).
- Si vous avez des fichiers qui peuvent être appropriés pour `Library/Caches` le `tmp` dossier ou, mais que vous ne souhaitez pas l’être, vous devez les stocker ailleurs (par exemple `Library/YourData` ) et appliquer l’attribut « ne pas sauvegarder » pour empêcher les fichiers d’utiliser la bande passante de sauvegarde icloud et l’espace de stockage. Ces données utilisent toujours de l’espace sur l’appareil. vous devez donc la gérer avec précaution et la supprimer si possible.

L’attribut’ne pas sauvegarder’est défini à l’aide de la `NSFileManager` classe. Vérifiez que votre classe est `using Foundation` et `SetSkipBackupAttribute` qu’elle se présente comme suit :

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Lorsque `SetSkipBackupAttribute` est `true` , le fichier ne sera pas sauvegardé, quel que soit le répertoire dans lequel il est stocké (même le `Documents` Répertoire). Vous pouvez interroger l’attribut à l’aide de la `GetSkipBackupAttribute` méthode, et vous pouvez le réinitialiser en appelant la `SetSkipBackupAttribute` méthode avec `false` , comme suit :

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Partage de données entre des applications iOS et des extensions d’application

Étant donné que les extensions d’application s’exécutent dans le cadre d’une application hôte (par opposition à leur application conteneur), le partage des données n’est pas automatique inclus afin que des tâches supplémentaires soient nécessaires. Les groupes d’applications sont le mécanisme utilisé par iOS pour permettre à différentes applications de partager des données. Si les applications ont été correctement configurées avec les droits et l’approvisionnement appropriés, ils peuvent accéder à un répertoire partagé en dehors de leur bac à sable (sandbox) iOS normal.

### <a name="configure-an-app-group"></a>Configurer un groupe d’applications

L’emplacement partagé est configuré à l’aide d’un [groupe d’applications](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), qui est configuré dans la section **certificats, identificateurs & profils** du [Centre de développement iOS](https://developer.apple.com/devcenter/ios/). Cette valeur doit également être référencée dans les **droits. plist**de chaque projet.

Pour plus d’informations sur la création et la configuration d’un groupe d’applications, reportez-vous au Guide des [fonctionnalités du groupe d’applications](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

### <a name="files"></a>Files

L’application iOS et l’extension peuvent également partager des fichiers à l’aide d’un chemin d’accès de fichier commun (étant donné qu’ils ont été correctement configurés avec les droits et l’approvisionnement appropriés) :

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> Si le chemin d’accès au groupe retourné est `null` , vérifiez la configuration des droits et le profil de provisionnement et assurez-vous qu’ils sont corrects.

## <a name="application-version-updates"></a>Mises à jour de la version de l’application

Quand une nouvelle version de votre application est téléchargée, iOS crée un répertoire de départ et y stocke le groupe de nouvelles applications. iOS déplace ensuite les dossiers suivants de la version précédente de votre lot d’applications vers votre nouveau répertoire de départ :

- **Documents**
- **Bibliothèque**

D’autres répertoires peuvent également être copiés dans le répertoire de base et placés dans celui-ci, mais ils ne sont pas forcément copiés. votre application ne doit donc pas reposer sur ce comportement système.

## <a name="summary"></a>Récapitulatif

Cet article a montré que les opérations de système de fichiers avec Xamarin. iOS sont similaires à toute autre application .NET. Elle a également introduit le bac à sable (sandbox) de l’application et examiné les implications en matière de sécurité. Ensuite, il a exploré le concept d’un bundle d’applications. Enfin, il a énuméré les répertoires spécialisés disponibles pour votre application et expliqué leurs rôles lors des mises à niveau et des sauvegardes d’applications.

## <a name="related-links"></a>Liens connexes

- [Exemple de code de système de fichiers](/samples/xamarin/ios-samples/filesystemsamplecode)
- [Guide de programmation du système de fichiers](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Enregistrement des types de fichiers pris en charge par votre application](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)