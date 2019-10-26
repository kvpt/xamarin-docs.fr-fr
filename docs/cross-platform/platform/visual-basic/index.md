---
title: Visual Basic et .NET Standard
description: Ce guide explique comment vous pouvez utiliser Visual Basic pour écrire des projets .NET Standard qui peuvent être utilisés dans des solutions ciblant Xamarin. iOS et Xamarin. Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: conceptdev
ms.author: crdun
ms.date: 04/24/2019
ms.openlocfilehash: 1e58dd4d7310c7f903433ce0b84123c39fbb5c8e
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959115"
---
# <a name="visual-basic-and-net-standard"></a>Visual Basic et .NET Standard

Les projets Xamarin Android et iOS ne prennent pas en charge en mode natif Visual Basic ; Toutefois, les développeurs peuvent utiliser des bibliothèques de [.NET standard](~/cross-platform/app-fundamentals/net-standard.md) pour migrer du code Visual Basic existant vers Android et iOS, ou écrire une partie importante de leur logique d’application dans Visual Basic. Les applications Xamarin. Forms peuvent être créées entièrement dans Visual Basic (à l’exception des convertisseurs personnalisés, des services de dépendance et du code-behind XAML).

## <a name="requirements"></a>spécifications

Pour créer et compiler des bibliothèques de .NET Standard Visual Basic vous devez utiliser Visual Studio sur Windows (Visual Studio 2017 ou version ultérieure).

> [!NOTE]
> Les bibliothèques de Visual Basic peuvent uniquement être créées et compilées à l’aide de Visual Studio. Xamarin. Android et Xamarin. iOS ne prennent pas en charge le langage de Visual Basic.
>
> Si vous travaillez uniquement dans Visual Studio, vous pouvez référencer le projet Visual Basic à partir de projets Xamarin. Android et Xamarin. iOS.
>
> Si vos projets Android et iOS doivent également être chargés dans Visual Studio pour Mac vous devez référencer l’assembly de sortie à partir de l’assembly Visual Basic.

## <a name="creating-a-visual-basicnet-net-standard-library"></a>Création d’une bibliothèque Visual Basic.NET .NET Standard

Cette section explique comment créer une bibliothèque Visual Basic .NET Standard à l’aide de Visual Studio 2019.
La bibliothèque peut ensuite être référencée dans d’autres projets, y compris les applications Xamarin. Android, Xamarin. iOS et Xamarin. Forms.

Quand vous ajoutez une bibliothèque de .NET Standard Visual Basic dans Visual Studio, vous devez veiller à choisir le type de projet correct :

1. Dans Visual Studio 2019, choisissez **créer un nouveau projet**.

2. Tapez **Visual Basic bibliothèque** pour filtrer les options du projet et choisissez l’option **bibliothèque de classes (.NET standard)** avec l’icône Visual Basic :

    [Filtre de![pour la bibliothèque de Visual Basic](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

3. Dans l’écran suivant, tapez un nom pour le projet et appuyez sur **créer**.

4. Le projet Visual Basic s’affiche comme indiqué dans le **Explorateur de solutions** comme suit :

    [![projet Visual Basic vide](images/new-library-sml.png)](images/new-library.png#lightbox)

Le projet est maintenant prêt pour l’ajout de code Visual Basic. Les projets .NET Standard peuvent être référencés par d’autres projets (projets d’application ou projets de bibliothèque).

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser Visual Basic code dans les applications Xamarin à l’aide de Visual Studio. Même si Xamarin ne prend pas en charge directement Visual Basic, la compilation des Visual Basic dans une bibliothèque de .NET Standard permet d’inclure du code écrit avec Visual Basic dans des applications Android et iOS.

Les pages suivantes décrivent comment utiliser les bibliothèques Visual Basic.NET .NET Standard dans des applications natives ou Xamarin. Forms :

- [Création d’applications natives Xamarin. iOS et Xamarin. Android qui utilisent VB](native-apps.md)
- [Création d’applications Xamarin. Forms avec VB](xamarin-forms.md)

## <a name="related-links"></a>Liens associés

- [TaskyVB (exemple)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)
- [XamarinFormsVB (exemple)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [.NET Standard et Xamarin](~/cross-platform/app-fundamentals/net-standard.md)
- [.NET Standard](/dotnet/standard/net-standard/)
