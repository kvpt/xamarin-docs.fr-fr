---
title: Prise en charge d’IPA dans Xamarin.iOS
description: Cet article explique comment créer un fichier IPA afin de déployer une application à l’aide de la distribution ad hoc, soit pour des tests, soit pour distribuer en interne des applications internes.
ms.prod: xamarin
ms.assetid: D253C2DB-852E-6FC6-C9FD-574730B8DB19
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: c430d7771d443b80bec2a3e8a79565e2a7994a8b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887577"
---
# <a name="ipa-support-in-xamarinios"></a>Prise en charge d’IPA dans Xamarin.iOS

_Cet article explique comment créer un fichier IPA afin de déployer une application à l’aide de la distribution ad hoc, soit pour des tests, soit pour distribuer en interne des applications internes._

Non seulement vous pouvez mettre en vente une application sur l’App Store d’iTunes, mais vous pouvez également la déployer pour les utilisations suivantes :

- **Tests ad hoc** - Vous pouvez déployer une application iOS pour un nombre maximal de 100 utilisateurs (identifiés par les UUID d’appareils iOS spécifiques) à des fins de test de versions alpha et bêta. Pour obtenir des informations détaillées sur l’ajout d’appareils iOS de test à votre compte de développeur Apple, consultez [Provisionnement d’un appareil iOS pour le développement](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning) dans notre documentation. Pour plus d’informations sur la distribution d’applications selon le mode ad hoc, consultez également le guide [Ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md).
- **Déploiement en interne/entreprise** - Une application iOS peut être déployée en interne, au sein d’une entreprise, ce qui implique l’adhésion au programme Developer Enterprise d’Apple. Pour plus d’informations sur la distribution en interne, consultez le guide [Distribution en interne](~/ios/deploy-test/app-distribution/in-house-distribution.md).

Dans les deux cas, un paquet IPA (type spécial de fichier zip) doit être créé et signé numériquement avec le profil de provisionnement de distribution approprié. Cet article décrit les étapes nécessaires pour générer le paquet IPA et l’installer sur un appareil iOS à l’aide d’iTunes depuis un Mac ou un PC Windows.

<a name="iTunesMetadata" />

## <a name="the-itunesmetadataplist-file"></a>Fichier iTunesMetadata.plist

Quand un développeur crée une application iOS dans iTunes Connect (qu’elle soit gratuite ou payante sur l’App Store d’iTunes), il peut spécifier des informations telles que le genre, le sous-genre, le copyright, les appareils iOS pris en charge, ainsi que les fonctionnalités offertes.

Les applications iOS fournies via une distribution ad hoc ou en interne doivent pouvoir prendre en charge ces informations pour qu’elles soient visibles sur iTunes et sur l’appareil de l’utilisateur. Par défaut, un petit fichier iTunesMetadata.plist est créé chaque fois que vous générez votre projet. Il est stocké dans le répertoire du projet.

Vous pouvez également créer un fichier **iTunesMetadata.plist** personnalisé pour fournir les informations supplémentaires d’une distribution. Pour plus d’informations sur le contenu de ce fichier et sur la manière de le créer, consultez [Contenu d’iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_contents) et [Création d’un fichier iTunesMetadata.plist ](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_creating) dans notre documentation.

<a name="iTunesArtwork" />

## <a name="itunes-artwork"></a>Conception graphique iTunes

Quand vous distribuez votre application via d’autres moyens que l’App Store, vous devez également inclure une image de 512 x 512 pixels et de 1 024 x 1 024 pixels, qui est utilisée pour représenter votre application dans iTunes.

Pour spécifier les illustrations iTunes, effectuez les tâches suivantes :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Info.plist** pour l’ouvrir et le modifier.
2. Faites défiler l’affichage jusqu’à la section **Conception graphique iTunes** de l’éditeur.
3. Pour toute image manquante, cliquez sur la miniature appropriée dans l’éditeur. À partir de la boîte de dialogue **Ouvrir un fichier**, sélectionnez le fichier image correspondant à l’illustration iTunes souhaitée, puis cliquez sur le bouton **OK** ou **Ouvrir**.
4. Répétez cette étape jusqu’à ce que toutes les images nécessaires aient été spécifiées pour votre application.

