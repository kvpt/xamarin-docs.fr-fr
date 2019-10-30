---
title: Quels sont les paramètres de projet obligatoires pour le débogueur ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 856c04d129058e8cbac30dcdf619e8b2b5a66cb6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014263"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Quels sont les paramètres de projet obligatoires pour le débogueur ?

Pour que le débogueur fonctionne comme prévu (points d’arrêt atteints, afficher les journaux de débogage, etc.), l’affichage des informations de débogage et l’instrumentation du développeur doivent être activés.

Pour vérifier les paramètres de votre environnement, procédez comme suit :

## <a name="visual-studio"></a>Visual Studio

1. Ouvrir les options du projet
2. Accédez à **Build > avancé...** Définir les informations de débogage sur **Full**
3. Paramètres pour chaque plateforme :
   - Accédez à **options Android > options de débogage**. Cochez la case **activer l’instrumentation du développeur** .
   - Accédez à **iOS Debug > débogage & instrumentation**. Cochez la case **activer le débogage** .

## <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. Ouvrir les options du projet
2. Accédez à **Build > compilateur > options générales**. Définir les informations de débogage sur **Full**
3. Paramètres pour chaque plateforme :
    - Accédez à **build > Android build > options de débogage**. Cochez la case **activer l’instrumentation du développeur** .
    - Accédez à **Build > iOS Debug**. Cochez la case **activer le débogage** .
