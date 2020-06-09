---
title: Utilisation des listes de propriétés dans Xamarin. iOS
description: Ce document présente l’éditeur de liste de propriétés (. plist) graphique et avancé de Visual Studio pour Mac pour l’utilisation d’info. plist et de conversions. plist. Il illustre la définition des icônes et des images de lancement pour les applications iOS dans Visual Studio pour Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6c2b5869f647f65b932b6ec92f359f8a79402c8f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569294"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Utilisation des listes de propriétés dans Xamarin. iOS

_Ce document présente l’éditeur de liste de propriétés (. plist) graphique et avancé de Visual Studio pour Mac pour l’utilisation d’info. plist et de conversions. plist. Il illustre la définition des icônes et des images de lancement pour les applications iOS dans Visual Studio pour Mac._

Visual Studio pour Mac propose un éditeur Graphical. plist, qui facilite la modification des propriétés et des fonctionnalités de l’application. Visual Studio pour Mac a deux. plist : `Info.plist` pour modifier les icônes et les propriétés de l’application, ainsi que `Entitlements.plist` pour gérer les fonctionnalités de l’application. Ce guide présente le fichier info. plist et fournit une vue d’ensemble de son utilisation dans Visual Studio pour Mac. Pour plus d’informations sur habilitations. plist, consultez le guide [utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md) .

## <a name="infoplist"></a>Info.plist

