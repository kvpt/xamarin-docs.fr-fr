---
title: Comment faire migrer mon application vers Xamarin.Forms 5,0 ?
description: Comment migrer votre application vers Xamarin.Forms 5,0, avec la focalisation sur Android sur UWP.
ms.assetid: AD04FEE9-B8F5-4CA5-AB31-EF1225867E4B
ms.prod: xamarin
ms.technology: xamarin-forms
ms.topic: troubleshooting
author: davidbritch
ms.author: dabritch
ms.date: 10/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f93d20dac789abed57f8f41bf41778ad50a5fb5
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972316"
---
# <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>Comment faire migrer mon application vers Xamarin.Forms 5,0 ?

Xamarin.Forms 5,0 comprend les modifications avec rupture suivantes :

- `Expander` a été déplacé vers Xamarin Community Toolkit. Pour plus d’informations, consultez [fonctionnalités déplacées à partir de Xamarin.Forms ](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms).
- `MediaElement` a été déplacé vers Xamarin Community Toolkit. Pour plus d’informations, consultez [fonctionnalités déplacées à partir de Xamarin.Forms ](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms).
- Les DataPages et les projets associés ont été supprimés de Xamarin.Forms .
- `MasterDetailPage` a été renommé en `FlyoutPage`. De même, l' `MasterBehavior` énumération a été renommée en `FlyoutLayoutBehavior` .
- Les références à `UIWebView` ont été supprimées de Xamarin.Forms sur iOS.
- La prise en charge de Visual Studio 2017 a été supprimée.
- XFCorePostProcessor. Tasks a été supprimé. Ce projet a injecté le langage intermédiaire pour maintenir la Xamarin.Forms compatibilité 2,5.

En outre, les projets Android et UWP générés avec Xamarin.Forms 5,0 requièrent une mise à jour.

## <a name="android"></a>Téléphone

Les projets Android créés avec Xamarin.Forms 5,0 requièrent que vous ayez installé la plateforme AndroidX (Android 10,0) dans votre environnement de développement. Pour ce faire, vous pouvez utiliser le gestionnaire de Android SDK. Pour plus d’informations sur AndroidX, consultez [migration de Xamarin.Forms AndroidX dans ](~/xamarin-forms/platform/android/androidx-migration.md).

Les projets Android requièrent alors plusieurs mises à jour pour être correctement générées.

### <a name="minimum-targetframeworkversion"></a>TargetFrameworkVersion minimum

Xamarin.Forms 5,0 requiert une version de Framework cible minimale de 10,0 (AndroidX) pour les projets Android. La version cible du .NET Framework peut être définie dans Visual Studio ou dans le fichier Android. csproj :

```xml
<TargetFrameworkVersion>v10.0</TargetFrameworkVersion>
```

Une erreur de build sera générée si cette exigence minimale n’est pas remplie :

```
error XF005: The $(TargetFrameworkVersion) for MyProject.Android (v9.0) is less than the minimum required $(TargetFrameworkVersion) for Xamarin.Forms (10.0). You need to increase the $(TargetFrameworkVersion) for MyProject.Android.
```

### <a name="minimum-targetsdkversion"></a>TargetSDKVersion minimale

AndroidX requiert que votre manifeste Android affecte au la `targetSdkVersion` valeur 29 + :

```xml
<uses-sdk android:minSdkVersion="21" android:targetSdkVersion="29" />
```

Si vous ne le faites pas, le sera `targetSdkVersion` défini sur `minSdkVersion` . En outre, dans certains cas, une est `Android.Views.InflateException` générée si le `targetSdkVersion` n’est pas correctement défini :

```
Android.View.InflateException has been thrown.

Binary XML file line #1 in com.companyname.myproject:layout/toolbar: Binary XML file line #1 in com.companyname.myproject:/layout/toolbar: Error inflating class android.support.v7.widget.Toolbar
```

> [!NOTE]
> Dans Visual Studio 2019, le manifeste Android est automatiquement mis à jour pour spécifier un `targetSdkVersion` de l’API 29 lorsque la version cible du .NET Framework est définie sur v 10.0.

