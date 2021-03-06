---
title: Comment voir les bibliothèques prises en charge dans une bibliothèque de classes portable ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: davidortinau
ms.author: daortin
ms.date: 07/27/2018
ms.openlocfilehash: 9484bcac199118bb1beb1b520be8e231dc9181ce
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457846"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>Comment voir les bibliothèques prises en charge dans une bibliothèque de classes portable ?

- Vous trouverez une vue d’ensemble des différentes fonctionnalités prises en charge par les différentes plateformes PCL cibles sous la section *fonctionnalités prises en charge* de cette page : [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- Une autre option consiste à utiliser l' [Analyseur de portabilité .net](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) pour déterminer si votre bibliothèque existante peut être convertie en profil PCL.

- Une troisième possibilité consiste à parcourir le contenu du profil réel que vous pouvez utiliser. À l’aide du profil 78, par exemple, vous pouvez accéder ici `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` et afficher tous les assemblys qu’il contient.

Quelle que soit la méthode choisie, veuillez noter que certaines fonctionnalités doivent être téléchargées via NuGet et la bibliothèque Microsoft BCL.