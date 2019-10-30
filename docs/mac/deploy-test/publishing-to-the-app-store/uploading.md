---
title: Charger sur le Mac App Store
description: Ce document décrit comment utiliser iTunes Connect pour charger une application Xamarin.Mac dans le Mac App Store. Il présente les informations requises par iTunes Connect pour terminer le processus.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: fede666071aad8702eda17a94a06c7de49c7397e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029476"
---
# <a name="upload-to-mac-app-store"></a>Charger sur le Mac App Store

_Ce guide présente en détail le chargement d’une application Xamarin.Mac en vue de sa publication sur le Mac App Store._

Les applications sont envoyées pour approbation du Mac App Store par le biais d’[iTunes Connect](https://itunesconnect.apple.com/).

1. Choisissez l’**application macOS** à créer : 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Entrez le nom de l’application et d’autres détails. Le développeur peut uniquement choisir un **Bundle ID** déjà créé : 

    [![](uploading-images/image66.png "Selecting the bundle ID")](uploading-images/image66.png#lightbox)

3. Sélectionnez la date de disponibilité et le prix. Quelle que soit la date de disponibilité sélectionnée par le développeur, l’application ne sera disponible à la vente qu’après avoir été approuvée. Cette valeur peut être définie bien plus tard si le développeur souhaite mieux contrôler la date de disponibilité effective : 

    [![](uploading-images/image67.png "Setting the available date and price")](uploading-images/image67.png#lightbox)

4. Entrez les informations de l’application, notamment la catégorie de l’App Store à laquelle elle appartient : 

    [![](uploading-images/image68.png "Entering the app information")](uploading-images/image68.png#lightbox) 

    Sélectionnez l’évaluation qui s’applique : 

    [![](uploading-images/image69.png "Setting the app ratings")](uploading-images/image69.png#lightbox) 

    Une description, des mots clés et des URL de contact : 

    [![](uploading-images/image70.png "Editing the Description, keywords and contact URLs")](uploading-images/image70.png#lightbox) 

    Des informations de contact et des conseils à destination des réviseurs App Store : 

    [![](uploading-images/image71.png "Editing the contact information and advice for the App Store reviewers")](uploading-images/image71.png#lightbox) 

    Et enfin, des captures d’écran : 

    [![](uploading-images/image72.png "Adding the required screenshots")](uploading-images/image72.png#lightbox) 

    Les captures d’écran doivent être au format JPG, TIF ou PNG, et d’une taille de 1280x800, 1440x900, 2880x1800 ou 2560x1600 pixels. Appuyez sur **Save** pour terminer.

5. Les informations de l’application sont affichées pour révision. Cliquez sur **View Details** (Voir les détails) pour changer d’état : 

    [![](uploading-images/image73.png "Viewing the app details")](uploading-images/image73.png#lightbox)

6. Dans la vue des détails, cliquez sur Ready to Upload Binary (Prêt à charger le fichier binaire) pour soumettre le fichier de paquet d’application : 

    [![](uploading-images/image74.png "Selecting Ready to Upload Binary")](uploading-images/image74.png#lightbox)

7. Répondez à la question relative au chiffrement : 

    [![](uploading-images/image75.png "Answering the cryptography question")](uploading-images/image75.png#lightbox)

8. Le site prévient quand il est prêt à accepter le fichier de paquet d’application : 

    [![](uploading-images/image76.png "The acceptance notification")](uploading-images/image76.png#lightbox)

9. Démarrez Application Loader et veillez à vous connecter avec l’identifiant Apple.
Choisissez **Deliver Your App** (Livrer votre application) pour continuer : 

    [![](uploading-images/image77.png "The Application Loader interface")](uploading-images/image77.png#lightbox)

10. Sélectionnez une application dans la liste des applications ayant l’état **Ready to Upload Binary** (Prêt à charger le fichier binaire), puis cliquez sur **Next** : 

    [![](uploading-images/image78.png "Selecting the app to load")](uploading-images/image78.png#lightbox)

11. Examinez les métadonnées de l’application, puis cliquez sur **Choose...** pour rechercher le fichier de paquet : 

    [![](uploading-images/image79.png "Reviewing the app metadata")](uploading-images/image79.png#lightbox)

12. Recherchez le fichier de paquet qui a été créé dans Visual Studio pour Mac à l’aide de la configuration de build App Store : 

    [![](uploading-images/image80.png "Selecting the file to upload")](uploading-images/image80.png#lightbox)

13. Appuyez sur **Envoyer** : 

    [![](uploading-images/image81.png "Sending the app")](uploading-images/image81.png#lightbox)

14. Le paquet est validé et toutes les erreurs signalées. Corrigez ces erreurs et effectuez à nouveau le chargement. Une fois le chargement correctement terminé, l’application est automatiquement envoyée pour être examinée par l’équipe de l’App Store : 

    [![](uploading-images/image82.png "An example of upload errors")](uploading-images/image82.png#lightbox)

Une fois l’application approuvée, elle peut être téléchargée ou achetée à partir du Mac App Store.

## <a name="related-links"></a>Liens associés

- [Installation](~//mac/get-started/installation.md)
- [Exemple Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guide sur les outils : Signature du code de votre application](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
