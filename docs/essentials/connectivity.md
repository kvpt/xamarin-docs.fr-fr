---
title : " Xamarin.Essentials : Connectivity" Description : "la classe de connectivité de vous Xamarin.Essentials permet de surveiller les modifications apportées aux conditions réseau de l’appareil, de vérifier l’accès au réseau actuel et de savoir comment il est actuellement connecté.
ms. AssetID : E1B1F152-B1D5-4227-965E-C0AEBF528F49 auteur : jamesmontemagno ms. Author : Jamont ms. Date : 01/08/2019 ms. Custom : Video No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: Connectivité

La classe **Connectivité** vous permet de surveiller les modifications des conditions réseau de l’appareil, l’accès au réseau actuel et le mode de connexion actuel.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **Connectivité**, la configuration suivante spécifique à la plateforme est requise.

# <a name="android"></a>[Android](#tab/android)

L’autorisation `AccessNetworkState` est obligatoire, et doit être configurée dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **fichier AndroidManifest. xml** dans le dossier **Propriétés** et ajoutez le code suivant dans le nœud **manifeste** .

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous **Manifeste Android** recherchez la zone **Autorisations requises :** et vérifiez l’autorisation **État d’accès réseau**. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

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
