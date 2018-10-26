---
title: Résolution des problèmes de Xamarin Live Player
description: Ce document décrit les problèmes connus avec le Xamarin Live Player et les corrections éventuelles. Il aborde les problèmes de connexion, les problèmes de configuration et bien plus encore.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: eb9d758d72febe0fc0b705d66246c99ade1fc80f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109690"
---
# <a name="troubleshooting-xamarin-live-player"></a>Résolution des problèmes de Xamarin Live Player

![Fonctionnalité en version préliminaire](~/media/shared/preview.png)

Cet article décrit certains problèmes courants et fournit des étapes pour les corriger.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Appareil mobile ne se connecte pas après analyse code-barres (ou l’entrée de code)

Se produit lorsque le périphérique mobile Xamarin Live Player en cours d’exécution n’est pas sur le même réseau que l’ordinateur qui exécute l’IDE. Découvrez les éléments suivants :

- Vérifiez que l’appareil et l’ordinateur sont sur le même réseau Wi-Fi.
  - Si l’ordinateur est également connecté à un réseau câblé, essayez de le débrancher la connexion câblée.
- Le réseau peut être étroitement sécurisé (par exemple, certains réseaux d’entreprise), bloquant les ports requis par Xamarin Live Player.
- Fermez l’application Xamarin Live Player et redémarrez-le.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Message « Erreur lors de la tentative de déploiement » dans l’IDE

**« Exception IOException : Impossible de lire les données à partir de la connexion de transport : susceptibles de bloquer l’opération sur un socket non bloquant »**

Cette erreur se produite souvent lorsque le périphérique mobile Xamarin Live Player en cours d’exécution n’est pas sur le même réseau que l’ordinateur qui exécute Visual Studio. Cela se produit souvent lors de la connexion à un périphérique qui a été précédemment associé avec succès.

* Vérifiez que l’appareil et l’ordinateur sont sur le même réseau Wi-Fi.
* Le réseau peut être étroitement sécurisé (par exemple, certains réseaux d’entreprise), bloquant les ports requis par Xamarin Live Player. Les ports suivants sont requis pour Xamarin Live Player :
  * 37847 – accès au réseau interne 
  * 8090 – accès réseau externe

## <a name="manually-configure-device"></a>Configurer manuellement des appareils

Si vous ne pouvez pas connecter à votre appareil via le Wi-Fi vous pouvez tenter de configurer manuellement votre appareil via le fichier de configuration, procédez comme suit :

**Étape 1 : Ouvrez le fichier de configuration**

Accédez à votre dossier de données d’application :

* Windows : **%userprofile%\AppData\Roaming**
* macOS : **~/Users/$USER/.config**

Vous trouverez dans ce dossier **PlayerDeviceList.xml** si elle n’existe pas, vous devrez créer un.

**Étape 2 : Obtenir l’adresse IP**

Dans l’application Xamarin Live Player, accédez à **sur > Test de connexion > Démarrer le Test connexion**.

Prenez note de l’adresse IP, vous devez l’adresse IP répertoriée quand vous configurez votre appareil.

**Étape 3 : Obtenir le code de jumelage**

À l’intérieur du drainage de Xamarin Live Player **paire** ou **paire à nouveau**, puis appuyez sur **saisir manuellement**. Un code numérique s’affichera, que vous devez mettre à jour le fichier de configuration.

**Étape 4 : Générer des GUID**

Accédez à : https://www.guidgenerator.com/online-guid-generator.aspx et générer un nouveau guid et assurez-vous qu’Upper Case est activée.

**Étape 5 : Configurer l’appareil**

Ouvrez le **PlayerDeviceList.xml** vers le haut dans un éditeur tel que Visual Studio ou Visual Studio Code. Vous devez configurer votre appareil manuellement dans ce fichier. Par défaut, le fichier doit contenir le vide suivant `Devices` élément XML :

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

**Fermez et rouvrez Visual Studio.** Votre appareil doit s’afficher dans la liste.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Message de « type ou espace de noms est introuvable » dans l’IDE

Vérifiez que vous avez sélectionné un **projet de démarrage** qui correspond à votre type d’appareil (par exemple). Android) et que la configuration correspond à ce type d’appareil (par exemple). **Déboguer** pour Android).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Message « Constructeur sur le type 'InterpretedXamarin.Forms.Button' introuvable » dans le lecteur

Certaines classes système ne peut pas être substituées, par exemple :

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>« MainActivity.cs : 'Resource.Layout' ne contient pas de définition pour 'Main' »

Cette erreur se produit pour les projets Android avec les interfaces utilisateur définies dans les fichiers AXML.
Les fichiers AXML ne sont pas actuellement pris en charge dans Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Les onglets et barre d’outils Android restituent correctement à l’aide de Xamarin.Forms

Les projets Android de Xamarin.Forms doivent utiliser « Toolbar.axml » et « Tabbar.axml » pour les noms des fichiers de disposition pertinentes. Le modèle par défaut utilise ces noms ; en les renommant entraîne des problèmes de rendu.

Signalez les problèmes supplémentaires sur [bugzilla](https://aka.ms/live-player-report-issue).

## <a name="related-links"></a>Liens associés

- [Limitations](~/tools/live-player/limitations.md)
- [Installation](~/tools/live-player/install.md)
