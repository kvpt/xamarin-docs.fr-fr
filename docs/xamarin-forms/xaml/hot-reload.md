---
title: Rechargement à chaud XAML pour Xamarin.Forms
description: Rechargez les modifications apportées à votre fichier XAML instantanément sur votre application en cours d’exécution afin de ne pas avoir à générer votre Xamarin.Forms projet après chaque modification XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/14/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ca79a8a4059a303b6f4bfa152d645311a6056ffd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563885"
---
# <a name="xaml-hot-reload-for-no-locxamarinforms"></a>Rechargement à chaud XAML pour Xamarin.Forms

Le rechargement à chaud XAML se connecte à votre flux de travail existant pour augmenter votre productivité et vous faire gagner du temps. Sans le rechargement à chaud XAML, vous devez générer et déployer votre application chaque fois que vous souhaitez voir une modification XAML. Avec le rechargement à chaud, lorsque vous enregistrez votre fichier XAML, les modifications sont reflétées dans votre application en cours d’exécution. En outre, votre état de navigation et vos données sont conservés, ce qui vous permet d’effectuer rapidement une itération sur votre interface utilisateur sans perdre votre place dans l’application. Par conséquent, avec le rechargement à chaud XAML, vous allez consacrer moins de temps à la reconstruction et au déploiement de vos applications pour valider les modifications de l’interface utilisateur.

> [!NOTE]
> Si vous écrivez une application WPF ou UWP, consultez [rechargement à chaud XAML pour UWP et WPF](/visualstudio/debugger/xaml-hot-reload).
>
> Le rechargement à chaud XAML pour Xamarin.Forms ne fonctionne _pas_ actuellement pour les Xamarin.Forms projets UWP.

## <a name="system-requirements"></a>Configuration système requise

| IDE/infrastructure | Version requise |
|------|------------------|
|Visual Studio 2019 | 16,4 ou version ultérieure
Visual Studio 2019 pour Mac | 8,4 ou version ultérieure
Xamarin.Forms | 4,1 ou version ultérieure

## <a name="enable-xaml-hot-reload-for-no-locxamarinforms"></a>Activer le rechargement à chaud XAML pour Xamarin.Forms

Si vous démarrez à partir d’un modèle, le rechargement à chaud XAML est activé par défaut et le projet est configuré pour fonctionner sans aucune configuration supplémentaire. Déboguez votre application sur un émulateur, un simulateur ou un appareil physique Android ou iOS, modifiez votre code XAML, puis enregistrez votre fichier pour déclencher un rechargement à chaud XAML.

Si vous travaillez à partir d’une Xamarin.Forms solution existante, aucune installation supplémentaire n’est requise pour utiliser le rechargement à chaud XAML, mais vous devrez peut-être vérifier votre configuration pour garantir la meilleure expérience. Tout d’abord, activez-le dans les paramètres de votre IDE :

* Sur Windows, activez la case à cocher **activer le rechargement à chaud Xamarin** dans **Outils**  >  **options**  >  **Xamarin**  >  **rechargement chaud**.
* Sur Mac, activez la case à cocher **activer le rechargement à chaud Xamarin** dans Outils Préférences **Visual Studio**  >  **Preferences**  >  **pour**le  >  **rechargement à chaud XAML**Xamarin.
  * Dans les versions antérieures de Visual Studio pour Mac, le menu se trouve dans les préférences **Visual Studio**  >  **Preferences**  >  **projets**  >  **Xamarin rechargement à chaud**.

Ensuite, dans vos paramètres de build Android et iOS, vérifiez que l’éditeur de liens est défini sur « ne pas lier » ou sur « lien aucun ». Pour utiliser le rechargement à chaud XAML avec un appareil iOS physique, vous devez également **activer l’option Activer l’interpréteur mono** (visual studio 16,4 et versions ultérieures) ou ajouter **--interpréteur** à vos **arguments mtouch supplémentaires** (Visual Studio 16,3 et versions antérieures).

Vous pouvez utiliser l’organigramme suivant pour vérifier la configuration de votre projet existant en vue de son utilisation avec le rechargement à chaud XAML :

![Configuration du rechargement à chaud XAML](hot-reload-images/hotreloadflowchart.png "Organigramme de configuration du rechargement à chaud XAML")

## <a name="resilient-reloading"></a>Rechargement résilient

