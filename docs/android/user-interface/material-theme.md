---
title: Thème matériau
description: Comment thème votre application Xamarin. Android avec un thème de matériau
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 5e83e34dab407c5be84fc5b4c3c0c445d56907e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028761"
---
# <a name="material-theme"></a>Thème matériau

Le *thème matériau* est un style d’interface utilisateur qui détermine l’apparence et la convivialité des vues et des activités à partir d’Android 5,0 (lollipop). Le thème du matériau est intégré à Android 5,0, de sorte qu’il est utilisé par l’interface utilisateur du système, ainsi que par les applications. Le thème matériel n’est pas un « thème » dans le sens d’une option d’apparence à l’ensemble du système qu’un utilisateur peut choisir dynamiquement dans un menu de paramètres. Au lieu de cela, le thème matériel peut être considéré comme un ensemble de styles de base intégrés associés que vous pouvez utiliser pour personnaliser l’apparence de votre application.

Android offre trois types de thèmes matériels :

- `Theme.Material` &ndash; version sombre du thème du matériau ; Il s’agit de la version par défaut d’Android 5,0.

- `Theme.Material.Light` &ndash; version légère du thème de matériau.

- `Theme.Material.Light.DarkActionBar` &ndash; version légère du thème de matériau, mais avec une barre d’action foncée.

Des exemples de ces types de thèmes de matériau s’affichent ici :

