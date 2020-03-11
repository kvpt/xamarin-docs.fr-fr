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
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019535"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Packages NuGet v4 / v13 de prise en charge Xamarin Android plus intelligents

## <a name="about-the-android-support-libraries"></a>À propos des bibliothèques de support Android

Google a créé des bibliothèques de prise en charge pour rendre les nouvelles fonctionnalités disponibles dans les versions antérieures d’Android. En général, les bibliothèques de prise en charge reçoivent un numéro de version dans leur nom, qui est le niveau d’API Android le plus bas avec lequel elles sont compatibles (par exemple, la prise en charge de v4 ne peut être utilisée que sur l’API de niveau 4 et versions ultérieures. Plus d’informations dans cette [Stack Overflow discussion](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Deux des bibliothèques de prise en charge : `Support-v4` et `Support-v13` ne peuvent pas être utilisées ensemble dans la même application, autrement dit, elles s’excluent mutuellement. Cela est dû au fait que `Support-v13` contient en fait tous les types et l’implémentation de `Support-v4`. Si vous essayez de faire référence à la fois dans le même projet, vous rencontrerez des erreurs de type dupliqué.

## <a name="problems-with-referencing"></a>Problèmes de référencement

Étant donné que `Support-v4` est devenu si populaire, beaucoup de bibliothèques tierces en dépendent maintenant. Ils auraient pu choisir de dépendre de la prise en charge de v13, mais il est plus courant de dépendre de _v4_ , car cela permet à toutes les applications utilisant ces bibliothèques tierces de prendre en charge les niveaux d’API jusqu’à 4.

Si une bibliothèque tierce Xamarin fait référence à la liaison de `Xamarin.Android.Support.v4.dll` à `Support-v4`, toute application qui utilise cette bibliothèque doit également référencer `Xamarin.Android.Support.v4.dll`. Cela pose un problème lorsque la même application souhaite également utiliser certaines fonctionnalités de la liaison de `Xamarin.Android.Support.v13.dll` à `Support-v13`. Si vous référencez les deux liaisons, vous rencontrerez des erreurs de type dupliqué.

## <a name="type-forwarded-v4-binding-assembly"></a>Assembly de liaison v4 transférée par type

Pour contourner ce problème, nous avons créé un assembly `Xamarin.Android.Support.v4.dll` spécial qui n’a aucune implémentation, mais qui `[assembly: TypeForwardedTo (..)]` simplement des attributs qui transfèrent tous les types de `Support-v4` à l’implémentation au sein de l’assembly `Xamarin.Android.Support.v13.dll`.

Cela signifie qu’un développeur peut référencer cet assembly _transféré par type_ dans son application, ce qui répond à la référence à `Xamarin.Android.Support.v4.dll` par les bibliothèques tierces, tout en autorisant l’utilisation de `Xamarin.Android.Support.v13.dll` dans l’application.

## <a name="nuget-assistance"></a>Assistance NuGet

Si un développeur peut ajouter manuellement les références appropriées nécessaires, nous pouvons utiliser NuGet pour choisir l’assembly approprié (soit la liaison _v4_ normale, soit l’assembly _v4_ transmis par type) lors de l’installation du package NuGet.

Ainsi, le package NuGet `Xamarin.Android.Support.v4` contient désormais la logique suivante :

Si votre application cible le niveau d’API 13 (Gingerbread 3,2) ou une version ultérieure :

* `Xamarin.Android.Support.v13` NuGet sera automatiquement ajouté en tant que dépendance
* Le `Xamarin.Android.Support.v4.dll` _transféré par type_ sera référencé dans le projet

Si votre application cible une valeur inférieure au niveau d’API 13, vous obtiendrez la liaison de `Xamarin.Android.Support.v4.dll` normale référencée dans votre projet.

## <a name="do-i-have-to-use-support-v13"></a>Dois-je utiliser support-v13 ?

Si votre application cible le niveau d’API 13 ou une version ultérieure et que vous choisissez d’utiliser le package NuGet `Xamarin Android Support-v4`, le package NuGet `Xamarin Android Support v13` est une dépendance requise.

Nous pensons que l’augmentation de la taille de l’application est très mineure (les deux fichiers. jar diffèrent par 17kb).

Si vous catégorique de l’utilisation de `Support-v4` dans une application qui cible le niveau d’API 13 ou supérieur, vous pouvez toujours télécharger manuellement le `.nupkg`, l’extraire et référencer l’assembly.
