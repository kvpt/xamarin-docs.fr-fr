---
title: Quels sont les paramètres de projet obligatoires pour le débogueur ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 343f8d37d77726d2cdc06a74c44e476af00dde27
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765155"
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