Pour plus d’informations, consultez [Conception graphique iTunes](~/ios/app-fundamentals/images-icons/app-icons.md) dans la documentation.

<a name="createipa" />

## <a name="creating-an-ipa"></a>Création d’un fichier IPA

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

La création d’un fichier IPA est désormais intégrée au nouveau workflow de publication. Suivez les instructions ci-dessous pour archiver votre application, la signer et enregistrer votre fichier IPA.

Avant de commencer à créer un fichier IPA pour une solution multiplateforme, vérifiez que vous avez sélectionné le projet iOS en tant que projet de démarrage :

![](ipa-support-images/setasstartup.png "Projet iOS sélectionné en tant que projet de démarrage")

### <a name="build-your-archive"></a>Générer votre archive

Pour générer un fichier IPA, nous devons créer une _archive_ d’une build de mise en production de notre application. Cette archive contient notre application et les informations d’identification correspondantes.


1. Sélectionnez la configuration **Mise en production | Appareil** dans Visual Studio pour Mac :

    ![](ipa-support-images/buildxs01new.png "Sélectionner la configuration Mise en production | Appareil")

1. Dans le menu **Générer**, sélectionnez **Archiver pour publication** :

    ![](ipa-support-images/buildxs02new.png "Sélectionner Archiver pour publication")

1. Une fois l’archive créée, l’affichage **Archives** est présenté :

    ![](ipa-support-images/buildxs03new.png "L’affichage Archives est présenté")


### <a name="sign-and-distribute-your-app"></a>Signer et distribuer votre application

Chaque fois que vous générez votre application pour qu’elle soit archivée, l’**affichage Archives** s’ouvre automatiquement, ce qui entraîne l’affichage de tous les projets archivés, regroupés par solution. Par défaut, cet affichage montre uniquement la solution actuelle, ouverte. Pour afficher toutes les solutions ayant des archives, cliquez sur l’option **Afficher toutes les archives**.

Il est recommandé de conserver les archives déployées auprès des clients (déploiements ad hoc ou Interne) pour que les informations de débogage générées puissent être symbolisées plus tard.

Notez que pour les builds non liées à l’App Store, le fichier **iTunesMetadata.plist** et les illustrations iTunes sont automatiquement inclus dans votre fichier IPA, s’ils se trouvent dans l’archive.

Pour signer votre application et préparer sa distribution :


1. Cliquez sur le bouton **Signer et distribuer**, comme indiqué dans l’illustration ci-dessous :

    ![](ipa-support-images/buildxs04new.png "Sélectionner Signer et distribuer")

1. Cela entraîne l’ouverture de l’Assistant Publication. Sélectionnez le canal de distribution **Ad hoc** ou **Entreprise**(interne) pour créer un paquet :

    ![](ipa-support-images/distribute01.png "Sélectionner le canal de distribution Ad hoc ou Entreprise (interne)")

1. Dans l’écran Profil de provisionnement, sélectionnez votre identité de signature et le profil de provisionnement correspondant, ou reconnectez-vous avec une autre identité :

    ![](ipa-support-images/distribute02.png "Sélectionner l’identité de signature et le profil de provisionnement correspondant")

1. Vérifiez les détails du paquet, puis cliquez sur **Publier** :

    ![](ipa-support-images/distribute03.png "Vérifier les détails du paquet")

1. Enfin, enregistrez le fichier IPA sur votre machine :

    ![](ipa-support-images/distribute04.png "Enregistrer le fichier IPA sur l’ordinateur")


### <a name="building-via-the-command-line-on-mac"></a>Génération via la ligne de commande (sur Mac)

Dans certains cas, par exemple dans un environnement d’intégration continue (CI), vous devez créer le fichier IPA via la ligne de commande. Suivez les étapes ci-dessous pour y parvenir :


1. Vérifiez que la case **Options du projet > Options IPA iOS > Inclure les images iTunesArtwork** est cochée, et que la case **Générer un paquet ad-hoc/enterprise (IPA)** est cochée :

    ![](ipa-support-images/imagexs04.png "Vérifier que les cases Inclure les images iTunesArtwork et Générer un paquet ad-hoc/enterprise (IPA) sont cochées")

    Si vous préférez, vous pouvez modifier le fichier **.csproj** dans un éditeur de texte et ajouter manuellement les deux propriétés correspondantes à `PropertyGroup` pour la configuration servant à générer l’application :

    ```xml
    <BuildIpa>true</BuildIpa>
    <IpaIncludeArtwork>false</IpaIncludeArtwork>
    ```

