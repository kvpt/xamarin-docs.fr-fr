---
title: Performances des applications Xamarin.Android
description: De nombreuses techniques permettent d’accroître les performances des applications générées avec Xamarin.Android. Ensemble, ces techniques peuvent considérablement réduire la charge de travail d’un processeur, de même que la quantité de mémoire consommée par une application. Cet article décrit et explique ces techniques.
ms.prod: xamarin
ms.assetid: dc2e27f2-7f71-4d57-9cf9-165528276613
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 88e1acecdc96af596a0151bbd3f64dc4547d4cce
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753821"
---
# <a name="xamarinandroid-performance"></a>Performances des applications Xamarin.Android

_De nombreuses techniques permettent d’accroître les performances des applications générées avec Xamarin.Android. Ensemble, ces techniques peuvent considérablement réduire la charge de travail d’un processeur, de même que la quantité de mémoire consommée par une application. Cet article décrit et explique ces techniques._

## <a name="performance-overview"></a>Vue d'ensemble des performances

Le mauvais niveau de performance d’une application se présente de plusieurs façons. L’application semble ne pas répondre, le défilement de l’affichage est ralenti et la durée de vie de la batterie de l’appareil réduite. Toutefois, l’optimisation des performances implique davantage de choses que l’implémentation d’un code efficace. L’expérience utilisateur liée au niveau de performance de l’application doit également être prise en compte. Par exemple, le fait de garantir que l’exécution de certaines opérations ne va pas empêcher l’utilisateur d’effectuer d’autres activités peut améliorer son expérience de l’application.

De nombreuses techniques permettent d’accroître les performances des applications générées avec Xamarin.Android (y compris du point de vue de l’utilisateur). Elles comprennent :

