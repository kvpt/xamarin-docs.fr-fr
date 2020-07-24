---
title: Informations de référence sur les contrôles
description: 'Description de tous les éléments de l’interface utilisateur utilisés pour construire une :::no-loc(Xamarin.Forms)::: application. Cet article répertorie les groupes de contrôle qui composent l’interface utilisateur d’une :::no-loc(Xamarin.Forms)::: application.'
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: b9e3f8b61ebfc73a26a967b83f60e005a652563f
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997371"
---
# <a name="controls-reference"></a>Informations de référence sur les contrôles

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

L’interface utilisateur d’une :::no-loc(Xamarin.Forms)::: application est constituée d’objets qui mappent aux contrôles natifs de chaque plateforme cible. Cela permet aux applications spécifiques à la plateforme pour iOS, Android et le plateforme Windows universelle d’utiliser le :::no-loc(Xamarin.Forms)::: code contenu dans une [bibliothèque de .NET standard](~/cross-platform/app-fundamentals/net-standard.md).

Les quatre principaux groupes de contrôles utilisés pour créer l’interface utilisateur d’une :::no-loc(Xamarin.Forms)::: application sont les suivants :

- [**Pages**](pages.md)
- [**Mises en forme**](layouts.md)
- [**Affichages**](views.md)
- [**Cellules**](cells.md)

Une :::no-loc(Xamarin.Forms)::: page occupe généralement la totalité de l’écran. La page contient généralement une disposition, qui contient des vues et éventuellement d’autres dispositions. Les cellules sont des composants spécialisés utilisés dans le cadre de la connexion à [`TableView`](xref::::no-loc(Xamarin.Forms):::.TableView) et [`ListView`](xref::::no-loc(Xamarin.Forms):::.ListView) . Un diagramme de classes qui affiche la hiérarchie des types généralement utilisés pour générer une interface utilisateur dans :::no-loc(Xamarin.Forms)::: peut être trouvé dans la [ :::no-loc(Xamarin.Forms)::: hiérarchie de classes de contrôles](~/xamarin-forms/internals/class-hierarchy.md).

Dans les quatre articles sur les [**pages**](pages.md), les [**dispositions**](layouts.md), les [**vues**](views.md)et les [**cellules**](cells.md), chaque type de contrôle est décrit avec des liens vers sa documentation d’API, un article décrivant son utilisation (le cas échéant) et un ou plusieurs exemples de programmes (s’ils existent). Chaque type de contrôle est également accompagné d’une capture d’écran montrant une page de l’exemple [**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) s’exécutant sur des appareils iOS et Android. Sous chaque capture d’écran se trouvent des liens vers le code source de la page C#, la page XAML équivalente et (le cas échéant) le fichier code-behind C# pour la page XAML.

> [!NOTE]
> Les pages, les dispositions et les vues dérivent de la `VisualElement` classe. La `VisualElement` classe fournit diverses propriétés, méthodes et événements qui sont utiles dans la dérivation des classes. Pour plus d’informations, consultez [Propriétés, méthodes et événements VisualElement](common-properties.md).

Outre les contrôles fournis avec, des :::no-loc(Xamarin.Forms)::: contrôles tiers sont disponibles. Pour plus d’informations, consultez [contrôles tiers](thirdparty.md).

## <a name="related-links"></a>Liens associés

- [:::no-loc(Xamarin.Forms):::Exemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [:::no-loc(Xamarin.Forms):::Hiérarchie des classes de contrôles](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentation sur les API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
