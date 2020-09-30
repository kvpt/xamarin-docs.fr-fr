---
title: Xamarin.Forms Double écran
description: Ce guide explique comment créer Xamarin.Forms des applications pour les appareils à deux écrans.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1d2a443a6005050c518e21e4e0f2df64c2aab0c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562624"
---
# <a name="no-locxamarinforms-dual-screen"></a>Xamarin.Forms double écran

Les périphériques à deux écrans comme Microsoft surface Duo facilitent les nouvelles possibilités d’expérience utilisateur pour vos applications. Xamarin.Forms comprend `TwoPaneView` les `DualScreenInfo` classes et pour vous permettre de développer des applications pour les appareils à deux écrans.

## <a name="get-started"></a>Prise en main

Pour ajouter des fonctionnalités à deux écrans à une application, procédez comme suit Xamarin.Forms :

1. Ouvrez la boîte de dialogue **Gestionnaire de package NuGet** pour votre solution.
2. Sous l’onglet **Parcourir**, recherchez `Xamarin.Forms.DualScreen`.
3. Installez le `Xamarin.Forms.DualScreen` package dans votre solution.
4. Ajoutez l’appel de méthode d’initialisation suivant à la classe du projet Android `MainActivity` , dans l' `OnCreate` événement :

    ```csharp
    Xamarin.Forms.DualScreen.DualScreenService.Init(this);
    ```

    Cette méthode est requise pour permettre à l’application de détecter les modifications de l’état de l’application, par exemple sur deux écrans.

5. Mettez à jour l' `Activity` attribut sur la classe du projet Android `MainActivity` , afin qu’il comprenne _toutes les_ `ConfigurationChanges` options suivantes :

    ```@csharp
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation
        | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.UiMode
    ```

    Ces valeurs sont nécessaires pour que les modifications de configuration et l’état d’étendue puissent être signalés de manière plus fiable. Par défaut, seuls deux sont ajoutés aux Xamarin.Forms projets. n’oubliez pas d’ajouter le reste pour une prise en charge fiable du double écran.

## <a name="troubleshooting"></a>Résolution des problèmes

Si la `DualScreenInfo` classe ou la `TwoPaneView` mise en page ne fonctionne pas comme prévu, consultez les instructions de configuration sur cette page. L’omission ou la configuration indéfinie `Init` de la méthode ou des `ConfigurationChanges` valeurs d’attribut sont des causes courantes des erreurs.

Consultez les [ Xamarin.Forms exemples à double écran](/dual-screen/xamarin/samples) pour obtenir des instructions et une implémentation de référence supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez ajouté le NuGet, ajoutez les fonctionnalités à deux écrans à votre application en suivant les instructions ci-dessous :

- [Modèles de conception à deux écrans](design-patterns.md) : Si vous envisagez d’utiliser au mieux plusieurs écrans sur un appareil à deux écrans, reportez-vous à ce guide de modèles pour trouver le meilleur choix pour votre interface d’application.
- [Disposition TwoPaneView](twopaneview.md) : la Xamarin.Forms `TwoPaneView` classe, inspirée par le contrôle UWP du même nom, est une disposition multiplateforme optimisée pour les appareils à deux écrans.
- [Classe d’assistance DualScreenInfo](dual-screen-info.md) : la `DualScreenInfo` classe vous permet de déterminer le volet sur lequel se trouve votre affichage, sa taille, la position de l’appareil, l’angle de la charnière et bien plus encore.
- [Déclencheurs](triggers.md) à deux écrans : l' [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) espace de noms comprend deux déclencheurs d’État qui déclenchent une [`VisualState`](xref:Xamarin.Forms.VisualState) modification lorsque le mode d’affichage de la disposition attachée, ou fenêtre, change.

Pour plus d’informations, consultez les documents sur les [développeurs à deux écrans](/dual-screen/) .