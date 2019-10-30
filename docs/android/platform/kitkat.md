---
title: Fonctionnalités de KitKat
description: Android 4,4 (KitKat) est chargé avec un multitude de fonctionnalités pour les utilisateurs et les développeurs. Ce guide met en évidence plusieurs de ces fonctionnalités et fournit des exemples de code et des détails d’implémentation pour vous aider à tirer le meilleur parti de KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 43061272f3d3486926f38af792ee3b9df0c53670
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027239"
---
# <a name="kitkat-features"></a>Fonctionnalités de KitKat

_Android 4,4 (KitKat) est chargé avec un multitude de fonctionnalités pour les utilisateurs et les développeurs. Ce guide met en évidence plusieurs de ces fonctionnalités et fournit des exemples de code et des détails d’implémentation pour vous aider à tirer le meilleur parti de KitKat._

## <a name="overview"></a>Vue d'ensemble

Android 4,4 (niveau d’API 19), également connu sous le nom de « KitKat », a été publié au plus tard le 2013. KitKat offre une variété de nouvelles fonctionnalités et améliorations, notamment :

- L' [expérience utilisateur](#user_experience) &ndash; animations faciles avec l’infrastructure de transition, l’État translucide et les barres de navigation, ainsi que le mode immersif plein écran permettent de créer une meilleure expérience pour l’utilisateur.

