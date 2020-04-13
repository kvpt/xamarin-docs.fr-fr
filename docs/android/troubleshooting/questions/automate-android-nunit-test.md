---
title: Comment automatiser un projet de test Android NUnit ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: 1246eeac63a0ae232396d4c2fd69d8bf516f5e3e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027004"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Comment automatiser un projet de test Android NUnit ?

> [!NOTE]
> Ce guide explique comment automatiser un projet de test Android NUnit, et non un projet Xamarin.UITest. Xamarin.UITest guides peuvent être trouvés [ici](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest).

Lorsque vous créez un projet **d’application de test unitaire (Android)** dans Visual Studio (ou **Android Unit Test** project dans Visual Studio for Mac), ce projet n’exécute pas automatiquement vos tests par défaut.
Pour exécuter des tests NUnit sur un appareil cible, vous pouvez créer une sous-classe [Android.App.Instrumentation](xref:Android.App.Instrumentation) qui est commencée par l’utilisation de la commande suivante : 

```shell
adb shell am instrument 
```

Les étapes suivantes expliquent ce processus :

1. Créer un nouveau fichier appelé **TestInstrumentation.cs**: 

    ```cs 
    using System;
    using System.Reflection;
    using Android.App;
    using Android.Content;
    using Android.Runtime;
    using Xamarin.Android.NUnitLite;

    namespace App.Tests {

        [Instrumentation(Name="app.tests.TestInstrumentation")]
        public class TestInstrumentation : TestSuiteInstrumentation {

            public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
            {
            }

            protected override void AddTests ()
            {
                AddTest (Assembly.GetExecutingAssembly ());
            }
        }
    }
    ```

    Dans ce `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` fichier, (de **Xamarin.Android.NUnitLite.dll**) `TestInstrumentation`est sous-classé pour créer .

2. Implémentez `TestInstrumentation` le `AddTests` constructeur et la méthode. La `AddTests` méthode contrôle les tests qui sont effectivement exécutés.

3. Modifier `.csproj` le fichier pour ajouter **TestInstrumentation.cs**. Par exemple :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

4. Utilisez la commande suivante pour exécuter les tests de l’unité. Remplacez-le `PACKAGE_NAME` par le nom du paquet de l’application `/manifest/@package` (le nom du paquet peut être trouvé dans l’attribut de l’application situé dans **AndroidManifest.xml**) :

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. En option, vous `.csproj` pouvez modifier `RunTests` le fichier pour ajouter la cible MSBuild. Cela permet d’invoquer les tests d’unité avec une commande comme la suivante:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    (Notez que l’utilisation de cette `adb` nouvelle cible n’est pas nécessaire; la commande antérieure peut être utilisée au lieu de `msbuild`.)

Pour plus d’informations sur l’utilisation de la `adb shell am instrument` commande pour exécuter des tests unitaires, voir les tests Android Developer Running avec le sujet [ADB.](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)

> [!NOTE]
> Avec la version [Xamarin.Android 5.0,](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) les noms de paquets par défaut pour Android Callable Wrappers seront basés sur le MD5SUM du nom d’assemblage qualifié du type exporté. Cela permet de fournir le même nom entièrement qualifié à partir de deux assemblages différents et de ne pas obtenir une erreur d’emballage. Assurez-vous donc que `Name` vous `Instrumentation` utilisez la propriété sur l’attribut pour générer un nom LIsible ACW / classe.

_Le nom ACW doit `adb` être utilisé dans la commande ci-dessus_.
Le changement de nom/refactorisation de la classe `RunTests` C nécessitera donc de modifier la commande pour utiliser le nom ACW correct.
