---
title: CardView
description: Le widget CardView est un composant d’interface utilisateur qui présente les contenus de texte et d’image dans les affichages qui ressemblent à des cartes. Ce guide explique comment utiliser et personnaliser CardView dans les applications Xamarin. Android tout en conservant la compatibilité descendante avec les versions antérieures d’Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 37afc9ef7773bbfefe442216055c0501af2ab966
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292280"
---
# <a name="xamarinandroid-cardview"></a>Xamarin. Android CardView

_Le widget CardView est un composant d’interface utilisateur qui présente les contenus de texte et d’image dans les affichages qui ressemblent à des cartes. Ce guide explique comment utiliser et personnaliser CardView dans les applications Xamarin. Android tout en conservant la compatibilité descendante avec les versions antérieures d’Android._

## <a name="overview"></a>Vue d’ensemble

Le widget `Cardview`, introduit dans Android 5,0 (lollipop), est un composant d’interface utilisateur qui présente le contenu du texte et de l’image dans les affichages qui ressemblent aux cartes. `CardView` est implémenté en tant que widget `FrameLayout` avec des angles arrondis et une ombre. En règle générale, un `CardView` est utilisé pour présenter un élément de ligne unique dans un groupe d’affichage `ListView` ou `GridView`. Par exemple, la capture d’écran suivante est un exemple d’application de réservation de voyage qui implémente des cartes de destination de voyage `CardView`dans un `ListView`à défilement :

![Exemple d’application utilisant un CardView pour chaque destination de voyage](card-view-images/01-cardview-example.png)

Ce guide explique comment ajouter le package `CardView` à votre projet Xamarin. Android, comment ajouter des `CardView` à votre disposition et comment personnaliser l’apparence des `CardView` dans votre application. En outre, ce guide fournit une liste détaillée des attributs de `CardView` que vous pouvez modifier, y compris les attributs pour vous aider à utiliser `CardView` sur les versions d’Android antérieures à l’interface Lollipop Android 5,0.

<a name="requirements" />

## <a name="requirements"></a>Spécifications

Les éléments suivants sont requis pour utiliser les nouvelles fonctionnalités Android 5,0 et versions ultérieures (y compris `CardView`) dans les applications basées sur Xamarin :

- **Xamarin. android** &ndash; Xamarin. Android 4,20 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac.

- **Android SDK** &ndash; Android 5,0 (API 21) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

- **Java jdk 1,8** &ndash; JDK 1,7 peut être utilisé si vous ciblez spécifiquement le niveau d’API 23 et les versions antérieures. JDK 1,8 est disponible à partir d' [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Votre application doit également inclure le package `Xamarin.Android.Support.v7.CardView`. Pour ajouter le package `Xamarin.Android.Support.v7.CardView` dans Visual Studio pour Mac :

1. Ouvrez votre projet, cliquez avec le bouton droit sur **packages** , puis sélectionnez **Ajouter des packages**.

2. Dans la boîte de dialogue **Ajouter des packages** , recherchez **CardView**.

3. Sélectionnez la **bibliothèque de prise en charge Xamarin v7 CardView**, puis cliquez sur **Ajouter un package**.

Pour ajouter le package `Xamarin.Android.Support.v7.CardView` dans Visual Studio :

1. Ouvrez votre projet, cliquez avec le bouton droit sur le nœud **références** (dans le volet **Explorateur de solutions** ), puis sélectionnez **gérer les packages NuGet...** .

2. Quand la boîte de dialogue **gérer les packages NuGet** s’affiche, entrez **CardView** dans la zone de recherche.

3. Lorsque la **bibliothèque de prise en charge Xamarin v7 CardView** s’affiche, cliquez sur **installer**.

Pour savoir comment configurer un projet d’application Android 5,0, consultez [configuration d’un projet android 5,0](~/android/platform/lollipop.md).
Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : inclusion d’un NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

## <a name="introducing-cardview"></a>Présentation de CardView

