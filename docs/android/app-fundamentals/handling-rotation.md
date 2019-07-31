---
title: Rotation de la gestion
description: Cette rubrique explique comment gérer les modifications d’orientation des appareils dans Xamarin. Android. Il explique comment utiliser le système de ressources Android pour charger automatiquement des ressources pour une orientation d’appareil particulière et comment gérer les modifications d’orientation par programmation.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: b1039dd433456731b775399ab42222fe0e4cc6d9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644370"
---
# <a name="handling-rotation"></a>Rotation de la gestion

_Cette rubrique explique comment gérer les modifications d’orientation des appareils dans Xamarin. Android. Il explique comment utiliser le système de ressources Android pour charger automatiquement des ressources pour une orientation d’appareil particulière et comment gérer les modifications d’orientation par programmation._


## <a name="overview"></a>Présentation

Étant donné que les appareils mobiles sont facilement pivotés, la rotation intégrée est une fonctionnalité standard des systèmes d’exploitation mobiles. Android fournit une infrastructure sophistiquée pour gérer la rotation au sein des applications, que l’interface utilisateur soit créée de façon déclarative en XML ou par programmation dans le code. Lors du traitement automatique des changements de disposition déclaratifs sur un appareil pivoté, une application peut tirer parti de l’intégration étroite au système de ressources Android. Pour la mise en page par programmation, les modifications doivent être gérées manuellement. Cela permet un contrôle plus précis lors de l’exécution, mais au détriment d’un travail supplémentaire pour le développeur. Une application peut également choisir de refuser le redémarrage de l’activité et d’effectuer un contrôle manuel des changements d’orientation.

Ce guide examine les rubriques d’orientation suivantes:

-   **Rotation de disposition déclarative** &ndash; Comment utiliser le système de ressources Android pour créer des applications orientées orientation, y compris le chargement des dispositions et des drawables pour des orientations particulières.

-   **Rotation de la disposition par programmation** &ndash; Comment ajouter des contrôles par programmation et comment gérer les modifications d’orientation manuellement.


## <a name="handling-rotation-declaratively-with-layouts"></a>Gestion de la rotation de manière déclarative avec les dispositions

En incluant des fichiers dans des dossiers qui suivent les conventions de nommage, Android charge automatiquement les fichiers appropriés lorsque l’orientation change.
Cela prend en charge les éléments suivants:

-   *Ressources de disposition* &ndash; Spécification des fichiers de disposition qui sont gonflés pour chaque orientation.

-   *Ressources dessinables* &ndash; Spécification des drawables chargés pour chaque orientation.


### <a name="layout-resources"></a>Ressources de disposition

Par défaut, les fichiers Android XML (AXML) inclus dans le dossier **ressources/mise en page** sont utilisés pour le rendu des vues d’une activité. Les ressources de ce dossier sont utilisées pour l’orientation portrait et paysage si aucune ressource de disposition supplémentaire n’est fournie spécifiquement pour le paysage. Considérez la structure de projet créée par le modèle de projet par défaut:

