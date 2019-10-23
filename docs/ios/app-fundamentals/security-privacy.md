---
title: Fonctionnalités de sécurité et de confidentialité iOS
description: Ce document décrit les fonctionnalités de sécurité et de confidentialité d’iOS et explique comment les utiliser avec Xamarin. iOS. Il examine les mises à jour apportées dans iOS 10 et explique comment accéder aux données utilisateur privées.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 7847148551c20dbcf49bcc263bdc50716a6ef14e
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70283173"
---
# <a name="ios-security-and-privacy-features"></a>Fonctionnalités de sécurité et de confidentialité iOS

_Cet article traite de l’utilisation de la sécurité et de la confidentialité dans iOS, ainsi que de la façon dont ils affectent une application Xamarin. iOS._

Apple a apporté plusieurs améliorations à la sécurité et à la confidentialité dans iOS 10 (et versions ultérieures), ce qui permet au développeur d’améliorer la sécurité de ses applications et de garantir la confidentialité de l’utilisateur final. Cet article aborde l’implémentation de ces fonctionnalités dans une application Xamarin. iOS.

<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Améliorations générales

Les modifications générales suivantes ont été apportées à la sécurité et à la confidentialité dans iOS 10 :

- L’API CDSA (Common Data Security Architecture) est dépréciée et doit être remplacée par l’API SecKey pour générer des clés asymétriques.
- La nouvelle clé de `NSAllowsArbitraryLoadsInWebContent` peut être ajoutée au fichier **info. plist** d’une application et autorisera le chargement correct des pages Web, alors que la protection d’Apple transport Security (ATS) est toujours activée pour le reste de l’application. Pour plus d’informations, consultez notre documentation sur la [sécurité des applications de transport](~/ios/app-fundamentals/ats.md) .
- Étant donné que le nouveau presse-papiers dans iOS 10 et macOS Sierra permet à l’utilisateur de copier et coller entre les appareils, l’API a été développée pour permettre à un presse-papiers d’être limité à un appareil spécifique et être mis en cache pour être effacé automatiquement à un point donné. En outre, les pasteboards nommés ne sont plus persistants et doivent être remplacés par les conteneurs de collage partagés.
- Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est maintenant désactivé par défaut. En outre, l’API de transport sécurisé ne prend plus en charge SSLv3 et il est recommandé que le développeur cesse d’utiliser le chiffrement SHA-1 et 3DES le plus rapidement possible.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Accès aux données de l’utilisateur privé

Les applications qui s’exécutent sur iOS 10 (ou version ultérieure) doivent déclarer de manière statique leur intention d’accéder à des fonctionnalités ou des informations utilisateur spécifiques en entrant une ou plusieurs clés de confidentialité dans leurs fichiers **info. plist** qui expliquent à l’utilisateur pourquoi l’application souhaite obtenir un accès.

> [!IMPORTANT]
> Les applications qui ne fournissent pas les clés requises sont arrêtées silencieusement par le système lorsqu’elles essaient d’accéder à l’une des fonctionnalités restreintes ou aux informations utilisateur, _sans erreur_. Si une application échoue de façon inattendue sur iOS 10, vérifiez que toutes les **informations requises. plist** ont été spécifiées.

Les clés associées à la confidentialité suivantes sont disponibles :

