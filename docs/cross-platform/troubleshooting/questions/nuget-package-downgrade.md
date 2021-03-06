---
title: Comment faire passer un package NuGet à une version antérieure ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 0c70859845915a821bb83b0f9d29528634b1a5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013607"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Comment faire passer un package NuGet à une version antérieure ?

Visual Studio pour Mac & Visual Studio possèdent des fonctionnalités permettant de sélectionner des versions antérieures des packages et de les installer automatiquement. semblable au fonctionnement de la mise à jour des packages. Ces étapes sont décrites ci-dessous.

## <a name="visual-studio"></a>Visual Studio

1. Accédez à **outils > gestionnaire de package NuGet > console du gestionnaire de package**
2. Définir le projet sous le **projet par défaut**
3. Utilisez la syntaxe suivante :

    > Install-Package [PackageName]-version [onglet du menu version]

Vous pouvez également copier/coller la commande exacte à partir de la page NuGet du package. Exemple pour Xamarin. Forms : [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. Dans votre projet, cliquez avec le bouton droit sur le dossier Packages & Sélectionnez **Ajouter des packages** .
2. Dans Searchbar, vous pouvez utiliser la syntaxe suivante pour rechercher les packages requis :

    `[PackageName] version:*`

### <a name="examples"></a>Exemples 

- Répertorie tous les packages Xamarin. Forms : 

    `Xamarin.Forms version:`

- Répertorie tous les packages Xamarin. Forms 1.4. x : 

    `Xamarin.Forms version:1.4`

> [!NOTE]
> Si vous ajoutez un espace entre `version:` & le numéro de version, la recherche se comportera comme si aucune version n’avait été spécifiée.
