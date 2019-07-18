---
title: 'Xamarin.Essentials: Connectivité'
description: La classe Connectivité dans Xamarin.Essentials vous permet de surveiller les modifications des conditions réseau de l’appareil, l’accès au réseau actuel et le mode de connexion actuel.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 01/08/2019
ms.custom: video
ms.openlocfilehash: c70510f7b47f93c6119532b6a1c06f6c2e9e56ea
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855757"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: Connectivité

La classe **Connectivité** vous permet de surveiller les modifications des conditions réseau de l’appareil, l’accès au réseau actuel et le mode de connexion actuel.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **Connectivité**, la configuration suivante spécifique à la plateforme est requise.

# <a name="androidtabandroid"></a>[Android](#tab/android)

L’autorisation `AccessNetworkState` est obligatoire, et doit être configurée dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** et ajoutez ce qui suit dans le nœud du **manifeste**.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Ou cliquez avec le bouton droit sur le projet Android et ouvrez les propriétés du projet. Sous **Manifeste Android** recherchez la zone **Autorisations requises :** et vérifiez l’autorisation **État d’accès réseau**. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Aucune configuration supplémentaire n’est requise.

-----

## <a name="using-connectivity"></a>Utilisation de Connectivité

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Vérifier l’accès réseau actuel :

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[L’accès réseau](xref:Xamarin.Essentials.NetworkAccess) est réparti dans les catégories suivantes :

* **Internet** : accès local et Internet.
* **ConstrainedInternet** : accès Internet limité. Indique la connectivité au portail captive, où un accès local à un portail web est fourni, mais l’accès à Internet nécessite que des informations d’identification spécifiques soient fournies via un portail.
* **Local** : accès au réseau local uniquement.
* **Aucun** : aucune connectivité n’est disponible.
* **Inconnu** : impossible de déterminer la connectivité Internet.

Vous pouvez vérifier le type de [profil de connexion](xref:Xamarin.Essentials.ConnectionProfile) utilisé activement par l’appareil avec ce qui suit :

```csharp
var profiles = Connectivity.ConnectionProfiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Dès que le profil de connexion ou l’accès réseau change, vous pouvez recevoir un événement lors du déclenchement :

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs e)
    {
        var access = e.NetworkAccess;
        var profiles = e.ConnectionProfiles;
    }
}
```

## <a name="limitations"></a>Limitations

Il est important de noter qu’il est possible que `Internet` soit signalé par `NetworkAccess` mais l’accès complet sur le web n’est pas disponible. En raison du fonctionnement de la connectivité sur chaque plateforme, seul le fait qu’une connexion est disponible peut être garanti. Par exemple, l’appareil peut être connecté à un réseau Wi-Fi, mais le routeur est déconnecté d’Internet. Dans cet exemple, Internet peut être signalé, mais une connexion active n’est pas disponible.

## <a name="api"></a>API

* [Code source Connectivité](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentation de l’API Connectivité](xref:Xamarin.Essentials.Connectivity)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Connectivity-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
