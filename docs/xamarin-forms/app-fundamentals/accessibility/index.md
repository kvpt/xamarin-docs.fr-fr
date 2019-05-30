---
title: Accessibilité Xamarin.Forms
description: La création d’une application accessible garantit que l’application est utilisable par des personnes qui approchent l’interface utilisateur avec un certain nombre de besoins et d’expériences.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 2aaa61400d3775c4d622b805d24ff0b338017de5
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354040"
---
# <a name="xamarinforms-accessibility"></a>Accessibilité Xamarin.Forms

_La création d’une application accessible garantit que l’application est utilisable par des personnes qui approchent l’interface utilisateur avec un certain nombre de besoins et d’expériences._

Rendre une application Xamarin.Forms accessible signifie qu’il faut réfléchir à la disposition et à la conception de nombreux éléments d’interface utilisateur. Pour des conseils sur les problèmes à prendre en compte, consultez la [Liste de vérification d’accessibilité](~/cross-platform/app-fundamentals/accessibility.md). Un grand nombre de questions d’accessibilité comme les grandes polices et les paramètres de couleur et de contraste appropriés peuvent déjà être résolues par les API Xamarin.Forms.

Les guides [Accessibilité Android](~/android/app-fundamentals/accessibility.md) et [Accessibilité iOS](~/ios/app-fundamentals/accessibility.md) contiennent les détails des API natives exposées par Xamarin, et le [guide d’accessibilité UWP sur MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explique l’approche native sur cette plateforme. Ces API est utilisées pour implémenter entièrement des applications accessibles sur chaque plateforme.

Xamarin.Forms ne prend pas encore en charge *nativement* toutes les API d’accessibilité disponibles sur chacune des plateformes sous-jacentes. Toutefois, il prend en charge la définition de propriétés d’automatisation sur les éléments d’interface utilisateur pour prendre en charge des outils d’aide à la navigation et à la lecture d’écran, ce qui représente une des parties les plus importantes de la création d’applications accessibles. Pour plus d’informations, consultez [Propriétés d’automatisation](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Il se peut que les applications Xamarin.Forms aient également un ordre de tabulation de contrôle spécifié afin d’améliorer la facilité d’utilisation et l’accessibilité. Pour plus d’informations, consultez [Accessibilité](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

D’autres API d’accessibilité (comme [PostNotification sur iOS](~/ios/app-fundamentals/accessibility.md)) peuvent être mieux adaptées à l’implémentation d’un [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) ou d’un [convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Elles ne sont pas abordées dans ce guide.

## <a name="testing-accessibility"></a>Test d’accessibilité

Les applications Xamarin.Forms ciblent généralement plusieurs plateformes, ce qui implique de tester les fonctionnalités d’accessibilité selon la plateforme. Suivez ces liens pour apprendre à tester l’accessibilité sur chaque plateforme :

- [**Tests iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Tests Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)** ](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Liens associés

- [Accessibilité multiplateforme](~/cross-platform/app-fundamentals/accessibility.md)
- [Propriétés d’automatisation](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Accessibilité du clavier](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
