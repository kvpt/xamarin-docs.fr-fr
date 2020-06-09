---
title: Mise à jour d’une application Xamarin. iOS en arrière-plan
description: Ce document décrit les différentes façons de mettre à jour une application Xamarin. iOS en arrière-plan, comme la surveillance des régions, la récupération en arrière-plan et les notifications à distance.
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: ae08d7d2d8d9de700570311f2294df737240b73f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572154"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>Mise à jour d’une application Xamarin. iOS en arrière-plan

L’actualisation en arrière-plan est le processus qui consiste à sortir une application suspendue ou non en cours d’exécution, et à la mettre à jour avec un nouveau contenu. iOS offre trois options d’actualisation du contenu en arrière-plan :

1. *Surveillance des régions* et *changements d’emplacement significatifs* les API prenant en charge l’emplacement déclenchent des mises à jour en arrière-plan en fonction des modifications apportées à l’emplacement de l’utilisateur. Ces API peuvent être utilisées avec précaution pour actualiser le contenu des applications iOS 6 non basées sur l’emplacement, où d’autres options ne sont pas disponibles.
1. *Extraction en arrière-plan (iOS 7 +)* : approche temporelle permettant d’actualiser le contenu *non critique* mis à jour *fréquemment* .
1. *Notifications distantes (iOS 7 +)* : les applications qui reçoivent des notifications push peuvent utiliser les notifications pour déclencher des actualisations de contenu en arrière-plan. Cette méthode peut être utilisée pour effectuer une mise à jour avec *un contenu important qui respecte le temps,* qui est mis à jour de façon *sporadique* .

Les sections suivantes couvrent les principes de base de ces options.

## <a name="region-monitoring-and-significant-location-changes"></a>Surveillance des régions et modifications significatives de l’emplacement

iOS fournit deux API prenant en charge l’emplacement avec des fonctionnalités d’arrière-plan :

