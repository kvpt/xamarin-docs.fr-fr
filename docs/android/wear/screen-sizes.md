---
title: Utilisation des tailles d’écran dans Xamarin. Android et système d’exploitation
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 1747601596cd1772210d9a66755d7aa98ca14052
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457027"
---
# <a name="working-with-screen-sizes"></a>Utilisation des tailles d’écran

Les appareils d’usure Android peuvent avoir un affichage rectangulaire ou arrondi, qui peut également être de tailles différentes.

![Captures d’écran d’affichages rectangulaires et d’usure ronde](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Type d’écran d’identification

La bibliothèque de prise en charge de l’usure fournit des contrôles qui vous aident à détecter et à s’adapter à différentes formes d’écran, telles que `WatchViewStub` et `BoxInsetLayout` .

N’oubliez pas que certains des autres contrôles de bibliothèque de prise en charge (tels que `GridViewPager` ) détectent *automatiquement* la forme d’écran eux-mêmes et ne doivent pas être ajoutés en tant qu’enfants des contrôles décrits ci-dessous.

### <a name="watchviewstub"></a>WatchViewStub

Consultez l’exemple [WatchViewStub](/samples/xamarin/monodroid-samples/wear-watchviewstub) pour voir comment détecter le type d’écran et afficher une disposition différente pour chaque type.

Le fichier de disposition principal contient un `android.support.wearable.view.WatchViewStub` qui référence différentes dispositions pour les écrans rectangulaires et arrondis à l’aide des `app:rectLayout` `app:roundLayout` attributs et :

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

La solution contient différentes dispositions pour chaque style qui sera sélectionné au moment de l’exécution :

![Fichiers affichés sous ressources/mise en page](screen-sizes-images/solution.png)

### <a name="boxinsetlayout"></a>BoxInsetLayout

Au lieu de créer des dispositions différentes pour chaque type d’écran, vous pouvez également créer une vue unique qui s’adapte aux écrans rectangulaires ou ronds.

Cet [exemple Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) montre comment utiliser `BoxInsetLayout` pour utiliser la même disposition sur des écrans rectangulaires et ronds.

## <a name="wear-ui-designer"></a>Générateur d’interface utilisateur d’usure

Xamarin Android Designer prend en charge les écrans rectangulaires et arrondis :

![Sélection de l’écran carré d’usure Android dans le Android Designer Xamarin](screen-sizes-images/design-screen-type.png)

L’aire de conception dans un style rectangulaire est illustrée ici :

![Aire de conception dans un style rectangulaire](screen-sizes-images/design-rect.png) 

L’aire de conception dans le style Round est illustrée ici :

![Aire de conception dans un style rond](screen-sizes-images/design-round.png)

## <a name="wear-simulator"></a>Simulateur d’usure

Le **Gestionnaire d’émulateur Google** contient des définitions d’appareils pour les deux types d’écran. Vous pouvez créer des émulateurs rectangulaires et arrondis pour tester votre application.

![Usure des définitions d’appareils affichées dans le gestionnaire d’émulateur Google](screen-sizes-images/emulator-devices.png)

L’émulateur s’affiche comme suit pour un écran rectangulaire :

![Affichage de l’émulateur d’un écran rectangulaire](screen-sizes-images/recipe-2.png) 

Elle s’affiche comme suit pour un écran rond :

![Affichage de l’émulateur d’un écran rond](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Vidéo

[Applications plein écran pour Android](https://www.youtube.com/watch?v=naf_WbtFAlY) à partir de [Developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).