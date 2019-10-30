---
title: Xamarin pour les développeurs Objective-C
description: Ce document fournit une description de Xamarin.iOS pour les développeurs Objective-C. Il fournit des liens vers des guides qui expliquent comment effectuer la transition d’Objective-C à C#, comment lier une bibliothèque Objective-C pour une utilisation dans C# et comment créer une application mobile multiplateforme.
ms.prod: xamarin
ms.assetid: 9F3C86A3-403E-4025-99CA-99FCA86DC828
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 2c5451b379968230d75599601052e3a33f5b5951
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022440"
---
# <a name="xamarin-for-objective-c-developers"></a>Xamarin pour les développeurs Objective-C

Xamarin offre aux développeurs s’intéressant à iOS un moyen de transformer leur code sans interface utilisateur en code C# indépendant de l’application, afin de pouvoir l’utiliser chaque fois que C# est disponible, notamment sur Android via Xamarin.Android et dans les différentes versions de Windows. Cependant, il ne suffit pas d’utiliser C# avec Xamarin pour pouvoir tirer parti des compétences et du code Objective-C existants. En réalité, le fait de connaître Objective-C fait de vous un meilleur développeur Xamarin.iOS, car Xamarin expose toutes les API de plateformes iOS et OS X natives qui vous sont familières, entre autres UIkit, Core Animation, Core Foundation et Core Graphics. Vous profitez en même temps de la puissance du langage C#, avec des fonctionnalités comme LINQ et les génériques, ainsi que de bibliothèques de classes de base .NET enrichies utilisables dans les applications natives.

Par ailleurs, Xamarin permet de tirer parti des ressources Objective-C existantes au moyen d’une technologie appelée liaison. Il vous suffit de créer une bibliothèque statique dans Objective-C et de l’exposer à C# grâce à une liaison, comme dans le diagramme suivant :

 [![](images/01-bindings.png "A static library in Objective-C exposed to C# via a binding")](images/01-bindings.png#lightbox)

Cela ne se limite pas forcément au code sans interface utilisateur. Des liaisons peuvent également exposer du code d’interface utilisateur développé en Objective-C.

## <a name="transitioning-from-objective-c"></a>Transition à partir d’Objective-C

Pour faciliter la transition à Xamarin, vous trouverez une multitude d’informations sur notre site de documentation, qui vous montrera comment intégrer du code C# avec ce que vous connaissez déjà. Voici quelques liens importants pour bien démarrer :

- [Premier manuel C# pour les développeurs Objective-C](primer.md) : un court manuel de démarrage pour les développeurs Objective-C qui souhaitent passer à Xamarin et au langage C#. 
- [Procédure pas à pas : lier une bibliothèque Objective-C](~/ios/platform/binding-objective-c/walkthrough.md) : les étapes à suivre pour réutiliser du code Objective-C existant dans une application Xamarin.iOS. 

## <a name="binding-objective-c"></a>Liaison Objective-C

Lorsque vous aurez compris les différences entre C# et Objective-C et que vous aurez suivi la procédure de liaison ci-dessus, vous serez en bonne voie pour la transition vers la plateforme de Xamarin. Pour la suite, des informations plus détaillées sur les technologies de liaison Xamarin.iOS, avec une référence complète sur la liaison, sont accessibles dans la section [Liaison Objective-C](~/ios/platform/binding-objective-c/index.md).

## <a name="cross-platform-development"></a>Développement interplateforme

Enfin, après la transition vers Xamarin.iOS, vous pourrez consulter l’aide multiplateforme que nous proposons, qui comporte des études de cas des applications de référence que nous avons développées, ainsi que les meilleures pratiques pour la création de code multiplateforme et réutilisable, dans la section [ Développement d’applications multiplateformes](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).