- [Contenu utilisateur](#user_content) &ndash; la gestion des fichiers utilisateur simplifiée avec l’infrastructure d’accès au stockage ; l’impression d’images, de sites Web et d’autres contenus est plus facile grâce à des API d’impression améliorées.

- [Matériel](#hardware) &ndash; transformer une application en carte NFC avec émulation de carte basée sur l’hôte NFC ; exécutez des capteurs de faible consommation d’énergie avec la `SensorManager`.

- [Outils de développement](#developer_tools) &ndash; les applications de capture vidéo en action avec le client Android Debug Bridge, disponibles dans le cadre de la Android SDK.

Ce guide fournit des conseils pour la migration d’une application Xamarin. Android existante vers KitKat, ainsi qu’une vue d’ensemble de haut niveau de KitKat pour les développeurs Xamarin. Android.

## <a name="requirements"></a>spécifications

Pour développer des applications Xamarin. Android à l’aide de KitKat, *Xamarin. Android 4.11.0* ou version ultérieure et Android 4,4 (niveau d’API 19) sont installés via le gestionnaire de Android SDK, comme illustré dans la capture d’écran suivante :

[![sélectionnant Android 4,4 dans le gestionnaire de Android SDK](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migration de votre application vers KitKat

Cette section fournit des éléments de première réponse pour faciliter la transition d’applications existantes vers Android 4,4.

### <a name="check-system-version"></a>Vérifier la version du système

Si une application doit être compatible avec les versions antérieures d’Android, veillez à inclure dans un wrapper un code spécifique à KitKat dans une vérification de la version du système, comme illustré dans l’exemple de code ci-dessous :

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Traitement par lot des alarmes

Android utilise les services d’alarme pour mettre en éveil une application en arrière-plan à une heure spécifiée. KitKat effectue cette étape plus loin en regroupant les alarmes pour préserver la puissance. Cela signifie que, au lieu de réveiller chaque application à un moment précis, KitKat préfère regrouper plusieurs applications qui sont inscrites pour sortir de veille pendant le même intervalle de temps et les mettre en éveil en même temps.
Pour indiquer à Android de réveiller une application pendant un intervalle de temps spécifié, appelez `SetWindow` sur le [`AlarmManager`](xref:Android.App.AlarmManager), en passant la durée minimale et maximale, en millisecondes, qui peut s’écouler avant que l’application ne soit réveillée et l’opération à effectuer lors de la mise en éveil.
Le code suivant fournit un exemple d’application qui doit être réveillée entre une demi-heure et une heure à partir de l’heure à laquelle la fenêtre est définie :

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Pour continuer à sortir une application à une heure précise, utilisez `SetExact`, en passant l’heure exacte à laquelle l’application doit être réveillée et l’opération à effectuer :

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat ne vous permet plus de définir une alarme de répétition exacte. Applications qui utilisent [`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
et exiger que les alarmes exactes fonctionnent maintenant doivent déclencher chaque alarme manuellement.

### <a name="external-storage"></a>Stockage externe

Le stockage externe est maintenant divisé en deux types : le stockage propre à votre application et les données partagées par plusieurs applications. La lecture et l’écriture de l’emplacement spécifique de votre application sur le stockage externe ne nécessitent pas d’autorisations spéciales. L’interaction avec les données sur le stockage partagé nécessite désormais l’autorisation `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE`. Les deux types peuvent être classés comme tels :

- Si vous obtenez un chemin d’accès de fichier ou de répertoire en appelant une méthode sur `Context`, par exemple, [`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  ou [`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
  - votre application ne requiert aucune autorisation supplémentaire.

- Si vous obtenez un chemin d’accès de fichier ou de répertoire en accédant à une propriété ou en appelant une méthode sur `Environment`, tel que [`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  ou [`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
  , votre application nécessite l’autorisation `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE`.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implique l’autorisation `READ_EXTERNAL_STORAGE`, vous ne devriez donc jamais avoir à définir une seule autorisation.

### <a name="sms-consolidation"></a>Consolidation SMS

KitKat simplifie la messagerie pour l’utilisateur en regroupant tout le contenu SMS dans une application par défaut sélectionnée par l’utilisateur. Le développeur est chargé de rendre l’application sélectionnable comme application de messagerie par défaut et de se comporter de manière appropriée dans le code et dans la vie si l’application n’est pas sélectionnée. Pour plus d’informations sur la transition de votre application SMS vers KitKat, reportez-vous au guide [obtention de vos applications SMS prêtes pour KitKat](https://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) sur Google.

### <a name="webview-apps"></a>Applications WebView

[WebView](xref:Android.Webkit.WebView) a obtenu une modernisation dans KitKat. La plus grande modification est la sécurité supplémentaire pour le chargement de contenu dans une `WebView`. Alors que la plupart des applications ciblant des versions d’API plus anciennes devraient fonctionner comme prévu, il est fortement recommandé de tester les applications qui utilisent la classe `WebView`. Pour plus d’informations sur les API WebView affectées, consultez la documentation relative à la [migration d’Android vers WebView dans android 4,4](https://developer.android.com/guide/webapps/migrating.html) .

<a name="user_experience" />

## <a name="user-experience"></a>Expérience utilisateur

KitKat est fourni avec plusieurs nouvelles API pour améliorer l’expérience utilisateur, y compris la nouvelle infrastructure de transition pour gérer les animations de propriétés et une option d’interface utilisateur translucide pour les thèmes. Ces modifications sont décrites ci-dessous.

### <a name="transition-framework"></a>Infrastructure de transition

L’infrastructure de transition rend les animations plus faciles à implémenter. KitKat vous permet d’effectuer une animation de propriété simple avec une seule ligne de code, ou de personnaliser des transitions à l’aide de *scènes*.

#### <a name="simple-property-animation"></a>Animation de propriétés simples

La nouvelle bibliothèque de transitions Android simplifie les animations de propriétés code-behind. L’infrastructure vous permet d’effectuer des animations simples avec un minimum de code. Par exemple, l’exemple de code suivant utilise [`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
pour animer l’émission et le masquage d’une `TextView`:

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

L’exemple ci-dessus utilise l’infrastructure de transition pour créer une transition automatique par défaut entre les valeurs de propriété variables. Étant donné que l’animation est gérée par une seule ligne de code, vous pouvez facilement la rendre compatible avec les versions antérieures d’Android en encapsulant le `BeginDelayedTransition` appel dans une vérification de la version du système. Pour plus d’informations, consultez la section [migration de votre application vers KitKat](#Migrating_Your_App_to_KitKat) .

La capture d’écran ci-dessous montre l’application avant l’animation :

[capture d’écran de l’application ![avant le démarrage de l’animation](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La capture d’écran ci-dessous montre l’application après l’animation :

[capture d’écran de l’application ![une fois l’animation terminée](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Vous pouvez mieux contrôler la transition avec des scènes, qui sont décrites dans la section suivante.

#### <a name="android-scenes"></a>Scènes Android

Des [scènes](xref:Android.Transitions.Scene) ont été introduites dans le cadre de l’infrastructure de transition pour permettre au développeur de mieux contrôler les animations. Les scènes créent une zone dynamique dans l’interface utilisateur : vous spécifiez un conteneur et plusieurs versions, ou « scènes », pour le contenu XML à l’intérieur du conteneur, et Android fait le reste du travail pour animer les transitions entre les scènes. Les scènes Android vous permettent de créer des animations complexes avec un minimum de travail du côté du développement.

L’élément d’interface utilisateur statique qui héberge le contenu dynamique est appelé une *base*de *conteneur* ou de scène. L’exemple ci-dessous utilise la Android Designer pour créer une `RelativeLayout` appelée `container`:

[![de l’utilisation du Android Designer pour créer un conteneur RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

L’exemple de disposition définit également un bouton appelé `sceneButton` sous le `container`. Ce bouton déclenche la transition.

Le contenu dynamique à l’intérieur du conteneur requiert deux nouvelles dispositions Android. Ces dispositions spécifient uniquement le code *à l’intérieur* du conteneur.
L’exemple de code ci-dessous définit une disposition appelée *Scene1* qui contient deux champs de texte qui lisent respectivement « Kit » et « Kat », et une deuxième disposition appelée *scene2* qui contient les mêmes champs de texte inversés. Le code XML est le suivant :

 **Scene1. AXML**:

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

 **Scene2. AXML**:

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

L’exemple ci-dessus utilise `merge` pour rendre le code de vue plus concis et simplifier la hiérarchie d’affichage. Pour en savoir plus sur les dispositions `merge`, cliquez [ici](https://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Une scène est créée en appelant [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*), en passant l’objet conteneur, l’ID de ressource du fichier de disposition de la scène et le `Context`actuel, comme illustré dans l’exemple de code ci-dessous :

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Le fait de cliquer sur le bouton retourne entre les deux scènes, que Android anime avec les valeurs de transition par défaut :

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

La capture d’écran ci-dessous illustre la scène avant l’animation :

[Capture d’écran ![de l’application avant le démarrage de l’animation](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

La capture d’écran ci-dessous illustre la scène après l’animation :

[![capture d’écran de l’application une fois l’animation terminée](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> Il existe un [bogue connu](https://code.google.com/p/android/issues/detail?id=62450) dans la bibliothèque de transitions Android qui amène les scènes créées à l’aide de `GetSceneForLayout` à s’arrêter lorsqu’un utilisateur parcourt une activité la deuxième fois. Une solution de contournement Java est décrite [ici](http://www.doubleencore.com/2013/11/new-transitions-framework/).

##### <a name="custom-transitions-in-scenes"></a>Transitions personnalisées en scènes

Une transition personnalisée peut être définie dans un fichier de ressources XML dans le répertoire `transition` sous `Resources`, comme illustré dans la capture d’écran ci-dessous :

[Emplacement ![du fichier transition. XML sous le répertoire Resources/transition](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

L’exemple de code suivant définit une transition qui s’anime pendant 5 secondes et utilise l' [interpolation de dépassement](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transition est créée dans l’activité à l’aide de [TransitionInflater](xref:Android.Transitions.TransitionInflater), comme illustré par le code ci-dessous :

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nouvelle transition est ensuite ajoutée à l’appel de `Go` qui commence l’animation :

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interface utilisateur translucide

KitKat vous permet de mieux contrôler la façon dont votre application est dotée de l’État translucide facultatif et des barres de navigation. Vous pouvez modifier la translucidité des éléments d’interface utilisateur du système dans le même fichier XML que celui utilisé pour définir votre thème Android. KitKat présente les propriétés suivantes :

- `windowTranslucentStatus`-lorsque la valeur est true, la barre d’état supérieure est translucide.

- `windowTranslucentNavigation`-lorsque la valeur est true, la barre de navigation inférieure est translucide.

- `fitsSystemWindows`-le paramétrage de la barre supérieure ou inférieure transcluent déplace le contenu sous les éléments d’interface utilisateur transparents par défaut. La définition de cette propriété sur `true` est un moyen simple d’empêcher le chevauchement du contenu avec les éléments d’interface utilisateur système translucides.

Le code suivant définit un thème avec l’État translucide et les barres de navigation :

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

La capture d’écran ci-dessous montre le thème ci-dessus avec l’État translucide et les barres de navigation :

[![exemple de capture d’écran de l’application avec l’État translucide et les barres de navigation](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenu utilisateur

### <a name="storage-access-framework"></a>Infrastructure d’accès au stockage

L’infrastructure d’accès au stockage (SAF) est une nouvelle méthode permettant aux utilisateurs d’interagir avec du contenu stocké, comme des images, des vidéos et des documents. Au lieu de présenter aux utilisateurs une boîte de dialogue permettant de choisir une application pour gérer le contenu, KitKat ouvre une nouvelle interface utilisateur qui permet aux utilisateurs d’accéder à leurs données dans un emplacement d’agrégation. Une fois que le contenu a été choisi, l’utilisateur revient à l’application qui a demandé le contenu, et l’expérience de l’application se poursuit normalement.

Cette modification nécessite deux actions côté développeur : tout d’abord, les applications qui nécessitent du contenu des fournisseurs doivent être mises à jour vers une nouvelle façon de demander du contenu. Deuxièmement, les applications qui écrivent des données dans un `ContentProvider` doivent être modifiées pour utiliser la nouvelle infrastructure. Les deux scénarios dépendent du nouveau [`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API :

#### <a name="documentsprovider"></a>DocumentsProvider

Dans KitKat, les interactions avec `ContentProviders` sont abstraites avec la classe `DocumentsProvider`. Cela signifie qu’SAF ne se soucie pas de l’emplacement physique des données, à condition qu’elles soient accessibles par le biais de l’API `DocumentsProvider`. Les fournisseurs locaux, les services de Cloud Computing et les périphériques de stockage externes utilisent tous la même interface et sont traités de la même façon, fournissant à l’utilisateur et au développeur un emplacement pour interagir avec le contenu de l’utilisateur.

Cette section explique comment charger et enregistrer du contenu avec l’infrastructure d’accès au stockage.

#### <a name="request-content-from-a-provider"></a>Demander du contenu auprès d’un fournisseur

Nous pouvons dire à KitKat que nous souhaitons sélectionner du contenu à l’aide de l’interface utilisateur de SAF avec l’intention de `ActionOpenDocument`, ce qui signifie que nous voulons nous connecter à tous les fournisseurs de contenu disponibles pour l’appareil. Vous pouvez ajouter un filtrage à cette intention en spécifiant `CategoryOpenable`, ce qui signifie que seul le contenu qui peut être ouvert (par exemple, un contenu accessible et utilisable) est retourné. KitKat permet également de filtrer le contenu avec le `MimeType`. Par exemple, le code ci-dessous filtre les résultats de l’image en spécifiant l’image `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

L’appel de `StartActivityForResult` lance l’interface utilisateur de SAF, que l’utilisateur peut ensuite parcourir pour choisir une image :

[![exemple de capture d’écran d’une application à l’aide de l’infrastructure d’accès au stockage pour naviguer jusqu’à une image](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Une fois que l’utilisateur a choisi une image, `OnActivityResult` retourne la `Android.Net.Uri` du fichier choisi. L’exemple de code ci-dessous affiche la sélection de l’image de l’utilisateur :

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

#### <a name="write-content-to-a-provider"></a>Écrire du contenu dans un fournisseur

En plus de charger du contenu à partir de l’interface utilisateur de SAF, KitKat vous permet également d’enregistrer le contenu dans n’importe quel `ContentProvider` qui implémente l’API `DocumentProvider`. L’enregistrement de contenu utilise un `Intent` avec `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

L’exemple de code ci-dessus charge l’interface utilisateur de SAF, permettant à l’utilisateur de modifier le nom de fichier et de sélectionner un répertoire pour héberger le nouveau fichier :

[![capture d’écran de l’utilisateur remplaçant le nom de fichier par NewDoc dans le répertoire des téléchargements](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quand l’utilisateur appuie sur **Enregistrer**, `OnActivityResult` reçoit le `Android.Net.Uri` du fichier nouvellement créé, qui est accessible avec `data.Data`. L’URI peut être utilisé pour diffuser des données dans le nouveau fichier :

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

Notez que [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
retourne un `System.IO.Stream`, de sorte que l’ensemble du processus de diffusion en continu peut être écrit dans .NET.

Pour plus d’informations sur le chargement, la création et la modification de contenu avec l’infrastructure d’accès au stockage, reportez-vous à la [documentation Android de l’infrastructure d’accès au stockage](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Impression

Le contenu d’impression est simplifié dans KitKat avec l’introduction des [services d’impression](xref:Android.PrintServices) et `PrintManager`. KitKat est également la première version de l’API à tirer pleinement parti des [API du service d’impression Cloud de Google](https://developers.google.com/cloud-print/) à l’aide de l' [application Google Cloud Print](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La plupart des appareils fournis avec KitKat téléchargent automatiquement l’application Google Cloud Print et le [plug-in du service d’impression HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)lorsqu’ils se connectent pour la première fois à WiFi. Un utilisateur peut vérifier les paramètres d’impression de son appareil en accédant à **paramètres > système > l’impression**:

[![exemple de capture d’écran de l’écran Paramètres d’impression](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> Bien que les API d’impression soient configurées pour fonctionner avec Google Cloud Print par défaut, Android permet toujours aux développeurs de préparer le contenu d’impression à l’aide des nouvelles API et de les envoyer à d’autres applications pour gérer l’impression.

#### <a name="printing-html-content"></a>Imprimer du contenu HTML

KitKat crée automatiquement un [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) pour une vue web avec `WebView.CreatePrintDocumentAdapter`. L’impression de contenu Web est un effort coordonné entre un [`WebViewClient`](xref:Android.Webkit.WebViewClient) qui attend le chargement du contenu HTML et permet à l’activité de faire en sorte que l’option d’impression soit disponible dans le menu options, et l’activité, qui attend que l’utilisateur sélectionne l’option d’impression et que c alls `Print`sur le `PrintManager`. Cette section décrit le programme d’installation de base requis pour imprimer le contenu HTML à l’écran.

Notez que le chargement et l’impression de contenu Web nécessite l’autorisation Internet :

[![définition de l’autorisation Internet dans les options de l’application](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Élément de menu imprimer

L’option d’impression s’affiche généralement dans le [menu options](https://developer.android.com/guide/topics/ui/menus.html#options-menu)de l’activité.
Le menu Options permet aux utilisateurs d’effectuer des actions sur une activité. Il se trouve dans le coin supérieur droit de l’écran et ressemble à ceci :

[![exemple de capture d’écran de l’élément de menu Imprimer dans le coin supérieur droit de l’écran](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

Des éléments de menu supplémentaires peuvent être définis dans le répertoire de *menu*sous *ressources*. Le code ci-dessous définit un exemple d’élément de menu appelé [Print](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

L’interaction avec le menu options de l’activité s’effectue par le biais des méthodes `OnCreateOptionsMenu` et `OnOptionsItemSelected`.
`OnCreateOptionsMenu` est l’endroit où vous pouvez ajouter de nouveaux éléments de menu, comme l’option imprimer, à partir du répertoire des ressources de *menu* .
`OnOptionsItemSelected` écoute l’utilisateur en sélectionnant l’option imprimer dans le menu, puis commence l’impression :

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

Le code ci-dessus définit également une variable appelée `dataLoaded` pour effectuer le suivi de l’état du contenu HTML. La `WebViewClient` affecte la valeur true à cette variable lorsque tout le contenu a été chargé, de sorte que l’activité sait ajouter l’élément de menu imprimer au menu options.

##### <a name="webviewclient"></a>WebViewClient

La tâche de l' `WebViewClient` consiste à s’assurer que les données du `WebView` sont entièrement chargées avant que l’option d’impression apparaisse dans le menu, avec la méthode `OnPageFinished`. `OnPageFinished` écoute le chargement du contenu Web et indique à l’activité de recréer son menu d’options avec `InvalidateOptionsMenu`:

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

`OnPageFinished` définit également la valeur de `dataLoaded` sur `true`, donc `OnCreateOptionsMenu` pouvez recréer le menu avec l’option d’impression en place.

##### <a name="printmanager"></a>PrintManager

L’exemple de code suivant imprime le contenu d’une `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` prend comme arguments : un nom pour le travail d’impression (« MyWebPage » dans cet exemple), un [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
qui génère le document d’impression à partir du contenu et [`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null` dans l’exemple ci-dessus). Vous pouvez spécifier `PrintAttributes` pour vous aider à disposer le contenu sur la page imprimée, bien que les attributs par défaut doivent gérer la plupart des scénarios.

L’appel de `Print` charge l’interface utilisateur d’impression, qui répertorie les options du travail d’impression. L’interface utilisateur donne aux utilisateurs la possibilité d’imprimer ou d’enregistrer le contenu HTML dans un fichier PDF, comme illustré par les captures d’écran ci-dessous :

[Capture d’écran ![de PrintHtmlActivity affichant le menu imprimer](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[Capture d’écran ![de PrintHtmlActivity affichant le menu enregistrer en tant que PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Matériel

KitKat ajoute plusieurs API pour prendre en charge les nouvelles fonctionnalités de l’appareil. Les plus notables sont l’émulation de carte basée sur l’hôte et la nouvelle `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Émulation de carte basée sur l’hôte dans NFC

L’émulation de carte basée sur l’hôte (HCE) permet aux applications de se comporter comme des cartes NFC ou des lecteurs de carte NFC sans avoir recours à l’élément sécurisé propriétaire du transporteur. Avant de configurer HCE, vérifiez que HCE est disponible sur l’appareil avec `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requiert que la fonctionnalité HCE et l’autorisation `Nfc` soient enregistrées avec l' `AndroidManifest.xml`de l’application :

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![de la définition de l’autorisation NFC dans les options de l’application](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Pour fonctionner, HCE doit être en mesure de s’exécuter en arrière-plan, et il doit démarrer lorsque l’utilisateur effectue une transaction NFC, même si l’application qui utilise HCE n’est pas en cours d’exécution. Nous pouvons le faire en écrivant le code HCE en tant que `Service`. Un service HCE implémente l’interface `HostApduService`, qui implémente les méthodes suivantes :

- *ProcessCommandApdu* : une unité de données de protocole d’application (APDU) est celle qui est envoyée entre le lecteur NFC et le service HCE. Cette méthode consomme un ADPU à partir du lecteur et retourne une unité de données en réponse.

- *OnDeactivated* : le `HostAdpuService` est désactivé lorsque le service HCE ne communique plus avec le lecteur NFC.

Un service HCE doit également être inscrit auprès du manifeste de l’application et être décoré avec les autorisations, le filtre d’intention et les métadonnées appropriés. Le code suivant est un exemple de `HostApduService` inscrit auprès du manifeste Android à l’aide de l’attribut `Service` (pour plus d’informations sur les attributs, reportez-vous au Guide Xamarin [utilisation du manifeste Android](~/android/platform/android-manifest.md) ) :

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

Le service ci-dessus offre un moyen pour le lecteur NFC d’interagir avec l’application, mais le lecteur NFC n’a toujours aucun moyen de savoir si ce service émule la carte NFC qu’il doit analyser. Pour aider le lecteur NFC à identifier le service, nous pouvons attribuer au service un *ID d’application unique (aide)* . Nous spécifions une aide, ainsi que d’autres métadonnées sur le service HCE, dans un fichier de ressources XML enregistré avec l’attribut `MetaData` (Voir l’exemple de code ci-dessus). Ce fichier de ressources spécifie un ou plusieurs filtres d’aide-chaînes d’identificateur unique au format hexadécimal qui correspondent aux aides d’un ou plusieurs périphériques de lecture NFC :

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

En plus des filtres d’aide, le fichier de ressources XML fournit également une description accessible à l’utilisateur du service HCE, spécifie un groupe d’aide (Payment application and « other ») et, dans le cas d’une application de paiement, une bannière 260x96 DP à afficher à l’utilisateur.

La configuration décrite ci-dessus fournit les blocs de construction de base d’une application qui émule une carte NFC. NFC lui-même nécessite plusieurs étapes supplémentaires et des tests supplémentaires pour configurer. Pour plus d’informations sur l’émulation de carte basée sur l’hôte, reportez-vous au [portail de documentation Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Pour plus d’informations sur l’utilisation de NFC avec Xamarin, consultez les [exemples XAMARIN NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sens

KitKat fournit l’accès aux capteurs de l’appareil par le biais d’un [`SensorManager`](xref:Android.Hardware.SensorManager).
Le `SensorManager` permet au système d’exploitation de planifier la remise d’informations de capteur à une application par lots, en préservant la durée de vie de la batterie.

KitKat est également fourni avec deux nouveaux types de capteur pour suivre les étapes de l’utilisateur. Ceux-ci sont basés sur accéléromètre et incluent :

- *StepDetector* -l’application est informée/réveillée lorsque l’utilisateur effectue une étape, et le détecteur fournit une valeur de temps pour le moment où l’étape s’est produite.

- *StepCounter* : effectue le suivi du nombre d’étapes que l’utilisateur a effectuées depuis l’inscription du capteur *jusqu’au redémarrage de l’appareil suivant*.

La capture d’écran ci-dessous illustre le compteur de l’étape en action :

[Capture d’écran![de l’application SensorsActivity affichant un compteur d’étapes](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Vous pouvez créer un `SensorManager` en appelant `GetSystemService(SensorService)` et en effectuant un cast du résultat en tant que `SensorManager`. Pour utiliser le compteur Step, appelez `GetDefaultSensor` sur le `SensorManager`. Vous pouvez inscrire le capteur et écouter les modifications apportées au nombre d’étapes à l’aide du [`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
interface, comme illustré par l’exemple de code ci-dessous :

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

`OnSensorChanged` est appelé si le nombre d’étapes est mis à jour pendant que l’application est au premier plan. Si l’application entre en arrière-plan ou si l’appareil est en veille, `OnSensorChanged` ne sera pas appelé ; Toutefois, les étapes continuent d’être comptées jusqu’à ce que `UnregisterListener` soit appelée.

Gardez à l’esprit que *la valeur du nombre d’étapes est cumulative pour toutes les applications qui inscrivent le capteur*. Cela signifie que même si vous désinstallez et réinstallez votre application et que vous initialisez la variable `count` à 0 au démarrage de l’application, la valeur signalée par le capteur restera le nombre total d’étapes effectuées pendant l’inscription du capteur, que ce soit par votre application ou une autre. Vous pouvez empêcher votre application d’être ajoutée au compteur de l’étape en appelant `UnregisterListener` sur le `SensorManager`, comme illustré dans le code ci-dessous :

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Le redémarrage de l’appareil réinitialise le nombre d’étapes à 0. Votre application nécessite du code supplémentaire pour s’assurer qu’elle signale un nombre précis pour l’application, quelles que soient les autres applications qui utilisent le capteur ou l’état de l’appareil.

> [!NOTE]
> Alors que l’API pour la détection d’étape et le comptage est fournie avec KitKat, tous les téléphones ne sont pas fournis avec le capteur. Vous pouvez vérifier si le capteur est disponible en exécutant `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`ou vérifier que la valeur renvoyée de `GetDefaultSensor` n’est pas `null`.

<a name="developer_tools" />

## <a name="developer-tools"></a>Outils de développement

### <a name="screen-recording"></a>Enregistrement à l’écran

KitKat comprend de nouvelles fonctionnalités d’enregistrement à l’écran qui permettent aux développeurs d’enregistrer des applications en action. L’enregistrement à l’écran est disponible via le client [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) , qui peut être téléchargé dans le cadre du Android SDK.

Pour enregistrer votre écran, connectez votre appareil ; Ensuite, localisez votre installation Android SDK, accédez au répertoire **Platform-Tools** et exécutez le client **ADB** :

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

La commande ci-dessus enregistre une vidéo de 3 minutes par défaut à la résolution par défaut de 4Mbps. Pour modifier la longueur, ajoutez l’indicateur *--Limit* .
Pour modifier la résolution, ajoutez l’indicateur *de taux de bits* . La commande suivante enregistre une vidéo de minute sur 8Mbps :

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Vous pouvez trouver votre vidéo sur votre appareil. celle-ci apparaîtra dans la Galerie une fois l’enregistrement terminé.

## <a name="other-kitkat-additions"></a>Autres ajouts KitKat

Outre les modifications décrites ci-dessus, KitKat vous permet d’effectuer les opérations suivantes :

- *Utiliser le mode plein écran* -KitKat introduit un nouveau [mode immersif](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) pour parcourir le contenu, jouer à des jeux et exécuter d’autres applications qui peuvent tirer parti d’une expérience plein écran.

- *Personnaliser les notifications* : obtenir des informations supplémentaires sur les notifications système avec le [`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . Cela vous permet de présenter les informations d’une manière différente dans votre application.

- *Ressources dessinables en miroir* -les ressources dessinables ont une nouvelle [`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  attribut qui indique au système de créer une version mise en miroir pour les images qui nécessitent un retournement pour les dispositions de gauche à droite.

- *Suspendre les animations* -suspendre et reprendre les animations créées avec la [`Animator`](xref:Android.Animation.Animator)
  .

- *Lire dynamiquement* les parties de l’interface utilisateur qui se mettent à jour de manière dynamique avec le nouveau texte sous la forme de « régions dynamiques » avec le nouveau [`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  attribut pour que le nouveau texte soit lu automatiquement en mode accessibilité.

- *Améliorez l’expérience audio* -rendez les pistes plus intenses avec les [`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  , recherchez le pic et le RMS d’un flux audio avec la [`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  et obtenir des informations à partir d’un [horodateur audio](xref:Android.Media.AudioTimestamp) pour faciliter la synchronisation audio-vidéo.

- *Synchroniser les ContentResolver à l’intervalle personnalisé* -KitKat ajoute une certaine variabilité au moment où une demande de synchronisation est effectuée. Synchroniser un `ContentResolver` à une heure ou un intervalle personnalisé en appelant `ContentResolver.RequestSync` et en passant un `SyncRequest`.

- *Distinguer les contrôleurs* : dans KitKat, les contrôleurs reçoivent des identificateurs entiers uniques qui sont accessibles par le biais de la propriété `ControllerNumber` de l’appareil. Cela permet de distinguer plus facilement les joueurs d’un jeu.

- *Contrôle à distance* -avec quelques modifications apportées côté matériel et logiciel, KitKat vous permet de mettre en place un appareil équipé d’un émetteur IR dans un contrôle à distance à l’aide de la `ConsumerIrService`et d’interagir avec les périphériques avec le nouveau [`RemoteController`](xref:Android.Media.RemoteController)
  Interfaces.

Pour plus d’informations sur les modifications de l’API ci-dessus, consultez la vue d’ensemble des [API Google Android 4,4](https://developer.android.com/about/versions/android-4.4.html) .

## <a name="summary"></a>Récapitulatif

Cet article a présenté quelques-unes des nouvelles API disponibles dans Android 4,4 (API de niveau 19) et a abordé les meilleures pratiques lors de la transition d’une application vers KitKat. Il a décrit les modifications apportées aux API qui affectent l’expérience utilisateur, y compris l' *infrastructure de transition* et de nouvelles options pour *les thèmes*. Ensuite, il a introduit l' *infrastructure d’accès au stockage* et la classe `DocumentsProvider`, ainsi que les nouvelles API d' *impression*. Il a exploré l' *émulation de carte basée sur l’hôte NFC* et comment travailler avec des *capteurs de faible puissance*, y compris deux nouveaux capteurs pour suivre les étapes de l’utilisateur. Enfin, il a démontré la capture de démonstrations en temps réel des applications avec *l’enregistrement à l’écran*et a fourni une liste détaillée des modifications et ajouts de l’API KitKat.

## <a name="related-links"></a>Liens associés

- [Exemple KitKat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [API Android 4,4](https://developer.android.com/about/versions/android-4.4.html)
- [KitKat Android](https://developer.android.com/about/versions/kitkat.html)
