---
title: Réglage des paramètres de mémoire Java pour le concepteur Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 9c9b9f5a205a2eef7db9f27e8d09b10ce65a4318
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027056"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Réglage des paramètres de mémoire Java pour le concepteur Android

Les paramètres de mémoire par défaut utilisés lors du démarrage du processus de `java` pour le concepteur Android peuvent être incompatibles avec certaines configurations système.

À partir de Xamarin Studio 5.7.2.7 (et versions ultérieures, Visual Studio pour Mac) et Visual Studio Tools pour Xamarin 3.9.344, ces paramètres peuvent être personnalisés pour chaque projet.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nouvelles propriétés du concepteur Android et options java correspondantes

Les noms de propriété suivants correspondent à l' [option de ligne de commande](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) Java indiquée

- **AndroidDesignerJavaRendererMinMemory** -XMS

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX : MaxPermSize

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Ouvrez votre solution dans Visual Studio.

2. Sélectionnez chaque projet Android un par un dans le Explorateur de solutions, puis cliquez sur [Afficher tous les fichiers](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) deux fois sur chaque projet. Vous pouvez ignorer des projets qui ne contiennent aucun fichier de disposition `.axml`. Cette étape permet de s’assurer que chaque répertoire de projet contient un fichier `.csproj.user`.

3. Quittez Visual Studio.

4. Recherchez le fichier `.csproj.user` pour chacun des projets de l’étape 2.

5. Modifiez chaque `.csproj.user` fichier dans un éditeur de texte.

6. Ajoutez tout ou partie des nouvelles propriétés de mémoire Android Designer dans un élément `<PropertyGroup>`. Vous pouvez utiliser un `<PropertyGroup>` existant ou en créer un nouveau. Voici un exemple complet `.csproj.user` fichier qui comprend les 3 attributs définis sur leurs valeurs par défaut :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7. Enregistrez et fermez tous les fichiers de `.csproj.user` mis à jour.

8. Redémarrez Visual Studio et rouvrez votre solution.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez votre solution dans Visual Studio pour Mac pour vous assurer que le répertoire de la solution contient un fichier `.userprefs`.

2. Quittez Visual Studio pour Mac.

3. Localisez le fichier `.userprefs` dans le répertoire de la solution.

4. Modifiez le fichier `.userprefs` dans un éditeur de texte.

5. Localisez l’élément XML existant au format suivant. La dernière partie de ce nom d’élément correspondra au nom de votre projet : « AndroidApplication1 » dans cet exemple :

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Si l’élément `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` n’existe pas, créez-le n’importe où dans l’élément englobant `<Properties>`. Veillez à remplacer « AndroidApplication1 » par le nom de votre projet.

7. Ajoutez tout ou partie des nouvelles propriétés de mémoire du concepteur Android comme attributs sur l’élément. Voici un exemple complet `.userprefs` fichier qui comprend les 3 attributs définis sur leurs valeurs par défaut :

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8. Répétez les étapes 5-7 pour chaque projet Android dans la solution qui contient des fichiers de disposition de `.axml`. (Autrement dit, ajoutez un élément `<MonoDevelop.Ide.ItemProperties.ProjectName>` pour chaque projet.)

9. Enregistrez et fermez le fichier `.userprefs`.

10. Redémarrez Visual Studio pour Mac et rouvrez votre solution.

-----