1. La *surveillance* des régions est le processus de configuration de régions avec des limites et de mise en éveil de l’appareil lorsque l’utilisateur entre ou quitte une région. Les régions sont circulaires et peuvent avoir une taille variable. Lorsque l’utilisateur franchit une limite de région, l’appareil se réveille pour gérer l’événement, généralement en déclenchant une notification ou en lançant une tâche. L’analyse des régions requiert le GPS et augmente la batterie et l’utilisation des données.
1. Le *service de changements d’emplacement significatifs* est une option plus simple pour les appareils avec des radios cellulaires. Une application écoutant des modifications d’emplacement importantes sera avertie lorsque l’appareil change de cellule Towers. Ce service peut être utilisé pour mettre en éveil une application interrompue ou arrêtée et permet de vérifier le nouveau contenu en arrière-plan. L’activité en arrière-plan est limitée à environ 10 secondes, sauf si elle est associée à une [tâche en arrière-plan](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .

Une application n’a pas besoin de l’emplacement `UIBackgroundMode` pour utiliser ces API prenant en charge l’emplacement. Comme iOS ne suit pas les types de tâches qui peuvent s’exécuter quand l’appareil est en veille par des modifications à l’emplacement de l’utilisateur, ces API fournissent une solution de contournement pour la mise à jour du contenu en arrière-plan sur iOS 6. N' *oubliez pas que le déclenchement des mises à jour en arrière-plan avec des API basées sur l’emplacement dessinera sur les ressources de l’appareil et risque de tromper les utilisateurs qui ne comprennent pas pourquoi une application nécessite l’accès à leur emplacement*. Utilisez la précaution lors de l’implémentation de la surveillance des régions ou des modifications d’emplacement importantes pour le traitement en arrière-plan dans les applications qui n’utilisent pas déjà les API de localisation

Les applications qui utilisent la surveillance d’emplacement pour le traitement en arrière-plan exposent une faille dans iOS 6 : si les besoins d’une application ne sont pas adaptés à une catégorie d’arrière-plan, les options d’arrière-plan sont limitées. Avec l’introduction de deux nouvelles API, l' *extraction en arrière-plan* et les *notifications distantes*, iOS 7 (et versions ultérieures) offre des possibilités d’arrière-plan à d’autres applications. Les deux sections suivantes présentent ces nouvelles API.

<a name="background_fetch"></a>

## <a name="background-fetch-ios-7-and-greater"></a>Récupération en arrière-plan (iOS 7 et versions ultérieures)

Dans iOS 6, une application qui entre en premier plan avait besoin de temps pour charger un nouveau contenu, en présentant brièvement les utilisateurs avec le contenu qu’ils ont déjà vu. La récupération en arrière-plan permet aux applications de charger de nouvelles données *avant qu'* un utilisateur ne lance l’application et de fournir à l’utilisateur le contenu le plus à jour.

Pour implémenter l’extraction en arrière-plan, modifiez *info. plist* et cochez les cases **activer les modes d’arrière-** plan et **récupérer l’arrière-plan** :

 [![](updating-an-application-in-the-background-images/fetch.png "Edit the Info.plist and check the Enable Background Modes and Background Fetch check boxes")](updating-an-application-in-the-background-images/fetch.png#lightbox)

Ensuite, dans `AppDelegate` , remplacez la `FinishedLaunching` méthode pour définir l’intervalle d’extraction minimal. Dans cet exemple, nous autorisons le système d’exploitation à déterminer la fréquence de récupération du nouveau contenu :

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Enfin, effectuez l’extraction en remplaçant la `PerformFetch` méthode dans le `AppDelegate` et en passant un gestionnaire d' *achèvement*. Le gestionnaire d’achèvement est un délégué qui prend un `UIBackgroundFetchResult` :

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Quand nous avons terminé la mise à jour du contenu, nous laissons le système d’exploitation savoir en appelant le gestionnaire d’achèvement avec l’état approprié. iOS offre trois options pour l’état du gestionnaire d’achèvement :

1. `UIBackgroundFetchResult.NewData`-Appelé quand un nouveau contenu a été récupéré et que l’application a été mise à jour.
1. `UIBackgroundFetchResult.NoData`-Appelé lorsque l’extraction de nouveau contenu est passée, mais qu’aucun contenu n’est disponible.
1. `UIBackgroundFetchResult.Failed`-Utile pour la gestion des erreurs, cette méthode est appelée lorsque l’extraction n’a pas pu traverser.

Les applications utilisant la récupération en arrière-plan peuvent appeler pour mettre à jour l’interface utilisateur à partir de l’arrière-plan. Lorsque l’utilisateur ouvre l’application, l’interface utilisateur est à jour et affiche le nouveau contenu. Cette opération met également à jour l’instantané du sélecteur d’applications de l’application, de sorte que l’utilisateur peut voir quand l’application a un nouveau contenu.

> [!IMPORTANT]
> Une fois `PerformFetch` appelée, l’application dispose d’environ 30 secondes pour lancer le téléchargement du nouveau contenu et appeler le bloc de gestionnaire d’achèvement. Si cela prend trop de temps, l’application se termine. Envisagez d’utiliser FETCH en arrière-plan avec le _service de transfert en arrière-plan_ lors du téléchargement de médias ou d’autres fichiers volumineux.

### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

Dans l’exemple de code ci-dessus, nous permettons au système d’exploitation de déterminer la fréquence d’extraction du nouveau contenu en définissant l’intervalle de récupération minimal sur `BackgroundFetchIntervalMinimum` . iOS offre trois options pour l’intervalle d’extraction :

1. `BackgroundFetchIntervalNever`-Indiquez au système de ne jamais récupérer le nouveau contenu. Utilisez cette option pour désactiver l’extraction dans certaines situations, par exemple lorsque l’utilisateur n’est pas connecté. Il s’agit de la valeur par défaut de l’intervalle d’extraction. 
1. `BackgroundFetchIntervalMinimum`-Laisser le système décider de la fréquence d’extraction en fonction des modèles utilisateur, de la durée de vie de la batterie, de l’utilisation des données et des besoins d’autres applications.
1. `BackgroundFetchIntervalCustom`-Si vous connaissez la fréquence de mise à jour du contenu d’une application, vous pouvez spécifier un intervalle de « veille » après chaque extraction, pendant laquelle l’application ne peut pas récupérer de nouveau contenu. Une fois cet intervalle défini, le système détermine le moment où il doit récupérer le contenu.

`BackgroundFetchIntervalMinimum`Et `BackgroundFetchIntervalCustom` s’appuient sur le système pour planifier des extractions. Cet intervalle est dynamique et s’adapte aux besoins de l’appareil, ainsi qu’aux habitudes de l’utilisateur individuel. Par exemple, si un utilisateur vérifie une application tous les matins, et qu’une autre vérification toutes les heures, iOS s’assure que le contenu est à jour pour les deux utilisateurs chaque fois qu’ils ouvrent l’application.

La récupération en arrière-plan doit être utilisée pour les applications qui sont fréquemment mises à jour avec du contenu non critique. Pour les applications avec des mises à jour critiques, des notifications distantes doivent être utilisées. Les notifications distantes sont basées sur la récupération en arrière-plan et partagent le même gestionnaire d’achèvement. Nous allons ensuite étudier les notifications distantes.

 <a name="remote_notifications"></a>

## <a name="remote-notifications-ios-7-and-greater"></a>Notifications à distance (iOS 7 et versions ultérieures)

Les notifications push sont des messages JSON envoyés d’un fournisseur à un appareil par le biais du *service Apple Push Notification (APNs)*.

Dans iOS 6, les notifications push entrantes indiquent au système d’alerter l’utilisateur qu’une action intéressante s’est produite dans une application. Cliquez sur la notification pour extraire l’application de l’état suspendu ou arrêté, et l’application commence à mettre à jour le contenu. iOS 7 (et versions ultérieures) étend les notifications push ordinaires en donnant aux applications la possibilité de mettre à jour le contenu en arrière-plan *avant* de notifier l’utilisateur, de sorte que l’utilisateur puisse ouvrir l’application et recevoir immédiatement un nouveau contenu.

Pour implémenter des notifications distantes, modifiez *info. plist* et cochez les cases **activer les modes d’arrière-plan** et **notifications à distance** :

 [![](updating-an-application-in-the-background-images/remote.png "Background Mode set to Enable Background Modes and Remote notifications")](updating-an-application-in-the-background-images/remote.png#lightbox)

Ensuite, affectez la valeur `content-available` 1 à l’indicateur sur la notification push. Cela permet à l’application de savoir récupérer le nouveau contenu avant d’afficher l’alerte :

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

Dans le *AppDelegate*, remplacez la `DidReceiveRemoteNotification` méthode pour vérifier la charge utile de notification pour le contenu disponible et appelez le bloc de gestionnaire d’achèvement approprié :

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Les notifications distantes doivent être utilisées pour les mises à jour peu fréquentes avec un contenu essentiel à la fonctionnalité de l’application. Pour plus d’informations sur les notifications distantes, consultez le guide Xamarin [notifications push dans iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) .

> [!IMPORTANT]
> Étant donné que le mécanisme de mise à jour dans les notifications distantes est basé sur l’extraction en arrière-plan, l’application doit lancer le téléchargement du nouveau contenu et appeler le bloc de gestionnaire d’achèvement dans les 30 secondes suivant la réception de la notification, ou iOS met fin à l’application. Envisagez de coupler les notifications à distance avec le _service de transfert en arrière-_ plan lors du téléchargement de médias ou d’autres fichiers volumineux en arrière-plan

### <a name="silent-remote-notifications"></a>Notifications distantes silencieuses

Les notifications distantes sont un moyen simple de notifier les applications de mises à jour et de lancer la récupération de nouveau contenu, mais dans certains cas, nous n’avons pas besoin d’informer l’utilisateur de la modification d’un événement. Par exemple, si un utilisateur signale un fichier pour la synchronisation, nous n’avons pas besoin de les notifier chaque fois que le fichier est mis à jour. La synchronisation de fichiers n’est pas un événement étonnant et ne nécessite pas l’attention immédiate de l’utilisateur. Les utilisateurs s’attendent à ce que le fichier soit à jour lorsqu’il l’ouvre.

Dans les cas comme celui ci-dessus, iOS autorise l’envoi de notifications push en mode silencieux, autrement dit, sans alerte. Pour transformer une notification normale en une notification en mode silencieux, supprimez simplement l’alerte de la charge utile de notification :

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Limites du débit

La plus grande différence entre les notifications normales et silencieuses du point de vue du développeur est que les push silencieux sont limités au débit. APNs retardera la remise des notifications push silencieuses à l’appareil si le taux de transmission est trop élevé. Cela permet de s’assurer que les applications ne drainent pas les ressources de l’appareil avec un trop grand nombre de notifications en mode silencieux.

Toutefois, APNs permet aux notifications silencieuses de se superposer à une notification à distance normale ou à une réponse Keep-Alive. Comme les notifications régulières ne sont pas limitées au tarif, elles peuvent être utilisées pour envoyer des notifications en mode silencieux stockées à partir du APNs vers l’appareil, comme l’illustre le diagramme suivant :

 [![](updating-an-application-in-the-background-images/silent.png "Regular notifications can be used to push stored silent notifications from the APNs to the device, as illustrated by this diagram")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple encourage les développeurs à envoyer des notifications push sans assistance chaque fois que l’application en a besoin, et à laisser les APNs planifier leur remise.

Dans cette section, nous avons abordé les différentes options d’actualisation du contenu en arrière-plan pour exécuter des tâches qui ne sont pas adaptées à une catégorie d’arrière-plan. À présent, voyons quelques-unes de ces API en action.

 [Suivant : partie 4-procédures pas à pas d’arrière-plan iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
