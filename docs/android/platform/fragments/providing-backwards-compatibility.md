---
title: Fournir une compatibilité à l’envers avec le forfait de support Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027297"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Fournir une compatibilité à l’envers avec le forfait de support Android

L’utilité des fragments serait limitée sans compatibilité à l’envers avec les appareils pré-Android 3.0 (API Level 11). Pour fournir cette capacité, Google a présenté la [bibliothèque de soutien](https://developer.android.com/sdk/compatibility-library.html) (à l’origine appelé la bibliothèque de *compatibilité Android* quand il a été libéré) qui soutient certaines des API de nouvelles versions d’Android à des versions plus anciennes d’Android. Il s’agit du forfait de support Android qui permet aux appareils fonctionnant sous Android 1.6 (niveau API 4) d’Android 2.3.3. (niveau API 10).

> [!NOTE]
> Seuls `ListFragment` les `DialogFragment` et les sont disponibles via le paquet de support Android. Aucune des autres sous-classes Fragment, `PreferenceFragment,` comme les sont pris en charge dans le package de support Android. Ils ne fonctionneront pas dans les applications pré-Android 3.0. 

## <a name="adding-the-support-package"></a>Ajout du paquet de soutien

Le forfait de support Android n’est pas automatiquement ajouté à une application Xamarin.Android. Xamarin fournit le [forfait Android Support Library v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pour simplifier l’ajout des bibliothèques de support à une application Xamarin.Android. Pour inclure les paquets de support dans votre application Xamarin.Android inclure le composant [Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) dans votre projet Xamarin.Android, comme illustré dans la capture d’écran suivante: 

[![Capture d’écran du paquet Android Support Library v4 ajouté au projet](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Une fois ces étapes effectuées, il devient possible d’utiliser Fragments dans les versions antérieures d’Android. Les API Fragment fonctionneront de la même façon maintenant dans ces versions antérieures, à quelques exceptions près : 

- **Modifier la version** &ndash; Android minimale L’application n’a plus besoin de cibler Android 3.0 ou plus, comme indiqué ci-dessous: 

    [![Capture d’écran de la cible Android minimum étant fixé sous Android Manifest](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Étendre FragmentActivity** &ndash; Les activités qui hébergent `Android.Support.V4.App.FragmentActivity` fragments doivent `Android.App.Activity` maintenant hériter de , et non de . 

- **Mettre à jour Namespaces** &ndash; Classes qui héritent de `Android.App.Fragment` doit maintenant hériter de `Android.Support.V4.App.Fragment` . Supprimer la déclaration `using Android.App;` d’utilisation " en haut du fichier `using Android.Support.V4.App` de code source et le remplacer par " ". 

- **Utiliser SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expose `SupportingFragmentManager` une propriété qui doit être `FragmentManager` utilisée pour obtenir une référence à la . Par exemple : 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Avec ces changements en place, il sera possible d’exécuter une application basée sur Fragment sur Android 1.6 ou 2.x ainsi que sur Honeycomb et Ice Cream Sandwich. 

## <a name="related-links"></a>Liens connexes

- [Bibliothèque de support Android v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
