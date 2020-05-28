---
title: Texte dansXamarin.Forms
description: Xamarin.Formsa trois vues principales pour travailler avec du texte, et cet article explique comment les utiliser pour entrer et afficher du texte dans les Xamarin.Forms applications.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 579ff44e9c58d7eea538d5478e99b4c480d44ac0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136186"
---
# <a name="text-in-xamarinforms"></a>Texte dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Utilisation Xamarin.Forms de pour entrer ou afficher du texte._

Xamarin.Formsa trois vues principales pour travailler avec du texte :

- **[Étiquette](#Label)** &mdash; pour présenter un texte unique ou multiligne. Peut afficher du texte avec plusieurs options de mise en forme sur la même ligne.
- **[Entrée](#Entry)** &mdash; pour entrer du texte qui n’est qu’une seule ligne. L’entrée a un mode de mot de passe.
- **[Éditeur](#Editor)** &mdash; pour entrer du texte qui peut prendre plus d’une ligne.

L’apparence du texte peut être modifiée à l’aide de [styles](#Styles) intégrés ou personnalisés, et certains contrôles prennent en charge les [polices](#Fonts)personnalisées.

<a name="Label" />

## <a name="label"></a>[Étiquette](label.md)

La `Label` vue est utilisée pour afficher du texte. Il peut afficher plusieurs lignes de texte ou une seule ligne de texte. `Label`peut présenter du texte avec plusieurs options de mise en forme utilisées dans Inline. La vue étiquette peut encapsuler ou tronquer le texte lorsqu’elle ne peut pas être ajustée sur une seule ligne.

![Exemple d’étiquette](images/label.png)

Pour plus d’informations, consultez l’article [intitulé étiquette](label.md) .

Pour plus d’informations sur la personnalisation de la police utilisée dans une étiquette, consultez [polices](fonts.md).

<a name="Entry" />

## <a name="entry"></a>[Entrée](entry.md)

`Entry`est utilisé pour accepter une entrée de texte sur une seule ligne. `Entry`permet de contrôler les couleurs et les polices. `Entry`dispose d’un mode de mot de passe et peut afficher le texte de l’espace réservé jusqu’à ce que le texte soit entré.

![Exemple d’entrée](images/entry.png)

Pour plus d’informations, consultez l’article d' [entrée](entry.md) .

Notez que, contrairement à `Label` , `Entry` ne peut pas avoir de paramètres de police personnalisés.

<a name="Editor" />

## <a name="editor"></a>[Éditeur](editor.md)

`Editor`est utilisé pour accepter une entrée de texte sur plusieurs lignes. `Editor`permet de contrôler les couleurs et les polices.

![Exemple d’éditeur](images/editor.png)

Pour plus d’informations, consultez l’article de l' [éditeur](editor.md) .

<a name="Fonts" />

## <a name="fonts"></a>[Fonts](fonts.md)

De nombreux contrôles prennent en charge des paramètres de police différents à l’aide des polices intégrées sur chaque plateforme, ou des polices personnalisées incluses avec votre application. Consultez l’article sur les [polices](fonts.md) pour obtenir des informations plus détaillées.

<a name="Styles" />

## <a name="styles"></a>[Styles](styles.md)

Reportez-vous à la rubrique [utilisation de styles](~/xamarin-forms/user-interface/styles/index.md) pour apprendre à configurer la police, la [couleur](~/xamarin-forms/user-interface/colors.md)et d’autres propriétés d’affichage qui s’appliquent à plusieurs contrôles.

## <a name="related-links"></a>Liens connexes

- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
