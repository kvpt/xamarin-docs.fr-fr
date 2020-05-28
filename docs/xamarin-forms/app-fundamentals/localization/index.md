---
title: Xamarin.FormsLocaliz
description: L’infrastructure de localisation .NET intégrée peut être utilisée pour créer des applications multilingues multiplateformes avec Xamarin.Forms . Le texte et les images peuvent être localisés, et les applications peuvent prendre en charge un sens de flux de droite à gauche.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dddb80e8fb683547d2bf6ba0b74e870fe240695c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137564"
---
# <a name="xamarinforms-localization"></a>Xamarin.FormsLocaliz

_L’infrastructure de localisation .NET intégrée peut être utilisée pour créer des applications multilingues multiplateformes avec Xamarin.Forms ._

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Xamarin.FormsLocalisation de chaînes et d’images](text.md)

Le mécanisme intégré de localisation des applications .NET utilise des [fichiers RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) ainsi que les classes des espaces de noms `System.Resources` et `System.Globalization`. Les fichiers RESX contenant des chaînes traduites sont incorporés dans l' Xamarin.Forms assembly, avec une classe générée par le compilateur qui fournit un accès fortement typé aux traductions. Vous pouvez ensuite récupérer le texte traduit dans le code.

## <a name="right-to-left-localization"></a>[Localisation de droite à gauche](right-to-left.md)

La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. La localisation de droite à gauche ajoute la prise en charge du sens du déroulement de droite à gauche aux Xamarin.Forms applications.
