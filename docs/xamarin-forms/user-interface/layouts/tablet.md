---
title: Mise en page pour les applications de bureau et tablette
description: Cet article explique comment optimiser Xamarin.Forms les dispositions des applications pour les tablettes, par opposition aux téléphones.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0ecbc850960465296dc4047277bdafe78ac800a4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573246"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Mise en page pour les applications de bureau et tablette

Xamarin.Formsprend en charge tous les types d’appareils disponibles sur les plateformes prises en charge. ainsi, en plus des téléphones, les applications peuvent également s’exécuter sur :

- iPad
- Tablettes Android,
- Tablettes et ordinateurs de bureau Windows (exécutant Windows 10).

Cette page présente brièvement les éléments suivants :

- les [types d’appareils](#device-types)pris en charge, et
- [optimisation](#optimize-for-tablet-and-desktop) des dispositions pour les tablettes et les téléphones.

## <a name="device-types"></a>Types d’appareils

Des appareils à écran plus volumineux sont disponibles pour toutes les plateformes prises en charge par Xamarin.Forms .

### <a name="ipads-ios"></a>iPad (iOS)

Le Xamarin.Forms modèle comprend automatiquement la prise en charge de iPad en configurant le paramètre **info. plist > Devices** sur **universel** (ce qui signifie que iPhone et iPad sont pris en charge).

Pour fournir une expérience de démarrage agréable et garantir que la résolution plein écran est utilisée sur tous les appareils, vous devez vous assurer qu’un [écran de lancement spécifique à iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (à l’aide d’un Storyboard) est fourni. Cela garantit l’affichage correct de l’application sur les appareils iPad mini, iPad et iPad Pro.

Avant iOS 9, toutes les applications remontent en mode plein écran sur l’appareil, mais certains iPad peuvent désormais effectuer des [tâches multitâches fractionnées](~/ios/platform/multitasking.md).
Cela signifie que votre application peut utiliser simplement une colonne fine sur le côté de l’écran, 50% de la largeur de l’écran ou l’écran entier.

[![](tablet-images/ipad-sml.png "iPad Split Screen Example")](tablet-images/ipad.png#lightbox "iPad Split Screen Example")

La fonctionnalité de fractionnement d’écran signifie que vous devez concevoir votre application pour qu’elle fonctionne correctement avec un minimum de 320 pixels de largeur, ou jusqu’à 1366 pixels de largeur.

### <a name="android-tablets"></a>Tablettes Android

L’écosystème Android offre une multitude de tailles d’écran prises en charge, allant des petits téléphones jusqu’aux tablettes volumineuses. Xamarin.Formspeut prendre en charge toutes les tailles d’écran, mais comme avec les autres plateformes, vous souhaiterez peut-être ajuster votre interface utilisateur pour les périphériques plus volumineux.

Lorsque vous prenez en charge de nombreuses résolutions d’écran différentes, vous pouvez fournir vos ressources d’images natives dans différentes tailles pour optimiser l’expérience utilisateur.
Consultez la documentation des [ressources Android](~/android/app-fundamentals/resources-in-android/index.md) (et, en particulier, la [création de ressources pour différentes tailles d’écran](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) pour plus d’informations sur la façon de structurer les dossiers et les noms de fichiers dans votre projet d’application Android afin d’inclure des ressources d’image optimisées dans votre application.

### <a name="windows-tablets-and-desktops"></a>Tablettes et ordinateurs de bureau Windows

Pour prendre en charge les tablettes et les ordinateurs de bureau exécutant Windows, vous devez utiliser la [prise en charge de Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), qui crée des applications universelles qui s’exécutent sur Windows 10.

Les applications qui s’exécutent sur des tablettes et des postes de travail Windows peuvent être redimensionnées en dimensions arbitraires, en plus de s’exécuter en mode plein écran.

[![](tablet-images/splitscreen-sml.png "Windows Split Screen Example")](tablet-images/splitscreen.png#lightbox "Windows Split Screen Example")

## <a name="optimize-for-tablet-and-desktop"></a>Optimiser pour tablette et bureau

Vous pouvez ajuster votre Xamarin.Forms interface utilisateur en fonction de l’utilisation ou non d’un téléphone ou d’un appareil de bureau. Cela signifie que vous pouvez optimiser l’expérience utilisateur pour les appareils à écran large tels que les tablettes et les ordinateurs de bureau.

### <a name="deviceidiom"></a>Appareil. idiome

Vous pouvez utiliser la [`Device`](~/xamarin-forms/platform/device.md) classe pour modifier le comportement de votre application ou de votre interface utilisateur. À l’aide de l' `Device.Idiom` énumération, vous pouvez

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Cette approche peut être développée pour apporter des modifications significatives à des dispositions de page individuelles, ou même pour restituer des pages entièrement différentes sur des écrans plus grands.

### <a name="leverage-masterdetailpage"></a>Tirer parti de MasterDetailPage

Le [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) est idéal pour les écrans plus grands, en particulier sur l’iPad où il utilise le [`UISplitViewController`](xref:UIKit.UISplitViewController) pour fournir une expérience iOS native.

Consultez [ce billet de blog Xamarin](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/) pour découvrir comment adapter votre interface utilisateur afin que les téléphones utilisent une disposition et les écrans plus grands puissent utiliser une autre (avec `MasterDetailPage` ).

## <a name="related-links"></a>Liens connexes

- [Blog Xamarin](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)
- [Exemple MyShoppe](https://github.com/jamesmontemagno/myshoppe)
