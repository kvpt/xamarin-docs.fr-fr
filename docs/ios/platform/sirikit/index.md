---
title: SiriKit dans Xamarin. iOS
description: Cet article explique comment utiliser SiriKit dans une application Xamarin. iOS pour fournir des services accessibles à l’utilisateur à l’aide de Siri sur un appareil iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c50d02b4fc806a9ce466da450cfeb6afaa68df93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433699"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit dans Xamarin. iOS

_Cet article explique comment utiliser SiriKit dans une application Xamarin. iOS pour fournir des services accessibles à l’utilisateur à l’aide de Siri sur un appareil iOS._

Nouveauté d’iOS 10, SiriKit permet à une application iOS de fournir des services accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS à l’aide d’extensions d’application, ainsi que **des nouvelles infrastructures** d' **interface utilisateur** intentions et intentions.

Siri fonctionne avec le concept de **domaines**, de groupes d’actions connues pour les tâches associées. Chaque interaction d’une application avec Siri doit appartenir à l’un de ses domaines de service connus, comme suit :

- Appel audio ou vidéo.
- Réservation d’une définition.
- Gestion des entraînements.
- Messag.
- Recherche de photos.
- Envoi ou réception de paiements.

Lorsque l’utilisateur effectue une requête de Siri impliquant l’un des services d’une extension d’application, SiriKit envoie à l’extension un objet d' **intention** qui décrit la demande de l’utilisateur ainsi que toutes les données de prise en charge. L’extension d’application génère ensuite l’objet de **réponse** approprié pour l' **intention**donnée, en détaillant la manière dont l’extension peut traiter la demande.

## <a name="understanding-sirikit-concepts"></a>[Présentation des concepts de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Cet article aborde les concepts clés qui seront nécessaires à l’utilisation de SiriKit dans une application Xamarin. iOS. Il couvre les nouvelles intentions et les points d’extension d’interface utilisateur et la façon dont ils fonctionnent avec l’application et le vocabulaire utilisateur pour ouvrir une application sur Siri.

## <a name="implementing-sirikit"></a>[Implémentation de SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Cet article décrit les étapes requises pour implémenter la prise en charge de SiriKit dans une application Xamarin. iOS. Le développeur doit lire le Guide de présentation des concepts SiriKit ci-dessus avant de tenter d’ajouter la prise en charge de SiriKit à une application, car les concepts clés qui seront nécessaires pour une implémentation réussie.

## <a name="related-links"></a>Liens associés

- [Exemple ElizaChat](/samples/xamarin/ios-samples/ios10-elizachat)
- [Guide de programmation SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informations de référence sur l’infrastructure Intent](https://developer.apple.com/reference/intents)
- [Informations de référence sur l’infrastructure d’interface utilisateur](https://developer.apple.com/reference/intentsui)