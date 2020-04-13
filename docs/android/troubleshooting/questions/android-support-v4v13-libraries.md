---
title: Packages NuGet v4 / v13 de prise en charge Xamarin Android plus intelligents
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: c74cac6a6d669385855999a565711a3fdc85f3b7
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019535"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Packages NuGet v4 / v13 de prise en charge Xamarin Android plus intelligents

## <a name="about-the-android-support-libraries"></a>À propos des bibliothèques de support Android

Google a créé des bibliothèques de support pour mettre de nouvelles fonctionnalités à la disposition des anciennes versions d’Android. En général, les bibliothèques de support reçoivent un numéro de version en leur nom, qui est le niveau d’API Android le plus bas avec lequel elles sont compatibles (par exemple : Support-v4 ne peut être utilisé que sur le niveau 4 et plus de l’API. Plus d’infos dans cette [discussion Stack Overflow](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Deux des bibliothèques `Support-v4` de `Support-v13` support: et ne peuvent pas être utilisés ensemble dans la même application, c’est-à-dire, ils sont mutuellement exclusifs. C’est `Support-v13` parce que contient réellement `Support-v4`tous les types et la mise en œuvre de . Si vous essayez de référencer les deux dans le même projet, vous rencontrerez des erreurs de type double.

## <a name="problems-with-referencing"></a>Problèmes de référencement

Depuis `Support-v4` est devenu si populaire, beaucoup de bibliothèques de 3ème parti dépendent maintenant de lui. Ils auraient pu choisir de dépendre de Support-v13 à la place, mais il est plus fréquent de dépendre de _v4_ car cela donne à toutes les applications utilisant ces bibliothèques de 3e partie la possibilité de soutenir les niveaux D’API tout le chemin jusqu’à 4.

Si une bibliothèque Xamarin 3ème partie fait référence à la `Xamarin.Android.Support.v4.dll` liaison à `Support-v4`, toute application qui utilise cette bibliothèque doit également référence `Xamarin.Android.Support.v4.dll`. Cela devient un problème lorsque la même application veut également `Xamarin.Android.Support.v13.dll` utiliser `Support-v13`une partie de la fonctionnalité de la liaison à . Si vous faites référence aux deux liaisons, vous rencontrerez des erreurs de type double.

## <a name="type-forwarded-v4-binding-assembly"></a>Type-Forwarded v4 Binding Assembly

Pour contourner ce problème, nous `Xamarin.Android.Support.v4.dll` avons créé une assemblée `[assembly: TypeForwardedTo (..)]` spéciale qui n’a `Support-v4` pas de mise `Xamarin.Android.Support.v13.dll` en œuvre, mais qui attribue simplement qui transmet tous les types à la mise en œuvre au sein de l’assemblée.

Cela signifie qu’un développeur peut faire référence à cet assemblage transmis par _type_ dans son application qui satisfera la référence à `Xamarin.Android.Support.v4.dll` n’importe quelle bibliothèque de tiers, tout en permettant `Xamarin.Android.Support.v13.dll` d’être utilisé dans l’application.

## <a name="nuget-assistance"></a>Aide NuGet

Alors qu’un développeur pourrait ajouter manuellement les références correctes nécessaires, nous sommes en mesure d’utiliser NuGet pour aider à choisir le bon assemblage (soit la liaison _V4_ normale ou l’assemblage _v4_ de type) lorsque le paquet NuGet est installé.

Ainsi, `Xamarin.Android.Support.v4` le paquet NuGet contient maintenant la logique suivante:

Si votre application cible le niveau 13 de l’API (Pain d’épice 3.2) ou plus :

* `Xamarin.Android.Support.v13`NuGet sera automatiquement ajouté comme une dépendance
* Le _type transmis_ `Xamarin.Android.Support.v4.dll` sera référencé dans le projet

Si votre application cible quelque chose de inférieur à celui du `Xamarin.Android.Support.v4.dll` niveau 13 de l’API, vous obtiendrez la liaison normale référencée dans votre projet.

## <a name="do-i-have-to-use-support-v13"></a>Dois-je utiliser Support-v13 ?

Si votre application cible le niveau 13 ou plus `Xamarin Android Support-v4` de l’API `Xamarin Android Support v13` et que vous choisissez d’utiliser le forfait NuGet, le paquet NuGet est une dépendance requise.

Nous pensons que l’augmentation très mineure de la taille de l’application (les deux fichiers .jar diffèrent par 17kb) vaut bien la compatibilité et moins de maux de tête qu’il en résulte.

Si vous êtes catégorique `Support-v4` sur l’utilisation dans une application qui cible API niveau `.nupkg`13 ou plus, vous pouvez toujours télécharger manuellement le , l’extraire, et de référence de l’assemblage.
