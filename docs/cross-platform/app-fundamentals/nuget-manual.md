---
title: Créer manuellement des Packages NuGet pour Xamarin
description: Ce document contient des conseils pour aider à créer des packages NuGet qui ciblent la plateforme Xamarin. Il décrit des profils de Xamarin de package NuGet, packages NuGet de bibliothèque de classes portable avec des dépendances de plateforme et des liens vers divers exemples open source.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 2f66d8a3960741643013a1010162f52d283026d6
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855709"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Créer manuellement des Packages NuGet pour Xamarin

_Cette page contient des conseils pour aider à créer des packages NuGet qui ciblent la plateforme Xamarin._

> [!NOTE]
> Xamarin Studio 6.2 (et Visual Studio pour Mac) incluent la possibilité de _automatiquement_ générer des packages NuGet à partir de la bibliothèque de classes portable, .NET Standard ou les projets partagés. Reportez-vous à la [bibliothèques multiplateformes pour le partage de Code](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) guide pour plus d’informations.

## <a name="nuget-package-xamarin-profiles"></a>Profils de Xamarin de Package NuGet

Du site Web NuGet [prenant en charge plusieurs Versions du .NET Framework et les profils](https://docs.nuget.org/create/enforced-package-conventions) explique comment prendre en charge différentes infrastructures de Microsoft et de profils, mais n’inclut pas les noms de framework cible utilisés par Xamarin.

Les frameworks cibles Xamarin principales en cours d’utilisation sont aujourd'hui :

* **MonoAndroid** - Xamarin.Android
* **Xamarin.iOS** -Xamarin.iOS [API unifiée](~/cross-platform/macios/unified/index.md) (prend en charge 64 bits)
* **Xamarin.Mac** -profil mobile de Xamarin.Mac, ce qui équivaut à la surface Xamarin.iOS et Xamarin.Android API.

Il existe également une cible pour les anciens iOS [API classique](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -Classic API iOS

Un **.nuspec** fichier ciblant tous ces ressemblent à :

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

La méthode ci-dessus ignore les bibliothèques de classes portables.

La plupart des **.nuspec** fichiers spécifient le numéro de version du framework cible, mais il est facultatif si votre assembly fonctionne avec toutes les versions de ce framework cible. Par conséquent, si votre cible a été **lib\MonoAndroid** cela signifierait qu’il fonctionne avec n’importe quelle version de Xamarin.Android.

Vous pouvez spécifier la version avec un ensemble de nombres sans virgule décimale, ou vous pouvez spécifier à l’aide de la virgule décimale. Sans la virgule décimale NuGet sera simplement prendre chaque nombre et le transformer en une version en insérant un '.' entre chaque chiffre.

Dans le ci-dessus « MonoAndroid10 » signifie « Android 1.0 ». Cela signifie simplement que le projet [framework cible](~/android/app-fundamentals/android-api-levels.md) doit être MonoAndroid version 1.0 ou version ultérieure. La version est spécifiée dans le `<TargetFrameworkVersion>` élément dans le fichier projet.

Pour préciser :

- **MonoAndroid403** correspond à Android 4.0.3 et versions ultérieures (ie API niveau 15)
- **Xamarin.iOS10** correspond à Xamarin.iOS 1.0 et versions ultérieures
- **Xamarin.iOS1.0** correspond également à Xamarin.iOS 1.0 et versions ultérieures

## <a name="pcl-nugets-with-platform-dependencies"></a>Packages NuGet de bibliothèque de classes portable avec des dépendances de plateforme

Profils de bibliothèque de classes portable sont limités dans l’API, ils peuvent accéder à .NET framework, et ils certainement ne peut pas y accéder code spécifique à la plateforme. Ces liens 3 rd-party traitent des différentes approches de création de packages NuGet qui utilisent la bibliothèque de classes portable et des API natives pour assurer la compatibilité pour Xamarin et d’autres plateformes :

- [Comment rendre le travail de bibliothèques de classes portables pour vous](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [L’astuce de la bibliothèque de classes portable soit](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Création d’une PCL NuGet qui fonctionne avec Xamarin.iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Cette externe [liste des profils de bibliothèque de classes portable avec leur nom de la cible NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) constitue également une référence utile.

## <a name="examples"></a>Exemples

Voici quelques exemples open source que vous pouvez faire référence à :

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) : écrire votre application à l’aide de System.Net.Http, mais supprimer cette bibliothèque dans et vous accéderez considérablement plus rapide (vue [source](https://github.com/paulcbetts/ModernHttpClient)).
- [**Césure** ](https://www.nuget.org/packages/Splat/) – une bibliothèque pour rendre les choses inter-plateformes qui doit être (vue [source](https://github.com/paulcbetts/Splat)).
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -une bibliothèque multiplateforme pour le rendu des graphiques vectoriels sur .NET (vue [source](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Liens associés

- [Nugetizer 3000 automatisée de la création de Nuget](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [Inclusion d’un package NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
