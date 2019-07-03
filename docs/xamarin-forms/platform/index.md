---
title: Fonctionnalités de la plateforme de Xamarin.Forms
description: Ce guide explique comment tirer parti des fonctionnalités spécifiques aux plateformes dans les applications Xamarin.Forms à l’aide de diverses techniques.
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: cc19e2a4ea85391f585120f85b222b95ef63543f
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512853"
---
# <a name="xamarinforms-platform-features"></a>Fonctionnalités de la plateforme de Xamarin.Forms

Xamarin.Forms est extensible et vous permet d’incorporer des fonctionnalités spécifiques à la plateforme à l’aide de [effets](~/xamarin-forms/app-fundamentals/effects/index.md), [renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), le [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md), le [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)et bien plus encore.

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

Ce guide décrit les spécificités de plateforme Android fournies par Xamarin.Forms et comment implémenter Material Design en mettant à jour des applications Android de Xamarin.Forms existantes.

## <a name="device-classdevicemd"></a>[Classe de périphérique](device.md)

Ce guide explique comment utiliser le `Device` classe pour créer un comportement spécifique à la plateforme dans le code partagé et l’interface utilisateur (y compris à l’aide de XAML) et comment interagir avec les contrôles d’interface utilisateur à partir de threads d’arrière-plan.

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

Ce guide décrit les iOS-spécificités de plateforme fournis par Xamarin.Forms et comment exécuter iOS supplémentaires par le biais de conception de styles **Info.plist** et `UIAppearance` API.

## <a name="native-formsnative-formsmd"></a>[Formulaires natifs](native-forms.md)

Formulaires natifs autorisent Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-dérivée des pages à être consommés par les projets Xamarin.iOS, Xamarin.Android et Universal Windows Platform (UWP) natifs.

## <a name="native-viewsnative-viewsindexmd"></a>[Vues natives](native-views/index.md)

Vues natives à partir d’iOS, Android et la plateforme Windows universelle peuvent être référencées directement à partir de Xamarin.Forms. Propriétés et gestionnaires d’événements peuvent être définies sur les vues natives, et ils peuvent interagir avec les vues Xamarin.Forms.

## <a name="platform-specificsplatform-specificsindexmd"></a>[Caractéristiques de la plateforme](platform-specifics/index.md)

Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans nécessiter de convertisseurs personnalisés ou des effets. En outre, les éditeurs peuvent créer leurs propres caractéristiques de la plateforme avec des effets.

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Ce guide décrit Windows-spécificités de la plateforme fournis par Xamarin.Forms et comment ajouter un projet UWP à une solution Xamarin.Forms existante.
