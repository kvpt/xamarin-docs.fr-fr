---
title: Partage de code sur plusieurs plateformes
description: Ce document contient des liens vers divers guides qui décrivent les techniques de partage de code, notamment les bibliothèques de classes portables, les projets partagés, les .NET Standard et NuGet.
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a91fba3cd1fba3bcf2317e8f9cb25631c62491cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016827"
---
# <a name="sharing-code-on-multiple-platforms"></a>Partage de code sur plusieurs plateformes

Ces articles décrivent les différentes options disponibles pour le partage de code entre plateformes, notamment Windows, Android, iOS et bien plus encore.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Vue d’ensemble du partage de code](code-sharing.md)

Découvrez les différentes options de partage de code disponibles pour les projets Xamarin, y compris les bibliothèques .NET Standard et les projets partagés. Les bibliothèques de classes portables sont également prises en charge, mais elles sont considérées comme dépréciées en faveur de .NET Standard.

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET Standard est l’option préférée pour le partage de code entre plateformes. Le code est généré par rapport à une version spécifique (2,0 fournit la meilleure compatibilité d’API avec le code .NET Framework existant) et peut ensuite être consommé par d’autres projets qui prennent en charge ce niveau ou plus. Les projets .NET Standard sont pris en charge dans Visual Studio 2019 et Visual Studio 2019 pour Mac.

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Projets partagés](~/cross-platform/app-fundamentals/shared-projects.md)

Les projets partagés vous permettent d’écrire du code commun qui est référencé par un certain nombre de projets d’application différents. Le code est compilé dans le cadre de chaque projet de référence et peut inclure des directives de compilateur pour incorporer les fonctionnalités spécifiques à la plateforme dans la base de code partagée. Cet article explique le fonctionnement des projets partagés et comment les créer et les utiliser avec des projets Xamarin.

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md)

Les projets de bibliothèque de classes portables vous permettent de générer et de distribuer des assemblys qui contiennent du code partagé à exécuter sur plusieurs plateformes. Pour créer une bibliothèque de classes portable (ou « PCL »), vous devez d’abord sélectionner les plateformes à cibler, puis écrire du code sur un sous-ensemble du .NET Framework qui est disponible dans le profil défini pour ces plateformes. Les classes portables sont considérés comme déconseillés dans les dernières versions de Visual Studio. les développeurs sont encouragés à utiliser .NET Standard 2,0 à la place.

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Projets NuGet : bibliothèques multiplateformes pour le partage de code](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Les packages NuGet peuvent être générés automatiquement à partir de projets PCL ou .NET standard. et les projets partagés peuvent être empaquetés dans des packages NuGet « appât et Switch » à l’aide du type de projet NuGet distinct. Cette section explique comment créer des packages NuGet pour chaque scénario de partage de code.

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Création manuelle de packages NuGet pour Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Conseils pour la création de packages NuGet qui fonctionnent avec la plateforme Xamarin.

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[Utiliser des bibliothèquesC++ C/dans des projets Xamarin multiplateforme](~/cross-platform/cpp/index.md)

Cette technique vous permet de découpler l’évolution de vos bibliothèquesC++ C/Libraries, une C# liaison dans un NuGet et vos applications Xamarin. La fonctionnalité est fournie par la bibliothèque C/C++ Platform native, mais tout le code spécifique à la plateforme est isolé des applications Xamarin finales, ce qui permet d’optimiser les performances sans duplication de code. 
