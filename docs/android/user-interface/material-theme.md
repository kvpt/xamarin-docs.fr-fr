---
title: Thème matériau
description: Comment thème votre application Xamarin. Android avec un thème de matériau
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 027460196f97173fcf977971b79def722ab672d9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647864"
---
# <a name="material-theme"></a>Thème matériau

Le *thème matériau* est un style d’interface utilisateur qui détermine l’apparence et la convivialité des vues et des activités à partir d’Android 5,0 (lollipop). Le thème du matériau est intégré à Android 5,0, de sorte qu’il est utilisé par l’interface utilisateur du système, ainsi que par les applications. Le thème matériel n’est pas un «thème» dans le sens d’une option d’apparence à l’ensemble du système qu’un utilisateur peut choisir dynamiquement dans un menu de paramètres. Au lieu de cela, le thème matériel peut être considéré comme un ensemble de styles de base intégrés associés que vous pouvez utiliser pour personnaliser l’apparence de votre application.

Android offre trois types de thèmes matériels:

-  `Theme.Material`&ndash; Version sombre du thème du matériau; il s’agit de la version par défaut dans Android 5,0.

-  `Theme.Material.Light`&ndash; Version légère du thème du matériau.

-  `Theme.Material.Light.DarkActionBar`&ndash; Version légère du thème de matériau, mais avec une barre d’action foncée.

Des exemples de ces types de thèmes de matériau s’affichent ici:

