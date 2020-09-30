---
title: Xamarin.Forms Accessibilité
description: La création d’une application accessible garantit que l’application est utilisable par des personnes qui approchent l’interface utilisateur avec un certain nombre de besoins et d’expériences.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: f2fc7000df2c14dfdd863bd431df67643c5501cf
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561285"
---
# <a name="no-locxamarinforms-accessibility"></a>Xamarin.Forms Accessibilité

_La création d’une application accessible garantit que l’application est utilisable par des personnes qui approchent l’interface utilisateur avec un certain nombre de besoins et d’expériences._

Rendre une Xamarin.Forms application accessible signifie réfléchir à la disposition et à la conception de nombreux éléments d’interface utilisateur. Pour des conseils sur les problèmes à prendre en compte, consultez la [Liste de vérification d’accessibilité](~/cross-platform/app-fundamentals/accessibility.md). De nombreux problèmes d’accessibilité, tels que les grandes polices et les paramètres de couleur et de contraste appropriés, peuvent déjà être traités par les Xamarin.Forms API.

Les guides [Accessibilité Android](~/android/app-fundamentals/accessibility.md) et [Accessibilité iOS](~/ios/app-fundamentals/accessibility.md) contiennent les détails des API natives exposées par Xamarin, et le [guide d’accessibilité UWP sur MSDN](/windows/uwp/design/accessibility/basic-accessibility-information) explique l’approche native sur cette plateforme. Ces API est utilisées pour implémenter entièrement des applications accessibles sur chaque plateforme.

Xamarin.Forms n’intègre pas actuellement *la prise en* charge de toutes les API d’accessibilité disponibles sur chacune des plateformes sous-jacentes. Toutefois, il prend en charge la définition de propriétés d’automatisation sur les éléments d’interface utilisateur pour prendre en charge des outils d’aide à la navigation et à la lecture d’écran, ce qui représente une des parties les plus importantes de la création d’applications accessibles. Pour plus d’informations, consultez [Propriétés d’automatisation](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Xamarin.Forms les applications peuvent également avoir l’ordre de tabulation des contrôles spécifiés, afin d’améliorer la convivialité et l’accessibilité. Pour plus d’informations, consultez [Accessibilité](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

D’autres API d’accessibilité (comme [PostNotification sur iOS](~/ios/app-fundamentals/accessibility.md)) peuvent être mieux adaptées à l’implémentation d’un [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) ou d’un [convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Elles ne sont pas abordées dans ce guide.

## <a name="testing-accessibility"></a>Test d’accessibilité

Xamarin.Forms en général, les applications ciblent plusieurs plateformes, ce qui signifie que les fonctionnalités d’accessibilité sont testées en fonction de la plateforme. Suivez ces liens pour apprendre à tester l’accessibilité sur chaque plateforme :

- [**Tests iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Tests Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](/windows/win32/winauto/accscope)

## <a name="related-links"></a>Liens associés

- [Accessibilité multiplateforme](~/cross-platform/app-fundamentals/accessibility.md)
- [Propriétés d’automatisation](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Accessibilité du clavier](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]