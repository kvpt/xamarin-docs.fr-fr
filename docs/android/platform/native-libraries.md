---
title: Utilisation de bibliothèques natives
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 7ef9e0415d7d1e5fe75be70e0ccf6e06a5eaf332
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027061"
---
# <a name="using-native-libraries"></a>Utilisation de bibliothèques natives

Xamarin. Android prend en charge l’utilisation de bibliothèques natives via le mécanisme PInvoke standard. Vous pouvez également regrouper des bibliothèques natives supplémentaires qui ne font pas partie du système d’exploitation dans votre. apk.

Pour déployer une bibliothèque native avec une application Xamarin. Android, ajoutez le fichier binaire de bibliothèque au projet et définissez son **action de génération** sur **AndroidNativeLibrary**.

Pour déployer une bibliothèque native avec un projet de bibliothèque Xamarin. Android, ajoutez le fichier binaire de bibliothèque au projet et définissez son **action de génération** sur **EmbeddedNativeLibrary**.

Notez que dans la mesure où Android prend en charge plusieurs interfaces binaires d’application (ABI), Xamarin. Android doit savoir à quel ABI la bibliothèque native est générée.
Vous pouvez faire cela de deux façons :

1. Chemin d’accès « détection »
1. En utilisant un élément `AndroidNativeLibrary/Abi` dans le fichier projet

Avec la détection de chemin, le nom du répertoire parent de la bibliothèque native est utilisé pour spécifier l’ABI ciblée par la bibliothèque. Par conséquent, si vous ajoutez `lib/armeabi/libfoo.so` au projet, l’ABI sera « reniflée » comme `armeabi`.

Vous pouvez également modifier votre fichier projet pour spécifier explicitement l’ABI à utiliser :

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Pour plus d’informations sur l’utilisation des bibliothèques natives, consultez [interopérabilité avec les bibliothèques natives](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Débogage de code natif avec Visual Studio

Si vous utilisez *Visual studio 2019* ou *Visual Studio 2017*, vous n’êtes pas obligé de modifier vos fichiers de projet, comme décrit ci-dessus.
Vous pouvez générer et déboguer C++ à l’intérieur de votre solution Xamarin. Android en ajoutant C++ une référence de projet à un projet de **bibliothèque partagée dynamique (Android)** .

Pour déboguer du code natif C++ dans votre projet, procédez comme suit :

1. Double-cliquez sur **Propriétés** du projet, puis sélectionnez la page **options Android** .
2. Faites défiler jusqu’à **options de débogage**.
3. Dans le menu déroulant du **débogueur** , sélectionnez **C++** (au lieu du .NET par défaut **(Xamarin)** ).

Les développeurs C++ Visual Studio peuvent voir l’exemple [SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) pour essayer le débogage C++ à partir de visual studio 2019 ou Visual Studio 2017 avec Xamarin ; Pour plus d’informations, consultez notre [billet de blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) .

## <a name="related-links"></a>Liens associés

- [SanAngeles_NativeDebug (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Développement d’applications Xamarin Android natives](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
