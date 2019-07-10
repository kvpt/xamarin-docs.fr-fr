---
title: Workbooks interactifs
description: Ce document décrit comment utiliser Xamarin Workbooks pour créer des documents dynamiques contenant C# code pour tester, apprentissage, formation ou Explorer.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: a900d427ad6ac2a0e211ef4f00d2f014b13e5d1c
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674416"
---
# <a name="interactive-workbooks"></a>Workbooks interactifs

Vous pouvez utiliser des classeurs comme une application autonome, distincte de votre IDE.

Pour commencer à créer un nouveau classeur, exécutez l’application de classeurs. Si vous n’avez pas cela déjà installé, visitez le [Installation](~/tools/workbooks/install.md#install) page. Vous devrez créer un classeur dans la plateforme de votre choix, qui se connectent automatiquement à une application de l’agent vous permettant de visualiser votre document en temps réel.

Si l’application de classeurs est déjà en cours d’exécution, vous pouvez créer un nouveau document en accédant à **fichier > nouveau**.

Les classeurs peuvent être enregistrés et ouvert plus tard dans l’application. Vous pouvez les partager avec d’autres utilisateurs pour illustrer des idées, explorez les nouvelles API ou apprendre de nouveaux concepts.

## <a name="code-editing"></a>Modification de code

La fenêtre d’édition de code fournit la complétion de code, la coloration syntaxique, inline live-diagnostics et prise en charge de l’instruction sur plusieurs lignes.

[![](workbook-images/inspector-0.6.0-repl-small.png "La fenêtre d’édition de code fournit la complétion de code, la coloration syntaxique, inline live-diagnostics et prise en charge de l’instruction sur plusieurs lignes")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks sont enregistrés dans un `.workbook` fichier, qui est un fichier CommonMark avec des métadonnées en haut (consultez [les Types de fichiers de classeurs](#workbooks-files-types) pour plus d’informations sur la façon dont les classeurs peuvent être enregistrées).

### <a name="nuget-package-support"></a>Prise en charge de Package NuGet

De nombreux packages NuGet populaires sont pris en charge directement dans Xamarin Workbooks. Vous pouvez rechercher des packages en accédant à **fichier > Ajouter un Package**. Ajout d’un package s’affiche automatiquement `#r` instructions faisant référence à des assemblys de package, ce qui vous permet de les utiliser immédiatement.

Lorsque vous enregistrez un classeur avec des références de package, ces références sont également enregistrés. Si vous partagez le classeur avec une autre personne, il télécharge automatiquement les packages référencés.

Il existe certaines limitations connues avec prise en charge de package NuGet dans les classeurs :

- Bibliothèques natives sont pris en charge uniquement sur iOS et uniquement quand liée à la bibliothèque managée.
- Les packages qui dépendent de `.targets` fichiers ou des scripts PowerShell ne fonctionnera probablement pas comme prévu.
- Pour supprimer ou modifier une dépendance de package, modifier le manifeste du classeur avec un éditeur de texte. Gestion des packages approprié a été envoyé.

### <a name="xamarinforms-support"></a>Prise en charge de Xamarin.Forms

Si vous référencez le package Xamarin.Forms NuGet dans votre classeur, l’application de classeur change sa vue principale pour être basé sur Xamarin.Forms. Vous pouvez y accéder via `Xamarin.Forms.Application.Current.MainPage`.

L’onglet inspecteur de vue a également une prise en charge spéciale pour afficher la hiérarchie d’affichage pour vous aider à comprendre vos dispositions Xamarin.Forms.

## <a name="rich-text-editing"></a>Édition de texte enrichi

Vous pouvez modifier le texte autour de votre code à l’aide de l’éditeur de texte enrichi inclus, comme illustré ci-dessous :

![](workbook-images/inspector-0.6.2-editing.gif "Modifier le texte autour du code à l’aide de l’éditeur de texte enrichi intégré")

### <a name="markdown-authoring"></a>Création de contenu markdown

Les auteurs de classeurs peuvent parfois s’avérer plus facile de modifier directement la « source » du classeur CommonMark avec leur éditeur préféré.

N’oubliez pas que si vous modifiez et enregistrez votre classeur dans le client de classeurs, votre texte CommonMark peut être remis en forme.

Veuillez noter que nous permet d’activer les métadonnées YAML dans les fichiers de classeur, en raison de l’extension CommonMark `---` est réservé à cet effet. Si vous souhaitez créer [sauts thématiques](http://spec.commonmark.org/0.27/#thematic-break) dans votre texte, vous devez utiliser `***` ou `___` à la place. Sauts de ce type doivent être évitées dans la version 1.2 de classeurs et précédemment en raison d’un bogue au cours d’enregistrement.

### <a name="improvements-in-workbooks-13"></a>Améliorations dans les classeurs 1.3

Nous avons également étendu la syntaxe de devis de bloc Markdown légèrement pour améliorer la présentation. En ajoutant un emoji comme premier caractère dans votre devis de bloc, vous pouvez influencer la couleur d’arrière-plan de la demande :

- `> [!NOTE]`
    > s’affiche en tant que note avec un arrière-plan bleu
- `> [!IMPORTANT]`
    > s’affiche en tant qu’avertissement avec un arrière-plan jaune
- `> [!WARNING]`
    > seront restituées sous la forme d’un problème avec un arrière-plan rouge

Vous pouvez également lier aux en-têtes dans le document du classeur. Nous générons ancres pour chaque en-tête, avec l’ID d’ancrage est le texte d’en-tête, traité comme suit :

1. L’en-tête est en minuscules.
1. Tous les caractères à l’exception des caractères alphanumériques et des tirets sont supprimés.
1. Tous les espaces sont remplacés par des tirets.

Cela signifie qu’un en-tête, comme « En-tête Important » Obtient un id de `important-header` et peut être lié à en insérant un lien vers `#important-header` dans le classeur.

## <a name="document-structure"></a>Structure du document

### <a name="cell"></a>Cellule

Une unité discrète de contenu, qui représente le code exécutable ou markdown. Une cellule de code se compose de quatre sous-composants :

- Éditeur
  - Mémoire tampon
- Diagnostics du compilateur
- Sortie de la console
- Résultats de l’exécution

### <a name="editor"></a>Éditeur

Le composant de texte interactif d’une cellule. Pour les cellules de code, il s’agit de l’éditeur de code réelle avec la coloration syntaxique, etc. Pour les cellules de markdown il s’agit d’un éditeur de texte enrichi contenu avec une mise en forme sensible au contexte et la barre d’outils de création.

### <a name="buffer"></a>Mémoire tampon

Le contenu de texte d’un éditeur.

### <a name="compiler-diagnostics"></a>Diagnostics du compilateur

Les diagnostics générés lors de la compilation de code, rendu uniquement lorsque l’exécution explicite est demandée. Affiche immédiatement au-dessous de l’éditeur de cellule.

### <a name="console-output"></a>Sortie de la console

Toute sortie est écrite dans la sortie standard ou une erreur standard pendant l’exécution de la cellule. La sortie standard est rendue en texte noir et erreur standard s’affiche en texte rouge.

### <a name="execution-results"></a>Résultats de l’exécution

Représentations potentiellement interactives et enrichies des résultats d’une cellule seront affichera lors de la compilation a réussi, fournie par un résultat est réellement généré par l’exécution. Exceptions sont considérées comme des résultats dans ce contexte, dans la mesure où elles sont créées par réellement l’exécution de la compilation.

## <a name="workbooks-files-types"></a>Types de fichiers de classeurs

### <a name="plain-files"></a>Fichiers bruts

Par défaut, un classeur enregistre sous la forme d’un texte brut `.workbook` fichier contenant le texte au format CommonMark.

### <a name="packages"></a>Packages

Un package de classeur est un répertoire qui est nommé avec le `.workbook` extension.
Sur Mac Finder et dans la boîte de dialogue Ouvrir Xamarin Workbooks et du menu de fichiers récents, ce répertoire est reconnu comme s’il s’agissait d’un fichier.

Le répertoire doit contenir un `index.workbook` fichier, c'est-à-dire le classeur de texte brut qui est chargé dans Xamarin Workbooks. Le répertoire peut également contenir des ressources requises par `index.workbook`, y compris des images ou d’autres fichiers.

Si un texte brut `.workbook` ouverture du fichier qui référence des ressources à partir de son répertoire de même dans les classeurs 0.99.3 ou une version ultérieure, quand il est enregistré, il sera converti en un `.workbook` package. Cela est vrai sur Mac et Windows.

> [!NOTE]
> Les utilisateurs de Windows seront ouvre le `package.workbook\index.workbook` fichier directement, mais sinon le package se comporte comme sur Mac.

### <a name="archives"></a>Archives

Packages de classeur, répertoires, peuvent être difficiles de distribuer facilement via internet. La solution consiste aux archives de classeur. Une archive de classeur est un package zip compressé un classeur nommé avec le `.workbook` extension.

À partir de classeurs 1.1, lors de l’enregistrement d’un package de classeur, la boîte de dialogue Enregistrer offre le choix de l’enregistrement sous forme d’archive à la place. Classeurs 1.0 n’avaient aucun moyen intégré de création ou de l’enregistrement des archives.

Dans les classeurs 1.0, lors de l’ouverture d’une archive de classeur, il a été converti en toute transparence dans un package de classeur et le fichier zip a été perdu. Dans la version 1.1 de classeurs, le fichier zip reste. Quand l’utilisateur enregistre l’archive, il est remplacé par un nouveau fichier zip.

Vous pouvez créer une archive de classeur manuellement en double-cliquant sur un package de classeur et en sélectionnant **compresser** sur Mac, ou **envoyer à > dossier compressé (zippé)** sur Windows. Renommez le fichier zip pour obtenir un `.workbook` extension de fichier. Cela fonctionne uniquement avec les packages de classeur, les fichiers de classeur pas simple.

## <a name="related-links"></a>Liens associés

- [Bienvenue dans les classeurs](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
