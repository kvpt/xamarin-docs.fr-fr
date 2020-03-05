---
title: Résolution des erreurs d’installation de la bibliothèque
description: Dans certains cas, vous pouvez recevoir des erreurs lors de l’installation des bibliothèques de support Android. Ce guide fournit des solutions de contournement pour certaines erreurs courantes.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291505"
---
# <a name="resolving-library-installation-errors"></a>Résolution des erreurs d’installation de la bibliothèque

_Dans certains cas, vous pouvez recevoir des erreurs lors de l’installation des bibliothèques de support Android. Ce guide fournit des solutions de contournement pour certaines erreurs courantes._

## <a name="overview"></a>Vue d’ensemble

Lors de la génération d’un projet d’application Xamarin. Android, vous pouvez recevoir des erreurs de génération quand Visual Studio ou Visual Studio pour Mac tentent de télécharger et d’installer des bibliothèques de dépendances. La plupart de ces erreurs sont dues à des problèmes de connectivité réseau, à des fichiers endommagés ou à des problèmes de contrôle de version. Ce guide décrit les erreurs d’installation de la bibliothèque de prise en charge la plus courante et fournit les étapes permettant de contourner ces problèmes et de réintégrer le projet d’application.

## <a name="errors-while-downloading-m2repository"></a>Erreurs lors du téléchargement de m2Repository

Vous pouvez voir des erreurs **m2repository** lors du référencement d’un package NuGet des bibliothèques de prise en charge Android ou des services de Google Play. Le message d’erreur est semblable au suivant :

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Cet exemple s’affiche pour **android\_m2repository\_R16**, mais il se peut que vous rencontriez ce même message d’erreur pour une version différente, par exemple **android\_m2repository\_r18** ou **Android\_m2repository\_R25**.

### <a name="automatic-recovery-from-m2repository-errors"></a>Récupération automatique à partir d’erreurs m2repository

Souvent, ce problème peut être résolu en supprimant la bibliothèque problématique et en reconstruisant les étapes suivantes :

1. Accédez au répertoire de la bibliothèque de prise en charge sur votre ordinateur :

    - Sur Windows, les bibliothèques de prise en charge se trouvent dans **C :\\utilisateurs\\_nom d’utilisateur_\\AppData\\local\\Xamarin**.

    - Sur Mac OS X, les bibliothèques de prise en charge se trouvent dans **/Users/_nom_utilisateur_/.local/share/Xamarin**.

