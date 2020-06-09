---
title: Résolution des problèmes de liaison
description: Ce guide décrit la procédure à suivre si vous avez des difficultés à lier une bibliothèque objective-C. En particulier, il aborde les liaisons manquantes, les exceptions d’argument lors du passage de la valeur null à une liaison et le signalement des bogues.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: e43e32b2ad598a7c80e04d8e28d67e85d5a0f9f5
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570945"
---
# <a name="binding-troubleshooting"></a>Résolution des problèmes de liaison

Quelques conseils pour résoudre les problèmes liés aux liaisons aux API macOS (anciennement appelées OS X) dans Xamarin. Mac.

## <a name="missing-bindings"></a>Liaisons manquantes

Bien que Xamarin. Mac couvre une grande partie des API Apple, vous pouvez parfois avoir besoin d’appeler une API Apple qui n’a pas encore de liaison. Dans d’autres cas, vous devez appeler le C/Objective-C tiers en dehors de l’étendue des liaisons Xamarin. Mac.

Si vous utilisez une API Apple, la première étape consiste à laisser Xamarin savoir que vous avez atteint une section de l’API que nous n’avons pas encore couverte. Signaler [un bogue](#reporting-bugs) en notant l’API manquante. Nous utilisons les rapports des clients pour hiérarchiser les API sur lesquelles nous travaillons. En outre, si vous disposez d’une licence professionnelle ou d’entreprise et que l’absence de liaison bloque votre progression, suivez également les instructions fournies à la [prise en charge](https://visualstudio.microsoft.com/vs/support/) de fichier de ticket. Nous ne pouvons pas mettre en œuvre une liaison, mais dans certains cas, nous pouvons vous faire une solution de contournement.

Une fois que vous avez notifié Xamarin (le cas échéant) de votre liaison manquante, l’étape suivante consiste à la lier vous-même. Nous avons [un guide complet](~/cross-platform/macios/binding/overview.md) et une documentation non officielle [ici](https://brendanzagaeski.appspot.com/xamarin/0002.html) pour l’encapsulation manuelle des liaisons objective-C. Si vous appelez une API C, vous pouvez utiliser le mécanisme P/Invoke de C#. la documentation est [ici](https://www.mono-project.com/docs/advanced/pinvoke/).

Si vous décidez de travailler vous-même sur la liaison, sachez que les erreurs dans la liaison peuvent produire toutes sortes de blocages intéressants dans le runtime natif. En particulier, veillez à ce que votre signature en C# corresponde à la signature Native en nombre d’arguments et à la taille de chaque argument. Si vous ne le faites pas, vous risquez d’endommager la mémoire et/ou la pile et vous risquez de provoquer un blocage immédiat ou à un moment arbitraire dans le futur ou d’endommager les données.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Exceptions d’argument lors du passage de null à une liaison

Alors que Xamarin fonctionne pour fournir des liaisons de haute qualité et bien testées pour les API Apple, parfois des erreurs et des bogues. Le problème le plus courant que vous pouvez rencontrer est le déclenchement d’une API `ArgumentNullException` lorsque vous transmettez la valeur NULL lorsque l’API sous-jacente accepte `nil` . Les fichiers d’en-tête natifs qui définissent l’API ne fournissent pas assez d’informations sur les API qui acceptent Nil et qui se bloquent si vous la transmettez.

Si vous rencontrez un cas où le passage de `null` lève une exception `ArgumentNullException` mais que vous pensez qu’il devrait fonctionner, procédez comme suit :

1. Consultez la documentation et/ou les exemples Apple pour voir si vous pouvez trouver la preuve qu’il accepte `nil` . Si vous êtes familiarisé avec Objective-C, vous pouvez écrire un petit programme de test pour le vérifier.
2. Signaler [un bogue](#reporting-bugs).
3. Pouvez-vous contourner le bogue ? Si vous pouvez éviter d’appeler l’API avec `null` , une simple vérification des valeurs NULL autour des appels peut être une solution simple.
4. Toutefois, certaines API requièrent le passage de la valeur null pour désactiver ou désactiver certaines fonctionnalités. Dans ce cas, vous pouvez contourner le problème en ouvrant le navigateur de l’assembly (consultez [recherche du membre C# pour un sélecteur donné](~/mac/app-fundamentals/mac-apis.md#finding_selector)), en copiant la liaison et en supprimant la vérification de la valeur null. Veillez à signaler un bogue (étape 2) si vous effectuez cette opération, car la liaison copiée ne recevra pas les mises à jour et les correctifs que nous effectuons dans Xamarin. Mac, ce qui devrait être considéré comme une solution de contournement à bref délai.

<a name="reporting-bugs"></a>

## <a name="reporting-bugs"></a>Signalement des bogues

Vos commentaires sont très importants pour nous. Si vous rencontrez des problèmes avec Xamarin. Mac :

- Consultez les [forums Xamarin.Mac](https://forums.xamarin.com/categories/xamarin-mac)
- Recherchez dans le [dépôt des problèmes](https://github.com/xamarin/xamarin-macios/issues)
- Avant de devenir des problèmes GitHub, les problèmes Xamarin étaient suivis sur [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Recherchez-y des problèmes correspondants.
- Si vous ne trouvez pas de problème correspondant, ouvrez un nouveau problème dans le [dépôt de problèmes GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Les problèmes GitHub sont tous publics. Il n’est pas possible de masquer des commentaires ou des pièces jointes.

Dans la mesure du possible, essayez d’inclure :

- Un exemple simple reproduisant le problème. Si vous pouvez en fournir un, c’est **inestimable**.
- La trace de pile complète du plantage.
- Le code C# se rapportant au plantage.