1. Si vous incluez un fichier **iTunesMetadata.plist** facultatif, cliquez sur le bouton **...** , sélectionnez-le dans la liste, puis cliquez sur **OK** :

     ![](ipa-support-images/imagexs03.png "Sélectionner iTunesMetadata.plist dans la liste")

1. Appelez **msbuild** directement et passez cette propriété sur la ligne de commande :

    ```bash
    /Library/Frameworks/Mono.framework/Commands/msbuild YourSolution.sln /p:Configuration=Ad-Hoc /p:Platform=iPhone /p:BuildIpa=true
    ```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Une fois que vous avez créé et sélectionné le profil de provisionnement, que vous avez créé le fichier facultatif **iTunesMetadata.plist** et que vous avez défini la conception graphique iTunes dans Visual Studio, vous pouvez générer un fichier IPA à distribuer. Vous devez ensuite configurer votre projet. Effectuez ce qui suit :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet Xamarin.iOS, puis sélectionnez **Propriétés** pour modifier ces dernières :

    ![](ipa-support-images/imagevs01.png "Sélectionner les propriétés")

2. Sélectionnez **Options IPA iOS**, puis **Ad hoc** dans la liste déroulante **Configuration** :

    ![](ipa-support-images/imagevs02.png "Sélectionner Ad hoc dans la liste déroulante Configuration")

    > [!NOTE]
    > Une configuration ad hoc n’est pas forcément disponible pour les nouveaux projets Xamarin.iOS. Si elle n’est pas disponible, sélectionnez la configuration **Mise en production**.

3. Si vous incluez un fichier **iTunesMetadata.plist** facultatif, cliquez sur le bouton **...** , sélectionnez-le dans la liste, puis cliquez sur **Ouvrir** :

    ![](ipa-support-images/imagevs03.png "Sélectionner iTunesMetadata.plist dans la liste")

4. Vous pouvez éventuellement spécifier un **Nom de paquet** pour le fichier IPA. Sinon, il aura le même nom que le projet Xamarin.iOS.
5. Enregistrez les changements apportés aux propriétés du projet.
6. Sélectionnez **Ad hoc** dans la liste déroulante **Configuration de build**, si ce choix est disponible. Sinon, sélectionnez **Mise en production** :

    ![](ipa-support-images/imagevs05.png "Sélectionner Ad hoc dans la liste déroulante Configuration de build")

7. Générez le projet pour créer le paquet IPA.
8. Le fichier IPA est généré dans le dossier **Bin > Appareil iOS > Ad hoc (ou Mise en production)**  :

    ![](ipa-support-images/imagevs06.png "Paquet IPA dans l’Explorateur de fichiers")

-----

<a name="Customizing-the-IPA-Location" />

## <a name="customizing-the-ipa-location"></a>Personnalisation de l’emplacement du fichier IPA

Une nouvelle propriété **MSBuild** `IpaPackageDir` a été ajoutée pour faciliter la personnalisation de l’emplacement de sortie du fichier **.ipa**. Si `IpaPackageDir` est défini à l’aide d’un emplacement personnalisé, le fichier **.ipa** est stocké dans cet emplacement au lieu du sous-répertoire horodaté par défaut. Cela peut être utile quand vous créez des builds automatisées qui reposent sur un chemin de répertoire spécifique pour fonctionner correctement, par exemple les build d’intégration continue (CI).

Il existe plusieurs façons d’utiliser la nouvelle propriété :

Par exemple, pour envoyer le fichier **.ipa** vers l’ancien répertoire par défaut (comme dans Xamarin.iOS 9.6 et les versions antérieures), vous pouvez affecter à la propriété `IpaPackageDir` la valeur `$(OutputPath)` en adoptant l’une des approches suivantes. Les deux approches sont compatibles avec toutes les builds d’API unifiée Xamarin.iOS, notamment les builds d’IDE et les builds de ligne de commande qui utilisent **msbuild**, **xbuild** ou **mdtool** :

