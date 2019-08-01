---
title: Journal de débogage Android
description: Comment utiliser le journal de débogage pour déboguer des applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: edd6fc92603783dc9de64b10304e8d48f97bdef3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509209"
---
# <a name="android-debug-log"></a>Journal de débogage Android

L’une des astuces couramment utilisées par les développeurs pour déboguer leurs applications consiste à utiliser `Console.WriteLine`. Toutefois, sur une plateforme mobile comme Android, il n’y a pas de console. Les appareils Android proposent un journal que vous pourrez utiliser pendant l’écriture d’applications. Il est parfois nommé _logcat_ en raison de la commande saisie pour le récupérer. Utilisez l’outil **Journal de débogage** pour afficher les données enregistrées.

## <a name="android-debug-log-overview"></a>Vue d’ensemble du journal de débogage Android

L’outil **Journal de débogage** fournit un moyen d’afficher la sortie du journal pendant le débogage d’une application via Visual Studio. Le journal de débogage prend en charge les périphériques suivants :

-   Les téléphones, tablettes et wearables Android physiques.
-   Un appareil Android virtuel en cours d’exécution sur l’émulateur Android. 

> [!NOTE]
> L’outil **Journal de débogage** ne fonctionne pas avec Xamarin Live Player.

Le **Journal de débogage** n’affiche pas de messages du journal générés pendant l’exécution de l’application en mode autonome sur l’appareil (par exemple, pendant qu’il est déconnecté de Visual Studio).


## <a name="accessing-the-debug-log-from-visual-studio"></a>Accès au Journal de débogage à partir de Visual Studio

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour ouvrir l’outil **Journal de l’appareil**, cliquez sur l’icône **Journal de l’appareil (logcat)** dans la barre d’outils :

