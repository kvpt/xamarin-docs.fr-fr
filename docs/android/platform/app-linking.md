---
title: Liaison d’applications dans Android
description: Ce guide explique comment Android 6,0 prend en charge la liaison d’applications, une technique qui permet aux applications mobiles de répondre aux URL sur les sites Web. Il aborde ce qu’est la liaison d’application, comment implémenter la liaison d’applications dans une application Android 6,0 et comment configurer un site Web pour accorder des autorisations à l’application mobile pour un domaine.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: af90c286d2bb960a9f78547dd15c3d98a69529ae
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "75487826"
---
# <a name="app-linking-in-android"></a>Liaison d’applications dans Android

_Ce guide explique comment Android 6,0 prend en charge la liaison d’applications, une technique qui permet aux applications mobiles de répondre aux URL sur les sites Web. Il aborde ce qu’est la liaison d’application, comment implémenter la liaison d’applications dans une application Android 6,0 et comment configurer un site Web pour accorder des autorisations à l’application mobile pour un domaine._

## <a name="app-linking-overview"></a>Vue d’ensemble des liens d’application

Les applications mobiles ne se trouvent plus dans un silo &ndash; dans de nombreux cas, il s’agit d’un composant important de leur entreprise, ainsi que de leur site Web. Il est souhaitable que les entreprises connectent en toute transparence leur présence sur le Web et leurs applications mobiles, avec des liens sur un site Web lançant des applications mobiles et affichant le contenu pertinent dans l’application mobile. La *liaison d’application* (également appelée *liaison profonde*) est une technique qui permet à un appareil mobile de répondre à un URI et de lancer une application mobile qui correspond à cet URI.

Android gère la liaison des applications via le *système d’intention* &ndash; lorsque l’utilisateur clique sur un lien dans un navigateur mobile, le navigateur mobile envoie une intention qu’Android délègue à une application inscrite. Par exemple, un clic sur un lien sur un site Web de cuisine ouvre une application mobile associée à ce site Web et affiche une recette spécifique pour l’utilisateur. Si plusieurs applications sont inscrites pour gérer cet objectif, Android déclenchera une *boîte de dialogue de désambiguïsation* qui demandera à l’utilisateur l’application de sélectionner l’application qui devrait gérer l’intention, par exemple :

![Exemple de capture d’écran d’une boîte de dialogue de désambiguïsation](app-linking-images/01-disambiguation-dialog.png)

Android 6,0 améliore cette fonctionnalité à l’aide de la gestion automatique des liaisons. Android peut inscrire automatiquement une application en tant que gestionnaire par défaut d’un URI &ndash; l’application est automatiquement lancée et accède directement à l’activité correspondante. La manière dont Android 6,0 décide de gérer un URI, cliquez sur dépend des critères suivants :

1. **Une application existante est déjà associée à l’uri** &ndash; l’utilisateur a peut-être déjà associé une application existante à un URI. Dans ce cas, Android continue à utiliser cette application.
2. **Aucune application existante n’est associée à l’URI, mais une application de prise en charge est installée** &ndash; dans ce scénario, l’utilisateur n’a pas spécifié d’application existante. par conséquent, Android utilise l’application de prise en charge installée pour traiter la demande.
3. **Aucune application existante n’est associée à l’URI, mais de nombreuses applications de prise en charge sont installées** &ndash; parce qu’il y a plusieurs applications qui prennent en charge l’URI, la boîte de dialogue de désambiguïsation s’affiche et l’utilisateur doit sélectionner l’application qui gérera l’URI.

Si l’utilisateur n’a pas d’applications installées qui prennent en charge l’URI et qu’une application est ensuite installée, Android définit cette application en tant que gestionnaire par défaut de l’URI après avoir vérifié l’association avec le site Web associé à l’URI.

Ce guide explique comment configurer une application Android 6,0 et comment créer et publier le fichier de liens de ressources numériques pour prendre en charge la liaison d’applications dans Android 6,0.

## <a name="requirements"></a>Configuration requise

Ce guide requiert Xamarin. Android 6,1 et une application qui cible Android 6,0 (niveau d’API 23) ou version ultérieure.

