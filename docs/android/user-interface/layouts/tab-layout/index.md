---
title: Dispositions à onglets
description: Vue d’ensemble des dispositions à onglets dans Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/08/2017
ms.openlocfilehash: 4ca4200d0f9036ed76e20e3a1840303e7bb3b7e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028791"
---
# <a name="tabbed-layouts"></a>Dispositions à onglets

## <a name="overview"></a>Vue d'ensemble

Les onglets sont un modèle d’interface utilisateur populaire dans les applications mobiles en raison de leur simplicité et de leur convivialité. Ils offrent un moyen cohérent et facile de naviguer entre les différents écrans d’une application. Android possède plusieurs API pour les interfaces avec onglets : 

- **Barre** &ndash; cela fait partie d’un nouvel ensemble d’API qui a été introduit dans Android 3,0 (niveau d’API 11), avec l’objectif de fournir une interface de navigation et de basculement de vue cohérente. Il a été porté sur Android 2,2 (API de niveau 8) avec la [bibliothèque de prise en charge Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

- **PagerTabStrip** &ndash; indique les pages actuelle, suivante et précédente d’un `ViewPager`. `ViewPager` est disponible uniquement par le biais de la [bibliothèque de prise en charge Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Pour plus d’informations sur `PagerTabStrip`, consultez [ViewPager](~/android/user-interface/controls/view-pager/index.md).

- **Toolbar** &ndash; `Toolbar` est un composant de barre d’action plus récent et plus flexible qui remplace `ActionBar`. `Toolbar` est disponible dans Android 5,0 Lollipop ou version ultérieure, et il est également disponible pour les versions antérieures d’Android via le package NuGet de la [bibliothèque de prise en charge Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) . 
    `Toolbar` est actuellement le composant de barre d’action recommandé à utiliser dans les applications Android.
    Pour plus d’informations, consultez [Toolbar](~/android/user-interface/controls/tool-bar/index.md). 

## <a name="related-links"></a>Liens associés

- [Conception de matériau-onglets](https://material.io/guidelines/components/tabs.html)- [barre](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Bibliothèque de prise en charge Android v7 paquet NuGet AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [Bibliothèque AppCompat v7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
