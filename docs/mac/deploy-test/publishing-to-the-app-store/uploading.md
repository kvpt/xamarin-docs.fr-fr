---
title: Charger sur le Mac App Store
description: Ce document décrit comment utiliser iTunes Connect pour charger une application Xamarin.Mac dans le Mac App Store. Il présente les informations requises par iTunes Connect pour terminer le processus.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: b0bb58fee1c9eaeee391b3a838b3fa2921e97a57
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284257"
---
# <a name="upload-to-mac-app-store"></a>Charger sur le Mac App Store

_Ce guide présente en détail le chargement d’une application Xamarin.Mac en vue de sa publication sur le Mac App Store._

Les applications sont envoyées pour approbation du Mac App Store par le biais d’[iTunes Connect](http://itunesconnect.apple.com/).

1. Choisissez l’**application macOS** à créer : 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Entrez le nom de l’application et d’autres détails. Le développeur peut uniquement choisir un **Bundle ID** déjà créé : 

    [![](uploading-images/image66.png "Sélection de l’ID de bundle")](uploading-images/image66.png#lightbox)

3. Sélectionnez la date de disponibilité et le prix. Quelle que soit la date de disponibilité sélectionnée par le développeur, l’application ne sera disponible à la vente qu’après avoir été approuvée. Cette valeur peut être définie bien plus tard si le développeur souhaite mieux contrôler la date de disponibilité effective : 

    [![](uploading-images/image67.png "Définition de la date de disponibilité et du prix")](uploading-images/image67.png#lightbox)

4. Entrez les informations de l’application, notamment la catégorie de l’App Store à laquelle elle appartient : 

    [![](uploading-images/image68.png "Entrée des informations relatives à l’application")](uploading-images/image68.png#lightbox) 

    Sélectionnez l’évaluation qui s’applique : 

    [![](uploading-images/image69.png "Définition de l’évaluation de l’application")](uploading-images/image69.png#lightbox) 

    Une description, des mots clés et des URL de contact : 

    [![](uploading-images/image70.png "Modification de la description, des mots clés et des URL de contact")](uploading-images/image70.png#lightbox) 

    Des informations de contact et des conseils à destination des réviseurs App Store : 

    [![](uploading-images/image71.png "Modification des informations de contact et des conseils à destination des réviseurs de l’App Store")](uploading-images/image71.png#lightbox) 

    Et enfin, des captures d’écran : 

    [![](uploading-images/image72.png "Ajout des captures d’écran nécessaires")](uploading-images/image72.png#lightbox) 

    Les captures d’écran doivent être au format JPG, TIF ou PNG, et d’une taille de 1280x800, 1440x900, 2880x1800 ou 2560x1600 pixels. Appuyez sur **Save** pour terminer.

5. Les informations de l’application sont affichées pour révision. Cliquez sur **View Details** (Voir les détails) pour changer d’état : 

    [![](uploading-images/image73.png "Affichage des détails de l’application")](uploading-images/image73.png#lightbox)

6. Dans la vue des détails, cliquez sur Ready to Upload Binary (Prêt à charger le fichier binaire) pour soumettre le fichier de paquet d’application : 

    [![](uploading-images/image74.png "Sélection de Ready to Upload Binary")](uploading-images/image74.png#lightbox)

7. Répondez à la question relative au chiffrement : 

    [![](uploading-images/image75.png "Réponse à la question relative au chiffrement")](uploading-images/image75.png#lightbox)

8. Le site prévient quand il est prêt à accepter le fichier de paquet d’application : 

    [![](uploading-images/image76.png "Notification d’acceptation")](uploading-images/image76.png#lightbox)

9. Démarrez Application Loader et veillez à vous connecter avec l’identifiant Apple.
Choisissez **Deliver Your App** (Livrer votre application) pour continuer : 

    [![](uploading-images/image77.png "Interface d’Application Loader")](uploading-images/image77.png#lightbox)

10. Sélectionnez une application dans la liste des applications ayant l’état **Ready to Upload Binary** (Prêt à charger le fichier binaire), puis cliquez sur **Next** : 

    [![](uploading-images/image78.png "Sélection de l’application à charger")](uploading-images/image78.png#lightbox)

11. Examinez les métadonnées de l’application, puis cliquez sur **Choose...** pour rechercher le fichier de paquet : 

    [![](uploading-images/image79.png "Examen des métadonnées de l’application")](uploading-images/image79.png#lightbox)

12. Recherchez le fichier de paquet qui a été créé dans Visual Studio pour Mac à l’aide de la configuration de build App Store : 

    [![](uploading-images/image80.png "Sélection du fichier à charger")](uploading-images/image80.png#lightbox)

13. Appuyez sur **Envoyer** : 

    [![](uploading-images/image81.png "Envoi de l’application")](uploading-images/image81.png#lightbox)

14. Le paquet est validé et toutes les erreurs signalées. Corrigez ces erreurs et effectuez à nouveau le chargement. Une fois le chargement correctement terminé, l’application est automatiquement envoyée pour être examinée par l’équipe de l’App Store : 

    [![](uploading-images/image82.png "Exemple d’erreurs de chargement")](uploading-images/image82.png#lightbox)

Une fois l’application approuvée, elle peut être téléchargée ou achetée à partir du Mac App Store.

## <a name="related-links"></a>Liens associés

- [Installation](~//mac/get-started/installation.md)
- [Exemple Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guide des outils : Signature de code de votre application](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