[![Emplacement de l’outil Journal de l’appareil sur la barre d’outils](android-debug-log-images/vswin-01-logcat-sml.png)](android-debug-log-images/vswin-01-logcat.png#lightbox)

Autrement, lancez l’outil **Journal de l’appareil** à partir d’une des sélections de menu suivantes :

-   **Affichage > Autres fenêtres > Journal de l’appareil**
-   **Outils > Android > Journal de l’appareil**

La capture d’écran suivante illustre les différentes parties de la fenêtre **Outil de débogage** :

[![Parties de la fenêtre Outils de débogage](android-debug-log-images/vswin-03-features-sml.png)](android-debug-log-images/vswin-03-features.png#lightbox)

-   **Sélecteur d’appareils** &ndash; sélectionne l’appareil physique ou l’émulateur en cours d’exécution à analyser.

-   **Entrées du journal** &ndash; une table de messages du journal à partir de logcat.

-   **Effacer les entrées du journal** &ndash; efface toutes les entrées du journal actuelles de la table.

-   **Lecture/Pause** &ndash; bascule entre la mise à jour ou la suspension de l’affichage des nouvelles entrées du journal.

-   **Arrêter** &ndash; arrête l’affichage de nouvelles entrées du journal.

-   **Zone de recherche** &ndash; entrez les chaînes de recherche dans cette zone pour filtrer un sous-ensemble d’entrées du journal.


Lorsque la fenêtre de l’outil **Journal de débogage** s’affiche, utilisez le menu déroulant de l’appareil pour sélectionner l’appareil Android à analyser :

[![Emplacement du Sélecteur d’appareils](android-debug-log-images/vswin-02-devices-combo-sml.png)](android-debug-log-images/vswin-02-devices-combo.png#lightbox)

Une fois l’appareil sélectionné, l’outil **Journal de l’appareil** ajoute automatiquement des entrées du journal à partir d’une application en cours d’exécution. Ces entrées de journal sont affichées dans la table des entrées de journal. Le basculement entre des appareils arrête et démarre la journalisation des appareils. Notez qu’un projet Android doit être chargé avant l’affichage des appareils dans le sélecteur d’appareils. Si l’appareil n’apparaît pas dans le sélecteur d’appareils, vérifiez qu’il est disponible dans le menu déroulant des appareils Visual Studio à côté du bouton **Démarrer**.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour ouvrir le **Journal de l’appareil**, cliquez sur **Afficher > Blocs > Journal de l’appareil** :

[![Emplacement de l’élément de menu Journal de l’appareil](android-debug-log-images/vsmac-01-logcat-sml.png)](android-debug-log-images/vsmac-01-logcat.png#lightbox)

La capture d’écran suivante illustre les différentes parties de la fenêtre **Outil de débogage** :

[![composants de la fenêtre Outils de débogage](android-debug-log-images/vsmac-03-features-sml.png)](android-debug-log-images/vsmac-03-features.png#lightbox)

-   **Sélecteur d’appareils** &ndash; sélectionne l’appareil physique ou l’émulateur en cours d’exécution à analyser.

-   **Entrées du journal** &ndash; une table de messages du journal à partir de logcat.

-   **Effacer les entrées du journal** &ndash; efface toutes les entrées du journal actuelles de la table.

-   **Zone de recherche** &ndash; entrez les chaînes de recherche dans cette zone pour filtrer un sous-ensemble d’entrées du journal.

-   **Afficher les Messages** &ndash; active ou désactive l’affichage des messages d’information.

-   **Afficher les avertissements** &ndash; active ou désactive l’affichage des messages d’avertissement (les messages d’avertissement sont affichés en jaune).

-   **Afficher les erreurs** &ndash; active ou désactive l’affichage des messages d’erreur (les messages d’erreur sont affichés en rouge).

-   **Reconnecter** &ndash; se reconnecte à l’appareil et actualise l’affichage des entrées du journal.

-   **Ajouter le marqueur** &ndash; insère un message de marqueur (tel que `--- Marker N ---`) après la dernière entrée du journal, où _N_ est un compteur qui démarre à partir de 1 et est incrémenté de 1 lors de l’ajout de nouveaux marqueurs.

Lorsque la fenêtre de l’outil Journal de débogage s’affiche, utilisez le menu déroulant de l’appareil pour sélectionner l’appareil Android à analyser :

[![Emplacement du Sélecteur d’appareils](android-debug-log-images/vsmac-02-devices-combo-sml.png)](android-debug-log-images/vsmac-02-devices-combo.png#lightbox)

Une fois l’appareil sélectionné, l’outil **Journal de l’appareil** ajoute automatiquement des entrées du journal à partir d’une application en cours d’exécution. Ces entrées de journal sont affichées dans la table des entrées de journal. Le basculement entre des appareils arrête et démarre la journalisation des appareils. Notez qu’un projet Android doit être chargé avant l’affichage des appareils dans le sélecteur d’appareils. Si l’appareil n’apparaît pas dans le sélecteur d’appareils, vérifiez qu’il est disponible dans le menu déroulant des appareils Visual Studio à côté du bouton **Démarrer**.

-----


## <a name="accessing-from-the-command-line"></a>Accès à partir de la ligne de commande

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Vous pouvez également afficher journal de débogage via la ligne de commande. Ouvrez une fenêtre d’invite de commandes et accédez au dossier des outils de plateforme du kit de développement logiciel (SDK) Android (en règle générale, le dossier des outils de plateforme du kit de développement logiciel (SDK) se trouve à l’adresse **C:\\Program Files (x86)\\Android\\android-sdk\\outils de plateforme**).

Si un seul appareil (appareil physique ou émulateur) est joint, le journal peut être consulté en entrant la commande suivante :

```shell
$ adb logcat
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Vous pouvez également afficher journal de débogage via la ligne de commande. Ouvrez une fenêtre Terminal et accédez au dossier des outils de plateforme du kit de développement logiciel (SDK) Android (en règle générale, le dossier des outils de plateforme du kit de développement logiciel (SDK) se trouve à l’adresse **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/platform-tools**).

Si un seul appareil (appareil physique ou émulateur) est joint, le journal peut être consulté en entrant la commande suivante :

```shell
$ ./adb logcat
```

-----


Si plusieurs appareils sont joints, l’appareil doit être identifié explicitement. Par exemple, **adb -d logcat** affiche le journal de l’unique appareil physique connecté, tandis que **adb -e logcat** affiche le journal de l’unique émulateur en cours d’exécution.

Plusieurs commandes sont accessibles en entrant **adb** et en lisant les messages d’aide.


## <a name="writing-to-the-debug-log"></a>Écriture dans le journal de débogage

Des messages peuvent être écrits dans le **Journal de débogage** en utilisant les méthodes de la classe [Android.Util.Log](xref:Android.Util.Log).
Par exemple : 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

Ceci produit une sortie semblable à la suivante :

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

Il est également possible d’utiliser `Console.WriteLine` pour écrire dans le **journal de débogage** &ndash; ces messages apparaissent dans logcat avec un format de sortie légèrement différent (cette technique est particulièrement utile lors du débogage des applications Xamarin.Forms sur Android) :

```csharp
System.Console.WriteLine ("DEBUG - Button Clicked!");
```

Ceci produit une sortie semblable à la suivante dans logcat :

```
Info (19543) / mono-stdout: DEBUG - Button Clicked!
```

## <a name="interesting-messages"></a>Messages intéressants

Lorsque vous lisez le journal (et en particulier lorsque vous fournissez des extraits de journaux à d’autres utilisateurs), l’examen du fichier journal dans son intégralité est souvent trop lourd.
Pour simplifier la navigation parmi les messages du journal, démarrerez en recherchant une entrée du journal qui ressemble à ceci :

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

Recherchez en particulier la ligne correspondant à l’expression régulière qui contient également le nom du package d’applications :

```shell
^I.*ActivityManager.*Starting: Intent
```

Cette ligne correspond au début d’une activité et *la plupart* des messages qui suivront sont normalement en rapport avec l’application.

Remarquez que chaque message contient l’identificateur de processus (pid) du processus générant le message. Dans le message `ActivityManager` ci-dessus, le processus `12944` a généré le message. Pour déterminer quel processus est le processus de l’application en cours de débogage, recherchez le message **mono.MonoRuntimeProvider** : 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

Ce message est fourni à partir du processus qui a été démarré. Tous les messages suivants qui contiennent ce pid proviennent du même processus.
