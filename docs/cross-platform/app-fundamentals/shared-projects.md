---
title: Utiliser des projets partagés pour partager du code
description: Les projets partagés vous permettent d’écrire du code commun qui est référencé par un certain nombre de projets d’application différents. Le code est compilé dans chacun des projets qui le référencent et peut inclure des directives de compilateur permettant d’incorporer des fonctionnalités propres à la plateforme dans la base de code partagée.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: ed58b0810d3c4fd3a3dd99cddd16227f9ac30273
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68739056"
---
# <a name="shared-projects-code-sharing"></a>Partage de code des projets partagés

_Les projets partagés vous permettent d’écrire du code commun qui est référencé par un certain nombre de projets d’application différents. Le code est compilé dans le cadre de chaque projet de référence et peut inclure des directives de compilateur pour incorporer les fonctionnalités spécifiques à la plateforme dans la base de code partagée._

Les projets partagés (parfois appelés projets de ressources partagées) vous permettent d’écrire du code partagé entre plusieurs projets cibles, y compris des applications Xamarin.

Ils prennent en charge les directives de compilateur pour vous permettre d’inclure du code spécifique à la plateforme à compiler dans un sous-ensemble des projets qui référencent le projet partagé. Il existe également une prise en charge de l’IDE pour faciliter la gestion des directives du compilateur et visualiser l’aspect du code dans chaque application.

Si vous avez utilisé la liaison de fichiers dans le passé pour partager du code entre des projets, les projets partagés fonctionnent de la même manière, mais avec une prise en charge améliorée de l’IDE.

## <a name="what-is-a-shared-project"></a>Qu’est-ce qu’un projet partagé ?

Contrairement à la plupart des autres types de projets, un projet partagé n’a pas de sortie (sous forme de DLL). au lieu de cela, le code est compilé dans chaque projet qui le référence. Cela est illustré dans le diagramme ci-dessous, de manière conceptuelle, le contenu entier du projet partagé est « copié dans » chaque projet de référencement et compilé comme s’il s’agissait d’une partie d’entre eux.

![](shared-projects-images/sharedassetproject.png "Architecture de projet partagé")

Le code d’un projet partagé peut contenir des directives de compilateur qui activent ou désactivent des sections de code selon le projet d’application qui utilise le code, ce qui est suggéré par les zones de plateforme de couleur dans le diagramme.

Un projet partagé n’est pas compilé seul, il existe simplement comme un regroupement de fichiers de code source qui peuvent être inclus dans d’autres projets. Lorsqu’il est référencé par un autre projet, le code est effectivement compilé dans le *cadre* de ce projet. Les projets partagés ne peuvent pas faire référence à un autre type de projet (y compris d’autres projets partagés).

