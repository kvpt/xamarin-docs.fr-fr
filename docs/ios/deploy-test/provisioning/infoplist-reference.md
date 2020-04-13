---
title: Informations de référence sur le fichier Info.plist pour Xamarin.iOS
description: Ce document décrit les différentes paires clé/valeur qui peuvent être définies dans le fichier Info.plist d’une application Xamarin.iOS. Ces clés sont nécessaires si votre application effectue des tâches spécifiques telles que l’accès à l’emplacement, aux photos, au microphone ou à l’appareil photo.
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: d94a647583539ac6af603a9074e1966c1c41d587
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73028459"
---
# <a name="infoplist-reference-for-xamarinios"></a>Informations de référence sur le fichier Info.plist pour Xamarin.iOS

Pour plus d’informations sur l’utilisation des clés Info.plist, consultez le guide [Sécurité et confidentialité](~/ios/app-fundamentals/security-privacy.md). 

## <a name="location"></a>Emplacement 

L’accès à l’emplacement de l’utilisateur nécessite également des modifications dans le fichier Info.plist. Vous devez définir les clés suivantes relatives aux données d’emplacement : 

- **NSLocationWhenInUseUsageDescription** : cette clé est utilisée quand vous accédez à l’emplacement d’un utilisateur en train d’interagir avec votre application. 
- **NSLocationAlwaysUsageDescription** : cette clé est utilisée quand votre application accède à l’emplacement d’un utilisateur en arrière-plan.

## <a name="photos"></a>Photos 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>Contacts 

NSContactsUsageDescription 

## <a name="calendar-data"></a>Données de calendrier 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>Données de rappel 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>Périphériques Bluetooth 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>Microphone 

NSMicrophoneUsageDescription 

## <a name="camera"></a>Caméra 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>Lecture HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>Modes d’arrière-plan 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 

## <a name="related-links"></a>Liens connexes

- [Guide de référence Apple](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10)
