---
title: Débogage des applications Xamarin.iOS
description: Ce document décrit comment utiliser le débogueur dans Visual Studio pour Mac ou Visual Studio 2019 afin de déboguer une application Xamarin.iOS, notamment la définition de points d’arrêt et le débogage sans fil.
ms.prod: xamarin
ms.assetid: 05460010-99E1-DC38-F855-2D691EF54484
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 8d4b60a2e8e53b5b3f97a781b7f1888239541f1a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431866"
---
# <a name="debugging-xamarinios-apps"></a>Débogage des applications Xamarin.iOS

_Vous pouvez déboguer les applications Xamarin.iOS à l’aide du débogueur intégré de Visual Studio pour Mac ou Visual Studio._

Utilisez la prise en charge du débogage natif de Visual Studio pour Mac afin de déboguer le code C# et celui d’autres langages managés. Utilisez [LLDB](https://lldb.llvm.org/tutorial.html) quand vous devez déboguer du code C, C++ ou Objective-C lié à votre projet Xamarin.iOS.

> [!NOTE]
> Lorsque vous compilez des applications en mode débogage, Xamarin.iOS génère des applications plus lentes et beaucoup plus volumineuses, car chaque ligne de code doit être instrumentée. Avant la mise en production, veillez à effectuer une build de mise en production.

Le débogueur Xamarin.iOS est intégré à votre IDE. Il permet aux développeurs de déboguer les applications Xamarin.iOS générées à l’aide de l’un des langages managés pris en charge par Xamarin.iOS dans le simulateur et sur l’appareil.

Le débogueur Xamarin.iOS utilise le [débogueur Mono Soft](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/), ce qui signifie que le code généré et le runtime Mono fonctionnent conjointement avec l’IDE pour fournir une expérience utilisateur de débogage. Il s’agit d’une approche différente de celle des débogueurs « durs » comme LLDB ou MDB, qui contrôlent un programme sans connaissance ou coopération du programme débogué.

## <a name="setting-breakpoints"></a>Définition de points d’arrêt

