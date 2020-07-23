---
title: Charger sur le Mac App Store
description: Ce document décrit comment utiliser iTunes Connect pour charger une application Xamarin.Mac dans le Mac App Store. Il présente les informations requises par iTunes Connect pour terminer le processus.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 57591a6e9fcaa3c8271ab27756160ee1c46a4fa3
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935926"
---
# <a name="upload-to-mac-app-store"></a>Charger sur le Mac App Store

_Ce guide présente en détail le chargement d’une application Xamarin.Mac en vue de sa publication sur le Mac App Store._

Les applications sont envoyées pour approbation du Mac App Store par le biais d’[iTunes Connect](https://itunesconnect.apple.com/). Vous aurez également besoin de l’outil [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) disponible dans l’App Store.

1. Choisissez l’**application macOS** à créer :

    [![iTunes Connect](uploading-images/image65.png)](uploading-images/image65.png#lightbox)

2. Entrez le nom de l’application et d’autres détails. Le développeur peut uniquement choisir un **Bundle ID** déjà créé :

    [![Sélection de l’ID de bundle](uploading-images/image66.png)](uploading-images/image66.png#lightbox)

3. Sélectionnez la date de disponibilité et le prix. Quelle que soit la date de disponibilité sélectionnée par le développeur, l’application ne sera disponible à la vente qu’après avoir été approuvée. Cette valeur peut être définie bien plus tard si le développeur souhaite mieux contrôler la date de disponibilité effective :

    [![Définition de la date de disponibilité et du prix](uploading-images/image67.png)](uploading-images/image67.png#lightbox)

4. Entrez les informations de l’application, notamment la catégorie de l’App Store à laquelle elle appartient :

    [![Entrée des informations relatives à l’application](uploading-images/image68.png)](uploading-images/image68.png#lightbox)

    Sélectionnez l’évaluation qui s’applique :

    [![Définition de l’évaluation de l’application](uploading-images/image69.png)](uploading-images/image69.png#lightbox)

    Une description, des mots clés et des URL de contact :

    [![Modification de la description, des mots clés et des URL de contact](uploading-images/image70.png)](uploading-images/image70.png#lightbox)

    Des informations de contact et des conseils à destination des réviseurs App Store :

    [![Modification des informations de contact et des conseils à destination des réviseurs de l’App Store](uploading-images/image71.png)](uploading-images/image71.png#lightbox)

    Et enfin, des captures d’écran :

    [![Ajout des captures d’écran nécessaires](uploading-images/image72.png)](uploading-images/image72.png#lightbox)

    Les captures d’écran doivent être au format JPG, TIF ou PNG, et d’une taille de 1280x800, 1440x900, 2880x1800 ou 2560x1600 pixels. Appuyez sur **Save** pour terminer.

5. Les informations de l’application sont affichées pour révision. Cliquez sur **View Details** (Voir les détails) pour changer d’état :

    [![Affichage des détails de l’application](uploading-images/image73.png)](uploading-images/image73.png#lightbox)

6. Dans la vue des détails, cliquez sur Ready to Upload Binary (Prêt à charger le fichier binaire) pour soumettre le fichier de paquet d’application :

    [![Sélection de Ready to Upload Binary](uploading-images/image74.png)](uploading-images/image74.png#lightbox)

7. Répondez à la question relative au chiffrement :

    [![Réponse à la question relative au chiffrement](uploading-images/image75.png)](uploading-images/image75.png#lightbox)

8. Le site prévient quand il est prêt à accepter le fichier de paquet d’application :

    [![Notification d’acceptation](uploading-images/image76.png)](uploading-images/image76.png#lightbox)

9. Démarrez **Transporter** et connectez-vous avec votre ID Apple, puis choisissez **ADD APP** :

    [![Interface d’Application Loader](uploading-images/transporter01-sml.png)](uploading-images/transporter01.png#lightbox)

    Suivez les instructions pour charger votre package d’application dans iTunes Connect.

    > [!NOTE]
    > [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) remplace l’outil **Application Loader** qui était utilisé avec Xcode 10 et versions antérieures.
    > Application Loader n’est plus disponible dans Xcode 11 ou version ultérieure.

Une fois l’application approuvée, elle peut être téléchargée ou achetée à partir du Mac App Store.

## <a name="related-links"></a>Liens connexes

- [Installation](~//mac/get-started/installation.md)
- [Exemple Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guide sur les outils : Signature du code de votre application](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de développeur et GateKeeper](https://developer.apple.com/developer-id/)
