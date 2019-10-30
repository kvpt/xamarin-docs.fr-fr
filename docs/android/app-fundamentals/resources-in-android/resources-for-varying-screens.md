---
title: Création de ressources pour différents écrans
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/28/2018
ms.openlocfilehash: cbd392dcae173eb3baf0fb8f0c3c4ec7c0da23a1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025114"
---
# <a name="creating-resources-for-varying-screens"></a>Création de ressources pour différents écrans

Android s’exécute sur de nombreux appareils différents, chacun d’entre eux ayant une grande variété de résolutions, de tailles d’écran et de densités d’écran. Android effectue la mise à l’échelle et le redimensionnement pour que votre application fonctionne sur ces appareils, mais cela peut entraîner une expérience utilisateur sous-optimale. Par exemple, les images peuvent apparaître floues ou peuvent être positionnées comme prévu sur une vue.

## <a name="concepts"></a>Concepts

Il est important de comprendre quelques termes et concepts pour prendre en charge plusieurs écrans.

- **Taille** de l’écran &ndash; la quantité d’espace physique pour l’affichage de votre application

- **Densité d’écran** &ndash; le nombre de pixels dans une zone donnée sur l’écran. L’unité de mesure classique est exprimée en points par pouce (dpi).

- **Résolution** &ndash; le nombre total de pixels sur l’écran. Lors du développement d’applications, la résolution n’est pas aussi importante que la taille et la densité de l’écran.

- Le point de contrôle de **densité (DP)** &ndash; une unité virtuelle de mesure pour permettre la conception de dispositions indépendantes de la densité. Cette formule est utilisée pour convertir DP en pixels d’écran :

    PX &equals; DP &times; PPP &divide; 160

- L' **orientation** &ndash; l’orientation de l’écran est considérée comme étant paysage quand elle est plus grande que la hauteur. En revanche, l’orientation portrait est lorsque l’écran est plus haut que la largeur. L’orientation peut changer pendant la durée de vie d’une application lorsque l’utilisateur fait pivoter l’appareil.

Notez que les trois premiers concepts sont liés &ndash; l’augmentation de la résolution sans augmenter la densité augmente la taille de l’écran. Toutefois, si la densité et la résolution sont augmentées, la taille de l’écran peut rester inchangée. Cette relation entre la taille de l’écran, la densité et la résolution complique la prise en charge des écrans rapidement.

Pour vous aider à gérer cette complexité, l’infrastructure Android préfère utiliser les *pixels indépendants de la densité (DP)* pour les dispositions d’écran. En utilisant des pixels indépendants de la densité, les éléments de l’interface utilisateur s’affichent pour que l’utilisateur ait la même taille physique sur les écrans avec des densités différentes.

## <a name="supporting-various-screen-sizes-and-densities"></a>Prise en charge de différentes tailles et densités d’écran

Android gère la plupart du travail pour restituer correctement les dispositions pour chaque configuration d’écran. Toutefois, certaines actions peuvent être effectuées pour aider le système à sortir.

L’utilisation de pixels indépendants de la densité plutôt que les pixels réels dans les dispositions est suffisante dans la plupart des cas pour garantir l’indépendance de la densité.
Android mettra à l’échelle le drawables au moment de l’exécution en fonction de la taille appropriée.
Toutefois, il est possible que la mise à l’échelle entraîne l’affichage d’une image bitmap floue. Pour contourner ce problème, fournissez d’autres ressources pour les différentes densités. Lorsque vous concevez des appareils pour plusieurs résolutions et densités d’écran, il est plus facile de commencer avec les images de haute résolution ou de densité, puis de les réduire.

### <a name="declare-the-supported-screen-size"></a>Déclarer la taille d’écran prise en charge

