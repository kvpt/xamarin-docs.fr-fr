---
title: Raccourcis clavier de l’éditeur de Xamarin Workbooks
description: Ce document décrit les raccourcis clavier utilisables dans l’éditeur de Xamarin Workbooks. En particulier, il examine différentes façons d’utiliser la clé de retour.
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 9904d0f9fb1acfc3c3c197b9881c2add00aba534
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285326"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Raccourcis clavier de l’éditeur de Xamarin Workbooks

## <a name="the-return-key-and-its-nuances"></a>La clé de retour et ses nuances

Le tableau suivant décrit les différentes combinaisons de touches permettant d’exécuter du code et de créer des démarques. Nous avons pris soin de choisir des liaisons de clé cohérentes et cohérentes qui sont à la fois familières et fluides.

|Combinaison de touches|Cellule de code|Cellule de la marque|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Si le signe insertion se trouve à la fin de la mémoire tampon de cellule et que la cellule peut être analysée correctement, elle est exécutée et les résultats sont affichés sous la mémoire tampon, et une nouvelle cellule de code est insérée et la cellule focalisée après la cellule exécutée.</p><p>Si l’analyse échoue, une nouvelle ligne est insérée dans la mémoire tampon. Les diagnostics du compilateur ne seront pas générés si l’analyse échoue.</p>|<p>Le <kbd>retour</kbd> présente un comportement différent selon le contexte de démarque au niveau du signe insertion.</p><ul><li>Si le signe insertion se trouve dans un bloc de code démarque, une nouvelle ligne littérale est insérée.</li><li>Si le signe insertion se trouve dans un bloc de liste des démarques, créer un élément de liste ou fractionner l’élément de liste actuel.</li><li>Si le signe insertion se trouve dans un autre type de bloc de démarque, créez un nouveau bloc de paragraphe ou fractionnez le bloc actuel.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Retour de commande</kbd></dd><dt>Gagn</dt><dd><kbd>Retour de contrôle</kbd></dd></dl>|<p>Tente toujours d’analyser et d’exécuter le contenu de la cellule. Si la compilation réussit, les résultats (y compris les exceptions d’exécution) s’affichent sous la mémoire tampon, et s’il n’y a aucune cellule suivante, une nouvelle est créée et focalisée.</p><p>S’il existe des erreurs de compilation, les Diagnostics s’affichent et la mémoire tampon reste concentrée avec la position du signe insertion inchangée.</p>|Insère et met en avant une nouvelle cellule de code après la cellule de la marque active.|
|<dl><dt>Mac</dt><dd><kbd>Commande-Shift-Return</kbd><dd><dt>Gagn</dt><dd><kbd>Ctrl-Maj-Retour</kbd></dd></dl>|Insère et met en avant une nouvelle cellule de démarque après la cellule active.|Même comportement que le <kbd>retour</kbd>|
|<kbd>Maj-Retour</kbd>|Insère toujours une nouvelle ligne, quel que soit l’emplacement ou le contenu du signe insertion.|Insère un saut de ligne physique dans le bloc de marque en cours.|
