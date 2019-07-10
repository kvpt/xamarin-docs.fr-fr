---
title: Réglage des paramètres de mémoire Java pour le concepteur Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: c42be49aff56453bb1bc3b3c732cad151ccd62e0
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674463"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Réglage des paramètres de mémoire Java pour le concepteur Android

Les paramètres de mémoire par défaut qui sont utilisés lors du démarrage de la `java` traiter d’Android designer peut être incompatible avec certaines configurations système.

En commençant par Xamarin Studio 5.7.2.7 (et versions ultérieur, Visual Studio pour Mac) et Visual Studio Tools pour Xamarin 3.9.344, ces paramètres peuvent être personnalisés par projet.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nouvelles propriétés de concepteur Android et les options correspondantes de Java

Les noms de propriété suivantes correspondent aux java indiqué [option de ligne de commande](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Ouvrez votre solution dans Visual Studio.

2.  Sélectionnez chaque projet Android, un par un dans l’Explorateur de solutions, cliquez sur [afficher tous les fichiers](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) deux fois sur chaque projet. Vous pouvez ignorer les projets qui ne contiennent aucun `.axml` fichiers de disposition. Cette étape garantit que chaque répertoire du projet contient un `.csproj.user` fichier.

3.  Quittez Visual Studio.

4.  Recherchez le `.csproj.user` fichier pour chacun des projets à partir de l’étape 2.

5.  Modifiez chacun `.csproj.user` fichier dans un éditeur de texte.

6.  Ajouter tout ou partie des nouvelles propriétés de mémoire concepteur Android dans un `<PropertyGroup>` élément. Vous pouvez utiliser un existant `<PropertyGroup>` ou créez-en un. Voici un exemple complet `.csproj.user` fichier qui inclut toutes les 3 attributs définie leurs valeurs par défaut :

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

7.  Enregistrez et fermez toutes les mises à jour `.csproj.user` fichiers.

8.  Redémarrez Visual Studio et rouvert votre solution.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1.  Ouvrez votre solution dans Visual Studio pour Mac garantir le répertoire de solution contient un `.userprefs` fichier.

2.  Quittez Visual Studio pour Mac.

3.  Recherchez le `.userprefs` fichier dans le répertoire de solution.

4.  Modifier le `.userprefs` fichier dans un éditeur de texte.

5.  Localisez l’élément XML existant avec le format suivant. La dernière partie de ce nom d’élément correspond au nom de votre projet : « AndroidApplication1 » dans cet exemple :

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Si le `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` élément n’existe pas, créez-le n’importe où dans la forme `<Properties>` élément. Veillez à remplacer « AndroidApplication1 » par le nom de votre projet.

7.  Ajouter tout ou partie des nouvelles propriétés de mémoire concepteur Android en tant qu’attributs sur l’élément. Voici un exemple complet `.userprefs` fichier qui inclut toutes les 3 attributs définie leurs valeurs par défaut :

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

8.  Répétez les étapes 5 à 7 pour chaque projet Android dans la solution qui contient des `.axml` fichiers de disposition. (Autrement dit, ajoutez-en un `<MonoDevelop.Ide.ItemProperties.ProjectName>` élément pour chaque projet.)

9.  Enregistrez et fermez le `.userprefs` fichier.

10. Redémarrez Visual Studio pour Mac et rouvert votre solution.

-----

