---
title: Instructions de configuration du pare-feu Xamarin
description: Ce document fournit une liste d’hôtes qui doivent être autorisés dans votre pare-feu pour permettre à Xamarin de fonctionner dans un environnement d’entreprise.
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
author: conceptdev
ms.author: crdun
ms.date: 07/17/2019
ms.openlocfilehash: 2b52dfd55194ec076f28f8c33e758a39d14f5943
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "70291329"
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

### <a name="nuget-including-xamarinforms"></a>NuGet (notamment Xamarin.Forms)

Les adresses suivantes doivent être ajoutées pour permettre l’accès à NuGet (Xamarin.Forms est empaqueté au format NuGet) :

- www.nuget.org (pour accéder à NuGet)
- globalcdn.nuget.org (téléchargements NuGet)
- dl-ssl.google.com (composants Google pour Android et Xamarin.Forms)

### <a name="software-updates"></a>Mises à jour logicielles

Les adresses suivantes doivent être ajoutées à la liste pour que les mises à jour logicielles puissent être téléchargées correctement :

- software.xamarin.com (service de mise à jour)
- download.visualstudio.microsoft.com
- dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Mac Agent Xamarin

Pour connecter Visual Studio à un Build Host Mac avec Mac Agent Xamarin, vous devez ouvrir le port SSH. Par défaut, il s’agit du **port 22**.