Si vous apportez une modification que le rechargement à chaud XAML ne peut pas recharger, un message d’erreur s’affiche à l’aide d’IntelliSense. Ces modifications, appelées modifications incorrectes, incluent le typage incorrect de votre code XAML ou la connexion d’un contrôle à un gestionnaire d’événements qui n’existe pas. Même avec une modification impropre, vous pouvez continuer à recharger sans redémarrer l’application : effectuez une autre modification ailleurs dans le fichier XAML et appuyez sur Enregistrer. La modification impropre n’est pas rechargée, mais vos autres modifications continuent à être appliquées.

## <a name="reload-on-multiple-platforms-at-once"></a>Rechargement sur plusieurs plateformes à la fois

Le rechargement à chaud XAML prend en charge le débogage simultané dans Visual Studio et Visual Studio pour Mac. Vous pouvez déployer une cible Android et une cible iOS en même temps pour voir vos modifications reflétées sur les deux plateformes à la fois. Pour déboguer sur plusieurs plateformes, consultez :
* **Windows** [: définir plusieurs projets de démarrage](/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [définir plusieurs projets de démarrage](/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>Limitations connues

* Les autres Xamarin.Forms cibles, telles que UWP et MacOS, ne sont *pas* encore prises en charge. Vous pouvez suivre la progression de la prise en charge UWP [ici](https://developercommunity.visualstudio.com/idea/661682/xaml-hot-reload-for-xamarinforms-on-uwp.html).
* Vous ne pouvez pas ajouter, supprimer ou renommer des fichiers ou des packages NuGet au cours d’une session de rechargement à chaud XAML. Si vous ajoutez ou supprimez un fichier ou un package NuGet, régénérez et redéployez votre application pour continuer à utiliser le rechargement à chaud XAML.
* Configurez votre éditeur de liens pour qu’il **ne** soit ni lié, ni **lié** à la meilleure expérience. Le paramètre du **Kit de développement logiciel (SDK) Link ne fonctionne que** la plupart du temps, mais il peut échouer dans certains cas. Les paramètres de l’éditeur de liens se trouvent dans vos options de génération Android et iOS.
* Le débogage sur un iPhone physique requiert que l’interpréteur utilise le rechargement à chaud XAML. Pour ce faire, ouvrez les paramètres du projet, sélectionnez l’onglet Build iOS et assurez-vous que **l’option Activer le paramètre interpréteur mono** est activée. Vous devrez peut-être modifier l’option de **plateforme** en haut de la page de propriétés en **iPhone**.
* Toutes les références créées en affectant un contrôle à un autre champ ou propriété à l’aide de sa `x:Name` valeur ne sont pas rechargées.
* La mise à jour de la hiérarchie visuelle de votre application de Shell dans AppShell. XAML peut entraîner des problèmes de maintenance de l’état de votre application. Si vous rencontrez des problèmes, régénérez l’application pour continuer le rechargement.
* Le rechargement à chaud XAML ne peut pas recharger le code C#, y compris les gestionnaires d’événements, les contrôles personnalisés, la page code-behind et les autres classes.

## <a name="more-resources"></a>Plus de ressources

* [Trucs et astuces pour le rechargement à chaud XAML](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
* [Rechargement à chaud XAML pour Xamarin.Forms en profondeur : le Xamarin Show](https://www.youtube.com/watch?v=crhjjPjzknk)

## <a name="troubleshooting"></a>Résolution des problèmes

* Si le rechargement à chaud XAML ne parvient pas à s’initialiser :
  * Mettez à jour votre Xamarin.Forms version.
  * Vérifiez que vous disposez de la dernière version de l’IDE.
  * Définissez vos paramètres de l’éditeur de liens Android ou iOS sur **ne pas lier** dans les paramètres de génération du projet.
* Si rien ne se produit lors de l’enregistrement de votre fichier XAML, assurez-vous que le rechargement à chaud XAML est activé dans l’IDE.
* Si vous effectuez un débogage sur un iPhone physique et que votre application ne répond plus, vérifiez que l’interpréteur est activé. Pour l’activer, activez la case à cocher **activer l’interpréteur mono** (Visual Studio 16.4/8.4 and up) ou Add **--interpréteur** dans le champ **arguments mTouch supplémentaires** (Visual Studio 16.3/8.3 et versions antérieures) dans vos paramètres de génération iOS.

Pour signaler un bogue, utilisez **l’aide**  >  **Envoyer des commentaires**  >  **signaler un problème** sur Windows et **l’aide**pour  >  **signaler un problème** sur Mac.