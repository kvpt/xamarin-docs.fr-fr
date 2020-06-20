---
title: Qu’est-ce que c’est Xamarin.Forms ?
description: Cet article présente Xamarin.Forms les bibliothèques associées et.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eb76da9be7fcb227c465c0a046b967b2f70b1cfb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198307"
---
# <a name="what-is-xamarinforms"></a>Qu’est-ce que c’est Xamarin.Forms ?

[![Captures d’écran de l’exemple d' Xamarin.Forms application dans iOS et Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Formsest une infrastructure d’interface utilisateur Open source. Xamarin.Formspermet aux développeurs de générer des applications Xamarin. Android, Xamarin. iOS et Windows à partir d’un code base partagé unique.

Xamarin.Formspermet aux développeurs de créer des interfaces utilisateur en XAML avec code-behind en C#. Ces interfaces sont restituées sous la forme de contrôles natifs performants sur chaque plateforme.

## <a name="who-xamarinforms-is-for"></a>Qui Xamarin.Forms est pour

Xamarin.Formsest destiné aux développeurs qui ont les objectifs suivants :

- Partager la disposition et la conception de l’interface utilisateur entre les plateformes.
- Partager le code, les tests et la logique métier entre les plateformes.
- Écrire des applications multiplateformes en C# avec Visual Studio.

## <a name="how-xamarinforms-works"></a>Fonctionnement Xamarin.Forms

![Xamarin.Formsdiagramme d’architecture](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Formsfournit une API cohérente pour créer des éléments d’interface utilisateur sur plusieurs plateformes. Cette API peut être implémentée en XAML ou en C# et prend en charge la liaison de données pour les modèles comme MVVM (Modèle-vue-vue modèle).

Lors de l’exécution, Xamarin.Forms utilise les convertisseurs de plateforme pour convertir les éléments d’interface utilisateur multiplateforme en contrôles natifs sur Xamarin. Android, Xamarin. iOS et UWP. Ceci permet aux développeurs d’obtenir des performances et une apparence natives tout en profitant des avantages du partage de code entre les plateformes.

Xamarin.Formsles applications se composent généralement d’une bibliothèque de .NET Standard partagée et de projets de plateforme individuels. La bibliothèque partagée contient les vues XAML ou C# et une logique métier telle que des services, des modèles ou tout autre code. Les projets de plateforme contiennent une logique spécifique à la plateforme ou les packages dont a besoin l’application.

Xamarin.Formsutilise la plateforme Xamarin pour exécuter des applications .NET en mode natif sur plusieurs plateformes. Pour plus d’informations sur la plateforme Xamarin, consultez [qu’est-ce que Xamarin ?](~/get-started/what-is-xamarin.md).

## <a name="additional-functionality"></a>Fonctionnalités supplémentaires

Xamarin.Formsdispose d’un grand écosystème de bibliothèques qui ajoutent diverses fonctionnalités aux applications. Cette section décrit certaines de ces fonctionnalités supplémentaires.

### Xamarin.Essentials

Xamarin.Essentialsest une bibliothèque qui fournit des API multiplateforme pour les fonctionnalités d’appareils natifs. Comme Xamarin lui-même, Xamarin.Essentials est une abstraction qui simplifie le processus d’accès aux utilitaires natifs. Voici quelques exemples d’utilitaires fournis par Xamarin.Essentials :

- Informations sur l’appareil
- Système de fichiers
- Accéléromètre
- Numéroteur téléphonique
- Synthèse vocale
- Verrouillage d’écran

Pour plus d’informations, consultez [Xamarin.Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin.FormsL’interpréteur de commandes réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications. Voici quelques exemples de fonctionnalités que fournit Shell :

- Expérience de navigation courante
- Modèle de navigation basé sur les URI
- Gestionnaire de recherche intégré

Pour plus d’informations, consultez [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Spécificités des plateformes

Xamarin.Formsfournit une API commune qui restitue des contrôles natifs sur plusieurs plateformes, mais une plateforme spécifique peut avoir des fonctionnalités qui n’existent pas sur d’autres plateformes. Par exemple, la plateforme Android offre des fonctionnalités natives pour le défilement rapide dans un `ListView` alors qu’iOS n’en offre pas. Xamarin.Formsles spécificités des plateformes vous permettent d’utiliser des fonctionnalités qui sont uniquement disponibles sur une plateforme spécifique sans créer de convertisseurs ou d’effets personnalisés.

Xamarin.Formscomprend des solutions prédéfinies pour une large gamme de fonctionnalités spécifiques à la plateforme. Pour plus d'informations, consultez les pages suivantes :

- [Xamarin.Formsspécificités de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md)
- [Caractéristiques de la plateforme Android](~/xamarin-forms/platform/android/index.md)
- [caractéristiques de la plateforme iOS](~/xamarin-forms/platform/ios/index.md)
- [Spécificités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visuel matériau

Xamarin.FormsLe visuel matériau est utilisé pour appliquer des règles de conception de matériau aux Xamarin.Forms applications. Xamarin.FormsLe visuel matériau utilise la propriété visuelle pour appliquer de manière sélective des convertisseurs personnalisés à l’interface utilisateur, ce qui donne à une application une apparence cohérente entre iOS et Android.

Pour plus d’informations, consultez [ Xamarin.Forms élément visuel](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Liens connexes

- [Prise en main deXamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.FormsShell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.FormsÉlément visuel](~/xamarin-forms/user-interface/visual/material-visual.md)
