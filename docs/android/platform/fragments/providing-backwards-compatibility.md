---
title: Offre une compatibilité descendante avec le package de support Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027297"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Offre une compatibilité descendante avec le package de support Android

L’utilité des fragments est limitée sans compatibilité descendante avec les appareils pré-Android 3,0 (niveau d’API 11). Pour fournir cette fonctionnalité, Google a introduit la [bibliothèque de prise en charge](https://developer.android.com/sdk/compatibility-library.html) (appelée initialement la *bibliothèque de compatibilité Android* lorsqu’elle a été publiée) qui intègre certaines des API des versions plus récentes d’Android aux anciennes versions d’Android. Il s’agit du package de support Android qui permet aux appareils exécutant Android 1,6 (niveau d’API 4) d’Android 2.3.3. (Niveau d’API 10).

> [!NOTE]
> Seuls les `ListFragment` et les `DialogFragment` sont disponibles via le package de support Android. Aucune des autres sous-classes de fragments, telles que les `PreferenceFragment,` sont prises en charge dans le package de support Android. Ils ne fonctionnent pas dans les applications pré-Android 3,0. 

## <a name="adding-the-support-package"></a>Ajout du package de support

Le package de support Android n’est pas automatiquement ajouté à une application Xamarin. Android. Xamarin fournit le [package NuGet v4 de la bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pour simplifier l’ajout des bibliothèques de prise en charge à une application Xamarin. Android. Pour inclure les packages de prise en charge dans votre application Xamarin. Android, incluez le composant v4 de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) dans votre projet Xamarin. Android, comme illustré dans la capture d’écran suivante : 

[Capture d’écran ![de l’ajout du package v4 de la bibliothèque de prise en charge Android au projet](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Une fois ces étapes effectuées, il devient possible d’utiliser des fragments dans les versions antérieures d’Android. Les API de fragments fonctionnent de la même façon dans ces versions antérieures, avec les exceptions suivantes : 

- **Modifiez la version minimale d’android** &ndash; l’application n’a plus besoin de cibler Android 3,0 ou une version ultérieure, comme indiqué ci-dessous : 

    [Capture d’écran ![de la cible Android minimale définie sous le manifeste Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Étendre FragmentActivity** &ndash; les activités qui hébergent des fragments doivent désormais hériter de `Android.Support.V4.App.FragmentActivity` et non de `Android.App.Activity`. 

- **Mettre à jour les espaces de noms** &ndash; les classes qui héritent de `Android.App.Fragment` doivent désormais hériter de `Android.Support.V4.App.Fragment`. Supprimez l’instruction using « `using Android.App;` » en haut du fichier de code source et remplacez-la par « `using Android.Support.V4.App` ». 

- **Utilisez SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expose une propriété `SupportingFragmentManager` qui doit être utilisée pour obtenir une référence au `FragmentManager`. Par exemple : 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Une fois ces modifications en place, il est possible d’exécuter une application basée sur des fragments sur Android 1,6 ou 2. x, ainsi que sur le sandwich de glace et de glace. 

## <a name="related-links"></a>Liens connexes

- [Bibliothèque de prise en charge Android v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
