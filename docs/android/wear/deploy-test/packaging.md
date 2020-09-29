---
title: Empaqueter des applications d’usure
description: Cet article explique comment empaqueter des applications Android usure.
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 3263bfd9cea184a7bfbb29099abe2f1b3deb4126
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458054"
---
# <a name="packaging-wear-apps"></a>Empaqueter des applications d’usure

> [!WARNING]
> Les documents et exemples de projets suivants peuvent ne plus être gérés.
> À compter de [Xamarin. Android 11,1][xa-11.1], le Packaging automatique d’une application Android d’usure dans une application de poche Android n’est plus pris en charge. Il est recommandé de distribuer les applications d’usure Android en tant qu' [applications autonomes][standalone] .

Android usure 1,0 les applications sont empaquetées avec une application Android complète pour la distribution sur Google Play.

Les applications Android usure 2,0 peuvent être soumises à Google Play en tant qu' [applications autonomes][standalone].

[xa-11.1]: /xamarin/android/release-notes/11/11.1
[standalone]: https://developer.android.com/training/wearables/apps/standalone-apps

## <a name="automatic-packaging"></a>Empaquetage automatique

À compter de Xamarin Android 5,0, votre application d’usure est automatiquement empaquetée en tant que ressource dans votre application de poche lorsque vous créez une référence de projet à partir du projet de poche vers le projet d’usure. Pour créer cette association, vous pouvez utiliser les étapes suivantes : 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Si votre application d’usure ne fait pas déjà partie de votre solution de poche, cliquez avec le bouton droit sur le nœud de la solution et sélectionnez **ajouter > ajouter un projet existant...**.

2. Accédez au fichier **. csproj** de votre application usure, sélectionnez-le, puis cliquez sur **ouvrir**. Le projet d’application d’usure doit maintenant être visible dans votre solution de poche.

3. Cliquez avec le bouton droit sur le nœud **références** et sélectionnez **Ajouter une référence**.

4. Dans la boîte de dialogue **Gestionnaire de références** , activez votre projet d’usure (cliquez pour ajouter une coche), puis cliquez sur **OK**.

5. Modifiez le nom du package pour votre projet d’usure afin qu’il corresponde au nom du package du projet de poche (le nom du package peut être modifié sous **propriétés > manifeste Android**).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Si votre application d’usure ne fait pas déjà partie de votre solution de poche, cliquez avec le bouton droit sur le nœud de la solution et sélectionnez **ajouter > ajouter un projet existant...**.

2. Accédez au fichier **. csproj** de votre application usure, sélectionnez-le, puis cliquez sur **ouvrir**. Le projet d’application d’usure doit maintenant être visible dans votre solution de poche.

3. Cliquez avec le bouton droit sur le nœud du projet Handheld dans la solution, puis cliquez sur **modifier les références...**.

4. Dans la boîte de dialogue **modifier les références** , activez votre projet d’usure (cliquez pour ajouter une coche), puis cliquez sur **OK**.

5. Modifiez le nom du package pour votre projet d’usure afin qu’il corresponde au nom du package du projet de poche (le nom du package peut être modifié sous **Options du projet > application Android**).

-----

Notez que vous obtiendrez une erreur **XA5211** si le nom du package de l’application d’usure ne correspond pas au nom du package de l’application de poche. Par exemple :

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Pour corriger cette erreur, modifiez le nom du package de l’application usure afin qu’il corresponde au nom du package de l’application de poche.

Lorsque vous cliquez sur **générer > générer tout**, cette association déclenche l’empaquetage automatique du projet d’usure dans le projet de poche (téléphone) principal. L’application d’usure est automatiquement créée et incluse en tant que ressource dans l’application de poche.

L’assembly généré par le projet d’application d’usure n’est pas utilisé comme référence d’assembly dans le projet de poche (téléphone). Au lieu de cela, le processus de génération effectue les opérations suivantes :

- Vérifie que les noms de packages correspondent. 

- Génère du code XML et l’ajoute au projet Handheld pour l’associer à l’application usure. Par exemple : 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- Ajoute l’application usure en tant que ressource **brute** au projet de poche. 

## <a name="manual-packaging"></a>Empaquetage manuel

Vous pouvez écrire des applications d’usure Android dans Xamarin. Android avant la version 5,0, mais vous devez suivre ces instructions de Packaging manuel pour distribuer l’application : 

1. Assurez-vous que votre projet portable et les projets de poche (téléphone) ont le même numéro de version et le même nom de package.

2. Générez manuellement le projet portable comme une **version Release** .

3. Ajoutez manuellement la version **. APK** de l’étape (2) dans le répertoire **Resources/RAW** du projet de portable (téléphone).

4. Ajoutez manuellement une nouvelle ressource de ressources XML **/XML/wearable_app_desc.xml** dans le projet de poche, qui fait référence à un **apk** portable de l’étape (3) :

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Ajoutez manuellement un `<meta-data />` élément à l’élément **AndroidManifest.xml** du projet Handheld `<application>` qui fait référence à la nouvelle ressource XML :

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Consultez également les [instructions packging manuelles](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)du site du développeur Android.