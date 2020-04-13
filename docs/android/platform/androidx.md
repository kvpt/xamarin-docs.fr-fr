---
title: AndroidX (androidX)
description: Comment commencer à développer des applications avec AndroidX en utilisant Xamarin.Android.
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291640"
---
# <a name="androidx-with-xamarin"></a>AndroidX avec Xamarin

_Comment commencer à développer des applications avec AndroidX en utilisant Xamarin.Android._

AndroidX est une amélioration majeure de l’original Android Support Library, qui n’est plus maintenue. Les forfaits **AndroidX** remplacent entièrement la bibliothèque de support Android en fournissant la parité des fonctionnalités et les nouvelles bibliothèques que vous pouvez utiliser dans vos applications Android.

AndroidX comprend les fonctionnalités suivantes :

- Tous les paquets à l’intérieur `androidx`d’AndroidX ont maintenant un espace de nom cohérent à partir de . Cela signifie que tous les paquets Android Support Library carte à un paquet correspondant. `androidx.*`
- `androidx`sont conservés séparément et mis à jour. Cela signifie que vous pouvez mettre à jour les bibliothèques AndroidX indépendamment les unes des autres.
- À partir de v28 de la bibliothèque de soutien Android, il n’y aura plus de communiqués. Tous les développements `androidx` seront inclus à la place.

![AndroidX Logo](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>Spécifications

La liste suivante est nécessaire pour utiliser les fonctionnalités AndroidX dans les applications basées sur Xamarin :

- **Visual Studio** - Sur windows mise à jour de Visual Studio 2019 version 16.4 ou plus tard. Sur macOS, mise à jour sur Visual Studio 2019 pour mac version 8.4 ou plus tard.
- **Xamarin.Android** - Xamarin.Android 10.0 ou plus tard doit être installé avec Visual Studio (Xamarin.Android est automatiquement installé dans le cadre du développement mobile avec la charge de travail **.NET** sur Windows et installé dans le cadre du **studio visuel pour Mac Installer**)
- **Java Developer Kit** - Xamarin.Android 10.0 développement nécessite JDK 8. La distribution de Microsoft de l’OpenJDK est automatiquement installée dans le cadre de Visual Studio.
- **Android SDK** - Android SDK API 28 ou plus doit être installé via le gestionnaire Android SDK.

## <a name="get-started"></a>Prise en main

Vous pouvez commencer avec AndroidX en incluant n’importe quel [paquet AndroidX NuGet](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) à l’intérieur de votre projet Android. En savoir plus sur l’installation et l’utilisation d’un forfait dans [Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) ou [Visual Studio pour Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)

## <a name="behavior-changes"></a>Changements de comportement

Parce qu’AndroidX est une refonte de la bibliothèque de support Android, il comprend des étapes de migration qui affecteront les applications Android construites avec la bibliothèque de support Android.

### <a name="package-name-change"></a>Changement de nom de paquet
Les noms de paquets ont été changés entre les anciens et les nouveaux paquets. Vous trouverez ci-dessous un exemple de ces modifications :

| Vieux                    | Nouveau                    |
| ---------------------- | ---------------------- |
| android.support.     | Androidx. @             |
| android.design.      | com.google.android.material. @ |
| android.support.test. | androidx.test. @       |
| android.arch.        | Androidx. @             |
| android.arch.persistence.room. | androidx.room. @ |
| android.arch.persistence. | androidx.sqlite. @ |

Pour plus de détails sur la dénomination du paquet, [voir la documentation suivante](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings).

## <a name="migration-tooling"></a>Outillage de migration

Il y a trois étapes de migration que vous voudrez être au courant de votre application.

1. Si votre application inclut des **espaces de nom Android Support Library et que vous souhaitez les migrer vers les espaces nominaux AndroidX,** vous pouvez utiliser notre **outil Migrate vers AndroidX** IDE pour prendre soin de la plupart des scénarios d’espace de nom. 

Activez le **migreur AndroidX** via **Tools > Options > Xamarin > Paramètres Android** à l’intérieur de Visual Studio 2019 (vous pouvez sauter cette étape sur Visual Studio pour Mac).

![Activez AndroidX Migrator](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

Cliquez à droite sur votre projet et **Migrez vers AndroidX**.

![Migrer vers AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> Vous aurez besoin de faire quelques modifications manuelles namespace pour les scénarios de l’outil ne couvre pas. Bien que nous cartographions le paquet correct pour vous, il est encouragé que vous jetiez un coup d’oeil aux [cartographies](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings) officielles d’artefacts et aux [cartographies de classe](https://developer.android.com/jetpack/androidx/migrate/class-mappings) pour aider votre projet à migration.

2. Si votre application comprend **des dépendances qui n’ont pas été migrées vers l’espace de nom AndroidX,** vous devrez utiliser la [bibliothèque de support Android pour androidX Migration forfait.](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. Si votre application **n’inclut pas de dépendances qui nécessitent la migration d’espace de nom AndroidX,** vous pouvez utiliser les [bibliothèques AndroidX sur NuGet aujourd’hui](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22).

## <a name="troubleshooting"></a>Dépannage

- Certains forfaits d’architecture au sein d’AndroidX entreront en conflit avec les versions Support Library. Pour résoudre ce problème, vous devez utiliser la version AndroidX de ces paquets et supprimer la version Bibliothèque de support. Par exemple, si vous `Xamarin.Android.Arch.Work.Runtime` faites référence à votre projet, il `AndroidX.Work` sera en conflit avec les types du paquet nouvellement ajouté.

## <a name="summary"></a>Récapitulatif

Cet article a introduit AndroidX et expliqué comment installer et configurer les derniers outils et paquets pour le développement Xamarin.Android avec AndroidX. Il a fourni un aperçu de ce qu’est AndroidX. Il comprenait des liens vers la documentation API et des sujets de développeur Android pour vous aider à démarrer dans la création d’applications en utilisant AndroidX. Il a également mis en évidence les changements de comportement AndroidX les plus importants et les sujets de dépannage qui pourraient avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens connexes

- [Introduction à AndroidX ( Le Xamarin Show](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX (androidX)](https://developer.android.com/jetpack/androidx)
- [Dépôt Xamarin AndroidX GitHub](https://github.com/xamarin/AndroidX)
- [Xamarin AndroidX Migration GitHub Repository](https://github.com/xamarin/XamarinAndroidXMigration)
