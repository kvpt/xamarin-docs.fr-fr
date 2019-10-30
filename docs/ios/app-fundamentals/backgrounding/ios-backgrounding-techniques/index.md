---
title: Techniques de backgrounding iOS
description: 'Ce document contient des liens vers des guides qui décrivent différentes techniques d’arrière-plan dans iOS : tâches en arrière-plan, service de transfert en arrière-plan, récupération en arrière-plan et notifications à distance.'
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: a81d6a651ee980b444da19341a89206afa8c4375
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010813"
---
# <a name="ios-backgrounding-techniques"></a>Techniques de backgrounding iOS

Dans les sections suivantes, nous allons explorer les fonctionnalités iOS suivantes, ainsi que les options d’arrière-plan existantes :

- [Tâches d’arrière-plan opportuniste](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7) : préserver la durée de vie de la batterie en exécutant des tâches en arrière-plan dans des blocs opportunistes lorsque l’appareil est en éveil pour un autre traitement.
- [Transfert en arrière-plan](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers) : chargez et téléchargez des fichiers de manière fiable, quel que soit l’état du réseau ou la taille du fichier.
- [Récupération en arrière-plan](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch) -actualiser une application à partir de l’arrière-plan à intervalles déterminés par le système.
- [Notifications distantes](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications) : utilisez des notifications push pour déclencher des mises à jour de contenu en arrière-plan avant que l’utilisateur n’ouvre l’application, avec une option pour notifier l’utilisateur ou mettre à jour en mode silencieux.
- **Mises à jour de l’interface utilisateur en arrière-plan** : Préparez l’interface utilisateur de l’application pour l’utilisateur et mettez à jour l’instantané de l’application, le tout à partir de l’arrière-plan.
