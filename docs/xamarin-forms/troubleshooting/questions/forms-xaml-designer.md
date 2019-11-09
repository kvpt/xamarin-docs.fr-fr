---
title: Pourquoi le concepteur Visual Studio XAML ne fonctionne-t-il pas pour les fichiers XAML de Xamarin.Forms ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: ce318faab1af07b95a7769d81a506979b37a34e4
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73843000"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Pourquoi le concepteur Visual Studio XAML ne fonctionne-t-il pas pour les fichiers XAML de Xamarin.Forms ?

Xamarin. Forms ne prend actuellement pas en charge les concepteurs visuels pour les fichiers XAML. Pour cette raison, lorsque vous tentez d’ouvrir un fichier de formulaires XAML dans le *Concepteur d’interface utilisateur XAML* de Visual Studio ou dans le *Concepteur d’interface utilisateur XAML avec encodage*, le message d’erreur suivant est généré :

> «Impossible d’ouvrir le fichier avec l’éditeur sélectionné. Choisissez un autre éditeur.»

Cette limitation est décrite dans le Guide de [base du langage XAML Xamarin. Forms](~/xamarin-forms/xaml/xaml-basics/index.md) :

> « Il n’existe pas encore de concepteur visuel pour la génération de code XAML dans les applications Xamarin. Forms. par conséquent, tout le code XAML doit être écrit manuellement ».

Toutefois, le générateur d’aperçu XAML Xamarin. Forms peut être affiché en sélectionnant l’option de menu **afficher > autres Windows > Xamarin. Forms** (aperçu).
