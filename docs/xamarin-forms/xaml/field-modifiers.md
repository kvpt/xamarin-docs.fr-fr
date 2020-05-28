---
title: Modificateurs de champ XAML dansXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: db00f522b71a8993ef0f7f6cf5070813ce07396a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138123"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificateurs de champ XAML dansXamarin.Forms

L' `x:FieldModifier` attribut namespace spécifie le niveau d’accès pour les champs générés pour les éléments XAML nommés. Les valeurs valides de l’attribut sont :

- `private`: spécifie que le champ généré pour l’élément XAML est accessible uniquement dans le corps de la classe dans laquelle il est déclaré.
- `public`: spécifie que le champ généré pour l’élément XAML n’a pas de restrictions d’accès.
- `protected`: spécifie que le champ généré pour l’élément XAML est accessible dans sa classe et par les instances de la classe dérivée.
- `internal`: spécifie que le champ généré pour l’élément XAML est accessible uniquement dans les types du même assembly.
- `notpublic`: spécifie que le champ généré pour l’élément XAML est accessible uniquement dans les types du même assembly.

Par défaut, si la valeur de l’attribut n’est pas définie, le champ généré pour l’élément sera `private` .

> [!NOTE]
> La valeur de l’attribut peut utiliser n’importe quelle casse, car elle sera convertie en minuscules par Xamarin.Forms .

Les conditions suivantes doivent être remplies pour qu’un `x:FieldModifier` attribut soit traité :

- L’élément XAML de niveau supérieur doit être un valide `x:Class` .
- L’élément XAML actuel a un `x:Name` spécifié.

Le code XAML suivant montre des exemples de définition de l’attribut :

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> L' `x:FieldModifier` attribut ne peut pas être utilisé pour spécifier le niveau d’accès d’une classe XAML.
