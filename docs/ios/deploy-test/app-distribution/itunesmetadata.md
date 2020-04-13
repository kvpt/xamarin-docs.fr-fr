---
title: Fichier iTunesMetadata.plist dans les applications Xamarin.iOS
description: Cet article présente le fichier iTunesMetadata.plist, qui permet de fournir des informations à iTunes sur une application iOS à l’aide de la distribution ad hoc pour des tests ou un déploiement en entreprise.
ms.prod: xamarin
ms.assetid: 70676eba-6a99-4a3a-bccc-84359fe9c2c3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 63a5ed357a903700ea89d858bcde9798ddf97942
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76724618"
---
# <a name="the-itunesmetadataplist-file-in-xamarinios-apps"></a>Fichier iTunesMetadata.plist dans les applications Xamarin.iOS

_Cet article présente le fichier iTunesMetadata.plist, qui permet de fournir des informations à iTunes sur une application iOS à l’aide de la distribution ad hoc pour des tests ou un déploiement en entreprise._

Quand un développeur crée une application iOS dans iTunes Connect (qu’elle soit gratuite ou payante sur l’App Store d’iTunes), il peut spécifier des informations telles que le genre, le sous-genre, le copyright, les appareils iOS pris en charge, ainsi que les fonctionnalités offertes. Pour les applications iOS fournies aux testeurs ou aux utilisateurs en entreprise via une distribution ad hoc, ces informations sont manquantes.

Pour fournir les informations manquantes d’une distribution ad hoc, vous pouvez créer un fichier `iTunesMetadata.plist` facultatif et l’inclure dans le fichier IPA des applications. Ce fichier plist est un fichier XML ayant un format spécial (pour plus d’informations, consultez le [Guide de programmation des listes de propriétés](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html) d’Apple), qui contient des paires clé/valeur définissant les informations relatives à une application iOS donnée.

<a name="iTunesMetadata_contents" />

## <a name="the-itunesmetadataplist-contents"></a>Contenu d’iTunesMetadata.plist

Voici un exemple de fichier `iTunesMetadata.plist` classique permettant de définir les informations d’iTunes pour une distribution ad hoc :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>UIRequiredDeviceCapabilities</key>
    <dict>
        <key>armv7</key>
        <true/>
        <key>front-facing-camera</key>
        <true/>
    </dict>
    <key>artistName</key>
    <string>Company, Inc.</string>
    <key>bundleDisplayName</key>
    <string>App Name</string>
    <key>bundleShortVersionString</key>
    <string>1.5.1</string>
    <key>bundleVersion</key>
    <string>1.5.1</string>
    <key>copyright</key>
    <string>© 2015 Company, Inc.</string>
    <key>drmVersionNumber</key>
    <integer>0</integer>
    <key>fileExtension</key>
    <string>.app</string>
    <key>gameCenterEnabled</key>
    <false/>
    <key>gameCenterEverEnabled</key>
    <false/>
    <key>genre</key>
    <string>Games</string>
    <key>genreId</key>
    <integer>6014</integer>
    <key>itemName</key>
    <string>App Name</string>
    <key>kind</key>
    <string>software</string>
    <key>playlistArtistName</key>
    <string>Company, Inc.</string>
    <key>playlistName</key>
    <string>App Name</string>
    <key>releaseDate</key>
    <string>2015-11-18T03:23:10Z</string>
    <key>s</key>
    <integer>143441</integer>
    <key>softwareIconNeedsShine</key>
    <false/>
    <key>softwareSupportedDeviceIds</key>
    <array>
        <integer>9</integer>
    </array>
    <key>softwareVersionBundleId</key>
    <string>com.company.appid</string>
    <key>subgenres</key>
    <array>
        <dict>
            <key>genre</key>
            <string>Puzzle</string>
            <key>genreId</key>
            <integer>7012</integer>
        </dict>
        <dict>
            <key>genre</key>
            <string>Word</string>
            <key>genreId</key>
            <integer>7019</integer>
        </dict>
    </array>
    <key>versionRestrictions</key>
    <integer>16843008</integer>
