---
title: Utilisation des paramètres par défaut de l’utilisateur dans Xamarin. iOS
description: Cet article traite de l’utilisation de NSUserDefaults pour enregistrer les paramètres par défaut dans une application ou une extension Xamarin iOS. Il décrit NSUserDefaults à un niveau élevé et explique comment lire et écrire des valeurs.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 4706b483f8f3a104f54ea2bf451cb4e2fb209486
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009079"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Utilisation des paramètres par défaut de l’utilisateur dans Xamarin. iOS

_Cet article traite de l’utilisation de NSUserDefault pour enregistrer les paramètres par défaut dans une application ou une extension Xamarin. iOS._

La classe `NSUserDefaults` offre aux applications et extensions iOS un moyen d’interagir par programmation avec le système par défaut à l’ensemble du système. En utilisant le système par défaut, l’utilisateur peut configurer le comportement ou le style d’une application pour qu’elle réponde à ses préférences (en fonction de la conception de l’application). Par exemple, pour présenter des données dans les mesures métriques vs impériales ou sélectionner un thème d’interface utilisateur donné.

Lorsqu’il est utilisé avec les groupes d’applications, `NSUserDefaults` offre également un moyen de communiquer entre les applications (ou extensions) au sein d’un groupe donné.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>À propos des valeurs par défaut de l’utilisateur

Comme indiqué ci-dessus, les valeurs par défaut de l’utilisateur (`NSUserDefaults`) peuvent être ajoutées à une application (ou extension) et utilisées pour fournir des options configurables que l’utilisateur final peut modifier pour ajuster l’apparence ou le fonctionnement de l’application au moment de l’exécution.

Lorsque votre application s’exécute pour la première fois, `NSUserDefaults` lit les clés et les valeurs de la base de données par défaut de l’utilisateur de l’application et les met en cache dans la mémoire pour éviter d’ouvrir et de lire la base de données chaque fois qu’une valeur est requise. 

> [!IMPORTANT]
> Apple ne recommande plus que le développeur appelle la méthode `Synchronize` pour synchroniser le cache en mémoire directement avec la base de données. Au lieu de cela, il est automatiquement appelé à intervalles réguliers pour maintenir la synchronisation du cache en mémoire avec la base de données par défaut de l’utilisateur.

La classe `NSUserDefaults` contient plusieurs méthodes pratiques pour la lecture et l’écriture de valeurs de préférence pour les types de données courants tels que : String, Integer, float, Boolean et URLs. D’autres types de données peuvent être archivés à l’aide de `NSData`, puis lus ou écrits dans la base de données des paramètres par défaut de l’utilisateur. Pour plus d’informations, consultez le [Guide de programmation des préférences et des paramètres](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)d’Apple.

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Accès à l’instance NSUserDefaults partagée 

L’instance des paramètres par défaut de l’utilisateur partagé permet d’accéder aux valeurs par défaut de l’utilisateur pour l’utilisateur actuel de l’appareil. Si l’objet partagé par défaut n’existe pas, il est créé la première fois qu’il est accédé et initialisé avec les informations suivantes :

- `NSArgumentDomain` comprenant les valeurs par défaut analysées à partir de l’application actuelle.
- Domaine de l’identificateur de Bundle de l’application.
- `NSGlobalDomain` comprenant les valeurs par défaut partagées par toutes les applications.
- Un domaine distinct pour chaque langue par défaut de l’utilisateur.
- `NSRegistrationDomain` avec un ensemble de valeurs par défaut temporaires qui peuvent être modifiées par l’application pour garantir la réussite des recherches.

Pour accéder à l’instance des paramètres par défaut des utilisateurs partagés, utilisez le code suivant :

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Accès à une instance de NSUserDefaults de groupe d’applications

Comme indiqué ci-dessus, en utilisant des groupes d’applications, `NSUserDefaults` peut être utilisé pour communiquer entre des applications (ou des extensions) au sein d’un groupe donné. Tout d’abord, vous devez vous assurer que le groupe d’applications et les ID d’application requis ont été correctement configurés dans la section **certificats, identificateurs & profils** du centre de développement [iOS](https://developer.apple.com/devcenter/ios/) et ont été installées dans l’environnement de développement.

Ensuite, vos projets d’application et/ou d’extension doivent avoir l’un des ID d’application valides créés ci-dessus, et le fichier `Entitlements.plist` doit être inclus dans l’ensemble d’applications avec les groupes d’applications activés et spécifiés.

Tout cela étant en place, il est possible d’accéder aux valeurs par défaut de l’utilisateur du groupe d’applications partagé à l’aide du code suivant :

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Où `group.com.xamarin.todaysharing` est le groupe d’applications créé dans **certificats, identificateurs & profils** auxquels vous souhaitez accéder. Pour plus d’informations, consultez la documentation sur les [fonctionnalités du groupe d’applications](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Lecture des valeurs par défaut

Une fois que vous avez accédé à la base de données par défaut de l’utilisateur souhaité, vous pouvez lire les valeurs par défaut à l’aide de paires clé/valeur et de plusieurs méthodes pratiques en fonction du type de données lues :

- `ArrayForKey`-retourne un tableau de `NSObjects` pour la valeur de clé donnée.
- `BoolForKey`-retourne une valeur booléenne pour la clé donnée.
- `DataForKey`-retourne un objet `NSData` pour la clé donnée.
- `DictionaryForKey`-retourne un `NSDictionary` pour la clé donnée.
- `DoubleForKey`-retourne une valeur double pour la clé donnée.
- `FloatForKey`-retourne une valeur de type float pour la clé donnée.
- `IntForKey`-retourne une valeur entière pour la clé donnée.
- `StringArrayForKey`-retourne un tableau d’objets `String` à partir de la valeur de clé donnée.
- `StringForKey`-retourne une valeur de chaîne pour la clé donnée.
- `URLForKey`-retourne une valeur de `NSUrl` pour la clé donnée.

Par exemple, le code suivant lit une valeur booléenne à partir des valeurs par défaut de l’utilisateur :

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>Écriture des valeurs par défaut

Tout comme la lecture des valeurs ci-dessus, une fois que vous avez accédé à la base de données par défaut de l’utilisateur souhaité, vous pouvez écrire des valeurs dans les valeurs par défaut à l’aide de paires clé/valeur et plusieurs méthodes pratiques en fonction du type de données en cours d’écriture :

- `SetBool` : écrit la valeur booléenne donnée dans la clé donnée.
- `SetDouble` : écrit la valeur double donnée dans la clé donnée.
- `SetFloat` : écrit la valeur float donnée dans la clé donnée.
- `SetString` : écrit la valeur de chaîne donnée dans la clé donnée.
- `SetURL`-écrit la valeur de l’URL (`NSUrl`) donnée dans la clé donnée.

Par exemple, le code suivant écrit une valeur booléenne dans les valeurs par défaut de l’utilisateur :

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Lorsque votre application s’exécute pour la première fois, `NSUserDefaults` lit les clés et les valeurs de la base de données par défaut de l’utilisateur de l’application et les met en cache dans la mémoire pour éviter d’ouvrir et de lire la base de données chaque fois qu’une valeur est requise.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la classe `NSUserDefaults` et comment elle peut être utilisée pour fournir un ensemble d’options que l’utilisateur final peut utiliser pour configurer votre application Xamarin. iOS. En outre, il a abordé l’utilisation des groupes d’applications pour communiquer entre une extension et son application parente ou entre les applications d’un groupe.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Guide de programmation des préférences et des paramètres](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
