---
title: Mise à jour des applications existantes vers le API unifiée
description: Ce document contient des liens vers les différents guides qui décrivent comment mettre à jour des applications Xamarin vers l’API unifiée. Il aborde les applications Xamarin. iOS, les applications Xamarin. Mac. Applications Xamarin. Forms, types natifs dans les applications multiplateforme et projets de liaison.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 61562eed8867b7a2e12b3d2e96feed28d957696b
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511201"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Mise à jour des applications existantes vers le API unifiée

> [!IMPORTANT]
> Le API classique Xamarin, qui précédait le API unifiée, est déconseillé.
> - La dernière version de Xamarin. iOS pour la prise en charge de l’API classique (unitouch. dll) était Xamarin. iOS 9,10.
> - Xamarin. Mac prend toujours en charge le API classique, mais il n’est plus mis à jour. Étant donné qu’il est déconseillé, les développeurs doivent déplacer leurs applications vers le API unifiée.

## <a name="how-to-update-your-apps"></a>Comment mettre à jour vos applications

Il existe trois étapes pour mettre à jour vos applications:

1. Corrigez tous les avertissements du compilateur dans votre code existant, en particulier ceux qui se rapportent à des API dépréciées.

2. Utilisez l’outil de migration intégré à Visual Studio pour Mac pour mettre à jour vos fichiers projet et espaces de noms.

3. Corrigez les erreurs de compilateur restantes relatives aux nouveaux [types 64](~/cross-platform/macios/nativetypes.md) et aux [autres API](~/cross-platform/macios/unified/overview.md#deprecated-typos) qui ont changé. Consultez [ces conseils](~/cross-platform/macios/unified/updating-tips.md) pour obtenir des informations supplémentaires sur les mises à jour manuelles qui peuvent être nécessaires.

Des guides spécifiques sont disponibles pour chaque produit pour vous aider à mettre à jour vos applications vers le API unifiée et la prise en charge de 64 bits:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Applications Xamarin. iOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Les applications Xamarin. iOS existantes peuvent être mises à jour vers le API unifiée à l’aide de l’outil de migration automatisé intégré à Visual Studio pour Mac. Certains correctifs supplémentaires peuvent être nécessaires, comme expliqué dans [ces instructions](~/cross-platform/macios/unified/updating-ios-apps.md) et [conseils](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Applications Xamarin. Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Les applications Xamarin. Mac existantes peuvent être mises à jour vers le API unifiée à l’aide de l’outil de migration automatisé intégré à Visual Studio pour Mac. Certains correctifs supplémentaires peuvent être nécessaires, comme expliqué dans [ces instructions](~/cross-platform/macios/unified/updating-mac-apps.md) et [conseils](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Applications Xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Suivez ces instructions pour mettre à jour une solution Xamarin. Forms existante avec un projet iOS afin d’utiliser le API unifiée. La prise en charge de API unifiée est disponible uniquement dans Xamarin. Forms 1,3 et versions ultérieures. par conséquent, [les instructions](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) expliquent également comment mettre à jour votre application Xamarin. Forms vers la version 1,3. Ces [conseils](~/cross-platform/macios/unified/updating-tips.md) peuvent vous aider à mettre à jour des codes iOS natifs dans des convertisseurs personnalisés ou des services de dépendance.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/nativetypes.md)

Cet article aborde l’utilisation des nouveaux types natifs iOS API unifiée (nint, nuint, nfloat) dans une application multiplateforme dans laquelle le code est partagé avec des appareils non-iOS tels que les systèmes d’exploitation Android ou Windows Phone. Il fournit des informations sur le moment où les types natifs doivent être utilisés et fournit plusieurs solutions possibles pour les cas où le nouveau type doit être utilisé avec du code multiplateforme.

## <a name="update-bindings-to-the-unified-api"></a>Mettre à jour les liaisons avec les API unifiée

Les clients qui ont créé des liaisons aux bibliothèques objective-C doivent mettre à jour le projet de liaison pour refléter les modifications apportées à l’API sous-jacente (où certains types sont maintenant 64 bits).
Suivez ces instructions pour [mettre à jour un projet de liaison existant afin de prendre en charge le API unifiée](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Liens associés

- [Mise à jour des applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Mise à jour des applications Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Mise à jour des applications Xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Mise à jour des liaisons](~/cross-platform/macios/unified/update-binding.md)
- [Mise à jour des conseils](~/cross-platform/macios/unified/updating-tips.md)
- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
