---
title: Utilisation de bibliothèques natives
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: b7d69e99327aa3d3e3e1f5e5dbc61697d1fb9b71
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75489165"
---
# <a name="using-native-libraries"></a>Utilisation de bibliothèques natives

Xamarin.Android prend en charge l’utilisation des bibliothèques indigènes via le mécanisme PInvoke standard. Vous pouvez également regrouper d’autres bibliothèques indigènes qui ne font pas partie du système d’exploitation dans votre .apk.

Pour déployer une bibliothèque native avec une application Xamarin.Android, ajoutez le binaire de la bibliothèque au projet et définissez son **action de construction** à **AndroidNativeLibrary**.

Pour déployer une bibliothèque native avec un projet de bibliothèque Xamarin.Android, ajoutez la bibliothèque binaire au projet et définissez son **action de construction** à **EmbeddedNativeLibrary**.

Notez que depuis Android prend en charge plusieurs interfaces binaires d’application (ABIs), Xamarin.Android doit savoir pour qui ABI la bibliothèque native est construite.
Vous pouvez faire cela de deux façons :

1. Chemin "sniffing"
1. En utilisant `AndroidNativeLibrary/Abi` un élément dans le fichier du projet

Avec la détection de chemin, le nom du répertoire parent de la bibliothèque native est utilisé pour spécifier l’ABI ciblée par la bibliothèque. Ainsi, si `lib/armeabi/libfoo.so` vous ajoutez au projet, alors l’ABI `armeabi`sera "sniffed" comme .

Vous pouvez également modifier votre fichier de projet pour spécifier explicitement l’ABI à utiliser :

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Pour plus d’informations sur l’utilisation des bibliothèques autochtones, voir [Interop avec les bibliothèques autochtones](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Code indigène de débogage avec Visual Studio

Si vous utilisez *Visual Studio 2019* ou *Visual Studio 2017,* vous n’avez pas à modifier vos fichiers de projet comme décrit ci-dessus.
Vous pouvez construire et déboiffer CMD à l’intérieur de votre solution Xamarin.Android en ajoutant une référence de projet à un projet **de bibliothèque partagée dynamique (Android).**

Pour déboiffer le code CMD natif de votre projet, suivez ces étapes :

1. Double clic **projet Propriétés** et sélectionnez la page **Options Android.**
2. Faites défiler vers le bas pour **les options Debugging**.
3. Dans le menu **Debugger** dropdown, sélectionnez **C (au** lieu de la valeur par défaut **.NET (Xamarin)**).

Les développeurs visual Studio CMD peuvent voir [l’échantillon SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) pour essayer de déboguer le CMD de Visual Studio 2019 ou Visual Studio 2017 avec Xamarin ; et se référer à notre [blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) pour plus d’informations.

## <a name="related-links"></a>Liens connexes

- [SanAngeles_NativeDebug (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Développer Xamarin Android Native Applications](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
