---
title: Gestion des appareils virtuels avec Android Device Manager
description: Cet article décrit comment utiliser Android Device Manager pour créer et configurer des appareils virtuels Android (AVD) qui émulent des appareils physiques Android. Vous pouvez utiliser ces appareils virtuels pour exécuter et tester votre application sans avoir à dépendre d’un appareil physique.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 60dcbd6f59e622fe5a1272bd2d61540b03e510e4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758431"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Gestion des appareils virtuels avec Android Device Manager

_Cet article décrit comment utiliser Android Device Manager pour créer et configurer des appareils virtuels Android (AVD) qui émulent des appareils physiques Android. Vous pouvez utiliser ces appareils virtuels pour exécuter et tester votre application sans avoir à dépendre d’un appareil physique._

Après avoir vérifié que l’accélération matérielle était activée (comme décrit dans [Accélération matérielle de l’émulateur Android](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), l’étape suivante consiste à utiliser _Android Device Manager_ (également appelé _Xamarin Android Device Manager_) afin de créer des appareils virtuels que vous pouvez utiliser pour tester et déboguer votre application.

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Android Device Manager sur Windows

Cet article explique comment utiliser Android Device Manager pour créer, dupliquer, personnaliser et lancer des appareils virtuels Android.

[![Capture d’écran d’Android Device Manager sous l’onglet Appareils](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

Vous utilisez Android Device Manager pour créer et configurer des _appareils virtuels Android_ (AVD) que vous exécutez dans l’[émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).
Chaque AVD est une configuration d’émulateur qui simule un appareil Android physique. Vous pouvez ainsi exécuter et tester votre application dans diverses configurations qui simulent différents appareils Android physiques.

## <a name="requirements"></a>Configuration requise

Pour utiliser Android Device Manager, vous devez disposer des éléments suivants :

- Visual Studio 2019 Community, Professional ou Enterprise

- OU Visual Studio 2017 version 15.8 ou ultérieure est nécessaire Les éditions Visual Studio Community, Professional et Enterprise sont prises en charge.

- Visual Studio Tools pour Xamarin version 4.9 ou ultérieure.

- Le kit Android SDK doit être installé (consultez [Configuration du kit Android SDK pour Xamarin.Android](~/android/get-started/installation/android-sdk.md)).
  Veillez à installer le kit Android SDK à son emplacement par défaut, s’il ne l’est pas déjà : **C:\\Program Files (x86)\\Android\\android-sdk**.

- Les paquets suivants doivent être installés (via [Android SDK Manager](~/android/get-started/installation/android-sdk.md)) : 
  - **Android SDK Tools version 26.1.1** ou version ultérieure
  - **Android SDK Platform-Tools {27.0.1}** ou ultérieur
  - **Android SDK Build-Tools 27.0.3** ou ultérieur 
  - **Émulateur Android 27.2.7** ou version ultérieure. 

  Ces paquets doivent être affichés avec l’état **Installé**, comme illustré dans la capture d’écran suivante :

  [![Installation d’Android SDK Tools](device-manager-images/win/02-sdk-tools-sml.png)](device-manager-images/win/02-sdk-tools.png#lightbox)

## <a name="launching-the-device-manager"></a>Lancement du Gestionnaire d’appareils

Lancez Android Device Manager à partir du menu **Outils** en cliquant sur **Outils > Android > Android Device Manager** :

[![Lancement de Device Manager à partir du menu Outils](device-manager-images/win/03-tools-menu-sml.png)](device-manager-images/win/03-tools-menu.png#lightbox)

Si la boîte de dialogue d’erreur suivante est présentée au lancement, consultez la section [Résolution des problèmes](#troubleshooting) pour obtenir des instructions de contournement :

![Boîte de dialogue d’erreur d’instance du kit Android SDK](device-manager-images/win/04-sdk-error.png)

## <a name="main-screen"></a>Écran principal

Lorsque vous lancez pour la première fois le Gestionnaire d’appareils Android, un écran affichant tous les appareils virtuels actuellement configurés s’affiche. Pour chaque appareil virtuel, le **Nom**, l’**OS** (version d’Android), le **Processeur**, la quantité de **Mémoire**, ainsi que la **Résolution** d’écran sont affichés :

[![Liste des appareils installés avec leurs paramètres](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Quand vous sélectionnez un appareil dans la liste, le bouton **Démarrer** apparaît à droite. Vous pouvez cliquer sur le bouton **Démarrer** pour lancer l’émulateur avec cet appareil virtuel :

[![Bouton Démarrer d’une image d’appareil](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

Une fois que l’émulateur a démarré avec l’appareil virtuel sélectionné, le bouton **Démarrer** se transforme en bouton **Arrêter** que vous pouvez utiliser pour arrêter l’émulateur :

[![Bouton Arrêter de l’appareil en cours d’exécution](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Nouvel appareil

Pour créer un nouvel appareil, cliquez sur le bouton **Nouveau** (situé en haut à droite de l’écran) :

[![Bouton Nouveau pour la création d’un nouvel appareil](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

Lorsque vous cliquez sur **Nouveau**, l’écran **Nouvel appareil** s’affiche :

[![Écran Nouvel appareil du Gestionnaire d’appareils](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Pour configurer un nouvel appareil dans l’écran **Nouvel appareil**, effectuez les étapes suivantes :

1. Donnez un nouveau nom à l’appareil. Dans l’exemple suivant, le nouvel appareil se nomme **Pixel_API_27** :

   [![Nommage du nouvel appareil](device-manager-images/win/10-device-name-sml.png)](device-manager-images/win/10-device-name.png#lightbox)

2. Sélectionnez un appareil physique à émuler en cliquant sur le menu déroulant **Appareil de base** :

   [![Sélection de l’appareil physique à émuler](device-manager-images/win/11-device-menu-sml.png)](device-manager-images/win/11-device-menu.png#lightbox)

3. Sélectionnez un type de processeur pour cet appareil virtuel en cliquant sur le menu déroulant **Processeur**. Si vous sélectionnez **x86**, vous obtenez les meilleures performances, car cela permet à l’émulateur de tirer parti de l’[accélération matérielle](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   L’option **x86_64** utilise également l’accélération matérielle, mais son exécution est légèrement plus lente que celle de **x86** (**x86_64** sert normalement à tester des applications 64 bits) :

   [![Sélection du type de processeur](device-manager-images/win/12-processor-type-menu-sml.png)](device-manager-images/win/12-processor-type-menu.png#lightbox)

4. Sélectionnez la version d’Android (niveau d’API) en cliquant sur le menu déroulant **OS**. Par exemple, sélectionnez **Oreo 8.1 - API 27** afin de créer un appareil virtuel pour le niveau d’API 27 :

   [![Sélection de la version d’Android](device-manager-images/win/13-android-version-w158-sml.png)](device-manager-images/win/13-android-version-w158.png#lightbox)

   Si vous sélectionnez un niveau d’API Android qui n’a pas encore été installé, Device Manager affiche le message **Une nouvelle image d’appareil sera téléchargée** en bas de l’écran. Il télécharge et installe les fichiers nécessaires pendant la création de l’appareil virtuel :

   ![Une nouvelle image d’appareil sera téléchargée](device-manager-images/win/14-automatic-download-w158.png)

5. Si vous souhaitez inclure les API Google Play Services dans votre appareil virtuel, activez l’option **API Google**. Pour inclure l’application Google Play Store, activez l’option **Google Play Store** :

   [![Sélection de Google Play Services et de Google Play Store](device-manager-images/win/15-google-play-services-sml.png)](device-manager-images/win/15-google-play-services.png#lightbox)

   Notez que les images du Google Play Store sont disponibles uniquement pour certains types d’appareil de base, par exemple Pixel, Pixel 2, Nexus 5 et Nexus 5X.

6. Modifiez les propriétés nécessaires. Pour apporter des modifications aux propriétés, consultez [Modification des propriétés d’appareil virtuel Android](~/android/get-started/installation/android-emulator/device-properties.md).

7. Ajoutez toute propriété supplémentaire que vous avez besoin de définir explicitement. L’écran **Nouvel appareil** liste uniquement les propriétés modifiées le plus souvent. Vous pouvez toutefois cliquer sur le menu déroulant **Ajouter une propriété** (en bas) pour ajouter des propriétés supplémentaires :

   [![Menu déroulant Ajouter une propriété](device-manager-images/win/16-add-property-menu-sml.png)](device-manager-images/win/16-add-property-menu.png#lightbox)

    Vous pouvez également définir une propriété personnalisée en sélectionnant **Personnaliser** en haut de la liste des propriétés.

8. Cliquez sur le bouton **Créer** (dans l’angle inférieur droit) pour créer le nouvel appareil :

   [![Bouton Créer](device-manager-images/win/17-create-button-sml.png)](device-manager-images/win/17-create-button.png#lightbox)

9. Un écran **Acceptation de la licence** est susceptible de s’afficher. Si vous êtes d’accord avec les termes du contrat de licence, cliquez sur **Accepter** :

   [![Écran Acceptation de la licence](device-manager-images/win/18-license-acceptance-sml.png)](device-manager-images/win/18-license-acceptance.png#lightbox)

10. Android Device Manager ajoute le nouvel appareil à la liste des appareils virtuels installés et affiche un indicateur de progression **Création en cours** pendant la création de l’appareil :

    [![Indicateur de progression de la création](device-manager-images/win/19-creating-the-device-sml.png)](device-manager-images/win/19-creating-the-device.png#lightbox)

11. Lorsque le processus de création est terminé, le nouvel appareil apparaît dans la liste des appareils virtuels installés avec un bouton **Démarrer**, prêt à être lancé :

    [![Appareil nouvellement créé prêt à être lancé](device-manager-images/win/20-created-device-sml.png)](device-manager-images/win/20-created-device.png#lightbox)

### <a name="edit-device"></a>Modifier l’appareil

Pour modifier un appareil virtuel existant, sélectionnez-le, puis cliquez sur le bouton **Modifier** (situé dans le coin supérieur droit de l’écran) :

[![Bouton Modifier pour modifier un appareil](device-manager-images/win/21-edit-button-sml.png)](device-manager-images/win/21-edit-button.png#lightbox)

Le fait de cliquer sur le bouton **Modifier** lance l’Éditeur d’appareil pour l’appareil virtuel sélectionné :

[![Écran Éditeur d’appareil](device-manager-images/win/22-device-editor-sml.png)](device-manager-images/win/22-device-editor.png#lightbox)

L’écran **Éditeur d’appareil** liste les propriétés de l’appareil virtuel dans la colonne **Propriété**, ainsi que les valeurs correspondantes de chaque propriété dans la colonne **Valeur**. Lorsque vous sélectionnez une propriété, une description détaillée de cette propriété s’affiche à droite.

Pour changer une propriété, modifiez sa valeur dans la colonne **Valeur**.
Par exemple, dans la capture d’écran suivante, la propriété `hw.lcd.density` passe de **480** à **240** :

[![Exemple de modification d’un appareil](device-manager-images/win/23-device-editing-sml.png)](device-manager-images/win/23-device-editing.png#lightbox)

Après avoir apporté les modifications de configuration nécessaires, cliquez sur le bouton **Enregistrer**.
Pour plus d’informations sur la modification des propriétés des appareils virtuels, consultez [Modification des propriétés d’appareil virtuel Android](~/android/get-started/installation/android-emulator/device-properties.md).

### <a name="additional-options"></a>Options supplémentaires

Des options supplémentaires permettant d’utiliser les appareils sont disponibles dans le menu déroulant **Options supplémentaires** (&hellip;) situé dans le coin supérieur droit :

[![Emplacement du menu Options supplémentaires](device-manager-images/win/24-overflow-menu-sml.png)](device-manager-images/win/24-overflow-menu.png#lightbox)

Le menu Options supplémentaires contient les éléments suivants :

- **Dupliquer et modifier** &ndash; Duplique l’appareil sélectionné et l’ouvre dans l’écran **Nouvel appareil** avec un nom unique différent. Par exemple, si vous sélectionnez **Pixel_API_27** et si vous cliquez sur **Dupliquer et modifier**, un compteur est ajouté au nom :

  [![Écran Duplicate et modifier](device-manager-images/win/25-dupe-and-edit-sml.png)](device-manager-images/win/25-dupe-and-edit.png#lightbox)

- **Révéler dans l’Explorateur** &ndash; Ouvre une fenêtre de l’Explorateur Windows dans le dossier contenant les fichiers de l’appareil virtuel. Par exemple, si vous sélectionnez **Pixel_API_27** et si vous cliquez sur **Révéler dans l’Explorateur**, une fenêtre similaire à l’exemple suivant s’affiche :

  [![Résultat du clic sur Afficher dans le Finder](device-manager-images/win/26-reveal-in-explorer-sml.png)](device-manager-images/win/26-reveal-in-explorer.png#lightbox)

- **Réinitialisation aux paramètres d’usine** &ndash; Réinitialise l’appareil aux paramètres par défaut, en effaçant toutes les modifications apportées par l’utilisateur à l’état interne de l’appareil pendant son exécution (l’instantané [Quick Boot](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot), s’il existe, est également effacé). Cette modification n’altère pas les modifications que vous apportez à l’appareil virtuel lors de la création et de la modification. Une boîte de dialogue rappelant que la réinitialisation ne peut pas être annulée s’affiche. Cliquez sur **Réinitialisation aux paramètres d’usine** pour confirmer la réinitialisation :

  ![Boîte de dialogue de réinitialisation aux paramètres d’usine](device-manager-images/win/27-factory-reset.png)

- **Supprimer** &ndash; Supprime définitivement l’appareil virtuel sélectionné. Une boîte de dialogue rappelant que la suppression d’un appareil ne peut pas être annulée s’affiche. Cliquez sur **Supprimer** si vous êtes certain que vous souhaitez supprimer l’appareil.

  ![Boîte de dialogue de suppression d’appareil](device-manager-images/win/28-delete-device-w158.png)

::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>Android Device Manager sur macOS

Cet article explique comment utiliser Android Device Manager pour créer, dupliquer, personnaliser et lancer des appareils virtuels Android.

[![Capture d’écran d’Android Device Manager sous l’onglet Appareils](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Ce guide s’applique uniquement à Visual Studio pour Mac.
Xamarin Studio n’est pas compatible avec Android Device Manager.

Vous utilisez Android Device Manager pour créer et configurer des *appareils virtuels Android* (AVD) que vous exécutez dans l’[émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).
Chaque AVD est une configuration d’émulateur qui simule un appareil Android physique. Vous pouvez ainsi exécuter et tester votre application dans diverses configurations qui simulent différents appareils Android physiques.

## <a name="requirements"></a>Configuration requise

Pour utiliser Android Device Manager, vous devez disposer des éléments suivants :

- Visual Studio pour Mac 7.6 ou version ultérieure.

- Le kit Android SDK doit être installé (consultez [Configuration du kit Android SDK pour Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

- Les paquets suivants doivent être installés (via [Android SDK Manager](~/android/get-started/installation/android-sdk.md)) : 
  - **SDK Tools 26.1.1** ou version ultérieure
  - **Android SDK Platform-Tools 28.0.1** ou version ultérieure 
  - **Android SDK Build-Tools 26.0.3** ou version ultérieure

  Ces paquets doivent être affichés avec l’état **Installé**, comme illustré dans la capture d’écran suivante :

  [![Installation d’Android SDK Tools](device-manager-images/mac/02-sdk-tools-sml.png)](device-manager-images/mac/02-sdk-tools.png#lightbox)

## <a name="launching-the-device-manager"></a>Lancement du Gestionnaire d’appareils

Lancez Android Device Manager en cliquant sur **Outils > Device Manager** :

[![Lancement de Device Manager à partir du menu Outils](device-manager-images/mac/03-tools-menu-sml.png)](device-manager-images/mac/03-tools-menu.png#lightbox)

Si la boîte de dialogue d’erreur suivante est présentée au lancement, consultez la section [Résolution des problèmes](#troubleshooting) pour obtenir des instructions de contournement :

![Boîte de dialogue d’erreur d’instance du kit Android SDK](device-manager-images/mac/04-sdk-instance-error.png)

## <a name="main-screen"></a>Écran principal

Lorsque vous lancez pour la première fois le Gestionnaire d’appareils Android, un écran affichant tous les appareils virtuels actuellement configurés s’affiche. Pour chaque appareil virtuel, le **Nom**, l’**OS** (version d’Android), le **Processeur**, la quantité de **Mémoire**, ainsi que la **Résolution** d’écran sont affichés :

[![Liste des appareils installés avec leurs paramètres](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Quand vous sélectionnez un appareil dans la liste, le bouton **Lecture** apparaît à droite. Vous pouvez cliquer sur le bouton **Lecture** pour lancer l’émulateur avec cet appareil virtuel :

[![Bouton de lecture d’une image d’appareil](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

Une fois que l’émulateur a démarré avec l’appareil virtuel sélectionné, le bouton **Lire** se transforme en bouton **Arrêter** que vous pouvez utiliser pour arrêter l’émulateur :

[![Bouton Arrêter de l’appareil en cours d’exécution](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

Quand vous arrêtez l’émulateur, une invite peut éventuellement s’afficher en vous demandant si vous souhaitez enregistrer l’état actuel pour le prochain démarrage rapide :

![Boîte de dialogue d’enregistrement de l’état actuel pour le démarrage rapide](device-manager-images/mac/08-save-for-quick-boot-m76.png)

L’enregistrement de l’état actuel va accélérer le démarrage de l’émulateur quand cet appareil virtuel sera relancé. Pour plus d’informations sur le démarrage rapide, consultez [Démarrage rapide](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot).

### <a name="new-device"></a>Nouvel appareil

Pour créer un appareil, cliquez sur le bouton **Nouvel appareil** (situé en haut à gauche de l’écran) :

[![Bouton Nouveau pour la création d’un nouvel appareil](device-manager-images/mac/09-new-button-sml.png)](device-manager-images/mac/09-new-button.png#lightbox)

Lorsque vous cliquez sur **Nouvel appareil**, l’écran **Nouvel appareil** s’affiche :

[![Écran Nouvel appareil du Gestionnaire d’appareils](device-manager-images/mac/10-new-device-editor-sml.png)](device-manager-images/mac/10-new-device-editor.png#lightbox)

Pour configurer un nouvel appareil dans l’écran **Nouvel appareil**, effectuez les étapes suivantes :

1. Donnez un nouveau nom à l’appareil. Dans l’exemple suivant, le nouvel appareil se nomme **Pixel_API_27** :

   [![Nommage du nouvel appareil](device-manager-images/mac/11-device-name-m76-sml.png)](device-manager-images/mac/11-device-name-m76.png#lightbox)

2. Sélectionnez un appareil physique à émuler en cliquant sur le menu déroulant **Appareil de base** :

   [![Sélection de l’appareil physique à émuler](device-manager-images/mac/12-device-menu-m76-sml.png)](device-manager-images/mac/12-device-menu-m76.png#lightbox)

3. Sélectionnez un type de processeur pour cet appareil virtuel en cliquant sur le menu déroulant **Processeur**. Si vous sélectionnez **x86**, vous obtenez les meilleures performances, car cela permet à l’émulateur de tirer parti de l’[accélération matérielle](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   L’option **x86_64** utilise également l’accélération matérielle, mais son exécution est légèrement plus lente que celle de **x86** (**x86_64** sert normalement à tester des applications 64 bits) :

   [![Sélection du type de processeur](device-manager-images/mac/13-processor-type-menu-m76-sml.png)](device-manager-images/mac/13-processor-type-menu-m76.png#lightbox)

4. Sélectionnez la version d’Android (niveau d’API) en cliquant sur le menu déroulant **OS**. Par exemple, sélectionnez **Oreo 8.1 - API 27** afin de créer un appareil virtuel pour le niveau d’API 27 :

   [![Sélection de la version d’Android](device-manager-images/mac/14-android-screenshot-m76-sml.png)](device-manager-images/mac/14-android-screenshot-m76.png#lightbox)

   Si vous sélectionnez un niveau d’API Android qui n’a pas encore été installé, Device Manager affiche le message **Une nouvelle image d’appareil sera téléchargée** en bas de l’écran. Il télécharge et installe les fichiers nécessaires pendant la création de l’appareil virtuel :

   ![Une nouvelle image d’appareil sera téléchargée](device-manager-images/mac/15-automatic-download-m76.png)

5. Si vous souhaitez inclure les API Google Play Services dans votre appareil virtuel, activez l’option **API Google**. Pour inclure l’application Google Play Store, activez l’option **Google Play Store** :

   [![Sélection de Google Play Services et de Google Play Store](device-manager-images/mac/16-google-play-services-m76-sml.png)](device-manager-images/mac/16-google-play-services-m76.png#lightbox)

   Notez que les images du Google Play Store sont disponibles uniquement pour certains types d’appareil de base, par exemple Pixel, Pixel 2, Nexus 5 et Nexus 5X.

6. Modifiez les propriétés nécessaires. Pour apporter des modifications aux propriétés, consultez [Modification des propriétés d’appareil virtuel Android](~/android/get-started/installation/android-emulator/device-properties.md).

7. Ajoutez toute propriété supplémentaire que vous avez besoin de définir explicitement. L’écran **Nouvel appareil** liste uniquement les propriétés modifiées le plus souvent. Vous pouvez toutefois cliquer sur le menu déroulant **Ajouter une propriété** (en bas) pour ajouter des propriétés supplémentaires :

   [![Menu déroulant Ajouter une propriété](device-manager-images/mac/17-add-property-menu-m76-sml.png)](device-manager-images/mac/17-add-property-menu-m76.png#lightbox)

   Vous pouvez également définir une propriété personnalisée en cliquant sur **Personnaliser** en haut de cette liste des propriétés.

8. Cliquez sur le bouton **Créer** (dans l’angle inférieur droit) pour créer le nouvel appareil :

   ![Bouton Créer](device-manager-images/mac/18-create-button-m76.png)

9. Android Device Manager ajoute le nouvel appareil à la liste des appareils virtuels installés et affiche un indicateur de progression **Création en cours** pendant la création de l’appareil :

   [![Indicateur de progression de la création](device-manager-images/mac/19-creating-the-device-m76-sml.png)](device-manager-images/mac/19-creating-the-device-m76.png#lightbox)

10. Lorsque le processus de création est terminé, le nouvel appareil apparaît dans la liste des appareils virtuels installés avec un bouton **Démarrer**, prêt à être lancé :

    [![Appareil nouvellement créé prêt à être lancé](device-manager-images/mac/20-created-device-m76-sml.png)](device-manager-images/mac/20-created-device-m76.png#lightbox)

### <a name="edit-device"></a>Modifier l’appareil

Pour modifier un appareil virtuel existant, sélectionnez le menu déroulant **Options supplémentaires** (icône d’engrenage) et sélectionnez **Modifier** :

[![Sélection du bouton Modifier pour modifier un nouvel appareil](device-manager-images/mac/21-edit-button-m76-sml.png)](device-manager-images/mac/21-edit-button-m76.png#lightbox)

Le fait de cliquer sur le bouton **Modifier** lance l’Éditeur d’appareil pour l’appareil virtuel sélectionné :

[![Écran Éditeur d’appareil](device-manager-images/mac/22-device-editor-sml.png)](device-manager-images/mac/22-device-editor.png#lightbox)

L’écran **Éditeur d’appareil** liste les propriétés de l’appareil virtuel dans la colonne **Propriété**, ainsi que les valeurs correspondantes de chaque propriété dans la colonne **Valeur**. Lorsque vous sélectionnez une propriété, une description détaillée de cette propriété s’affiche à droite.

Pour changer une propriété, modifiez sa valeur dans la colonne **Valeur**.
Par exemple, dans la capture d’écran suivante, la propriété `hw.lcd.density` passe de **480** à **240** :

[![Exemple de modification d’un appareil](device-manager-images/mac/23-device-editing-sml.png)](device-manager-images/mac/23-device-editing.png#lightbox)

Après avoir apporté les modifications de configuration nécessaires, cliquez sur le bouton **Enregistrer**.
Pour plus d’informations sur la modification des propriétés des appareils virtuels, consultez [Modification des propriétés d’appareil virtuel Android](~/android/get-started/installation/android-emulator/device-properties.md).

### <a name="additional-options"></a>Options supplémentaires

Des options supplémentaires pour travailler avec un appareil sont disponibles dans le menu déroulant situé à gauche du bouton **Lire** :

[![Emplacement du menu Options supplémentaires](device-manager-images/mac/24-overflow-menu-sml.png)](device-manager-images/mac/24-overflow-menu.png#lightbox)

Le menu Options supplémentaires contient les éléments suivants :

- **Modifier** &ndash; Ouvre l’appareil sélectionné dans l’éditeur d’appareil, comme décrit précédemment.

- **Dupliquer et modifier** &ndash; Duplique l’appareil sélectionné et l’ouvre dans l’écran **Nouvel appareil** avec un nom unique différent. Par exemple, si vous sélectionnez **Pixel 2 API 28** et si vous cliquez sur **Dupliquer et modifier**, un compteur est ajouté au nom :

  [![Écran Duplicate et modifier](device-manager-images/mac/25-dupe-and-edit-sml.png)](device-manager-images/mac/25-dupe-and-edit.png#lightbox)

- **Révéler dans l’Explorateur** &ndash; Ouvre une fenêtre Finder macOS dans le dossier contenant les fichiers de l’appareil virtuel. Par exemple, si vous sélectionnez **Pixel 2 API 28** et si vous cliquez sur **Afficher dans le Finder**, une fenêtre similaire à l’exemple suivant s’affiche :

  [![Résultats du clic sur Afficher dans le Finder](device-manager-images/mac/26-reveal-in-finder-sml.png)](device-manager-images/mac/26-reveal-in-finder.png#lightbox)

- **Réinitialisation aux paramètres d’usine** &ndash; Réinitialise l’appareil aux paramètres par défaut, en effaçant toutes les modifications apportées par l’utilisateur à l’état interne de l’appareil pendant son exécution (l’instantané [Quick Boot](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot), s’il existe, est également effacé). Cette modification n’altère pas les modifications que vous apportez à l’appareil virtuel lors de la création et de la modification. Une boîte de dialogue rappelant que la réinitialisation ne peut pas être annulée s’affiche. Cliquez sur **Réinitialisation aux paramètres d’usine** pour confirmer la réinitialisation.

  ![Boîte de dialogue de réinitialisation aux paramètres d’usine](device-manager-images/mac/27-factory-reset-m76.png)

- **Supprimer** &ndash; Supprime définitivement l’appareil virtuel sélectionné. Une boîte de dialogue rappelant que la suppression d’un appareil ne peut pas être annulée s’affiche. Cliquez sur **Supprimer** si vous êtes certain que vous souhaitez supprimer l’appareil.

  ![Boîte de dialogue de suppression d’appareil](device-manager-images/mac/28-delete-device-m76.png)

-----

<a name="troubleshooting" />

## <a name="troubleshooting"></a>Résolution des problèmes

Les sections suivantes expliquent comment diagnostiquer et résoudre les problèmes qui peuvent se produire quand vous utilisez Android Device Manager pour configurer les appareils virtuels.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="android-sdk-in-non-standard-location"></a>Kit Android SDK dans un emplacement non standard

En règle générale, le kit Android SDK est installé à l’emplacement suivant :

**C:\\Program Files (x86)\\Android\\android-sdk**

Si le SDK n’est pas installé à cet emplacement, cette erreur peut s’afficher quand vous lancez Android Device Manager :

![Erreur d’instance du kit Android SDK](device-manager-images/win/29-sdk-error.png)

Pour contourner ce problème, effectuez les étapes suivantes :

1. À partir du bureau Windows, accédez à **C:\\Utilisateurs\\*nom d’utilisateur*\\AppData\\Roaming\\XamarinDeviceManager** :

   ![Emplacement du fichier journal d’Android Device Manager](device-manager-images/win/30-log-files.png)

2. Double-cliquez pour ouvrir l’un des fichiers journaux et recherchez le **chemin du fichier config**. Par exemple :

   [![Chemin du fichier config dans le fichier journal](device-manager-images/win/31-config-file-path-sml.png)](device-manager-images/win/31-config-file-path.png#lightbox)

3. Accédez à cet emplacement et double-cliquez sur **user.config** pour l’ouvrir.

4. Dans **user.config**, recherchez l’élément `<UserSettings>`, et ajoutez-lui un attribut **AndroidSdkPath**. Définissez cet attribut sur le chemin d’installation du kit Android SDK sur votre ordinateur et enregistrez le fichier. Par exemple, `<UserSettings>` ressemble à ce qui suit si le kit Android SDK est installé sur **C:\\Programs\\Android\\SDK** :

   ```xml
   <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:ProgramsAndroidSDK" />
   ```

Après avoir apporté cette modification à **user.config**, vous devriez pouvoir lancer Android Device Manager.

### <a name="wrong-version-of-android-sdk-tools"></a>Version incorrecte d’Android SDK Tools

Si Android SDK Tools version 26.1.1 ou ultérieure n’est pas installé, la boîte de dialogue d’erreur suivante peut s’afficher au lancement :

![Boîte de dialogue d’erreur d’instance du kit Android SDK](device-manager-images/win/32-sdk-instance-error.png)

Si cette boîte de dialogue d’erreur s’affiche, cliquez sur **Ouvrir SDK Manager** pour ouvrir Android SDK Manager. Dans Android SDK Manager, cliquez sur l’onglet **Outils**, puis installez les paquets suivants :

- **Android SDK Tools 26.1.1** ou ultérieur
- **Android SDK Platform-Tools {27.0.1}** ou ultérieur
- **Android SDK Build-Tools 27.0.3** ou ultérieur

### <a name="snapshot-disables-wifi-on-android-oreo"></a>La capture instantanée désactive le Wi-Fi sur Android Oreo

Si vous avez un AVD configuré pour Android Oreo avec un accès Wi-Fi simulé, le redémarrage de l’AVD après une capture instantanée peut désactiver l’accès Wi-Fi.

Pour éviter ce problème,

1. Sélectionnez l’AVD dans Android Device Manager.

2. Dans le menu des options supplémentaires, cliquez sur **Révéler dans l’Explorateur**.

3. Accédez à **capture instantanée > default_boot**.

4. Supprimer le fichier **snapshot.pb** :

   ![Emplacement du fichier snapshot.pb](device-manager-images/win/33-delete-snapshot.png)

5. Redémarrez l'AVD.

Une fois ces modifications apportées, l’AVD sera redémarré dans un état qui permet au Wi-Fi de fonctionner à nouveau.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

### <a name="wrong-version-of-android-sdk-tools"></a>Version incorrecte d’Android SDK Tools

Si Android SDK Tools version 26.1.1 ou ultérieure n’est pas installé, la boîte de dialogue d’erreur suivante peut s’afficher au lancement :

![Boîte de dialogue d’erreur d’instance du kit Android SDK](device-manager-images/mac/29-sdk-instance-error.png)

Si cette boîte de dialogue d’erreur s’affiche, cliquez sur **OK** pour ouvrir le Gestionnaire du kit Android SDK. Dans Android SDK Manager, cliquez sur l’onglet **Outils**, puis installez les paquets suivants :

- **Android SDK Tools 26.1.1** ou ultérieur
- **Android SDK Platform-Tools 28.0.1** ou version ultérieure
- **Android SDK Build-Tools 26.0.3** ou version ultérieure

### <a name="snapshot-disables-wifi-on-android-oreo"></a>La capture instantanée désactive le Wi-Fi sur Android Oreo

Si vous avez un AVD configuré pour Android Oreo avec un accès Wi-Fi simulé, le redémarrage de l’AVD après une capture instantanée peut désactiver l’accès Wi-Fi.

Pour éviter ce problème,

1. Sélectionnez l’AVD dans Android Device Manager.

2. Dans le menu des options supplémentaires, cliquez sur **Révéler dans le Finder**.

3. Accédez à **capture instantanée > default_boot**.

4. Supprimer le fichier **snapshot.pb** :

   [![Emplacement du fichier snapshot.pb](device-manager-images/mac/30-delete-snapshot-sml.png)](device-manager-images/mac/30-delete-snapshot.png#lightbox)

5. Redémarrez l'AVD.

Une fois ces modifications apportées, l’AVD sera redémarré dans un état qui permet au Wi-Fi de fonctionner à nouveau.

-----

### <a name="generating-a-bug-report"></a>Génération d’un rapport de bogues

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Si vous rencontrez un problème avec Android Device Manager qui ne peut pas être résolu à l’aide des conseils de résolution des problèmes ci-dessus, veuillez soumettre un rapport de bogue en cliquant sur la barre de titre et en sélectionnant **Générer un rapport de bogue** :

[![Emplacement de l’élément de menu pour soumettre un rapport de bogues](device-manager-images/win/34-bug-report-sml.png)](device-manager-images/win/34-bug-report.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Si vous rencontrez un problème avec Android Device Manager qui ne peut pas être résolu à l’aide des conseils ci-dessus, entrez un rapport de bogue en cliquant sur **Aide > Signaler un problème** :

[![Emplacement de l’élément de menu pour soumettre un rapport de bogues](device-manager-images/mac/31-bug-report-sml.png)](device-manager-images/mac/31-bug-report.png#lightbox)

::: zone-end

## <a name="summary"></a>Récapitulatif

Ce guide vous a présenté Android Device Manager, disponible dans Visual Studio Tools pour Xamarin et Visual Studio pour Mac. Les fonctionnalités essentielles vous ont été expliquées, comme le démarrage et l’arrêt de l’émulateur Android, la sélection d’un appareil virtuel Android (AVD) à exécuter, la création de nouveaux appareils virtuels et la manière de modifier un appareil virtuel. Ce guide a expliqué comment modifier les propriétés matérielles du profil pour une personnalisation plus poussée et a fourni des conseils de dépannage pour les problèmes courants.

## <a name="related-links"></a>Liens associés

- [Modifications des outils Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Débogage sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md)
- [Notes de publication de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/How-to-Create-and-Manage-Your-Own-Android-Emulators/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
