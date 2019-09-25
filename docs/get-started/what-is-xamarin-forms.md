---
title: Qu’est-ce que Xamarin.Forms ?
description: Cet article présente Xamarin. Forms et les bibliothèques associées.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: 8bd530e743330ab8058f13cb7ba09f95a30ee886
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256580"
---
# <a name="what-is-xamarinforms"></a>Qu’est-ce que Xamarin.Forms ?

[![Captures d’écran de l’exemple d’application Xamarin. Forms dans iOS et Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin. Forms est une infrastructure d’interface utilisateur Open source. Xamarin. Forms permet aux développeurs de générer des applications Android, iOS et Windows à partir d’un code base partagé unique.

Xamarin. Forms permet aux développeurs de créer des interfaces utilisateur en XAML avec code C#-behind dans. Ces interfaces sont rendues sous la forme de contrôles natifs performants sur chaque plateforme.

## <a name="who-xamarinforms-is-for"></a>Pour qui Xamarin. Forms

Xamarin. Forms est destiné aux développeurs qui ont les objectifs suivants :

- Partager la disposition et la conception de l’interface utilisateur entre les plateformes.
- Partagez du code, des tests et de la logique métier entre les plateformes.
- Écrire des applications multiplateformes C# dans avec Visual Studio.

## <a name="how-xamarinforms-works"></a>Fonctionnement de Xamarin. Forms

![Diagramme d’architecture Xamarin. Forms](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin. Forms fournit une API cohérente pour créer des éléments d’interface utilisateur sur plusieurs plateformes. Cette API peut être implémentée en XAML ou C# et prend en charge la liaison de liaison pour des modèles tels que MVVM (Model-View-ViewModel).

Lors de l’exécution, Xamarin. Forms utilise les convertisseurs de plateforme pour convertir les éléments d’interface utilisateur multiplateforme en contrôles natifs sur Android, iOS et UWP. Le permet aux développeurs d’obtenir des performances et une apparence natives tout en profitant des avantages du partage de code entre les plateformes.

Les applications Xamarin. Forms se composent généralement d’une bibliothèque de .NET Standard partagée et de projets de plateforme individuels. La bibliothèque partagée contient le XAML ou C# les vues et toute logique métier telle que les services, les modèles ou tout autre code. Les projets de plateforme contiennent toute logique spécifique à la plateforme ou les packages requis par l’application.

Xamarin. Forms utilise Xamarin pour exécuter les applications .NET en mode natif entre les plateformes. Pour plus d’informations sur Xamarin, consultez [qu’est-ce que Xamarin ?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Outils supplémentaires

Xamarin. Forms dispose d’un grand écosystème de packages NuGet qui ajoutent diverses fonctionnalités aux applications. Cette section décrit quelques packages NuGet couramment utilisés.

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin. Essentials est une bibliothèque qui fournit des API multiplateforme pour les fonctionnalités d’appareils natives. Comme Xamarin lui-même, Xamarin. Essentials est une abstraction qui simplifie le processus d’accès aux utilitaires natifs. Voici quelques exemples d’utilitaires fournis par Xamarin. Essentials :

- Informations sur l’appareil
- Système de fichiers
- Accéléromètre
- Numéroteur téléphonique
- Conversion de texte par synthèse vocale
- Verrouillage de l’écran

Pour plus d’informations, consultez [Xamarin. Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin. Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications. Voici quelques exemples de fonctionnalités fournies par l’interpréteur de commandes :

- Expérience de navigation courante
- Schéma de navigation basé sur les URI
- Gestionnaire de recherche intégré

Pour plus d’informations, consultez [Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Spécificités de la plateforme

Xamarin. Forms fournit une API commune qui restitue les contrôles natifs sur les plateformes, mais une plateforme spécifique peut avoir des fonctionnalités qui n’existent pas sur d’autres plateformes. Par exemple, la plateforme Android offre des fonctionnalités natives pour le défilement rapide `ListView` dans une, contrairement à IOS. Les spécificités de la plateforme Xamarin. Forms vous permettent d’utiliser des fonctionnalités qui sont uniquement disponibles sur une plateforme spécifique sans créer de convertisseurs ou d’effets personnalisés.

Xamarin. Forms comprend des solutions prédéfinies pour une large gamme de fonctionnalités spécifiques à la plateforme. Pour plus d'informations, voir :

- [Xamarin. Forms-spécificités de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md)
- [Caractéristiques de la plateforme Android](~/xamarin-forms/platform/android/index.md)
- [caractéristiques de la plateforme iOS](~/xamarin-forms/platform/ios/index.md)
- [Spécificités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visuel matériau

Xamarin. Forms Material Visual est utilisé pour appliquer des règles de conception de matériau aux applications Xamarin. Forms. L’élément visuel Xamarin. Forms Material utilise la propriété visuelle pour appliquer de manière sélective des convertisseurs personnalisés à l’interface utilisateur, ce qui donne à une application une apparence cohérente entre iOS et Android.

Pour plus d’informations, consultez [Xamarin. Forms, élément visuel](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Liens connexes

- [Prise en main de Xamarin. Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Interpréteur de commandes Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Élément visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual/material-visual.md)