2. Localisez le dossier de la bibliothèque et de la version correspondant au message d’erreur. Par exemple, le dossier Bibliothèque et version pour le message d’erreur ci-dessus se trouve dans **Android. support. v4\\22.2.1**:

    [![exemple d’emplacement de dossier pour la bibliothèque de prise en charge 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Supprimez le contenu du dossier de version. Veillez à supprimer le fichier **. zip** , ainsi que le **contenu** et les sous-répertoires **incorporés** dans ce dossier. Pour l’exemple de message d’erreur ci-dessus, les fichiers et les sous-répertoires indiqués dans cette capture d’écran (**contenu**, **incorporé**et **android_m2repository_r16. zip**) doivent être supprimés :

    [![exemple de contenu du dossier de la bibliothèque de prise en charge 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Notez qu’il est important de supprimer *tout* le contenu de ce dossier. Bien que ce dossier puisse contenir initialement le fichier « Missing » **android\_m2repository\_R16. zip** , ce fichier a peut-être été partiellement téléchargé ou endommagé.

4. Si vous régénérez le projet &ndash; cela entraînera le nouveau téléchargement de la bibliothèque manquante par le processus de génération.

Dans la plupart des cas, ces étapes résolvent l’erreur de build et vous permettent de continuer. Si la suppression de cette bibliothèque ne résout pas l’erreur de build, vous devez télécharger et installer manuellement **android\_m2repository\_r_nn_ fichier. zip** , comme décrit dans la section suivante.

### <a name="manually-downloading-m2repository"></a>Téléchargement manuel de m2repository

Si vous avez essayé d’utiliser les étapes de récupération automatique ci-dessus et que vous rencontrez toujours des erreurs de build, vous pouvez télécharger manuellement le fichier **android\_m2repository\_r_nn_. zip** (à l’aide d’un navigateur Web) et l’installer en suivant les étapes ci-dessous. Cette procédure est également utile si vous n’avez pas accès à Internet sur votre ordinateur de développement, mais que vous pouvez télécharger l’archive à l’aide d’un autre ordinateur.

1. Téléchargez le fichier **android\_m2repository\_r_nn_. zip** qui correspond au message d’erreur &ndash; des liens sont fournis dans la liste suivante (avec le hachage MD5 correspondant de l’URL de chaque lien) :

    - [android\_m2repository\_R33. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_R32. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_R31. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_R30. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_R29. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_R28. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2repository\_R27. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_R26. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_R25. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_R24. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_R23. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_R22. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_R21. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_R20. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_R19. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [android\_m2repository\_R18. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_R17. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_R16. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Si l’archive **m2repository** n’est pas affichée dans ce tableau, vous pouvez créer l’URL de téléchargement en ajoutant `https://dl-ssl.google.com/android/repository/` au nom du **m2repository** à télécharger. Par exemple, utilisez **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** pour télécharger **android\_m2repository\_R10. zip**.

2. Renommez le fichier avec le hachage MD5 correspondant de l’URL de téléchargement, comme indiqué dans le tableau ci-dessus. Par exemple, si vous avez téléchargé **android\_m2repository\_R25. zip**, renommez-le **0B3F1796C97C707339FB13AE8507AF50. zip**. Si le hachage MD5 de l’URL de téléchargement du fichier téléchargé n’est pas indiqué dans la table, vous pouvez utiliser un [Générateur MD5 en ligne](http://www.webconfs.com/online-md5-generator.php) pour convertir l’URL en chaîne de hachage MD5.

3. Copiez le fichier dans le dossier Xamarin **compresse** :

    - Sur Windows, ce dossier se trouve à l’emplacement **C :\\utilisateurs\\***nom d’utilisateur***\\AppData\\\\locale Xamarin\\** .

    - Sur Mac OS X, ce dossier se trouve dans **/Users/***nom_utilisateur***/.local/share/Xamarin/zips**.

    Par exemple, la capture d’écran suivante illustre le résultat lorsque **android\_m2repository\_R16. zip** est téléchargé et renommé en hachage MD5 de son URL de téléchargement sur Windows :

    [![exemple de référentiel R16. zip renommé en 0595E577D19D31708195A83087881EE6. zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Si cette procédure ne résout pas l’erreur de build, vous devez télécharger manuellement le fichier **android\_m2repository\_r_nn_. zip** , le décompresser et installer son contenu comme décrit dans la section suivante.

### <a name="manually-downloading-and-installing-m2repository-files"></a>Téléchargement et installation manuels des fichiers m2repository

Le processus entièrement manuel pour la récupération à partir d’erreurs **m2repository** implique le téléchargement du fichier **android\_m2repository\_r_nn_. zip** (à l’aide d’un navigateur Web), sa décompression et la copie de son contenu dans le répertoire de la bibliothèque de prise en charge sur votre ordinateur. Dans l’exemple suivant, nous allons récupérer à partir de ce message d’erreur :

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Procédez comme suit pour télécharger **m2repository** et installer son contenu :

1. Supprimez le contenu du dossier de bibliothèque correspondant au message d’erreur. Par exemple, dans le message d’erreur ci-dessus, vous supprimez le contenu de **C :\\utilisateurs\\***nom d’utilisateur***\\AppData\\Local\\Xamarin\\Android. Support. v4\\23.1.1.0**.
    Comme décrit précédemment, vous devez supprimer tout le contenu de ce répertoire :

    [![suppression de dossiers de contenu, incorporés et android_m2repository à partir du dossier 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Téléchargez le fichier **android\_m2repository\_r_nn_. zip** à partir de Google qui correspond au message d’erreur (voir le tableau de la section précédente pour les liens).

3. Extrayez cette archive **. zip** à n’importe quel emplacement (par exemple, le bureau). Cela doit créer un répertoire qui correspond au nom de l’archive **. zip** . Dans ce répertoire, vous devez trouver un sous-répertoire appelé **m2repository**:

    [![dossier m2repository trouvé dans l’archive zip extraite](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. Dans le répertoire de la bibliothèque avec version que vous avez purgé à l’étape 1, recréez le **contenu** et les sous-répertoires **incorporés** . Par exemple, la capture d’écran suivante illustre le **contenu** et les sous-répertoires **incorporés** en cours de création dans le dossier **23.1.1.0** pour **Android\_m2repository\_R25. zip**:

    [![créer du contenu et des dossiers incorporés dans le dossier 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copiez **m2repository** à partir du fichier **. zip** extrait dans le répertoire de **contenu** que vous avez créé à l’étape précédente :

    [Capture d’écran de ![de m2repository copiée dans le dossier 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. Dans le répertoire **. zip** extrait, accédez à **m2repository\\com\\Android\\prise en charge\\prise en charge-v4** , puis ouvrez le dossier correspondant au numéro de version créé ci-dessus (dans cet exemple, **23.1.1**) :

    [![exemple de liste des fichiers contenus dans le dossier Support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copiez tous les fichiers de ce dossier dans le répertoire **incorporé** créé à l’étape 4 :

    [![exemple de fichiers copiés dans le dossier 23.1.1.0/Embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Vérifiez que tous les fichiers sont copiés. Le répertoire **incorporé** doit maintenant contenir des fichiers tels que **. jar**, **. AAR**et **. fichier POM**.

9. Décompressez le contenu des fichiers **. AAR** extraits dans le répertoire **incorporé** . Sur Windows, ajoutez une extension **. zip** au fichier **. AAR** , ouvrez-la et copiez le contenu dans le répertoire **incorporé** .
    Sur macOS, décompressez le fichier **. AAR** à l’aide de la commande **Unzip** dans le terminal (par exemple, **Unzip file. AAR**).

À ce stade, vous avez installé manuellement les composants manquants et votre projet doit être généré sans erreur. Si ce n’est pas le cas, vérifiez que vous avez téléchargé la version de l’archive **m2repository** **. zip** qui correspond exactement à la version figurant dans le message d’erreur et vérifiez que vous avez installé son contenu aux emplacements corrects, comme décrit dans les étapes ci-dessus.

## <a name="summary"></a>Résumé

Cet article explique comment récupérer des erreurs courantes qui peuvent se produire pendant le téléchargement et l’installation automatiques des bibliothèques de dépendances. Il a décrit comment supprimer la bibliothèque problématique et reconstruire le projet comme un moyen de retélécharger et réinstaller la bibliothèque.
Il a décrit comment télécharger la bibliothèque et l’installer dans le dossier de **compresse** . Elle a également décrit une procédure plus complexe pour télécharger et installer manuellement les fichiers nécessaires afin de contourner les problèmes qui ne peuvent pas être résolus par le biais d’une méthode automatique.
