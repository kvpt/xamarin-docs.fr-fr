---
title: Utiliser des bibliothèques de .NET Standard pour partager du code
description: Ce document explique comment utiliser les bibliothèques de .NET Standard pour partager du code. Il aborde la création d’une bibliothèque de .NET Standard, la modification de ses paramètres et son utilisation dans une application.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: cae59053374f673a56d02e86cd59fb85f313c41b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016814"
---
# <a name="net-standard-library-code-sharing"></a>Partage de code de la bibliothèque .NET Standard

Les bibliothèques .NET Standard ont une API uniforme pour toutes les plateformes .NET, y compris Xamarin et .NET Core. Créez une seule bibliothèque de .NET Standard et utilisez-la à partir de n’importe quel Runtime qui prend en charge la plateforme .NET Standard. Reportez-vous à [Ce graphique](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support) pour plus d’informations sur les plateformes prises en charge.

Bien .NET Standard que les versions 1,0 à 1,6 fournissent des sous-ensembles incrémentiels plus grands du .NET Framework, .NET Standard 2,0 offre le meilleur niveau de prise en charge pour les applications Xamarin et le portage de bibliothèques de classes portables existantes.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

Cette section explique comment créer et utiliser une bibliothèque .NET Standard à l’aide de Visual Studio pour Mac.

### <a name="creating-a-net-standard-library"></a>Création d’une bibliothèque de .NET Standard

Vous pouvez ajouter une bibliothèque .NET Standard à votre solution en procédant comme suit :

1. Dans la boîte de dialogue **Ajouter un nouveau projet** , sélectionnez la catégorie **.net Core** , puis sélectionnez **bibliothèque de .NET standard**:

    ![Créer une bibliothèque de .NET Standard](net-standard-images/vsm01-m157.png "Création d’une bibliothèque de .NET Standard")

2. Dans l’écran suivant, choisissez Framework cible- **.NET Standard 2,0** est recommandé :

    [![choisir .NET Standard 2,0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. Dans l’écran final, tapez le nom du projet et cliquez sur **créer**.

4. Le projet de bibliothèque de .NET Standard s’affiche comme indiqué dans le Explorateur de solutions. Le nœud dépendances indique que la bibliothèque utilise la bibliothèque [Netstandard](https://www.nuget.org/packages/NETStandard.Library/).

    ![Nœud dépendances dans la solution indique .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Modification des paramètres de la bibliothèque de .NET Standard

Les paramètres de la bibliothèque .NET Standard peuvent être affichés et modifiés en cliquant avec le bouton droit sur le projet et en sélectionnant `Options` comme indiqué dans cette capture d’écran :

![Modifier .NET Standard Framework cible dans les options du projet](net-standard-images/vsm03-m157.png "Modifier la version du Framework cible .NET Standard dans options du projet")

Dans, vous pouvez modifier votre version de `netstandard` en modifiant la valeur de la liste déroulante `Target Framework`.

**En outre :** Vous pouvez modifier la `.csproj` directement pour modifier cette valeur.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

Cette section explique comment créer et utiliser une bibliothèque .NET Standard à l’aide de Visual Studio.

### <a name="creating-a-net-standard-library"></a>Création d’une bibliothèque de .NET Standard

L’ajout d’une bibliothèque de .NET Standard à votre solution est relativement simple.

1. Dans la boîte de dialogue **nouveau projet** , sélectionnez la catégorie **.NET standard** , puis sélectionnez **bibliothèque de classes (.NET standard)** .

    ![Création d’une bibliothèque de classes .NET Standard](net-standard-images/vs01-w157.png "Créer une bibliothèque de classes de .NET Standard")

2. Le projet de bibliothèque de .NET Standard s’affiche comme indiqué dans le Explorateur de solutions. Le nœud dépendances indique que la bibliothèque utilise la bibliothèque [Netstandard](https://www.nuget.org/packages/NETStandard.Library/).

    ![Netstandard. Library dans le dossier du projet](net-standard-images/vs02-w157.png "Projet de .NET Standard dans la solution")

### <a name="editing-net-standard-library-settings"></a>Modification des paramètres de la bibliothèque de .NET Standard

Vous pouvez afficher et modifier les paramètres de la bibliothèque .NET Standard en cliquant avec le bouton droit sur le projet et en sélectionnant **Propriétés** , comme indiqué dans cette capture d’écran :

![Modifier des frameworks cibles .NET standard dans les propriétés du projet](net-standard-images/vs03-w157.png "Référencer une bibliothèque de .NET Standard de la même façon que les autres projets")

**En outre :** Vous pouvez modifier la `.csproj` directement pour modifier l’élément de `TargetFramework` et changer la version ciblée (par exemple, `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>Utilisation d’un projet de bibliothèque .NET Standard

Une fois la bibliothèque de .NET Standard créée, vous pouvez y ajouter une référence à partir de n’importe quel projet d’application ou de bibliothèque compatible, de la même façon que vous ajoutez normalement des références. Dans Visual Studio, cliquez avec le bouton droit sur le nœud Références, puis choisissez **Ajouter une référence...** , puis basculez vers l’onglet **projets > solution** , comme indiqué ci-dessous :

![Référencement d’une bibliothèque de .NET Standard](net-standard-images/vs04.png "Dans Visual Studio, cliquez avec le bouton droit sur le nœud Références, puis choisissez Ajouter une référence... Ensuite, basculez vers l’onglet projets de solution comme indiqué.")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET Standard et Xamarin. Forms pour le développeur .NET (vidéo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>Liens associés

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -informations détaillées et comparaison avec PCL.
