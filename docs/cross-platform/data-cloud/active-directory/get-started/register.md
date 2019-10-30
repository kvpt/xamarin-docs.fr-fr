---
title: Étape 1. Inscrire une application pour utiliser Azure Active Directory
description: Ce document explique comment inscrire une application Azure avec Azure Active Directory pour qu’elle puisse être accessible en toute sécurité par les clients mobiles.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: c3b93ec4fa4ec2ffad9db26414c2453ba7281974
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016653"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Étape 1. Inscrire une application pour utiliser Azure Active Directory

1. Accédez à [WindowsAzure.com](https://manage.windowsazure.com) et connectez-vous avec votre compte Microsoft ou votre compte d’organisation dans le portail Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez obtenir une version d’évaluation à partir de [Azure.com](https://www.azure.com)

2. Après vous être connecté, accédez à la section **Active Directory** (1) et choisissez le répertoire dans lequel vous souhaitez inscrire l’application (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "section and choose the directory where you want to register the application")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Cliquez sur **Ajouter** pour créer une nouvelle application, puis sélectionnez **Ajouter une application développée par mon organisation**

   [![](register-images/02.-add-new-application-sml.jpg "Add an application my organization is developing")](register-images/02.-add-new-application.jpg#lightbox)

4. Dans l’écran suivant, donnez un nom à votre application (par exemple, XAM-DEMO).
   Veillez à sélectionner **application cliente Native** comme type d’application.

   ![](register-images/03.-app-name.jpg "Make sure you select Native Client Application as the type of application")

5. Sur l’écran final, fournissez un*URI * de redirection* qui est unique pour votre application, car elle sera retournée à cet URI lorsque l’authentification est terminée.

   ![](register-images/04.-app-redirect.jpg "On the final screen, provide a Redirect URI that is unique to your application as it will return to this URI when   authentication is complete")

6. Une fois l’application créée, accédez à l’onglet **configurer** . Notez l' **ID client** que nous utiliserons plus tard dans notre application. En outre, sur cet écran, vous pouvez autoriser votre application mobile à accéder à Active Directory ou à ajouter une autre application telle que l’API Web ou Office 365, qui peut être utilisée par une application mobile une fois l’authentification terminée.

   ![](register-images/05.-configure.jpg "Also, on this screen you can give your mobile application access to Active Directory or add another application like Web API or Office365")

## <a name="related-links"></a>Liens associés

- [Exemple Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
