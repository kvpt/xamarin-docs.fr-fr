---
title: Qu’est-ce que Xamarin.Forms ?
description: Cet article présente Xamarin.Forms et les bibliothèques associées.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: aaceb6089a5b7e5f0551dafe9ef1fe50d01433d9
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77374021"
---
# <a name="what-is-xamarinforms"></a>Qu’est-ce que Xamarin.Forms ?

[![Captures d’écran d’un exemple d’application Xamarin.Forms dans iOS et Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Forms est un framework d’interface utilisateur open source. Xamarin.Forms permet aux développeurs de générer des applications Android, iOS et Windows à partir d’une seule base de code partagée.

Xamarin.Forms permet aux développeurs de créer des interfaces utilisateur en XAML avec du code-behind en C#. Ces interfaces sont restituées sous la forme de contrôles natifs performants sur chaque plateforme.

## <a name="who-xamarinforms-is-for"></a>À qui s’adresse Xamarin.Forms

Xamarin.Forms est destiné aux développeurs qui ont les objectifs suivants :

- Partager la disposition et la conception de l’interface utilisateur entre les plateformes.
- Partager le code, les tests et la logique métier entre les plateformes.
- Écrire des applications multiplateformes en C# avec Visual Studio.

## <a name="how-xamarinforms-works"></a>Comment fonctionne Xamarin.Forms

![Diagramme de l’architecture de Xamarin.Forms](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Forms fournit une API cohérente qui permet de créer des éléments d’interface utilisateur sur plusieurs plateformes. Cette API peut être implémentée en XAML ou en C# et prend en charge la liaison de données pour les modèles comme MVVM (Modèle-vue-vue modèle).

Lors de l’exécution, Xamarin.Forms utilise les renderers de plateforme pour convertir les éléments d’interface utilisateur multiplateformes en contrôles natifs sur Android, iOS et UWP. Ceci permet aux développeurs d’obtenir des performances et une apparence natives tout en profitant des avantages du partage de code entre les plateformes.

Les applications Xamarin.Forms se composent généralement d’une bibliothèque .NET Standard partagée et de projets de plateforme individuels. La bibliothèque partagée contient les vues XAML ou C# et une logique métier telle que des services, des modèles ou tout autre code. Les projets de plateforme contiennent une logique spécifique à la plateforme ou les packages dont a besoin l’application.

Xamarin.Forms utilise Xamarin pour exécuter les applications .NET en mode natif sur les plateformes. Pour plus d’informations sur Xamarin, consultez [Qu’est-ce que Xamarin ?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Outils supplémentaires

Xamarin.Forms dispose d’un grand écosystème de packages NuGet qui ajoutent diverses fonctionnalités aux applications. Cette section décrit quelques packages NuGet couramment utilisés.

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin.Essentials est une bibliothèque qui fournit des API multiplateformes pour les fonctionnalités d’appareil natives. Tout comme Xamarin, Xamarin.Essentials est une abstraction qui simplifie le processus d’accès aux utilitaires natifs. Voici quelques exemples d’utilitaires fournis par Xamarin.Essentials :

- Informations sur l’appareil
- Système de fichiers
- Accéléromètre
- Numéroteur téléphonique
- Conversion de texte par synthèse vocale
- Verrouillage d’écran

Pour plus d’informations, consultez [Xamarin.Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin.Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications. Voici quelques exemples de fonctionnalités que fournit Shell :

- Expérience de navigation courante
- Modèle de navigation basé sur les URI
- Gestionnaire de recherche intégré

Pour plus d’informations, consultez [Shell Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

### <a name="platform-specifics"></a>Spécificités des plateformes

Xamarin.Forms fournit une API commune qui restitue les contrôles natifs sur les plateformes, mais une plateforme donnée peut avoir des fonctionnalités qui n’existent pas sur d’autres plateformes. Par exemple, la plateforme Android offre des fonctionnalités natives pour le défilement rapide dans un `ListView` alors qu’iOS n’en offre pas. Les spécificités de la plateforme Xamarin.Forms vous permettent d’utiliser des fonctionnalités qui sont uniquement disponibles sur une plateforme spécifique sans créer de renderers ou d’effets personnalisés.

Xamarin.Forms comprend des solutions prédéfinies pour diverses fonctionnalités spécifiques à la plateforme. Pour plus d'informations, voir :

- [Spécificités de la plateforme Xamarin.Forms](~/xamarin-forms/platform/platform-specifics/index.md)
- [Spécificités de la plateforme Android](~/xamarin-forms/platform/android/index.md)
- [Spécificités de la plateforme iOS](~/xamarin-forms/platform/ios/index.md)
- [Spécificités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visuel matériau

Xamarin.Forms Material Visual est utilisé pour appliquer les règles de Material Design aux applications Xamarin.Forms. Xamarin.Forms Material Visual utilise la propriété Visual pour appliquer de manière sélective des renderers personnalisés à l’interface utilisateur en vue de donner à une application une apparence cohérente entre iOS et Android.

Pour plus d’informations, consultez [Xamarin.Forms Material Visual](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Liens connexes

- [Bien démarrer avec Xamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Interpréteur de commandes Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.Forms Material Visual](~/xamarin-forms/user-interface/visual/material-visual.md)
