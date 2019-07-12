---
title: CardView
description: Le widget Cardview est un composant de l’interface utilisateur qui présente le contenu de texte et image dans les vues qui ressemblent à des cartes. Ce guide explique comment utiliser et personnaliser CardView dans les applications Xamarin.Android, tout en conservant la compatibilité descendante avec les versions antérieures d’Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: d145a8a3cd8bc321f0fce76a8831fca681ad29a0
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830577"
---
# <a name="cardview"></a>CardView

_Le widget Cardview est un composant de l’interface utilisateur qui présente le contenu de texte et image dans les vues qui ressemblent à des cartes. Ce guide explique comment utiliser et personnaliser CardView dans les applications Xamarin.Android, tout en conservant la compatibilité descendante avec les versions antérieures d’Android._


## <a name="overview"></a>Présentation

Le `Cardview` widget, introduit dans Android 5.0 (Lollipop), est un composant de l’interface utilisateur qui présente le contenu de texte et image dans les vues qui ressemblent à des cartes. `CardView` est implémenté comme un `FrameLayout` widget avec des angles arrondis et une ombre. En règle générale, un `CardView` est utilisé pour présenter un élément de ligne unique dans un `ListView` ou `GridView` afficher le groupe. Par exemple, la capture d’écran suivante est un exemple d’une application de réservation de voyages implémente `CardView`-en fonction des cartes de destination de voyage dans un défilement `ListView`:

![Exemple d’application à l’aide d’un objet CardView pour chaque destination de voyage](card-view-images/01-cardview-example.png)

Ce guide explique comment ajouter la `CardView` projet de package pour votre Xamarin.Android, comment ajouter `CardView` à votre disposition et comment personnaliser l’apparence de `CardView` dans votre application. En outre, ce guide fournit une liste détaillée des `CardView` attributs que vous pouvez modifier, y compris les attributs pour vous aider à utiliser `CardView` sur les versions d’Android antérieures à Android 5.0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Configuration requise

Ce qui suit est requis pour utiliser la nouvelle Android 5.0 et ultérieures fonctionnalités (y compris `CardView`) dans les applications basées sur Xamarin :

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 ou version ultérieure doit être installé et configuré avec Visual Studio ou Visual Studio pour Mac.

-  **Kit de développement logiciel Android** &ndash; Android 5.0 (API 21) ou version ultérieure doit être installé via le gestionnaire Android SDK.