Notez que les projets d’application Android ne peuvent pas référencer d’autres projets d’application Android. par exemple, un projet de test unitaire Android ne peut pas faire référence à un projet d’application Android. Pour plus d’informations sur cette limitation, consultez ce [Forum de discussion](http://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Procédure pas à pas Visual Studio pour Mac

Cette section explique comment créer et utiliser un projet partagé à l’aide de Visual Studio pour Mac. Reportez-vous à la section [exemple de projet partagé](#Shared_Project_Example) pour obtenir un exemple complet.

## <a name="creating-a-shared-project"></a>Création d’un projet partagé

Pour créer un nouveau projet partagé, accédez à **fichier > nouvelle solution...** (ou cliquez avec le bouton droit sur une solution existante et choisissez **ajouter > ajouter un nouveau projet...** ) :

[![Nouveau projet partagé](shared-projects-images/xs-newsolution-sml.png "Nouvelle solution")](shared-projects-images/xs-newsolution.png#lightbox)

Dans l’écran suivant, choisissez le nom du projet, puis cliquez sur **créer**.

Un nouveau projet partagé est illustré ci-dessous-Notez qu’il n’y a aucune référence ni aucun nœud de composant ; celles-ci ne sont pas prises en charge pour les projets partagés.

![Projet partagé vide](shared-projects-images/xs-empty.png "Projet partagé vide")

Pour qu’un projet partagé soit utile, il doit être référencé par au moins un projet de génération (par exemple, une bibliothèque ou une application iOS ou Android, ou un projet PCL). Un projet partagé n’est pas compilé lorsqu’il ne fait pas référence à celui-ci. par conséquent, la syntaxe (ou toute autre erreur) n’est pas mise en surbrillance tant qu’il n’a pas été référencé par autre chose.

L’ajout d’une référence à un projet partagé s’effectue de la même façon que le référencement d’un projet de bibliothèque standard. Cette capture d’écran montre un projet Xamarin. iOS référençant un projet partagé.

![](shared-projects-images/xs-reference.png "Référence de projet au projet partagé")

Une fois que le projet partagé est référencé par une autre bibliothèque ou application, vous pouvez générer la solution et afficher toutes les erreurs dans le code. Quand le projet partagé est référencé par _deux ou plusieurs_ autres projets, un menu s’affiche dans l’angle supérieur gauche de l’éditeur de code source qui montre le choix des projets qui font référence à ce fichier.

## <a name="shared-project-options"></a>Options de projet partagé

Lorsque vous cliquez avec le bouton droit sur un projet partagé et choisissez **options** , il y a moins de paramètres que d’autres types de projets. Étant donné que les projets partagés ne sont pas compilés (seuls), vous ne pouvez pas définir les options de sortie ou de compilateur, les configurations de projet, la signature d’assembly ou les commandes personnalisées. Le code d’un projet partagé hérite effectivement de ces valeurs de ce qui y fait référence.

L’écran d' **options** est illustré ci-dessous : le **nom** du projet et l' **espace de noms par défaut** sont les deux seuls paramètres que vous allez généralement modifier.

![](shared-projects-images/xs-sharedprojectoptions.png "Options de projet partagé")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Procédure pas à pas Visual Studio

Cette section explique comment créer et utiliser un projet partagé à l’aide de Visual Studio. Reportez-vous à la section [exemple de projet partagé](#Shared_Project_Example) pour une implémentation complète.

### <a name="creating-a-shared-project"></a>Création d’un projet partagé

Pour créer un projet partagé, accédez à **fichier** > **nouveau** > **projet**.

Dans Visual Studio 2019, entrez **Shared** dans la zone de recherche de la page **créer un nouveau projet** . Sélectionnez le modèle de **projet partagé** , puis cliquez sur **suivant**. Entrez un nom pour le projet, puis sélectionnez **créer**.

Dans Visual Studio 2017, sélectionnez le modèle de **projet partagé** , puis choisissez un nom pour le projet.

![Modèle de projet partagé dans Visual Studio 2017](shared-projects-images/vs-newsolution.png)

Vous pouvez également ajouter un nouveau projet partagé à une solution existante en cliquant avec le bouton droit sur le fichier de solution et en choisissant **ajouter > nouveau projet**. Un nouveau projet partagé apparaît comme indiqué ci-dessous (après l’ajout d’un fichier de classe). Notez qu’il n’existe aucune référence ni aucun nœud de composant ; celles-ci ne sont pas prises en charge pour les projets partagés.

![](shared-projects-images/vs-empty.png "Projet partagé vide")

Pour qu’un projet partagé soit utile, il doit être référencé par au moins un projet de génération (par exemple, une bibliothèque ou une application iOS ou Android, ou un projet PCL). Un projet partagé n’est pas compilé lorsqu’il ne fait pas référence à celui-ci. par conséquent, la syntaxe (ou toute autre erreur) n’est pas mise en surbrillance tant qu’il n’a pas été référencé par autre chose.

L’ajout d’une référence à un projet partagé s’effectue de la même façon que le référencement d’un projet de bibliothèque standard. Cette capture d’écran montre un projet Xamarin. iOS référençant un projet partagé.

![](shared-projects-images/vs-reference.png "Référence de projet au projet partagé")

Une fois que le projet partagé est référencé par une autre bibliothèque ou application, vous pouvez générer la solution et afficher toutes les erreurs dans le code. Quand le projet partagé est référencé par _deux ou plusieurs_ autres projets, un menu s’affiche dans l’angle supérieur gauche de l’éditeur de code source pour voir quels projets font référence au fichier de code actuel.

### <a name="shared-project-properties"></a>Propriétés du projet partagé

Lorsque vous sélectionnez un projet partagé, il y a moins de paramètres dans le volet Propriétés que d’autres types de projets. Étant donné que les projets partagés ne sont pas compilés (seuls), vous ne pouvez pas définir les options de sortie ou de compilateur, les configurations de projet, la signature d’assembly ou les commandes personnalisées. Le code d’un projet partagé hérite effectivement de ces valeurs de ce qui y fait référence.

Le volet **Propriétés** est indiqué ci-dessous : l' **espace de noms racine** est le seul paramètre que vous pouvez modifier.

![](shared-projects-images/vs-sharedprojectproperties.png "Propriétés du projet partagé")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Exemple de projet partagé

L' exemple [Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky) utilise un projet partagé pour contenir le code commun utilisé par les applications iOS, Android et Windows Phone. Les `SQLite.cs` fichiers de `TaskRepository.cs` code source et utilisent les directives de compilateur (par exemple, `#if __ANDROID__`) pour produire différentes sorties pour chacune des applications qui les référencent.

La structure complète de la solution est illustrée ci-dessous (dans Visual Studio pour Mac et Visual Studio, respectivement) :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Solution Visual Studio pour Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Solution Visual Studio")

-----

Le projet Windows Phone peut être parcouru à partir d’Visual Studio pour Mac, même si ce type de projet n’est pas pris en charge pour la compilation dans Visual Studio pour Mac.

Les applications en cours d’exécution sont présentées ci-dessous :

![](shared-projects-images/example.png "exemples iOS, Android, Windows Phone")

## <a name="summary"></a>Récapitulatif

Ce document décrit le fonctionnement des projets partagés, comment ils peuvent être créés et utilisés dans Visual Studio pour Mac et Visual Studio, et introduit un exemple d’application simple qui illustre un projet partagé en action.

## <a name="related-links"></a>Liens associés

- [Exemple d’application Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Bibliothèques de classes portables (exemple)](~/cross-platform/app-fundamentals/pcl.md)
- [Options de partage de code (exemple)](~/cross-platform/app-fundamentals/code-sharing.md)
