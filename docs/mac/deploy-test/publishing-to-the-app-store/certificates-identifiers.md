---
title: Certificats et identificateurs dans Xamarin.Mac
description: Ce guide présente en détail la création des certificats et des identificateurs nécessaires qui seront exigés pour publier une application Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 30731dda1007127caff84eae20696b45803b74f1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528994"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificats et identificateurs dans Xamarin.Mac

_Ce guide présente en détail la création des certificats et des identificateurs nécessaires exigés pour publier une application Xamarin.Mac._

## <a name="certificates-and-identifiers"></a>Certificats et identificateurs

Visitez l’[Apple Developer Member Center](https://developer.apple.com) pour configurer le Mac pour le développement. Le menu principal est affiché ci-dessous :

[![Apple Developer Member Center](certificates-identifiers-images/devcenter01.png "Apple Developer Member Center")](certificates-identifiers-images/devcenter01-large.png#lightbox)

Cliquez sur le lien **Certificates, Identifiers & Profiles** (Certificats, identificateurs et profils) :

[![Sélection de certificats, d’identificateurs et de profils](certificates-identifiers-images/devcenter02.png "Sélection de certificats, d’identificateurs et de profils")](certificates-identifiers-images/devcenter02-large.png#lightbox)

Cliquez ensuite sur le **lien Certificates** sous la section **Mac Apps** :

[![Sélection du lien des certificats](certificates-identifiers-images/devcenter03.png "Sélection du lien des certificats")](certificates-identifiers-images/devcenter03-large.png#lightbox)

Cliquez sur le lien **All**, puis sur le bouton  **+**  :

[![Sélection de tout et ajout d’un nouvel élément](certificates-identifiers-images/certif01.png "Sélection de tout et ajout d’un nouvel élément")](certificates-identifiers-images/certif01-large.png#lightbox)

À partir de là, téléchargez les **certificats intermédiaires** (Autorité de certification des relations des développeurs dans le monde entier et Autorité de certification d’ID de développeur), si nécessaire. Toutefois, ceux-ci doivent être automatiquement configurés par Xcode pour le développeur.

Le reste de cette section décrit en détail chacune des quatre étapes à effectuer pour configurer un compte de développeur Mac.

- **Inscrire l’ID d’application Mac** : le développeur doit suivre ces étapes pour chaque application qu’il écrit.
- **Inscrire les systèmes macOS** : cette opération est obligatoire uniquement lors de l’ajout d’ordinateurs sur lesquels effectuer les tests.
- **Créer des certificats** : opération nécessaire une seule fois lors de la configuration des certificats, et ultérieurement lors de leur renouvellement.
- **Créer un profil de provisionnement** : le développeur doit suivre ces étapes pour chaque nouvelle application écrite et lors de l’ajout de nouveaux systèmes.

Cliquez sur le lien **Overview** situé en haut à gauche de la page pour revenir à ce menu à tout moment.

### <a name="register-mac-app-id"></a>Inscrire l’ID d’application Mac

Le développeur doit inscrire un ID d’application pour chaque application écrite. Utilisez les étapes ci-dessous pour créer une entrée pour un exemple d’application de base appelée « MacWriter ».

1. Entrez une valeur **App ID Description** et sélectionnez les **App Services** dont l’application a besoin : 

    [![Entrée de la description et des services d’application](certificates-identifiers-images/devcenter04.png "Entrée de la description et des services d’application")](certificates-identifiers-images/devcenter04-large.png#lightbox)
2. Entrez une valeur **Bundle ID** pour l’application, puis cliquez sur le bouton **Continue** : 

    [![Entrée d’un ID de bundle](certificates-identifiers-images/devcenter05.png "Entrée d’un ID de bundle")](certificates-identifiers-images/devcenter05-large.png#lightbox)
3. Vérifiez les informations, puis cliquez sur le bouton **Submit** : 

    [![Vérification des informations](certificates-identifiers-images/devcenter06.png "Vérification des informations")](certificates-identifiers-images/devcenter06-large.png#lightbox)

Certains **services d’application** peuvent nécessiter une configuration supplémentaire (par exemple, iCloud). Si tel est le cas, sélectionnez le nouvel ID d’application que vous venez de créer, puis cliquez sur le bouton **Modify** :

[![Modification de l’ID de la nouvelle application](certificates-identifiers-images/devcenter07.png "Modification de l’ID de la nouvelle application")](certificates-identifiers-images/devcenter07-large.png#lightbox)

Pour configurer les services iCloud, cliquez sur le bouton **Modify** :

[![Configuration des services iCloud](certificates-identifiers-images/devcenter08.png "Configuration des services iCloud")](certificates-identifiers-images/devcenter08-large.png#lightbox)

À partir de là, le développeur peut configurer les bases de données qui seront utilisées :

[![Configuration des bases de données](certificates-identifiers-images/devcenter09.png "Configuration des bases de données")](certificates-identifiers-images/devcenter09-large.png#lightbox)

### <a name="register-macos-systems"></a>Inscrire les systèmes macOS

Pour créer un profil de provisionnement à des fins de test, les ordinateurs Mac des développeurs doivent être inscrits. Ils peuvent inscrire au maximum 100 ordinateurs pour tester leurs applications Mac.

Dans le Mac Developer Center, sélectionnez **All** dans la section **Devices**, puis cliquez sur le bouton **+**  :

[![Ajout d’un nouvel ordinateur](certificates-identifiers-images/devcenter10.png "Ajout d’un nouvel ordinateur")](certificates-identifiers-images/devcenter10-large.png#lightbox)

Entrez un **nom** et l’**UUID** de l’ordinateur à ajouter, puis cliquez sur le bouton **Continue**. Examinez les informations, puis cliquez sur le bouton **Register** :

[![Entrée des informations du nouvel ordinateur](certificates-identifiers-images/devcenter11.png "Entrée des informations du nouvel ordinateur")](certificates-identifiers-images/devcenter11-large.png#lightbox)

### <a name="create-certificates"></a>Créer des certificats

La section Certificates permet de créer différents types de certificats qui seront utilisés pour signer les applications Mac :

[![Création d’un certificat](certificates-identifiers-images/certif01.png "Création d’un certificat")](certificates-identifiers-images/certif01-large.png#lightbox)

Il existe trois types principaux de certificats :

- **Certificat de développement Mac** : facultatif pour le développement d’application général, mais obligatoire si le développeur prévoit d’utiliser des fonctionnalités comme iCloud ou les notifications Push. Le développeur doit disposer d’un certificat de développement avant de créer des profils de provisionnement qui l’autorisent à accéder à ces fonctionnalités.
- **Mac App Store** : le développeur doit disposer d’un certificat pour son application et d’un autre certificat pour le programme d’installation.
- **ID de développeur** : certificats pour l’application et le programme d’installation si vous choisissez d’effectuer une distribution en dehors du Mac App Store.

Les sections suivantes fournissent les exemples de création de chacun des types de certificats mentionnés ci-dessus.

#### <a name="mac-development-certificate"></a>Certificat de développement Mac

Comme mentionné précédemment, le certificat de développement d’application Mac n’est nécessaire que si des fonctionnalités macOS comme iCloud ou les notifications Push sont utilisées.

Effectuez les étapes suivantes pour créer un certificat de développement :

1. Sélectionnez la case d’option **Mac Development**, puis cliquez sur **Continue** : 

     [![Ajout d’un certificat de développement](certificates-identifiers-images/certif02.png "Ajout d’un certificat de développement")](certificates-identifiers-images/certif02-large.png#lightbox)
2. L’écran suivant explique comment utiliser un accès au trousseau pour créer un fichier de demande de signature de certificat à charger : 

    [![L’écran de chargement de l’accès au trousseau](certificates-identifiers-images/certif03.png "L’écran de chargement de l’accès au trousseau")](certificates-identifiers-images/certif03-large.png#lightbox)
3. Choisissez un nom commun significatif pour le certificat, afin qu’il soit facilement reconnaissable ultérieurement une fois le certificat final créé. Rappelez-vous où le fichier est enregistré, afin de pouvoir le trouver à la prochaine étape : 

     ![Exportation d’un certificat](certificates-identifiers-images/image12.png "Exportation d’un certificat")
4. Un fichier de demande de certificat (extension `.certSigningRequest`) est enregistré localement sur le Mac. Rappelez-vous où il est enregistré (l’emplacement par défaut est le bureau), car il doit être choisi à la prochaine étape : 

     [![Chargement du fichier de certificat](certificates-identifiers-images/image13.png "Chargement du fichier de certificat")](certificates-identifiers-images/image13-large.png#lightbox)
5. Cliquez sur **Download** pour obtenir le certificat, puis double-cliquez dessus pour l’installer dans le **trousseau** : 

     [![Téléchargement d’un certificat de développement](certificates-identifiers-images/image15.png "Téléchargement d’un certificat de développement")](certificates-identifiers-images/image15-large.png#lightbox)
6. Cliquez sur **Download** pour obtenir le certificat, puis double-cliquez dessus pour l’installer dans le **trousseau**. L’utilitaire **Developer Certificate Utility** affiche le certificat comme suit : 

     [![Developer Certificate Utility](certificates-identifiers-images/image16.png "Developer Certificate Utility")](certificates-identifiers-images/image16-large.png#lightbox)
7. Il s’affiche également dans le **trousseau** comme suit : 

     ![Certificat dans l’accès au trousseau](certificates-identifiers-images/image17.png "Certificat dans l’accès au trousseau")

Comme mentionné précédemment, le certificat de développeur n’est pas toujours obligatoire, sauf si le développeur implémente des fonctionnalités macOS comme iCloud et les notifications Push. Il est également obligatoire pour créer un **profil de provisionnement de développement**, qui sera nécessaire pour tester les applications Mac App Store.

#### <a name="mac-app-store-certificates"></a>Certificats Mac App Store

Pour publier une application sur l’App Store, le certificat **Mac App Store** qui sera utilisé pour signer l’application et le paquet d’installation Mac doit être créé.

1. Sélectionnez **Mac App Store** comme type de certificat, puis cliquez sur le bouton **Continue** : 

    [![Création d’un certificat App Store](certificates-identifiers-images/certif04.png "Création d’un certificat App Store")](certificates-identifiers-images/certif04-large.png#lightbox)
2. Sélectionnez le type de certificat à créer (un de chaque type est nécessaire pour une publication sur l’App Store) : 

    [![Sélection du type de certificat](certificates-identifiers-images/certif05.png "Sélection du type de certificat")](certificates-identifiers-images/certif05-large.png#lightbox)
3. La page suivante explique comment utiliser l’**accès au trousseau** pour générer un fichier de demande de certificat. Suivez les instructions spécifiées : 

     [![Génération de la demande de trousseau](certificates-identifiers-images/certif06.png "Génération de la demande de trousseau")](certificates-identifiers-images/certif06-large.png#lightbox)
4. Choisissez un nom **Common Name** descriptif ; par exemple, utilisez le texte « Application de l’App Store » dans le nom : 

     ![Entrée d’un nom descriptif](certificates-identifiers-images/image20.png "Entrée d’un nom descriptif")
5. Un fichier de demande de certificat (extension `.certSigningRequest`) est enregistré localement sur le Mac. Rappelez-vous où il est enregistré (l’emplacement par défaut est le bureau) : 

     [![Enregistrement du certificat](certificates-identifiers-images/image21.png "Enregistrement du certificat")](certificates-identifiers-images/image21-large.png#lightbox)
6. Cliquez sur **Download** pour obtenir votre certificat, puis double-cliquez dessus pour l’installer dans le **trousseau** : 

      [![Téléchargement du certificat App Store](certificates-identifiers-images/image23.png "Téléchargement du certificat App Store")](certificates-identifiers-images/image23-large.png#lightbox)
7. Cliquez sur **Continue**, puis suivez exactement les mêmes étapes pour télécharger un autre certificat qui, cette fois, sera pour le *programme d’installation* : 

     [![Sélection du programme d’installation](certificates-identifiers-images/image24.png "Sélection du programme d’installation")](certificates-identifiers-images/image24-large.png#lightbox)
8. Choisissez un nom **Common Name** descriptif ; par exemple, utilisez le texte « Programme d’installation de l’App Store » dans le nom : 

     ![Définition du nom du certificat](certificates-identifiers-images/image25.png "Définition du nom du certificat")
9. Un fichier de demande de certificat (extension `.certSigningRequest`) est enregistré localement sur le Mac. Rappelez-vous où il est enregistré (l’emplacement par défaut est le bureau) : 

     [![Chargement du certificat](certificates-identifiers-images/image26.png "Chargement du certificat")](certificates-identifiers-images/image26-large.png#lightbox) 

     [![Téléchargement du certificat de distribution](certificates-identifiers-images/image28.png "Téléchargement du certificat de distribution")](certificates-identifiers-images/image28-large.png#lightbox)
10. Cliquez sur **Download** pour obtenir le certificat, puis double-cliquez dessus pour l’installer dans le **trousseau**. L’utilitaire Developer Certificate Utility affiche le certificat comme suit : 

     [![Developer Certificate Utility](certificates-identifiers-images/image29.png "Developer Certificate Utility")](certificates-identifiers-images/image29-large.png#lightbox)
11. Les deux nouveaux certificats sont maintenant visibles dans le **trousseau** : 

     [![Certificat dans l’accès au trousseau](certificates-identifiers-images/image30.png "Certificat dans l’accès au trousseau")](certificates-identifiers-images/image30-large.png#lightbox)

#### <a name="developer-id-certificates"></a>Certificats d’ID de développeur

Pour publier automatiquement une application Xamarin.Mac (et ne pas la publier par le biais de l’Apple App Store), le développeur doit disposer d’un certificat d’ID de développeur pour signer l’application pour sa publication et sont installation.

Effectuez les étapes suivantes :

1. Dans la section **Certificates**, commencez par cliquer sur le bouton **+** , puis sélectionnez la case d’option **Developer ID** : 

    [![Ajout d’un ID de développeur](certificates-identifiers-images/certif07.png "Ajout d’un ID de développeur")](certificates-identifiers-images/certif07-large.png#lightbox)
2. Cliquez sur le bouton **Continue**, puis sélectionnez le type d’ID de développeur à créer : 

    [![Sélection du type d’ID de développeur](certificates-identifiers-images/certif08.png "Sélection du type d’ID de développeur")](certificates-identifiers-images/certif08-large.png#lightbox)
3. Il en faudra deux : un pour signer l’application elle-même et un autre pour signer le programme d’installation de l’application. Soyez prudent quand vous nommez les demandes de certificat pour ces clés : utilisez des noms descriptifs qui contiennent le texte `Application` et `Installer` afin qu’ils puissent être distingués ultérieurement.
4. L’écran suivant fournit des instructions détaillées sur la façon de créer le certificat. Cliquez sur le bouton **Continue** : 

    [![Comment créer le certificat](certificates-identifiers-images/certif09.png "Comment créer le certificat")](certificates-identifiers-images/certif09-large.png#lightbox)
5. Choisissez un nom **Common Name** descriptif ; par exemple, utilisez le texte « Application de l’ID du développeur » dans le nom : 

     ![Entrée d’un nom pour le certificat](certificates-identifiers-images/image33.png "Entrée d’un nom pour le certificat")
6. Un fichier de demande de certificat (extension `.certSigningRequest`) est enregistré localement sur votre Mac. Rappelez-vous où il est enregistré (l’emplacement par défaut est le bureau) : 

     [![Chargement du certificat](certificates-identifiers-images/certif10.png "Chargement du certificat")](certificates-identifiers-images/certif10-large.png#lightbox) 

     [![Téléchargement de l’ID de développeur](certificates-identifiers-images/certif11.png "Téléchargement de l’ID de développeur")](certificates-identifiers-images/certif11-large.png#lightbox)
7. Cliquez sur **Download** pour obtenir le certificat, puis double-cliquez dessus pour l’installer dans le **trousseau**.
8. Cliquez sur **Continue**, puis suivez exactement les mêmes étapes pour télécharger un autre certificat qui, cette fois, sera pour le *programme d’installation*.
9. Choisissez un nom **Common Name** descriptif ; par exemple, utilisez le texte « Programme d’installation de l’ID du développeur » dans le nom : 

     ![Entrée d’un nom commun](certificates-identifiers-images/image38.png "Entrée d’un nom commun")
10. Un fichier de demande de certificat (extension `.certSigningRequest`) est enregistré localement sur le Mac. Rappelez-vous où il est enregistré (l’emplacement par défaut est le bureau) : 

     [![Chargement d’un certificat](certificates-identifiers-images/certif10.png "Chargement d’un certificat")](certificates-identifiers-images/certif10-large.png#lightbox)
11. Le certificat peut alors être téléchargé : cliquez sur le bouton **Download** avant de cliquer sur **Done** : 

     [![Téléchargement d’un certificat](certificates-identifiers-images/certif11.png "Téléchargement d’un certificat")](certificates-identifiers-images/certif11-large.png#lightbox)
12. Cliquez sur **Download** pour obtenir le certificat, puis double-cliquez dessus pour l’installer dans le **trousseau**. L’utilitaire **Developer Certificate Utility** affiche le certificat comme suit : 

     [![Developer Certificate Utility](certificates-identifiers-images/certif12.png "Developer Certificate Utility")](certificates-identifiers-images/certif12-large.png#lightbox)
13. Les éléments suivants sont visibles dans le **trousseau** : 

     [![Certificat dans l’accès au trousseau](certificates-identifiers-images/image43.png "Certificat dans l’accès au trousseau")](certificates-identifiers-images/image43-large.png#lightbox)


## <a name="related-links"></a>Liens associés

- [Installation](/visualstudio/mac/installation/)
- [Exemple Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
