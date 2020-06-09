---
title: Installation et configuration de l’inspecteur
description: Ce document explique comment installer le Xamarin Inspector et présente les plateformes de système d’exploitation, IDE et d’application prises en charge.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 39e7a6c86a2d4ab4231a29c0e1014de702af01ac
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573415"
---
# <a name="inspector-installation-and-requirements"></a>Installation et configuration de l’inspecteur

## <a name="download-and-installation"></a>Téléchargement et installation

# <a name="windows"></a>[Windows](#tab/windows)

1. Téléchargez et installez [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) et sélectionnez le **développement mobile avec** la charge de travail .net.
1. [Connectez-vous](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) pour activer votre abonnement Enterprise.
1. [Inspectez](~/tools/inspector/inspect.md) votre propre application !

# <a name="macos"></a>[MacOS](#tab/macos)

1. Téléchargez et installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Connectez-vous](https://docs.microsoft.com/visualstudio/mac/activation) pour activer votre abonnement Enterprise.
1. [Inspectez](~/tools/inspector/inspect.md) votre propre application !

-----

## <a name="requirements"></a>Spécifications

### <a name="supported-operating-systems"></a>Systèmes d'exploitation pris en charge

- **Mac** -OS X 10,11 ou version ultérieure
- **Windows** -Windows 7 ou version ultérieure (avec Internet Explorer 11 ou version ultérieure et .net 4.6.1 ou version ultérieure)

### <a name="supported-ides"></a>IDE pris en charge

- Visual Studio pour Mac
- Visual Studio 2017 avec le **développement mobile avec** la charge de travail .net

L’inspection des applications en direct est disponible pour les entreprises.

<a name="supported-platforms"></a>

### <a name="supported-app-platforms"></a>Plateformes d’application prises en charge

|Plateforme d’application|Prise en charge IDE|Notes|
|--- |--- |--- |
|Mac|Uniquement pris en charge dans Visual Studio pour Mac|
|iOS|Pris en charge dans Visual Studio 2017 et Visual Studio pour Mac| Le comportement de l’éditeur de liens doit être défini sur **ne pas lier** (sous options du projet de **génération iOS** ) |
|Android|Pris en charge dans Visual Studio 2017 et Visual Studio pour Mac|Vous devez cibler Android >= 4.0.3, avec **fastdev** activé.<br />Doit utiliser des émulateurs Google, Visual Studio ou Xamarin Android. Les émulateurs Android 7 ne peuvent pas autoriser l’inspection pour l’instant.|
|WPF|Pris en charge uniquement dans Visual Studio 2017|

<a name="reporting-bugs"></a>

## <a name="reporting-bugs"></a>Bogues de création de rapports

Les bogues doivent être signalés directement par le biais de Visual Studio :

- **Aide > envoyer des commentaires > signaler un problème**

Veuillez inclure toutes les informations suivantes :

### <a name="platform-version-information"></a>Informations sur la version de la plateforme

Ces informations sont essentielles.

Visual Studio pour Mac

- **Visual Studio > à propos de Visual Studio > afficher les détails > copier les informations**
- Coller dans le rapport de bogue

Visual Studio

- **Aide > sur Visual Studio > copier les informations**
- Indiquez-nous la version de votre système d’exploitation et indiquez si vous exécutez Windows 32 bits ou 64 bits.

### <a name="log-files"></a>Fichiers journaux

Attachez toujours les fichiers journaux des clients IDE et Inspector.

Client de l’inspecteur

- Mac : `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows : `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x offre également la possibilité de sélectionner le fichier journal dans Finder (macOS) ou Explorer (Windows) directement à partir du menu principal :

- **Aide > afficher le fichier journal**

Visual Studio pour Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Le contenu du volet de **sortie** de Visual Studio peut également être informatif.

### <a name="project-settings"></a>Paramètres du projet

Si vous pouvez attacher le **. csproj** pour le projet que vous essayez d’inspecter, il est très utile. Cela est plus facile que de demander des paramètres individuels.

Vérifiez également que vous êtes dans la configuration Debug.

### <a name="selected-devices"></a>Appareils sélectionnés

Pour Android et iOS, il est vital que nous sachions l’appareil sur lequel vous effectuez le débogage lorsque vous souhaitez effectuer l’inspection. Nous devons connaître les éléments suivants :

- Nom de l’appareil tel qu’il apparaît dans votre IDE
- Version du système d’exploitation de votre appareil
- Android : Vérifiez que vous utilisez un émulateur x86
- Android : quelle plateforme d’émulateur utilisez-vous ? Émulateur Google ? Visual Studio Émulateur Android ? Xamarin Android Player?
- L’application que vous déboguez correctement s’affiche-t-elle et fonctionne-t-elle dans l’appareil ?
- L’appareil dispose-t-il d’une connectivité réseau (Vérifiez via le navigateur Web) ?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
