---
title: Notions de base des applications Xamarin.Forms
description: Exploration des principes fondamentaux du développement d’applications Xamarin.Forms, y compris tous les concepts principaux nécessaires, jusqu’aux touches finales comme l’accessibilité et la localisation.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 61d9c5b1c7b81bfc8a20932ce1d188cd0cb0f980
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650435"
---
# <a name="xamarinforms-application-fundamentals"></a>Notions de base des applications Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Accessibilité](accessibility/index.md)

Conseils pour incorporer des fonctionnalités accessibles (par exemple, la prise en charge des outils de lecture d’écran) avec Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Classe d’application](application-class.md)

La classe `Application` est le point de départ de Xamarin.Forms : chaque application doit implémenter une sous-classe `App` pour définir la page initiale. Elle fournit aussi la collection `Properties` pour le stockage simple des données. Elle peut être définie en C# ou XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Cycle de vie d’application](app-lifecycle.md)

Les méthodes `OnStart`, `OnSleep`, et `OnResume` de la classe `Application`, ainsi que les événements de navigation modale, vous permettent de gérer les événements de cycle de vie des applications avec du code personnalisé.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Indexation de l’application et lien profond](deep-linking.md)

L’indexation d’applications permet aux applications, qui sinon auraient été oubliées après quelques utilisations, de rester pertinentes en apparaissant dans les résultats de recherche. Les liens ciblés permettent aux applications de répondre à un résultat de recherche qui contient des données d’application, généralement en parcourant une page référencée à partir d’un lien ciblé.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportements](behaviors/index.md)

Les contrôles d’interface utilisateur peuvent facilement être étendus sans utilisation de sous-classes à l’aide de comportements qui ajoutent des fonctionnalités.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Renderers personnalisés](custom-renderer/index.md)

Les convertisseurs personnalisés permettent aux développeurs de « remplacer » le rendu par défaut des contrôles Xamarin.Forms pour personnaliser leur apparence et leur comportement sur chaque plateforme (avec des SDK natifs s’ils le souhaitent).

## <a name="data-bindingdata-bindingindexmd"></a>[Liaison de données](data-binding/index.md)

La liaison de données lie les propriétés de deux objets pour que les changements d’une propriété soient automatiquement répercutés sur l’autre propriété. La liaison de données fait partie intégrante de l’architecture d’application modèle-vue-vue modèle ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)).

## <a name="dependency-servicedependency-serviceindexmd"></a>[Service de dépendance](dependency-service/index.md)

Le `DependencyService` fournit un localisateur simple pour vous permettre de coder dans des interfaces dans votre code partagé, et fournit des implémentations propres à la plateforme qui sont résolues automatiquement pour simplifier la référence des fonctionnalités propres à la plateforme dans Xamarin.Forms.

## <a name="effectseffectsindexmd"></a>[Effets](effects/index.md)

Les effets permettent de personnaliser les contrôles natifs de chaque plateforme et sont généralement utilisés pour les petits changements de style.

## <a name="gesturesgesturesindexmd"></a>[Gestes](gestures/index.md)

La classe Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) prend en charge les appuis, les pincements et les mouvements panoramiques sur les contrôles d’interface utilisateur.

## <a name="localizationlocalizationindexmd"></a>[Localisation](localization/index.md)

Le framework de localisation .NET intégré peut être utilisé pour créer des applications multilingues multiplateformes avec Xamarin.Forms.

## <a name="messaging-centermessaging-centermd"></a>[Centre de messagerie](messaging-center.md)

Le `MessagingCenter` de Xamarin.Forms permet aux modèles de vue et aux autres composants de communiquer sans avoir aucun lien entre eux autre qu’un simple contrat de message.

## <a name="navigationnavigationindexmd"></a>[Navigation](navigation/index.md)

Xamarin.Forms propose plusieurs expériences différentes de navigation dans les pages, selon le type de `Page` utilisé.

## <a name="shellshellindexmd"></a>[Shell](shell/index.md)

Xamarin.Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications mobiles. Cela inclut une expérience utilisateur de navigation commune, un schéma de navigation basée sur des URI et un gestionnaire de recherche intégré.

## <a name="templatestemplatesindexmd"></a>[Modèles](templates/index.md)

Les modèles de contrôle permettent de facilement créer et recréer des thèmes pour les pages d’application au moment de l’exécution, tandis que les modèles de données offrent la possibilité de définir la présentation des données sur les contrôles pris en charge.

## <a name="triggerstriggersmd"></a>[Déclencheurs](triggers.md)

Mettent à jour les contrôles en répondant aux changements de propriété et aux événements en XAML.
