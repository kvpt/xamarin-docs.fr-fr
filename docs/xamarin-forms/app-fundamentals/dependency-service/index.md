---
title: Xamarin.FormsDependencyService
description: La Xamarin.Forms classe DependencyService est un localisateur de service qui permet Xamarin.Forms aux applications d’appeler des fonctionnalités de plateforme natives à partir de code partagé.
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 126e2d2373bad923fe1d66fe355ad811c15fbe4f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138370"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.FormsDependencyService

## <a name="introduction"></a>[Introduction](introduction.md)

La [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe est un localisateur de service qui permet Xamarin.Forms aux applications d’appeler des fonctionnalités de plateforme natives à partir de code partagé.

## <a name="registration-and-resolution"></a>[Inscription et résolution](registration-and-resolution.md)

Les implémentations de plateforme doivent être inscrites auprès du [`DependencyService`](xref:Xamarin.Forms.DependencyService) , puis résolues à partir du code partagé pour les appeler.

## <a name="picking-a-photo-from-the-library"></a>[Sélection d’une photo dans la bibliothèque](photo-picker.md)

Cet article explique comment utiliser la Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe pour choisir une photo dans la bibliothèque d’images du téléphone.
