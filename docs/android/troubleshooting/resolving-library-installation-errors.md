---
title: Résolution des erreurs d’installation de la bibliothèque
description: Dans certains cas, vous pouvez obtenir des erreurs lors de l’installation de bibliothèques de support Android. Ce guide fournit des solution de contournement pour certaines erreurs courantes.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291505"
---
# <a name="resolving-library-installation-errors"></a>Résolution des erreurs d’installation de la bibliothèque

_Dans certains cas, vous pouvez obtenir des erreurs lors de l’installation de bibliothèques de support Android. Ce guide fournit des solution de contournement pour certaines erreurs courantes._

## <a name="overview"></a>Vue d’ensemble

Tout en construisant un projet d’application Xamarin.Android, vous pouvez obtenir des erreurs de construction lorsque Visual Studio ou Visual Studio pour Mac essayer de télécharger et d’installer des bibliothèques de dépendance. Bon nombre de ces erreurs sont causées par des problèmes de connectivité réseau, la corruption de fichiers ou des problèmes de version. Ce guide décrit les erreurs d’installation de bibliothèque de support les plus courantes et fournit les étapes pour contourner ces problèmes et obtenir votre projet d’application à nouveau.

## <a name="errors-while-downloading-m2repository"></a>Erreurs tout en téléchargeant m2Repository

Vous pouvez voir des erreurs **m2repository** lors du référencement d’un paquet NuGet des bibliothèques de support Android ou des services Google Play. Le message d’erreur est semblable au suivant :

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Cet exemple est pour **Android\_\_m2repository r16**, mais vous pouvez voir ce même message d’erreur pour une version différente comme **Android\_m2repository\_r18** ou android **\_m2repository\_r25**.

### <a name="automatic-recovery-from-m2repository-errors"></a>Récupération automatique des erreurs m2repository

Souvent, cette question peut être résolue en supprimant la bibliothèque problématique et en reconstruisant en fonction de ces étapes :

1. Naviguez vers l’annuaire de la bibliothèque de support sur votre ordinateur :

    - Sur Windows, les bibliothèques de support sont situées à **C:\\Users\\nom_d’utilisateur_\\AppData\\Local\\Xamarin**.

    - Sur Mac OS X, les bibliothèques de support sont situées à **/Utilisateurs/_nom d’utilisateur_/.local/share/Xamarin**.

