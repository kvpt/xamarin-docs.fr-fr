---
title: 'Pourquoi mon application iOS 9 échoue-t-elle avec : System. exception : impossible de marshaler l’objet objective-C ?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 1bb67eaa884e523e96ef1015daaa6b959ea1512d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031103"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Pourquoi mon application iOS 9 échoue-t-elle avec : System. exception : impossible de marshaler l’objet objective-C ?

Vous pouvez voir une erreur de ce type :

> System. exception : échec du marshaling de l’objet objective-C... Impossible de trouver une instance managée existante pour cet objet...

Dans iOS 9, les modifications d’API requièrent l’utilisation d’un constructeur de rappel lors de l’appel de code non managé, car l’API sous-jacente l’attend à présent. Utilisez la ligne suivante pour ajouter le constructeur de rappel à la classe : 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. . 
