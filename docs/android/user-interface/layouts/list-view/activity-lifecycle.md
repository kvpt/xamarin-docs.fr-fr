---
title: Xamarin. Android ListView et le cycle de vie de l’activité
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: a4ebda89ad3bdcb663dc9d7cd513e45760163c34
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028947"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>Xamarin. Android ListView et le cycle de vie de l’activité

Les activités passent par certains États au fur et à mesure que votre application s’exécute, telle que le démarrage, l’exécution, l’interruption et l’arrêt. Pour plus d’informations et pour obtenir des instructions spécifiques sur la gestion des transitions d’État, consultez le [didacticiel cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md).
Il est important de comprendre le cycle de vie de l’activité et de placer votre code `ListView` dans les emplacements appropriés.

Tous les exemples de ce document effectuent des tâches de configuration dans la méthode `OnCreate` de l’activité et (si nécessaire) effectuent la « destruction » dans `OnDestroy`. Les exemples utilisent généralement des petits jeux de données qui ne changent pas. par conséquent, le rechargement des données est inutile.

Toutefois, si vos données changent fréquemment ou utilisent beaucoup de mémoire, il peut être approprié d’utiliser des méthodes de cycle de vie différentes pour remplir et actualiser votre `ListView`. Par exemple, si les données sous-jacentes changent constamment (ou peuvent être affectées par des mises à jour sur d’autres activités), la création de l’adaptateur dans `OnStart` ou `OnResume` garantit que les données les plus récentes sont affichées à chaque fois que l’activité est affichée.

Si l’adaptateur utilise des ressources telles que la mémoire ou un curseur géré, n’oubliez pas de libérer ces ressources dans la méthode complémentaire à l’emplacement où elles ont été instanciées (par exemple, les objets créés dans `OnStart` peuvent être supprimés dans `OnStop`).

## <a name="configuration-changes"></a>Modifications de configuration

Il est important de se souvenir que les modifications de configuration &ndash; notamment la rotation de l’écran et la visibilité du clavier &ndash; peuvent entraîner la destruction et la recréation de l’activité en cours (sauf spécification contraire de l’attribut `ConfigurationChanges`). Cela signifie que, dans des conditions normales, la rotation d’un appareil entraîne la recréation d’un `ListView` et d' `Adapter` et (à moins que vous n’ayez écrit du code dans `OnPause` et `OnResume`), les États de position de défilement et de sélection de ligne seront perdus.

L’attribut suivant empêche une activité d’être détruite et recréée suite à des modifications de configuration :

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

L’activité doit ensuite remplacer `OnConfigurationChanged` pour répondre correctement à ces modifications. Pour plus d’informations sur la gestion des modifications de configuration, consultez la documentation.
