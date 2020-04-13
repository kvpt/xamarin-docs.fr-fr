---
title: Chargement manuel de l’APK
ms.prod: xamarin
ms.assetid: 1309C251-ABF0-4412-B1F5-200DC8321A9D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: b54296da3f1253b77b63363583c34e1548cd6abc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76724082"
---
# <a name="manually-uploading-the-apk"></a>Chargement manuel de l’APK

La première fois qu’un APK est soumis à Google Play (ou si une version préliminaire de Xamarin.Android est utilisée), l’APK doit être téléchargé manuellement via la [Console développeur de Google Play](https://play.google.com/apps/publish).
Ce guide explique les étapes requises pour ce processus.

## <a name="google-play-developer-console"></a>Console développeur de Google Play

Une fois l’APK compilé et les ressources promotionnelles préparées, l’application doit être chargée sur Google Play. Pour cela, vous devez vous connecter à la [Console développeur de Google Play](https://play.google.com/apps/publish), illustrée ci-dessous. Cliquez sur le bouton **Publish an Android App on Google Play** pour lancer le processus de distribution d’une application.

[![Console développeur de Google Play](manually-uploading-the-apk-images/00-google-play-developer-console-sml.png)](manually-uploading-the-apk-images/00-google-play-developer-console.png#lightbox)

Si vous avez déjà enregistré une application sur Google Play, cliquez sur le bouton **Add new application** :

[![Ajouter un nouveau bouton d’application](manually-uploading-the-apk-images/01-existing-app-sml.png)](manually-uploading-the-apk-images/01-existing-app.png#lightbox)

Lorsque la boîte de dialogue **ADD NEW APPLICATION** s’affiche, entrez le nom de l’application et cliquez sur **Upload APK** :

[![Télécharger le bouton APK](manually-uploading-the-apk-images/02-add-new-application-sml.png)](manually-uploading-the-apk-images/02-add-new-application.png#lightbox)

L’écran suivant permet de publier l’application pour le test alpha, le test bêta ou la production. Dans l’exemple suivant, l’onglet **ALPHA TESTING** est sélectionné. Étant donné que **MyApp** n’utilise pas les services de gestion des licences, il n’est pas nécessaire de cliquer sur le bouton **Get license key** pour cet exemple. Cliquez sur le bouton **Upload your first APK to Alpha** pour publier l’application sur le canal Alpha :

[![Téléchargez votre premier bouton APK sur Alpha](manually-uploading-the-apk-images/03-upload-to-alpha-sml.png)](manually-uploading-the-apk-images/03-upload-to-alpha.png#lightbox)

La boîte de dialogue **UPLOAD NEW APK TO ALPHA** s’affiche. L’APK peut être chargé en cliquant sur le bouton **Browse files** ou par une opération de glisser-déplacer :

[![Téléchargez un nouvel APK sur le dialogue Alpha](manually-uploading-the-apk-images/04-upload-dialog-sml.png)](manually-uploading-the-apk-images/04-upload-dialog.png#lightbox)

Veillez à charger l’APK prêt pour la mise en production qui doit être distribué.
La boîte de dialogue suivante indique la progression du chargement de l’APK :

[![Indication de progression de téléchargement](manually-uploading-the-apk-images/05-upload-progress-sml.png)](manually-uploading-the-apk-images/05-upload-progress.png#lightbox)

Une fois l’APK chargé, il est possible de sélectionner une méthode de test :

[![Choisissez un dialogue de méthode de test](manually-uploading-the-apk-images/06-select-testing-method-sml.png)](manually-uploading-the-apk-images/06-select-testing-method.png#lightbox)

Pour plus d’informations sur le test de l’application, consultez le guide [Configurer les tests alpha/bêta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

Une fois chargé, l’APK est enregistré en tant que brouillon. Il ne peut pas être publié tant que vous n’avez pas fourni à Google Play des détails supplémentaires comme décrit ci-après.

## <a name="store-listing"></a>Description dans le Store

Cliquez sur **Store Listing** dans la **Console développeur de Google Play** pour entrer les informations que Google Play affichera aux utilisateurs potentiels de l’application :

[![Dialogue d’inscription des magasins](manually-uploading-the-apk-images/07-store-listing-sml.png)](manually-uploading-the-apk-images/07-store-listing.png#lightbox)

### <a name="graphics-assets"></a>Ressources graphiques

Faites défiler la page **Store Listing** jusqu'à la section **GRAPHICS ASSETS** :

[![Section Des actifs graphiques](manually-uploading-the-apk-images/08-graphic-assets-sml.png)](manually-uploading-the-apk-images/08-graphic-assets.png#lightbox)

Toutes les ressources promotionnelles préparées précédemment sont chargées dans cette section. Des indications relatives aux ressources promotionnelles qui doivent être fournies et dans quel format sont fournies.

### <a name="categorization"></a>Catégorisation

Dans la section **CATEGORIZATION** qui vient après la section **GRAPHICS ASSETS**, sélectionnez le type d’application et sa catégorie :

[![Section de catégorisation](manually-uploading-the-apk-images/09-categorization-sml.png)](manually-uploading-the-apk-images/09-categorization.png#lightbox)

La classification du contenu est abordée après la section suivante.

### <a name="contact-details"></a>Informations de contact

La dernière section de cette page est **CONTACT DETAILS**. Cette section permet pour de collecter les informations de contact du développeur de l’application :

[![Section Détails de contact](manually-uploading-the-apk-images/10-contact-details-sml.png)](manually-uploading-the-apk-images/10-contact-details.png#lightbox)

Il est possible de fournir une URL pour la politique de confidentialité de l’application dans la section **PRIVACY POLICY**, comme indiqué ci-dessus.

## <a name="content-rating"></a>Classification du contenu

Cliquez sur **Content Rating** dans la **Console développeur de Google Play**. Dans cette page, vous spécifiez la classification du contenu de votre application. Google Play exige que la classification du contenu soit spécifiée pour toutes les applications. Cliquez sur le bouton **Continuer** pour remplir le questionnaire de classification du contenu :

[![Section d’évaluation du contenu](manually-uploading-the-apk-images/11-content-rating-sml.png)](manually-uploading-the-apk-images/11-content-rating.png#lightbox)

Toutes les applications sur Google Play doivent être évaluées selon le système de classification Google Play. Outre la classification du contenu, toutes les applications doivent respecter le [Règlement du programme Google Play (pour les développeurs)](https://www.android.com/us/developer-content-policy.html).

La liste qui suit répertorie les quatre niveaux définis dans le système de classification de contenu de Google Play et donne des indications sur les fonctionnalités ou le contenu qui astreignent à chaque niveau de classification :

- **Tout le monde** &ndash; L’application ne peut pas accéder à des données d’emplacement, ni en publier ou en partager. Elle peut pas héberger du contenu généré par l’utilisateur. Elle ne peut activer la communication entre les utilisateurs.

- **Maturité faible** &ndash; Applications qui accèdent à des données d’emplacement mais n’en partagent pas. Représentations de violence légère ou sous forme d’animation.

- **Maturité moyenne** &ndash; Références aux stupéfiants, à l’alcool ou au tabac. Jeux d’argent ou simulations de jeu d’argent. Contenu explosif. Blasphème, humour grossier. Références suggestives ou sexuelles.
    Violence fictive intense. Violence réaliste. Autorisant les utilisateurs à se trouver. Autorisant les utilisateurs à communiquer entre eux.
    Partage des données d’emplacement d’un utilisateur.

- **Maturité élevée** &ndash; Accent mis sur la consommation ou la vente d’alcool, de tabac ou de stupéfiants. Accent mis sur des références suggestives ou sexuelles. Violence graphique.

Les éléments de la liste Maturité moyenne sont subjectifs, en tant que tel, il est possible qu’une indication semblant dicter une classification en Maturité moyenne soit suffisamment intense pour mériter la classification en Maturité élevée.

## <a name="pricing-amp-distribution"></a>Prix &amp; distribution

Cliquez sur **Pricing and Distribution** dans la **Console développeur Google Play**. Dans cette page, définissez un prix si l’application est une application payante.
L’application peut également être distribuée gratuitement à tous les utilisateurs. Lorsqu’une application est définie comme étant gratuite, elle doit le rester.
Google Play n’autorise pas la transformations d’une application gratuite en application payante (toutefois, il est possible de vendre du contenu dans une application gratuite avec une facturation interne). Google Play autorise la transformation d’une application payante en application gratuite à tout moment.

Un compte de marchand est nécessaire pour pouvoir publier une application payante. Pour configurer un compte de marchand, cliquez sur **set up a merchant account** et suivez les instructions.

[![Dialogue sur les prix et la distribution](manually-uploading-the-apk-images/12-pricing-sml.png)](manually-uploading-the-apk-images/12-pricing.png#lightbox)

### <a name="manage-countries"></a>Gérer les pays

La section suivante, **Manage Countries**, permet de contrôler les pays dans lesquels une application peut être distribuée :

[![Gérer le dialogue entre les pays](manually-uploading-the-apk-images/13-manage-countries-sml.png)](manually-uploading-the-apk-images/13-manage-countries.png#lightbox)

### <a name="other-information"></a>Autres informations

Descendez plus bas pour indiquer si l’application contient des publicités. En outre, la section **DEVICE CATEGORIES** propose des options permettant de distribuer l’application pour Android Wear, Android TV ou Android Auto :

[![Contient la section Annonces](manually-uploading-the-apk-images/14-contains-ads-sml.png)](manually-uploading-the-apk-images/14-contains-ads.png#lightbox)

À la suite de cette section, des options supplémentaires peuvent être sélectionnées, comme opter pour **Conçu pour les familles** et distribuer l’application via Google Play pour l’éducation.

### <a name="consent"></a>Consentement

En bas de la page **Pricing &amp; Distribution** se trouve la section **CONSENT**.
Cette section est obligatoire. Elle permet de déclarer que l’application est conforme aux [Règles de contenu Android](https://play.google.com/about/restricted-content/) et de reconnaître que l’application est soumise à la réglementation américaine relative à l’exportation :

[![Section consentement](manually-uploading-the-apk-images/15-consent-sml.png)](manually-uploading-the-apk-images/15-consent.png#lightbox)

Il y aurait tant d’autres choses à dire sur la publication d’une application Xamarin.Android. Mais ce guide ne peut pas tout couvrir.
Pour plus d’informations sur la publication de votre application dans Google Play, consultez [Bienvenue dans le Centre d'aide Console Google Play](https://support.google.com/googleplay/android-developer#topic=3450769).

## <a name="google-play-filters"></a>Filtres Google Play

Lorsque les utilisateurs recherchent des applications sur le site web Google Play, ils peuvent rechercher toutes les applications publiées. Lorsque les utilisateurs naviguent sur Google Play à partir d’un appareil Android, les résultats sont légèrement différents. Les résultats sont filtrés en fonction de la compatibilité avec l’appareil qui est utilisé. Par exemple, si une application doit envoyer des messages SMS, Google Play n’affiche pas cette application sur les appareils qui ne peuvent pas envoyer des messages SMS. Les filtres qui sont appliqués à une recherche sont créés à partir des éléments suivants :

1. La configuration matérielle de l’appareil.
2. Les déclarations dans le fichier manifeste de l’application.
3. L’opérateur qui est utilisé (le cas échéant).
4. L’emplacement de l’appareil.

Il est possible d’ajouter des éléments au manifeste de l’application pour mieux contrôler la façon dont l’application est filtrée dans Google Play Store. La liste ci-dessous répertorie les éléments et attributs de manifeste qui peuvent être utilisés pour filtrer les applications :

- [supports-screen](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) &ndash; Google Play utilise les attributs pour déterminer si une application peut être déployée sur un appareil en fonction de la taille de l’écran.
    Google Play suppose qu’Android peut adapter des dispositions plus petites à des écrans plus grands, mais pas l’inverse. Ainsi, une application qui déclare prendre en charge les écrans normaux apparaît dans les recherches de grands écrans, mais pas de petits écrans. Si une application Xamarin.Android ne fournit pas d’élément `<supports-screen>` dans le fichier manifeste, Google Play suppose que tous les attributs ont la valeur true et que l’application prend en charge toutes les tailles d’écran. Cet élément doit être ajouté manuellement à **AndroidManifest.xml**.

- [utilisations-configuration](https://developer.android.com/guide/topics/manifest/uses-configuration-element.html) &ndash; Cet élément manifeste est utilisé pour demander certaines fonctionnalités matérielles, telles que le type de clavier, les dispositifs de navigation, un écran tactile, etc. Cet élément doit être ajouté manuellement à **AndroidManifest.xml.**

- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) &ndash; Cet élément de manifeste déclare les fonctionnalités matérielles ou logicielles que doit avoir un appareil pour que l’application fonctionne. Cet attribut est informatif uniquement. Google Play n’affiche pas l’application sur les appareils qui ne répondent pas à ce filtre. Il est toujours possible d’installer l’application par d’autres moyens (manuellement ou par téléchargement). Cet élément doit être ajouté manuellement à **AndroidManifest.xml**.

- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) &ndash; Cet élément spécifie que certaines bibliothèques partagées doivent être présentes sur l’appareil, par exemple Google Maps. Cet élément peut également être spécifié avec `Android.App.UsesLibraryAttribute`. Par exemple :

    ```csharp
    [assembly: UsesLibrary("com.google.android.maps", true)]
    ```

- [uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) &ndash; Cet élément sert à déduire certaines fonctionnalités matérielles qui sont requises pour exécuter l’application et qui peuvent ne pas avoir été correctement déclarées avec un élément `<uses-feature>`. Par exemple, si une application demande l’autorisation d’utiliser l’appareil photo, Google Play suppose que les appareils doivent disposer d’un appareil photo, même si aucun élément `<uses-feature>` ne déclare l’appareil photo. Cet élément peut être défini avec `Android.App.UsesPermissionsAttribute`. Par exemple :

    ```csharp
    [assembly: UsesPermission(Manifest.Permission.Camera)]
    ```

- [uses-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) &ndash; Cet élément sert à déclarer le niveau d’API Android minimal requis pour l’application. Cet élément peut être défini dans les options Xamarin.Android d’un projet Xamarin.Android.

- [compatible-screens](https://developer.android.com/guide/topics/manifest/compatible-screens-element.html) &ndash; Cet élément sert à filtrer les applications qui ne correspondent pas à la taille de l’écran et la densité spécifiés par cet élément. La plupart des applications n’utilisent pas ce filtre. Il est prévu pour des jeux ou des applications hautes performances spécifiques nécessitant un contrôle strict sur la distribution de l’application. L’attribut `<support-screen>` mentionné plus haut est préféré.

- [supports-gl-texture](https://developer.android.com/guide/topics/manifest/supports-gl-texture-element.html) &ndash; Cet élément sert à déclarer les formations de compression de texture GL requises par l’application. La plupart des applications n’utilisent pas ce filtre. Il est prévu pour des jeux ou des applications hautes performances spécifiques nécessitant un contrôle strict sur la distribution de l’application.

Pour plus d’informations sur la configuration du manifeste d’application, consultez la rubrique [Manifeste d’application](https://developer.android.com/guide/topics/manifest/manifest-intro.html) Android.
