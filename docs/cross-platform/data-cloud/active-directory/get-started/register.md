---
title: Étape 1. Inscrire une application pour utiliser Azure Active Directory
description: Ce document décrit comment inscrire une application Azure avec Azure Active Directory pour qu’il peut être accessible en toute sécurité par les clients mobiles.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: fd3cb0e8201a6e84c726b211852013bd5f35fba1
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675112"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Étape 1. Inscrire une application pour utiliser Azure Active Directory

1. Accédez à [windowsazure.com](https://manage.windowsazure.com) et connectez-vous avec votre Microsoft Account ou organisation dans le portail Azure. Si vous n’avez pas un abonnement Azure, vous pouvez obtenir une version d’évaluation à partir de [azure.com](http://www.azure.com)

2. Après vous être connecté, accédez à la **Active Directory** section (1) et sélectionnez le répertoire où vous voulez inscrire l’application (2)

  [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "section et sélectionnez le répertoire où vous souhaitez enregistrer l’application")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Cliquez sur **ajouter** pour créer la nouvelle application, puis sélectionnez **ajouter une application développée par mon organisation**

  [![](register-images/02.-add-new-application-sml.jpg "Ajouter une application développée par mon organisation")](register-images/02.-add-new-application.jpg#lightbox)

4. Dans l’écran suivant, nommez votre application (par exemple). XAM-DEMO).
  Veillez à sélectionner **Application cliente Native** en tant que le type d’application.

  ![](register-images/03.-app-name.jpg "Vérifiez que vous sélectionnez l’Application cliente Native en tant que le type d’application")

5. Dans le dernier écran, vous devez fournir un **URI de redirection* qui est propre à votre application en tant qu’il renverra à cet URI lors de l’authentification est terminée.

  ![](register-images/04.-app-redirect.jpg "Dans le dernier écran, fournir un URI de redirection qui est unique à votre application en tant qu’il renverra à cet URI lors de l’authentification est terminée")

6. Une fois que l’application est créée, accédez à la **configurer** onglet. Notez le **ID Client** que nous allons utiliser dans notre application plus tard. En outre, dans cet écran, vous pouvez donner à votre application mobile à accéder à Active Directory ou ajouter une autre application comme API Web ou Office 365, ce qui peut être utilisé par les applications mobiles une fois que l’authentification est terminée.

    ![](register-images/05.-configure.jpg "En outre, dans cet écran, vous pouvez donner à votre application mobile à accéder à Active Directory ou ajouter une autre application, comme les API Web ou Office 365")



## <a name="related-links"></a>Liens associés

- [Exemple de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
