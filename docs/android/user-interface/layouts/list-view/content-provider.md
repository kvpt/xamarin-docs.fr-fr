---
title: Utilisation d’un ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5948d4b5db53db97c4e76cb7568c109b5faf90fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762203"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Utilisation d’un ContentProvider avec Xamarin. Android

CursorAdapters peut également être utilisé pour afficher des données à partir d’un ContentProvider.
Les ContentProvider vous permettent d’accéder aux données exposées par d’autres applications (y compris les données système Android telles que les contacts, les médias et les informations de calendrier).

Le meilleur moyen d’accéder à un ContentProvider consiste à utiliser un CursorLoader à l’aide de LoaderManager. LoaderManager a été introduit dans Android 3,0 (niveau d’API 11, nids) pour déplacer les tâches bloquantes du thread principal, et l’utilisation d’un CursorLoader permet de charger les données dans un thread avant d’être liées à un ListView pour l’affichage.

Pour plus d’informations, consultez [Intro to ContentProvider](~/android/platform/content-providers/index.md) .
