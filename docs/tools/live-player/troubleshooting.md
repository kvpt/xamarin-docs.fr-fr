---
title: Résolution des problèmes de Xamarin Live Player
description: Ce document décrit les problèmes connus avec le Xamarin Live Player et les corrections éventuelles. Il aborde les problèmes de connexion, les problèmes de configuration et bien plus encore.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: bf0186b55b14d9797397b98390f4d825d669d0f4
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157698"
---
# <a name="troubleshooting-xamarin-live-player"></a>Résolution des problèmes de Xamarin Live Player

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

> [!WARNING]
> La version préliminaire Xamarin Live Player est terminée. L’application n’est plus disponible. Les instructions ci-dessous sont fournies pour les clients de continuer à utiliser la version préliminaire avec Visual Studio 2017...

> [!TIP]
> Vous pouvez utiliser la [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio 2019 ou Visual Studio pour Mac pour afficher vos conceptions de l’écran lorsque vous les modifiez.

Cet article explique les limitations de Live Player et certains problèmes courants avec les étapes pour les corriger.

## <a name="limitations-of-xamarin-live-player"></a>Limitations de Xamarin Live Player

### <a name="ide-requirements"></a>Exigences de l’IDE

L’aperçu instantané de lecteur est uniquement disponible dans Visual Studio 2017.

### <a name="device-requirements"></a>Exigences de l’appareil

L’application Xamarin Live Player prend en charge les appareils Android suivants :

- Android 4.2 ou version ultérieure.
- ARM-v7a, v8a de ARM, ARM64-v8a, x 86 ou x86_64 processeur.

### <a name="ios-limitations"></a>limitations d’iOS

Live Player n’est pas disponible pour iOS.

### <a name="xamarinforms-limitations"></a>Limitations de Xamarin.Forms

- Renderers personnalisés ne sont pas pris en charge.
- Effets ne sont pas pris en charge.
- Contrôles personnalisés avec des propriétés pouvant être liées personnalisés ne sont pas pris en charge.
- Ressources incorporées ne sont pas pris en charge (ie. l’incorporation d’images ou autres ressources dans une bibliothèque de classes portable).
- Infrastructures MVVM de tiers ne sont pas pris en charge (p. ex. Prism, Mvvm Cross, Mvvm Light, etc.).

### <a name="other-project-type-limitations"></a>Autres limitations des types de projet

- Live Player n’est pas destinée à être des projets Android natifs (qui utilisent Android XML pour l’interface utilisateur).

### <a name="miscellaneous-limitations"></a>Autres limitations

- Prise en charge pour la réflexion limitée (actuellement affecte certains packages NuGet populaires, telles que SQLite et Json.NET). Autres packages NuGet peut toujours être pris en charge.
- Certaines classes système ne peut pas être remplacés (par exemple, vous ne peut pas implémenter une sous-classe).
- Certaines fonctionnalités de plateforme qui nécessitent un approvisionnement ne fonctionnent pas dans l’application Xamarin Live Player (Toutefois il a été configuré pour les opérations courantes telles que l’accès de galerie de photos).
- Cibles personnalisées et les étapes de génération sont ignorés. Par exemple, les outils tels que Fody, réajuster, AutoFac et AutoMapper ne peut pas être incorporées.
- F#projets ne sont pas pris en charge.
- Scénarios avancés avec des classes génériques personnalisés et les interfaces ne peuvent pas pris en charge.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Appareil mobile ne se connecte pas après analyse code-barres (ou l’entrée de code)

Se produit lorsque le périphérique mobile Xamarin Live Player en cours d’exécution n’est pas sur le même réseau que l’ordinateur qui exécute l’IDE. Découvrez les éléments suivants :

- Vérifiez que l’appareil et l’ordinateur sont sur le même réseau Wi-Fi.
  - Si l’ordinateur est également connecté à un réseau câblé, essayez de le débrancher la connexion câblée.
- Le réseau peut être étroitement sécurisé (par exemple, certains réseaux d’entreprise), bloquant les ports requis par Xamarin Live Player.
- Fermez l’application Xamarin Live Player et redémarrez-le.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Message « Erreur lors de la tentative de déploiement » dans l’IDE

**« Exception IOException : Impossible de lire les données à partir de la connexion de transport : Opération sur un socket non bloquant bloquerait »**

Cette erreur se produite souvent lorsque le périphérique mobile Xamarin Live Player en cours d’exécution n’est pas sur le même réseau que l’ordinateur qui exécute Visual Studio. Cela se produit souvent lors de la connexion à un périphérique qui a été précédemment associé avec succès.

* Vérifiez que l’appareil et l’ordinateur sont sur le même réseau Wi-Fi.
* Le réseau peut être étroitement sécurisé (par exemple, certains réseaux d’entreprise), bloquant les ports requis par Xamarin Live Player. Les ports suivants sont requis pour Xamarin Live Player :
  * 37847 – accès au réseau interne 
  * 8090 – accès réseau externe

## <a name="manually-configure-device"></a>Configurer manuellement des appareils

Si vous ne pouvez pas connecter à votre appareil via le Wi-Fi vous pouvez tenter de configurer manuellement votre appareil via le fichier de configuration, procédez comme suit :

**Étape 1 : Fichier de configuration ouvert**

Accédez à votre dossier de données d’application :

* Windows : **%userprofile%\AppData\Roaming**
* macOS : **~/Users/$USER/.config**

Vous trouverez dans ce dossier **PlayerDeviceList.xml** si elle n’existe pas, vous devrez créer un.

**Étape 2 : Obtenir l’adresse IP**

Dans l’application Xamarin Live Player, accédez à **sur > Test de connexion > Démarrer le Test connexion**.

Prenez note de l’adresse IP, vous devez l’adresse IP répertoriée quand vous configurez votre appareil.

**Étape 3 : Obtenir le code de jumelage**

À l’intérieur du drainage de Xamarin Live Player **paire** ou **paire à nouveau**, puis appuyez sur **saisir manuellement**. Un code numérique s’affichera, que vous devez mettre à jour le fichier de configuration.

**Étape 4 : Générer des GUID**

Accédez à : https://www.guidgenerator.com/online-guid-generator.aspx et générer un nouveau guid et assurez-vous qu’Upper Case est activée.

**Étape 5 : Configurer l’appareil**

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

## <a name="related-links"></a>Liens associés

- [Installation](~/tools/live-player/install.md)