[![Exemples de captures d’écran du thème sombre, du thème clair et du thème Barre d’action foncé](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Vous pouvez dériver à partir d’un thème de matériau pour créer votre propre thème, en remplaçant certains ou tous les attributs de couleur. Par exemple, vous pouvez créer un thème qui dérive de `Theme.Material.Light`, mais substitue la couleur de la barre de l’application pour qu’elle corresponde à la couleur de votre personnalisation. Vous pouvez également styliser des vues individuelles. par exemple, vous pouvez créer un style pour [CardView](~/android/user-interface/controls/card-view.md) qui a des angles arrondis et utilise une couleur d’arrière-plan plus sombre.

Vous pouvez utiliser un thème unique pour une application entière, ou vous pouvez utiliser différents thèmes pour différents écrans (activités) dans une application. Dans les captures d’écran ci-dessus, par exemple, une application unique utilise un thème différent pour chaque activité afin d’illustrer les jeux de couleurs intégrés. Les cases d’option basculent l’application vers des activités différentes et, par conséquent, affichent des thèmes différents.

Étant donné que le thème matériel est pris en charge uniquement sur Android 5,0 et versions ultérieures, vous ne pouvez pas l’utiliser (ou un thème personnalisé dérivé du thème matériel) pour créer un thème pour votre application et l’exécuter sur des versions antérieures d’Android. Toutefois, vous pouvez configurer votre application pour utiliser le thème de matériau sur les appareils Android 5,0 et revenir en douceur à un thème précédent lorsqu’il s’exécute sur des versions antérieures d’Android (pour plus d’informations, consultez la section [compatibilité](#compatibility) de cet article).


## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour utiliser les nouvelles fonctionnalités de thème de matériel Android 5,0 dans les applications basées sur Xamarin:

-  **Xamarin. Android** &ndash; Xamarin. Android 4,20 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac. 

-  **Android SDK** &ndash; Android 5,0 (API 21) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

-  **JDK Java 1,8** &ndash; JDK 1,7 peut être utilisé si vous ciblez spécifiquement le niveau d’API 23 et les versions antérieures. JDK 1,8 est disponible à partir d' [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Pour savoir comment configurer un projet d’application Android 5,0, consultez [configuration d’un projet android 5,0](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>Utilisation des thèmes intégrés

Le moyen le plus simple d’utiliser un thème matériel consiste à configurer votre application pour qu’elle utilise un thème intégré sans personnalisation. Si vous ne souhaitez pas configurer explicitement un thème, votre application prend par défaut `Theme.Material` la valeur (le thème sombre). Si votre application n’a qu’une seule activité, vous pouvez configurer un thème au niveau de l’activité. Si votre application comporte plusieurs activités, vous pouvez configurer un thème au niveau de l’application afin qu’il utilise le même thème pour toutes les activités, ou vous pouvez assigner différents thèmes à différentes activités. Les sections suivantes expliquent comment configurer des thèmes au niveau de l’application et au niveau de l’activité.


### <a name="theming-an-application"></a>Thèmes d’une application

Pour configurer l’intégralité d’une application pour qu’elle utilise une version de `android:theme` thème de matériau, affectez l’un des éléments suivants à l’attribut du nœud d’application dans **fichier AndroidManifest. xml** :

-  `@android:style/Theme.Material`&ndash; Thème foncé.

-  `@android:style/Theme.Material.Light`&ndash; Thème clair.

-  `@android:style/Theme.Material.Light.DarkActionBar`&ndash; Thème clair avec barre d’action foncée.

L’exemple suivant configure l’application *MyApp* pour utiliser le thème clair:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Vous pouvez également définir l’attribut application `Theme` dans **AssemblyInfo.cs** (ou **Properties.cs**). Par exemple :

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Lorsque le thème de l’application a `@android:style/Theme.Material.Light`la valeur, chaque activité dans *MyApp* sera affichée `Theme.Material.Light`à l’aide de.


### <a name="theming-an-activity"></a>Thèmes d’une activité

Pour créer un thème pour une activité, `Theme` vous devez ajouter `[Activity]` un paramètre à l’attribut au- `Theme` dessus de votre déclaration d’activité et l’assigner à la version du thème matériel que vous souhaitez utiliser. L’exemple suivant illustre une activité avec `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

D’autres activités dans cette application utilisent le modèle `Theme.Material` de couleurs sombres par défaut (ou, s’il est configuré, le paramètre de thème d’application).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Utilisation de thèmes personnalisés

Vous pouvez améliorer votre personnalisation en créant un thème personnalisé qui style votre application avec les couleurs&rsquo;de votre image de personnalisation. Pour créer un thème personnalisé, vous définissez un nouveau style qui dérive d’une version de thème de matériau intégrée, en substituant les attributs de couleur que vous souhaitez modifier. Par exemple, vous pouvez définir un thème personnalisé qui dérive de `Theme.Material.Light.DarkActionBar` et modifie la couleur d’arrière-plan de l’écran en beige au lieu de blanc.

Le thème matériau expose les attributs de disposition suivants pour la personnalisation:

-  `colorPrimary`&ndash; Couleur de la barre de l’application.

-  `colorPrimaryDark`Couleur de la barre d’État et des barres d’application contextuelles; il s’agit normalement d’une version sombre de `colorPrimary`. &ndash;

-  `colorAccent`&ndash; Couleur des contrôles d’interface utilisateur tels que les cases à cocher, les cases d’option et les zones de texte de modification.

-  `windowBackground`&ndash; Couleur de l’arrière-plan de l’écran.

-  `textColorPrimary`&ndash; Couleur du texte de l’interface utilisateur dans la barre de l’application.

-  `statusBarColor`&ndash; Couleur de la barre d’État.

-  `navigationBarColor`&ndash; Couleur de la barre de navigation.

Ces zones d’écran sont étiquetées dans le diagramme suivant:

[![Diagramme d’attributs et de leurs zones d’écran associées](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

Par défaut, `statusBarColor` est défini sur la valeur de `colorPrimaryDark`. Vous pouvez définir `statusBarColor` une couleur unie ou la définir `@android:color/transparent` sur pour rendre la barre d’État transparente. La barre de navigation peut également être rendue transparente en `navigationBarColor` affectant à `@android:color/transparent`la valeur.


### <a name="creating-a-custom-app-theme"></a>Création d’un thème d’application personnalisé

Vous pouvez créer un thème d’application personnalisé en créant et en modifiant des fichiers dans le dossier **ressources** de votre projet d’application. Pour appliquer un style à votre application avec un thème personnalisé, procédez comme suit:

-   Créer un fichier Colors **. xml** dans les **ressources/valeurs** &mdash; vous utilisez ce fichier pour définir vos couleurs de thème personnalisées. Par exemple, vous pouvez coller le code suivant dans le **fichier Colors. xml** pour vous aider à démarrer:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Modifiez cet exemple de fichier pour définir les noms et les codes de couleur des ressources de couleur que vous allez utiliser dans votre thème personnalisé.

-   Créez un dossier Resources **/values-v21** . Dans ce dossier, créez un fichier **styles. xml** :

    [![Emplacement de styles. xml dans le dossier Resources/values-21. Xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Notez que **les ressources/valeurs-v21** sont spécifiques aux versions &ndash; antérieures d’Android 5,0, les fichiers de ce dossier ne seront pas lus.

-   Ajoutez un `resources` nœud à **styles. xml** et définissez un `style` nœud avec le nom de votre thème personnalisé. Par exemple, voici un fichier **styles. xml** qui définit *MyCustomTheme* (dérivé du style de `Theme.Material.Light` thème intégré):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   À ce stade, une application qui utilise *MyCustomTheme* affiche le thème stock `Theme.Material.Light` sans personnalisations:

    [![Apparence du thème personnalisé avant les personnalisations](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Ajoutez des personnalisations de couleur à **styles. xml** en définissant les couleurs des attributs de mise en page que vous souhaitez modifier. Par exemple, pour `my_blue` modifier la couleur de la barre de l’application et changer la couleur des contrôles de l’interface utilisateur en `my_purple`, ajoutez des remplacements de couleurs au **fichier styles. xml** qui font référence aux ressources de couleur configurées dans le fichier Colors **. xml**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Une fois ces modifications en place, une application qui utilise *MyCustomTheme* affiche une couleur de barre d' `my_blue` application dans les contrôles `my_purple`d’interface utilisateur et `Theme.Material.Light` dans, mais utilise le modèle de couleurs partout ailleurs:

[![Apparence du thème personnalisé après les personnalisations](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

Dans cet exemple, *MyCustomTheme* emprunte les couleurs `Theme.Material.Light` pour la couleur d’arrière-plan, la barre d’État et les couleurs de texte, mais modifie la couleur `my_blue` de la barre de l’application en et définit `my_purple`la couleur de la case d’option sur.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Création d’un style de vue personnalisé

Android 5,0 vous permet également de styliser une vue individuelle. Après avoir créé des **couleurs. xml** et **styles. xml** (comme décrit dans la section précédente), vous pouvez ajouter un style de vue à **styles. xml**.
Pour appliquer un style à une vue individuelle, procédez comme suit:

-   Modifiez **Resources/values-v21/styles. xml** et `style` ajoutez un nœud avec le nom de votre style d’affichage personnalisé. Définissez les attributs de couleur personnalisés de votre vue dans `style` ce nœud. Par exemple, pour créer un style [CardView](~/android/user-interface/controls/card-view.md) personnalisé qui a des angles arrondis et `my_blue` utilise comme couleur d’arrière-plan de `style` la carte, ajoutez un nœud à **styles. xml** (à l’intérieur du `resources` nœud) et configurez la couleur d’arrière-plan et rayon de l’angle:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   Dans votre disposition, définissez l' `style` attribut de cette vue pour qu’il corresponde au nom de style personnalisé que vous avez choisi à l’étape précédente. Par exemple :

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

La capture d’écran suivante fournit un exemple de `CardView` la valeur par défaut (illustrée à gauche) `CardView` par rapport à un qui a été appliqué `CardView.MyBlue` au thème personnalisé (indiqué à droite):

[![Exemples de CardView par défaut et de CardView personnalisé](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

Dans cet exemple, le personnalisé `CardView` s’affiche avec la couleur `my_blue` d’arrière-plan et un rayon de l’angle 18DP.


## <a name="compatibility"></a>Compatibilité

Pour appliquer un style à votre application afin qu’elle utilise le thème de matériau sur Android 5,0 mais repasse automatiquement à un style à compatibilité descendante sur les anciennes versions d’Android, procédez comme suit:

-   Définissez un thème personnalisé dans le **fichier Resources/values-v21/styles. xml** qui dérive d’un style de thème matériel. Par exemple :

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Définissez un thème personnalisé dans le **fichier Resources/values/styles. xml** qui dérive d’un thème plus ancien, mais qui utilise le même nom de thème que ci-dessus. Par exemple :

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Dans **fichier AndroidManifest. xml**, configurez votre application avec le nom du thème personnalisé. 
    Par exemple :

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   Vous pouvez également appliquer un style à une activité spécifique à l’aide de votre thème personnalisé:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Si votre thème utilise des couleurs définies dans un fichier **Colors. xml** , veillez à placer ce fichier dans les **ressources/valeurs** (plutôt que **ressources/valeurs-v21**) afin que les deux versions de votre thème personnalisé puissent accéder à vos définitions de couleur.

Quand votre application s’exécute sur un appareil Android 5,0, elle utilise la définition de thème spécifiée dans Resources **/values-v21/styles. xml**. Lorsque cette application s’exécute sur des appareils Android plus anciens, elle revient automatiquement à la définition de thème spécifiée dans Resources **/values/styles. xml**.

Pour plus d’informations sur la compatibilité des thèmes avec les anciennes versions d’Android, consultez [ressources secondaires](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Récapitulatif

Cet article a présenté le nouveau style d’interface utilisateur de thème de matériau inclus dans Android 5,0 (lollipop). Il a décrit les trois types de thème de matériau intégrés que vous pouvez utiliser pour appliquer un style à votre application, vous avez expliqué comment créer un thème personnalisé pour la personnalisation de votre application et vous avez fourni un exemple de création d’un thème pour une vue individuelle. Enfin, cet article a expliqué comment utiliser le thème de matériau dans votre application tout en conservant la compatibilité descendante avec les versions antérieures d’Android.



## <a name="related-links"></a>Liens associés

- [ThemeSwitcher (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-themeswitcher)
- [Présentation du symbole Lollipop](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Autres ressources](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Interface Lollipop Android](https://developer.android.com/about/versions/lollipop)
- [Développeur de secteurs Android](https://developer.android.com/about/versions/pie/)
- [Conception de matériau](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Principes de conception de matériau](https://material.io/design/)
- [Gestion de la compatibilité](https://developer.android.com/training/backward-compatible-ui/)
