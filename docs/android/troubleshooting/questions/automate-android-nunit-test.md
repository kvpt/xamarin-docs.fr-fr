---
title: Comment automatiser un projet de test Android NUnit ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 0837deccdb535c178e8b00b052efeb7c9bd49679
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864130"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Comment automatiser un projet de test Android NUnit ?

> [!NOTE]
> Ce guide explique comment automatiser un projet de test Android NUnit, pas un projet Xamarin.UITest. Vous pouvez trouver des guides de Xamarin.UITest [ici](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Lorsque vous créez un **application de tests unitaires (Android)** projet dans Visual Studio (ou **Android Test unitaire** projet dans Visual Studio pour Mac), cela va de projet pas automatiquement d’exécuter vos tests par défaut.
Pour exécuter des tests NUnit sur un appareil cible, vous pouvez créer un [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) sous-classe est démarrée à l’aide de la commande suivante : 

```shell
adb shell am instrument 
```

Les étapes suivantes expliquent ce processus :

1.  Créer un nouveau fichier appelé **TestInstrumentation.cs**: 

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
    Dans ce fichier, [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (à partir de **Xamarin.Android.NUnitLite.dll**) est une sous-classe pour créer `TestInstrumentation`.

2.  Implémentez le [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructeur et le [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) (méthode). Le `AddTests` contrôles méthode quels tests sont réellement exécutées.

3.  Modifier le `.csproj` fichier à ajouter **TestInstrumentation.cs**. Par exemple :

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

4.  Utilisez la commande suivante pour exécuter les tests unitaires. Remplacez `PACKAGE_NAME` avec le nom du package de l’application (vous trouverez le nom du package dans l’application `/manifest/@package` attribut situé dans **AndroidManifest.xml**) :

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5.  Si vous le souhaitez, vous pouvez modifier le `.csproj` fichier pour ajouter le `RunTests` cible MSBuild. Cela rend possible appeler les tests unitaires avec une commande comme suit :

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Notez qu’à l’aide de cette nouvelle cible n’est pas obligatoire ; l’ancien `adb` commande peut être utilisée à la place de `msbuild`.)

Pour plus d’informations sur l’utilisation de la `adb shell am instrument` commande pour exécuter des tests unitaires, consultez le développeur Android [exécution de tests avec ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) rubrique.


> [!NOTE]
> Avec le [Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) release, les noms de package par défaut pour les Wrappers RCW Android doit reposer le MD5SUM du nom qualifié d’assembly du type en cours d’exportation. Ainsi, le même nom qualifié complet être fournis à partir de deux assemblys différents et pas obtenir une erreur d’empaquetage. Par conséquent, assurez-vous que vous utilisez le `Name` propriété sur le `Instrumentation` attribut pour générer un nom ACW/classe lisible.

_Le nom ACW doit être utilisé dans le `adb` commande ci-dessus_.
Refactorisation du changement de nom/la C# classe sera donc nécessitent la modification de la `RunTests` commande pour utiliser le nom ACW correct.

