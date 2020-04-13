---
title: Diagnostics de mise en page Android
description: Explique les diagnostics de mise en page Android et comment commencer
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 746f74e68fa4816f1f7979980af9506dc0173542
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987580"
---
# <a name="android-layout-diagnostics"></a>Diagnostics de mise en page Android

Les diagnostics de mise en page Android sont conçus pour aider à améliorer la qualité des fichiers de mise en page Android en mettant en évidence des problèmes de qualité communs et des optimisations utiles. Cette fonctionnalité est disponible pour Visual Studio 16.5 et Visual Studio pour Mac 8.5 .

Un ensemble d’analyseurs par défaut est fourni pour un large éventail de problèmes et chacun peut être personnalisé pour couvrir les besoins spécifiques d’un projet. Les analyseurs sont vaguement basés sur le système de linting Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Activez les diagnostics de mise en page Android sur Visual Studio 2019

Assurez-vous que le réglage des diagnostics de mise en page, Activez les **diagnostics de mise en page,** est activé. Pour accéder à cette page d’options, choisissez **Tools** > **Options**, puis choisissez Text **Editor** > **Android XML** > **Advanced**:

![Dialogue d’options montrant comment activer l’option de diagnostic](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Une fois activé, l’éditeur de mise en page Android affichera les problèmes :

![Diagnostic Android activé sur Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Activez les diagnostics de mise en page Android sur Visual Studio pour Mac

Assurez-vous que le réglage des diagnostics de mise en page, Activez les **diagnostics de mise en page,** est activé. Pour accéder à cette page d’options, choisissez **Visual Studio** > **Preferences...**, puis choisissez **Text Editor** > **Android XML**:

![Dialogue de préférences montrant comment activer l’option de diagnostic](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Une fois activé, l’éditeur de mise en page Android affichera les problèmes :

![Diagnostic Android activé sur Visual Studio pour Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Fonctionnalités

Les sections suivantes décrivent les fonctionnalités disponibles dans les diagnostics de mise en page Android.

### <a name="analyzers"></a>Analyseurs

Les analyseurs sont utilisés pour aider à détecter les problèmes dans les fichiers de mise en page, réduire les valeurs codées en dur, améliorer les performances et signaler les erreurs. Pour une liste d’analyseurs, voir [analyseurs diagnostiques de concepteur d’Android](diagnostic-analyzers.md)

### <a name="diagnostic-configuration"></a>Configuration diagnostique

Les analyseurs peuvent être configurés à l’aide d’un fichier XML, ce qui vous permet de modifier le niveau de gravité par défaut, d’ignorer certains fichiers et de passer dans les variables.

Vous pouvez utiliser un fichier de base si vous avez un ensemble de configurations que vous souhaitez partager sur plusieurs applications Android. Pour utiliser cette fonctionnalité, créez un `-baseline` nouveau fichier de configuration et appendez le nom du fichier. Les configurations de base sont appliquées d’abord, puis les fichiers de configuration restants.

> [!TIP]
> Cela peut être utile si vous voulez ignorer un ensemble de problèmes sur une application Android nouvelle ou existante.

Le format est le suivant :

```xml
<?xml version="1.0" encoding="utf-8" ?> 
<configuration>
    <issue id="DuplicateIDs" severity="warning">
        <ignore path="Resources/layout/layout1.xml" />
    </issue>
    <issue id="HardcodedText" severity="informational">
        <ignore path="Resources/layout/layout1.xml" />
        <ignore path="Resource/layout/layout2.xml" />
    </issue>
    <issue id="TooManyViews">
        <variable name="MAX_VIEW_COUNT" value="12" />
    </issue>
    <issue id="TooDeepLayout">
        <variable name="MAX_DEPTH" value="12" />
    </issue>
</configuration>
```

> [!NOTE]
> Actuellement, les `MAX_VIEW_COUNT` seules variables sont (par `MAX_DEPTH` défaut: 80) `TooManyViews` `TooDeepLayout` et (par défaut: 10) pour et respectivement.

Les niveaux de gravité des alertes sont :

- Suggestion
- Info
- Avertissement
- Error
- Ignorer

### <a name="add-a-configuration-file"></a>Ajouter un fichier de configuration

Créez un nouveau fichier XML à la racine d’un projet d’application Android. Le nom du fichier n’est pas `AndroidLayoutDiagnostics.xml`important, mais cet exemple utilise :

![Ajouter un nouvel élément](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Une fois le nouveau fichier XML ajouté, il doit apparaître dans l’arbre du projet d’application Android :

![Arbre de projet d’application Android](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Assurez-vous que l’action de construction est définie à **AndroidResourceAnalysisConfig** dans le panneau de propriétés.
La façon la plus simple de tirer vers le haut du panneau de propriété pour le nouveau fichier est de cliquer à droite sur le fichier et sélectionner les propriétés. Une fois que le panneau de propriétés est montré, vous devez changer **l’action de construction** en **AndroidResourceAnalysisConfig**:

![Définir l’action de construction dans les propriétés d’objets](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Maintenant que vous avez un fichier XML `<configuration>` vierge, vous devez ajouter l’élément racine. À ce stade, vous pouvez ajuster le comportement par défaut de tous les problèmes pris en charge.
Si vous voulez vous assurer que les chaînes codées en dur sont traitées comme des erreurs ajoutent :

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![Fichier de configuration de diagnostic](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Maintenant que le texte codé dur est considéré comme une erreur, il est maintenant signalé avec un gribouillis rouge dans l’éditeur de mise en page:

![Mise en page à l’aide de La configuration des diagnostics](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Pour que toute nouvelle modification de fichier de configuration entre en vigueur, tous les fichiers de mise en page actuellement ouverts doivent être rouverts.
>

## <a name="troubleshooting"></a>Dépannage

Voici quelques problèmes communs possibles.

- Assurez-vous qu’il n’y a pas d’erreur de format XML.
- L’action de construction est réglée correctement sur **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problèmes connus

- Le bloc d’erreur n’est pas peuplé jusqu’à ce que le fichier soit changé la première fois.

## <a name="related-links"></a>Liens connexes

- [Vérifications Android Lint](http://tools.android.com/tips/lint-checks)
- [Améliorez votre code avec des vérifications de peluche](https://developer.android.com/studio/write/lint)