</dict>
</plist>

```

Les valeurs des clés individuelles sont détaillées ci-dessous.

### <a name="uirequireddevicecapabilities"></a>UIRequiredDeviceCapabilities

La clé `UIRequiredDeviceCapabilities` permet à iTunes de connaître les fonctionnalités spécifiques qu’une application iOS doit avoir pour pouvoir être installée sur un appareil iOS donné. Elle est fournie sous la forme d’un dictionnaire (`<dict>...</dict>`) de fonctionnalités (`<key>...</key>`) et d’une valeur booléenne pour chaque fonctionnalité. Si la valeur d’une fonctionnalité est `true`, cette fonctionnalité doit être présente. Si la valeur est `false`, la fonctionnalité ne doit pas être présente sur l’appareil. Par exemple :

```xml
<key>UIRequiredDeviceCapabilities</key>
<dict>
    <key>armv7</key>
    <true/>
    <key>front-facing-camera</key>
    <true/>
</dict>
```

Spécifie que l’appareil iOS doit prendre en charge le jeu d’instructions ARM7 et disposer d’une caméra frontale pour que l’application puisse être installée. Pour obtenir la liste complète des valeurs autorisées, consultez la documentation d’Apple relative à [UIRequiredDeviceCapabilities](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW3).

### <a name="artistname-and-playlistartistname"></a>artistName et playlistArtistName

Utilisez les clés `artistName` et `playlistArtistName` pour définir le nom de l’entreprise qui a créé l’application iOS à afficher dans iTunes. Exemple :

```xml
<key>artistName</key>
<string>Company, Inc.</string>
...
<key>playlistArtistName</key>
<string>Company, Inc.</string>
```

### <a name="bundledisplayname-itemname-and-playlistname"></a>bundleDisplayName, itemName et playlistName

Utilisez les clés `bundleDisplayName`, `itemName` et `playlistName` pour définir le nom de l’application iOS à afficher dans iTunes. Exemple :

```xml
<key>bundleDisplayName</key>
<string>App Name</string>
...
<key>itemName</key>
<string>App Name</string>
...
<key>playlistName</key>
<string>App Name</string>
```

### <a name="bundleshortversionstring-and-bundleversion"></a>bundleShortVersionString et bundleVersion

Utilisez les clés `bundleShortVersionString` et `bundleVersion` pour définir le numéro de version de l’application iOS à afficher dans iTunes. Exemple :

```xml
<key>bundleShortVersionString</key>
<string>1.5.1</string>
<key>bundleVersion</key>
<string>1.5.1</string>
```

### <a name="softwareversionbundleid"></a>softwareVersionBundleId

Utilisez la clé `softwareVersionBundleId` pour spécifier l’ID de bundle de l’application iOS. Exemple :

```xml
<key>softwareVersionBundleId</key>
<string>com.company.appid</string>
```

### <a name="copyright"></a>copyright

Utilisez la clé `copyright` pour définir le copyright affiché dans iTunes. Exemple :

```xml
<key>copyright</key>
<string>© 2015 Company, Inc.</string>
```

### <a name="releasedate"></a>releaseDate

Utilisez la clé `releaseDate` pour indiquer la date de sortie de l’application iOS à afficher dans iTunes. Exemple :

```xml
<key>releaseDate</key>
<string>2015-11-18T03:23:10Z</string>
```

### <a name="softwareiconneedsshine"></a>softwareIconNeedsShine

Utilisez la clé `softwareIconNeedsShine` pour indiquer à iTunes si l’icône de l’application iOS nécessite une _mise en surbrillance_ pour iOS 6 (et les versions antérieures). Exemple :

```xml
<key>softwareIconNeedsShine</key>
<false/>
```

### <a name="gamecenterenabled-and-gamecentereverenabled"></a>gameCenterEnabled et gameCenterEverEnabled

Utilisez les clés `gameCenterEnabled` et `gameCenterEverEnabled` pour indiquer à iTunes si l’application iOS prend en charge le Game Center d’Apple. Exemple :

```xml
<key>gameCenterEnabled</key>
<false/>
<key>gameCenterEverEnabled</key>
<false/>
```

### <a name="genre-genreid-and-subgenres"></a>genre, genreId et subgenres

Utilisez les clés `genre` et `genreId` pour indiquer à iTunes le genre auquel appartient l’application iOS. Exemple :

```xml
<key>genre</key>
<string>Games</string>
<key>genreId</key>
<integer>6014</integer>
```

Éventuellement, vous pouvez utiliser la clé `subgenres` afin de préciser deux sous-genres au maximum pour l’application iOS. Exemple :

```xml
<key>subgenres</key>
<array>
    <dict>
        <key>genre</key>
        <string>Puzzle</string>
        <key>genreId</key>
        <integer>7012</integer>
    </dict>
    <dict>
        <key>genre</key>
        <string>Word</string>
        <key>genreId</key>
        <integer>7019</integer>
    </dict>