La liaison d’application est possible dans les versions antérieures d’Android en utilisant le [package NuGet rivets](https://www.nuget.org/packages/Rivets/) du magasin de composants Xamarin. Le package rivets n’est pas compatible avec la liaison d’applications dans Android 6,0 ; il ne prend pas en charge la liaison d’applications Android 6,0.

## <a name="configuring-app-linking-in-android-60"></a>Configuration de la liaison d’applications dans Android 6,0

La configuration des liens d’application dans Android 6,0 implique deux étapes principales :

1. L' **Ajout d’un ou de plusieurs filtres intentionnels pour les URI du site web** &ndash; le guide des filtres d’intention Android dans Comment gérer une URL cliquez sur dans un navigateur mobile.
2. **Publication d’un fichier *JSON de liens de biens numériques* sur le site Web** &ndash; il s’agit d’un fichier qui est téléchargé vers un site Web et utilisé par Android pour vérifier la relation entre l’application mobile et le domaine du site Web. Sans cela, Android ne peut pas installer l’application en tant que descripteur par défaut pour les URI ; l’utilisateur doit le faire manuellement.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configuration du filtre d’intention

Il est nécessaire de configurer un filtre d’intention qui mappe un URI (ou un ensemble d’URI) d’un site Web à une activité dans une application Android. Dans Xamarin. Android, cette relation est établie en orneant une activité avec [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute). Le filtre d’intention doit déclarer les informations suivantes :

- **`Intent.ActionView`** &ndash; cela inscrira le filtre d’intention pour répondre aux demandes d’affichage des informations.
- **`Categories`** &ndash; le filtre d’intention doit enregistrer les **[intentions. CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** et **[intentionnelle. CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** pour pouvoir gérer correctement l’URI Web.
- **`DataScheme`** &ndash; le filtre d’intention doit déclarer `http` et/ou `https`. Il s’agit des deux seuls schémas valides.
- **`DataHost`** &ndash; il s’agit du domaine à partir duquel les URI proviennent.
- **`DataPathPrefix`** &ndash; il s’agit d’un chemin d’accès facultatif aux ressources sur le site Web.
- **`AutoVerify`** &ndash; l’attribut `autoVerify` indique à Android de vérifier la relation entre l’application et le site Web. Vous y trouverez plus d’informations ci-dessous.

L’exemple suivant montre comment utiliser [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) pour gérer les liens à partir de `https://www.recipe-app.com/recipes` et à partir de `http://www.recipe-app.com/recipes`:

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

Android vérifie chaque ordinateur hôte identifié par les filtres d’intention par rapport au fichier de ressources numériques sur le site Web avant d’inscrire l’application en tant que gestionnaire par défaut d’un URI. Tous les filtres d’intention doivent réussir la vérification avant qu’Android puisse établir l’application en tant que gestionnaire par défaut.

### <a name="creating-the-digital-assets-link-file"></a>Création du fichier de liaison de ressources numériques

La liaison d’application Android 6,0 requiert que Android vérifie l’association entre l’application et le site Web avant de définir l’application comme gestionnaire par défaut de l’URI. Cette vérification se produit lors de la première installation de l’application. Le fichier de *liens des ressources numériques* est un fichier JSON qui est hébergé par le ou les domaines webdomaine pertinents.

> [!NOTE]
> L’attribut `android:autoVerify` doit être défini par le filtre d’intention &ndash; sinon Android n’effectue pas la vérification.

Le fichier est placé par l’webmaster du domaine à l’emplacement **https://domain/.well-known/assetlinks.json** .

Le fichier de ressources numériques contient les métadonnées nécessaires à Android pour vérifier l’Association. Un fichier **assetlinks. JSON** contient les paires clé/valeur suivantes :

- `namespace` &ndash; l’espace de noms de l’application Android.
- `package_name` &ndash; le nom du package de l’application Android (déclaré dans le manifeste de l’application).
- `sha256_cert_fingerprints` &ndash; les empreintes digitales SHA256 de l’application signée. Pour plus d’informations sur la façon d’obtenir l’empreinte digitale SHA1 d’une application, consultez le Guide de recherche de la [signature MD5 ou SHA1 de votre magasin de clés](~/android/deploy-test/signing/keystore-signature.md) .

L’extrait de code suivant est un exemple de **assetlinks. JSON** avec une seule application listée :

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

Il est possible d’inscrire plus d’une empreinte digitale SHA256 pour prendre en charge différentes versions ou builds de votre application. Ce prochain fichier **assetlinks. JSON** est un exemple d’inscription de plusieurs applications :

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

Le [site Web de Microsoft Google Digital Asset Links](https://developers.google.com/digital-asset-links/tools/generator) dispose d’un outil en ligne qui peut vous aider à créer et à tester le fichier de ressources numériques.

### <a name="testing-app-links"></a>Test de l’application-liens

Après l’implémentation des liens vers les applications, les différents éléments doivent être testés pour s’assurer qu’ils fonctionnent comme prévu.

Il est possible de confirmer que le fichier de ressources numériques est correctement formaté et hébergé à l’aide de l’API de liens de ressources numériques de Google, comme illustré dans cet exemple :

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Deux tests peuvent être effectués pour s’assurer que les filtres d’intention ont été correctement configurés et que l’application est définie en tant que gestionnaire par défaut d’un URI :

1. Le fichier de ressources numériques est correctement hébergé comme décrit ci-dessus. Le premier test distribuera une intention qu’Android devrait rediriger vers l’application mobile. L’application Android doit démarrer et afficher l’activité inscrite pour l’URL. À l’invite de commandes, tapez :

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Affichez les stratégies de gestion des liens existantes pour les applications installées sur un appareil donné. La commande suivante permet de vider une liste de stratégies de liaison pour chaque utilisateur sur l’appareil avec les informations suivantes. À l’invite de commandes, tapez la commande suivante :

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`** &ndash; le nom du package de l’application.
    - **`Domain`** &ndash; les domaines (séparés par des espaces) dont les liens Web seront gérés par l’application
    - **`Status`** &ndash; il s’agit de l’état actuel de la gestion des liens pour l’application. La valeur **Always** signifie que l’application a `android:autoVerify=true` déclarée et a réussi la vérification du système. Il est suivi d’un nombre hexadécimal représentant l’enregistrement de la préférence du système Android.

    Par exemple :

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Résumé

Ce guide a abordé le fonctionnement de la liaison d’application dans Android 6,0. Il a ensuite abordé la configuration d’une application Android 6,0 pour prendre en charge les liens d’application et y répondre. Elle a également expliqué comment tester la liaison d’application dans une application Android.

## <a name="related-links"></a>Liens connexes

- [Recherche de la signature MD5 ou SHA1 de votre magasin de clés](~/android/deploy-test/signing/keystore-signature.md)
- [AppLinks](https://developers.facebook.com/docs/applinks)
- [Liens de Google Digital actifs](https://developers.google.com/digital-asset-links/)
- [Générateur de listes d’instructions et testeur](https://developers.google.com/digital-asset-links/tools/generator)
