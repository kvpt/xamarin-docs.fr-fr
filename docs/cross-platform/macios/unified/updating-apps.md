---
title: La mise à jour des applications existantes vers l’API unifiée
description: Ce document contient des liens vers les guides pas à pas qui décrivent comment mettre à jour des applications Xamarin à l’API unifiée. Il aborde les applications Xamarin.iOS, Xamarin.Mac. Applications de Xamarin.Forms, les types natifs dans des applications multiplateformes et projets de liaison.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 7730d19e4f261a0e414e4946fd0add5312f5d030
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864361"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>La mise à jour des applications existantes vers l’API unifiée

> [!IMPORTANT]
> L’API classique Xamarin, ce qui a précédé l’API unifiée, a été déconseillée.
> - La dernière version de Xamarin.iOS pour prendre en charge de l’API classique (monotouch.dll) a été 9.10 de Xamarin.iOS.
> - Xamarin.Mac prend toujours en charge l’API classique, mais il n’est plus mis à jour. Dans la mesure où il est déconseillé, les développeurs doivent déplacer leurs applications vers l’API unifiée.

## <a name="how-to-update-your-apps"></a>Comment mettre à jour de vos applications

Il existe trois étapes pour mettre à jour vos applications :

1. Résoudre les avertissements du compilateur dans votre code existant, en particulier celles qui concernent les API déconseillées.

2. Utilisez l’outil de Migration intégrés à Visual Studio pour Mac pour mettre à jour vos espaces de noms et les fichiers projet.

3. Corriger les erreurs du compilateur relatives au nouveau restantes [64-types](~/cross-platform/macios/nativetypes.md) et [autres API](~/cross-platform/macios/unified/overview.md#deprecated-typos) qui ont été modifiés. Découvrez [ces conseils](~/cross-platform/macios/unified/updating-tips.md) pour plus d’informations sur les mises à jour manuelles pouvant être nécessaires.

Des guides sont disponibles pour chaque produit pour vous aider à mettre à jour vos applications à l’API unifiée et la prise en charge 64 bits :

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Applications Xamarin.iOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Les applications Xamarin.iOS existantes peuvent être mis à jour vers l’API unifiée à l’aide de l’outil de migration automatisés intégré à Visual Studio pour Mac. Certains correctifs supplémentaires peuvent ensuite être requis, comme expliqué dans [ces instructions](~/cross-platform/macios/unified/updating-ios-apps.md) et [conseils](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Applications Xamarin.Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Les applications Xamarin.Mac existantes peuvent être mis à jour vers l’API unifiée à l’aide de l’outil de migration automatisés intégré à Visual Studio pour Mac. Certains correctifs supplémentaires peuvent ensuite être requis, comme expliqué dans [ces instructions](~/cross-platform/macios/unified/updating-mac-apps.md) et [conseils](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Applications Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Suivez ces instructions pour mettre à jour d’une solution Xamarin.Forms existante avec un projet iOS à utiliser l’API unifiée. Prise en charge des API unifiée est uniquement disponible dans Xamarin.Forms 1.3 et versions ultérieures, donc [les instructions](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) expliquent également comment mettre à jour votre application Xamarin.Forms vers la version 1.3. Ces [conseils](~/cross-platform/macios/unified/updating-tips.md) peut aider à la mise à jour n’importe quel code iOS natif dans les convertisseurs personnalisés ou des services de dépendance.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/nativetypes.md)

Cet article traite de l’utilisation de la nouvelle iOS types unifiée l’API Native (nint, nuint, nfloat) dans une application multiplateforme où le code est partagé avec des appareils non-iOS comme Android ou les systèmes d’exploitation de Windows Phone. Il fournit un aperçu de quand les types natifs doivent être utilisées et offre plusieurs solutions possibles pour les cas où le nouveau type doit être utilisé avec code multiplateforme.

## <a name="update-bindings-to-the-unified-api"></a>Mettre à jour des liaisons à l’API unifiée

Les clients qui ont créé des liaisons pour les bibliothèques Objective-C devez mettre à jour le projet de liaison pour refléter les modifications dans l’API sous-jacente (où certains types sont maintenant être 64 bits).
Suivez ces instructions pour [mettre à jour un projet existant de liaison pour prendre en charge de l’API unifiée](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Liens associés

- [La mise à jour des applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [La mise à jour des applications Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [La mise à jour des applications Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [La mise à jour de liaisons](~/cross-platform/macios/unified/update-binding.md)
- [Conseils de mise à jour](~/cross-platform/macios/unified/updating-tips.md)
- [Différences d’API unifiée de vs classiques](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
