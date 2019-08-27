---
title: Publication indépendante
ms.prod: xamarin
ms.assetid: 6FB4DEF2-01AD-C5FE-0950-CE1BF088A9C6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 7d324d1faa045dcfbb2b3e6dcc3000a67b5f9e10
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525382"
---
# <a name="publishing-independently"></a>Publication indépendante

Il est possible de publier une application sans utiliser l’une des marketplaces Android existantes. Cette section présente ces autres méthodes de publication et les niveaux de licences de Xamarin.Android.

## <a name="xamarin-licensing"></a>Gestion des licences Xamarin

Un nombre de licences est disponible pour le développement, le déploiement et la distribution d’applications Xamarin.Android :

- **Visual Studio Community** &ndash; Pour les étudiants, les petites équipes et les développeurs OSS qui utilisent Windows.

- **Visual Studio Professional** &ndash; Pour les développeurs indépendants ou les petites équipes (Windows uniquement). Cette licence offre un abonnement standard ou cloud et aucune restriction d’utilisation.

- **Visual Studio Enterprise** &ndash; Pour les équipes de toute taille (Windows uniquement). Cette licence comprend des fonctionnalités d’entreprise, un abonnement standard ou cloud.

Visitez le site [visualstudio.com](https://visualstudio.microsoft.com/xamarin/) pour télécharger l’édition Community ou pour en savoir plus sur l’achat des éditions Professional et Enterprise.


## <a name="allow-installation-from-unknown-sources"></a>Autoriser l'installation pour les sources inconnues

Par défaut, Android empêche les utilisateurs de télécharger et d’installer des applications à partir d’emplacements autres que Google Play. Pour autoriser l’installation à partir de sources autres que des places de marché, un utilisateur doit activer le paramètre *Sources inconnues* sur un appareil avant d’installer une application. Ce paramètre peut se trouver sous **Paramètres > Sécurité**, comme illustré dans la capture d’écran suivante :

[![Écran Paramètres de sécurité](publishing-independently-images/settings.png)](publishing-independently-images/settings.png#lightbox)


> [!IMPORTANT]
> Certains fournisseurs réseau peuvent empêcher l’installation d’applications provenant de sources inconnues, indépendamment de ce paramètre.



## <a name="publishing-by-e-mail"></a>Publication par e-mail

Joindre l’APK de mise en production à un e-mail est un moyen simple et rapide de distribuer une application aux utilisateurs. Lorsque l’utilisateur ouvre l’e-mail sur un appareil Android, celui-ci reconnaît la pièce jointe APK et affiche un bouton **Installer**, comme illustré dans l’image suivante :

[![Bouton Installer pour la pièce jointe](publishing-independently-images/publishing-via-email.png)](publishing-independently-images/publishing-via-email.png#lightbox)

Certes simple, la distribution par e-mail n’offre qu’une faible protection contre le piratage ou la distribution non autorisée. Elle doit être réservée aux situations dans lesquelles les destinataires de l’application sont peu nombreux et dignes de confiance pour ne pas distribuer l’application.


## <a name="publishing-by-web"></a>Publication par le web

Il est possible de distribuer une application par un serveur web. L’application est chargée sur le serveur web, puis le lien de téléchargement est fourni aux utilisateurs. Quand un appareil Android accède à un lien, puis télécharge l’application, celle-ci est automatiquement installée une fois le téléchargement terminé.


## <a name="manually-installing-an-apk"></a>Installation manuelle d’un APK

L’installation manuelle est une troisième option pour installer des applications. Pour installer manuellement une application :

1. **Distribuez une copie de l’APK à l’utilisateur** &ndash; Par exemple, cette copie peut être distribuée sur un CD ou une clé USB.
1. **(L’utilisateur) installe l’application sur un appareil Android** &ndash; Utiliser l’outil en ligne de commande *Android Debug Bridge* (**adb**). **adb** est un outil en ligne de commande polyvalent qui permet la communication avec une instance d’émulateur ou un appareil Android. Le kit Android SDK inclut **adb** et se trouve dans le répertoire **\<sdk>/platform-tools/** .

L’appareil Android doit être connecté par un câble USB à l’ordinateur.
Les ordinateurs Windows auront peut-être besoin de pilotes USB supplémentaires mis à disposition par le fournisseur du téléphone pour être reconnus par **adb**. Les instructions d’installation de ces pilotes USB supplémentaires ne sont pas abordées dans ce document.

Avant d’émettre des commandes **adb**, il est utile de savoir quelles instances d’émulateur ou quels appareils sont connectés, le cas échéant. Il est possible d’afficher la liste des appareils connectés à l’aide de la commande `devices`, comme illustré dans l’extrait de code suivant :

```shell
$ adb devices
List of devices attached
        0149B2EC03012005device
```

Une fois les appareils connectés confirmés, l’application peut être installée en émettant la commande    `install` dans l’outil    **adb** :

```shell
$ adb install <path-to-apk>
```

L’extrait de code suivant montre un exemple d’installation d’une application sur un appareil connecté :

```shell
$ adb install helloworld.apk
3772 KB/s (3013594 bytes in 0.780s)
        pkg: /data/local/tmp/helloworld.apk
Success
```

Si l’application est déjà installée, la commande    `adb install` ne peut pas installer l’APK et signale un échec, comme illustré dans l’exemple suivant :

```shell
$ adb install helloworld.apk
4037 KB/s (3013594 bytes in 0.728s)
        pkg: /data/local/tmp/helloworld.apk
Failure [INSTALL_FAILED_ALREADY_EXISTS]
```

Il est nécessaire de désinstaller l’application de l’appareil. Tout d’abord, émettez la commande    `adb uninstall` :

```shell
adb uninstall <package_name>
```

L’extrait de code suivant est un exemple de désinstallation d’une application :

```shell
$ adb uninstall mono.samples.helloworld
Success
```
