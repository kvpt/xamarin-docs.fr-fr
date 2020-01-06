---
title: Qu’est-ce que Xamarin. Forms?
description: Cet article présente Xamarin. Formulaires et bibliothèques associées.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: c217acdc3bd5c007822cc29fc730df99e373ba01
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607839"
---
# <a name="what-is-opno-locxamarinforms"></a>Qu’est-ce que Xamarin. Forms?

[![captures d’écran de l’exemple [ ! Opérationnel. NO-LOC (Xamarin)]. Application Forms dans iOS et Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin. Forms est une infrastructure d’interface utilisateur Open source. Xamarin. Les formulaires permettent aux développeurs de générer des applications Android, iOS et Windows à partir d’un code base partagé unique.

Xamarin. Forms permet aux développeurs de créer des interfaces utilisateur en XAML avec code C#-behind dans. Ces interfaces sont rendues sous la forme de contrôles natifs performants sur chaque plateforme.

## <a name="who-opno-locxamarinforms-is-for"></a>Qui Xamarin. Formulaires pour

Xamarin. Forms est destiné aux développeurs qui ont les objectifs suivants :

- Partager la disposition et la conception de l’interface utilisateur entre les plateformes.
- Partagez du code, des tests et de la logique métier entre les plateformes.
- Écrire des applications multiplateformes C# dans avec Visual Studio.

## <a name="how-opno-locxamarinforms-works"></a>Comment Xamarin. Les formulaires fonctionnent

![[! Opérationnel. NO-LOC (Xamarin)]. Diagramme d’architecture des formulaires](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin. Forms fournit une API cohérente pour créer des éléments d’interface utilisateur sur plusieurs plateformes. Cette API peut être implémentée en XAML ou C# et prend en charge la liaison de liaison pour des modèles tels que MVVM (Model-View-ViewModel).

Au moment de l’exécution, Xamarin. Forms utilise les convertisseurs de plate-forme pour convertir les éléments d’interface utilisateur multiplateforme en contrôles natifs sur Android, iOS et UWP. Le permet aux développeurs d’obtenir des performances et une apparence natives tout en profitant des avantages du partage de code entre les plateformes.

Xamarin. Les applications de formulaires se composent généralement d’une bibliothèque de .NET Standard partagée et de projets de plateforme individuels. La bibliothèque partagée contient le XAML ou C# les vues et toute logique métier telle que les services, les modèles ou tout autre code. Les projets de plateforme contiennent toute logique spécifique à la plateforme ou les packages requis par l’application.

Xamarin. Forms utilise Xamarin pour exécuter des applications .NET en mode natif sur plusieurs plateformes. Pour plus d’informations sur Xamarin, voir [qu’est-ce que Xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Outils supplémentaires

Xamarin. Les formulaires ont un vaste écosystème de packages NuGet qui ajoutent diverses fonctionnalités aux applications. Cette section décrit quelques packages NuGet couramment utilisés.

### <a name="opno-locxamarinessentials"></a>Xamarin. Essentials

Xamarin. Essentials est une bibliothèque qui fournit des API multiplateforme pour les fonctionnalités d’appareils natives. Comme Xamarin lui-même, Xamarin. Essentials est une abstraction qui simplifie le processus d’accès aux utilitaires natifs. Voici quelques exemples d’utilitaires fournis par Xamarin. Les éléments essentiels sont les suivants :

- Informations sur l’appareil
- Système de fichiers
- Accéléromètre
- Numéroteur téléphonique
- Conversion de texte par synthèse vocale
- Verrouillage de l’écran

Pour plus d’informations, consultez [Xamarin. Notions de bases](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin. Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications. Voici quelques exemples de fonctionnalités fournies par l’interpréteur de commandes :

- Expérience de navigation courante
- Schéma de navigation basé sur les URI
- Gestionnaire de recherche intégré

Pour plus d’informations, consultez [Xamarin. Shell de formulaires](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Spécificités de la plateforme

Xamarin. Forms fournit une API commune qui restitue les contrôles natifs sur les plateformes, mais une plateforme spécifique peut avoir des fonctionnalités qui n’existent pas sur d’autres plateformes. Par exemple, la plateforme Android offre des fonctionnalités natives pour le défilement rapide dans un `ListView` mais iOS ne le fait pas. Xamarin. Les spécificités des plateformes de formulaires vous permettent d’utiliser des fonctionnalités qui sont uniquement disponibles sur une plateforme spécifique sans créer de convertisseurs ou d’effets personnalisés.

Xamarin. Forms comprend des solutions prédéfinies pour une large gamme de fonctionnalités spécifiques à la plateforme. Pour plus d'informations, consultez .

- [Xamarin. Caractéristiques de la plateforme de formulaires](~/xamarin-forms/platform/platform-specifics/index.md)
- [Caractéristiques de la plateforme Android](~/xamarin-forms/platform/android/index.md)
- [caractéristiques de la plateforme iOS](~/xamarin-forms/platform/ios/index.md)
- [Spécificités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visuel matériau

Xamarin. L’élément visuel formulaires est utilisé pour appliquer des règles de conception de matériau à Xamarin. Applications de formulaires. Xamarin. L’élément visuel formulaires utilise la propriété visuelle pour appliquer de manière sélective des convertisseurs personnalisés à l’interface utilisateur, ce qui donne à une application une apparence cohérente entre iOS et Android.

Pour plus d’informations, consultez [Xamarin. Élément visuel de forme](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Liens connexes

- [Prise en main de Xamarin. Forms](~/xamarin-forms/index.yml)
- [Xamarin. Essentials](~/essentials/index.md)
- [Xamarin. Shell de formulaires](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin. Élément visuel de forme](~/xamarin-forms/user-interface/visual/material-visual.md)