</array>
```

Pour les applications iOS, Apple définit les genres et ID de genres suivants :

[!include[](~/ios/includes/table-appstore.md)]

### <a name="softwaresupporteddeviceids"></a>softwareSupportedDeviceIds

Utilisez la clé `softwareSupportedDeviceIds` pour indiquer à iTunes les appareils iOS pris en charge par l’application iOS. Exemple :

```xml
<key>softwareSupportedDeviceIds</key>
<array>
    <integer>9</integer>
</array>
```

Où les valeurs suivantes sont possibles :

- 1 - iPhones classiques
- 2 - iPod touch
- 4 - iPad
- 9 - iPhones modernes

### <a name="standard-keys"></a>Clés standard

Les clés suivantes sont incluses dans tous les fichiers `iTunesMetadata.plist` des applications iOS et ont toujours les mêmes valeurs :

```xml
<key>drmVersionNumber</key>
<integer>0</integer>
<key>fileExtension</key>
<string>.app</string>
...
<key>kind</key>
<string>software</string>
...
<key>s</key>
<integer>143441</integer>
...
<key>versionRestrictions</key>
<integer>16843008</integer>
```

<a name="iTunesMetadata_creating" />

## <a name="creating-an-itunesmetadataplist-file"></a>Création d’un fichier iTunesMetadata.plist

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

 Quand vous utilisez un fichier `iTunesMetadata.plist` dans Visual Studio pour Mac, vous avez deux options :

- Créer et tenir à jour le fichier à l’aide de l’éditeur Visual Plist de Visual Studio pour Mac
- Créer et tenir à jour le fichier dans un éditeur de texte brut

 Les deux options sont traitées en détail ci-dessous.

### <a name="using-the-visual-plist-editor"></a>Utilisation de l’éditeur Visual Plist

Effectuez les actions suivantes :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le fichier projet Xamarin.iOS, puis sélectionnez **Ajouter** > **Nouveau fichier...**
2. Du nouveau dialogue de fichier, sélectionnez la**liste des biens** **iOS** > :

    ![](itunesmetadata-images/image01.png "Select iOS Property List")
3. Entrez `iTunesMetadata` comme **Nom**, puis cliquez sur le bouton **Nouveau**.
4. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `iTunesMetadata.plist` pour l’ouvrir et le modifier :

    ![](itunesmetadata-images/image02.png "The iTunesMetadata.plist editor")
5. Cliquez sur **+** le vert pour `UIRequiredDeviceCapabilities` créer une nouvelle entrée et entrez comme nom clé:

    ![](itunesmetadata-images/image03.png "Create a new entry and enter UIRequiredDeviceCapabilities as the key name")
6. Cliquez sur le type valeur **Chaîne**, puis sélectionnez **Dictionnaire** dans la liste déroulante :

    ![](itunesmetadata-images/image04.png "Select Dictionary from the popup list")
7. Cliquez sur la flèche vers le bas située à gauche du nom de la propriété pour afficher les entrées du dictionnaire :

    ![](itunesmetadata-images/image05.png "Reveal the dictionary entries")
8. Cliquez sur le texte **Ajouter une nouvelle entrée**, puis sur le **+** de couleur verte pour ajouter une entrée au dictionnaire :

    ![](itunesmetadata-images/image06.png "Add an entry to the dictionary")
9. Entrez le nom de clé `armv7`, sélectionnez le type **Booléen**, puis entrez la valeur **Oui** :

    ![](itunesmetadata-images/image07.png "Enter armv7 for the key name, select a type of Boolean and enter Yes as the value")
10. Répétez les étapes ci-dessus jusqu’à ce que vous ayez rempli le fichier `iTunesMetadata.plist` avec toutes les paires clé/valeur nécessaires (pour plus d’informations, consultez la section [Contenu d’iTunesMetadata.plist](#iTunesMetadata_contents) ci-dessus).

11. Enregistrez les changements dans le fichier plist.

### <a name="using-a-plain-text-editor"></a>Utilisation d’un éditeur de texte brut

Effectuez les actions suivantes :

1. Dans un éditeur de texte brut, créez un fichier texte et nommez-le `iTunesMetadata.plist`.
2. Copiez l’exemple de contenu de la section [Contenu d’iTunesMetadata.plist](#iTunesMetadata_contents) ci-dessus.
3. Collez le contenu dans le fichier, puis modifiez-le en fonction de vos besoins.
4. Enregistrez le fichier, puis retournez à Visual Studio pour Mac.
5. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le fichier projet Xamarin.iOS, puis sélectionnez **Ajouter** > **Fichiers existants...**.
6. Dans la boîte de dialogue Ouvrir un fichier, sélectionnez le fichier `iTunesMetadata.plist` créé ci-dessus, puis cliquez sur le bouton **OK**.
7. Laissez à l’option **Action de génération** de ce fichier la valeur **Aucune**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Le plugin Xamarin pour Visual Studio ne prend en charge qu’un éditeur visuel pour les fichiers `Info.plist` et `Entitlement.plist`, vous devez donc créer votre fichier `iTunesMetadata.plist` dans un éditeur de texte standard et l’inclure manuellement dans votre projet Xamarin.iOS.

Effectuez les actions suivantes :

1. Dans un éditeur de texte brut, créez un fichier texte et nommez-le `iTunesMetadata.plist`.
2. Copiez l’exemple de contenu de la section [Contenu d’iTunesMetadata.plist](#iTunesMetadata_contents) ci-dessus.
3. Collez le contenu dans le fichier, puis modifiez-le en fonction de vos besoins.
4. Enregistrez le fichier, puis retournez à Visual Studio.
5. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le fichier projet Xamarin.iOS, puis sélectionnez **Ajouter** > **Fichiers existants...**.
6. Dans la boîte de dialogue Ouvrir un fichier, sélectionnez le fichier `iTunesMetadata.plist` créé ci-dessus, puis cliquez sur le bouton **Ouvrir**.
7. Laissez à l’option **Action de génération** de ce fichier la valeur **Aucune**.

-----

Plus tard, vous devez sélectionner ce fichier `iTunesMetadata.plist` quand vous vous préparez à créer votre fichier IPA dans l’IDE.

## <a name="summary"></a>Récapitulatif

Cet article a présenté le fichier `iTunesMetadata.plist`, qui permet d’informer iTunes sur une application iOS faisant l’objet d’une distribution ad hoc. Il a décrit les clés standard du fichier plist. Il a ensuite expliqué comment créer et tenir à jour ce fichier dans Visual Studio et Visual Studio pour Mac.

## <a name="related-links"></a>Liens connexes

- [Distribution De l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configurer une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publication dans l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribution en interne](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Soutien de l’API](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