Le `CardView` par défaut ressemble à une carte blanche avec des angles avec un arrondi minimal et une petite ombre. L’exemple suivant de disposition **main. AXML** affiche un widget de `CardView` unique qui contient un `TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Si vous utilisez ce code XML pour remplacer le contenu existant de **main. AXML**, veillez à commenter tout code dans **MainActivity.cs** qui fait référence aux ressources dans le code XML précédent.

Cet exemple de disposition crée une `CardView` par défaut avec une seule ligne de texte, comme indiqué dans la capture d’écran suivante :

[Capture d’écran ![de CardView avec l’arrière-plan et la ligne de texte blancs](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

Dans cet exemple, le style d’application est défini sur le thème de matériau clair (`Theme.Material.Light`) afin que les ombres et les bords `CardView` soient plus faciles à voir. Pour plus d’informations sur les applications Android 5,0, consultez [thème matériel](~/android/user-interface/material-theme.md). Dans la section suivante, nous allons apprendre à personnaliser `CardView` pour une application.

## <a name="customizing-cardview"></a>Personnalisation de CardView

Vous pouvez modifier les attributs de `CardView` de base pour personnaliser l’apparence des `CardView` dans votre application. Par exemple, l’élévation de la `CardView` peut être augmentée pour effectuer un cast d’une ombre plus grande (ce qui fait que la carte semble flotter au-dessus de l’arrière-plan). En outre, le rayon de l’angle peut être augmenté pour augmenter l’arrondi des angles de la carte.

Dans l’exemple de présentation suivant, un `CardView` personnalisé est utilisé pour créer une simulation d’une photographie d’impression (un « instantané »). Une `ImageView` est ajoutée au `CardView` pour afficher l’image, et un `TextView` est positionné sous le `ImageView` pour afficher le titre de l’image.
Dans cet exemple de disposition, le `CardView` présente les personnalisations suivantes :

- La `cardElevation` est augmentée à 4DP pour effectuer un cast d’une ombre plus grande.
- La `cardCornerRadius` est augmentée sur 5DP pour que les angles apparaissent plus arrondis.

Étant donné que `CardView` est fournie par la bibliothèque de prise en charge Android v7, ses attributs ne sont pas disponibles à partir de l’espace de noms `android:`. Par conséquent, vous devez définir votre propre espace de noms XML et utiliser cet espace de noms comme préfixe d’attribut `CardView`. Dans l’exemple de disposition ci-dessous, nous allons utiliser cette ligne pour définir un espace de noms appelé `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Vous pouvez appeler cet espace de noms `card_view` ou même `myapp` si vous le choisissez (il est accessible uniquement dans le cadre de ce fichier). Tout ce que vous choisissez d’appeler cet espace de noms, vous devez l’utiliser pour préfixer l’attribut `CardView` que vous souhaitez modifier. Dans cet exemple de disposition, l’espace de noms `cardview` est le préfixe pour `cardElevation` et `cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Lorsque cet exemple de disposition est utilisé pour afficher une image dans une application de visualisation photo, le `CardView` a l’apparence d’un instantané de photo, comme illustré dans la capture d’écran suivante :

[![CardView avec une image et une légende sous l’image](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Cette capture d’écran est tirée de l’exemple d’application [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , qui utilise un widget `RecyclerView` pour présenter une liste déroulante de `CardView` images permettant d’afficher des photos. Pour plus d’informations sur `RecyclerView`, consultez le guide [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) .

Notez qu’une `CardView` peut afficher plusieurs vues enfants dans sa zone de contenu. Par exemple, dans l’exemple d’application de visualisation de photo ci-dessus, la zone de contenu est composée d’un `ListView` qui contient un `ImageView` et un `TextView`. Bien que les instances `CardView` soient souvent organisées verticalement, vous pouvez également les réorganiser horizontalement (consultez [création d’un style de vue personnalisé](~/android/user-interface/material-theme.md#customview) pour obtenir un exemple de capture d’écran).

### <a name="cardview-layout-options"></a>Options de disposition de CardView

`CardView` dispositions peuvent être personnalisées en définissant un ou plusieurs attributs qui affectent son remplissage, son élévation, son rayon d’angle et sa couleur d’arrière-plan :

[![diagramme des attributs CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Chaque attribut peut également être modifié dynamiquement en appelant une méthode de `CardView` équivalente (pour plus d’informations sur les méthodes de `CardView`, consultez la [référence de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Notez que ces attributs (à l’exception de la couleur d’arrière-plan) acceptent une valeur de dimension, qui est un nombre décimal suivi de l’unité. Par exemple, `11.5dp` spécifie des pixels indépendants de la densité 11,5.

#### <a name="padding"></a>Remplissage

`CardView` propose cinq attributs de remplissage pour positionner le contenu dans la carte. Vous pouvez les définir dans votre fichier XML de disposition ou vous pouvez appeler des méthodes analogues dans votre code :

[![diagramme des attributs de remplissage de CardView](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Les attributs de remplissage sont expliqués comme suit :

- `contentPadding` &ndash; le remplissage interne entre les vues enfants du `CardView` et tous les bords de la carte.

- `contentPaddingBottom` &ndash; le remplissage interne entre les vues enfants du `CardView` et le bord inférieur de la carte.

- `contentPaddingLeft` &ndash; le remplissage interne entre les vues enfants du `CardView` et le bord gauche de la carte.

- `contentPaddingRight` &ndash; le remplissage interne entre les vues enfants du `CardView` et le bord droit de la carte.

- `contentPaddingTop` &ndash; le remplissage interne entre les vues enfants du `CardView` et le bord supérieur de la carte.

Les attributs de remplissage du contenu sont relatifs à la limite de la zone de contenu plutôt qu’à un widget donné situé dans la zone de contenu.
Par exemple, si `contentPadding` ont été suffisamment augmentées dans l’application d’affichage photo, le `CardView` rogne l’image et le texte indiqué sur la carte.

#### <a name="elevation"></a>Elevation

`CardView` offre deux attributs d’élévation pour contrôler son élévation et, par conséquent, la taille de son ombre :

[![diagramme des attributs d’élévation de CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Les attributs d’élévation sont expliqués comme suit :

- `cardElevation` &ndash; l’élévation du `CardView` (représente son axe Z).

- `cardMaxElevation` &ndash; la valeur maximale de l’élévation de `CardView`.

Les plus grandes valeurs de `cardElevation` augmentent la taille de l’ombre afin que `CardView` semblent flotter au-dessus de l’arrière-plan. L’attribut `cardElevation` détermine également l’ordre de dessin des vues qui se chevauchent ; autrement dit, le `CardView` est dessiné sous une autre vue qui se chevauche avec un paramètre d’élévation plus élevé et au-dessus de toutes les vues qui se chevauchent avec un paramètre d’élévation inférieure.
Le paramètre `cardMaxElevation` est utile lorsque votre application modifie dynamiquement l’élévation &ndash; elle empêche l’ombre de s’étendre au-delà de la limite que vous définissez avec ce paramètre.

#### <a name="corner-radius-and-background-color"></a>Rayon de l’angle et couleur d’arrière-plan

`CardView` propose des attributs que vous pouvez utiliser pour contrôler son rayon d’angle et sa couleur d’arrière-plan. Ces deux propriétés vous permettent de modifier le style global de l' `CardView`:

[![diagramme de l’angle de l’angle de CardView et des attributs de couleur d’arrière-plan](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Ces attributs sont expliqués comme suit :

- `cardCornerRadius` &ndash; le rayon de l’angle de tous les angles de l' `CardView`.

- `cardBackgroundColor` &ndash; la couleur d’arrière-plan du `CardView`.

Dans ce diagramme, `cardCornerRadius` est défini sur un 10dp plus arrondi et `cardBackgroundColor` est défini sur `"#FFFFCC"` (jaune clair).

## <a name="compatibility"></a>Compatibilité

Vous pouvez utiliser `CardView` sur les versions d’Android antérieures à l’interface Lollipop Android 5,0. Étant donné que `CardView` fait partie de la bibliothèque de prise en charge Android v7, vous pouvez utiliser `CardView` avec Android 2,1 (niveau d’API 7) et ultérieur.
Toutefois, vous devez installer le package `Xamarin.Android.Support.v7.CardView` comme décrit dans la section [Configuration requise](#requirements), ci-dessus.

`CardView` présente un comportement légèrement différent sur les appareils avant les interfaces Lollipop (niveau d’API 21) :

- `CardView` utilise une implémentation Shadow de programmation qui ajoute un remplissage supplémentaire.

- `CardView` ne découpe pas les vues enfants qui croisent les angles arrondis du `CardView`.

Pour faciliter la gestion de ces différences de compatibilité, `CardView` fournit plusieurs attributs supplémentaires que vous pouvez configurer dans votre disposition :

- `cardPreventCornerOverlap` &ndash; définir cet attribut sur `true` pour ajouter un remplissage lorsque votre application s’exécute sur des versions Android antérieures (niveau d’API 20 et antérieur). Ce paramètre empêche `CardView` contenu d’intersecter avec les angles arrondis du `CardView`.

- `cardUseCompatPadding` &ndash; définissez cet attribut sur `true` pour ajouter un remplissage lorsque votre application s’exécute dans des versions d’Android au moins au niveau d’API 21. Si vous souhaitez utiliser `CardView` sur des appareils pré-Lollipop et que vous voulez qu’ils aient le même aspect sur Lollipop (ou version ultérieure), définissez cet attribut sur `true`. Lorsque cet attribut est activé, `CardView` ajoute un remplissage supplémentaire pour dessiner des ombres lorsqu’il s’exécute sur des appareils pré-Lollipop. Cela permet de surmonter les différences de remplissage introduites lorsque les implémentations de clichés instantanés prédéfinies sont en vigueur.

Pour plus d’informations sur la gestion de la compatibilité avec les versions antérieures d’Android, voir [maintenance de la compatibilité](https://developer.android.com/training/material/compatibility.html).

## <a name="summary"></a>Résumé

Ce guide a introduit le nouveau `CardView` widget inclus dans Android 5,0 (lollipop). Il a démontré l’apparence `CardView` par défaut et expliqué comment personnaliser `CardView` en modifiant son élévation, son arrondi, son remplissage et sa couleur d’arrière-plan. Il répertorie les attributs de mise en page `CardView` (avec les diagrammes de référence) et explique comment utiliser des `CardView` sur des appareils Android antérieurs à l’interface Lollipop 5,0 d’Android. Pour plus d’informations sur `CardView`, consultez la [référence de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).

## <a name="related-links"></a>Liens connexes

- [RecyclerView (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Présentation du symbole Lollipop](~/android/platform/lollipop.md)
- [Informations de référence sur la classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
