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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "67650455"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introduction"></a>[Introduction](introduction.md)

La [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe est un localisateur de service qui permet aux applications Xamarin.Forms d’invoquer la fonctionnalité de la plate-forme native à partir de code partagé.

## <a name="registration-and-resolution"></a>[Inscription et résolution](registration-and-resolution.md)

Les implémentations [`DependencyService`](xref:Xamarin.Forms.DependencyService)de la plate-forme doivent être enregistrées auprès du , puis résolues à partir du code partagé pour les invoquer.

## <a name="picking-a-photo-from-the-library"></a>[Sélection d’une photo dans la bibliothèque](photo-picker.md)

Cet article explique comment utiliser la [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe Xamarin.Forms pour choisir une photo de la bibliothèque d’images du téléphone.
