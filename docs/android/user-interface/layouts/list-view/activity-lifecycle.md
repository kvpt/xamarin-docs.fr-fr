---
title: Xamarin. Android ListView et le cycle de vie de l’activité
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 7c6e395a353dcfd737ad244df9d169edc5b08f1c
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510307"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>Xamarin. Android ListView et le cycle de vie de l’activité

Les activités passent par certains États au fur et à mesure que votre application s’exécute, telle que le démarrage, l’exécution, l’interruption et l’arrêt. Pour plus d’informations et pour obtenir des instructions spécifiques sur la gestion des transitions d’État, consultez le [didacticiel cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md).
Il est important de comprendre le cycle de vie de l' `ListView` activité et de placer votre code dans les emplacements appropriés.

Tous les exemples de ce document effectuent des tâches de configuration dans la méthode de `OnCreate` l’activité et (si nécessaire) effectuent la « `OnDestroy`destruction» dans. Les exemples utilisent généralement des petits jeux de données qui ne changent pas. par conséquent, le rechargement des données est inutile.

Toutefois, si vos données changent fréquemment ou utilisent beaucoup de mémoire, il peut être approprié d’utiliser des méthodes de cycle de vie différentes pour remplir `ListView`et actualiser votre. Par exemple, si les données sous-jacentes changent constamment (ou peuvent être affectées par des mises à jour sur d’autres activités), `OnStart` la `OnResume` création de l’adaptateur dans ou garantit que les données les plus récentes sont affichées à chaque fois que l’activité est affichée.

Si l’adaptateur utilise des ressources telles que la mémoire ou un curseur géré, n’oubliez pas de libérer ces ressources dans la méthode complémentaire à l’emplacement où elles ont été instanciées (par exemple, les objets créés `OnStart` dans peuvent être supprimés de dans `OnStop`).


## <a name="configuration-changes"></a>Modifications de configuration

Il est important de se souvenir que les &ndash; modifications de configuration, notamment la &ndash; rotation de l’écran et la visibilité du clavier, peuvent entraîner la destruction et la recréation de `ConfigurationChanges` l’activité en cours (sauf indication contraire) à l’aide de attribut). Cela signifie que, dans des conditions normales, la rotation d’un `ListView` appareil `Adapter` entraîne la recréation d’un et d’un et (sauf si `OnPause` vous `OnResume`avez écrit du code dans et), les États de position de défilement et de sélection de ligne seront perdus.

L’attribut suivant empêche une activité d’être détruite et recréée suite à des modifications de configuration:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

L’activité doit ensuite remplacer `OnConfigurationChanged` pour répondre à ces modifications de manière appropriée. Pour plus d’informations sur la gestion des modifications de configuration, consultez la documentation.

