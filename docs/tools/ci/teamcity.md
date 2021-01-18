---
title: Utilisation de TeamCity avec Xamarin
description: Ce guide aborde les étapes nécessaires à l’utilisation de TeamCity pour compiler des applications mobiles, puis les envoyer à App Center test.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: 553f40a407fa8003a6545214a545f00b01fb0ed6
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555033"
---
# <a name="using-teamcity-with-xamarin"></a>Utilisation de TeamCity avec Xamarin

_Ce guide aborde les étapes nécessaires à l’utilisation de TeamCity pour compiler des applications mobiles, puis les envoyer à App Center test._

Comme nous l’avons vu dans le guide [d’intégration continue](~/tools/ci/intro-to-ci.md) , l’intégration continue (ci) est une pratique utile lors du développement d’applications mobiles de qualité. Il existe de nombreuses options viables pour le logiciel du serveur d’intégration continue. ce guide se concentre sur [TeamCity](https://www.jetbrains.com/teamcity/) à partir de JetBrains.

Il existe plusieurs permutations différentes d’une installation TeamCity. La liste suivante décrit quelques-unes de ces permutations :

- **Service Windows** : dans ce scénario, TeamCity démarre lorsque Windows démarre en tant que service Windows. Il doit être associé à un hôte de build Mac pour compiler toutes les applications iOS.

- **Lancer le démon sur OS X** : conceptuellement, cette opération est similaire à l’exécution en tant que service Windows décrit à l’étape précédente. Par défaut, les builds sont exécutées sous le compte racine.

- **Compte d’utilisateur sur OS X** : il est possible d’exécuter TeamCity sous un compte d’utilisateur qui démarre chaque fois que l’utilisateur se connecte.

Parmi les scénarios précédents, l’exécution de TeamCity sous un compte d’utilisateur sur OS X est la plus simple et la plus facile à configurer.

Plusieurs étapes sont nécessaires à la configuration de TeamCity :

- **Installation de TeamCity** – l’installation de TeamCity n’est pas abordée dans ce guide. Ce guide part du principe que TeamCity est installé et s’exécute sous un compte d’utilisateur. Vous trouverez des instructions sur [l’installation de TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) dans la [documentation TeamCity 8](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) de JetBrains.

- **Préparation du serveur de builds** : cette étape implique l’installation des logiciels, des outils et des certificats nécessaires à la création d’applications mobiles et leur préparation à la distribution.

- **Création d’un script de génération** : cette étape n’est pas strictement nécessaire, mais un script de génération est une aide utile pour créer des applications sans assistance. L’utilisation d’un script de génération vous aide à résoudre les problèmes de génération pouvant survenir et fournit un moyen cohérent et reproductible de créer les binaires pour la distribution, même si l’intégration continue n’est pas recommandée.

- **Création d’un projet TeamCity** : une fois les trois étapes précédentes terminées, nous devons créer un projet TeamCity qui contiendra toutes les métadonnées nécessaires pour récupérer le code source, compilez les projets et soumettez les tests à App Center test.

## <a name="requirements"></a>Spécifications

Une expérience avec [App Center Test](/appcenter/test-cloud/) est nécessaire.

Vous devez vous familiariser avec TeamCity 8,1. L’installation de TeamCity n’est pas traitée dans ce document. Il est supposé que TeamCity est installé sur OS X Mavericks et qu’il s’exécute sous un compte d’utilisateur standard et non pas le compte racine.

Le serveur de builds doit être un ordinateur autonome, exécutant OS X, qui est dédié à l’intégration continue. Dans l’idéal, le serveur de builds ne sera pas responsable de tout autre rôle, tel qu’un serveur de base de données, un serveur Web ou une station de travail de développeur.

> [!IMPORTANT]
> Ce guide ne couvre pas une installation « headless » de Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Préparation du serveur de builds

L’une des étapes importantes de la configuration d’un serveur de builds consiste à installer tous les outils, logiciels et certificats nécessaires pour créer les applications mobiles. Il est important que le serveur de builds puisse compiler la solution mobile et exécuter des tests. Pour réduire les problèmes de configuration, vous devez installer les logiciels et les outils dans le même compte d’utilisateur qui héberge TeamCity. La liste suivante détaille les éléments requis :

1. **Visual Studio pour Mac** : cela comprend Xamarin. iOS et Xamarin. Android.
2. **Connectez-vous au magasin de composants Xamarin** – cette étape est facultative et n’est nécessaire que si votre application utilise des composants du magasin de composants Xamarin. La connexion proactive au magasin de composants à ce stade empêchera tout problème quand une build TeamCity essaie de compiler l’application.
3. **Xcode** – Xcode est requis pour compiler et signer des applications iOS.
4. **Outils de Command-Line Xcode** – décrit à l’étape 1 de la section Installation du Guide de [mise à jour de Ruby avec rbenv](https://github.com/calabash/calabash-ios/wiki) .
5. **Signature de l’identité & profils de provisionnement** : importez les certificats et le profil de provisionnement via Xcode. Pour plus d’informations, consultez le Guide d’Apple sur l' [exportation des identités de signature et des profils de provisionnement](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) .
6. **Magasins de clés Android** : copiez les magasins de clés Android nécessaires dans un répertoire auquel l’utilisateur TeamCity a accès, c.-à-d. `~/Documents/keystores/MyAndroidApp1` .
7. **Calabash** : il s’agit d’une étape facultative si votre application a des tests écrits à l’aide de Calabash. Pour plus d’informations, consultez le guide [d’installation de Calabash sur OS X Mavericks](https://github.com/calabash/calabash-ios/wiki) et le Guide de [mise à jour de Ruby avec rbenv](https://github.com/calabash/calabash-ios/wiki) .

Le diagramme suivant illustre tous les composants suivants :

![Ce diagramme illustre tous ces composants.](teamcity-images/image1.png)

Une fois que tout le logiciel est installé, connectez-vous au compte d’utilisateur et confirmez que l’ensemble du logiciel est correctement installé et qu’il fonctionne. Cela doit impliquer la compilation de la solution et la soumission de l’application à App Center test. Cela peut être simplifié en exécutant le script de génération, comme décrit dans la section suivante.

## <a name="create-a-build-script"></a>Créer un script de génération

Bien qu’il soit possible pour TeamCity de gérer tous les aspects de la compilation et de la soumission d’applications mobiles à App Center test par lui-même ; Il est recommandé de créer un script de génération. Un script de génération offre les avantages suivants :

1. **Documentation** : un script de génération sert de documentation sur la façon dont le logiciel est créé. Cela supprime une partie du « Magic » associé au déploiement de l’application et permet aux développeurs de se concentrer sur les fonctionnalités.
1. **Répétabilité** : un script de génération garantit que chaque fois que l’application est compilée et déployée, elle se produit de la même manière, quel que soit l’utilisateur ou le travail. Cette cohérence reproductible supprime les problèmes ou erreurs qui peuvent apparaître en raison d’une génération incorrecte ou d’une erreur humaine.
1. Contrôle de **version** : un script de génération peut être inclus dans le système de contrôle de code source. Cela signifie que les modifications apportées au script de génération peuvent être suivies, surveillées et corrigées en cas d’erreurs ou d’inexactitudes.
1. **Préparer l’environnement** : un script de génération peut inclure une logique pour installer les dépendances tierces requises. Cela permet de s’assurer que les applications sont générées avec les composants appropriés.

Le script de génération peut être aussi simple qu’un fichier PowerShell (sur Windows) ou un script bash (sur OS X). Lors de la création du script de génération, il existe plusieurs choix pour les langages de script :

- [**Rake**](https://github.com/jimweirich/rake) : il s’agit d’un langage de Domain-Specific (DSL) pour la génération de projets, en fonction de Ruby. Rake présente l’avantage de la popularité et un écosystème riche de bibliothèques.

- [**psake**](https://github.com/psake/psake) – il s’agit d’une bibliothèque Windows PowerShell pour la création de logiciels

- [**Factice**](https://fsharp.github.io/FAKE/) : il s’agit d’un DSL basé sur F #, qui permet d’utiliser des bibliothèques .NET existantes si nécessaire.

Le langage de script utilisé dépend de vos préférences et de vos exigences.

> [!NOTE]
> Il est possible d’utiliser un système de génération basé sur XML, tel que MSBuild ou NAnt, mais ceux-ci ne disposent pas de l’expressivité et de la maintenabilité d’un langage DSL dédié à la création de logiciels.

### <a name="parameterizing-the-build-script"></a>Paramétrage du script de génération

Le processus de génération et de test des logiciels nécessite des informations qui doivent être tenues secrètes. La création d’un APK peut nécessiter un mot de passe pour le magasin de clés et/ou l’alias de clé dans le magasin de clés. De même, App Center test nécessite une [clé API](/appcenter/api-docs/) propre à un développeur. Ces types de valeurs ne doivent pas être codés en dur dans le script de génération. Au lieu de cela, elles doivent être passées en tant que variables au script de compilation.

Moins sensibles sont des valeurs telles que l’ID d’appareil iOS ou l’ID d’appareil Android qui identifient les périphériques que App Center doit utiliser pour les séries de tests. Ce ne sont pas des valeurs qui doivent être protégées, mais elles peuvent passer de build à Build.

Le stockage de ces types de variables en dehors du script de génération facilite également le partage du script de compilation au sein d’une organisation, avec les développeurs, par exemple. Les développeurs peuvent utiliser exactement le même script que le serveur de builds, mais ils peuvent utiliser leurs propres [clés API](/appcenter/api-docs/)et keystore.

Il existe deux options possibles pour stocker ces valeurs sensibles :

- **Un fichier de configuration** : pour protéger la [clé API](/appcenter/api-docs/) , cette valeur ne doit pas être archivée dans le contrôle de version. Le fichier peut être créé pour chaque ordinateur. La façon dont les valeurs sont lues à partir de ce fichier dépend du langage de script utilisé.

- **Variables d’environnement** : elles peuvent être facilement définies sur une base par ordinateur et sont indépendantes du langage de script sous-jacent.

Chacun de ces choix présente des avantages et des inconvénients. TeamCity fonctionne parfaitement avec les variables d’environnement. ce guide vous recommandera donc cette technique lors de la création de scripts de génération.

### <a name="build-steps"></a>Étapes de génération

Le script de génération doit effectuer les étapes suivantes :

- **Compilez l’application** : cela comprend la signature de l’application avec le profil de provisionnement approprié.

- **Soumettre l’application à Xamarin test Cloud** – cela comprend la signature et l’alignement du fichier zip avec le magasin de clés approprié.

Ces deux étapes sont expliquées plus en détail ci-dessous.

#### <a name="compiling-a-xamarinios-application"></a>Compilation d’une application Xamarin. iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilation d’une application Xamarin. Android

Pour compiler une application Android, utilisez **xbuild** (ou **MSBuild** sur Windows) :

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
La compilation de l’application Android **xbuild** utilise le projet, tandis que l’application iOS **xbuild** utilise la solution.

#### <a name="submitting-xamarinuitests-to-app-center"></a>Envoi de Xamarin. tests UITest à App Center

Les tests UITest sont envoyés à l’aide de l' [interface CLI App Center](https://github.com/microsoft/appcenter-cli), comme illustré dans l’extrait de code suivant :

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

Lorsque le test est exécuté, les résultats des tests sont retournés sous la forme d’un fichier XML de style NUnit appelé **report.xml**. TeamCity affiche les informations dans le journal de génération.

Pour plus d’informations sur la façon d’envoyer des tests UITest à des App Center, consultez [préparation des applications Xamarin. Android](/appcenter/test-cloud/uitest/preparing-for-upload-android) ou [préparation des applications Xamarin. iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios).

#### <a name="submitting-calabash-tests-to-app-center"></a>Envoi de tests Calabash à App Center

Les tests Calabash sont envoyés à l’aide de l' [interface CLI App Center](https://github.com/microsoft/appcenter-cli), comme illustré dans l’extrait de code suivant :

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

Pour soumettre une application Android à App Center test, il est nécessaire de commencer par reconstruire le serveur de test APK à l’aide de Calabash-Android :

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

Pour plus d’informations sur l’envoi de tests Calabash, consultez le Guide de Xamarin sur l' [envoi de tests Calabash à test Cloud](https://github.com/calabash/calabash-ios/wiki).

## <a name="creating-a-teamcity-project"></a>Création d’un projet TeamCity

Une fois TeamCity installé et Visual Studio pour Mac pouvez générer votre projet, il est temps de créer un projet dans TeamCity pour générer le projet et l’envoyer à App Center.

1. Démarré en se connectant à TeamCity via le navigateur Web. Accédez au projet racine :

    ![Accéder au projet racine](teamcity-images/image2.png "Accéder au projet racine") Sous le projet racine, créez un sous-projet :

    ![Accédez au projet racine sous le projet racine, puis créez un nouveau sous-projet](teamcity-images/image3.png "Accédez au projet racine sous le projet racine, puis créez un sous-projet.")
2. Une fois le sous-projet créé, ajoutez une nouvelle configuration de build :

    ![Une fois le sous-projet créé, ajoutez une nouvelle configuration de build](teamcity-images/image5.png "Une fois le sous-projet créé, ajoutez une nouvelle configuration de build")
3. Attachez un projet VCS à la configuration de Build. Cette opération s’effectue via l’écran de paramètre de contrôle de version :

    ![Cette opération s’effectue à l’aide de l’écran paramètre de contrôle de version](teamcity-images/image6.png "Cette opération s’effectue à l’aide de l’écran paramètre de contrôle de version")

    Si aucun projet VCS n’est créé, vous pouvez en créer un à partir de la page racine du nouveau VCS, comme indiqué ci-dessous :

    ![Si aucun projet VCS n’est créé, vous pouvez en créer un à partir de la page racine du nouveau VCS.](teamcity-images/image7.png "Si aucun projet VCS n’est créé, vous avez la possibilité d’en créer un à partir de la nouvelle page racine du VCS.")

    Une fois la racine du VCS attachée, TeamCity examine le projet et tente de détecter automatiquement les étapes de génération. Si vous êtes familiarisé avec TeamCity, vous pouvez sélectionner l’une des étapes de build détectées. Il est possible d’ignorer les étapes de génération détectées pour l’instant.

4. Ensuite, configurez un déclencheur de Build. Cela met en file d’attente une build lorsque certaines conditions sont remplies, par exemple lorsqu’un utilisateur valide du code dans le référentiel. La capture d’écran suivante montre comment ajouter un déclencheur de build :

    ![Cette capture d’écran montre comment ajouter un déclencheur de build](teamcity-images/image8.png "Cette capture d’écran montre comment ajouter un déclencheur de build") Vous trouverez un exemple de configuration d’un déclencheur de build dans la capture d’écran suivante :

    ![Vous pouvez voir un exemple de configuration d’un déclencheur de build dans cette capture d’écran.](teamcity-images/image9.png "Vous pouvez voir un exemple de configuration d’un déclencheur de build dans cette capture d’écran.")

5. La section précédente, paramétrage du script de génération, suggérant le stockage de certaines valeurs en tant que variables d’environnement. Ces variables peuvent être ajoutées à la configuration de build via l’écran Paramètres. Ajoutez les variables pour la [clé API](/appcenter/api-docs/)App Center, l’ID d’appareil iOS et l’ID d’appareil Android, comme indiqué dans la capture d’écran ci-dessous :

    ![Ajouter les variables pour la App Center clé API de test, l’ID d’appareil iOS et l’ID d’appareil Android](teamcity-images/image11.png "Ajoutez les variables pour la clé API Test Cloud, l’ID d’appareil iOS et l’ID d’appareil Android.")

6. La dernière étape consiste à ajouter une étape de génération qui appellera le script de compilation pour compiler l’application et mettre l’application en file d’attente pour App Center test. La capture d’écran suivante est un exemple d’une étape de génération qui utilise un Rakefile pour créer une application :

    ![Cette capture d’écran est un exemple d’une étape de génération qui utilise un Rakefile pour générer une application](teamcity-images/image12.png "Cette capture d’écran est un exemple d’une étape de génération qui utilise un Rakefile pour générer une application")

7. À ce stade, la configuration de build est terminée. Il est judicieux de déclencher une build pour confirmer que le projet est correctement configuré. Une bonne façon de procéder est de valider une modification minime et non significative dans le référentiel. TeamCity doit détecter la validation et démarrer une build.

8. Une fois la génération terminée, examinez le journal de génération et vérifiez s’il existe des problèmes ou des avertissements avec la build qui requièrent votre attention.

## <a name="summary"></a>Récapitulatif

Ce guide a expliqué comment utiliser TeamCity pour générer des applications mobiles Xamarin, puis les envoyer à App Center test. Nous avons abordé la création d’un script de génération pour automatiser le processus de génération. Le script de génération s’occupe de la compilation de l’application, de l’envoi à App Center test et de l’attente des résultats.

Nous avons ensuite abordé la création d’un projet dans TeamCity qui met en file d’attente une build chaque fois qu’un développeur valide le code et appelle le script de compilation.

## <a name="related-links"></a>Liens associés

- [Préparation des applications Xamarin. Android](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [Préparation des applications Xamarin. iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [Installation et configuration de TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