[![Structure de modèle de projet par défaut](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Ce projet crée un seul fichier **main. AXML** dans le dossier **ressources/mise en page** . Quand la méthode de `OnCreate` l’activité est appelée, elle augmente la vue définie dans **main. AXML,** qui déclare un bouton comme indiqué dans le code XML ci-dessous:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

Si l’appareil est pivoté en orientation paysage, la méthode de `OnCreate` l’activité est à nouveau appelée et le même fichier **main. AXML** est augmenté, comme illustré dans la capture d’écran ci-dessous:

[![Même écran mais en orientation paysage](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Dispositions spécifiques à l’orientation

En plus du dossier de disposition (qui est défini par défaut sur portrait et peut également être explicitement nommé *Layout-port* en incluant un `layout-land`dossier nommé), une application peut définir les vues dont elle a besoin en mode paysage sans modification du code.

Supposons que le fichier **main. AXML** contenait le code XML suivant:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Si un dossier nommé Layout-Land qui contient un fichier **main. AXML** supplémentaire est ajouté au projet, le fait de le déflater en mode paysage entraîne le chargement par Android du fichier **main. AXML** qui vient d’être ajouté. Prenons la version paysage du fichier **main. AXML** qui contient le code suivant (par souci de simplicité, ce code XML est similaire à la version Portrait par défaut du code, mais utilise une chaîne différente `TextView`dans le):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

L’exécution de ce code et la rotation de l’appareil du portrait au paysage illustrent le nouveau chargement XML, comme indiqué ci-dessous:

[![Captures d’écran portrait et paysage pour l’impression en mode portrait](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Ressources dessinables

Lors de la rotation, Android traite les ressources dessinables de la même façon que les ressources de disposition. Dans ce cas, le système obtient le drawables à partir des dossiers Resources **/drawic** and Resources **/Drawing-Land** , respectivement.

Par exemple, imaginons que le projet comprenne une image nommée singe. png dans le dossier **ressources/dessinable** , où le dessinable est référencé à `ImageView` partir d’un fichier XML comme suit:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Supposons à présent qu’une version différente de **singe. png** soit incluse sous **ressources/dessinables-terrains**. À l’instar des fichiers de disposition, lorsque l’appareil est pivoté, le dessinable change pour l’orientation donnée, comme indiqué ci-dessous:

[![Version différente de singe. png affichée en mode portrait et paysage](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Gestion de la rotation par programmation

Parfois, nous définissons des dispositions dans le code. Cela peut se produire pour diverses raisons, notamment des limitations techniques, des préférences de développement, etc. Lorsque nous ajoutons des contrôles par programme, une application doit manuellement tenir compte de l’orientation de l’appareil, qui est gérée automatiquement quand nous utilisons des ressources XML.


### <a name="adding-controls-in-code"></a>Ajout de contrôles dans le code

Pour ajouter des contrôles par programme, une application doit effectuer les étapes suivantes:

-  Créer une disposition.
-  Définissez les paramètres de disposition.
-  Créer des contrôles.
-  Définissez les paramètres de disposition du contrôle.
-  Ajoutez des contrôles à la disposition.
-  Définissez la disposition comme affichage du contenu.

Par exemple, considérez une interface utilisateur composée d’un `TextView` seul contrôle ajouté à `RelativeLayout`un, comme illustré dans le code suivant.

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Ce code crée une instance d’une `RelativeLayout` classe et définit sa `LayoutParameters` propriété. La `LayoutParams` classe permet à Android d’encapsuler la manière dont les contrôles sont positionnés de manière réutilisable. Une fois qu’une instance d’une disposition est créée, des contrôles peuvent être créés et ajoutés à celle-ci. `LayoutParameters` Les`TextView` contrôles ont également, comme dans cet exemple. Une fois `TextView` le créé, en l’ajoutant `RelativeLayout` au et `RelativeLayout` en définissant comme affichage du contenu, l’application affiche le `TextView` comme illustré ci-dessous:

[![Bouton incrémenter le compteur affiché en mode portrait et paysage](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Détection de l’orientation dans le code

Si une application tente de charger une interface utilisateur différente pour chaque orientation lorsque `OnCreate` est appelé (cela se produit chaque fois qu’un appareil est pivoté), il doit détecter l’orientation, puis charger le code d’interface utilisateur souhaité. Android a une classe appelée `WindowManager`, qui peut être utilisée pour déterminer la rotation de l’appareil en cours via la `WindowManager.DefaultDisplay.Rotation` propriété, comme indiqué ci-dessous:

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Ce code définit la `TextView` valeur à positionner 100 pixels à partir du coin supérieur gauche de l’écran, en animant automatiquement la nouvelle disposition, en cas de rotation en mode paysage, comme illustré ici:

[![L’état d’affichage est conservé dans les modes portrait et paysage](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Prévention du redémarrage de l’activité

En plus de gérer tout dans `OnCreate`, une application peut également empêcher le redémarrage d’une activité lorsque l’orientation change en définissant `ConfigurationChanges` dans le `ActivityAttribute` comme suit:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Désormais, lorsque l’appareil pivote, l’activité n’est pas redémarrée. Pour gérer manuellement le changement d’orientation dans ce cas, une activité peut substituer la `OnConfigurationChanged` méthode et déterminer l’orientation de l' `Configuration` objet qui est passé, comme dans la nouvelle implémentation de l’activité ci-dessous:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

Ici, `TextView's` les paramètres de disposition sont initialisés pour le mode paysage et le mode portrait. Les variables de classe contiennent les paramètres, ainsi `TextView` que le lui-même, car l’activité ne sera pas recréée lorsque l’orientation change. Le code utilise `surfaceOrientartion` toujours dans `OnCreate` pour définir la disposition initiale pour le `TextView`. Après cela, `OnConfigurationChanged` gère toutes les modifications de disposition suivantes.

Lorsque nous exécutons l’application, Android charge les modifications de l’interface utilisateur lors de la rotation de l’appareil et ne redémarre pas l’activité.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Prévention du redémarrage de l’activité pour les dispositions déclaratives

Les redémarrages d’activité provoqués par la rotation de l’appareil peuvent également être évités si nous définissons la disposition dans XML. Par exemple, nous pouvons utiliser cette approche si nous voulons empêcher le redémarrage d’une activité (pour des raisons de performances, par exemple) et que nous n’avons pas besoin de charger de nouvelles ressources pour différentes orientations.

Pour ce faire, nous suivons la même procédure que celle utilisée avec une disposition de programmation. Il suffit `ConfigurationChanges` de définir `ActivityAttribute`dans le, comme nous l' `CodeLayoutActivity` avons fait précédemment. Tout code qui doit s’exécuter pour le changement d’orientation peut être à nouveau implémenté dans `OnConfigurationChanged` la méthode.


## <a name="maintaining-state-during-orientation-changes"></a>Conservation de l’État pendant les changements d’orientation

Qu’il s’agisse de gérer la rotation de manière déclarative ou par programme, toutes les applications Android doivent implémenter les mêmes techniques pour la gestion de l’État lorsque l’orientation de l’appareil change. La gestion de l’État est importante car le système redémarre une activité en cours d’exécution lors de la rotation d’un appareil Android. Android effectue cette opération pour faciliter le chargement de ressources de remplacement, telles que les mises en page et les drawables conçues spécifiquement pour une orientation particulière. Lorsqu’il redémarre, l’activité perd tout état temporaire qu’elle peut avoir stocké dans des variables de classe locales. Par conséquent, si une activité est tributaire de l’État, elle doit rendre son état persistant au niveau de l’application. Une application doit gérer l’enregistrement et la restauration de l’état de l’application qu’elle souhaite conserver au fil des changements d’orientation.

Pour plus d’informations sur l’état de persistance dans Android, reportez-vous au guide [du cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md) .


## <a name="summary"></a>Récapitulatif

Cet article a décrit comment utiliser les fonctionnalités intégrées d’Android pour travailler avec la rotation. Tout d’abord, il a expliqué comment utiliser le système de ressources Android pour créer des applications prenant en charge l’orientation. Ensuite, il a présenté comment ajouter des contrôles dans le code et comment gérer les modifications d’orientation manuellement.



## <a name="related-links"></a>Liens associés

- [Démonstration de rotation (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-rotationdemo)
- [Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Gestion des modifications du Runtime](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Changement d’orientation rapide de l’écran](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
