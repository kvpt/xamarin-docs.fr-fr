---
title: Caractéristiques KitKat
description: Android 4.4 (KitKat) est livré avec une corne d’abondance de fonctionnalités pour les utilisateurs et les développeurs. Ce guide met en évidence plusieurs de ces fonctionnalités et fournit des exemples de code et des détails d’implémentation pour vous aider à tirer le meilleur parti de KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 43061272f3d3486926f38af792ee3b9df0c53670
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027239"
---
# <a name="kitkat-features"></a>Caractéristiques KitKat

_Android 4.4 (KitKat) est livré avec une corne d’abondance de fonctionnalités pour les utilisateurs et les développeurs. Ce guide met en évidence plusieurs de ces fonctionnalités et fournit des exemples de code et des détails d’implémentation pour vous aider à tirer le meilleur parti de KitKat._

## <a name="overview"></a>Vue d’ensemble

Android 4.4 (API Level 19), également connu sous le nom "KitKat", a été publié à la fin de 2013. KitKat offre une variété de nouvelles fonctionnalités et améliorations, y compris:

- [Expérience utilisateur](#user_experience) &ndash; Les animations faciles avec cadre de transition, l’état translucide et les barres de navigation, et le mode immersif plein écran aident à créer une meilleure expérience pour l’utilisateur.

- Gestion des fichiers utilisateur [simplifiée](#user_content) &ndash; grâce au cadre d’accès au stockage; l’impression d’images, de sites Web et d’autres contenus est plus facile avec l’amélioration des API d’impression.

- [Matériel](#hardware) &ndash; Transformez n’importe quelle application en une carte NFC avec NFC Host-Based Card Emulation; exécuter des capteurs de `SensorManager` faible puissance avec le .

- [Developer Tools Screencast](#developer_tools) &ndash; applications en action avec le client Android Debug Bridge, disponible dans le cadre de l’Android SDK.

Ce guide fournit des conseils pour la migration d’une application Xamarin.Android existante à KitKat, ainsi qu’un aperçu de haut niveau de KitKat pour les développeurs Xamarin.Android.

## <a name="requirements"></a>Spécifications

Pour développer des applications Xamarin.Android à l’aide de KitKat, vous avez besoin *de Xamarin.Android 4.11.0* ou plus et Android 4.4 (niveau API 19) installé via le gestionnaire Android SDK, comme l’illustre la capture d’écran suivante:

[![Sélection d’Android 4.4 dans le gestionnaire Android SDK](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrer votre application à KitKat

Cette section fournit quelques éléments de première réponse pour aider à la transition des applications existantes vers Android 4.4.

### <a name="check-system-version"></a>Vérifier la version système

Si une application doit être compatible avec les anciennes versions d’Android, assurez-vous d’envelopper n’importe quel code spécifique à KitKat dans une version système de contrôle, comme l’illustre l’échantillon de code ci-dessous :

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Batching d’alarme

Android utilise les services d’alarme pour réveiller une application en arrière-plan à un moment précis. KitKat va plus loin en par lots d’alarmes pour préserver la puissance. Cela signifie qu’au lieu de réveiller chaque application à un moment précis, KitKat préfère regrouper plusieurs applications qui sont enregistrées pour se réveiller au cours du même intervalle de temps, et les réveiller en même temps.
Pour dire à Android de réveiller une `SetWindow` application [`AlarmManager`](xref:Android.App.AlarmManager)pendant un intervalle de temps spécifié, faites appel au , en passant dans le temps minimum et maximum, en millisecondes, qui peut s’écouler avant que l’application est réveillée, et l’opération pour effectuer au réveil.
Le code suivant fournit un exemple d’une application qui doit être réveillée entre une demi-heure et une heure à partir du moment où la fenêtre est définie :

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Pour continuer à réveiller une application `SetExact`à un moment précis, utilisez, en passant dans le temps exact que l’application doit être réveillée, et l’opération pour effectuer:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat ne vous permet plus de régler une alarme de répétition exacte. Applications qui utilisent[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
et nécessitent des alarmes exactes pour fonctionner devra maintenant déclencher chaque alarme manuellement.

### <a name="external-storage"></a>Stockage externe

Le stockage externe est maintenant divisé en deux types - stockage unique à votre application, et les données partagées par plusieurs applications. La lecture et l’écriture à l’emplacement spécifique de votre application sur le stockage externe ne nécessite aucune autorisation spéciale. Interagir avec les données sur `READ_EXTERNAL_STORAGE` le `WRITE_EXTERNAL_STORAGE` stockage partagé nécessite maintenant l’autorisation ou l’autorisation. Les deux types peuvent être classés comme tels :

- Si vous obtenez un fichier ou un parcours `Context` d’annuaire en appelant une méthode - par exemple,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  Ou[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
  - votre application ne nécessite aucune autorisation supplémentaire.

- Si vous obtenez un fichier ou un parcours d’annuaire en `Environment` accédant à une propriété ou en appelant une méthode,[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  Ou[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
  , votre application `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` nécessite ou la permission.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE`implique la `READ_EXTERNAL_STORAGE` permission, de sorte que vous ne devriez jamais avoir besoin de définir une seule autorisation.

### <a name="sms-consolidation"></a>SMS Consolidation

KitKat simplifie la messagerie pour l’utilisateur en agrégeant tout le contenu SMS dans une application par défaut sélectionnée par l’utilisateur. Le développeur est responsable de rendre l’application sélectionnable comme application de messagerie par défaut, et de se comporter de manière appropriée dans le code et dans la vie si l’application n’est pas sélectionnée. Pour plus d’informations sur la transition de votre application SMS vers KitKat, consultez le guide [Getting Your SMS Apps Ready for KitKat](https://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) de Google.

### <a name="webview-apps"></a>Applications WebView

[WebView](xref:Android.Webkit.WebView) a obtenu une cure de jouvence à KitKat. Le plus grand changement est la `WebView`sécurité supplémentaire pour le chargement du contenu dans un . Bien que la plupart des applications ciblant les anciennes versions API devraient fonctionner comme prévu, il est fortement recommandé de tester les applications qui utilisent la `WebView` classe. Pour plus d’informations sur les API WebView concernés se référer à la migration Android [à WebView dans Android 4.4](https://developer.android.com/guide/webapps/migrating.html) documentation.

<a name="user_experience" />

## <a name="user-experience"></a>Expérience utilisateur

KitKat est livré avec plusieurs nouvelles API pour améliorer l’expérience utilisateur, y compris le nouveau cadre de transition pour la manipulation des animations immobilières et une option d’interface utilisateur translucide pour le thème. Ces modifications sont couvertes ci-dessous.

### <a name="transition-framework"></a>Cadre de transition

Le cadre de transition facilite la mise en œuvre des animations. KitKat vous permet d’effectuer une animation de propriété simple avec une seule ligne de code, ou personnaliser les transitions en utilisant *Scenes*.

#### <a name="simple-property-animation"></a>Animation de propriété simple

La nouvelle bibliothèque Android Transitions simplifie le code derrière les animations immobilières. Le cadre vous permet d’effectuer des animations simples avec un code minimal. Par exemple, l’échantillon de code suivant utilise[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
pour animer montrant et `TextView`cacher un :

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

L’exemple ci-dessus utilise le cadre de transition pour créer une transition automatique par défaut entre les valeurs changeantes de propriété. Parce que l’animation est gérée par une seule ligne de code, vous pouvez `BeginDelayedTransition` facilement rendre cela compatible avec les anciennes versions d’Android en enveloppant l’appel dans une version système de contrôle. Consultez la section [Migrating Your App To KitKat](#Migrating_Your_App_to_KitKat) pour en savoir plus.

La capture d’écran ci-dessous montre l’application avant l’animation:

[![Capture d’écran de l’application avant le début de l’animation](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La capture d’écran ci-dessous montre l’application après l’animation:

[![Capture d’écran de l’application après la fin de l’animation](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Vous pouvez obtenir plus de contrôle sur la transition avec Scènes, qui sont couverts dans la section suivante.

#### <a name="android-scenes"></a>Scènes Android

[Des scènes](xref:Android.Transitions.Scene) ont été introduites dans le cadre de transition pour donner au développeur plus de contrôle sur les animations. Les scènes créent une zone dynamique dans l’interface utilisateur : vous spécifiez un conteneur et plusieurs versions, ou « scènes », pour le contenu XML à l’intérieur du conteneur, et Android fait le reste du travail pour animer les transitions entre les scènes. Android Scenes vous permet de construire des animations complexes avec un minimum de travail sur le côté développement.

L’élément d’interface utilisateur statique abritant le contenu dynamique est appelé un *conteneur* ou une *base de scène*. L’exemple ci-dessous utilise `RelativeLayout` l’Android Designer pour créer un appelé `container`:

[![Utilisation de l’Android Designer pour créer un conteneur RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

La disposition de l’échantillon `sceneButton` définit `container`également un bouton appelé ci-dessous le . Ce bouton déclenchera la transition.

Le contenu dynamique à l’intérieur du conteneur nécessite deux nouvelles mises en page Android. Ces mises en page ne spécifient que le code *à l’intérieur* du conteneur.
Le code d’exemple ci-dessous définit une mise en page appelée *Scene1* qui contient deux champs de texte lisant "Kit" et "Kat" respectivement, et une deuxième mise en page appelée *Scene2* qui contient les mêmes champs de texte inversés. Le XML est le suivant:

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

L’exemple `merge` ci-dessus sert à raccourcir le code de vue et à simplifier la hiérarchie de vue. Vous pouvez en `merge` savoir plus sur les mises en page [ici](https://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Une scène est [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*)créée en appelant, en passant dans l’objet de conteneur, `Context`l’ID de ressources du fichier de mise en page de la Scène, et le courant , comme illustré par l’exemple de code ci-dessous:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

En cliquant sur le bouton bascule entre les deux Scènes, qu’Android anime avec les valeurs de transition par défaut:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

La capture d’écran ci-dessous illustre la scène avant l’animation:

[![Capture d’écran de l’application avant le début de l’animation](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

La capture d’écran ci-dessous illustre la scène après l’animation:

[![Capture d’écran de l’application après la fin de l’animation](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> Il ya un [bug connu](https://code.google.com/p/android/issues/detail?id=62450) dans la bibliothèque `GetSceneForLayout` Android Transitions qui provoque Scènes créées à l’aide de briser quand un utilisateur navigue à travers une activité la deuxième fois. Une solution de contournement java est décrite [ici](http://www.doubleencore.com/2013/11/new-transitions-framework/).

##### <a name="custom-transitions-in-scenes"></a>Transitions personnalisées en scènes

Une transition personnalisée peut être définie dans `transition` un fichier `Resources`de ressources xml dans l’annuaire sous , comme illustré par la capture d’écran ci-dessous:

[![Emplacement du fichier transition.xml sous l’annuaire Ressources/transition](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

L’échantillon de code suivant définit une transition qui anime pendant 5 secondes et utilise [l’interpolateur de dépassement](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transition est créée dans l’activité à l’aide de [l’inflammation Transition,](xref:Android.Transitions.TransitionInflater)comme l’illustre le code ci-dessous :

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nouvelle transition est `Go` ensuite ajoutée à l’appel qui commence l’animation :

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interface utilisateur translucide

KitKat vous donne plus de contrôle sur le thème de votre application avec un statut translucide optionnel et des barres de navigation. Vous pouvez modifier la translucidité des éléments d’interface utilisateur système dans le même fichier XML que vous utilisez pour définir votre thème Android. KitKat présente les propriétés suivantes :

- `windowTranslucentStatus`- Lorsqu’il est réglé pour vrai, rend la barre de statut supérieur translucide.

- `windowTranslucentNavigation`- Lorsqu’il est réglé pour vrai, rend la barre de navigation inférieure translucide.

- `fitsSystemWindows`- Définir la barre supérieure ou inférieure pour transcluent déplace le contenu sous les éléments transparents de l’interface utilisateur par défaut. La définition `true` de cette propriété est un moyen simple d’empêcher le contenu de se chevaucher avec les éléments d’interface utilisateur du système translucide.

Le code suivant définit un thème avec le statut translucide et les barres de navigation :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

La capture d’écran ci-dessous montre le thème ci-dessus avec le statut translucide et les barres de navigation:

[![Exemple de capture d’écran de l’application avec statut translucide et barres de navigation](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenu utilisateur

### <a name="storage-access-framework"></a>Cadre de stockage-accès

Le cadre d’accès au stockage (SAF) est une nouvelle façon pour les utilisateurs d’interagir avec du contenu stocké tels que des images, des vidéos et des documents. Au lieu de présenter aux utilisateurs un dialogue pour choisir une application pour gérer le contenu, KitKat ouvre une nouvelle interface utilisateur qui permet aux utilisateurs d’accéder à leurs données dans un emplacement agrégé. Une fois que le contenu a été choisi, l’utilisateur retournera à l’application qui a demandé le contenu, et l’expérience de l’application se poursuivra normalement.

Cette modification nécessite deux actions du côté des développeurs : premièrement, les applications qui nécessitent du contenu des fournisseurs doivent être mises à jour pour une nouvelle façon de demander du contenu. Deuxièmement, les applications `ContentProvider` qui rédigent des données doivent être modifiées pour utiliser le nouveau cadre. Les deux scénarios dépendent de la nouvelle[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
.

#### <a name="documentsprovider"></a>DocumentsProvider

Dans KitKat, `ContentProviders` les interactions avec `DocumentsProvider` sont abstraites avec la classe. Cela signifie que SAF ne se soucie pas où les données `DocumentsProvider` sont physiquement, tant qu’elles sont accessibles par l’API. Les fournisseurs locaux, les services cloud et les périphériques de stockage externes utilisent tous la même interface, et sont traités de la même manière, fournissant à l’utilisateur et au développeur un seul endroit pour interagir avec le contenu de l’utilisateur.

Cette section couvre la façon de charger et d’enregistrer le contenu avec le cadre d’accès au stockage.

#### <a name="request-content-from-a-provider"></a>Demander du contenu auprès d’un fournisseur

Nous pouvons dire KitKat que nous voulons choisir du `ActionOpenDocument` contenu en utilisant l’interface utilisateur SAF avec l’intention, ce qui signifie que nous voulons nous connecter à tous les fournisseurs de contenu disponibles à l’appareil. Vous pouvez ajouter un peu de `CategoryOpenable`filtrage à cette intention en spécifiant , ce qui signifie que seul le contenu qui peut être ouvert (c’est-à-dire accessible, contenu utilisable) sera retourné. KitKat permet également le filtrage du contenu avec le `MimeType`. Par exemple, le code ci-dessous filtre les `MimeType`résultats de l’image en spécifiant l’image :

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

L’appel `StartActivityForResult` lance l’interface utilisateur SAF, que l’utilisateur peut ensuite parcourir pour choisir une image :

[![Exemple de capture d’écran d’une application utilisant le cadre d’accès au stockage pour naviguer vers une image](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Une fois que l’utilisateur a choisi une image, `OnActivityResult` renvoie le `Android.Net.Uri` fichier choisi. L’échantillon de code ci-dessous affiche la sélection d’images de l’utilisateur :

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Écrire du contenu à un fournisseur

En plus de charger du contenu de l’interface utilisateur SAF, KitKat vous permet également d’enregistrer du contenu à tout `ContentProvider` ce qui implémente l’API. `DocumentProvider` L’enregistrement `Intent` du `ActionCreateDocument`contenu utilise un avec :

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

L’échantillon de code ci-dessus charge l’interface utilisateur SAF, permettant à l’utilisateur de modifier le nom du fichier et de sélectionner un répertoire pour loger le nouveau fichier :

[![Capture d’écran de l’utilisateur changeant le nom du fichier en NewDoc dans le répertoire Téléchargements](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Lorsque l’utilisateur **Save**presse `OnActivityResult` Save `Android.Net.Uri` , obtient passé le fichier nouvellement `data.Data`créé, qui peut être consulté avec . L’uri peut être utilisé pour diffuser des données dans le nouveau fichier :

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Notez que[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
retourne `System.IO.Stream`un , de sorte que l’ensemble du processus de streaming peut être écrit en .NET.

Pour plus d’informations sur le chargement, la création et l’édition de contenu avec le cadre d’accès au stockage, consultez la [documentation Android pour le cadre d’accès au stockage](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Impression

Le contenu d’impression est simplifié dans KitKat avec l’introduction des [services d’impression](xref:Android.PrintServices) et `PrintManager`. KitKat est également la première version API à tirer pleinement parti des API du service Cloud Print de Google à [l’aide de l’application](https://developers.google.com/cloud-print/) [Google Cloud Print](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La plupart des appareils qui expédient avec KitKat téléchargent automatiquement l’application Google Cloud Print et le [HP Print Service Plugin](https://play.google.com/store/apps/details?id=com.hp.android.printservice)lorsqu’ils se connectent pour la première fois au WiFi. Un utilisateur peut vérifier les paramètres d’impression de son appareil en naviguant vers **Paramètres > Système > Impression**:

[![Exemple de capture d’écran de l’écran des paramètres d’impression](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> Bien que les API d’impression soient configurés pour fonctionner avec Google Cloud Print par défaut, Android permet toujours aux développeurs de préparer du contenu d’impression à l’aide des nouvelles API et de l’envoyer à d’autres applications pour gérer l’impression.

#### <a name="printing-html-content"></a>Impression de contenu HTML

KitKat crée [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) automatiquement un pour `WebView.CreatePrintDocumentAdapter`une vue web avec . L’impression de contenu Web [`WebViewClient`](xref:Android.Webkit.WebViewClient) est un effort coordonné entre un qui attend que le contenu HTML se charge et permet à l’activité de rendre `Print`l’option d’impression disponible dans le menu d’options, et l’activité, qui attend que l’utilisateur sélectionne l’option d’impression et appelle sur le `PrintManager`. Cette section couvre la configuration de base requise pour imprimer du contenu HTML à l’écran.

Notez que le chargement et l’impression de contenu Web nécessitent l’autorisation d’Internet :

[![Définir l’autorisation Internet dans les options d’application](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Imprimer l’article du menu

L’option d’impression apparaîtra généralement dans le [menu d’options](https://developer.android.com/guide/topics/ui/menus.html#options-menu)de l’activité.
Le menu d’options permet aux utilisateurs d’effectuer des actions sur une activité. Il est dans le coin supérieur droit de l’écran, et ressemble à ceci:

[![Exemple de capture d’écran de l’élément de menu d’impression affiché dans le coin supérieur droit de l’écran](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

Des éléments de menu supplémentaires peuvent être définis dans l’annuaire du *menu*sous *Ressources*. Le code ci-dessous définit un élément de menu d’échantillon appelé [Impression](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

L’interaction avec le menu d’options dans l’activité se fait par le biais de la et `OnCreateOptionsMenu` `OnOptionsItemSelected` des méthodes.
`OnCreateOptionsMenu`est l’endroit idéal pour ajouter de nouveaux éléments de menu, comme l’option Imprimer, à partir du répertoire des ressources du *menu.*
`OnOptionsItemSelected`écoute l’utilisateur en sélectionnant l’option Impression dans le menu et commence à imprimer :

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

Le code ci-dessus définit `dataLoaded` également une variable appelée pour garder une trace de l’état du contenu HTML. La `WebViewClient` variable définira cette variable à vrai lorsque tout le contenu a chargé, de sorte que l’activité sait ajouter l’élément de menu d’impression au menu d’options.

##### <a name="webviewclient"></a>WebViewClient (en)

Le travail `WebViewClient` de la est `WebView` de s’assurer que les données dans le est `OnPageFinished` entièrement chargé avant que l’option d’impression apparaît dans le menu, ce qu’il fait avec la méthode. `OnPageFinished`écoute du contenu web pour terminer le chargement, et `InvalidateOptionsMenu`dit à l’activité de recréer son menu d’options avec :

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished`définit également `dataLoaded` la `true`valeur `OnCreateOptionsMenu` à , peut donc recréer le menu avec l’option Imprimer en place.

##### <a name="printmanager"></a>PrintManager

L’exemple de code suivant `WebView`imprime le contenu d’un :

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`prend comme arguments: un nom pour le travail d’impression ("MyWebPage" dans cet exemple), un[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
qui génère le document d’impression à partir du contenu, et[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(dans`null` l’exemple ci-dessus). Vous pouvez `PrintAttributes` spécifier pour aider à établir du contenu sur la page imprimée, bien que les attributs par défaut doivent gérer la plupart des scénarios.

L’appel `Print` charge l’interface utilisateur d’impression, qui répertorie les options pour le travail d’impression. L’interface utilisateur donne aux utilisateurs la possibilité d’imprimer ou d’enregistrer le contenu HTML à un PDF, comme l’illustrent les captures d’écran ci-dessous:

[![Capture d’écran de PrintHtmlActivity affichant le menu Imprimer](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Capture d’écran de PrintHtmlActivity affichant l’enregistrement comme menu PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Matériel

KitKat ajoute plusieurs API pour accueillir de nouvelles fonctionnalités de l’appareil. Le plus notable d’entre eux sont Host-Based Card Emulation et le nouveau `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulation de carte basée sur l’hôte dans NFC

L’émulation de carte basée sur l’hôte (HCE) permet aux applications de se comporter comme des cartes NFC ou des lecteurs de cartes NFC sans compter sur l’élément sécurisé propriétaire du transporteur. Avant de configurer HCE, assurez-vous `PackageManager.HasSystemFeature`que HCE est disponible sur l’appareil avec :

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE exige que la fonction `Nfc` HCE et l’autorisation `AndroidManifest.xml`soient enregistrées auprès de la demande :

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Définir l’autorisation NFC dans les options d’application](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Pour fonctionner, HCE doit être en mesure de s’exécuter en arrière-plan, et il doit commencer lorsque l’utilisateur effectue une transaction NFC, même si l’application utilisant HCE n’est pas en cours d’exécution. Nous pouvons y parvenir en écrivant `Service`le code HCE comme un . Un service HCE `HostApduService` implémente l’interface, qui met en œuvre les méthodes suivantes :

- *ProcessCommandApdu* - Une unité de données du protocole d’application (APDU) est ce qui est envoyé entre le lecteur NFC et le service HCE. Cette méthode consomme un ADPU du lecteur, et renvoie une unité de données en réponse.

- *OnDeactivated* - `HostAdpuService` Le est désactivé lorsque le service HCE ne communique plus avec le lecteur NFC.

Un service HCE doit également être enregistré avec le manifeste de l’application, et décoré avec les autorisations appropriées, filtre d’intention, et métadonnées. Le code suivant est `HostApduService` un exemple d’un `Service` enregistré avec le Manifeste Android en utilisant l’attribut (pour plus d’informations sur les attributs, se référer à la Xamarin [De travail avec Android Manifest](~/android/platform/android-manifest.md) guide):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

Le service ci-dessus fournit un moyen pour le lecteur NFC d’interagir avec l’application, mais le lecteur NFC n’a toujours aucun moyen de savoir si ce service est en train d’imiter la carte NFC dont il a besoin pour numériser. Pour aider le lecteur NFC à identifier le Service, nous pouvons attribuer au Service un *ID d’application unique (AID).* Nous spécifions un AID, ainsi que d’autres métadonnées `MetaData` sur le service HCE, dans un fichier de ressources xml enregistré avec l’attribut (voir l’exemple de code ci-dessus). Ce fichier de ressources spécifie un ou plusieurs filtres AID - des chaînes d’identification uniques dans le format hexadecimal qui correspondent aux IAD d’un ou plusieurs appareils de lecteur NFC :

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

En plus des filtres AID, le fichier de ressources xml fournit également une description orientée vers l’utilisateur du service HCE, spécifie un groupe AID (application de paiement par rapport à «autre») et, dans le cas d’une demande de paiement, une bannière 260x96 dp à afficher à l’utilisateur.

La configuration décrite ci-dessus fournit les éléments de base d’une application imitant une carte NFC. NFC lui-même nécessite plusieurs étapes supplémentaires et d’autres tests pour configurer. Pour plus d’informations sur l’émulation de carte basée sur l’hôte, consultez le [portail de documentation Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Pour plus d’informations sur l’utilisation de NFC avec Xamarin, consultez les [échantillons Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Capteurs

KitKat donne accès aux capteurs de [`SensorManager`](xref:Android.Hardware.SensorManager)l’appareil par le biais d’un .
L’exploitation `SensorManager` permet au système d’exploitation de planifier la livraison des informations du capteur à une application en lots, en préservant la durée de vie de la batterie.

KitKat expédie également avec deux nouveaux types de capteurs pour le suivi des étapes de l’utilisateur. Ceux-ci sont basés sur l’accéléromètre et comprennent:

- *StepDetector* - L’application est notifiée/réveillée lorsque l’utilisateur fait un pas, et le détecteur fournit une valeur de temps pour le moment où l’étape s’est produite.

- *StepCounter* - Garde une trace du nombre de mesures que l’utilisateur a prises depuis que le capteur a été enregistré *jusqu’au redémarrage du prochain appareil.*

La capture d’écran ci-dessous représente le compteur d’étapes en action:

[![Capture d’écran de l’application SensorsActivity affichant un compteur d’étapes](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Vous pouvez `SensorManager` créer `GetSystemService(SensorService)` un en appelant `SensorManager`et en jetant le résultat comme un . Pour utiliser le compteur `GetDefaultSensor` d’étape, appelez le `SensorManager`. Vous pouvez enregistrer le capteur et écouter les changements dans le nombre d’étapes à l’aide de la[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
interface, comme l’illustre l’échantillon de code ci-dessous :

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged`est appelé si le nombre d’étapes met à jour pendant que l’application est au premier plan. Si l’application entre en arrière-plan, `OnSensorChanged` ou si l’appareil est endormi, ne sera pas appelée; cependant, les étapes continueront d’être comptées jusqu’à ce qu’elles `UnregisterListener` soient appelées.

Gardez à l’esprit que *la valeur de comptage d’étape est cumulative dans toutes les applications qui enregistrent le capteur*. Cela signifie que même si vous désinstallez et réinstallez votre application, et initialisez la `count` variable à 0 au démarrage de l’application, la valeur déclarée par le capteur restera le nombre total de mesures prises pendant l’enregistrement du capteur, que ce soit par votre application ou une autre. Vous pouvez empêcher votre application d’ajouter `UnregisterListener` au `SensorManager`compteur d’étapes en faisant appel au , comme l’illustre le code ci-dessous :

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Le redémarrage de l’appareil réinitialise le nombre d’étapes à 0. Votre application aura besoin de code supplémentaire pour s’assurer qu’elle signale un compte précis pour l’application, indépendamment des autres applications utilisant le capteur ou l’état de l’appareil.

> [!NOTE]
> Alors que l’API pour la détection d’étapes et le comptage des navires avec KitKat, tous les téléphones ne sont pas équipés avec le capteur. Vous pouvez vérifier si le `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`capteur est disponible en cours `GetDefaultSensor` d’exécution, ou vérifier pour s’assurer que la valeur retournée de n’est pas `null`.

<a name="developer_tools" />

## <a name="developer-tools"></a>Outils de développement

### <a name="screen-recording"></a>Enregistrement d’écran

KitKat inclut de nouvelles capacités d’enregistrement d’écran afin que les développeurs puissent enregistrer des applications en action. L’enregistrement d’écran est disponible via le client [Android Debug Bridge (ADB),](https://developer.android.com/tools/help/adb.html) qui peut être téléchargé dans le cadre de l’Android SDK.

Pour enregistrer votre écran, connectez votre appareil; puis, localiser votre installation Android SDK, naviguer vers le répertoire **des outils de plate-forme** et exécuter le client **adb:**

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

La commande ci-dessus enregistrera une vidéo par défaut de 3 minutes à la résolution par défaut de 4Mbps. Pour modifier la longueur, ajoutez le drapeau *--temps-limite.*
Pour modifier la résolution, ajoutez le drapeau *à taux de bits.* La commande suivante enregistrera une vidéo d’une minute à 8Mbps :

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Vous pouvez trouver votre vidéo sur votre appareil - elle apparaîtra dans votre galerie lorsque l’enregistrement sera terminé.

## <a name="other-kitkat-additions"></a>Autres ajouts KitKat

En plus des changements décrits ci-dessus, KitKat vous permet de :

- *Utilisez le plein écran* - KitKat introduit un nouveau [mode Immersive](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) pour naviguer du contenu, jouer à des jeux et exécuter d’autres applications qui pourraient bénéficier d’une expérience en plein écran.

- *Personnaliser les notifications* - Obtenez des détails supplémentaires sur les notifications système avec le[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . Cela vous permet de présenter les informations d’une manière différente à l’intérieur de votre application.

- *Mirror Drawable Resources* - Les ressources drawables ont une nouvelle[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  attribut qui indique au système de créer une version miroir pour les images qui nécessitent un retournement pour les mises en page de gauche à droite.

- *Pause Animations* - Pause et reprise des animations créées avec le[`Animator`](xref:Android.Animation.Animator)
  .

- *Lire Dynamically Changing Text* - Désigner des parties de l’assurance-chômage qui mettent à jour dynamiquement avec un nouveau texte en tant que "régions vivantes" avec la nouvelle[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  attribut de sorte que le nouveau texte sera lu automatiquement en mode d’accessibilité.

- *Améliorer l’expérience audio* - Rendre les pistes plus fortes avec le[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  , trouver le Peak et le RMS d’un flux audio avec le[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  classe, et obtenir des informations à partir d’un [chrono audio](xref:Android.Media.AudioTimestamp) pour aider à la synchronisation audio-vidéo.

- *Synchronisez contentResolver à intervalle personnalisé* - KitKat ajoute une certaine variabilité au moment où une demande de synchronisation est effectuée. Synchronisez un à l’heure `ContentResolver` ou à l’intervalle personnalisé en appelant `ContentResolver.RequestSync` et en passant dans un `SyncRequest`.

- *Distinction entre les contrôleurs* - Dans KitKat, les contrôleurs se voient attribuer des `ControllerNumber` identifiants integer uniques qui peuvent être consultés via la propriété de l’appareil. Il est ainsi plus facile de distinguer les joueurs dans un jeu.

- *Télécommande* - Avec quelques changements tant du côté matériel que logiciel, KitKat vous permet de transformer un `ConsumerIrService`appareil équipé d’un émetteur IR en télécommande à l’aide de la , et d’interagir avec les périphériques avec le nouveau[`RemoteController`](xref:Android.Media.RemoteController)
  Api.

Pour plus d’informations sur les modifications aPI ci-dessus, veuillez consulter l’aperçu des [API Android 4.4](https://developer.android.com/about/versions/android-4.4.html) de Google.

## <a name="summary"></a>Récapitulatif

Cet article a introduit certaines des nouvelles API disponibles dans Android 4.4 (niveau API 19), et a couvert les meilleures pratiques lors de la transition d’une application à KitKat. Il a décrit les changements apportés aux API affectant l’expérience utilisateur, y compris le *cadre de transition* et les nouvelles options de *thème*. Ensuite, il a introduit le `DocumentsProvider` cadre et la classe *d’accès au stockage,* ainsi que les nouvelles *API d’impression*. Il a exploré *l’émulation de carte basée sur l’hôte NFC* et comment travailler avec des capteurs de *faible puissance,* y compris deux nouveaux capteurs pour le suivi des étapes de l’utilisateur. Enfin, il a démontré la capture de démonstrations en temps réel des applications avec *enregistrement d’écran*, et a fourni une liste détaillée des modifications et des ajouts de KitKat API.

## <a name="related-links"></a>Liens connexes

- [Échantillon KitKat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [Android 4.4 API](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
