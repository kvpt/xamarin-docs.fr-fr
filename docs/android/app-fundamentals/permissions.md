---
title: Autorisations dans Xamarin. Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1426054b60d182f7f40bf3c4b0bf69b2287ad57e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509408"
---
# <a name="permissions-in-xamarinandroid"></a>Autorisations dans Xamarin. Android


## <a name="overview"></a>Présentation

Les applications Android s’exécutent dans leur propre bac à sable (sandbox) et, pour des raisons de sécurité, n’ont pas accès à certaines ressources système ou matériel sur l’appareil. L’utilisateur doit explicitement accorder une autorisation à l’application avant de pouvoir utiliser ces ressources. Par exemple, une application ne peut pas accéder au GPS sur un appareil sans autorisation explicite de l’utilisateur. Android lève une `Java.Lang.SecurityException` si une application tente d’accéder à une ressource protégée sans autorisation.

Les autorisations sont déclarées dans **fichier AndroidManifest. xml** par le développeur de l’application lors du développement de l’application. Android dispose de deux flux de travail différents pour obtenir le consentement de l’utilisateur pour ces autorisations:
 
* Pour les applications qui ont ciblé Android 5,1 (niveau d’API 22) ou une date antérieure, la demande d’autorisation a eu lieu lors de l’installation de l’application. Si l’utilisateur n’a pas accordé les autorisations, l’application n’est pas installée. Une fois l’application installée, il n’existe aucun moyen de révoquer les autorisations sauf en désinstallant l’application.
* À partir de la 6,0 Android (niveau d’API 23), les utilisateurs ont eu davantage de contrôle sur les autorisations. ils peuvent accorder ou révoquer des autorisations tant que l’application est installée sur l’appareil. Cette capture d’écran montre les paramètres d’autorisation de l’application Google contacts. Elle répertorie les différentes autorisations et permet à l’utilisateur d’activer ou de désactiver les autorisations:

![Écran exemples d’autorisations](permissions-images/01-permissions-check.png) 

Les applications Android doivent vérifier au moment de l’exécution pour voir si elles sont autorisées à accéder à une ressource protégée. Si l’application n’a pas d’autorisation, elle doit faire des demandes à l’aide des nouvelles API fournies par l’Android SDK pour que l’utilisateur accorde les autorisations. Les autorisations sont divisées en deux catégories:

