---
title: Xamarin Live Reload (préversion)
description: Consultez les modifications apportées à votre XAML en direct, sans nécessiter d’autre compilation et déploiement.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
ms.openlocfilehash: a5a5a9acf47603601461660df689a7a5fa6aee00
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728341"
---
# <a name="xamarin-live-reload-preview"></a>Xamarin Live Reload (préversion)

> [!NOTE]
> La version préliminaire de Xamarin Live reload est terminée et nous souhaitons remercier tout le monde pour vos commentaires et commentaires. Lisez notre feuille de [route](https://docs.microsoft.com/visualstudio/productinfo/vs-roadmap) pour Visual Studio 2019 pour en savoir plus sur les nouvelles fonctionnalités de productivité sur lesquelles nous travaillons pour Xamarin. Forms. Cette extension reste disponible pour Visual Studio 2017, mais ne reçoit pas de mises à jour ultérieures.

Le rechargement Xamarin Live vous permet d' **apporter des modifications à votre code XAML et de voir qu’ils sont reflétés, sans nécessiter une autre compilation et un nouveau déploiement**. Toutes les modifications apportées à votre code XAML seront redéployées lors de l’enregistrement et reflétées sur votre cible de déploiement.

## <a name="requirements"></a>Configuration requise pour

* [Visual Studio 2017 version 15,7 ou ultérieure](https://visualstudio.microsoft.com/vs/) avec la charge de travail **développement mobile avec .net** .
* [Xamarin. Forms 3.0.0 ou version ultérieure](https://www.nuget.org/packages/Xamarin.Forms/).

## <a name="getting-started"></a>Mise en route
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Installez Xamarin Live Reload à partir du Visual Studio Marketplace

Le rechargement Xamarin Live est distribué via le Visual Studio Marketplace. Pour installer l’extension, accédez à la [page Xamarin Live reload sur le](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) site Web Visual Studio Marketplace, puis cliquez sur **Télécharger**.

Ouvrez le. vsix téléchargé, puis cliquez sur **installer**.

![Confirmation du rechargement en temps réel de Visual Studio installer Xamarin](images/LiveReloadVSIXInstall.png)

Vous pouvez également effectuer une recherche dans l’onglet **en ligne** de la boîte de dialogue **extensions et mises à jour** dans Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. configurer votre application pour utiliser le rechargement dynamique

L’ajout de rechargement dynamique à des applications mobiles existantes peut être effectué en trois étapes :

1. Assurez-vous que tous les projets sont mis à jour pour utiliser [Xamarin. Forms 3.0.0 ou supérieur](https://www.nuget.org/packages/Xamarin.Forms/) ou supérieur.

2. Ajoutez le package NuGet **Xamarin. LiveReload** :

    a. **.NET standard** : Installez le NuGet **Xamarin. LiveReload** dans votre bibliothèque .NET standard 2,0. Il n’est pas nécessaire d’installer cela dans vos projets de plateforme. Vérifiez que la **source du package** est définie sur **All**.
    
    b. **Projets partagés** : Installez le NuGet **Xamarin. LiveReload** dans tous les projets de plateforme (comme Android, iOS, UWP, etc.). Vérifiez que la **source du package** est définie sur **All**.

    [![ajouter Xamarin Live Reload NuGet avec le gestionnaire de package NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Ajoutez `LiveReload.Init();` au constructeur dans la classe `Application`, comme indiqué dans l’extrait de code suivant :

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. démarrer le rechargement en direct

Compilez et déployez votre application. Une fois l’application déployée, ouvrez un fichier XAML, apportez des modifications et enregistrez le fichier. Vos modifications sont redéployées sur la cible de déploiement.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Le rechargement dynamique fonctionne avec les modifications apportées à un fichier XAML. Les modifications C# apportées à ou l’ajout/la suppression de packages NuGet nécessitent une nouvelle build et le déploiement prennent effet.

## <a name="frequently-asked-questions"></a>Questions fréquemment posées 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>Le rechargement dynamique de Xamarin est-il disponible sur Visual Studio pour Mac ? 

Non, la version préliminaire de Xamarin Live Reload n’est disponible que pour Visual Studio 2017.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>Cela fonctionne-t-il avec toutes les bibliothèques, telles que Prism ? 

Étant donné que votre application est compilée, le rechargement en direct fonctionne avec toutes les bibliothèques, telles que Prism, et les bibliothèques de contrôle tierces, telles que Telerik, Infragistics, syncfusion, ArcGIS, GrapeCity et d’autres fournisseurs de contrôle.

### <a name="what-changes-does-live-reload-redeploy"></a>Quelles modifications le rechargement en direct recharge-t-il ? 

Le rechargement dynamique applique uniquement les modifications apportées à XAML ou CSS. Si vous apportez des modifications à C# un fichier, une recompilation est nécessaire. 

### <a name="what-platforms-are-supported"></a>Quelles sont les plateformes prises en charge ? 

Le rechargement en direct fonctionne sur toutes les plateformes prises en charge par Xamarin. Forms, notamment Android, iOS et UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>Cela fonctionne-t-il sur les émulateurs, les simulateurs et les appareils physiques ? 

Oui, le rechargement en direct fonctionne avec toutes les cibles de déploiement valides, y compris les émulateurs Android, les simulateurs iOS et les appareils physiques. Le déploiement sur un appareil requiert que l’appareil et l’ordinateur se trouvent sur le même réseau Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>Cela fonctionne-t-il avec les réseaux d’entreprise ?

Si vous déboguez sur un émulateur Android ou un simulateur iOS, le rechargement en direct utilise localhost pour communiquer. Si vous souhaitez effectuer un déploiement sur un appareil, l’appareil et l’ordinateur doivent se trouver sur le même réseau Wi-Fi. Dans les scénarios où cela n’est pas possible, vous pouvez [configurer votre propre serveur de rechargement dynamique](#live-reload-server), ce qui vous permettra de recharger en direct, quels que soient les paramètres de connectivité réseau.

### <a name="does-it-require-debugging-the-app"></a>Le débogage de l’application est-il nécessaire ? 

No. En fait, vous pouvez même démarrer toutes les cibles d’application prises en charge (Android, iOS et UWP) sur un nombre quelconque d’appareils ou de simulateurs/émulateurs, et voir toutes les mises à jour en même temps. 

## <a name="limitations"></a>Limitations

* Seul le rechargement du code XAML est pris en charge.
* L’état de l’interface utilisateur ne peut pas être maintenu entre les redéploiement, sauf si vous utilisez MVVM.

## <a name="known-issues"></a>Problèmes connus

* Pris en charge uniquement dans Visual Studio.
* La liaison doit être définie sur **ne pas lier** ou **lier les Kits SDK Framework uniquement** 
* Rechargement des ressources au niveau de l’application (par exemple, **app. Xaml** ou des dictionnaires de ressources partagées), la navigation dans l’application est réinitialisée. 
* Le rechargement de ContentView nécessite actuellement le rechargement de la page conteneur.
* Les éléments contenant AutomationId peuvent entraîner un échec de rechargement.
* La modification du code XAML pendant le débogage UWP peut entraîner un blocage du Runtime. Solution de contournement : utilisez **exécuter sans débogage (Ctrl + F5)** au lieu de **Démarrer le débogage (F5)** .

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="error-codes"></a>Codes d’erreur

* **XLR001**: *le projet en cours fait référence à la version du package NuGet « Xamarin. LiveReload » « [version] », mais l’extension de rechargement dynamique Xamarin nécessite la VERSION « [version] ».*

  Afin d’autoriser l’itération et l’évolution rapides de la fonctionnalité de rechargement en direct, le package NuGet et l’extension Visual Studio doivent correspondre exactement. Mettez à jour votre package NuGet vers la même version de l’extension que celle que vous avez installée.

* **XLR002**: *le rechargement dynamique requiert au moins la propriété’MqttHostname’lors de la génération à partir de la ligne de commande. Vous pouvez également définir « EnableLiveReload » sur « false » pour désactiver la fonctionnalité.*

  Les propriétés requises par le rechargement réel ne sont pas disponibles lors de la génération à partir de la ligne de commande (ou dans l’intégration continue) et doivent donc être fournies explicitement. 

* **XLR003**: *le package NuGet de rechargement dynamique nécessite l’installation de l’extension Visual Studio Xamarin Live reload.*

  Tentative de création d’un projet qui fait référence au package NuGet de rechargement dynamique, mais l’extension visuelle n’est pas installée.  

* *Exception lors du chargement des assemblys : System. IO. FileNotFoundException : impossible de charger l’assembly’Xamarin. live. Reload, version = 0.3.27.0, culture = neutral, PublicKeyToken = '.*

  Le projet hôte doit utiliser `PackageReference` au lieu de `packages.config`

### <a name="app-doesnt-connect"></a>L’application ne se connecte pas

Lorsque l’application est générée, les informations à partir des **outils > Options > Xamarin > rechargement en direct** (nom d’hôte, port et clés de chiffrement) sont incorporées dans l’application. ainsi, lorsque `LiveReload.Init()` s’exécute, aucune association ou configuration n’est nécessaire pour que la connexion aboutisse.

En dehors des problèmes de mise en réseau normaux (pare-feu, périphérique sur un autre réseau), la raison principale pour laquelle l’application peut ne pas se connecter à l’IDE est que sa configuration diffère de celle de Visual Studio. Cela peut se produire dans les cas suivants :

* L’application a été compilée sur un autre ordinateur.
* L’application a été compilée et déployée dans une autre session Visual Studio, et la **génération automatique des clés de chiffrement** est activée (valeur par défaut) dans **outils > Options > Xamarin > rechargement dynamique**.
* Les paramètres de Visual Studio ont été modifiés (par exemple, le nom d’hôte, le port ou les clés de chiffrement), mais l’application n’a pas été générée et déployée à nouveau.

Ces cas sont tous résolus par la génération et le déploiement de l’application.

### <a name="uninstalling-preview-1"></a>Désinstallation de Preview 1

Si vous disposez d’une version préliminaire plus ancienne et que vous rencontrez des problèmes pour la désinstaller, procédez comme suit :

1. Supprimez le dossier **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Remarque : remplacez « Enterprise » par votre édition installée, et « Preview » par « 2017 » si vous avez installé sur un ordinateur stable et
2. Ouvrez une **invite de commandes développeur** pour Visual Studio et exécutez `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Trucs et astuces

* Tant que les paramètres de rechargement en direct ne sont pas modifiés (y compris les clés de chiffrement, par exemple si vous désactivez la génération **automatique des clés de chiffrement**) et que vous générez à partir du même ordinateur, vous n’avez pas besoin de créer et de déployer l’application après le déploiement initial, sauf si vous modifiez le code ou les dépendances. Vous pouvez simplement lancer une application précédemment déployée pour qu’elle se connecte au dernier hôte utilisé.

* Il n’existe aucune limitation quant au nombre d’appareils que vous pouvez connecter à la même session Visual Studio. Vous pouvez déployer et démarrer l’application dans autant d’appareils et de simulateurs que nécessaire pour voir le rechargement dynamique qui fonctionne sur toutes les applications en même temps.

* Le rechargement dynamique rechargera uniquement la partie de l’interface utilisateur de votre application, mais il ne recréera *pas* vos pages, ni le modèle de vue (ou le contexte de liaison). Cela signifie que l' *intégralité* de l’état de l’application est toujours conservée entre les rechargements, y compris vos dépendances injectées.

## <a name="live-reload-server"></a>Serveur de rechargement dynamique

Dans les scénarios où une connexion de l’application en cours d’exécution à votre ordinateur (comme indiqué à l’aide de `localhost` ou `127.0.0.1` dans **outils > Options > Xamarin > le rechargement en direct**) n’est pas possible (par exemple, les pare-feu, les différents réseaux), vous pouvez configurer un serveur distant à la place, à la fois l’IDE et l’application conect.

Le rechargement en direct utilise le [protocole MQTT](https://mqtt.org/) standard pour échanger des messages et peut donc communiquer avec des [serveurs tiers](https://github.com/mqtt/mqtt.github.io/wiki/servers). Il existe même des [serveurs publics](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (également appelés « *courtiers*») que vous pouvez utiliser. Le rechargement dynamique a été testé avec `broker.hivemq.com` et les noms d’hôtes `iot.eclipse.org`, ainsi que les services fournis par [www.cloudmqtt.com](https://www.cloudmqtt.com) et [www.cloudamqp.com](https://www.cloudamqp.com). Vous pouvez également déployer votre propre serveur MQTT dans le Cloud, par exemple [HiveMQ sur Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

Vous pouvez configurer n’importe quel port, mais il est courant d’utiliser le port 1883 par défaut pour les serveurs distants. Les messages de rechargement en direct utilisent un chiffrement symétrique AES de bout en bout, de sorte qu’il est possible de se connecter aux serveurs distants. Par défaut, la clé de chiffrement et le vecteur d’initialisation (IV) sont tous deux régénérés sur chaque session Visual Studio.

Le moyen le plus simple consiste probablement à installer le serveur [mosquitto](https://mosquitto.org) sur une machine virtuelle Ubuntu vide dans Azure :

1. Créer une nouvelle machine virtuelle Ubuntu Server dans le portail Azure
2. Ajouter une nouvelle règle de port d’entrée pour 1883 (port MQTT par défaut) dans l’onglet mise en réseau
3. Ouvrir le [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (mode bash)
4. Tapez `ssh [USERNAME]@[PUBLIC_IP]` à l’aide du nom d’utilisateur que vous avez choisi dans 1) et de l’adresse IP publique indiquée dans la page vue d’ensemble de votre machine virtuelle
5. Exécutez `sudo apt-get install mosquitto`, en entrant le mot de passe que vous avez choisi dans 1)

Vous pouvez maintenant utiliser cette adresse IP pour vous connecter à votre propre serveur MQTT.
