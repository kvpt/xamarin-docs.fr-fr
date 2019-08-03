---
title: Modificateurs de champ XAML dans Xamarin. Forms
description: L’attribut d’espace de noms x:FieldModifier spécifie le niveau d’accès pour les champs générés pour les éléments XAML nommés.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/02/2019
ms.openlocfilehash: 0f6050de943ca9878cf41b448d44bf222689be56
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739447"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificateurs de champ XAML dans Xamarin. Forms

L' `x:FieldModifier` attribut namespace spécifie le niveau d’accès pour les champs générés pour les éléments XAML nommés. Les valeurs valides de l’attribut sont:

- `private`: spécifie que le champ généré pour l’élément XAML est accessible uniquement dans le corps de la classe dans laquelle il est déclaré.
- `public`: spécifie que le champ généré pour l’élément XAML n’a pas de restrictions d’accès.
- `protected`: spécifie que le champ généré pour l’élément XAML est accessible dans sa classe et par les instances de la classe dérivée.
- `internal`: spécifie que le champ généré pour l’élément XAML est accessible uniquement dans les types du même assembly.
- `notpublic`: spécifie que le champ généré pour l’élément XAML est accessible uniquement dans les types du même assembly.

Par défaut, si la valeur de l’attribut n’est pas définie, le champ généré pour l’élément `private`sera.

> [!NOTE]
> La valeur de l’attribut peut utiliser n’importe quelle casse, car elle sera convertie en minuscules par Xamarin. Forms.

Les conditions suivantes doivent être remplies pour qu' `x:FieldModifier` un attribut soit traité:

- L’élément XAML de niveau supérieur doit être un valide `x:Class`.
- L’élément XAML actuel a un `x:Name` spécifié.

Le code XAML suivant montre des exemples de définition de l’attribut:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> L' `x:FieldModifier` attribut ne peut pas être utilisé pour spécifier le niveau d’accès d’une classe XAML.
