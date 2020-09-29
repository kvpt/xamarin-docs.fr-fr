---
title: 'Partie 6 : Test et approbations de l’App Store'
description: Ce document décrit comment tester une application multiplateforme sur un appareil, gérer des cas de test, automatiser des tests, exécuter des tests unitaires et utiliser le processus de soumission d’application.
ms.prod: xamarin
ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 5bddbee99f068baea69d3bc7dc8bcb731c547a43
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458015"
---
# <a name="part-6---testing-and-app-store-approvals"></a>Partie 6 : Test et approbations de l’App Store

## <a name="testing"></a>Test

De nombreuses applications (même les applications Android, sur certains magasins) devront passer un processus d’approbation avant leur publication. le test est donc essentiel pour s’assurer que votre application atteint le marché (la seule fois avec vos clients). Les tests peuvent prendre de nombreuses formes, des tests unitaires au niveau du développeur à la gestion des tests bêta sur une large gamme de matériel.

### <a name="test-on-all-platforms"></a>Tester sur toutes les plateformes

Il existe de légères différences entre la prise en charge par .NET sur les appareils Windows Phone, tablette et de bureau, ainsi que les limitations sur iOS qui empêchent la génération de code dynamique à la volée. Vous pouvez soit planifier le test du code sur plusieurs plateformes à mesure que vous le développez, soit planifier le refactorisation et mettre à jour la partie du modèle de votre application à la fin du projet.

Il est toujours conseillé d’utiliser le simulateur/émulateur pour tester plusieurs versions du système d’exploitation, ainsi que différentes fonctionnalités/configurations de l’appareil.

Vous devez également tester autant de périphériques matériels physiques que vous le pouvez.

#### <a name="devices-in-cloud"></a>Appareils dans le Cloud

Le téléphone mobile et l’écosystème de tablettes évoluent tout le temps, ce qui rend impossible le test sur le nombre toujours plus important d’appareils disponibles. Pour résoudre ce problème, un certain nombre de services permettent de contrôler à distance de nombreux appareils différents afin que les applications puissent être installées et testées sans avoir à investir directement dans un grand nombre de matériel.

