---
title: Rechargement à chaud XAML pour Xamarin. Forms
description: Rechargez les modifications apportées à votre fichier XAML instantanément sur votre application en cours d’exécution. vous n’avez donc pas besoin de générer votre projet Xamarin. Forms après chaque modification XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: 2a47876b7b53cf557014c772333e651146afe53f
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106014"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>Rechargement à chaud XAML pour Xamarin. Forms (version préliminaire)

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

Le rechargement à chaud XAML se connecte à votre flux de travail existant pour augmenter votre productivité et vous faire gagner du temps. Sans le rechargement à chaud XAML, vous devez générer et déployer votre application chaque fois que vous souhaitez voir une modification XAML. Avec le rechargement à chaud, lorsque vous enregistrez votre fichier XAML, les modifications sont reflétées dans votre application en cours d’exécution. En outre, votre état de navigation et vos données sont conservés, ce qui vous permet d’effectuer rapidement une itération sur votre interface utilisateur sans perdre votre place dans l’application. Par conséquent, avec le rechargement à chaud XAML, vous allez consacrer moins de temps à la reconstruction et au déploiement de vos applications pour valider les modifications de l’interface utilisateur.

> [!NOTE]
> Si vous écrivez une application WPF ou UWP, consultez [rechargement à chaud XAML pour UWP et WPF](/visualstudio/debugger/xaml-hot-reload).

## <a name="system-requirements"></a>Configuration requise

| IDE/infrastructure | Version requise |
|------|------------------|
|Visual Studio 2019 | 16,3 ou version ultérieure
Visual Studio 2019 pour Mac | 8,3 ou version ultérieure
Xamarin.Forms | 4,1 ou version ultérieure

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>Utiliser le rechargement à chaud XAML pour Xamarin. Forms

Aucune installation ou configuration supplémentaire n’est requise pour utiliser le rechargement à chaud XAML. Il est intégré à Visual Studio et peut être activé dans les paramètres de l’IDE. Une fois activé, vous pouvez commencer à utiliser le rechargement à chaud XAML en déboguant votre application sur un émulateur, un simulateur ou un appareil physique. Actuellement, le rechargement à chaud XAML fonctionne uniquement lors du débogage sur iOS ou Android.

Sur Windows, le rechargement à chaud XAML peut être activé en activant la case à cocher **activer le rechargement à chaud Xamarin** dans **Outils** > **options** > **Xamarin** > **rechargement chaud**.

Sur un Mac, le rechargement à chaud XAML peut être activé en activant la case à cocher **activer le rechargement Xamarin** à chaud dans les**projets** > **Préférences** >  **Visual Studio** > **Xamarin rechargement à chaud**.

## <a name="resilient-reloading"></a>Rechargement résilient

Si vous apportez une modification que le rechargement à chaud XAML ne peut pas recharger, un message d’erreur s’affiche à l’aide d’IntelliSense. Ces modifications, appelées modifications incorrectes, incluent le typage incorrect de votre code XAML ou la connexion d’un contrôle à un gestionnaire d’événements qui n’existe pas. Même avec une modification impropre, vous pouvez continuer à recharger sans redémarrer l’application : effectuez une autre modification ailleurs dans le fichier XAML et appuyez sur Enregistrer. La modification impropre n’est pas rechargée, mais vos autres modifications continuent à être appliquées.

## <a name="known-limitations"></a>Limites connues

- Vous ne pouvez pas ajouter, supprimer ou renommer des fichiers ou des packages NuGet au cours d’une session de rechargement à chaud XAML. Si vous ajoutez ou supprimez un fichier ou un package NuGet, régénérez et redéployez votre application pour continuer à utiliser le rechargement à chaud XAML.
- Définissez l’éditeur de liens sur **Link None** pour une expérience optimale. Le paramètre du **Kit de développement logiciel (SDK) Link ne fonctionne que** la plupart du temps, mais il peut échouer dans certains cas.
- Le débogage sur un iPhone physique requiert que l’interpréteur utilise le rechargement à chaud XAML. Ajoutez **--interpréteur** dans le champ **arguments mTouch supplémentaires** de vos paramètres de build IOS pour utiliser le rechargement à chaud XAML.
- Toutes les références créées en affectant un contrôle à un autre champ ou propriété `x:Name` à l’aide de sa valeur ne sont pas rechargées.
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
- Si vous effectuez un débogage sur un iPhone physique et que votre application ne répond plus, vérifiez que l’interpréteur est activé. Pour l’activer, ajoutez **--interpréteur** dans le champ **arguments mTouch supplémentaires** de vos paramètres de génération iOS.

Pour signaler un bogue, utilisez l’outil de commentaires dans le menu de l' **aide** > **Envoyer des commentaires** > sur Windows et**le menu de** l' **aide** > **signaler un problème** sur un Mac.
