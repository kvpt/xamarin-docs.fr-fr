---
title: Données et Services de Cloud dans les applications Xamarin.iOS
description: Ce document contient des liens vers des guides qui expliquent comment utiliser des données locales, iCloud et CloudKit dans une application Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 930ff3df8266053ab5de09c01d82d8d583ffac81
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865554"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Données et Services de Cloud dans les applications Xamarin.iOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Accès aux données](~/ios/data-cloud/data/index.md)

La plupart des applications a une exigence pour enregistrer les données sur l’appareil localement. À moins que la quantité de données est très petite, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès de base de données. iOS a le moteur de base de données Sqlite « intégré » et l’accès aux données est simplifié par la plateforme de Xamarin qui est fourni avec le fournisseur de données SQLite.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L’API de stockage iCloud dans iOS 5 permet aux applications d’enregistrer des documents d’utilisateur et les données spécifiques à l’application dans un emplacement central et accèdent à ces éléments à partir de tous les appareils de l’utilisateur.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Le framework de CloudKit simplifie le développement d’applications qu’iCloud de l’accès. Cela inclut la récupération des données d’application et les droits de ressource ainsi que la possibilité de stocker en toute sécurité les informations de l’application. Ce kit permet aux utilisateurs d’une couche d’anonymat en autorisant l’accès aux applications avec leur ID iCloud sans partager les informations personnelles.