Quand vous êtes prêt à déboguer votre application, la première étape consiste à [définir des points d’arrêt pour votre application](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint). Pour ce faire, cliquez dans la zone de marge de l’éditeur, à côté du numéro de ligne de code où vous souhaitez marquer un arrêt :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Définition de points d’arrêt](debugging-in-xamarin-ios-images/debugging1.png)](debugging-in-xamarin-ios-images/debugging1.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Définition de points d’arrêt](debugging-in-xamarin-ios-images/debugging1a.png)](debugging-in-xamarin-ios-images/debugging1a.png#lightbox)

-----

Vous pouvez afficher tous les points d’arrêt définis dans votre code en accédant au **panneau Points d’arrêt** :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Panneau Points d’arrêt](debugging-in-xamarin-ios-images/image0a.png)](debugging-in-xamarin-ios-images/image0a.png#lightbox)

 Si le panneau Points d’arrêt ne s’affiche pas automatiquement, vous pouvez le rendre visible en sélectionnant _Affichage > Fenêtres de débogage > Points d’arrêt_

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Panneau Points d’arrêt](debugging-in-xamarin-ios-images/image0.png)](debugging-in-xamarin-ios-images/image0.png#lightbox)

 Si le panneau Points d’arrêt ne s’affiche pas automatiquement, vous pouvez le rendre visible en sélectionnant _Débogage > Fenêtres > Points d’arrêt_

-----

Avant de déboguer une application, vérifiez toujours que la configuration est de type **Débogage**, car elle contient un ensemble d’outils utiles au débogage, par exemple la définition des points d’arrêt, l’utilisation des visualiseurs de données et l’affichage de la pile des appels :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[ ![ Débogage sur](debugging-in-xamarin-ios-images/debugging7.png)](debugging-in-xamarin-ios-images/debugging7.png#lightbox) 
 [ ![ un appareil physique](debugging-in-xamarin-ios-images/debugging7a.png)](debugging-in-xamarin-ios-images/debugging7a.png#lightbox) avec le débogage du simulateur

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[ ![ Débogage sur](debugging-in-xamarin-ios-images/debugging7c.png)](debugging-in-xamarin-ios-images/debugging7c.png#lightbox) 
 [ ![ un appareil physique](debugging-in-xamarin-ios-images/debugging7d.png)](debugging-in-xamarin-ios-images/debugging7d.png#lightbox) avec le débogage du simulateur

-----

## <a name="start-debugging"></a>Démarrer le débogage
Pour démarrer le débogage, sélectionnez l’appareil cible ou un appareil similaire dans votre IDE :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Sélectionner l’appareil cible](debugging-in-xamarin-ios-images/debugging7b.png)](debugging-in-xamarin-ios-images/debugging7b.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Sélectionner l’appareil cible](debugging-in-xamarin-ios-images/debugging7e.png)](debugging-in-xamarin-ios-images/debugging7e.png#lightbox)

-----

Déployez ensuite votre application en appuyant sur le bouton **Lecture**.

Quand vous atteignez un point d’arrêt, le code est mis en surbrillance en jaune :

[![Le code est mis en surbrillance en jaune](debugging-in-xamarin-ios-images/image2.png)](debugging-in-xamarin-ios-images/image2.png#lightbox)

À ce stade, vous pouvez utiliser les outils de débogage tels que l’inspection des valeurs des objets, pour obtenir des informations supplémentaires sur ce qui se passe dans votre code :

[![Affichage d’une valeur de couleur](debugging-in-xamarin-ios-images/image3.png)](debugging-in-xamarin-ios-images/image3.png#lightbox)

## <a name="conditional-breakpoints"></a>Points d'arrêt conditionnels

Vous pouvez également définir des règles qui dictent les circonstances dans lesquelles un point d’arrêt doit se produire : on parle alors d’ajout d’un *point d’arrêt conditionnel*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour définir un point d’arrêt conditionnel, accédez à la **fenêtre Propriétés de point d’arrêt**, ce que vous pouvez faire de deux façons :

- Pour ajouter un nouveau point d’arrêt conditionnel, cliquez avec le bouton droit sur la marge de l’éditeur, à gauche du numéro de la ligne de code pour laquelle vous voulez définir un point d’arrêt, et sélectionnez Nouveau point d’arrêt :

  [![Sélectionner un nouveau point d’arrêt](debugging-in-xamarin-ios-images/image4.png)](debugging-in-xamarin-ios-images/image4.png#lightbox)

- Pour ajouter une condition à un point d’arrêt existant, cliquez avec le bouton droit sur le point d’arrêt, puis sélectionnez **Propriétés de point d’arrêt** ou dans le **Panneau Points d’arrêt**, sélectionnez le bouton de propriétés illustré ci-dessous :

  [![Le panneau points d’arrêt](debugging-in-xamarin-ios-images/image5.png)](debugging-in-xamarin-ios-images/image5.png#lightbox)

Vous pouvez ensuite entrer la condition de déclenchement du point d’arrêt :

[![Entrer la condition pour que le point d’arrêt se produise](debugging-in-xamarin-ios-images/image6.png)](debugging-in-xamarin-ios-images/image6.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour définir un point d’arrêt conditionnel dans Visual Studio, commencez par [définir un point d’arrêt classique](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint). Cliquez avec le bouton droit sur le point d’arrêt pour afficher son menu contextuel :

 [![Menu contextuel du point d’arrêt](debugging-in-xamarin-ios-images/image4vs.png)](debugging-in-xamarin-ios-images/image4vs.png#lightbox)

Sélectionnez **Conditions...** pour afficher le menu _Paramètres de point d’arrêt_ :

 [![Menu Paramètres de point d’arrêt](debugging-in-xamarin-ios-images/image6vs.png)](debugging-in-xamarin-ios-images/image6vs.png#lightbox)

Ici, vous pouvez entrer les conditions de déclenchement du point d’arrêt

Pour plus d’informations sur l’utilisation de conditions de point d’arrêt dans les versions antérieures de Visual Studio, consultez la [documentation de Visual Studio](/visualstudio/debugger/using-breakpoints).

-----

## <a name="navigating-through-code"></a>Naviguer dans le code

Quand un point d’arrêt a été atteint, les outils de débogage vous permettent de contrôler l’exécution du programme. L’IDE affiche quatre boutons qui vous permettent d’exécuter le code pas à pas.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, ils se présentent comme ceci :

 [![Les outils de débogage permettent au développeur de contrôler l’exécution du programme](debugging-in-xamarin-ios-images/image7.png)](debugging-in-xamarin-ios-images/image7.png#lightbox)

Ces règles sont les suivantes :

- **Lecture/Arrêt** - Permet de démarrer/d’arrêter l’exécution du code, jusqu’au point d’arrêt suivant.
- **Pas à pas principal** - Permet d’exécuter la ligne de code suivante. Si la ligne suivante est un appel de fonction, l’opération Pas à pas principal exécute la fonction et s’arrête à la ligne de code suivante située _après_ la fonction.
- **Pas à pas détaillé** - Permet également d’exécuter la ligne de code suivante. Si la ligne suivante est un appel de fonction, Pas à pas détaillé s’arrête à la première ligne de la fonction, ce qui vous permet de continuer le débogage ligne par ligne de la fonction. Si la ligne suivante n’est pas une fonction, il se comporte comme Pas à pas principal.
- **Pas à pas sortant** - Permet de retourner à la ligne où la fonction active a été appelée.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Dans Visual Studio, ils se présentent comme ceci :

[![Les outils de débogage permettent au développeur de contrôler l’exécution du programme](debugging-in-xamarin-ios-images/image7vs.png)](debugging-in-xamarin-ios-images/image7vs.png#lightbox)

Ces règles sont les suivantes :

- **Lecture/Arrêt** - Permet de démarrer/d’arrêter l’exécution du code, jusqu’au point d’arrêt suivant.
- **Pas à pas principal (F11)**  - Permet d’exécuter la ligne de code suivante. Si la ligne suivante est un appel de fonction, l’opération Pas à pas principal exécute la fonction et s’arrête à la ligne de code suivante située _après_ la fonction.
- **Pas à pas détaillé (F10)**  - Permet également d’exécuter la ligne de code suivante. Si la ligne suivante est un appel de fonction, Pas à pas détaillé s’arrête à la première ligne de la fonction, ce qui vous permet de continuer le débogage ligne par ligne de la fonction. Si la ligne suivante n’est pas une fonction, il se comporte comme Pas à pas principal.
- **Pas à pas sortant (Maj + F11)**  - Permet de retourner à la ligne où la fonction active a été appelée.

Pour une documentation plus approfondie sur le débogage, consultez [Naviguer dans le code avec le débogueur Visual Studio](/visualstudio/debugger/navigating-through-code-with-the-debugger).

-----

### <a name="breakpoints"></a>Points d’arrêt

Il est important de souligner qu’iOS n’offre aux applications que quelques secondes (10) pour démarrer et exécuter la méthode `FinishedLaunching` dans le délégué Application. Si l’application n’exécute pas cette méthode en moins de 10 secondes, iOS tue le processus.

Cela signifie qu’il est presque impossible de définir des points d’arrêt dans le code de démarrage de votre programme. Si vous souhaitez déboguer votre code de démarrage, vous devez retarder son initialisation et l’insérer dans une méthode appelée par un minuteur, ou dans une méthode de rappel exécutée après la fin de FinishedLaunching.

## <a name="device-diagnostics"></a>Diagnostics de l’appareil

Si une erreur se produit durant la configuration du débogueur, vous pouvez activer les diagnostics détaillés en ajoutant "-v -v -v" aux arguments mtouch supplémentaires dans les options du projet. Cela permet d’afficher des informations détaillées relatives à l’erreur sur la console de l’appareil.

 <a name="WiFi_Debugging"></a>

## <a name="wireless-debugging"></a>Débogage sans fil

Par défaut, dans Xamarin.iOS, votre application est déboguée sur les appareils via la connexion USB. Parfois, il est nécessaire de disposer d’un périphérique USB pour tester le branchement/débranchement du câble afin de développer des applications fonctionnant avec un ExternalAccessory. Dans ce cas, vous pouvez utiliser le débogage via le réseau sans fil.

Pour plus d’informations sur le déploiement et le débogage sans fil, consultez le guide [Déploiement sans fil](~/ios/deploy-test/wireless-deployment.md).

<a name="Technical_Details"></a>

## <a name="technical-details"></a>Détails techniques

Xamarin.iOS utilise le nouveau débogueur Mono Soft. Contrairement au débogueur Mono standard (programme qui contrôle un processus distinct à l’aide des interfaces du système d’exploitation) le débogueur Soft fonctionne de telle sorte que le runtime Mono expose la fonctionnalité de débogage via un protocole filaire.

Au démarrage, l’application à déboguer contacte le débogueur, qui démarre ensuite son exécution. Dans Xamarin.iOS pour Visual Studio, l’Agent Mac Xamarin sert d’intermédiaire entre l’application (dans Visual Studio) et le débogueur.

Ce débogueur Soft nécessite un schéma de débogage coopératif quand il s’exécute sur l’appareil. Cela signifie que vos builds binaires au moment du débogage sont plus volumineuses, car le code est instrumenté pour contenir du code supplémentaire à chaque point de séquence afin de prendre en charge le débogage.

<a name="Accessing_the_Console"></a>

## <a name="accessing-the-console"></a>Accès à la console

Les journaux d’incidents et la sortie de la classe Console sont envoyés à la console de l’iPhone. Vous pouvez accéder à cette console avec Xcode en utilisant l’organisateur, et en sélectionnant votre appareil dans l’organisateur.

Si vous ne souhaitez pas démarrer Xcode, vous pouvez utiliser le iPhone Configuration Utility (Utilitaire de configuration iPhone) d’Apple pour accéder directement à la console. En prime, vous pouvez accéder aux journaux de la console à partir d’une machine Windows, si vous déboguez un problème sur le terrain.

Pour les utilisateurs de Visual Studio, certains journaux sont disponibles dans la fenêtre Sortie. Toutefois, passez sur un Mac pour obtenir des journaux plus complets et plus détaillés.

-----

<a name="Debugging_Mono's_Class_Libraries"></a>

## <a name="debugging-monos-class-libraries"></a>Débogage des bibliothèques de classes de Mono

Xamarin.iOS est livré avec le code source des bibliothèques de classes de Mono. Vous pouvez vous en servir pour effectuer un pas à pas détaillé à partir du débogueur afin d’examiner la façon dont les choses se déroulent en arrière-plan.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Comme cette fonctionnalité consomme plus de mémoire durant le débogage, elle est désactivée par défaut.

Pour activer cette fonctionnalité, vérifiez que l’option **Déboguer uniquement le code du projet ; ignorer le code du framework** est désélectionnée sous le menu _Visual Studio pour Mac > Préférences > Débogueur_ comme illustré ci-dessous :

[![Débogage des bibliothèques de classes de mono](debugging-in-xamarin-ios-images/debugging6.png)](debugging-in-xamarin-ios-images/debugging6.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour déboguer les bibliothèques de classes dans Visual Studio, vous devez désactiver **Uniquement mon code** sous le menu _Déboguer > Options_. Dans le nœud _Débogage > Général_, décochez la case **Activer Uniquement mon code** :

[![Débogage des bibliothèques de classes de mono](debugging-in-xamarin-ios-images/debugging6vs.png)](debugging-in-xamarin-ios-images/debugging6vs.png#lightbox)

-----

Ensuite, vous pouvez démarrer votre application et effectuer un pas à pas détaillé dans les bibliothèques de classes principales de Mono.

## <a name="related-links"></a>Liens associés

- [Débogage avec Xamarin](/visualstudio/mac/debugging/)
- [Visualisations des données](/visualstudio/mac/data-visualizations/)
- [Définir un point d’arrêt](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)
- [Exécuter le code pas à pas](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)
- [Envoyer les informations de sortie dans la fenêtre de journal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)