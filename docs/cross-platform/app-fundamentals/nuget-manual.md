---
title: Création manuelle de packages NuGet pour Xamarin
description: Ce document contient des conseils pour vous aider à générer des packages NuGet qui ciblent la plateforme Xamarin. Il décrit les profils Xamarin de package NuGet, les packages NuGet PCL avec les dépendances de plateforme et les liens vers divers exemples Open source.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: cf694b54c8d2cdb33fd480d89d32b439f036ddc5
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119445"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Création manuelle de packages NuGet pour Xamarin

_Cette page contient des conseils pour vous aider à générer des packages NuGet qui ciblent la plateforme Xamarin._

> [!NOTE]
> Xamarin Studio 6,2 (et Visual Studio pour Mac) offre la possibilité de générer _automatiquement_ des packages NuGet à partir de bibliothèques PCL, .NET standard ou partagées. Pour plus d’informations, consultez le guide des [bibliothèques multiplateformes pour le partage de code](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) .

## <a name="nuget-package-xamarin-profiles"></a>Profils Xamarin de package NuGet

Le site Web NuGet [prenant en charge plusieurs versions et profils de .NET Framework](https://docs.nuget.org/create/enforced-package-conventions) explique comment prendre en charge différents profils et infrastructures Microsoft, mais n’inclut pas les noms de Framework cibles utilisés par Xamarin.

Les principales infrastructures cibles Xamarin en cours d’utilisation sont les suivantes:

- **Monoandroid** -Xamarin. Android
- **Xamarin. iOS** -Xamarin. iOS [API unifiée](~/cross-platform/macios/unified/index.md) (prend en charge 64 bits)
- Le profil Mobile **Xamarin. Mac** -Xamarin. Mac, qui est équivalent à la surface de l’API Xamarin. iOS et Xamarin. Android.

Il existe également une cible pour l’ancien [API classique](~/cross-platform/macios/unified/index.md)iOS:

- API classique monocontact-iOS

Un fichier **. NuSpec** ciblant tous ces éléments ressemble à ce qui suit:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

La version ci-dessus ignore toutes les bibliothèques de classes portables.

La plupart des fichiers **. NuSpec** spécifient le numéro de version de la version cible de .NET Framework, mais il est facultatif si votre assembly fonctionne avec toutes les versions de ce Framework cible. Par conséquent, si votre cible était **lib\MonoAndroid** , cela signifierait qu’elle fonctionne avec n’importe quelle version de Xamarin. Android.

Vous pouvez spécifier la version avec un ensemble de nombres sans virgule décimale, ou vous pouvez la spécifier à l’aide de points décimaux. Sans la virgule décimale, NuGet prend chaque nombre et le transforme en une version en insérant un «.» entre chaque chiffre.

Dans le «MonoAndroid10» ci-dessus, «Android 1,0». Cela signifie simplement que le [Framework cible](~/android/app-fundamentals/android-api-levels.md) du projet doit être monoandroid version 1,0 ou supérieure. La version est spécifiée dans l' `<TargetFrameworkVersion>` élément dans le fichier projet.

Pour clarifier:

- **MonoAndroid403** correspond à Android 4.0.3 et versions ultérieures (IE API de niveau 15)
- **Xamarin. iOS10** correspond à Xamarin. iOS 1,0 et versions ultérieures
- **Xamarin. iOS 1.0** correspond également à Xamarin. iOS 1,0 et versions ultérieures

## <a name="pcl-nugets-with-platform-dependencies"></a>Packages NuGet PCL avec dépendances de plateforme

Les profils PCL sont limités dans les API .NET Framework auxquelles ils peuvent accéder, et ils ne peuvent certainement pas accéder au code spécifique à la plateforme. Ces liens tiers décrivent les différentes approches de création de packages NuGet qui utilisent des API PCL et natives pour assurer la compatibilité avec Xamarin et d’autres plateformes:

- [Comment faire fonctionner les bibliothèques de classes portables pour vous](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [L’appât et le commutateur de la astuce PCL](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Création d’un PCL NuGet qui fonctionne avec Xamarin. iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Cette [liste externe de profils PCL avec leur nom de cible NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) est également une référence utile.

## <a name="examples"></a>Exemples

Voici quelques exemples Open source auxquels vous pouvez faire référence:

- [**ModernHttpClient**](https://www.nuget.org/packages/modernhttpclient/) : écrivez votre application à l’aide de System .net. http, mais déposez cette bibliothèque dans et elle sera considérablement plus rapide (afficher la [source](https://github.com/paulcbetts/ModernHttpClient)).
- [**Se terminant par**](https://www.nuget.org/packages/Splat/) : bibliothèque pour faire des choses multiplateformes qui doivent être (afficher la [source](https://github.com/paulcbetts/Splat)).
- [**NGraphics**](https://www.nuget.org/packages/NGraphics/) : bibliothèque multiplateforme pour le rendu des graphiques vectoriels sur .net (afficher la [source](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Liens associés

- [Nugetizer-3000 création de NuGet automatisée](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [Inclusion d’un NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
