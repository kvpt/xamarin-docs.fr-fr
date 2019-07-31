---
title: SiriKit dans Xamarin. iOS
description: Cet article explique comment utiliser SiriKit dans une application Xamarin. iOS pour fournir des services accessibles à l’utilisateur à l’aide de Siri sur un appareil iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0438ea08bbdcbf0ce3c64e15192cbb90cd835b00
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654012"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit dans Xamarin. iOS

_Cet article explique comment utiliser SiriKit dans une application Xamarin. iOS pour fournir des services accessibles à l’utilisateur à l’aide de Siri sur un appareil iOS._

Nouveauté d’iOS 10, SiriKit permet à une application iOS de fournir des services accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS à l’aide d’extensions d’application, ainsi que **des nouvelles infrastructures** d' **interface utilisateur** intentions et intentions.

Siri fonctionne avec le concept de **domaines**, de groupes d’actions connues pour les tâches associées. Chaque interaction d’une application avec Siri doit appartenir à l’un de ses domaines de service connus, comme suit:

- Appel audio ou vidéo.
- Réservation d’une définition.
- Gestion des entraînements.
- Messag.
- Recherche de photos.
- Envoi ou réception de paiements.

Lorsque l’utilisateur effectue une requête de Siri impliquant l’un des services d’une extension d’application, SiriKit envoie à l’extension un objet d' **intention** qui décrit la demande de l’utilisateur ainsi que toutes les données de prise en charge. L’extension d’application génère ensuite l’objet de **réponse** approprié pour l' **intention**donnée, en détaillant la manière dont l’extension peut traiter la demande.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Présentation des concepts de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Cet article aborde les concepts clés qui seront nécessaires à l’utilisation de SiriKit dans une application Xamarin. iOS. Il couvre les nouvelles intentions et les points d’extension d’interface utilisateur et la façon dont ils fonctionnent avec l’application et le vocabulaire utilisateur pour ouvrir une application sur Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implémentation de SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Cet article décrit les étapes requises pour implémenter la prise en charge de SiriKit dans une application Xamarin. iOS. Le développeur doit lire le Guide de présentation des concepts SiriKit ci-dessus avant de tenter d’ajouter la prise en charge de SiriKit à une application, car les concepts clés qui seront nécessaires pour une implémentation réussie.





## <a name="related-links"></a>Liens associés

- [Exemple ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guide de programmation SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informations de référence sur l’infrastructure Intent](https://developer.apple.com/reference/intents)
- [Informations de référence sur l’infrastructure d’interface utilisateur](https://developer.apple.com/reference/intentsui)
