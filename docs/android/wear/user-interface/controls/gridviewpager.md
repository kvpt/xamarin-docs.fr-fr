---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: d924bac99b4edc5f41afd024d56fb0c6ace2613d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647636"
---
# <a name="gridviewpager"></a>GridViewPager

L’exemple [GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager) montre comment implémenter le modèle de navigation de sélecteur 2D pour Android.

![Exemple de capture d’écran de GridViewPager sur un écran carré](gridviewpager-images/gridviewpager.png)

Tout d’abord, ajoutez le package NuGet de [prise en charge de l’usure Xamarin Android](https://www.nuget.org/packages/Xamarin.Android.Wear/) à votre projet.

Le XML de disposition se présente comme suit:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Créer un[`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(ou sous-classe, par exemple[`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
pour fournir des vues à afficher lorsque l’utilisateur navigue.

L' [exemple d’adaptateur](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) montre comment implémenter les méthodes requises, y compris les substitutions `GetColumnCount`pour `GetBackground` `RowCount`,, et.`GetFragment`

Connectez l’adaptateur comme indiqué ci-dessous:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Liens associés

- [Document de sélecteur 2D de Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [Android. support. documents en portable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)
