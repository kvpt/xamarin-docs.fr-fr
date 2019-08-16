---
title: Comment automatiser un projet de test Android NUnit ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: f25ce3c5bfe7e3d8032709e9df99e7538e978862
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523414"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Comment automatiser un projet de test Android NUnit ?

> [!NOTE]
> Ce guide explique comment automatiser un projet de test Android NUnit, et non un projet Xamarin. UITest. Vous trouverez les guides Xamarin. UITest [ici](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Lorsque vous créez un projet d' **application de test unitaire (Android)** dans Visual Studio (ou un projet de **test unitaire Android** dans Visual Studio pour Mac), ce projet n’exécute pas automatiquement vos tests par défaut.
Pour exécuter des tests NUnit sur un appareil cible, vous pouvez créer une sous-classe [Android. app. Instrumentation](xref:Android.App.Instrumentation) qui est démarrée à l’aide de la commande suivante: 

```shell
adb shell am instrument 
```

Les étapes suivantes expliquent ce processus:

1. Créez un nouveau fichier appelé **TestInstrumentation.cs**: 

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

    Dans ce fichier, `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (à partir de **Xamarin. Android. NUnitLite. dll**) est sous-classé `TestInstrumentation`pour créer.

2. Implémentez `TestInstrumentation` le constructeur et `AddTests` la méthode. La `AddTests` méthode contrôle les tests qui sont réellement exécutés.

3. Modifiez le `.csproj` fichier pour ajouter **TestInstrumentation.cs**. Par exemple :

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

4. Utilisez la commande suivante pour exécuter les tests unitaires. Remplacez `PACKAGE_NAME` par le nom du package de l’application (le nom du package se trouve dans l' `/manifest/@package` attribut de l’application situé dans **fichier AndroidManifest. xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. Si vous le souhaitez, vous pouvez `.csproj` modifier le fichier pour `RunTests` ajouter la cible MSBuild. Cela permet d’appeler les tests unitaires à l’aide d’une commande telle que la suivante:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Notez que l’utilisation de cette nouvelle cible n’est pas obligatoire `adb` ; la commande antérieure peut être `msbuild`utilisée à la place de.)

Pour plus d’informations sur l' `adb shell am instrument` utilisation de la commande pour exécuter des tests unitaires, consultez la rubrique développeurs Android [exécutant des tests avec ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) .


> [!NOTE]
> Avec la version [Xamarin. Android 5,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) , les noms de package par défaut pour les wrappers pouvant être appelés Android seront basés sur le md5sum du nom qualifié d’assembly du type en cours d’exportation. Cela permet de fournir le même nom qualifié complet à partir de deux assemblys différents et de ne pas obtenir d’erreur de Packaging. Veillez donc à utiliser la `Name` propriété sur l' `Instrumentation` attribut pour générer un nom de ACW/classe lisible.

_Le nom ACW doit être utilisé dans la `adb` commande ci-dessus_.
Le renommage ou la refactorisation de la C# classe nécessitera donc `RunTests` la modification de la commande pour utiliser le nom de ACW correct.

