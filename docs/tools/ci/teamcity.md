---
title: Utilisation de Team City avec Xamarin
description: Ce guide discutera des étapes à suivre pour utiliser TeamCity pour compiler des applications mobiles et les soumettre ensuite à App Center Test.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: 6ecd453180e8c392ba7d7527778617eb40950a9e
ms.sourcegitcommit: 6f3281a32017cfcebadde8a2d6e10651a277828f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587468"
---
# <a name="using-team-city-with-xamarin"></a>Utilisation de Team City avec Xamarin

_Ce guide discutera des étapes à suivre pour utiliser TeamCity pour compiler des applications mobiles et les soumettre ensuite à App Center Test._

Comme nous l’avons vu dans le guide [Introduction à l’intégration continue,](~/tools/ci/intro-to-ci.md) l’intégration continue (CI) est une pratique utile lors du développement d’applications mobiles de qualité. Il existe de nombreuses options viables pour les logiciels de serveur d’intégration continue; ce guide se concentrera sur [TeamCity](https://www.jetbrains.com/teamcity/) de JetBrains.

Il existe plusieurs permutations différentes d’une installation TeamCity. La liste suivante décrit certaines de ces permutations :

- **Service Windows** - Dans ce scénario, TeamCity démarre lorsque Windows démarre comme un service Windows. Il doit être jumelé à un Mac Build Host pour compiler toutes les applications iOS.

- **Lancez Daemon sur OS X** - Conceptuellement, c’est similaire à l’exécution comme un service Windows décrit dans l’étape précédente. Par défaut, les builds seront exécutés sous le compte racine.

- **Compte utilisateur sur OS X** - Il est possible d’exécuter TeamCity sous un compte utilisateur qui démarre chaque fois que l’utilisateur se connecte.

Parmi les scénarios précédents, l’exécution de TeamCity sous un compte utilisateur sur OS X est la plus simple et la plus facile à configurer.

Il y a plusieurs étapes impliquées dans la mise en place de TeamCity :

- **Installation de TeamCity** - L’installation de TeamCity n’est pas couverte dans ce guide. Ce guide suppose que TeamCity est installé et fonctionne sous un compte utilisateur. Les instructions sur [l’installation de TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) peuvent être trouvées dans la [documentation TeamCity 8](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) par JetBrains.

- **Préparation du serveur build** - Cette étape consiste à installer les logiciels, outils et certificats nécessaires pour construire des applications mobiles et les préparer à la distribution.

- **Création d’un script de construction** - Cette étape n’est pas strictement nécessaire, mais un script de construction est une aide utile à la construction d’applications sans surveillance. L’utilisation d’un script de construction aidera à dépanner les problèmes de construction qui peuvent survenir et fournit un moyen cohérent et répétable de créer les binaires pour la distribution, même si l’intégration continue n’est pas pratiquée.

- **Création d’un projet TeamCity** - Une fois les trois étapes précédentes terminées, nous devons créer un projet TeamCity qui contiendra toutes les méta-données nécessaires pour récupérer le code source, compiler les projets et soumettre les tests à App Center Test.

## <a name="requirements"></a>Spécifications

L’expérience avec [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/) est requise.

La familiarité avec TeamCity 8.1 est requise. L’installation de TeamCity dépasse le cadre de ce document. On suppose que TeamCity est installé sur OS X Mavericks et fonctionne sous un compte utilisateur régulier et non le compte racine.

Le serveur de construction doit être un ordinateur autonome, en cours d’exécution OS X, qui est dédié à l’intégration continue. Idéalement, le serveur de construction ne sera pas responsable d’autres rôles, tels qu’un serveur de base de données, un serveur Web ou une poste de travail de développeur.

> [!IMPORTANT]
> Ce guide ne couvre pas une installation « sans tête » de Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Préparation du serveur de construction

Une étape cruciale dans la configuration d’un serveur de construction est d’installer tous les outils, logiciels et certificats nécessaires pour construire les applications mobiles. Il est important que le serveur de construction puisse compiler la solution mobile et exécuter tous les tests. Pour minimiser les problèmes de configuration, le logiciel et les outils doivent être installés dans le même compte utilisateur qui héberge TeamCity. La liste suivante détaille ce qui est requis :

1. **Studio visuel pour Mac** - Cela comprend Xamarin.iOS et Xamarin.Android.
2. **Connectez-vous au magasin de composants Xamarin** - Cette étape est facultative et seulement nécessaire si votre application utilise des composants du magasin de composants Xamarin. La connexion proactive dans le magasin de composants à ce stade permettra d’éviter tout problème quand une construction TeamCity tente de compiler l’application.
3. **Xcode** Xcode est tenu de compiler et de signer des applications iOS.
4. **Outils Xcode Command-Line** - Ceci est décrit dans l’étape 1 de la section d’installation du Rubis mise à jour avec guide [rbenv.](https://github.com/calabash/calabash-ios/wiki)
5. **Signature de l’identité & Profils de provisionnement** - Importer les certificats et le profil de provisionnement via XCode. Consultez le guide d’Apple sur [l’exportation des identités et des profils de provisionnement](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) pour plus de détails.
6. **Android Keystores** - Copiez les claviers Android requis à un répertoire à `~/Documents/keystores/MyAndroidApp1`laquelle l’utilisateur TeamCity a accès, c’est-à-dire .
7. **Calabash** - Il s’agit d’une étape facultative si votre application a des tests écrits à l’aide de Calabash. Voir le guide [Installing Calabash sur OS X Mavericks](https://github.com/calabash/calabash-ios/wiki) et le Rubis mise à jour avec guide [rbenv](https://github.com/calabash/calabash-ios/wiki) pour plus d’informations.

Le diagramme suivant illustre tous ces éléments :

![](teamcity-images/image1.png "This diagram illustrates all of these components")

Une fois que tout le logiciel est installé, connectez-vous au compte utilisateur et confirmez que tout le logiciel est correctement installé et fonctionne. Cela devrait impliquer la compilation de la solution et la soumission de la demande à App Center Test. Cela peut être simplifié en exécutant le script de construction, tel que décrit dans la section suivante.

## <a name="create-a-build-script"></a>Créer un script build

Bien qu’il soit possible pour TeamCity de gérer tous les aspects de la compilation et de la soumission d’applications mobiles à App Center Test par lui-même; il est recommandé de créer un script de construction. Un script de build offre les avantages suivants :

1. **Documentation** - Un script de construction sert de forme de documentation sur la façon dont le logiciel est construit. Cela supprime une partie de la «magie» qui est associée au déploiement de l’application et permet aux développeurs de se concentrer sur la fonctionnalité.
1. **Répétabilité** - Un script de construction garantit que chaque fois que l’application est compilée et déployée, elle se produit de la même manière, peu importe qui ou ce qui fonctionne. Cette cohérence répétable supprime tous les problèmes ou erreurs qui peuvent apparaître en raison d’une version incorrecte ou d’une erreur humaine.
1. **Version -** Un script de construction peut être inclus dans le système de contrôle source. Cela signifie que les modifications apportées au script de construction peuvent être suivies, surveillées et corrigées si des erreurs ou des inexactitudes sont détectées.
1. **Préparer l’environnement** - Un script de construction peut inclure la logique d’installer toutes les dépendances requises 3ème partie. Cela permettra de s’assurer que les applications sont construites avec les composants appropriés.

Le script de construction peut être aussi simple qu’un fichier PowerShell (sur Windows) ou un script bash (sur OS X). Lors de la création du script de construction, il ya plusieurs choix pour les langues script:

- [**Rake**](https://github.com/jimweirich/rake) - il s’agit d’un domaine spécifique langue (DSL) pour la construction de projets, basé sur Ruby. Rake a l’avantage de la popularité et un riche écosystème de bibliothèques.

- [**psake**](https://github.com/psake/psake) - c’est une bibliothèque Windows PowerShell pour la construction de logiciels

- [**FAKE**](https://fsharp.github.io/FAKE/) - il s’agit d’un DSL basé en F ' qui permet d’utiliser les bibliothèques existantes .NET si nécessaire.

Le langage de script utilisé dépend de vos préférences et de vos exigences.

> [!NOTE]
> Il est possible d’utiliser un système de construction basé sur XML comme MSBuild ou NAnt, mais ceux-ci manquent d’expressivité et de maintenance d’un DSL dédié à la construction de logiciels.

### <a name="parameterizing-the-build-script"></a>Paramétriser le script de construction

Le processus de construction et de test de logiciels nécessite des informations qui doivent être gardées secrètes. La création d’un APK peut nécessiter un mot de passe pour le keystore et/ou l’alias clé dans le keystore. De même, App Center Test nécessite une [clé API](/appcenter/api-docs/) unique à un développeur. Ces types de valeurs ne devraient pas être codés dans le script de construction. Au lieu de cela, ils devraient être adoptés comme des variables pour le script de construction.

Moins sensibles sont des valeurs telles que l’ID de l’appareil iOS ou l’ID de l’appareil Android qui identifient les appareils que Le centre d’applications devrait utiliser pour les essais. Ce ne sont pas des valeurs qui doivent être protégées, mais elles peuvent changer de construction en construction.

Le stockage de ces types de variables en dehors du script de construction facilite également le partage du script de construction au sein d’une organisation, avec les développeurs par exemple. Les développeurs peuvent utiliser exactement le même script que le serveur de construction, mais peuvent utiliser leurs propres claviers et [clés API](/appcenter/api-docs/).

Il existe deux options possibles pour stocker ces valeurs sensibles :

- **Un fichier de configuration** - Pour protéger la [clé API](/appcenter/api-docs/) cette valeur ne doit pas être vérifié dans le contrôle de la version. Le fichier peut être créé pour chaque machine. La façon dont les valeurs sont lues à partir de ce fichier dépend du langage de script utilisé.

- **Variables de l’environnement** - celles-ci peuvent être facilement définies sur une base par machine et sont indépendantes du langage de script sous-jacent.

Il y a des avantages et des inconvénients à chacun de ces choix. TeamCity fonctionne bien avec les variables de l’environnement, de sorte que ce guide recommandera cette technique lors de la création de scripts de construction.

### <a name="build-steps"></a>Construire des étapes

Le script de construction doit faire les étapes suivantes :

- **Compiler l’application** - Cela comprend la signature de la demande avec le profil de provisionnement correct.

- **Soumettez la demande à Xamarin Test Cloud** - Cela comprend la signature et l’alignement de l’APK avec le clavier approprié.

Ces deux étapes seront expliquées plus en détail ci-dessous.

#### <a name="compiling-a-xamarinios-application"></a>Compilation d’une application Xamarin.iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilation d’une application Xamarin.Android

Pour compiler une application Android, utilisez **xbuild** (ou **msbuild** sur Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
La compilation de l’application Android **xbuild** utilise le projet, tandis que l’application iOS **xbuild** utilise la solution.

#### <a name="submitting-xamarinuitests-to-app-center"></a>Soumettre Xamarin.UITests à App Center

Les tests d’interface sont soumis à l’aide de [l’App Center CLI](https://github.com/microsoft/appcenter-cli), comme le montre l’extrait suivant :

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

Lorsque le test est exécuté, les résultats des tests seront retournés sous la forme d’un fichier XML de style NUnit appelé **report.xml**. TeamCity affichera les informations dans le journal Build.

Pour plus d’informations sur la façon de soumettre des tests d’interface utilisateur à App Center, voir [Preparing Xamarin.Android Apps](/appcenter/test-cloud/uitest/preparing-for-upload-android) ou [Préparation des applications Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios).

#### <a name="submitting-calabash-tests-to-app-center"></a>Soumettre des tests calabash à App Center

Les tests de calebasse sont soumis à l’aide de [l’App Center CLI](https://github.com/microsoft/appcenter-cli), comme le montre l’extrait suivant :

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

Pour soumettre une application Android à App Center Test, il est nécessaire de reconstruire d’abord le serveur de test APK à l’aide de calabash-android:

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

Pour plus d’informations sur la soumission de tests calabash, consultez le guide de Xamarin sur [la soumission des tests de calabash pour tester le cloud](https://github.com/calabash/calabash-ios/wiki).

## <a name="creating-a-teamcity-project"></a>Création d’un projet TeamCity

Une fois que TeamCity est installé et Visual Studio pour Mac peut construire votre projet, il est temps de créer un projet dans TeamCity pour construire le projet et le soumettre à App Center.

1. Commencé par se connecter à TeamCity via le navigateur web. Naviguez vers le projet Root :

    ![Naviguez vers le projet Root](teamcity-images/image2.png "Naviguez vers le projet Root") Sous le projet Root, créez un nouveau sous-projet :

    ![Naviguez vers le projet Root Under the Root Project, créez un nouveau sous-projet](teamcity-images/image3.png "Naviguez vers le projet Root Under the Root Project, créez un nouveau sous-projet")
2. Une fois le sous-projet créé, ajoutez une nouvelle configuration de build :

    ![Une fois le sous-projet créé, ajoutez une nouvelle configuration de build](teamcity-images/image5.png "Une fois le sous-projet créé, ajoutez une nouvelle configuration de build")
3. Attachez un projet VCS à la configuration build. Ceci est fait via l’écran de réglage de contrôle de version :

    ![Ceci est fait via l’écran de réglage de contrôle de version](teamcity-images/image6.png "Ceci est fait via l’écran de réglage de contrôle de version")

    S’il n’y a pas de projet VCS créé, vous pouvez en créer un à partir de la nouvelle page VCS Root ci-dessous :

    ![S’il n’y a pas de projet VCS créé, vous pouvez en créer un à partir de la nouvelle page VCS Root](teamcity-images/image7.png "S’il n’y a pas de projet VCS créé, vous avez la possibilité d’en créer un à partir de la nouvelle page VCS Root")

    Une fois que la racine VCS a été attachée, TeamCity vérifiera le projet et essayera de détecter automatiquement les étapes de construction. Si vous connaissez TeamCity, vous pouvez sélectionner l’une des étapes de construction détectées. Il est sûr d’ignorer les étapes de construction détectées pour l’instant.

4. Ensuite, configurez un déclencheur de build. Cela fera la queue jusqu’à une accumulation lorsque certaines conditions sont remplies, par exemple lorsqu’un utilisateur commet du code au référentiel. La capture d’écran suivante montre comment ajouter un déclencheur de construction :

    ![Cette capture d’écran montre comment ajouter un déclencheur de construction](teamcity-images/image8.png "Cette capture d’écran montre comment ajouter un déclencheur de construction") Un exemple de configuration d’un déclencheur de construction peut être vu dans la capture d’écran suivante:

    ![Un exemple de configuration d’un déclencheur de construction peut être vu dans cette capture d’écran](teamcity-images/image9.png "Un exemple de configuration d’un déclencheur de construction peut être vu dans cette capture d’écran")

5. La section précédente, Parameterizing the Build Script, suggérait de stocker certaines valeurs comme variables de l’environnement. Ces variables peuvent être ajoutées à la configuration de construction via l’écran Paramètres. Ajoutez les variables pour la [clé aPI](/appcenter/api-docs/)App Center , l’ID de l’appareil iOS et l’ID appareil Android comme indiqué dans la capture d’écran ci-dessous:

    ![Ajoutez les variables pour la clé aPI de test App Center, l’ID de l’appareil iOS et l’ID de périphérique Android](teamcity-images/image11.png "Ajoutez les variables pour la clé d’API De Test Cloud, l’ID de l’appareil iOS et l’ID de l’appareil Android")

6. La dernière étape consiste à ajouter une étape de construction qui invoquera le script de construction pour compiler l’application et enqueue l’application à App Center Test. La capture d’écran suivante est un exemple d’étape de construction qui utilise un Rakefile pour construire une application :

    ![Cette capture d’écran est un exemple d’étape de construction qui utilise un Rakefile pour construire une application](teamcity-images/image12.png "Cette capture d’écran est un exemple d’étape de construction qui utilise un Rakefile pour construire une application")

7. À ce stade, la configuration de construction est complète. C’est une bonne idée de déclencher une build pour confirmer que le projet est correctement configuré. Une bonne façon de le faire est d’engager un petit changement insignifiant au référentiel. TeamCity doit détecter l’commit et démarrer une build.

8. Une fois la construction terminée, inspectez le journal de construction et voyez s’il y a des problèmes ou des avertissements avec la construction qui nécessitent une attention particulière.

## <a name="summary"></a>Récapitulatif

Ce guide a couvert la façon d’utiliser TeamCity pour construire des applications Xamarin Mobile, puis de les soumettre à App Center Test. Nous avons discuté de la création d’un script de construction pour automatiser le processus de construction. Le script de construction s’occupe de la compilation de l’application, de la soumission au test App Center et de l’attente des résultats.

Ensuite, nous avons couvert la façon de créer un projet dans TeamCity qui fera la queue d’une version chaque fois qu’un développeur commet du code et appellera le script de construction.

## <a name="related-links"></a>Liens connexes

- [Préparation des applications Xamarin.Android](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [Préparation des applications Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [Installation et configuration TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
