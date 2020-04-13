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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027056"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Réglage des paramètres de mémoire Java pour le concepteur Android

Les paramètres de mémoire par défaut `java` qui sont utilisés lors du démarrage du processus pour le concepteur Android peuvent être incompatibles avec certaines configurations du système.

En commençant par Xamarin Studio 5.7.2.7 (et plus tard, Visual Studio for Mac) et Visual Studio Tools pour Xamarin 3.9.344, ces paramètres peuvent être personnalisés sur une base par projet.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nouvelles propriétés de concepteur Android et options Java correspondantes

Les noms de propriété suivants correspondent à l’option de ligne de commande java [indiquée](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Ouvrez votre solution dans Visual Studio.

2. Sélectionnez chaque projet Android un par un dans la Solution Explorer et cliquez sur [Afficher tous les fichiers](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) deux fois sur chaque projet. Vous pouvez sauter des projets qui ne contiennent pas `.axml` de fichiers de mise en page. Cette étape permettra de s’assurer `.csproj.user` que chaque répertoire de projet contient un fichier.

3. Quittez Visual Studio.

4. Localiser `.csproj.user` le fichier pour chacun des projets à partir de l’étape 2.

5. Modifier `.csproj.user` chaque fichier dans un éditeur de texte.

6. Ajoutez l’une ou l’autre des `<PropertyGroup>` nouvelles propriétés de mémoire de concepteur Android dans un élément. Vous pouvez utiliser `<PropertyGroup>` un existant ou en créer un nouveau. Voici un fichier `.csproj.user` d’exemple complet qui inclut les 3 attributs définis à leurs valeurs par défaut :

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

7. Enregistrer et fermer tous `.csproj.user` les fichiers mis à jour.

8. Redémarrez Visual Studio et réouvrez votre solution.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez votre solution dans Visual Studio pour Mac `.userprefs` pour vous assurer que le répertoire de solution contient un fichier.

2. Quittez Visual Studio pour Mac.

3. Localiser `.userprefs` le fichier dans le répertoire de la solution.

4. Modifier `.userprefs` le fichier dans un éditeur de texte.

5. Localiser l’élément XML existant avec le format suivant. La dernière partie de ce nom d’élément correspondra au nom de votre projet : "AndroidApplication1" dans cet exemple:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Si `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` l’élément n’existe pas, créez-le n’importe où dans l’élément d’enceinte. `<Properties>` Assurez-vous de remplacer "AndroidApplication1" par le nom de votre projet.

7. Ajoutez l’une ou l’autre des nouvelles propriétés de mémoire de concepteur Android comme attributs sur l’élément. Voici un fichier `.userprefs` d’exemple complet qui inclut les 3 attributs définis à leurs valeurs par défaut :

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

8. Répétez les étapes 5-7 pour chaque `.axml` projet Android dans la solution qui contient tous les fichiers de mise en page. (C’est-à-dire ajouter un `<MonoDevelop.Ide.ItemProperties.ProjectName>` élément pour chaque projet.)

9. Enregistrez et fermez le fichier `.userprefs`.

10. Redémarrez Visual Studio pour Mac et réouvrez votre solution.

-----