[![des exemples de captures d’écran du thème sombre, du thème clair et du thème Barre d’action foncé](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Vous pouvez dériver à partir d’un thème de matériau pour créer votre propre thème, en remplaçant certains ou tous les attributs de couleur. Par exemple, vous pouvez créer un thème qui dérive de `Theme.Material.Light`, mais remplace la couleur de la barre de l’application pour qu’elle corresponde à la couleur de votre marquage. Vous pouvez également styliser des vues individuelles. par exemple, vous pouvez créer un style pour [CardView](~/android/user-interface/controls/card-view.md) qui a des angles arrondis et utilise une couleur d’arrière-plan plus sombre.

Vous pouvez utiliser un thème unique pour une application entière, ou vous pouvez utiliser différents thèmes pour différents écrans (activités) dans une application. Dans les captures d’écran ci-dessus, par exemple, une application unique utilise un thème différent pour chaque activité afin d’illustrer les jeux de couleurs intégrés. Les cases d’option basculent l’application vers des activités différentes et, par conséquent, affichent des thèmes différents.

Étant donné que le thème matériel est pris en charge uniquement sur Android 5,0 et versions ultérieures, vous ne pouvez pas l’utiliser (ou un thème personnalisé dérivé du thème matériel) pour créer un thème pour votre application et l’exécuter sur des versions antérieures d’Android. Toutefois, vous pouvez configurer votre application pour utiliser le thème de matériau sur les appareils Android 5,0 et revenir en douceur à un thème précédent lorsqu’il s’exécute sur des versions antérieures d’Android (pour plus d’informations, consultez la section [compatibilité](#compatibility) de cet article).

## <a name="requirements"></a>spécifications

Les éléments suivants sont requis pour utiliser les nouvelles fonctionnalités de thème de matériel Android 5,0 dans les applications basées sur Xamarin :

- **Xamarin. android** &ndash; Xamarin. Android 4,20 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac. 

- **Android SDK** &ndash; Android 5,0 (API 21) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

- **Java jdk 1,8** &ndash; JDK 1,7 peut être utilisé si vous ciblez spécifiquement le niveau d’API 23 et les versions antérieures. JDK 1,8 est disponible à partir d' [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Pour savoir comment configurer un projet d’application Android 5,0, consultez [configuration d’un projet android 5,0](~/android/platform/lollipop.md).

## <a name="using-the-built-in-themes"></a>Utilisation des thèmes intégrés

Le moyen le plus simple d’utiliser un thème matériel consiste à configurer votre application pour qu’elle utilise un thème intégré sans personnalisation. Si vous ne souhaitez pas configurer explicitement un thème, votre application prend par défaut la valeur `Theme.Material` (thème sombre). Si votre application n’a qu’une seule activité, vous pouvez configurer un thème au niveau de l’activité. Si votre application comporte plusieurs activités, vous pouvez configurer un thème au niveau de l’application afin qu’il utilise le même thème pour toutes les activités, ou vous pouvez assigner différents thèmes à différentes activités. Les sections suivantes expliquent comment configurer des thèmes au niveau de l’application et au niveau de l’activité.

### <a name="theming-an-application"></a>Thèmes d’une application

Pour configurer l’intégralité d’une application pour qu’elle utilise une version de thème de matériau, définissez l’attribut `android:theme` du nœud d’application dans **fichier AndroidManifest. xml** sur l’un des éléments suivants :

- `@android:style/Theme.Material` &ndash; thème sombre.

- `@android:style/Theme.Material.Light` &ndash; thème clair.

- `@android:style/Theme.Material.Light.DarkActionBar` &ndash; thème clair avec la barre d’action foncée.

L’exemple suivant configure l’application *MyApp* pour utiliser le thème clair :

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Vous pouvez également définir l’attribut `Theme` de l’application dans **AssemblyInfo.cs** (ou **Properties.cs**). Exemple :

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Lorsque le thème de l’application est défini sur `@android:style/Theme.Material.Light`, chaque activité dans *MyApp* sera affichée à l’aide de `Theme.Material.Light`.

### <a name="theming-an-activity"></a>Thèmes d’une activité

Pour créer un thème pour une activité, vous ajoutez un paramètre de `Theme` à l’attribut `[Activity]` au-dessus de votre déclaration d’activité et vous affectez `Theme` à la version du thème de matériau que vous souhaitez utiliser. L’exemple suivant présente une activité avec `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

D’autres activités dans cette application utilisent le modèle de couleurs par défaut `Theme.Material` foncé (ou, s’ils sont configurés, le paramètre de thème d’application).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Utilisation de thèmes personnalisés

Vous pouvez améliorer votre personnalisation en créant un thème personnalisé qui style votre application avec vos couleurs de&rsquo;s. Pour créer un thème personnalisé, vous définissez un nouveau style qui dérive d’une version de thème de matériau intégrée, en substituant les attributs de couleur que vous souhaitez modifier. Par exemple, vous pouvez définir un thème personnalisé qui dérive de `Theme.Material.Light.DarkActionBar` et modifie la couleur d’arrière-plan de l’écran en beige au lieu de blanc.

Le thème matériau expose les attributs de disposition suivants pour la personnalisation :

- `colorPrimary` &ndash; la couleur de la barre de l’application.

- `colorPrimaryDark` &ndash; la couleur de la barre d’État et des barres d’application contextuelles. Il s’agit normalement d’une version sombre de `colorPrimary`.

- `colorAccent` &ndash; la couleur des contrôles d’interface utilisateur tels que les cases à cocher, les cases d’option et les zones de texte de modification.

- `windowBackground` &ndash; la couleur de l’arrière-plan de l’écran.

- `textColorPrimary` &ndash; la couleur du texte de l’interface utilisateur dans la barre de l’application.

- `statusBarColor` &ndash; la couleur de la barre d’État.

- `navigationBarColor` &ndash; la couleur de la barre de navigation.

Ces zones d’écran sont étiquetées dans le diagramme suivant :

[![diagramme d’attributs et de leurs zones d’écran associées](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

Par défaut, `statusBarColor` est défini sur la valeur de `colorPrimaryDark`. Vous pouvez définir `statusBarColor` sur une couleur unie, ou vous pouvez lui affecter la valeur `@android:color/transparent` pour rendre la barre d’État transparente. La barre de navigation peut également être rendue transparente en définissant `navigationBarColor` sur `@android:color/transparent`.

### <a name="creating-a-custom-app-theme"></a>Création d’un thème d’application personnalisé

Vous pouvez créer un thème d’application personnalisé en créant et en modifiant des fichiers dans le dossier **ressources** de votre projet d’application. Pour appliquer un style à votre application avec un thème personnalisé, procédez comme suit :

- Créez un fichier **Colors. xml** dans **ressources/valeurs** &mdash; vous utilisez ce fichier pour définir vos couleurs de thème personnalisées. Par exemple, vous pouvez coller le code suivant dans le **fichier Colors. xml** pour vous aider à démarrer :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

- Modifiez cet exemple de fichier pour définir les noms et les codes de couleur des ressources de couleur que vous allez utiliser dans votre thème personnalisé.

- Créez un dossier **Resources/values-v21** . Dans ce dossier, créez un fichier **styles. xml** :

    [![emplacement de styles. xml dans le dossier Resources/values-21. Xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Notez que **ressources/values-v21** est spécifique à android 5,0 &ndash; les versions antérieures d’Android ne lisent pas les fichiers de ce dossier.

- Ajoutez un nœud de `resources` à **styles. xml** et définissez un nœud de `style` avec le nom de votre thème personnalisé. Par exemple, voici un fichier **styles. xml** qui définit *MyCustomTheme* (dérivé du style de thème `Theme.Material.Light` intégré) :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

- À ce stade, une application qui utilise *MyCustomTheme* affiche le thème stock `Theme.Material.Light` sans personnalisations :

    [![l’apparence du thème personnalisé avant les personnalisations](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

- Ajoutez des personnalisations de couleur à **styles. xml** en définissant les couleurs des attributs de mise en page que vous souhaitez modifier. Par exemple, pour modifier la couleur de la barre de l’application en `my_blue` et modifier la couleur des contrôles d’interface utilisateur en `my_purple`, ajoutez des remplacements de couleurs au **fichier styles. xml** qui font référence aux ressources de couleur configurées dans le **fichier Colors. xml**:

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

Une fois ces modifications apportées, une application qui utilise *MyCustomTheme* affiche une couleur de barre d’application dans `my_blue` et les contrôles d’interface utilisateur dans `my_purple`, mais utilise le modèle de couleurs `Theme.Material.Light`s partout ailleurs :

[![l’apparence d’un thème personnalisé après les personnalisations](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

Dans cet exemple, *MyCustomTheme* emprunte les couleurs de `Theme.Material.Light` pour la couleur d’arrière-plan, la barre d’État et les couleurs de texte, mais modifie la couleur de la barre de l’application pour `my_blue` et définit la couleur de la case d’option sur `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Création d’un style de vue personnalisé

Android 5,0 vous permet également de styliser une vue individuelle. Après avoir créé des **couleurs. xml** et **styles. xml** (comme décrit dans la section précédente), vous pouvez ajouter un style de vue à **styles. xml**.
Pour appliquer un style à une vue individuelle, procédez comme suit :

- Modifiez **Resources/values-v21/styles. xml** et ajoutez un nœud `style` avec le nom de votre style d’affichage personnalisé. Définissez les attributs de couleur personnalisés de votre affichage dans ce nœud de `style`. Par exemple, pour créer un style [CardView](~/android/user-interface/controls/card-view.md) personnalisé qui a plus d’angles arrondis et qui utilise `my_blue` comme couleur d’arrière-plan de la carte, ajoutez un nœud `style` à **styles. xml** (à l’intérieur du nœud `resources`) et configurez la couleur d’arrière-plan et le rayon de l’angle :

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

- Dans votre disposition, définissez l’attribut `style` pour cette vue afin qu’il corresponde au nom de style personnalisé que vous avez choisi à l’étape précédente. Exemple :

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

La capture d’écran suivante fournit un exemple de la `CardView` par défaut (illustrée à gauche) par rapport à un `CardView` qui a été appliqué au thème de `CardView.MyBlue` personnalisé (indiqué à droite) :

[![des exemples de CardView par défaut et de CardView personnalisé](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

Dans cet exemple, la `CardView` personnalisée s’affiche avec la couleur d’arrière-plan `my_blue` et un rayon d’angle 18DP.

## <a name="compatibility"></a>Compatibilité

Pour appliquer un style à votre application afin qu’elle utilise le thème de matériau sur Android 5,0 mais repasse automatiquement à un style à compatibilité descendante sur les anciennes versions d’Android, procédez comme suit :

- Définissez un thème personnalisé dans le **fichier Resources/values-v21/styles. xml** qui dérive d’un style de thème matériel. Exemple :

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- Définissez un thème personnalisé dans le **fichier Resources/values/styles. xml** qui dérive d’un thème plus ancien, mais qui utilise le même nom de thème que ci-dessus. Exemple :

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- Dans **fichier AndroidManifest. xml**, configurez votre application avec le nom du thème personnalisé. 
    Exemple :

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

- Vous pouvez également appliquer un style à une activité spécifique à l’aide de votre thème personnalisé :

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Si votre thème utilise des couleurs définies dans un fichier **Colors. xml** , veillez à placer ce fichier dans les **ressources/valeurs** (plutôt que **ressources/valeurs-v21**) afin que les deux versions de votre thème personnalisé puissent accéder à vos définitions de couleur.

Quand votre application s’exécute sur un appareil Android 5,0, elle utilise la définition de thème spécifiée dans **Resources/values-v21/styles. xml**. Lorsque cette application s’exécute sur des appareils Android plus anciens, elle revient automatiquement à la définition de thème spécifiée dans **Resources/values/styles. xml**.

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
