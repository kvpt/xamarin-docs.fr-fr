---
title: Utilisation de tvOS icônes et des Images dans Xamarin
description: Ce document décrit comment utiliser des icônes et des images dans une application tvOS générées avec Xamarin. Il aborde les images de lancement, les images en couche, l’icône d’application et bien plus encore.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: dab1b0f7bf7aabb4dfcfbfdcb5e202baa48e664d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865164"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Utilisation de tvOS icônes et des Images dans Xamarin

Création captivante icônes et images sont un élément essentiel du développement d’une expérience utilisateur immersives pour vos applications Apple TV. Ce guide décrit les étapes nécessaires pour créer et inclure les ressources graphiques nécessaires pour vos applications Xamarin.tvOS :

- [Image de lancement](#Launch-Image) -une image de lancement s’affiche lors du premier démarrage de votre application et est remplacée par le premier écran de l’application une fois que le lancement est terminée.
- [Couches d’Images](#Layered-Images) - spécifiques à l’Apple TV, nouvelles Images superposées travail d’Apple avec l’effet parallaxe pour créer un effet 3D pour les éléments sélectionnés. Il existe plusieurs façons de [créer des Images en couches](#Creating-Layered-Images).
- [Icône de l’application](#App-Icons) -les icônes sont obligatoires pour non seulement l’Apple TV accueil de l’écran, mais pour l’App Store. Elles doivent être fournies en tant qu’une Image en couches.
- [Image d’étagère de TOP](#Top-Shelf-Image) -si votre application est placée sur la ligne supérieure de l’écran d’accueil, il doit d’une Image d’étagère supérieure pour mettre en évidence les fonctionnalités de votre application. Si vous le souhaitez, vous pouvez fournir [contenu de la tablette haut dynamique](#Dynamic-Top-Shelf-Content) pour mettre en surbrillance le contenu de votre application.
- [Images de centre de jeux](#Game-Center-Images) -si votre application est un jeu et utilise Game Center, plusieurs images supplémentaires sera nécessaire.
- [Définir les Images de projet Xamarin.tvOS](#Setting-Xamarin.tvOS-Project-Images) -décrit les étapes nécessaires pour définir les images de lancement et l’icône de l’application pour votre application Xamarin.tvOS.

> [!IMPORTANT]
> Toutes les images sur Apple TV sont à la résolution de 1 x (`@1x`) et vous ne devez _uniquement_ utiliser des images de cette taille. Y compris les plus volumineux, graphiques de résolution plus élevée non seulement un temps télécharger et utiliser plus de mémoire et de stockage, mais ils doivent être remises dynamiquement à l’échelle lors de l’exécution et seront affecter négativement les performances de dessin.

<a name="Launch-Image" />

## <a name="launch-image"></a>Image de lancement

L’Image de lancement est la première chose qui s’affiche au démarrage de votre application Xamarin.tvOS initialement sur Apple TV, et par conséquent, chaque application tvOS doit fournir une Image de lancement. 

L’Image de lancement s’affiche rapidement et donne l’impression que votre application est rapide et réactif. L’Apple TV remplace l’Image de lancement par le premier écran de votre application peu de temps il après.

Images de lancement ne sont pas une opportunité pour les annonces ou expression artistique, elles existent uniquement pour donner l’impression que votre application lance rapidement et qu’il est prête à utiliser.

|Taille de l’Image de lancement|Notes|
|---|---|
|1920x1080px|Uniquement les fichiers .png de couches non|

Apple rend les suggestions suivantes pour la conception d’images de lancement de votre application :

- **Presque identique au premier écran** -votre écran de lancement doit être aussi proche premier écran de votre application en tant que possible. Y compris les graphiques différents ou élément peut entraîner un agaçante « flash » lorsque le premier écran s’affiche.
- **Éviter le texte à l’aide de** -Images de lancement sont statiques et ne sera par conséquent, ne pas être localisées avant d’être affichées.
- **Minimiser le lancement** -les utilisateurs car Apple TV changer fréquemment des applications, vous ne devez pas attirer l’attention sur le processus de lancement d’application.
- **Aucun publicités ou la personnalisation** -ne doit pas être utilisé comme un écran à propos de votre Image de lancement ou inclure toute personnalisation, sauf si elle fait partie statique du premier écran de votre application. ADS sont strictement interdite.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Définition de l’Image de lancement

Pour définir l’Image de lancement de votre projet de tvOS, effectuez le des actions suivantes :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur `Assets.xcassets` pour l’ouvrir pour modification : 

    [![](icons-images-images/asset01.png "Le fichier Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. Dans le **éditeur de ressources**, cliquez sur le `LaunchImages` actif : 

    [![](icons-images-images/asset02.png "L’élément multimédia LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Cliquez sur le **1 x Apple TV** entrée et sélectionnez l’Image de lancement ou éventuellement faire glisser une nouvelle image le système de fichiers : 

    [![](icons-images-images/asset03.png "Sélectionnez une Image de lancement")](icons-images-images/asset03.png#lightbox)
4. Enregistrez les modifications apportées.

<a name="Layered-Images" />

## <a name="layered-images"></a>Images superposées

Nouveau à l’Apple TV, travail Images en couche avec l’effet parallaxe pour produire un effet 3D qui permet de garder l’utilisateur sur le canapé mentalement connecté au contenu sur l’écran dans la salle.

Contiennent des Images en couches à partir de deux (2) à cinq (5) séparer les couches sont combinées pour former une image complète. À l’exception de la couche d’arrière-plan, chaque couche utilise son ordre de plan, ainsi que la transparence pour créer une illusion de profondeur. Quand l’utilisateur interagit avec une Image en couches, des couches supérieures ordonné Z sont mis à l’échelle et qui se chevauchent pour créer cet effet.

[![](icons-images-images/layered01.png "Diagramme d’Images Z ordonné en couches")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Images superposées sont requis pour les icônes de votre application et sont facultatifs pour d’autres [peut recevoir le focus des éléments](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (par exemple, l’Image d’étagère supérieure). Toutefois, les suggestions d’Apple à l’aide d’Images en couches pour toute image qui peut obtenir le focus dans votre application.




Apple rend les suggestions suivantes pour la conception de vos Images en couches :

- **Rendre le Opaque de couche d’arrière-plan** -votre couche d’arrière-plan (couche 1) **doit** être opaques ou vous obtiendrez une erreur lorsque vous essayez d’utiliser l’Image en couches sur Apple TV. Toutes les autres couches peuvent contenir plusieurs niveaux de transparence pour améliorer l’effet 3D.
- **Isoler de premier plan, au milieu et les éléments d’arrière-plan** -éléments importants (par exemple, le jeu de caractères) au premier plan, utilisez au milieu d’éléments secondaires ou des ombres. Enfin, inclure un arrière-plan neutre pour fournir une étape pour vos couches supérieures.
- **Conserver le texte au premier plan** -sauf si vous souhaitez être masqué par les niveaux supérieurs de votre texte, généralement il doit se trouver sur la couche de niveau supérieur.
- **Utiliser la superposition Simple** -l’effet parallaxe a été conçu pour être discret, gardez vos couches à un minimale afin d’éviter des effets souffrent, irréalistes.
- **Inclure une Zone Safe** -étant donné que les couches supérieures peuvent être rognées pendant un effet parallaxe, vous devez créer une bordure de Zone de Safe dans chaque couche. Si vous obtenez votre contenu trop fermer le bord de couches, il peut devenir rogné. Couches supérieures seront confrontés à plus de mise à l’échelle et de rognage que les couches inférieures. Consultez le [couches d’Image dimensionnement](#Sizing-Image-Layers) section ci-dessous.
- **Afficher un aperçu souvent** -Images superposées à prévisualiser souvent pour vous assurer que l’effet 3D souhaité se produit et qu’aucun du contenu sur les couches individuelles est rognage. Vous devez prévisualiser vos Images en couches sur du vrai matériel Apple TV pour vous assurer de que leur rendu comme prévu.

Si possible, vous devez toujours utiliser intégrés `UIKit` contrôles pour afficher vos Images en couche, car ils obtiennent automatiquement l’effet parallaxe lorsqu’ils sont dans le focus.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Couches d’Image de dimensionnement

Il est important de se rappeler à inclure un _Safe Zone_ bordure dans chaque couche qui constituera votre Image en couches. Étant donné que les couches individuelles peuvent être mis à l’échelle et rognées lors de l’effet parallaxe, le contenu des couches de peut être rogné s’il est trop près bord de la couche :

[![](icons-images-images/layered02.png "bordure de 35 pixels")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Création en couches des Images

tvOS fonctionne avec des Images en couches dans les formats suivants :

- **Fichiers de voiture** -il s’agit d’un format propriétaire de catalogue de ressources créé par Apple. Vous ne créez pas directement les fichiers de voiture, elles sont créées au moment de la compilation à partir de tous les fichiers LSR et inclus dans votre offre groupée d’applications.
- **Les Images LSR** -il s’agit d’un format d’image propriétaires créé par Apple. Utilisez le [parallaxe exportateur Adobe Photoshop plug-in](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) ou [Générateur d’aperçu parallaxe](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) pour créer et afficher un aperçu des Images en couches dans le format LSR.
- **Assets.xcassets** : à partir de deux (2) à cinq (5) standard `.png` mise en forme des images incluses dans un catalogue de ressources qui seront compilées dans une voiture ou une LSR au format Image en couches au moment de la compilation.
- **Fichiers LCR** -il s’agit d’un format de fichier propriétaire créé par Apple. Fichiers LCR sont destinées à être utilisées en tant que contenu supplémentaire téléchargé à partir d’un de vos serveurs de contenu. Fichier LCR n’a jamais doit être inclus dans votre offre groupée d’applications. LCR sont générés à partir de fichiers LSR ou Photoshop à l’aide du `layerutil` outil de ligne de commande inclus dans Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>Le Générateur d’aperçu parallaxe

Apple créé le [Générateur d’aperçu parallaxe](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) pour afficher un aperçu et les Images créées en couches requises pour les icônes d’application et peut recevoir le focus des éléments facultatifs. Le Générateur d’aperçu montre chaque couche qui constitue l’Image en couches terminée :

[![](icons-images-images/layered03.png "Le Générateur d’aperçu parallaxe")](icons-images-images/layered03.png#lightbox)

Lorsque vous prévisualisez une Image en couches, vous pouvez utiliser la souris pour faire pivoter l’image et d’afficher un aperçu de l’effet parallaxe. Utilisez le **+** (plus) et **-** (boutons pour ajouter et supprimer les couches moins).

Lorsque vous créez une nouvelle Image en couches, peuvent être exportée au format LSR et inclus dans l’offre groupée de votre application.

Pour plus d’informations sur la création et l’aperçu des Images en couches, consultez le site d’Apple [création d’une illustration de parallaxe](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) section de la [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Icônes d’application

Votre application Xamarin.tvOS requiert non seulement une icône d’application pour l’écran d’accueil de Apple TV, mais également une icône pour l’App Store. L’icône d’application est le votre premier modifier pour faire une impression excellente sur les utilisateurs potentiels et doit communiquer l’objectif de votre application en un clin de œil.

[![](icons-images-images/icon01.png "L’icône d’application")](icons-images-images/icon01.png#lightbox)

Chaque application doit fournir une petite et une grande version de son icône de l’application. La petite icône servira à l’écran d’accueil de Apple TV quand l’application est installée. La version grand est utilisée par l’App Store. Icône application de grande taille doit reproduire l’apparence de la version de la petite icône.

|Petite icône||Grande icône||
|---|---|---|---|
|Taille réelle|400x240px|Taille|1280x768px|
|Taille de la Zone de safe|370x222px|||
|Taille sans focus|300x180px|||
|Taille ayant le focus|370x222px|||

> [!IMPORTANT]
> Icônes de votre application doit être fournie comme **Images superposées**. Veuillez consulter la [Image en couches](#Layered-Images) section ci-dessus pour plus d’informations.




Apple propose les suggestions suivantes pour la création d’icônes d’application :

- **Fournir un Point de Focus unique** – conception votre icône avec un point unique de focus placé directement dans le centre de l’icône.
- **Utilisez un arrière-plan Simple** – simplifier votre arrière-plan de l’icône afin que les couches supérieures ressortent. Envisagez d’utiliser une couleur simple ou le dégradé subtil.
- **Limiter la quantité de texte** – dans la mesure où le nom de l’application apparaît sous l’icône lorsqu’il est sélectionné par l’utilisateur, vous devez inclure uniquement de texte lorsqu’il est essentiel de la conception de l’icône.
- **N’utilisez pas de captures d’écran** – captures d’écran sont trop complexes pour une icône et ne pas autoriser l’utilisateur d’afficher l’objectif de votre application en un clin de œil.
- **Keep icônes carré** – tvOS applique automatiquement un masque qui légèrement arrondit les coins de vos icônes. N’incluez pas cet arrondi vous-même.
- **Séparer les couches votre soigneusement** – texte doit être dans le coin supérieur, la plupart des couche, les éléments secondaires au milieu et un arrière-plan neutre qui permet à vos couches supérieures à briller.
- **Utiliser des dégradés et les ombres soigneusement** – des dégradés et les ombres peuvent entrer en conflit avec l’effet parallaxe donc doit être utilisées avec précaution. Simple haut-bas, styles de dégradé de clair à sombre fonctionnent le mieux. Shadows normalement mieux en tant que teintes sharp, bien marquées.
- **Varier la transparence de la couche** : utilisation de différents niveaux de transparence sur les niveaux supérieurs de l’icône de votre application pour augmenter l’effet 3D. La couche d’arrière-plan doit être opaque ou cela entraînerait une erreur.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Définir les icônes d’application

Pour définir les icônes d’application requis pour votre projet de tvOS, effectuez le des actions suivantes :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur `Assets.xcassets` pour l’ouvrir pour modification : 

    [![](icons-images-images/asset01.png "Le fileg Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. Dans le **éditeur de ressources**, développez le `App Icon & Top Shelf Image` actif : 

    [![](icons-images-images/asset04.png "Développez la ressource d’Image d’étagère supérieure")](icons-images-images/asset04.png#lightbox)
3. Ensuite, développez le `App Icon - Small` actif : 

    [![](icons-images-images/asset05.png "Développez l’icône d’application - petite asset")](icons-images-images/asset05.png#lightbox)
4. Puis développez le `Back` actif, puis cliquez sur le `Contents` entrée : 

    [![](icons-images-images/asset06.png "Puis développez l’élément multimédia précédent")](icons-images-images/asset06.png#lightbox)
5. Cliquez sur le **1 x entrée de l’Apple TV** et sélectionnez un fichier image.
6. Répétez les étapes ci-dessus pour le `Front` et `Middle` actifs.
7. Puis répétez ces étapes pour définir le `App Icon - Large` actif.
8. Enregistrez les modifications apportées.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Image d’étagère supérieure

Si l’utilisateur a placé votre application Xamarin.tvOS sur la ligne supérieure sur l’écran d’accueil de Apple TV, une Image d’étagère supérieure volumineux s’affichera lorsque votre application est sélectionnée par l’utilisateur. Cette image doit mettre en évidence les fonctionnalités de votre application ou fournissent des liens directs vers son contenu.

[![](icons-images-images/topshelf01.png "Exemple d’Image d’étagère supérieure")](icons-images-images/topshelf01.png#lightbox)

L’Image d’étagère supérieure peut être fourni en tant que statique unique `.png` ou `.lsr` fichier (voir [création d’Images en couches](#Creating-Layered-Images)) ou il peut être créé dynamiquement lors de l’exécution en tant qu’une seule ligne d’éléments pouvant être actif (consultez [ Contenu de l’étagère supérieure dynamique](#Dynamic-Top-Shelf-Content) ci-dessous).

|Taille de l’Image étagère supérieure|Notes|
|---|---|
|1920x720px|.Png statique ou un fichier de .lsr en couches|

Apple propose les suggestions suivantes pour la création de vos Images de rayon en haut :

- **Utiliser des images statiques riche** – si votre application ne fournit pas un contenu dynamique, son Image d’étagère supérieure sera non peut recevoir le focus. Utiliser cette image pour mettre en évidence les fonctionnalités de l’application ou votre marque.
- **Lien vers le contenu de l’application** – dynamique haut étagère dotent un lien rapide vers le contenu que votre utilisateur trouve plus importante dans votre application. Utilisez cette zone pour fournir un lien rapide pour démarrer votre application et allez directement dans le contenu donné.
- **Présenter le contenu le plus récent** – contenu de riches haut étagère peut dessiner un utilisateur dans votre application et les rendre plus son utilisation. Utilisez-la en tant qu’une zone pour présenter le contenu du nombre le plus élevé nominal ou plus récente.
- **Contenu personnalisé** – place les utilisateurs les plus fréquemment utilisées ou applications favorites dans la ligne supérieure de l’écran d’accueil. Utiliser le rayon en haut pour afficher le contenu, qu'ils seraient plus intéressés.
- **Annonces interdit** – publicités sont strictement interdite s’affiche dans le rayon en haut. Vous pouvez afficher le contenu le plus récent pouvant être achetée, mais aucune information tarifaire ne doit être affichée.

### <a name="setting-the-top-shelf-image"></a>Définition de l’Image d’étagère supérieure

Pour définir l’Image d’étagère supérieure requis pour votre projet de tvOS, effectuez le des actions suivantes :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur `Assets.xcassets` pour l’ouvrir pour modification : 

    [![](icons-images-images/asset01.png "Le fichier Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. Dans le **éditeur de ressources**, développez le `App Icon & Top Shelf Image` actif : 

    [![](icons-images-images/asset04.png "Développez la ressource d’Image d’étagère supérieure")](icons-images-images/asset04.png#lightbox)
3. Cliquez sur le `Top Shelf Image` actif : 

    [![](icons-images-images/asset07.png "La ressource d’Image d’étagère supérieure")](icons-images-images/asset07.png#lightbox)
4. Cliquez sur le **1 x entrée de l’Apple TV** et sélectionnez un fichier image.
5. Enregistrez les modifications apportées.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Contenu de l’étagère supérieure dynamique

Au lieu d’afficher une Image d’étagère supérieure statique, l’emploi de haut peut contenir une ligne de dynamique [peut recevoir le focus des éléments](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) ou un ensemble dynamique de bannières de défilement. Deux ces style dynamique vous permettent de mettre en surbrillance le contenu fourni par votre application ou d’un saut dans ses fonctionnalités utilisées le plus.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Ligne de contenu sectionnée

Ce type de contenu haut étagère dynamique présente une seule ligne de défilement, d’éléments pouvant être actif éventuellement divisée en sections. Il sert généralement à nouveau, mettez en surbrillance favori ou que vous avez récemment consulté le contenu de l’application.

Contenu est présenté comme une liste déroulante unique, horizontale du contenu avec une étiquette qui apparaissent sous l’élément actuel de contenu sélectionné (qui possède actuellement le focus). Si l’utilisateur sélectionne un élément de contenu donné, votre application doit être lancée, et il doivent être dirigé directement dans ce contenu.

Les tailles de contenu suivantes seront nécessaires :

||Poster (2:3)|Carré (1:1)|HDTV (16:9)|
|---|---|---|---|
|Taille réelle|404x608px|608x608px|908x512px|
|Taille de la Zone de safe|380x570px|570x570px|852x479px|
|Taille sans focus|333x500px|500x500px|782x440px|
|Taille ayant le focus|380x570px|570x570px|852x479px|

Apple propose les suggestions suivantes pour la ligne Sectioned contenu :

- **Terminer la ligne** – vous devez fournir assez de contenu pour couvrir toute la largeur de l’écran.
- **Mise à l’échelle des Images mixte** – ligne Sectioned le contenu a été conçu pour contenir une combinaison des tailles des images (dans la liste ci-dessus). Si vous mélangez toutefois les tailles d’image, n’oubliez pas que la mise à l’échelle supplémentaires s’appliqueront pour normaliser l’affichage de contenu.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Le défilement incrustation bannières

Si vous le souhaitez, votre application Xamarin.tvOS peut présenter son contenu dans l’emploi de haut en tant qu’automatiquement le défilement et bouclage de collection de bannières qui presque plein écran. Ce style est généralement utilisé pour présenter un nouveau contenu comme nouvelles émissions TV.

En plus de la barre de défilement automatique, l’utilisateur peut prendre le contrôle des bannières et faites défiler dans les deux sens à l’aide de l’instance distante de Siri. Rendre un petit, mouvement circulaire Siri à un emplacement distant quand une bannière est en focus activera l’effet parallaxe pour cette bannière.

**Image de bannière (très large)**

|   |   |
|---|---|
|Taille réelle|1940x624px|
|Taille de la Zone de safe|1740x620px|
|Taille sans focus|1740x560px|
|Taille ayant le focus|1740x620px|

Le défilement incrustation bannières peut soit être fourni sous forme de statique `.png` ou en couches `.lsr` fichier.

Apple propose les suggestions suivantes pour les bannières d’incrustation de défilement :

- **Quantité de contenu** -vous devez fournir un minimum de trois (3) les bannières pour le défilement naturelles. Vous devez inclure des bannières pas plus de huit (8) ou il rendre la navigation difficile pour l’utilisateur final.
- **Contenu texte** : Si votre bannière nécessite le texte dans doit être inclus dans l’image de bannière. Si vous utilisez des Images en couches, votre texte doit être dans la couche de niveau supérieur.

Consultez d’Apple [TVServices Framework référence](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) pour plus d’informations sur l’ajout d’une Extension de rayon en haut à votre application pour fournir un contenu dynamique étagère de haut.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Images de Game Center

Si votre application Xamarin.tvOS est un jeu et que vous avez inclus la prise en charge de Game Center, plusieurs ressources d’image plus seront nécessaires :

- **Icônes de réalisation** -une image opaque est nécessaire pour chaque prime est détourée automatiquement dans un cercle. Primes sont des éléments non peut recevoir le focus.
- **Illustration du tableau de bord** -image facultative peut être fournie qui s’affichent en haut du tableau de bord de votre application au sein de Game Center. Ces images sont non peut recevoir le focus.
- **Illustration de classement** -vous devez fournir entre un (1) à trois (3) les images de proportions 16:9 pour chaque classement qui prend en charge de votre application. Il peut s’agir soit statique `.png` ou en couches `.lsr` fichiers. L’illustration de classement ne peut être actif.

||Icônes de réalisation|Illustration du tableau de bord|Illustration de classement|
|---|---|---|---|
|Taille visible|200x200px|923x150px|N/A|
|Taille réelle|320x320px|N/A|659x371px|
|Taille de la Zone de safe|N/A|N/A|618x348px|
|Taille sans focus|N/A|N/A|548x309px|
|Taille ayant le focus|N/A|N/A|618x348px|

Pour plus d’informations sur l’utilisation de Game Center, consultez le site d’Apple [Guide de programmation Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html).

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Utilisation d’images

Étant donné que tvOS 9 est un sous-ensemble d’iOS 9, les mêmes techniques utilisées pour inclure et afficher des images dans une application Xamarin.iOS, fonctionnent également pour une application Xamarin.tvOS. Consultez notre [affichage d’une Image](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documentation pour plus d’informations.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Définir les Images de projet de Xamarin.tvOS

Comme indiqué ci-dessus, toutes les applications tvOS nécessitent un [images de lancement](#Launch-Image), et [icône de l’application](#App-Icons). Cette section aborde en sélectionnant les images de lancement et l’icône de l’application pour votre projet d’application Xamarin.tvOS une fois qu’ils ont été définis dans un catalogue de ressources.

Effectuez ce qui suit :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur le `Info.plist` à ouvrir pour modification : 

    [![](icons-images-images/info01.png "Le fichier Info.plist")](icons-images-images/info01.png#lightbox)
2. Dans le **éditeur Info.Plist**, sélectionnez le catalogue de ressources (configuré ci-dessus dans le [définissant les icônes d’application](#Setting-the-App-Icons) section) pour le **icônes d’application**: 

    [![](icons-images-images/info02.png "L’éditeur Info.Plist")](icons-images-images/info02.png#lightbox)
3. Ensuite, sélectionnez le catalogue de ressources (configuré ci-dessus dans le [définition de l’Image de lancement](#Setting-the-Launch-Image) section) pour le **Images de lancement**.
4. Enregistrez les modifications apportées.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a couvert tous les types d’images et tailles utilisés dans une application Xamarin.tvOS. Tout d’abord, elle couvrait les Images de lancement, Images superposées, icônes d’application, les Images de rayon en haut et Game Center Images. Ensuite, elle couvrait utilisation des images dans votre application Xamarin.tvOS.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