- La première option consiste à définir la propriété `IpaPackageDir` dans un élément `<PropertyGroup>` d’un fichier **MSBuild**. Par exemple, vous pouvez ajouter le `<PropertyGroup>` suivant au bas du fichier **.csproj** du projet d’application iOS (juste avant la balise de fermeture `</Project>`) :

    ```xml
    <PropertyGroup>
        <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- Une meilleure approche consiste à ajouter un élément `<IpaPackageDir>` au bas du `<PropertyGroup>` existant, qui correspond à la configuration utilisée pour générer le fichier **.ipa**. Cette solution est préférable, car elle permet de préparer le projet pour une compatibilité future avec un paramètre planifié de la page de propriétés de projet dans les Options IPA iOS. Si vous utilisez la configuration `Release|iPhone` pour générer le fichier **.ipa**, l’ensemble du groupe de propriétés mis à jour peut ressembler à ce qui suit :

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' ">
        <Optimize>true</Optimize>
        <OutputPath>bin\iPhone\Release</OutputPath>
        <ErrorReport>prompt</ErrorReport>
        <WarningLevel>4</WarningLevel>
        <ConsolePause>false</ConsolePause>
        <CodesignKey>iPhone Developer</CodesignKey>
        <MtouchUseSGen>true</MtouchUseSGen>
        <MtouchUseRefCounting>true</MtouchUseRefCounting>
        <MtouchFloat32>true</MtouchFloat32>
        <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
        <MtouchLink>SdkOnly</MtouchLink>
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
    </PropertyGroup>
    ```

Il existe une autre technique pour les builds de ligne de commande **msbuild** ou **xbuild**. Elle consiste à ajouter un argument `/p:` pour définir la propriété `IpaPackageDir`. Dans ce cas, notez que **msbuild** ne développe pas les expressions `$()` passées sur la ligne de commande, il est donc impossible d’utiliser la syntaxe `$(OutputPath)`. À la place, vous devez fournir un chemin complet. La commande Mono **xbuild** développe les expressions `$()`, mais il est tout de même préférable d’utiliser un nom de chemin complet, car **xbuild** a été déprécié au profit de la [version multiplateforme de **msbuild**](https://www.mono-project.com/docs/about-mono/releases/5.0.0/#msbuild).

Voici à quoi peut ressembler un exemple complet de cette approche sur Windows :

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

Ou sur Mac :

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

<a name="installipa" />

## <a name="installing-an-ipa-using-itunes"></a>Installation d’un fichier IPA à l’aide d’iTunes

Vous pouvez remettre le paquet IPA à vos utilisateurs de test pour qu’ils l’installent sur leurs appareils iOS, ou le préparer à un déploiement en entreprise. Quelle que soit la méthode choisie, l’utilisateur final installe le paquet dans son application iTunes sur Mac ou sur PC Windows en double-cliquant sur le fichier IPA (ou en le faisant glisser vers la fenêtre ouverte d’iTunes).

La nouvelle application iOS s’affiche dans la section **My Apps (Mes applications)** . Pour obtenir des informations sur l’application, il vous suffit de cliquer sur celle-ci avec le bouton droit de la souris :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

 ![](ipa-support-images/installxs01.png "Nouvelle application iOS dans la section My Apps (Mes applications)")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 ![](ipa-support-images/installvs01.png "Nouvelle application iOS dans la section My Apps (Mes applications)")

-----

L’utilisateur peut à présent synchroniser son appareil avec iTunes pour installer la nouvelle application iOS.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit les étapes nécessaires à la préparation d’une application Xamarin.iOS pour une build non liée à l’App Store. Il a montré comment créer un paquet IPA et comment installer l’application iOS qui en résulte sur l’appareil iOS de l’utilisateur final à des fins de test ou de distribution en interne.


## <a name="related-links"></a>Liens associés

- [Distribution sur l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publication dans l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribution en interne](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Fichier iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Résolution des problèmes](~/ios/deploy-test/troubleshooting.md)
- [Conception graphique iTunes](~/ios/app-fundamentals/images-icons/app-icons.md#itunes)
- [Développer et distribuer des applications d’entreprise (Apple)](https://help.apple.com/xcode/mac/current/#/devba5e7054d)
- [Distribution d’applications d’entreprise (vidéo WWDC)](https://developer.apple.com/videos/play/wwdc2014/705/)
