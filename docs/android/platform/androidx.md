---
title: AndroidX
description: Comment commencer à développer des applications avec AndroidX à l’aide de Xamarin. Android.
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291640"
---
# <a name="androidx-with-xamarin"></a>AndroidX avec Xamarin

_Comment commencer à développer des applications avec AndroidX à l’aide de Xamarin. Android._

AndroidX est une amélioration majeure de la bibliothèque de support Android d’origine, qui n’est plus gérée. Les packages **AndroidX** remplacent entièrement la bibliothèque de prise en charge Android en fournissant la parité des fonctionnalités et de nouvelles bibliothèques que vous pouvez utiliser dans vos applications Android.

AndroidX comprend les fonctionnalités suivantes :

- Tous les packages à l’intérieur de AndroidX ont maintenant un espace de noms cohérent commençant par `androidx`. Cela signifie que tous les packages de bibliothèque de prise en charge Android sont mappés à un package de `androidx.*` correspondant.
- les packages de `androidx` sont gérés et mis à jour séparément. Cela signifie que vous pouvez mettre à jour les bibliothèques AndroidX indépendamment les unes des autres.
- À compter de la V28 de la bibliothèque de support Android, il n’y aura plus de version finale. Tout le développement sera inclus dans `androidx` à la place.

![Logo AndroidX](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>Configuration requise

La liste suivante est requise pour utiliser les fonctionnalités AndroidX dans les applications basées sur Xamarin :

- **Visual Studio** -sur Windows Update vers visual studio 2019 version 16,4 ou ultérieure. Sur macOS, mettez à jour vers Visual Studio 2019 pour Mac version 8,4 ou ultérieure.
- **Xamarin. Android** -Xamarin. Android 10,0 ou version ultérieure doit être installé avec Visual Studio (Xamarin. Android est installé automatiquement dans le cadre du **développement mobile avec** la charge de travail .net sur Windows et installé dans le cadre du **programme d’installation de Visual Studio pour Mac**)
- **Kit** de développement Java-le développement Xamarin. Android 10,0 requiert JDK 8. La distribution de Microsoft OpenJDK est automatiquement installée dans le cadre de Visual Studio.
- **Android SDK** -Android SDK l’API 28 ou une version ultérieure doit être installée via le gestionnaire de Android SDK.

## <a name="get-started"></a>Prise en main

Vous pouvez commencer à utiliser AndroidX en incluant n’importe quel [package NuGet AndroidX](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) à l’intérieur de votre projet Android. En savoir plus sur l’installation et l’utilisation d’un package dans [Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) ou [Visual Studio pour Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)

## <a name="behavior-changes"></a>Changements de comportement

Étant donné que AndroidX est une nouvelle conception de la bibliothèque de prise en charge Android, il comprend des étapes de migration qui affectent les applications Android créées avec la bibliothèque de prise en charge Android.

### <a name="package-name-change"></a>Modification du nom du package
Les noms des packages ont été modifiés entre l’ancien et le nouveau package. Vous trouverez ci-dessous un exemple de ces modifications :

| Ancien                    | Nouveau                    |
| ---------------------- | ---------------------- |
| Android. support. * *     | androidx. @             |
| Android. Design. * *      | com. google. Android. Material. @ |
| Android. support. test. * * | androidx. test. @       |
| Android. Arch. * *        | androidx. @             |
| Android. Arch. persistance. Room. * * | androidx. Room. @ |
| Android. Arch. persistance. * * | androidx. sqlite. @ |

Pour plus d’informations sur la dénomination des packages, [consultez la documentation suivante](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings).

## <a name="migration-tooling"></a>Outils de migration

Il existe trois étapes de migration que vous souhaitez connaître pour votre application.

1. Si votre application comprend des espaces de noms de la **bibliothèque de prise en charge Android et que vous souhaitez les migrer vers des espaces de noms AndroidX**, vous pouvez utiliser nos outils de **migration vers** l’IDE AndroidX pour prendre en charge la plupart des scénarios d’espace de noms. 

Activez **AndroidX Migrator** via **outils > Options > Xamarin > paramètres Android** dans Visual Studio 2019 (vous pouvez ignorer cette étape sur Visual Studio pour Mac).

![Activer AndroidX Migrator](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

Cliquez avec le bouton droit sur votre projet et **migrez vers AndroidX**.

![Migrer vers AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> Vous devrez apporter des modifications manuelles à l’espace de noms pour les scénarios que l’outil ne couvre pas. Alors que nous allons mapper le package approprié pour vous, il est recommandé de jeter un coup d’œil aux mappages d' [artefacts](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings) et aux [mappages de classe](https://developer.android.com/jetpack/androidx/migrate/class-mappings) officiels pour faciliter la migration de votre projet.

2. Si votre application comprend **des dépendances qui n’ont pas été migrées vers l’espace de noms AndroidX**, vous devez utiliser la [bibliothèque de prise en charge Android pour AndroidX le package de migration.](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. Si votre application **n’inclut pas de dépendances nécessitant une migration d’espace de noms AndroidX**, vous pouvez utiliser les [bibliothèques AndroidX sur NuGet dès aujourd’hui](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22).

## <a name="troubleshooting"></a>Résolution des problèmes

- Certains packages d’architecture dans AndroidX sont en conflit avec les versions de la bibliothèque de prise en charge. Pour résoudre ce problème, vous devez utiliser la version AndroidX de ces packages et supprimer la version de la bibliothèque de prise en charge. Par exemple, si vous référencez `Xamarin.Android.Arch.Work.Runtime` dans votre projet, il sera en conflit avec les types du package d' `AndroidX.Work` que vous venez d’ajouter.

## <a name="summary"></a>Résumé

Cet article a présenté AndroidX et a expliqué comment installer et configurer les outils et packages les plus récents pour le développement Xamarin. Android avec AndroidX. Il a fourni une vue d’ensemble de ce que AndroidX est. Elle contient des liens vers des rubriques de documentation sur les API et des développeurs Android qui vous aideront à créer des applications à l’aide de AndroidX. Elle a également mis en évidence les modifications de comportement AndroidX les plus importantes et les rubriques de dépannage susceptibles d’avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens connexes

- [Présentation de AndroidX | Xamarin afficher](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Référentiel Xamarin AndroidX GitHub](https://github.com/xamarin/AndroidX)
- [Référentiel GitHub de migration Xamarin AndroidX](https://github.com/xamarin/XamarinAndroidXMigration)