- **Confidentialité-Description de l’utilisation de Apple Music** (`NSAppleMusicUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à la bibliothèque multimédia de l’utilisateur.
- **Confidentialité-Description de l’utilisation du périphérique Bluetooth** (`NSBluetoothPeripheralUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à Bluetooth sur l’appareil de l’utilisateur.
- **Confidentialité-Description de l’utilisation des calendriers** (`NSCalendarsUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder au calendrier de l’utilisateur.
- **Confidentialité-Description de l’utilisation de l’appareil photo** (`NSCameraUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à l’appareil photo de l’appareil.
- **Confidentialité-Description de l’utilisation des contacts** (`NSContactsUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder aux contacts de l’utilisateur.
- **Confidentialité-Description de l’utilisation du partage d’intégrité** (`NSHealthShareUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder aux données d’intégrité de l’utilisateur. Pour plus d’informations, consultez Référence de la [classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)d’Apple.
- **Confidentialité-Description de l’utilisation de la mise à jour de l’intégrité** (`NSHealthUpdateUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite modifier les données d’intégrité de l’utilisateur. Pour plus d’informations, consultez Référence de la [classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)d’Apple.
- **Confidentialité-Description de l’utilisation de HomeKit** (`NSHomeKitUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder aux données de configuration HomeKit de l’utilisateur.
- **Confidentialité-Description de l’utilisation toujours** de l’emplacement (`NSLocationAlwaysUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite toujours avoir accès à l’emplacement de l’utilisateur.
- Déconseillé **Confidentialité-Description de l’utilisation** de l’emplacement (`NSLocationUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à l’emplacement de l’utilisateur. *Remarque : cette clé a été dépréciée dans iOS 8 (et versions ultérieures). Utilisez à la place `NSLocationAlwaysUsageDescription` ou `NSLocationWhenInUseUsageDescription`.*
- **Confidentialité-emplacement de la description de l’utilisation en cours d’utilisation** (`NSLocationWhenInUseUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à l’emplacement de l’utilisateur pendant son exécution.
- Déconseillé **Confidentialité-Description de l’utilisation de la bibliothèque multimédia** : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à la bibliothèque multimédia de l’utilisateur. *Remarque : cette clé a été dépréciée dans iOS 8 (et versions ultérieures). Utilisez à la place `NSAppleMusicUsageDescription`.*
- **Confidentialité-Description de l’utilisation du microphone** (`NSMicrophoneUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder au microphone des appareils.
- **Confidentialité-Description de l’utilisation du mouvement** (`NSMotionUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à l’accéléromètre de l’appareil.
- **Confidentialité-Description de l’utilisation de la bibliothèque photo** (`NSPhotoLibraryUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder à la bibliothèque de photos de l’utilisateur.
- **Confidentialité-Description de l’utilisation des rappels** (`NSRemindersUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder aux rappels de l’utilisateur.
- **Confidentialité-Description de l’utilisation de Siri** (`NSSiriUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite envoyer des données utilisateur à Siri.
- **Confidentialité-Description de l’utilisation de la reconnaissance vocale** (`NSSpeechRecognitionUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite envoyer des données utilisateur aux serveurs de reconnaissance vocale d’Apple.
- **Confidentialité-Description de l’utilisation du fournisseur TV** (`NSVideoSubscriberAccountUsageDescription`) : permet au développeur de décrire la raison pour laquelle l’application souhaite accéder au compte du fournisseur TV de l’utilisateur.

Pour plus d’informations sur l’utilisation des clés **info. plist** , consultez [référence des clés de liste de propriétés d’informations](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)d’Apple.

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Définition des clés de confidentialité

Prenons l’exemple suivant d’accès à HomeKit sur iOS 10 (et versions ultérieures), le développeur doit ajouter la clé de `NSHomeKitUsageDescription` au fichier **info. plist** de l’application et fournir une chaîne qui déclare pourquoi l’application souhaite accéder à la base de données HomeKit de l’utilisateur. Cette chaîne est présentée à l’utilisateur la première fois qu’il exécute l’application :

![Exemple d’alerte NSHomeKitUsageDescription](security-privacy-images/info01.png "Exemple d’alerte NSHomeKitUsageDescription")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin. iOS pour Visual Studio ne prend pas actuellement en charge la modification des clés de confidentialité **info. plist** dans l’éditeur de manifeste iOS par défaut. Au lieu de cela, vous devrez utiliser l’éditeur de plist générique. procédez comme suit :

1. Cliquez avec le bouton droit sur le fichier **info. plist** dans le **Explorateur de solutions** , puis sélectionnez **Ouvrir avec...** .
2. Sélectionnez l' **éditeur plist générique** dans la liste des programmes pour ouvrir le fichier, puis cliquez sur **OK**.

    ![Sélectionner l’éditeur de plist générique](security-privacy-images/InfoEditorSelectionVs.png "Sélectionner l’éditeur de plist générique")
3. Cliquez sur le bouton **+** sur la dernière ligne de l’éditeur pour ajouter une nouvelle entrée à la liste. Ce sera appelé « propriété personnalisée », avec le type défini sur `String` et une valeur vide.
4. Cliquez sur le nom de la propriété et une liste déroulante s’affiche.
5. Dans la liste déroulante, sélectionnez une clé de confidentialité (par exemple, **confidentialité-Description de l’utilisation de HomeKit**) : 

    ![Sélectionner une clé de confidentialité](security-privacy-images/InfoPListEditorSelectKey.png "Sélectionner une clé de confidentialité")
6. Entrez une description dans la colonne valeur pour la raison pour laquelle l’application souhaite accéder aux informations sur la fonctionnalité donnée ou l’utilisateur : 

    ![Entrer une description](security-privacy-images/InfoPListSetValue.png "Entrez une description")
7. Enregistrez les modifications dans le fichier.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour définir l’une des clés de confidentialité, procédez comme suit :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Info.plist** pour l’ouvrir et le modifier.
2. En bas de l’écran, basculez en mode **source** .
3. Ajoutez une nouvelle **entrée** à la liste.
4. Dans la liste déroulante, sélectionnez une clé de confidentialité (par exemple, **confidentialité-Description de l’utilisation de HomeKit**) : 

    ![Sélectionner une clé de confidentialité](security-privacy-images/info02.png "Sélectionner une clé de confidentialité")
5. Entrez une description de la raison pour laquelle l’application souhaite accéder aux informations sur la fonctionnalité donnée ou l’utilisateur : 

    ![Entrer une description](security-privacy-images/info03.png "Entrez une description")
6. Enregistrez les modifications dans le fichier.

-----

> [!IMPORTANT]
> Dans l’exemple ci-dessus, l’échec de définition de la clé de `NSHomeKitUsageDescription` dans le fichier **info. plist** entraînerait l’échec _silencieux_ de l’application (en cours de fermeture par le système lors de l’exécution) sans erreur lorsqu’elle est exécutée dans iOS 10 (ou version ultérieure).

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé les modifications de sécurité et de confidentialité apportées par Apple dans iOS 10 et comment elles affectent une application Xamarin. iOS.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