-  **Java JDK 1.8** &ndash; JDK 1.7 peut être utilisé si vous êtes spécifiquement niveau d’API ciblé 23 et versions antérieur. JDK 1.8 est disponible à partir de [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Votre application doit également inclure le `Xamarin.Android.Support.v7.CardView` package. Pour ajouter le `Xamarin.Android.Support.v7.CardView` package dans Visual Studio pour Mac :

1. Ouvrez votre projet, avec le bouton droit **Packages** et sélectionnez **ajouter des Packages**.

2. Dans le **ajouter des Packages** boîte de dialogue, recherchez **CardView**.

3. Sélectionnez **bibliothèque de prise en charge de Xamarin v7 CardView**, puis cliquez sur **ajouter un Package**.

Pour ajouter le `Xamarin.Android.Support.v7.CardView` package dans Visual Studio :

1. Ouvrez votre projet, cliquez sur le **références** nœud (dans le **l’Explorateur de solutions** volet) et sélectionnez **gérer les Packages NuGet...** .

2. Lorsque le **gérer les Packages NuGet** boîte de dialogue s’affiche, entrez **CardView** dans la zone de recherche.

3. Lorsque **bibliothèque de prise en charge de Xamarin v7 CardView** s’affiche, cliquez sur **installer**.

Pour savoir comment configurer un projet d’application Android 5.0, consultez [paramètre configurer un projet Android 5.0](~/android/platform/lollipop.md).
Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : Inclusion d’un package NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Présentation de CardView

La valeur par défaut `CardView` ressemble à une carte blanche avec des angles arrondis au minimum et une ombre légère. L’exemple suivant **Main.axml** disposition affiche un seul `CardView` widget qui contient un `TextView`:

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

Si vous utilisez ce code XML pour remplacer le contenu existant de **Main.axml**, veillez à commenter tout code dans **MainActivity.cs** qui fait référence aux ressources dans le code XML précédent.

Cet exemple de disposition crée une valeur par défaut `CardView` avec une seule ligne de texte, comme illustré dans la capture d’écran suivante :

[![Capture d’écran de CardView avec arrière-plan blanc et de la ligne de texte](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

Dans cet exemple, le style de l’application est défini pour le matériel de thème clair (`Theme.Material.Light`) afin que le `CardView` ombres et les bords sont plus faciles à voir. Pour plus d’informations sur les applications de thèmes Android 5.0, consultez [matériau thème](~/android/user-interface/material-theme.md). Dans la section suivante, nous allez apprendre à personnaliser `CardView` pour une application.


## <a name="customizing-cardview"></a>Personnalisation des widgets CardView

Vous pouvez modifier la base `CardView` attributs afin de personnaliser l’apparence de la `CardView` dans votre application. Par exemple, l’élévation de la `CardView` peut être augmentée pour effectuer un cast d’une ombre plus volumineuse (ce qui rend la carte la plus élevée supérieure à la valeur float). En outre, le rayon de l’angle peut être augmenté pour rendre les angles de la carte plus arrondis.

Dans l’exemple suivant de disposition, personnalisé `CardView` est utilisé pour créer une simulation d’une photographie d’impression (un « instantané »). Un `ImageView` est ajouté à la `CardView` pour l’affichage de l’image et un `TextView` est positionnée au-dessous du `ImageView` d’afficher le titre de l’image.
Dans cet exemple de disposition, le `CardView` présente les personnalisations suivantes :

-  Le `cardElevation` est augmentée à 4dp peut convertir une ombre plus volumineuse.
-  Le `cardCornerRadius` est augmentée à 5dp pour afficher les angles arrondis plus.

Étant donné que `CardView` est fournie par la bibliothèque de prise en charge v7 Android, ses attributs ne sont pas disponibles à partir de la `android:` espace de noms. Par conséquent, vous devez définir votre propre espace de noms XML et utiliser cet espace de noms en tant que le `CardView` préfixe d’attribut. Dans l’exemple de disposition ci-dessous, nous allons utiliser cette ligne pour définir un espace de noms appelé `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Vous pouvez appeler cet espace de noms `card_view` , voire `myapp` si vous choisissez (il est accessible uniquement dans la portée de ce fichier). Ce que vous choisissez d’appeler cet espace de noms, vous devez l’utiliser pour préfixer la `CardView` attribut que vous souhaitez modifier. Dans cet exemple de disposition, le `cardview` espace de noms est le préfixe pour `cardElevation` et `cardCornerRadius`:

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

Lorsque cet exemple de disposition est utilisé pour afficher une image dans une application d’affichage de photos, la `CardView` a l’apparence d’un instantané de la photo, comme illustré dans la capture d’écran suivante :

[![CardView avec une image et la légende sous l’image](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Cette capture d’écran provient de la [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) exemple d’application qui utilise un `RecyclerView` widget pour présenter une liste déroulante de `CardView` images pour l’affichage des photos. Pour plus d’informations sur `RecyclerView`, consultez le [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) guide.

Notez qu’un `CardView` peut afficher plusieurs enfants dans sa zone de contenu. Par exemple, dans l’exemple d’application d’affichage de photos ci-dessus, la zone de contenu comprend un `ListView` qui contient un `ImageView` et un `TextView`. Bien que `CardView` instances sont souvent organisées verticalement, vous pouvez également les disposer horizontalement (consultez [création d’un Style d’affichage personnalisé](~/android/user-interface/material-theme.md#customview) pour une capture d’écran de l’exemple).


### <a name="cardview-layout-options"></a>Options de mise en page CardView

`CardView` mises en page peuvent être personnalisés en définissant un ou plusieurs attributs qui affectent son remplissage, élévation, rayon de l’angle et la couleur d’arrière-plan :

[![Diagramme des attributs de CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Chaque attribut peut également être modifiée de manière dynamique en appelant un équivalent `CardView` (méthode) (pour plus d’informations sur `CardView` méthodes, consultez le [référence de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Notez que ces attributs (à l’exception de la couleur d’arrière-plan) acceptent une valeur de dimension, qui est un nombre décimal suivi de l’unité. Par exemple, `11.5dp` spécifie 11,5 pixels indépendants de densité.


#### <a name="padding"></a>Padding

`CardView` propose cinq attributs de marge intérieure pour positionner du contenu au sein de la carte. Vous pouvez les définir dans votre disposition XML, ou vous pouvez appeler des méthodes analogues dans votre code :

[![Diagramme de CardView attributs de marge intérieure](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Les attributs de marge intérieure sont présentent comme suit :

-  `contentPadding` &ndash; Remplissage entre les vues enfants d’interne le `CardView` et tous les bords de la carte.

-  `contentPaddingBottom` &ndash; Remplissage entre les vues enfants d’interne le `CardView` et le bord inférieur de la carte.

-  `contentPaddingLeft` &ndash; Remplissage entre les vues enfants d’interne le `CardView` et le bord gauche de la carte.

-  `contentPaddingRight` &ndash; Remplissage entre les vues enfants d’interne le `CardView` et le bord droit de la carte.

-  `contentPaddingTop` &ndash; Remplissage entre les vues enfants d’interne le `CardView` et le bord supérieur de la carte.

Attributs de marge intérieure de contenu sont par rapport à la limite de la zone de contenu, plutôt qu’à n’importe quel widget donné situé dans la zone de contenu.
Par exemple, si `contentPadding` suffisamment une augmentation dans l’application d’affichage de photos, la `CardView` serait rogner l’image et le texte affiché sur la carte.



#### <a name="elevation"></a>Élévation

`CardView` offre deux attributs d’élévation pour contrôler son élévation et, par conséquent, la taille de son ombre :

[![Diagramme des attributs d’élévation CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Les attributs de l’élévation sont présentent comme suit :

-  `cardElevation` &ndash; L’élévation de la `CardView` (représente son axe Z).

-  `cardMaxElevation` &ndash; La valeur maximale de la `CardView`d’élévation.

Des valeurs plus élevées de `cardElevation` augmenter la taille des clichés instantanés pour rendre `CardView` semblent float supérieur au-dessus de l’arrière-plan. Le `cardElevation` attribut détermine également l’ordre de dessin de chevauchement des vues ; autrement dit, la `CardView` seront dessinés sous une autre vue qui se chevauche avec un paramètre d’élévation plus élevé et au-dessus de toutes les vues qui se chevauchent avec un paramètre d’élévation inférieur.
Le `cardMaxElevation` paramètre est utile pour lorsque votre application change dynamiquement une élévation &ndash; il empêche l’ombre de l’extension au-delà de la limite que vous définissez ce paramètre.


#### <a name="corner-radius-and-background-color"></a>Rayon de l’angle et la couleur d’arrière-plan

`CardView` propose des attributs que vous pouvez utiliser pour contrôler son rayon d’angle et sa couleur d’arrière-plan. Ces deux propriétés vous permettent de modifier le style de la `CardView`:

[![Diagramme de coin CardView radious et des attributs de couleur d’arrière-plan](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Ces attributs sont décrites comme suit :

-  `cardCornerRadius` &ndash; Le rayon de l’angle de tous les angles de la `CardView`.

-  `cardBackgroundColor` &ndash; La couleur d’arrière-plan de la `CardView`.

Dans ce diagramme, `cardCornerRadius` est défini sur un 10dp plus arrondis et `cardBackgroundColor` a la valeur `"#FFFFCC"` (jaune clair).


## <a name="compatibility"></a>Compatibilité

Vous pouvez utiliser `CardView` sur les versions d’Android antérieures à Android 5.0 Lollipop. Étant donné que `CardView` fait partie de la bibliothèque de prise en charge v7 Android, vous pouvez utiliser `CardView` avec Android 2.1 (API niveau 7) et versions ultérieures.
Toutefois, vous devez installer le `Xamarin.Android.Support.v7.CardView` package comme décrit dans [exigences](#requirements), ci-dessus.

`CardView` a un comportement légèrement différent sur les appareils avant Lollipop (niveau 21 d’API) :

-  `CardView` utilise une implémentation de clichés instantanés par programme qui ajoute une marge intérieure supplémentaire.

-  `CardView` ne détoure pas les vues enfants qui croise le `CardView`des angles arrondis.

Pour aider à gérer ces différences de compatibilité, `CardView` fournit plusieurs attributs supplémentaires que vous pouvez configurer dans votre disposition :

-   `cardPreventCornerOverlap` &ndash; Définissez cet attribut sur `true` pour ajouter une marge intérieure quand votre application s’exécute sur Android versions antérieures (niveau d’API 20 et versions antérieur). Ce paramètre empêche `CardView` contenu en intersection avec le `CardView`des angles arrondis.

-   `cardUseCompatPadding` &ndash; Définissez cet attribut sur `true` pour ajouter une marge intérieure quand votre application s’exécute dans les versions d’Android à ou supérieur à niveau 21 d’API. Si vous souhaitez utiliser `CardView` sur les appareils Lollipop préliminaire et lui ressemblent sur Lollipop (ou version ultérieure), définissez cet attribut sur `true`. Lorsque cet attribut est activé, `CardView` ajoute le remplissage supplémentaire pour dessiner des ombres lorsqu’elle s’exécute sur les appareils Lollipop préliminaire. Cela permet de surmonter les différences dans la marge intérieure qui sont introduits lors de l’ombre par programmation Lollipop préliminaire implémentations est en vigueur.

Pour plus d’informations sur la conservation de la compatibilité avec les versions antérieures d’Android, consultez [maintenir la compatibilité](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Récapitulatif

Ce guide a introduit la nouvelle `CardView` widget inclus dans Android 5.0 (Lollipop). Il vous a expliqué la valeur par défaut `CardView` apparence et expliqué comment personnaliser `CardView` en modifiant son élévation, un arrondi, remplissage de contenu et la couleur d’arrière-plan. Il apparaît la `CardView` les attributs de mise en page (avec des diagrammes de référence) et vous avez appris comment utiliser `CardView` sur les appareils Android antérieures à Android 5.0 Lollipop. Pour plus d’informations sur `CardView`, consultez le [référence de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Liens associés

- [RecyclerView (exemple)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Présentation d’interface (lollipop)](~/android/platform/lollipop.md)
- [Référence de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
