---
title: 'Pourquoi mon application iOS 9 échoue avec : System.Exception : Impossible de marshaler l’objet Objective-C ?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 62a63dc5156d1acf9ad6ca15029978131c151726
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290470"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Pourquoi mon application iOS 9 échoue avec : System.Exception : Impossible de marshaler l’objet Objective-C ?

Vous pouvez voir une erreur de ce type :

> System.Exception : Échec du marshaling de l’objet objective-C... Impossible de trouver une instance managée existante pour cet objet...

Dans iOS 9, les modifications d’API requièrent l’utilisation d’un constructeur de rappel lors de l’appel de code non managé, car l’API sous-jacente l’attend à présent. Utilisez la ligne suivante pour ajouter le constructeur de rappel à la classe : 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. . 