2. Localisez la bibliothèque et le dossier de version correspondant au message d’erreur. Par exemple, le dossier de bibliothèque et de version pour le message d’erreur ci-dessus est situé à **Android.Support.v4\\22.2.1**:

    [![Exemple d’emplacement de dossier pour la bibliothèque de support 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Supprimer le contenu du dossier de version. Assurez-vous de supprimer le fichier **.zip** ainsi que le **contenu** et les sous-directions **intégrés** dans ce dossier. Pour l’exemple du message d’erreur ci-dessus, les fichiers et sous-directeurs affichés dans cette capture d’écran **(contenu**, **intégré**, et **android_m2repository_r16.zip**) doivent être supprimés:

    [![Exemple de contenu du dossier de bibliothèque de support 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Notez qu’il est important de supprimer *l’ensemble* du contenu de ce dossier. Bien que ce dossier puisse initialement contenir le fichier "manquant" **Android\_m2repository\_r16.zip,** ce fichier peut avoir été partiellement téléchargé ou corrompu.

4. Reconstruire le &ndash; projet, ce qui entraînera le processus de construction de re-télécharger la bibliothèque manquante.

Dans la plupart des cas, ces étapes résoudront l’erreur de construction et vous permettront de continuer. Si la suppression de cette bibliothèque ne résout pas l’erreur de construction, vous devez télécharger manuellement et installer le fichier **android\_m2repository\_r_nn_.zip** tel que décrit dans la section suivante.

### <a name="manually-downloading-m2repository"></a>Téléchargement manuel m2repository

Si vous avez essayé d’utiliser les étapes de récupération automatique ci-dessus et ont encore des erreurs de construction, vous pouvez télécharger manuellement le **fichier android\_m2repository\_r_nn_.zip** (à l’aide d’un navigateur web) et l’installer selon les étapes suivantes. Cette procédure est également utile si vous n’avez pas accès à Internet sur votre ordinateur de développement, mais vous êtes en mesure de télécharger l’archive à l’aide d’un ordinateur différent.

1. Téléchargez le fichier **android\_\_m2repository r_nn_.zip** qui correspond &ndash; aux liens de message d’erreur sont fournis dans la liste suivante (avec le hachage MD5 correspondant de l’URL de chaque lien):

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [Android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [Android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94D7F2C8F

    - [Android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC6530E7D695C41

    - [Android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [Android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [Android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C31BB36420C1D8992AF54A4D

    - [Android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [Android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [Android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [Android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [Android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB6152FAFDA0E

    - [Android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F4AE5F35A6BA9D3C17EFD8

    - [Android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Si **l’archive m2repository** n’est pas affichée dans ce `https://dl-ssl.google.com/android/repository/` tableau, vous pouvez créer l’URL de téléchargement en préenchantant au nom du **m2repository** à télécharger. Par exemple, **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** utiliser pour télécharger **\_Android\_m2repository r10.zip**.

2. Rebaptiser le fichier au hachage MD5 correspondant de l’URL de téléchargement tel qu’indiqué dans le tableau ci-dessus. Par exemple, si vous avez téléchargé **Android\_m2repository\_r25.zip**, renommer à **0B3F1796C97C707339FB13AE8507AF50.zip**. Si le hachage MD5 pour l’URL de téléchargement du fichier téléchargé n’est pas affiché dans le tableau, vous pouvez utiliser un [générateur MD5 en ligne](http://www.webconfs.com/online-md5-generator.php) pour convertir l’URL en chaîne de hachage MD5.

3. Copiez le fichier du dossier **zips** Xamarin :

    - Sur Windows, ce dossier est situé à **\\C:\\Users\\nom\\***d’utilisateur***\\AppData\\Local Xamarin zips**.

    - Sur Mac OS X, ce dossier est situé à **/Utilisateurs/***nom d’utilisateur***/.local/share/Xamarin/zips**.

    Par exemple, la capture d’écran suivante illustre le résultat lorsque **Android\_m2repository\_r16.zip** est téléchargé et renommé sur le hachage MD5 de son URL de téléchargement sur Windows:

    [![Exemple du référentiel r16.zip rebaptisé 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Si cette procédure ne résout pas l’erreur de construction, vous devez télécharger manuellement le fichier **\_android m2repository\_r_nn_.zip,** le décompresser, et installer son contenu tel que décrit dans la section suivante.

### <a name="manually-downloading-and-installing-m2repository-files"></a>Téléchargement manuel et installation de fichiers m2repository

Le processus entièrement manuel pour récupérer des erreurs **m2repository** implique le téléchargement de **l’android\_m2repository\_r_nn_.zip** fichier (à l’aide d’un navigateur web), le décompresser, et la copie de son contenu à l’annuaire de la bibliothèque de soutien sur votre ordinateur. Dans l’exemple suivant, nous nous remettrons de ce message d’erreur :

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Utilisez les étapes suivantes pour télécharger **m2repository** et installer son contenu :

1. Supprimer le contenu du dossier de bibliothèque correspondant au message d’erreur. Par exemple, dans le message d’erreur ci-dessus, vous supprimeriez le contenu de **C:\\Users\\***username***\\AppData\\Local\\Xamarin\\Android.Support.v4\\23.1.1.0**.
    Comme décrit précédemment, vous devez supprimer l’ensemble du contenu de ce répertoire :

    [![Suppression du contenu, des dossiers intégrés et android_m2repository du dossier 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Téléchargez le fichier **\_android\_m2repository r_nn_.zip** de Google qui correspond au message d’erreur (voir la table dans la section précédente pour les liens).

3. Extraire cette archive **.zip** à n’importe quel endroit (tel que le bureau). Cela devrait créer un répertoire qui correspond au nom de l’archive **.zip.** Dans ce répertoire, vous devriez trouver une sous-direction appelée **m2repository**:

    [![dossier m2repository trouvé dans les archives zip extraites](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. Dans l’annuaire de la bibliothèque version que vous avez purgé à l’étape 1, recréez le **contenu** et les sous-directeurs **intégrés.** Par exemple, la capture d’écran suivante illustre le **contenu** et les sous-directeurs **intégrés** créés dans le dossier **23.1.1.0** pour **android\_m2repository\_r25.zip**:

    [![Créer du contenu et des dossiers intégrés dans le dossier 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copiez **m2repository** de la **.zip** extraite dans le répertoire de **contenu** que vous avez créé dans l’étape précédente:

    [![Capture d’écran du dossier m2repository copié à 23.1.1.0/content dossier](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. Dans le répertoire **.zip** extrait, naviguez sur **\\m2repository com\\android\\support-v4\\** et ouvrez le dossier correspondant au numéro de version créé ci-dessus (dans cet exemple, **23.1.1**):

    [![Liste d’exemples de fichiers contenus dans le dossier support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copiez tous les fichiers de ce dossier à l’annuaire **intégré** créé à l’étape 4 :

    [![Exemple de fichiers copiés sur le dossier 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Vérifiez que tous les fichiers sont copiés. **L’annuaire intégré** devrait maintenant contenir des fichiers tels que **.jar**, **.aar**, et **.pom**.

9. Décompressez le contenu de tous les fichiers **.aar** extraits au répertoire **intégré.** Sur Windows, appendez une extension **.zip** au fichier **.aar,** ouvrez-le et copiez le contenu de l’annuaire **intégré.**
    Sur macOS, dézipez le fichier **.aar** en utilisant la commande **unzip** dans le terminal (par exemple, **unzip file.aar**).

À ce stade, vous avez installé manuellement les composants manquants et votre projet doit construire sans erreurs. Si ce n’est pas le cas, vérifiez que vous avez téléchargé la version d’archivage **.zip** **m2repository** qui correspond exactement à la version dans le message d’erreur, et vérifiez que vous avez installé son contenu dans les emplacements corrects tels que décrits dans les étapes ci-dessus.

## <a name="summary"></a>Récapitulatif

Cet article expliquait comment se remettre d’erreurs courantes qui peuvent avoir lieu lors du téléchargement automatique et de l’installation de bibliothèques de dépendance. Il a décrit comment supprimer la bibliothèque problématique et reconstruire le projet comme un moyen de re-télécharger et de réin installation de la bibliothèque.
Il a décrit comment télécharger la bibliothèque et l’installer dans le dossier **zips.** Il a également décrit une procédure plus impliquée pour le téléchargement manuel et l’installation des fichiers nécessaires comme un moyen de contourner les problèmes qui ne peuvent pas être résolus par des moyens automatiques.