- [Optimiser les hiérarchies de dispositions](#optimizelayout)
- [Optimiser les affichages de liste](#optimizelistviews)
- [Supprimer les Gestionnaires d’événements dans les activités](#removeeventhandlers)
- [Limite la durée de vie des services](#limitservices)
- [Libérer des ressources après notification](#releaseresources)
- [Libérer des ressources lorsque l’interface utilisateur est masquée](#releaseresourcesuihidden)
- [Optimiser les ressources d’images](#optimizeimages)
- [Supprimer les ressources d’images non utilisées](#disposeimages)
- [Éviter l’arithmétique à virgule flottante](#avoidfloats)
- [Faire disparaître les boîtes de dialogue](#dismissdialogs)

> [!NOTE]
> Avant de lire cet article, lisez d’abord [Niveau de performance multiplateforme](~/cross-platform/deploy-test/memory-perf-best-practices.md), qui décrit les techniques spécifiques indépendantes des plateformes qui permettent d’améliorer l’utilisation de la mémoire et le niveau de performance des applications générées à l’aide de la plateforme Xamarin.

<a name="optimizelayout" />

## <a name="optimize-layout-hierarchies"></a>Optimiser les hiérarchies de dispositions

Chaque disposition ajoutée à une application requiert une initialisation, une mise en page et un dessin. La passe de disposition peut être coûteuse en cas d’imbrication d’instances de [`LinearLayout`](xref:Android.Widget.LinearLayout) qui utilisent le paramètre `weight`, car chaque enfant sera mesuré deux fois. L’utilisation d’instances imbriquées de `LinearLayout` peut conduire à une hiérarchie d’affichage profonde, ce qui peut entraîner une dégradation des performances pour les dispositions compressées plusieurs fois, comme dans un [`ListView`](xref:Android.Widget.ListView). Il est par conséquent important que ces dispositions soient optimisées, d’autant que les bénéfices sur les performances seront multipliés.

Prenons par exemple le [`LinearLayout`](xref:Android.Widget.LinearLayout) d’une ligne d’affichage de liste ayant une icône, un titre et une description. Le `LinearLayout` contiendra un [`ImageView`](xref:Android.Widget.ImageView) et un `LinearLayout` vertical contenant deux instances de [`TextView`](xref:Android.Widget.TextView) :

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="0dip"
        android:layout_weight="1"
        android:layout_height="fill_parent">
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:text="Mei tempor iuvaret ad." />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:text="Lorem ipsum dolor sit amet." />
    </LinearLayout>
</LinearLayout>
```

Cette disposition a une profondeur de 3 niveaux, ce qui est inutile lorsqu’elle est compressée pour chaque ligne [`ListView`](xref:Android.Widget.ListView). Elle peut toutefois être améliorée par l’aplanissement de la disposition, comme illustré dans l’exemple de code suivant :

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentTop="true"
        android:layout_alignParentBottom="true"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <TextView
        android:id="@+id/secondLine"
        android:layout_width="fill_parent"
        android:layout_height="25dip"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:singleLine="true"
        android:ellipsize="marquee"
        android:text="Lorem ipsum dolor sit amet." />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:layout_above="@id/secondLine"
        android:layout_alignWithParentIfMissing="true"
        android:gravity="center_vertical"
        android:text="Mei tempor iuvaret ad." />
</RelativeLayout>
```

La hiérarchie à  3 niveaux est passée à 2 niveaux et un seul [`RelativeLayout`](xref:Android.Widget.RelativeLayout) a remplacé deux [`LinearLayout`](xref:Android.Widget.LinearLayout). Les performances seront considérablement améliorées lors de la compression de la disposition pour chaque ligne [`ListView`](xref:Android.Widget.ListView).

<a name="optimizelistviews" />

## <a name="optimize-list-views"></a>Optimiser les affichages de liste

Les utilisateurs attendent un défilement régulier et des temps de chargement rapides pour les instances de [`ListView`](xref:Android.Widget.ListView). Toutefois, les performances de défilement peuvent être réduites lorsque chaque ligne d’affichage de liste contient des hiérarchies d’affichage profondément imbriquées ou lorsque les lignes d’affichage de liste contiennent des dispositions complexes. Toutefois, différentes techniques permettent d’éviter la dégradation des performances de `ListView` :

- Réutiliser des affichages de ligne. Pour plus d’informations, consultez [Réutiliser des affichages de ligne](#reuserowviews).
- Aplatir les dispositions, lorsque cela est possible.
- Mettre en cache le contenu de ligne qui est récupéré depuis un service web.
- Éviter la mise à l’échelle des images.

Utilisées conjointement, ces techniques peuvent contribuer au défilement régulier des instances de [`ListView`](xref:Android.Widget.ListView).

<a name="reuserowviews" />

### <a name="reuse-row-views"></a>Réutiliser des affichages de ligne

Lorsque des centaines de lignes d’un [`ListView`](xref:Android.Widget.ListView) sont affichées, le fait de créer des centaines d’objets [`View`](xref:Android.Views.View) utiliserait inutilement de la mémoire, alors que seulement un petit nombre d'entre eux sont affichés simultanément sur l’écran. Au lieu de cela, seuls les objets `View` visibles dans les lignes à l’écran peuvent être chargés en mémoire, le **contenu** étant chargé dans ces objets réutilisés. Cela évite l’instanciation de centaines d’objets supplémentaires et permet d’économiser à la fois du temps et de la mémoire.

Par conséquent, lorsqu’une ligne disparaît de l’écran, son affichage peut être placé dans une file d’attente en vue de sa réutilisation, comme illustré dans l’exemple de code suivant :

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Lorsque l’utilisateur utilise le défilement, l’instance de [`ListView`](xref:Android.Widget.ListView) appelle la substitution `GetView` pour demander l’affichage de nouveaux affichages. Si des affichages sont disponibles, elle passe un affichage non utilisé dans le paramètre `convertView`. Si cette valeur est `null`, le code crée une nouvelle instance de [`View`](xref:Android.Views.View) ; sinon les propriétés `convertView` peuvent être réinitialisées et réutilisées.

Pour plus d’informations, consultez [Réutilisation d’un affichage de ligne](~/android/user-interface/layouts/list-view/populating.md#row-view-re-use) dans [Remplissage d’un affichage de liste avec des données](~/android/user-interface/layouts/list-view/populating.md).

<a name="removeeventhandlers" />

## <a name="remove-event-handlers-in-activities"></a>Supprimer les gestionnaires d’événements dans les activités

Lorsqu’une activité est détruite dans le runtime Android, il est possible qu’elle soit toujours active dans le runtime Mono. Par conséquent, supprimez les gestionnaires d’événements pour les objets externes dans `Activity.OnPause` afin d’empêcher le runtime de conserver une référence à une activité qui a été détruite.

Dans une activité, déclarez les gestionnaires d’événements au niveau de la classe :

```csharp
EventHandler<UpdatingEventArgs> service1UpdateHandler;
```

Implémentez ensuite les gestionnaires dans l’activité, comme dans `OnResume` :

```csharp
service1UpdateHandler = (object s, UpdatingEventArgs args) => {
    this.RunOnUiThread (() => {
        this.updateStatusText1.Text = args.Message;
    });
};
App.Current.Service1.Updated += service1UpdateHandler;
```

Lorsque l’activité quitte l’état d’exécution, `OnPause` est appelé. Dans l’implémentation `OnPause`, supprimez les gestionnaires comme suit :

```csharp
App.Current.Service1.Updated -= service1UpdateHandler;
```

<a name="limitservices" />

## <a name="limit-the-lifespan-of-services"></a>Limiter la durée de vie des services

Au démarrage d’un service, Android maintient l’exécution du processus du service. De ce fait, le processus est onéreux car sa mémoire ne peut pas être paginée, ni utilisée ailleurs. Le fait de maintenir un service en exécution alors qu’il n’est pas requis augmente par conséquent le risque qu’une application montre des performances dégradées en raison des contraintes de mémoire. Le basculement entre les applications peut également être moins efficace car le nombre de processus qu’Android peut mettre en cache est réduit.

La durée de vie d’un service peut être limitée à l’aide d’un `IntentService`, qui se termine lui-même dès qu’il a traité l’intention qui l’a démarré.

<a name="releaseresources" />

## <a name="release-resources-when-notified"></a>Libérer des ressources après notification

Pendant le cycle de vie des applications, le rappel [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) envoie une notification lorsque la mémoire de l’appareil est faible. Ce rappel doit être implémenté pour écouter les notifications de niveau de mémoire suivantes :

- [`TrimMemoryRunningModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningModerate) : l’application *peut* vouloir libérer des ressources non nécessaires.
- [`TrimMemoryRunningLow`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningLow) : l’application *devrait* libérer des ressources non nécessaires.
- [`TrimMemoryRunningCritical`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningCritical) : l’application *devrait* libérer autant de processus non critiques qu’elle le peut.

En outre, lorsque le processus de l’application est mis en cache, les notifications de niveau de mémoire suivantes peuvent être reçues par le rappel [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) :

- [`TrimMemoryBackground`](xref:Android.Content.ComponentCallbacks2.TrimMemoryBackground) : libérer les ressources qui peuvent rapidement et efficacement être regénérer si l’utilisateur retourne à l’application.
- [`TrimMemoryModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryModerate) : la libération de ressources peut aider le système à garder en cache d’autres processus pour de meilleures performances globales.
- [`TrimMemoryComplete`](xref:Android.Content.ComponentCallbacks2.TrimMemoryComplete) : le processus d’application va bientôt être interrompu si de la mémoire supplémentaire n’est pas récupérée.

La réponse aux notifications doit être la libération de ressources en fonction du niveau reçu.

<a name="releaseresourcesuihidden" />

## <a name="release-resources-when-the-user-interface-is-hidden"></a>Libérer des ressources lorsque l’interface utilisateur est masquée

Libérez les ressources utilisées par l’interface utilisateur de l’application dès que l’utilisateur navigue vers une autre application, car celle-ci peut augmenter considérablement la capacité d’Android en termes de processus mis en cache, qui à son tour peut avoir un impact sur la qualité de l’expérience utilisateur.

Pour recevoir une notification lorsque l’utilisateur quitte l’interface utilisateur, vous devez implémenter le rappel [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) dans les classes `Activity` et écouter le niveau [`TrimMemoryUiHidden`](xref:Android.Content.ComponentCallbacks2.TrimMemoryUiHidden), qui indique que l’interface utilisateur est masquée. Cette notification est reçue uniquement lorsque *tous* les composants de l’interface utilisateur de l’application sont masqués de la vue de l’utilisateur. La libération des ressources de l’interface utilisateur lorsque cette notification est reçue garantit que si l’utilisateur revient d’une autre activité de l’application, les ressources de l’interface utilisateur sont toujours disponibles pour rapidement reprendre l’activité.

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Optimiser les ressources d’images

Les images font partie des ressources les plus lourdes utilisées par les applications, et sont souvent capturées à des résolutions élevées. Par conséquent, affichez les images à la résolution requise pour l’écran de l’appareil. Si la résolution de l’image est supérieure à celle de l’écran, elle doit être réduite.

Pour plus d’informations, consultez [Optimiser les ressources d’images](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) dans le guide [Performances entre plateformes](~/cross-platform/deploy-test/memory-perf-best-practices.md).

<a name="disposeimages" />

## <a name="dispose-of-unused-image-resources"></a>Supprimer les ressources d’images non utilisées

Pour utiliser moins de mémoire, il est judicieux de supprimer les grandes ressources d’images qui ne sont plus nécessaires. Toutefois, il est important de s’assurer que les images sont correctement supprimées. Au lieu d’utiliser une invocation `.Dispose()` explicite, vous pouvez tirer parti des instructions [using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) pour garantir l’utilisation correcte des objets `IDisposable`. 

Par exemple, la classe [Bitmap](xref:Android.Graphics.Bitmap) implémente `IDisposable`. L’inclusion dans un wrapper de l’instanciation d’un objet `BitMap` dans un bloc `using` permet de s’assurer qu’il est supprimé correctement à la sortie du bloc :

```csharp
using (Bitmap smallPic = BitmapFactory.DecodeByteArray(smallImageByte, 0, smallImageByte.Length))
{
    // Use the smallPic bit map here
}
```

Pour plus d’informations sur la libération des ressources pouvant être supprimées, consultez [Libérer des ressources IDisposable](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable).  

<a name="avoidfloats" />

## <a name="avoid-floating-point-arithmetic"></a>Éviter l’arithmétique à virgule flottante

Sur les appareils Android, l’arithmétique à virgule flottante est environ 2 fois plus lente que l’arithmétique sur les entiers. Par conséquent, remplacez l’arithmétique à virgule flottante par l’arithmétique sur les entiers, si possible. Toutefois, il n’y a pas de différence de temps d’exécution entre l’arithmétique `float` et l’arithmétique `double` sur du matériel récent.

> [!NOTE]
> Même pour l’arithmétique sur les entiers, certains processeurs ne disposent pas de fonctionnalités de division du matériel. Par conséquent, les opérations de division et de modulo entier sont souvent exécutées dans le logiciel.

<a name="dismissdialogs" />

## <a name="dismiss-dialogs"></a>Faire disparaître les boîtes de dialogue

Lorsque vous utilisez la classe [`ProgressDialog`](xref:Android.App.ProgressDialog) (ou toute boîte de dialogue ou alerte), au lieu d’appeler la méthode [`Hide`](xref:Android.App.Dialog.Hide*) lorsque l’objet de la boîte de dialogue est terminé, appelez la méthode [`Dismiss`](xref:Android.App.Dialog.Dismiss*). Dans le cas contraire, la boîte de dialogue sera toujours active et provoquera une fuite de l’activité en maintenant une référence à celle-ci.

## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez découvert les techniques qui permettent d’accroître les performances des applications générées avec Xamarin.Android. Ensemble, ces techniques peuvent considérablement réduire la charge de travail d’un processeur, de même que la quantité de mémoire consommée par une application.

## <a name="related-links"></a>Liens associés

- [Performances entre plateformes](~/cross-platform/deploy-test/memory-perf-best-practices.md)