La déclaration de la taille de l’écran garantit que seuls les appareils pris en charge peuvent télécharger l’application. Pour ce faire, vous devez définir l’élément [supports-screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) dans le fichier **fichier AndroidManifest. xml** . Cet élément est utilisé pour spécifier les tailles d’écran prises en charge par l’application. Un écran donné est considéré comme pris en charge si l’application peut placer correctement ses dispositions sur l’écran de remplissage. À l’aide de cet élément de manifeste, l’application n’apparaît pas dans [*Google Play*](https://play.google.com/) pour les appareils qui ne respectent pas les spécifications de l’écran. Toutefois, l’application s’exécute toujours sur les appareils avec des écrans non pris en charge, mais les dispositions peuvent apparaître floues et pixellisée.

Les sixes d’écran pris en charge sont déclarés dans le fichier **/fichier AndroidManifest. xml** de la solution :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[Manifeste ![Android](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[Manifeste ![Android](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Modifiez **fichier AndroidManifest. xml** de façon à inclure [supports-screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Fournir d’autres dispositions pour différentes tailles d’écran

Les autres dispositions permettent de personnaliser une vue pour une taille d’écran spécifique, en modifiant la position ou la taille des éléments de l’interface utilisateur du composant.

À partir de l’API de niveau 13 (Android 3,2), les tailles d’écran sont dépréciées en faveur de l’utilisation du qualificateur SW*N*DP. Ce nouveau qualificateur déclare la quantité d’espace nécessaire à une disposition donnée. Il est recommandé que les applications conçues pour s’exécuter sur Android 3,2 ou une version ultérieure doivent utiliser ces qualificateurs plus récents.

Par exemple, si une disposition nécessitait un DP minimum de 700 de largeur d’écran, l’autre disposition serait dans un dossier **Layout-sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Dossier de disposition pour la largeur de l’écran 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Dossier de disposition pour la largeur de l’écran 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----

En règle générale, voici quelques nombres pour différents appareils :

- **Téléphone standard** &ndash; 320 DP : téléphone classique

- **Un appareil 5 « tablette/«interpolateur »** &ndash; 480 DP : comme la note Samsung

- **Un DP 7 "tablet** &ndash; 600 : comme Barnes &amp; noble Nook

- **Un DP 10 «tablet** &ndash; 720 : comme Motorola Xoom

Pour les applications qui ciblent des niveaux d’API allant jusqu’à 12 (Android 3,1), les dispositions doivent se trouver dans les répertoires qui utilisent les qualificateurs **small**/**normal**/**grand**/**XLarge** comme généralisations des différents écrans tailles disponibles dans la plupart des appareils. Par exemple, dans l’image ci-dessous, il existe d’autres ressources pour les quatre tailles d’écran différentes :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Ressources de remplacement pour quatre tailles d’écran](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Ressources de remplacement pour quatre tailles d’écran](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

Vous trouverez ci-dessous une comparaison de la façon dont les qualificateurs de taille d’écran antérieurs au niveau d’API 13 sont comparés aux pixels indépendants de la densité :

- 426 DP x 320 DP est **petit**

- 470 DP x 320 DP est **normal**

- 640 DP x 480 DP est **volumineux**

- 960 DP x 720 DP est **XLarge**

Les qualificateurs de taille d’écran plus récents dans les API de niveau 13 et supérieur ont une priorité supérieure à celle des qualificateurs d’écran les plus anciens des niveaux d’API 12 et inférieurs.
Pour les applications qui s’étendent sur les anciens et les nouveaux niveaux d’API, il peut être nécessaire de créer d’autres ressources à l’aide des deux ensembles de qualificateurs, comme indiqué dans la capture d’écran suivante :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Autres ressources avec les deux qualificateurs](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Autres ressources avec les deux qualificateurs](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Fournir différentes bitmaps pour différentes densités d’écran

Bien qu’Android met à l’échelle les bitmaps en fonction des besoins d’un appareil, les bitmaps elles-mêmes peuvent ne pas évoluer de manière élégante : elles peuvent devenir floues ou floues. Le fait de fournir des bitmaps adaptés à la densité de l’écran atténue ce problème.

Par exemple, l’image ci-dessous est un exemple de problèmes de mise en page et d’apparence qui peuvent se produire lorsque la densité-spécifiez des ressources non fournies.

![Captures d’écran sans ressources de densité](resources-for-varying-screens-images/06-density-not-provided.png)

Comparez cela à une disposition conçue avec des ressources spécifiques à la densité :

![Captures d’écran avec ressources spécifiques à la densité](resources-for-varying-screens-images/07-density-specific-resources.png)

### <a name="create-varying-density-resources-with-android-asset-studio"></a>Créer des ressources à densité variable avec Android Asset Studio

La création de ces bitmaps de différentes densités peut être un peu fastidieuse. Par conséquent, Google a créé un utilitaire en ligne qui peut réduire certains des caractère fastidieux impliqués dans la création de ces bitmaps appelées [**Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Ce site Web permet de créer des bitmaps qui ciblent les quatre densités d’écran courantes en fournissant une image. Android Asset Studio crée ensuite les bitmaps avec certaines personnalisations, puis les autorise à être téléchargé sous forme de fichier zip.

## <a name="tips-for-multiple-screens"></a>Conseils pour plusieurs écrans

Android s’exécute sur un nombre inventés d’appareils, et la combinaison des tailles d’écran et des densités d’écran peut paraître insurmontable. Les conseils suivants peuvent vous aider à réduire les efforts nécessaires à la prise en charge de différents appareils :

- **Concevez et développez uniquement les éléments dont vous avez besoin** &ndash; il y a de nombreux appareils différents, mais certains d’entre eux existent rarement, ce qui peut nécessiter des efforts considérables pour concevoir et développer pour. Le tableau de bord [**taille et densité**](https://developer.android.com/resources/dashboard/screens.html) de l’écran est une page fournie par Google, qui fournit des données sur la répartition de la taille d’écran/matrice de densité d’écran. Cette répartition fournit des informations sur l’effort de développement sur les écrans de prise en charge.

- **Utilisez DPS plutôt que pixels** -les pixels deviennent gênants en cas de modification de la densité de l’écran. Ne pas coder en dur les valeurs de pixel. Évitez les pixels en faveur du point de distribution (en pixels indépendants de la densité).

- **Évitez** les
  [AbsoluteLayout](xref:Android.Widget.AbsoluteLayout) dans la mesure du **possible** &ndash; il est déconseillé au niveau de l’API 3 (Android 1,5) et produira des dispositions fragiles. Elle ne doit pas être utilisée. Essayez plutôt d’utiliser des widgets de mise en page plus flexibles tels que [**élément LinearLayout**](xref:Android.Widget.LinearLayout), [**RelativeLayout**](xref:Android.Widget.RelativeLayout)ou la nouvelle [**GridLayout**](xref:Android.Widget.GridLayout).

- **Choisissez une orientation de disposition comme &ndash; par défaut** . par exemple, au lieu de fournir les ressources alternatives **disposition-Land** et **disposition-port**, placez les ressources pour le paysage dans la **disposition**et les ressources pour portrait dans **disposition-port**.

- **Utiliser LayoutParams pour la hauteur et la largeur** : lors de la définition d’éléments d’interface utilisateur dans un fichier de disposition XML, une application Android utilisant les valeurs **wrap_content** et **fill_parent** aura plus de succès garantissant une bonne présentation des différents appareils. utilisation d’unités indépendantes du pixel ou de la densité. Ces valeurs de dimension obligent Android à mettre à l’échelle les ressources bitmap selon le cas. Pour cette même raison, il est préférable de réserver les unités indépendantes de la densité lors de la spécification des marges et du remplissage des éléments d’interface utilisateur.

## <a name="testing-multiple-screens"></a>Test de plusieurs écrans

Une application Android doit être testée par rapport à toutes les configurations qui seront prises en charge. Idéalement, les appareils doivent être testés sur les appareils réels, mais dans de nombreux cas, cela n’est pas possible ou pratique.
Dans ce cas, l’utilisation de la configuration de l’émulateur et des appareils virtuels Android pour chaque configuration d’appareil sera utile.

Le Android SDK fournit des apparences d’émulateur qui peuvent être utilisées pour créer des AVD, qui répliquent la taille, la densité et la résolution de nombreux appareils.
La plupart des fournisseurs de matériel fournissent également des habillages pour leurs appareils.

Une autre option consiste à utiliser les services d’un service de test tiers.
Ces services prennent un APK, les exécutent sur de nombreux appareils différents, puis fournissent des commentaires sur le fonctionnement de l’application.
