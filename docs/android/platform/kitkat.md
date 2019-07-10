---
title: Fonctionnalités de KitKat
description: Android 4.4 (KitKat) livrée avec une multitude de fonctionnalités pour les utilisateurs et les développeurs. Ce guide met en évidence plusieurs de ces fonctionnalités et fournit des exemples de code et les détails d’implémentation pour vous aider à tirer le meilleur parti KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 9572a08a2403bf13d74fc5fda7c62ec1fb2d1537
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674705"
---
# <a name="kitkat-features"></a>Fonctionnalités de KitKat

_Android 4.4 (KitKat) livrée avec une multitude de fonctionnalités pour les utilisateurs et les développeurs. Ce guide met en évidence plusieurs de ces fonctionnalités et fournit des exemples de code et les détails d’implémentation pour vous aider à tirer le meilleur parti KitKat._

## <a name="overview"></a>Vue d'ensemble

Android 4.4 (API niveau 19), également appelé « KitKat », a été publiée en liaison tardive 2013. KitKat offre une variété de nouvelles fonctionnalités et améliorations, notamment :

-  [Expérience utilisateur](#user_experience) &ndash; animations facile avec le framework de transition, translucides barres d’état et la navigation et le mode plein écran immersif aider à créer une meilleure expérience pour l’utilisateur.

-  [Contenu de l’utilisateur](#user_content) &ndash; gestion des fichiers utilisateur simplifiée avec le framework d’accès de stockage ; l’impression d’images, des sites web et autres contenus est plus facile avec l’impression améliorée API.

-  [Matériel](#hardware) &ndash; transformer n’importe quelle application en une carte NFC avec NFC émulation sur l’hôte de la carte ; exécuter des capteurs de faible puissance avec le `SensorManager` .

-  [Outils de développement](#developer_tools) &ndash; des applications de capture vidéo en action avec le client Android Debug Bridge, disponible dans le cadre du SDK Android.


Ce guide fournit des conseils pour migrer une application Xamarin.Android pour KitKat, ainsi que d’une vue d’ensemble de KitKat pour les développeurs Xamarin.Android.

## <a name="requirements"></a>Configuration requise

Pour développer des applications Xamarin.Android à l’aide de KitKat, vous devez *Xamarin.Android 4.11.0* ou supérieur et Android 4.4 (API niveau 19) installé via le gestionnaire Android SDK, comme illustré par la capture d’écran suivante :

[![Sélection d’Android 4.4 dans le gestionnaire Android SDK](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migration de votre application à KitKat

Cette section fournit des éléments de réponse à la première pour aider les applications existantes de transition vers Android 4.4.

### <a name="check-system-version"></a>Vérifier la Version du système

Si une application doit être compatible avec les versions antérieures d’Android, veillez à encapsuler tout code KitKat spécifique dans un contrôle de version système, comme l’illustre l’exemple de code ci-dessous :

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Le traitement par lots de l’alarme

Android utilise les services de l’alarme à sortir de veille une application en arrière-plan à un moment précis. KitKat cela prend d’aller plus loin en alarmes pour conserver la puissance de traitement par lot. Cela signifie que, à la place de sortir de veille chaque application à un moment exact, KitKat préfère regrouper plusieurs applications qui sont inscrits pour l’éveil par pendant le même intervalle de temps et les mettre en éveil en même temps.
Pour indiquer à Android à une application de sortir de veille pendant un intervalle de temps spécifié, appelez `SetWindow` sur le [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), en passant la valeur minimale et une durée maximale, en millisecondes, ce qui peut s’écouler avant que l’application est sortie et l’opération à effectuer à la mise en éveil.
Le code suivant fournit un exemple d’une application qui a besoin d’être sorti entre une demi-heure et une heure à partir de la durée de que la fenêtre est définie :

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Pour continuer la mise en éveil d’une application à un moment exact, utilisez `SetExact`, en passant l’heure exacte doit être sortie de l’application et l’opération à effectuer :

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat n’est plus vous permet de définir une alarme extensible exacte. Applications qui utilisent [`SetRepeating`](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)
et nécessitent des alarmes exactes fonctionne va maintenant devoir déclencher chaque alarme manuellement.

### <a name="external-storage"></a>Stockage externe

Stockage externe est désormais divisé en deux types : stockage unique pour votre application, ainsi que les données partagées par plusieurs applications. Lecture et écriture à un emplacement spécifique de votre application sur un stockage externe nécessitent aucune autorisation particulière. Interaction avec les données sur le stockage partagé maintenant nécessite la `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` autorisation. Les deux types peuvent être classés ainsi :

-  Si vous obtenez un chemin d’accès du fichier ou répertoire en appelant une méthode sur `Context` , par exemple, [`GetExternalFilesDir`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)
   Ou [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - votre application a besoin d’aucune autorisation supplémentaire.

-  Si vous obtenez un chemin d’accès du fichier ou répertoire en accès à une propriété ou en appelant une méthode sur `Environment` , tel que [`GetExternalStorageDirectory`](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)
   Ou [`GetExternalStoragePublicDirectory`](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/)
   , votre application nécessite la `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` autorisation.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implique le `READ_EXTERNAL_STORAGE` autorisation, donc vous devez toujours définir une autorisation.

### <a name="sms-consolidation"></a>Consolidation de SMS

KitKat simplifie la messagerie de l’utilisateur en regroupant tous les contenus SMS dans l’application d’une valeur par défaut sélectionnée par l’utilisateur. Il incombe au développeur pour rendre l’application peut être sélectionné en tant que l’application de messagerie par défaut et comporte de manière appropriée dans le code et dans la vie si l’application n’est pas sélectionnée. Pour plus d’informations sur la transition de votre application SMS pour KitKat, reportez-vous à la [votre SMS applications préparation pour KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) guide à partir de Google.

### <a name="webview-apps"></a>Applications WebView

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) obtenu une modernisation à KitKat. La plus importante modification est ajoutée à la sécurité pour le chargement de contenu dans un `WebView`. Bien que la plupart des applications qui ciblent des versions d’API plus anciennes devrait fonctionner comme prévu, tester des applications qui utilisent la `WebView` classe est fortement recommandée. Pour plus d’informations sur les APIs WebView affectées, consultez l’Android [migration vers WebView Android 4.4](https://developer.android.com/guide/webapps/migrating.html) documentation.

<a name="user_experience" />

## <a name="user-experience"></a>Expérience utilisateur

KitKat est fourni avec plusieurs nouvelles API pour améliorer l’expérience utilisateur, y compris la nouvelle infrastructure de transition pour la gestion des animations de propriété et une option d’interface utilisateur translucide pour le thème. Ces modifications sont décrites ci-dessous.

### <a name="transition-framework"></a>Framework de transition

Le framework de transition rend les animations plus facile à implémenter. KitKat vous permet d’effectuer une animation d’une propriété simple avec une seule ligne de code, ou de personnaliser à l’aide de transitions *scènes*.

#### <a name="simple-property-animation"></a>Animation de propriétés simples

La nouvelle bibliothèque de Transitions Android simplifie le code-behind des animations de propriété. L’infrastructure vous permet de réaliser des animations simples avec un minimum de code. Par exemple, l’exemple de code suivant utilise [`TransitionManager.BeginDelayedTransition`](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)
Pour animer les afficher et masquer un `TextView`:

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

L’exemple ci-dessus utilise l’infrastructure de transition pour créer un automatique, la transition par défaut entre les valeurs de propriété de variables. Étant donné que l’animation est gérée par une seule ligne de code, vous pouvez facilement apporter ce compatible avec les versions antérieures d’Android en encapsulant le `BeginDelayedTransition` appeler dans une vérification de version du système. Consultez le [migration de votre application à KitKat](#Migrating_Your_App_to_KitKat) section pour en savoir plus.

La capture d’écran ci-dessous montre l’application avant de l’animation :

[![Capture d’écran de l’application avant le démarrage de l’animation](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La capture d’écran ci-dessous montre l’application après l’animation :

[![Capture d’écran de l’application une fois l’animation terminée](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Vous pouvez obtenir davantage de contrôle sur la transition avec arrière-plan, qui font l’objet dans la section suivante.

#### <a name="android-scenes"></a>Arrière-plan Android

[Arrière-plan](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) ont été introduites dans le cadre de l’infrastructure de transition pour donner le développeur davantage de contrôle sur les animations. Arrière-plan créer une zone dynamique dans l’interface utilisateur : vous spécifiez un conteneur et plusieurs versions ou « arrière-plan », le contenu XML à l’intérieur du conteneur, et Android fait le reste du travail pour animer les transitions entre les scènes. Arrière-plan Android vous permettre de créer des animations complexes avec un minimum de travail sur le côté de développement.

L’élément d’interface statique qui héberge le contenu dynamique est appelé un *conteneur* ou *scène base*. L’exemple ci-dessous utilise Android Designer pour créer un `RelativeLayout` appelée `container`:

[![À l’aide d’Android Designer pour créer un conteneur RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

La disposition de l’exemple définit également un bouton appelé `sceneButton` ci-dessous le `container`. Ce bouton déclenche la transition.

Le contenu dynamique à l’intérieur du conteneur nécessite deux nouvelles dispositions Android. Ces dispositions de spécifient uniquement le code *à l’intérieur de* le conteneur.
L’exemple de code ci-dessous définit une disposition appelée *Scene1* qui contient deux champs de texte lecture « Kit » et « Kat » respectivement, et une deuxième disposition appelé *Scene2* qui contient les mêmes champs de texte inversés. Le code XML est comme suit :

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

L’exemple ci-dessus utilise `merge` pour raccourcir le code de vue et de simplifier la hiérarchie d’affichage. Vous pouvez en savoir plus sur `merge` dispositions [ici](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Une scène est créée en appelant [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), en passant l’objet de conteneur, l’ID de ressource de fichier de disposition de la scène et en cours `Context`, comme l’illustre l’exemple de code ci-dessous :

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Cliquez sur le bouton bascule entre les deux scènes, Android anime avec les valeurs de la transition par défaut :

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

La capture d’écran ci-dessous illustre la scène avant l’animation :

[![Capture d’écran de l’application avant le démarrage de l’animation](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

La capture d’écran ci-dessous illustre la scène après l’animation :

[![Capture d’écran de l’application une fois l’animation terminée](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Il existe un [bogue connu](https://code.google.com/p/android/issues/detail?id=62450) dans les Transitions Android bibliothèque qui provoque des scènes créée à l’aide de `GetSceneForLayout` pour arrêter l’exécution lorsqu’un utilisateur accède à une activité, la deuxième fois. Décrit une solution de contournement java [ici](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transitions personnalisées dans les scènes

Une transition personnalisée peut être définie dans un fichier de ressources xml dans le `transition` répertoire sous `Resources`, comme illustré par la capture d’écran ci-dessous :

[![Emplacement du fichier transition.xml sous le répertoire de ressources/transition](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

L’exemple de code suivant définit une transition qui anime pendant 5 secondes et utilise le [dépassement interpolateur](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transition est créée dans l’activité à l’aide de la [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), comme illustré par le code ci-dessous :

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nouvelle transition est ensuite ajoutée à la `Go` appel qui commence l’animation :

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interface utilisateur translucide

KitKat vous renforcez votre contrôle sur le thème de votre application avec des barres de navigation et d’état translucides facultatifs. Vous pouvez modifier la transparence d’éléments d’interface utilisateur système dans le même fichier XML que vous utilisez pour définir votre thème Android. KitKat présente les propriétés suivantes :

-  `windowTranslucentStatus` -Lorsque la valeur true, rend la barre d’état supérieur translucide.

-  `windowTranslucentNavigation` -Lorsque la valeur true, rend la barre de navigation inférieure translucide.

-  `fitsSystemWindows` -Définition de la barre supérieure ou inférieure à transcluent déplace le contenu sous les éléments d’interface utilisateur transparents par défaut. Si cette propriété `true` est un moyen simple pour empêcher le chevauchement avec les éléments d’interface utilisateur système translucide de contenu.


Le code suivant définit un thème avec translucides barres d’état et la navigation :

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

La capture d’écran ci-dessous montre le thème ci-dessus avec l’état translucide et de barres de navigation :

[![Capture d’écran de l’exemple d’application avec des barres de navigation et d’état translucides](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenu de l’utilisateur

### <a name="storage-access-framework"></a>Framework d’accès au stockage

Le Framework de l’accès de stockage (SAF) est une nouvelle façon pour les utilisateurs à interagir avec le contenu stocké comme les images, vidéos et documents. Au lieu de présenter des utilisateurs avec une boîte de dialogue pour choisir une application de gérer le contenu, KitKat ouvre une nouvelle interface utilisateur qui permet aux utilisateurs d’accéder à leurs données dans un emplacement d’agrégation. Une fois que le contenu a été choisi, l’utilisateur retourne à l’application qui a demandé le contenu et l’expérience de l’application continue normalement.

Cette modification nécessite deux actions sur le côté développeur : tout d’abord, les applications qui nécessitent le contenu des fournisseurs doivent être mis à jour vers une nouvelle façon de recherche du contenu. Ensuite, applications qui écrivent des données à un `ContentProvider` doivent être modifiés pour utiliser la nouvelle infrastructure. Les deux scénarios dépendent de la nouvelle [`DocumentsProvider`](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)
API :

#### <a name="documentsprovider"></a>DocumentsProvider

Dans KitKat, les interactions avec `ContentProviders` sont extraites avec la `DocumentsProvider` classe. Cela signifie que SAF ne soucie pas où les données sont physiquement, tant qu’il est accessible via la `DocumentsProvider` API. Fournisseurs locaux, cloud services et les périphériques de stockage externe toutes les utilisations de la même interface et sont traitée de la même façon, en fournissant l’utilisateur et le développeur avec un seul emplacement pour interagir avec le contenu de l’utilisateur.

Cette section explique comment charger et enregistrer le contenu avec l’infrastructure d’accès de stockage.

#### <a name="request-content-from-a-provider"></a>Contenu de la demande à partir d’un fournisseur

Nous pouvons dire KitKat que nous souhaitons choisir le contenu à l’aide de l’UI SAF avec le `ActionOpenDocument` intention, ce qui signifie que nous souhaitons vous connecter à tous les fournisseurs de contenu disponibles sur l’appareil. Vous pouvez ajouter une partie du filtrage à cette intention en spécifiant `CategoryOpenable`, ce qui ne signifie que le contenu peut être ouvert (autrement dit, accessible et utilisable de contenu) est retournée. KitKat autorise également le filtrage de contenu avec le `MimeType`. Par exemple, le code suivant filtre les résultats d’images en spécifiant l’image `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Appel `StartActivityForResult` lance l’UI SAF, dont l’utilisateur peut ensuite Parcourir pour choisir une image :

[![Capture d’écran de l’exemple d’une application à l’aide de l’infrastructure d’accès de stockage pour la navigation vers une image](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Une fois que l’utilisateur a choisi une image, `OnActivityResult` retourne le `Android.Net.Uri` du fichier choisi. L’exemple de code ci-dessous affiche la sélection de l’image de l’utilisateur :

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

#### <a name="write-content-to-a-provider"></a>Écrire le contenu sur un fournisseur

Outre le chargement de contenu à partir de l’UI SAF, KitKat permet également de vous enregistrer le contenu dans les `ContentProvider` qui implémente le `DocumentProvider` API. L’enregistrement de contenu utilise un `Intent` avec `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

L’exemple de code ci-dessus, charge l’UI SAF, ce qui permet de l’utilisateur modifier le nom de fichier et sélectionnez un répertoire pour héberger le nouveau fichier :

[![Capture d’écran de l’utilisateur de modifier le nom du fichier à NewDoc dans le répertoire de téléchargements](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Lorsque l’utilisateur appuie sur **enregistrer**, `OnActivityResult` reçoit le `Android.Net.Uri` du fichier qui vient d’être créé, qui est accessible avec `data.Data`. L’uri peut être utilisé pour transmettre les données dans le nouveau fichier :

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

Notez que [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))
Retourne un `System.IO.Stream`, de sorte que tout le processus de diffusion en continu peuvent être écrits en .NET.

Pour plus d’informations sur le chargement, création et modification de contenu avec l’infrastructure d’accès de stockage, consultez le [documentation Android pour l’infrastructure d’accès de stockage](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Impression

Contenu de l’impression est simplifié dans KitKat avec l’introduction de la [Services d’impression](https://developer.xamarin.com/api/namespace/Android.PrintServices/) et `PrintManager`. KitKat est également la première version d’API pour tirer pleinement parti de la [API de service d’impression de Cloud de Google](https://developers.google.com/cloud-print/) à l’aide de la [application de Google Cloud impression](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La plupart des appareils fournis avec KitKat automatiquement téléchargement l’application d’impression de Cloud de Google et [le plug-in Service HP impression](https://play.google.com/store/apps/details?id=com.hp.android.printservice)lorsque leur première connexion au réseau sans fil. Un utilisateur peut vérifier les paramètres d’impression de son appareil en accédant à **Paramètres > système > impression**:

[![Capture d’écran de l’écran de paramètres d’impression](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Bien que les API d’impression sont configurés pour fonctionner avec l’impression Cloud Google par défaut, Android permet toujours aux développeurs de préparer le contenu à l’aide des nouvelles API d’impression et de les envoyer à d’autres applications pour gérer l’impression.



#### <a name="printing-html-content"></a>Contenu HTML de l’impression

KitKat crée automatiquement un [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) pour obtenir une vue web avec `WebView.CreatePrintDocumentAdapter`. Contenu web de l’impression est un effort coordonné entre un [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) qui attend que le contenu HTML à charger et vous permet du savoir pour rendre l’option d’impression disponible dans le menu options et l’activité, qui attend que l’utilisateur Sélectionnez l’option d’impression et les appels `Print`sur le `PrintManager`. Cette section décrit la configuration de base requise pour imprimer à l’écran du contenu HTML.

Notez que le chargement et l’impression de contenu web nécessitent l’autorisation Internet :

[![Définition des autorisations Internet dans les options de l’application](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Élément de Menu Imprimer

L’option d’impression s’affiche généralement dans l’activité [menu options](https://developer.android.com/guide/topics/ui/menus.html#options-menu).
Le menu options permet aux utilisateurs d’effectuer des actions sur une activité. Il est dans l’angle supérieur droit de l’écran et ressemble à ceci :

[![Capture d’écran de l’exemple d’élément de menu Imprimer affiché dans l’angle supérieur droit de l’écran](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Éléments de menu supplémentaires peuvent être définies dans le *menu*répertoire sous *ressources*. Le code ci-dessous définit un menu exemple appelé [impression](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

Interaction avec le menu d’options dans l’activité se produit via le `OnCreateOptionsMenu` et `OnOptionsItemSelected` méthodes.
`OnCreateOptionsMenu` est l’endroit où ajouter de nouveaux éléments de menu, comme l’option d’impression, à partir de la *menu* répertoire des ressources.
`OnOptionsItemSelected` écoute de l’utilisateur en sélectionnant l’option d’impression à partir du menu et commence à imprimer :

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

Le code ci-dessus définit également une variable appelée `dataLoaded` pour suivre l’état du contenu HTML. Le `WebViewClient` définira cette variable avec la valeur true lorsque tout le contenu a été chargé, afin que l’activité sache pour ajouter l’élément de menu Imprimer sur le menu options.

##### <a name="webviewclient"></a>WebViewClient

Le travail de le `WebViewClient` consiste à garantir que les données dans le `WebView` est entièrement chargé avant que l’option d’impression apparaît dans le menu, il le fait avec le `OnPageFinished` (méthode). `OnPageFinished` écoute pour le contenu web à la fin du chargement, et indique l’activité pour recréer son menu options avec `InvalidateOptionsMenu`:

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

`OnPageFinished` définit également le `dataLoaded` valeur `true`, de sorte que `OnCreateOptionsMenu` pouvez recréer le menu avec l’option d’impression en place.

##### <a name="printmanager"></a>PrintManager

L’exemple de code suivant imprime le contenu d’un `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` prend comme arguments : un nom pour le travail d’impression (« MyWebPage » dans cet exemple), un [`PrintDocumentAdapter`](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)
qui génère le document d’impression à partir du contenu, et [`PrintAttributes`](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/)
(`null` dans l’exemple ci-dessus). Vous pouvez spécifier `PrintAttributes` pour aider à présenter le contenu sur la page imprimée, bien que les attributs par défaut doivent gérer la plupart des scénarios.

Appel `Print` se charge de l’interface utilisateur impression, qui répertorie les options pour le travail d’impression. L’interface utilisateur permet aux utilisateurs d’imprimer ou enregistrer le contenu HTML dans un fichier PDF, comme illustré par les captures d’écran ci-dessous :

[![Capture d’écran de PrintHtmlActivity affiche le menu Imprimer](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Capture d’écran de PrintHtmlActivity affichage de l’enregistrement sous forme de menu PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Matériel

KitKat ajoute plusieurs API pour prendre en compte les nouvelles fonctionnalités des appareils. Le plus notable de ceux-ci sont l’émulation de la carte sur l’hôte et le nouveau `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Émulation de la carte sur l’hôte dans NFC

Émulation de carte sur l’hôte (HCE) permet aux applications se comportent comme des cartes de NFC ou les lecteurs de carte NFC sans se baser sur l’élément de sécuriser les propriétaires du transporteur. Avant de configurer HCE, assurez-vous HCE est disponible sur l’appareil avec `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE exige que les deux la fonctionnalité HCE et `Nfc` autorisation auprès de l’application `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Définition de l’autorisation de NFC dans les options de l’application](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Pour travailler, HCE doit être en mesure d’exécuter en arrière-plan, et il doit démarrer quand l’utilisateur effectue une transaction NFC, même si l’application à l’aide de HCE n’est pas en cours d’exécution. Pour ce faire, nous pouvons écrire le code HCE comme un `Service`. Implémente un Service HCE le `HostApduService` interface qui implémente les méthodes suivantes :

-  *ProcessCommandApdu* -An données unité APDU (Application Protocol) est envoyé entre le lecteur NFC et le Service HCE. Cette méthode consomme un ADPU à partir du lecteur et retourne une unité de données dans la réponse.

-  *OnDeactivated* - le `HostAdpuService` est désactivé lorsque le Service HCE est ne communiquent plus avec le lecteur NFC.


Un Service HCE doit également être enregistrés avec le manifeste de l’application et décorée avec les autorisations appropriées, filtre d’intention et les métadonnées. Le code suivant est un exemple d’un `HostApduService` inscrit avec le manifeste Android à l’aide de la `Service` attribut (pour plus d’informations sur les attributs, reportez-vous à la Xamarin [fonctionne avec un manifeste Android](~/android/platform/android-manifest.md) guide) :

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

Le Service ci-dessus fournit un moyen pour le lecteur NFC interagir avec l’application, mais le lecteur NFC n’a encore aucun moyen de savoir si ce Service émule la carte NFC qu’il doit analyser. Pour identifier le Service au lecteur NFC, nous pouvons affecter le Service unique *ID Application (AID)* . Nous spécifier une aide, ainsi que d’autres métadonnées relatives au Service HCE, dans un fichier de ressources xml enregistré avec le `MetaData` attribut (voir l’exemple de code ci-dessus). Ce fichier de ressource spécifie un ou plusieurs filtres aide - les chaînes d’identificateur unique au format hexadécimal qui correspondent aux outils d’un ou plusieurs appareils de lecteur NFC :

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

En plus des filtres d’aide, le fichier de ressources xml fournit également une description d’orientées utilisateur du Service HCE, spécifie un groupe d’aide (application de paiement par rapport aux « autres ») et, dans le cas d’une demande de paiement, une bannière de point de distribution de 260 x 96 à afficher à l’utilisateur.

Le programme d’installation décrite ci-dessus fournit les blocs de construction de base pour une application émule une carte NFC. NFC lui-même nécessite plusieurs étapes plus et effectuer d’autres tests pour configurer. Pour plus d’informations sur l’émulation de la carte sur l’hôte, reportez-vous à la [portail de documentation Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Pour plus d’informations sur l’utilisation de NFC avec Xamarin, consultez le [les exemples Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Capteurs

KitKat fournit l’accès aux capteurs de l’appareil via un [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
Le `SensorManager` permet le système d’exploitation planifier la remise des informations de capteurs à une application dans des lots, en conservant autonomie.

KitKat est également livré avec deux nouveaux types de capteur pour le suivi des étapes de l’utilisateur. Elles sont basées sur l’accéléromètre et incluent :

-  *StepDetector* -application est notifiée/sorti lorsque l’utilisateur effectue une étape et le détecteur fournit une valeur d’heure pour laquelle l’étape s’est produite.

-  *StepCounter* -conserve une trace du nombre d’étapes de l’utilisateur dans la mesure où le capteur a été inscrit *jusqu’au prochain redémarrage de l’appareil*.

La capture d’écran ci-dessous illustre le compteur de l’étape en action :

[![Capture d’écran de l’application de SensorsActivity afficher un compteur de l’étape](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Vous pouvez créer un `SensorManager` en appelant `GetSystemService(SensorService)` et en convertissant le résultat comme un `SensorManager`. Pour utiliser le compteur de l’étape, appelez `GetDefaultSensor` sur le `SensorManager`. Vous pouvez inscrire le capteur et écouter les modifications dans le nombre d’étape à l’aide de la [`ISensorEventListener`](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)
interface, comme l’illustre l’exemple de code ci-dessous :

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

`OnSensorChanged` est appelée si le nombre d’étape met à jour lorsque l’application est au premier plan. Si l’application passe à l’arrière-plan ou l’appareil est en veille, `OnSensorChanged` ne sera pas appelé ; Toutefois, les étapes continueront à compter jusqu'à ce que `UnregisterListener` est appelée.

N’oubliez pas que *la valeur du nombre étape est cumulée pour toutes les applications qui s’inscrivent le capteur*. Cela signifie que même si vous désinstallez et réinstallez votre application et initialisez le `count` variable au niveau 0 au démarrage de l’application, la valeur indiquée par le capteur restera le nombre total d’étapes parcourues alors que le capteur a été enregistré, que ce soit par votre application ou un autre. Vous pouvez empêcher votre application à partir de l’ajout au compteur étape en appelant `UnregisterListener` sur la `SensorManager`, comme illustré par le code ci-dessous :

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Le redémarrage de l’appareil rétablit le nombre d’étape 0. Votre application nécessite un code supplémentaire pour s’assurer qu’il signale un nombre précis de l’application, indépendamment des autres applications utilisant le capteur ou l’état de l’appareil.


> [!NOTE]
> Lors de l’API pour la détection de l’étape et de comptage est fourni avec KitKat, le capteur validation pas tous les téléphones. Vous pouvez vérifier si le capteur est disponible en exécutant `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, ou assurez-vous que la valeur retournée de `GetDefaultSensor` n’est pas `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Outils de développement

### <a name="screen-recording"></a>Enregistrement d’écran

KitKat inclut un nouvel écran enregistrement des fonctionnalités afin que les développeurs peuvent enregistrer des applications en action. Enregistrement d’écran est disponible via le [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) client, ce qui peut être téléchargé en tant que partie du SDK Android.

Pour enregistrer votre écran, connectez votre appareil ; Ensuite, recherchez votre installation du Kit Android SDK, accédez à la **outils de plateforme** répertoire et exécutez le **adb** client :

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

La commande ci-dessus enregistrera une vidéo de 3 minutes par défaut à la résolution par défaut de 4 Mbits/s. Pour modifier la longueur, ajoutez le *--délai* indicateur.
Pour modifier la résolution, ajoutez le *--vitesse de transmission* indicateur. La commande suivante enregistre une vidéo longue à la minute à 8 :

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Vous pouvez trouver votre vidéo sur votre appareil, il apparaît dans votre galerie lors de l’enregistrement est terminé.


## <a name="other-kitkat-additions"></a>Autres ajouts KitKat

Outre les modifications décrites ci-dessus, KitKat vous permet de :

-  *Utiliser le mode plein écran* -KitKat introduit une nouvelle [mode immersif](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) pour Explorer le contenu, jouer à des jeux et exécutant d’autres applications susceptibles de bénéficier d’une expérience plein écran.

-  *Personnaliser les Notifications* -obtenir des détails supplémentaires concernant les notifications système avec le [`NotificationListenerService`](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/)
   . Cela vous permet de présenter les informations d’une manière différente à l’intérieur de votre application.

-  *Mettre en miroir des ressources Drawable* -ressources Drawable ont un nouveau [`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
   attribut qui indique au système de créer une version mise en miroir pour les images qui nécessitent le retournement pour la disposition de gauche à droite.

-  *Suspendre des Animations* -suspendre et reprendre des animations créées avec le [`Animator`](https://developer.xamarin.com/api/type/Android.Animation.Animator/)
   .

-  *Dynamiquement modification de texte en lecture* -indiquent les parties de l’interface utilisateur qui mettent à jour dynamiquement par un nouveau texte en tant que « zones dynamiques » avec le nouveau [`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   attribut pour le nouveau texte lira automatiquement en mode d’accessibilité.

-  *Améliorer l’expérience de l’Audio* -Vérifiez effectue le suivi plus fort avec le [`LoudnessEnhancer`](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/)
   , trouver des pics et RMS d’un flux audio avec le [`Visualizer`](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)
   classe et obtenir des informations à partir d’un [timestamp audio](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) pour faciliter la synchronisation de l’audio et vidéo.

-  *ContentResolver de synchronisation personnalisée des intervalles* -KitKat ajoute certaine variabilité jusqu’au moment où une demande de synchronisation est effectuée. Synchronisation un `ContentResolver` à personnalisé intervalle de temps ou en appelant `ContentResolver.RequestSync` et en passant un `SyncRequest`.

-  *Distinguer entre les contrôleurs* -dans KitKat, les contrôleurs sont affectés des identificateurs entier unique qui sont accessible par le biais de l’appareil `ControllerNumber` propriété. Cela rend plus facile à dire les uns des autres acteurs dans un jeu.

-  *Contrôle à distance* -avec peu de modifications sur le matériel et les logiciels côté KitKat vous permet d’activer un appareil équipé d’un émetteur de runtime d’intégration dans un contrôle à distance à l’aide du `ConsumerIrService`et d’interagir avec les périphériques avec le nouveau [`RemoteController`](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)
   API.

Pour plus d’informations sur les modifications de l’API ci-dessus, reportez-vous à Google [Android 4.4 API](https://developer.android.com/about/versions/android-4.4.html) vue d’ensemble.


## <a name="summary"></a>Récapitulatif

Cet article introduit certaines des nouvelles API disponibles dans Android 4.4 (API niveau 19) et couvert les meilleures pratiques lors de la transition d’une application à KitKat. Informatique rencontrer des modifications indiquées aux API affecter l’utilisateur, y compris le *framework de transition* et de nouvelles options pour *thèmes*. Ensuite, il introduit le *Framework d’accès au stockage* et `DocumentsProvider` classe, ainsi que la nouvelle *API d’impression*. Il exploré *émulation de la carte sur l’hôte NFC* et comment travailler avec *capteurs de faible puissance*, y compris deux nouveaux capteurs pour suivre les étapes de l’utilisateur. Enfin, il vous a expliqué capture des démonstrations en temps réel des applications avec *enregistrement d’écran*et fourni une liste détaillée des modifications de l’API de KitKat et ajouts.


## <a name="related-links"></a>Liens associés

- [Exemple de KitKat](https://developer.xamarin.com/samples/monodroid/KitKat/)
- [Android 4.4 API](https://developer.android.com/about/versions/android-4.4.html)
- [KitKat Android](https://developer.android.com/about/versions/kitkat.html)
