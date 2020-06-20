---
title: Pourquoi le concepteur XAML Visual Studio ne fonctionne-t-il pas pour les Xamarin.Forms fichiers XAML ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9f9cf570da0d8e078d1d05e74dc0f65994080c6c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135887"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Pourquoi le concepteur XAML Visual Studio ne fonctionne-t-il pas pour les Xamarin.Forms fichiers XAML ?

Xamarin.Formsne prend actuellement pas en charge les concepteurs visuels pour les fichiers XAML. Pour cette raison, lorsque vous tentez d’ouvrir un fichier de formulaires XAML dans le *Concepteur d’interface utilisateur XAML* de Visual Studio ou dans le *Concepteur d’interface utilisateur XAML avec encodage*, le message d’erreur suivant est généré :

> «Impossible d’ouvrir le fichier avec l’éditeur sélectionné. Choisissez un autre éditeur.»

Cette limitation est décrite dans le Guide de [ Xamarin.Forms base du langage XAML](~/xamarin-forms/xaml/xaml-basics/index.md) :

> « Il n’existe pas encore de concepteur visuel pour la génération de code XAML dans Xamarin.Forms les applications. par conséquent, tout le code XAML doit être écrit manuellement ».

Toutefois, le Xamarin.Forms Générateur d’aperçu XAML peut être affiché en sélectionnant l’option **Afficher > autre Xamarin.Forms >** du générateur d’aperçu Windows.
