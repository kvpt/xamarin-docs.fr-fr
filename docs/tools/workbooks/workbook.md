---
title: Workbooks interactifs
description: Ce document explique comment utiliser Xamarin Workbooks pour créer des documents dynamiques contenant C# du code pour l’expérimentation, l’enseignement, la formation ou l’exploration.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: a6ca347c231d001cab521d7280a66b714b6a5aef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029561"
---
# <a name="interactive-workbooks"></a>Workbooks interactifs

Vous pouvez utiliser des classeurs comme une application autonome, séparée de votre IDE.

Pour commencer à créer un nouveau classeur, exécutez l’application classeurs. Si vous ne l’avez pas déjà fait, accédez à la page d' [installation](~/tools/workbooks/install.md#install) . Vous serez invité à créer un classeur dans votre plateforme de votre choix, qui se connectera automatiquement à une application d’agent, ce qui vous permettra de visualiser votre document en temps réel.

Si l’application classeurs est déjà en cours d’exécution, vous pouvez créer un nouveau document en accédant à **fichier > nouveau**.

Les classeurs peuvent être enregistrés et rouverts plus tard au sein de l’application. Vous pouvez ensuite les partager avec d’autres personnes pour démontrer des idées, explorer de nouvelles API ou enseigner de nouveaux concepts.

## <a name="code-editing"></a>Modification du code

La fenêtre d’édition de code fournit la saisie semi-automatique du code, la coloration de la syntaxe, les diagnostics en direct inline et la prise en charge des instructions sur plusieurs lignes.

