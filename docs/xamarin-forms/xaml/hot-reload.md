---
title: Rechargement à chaud XAML pour Xamarin. Forms
description: Rechargez les modifications apportées à votre fichier XAML instantanément sur votre application en cours d’exécution. vous n’avez donc pas besoin de générer votre projet Xamarin. Forms après chaque modification XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: 0f3ff5357d3fb4c60a910dda6befa8c699c6fc07
ms.sourcegitcommit: 5f7749d6ba1db79aa0fbf4ee7c90f2dcd4de812b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74884122"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>Rechargement à chaud XAML pour Xamarin. Forms (version préliminaire)

Le rechargement à chaud XAML se connecte à votre flux de travail existant pour augmenter votre productivité et vous faire gagner du temps. Sans le rechargement à chaud XAML, vous devez générer et déployer votre application chaque fois que vous souhaitez voir une modification XAML. Avec le rechargement à chaud, lorsque vous enregistrez votre fichier XAML, les modifications sont reflétées dans votre application en cours d’exécution. En outre, votre état de navigation et vos données sont conservés, ce qui vous permet d’effectuer rapidement une itération sur votre interface utilisateur sans perdre votre place dans l’application. Par conséquent, avec le rechargement à chaud XAML, vous allez consacrer moins de temps à la reconstruction et au déploiement de vos applications pour valider les modifications de l’interface utilisateur.

> [!NOTE]
> Si vous écrivez une application WPF ou UWP, consultez [rechargement à chaud XAML pour UWP et WPF](/visualstudio/debugger/xaml-hot-reload).

## <a name="system-requirements"></a>Configuration requise

| IDE/infrastructure | Version requise |
|------|------------------|
|Visual Studio 2019 | 16,4 ou version ultérieure
Visual Studio 2019 pour Mac | 8,4 ou version ultérieure
Xamarin.Forms | 4,1 ou version ultérieure

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>Utiliser le rechargement à chaud XAML pour Xamarin. Forms

Aucune installation ou configuration supplémentaire n’est requise pour utiliser le rechargement à chaud XAML. Il est intégré à Visual Studio et peut être activé dans les paramètres de l’IDE. Une fois activé, vous pouvez commencer à utiliser le rechargement à chaud XAML en déboguant votre application sur un émulateur, un simulateur ou un appareil physique. Actuellement, le rechargement à chaud XAML fonctionne uniquement lors du débogage sur iOS ou Android.

Sur Windows, le rechargement à chaud XAML peut être activé en activant la case à cocher **activer le rechargement à chaud Xamarin** dans **outils** > **options** > **Xamarin** > **rechargement chaud**.

Sur un Mac, le rechargement à chaud XAML peut être activé en activant la case à cocher **activer le rechargement à chaud Xamarin** dans **Visual Studio** > **Préférences** > **projets** > le **rechargement à chaud Xamarin**.

## <a name="resilient-reloading"></a>Rechargement résilient

Si vous apportez une modification que le rechargement à chaud XAML ne peut pas recharger, un message d’erreur s’affiche à l’aide d’IntelliSense. Ces modifications, appelées modifications incorrectes, incluent le typage incorrect de votre code XAML ou la connexion d’un contrôle à un gestionnaire d’événements qui n’existe pas. Même avec une modification impropre, vous pouvez continuer à recharger sans redémarrer l’application : effectuez une autre modification ailleurs dans le fichier XAML et appuyez sur Enregistrer. La modification impropre n’est pas rechargée, mais vos autres modifications continuent à être appliquées.

## <a name="known-limitations"></a>Limitations connues

- Vous ne pouvez pas ajouter, supprimer ou renommer des fichiers ou des packages NuGet au cours d’une session de rechargement à chaud XAML. Si vous ajoutez ou supprimez un fichier ou un package NuGet, régénérez et redéployez votre application pour continuer à utiliser le rechargement à chaud XAML.
- Définissez l’éditeur de liens sur **ne pas lier** pour la meilleure expérience. Le paramètre du **Kit de développement logiciel (SDK) Link ne fonctionne que** la plupart du temps, mais il peut échouer dans certains cas.
- Le débogage sur un iPhone physique requiert que l’interpréteur utilise le rechargement à chaud XAML. Pour ce faire, ouvrez les paramètres du projet, sélectionnez l’onglet Build iOS et assurez-vous que **l’option Activer le paramètre interpréteur mono** est activée. Vous devrez peut-être modifier l’option de **plateforme** en haut de la page de propriétés en **iPhone**.
- Toutes les références créées en affectant un contrôle à un autre champ ou propriété à l’aide de sa valeur `x:Name` ne sont pas rechargées.
- La mise à jour de la hiérarchie visuelle de votre application de Shell dans **AppShell. Xaml** peut entraîner des problèmes de maintenance de l’état de votre application. Régénérez l’application pour continuer le rechargement.
- Le rechargement à chaud XAML C# ne peut pas recharger le code, y compris les gestionnaires d’événements, les contrôles personnalisés, la page code-behind et les autres classes.

## <a name="migrate-from-the-private-preview"></a>Migrer à partir de la version préliminaire privée

Si vous étiez dans le cadre de la préversion privée, votre extension de rechargement à chaud XAML sera automatiquement mise à jour lors de la mise à jour de Visual Studio. Si vous choisissez de ne pas mettre à jour Visual Studio, vous pouvez continuer à utiliser la version actuelle du rechargement à chaud XAML, mais vous ne recevrez pas d’autres mises à jour via le flux d’extension privé en préversion.

## <a name="troubleshooting"></a>Résolution des problèmes

- Si le rechargement à chaud XAML ne parvient pas à s’initialiser :
  - Mettez à jour votre version de Xamarin. Forms.
  - Vérifiez que vous disposez de la dernière version de l’IDE.
  - Définissez vos paramètres de l’éditeur de liens Android ou iOS sur **ne pas lier** dans les paramètres de génération du projet.
- Si rien ne se produit lors de l’enregistrement de votre fichier XAML, assurez-vous que le rechargement à chaud est activé dans l’IDE.
- Si vous effectuez un débogage sur un iPhone physique et que votre application ne répond plus, vérifiez que l’interpréteur est activé. Pour l’activer, ouvrez les paramètres du projet, sélectionnez l’onglet Build iOS, puis activez le paramètre **activer l’interpréteur mono** .

Pour signaler un bogue, utilisez l’outil commentaires dans le menu **aide** > **envoyer des commentaires** > **signaler un problème** sur Windows, et l' > **d’aide** **signaler un menu problème** sur un Mac.
