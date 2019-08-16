---
title: Packages NuGet v4 / v13 de prise en charge Xamarin Android plus intelligents
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 64ceacc37a303e69b0dd7073ec6547ed344af51d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523433"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Packages NuGet v4 / v13 de prise en charge Xamarin Android plus intelligents

## <a name="about-the-android-support-libraries"></a>À propos des bibliothèques de support Android

Google a créé des bibliothèques de prise en charge pour rendre les nouvelles fonctionnalités disponibles dans les versions antérieures d’Android. En général, les bibliothèques de prise en charge reçoivent un numéro de version dans leur nom, qui est le niveau d’API Android le plus bas avec lequel elles sont compatibles (par exemple: Prise en charge-v4 peut uniquement être utilisé sur l’API de niveau 4 et versions ultérieures. Plus d’informations dans cette [Stack Overflow discussion](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Deux des bibliothèques de prise en `Support-v4` charge `Support-v13` : et ne peuvent pas être utilisées ensemble dans la même application, autrement dit, elles s’excluent mutuellement. Cela est dû `Support-v13` au fait que contient en fait tous les types `Support-v4`et l’implémentation de. Si vous essayez de faire référence à la fois dans le même projet, vous rencontrerez des erreurs de type dupliqué.

## <a name="problems-with-referencing"></a>Problèmes de référencement

Étant `Support-v4` donné que est devenu si populaire, un grand nombre de bibliothèques tierces en dépendent maintenant. Ils auraient pu choisir de dépendre de la prise en charge de v13, mais il est plus courant de dépendre de _v4_ , car cela permet à toutes les applications utilisant ces bibliothèques tierces de prendre en charge les niveaux d’API jusqu’à 4.

Si une bibliothèque tierce Xamarin fait référence à `Xamarin.Android.Support.v4.dll` la liaison `Support-v4`à, toute application qui utilise cette bibliothèque doit également `Xamarin.Android.Support.v4.dll`faire référence à. Cela devient problématique lorsque la même application souhaite également utiliser certaines fonctionnalités de la `Xamarin.Android.Support.v13.dll` liaison à. `Support-v13` Si vous référencez les deux liaisons, vous rencontrerez des erreurs de type dupliqué.

## <a name="type-forwarded-v4-binding-assembly"></a>Assembly de liaison v4 transférée par type

Pour `Xamarin.Android.Support.v4.dll` contourner ce problème, nous avons créé un assembly spécial qui n’a pas d’implémentation, mais simplement `[assembly: TypeForwardedTo (..)]` des attributs `Support-v4` qui transfèrent tous les types à `Xamarin.Android.Support.v13.dll` l’implémentation au sein de l’assembly.

Cela signifie qu’un développeur peut faire référence à cet assembly _transféré par type_ dans son application, qui `Xamarin.Android.Support.v4.dll` répondra à la référence de par toutes les `Xamarin.Android.Support.v13.dll` bibliothèques tierces, tout en autorisant l’utilisation dans l’application.

## <a name="nuget-assistance"></a>Assistance NuGet

Si un développeur peut ajouter manuellement les références appropriées nécessaires, nous pouvons utiliser NuGet pour choisir l’assembly approprié (soit la liaison _v4_ normale, soit l’assembly _v4_ transmis par type) lors de l’installation du package NuGet.

Par conséquent, `Xamarin.Android.Support.v4` le package NuGet contient désormais la logique suivante:

Si votre application cible le niveau d’API 13 (Gingerbread 3,2) ou une version ultérieure:

* `Xamarin.Android.Support.v13`NuGet sera automatiquement ajouté en tant que dépendance
* Le _type-forwarded_ `Xamarin.Android.Support.v4.dll` sera référencé dans le projet

Si votre application cible une valeur inférieure au niveau d’API 13, vous obtiendrez la `Xamarin.Android.Support.v4.dll` liaison normale référencée dans votre projet.

## <a name="do-i-have-to-use-support-v13"></a>Dois-je utiliser support-v13?

Si votre application cible le niveau d’API 13 ou une version ultérieure et que vous `Xamarin Android Support-v4` choisissez d’utiliser le package `Xamarin Android Support v13` NuGet, le package NuGet est une dépendance requise.

Nous pensons que l’augmentation de la taille de l’application est très mineure (les deux fichiers. jar diffèrent par 17kb).

Si vous catégorique d’utiliser `Support-v4` dans une application qui cible le niveau d’API 13 ou supérieur, vous pouvez toujours télécharger manuellement le, l' `.nupkg`extraire et référencer l’assembly.