La liste de propriétés d’informations ( `Info.plist` ) est un fichier iOS requis qui fournit des informations sur la configuration de votre application au système. L’éditeur personnalisé de Visual Studio pour Mac `Info.plist` présente trois panneaux contrôlés par des onglets en bas à gauche de la fenêtre de l’éditeur :

 [![](property-lists-images/tabs.png "The Info.plist editor tabs at the bottom left of the editor window")](property-lists-images/tabs.png#lightbox)

Chaque panneau contrôle différentes propriétés, comme indiqué ci-dessous :

- **Panneau** de l’application : interface graphique permettant de définir des propriétés d’application courantes, ainsi que des icônes et des images de lancement. Spécifiez l’intégration des cartes et les modes d’arrière-plan.
- **Panneau avancé** : le panneau avancé est l’endroit où vous pouvez spécifier les types de documents pris en charge, les UTI et les types d’URL.
- **Panneau** source : le panneau source contrôle les propriétés moins courantes, ainsi que les propriétés personnalisées de l’application.

Les trois sections suivantes étudient plus en détail les fonctionnalités de chaque volet.

## <a name="application-panel"></a>Panneau de l’application

Visual Studio pour Mac propose une interface graphique pour la modification `Info.plist` des entrées communes pour une application :

1. Propriétés de l’application
1. Types d’appareils pris en charge
1. Prendre en charge les orientations pour chaque type d’appareil
1. Style et couleur de la barre d’État
1. Icônes et écrans de démarrage
1. Cartes et modes d’arrière-plan

Celles-ci sont décrites plus en détail dans les sections suivantes.

 <a name="iOS_Application_Target"></a>

### <a name="ios-application-target"></a>Cible d’application iOS

Cette section contient des informations importantes qui décrivent votre application.
L' **identificateur** stocké ici doit correspondre à l’identificateur de bundle qui est entré dans iTunes Connect (pour les applications App Store) et également dans le portail d’approvisionnement iOS liste des ID d’application et certificats de développement et de distribution.

 [![](property-lists-images/image24.png "iOS Application Target")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Déploiement de l’appareil

 [![](property-lists-images/deployment.png "Device Deployment")](property-lists-images/deployment.png#lightbox)

Les sections informations de **déploiement** de l’appareil s’affichent de manière sélective, en fonction de la sélection dans la liste déroulante **appareils** de la section **cible d’application** ci-dessus. La liste déroulante **interface principale** est définie sur **fichier mainstoryboard** dans les applications pilotées par des storyboards. Si l’interface utilisateur est entièrement écrite dans le code, ce champ peut être laissé vide.

### <a name="supported-device-orientations"></a>Orientations d’appareils prises en charge

 L' **orientation des appareils pris en charge** contrôle la manière dont l’application répond à la rotation de l’appareil. Il est très courant que les applications iPhone/iPad prennent uniquement en charge le mode **portrait**, ou tout ce qui est à l' **envers**. En général, toutes les applications iPad, à l’exception des jeux, doivent prendre en charge toutes les orientations.

### <a name="status-bar-styles"></a>Styles de barre d’État

La section des **styles de barre d’État** est une interface graphique pour la modification d’une application `UIStatusBarStyle` :

 [![](property-lists-images/status.png "Status Bar Styles")](property-lists-images/status.png#lightbox)

 <a name="Icons"></a>

### <a name="icons-launch-images-and-itunes-artwork"></a>Icônes, images de lancement et illustration iTunes

Vous trouverez des informations sur l’utilisation des icônes, des images et des illustrations dans le fichier info. plist dans le guide [utilisation des images](~/ios/app-fundamentals/images-icons/index.md) .

### <a name="maps-integration-and-background-modes"></a>Modes d’intégration et d’arrière-plan de Maps

Le `Info.plist` contient des sections spéciales pour spécifier l’intégration des cartes et les modes d’arrière-plan. Si vous choisissez les options que vous souhaitez prendre en charge, vous devez ajouter les propriétés requises à votre application.

 [![](property-lists-images/maps.png "Maps Integration")](property-lists-images/maps.png#lightbox)

Pour plus d’informations sur l’utilisation de Maps, reportez-vous au Guide des [cartes iOS](~/ios/user-interface/controls/ios-maps/index.md) Xamarin.

 [![](property-lists-images/bging.png "Background Modes")](property-lists-images/bging.png#lightbox)

Pour plus d’informations sur les modes d’arrière-plan, reportez-vous au Guide d’Xamarin [en arrière-plan dans iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) .

## <a name="advanced-panel"></a>Panneau avancé

Le volet avancé contrôle les types de documents et les schémas d’URL que l’application prend en charge.

 [![](property-lists-images/image34.png "Advanced Panel")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types"></a>

## <a name="document-types"></a>Types de documents

Pour les applications qui prennent en charge l’ouverture de types spécifiques de fichiers, iOS fournit la `CFBundleDocumentTypes` clé. Si nous souhaitons que notre application prenne en charge certains types de fichiers connus (PDF, par exemple), nous ajouterons la valeur PDF à la clé. Cette section offre un moyen pratique d’entrer les données qui seront stockées dans la `CFBundleDocumentTypes` clé dans le `Info.plist` fichier.

Pour plus d’informations sur la configuration de ces valeurs, reportez-vous à la documentation sur l' [enregistrement des types de fichiers pris en charge par votre application](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) .

## <a name="utis"></a>UTI

Parfois, une application doit prendre en charge l’ouverture d’un type de fichier personnalisé. Par exemple, nous pouvons souhaiter ouvrir des fichiers image avec une extension personnalisée *. Xam*. Pour spécifier un type de fichier personnalisé, nous allons créer un identificateur de type UTI-Universal personnalisé à l’aide de la `UIExportedTypeDeclarations` clé. La capture d’écran ci-dessous montre comment créer un UTI personnalisé pour l’extension. Xam :

 [![](property-lists-images/uti.png "UTIs Editor")](property-lists-images/uti.png#lightbox)

Tout comme le type exporté UTI spécifiez des UTI personnalisés spécifiques à votre application, le *type importé UTI* ( `UIImportedTypeDeclarations` clé) Spécifiez les types personnalisés pris en charge, mais qui ne sont pas détenus par votre application.

Pour plus d’informations sur l’utilisation de UTI personnalisés, reportez-vous à la rubrique [inscription des types de fichiers d’Apple que votre application prend en charge](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) .

## <a name="custom-urls"></a>URL personnalisées

Un nom de schéma d’URL (également appelé protocole) est la première partie de l’URL. Par exemple, `http://` et `https://` sont des schémas d’URL courants. Vous avez la possibilité de créer un modèle d’URL personnalisé pour votre application. Les schémas d’URL personnalisés servent à communiquer et à envoyer des données avec d’autres applications. La capture d’écran suivante illustre la création d’un nouveau schéma d’URL personnalisé appelé `monkeys://` :

 [![](property-lists-images/url.png "Custom URLs")](property-lists-images/url.png#lightbox)

Pour plus d’informations sur l’implémentation de modèles d’URL personnalisés, reportez-vous à la [section implémentation de modèles d’URL personnalisés d’Apple dans ce guide](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html) .

## <a name="source-panel"></a>Panneau source

L’onglet **source** du `Info.plist` fichier permet d’ajouter ou de modifier des valeurs personnalisées. Visual Studio pour Mac fournit une liste des propriétés les plus courantes :

 [![](property-lists-images/image31.png "Adding a new property from a dropdown")](property-lists-images/image31.png#lightbox)

Pour les propriétés connues Visual Studio pour Mac est une liste de valeurs valides, comme l’illustre la capture d’écran suivante :

 [![](property-lists-images/image32.png "Select a value from a know value list")](property-lists-images/image32.png#lightbox)

Visual Studio pour Mac détecte également le type de propriété, comme indiqué ci-dessous :

 [![](property-lists-images/image33.png "The available property types")](property-lists-images/image33.png#lightbox)

Consultez les liens vers les [ressources relatives aux applications](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) Apple pour plus d’informations sur les propriétés facultatives.

 <a name="Entitlements"></a>

## <a name="summary"></a>Résumé

Cet article a montré comment utiliser les éditeurs graphiques et avancés. plist pour modifier les configurations d’applications courantes, ainsi que pour spécifier des icônes et des images de lancement. Elle a également introduit le `Entitlements.plist` pour l’ajout et la gestion des fonctionnalités de l’application.

## <a name="related-links"></a>Liens connexes

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Ressources associées à l’application](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Enregistrement des types de fichiers pris en charge par votre application](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implémentation de modèles d’URL personnalisés](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Référence du format du catalogue de ressources](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
