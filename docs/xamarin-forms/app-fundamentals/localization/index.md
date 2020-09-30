---
title: Xamarin.Forms Localiz
description: L’infrastructure de localisation .NET intégrée peut être utilisée pour créer des applications multilingues multiplateformes avec Xamarin.Forms . Le texte et les images peuvent être localisés, et les applications peuvent prendre en charge un sens de flux de droite à gauche.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5dbcc63eb162454845b78fc24a3beb0ca8296453
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558152"
---
# <a name="no-locxamarinforms-localization"></a>Xamarin.Forms Localiz

_L’infrastructure de localisation .NET intégrée peut être utilisée pour créer des applications multilingues multiplateformes avec Xamarin.Forms ._

## <a name="no-locxamarinforms-string-and-image-localization"></a>[Xamarin.Forms Localisation de chaînes et d’images](text.md)

Le mécanisme intégré de localisation des applications .NET utilise des [fichiers RESX](/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) ainsi que les classes des espaces de noms `System.Resources` et `System.Globalization`. Les fichiers RESX contenant des chaînes traduites sont incorporés dans l' Xamarin.Forms assembly, avec une classe générée par le compilateur qui fournit un accès fortement typé aux traductions. Vous pouvez ensuite récupérer le texte traduit dans le code.

## <a name="right-to-left-localization"></a>[Localisation de droite à gauche](right-to-left.md)

La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. La localisation de droite à gauche ajoute la prise en charge du sens du déroulement de droite à gauche aux Xamarin.Forms applications.