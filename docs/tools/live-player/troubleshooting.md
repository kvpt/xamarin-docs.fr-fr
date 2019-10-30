---
title: Résolution des problèmes Xamarin Live Player
description: Ce document décrit les problèmes connus avec les Xamarin Live Player et les correctifs potentiels. Il aborde les problèmes de connexion, les problèmes de configuration et bien plus encore.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: d51241bee5f4ddc06032006071fa8296be37f2fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005942"
---
# <a name="troubleshooting-xamarin-live-player"></a>Résolution des problèmes Xamarin Live Player

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

> [!WARNING]
> L’aperçu de la Xamarin Live Player s’est terminé. L’application n’est plus disponible. Les instructions ci-dessous sont fournies aux clients qui continuent d’utiliser la version préliminaire de Visual Studio 2017.

> [!TIP]
> Vous pouvez utiliser le [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio 2019 ou Visual Studio pour Mac pour afficher les conceptions d’écran au fur et à mesure de leur modification.

Cet article explique les limitations de Live Player et certains problèmes courants liés aux étapes de correction.

## <a name="limitations-of-xamarin-live-player"></a>Limitations de Xamarin Live Player

### <a name="ide-requirements"></a>Exigences de l’IDE

La version préliminaire de Live Player est uniquement disponible dans Visual Studio 2017.

### <a name="device-requirements"></a>Configuration requise pour l’appareil

L’application Xamarin Live Player prend en charge les appareils Android suivants :

- Android 4,2 ou version ultérieure.
- ARM-V7A, ARM-V8A, ARM64-V8A, x86 ou x86_64 Processor.

### <a name="ios-limitations"></a>limitations d’iOS

Live Player n’est pas disponible pour iOS.

### <a name="xamarinforms-limitations"></a>Limitations de Xamarin. Forms

- Les convertisseurs personnalisés ne sont pas pris en charge.
- Les effets ne sont pas pris en charge.
- Les contrôles personnalisés avec des propriétés pouvant être liées personnalisées ne sont pas pris en charge.
- Les ressources incorporées ne sont pas prises en charge (c’est-à-dire l’incorporation d’images ou d’autres ressources dans un PCL).
- Les frameworks MVVM tiers ne sont pas pris en charge (par ex. Prisme, Cross MVVM, MVVM Light, etc.).

### <a name="other-project-type-limitations"></a>Autres limitations de type de projet

- Live Player n’est pas destiné aux projets Android natifs (qui utilisent Android XML pour l’interface utilisateur).

### <a name="miscellaneous-limitations"></a>Limitations diverses

- Prise en charge limitée de la réflexion (affecte actuellement certains packages NuGet populaires, comme SQLite et Json.NET). D’autres packages NuGet peuvent toujours être pris en charge.
- Certaines classes système ne peuvent pas être substituées (par exemple, vous ne pouvez pas implémenter une sous-classe).
- Certaines fonctionnalités de plateforme nécessitant l’approvisionnement ne peuvent pas fonctionner dans l’application Xamarin Live Player (mais elles ont été configurées pour des opérations courantes comme l’accès à la Galerie de photos).
- Les cibles personnalisées et les étapes de génération sont ignorées. Par exemple, les outils tels que Fody, refit, AutoFac et AutoMapper ne peuvent pas être incorporés.
- F#les projets ne sont pas pris en charge
- Les scénarios avancés avec des classes et des interfaces génériques personnalisées peuvent ne pas être pris en charge.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Le périphérique mobile ne se connecte pas après l’analyse du code-barres (ou l’entrée du code)

Se produit lorsque l’appareil mobile en cours d’exécution Xamarin Live Player ne se trouve pas sur le même réseau que l’ordinateur qui exécute l’IDE. Consultez les rubriques suivantes :

- Vérifiez que l’appareil et l’ordinateur se trouvent sur le même réseau Wi-Fi.
  - Si l’ordinateur est également connecté à un réseau câblé, essayez de déconnecter la connexion câblée.
- Le réseau peut être fortement sécurisé (par exemple certains réseaux d’entreprise) et bloquer les ports requis par Xamarin Live Player.
- Fermez l’application Xamarin Live Player et redémarrez-la.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>« Erreur lors de la tentative de déploiement » d’un message dans l’IDE

**« IOException : impossible de lire les données à partir de la connexion de transport : l’opération sur un socket non bloquant est bloquée »**

Cette erreur se produit souvent lorsque l’appareil mobile exécutant Xamarin Live Player ne se trouve pas sur le même réseau que l’ordinateur exécutant Visual Studio. Cela se produit souvent lors de la connexion à un appareil qui a été précédemment couplé avec succès.

- Vérifiez que l’appareil et l’ordinateur se trouvent sur le même réseau Wi-Fi.
- Le réseau peut être fortement sécurisé (par exemple certains réseaux d’entreprise) et bloquer les ports requis par Xamarin Live Player. Les ports suivants sont requis pour l’Xamarin Live Player :
  - 37847 – accès réseau interne 
  - 8090 – accès réseau externe

## <a name="manually-configure-device"></a>Configurer manuellement l’appareil

Si vous ne pouvez pas vous connecter à votre appareil via Wi-Fi, vous pouvez essayer de configurer manuellement votre appareil à l’aide du fichier de configuration, en procédant comme suit :

**Étape 1 : ouvrir le fichier de configuration**

Accédez à votre dossier de données d’application :

- Windows : **%UserProfile%\AppData\Roaming**
- macOS : **~/Users/$User/.config**

Dans ce dossier, vous trouverez **PlayerDeviceList. xml** , s’il n’existe pas, vous devez en créer un.

**Étape 2 : obtenir l’adresse IP**

Dans l’application Xamarin Live Player, accédez à **About > connection test > Start connection test**.

Prenez note de l’adresse IP, vous aurez besoin de l’adresse IP indiquée lors de la configuration de votre appareil.

**Étape 3 : obtenir le code de jumelage**

À l’intérieur de la **paire ou de Xamarin Live Player la paire** appuyer **à nouveau**, appuyez de nouveau sur **entrée manuellement**. Un code numérique s’affiche, dont vous aurez besoin pour mettre à jour le fichier de configuration.

**Étape 4 : générer le GUID**

Accédez à : https://www.guidgenerator.com/online-guid-generator.aspx et générez un nouveau GUID et vérifiez que la casse est activée.

**Étape 5 : configurer l’appareil**

Ouvrez le **fichier PlayerDeviceList. xml** dans un éditeur tel que Visual Studio ou Visual Studio code. Vous devez configurer votre appareil manuellement dans ce fichier. Par défaut, le fichier doit contenir l’élément XML `Devices` vide suivant :

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Ajouter un appareil Android :**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**Fermez et rouvrez Visual Studio.** Votre appareil doit apparaître dans la liste.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Message « type ou espace de noms introuvable » dans l’IDE

Vérifiez que vous avez sélectionné un **projet de démarrage** qui correspond à votre type d’appareil (par exemple, Android) et que la configuration correspond à ce type d’appareil (par exemple, **Déboguer** pour Android).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Message « constructeur sur le type’InterpretedXamarin. Forms. Button’introuvable » dans le lecteur

Certaines classes système ne peuvent pas être substituées, par exemple :

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>« MainActivity.cs : 'Resource. layout’ne contient pas de définition pour’main' »

Cette erreur se produit pour les projets Android avec des interfaces utilisateur définies dans les fichiers AXML.
Les fichiers AXML ne sont actuellement pas pris en charge dans Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>La barre d’outils et les onglets Android s’affichent incorrectement à l’aide de Xamarin. Forms

Les projets Android Xamarin. Forms doivent utiliser « Toolbar. AXML » et « TabBar. AXML » pour les noms des fichiers de disposition appropriés. Le modèle par défaut utilise ces noms ; les renommer entraînent des problèmes de rendu.

## <a name="related-links"></a>Liens associés

- [Installation](~/tools/live-player/install.md)
