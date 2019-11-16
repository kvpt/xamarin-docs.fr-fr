---
title: Localisation Xamarin.Forms
description: Le framework de localisation .NET intégré peut être utilisé pour créer des applications multilingues multiplateformes avec Xamarin.Forms. Le texte et les images peuvent être localisés, et les applications peuvent prendre en charge un sens de flux de droite à gauche.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: b580c6e41aa689ff8fcea698c40d7aaf5f2ca050
ms.sourcegitcommit: 233aaa1ac3d8f40c09b6daf6d944ea0b4cbee381
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135265"
---
# <a name="xamarinforms-localization"></a>Localisation Xamarin.Forms

_Le framework de localisation .NET intégré peut être utilisé pour créer des applications multilingues multiplateformes avec Xamarin.Forms._

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Localisation de la chaîne et de l’image Xamarin. Forms](text.md)

Le mécanisme intégré de localisation des applications .NET utilise des [fichiers RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) ainsi que les classes des espaces de noms `System.Resources` et `System.Globalization`. Les fichiers RESX contenant les chaînes traduites sont incorporés dans l’assembly Xamarin.Forms, ainsi qu’une classe générée par le compilateur qui fournit un accès fortement typé aux traductions. Vous pouvez ensuite récupérer le texte traduit dans le code.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localisation de droite à gauche](right-to-left.md)

La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. La localisation de droite à gauche ajoute la prise en charge du sens de flux de droite à gauche pour les applications Xamarin.Forms.
