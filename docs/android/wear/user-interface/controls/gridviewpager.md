---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 12e7e31cda9818a3cb2e2efc331a0be5d0c334e5
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740848"
---
# <a name="gridviewpager"></a>GridViewPager

Le [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) exemple montre comment implémenter le modèle de navigation 2D sélecteur pour Android Wear.

![Capture d’écran de GridViewPager sur un écran carré](gridviewpager-images/gridviewpager.png)

Tout d’abord ajouter le [Xamarin Android Wear prise en charge](https://www.nuget.org/packages/Xamarin.Android.Wear/) package NuGet à votre projet.

La mise en page XML ressemble à ceci :

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Créer un [`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(ou sous-classe comme [`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
pour fournir des vues pour afficher en tant que l’utilisateur navigue.

Le [exemple d’adaptateur](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) montre comment implémenter les méthodes requises, y compris des remplacements pour `RowCount`, `GetColumnCount`, `GetBackground`, et `GetFragment`

Associer l’adaptateur comme indiqué :

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Liens associés

- [Doc de sélecteur 2D de Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [Android.support.wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (sample)](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)