* **Autorisations normales** &ndash; Il s’agit d’autorisations qui présentent un faible risque pour la sécurité ou la confidentialité de l’utilisateur. Android 6,0 accorde automatiquement des autorisations normales au moment de l’installation. Consultez la documentation Android pour obtenir une [liste complète des autorisations normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Autorisations dangereuses** &ndash; Contrairement aux autorisations normales, les autorisations dangereuses sont celles qui protègent la sécurité ou la confidentialité de l’utilisateur. Celles-ci doivent être explicitement accordées par l’utilisateur. L’envoi ou la réception d’un message SMS est un exemple d’action nécessitant une autorisation dangereuse.

> [!IMPORTANT]
> La catégorie à laquelle une autorisation appartient peut changer au fil du temps.  Il est possible qu’une autorisation qui a été catégorisée comme une autorisation «normale» puisse être élevée dans les futurs niveaux d’API à une autorisation dangereuse.

Les autorisations dangereuses sont encore sous-divisées en [_groupes d’autorisations_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Un groupe d’autorisations contiendra des autorisations liées de manière logique. Lorsque l’utilisateur accorde l’autorisation à un membre d’un groupe d’autorisations, Android accorde automatiquement l’autorisation à tous les membres de ce groupe. Par exemple, le [`STORAGE`](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) groupe d’autorisations contient à `WRITE_EXTERNAL_STORAGE` la `READ_EXTERNAL_STORAGE` fois les autorisations et. Si l’utilisateur accorde l’autorisation `READ_EXTERNAL_STORAGE`à, l' `WRITE_EXTERNAL_STORAGE` autorisation est alors automatiquement accordée en même temps.

Avant de demander une ou plusieurs autorisations, il est recommandé de fournir un raisonnement sur la raison pour laquelle l’application requiert l’autorisation avant de demander l’autorisation. Une fois que l’utilisateur comprend le raisonnement, l’application peut demander l’autorisation à l’utilisateur. En maîtrisant le raisonnement, l’utilisateur peut prendre une décision informée s’il souhaite accorder l’autorisation et comprendre les répercussions dans le cas contraire. 

L’intégralité du flux de travail de vérification et de demande des autorisations est appelée vérification des _autorisations au moment_ de l’exécution et peut être résumée dans le diagramme suivant: 

[![Organigramme de la vérification des autorisations au moment de l’exécution](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

La bibliothèque de prise en charge Android reportne certaines des nouvelles API pour les autorisations sur les versions antérieures d’Android. Ces API en retard vérifient automatiquement la version d’Android sur l’appareil. il n’est donc pas nécessaire d’effectuer une vérification de niveau d’API à chaque fois.  

Ce document explique comment ajouter des autorisations à une application Xamarin. Android et comment les applications qui ciblent Android 6,0 (niveau d’API 23) ou supérieur doivent effectuer une vérification d’autorisation au moment de l’exécution.


> [!NOTE]
> Il est possible que les autorisations pour le matériel affectent la manière dont l’application est filtrée par Google Play. Par exemple, si l’application requiert une autorisation pour l’appareil photo, Google Play n’affichera pas l’application dans le Google Play Store sur un appareil sur lequel aucune caméra n’est installée.


<a name="requirements" />

## <a name="requirements"></a>Configuration requise

Il est fortement recommandé que les projets Xamarin. Android incluent le package NuGet [Xamarin. Android. support. compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) . Ce package rétroporter des API spécifiques d’autorisation à des versions antérieures d’Android, fournissant une interface commune sans avoir à vérifier en permanence la version d’Android sur laquelle l’application s’exécute.


## <a name="requesting-system-permissions"></a>Demande d’autorisations système

La première étape de l’utilisation des autorisations Android consiste à déclarer les autorisations dans le fichier manifeste Android. Cela doit être fait quel que soit le niveau d’API ciblé par l’application.

Les applications qui ciblent Android 6,0 ou version ultérieure ne peuvent pas supposer que, étant donné que l’utilisateur a accordé une autorisation à un moment donné dans le passé, cette autorisation sera valide la prochaine fois. Une application qui cible Android 6,0 doit toujours effectuer une vérification d’autorisation Runtime. Les applications qui ciblent Android 5,1 ou une partie antérieure n’ont pas besoin d’effectuer une vérification des autorisations au moment de l’exécution.

> [!NOTE]
> Les applications doivent uniquement demander les autorisations dont elles ont besoin.


### <a name="declaring-permissions-in-the-manifest"></a>Déclaration d’autorisations dans le manifeste

Les autorisations sont ajoutées à **fichier AndroidManifest. xml** avec l' `uses-permission` élément. Par exemple, si une application doit localiser la position de l’appareil, elle requiert des autorisations de localisation fine et de cours. Les deux éléments suivants sont ajoutés au manifeste: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il est possible de déclarer les autorisations à l’aide de la prise en charge des outils intégrée à Visual Studio:

1. Double-cliquez sur **Propriétés** dans le **Explorateur de solutions** , puis sélectionnez l’onglet **manifeste Android** dans la fenêtre Propriétés:

    [![Autorisations requises dans l’onglet manifeste Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Si l’application ne dispose pas déjà d’un fichier AndroidManifest. xml, **cliquez sur aucun fichier AndroidManifest. xml trouvé. Cliquez pour en ajouter** un comme indiqué ci-dessous:

    [![Aucun message fichier AndroidManifest. Xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Sélectionnez les autorisations dont votre application a besoin dans la liste des **autorisations requises** et enregistrez:

    [![Exemples d’autorisations d’appareil photo sélectionnées](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Il est possible de déclarer les autorisations à l’aide de la prise en charge des outils intégrée à Visual Studio pour Mac:

1. Double-cliquez sur le projet dans le **panneau solutions** , puis sélectionnez **Options > générer > application Android**:

    [![Section autorisations requises affichées](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Cliquez sur le bouton **Ajouter un manifeste Android** si le projet n’a pas encore **fichier AndroidManifest. xml**:

    [![Le manifeste Android du projet est manquant](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Sélectionnez les autorisations dont votre application a besoin dans la liste des **autorisations requises** , puis cliquez sur **OK**:

    [![Exemples d’autorisations d’appareil photo sélectionnées](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin. Android ajoute automatiquement des autorisations au moment de la génération pour déboguer les builds. Cela facilite le débogage de l’application. En particulier, deux autorisations notables `INTERNET` sont `READ_EXTERNAL_STORAGE`et. Ces autorisations définies automatiquement ne semblent pas être activées dans la liste des **autorisations requises** . Toutefois, les versions release n’utilisent que les autorisations définies explicitement dans la liste des **autorisations requises** . 

Pour les applications qui ciblent Android 5.1 (niveau d’API 22) ou une valeur inférieure, il n’y a rien d’autre à faire. Les applications qui s’exécutent sur Android 6,0 (API 23 niveau 23) ou une version ultérieure doivent passer à la section suivante sur la façon d’effectuer des vérifications d’autorisation au moment de l’exécution. 


### <a name="runtime-permission-checks-in-android-60"></a>Vérifications des autorisations d’exécution dans Android 6,0

La `ContextCompat.CheckSelfPermission` méthode (disponible avec la bibliothèque de prise en charge Android) est utilisée pour vérifier si une autorisation spécifique a été accordée. Cette méthode retourne une [`Android.Content.PM.Permission`](xref:Android.Content.PM.Permission) énumération qui a l’une des deux valeurs suivantes:

* **`Permission.Granted`** &ndash; L’autorisation spécifiée a été accordée.
* **`Permission.Denied`** &ndash; L’autorisation spécifiée n’a pas été accordée.

Cet extrait de code est un exemple de vérification de l’autorisation de l’appareil photo dans une activité: 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

Il est recommandé d’informer l’utilisateur de la raison pour laquelle une autorisation est nécessaire pour une application afin de pouvoir prendre une décision en connaissance de cause. Par exemple, une application qui prend des photos et des balises géospatiales. Il est clair pour l’utilisateur que l’autorisation de l’appareil photo est nécessaire, mais il n’est peut-être pas évident que l’application a également besoin de l’emplacement de l’appareil. Le raisonnement doit afficher un message pour aider l’utilisateur à comprendre pourquoi l’autorisation d’emplacement est souhaitable et que l’autorisation de l’appareil photo est nécessaire.

La `ActivityCompat.ShouldShowRequestPermissionRationale` méthode est utilisée pour déterminer si la logique doit être présentée à l’utilisateur. Cette méthode retournera `true` si le raisonnement pour une autorisation donnée doit être affiché. Cette capture d’écran montre un exemple de snackbar affiché par une application qui explique pourquoi l’application doit connaître l’emplacement de l’appareil:

![Raisonnement pour l’emplacement](permissions-images/07-rationale-snackbar.png) 

Si l’utilisateur accorde l’autorisation, la `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` méthode doit être appelée. Cette méthode requiert les paramètres suivants:

* **activité** &ndash; Il s’agit de l’activité qui demande les autorisations et qui doit être informée par Android des résultats.
* **autorisations** &ndash; Liste des autorisations demandées.
* **requestCode** Valeur entière qui est utilisée pour faire correspondre les résultats de la demande d’autorisation à `RequestPermissions` un appel. &ndash; Cette valeur doit être supérieure à zéro.

Cet extrait de code est un exemple des deux méthodes qui ont été abordées. Tout d’abord, une vérification est effectuée pour déterminer si le raisonnement d’autorisation doit être affiché. Si le raisonnement doit être affiché, un snackbar s’affiche avec le raisonnement. Si l’utilisateur clique sur **OK** dans snackbar, l’application demande les autorisations. Si l’utilisateur n’accepte pas le raisonnement, l’application ne doit pas continuer à demander des autorisations. Si le raisonnement n’est pas affiché, l’activité demande l’autorisation:

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission`peut être appelée même si l’utilisateur a déjà accordé l’autorisation. Les appels suivants ne sont pas nécessaires, mais ils offrent à l’utilisateur la possibilité de confirmer (ou de révoquer) l’autorisation. Lorsque `RequestPermission` est appelé, le contrôle est transmis au système d’exploitation, qui affiche une interface utilisateur pour accepter les autorisations:  

![Boîte de dialogue permssion](permissions-images/08-location-permission-dialog.png)

Une fois l’utilisateur terminé, Android retourne les résultats à l’activité à l’aide d’une méthode `OnRequestPermissionResult`de rappel,. Cette méthode fait partie de l’interface `ActivityCompat.IOnRequestPermissionsResultCallback` qui doit être implémentée par l’activité. Cette interface a une méthode unique, `OnRequestPermissionsResult`, qui sera appelée par Android pour informer l’activité des choix de l’utilisateur. Si l’utilisateur a accordé l’autorisation, l’application peut continuer et utiliser la ressource protégée. Vous trouverez ci-dessous un `OnRequestPermissionResult` exemple de la façon d’implémenter: 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permission_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  


## <a name="summary"></a>Récapitulatif

Ce guide a expliqué comment ajouter et vérifier les autorisations dans un appareil Android. Les différences dans la façon dont les autorisations fonctionnent entre les anciennes applications Android (niveau d’API < 23) et les nouvelles applications Android (niveau d’API > 22). Il a expliqué comment effectuer des vérifications d’autorisation au moment de l’exécution dans Android 6,0.


## <a name="related-links"></a>Liens associés

- [Liste des autorisations normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Exemple d’application d’autorisations Runtime](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Gestion des autorisations dans Xamarin. Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