### <a name="automatic-migration-to-androidx"></a>Migration automatique vers AndroidX

Si votre projet Android fait référence à des bibliothèques de prise en charge Android, en tant que dépendances directes ou dépendances transitives, les dépendances et les liaisons de la bibliothèque de prise en charge sont automatiquement échangées avec les dépendances AndroidX pendant le processus de génération. Pour plus d’informations sur la migration automatique des AndroidX, consultez [migration automatique dans Xamarin.Forms ](~/xamarin-forms/platform/android/androidx-migration.md#automatic-migration-in-xamarinforms).

### <a name="manual-migration-to-androidx"></a>Migration manuelle vers AndroidX

Si votre projet Android n’a pas de dépendances directes ou transitives sur les bibliothèques de support Android, mais tente toujours d’utiliser des types de bibliothèque de prise en charge par le biais du code, vous devez migrer manuellement votre application vers AndroidX. Pour ce faire, vous pouvez utiliser les types AndroidX et supprimer tous les fichiers AXML que vous n’avez pas personnalisés.

> [!TIP]
> La migration manuelle vers AndroidX se traduira par le processus de génération le plus rapide pour votre application.

#### <a name="use-androidx-types"></a>Utiliser des types AndroidX

AndroidX remplace les bibliothèques de support Android. par conséquent, toutes les références aux types de bibliothèque de prise en charge Android doivent être remplacées par des références aux types AndroidX.

Pour ce faire, vous pouvez mettre à jour vos `using` instructions pour utiliser des `AndroidX` espaces de noms plutôt que des `Android.Support` espaces de noms. Le tableau suivant répertorie quelques-unes des modifications d’espace de noms courantes lors du passage des bibliothèques de prise en charge Android à AndroidX :

| Espace de noms de la bibliothèque de prise en charge Android | Espace de noms AndroidX |
| --- | --- |
| `Android.Support.V4.App` | `AndroidX.Core.App` |
| `Android.Support.V4.Content` | `AndroidX.Core.Content` |
| `Android.Support.V4.App` | `AndroidX.Fragment.App` |
| `Android.Support.V7.App` | `AndroidX.AppCompat.App` |
| `Android.Support.V7.Widget` | `AndroidX.AppCompat.Widget` |

Pour obtenir la liste complète des mappages de classe à partir des bibliothèques de prise en charge vers AndroidX, consultez [mappages de classes de bibliothèque de prise en charge](https://developer.android.com/jetpack/androidx/migrate/class-mappings) sur Developer.Android.com.

#### <a name="remove-axml-files"></a>Supprimer les fichiers AXML

Vous devez supprimer tous les fichiers AXML de votre projet Android, à condition qu’il n’utilise pas de fichiers AXML personnalisés. Après la suppression, les lignes suivantes doivent être supprimées de votre `MainActivity` classe :

```csharp
TabLayoutResource = Resource.Layout.Tabbar;
ToolbarResource = Resource.Layout.Toolbar;
```

> [!IMPORTANT]
> Les projets Android avec des fichiers AXML personnalisés doivent être mis à jour afin que ces fichiers utilisent des types AndroidX.

## <a name="uwp"></a>UWP

Xamarin.Forms 5,0 recommande une version de plateforme cible de >= 10.0.18362.0 pour les projets UWP. La version de la plateforme cible peut être définie dans Visual Studio ou dans le fichier UWP. csproj :

```xml
<TargetPlatformVersion Condition=" '$(TargetPlatformVersion)' == '' ">10.0.18362.0</TargetPlatformVersion>
```

Un avertissement de génération est généré si votre projet UWP utilise une version de plateforme cible inférieure.

## <a name="related-links"></a>Liens connexes

- [Fonctionnalités déplacées à partir de Xamarin.Forms](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms)
- [Migration de AndroidX dans Xamarin.Forms](~/xamarin-forms/platform/android/androidx-migration.md)
