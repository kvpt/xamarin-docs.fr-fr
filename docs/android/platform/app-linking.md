---
title: App-Linking sur Android
description: Ce guide discutera de la façon dont Android 6.0 prend en charge la liaison d’applications, une technique qui permet aux applications mobiles de répondre aux URL sur les sites Web. Il discutera de ce qu’est le lien avec l’application, de la façon d’implémenter des liaisons d’applications dans une application Android 6.0 et de la façon de configurer un site Web pour accorder des autorisations à l’application mobile pour un domaine.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: af90c286d2bb960a9f78547dd15c3d98a69529ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487826"
---
# <a name="app-linking-in-android"></a>App-Linking sur Android

_Ce guide discutera de la façon dont Android 6.0 prend en charge la liaison d’applications, une technique qui permet aux applications mobiles de répondre aux URL sur les sites Web. Il discutera de ce qu’est le lien avec l’application, de la façon d’implémenter des liaisons d’applications dans une application Android 6.0 et de la façon de configurer un site Web pour accorder des autorisations à l’application mobile pour un domaine._

## <a name="app-linking-overview"></a>Aperçu de l’application de liaison

Les applications mobiles ne vivent &ndash; plus dans un silo dans de nombreux cas, elles sont des composantes importantes de leurs activités, ainsi que leur site Web. Il est souhaitable que les entreprises connectent en toute transparence leur présence sur le Web et leurs applications mobiles, avec des liens sur un site Web lançant des applications mobiles et affichant du contenu pertinent dans l’application mobile. *App-linking* (également appelé *lien profond*) est une technique qui permet à un appareil mobile de répondre à un URI et de lancer une application mobile qui correspond à cette URI.

Android gère app-liaison à travers le &ndash; *système d’intention* lorsque l’utilisateur clique sur un lien dans un navigateur mobile, le navigateur mobile enverra une intention que Android déléguer à une application enregistrée. Par exemple, en cliquant sur un lien sur un site Web de cuisine ouvrirait une application mobile qui est associée à ce site Web et afficherait une recette spécifique à l’utilisateur. S’il y a plus d’une application enregistrée pour gérer cette intention, Alors Android soulèvera ce qui est connu comme un *dialogue de désambiguation* qui demandera à un utilisateur quelle application pour sélectionner l’application qui devrait gérer l’intention, par exemple :

