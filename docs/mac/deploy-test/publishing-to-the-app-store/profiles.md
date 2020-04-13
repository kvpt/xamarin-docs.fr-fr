---
title: Profils de provisionnement pour les applications Xamarin.Mac
description: Ce guide présente en détail la création des profils de provisionnement nécessaires qui seront exigés pour publier une application Xamarin.Mac.
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 00d7610bda3d1b7c9f954df64bb6e3af982b1d06
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725517"
---
# <a name="provisioning-profiles-for-xamarinmac-apps"></a>Profils de provisionnement pour les applications Xamarin.Mac

Les profils de provisionnement permettent aux développeurs d’incorporer plusieurs fonctionnalités spécifiques à macOS (anciennement Mac OS X) (comme iCloud et les notifications Push) dans leurs applications Xamarin.Mac. Ils doivent créer, télécharger et installer un profil de provisionnement Mac pour chaque application qu’ils développent et qui utilise ces fonctionnalités.

[![](profiles-images/certif13.png "The Apple Provisioning Portal")](profiles-images/certif13.png#lightbox)

## <a name="development-provisioning-profile"></a>Profil de provisionnement de développement

Un profil de provisionnement de développement permet à une application ciblée Mac App Store d’être testée sur les ordinateurs spécifiques qui ont été configurés dans le profil. Cela s’avère particulièrement utile lors de l’utilisation de fonctionnalités macOS comme iCloud et les notifications Push.

> [!NOTE]
> Pour pouvoir créer un profil de provisionnement de développement, le développeur doit avoir préalablement créé un certificat de développement Mac. Complétez les informations comme indiqué dans cette capture d’écran pour générer un **profil de provisionnement de développement** pouvant être utilisé pour créer des builds. Un certificat de développement Mac valide doit pouvoir être sélectionné dans la zone **Certificat** et au moins un système doit être inscrit à des fins de test.

Effectuez les actions suivantes :

1. Sélectionnez le type de profil de provisionnement à créer, puis cliquez sur le bouton **Continue** :

    [![](profiles-images/certif14.png "Selecting the profile type")](profiles-images/certif14.png#lightbox)
2. Sélectionnez l’ID de l’application pour laquelle créer le profil, puis cliquez sur le bouton **Continue** :

    [![](profiles-images/certif15.png "Selecting the app ID")](profiles-images/certif15.png#lightbox)
3. Sélectionnez l’ID de développeur utilisé pour signer le profil, puis cliquez sur **Continue** :

    [![](profiles-images/certif16.png "Selecting the developer ID")](profiles-images/certif16.png#lightbox)
4. Sélectionnez les ordinateurs sur lesquels ce profil peut être utilisé, puis cliquez sur **Continue** :

    [![](profiles-images/certif17.png "Selecting the allowed computers")](profiles-images/certif17.png#lightbox)
5. À présent, entrez une valeur **Profile Name**, puis cliquez sur le bouton **Generate** :

    [![](profiles-images/certif18.png "Generating the profile")](profiles-images/certif18.png#lightbox)
6. Cliquez sur le bouton **Download** pour télécharger le nouveau profil :

    [![](profiles-images/certif19.png "Downloading the profile")](profiles-images/certif19.png#lightbox)
7. Les profils de provisionnement de développement sont installés dans le volet Profils de provisionnement de l’application **Préférences Système** du Mac :

    [![](profiles-images/certif20.png "Installing the profile")](profiles-images/certif20.png#lightbox)
8. Le volet Profils de provisionnement affiche tous les profils installés :

    [![](profiles-images/image47.png "Showing all installed profiles")](profiles-images/image47.png#lightbox)
9. Le profil apparaît également dans l’utilitaire **Developer Certificate Utility** au cas où il devrait être à nouveau téléchargé :

    [![](profiles-images/image48.png "The Developer Certificate Utility")](profiles-images/image48.png#lightbox)

Un nouveau profil de provisionnement de développement doit être créé pour chaque nouvelle application ou quand un nouvel ordinateur est ajouté en vue d’effectuer des tester dessus.

## <a name="production-provisioning-profile"></a>Profil de provisionnement de production

Les profils de provisionnement de production sont obligatoires pour générer un paquet à envoyer au Mac App Store.

Effectuez les actions suivantes :

1. Sélectionnez le type de profil à créer, puis cliquez sur le bouton **Continue** :

    [![](profiles-images/certif21.png "Selecting the type of profile")](profiles-images/certif21.png#lightbox)
2. Sélectionnez l’ID de l’application pour laquelle créer le profil, puis cliquez sur le bouton **Continue** :

    [![](profiles-images/certif15.png "Selecting the app ID")](profiles-images/certif15.png#lightbox)
3. Sélectionnez l’ID de société utilisé pour signer le profil, puis cliquez sur le bouton **Continue** :

    [![](profiles-images/certif23.png "Selecting the company ID")](profiles-images/certif23.png#lightbox)
4. Entrez une valeur **Profile Name**, puis cliquez sur le bouton **Generate** :

    [![](profiles-images/certif24.png "Generating the profile")](profiles-images/certif24.png#lightbox)
5. Cliquez sur **Download** pour obtenir le fichier de profil de provisionnement (extension `.provisionprofile`) :

    [![](profiles-images/certif25.png "Downloading the profile")](profiles-images/certif25.png#lightbox)
6. Faites-le glisser dans **Xcode Organizer** ou double-cliquez dessus pour l’installer. Le profil apparaît alors dans Xcode Organizer :

    [![](profiles-images/image51.png "Installing the profile")](profiles-images/image51.png#lightbox)
7. Le profil de provisionnement apparaît également dans la liste :

    [![](profiles-images/certif26.png "Showing the installed profiles")](profiles-images/certif26.png#lightbox)

Si le développeur change les fonctionnalités utilisées par un ID d’application (par exemple, en activant iCloud ou les notifications Push), il doit recréer les profils de provisionnement pour cet ID d’application.

## <a name="related-links"></a>Liens connexes

- [Installation](~//mac/get-started/installation.md)
- [Bonjour, échantillon De Mac](~//mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guide sur les outils : Signature du code de votre application](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de développeur et GateKeeper](https://developer.apple.com/developer-id/)
