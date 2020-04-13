---
title: Environnement d’exécution pour les applications Xamarin.iOS
description: Ce document décrit comment configurer les variables d’environnement temporaires et permanentes d’une application Xamarin.iOS. Les variables peuvent être spécifiées dans les propriétés d’un projet ou en tant qu’arguments supplémentaires avec l’outil d’empaquetage mtouch.
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: b2452c50fa978f3cabb718afe7330bf0fb701ce3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73030242"
---
# <a name="execution-environment-for-xamarinios-apps"></a>Environnement d’exécution pour les applications Xamarin.iOS

*L’environnement d’exécution* correspond à l’ensemble de variables d’environnement qui influencent l’exécution du programme. Les variables d’environnement peuvent être définies temporairement dans les propriétés du projet ou définitivement en spécifiant des arguments supplémentaires à l’outil d’empaquetage mtouch.

## <a name="temporary-environment-variables"></a>Variables d’environnement temporaires

Les variables temporaires de l’environnement sont définies dans la fenêtre **Options propriétés**/**Options** du projet dans la section Run **> General.** Ces variables d’environnement sont uniquement en vigueur lorsque l’application est démarrée à l’aide de Visual Studio pour Mac, si l’application est démarrée manuellement en tapant dessus, ces variables d’environnement ne sont pas définies.

## <a name="permanent-environment-variables"></a>Variables d'environnement permanentes

Les variables d’environnement permanentes sont définies en spécifiant des arguments supplémentaires à l’outil d’empaquetage mtouch. Ces variables d’environnement sont compilées dans le fichier exécutable et seront définies même si l’application n’est pas lancée à partir de Visual Studio pour Mac.

## <a name="example"></a>Exemple

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```