[![](workbook-images/inspector-0.6.0-repl-small.png "The code editing window provides code completion, syntax coloring, inline live-diagnostics, and multi-line statement support")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks sont enregistrés dans un fichier `.workbook`, qui est un fichier CommonMark avec certaines métadonnées en haut (pour plus d’informations sur la façon dont les classeurs peuvent être enregistrés, consultez [types de fichiers de classeurs](#workbooks-files-types) ).

### <a name="nuget-package-support"></a>Prise en charge des packages NuGet

De nombreux packages NuGet populaires sont pris en charge directement dans Xamarin Workbooks. Vous pouvez rechercher des packages en accédant à **fichier > ajouter un package**. L’ajout d’un package entraîne l’insertion automatique des instructions `#r` faisant référence à des assemblys de package, ce qui vous permet de les utiliser immédiatement.

Lorsque vous enregistrez un classeur avec des références de package, ces références sont également enregistrées. Si vous partagez le classeur avec une autre personne, il télécharge automatiquement les packages référencés.

Il existe des limitations connues avec la prise en charge des packages NuGet dans les classeurs :

- Les bibliothèques natives sont prises en charge uniquement sur iOS et uniquement lorsqu’elles sont liées à la bibliothèque managée.
- Les packages qui dépendent de `.targets` fichiers ou de scripts PowerShell ne fonctionneront probablement pas comme prévu.
- Pour supprimer ou modifier une dépendance de package, modifiez le manifeste du classeur à l’aide d’un éditeur de texte. La gestion appropriée des packages est en route.

### <a name="xamarinforms-support"></a>Prise en charge de Xamarin. Forms

Si vous référencez le package NuGet Xamarin. Forms dans votre classeur, l’application de classeur change sa vue principale en base Xamarin. Forms. Vous pouvez y accéder par le biais de `Xamarin.Forms.Application.Current.MainPage`.

L’onglet inspecteur d’affichage offre également une prise en charge spéciale de l’affichage de la hiérarchie Xamarin. Forms pour vous aider à comprendre vos dispositions.

## <a name="rich-text-editing"></a>Modification de texte enrichi

Vous pouvez modifier le texte autour de votre code à l’aide de l’éditeur de texte enrichi inclus, comme illustré ci-dessous :

![](workbook-images/inspector-0.6.2-editing.gif "Edit the text around the code using the built-in rich text editor")

### <a name="markdown-authoring"></a>Création de la démarque

Les auteurs de classeurs peuvent parfois trouver plus facile de modifier directement la « source » CommonMark du classeur à l’aide de leur éditeur favori.

Sachez que si vous modifiez et enregistrez votre classeur dans le client classeurs, votre texte CommonMark peut être reformaté.

Notez qu’en raison de l’extension CommonMark que nous utilisons pour activer les métadonnées YAML dans les fichiers de classeur, `---` est réservé à cet effet. Si vous souhaitez créer des [sauts thématiques](https://spec.commonmark.org/0.27/#thematic-break) dans votre texte, vous devez utiliser `***` ou `___` à la place. Ces interruptions doivent être évitées dans les classeurs 1,2 et antérieurs en raison d’un bogue pendant l’enregistrement.

### <a name="improvements-in-workbooks-13"></a>Améliorations des classeurs 1,3

Nous avons également étendu légèrement la syntaxe du guillemet de bloc de la marque pour améliorer la présentation. En ajoutant un Emoji comme premier caractère de votre citation de bloc, vous pouvez influencer la couleur d’arrière-plan de la citation :

- `> [!NOTE]`
    > s’affiche en tant que note avec un arrière-plan bleu
- `> [!IMPORTANT]`
    > s’affiche en tant qu’avertissement avec un arrière-plan jaune
- `> [!WARNING]`
    > s’affiche en tant que problème avec un arrière-plan rouge

Vous pouvez également créer un lien vers les en-têtes dans le document du classeur. Nous générons des ancres pour chaque en-tête, l’ID d’ancrage étant le texte d’en-tête, traité comme suit :

1. L’en-tête est en minuscules.
1. Tous les caractères à l’exception des caractères alphanumériques et des tirets sont supprimés.
1. Tous les espaces sont remplacés par des tirets.

Cela signifie qu’un en-tête comme « en-tête important » obtient un ID de `important-header` et peut être lié à en insérant un lien vers `#important-header` dans le classeur.

## <a name="document-structure"></a>Structure du document

### <a name="cell"></a>Cellules

Unité discrète de contenu, qui représente le code exécutable ou la démarque. Une cellule de code est composée de quatre sous-composants au maximum :

- Éditeur
  - Mémoire tampon
- Diagnostics du compilateur
- Sortie de la console
- Résultats de l’exécution

### <a name="editor"></a>Éditeur

Composant de texte interactif d’une cellule. Pour les cellules de code, il s’agit de l’éditeur de code réel avec mise en surbrillance de la syntaxe, etc. Pour les cellules de démarque, il s’agit d’un éditeur de contenu de texte enrichi avec une barre d’outils de mise en forme et de création sensible au contexte.

### <a name="buffer"></a>Mémoire tampon

Contenu de texte réel d’un éditeur.

### <a name="compiler-diagnostics"></a>Diagnostics du compilateur

Diagnostics générés lors de la compilation du code, rendu uniquement lorsque l’exécution explicite est demandée. Affiché immédiatement sous l’éditeur de cellule.

### <a name="console-output"></a>Sortie de la console

Toute sortie écrite dans une erreur de sortie standard ou standard pendant l’exécution de la cellule. La sortie standard est rendue en texte noir et l’erreur standard est rendue en texte rouge.

### <a name="execution-results"></a>Résultats de l’exécution

Les représentations riches et potentiellement interactives des résultats d’une cellule sont rendues en cas de réussite de la compilation, à condition qu’un résultat soit effectivement produit par l’exécution. Les exceptions sont considérées comme des résultats dans ce contexte, car elles sont générées suite à l’exécution réelle de la compilation.

## <a name="workbooks-files-types"></a>Types de fichiers de classeurs

### <a name="plain-files"></a>Fichiers bruts

Par défaut, un classeur enregistre sous forme de texte brut `.workbook` fichier contenant du texte au format CommonMark.

### <a name="packages"></a>Packages

Un package de classeur est un répertoire nommé avec l’extension `.workbook`.
Sur le Finder de Mac et dans le menu Xamarin Workbooks ouvrir boîte de dialogue et fichiers récents, ce répertoire est reconnu comme s’il s’agissait d’un fichier.

Le répertoire doit contenir un fichier `index.workbook`, qui est le classeur de texte brut réel qui sera chargé dans Xamarin Workbooks. Le répertoire peut également contenir des ressources requises par `index.workbook`, y compris des images ou d’autres fichiers.

Si un fichier de `.workbook` de texte brut qui référence des ressources à partir de son répertoire est ouvert dans des classeurs 0.99.3 ou ultérieur, lorsqu’il est enregistré, il est converti en package `.workbook`. Cela est vrai sur Mac et Windows.

> [!NOTE]
> Les utilisateurs Windows ouvrent le fichier `package.workbook\index.workbook` directement, mais dans le cas contraire, le package se comporte de la même façon que sur Mac.

### <a name="archives"></a>Archives

Les packages de classeurs, les répertoires, peuvent être difficiles à distribuer facilement sur Internet. La solution est l’archivage des classeurs. Une archive de classeur est un package de classeur compressé, nommé avec l’extension `.workbook`.

À compter des classeurs 1,1, lors de l’enregistrement d’un package de classeur, la boîte de dialogue Enregistrer vous permet de choisir d’enregistrer à la place en tant qu’archive. Les classeurs 1,0 ne disposaient pas d’un moyen intégré de créer ou d’enregistrer des archives.

Dans les classeurs 1,0, quand une archive de classeur a été ouverte, elle a été convertie de manière transparente en package de classeur, et le fichier zip a été perdu. Dans les classeurs 1,1, le fichier zip reste. Lorsque l’utilisateur enregistre l’archive, il est remplacé par un nouveau fichier zip.

Vous pouvez créer une archive de classeurs manuellement en cliquant avec le bouton droit sur un package de classeurs et en sélectionnant **compresser** sur Mac, ou **Envoyer à > dossier compressé (zippé)** sur Windows. Ensuite, renommez le fichier zip pour obtenir une extension de fichier `.workbook`. Cela fonctionne uniquement avec les packages de classeurs, pas avec les fichiers de classeur ordinaires.

## <a name="related-links"></a>Liens associés

- [Bienvenue dans les classeurs](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
