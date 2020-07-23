---
title: Étape 1. Inscrire une application pour utiliser Azure Active Directory
description: Ce document explique comment inscrire une application Azure avec Azure Active Directory pour qu’elle puisse être accessible en toute sécurité par les clients mobiles.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 6b10b2fd489881292d95bbf79375a6488aa88a17
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932543"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Étape 1. Inscrire une application pour utiliser Azure Active Directory

1. Accédez à [WindowsAzure.com](https://manage.windowsazure.com) et connectez-vous avec votre compte Microsoft ou votre compte d’organisation dans le portail Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez obtenir une version d’évaluation à partir de [Azure.com](https://www.azure.com)

2. Après vous être connecté, accédez à la section **Active Directory** (1) et choisissez le répertoire dans lequel vous souhaitez inscrire l’application (2)

   [![et choisissez le répertoire dans lequel vous souhaitez inscrire l’application](register-images/01.-active-directory-in-azure-portal-sml.jpg)](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Cliquez sur **Ajouter** pour créer une nouvelle application, puis sélectionnez **Ajouter une application développée par mon organisation**

   [![Ajouter une application développée par mon organisation](register-images/02.-add-new-application-sml.jpg)](register-images/02.-add-new-application.jpg#lightbox)

4. Dans l’écran suivant, donnez un nom à votre application (par exemple, XAM-DEMO).
   Veillez à sélectionner **application cliente Native** comme type d’application.

   ![Veillez à sélectionner application cliente Native comme type d’application.](register-images/03.-app-name.jpg)

5. Sur l’écran final, fournissez un*URI * de redirection* qui est unique pour votre application, car elle sera retournée à cet URI lorsque l’authentification est terminée.

   ![Sur l’écran final, fournissez un URI de redirection qui est unique à votre application, car il sera renvoyé à cet URI lorsque l’authentification est terminée.](register-images/04.-app-redirect.jpg)

6. Une fois l’application créée, accédez à l’onglet **configurer** . Notez l' **ID client** que nous utiliserons plus tard dans notre application. En outre, sur cet écran, vous pouvez autoriser votre application mobile à accéder à Active Directory ou à ajouter une autre application telle que l’API Web ou Office 365, qui peut être utilisée par une application mobile une fois l’authentification terminée.

   ![En outre, sur cet écran, vous pouvez autoriser votre application mobile à accéder à Active Directory ou à ajouter une autre application comme l’API Web ou Office 365](register-images/05.-configure.jpg)

## <a name="related-links"></a>Liens associés

- [Exemple Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
