---
title: Liaison d’un projet de bibliothèque Eclipse
description: Ce pas-là explique comment utiliser les modèles de projet Xamarin.Android pour lier un projet de bibliothèque Android Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 2ac402bf423c9f3fe136d1ba31622d915d2e2eef
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027739"
---
# <a name="binding-an-eclipse-library-project"></a>Liaison d’un projet de bibliothèque Eclipse

_Ce pas-là explique comment utiliser les modèles de projet Xamarin.Android pour lier un projet de bibliothèque Android Eclipse._

## <a name="overview"></a>Vue d’ensemble

Mais. Les fichiers AAR deviennent de plus en plus la norme pour la distribution de bibliothèque Android, dans certains cas, il est nécessaire de créer une liaison pour un *projet de bibliothèque Android*. Les projets de bibliothèque Android sont des projets Android spéciaux qui contiennent du code et des ressources partageables qui peuvent être référencés par des projets d’applications Android. En règle générale, vous vous liez à un projet de bibliothèque Android lorsque la bibliothèque est créée dans l’Eclipse IDE.
Ce pas-là fournit des exemples de la façon de créer un projet de bibliothèque Android . ZIP de la structure d’annuaire d’un projet Eclipse.

Les projets de bibliothèque Android sont différents des projets Android réguliers en ce qu’ils ne sont pas compilés dans un APK et ne sont pas, à eux seuls, déployables à un appareil. Au lieu de cela, un projet de bibliothèque Android est destiné à être référencé par un projet d’application Android. Lorsqu’un projet d’application Android est construit, le projet de bibliothèque Android est compilé en premier. Le projet d’application Android sera ensuite absorbé dans le projet de bibliothèque Android compilé et inclure le code et les ressources dans l’APK pour la distribution. En raison de cette différence, la création d’une liaison pour un projet de bibliothèque Android est légèrement différente de la création d’une liaison pour un Java . JAR ou . Fichier AAR.

## <a name="walkthrough"></a>Procédure pas à pas

Pour utiliser un projet de bibliothèque Android dans un projet de liaison Java Xamarin.Android, il est d’abord nécessaire de construire le projet de bibliothèque Android à Eclipse. La capture d’écran suivante montre un exemple d’un projet de bibliothèque Android après compilation: 

[![Exemple de projet de bibliothèque à Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Notez que le code source du projet de bibliothèque Android a été compilé à un temporaire . Fichier JAR nommé **android-mapviewballoons.jar**, et que les ressources ont été copiées sur le **dossier bin/res/crunch.** 

Une fois que le projet de bibliothèque Android a été compilé dans Eclipse, il peut alors être lié à l’aide d’un projet de liaison Java Xamarin.Android. D’abord un . Fichier ZIP doit être créé qui contient le **bac** et les dossiers de **res** du projet de bibliothèque Android. Il est important que vous supprimiez la sous-direction **de crunch** intervenante de sorte que les ressources résident dans **bin/res**. La capture d’écran suivante montre le contenu d’un tel . Fichier ZIP: 

[![Contenu du projet de bibliothèque Android .zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Ce. Le fichier ZIP est ensuite ajouté au projet de liaison Java Xamarin.Android, comme le montre la capture d’écran suivante :

[![Zip ajouté au projet Java Binding](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Notez que l’action de construction de la . Le fichier ZIP a été automatiquement réglé sur **LibraryProjectZip**.

S’il ya tout . Fichiers JAR qui sont requis par le projet de bibliothèque Android, ils doivent être **ajoutés** au dossier Jars du projet Java Binding Library et **l’action build** set à **ReferenceJar**. Un exemple de ceci peut être vu dans la capture d’écran ci-dessous : 

[![Construire l’action définie sur ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Une fois ces étapes terminées, le projet de liaison Java Xamarin.Android peut être utilisé comme décrit précédemment dans ce document.

> [!NOTE]
> La compilation des projets de bibliothèque Android dans d’autres IDE n’est pas prise en charge pour le moment. D’autres IDE ne peuvent pas créer la même structure d’annuaire ou des fichiers dans le dossier **de bac** comme Eclipse. 

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons parcouru le processus de liaison d’un projet de bibliothèque Android. Nous avons construit le projet de bibliothèque Android à Eclipse, puis nous avons créé un fichier zip à partir de la **poubelle** et **res dossiers** du projet de bibliothèque Android. Ensuite, nous avons utilisé cette fermeture éclair pour créer un projet Xamarin.Android Java Binding. 
