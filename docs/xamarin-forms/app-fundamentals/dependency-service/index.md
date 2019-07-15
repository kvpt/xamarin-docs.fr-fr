---
title: Xamarin.Forms DependencyService
description: La classe Xamarin.Forms DependencyService est un localisateur de service qui permet aux applications Xamarin.Forms d’appeler des fonctionnalités natives d’une plateforme à partir du code partagé.
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: ea259d1ee9dc4a94322c38b3e96bee654197bb87
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650455"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

La classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) est un localisateur de service qui permet aux applications Xamarin.Forms d’appeler des fonctionnalités natives d’une plateforme à partir du code partagé.

## <a name="registration-and-resolutionregistration-and-resolutionmd"></a>[Inscription et résolution](registration-and-resolution.md)

Les implémentations de plateforme doivent être inscrites auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService) puis résolues pour que le code partagé puisse les appeler.

## <a name="picking-a-photo-from-the-libraryphoto-pickermd"></a>[Sélection d’une photo dans la bibliothèque](photo-picker.md)

Cet article explique comment utiliser la classe Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour sélectionner une photo dans la bibliothèque d’images du téléphone.
