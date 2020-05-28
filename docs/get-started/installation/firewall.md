---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 71b2754d19d00b7bf4860acd96bfb7ad8dec4ce5
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132989"
---
# <a name="xamarin-firewall-configuration-instructions"></a>Instructions de configuration du pare-feu Xamarin

_Une liste d’hôtes que vous devez autoriser dans votre pare-feu afin de permettre à la plateforme de Xamarin de fonctionner pour votre entreprise._

Pour que les produits Xamarin puissent être installés et fonctionner correctement, certains points de terminaison doivent être accessibles pour télécharger les outils et les mises à jour nécessaires. Si vos paramètres de pare-feu ou ceux de votre entreprise sont stricts, vous pouvez rencontrer des problèmes avec l’installation, la gestion des licences ou certains composants, par exemple. Ce document décrit certains des points de terminaison connus qui doivent être autorisés dans votre pare-feu pour permettre à Xamarin de fonctionner. Cette liste ne comprend pas les points de terminaison qui sont nécessaires pour les outils tiers inclus dans le téléchargement. Si vous rencontrez encore des problèmes après avoir étudié cette liste, reportez-vous aux guides de dépannage de l’installation pour les produits Apple ou Android.

## <a name="endpoints-to-allow"></a>Points de terminaison à autoriser

### <a name="xamarin-installer"></a>Programme d’installation de Xamarin

Les adresses connues suivantes doivent être ajoutées à la liste pour que les logiciels soient installés correctement à l’aide de la dernière version du programme d’installation Xamarin :

- xamarin.com (manifestes du programme d’installation)
- dl.xamarin.com (emplacement de téléchargement du paquet)
- dl.google.com (pour télécharger Android SDK)
- download.oracle.com (JDK)
- visualstudio.com (emplacement de téléchargement des paquets d’installation)
- go.microsoft.com (résolution de l’URL d’installation)
- aka.ms (résolution de l’URL d’installation)

Si vous utilisez un ordinateur Mac et rencontrez des problèmes d’installation avec Xamarin.Android, vérifiez que Mac OS est en mesure de télécharger Java.

### <a name="nuget-including-xamarinforms"></a>NuGet (y compris Xamarin.Forms )

Les adresses suivantes doivent être ajoutées pour accéder à NuGet ( Xamarin.Forms est empaqueté en tant que NuGet) :

- www.nuget.org (pour accéder à NuGet)
- globalcdn.nuget.org (téléchargements NuGet)
- dl-ssl.google.com (composants Google pour Android et Xamarin.Forms )

### <a name="software-updates"></a>Mises à jour logicielles

Les adresses suivantes doivent être ajoutées à la liste pour que les mises à jour logicielles puissent être téléchargées correctement :

- software.xamarin.com (service de mise à jour)
- download.visualstudio.microsoft.com
- dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Mac Agent Xamarin

Pour connecter Visual Studio à un Build Host Mac avec Mac Agent Xamarin, vous devez ouvrir le port SSH. Par défaut, il s’agit du **port 22**.
