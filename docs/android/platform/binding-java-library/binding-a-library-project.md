---
title: Liaison d’un projet de bibliothèque Eclipse
description: Cette procédure pas à pas explique comment utiliser les modèles de projet Xamarin. Android pour lier un projet de bibliothèque Android Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 54de1cde65e9c1ab9cb758c057a87c6b15c4da54
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853038"
---
# <a name="binding-an-eclipse-library-project"></a>Liaison d’un projet de bibliothèque Eclipse

> [!IMPORTANT]
> Nous étudions actuellement l’utilisation de la liaison personnalisée sur la plateforme Xamarin. Veuillez suivre [**ce questionnaire**](https://www.surveymonkey.com/r/KKBHNLT) pour informer les futurs efforts de développement.

_Cette procédure pas à pas explique comment utiliser les modèles de projet Xamarin. Android pour lier un projet de bibliothèque Android Eclipse._

## <a name="overview"></a>Vue d’ensemble

Alors. Les fichiers AAR deviennent de plus en plus la norme pour la distribution de bibliothèque Android. dans certains cas, il est nécessaire de créer une liaison pour un *projet de bibliothèque Android*. Les projets de bibliothèque Android sont des projets Android spéciaux qui contiennent du code partageable et des ressources qui peuvent être référencées par des projets d’application Android. En règle générale, vous créez une liaison à un projet de bibliothèque Android lorsque la bibliothèque est créée dans l’IDE Eclipse.
Cette procédure pas à pas fournit des exemples de création d’un projet de bibliothèque Android. ZIP à partir de la structure de répertoires d’un projet Eclipse.

Les projets de bibliothèque Android sont différents des projets Android classiques en ce qu’ils ne sont pas compilés dans un APK et ne sont pas, de leur propre, déployables sur un appareil. Au lieu de cela, un projet de bibliothèque Android est destiné à être référencé par un projet d’application Android. Lors de la génération d’un projet d’application Android, le projet de bibliothèque Android est compilé en premier. Le projet d’application Android est ensuite absorbé dans le projet de bibliothèque Android compilé et inclut le code et les ressources dans le APK pour la distribution. En raison de cette différence, la création d’une liaison pour un projet de bibliothèque Android est légèrement différente de la création d’une liaison pour Java. JAR ou. Fichier AAR.

## <a name="walkthrough"></a>Procédure pas à pas

Pour utiliser un projet de bibliothèque Android dans un projet de liaison Java Xamarin. Android, il est tout d’abord nécessaire de créer le projet de bibliothèque Android dans Eclipse. La capture d’écran suivante montre un exemple d’un projet de bibliothèque Android après compilation : 

[![Exemple de projet de bibliothèque dans Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Notez que le code source du projet de bibliothèque Android a été compilé en un temporaire. Fichier JAR nommé **Android-mapviewballoons. jar**, et dont les ressources ont été copiées dans le dossier **bin/res/** . 

Une fois que le projet de bibliothèque Android a été compilé dans Eclipse, il peut être lié à l’aide d’un projet de liaison Xamarin. Android Java. Premier a. Le fichier ZIP doit être créé, qui contient les dossiers **bin** et **res** du projet de bibliothèque Android. Il est important de supprimer le sous-répertoire **crunch** de la façon dont les ressources se trouvent dans **bin/res**. La capture d’écran suivante montre le contenu d’un tel. Fichier ZIP : 

[![Contenu de la bibliothèque Android Project. zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Voici. Le fichier ZIP est ensuite ajouté au projet de liaison Java Xamarin. Android, comme illustré dans la capture d’écran suivante :

[![Zip ajouté au projet de liaison Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Notez que l’action de génération du. Le fichier ZIP a été automatiquement défini sur **LibraryProjectZip**.

S’il en existe un. Fichiers JAR requis par le projet de bibliothèque Android, ils doivent être ajoutés au dossier **jar** du projet de bibliothèque de liaisons Java et l' **action de génération** doit être définie sur **ReferenceJar**. Vous pouvez voir un exemple dans la capture d’écran ci-dessous : 

[![Action de génération définie sur ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Une fois ces étapes terminées, le projet de liaison Java Xamarin. Android peut être utilisé comme décrit précédemment dans ce document.

> [!NOTE]
> La compilation des projets de bibliothèque Android dans d’autres IDE n’est pas prise en charge pour l’instant. D’autres IDE ne peuvent pas créer la même structure de répertoires ou les mêmes fichiers dans le dossier **bin** que Eclipse. 

## <a name="summary"></a>Résumé

Dans cet article, nous avons parcouru le processus de liaison d’un projet de bibliothèque Android. Nous avons créé le projet de bibliothèque Android dans Eclipse, puis nous avons créé un fichier zip à partir des dossiers **bin** et **res** du projet de bibliothèque Android. Nous avons ensuite utilisé ce fichier zip pour créer un projet de liaison Java Xamarin. Android. 