App Center Test offre un moyen simple de tester des applications iOS et Android sur des centaines d’appareils différents. Pour plus d’informations, consultez [Preparing Xamarin. Android Apps](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest) et [Preparing Xamarin. iOS Apps](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

### <a name="test-management"></a>Gestion des tests

Lors du test d’applications au sein de votre organisation ou de la gestion d’un programme bêta avec des utilisateurs externes, il existe deux défis :

- **Distribution** : gestion du processus d’approvisionnement (en particulier pour les appareils IOS) et obtention des versions mises à jour des logiciels aux testeurs.
- **Commentaires** : collecte des informations sur l’utilisation des applications et des informations détaillées sur les erreurs qui peuvent se produire.

Il existe un certain nombre de services qui permettent de résoudre ces problèmes en fournissant une infrastructure intégrée à votre application pour collecter et signaler l’utilisation et les erreurs, ainsi que pour rationaliser le processus d’approvisionnement afin de vous aider à inscrire et gérer des testeurs et leurs appareils.

[Visual Studio App Center](/appcenter/) offre une solution à ces problèmes, en fournissant la distribution des versions de test, les rapports d’incidents et les informations d’utilisation des applications sophistiquées.

### <a name="test-automation"></a>Automatisation des tests

Xamarin [UITest](/appcenter/test-cloud/preparing-for-upload/uitest) peut être utilisé pour créer des scripts de test d’interface utilisateur automatisés qui peuvent être exécutés localement ou téléchargés vers [App Center test](/appcenter/test-cloud/).

## <a name="unit-testing"></a>Test unitaire

### <a name="touchunit"></a>Touch.Unit

Xamarin. iOS comprend une infrastructure de tests unitaires appelée Touch. Unit qui suit les tests d’écriture de style JUnit/NUnit.

Pour plus d’informations sur l’écriture de tests et l’exécution de Touch. Unit, consultez la documentation sur [les tests unitaires avec Xamarin. iOS](~/ios/deploy-test/touch.unit.md) .

### <a name="andrunit"></a>Andr. Unit

Il existe un équivalent Open source de Touch. Unit pour Android appelé Andr. Unit. Vous pouvez le télécharger à partir de [GitHub](https://github.com/spouliot/Andr.Unit) et en savoir plus sur le [ @spouliot blog](https://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/)de l’outil sur.

## <a name="app-store-approvals"></a>Approbations de l’App Store

Apple et Microsoft exploitent la seule boutique sur leurs plateformes : l’App Store et la place de marché, respectivement. Verrouillez leurs appareils et implémentez un processus de révision de l’application rigoureux pour contrôler la qualité des applications disponibles pour le téléchargement. La nature ouverte d’Android signifie qu’il existe un certain nombre d’options de stockage allant de la lecture de Google, qui est largement disponible et n’a pas de processus de révision, à Amazon AppStore pour Android et les efforts spécifiques au matériel, comme les applications Samsung, qui ont une distribution plus limitée et mettent en œuvre un processus d’approbation.

L’attente d’une application à réviser peut être une pression très importante : les applications sont souvent soumises à l’approbation, avec une marge d’erreur très faible avant une date de lancement « ciblée ». Le processus lui-même peut prendre jusqu’à deux semaines et n’est pas nécessairement transparent : il y a un commentaire limité sur la progression de votre application jusqu’à ce qu’elle soit définitivement rejetée ou approuvée. Le rejet peut signifier qu’il manque une fenêtre marketing d’opportunités, surtout si elle se produit plusieurs fois, et les semaines passent entre la date de lancement d’origine et le moment où l’application est finalement approuvée.

### <a name="be-prepared"></a>Soyez prêt

Le premier Conseil : planifiez le lancement de votre application à l’avance et assurez-vous qu’un refus et une nouvelle soumission sont possibles. Chaque magasin vous oblige à créer un compte avant de soumettre votre application. Procédez ainsi dès que possible.
Alors que l’inscription de Google Play ne prend que quelques minutes si vos applications sont gratuites, le processus est beaucoup plus complexe si vous les vendez et que vous devez fournir des informations bancaires et fiscales. Les processus Apple et Microsoft sont bien plus impliqués que ceux de Google, ce qui peut nécessiter une semaine ou plus pour que votre compte soit approuvé.

Une fois votre compte approuvé, vous êtes prêt à soumettre une application. Le processus réel de soumission d’applications est abordé dans la documentation suivante :

- [Publication sur l’App Store iOS d’Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Préparation d’une application pour Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
- Les développeurs Windows doivent visiter le [Centre de développement Windows](https://developer.microsoft.com/windows/windows-apps) pour en savoir plus sur l’envoi de leurs applications.

Le reste de cette section traite des éléments à prendre en compte pour s’assurer que votre application est approuvée sans interruptions.

### <a name="quality"></a>Qualité

Cela semble évident, mais les applications sont souvent rejetées, car elles ne répondent pas à un certain niveau de qualité : après tout, c’est la raison pour laquelle les magasins organisés ont un processus d’approbation en premier lieu.

Les incidents sont souvent à l’origine du rejet. S’il est trop facile de faire échouer votre application, il est garanti qu’elle sera rejetée. La plupart des développeurs n’envoient pas leurs applications dans l’attente qu’ils se bloquent, mais ils le font souvent. Testez soigneusement votre application avant de la soumettre, en vous concentrant non seulement sur la vérification de tous les éléments, mais également sur la gestion des scénarios d’erreurs mobiles courants tels que les problèmes de réseau et les contraintes de ressources telles que la mémoire ou l’espace de stockage. Utilisez à la fois le simulateur et les appareils physiques pour tester, quelle que soit la façon dont le code s’exécute dans un simulateur, seul un appareil peut démontrer les performances réelles d’une application. Utilisez autant d’appareils que vous le pouvez trouver et inscrivez une équipe de bêta-testeurs si vous pouvez utiliser des services tiers pour gérer la distribution et les commentaires de la version bêta.

Tous les systèmes d’exploitation mobiles supprimeront une application qui ne démarre pas assez rapidement. La durée allouée varie, mais dans les applications générales, il est préférable que les applications soient réactives en quelques secondes et qu’elles utilisent des tâches en arrière-plan pour effectuer tout travail qui prendra plus de temps. Les applications qui prennent trop de temps à charger ou qui ne sont pas suffisamment réactives en utilisation régulière seront rejetées. Fournissez toujours des commentaires à l’utilisateur en cas de problème en arrière-plan, ou l’application semble se bloquer et, une fois encore, être rejetée.

### <a name="check-your-edge-cases"></a>Vérifier vos cas de bord

Une interruption courante pour les développeurs ne parvient pas à traiter les cas de périphérie, en particulier ceux qui nécessitent la reconfiguration du simulateur ou de l’appareil pour un test correct. Il peut être facile d’oublier que tous les clients ne sont pas autorisés à « autoriser » votre application à accéder à leur emplacement, car une fois que le développeur a accepté la demande une fois, il ne sera plus jamais invité à le faire. Les autorisations et l’utilisation du réseau sont spécifiquement axées sur au cours du processus d’approbation, ce qui signifie qu’un petit aperçu dans ces domaines peut entraîner un rejet.

La liste suivante est un bon point de départ pour vérifier les cas de périphérie qui peuvent avoir été manqués :

- Les **clients peuvent « refuser » l’accès aux services** , en particulier dans iOS, l’accès aux données, telles que les informations de géolocalisation, est fourni uniquement si l’utilisateur accorde l’autorisation à votre application. Les testeurs d’applications doivent souvent réinstaller l’application dans son état initial et interdire les demandes d’autorisation pour s’assurer que l’application se comporte correctement. Activez ou désactivez les autorisations pour vérifier le comportement correct, car les clients changent d’avis.
- Les **clients sont partout** : ne partez pas du principe qu’une application sera utilisée uniquement dans la ville ou le pays où elle a été développée ! Le code qui fonctionne avec les coordonnées GPS, les valeurs de date et d’heure et les devises peuvent tous être affectés par l’emplacement du client et les paramètres régionaux. Toutes les plateformes offrent un simulateur qui vous permet de spécifier des emplacements et des paramètres régionaux différents. Utilisez-le pour tester des emplacements dans d’autres hémisphères et avec des cultures qui mettent en forme différemment les dates et les devises. Les valeurs de latitude et de longitude peuvent être positives ou négatives, le séparateur décimal peut être un point ou une virgule, et les dates peuvent être mises en forme de nombreuses façons :
- **Connexions réseau lentes** : les développeurs d’applications travaillent souvent dans un « monde idéal » de la connectivité réseau rapide, qui fonctionne toujours, ce qui n’est évidemment pas le cas dans le monde réel. Les tests avec une connectivité réseau lente (par exemple, une connexion 3G médiocre) et sans accès réseau sont essentiels pour vous assurer que vous ne livrez pas une application de bogue. Le processus d’approbation inclura toujours un test avec l’appareil en mode avion, donc assurez-vous que vous l’avez testé pour vous-même.
- Le **matériel varie** : n’oubliez pas de tester le matériel le plus ancien et le plus lent que vous envisagez de prendre en charge. Il existe deux aspects susceptibles d’affecter votre application : les performances, qui peuvent être inutilisables sur un appareil plus ancien et la prise en charge des fonctionnalités matérielles telles qu’un appareil photo, un microphone, un GPS, un gyroscope ou tout autre composant facultatif. Les applications doivent se dégrader normalement (et ne pas se bloquer) quand un composant n’est pas disponible.

### <a name="guidelines-are-more-than-just-a-guide"></a>Les recommandations sont plus qu’un simple « guide »

Apple est célèbre pour être rigoureux quant à l’adhérence à ses directives en matière d’interface humaine, car l’un des principaux atouts de la plateforme est la cohérence (et l’augmentation perçue de la convivialité). Microsoft a adopté une approche similaire avec les applications Windows qui implémentent le [système de conception Fluent](https://microsoft.com/design/fluent). Le processus d’approbation pour les deux plateformes implique que votre application est évaluée pour son adhésion à la philosophie de conception appropriée.

Cela ne signifie pas que l’innovation de l’interface utilisateur n’est pas prise en charge ou encouragée, mais que vous n’êtes pas sûr de ne pas l’utiliser ou que votre application est rejetée.

Sur iOS, cela implique l’utilisation incorrecte d’icônes intégrées ou l’utilisation d’autres métaphores bien établies de manière non cohérente. par exemple, l’utilisation de l’icône « compose » pour tout autre chose que la création de contenu.

Les développeurs Windows doivent être de la même manière prudente ; une erreur courante ne peut pas prendre en charge correctement le bouton de retour matériel conformément aux instructions de Microsoft.

Encouragez vos concepteurs à lire et suivre les règles de conception pour chaque plateforme.

### <a name="implementing-platform-specific-features"></a>Implémentation de fonctionnalités spécifiques à la plateforme

Les choses sont un peu plus strictes lorsqu’il s’agit d’implémenter des services spécifiques à la plateforme, en particulier sur iOS. Pour éviter le rejet automatique par Apple, vous pouvez suivre certaines règles avec les fonctionnalités iOS suivantes :

- **Achats dans l’application** : les applications ne doivent pas implémenter des mécanismes de paiement externes pour les produits numériques, notamment la devise du jeu, les fonctionnalités de l’application, les abonnements au magazine et bien plus encore. les applications iOS doivent utiliser le service basé sur iTunes d’Apple pour ce genre de fonctionnalité. Il existe une faille : des applications telles que le lecteur Kindle et certaines applications basées sur un abonnement vous permettent d’acheter du contenu ailleurs qui est attaché à un « compte » auquel vous pouvez accéder via l’application. Toutefois, dans ce cas, l’application ne doit pas contenir de liens ou de références au processus d’achat hors de l’application (ou, une fois encore, il sera rejeté).
- **sauvegarde icloud** : avec l’avènement de iCloud, les réviseurs d’Apple sont bien plus stricts quant à la façon dont les applications utilisent le stockage (pour s’assurer que l’expérience de sauvegarde à distance du client est agréable). Les applications qui gaspillent de l’espace de stockage capable de sauvegarder peuvent être rejetées. Utilisez le dossier de cache de manière appropriée et suivez les autres instructions relatives au stockage d’Apple.
- **Newsstand** – les applications de journal et de magazine sont idéales pour les Newsstand d’Apple. Toutefois, les applications doivent implémenter au moins un abonnement de renouvellement automatique et prendre en charge le téléchargement en arrière-plan pour être approuvé.
- **Maps** : il est de plus en plus courant d’ajouter des superpositions et d’autres fonctionnalités aux cartes mobiles. Toutefois, veillez à ne pas masquer les informations de « crédits » de la carte (comme le logo Google dans IOS5), car cela entraînera un rejet.

### <a name="manage-your-metadata"></a>Gérer vos métadonnées

En plus des problèmes techniques évidents pouvant entraîner le rejet d’une application, il existe des aspects plus subtils de votre envoi qui peuvent entraîner un rejet, en particulier autour des métadonnées (description, Mots clés et images marketing) que vous envoyez avec votre application pour l’affichage dans l’App Store ou la place de marché.

- **Image** : suivez les instructions de la plateforme pour les icônes d’application et les images de magasin. N’utilisez pas d’images de marque déposée, nous avons vu que les applications ont été rejetées, car leurs icônes ont mis en vedette un iPhone !
- **Marques** : Évitez d’utiliser des marques autres que les vôtres. Les applications ont été refusées pour mentionner des marques dans la description de l’application, ou même dans les mots clés sur l’App Store d’Apple.
- **Description** : n’utilisez pas le mot’bêta’ou de quelque façon que ce soit pour indiquer que l’application n’est pas prête pour la première fois. Ne mentionnez pas d’autres plateformes mobiles (même si votre application est multiplateforme). Plus important encore, assurez-vous que l’application fait exactement ce que vous dites. Si vous répertoriez un grand nombre de fonctionnalités dans votre description, il s’agissait d’une meilleure façon d’utiliser chacune de ces fonctionnalités ou vous obtiendrez une « fonctionnalité mentionnée dans la description de l’application qui n’est pas implémentée ».

Placez le plus d’effort dans les métadonnées de l’application en tant que développement et test. Les applications sont rejetées pour des violations mineures dans les métadonnées. il est donc judicieux de prendre le temps de le faire.

### <a name="app-stores-not-for-everyone"></a>Magasins d’applications : pas pour tout le monde

L’objectif principal des magasins sur chaque plateforme est la distribution des consommateurs, c’est-à-dire la capacité à atteindre le plus grand nombre de clients possible. Toutefois, toutes les applications ne sont pas destinées aux consommateurs, mais il existe une base rapide d’applications internes et extranet qui nécessitent une distribution limitée aux employés, fournisseurs ou clients. Ces applications ne sont pas « à la vente » et n’ont pas besoin d’être approuvées, dans la mesure où le développeur contrôle la distribution à un groupe d’utilisateurs fermé.
La prise en charge de ce type de déploiement varie selon la plateforme.

Android offre la plus grande flexibilité en ce qui concerne : les applications peuvent être installées directement à partir d’une URL ou d’une pièce jointe de courrier électronique (tant que la configuration de l’appareil l’autorise). Cela signifie qu’il est facile de créer et de distribuer des applications d’entreprise internes ou de publier des applications sur des clients ou des fournisseurs spécifiques.

Apple offre une option de déploiement interne aux développeurs inscrits au programme iOS Developer Enterprise, qui contourne le processus d’approbation de l’App Store et permet aux entreprises de distribuer des applications internes à leurs employés.
Malheureusement, cette licence ne répond pas à la nécessité d’une distribution d’applications de type extranet à d’autres groupes de clients ou fournisseurs fermés. [Déploiement d’entreprise (et ad hoc)](~/ios/deploy-test/app-distribution/ipa-support.md)

### <a name="app-store-summary"></a>Résumé de l’App Store

Le processus de révision peut être décourageant, mais comme le reste du cycle de vie de développement, vous pouvez garantir la réussite de la planification et de l’attention à des détails. Tout cela se résume à quelques étapes simples : Lisez et comprenez les instructions de l’interface utilisateur auxquelles vous devez vous conformer, suivez les règles si vous implémentez des fonctionnalités spécifiques à la plateforme, testez minutieusement (puis testez-en d’autres) et vérifiez enfin que les métadonnées de votre application sont correctes avant de soumettre.

Un dernier mot d’avis destiné aux développeurs publiant sur Google Play : le manque de processus d’approbation peut paraître plus facile, mais vos clients seront encore plus exigeants qu’une équipe de révision. Suivez ces instructions comme si votre application pouvait être rejetée. dans le cas contraire, il s’agira de vos clients qui procéderont au rejet.