![Exemple de capture d’écran d’un dialogue de disambiguation](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 améliore à ce sujet en utilisant la manipulation automatique de lien. Il est possible pour Android d’enregistrer automatiquement une &ndash; application car le gestionnaire par défaut pour un URI l’application sera automatiquement lancer et naviguer directement vers l’activité pertinente. La façon dont Android 6.0 décide de gérer un clic URI dépend des critères suivants :

1. **Une application existante est déjà associée à l’URI** &ndash; L’utilisateur a peut-être déjà associé une application existante à une URI. Dans ce cas, Android continuera à utiliser cette application.
2. **Aucune application existante n’est associée à l’URI, mais une application de soutien est installée** &ndash; Dans ce scénario, l’utilisateur n’a pas spécifié une application existante, donc Android utilisera l’application de support installée pour traiter la demande.
3. **Aucune application existante n’est associée à l’URI, mais de nombreuses applications de soutien sont installées** &ndash; Car il existe plusieurs applications qui prennent en charge l’URI, le dialogue de désambiguation sera affiché et l’utilisateur doit sélectionner quelle application gérera l’URI.

Si l’utilisateur n’a pas d’applications installées qui prennent en charge l’URI, et l’un est ensuite installé, puis Android définira cette application comme le gestionnaire par défaut pour l’URI après vérification de l’association avec le site Web qui est associé à l’URI.

Ce guide discutera de la façon de configurer une application Android 6.0 et de la façon de créer et de publier le fichier Digital Asset Links pour prendre en charge la liaison d’applications dans Android 6.0.

## <a name="requirements"></a>Spécifications

Ce guide nécessite Xamarin.Android 6.1 et une application qui cible Android 6.0 (niveau API 23) ou plus.

App-linking est possible dans les versions antérieures d’Android en utilisant le [paquet Rivets NuGet](https://www.nuget.org/packages/Rivets/) de la boutique de composants Xamarin. Le forfait Rivets n’est pas compatible avec l’app-liaison dans Android 6.0; il ne prend pas en charge Android 6.0 liaison app.

## <a name="configuring-app-linking-in-android-60"></a>Configurer App-Linking dans Android 6.0

La mise en place de liens d’applications dans Android 6.0 comporte deux étapes majeures :

1. **Ajout d’un ou plusieurs filtres d’intention pour le site URI** &ndash; les filtres d’intention guident Android dans la façon de gérer un clic URL dans un navigateur mobile.
2. **Publication d’un fichier *Digital Asset Links JSON* sur le site Web** &ndash; c’est un fichier qui est téléchargé sur un site Web et est utilisé par Android pour vérifier la relation entre l’application mobile et le domaine du site. Sans cela, Android ne peut pas installer l’application comme la poignée par défaut pour URI; l’utilisateur doit le faire manuellement.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurer le filtre d’intention

Il est nécessaire de configurer un filtre d’intention qui cartographie une URI (ou possible un ensemble d’URI) à partir d’un site Web à une activité dans une application Android. Dans Xamarin.Android, cette relation est établie en ornant une activité avec [l’IntentionFilterAttribute](xref:Android.App.IntentFilterAttribute). Le filtre d’intention doit déclarer les informations suivantes :

- **`Intent.ActionView`**&ndash; Cela enregistrera le filtre d’intention pour répondre aux demandes de visionnage de l’information
- **`Categories`**&ndash; Le filtre d’intention doit enregistrer à la fois **[l’intention.CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** et **[l’intention.CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** pour être en mesure de gérer correctement l’URI Web.
- **`DataScheme`**&ndash; Le filtre d’intention doit déclarer `http` et/ou `https`. Ce sont les deux seuls régimes valables.
- **`DataHost`**&ndash; C’est le domaine d’où les ITI viendront.
- **`DataPathPrefix`**&ndash; Il s’agit d’un chemin facultatif vers les ressources sur le site Web.
- **`AutoVerify`**&ndash; L’attribut `autoVerify` indique à Android de vérifier la relation entre l’application et le site Web. Cela sera discuté plus ci-dessous.

L’exemple suivant montre comment utiliser [l’IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) pour gérer les liens à partir de `https://www.recipe-app.com/recipes` et à partir de `http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe",
              AutoVerify=true)]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android vérifiera chaque hôte qui est identifié par les filtres d’intention contre le fichier d’actifs numériques sur le site Web avant d’enregistrer l’application en tant que gestionnaire par défaut pour un URI. Tous les filtres d’intention doivent passer la vérification avant qu’Android puisse établir l’application en tant que gestionnaire par défaut.

### <a name="creating-the-digital-assets-link-file"></a>Création du fichier Digital Assets Link

Android 6.0 app-linking exige qu’Android vérifier l’association entre l’application et le site Web avant de définir l’application comme le gestionnaire par défaut pour l’URI. Cette vérification aura lieu lorsque l’application sera installée pour la première fois. Le fichier *Digital Assets Links* est un fichier JSON hébergé par le webdomain pertinent.

> [!NOTE]
> L’attribut `android:autoVerify` doit être défini &ndash; par le filtre d’intention sinon Android n’effectuera pas la vérification.

Le fichier est placé par le webmaster **https://domain/.well-known/assetlinks.json**du domaine à l’emplacement .

Le fichier d’actifs numériques contient les méta-données nécessaires pour Android pour vérifier l’association. Un fichier **assetlinks.json** a les paires de valeur clé suivantes :

- `namespace`&ndash; l’espace nom de l’application Android.
- `package_name`&ndash; le nom du paquet de l’application Android (déclaré dans le manifeste de l’application).
- `sha256_cert_fingerprints`&ndash; les empreintes digitales SHA256 de la demande signée. Veuillez consulter le guide [Trouver le MD5 ou la signature SHA1 de votre Keystore](~/android/deploy-test/signing/keystore-signature.md) pour plus d’informations sur la façon d’obtenir l’empreinte digitale SHA1 d’une application.

L’extrait suivant est un exemple de **assetlinks.json** avec une seule application répertoriée:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

Il est possible d’enregistrer plus d’une empreinte digitale SHA256 pour prendre en charge différentes versions ou builds de votre application. Ce prochain fichier **assetlinks.json** est un exemple d’enregistrement de plusieurs applications :

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

Le [site Web de Google Digital Asset Links](https://developers.google.com/digital-asset-links/tools/generator) dispose d’un outil en ligne qui peut aider à créer et tester le fichier Digital Assets.

### <a name="testing-app-links"></a>Test d’App-Links

Après la mise en œuvre des app-links, les différentes pièces doivent être testées pour s’assurer qu’elles fonctionnent comme prévu.

Il est possible de confirmer que le fichier Digital Assets est correctement formaté et hébergé en utilisant l’API Digital Asset Links de Google, comme le montre cet exemple :

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Il existe deux tests qui peuvent être effectués pour s’assurer que les filtres d’intention ont été correctement configurés et que l’application est définie comme le gestionnaire par défaut pour un URI:

1. Le fichier d’actifs numériques est correctement hébergé comme décrit ci-dessus. Le premier test enverra une intention qu’Android devrait rediriger vers l’application mobile. L’application Android doit lancer et afficher l’activité enregistrée pour l’URL. À un type d’invite de commande :

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Affichez les stratégies de traitement des liaisons existantes pour les applications installées sur un appareil donné. La commande suivante videra une liste des stratégies de liaison pour chaque utilisateur sur l’appareil avec les informations suivantes. Saisissez ensuite la commande suivante dans l’invite de commandes :

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`**&ndash; Le nom du paquet de l’application.
    - **`Domain`**&ndash; Les domaines (séparés par des espaces) dont les liens web seront gérés par l’application
    - **`Status`**&ndash; Il s’agit de l’état actuel de gestion des liens pour l’application. Une valeur de **toujours** signifie `android:autoVerify=true` que l’application a déclaré et a passé la vérification du système. Il est suivi d’un nombre hexadecimal représentant l’enregistrement du système Android de la préférence.

    Par exemple :

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Récapitulatif

Ce guide a discuté du fonctionnement de l’application-liaison dans Android 6.0. Il a ensuite couvert la façon de configurer une application Android 6.0 pour prendre en charge et répondre aux liens d’application. Il a également discuté de la façon de tester app-liaison dans une application Android.

## <a name="related-links"></a>Liens connexes

- [Recherche de la signature MD5 ou SHA1 de votre magasin de clés](~/android/deploy-test/signing/keystore-signature.md)
- [AppLinks (en)](https://developers.facebook.com/docs/applinks)
- [Liens Google Digital Assets](https://developers.google.com/digital-asset-links/)
- [Générateur et testeur de liste d’énoncés](https://developers.google.com/digital-asset-links/tools/generator)
