---
title: Unification des composants de Google Play Services et de NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 524456a5d5419c9ef2f2cef741135c90e624b620
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521598"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unification des composants de Google Play Services et de NuGet

## <a name="history"></a>Historique

Il s’agissait de plusieurs composants Google Play Services et de packages NuGet:

- Google Play Services (Froyo)
- Google Play Services (Gingerbread)
- Google Play Services (ICS)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

Google ne fournit en fait que deux fichiers. jar pour Google Play Services:

- `google-play-services-froyo.jar`
- `google-play-services.jar`

L’écart était dû au fait que nos outils n’indiquaient `aapt.exe` pas correctement le niveau d’API de ressource maximal à utiliser pour une application donnée. Cela signifiait que nous ayons reçu des erreurs de compilation si nous avons essayé d’utiliser la liaison Google Play Services (KitKat) sur un niveau d’API inférieur comme Gingerbread.

## <a name="unifying-google-play-services"></a>Unification des Google Play Services

Dans les versions les plus récentes de Xamarin. Android, nous `aapt.exe` indiquons maintenant la version de ressource maximale à utiliser. ce problème est donc absent pour nous.

Cela signifie qu’il n’existe aucune véritable raison d’avoir des packages distincts pour Gingerbread/ICS/JellyBean/KitKat (Toutefois, nous avons besoin d’une liaison distincte pour Froyo, car il s’agit d’un fichier. jar différent).

Pour faciliter les choses pour les développeurs, nous avons à présent unifié nos composants et les packages NuGet en deux:

- Google Play Services (Froyo) (Bindings `google-play-services-froyo.jar`)
- Google Play Services (liaisons `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Lequel doit être utilisé?

Dans presque tous les cas, Google Play Services doit être utilisé. La seule raison d’utiliser le package (Froyo) est si vous ciblez activement Froyo. La seule raison pour laquelle ce fichier. jar distinct existe de Google est que Froyo se trouve sur un petit pourcentage d’appareils, ils ont eux-mêmes décidé d’arrêter la prise en charge de ce fichier. jar est donc un instantané figé et non pris en charge de Google Play Services.

### <a name="note-about-gingerbread"></a>Remarque à propos de Gingerbread

Gingerbread n’a pas de prise en charge de fragment par défaut, et pour cette raison, certaines des classes de la liaison ne seront pas utilisables dans une application au moment de l’exécution sur un appareil Gingerbread. Les classes `MapFragment` telles que ne fonctionnent pas sur Gingerbread et leur variante de prise en charge `SupportMapFragment`doit être utilisée à la place. C’est au développeur de savoir laquelle utiliser. Cette incompatibilité est indiquée par Google dans la documentation de Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>Qu’advient-il des anciens composants/NuGet?

Étant donné qu’ils ne sont plus nécessaires, nous avons désactivé/dérépertorié les composants/packages NuGet suivants:

- Google Play Services (Gingerbread)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

Le composant _Google Play services (ICS)_ existant/NuGet a été renommé en _Google Play services_ et sera mis à jour à l’avenir. Tous les projets faisant référence à l’un des packages désactivés/dérépertoriés doivent être mis à jour pour utiliser celui-ci.

Les composants désactivés existent toujours et doivent pouvoir être restaurés pour les projets dans lesquels ils sont toujours référencés, afin d’éviter de les rompre. De même, les packages NuGet dérépertoriés existent toujours et peuvent être restaurés. Ils ne seront pas mis à jour à l’avenir.
