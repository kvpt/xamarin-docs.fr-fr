---
title: Introduction à macOS Sierra
description: Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans macOS Sierra pour les développeurs Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 4dde8941b09ac7b235b94e73e86dc60167fb6bd4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574455"
---
# <a name="introduction-to-macos-sierra"></a>Introduction à macOS Sierra

Avec la nouvelle macOS Sierra, le développeur peut tirer parti des nouvelles API qui permettent à l’utilisateur final d’interagir avec ses applications et sites Web de manière autrefois indisponible. Par exemple, Apple permet désormais aux sites Web de proposer aux clients la possibilité de payer en toute sécurité via Apple Pay et les améliorations apportées à l’infrastructure métallique renforcent le potentiel des graphiques et du calcul de l’application. 

Pour plus d’informations sur macOS Sierra, consultez la documentation sur [MacOS + Apps](https://developer.apple.com/macos/) d’Apple.

<a name="Whats-New-in-macOS-Sierra"></a>

## <a name="whats-new-in-macos-sierra"></a>Nouveautés de macOS Sierra

Apple a ajouté plusieurs nouvelles API et services dans macOS Sierra, ainsi que de nombreuses améliorations apportées aux fonctionnalités existantes, notamment :

<a name="Apple-File-System"></a>

### <a name="apple-file-system"></a>Système de fichiers Apple

Avec macOS Sierra, Apple a publié le nouveau système de fichiers Apple comme système de fichiers moderne pour iOS, macOS, tvOS et Watchos. Le système de fichiers Apple a été optimisé pour le stockage flash et SSD et fournit les fonctionnalités suivantes : chiffrement renforcé, métadonnées de copie sur écriture, partage d’espace, clonage pour les fichiers et répertoires, instantanés, dimensionnement rapide d’annuaire et primitives d’enregistrement atomiques sécurisées.

Pour plus d’informations, consultez le [Guide du système de fichiers Apple](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)d’Apple.

<a name="Apple-Pay-Enhancements"></a>

### <a name="apple-pay-enhancements"></a>Améliorations apportées à Apple Pay

Apple a apporté plusieurs améliorations à Apple Pay dans macOS Sierra qui permettent à l’utilisateur d’effectuer des paiements sécurisés à partir de sites Web.

Avec macOS Sierra, plusieurs nouvelles API ont été ajoutées qui fonctionnent avec macOS Sierra, iOS et Watchos pour prendre en charge les réseaux de paiement dynamiques et un nouvel environnement de test sandbox.

macOS Sierra inclut la nouvelle infrastructure JavaScript ApplePay qui permet au développeur d’intégrer des Apple Pay directement dans des sites Web basés sur iOS et macOS. Pour les sites Web qui prennent en charge Apple Pay, l’utilisateur peut autoriser le paiement à l’aide de son iPhone ou Apple Watch.

Pour plus d’informations, consultez la référence du [Framework APPLEPAY js](https://developer.apple.com/reference/applepayjs) d’Apple.

<a name="Building-Modern-macOS-Apps"></a>

### <a name="building-modern-macos-apps"></a>Création d’applications macOS modernes

Les applications macOS modernes, telles que le navigateur Web Safari d’Apple, le processeur de texte de pages et les numéros, utilisent de nombreuses nouvelles technologies pour présenter une interface utilisateur unifiée et sensible au contexte qui s’éloigne avec les éléments d’interface utilisateur traditionnels tels que les panneaux flottants et les fenêtres ouvertes multiples.

[![Exemple de fenêtre Mac avec onglets](images/content08.png)](images/content08.png#lightbox)

Notre guide de [création d’applications MacOS modernes](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) couvre plusieurs conseils, fonctionnalités et techniques que les développeurs peuvent utiliser pour créer une application MacOS moderne dans Xamarin. Mac.

<a name="CloudKit-Data-Sharing"></a>

### <a name="cloudkit-data-sharing"></a>Partage de données CloudKit

L’infrastructure CloudKit a été développée en macOS Sierra pour permettre aux utilisateurs de partager rapidement et facilement des enregistrements ou des jeux d’enregistrements à partir de leurs bases de données iCloud privées.

CloudKit fournit une interface utilisateur complète pour envoyer et accepter des invitations à des enregistrements partagés et l’utilisateur dispose d’un contrôle complet en lecture/écriture sur les personnes qui ont accès aux enregistrements.

Pour plus d’informations, consultez Référence du [Framework CloudKit](https://developer.apple.com/reference/clockkit) d’Apple et [Référence du Framework CloudKit js](https://developer.apple.com/reference/cloudkitjs).

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

<a name="Safari-App-Extensions-Support"></a>

### <a name="safari-app-extensions-support"></a>Prise en charge des extensions d’application Safari

Les extensions d’application Safari permettent à l’application d’étendre le comportement du navigateur Web Safari tout en étant étroitement intégré avec macOS Sierra. Étant donné que les extensions d’application macOS Safari fonctionnent comme les extensions d’application Safari Safari, elles sont faciles à porter d’un système à un autre.

Pour plus d’informations, consultez le Guide de programmation de l' [extension d’application Safari](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)d’Apple.

<a name="Security-and-Privacy-Enhancements"></a>

### <a name="security-and-privacy-enhancements"></a>Améliorations en matière de sécurité et de confidentialité

Apple a apporté plusieurs améliorations à la sécurité et à la confidentialité dans macOS Sierra, ce qui permet à l’application d’améliorer la sécurité de l’application et de garantir la confidentialité de l’utilisateur final, y compris les éléments suivants :

- La nouvelle `NSAllowsArbitraryLoadsInWebContent` clé peut être ajoutée au fichier de l’application `Info.plist` et autoriser le chargement correct des pages Web, tandis que la protection d’Apple transport Security (ATS) est toujours activée pour le reste de l’application.
- L’API CDSA (Common Data Security Architecture) est dépréciée et doit être remplacée par l’API SecKey pour générer des clés asymétriques.
- Pour toutes les connexions SSL/TLS, le chiffrement symétrique RC4 est maintenant désactivé par défaut. En outre, l’API de transport sécurisé ne prend plus en charge SSLv3 et il est recommandé que l’application cesse d’utiliser le chiffrement SHA-1 et 3DES le plus rapidement possible.
- Étant donné que le nouveau presse-papiers dans iOS 10 et macOS Sierra permet à l’utilisateur de copier et coller entre les appareils, l’API a été développée pour permettre à un presse-papiers d’être limité à un appareil spécifique et être mis en cache pour être effacé automatiquement à un point donné. En outre, les pasteboards nommés ne sont plus persistants et doivent être remplacés par les conteneurs de collage partagés.
- Si l’application accède à des données protégées (telles que le calendrier de l’utilisateur), elle _doit_ déclarer cette intention avec la clé de valeur de chaîne d’objectif correcte dans son `Info.plist` fichier ( `NSCalendarUsageDescription` dans le cas du calendrier).
- Les applications signées par le développeur qui ne sont pas fournies via le Mac App Store peuvent désormais tirer parti de CloudKit, du trousseau iCloud, du lecteur iCloud, des notifications push à distance, des MapKit et des droits VPN.
- macOS Sierra ne prend plus en charge la diffusion de code ou de données externes avec l’application de signataire de code dans son archive zip ou l’image de disque non signée, car le chemin d’accès au moment de l’exécution n’est pas connu avant le Runtime.

En outre, les applications qui s’exécutent sur macOS Sierra (ou version ultérieure) doivent déclarer de manière statique leur intention d’accéder à des fonctionnalités ou des informations utilisateur spécifiques en entrant une ou plusieurs clés spécifiques à la confidentialité dans leurs `Info.plist` fichiers qui expliquent à l’utilisateur pourquoi l’application souhaite obtenir un accès.

Étant donné que macOS Sierra partage ces modifications avec iOS 10, consultez notre guide d’amélioration de la [sécurité et](~/ios/app-fundamentals/security-privacy.md) de la confidentialité d’iOS 10 pour plus d’informations.

<a name="Smart-Card-Driver-Extension-Support"></a>

### <a name="smart-card-driver-extension-support"></a>Prise en charge de l’extension du pilote de carte à puce

Avec macOS Sierra, l’application peut créer des `NSExtension` pilotes de carte à puce basés sur des cartes à puce, qui autorisent l’accès en lecture seule au contenu à partir de certains types de cartes à puce. Ces informations sont ensuite présentées dans le trousseau du système (en remplaçant la méthode Common Data Security architecture déconseillée).

Pour plus d’informations, consultez le Guide de référence de l' [infrastructure CryptoTokenKit](https://developer.apple.com/reference/cryptotokenkit)d’Apple.

<a name="Unified-Logging"></a>

### <a name="unified-logging"></a>Journalisation unifiée

La journalisation unifiée fournit à l’application une seule API pour une messagerie efficace sur tous les niveaux du système. Avec la journalisation unifiée, l’application dispose d’un contrôle affiné sur plusieurs niveaux de journalisation qui incluent des contrôles de confidentialité et le suivi des activités pour faciliter le débogage. 

La journalisation assure la corrélation automatique des messages lorsque le suivi et la journalisation des activités sont utilisés ensemble.

macOS Sierra inclut une nouvelle application console (dans Applications/Utilitaires) qui est capable d’afficher les données de journal provenant de plusieurs sources, y compris les appareils connectés. Il prend également en charge les recherches par jetons et enregistrées, et affiche les connexions entre les messages connexes dans plusieurs processus.

En outre, les messages de journal peuvent être affichés et gérés à l’aide des outils en ligne de commande.

Pour plus d’informations, consultez les informations de référence sur la [journalisation](https://developer.apple.com/documentation/os/logging)d’Apple.

<a name="Wide-Color"></a>

### <a name="wide-color"></a>Couleur à spectre large

macOS Sierra étend la prise en charge des formats de pixel étendus et des espaces de couleurs à grande échelle dans le système, y compris les infrastructures telles que Core Graphics, Core image, Metal et AVFoundation. La prise en charge des appareils avec des affichages de couleurs larges est encore plus facilitée en fournissant ce comportement dans l’ensemble de la pile graphique.

En outre, `AppKit` a été modifié pour fonctionner dans le nouveau colorspace **sRVB** étendu, ce qui facilite le mixage des couleurs dans des gammes de couleurs larges sans perte de performances significative.

Apple offre les meilleures pratiques suivantes lorsque vous travaillez avec des couleurs larges :

- `NSColor`utilise à présent l’espace de couleurs sRVB et ne pincera plus les valeurs à la `0.0` `1.0` plage à. Si l’application s’appuie sur le comportement de verrouillage précédent, elle doit être modifiée pour macOS Sierra.
- Lorsque vous utilisez une API de bas niveau, telle que des graphiques de base ou du métal, pour fournir un traitement d’image, l’application doit utiliser un espace de couleurs de plage étendue et un format de pixel qui prend en charge les valeurs à virgule flottante 16 bits. Le cas échéant, l’application devra fixer manuellement les valeurs des composants de couleur.
- Core Graphics, Core image et Metal performance Nuancers fournissent de nouvelles méthodes pour la conversion entre les deux espaces de couleurs.

Pour en savoir plus, consultez notre guide [de présentation des couleurs larges](~/ios/platform/wide-color.md) .

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>Modifications supplémentaires de l’infrastructure

Outre les principales modifications apportées à l’infrastructure et les ajouts ci-dessus, Apple a apporté de nombreuses modifications d’infrastructure mineures supplémentaires dans macOS Sierra.

Pour en savoir plus, consultez notre guide [supplémentaire sur les modifications](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) de l’infrastructure.

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>API déconseillées

Les API suivantes sont dépréciées dans macOS Sierra :

- Le système de fichiers standard HFS n’est plus pris en charge.

Pour obtenir la liste complète des désapprobations et des modifications, consultez la documentation relative aux différences de l' [API MacOS v 10.12](https://developer.apple.com/library/archive/releasenotes/General/APIDiffsMacOS10_12/index.html) d’Apple.

## <a name="related-links"></a>Liens connexes

- [Exemples Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Nouveautés de macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
