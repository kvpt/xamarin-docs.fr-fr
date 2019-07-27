---
title: Utilisation d’un ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 142541dcc35b55e43b54eeb729c486ac9fc88b54
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510074"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Utilisation d’un ContentProvider avec Xamarin. Android

CursorAdapters peut également être utilisé pour afficher des données à partir d’un ContentProvider.
Les ContentProvider vous permettent d’accéder aux données exposées par d’autres applications (y compris les données système Android telles que les contacts, les médias et les informations de calendrier).

Le meilleur moyen d’accéder à un ContentProvider consiste à utiliser un CursorLoader à l’aide de LoaderManager. LoaderManager a été introduit dans Android 3,0 (niveau d’API 11, nids) pour déplacer les tâches bloquantes du thread principal, et l’utilisation d’un CursorLoader permet de charger les données dans un thread avant d’être liées à un ListView pour l’affichage.

Pour plus d’informations, consultez [Intro to ContentProvider](~/android/platform/content-providers/index.md) .

