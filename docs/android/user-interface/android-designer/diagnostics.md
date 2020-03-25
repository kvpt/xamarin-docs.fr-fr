---
title: Diagnostics de disposition Android
description: Explique les diagnostics de mise en page Android et la prise en main
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 5c29a1a80d8c1f599f0bbc750d22d8334ddb3494
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247740"
---
# <a name="android-layout-diagnostics"></a>Diagnostics de disposition Android

Les diagnostics de disposition Android sont conçus pour aider à améliorer la qualité des fichiers de disposition Android en mettant en évidence les problèmes de qualité courants et les optimisations utiles. Cette fonctionnalité est disponible à la fois pour Visual Studio 16,5 + et Visual Studio pour Mac 8.5 +.

Un ensemble d’analyseurs par défaut est fourni pour un large éventail de problèmes et peut être personnalisé pour couvrir les besoins spécifiques d’un projet. Les analyseurs sont faiblement basés sur le système d’inversion Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Activer les diagnostics de disposition Android sur Visual Studio 2019

Assurez-vous que le paramètre de diagnostic disposition, **activer les diagnostics de disposition**, est activé. Pour accéder à cette page d’options, choisissez **outils** > **options**, puis choisissez **éditeur de texte** > **Android XML** > **avancé**:

![Boîte de dialogue Options illustrant l’activation de l’option Diagnostics](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Une fois activé, l’éditeur de disposition Android affiche des problèmes :

![Diagnostics Android activés sur Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Activer les diagnostics de disposition Android sur Visual Studio pour Mac

Assurez-vous que le paramètre de diagnostic disposition, **activer les diagnostics de disposition**, est activé. Pour accéder à cette page d’options, choisissez **Visual Studio** > **Préférences...** , puis choisissez **éditeur de texte** > **Android XML**:

![Boîte de dialogue Préférences illustrant l’activation de l’option Diagnostics](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Une fois activé, l’éditeur de disposition Android affiche des problèmes :

![Diagnostics Android activés sur Visual Studio pour Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Fonctionnalités

Les sections suivantes décrivent les fonctionnalités disponibles dans les diagnostics de mise en page Android.

### <a name="analyzers"></a>Analyseurs

Les analyseurs sont utilisés pour aider à détecter les problèmes dans les fichiers de disposition. Certains permettent de réduire les valeurs codées en dur, d’améliorer les performances et de marquer les erreurs.

### <a name="diagnostic-configuration"></a>Configuration de diagnostic

Les analyseurs peuvent être configurés à l’aide d’un fichier XML, ce qui vous permet de modifier le niveau de gravité par défaut, d’ignorer certains fichiers et de transmettre des variables.

Vous pouvez utiliser un fichier de base de référence si vous disposez d’un ensemble de configurations que vous souhaitez partager entre plusieurs applications Android. Pour utiliser cette fonctionnalité, créez un nouveau fichier de configuration et ajoutez `-baseline` au nom de fichier. Les configurations de base sont appliquées en premier, puis les fichiers de configuration restants.

> [!TIP]
> Cela peut être utile si vous souhaitez ignorer un ensemble de problèmes sur une application Android nouvelle ou existante.

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
> Actuellement, les seules variables sont `MAX_VIEW_COUNT` (par défaut : 80) et `MAX_DEPTH` (valeur par défaut : 10) pour `TooManyViews` et `TooDeepLayout` respectivement.

Les niveaux de gravité des alertes sont :

- Suggestion
- Info
- Avertissement
- Error
- Ignorer

### <a name="add-a-configuration-file"></a>Ajouter un fichier de configuration

Créez un nouveau fichier XML à la racine d’un projet d’application Android. Le nom du fichier n’est pas important, mais cet exemple utilise `AndroidLayoutDiagnostics.xml`:

![Ajouter un nouvel élément](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Une fois le nouveau fichier XML ajouté, il doit apparaître dans l’arborescence de projet de l’application Android :

![Arborescence de projet d’application Android](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Assurez-vous que l’action de génération est définie sur **AndroidResourceAnalysisConfig** dans le panneau Propriétés.
Le moyen le plus simple d’extraire le panneau des propriétés du nouveau fichier consiste à cliquer avec le bouton droit sur le fichier et à sélectionner Propriétés. Une fois le panneau Propriétés affiché, vous devez remplacer l' **action de génération** par **AndroidResourceAnalysisConfig**:

![Définir l’action de génération dans les propriétés de l’élément](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Maintenant que vous avez un fichier XML vide, vous devez ajouter l’élément racine `<configuration>`. À ce stade, vous pouvez ajuster le comportement par défaut de tous les problèmes pris en charge.
Si vous souhaitez vous assurer que les chaînes codées en dur sont traitées comme des erreurs, ajoutez :

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![Fichier de configuration des diagnostics](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Maintenant que le texte codé en dur est considéré comme une erreur, il est désormais marqué d’un tilde rouge dans l’éditeur de disposition :

![Disposition à l’aide de la configuration des diagnostics](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Pour que les modifications apportées au nouveau fichier de configuration prennent effet, tous les fichiers de disposition actuellement ouverts doivent être rouverts.
>

## <a name="troubleshooting"></a>Dépannage

Voici quelques problèmes courants possibles.

- Assurez-vous qu’il n’y a pas d’erreur de format XML.
- L’action de génération est correctement définie sur **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problèmes connus

- Le bloc d’erreurs n’est pas rempli tant que le fichier n’a pas été modifié la première fois.

## <a name="related-links"></a>Liens connexes

- [Contrôles Android Lint](http://tools.android.com/tips/lint-checks)
- [Améliorez votre code avec des contrôles Lint](https://developer.android.com/studio/write/lint)
