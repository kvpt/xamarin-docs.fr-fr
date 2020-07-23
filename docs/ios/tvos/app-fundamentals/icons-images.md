---
title: Utilisation des icônes et des images tvOS dans Xamarin
description: Ce document décrit comment utiliser des icônes et des images dans une application tvOS générée avec Xamarin. Il aborde les images de lancement, les images en couches, l’icône d’application, et bien plus encore.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c0f016898a18ca38b04d752f2ae4f26e583bb7f2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937408"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Utilisation des icônes et des images tvOS dans Xamarin

La création d’icônes et d’images captivistes est un élément essentiel du développement d’une expérience utilisateur immersive pour vos applications Apple TV. Ce guide aborde les étapes nécessaires à la création et à l’inclusion des ressources graphiques nécessaires à vos applications Xamarin. tvOS :

- [Image de lancement](#Launch-Image) : une image de lancement s’affiche lorsque votre application est démarrée pour la première fois et est remplacée par le premier écran de l’application une fois le lancement terminé.
- [Images superposées](#Layered-Images) -spécifiques au téléviseur Apple, les nouvelles images superposées d’Apple fonctionnent avec l’effet de parallaxe pour créer un effet 3D pour les éléments sélectionnés. Il existe plusieurs façons de [créer des images en couches](#Creating-Layered-Images).
- [Icône d’application](#App-Icons) : les icônes sont requises pour non seulement l’écran d’accueil Apple TV mais pour l’App Store. Ils doivent être fournis sous la forme d’une image en couches.
- [Image d’étagère supérieure](#Top-Shelf-Image) : Si votre application est placée sur la ligne supérieure de l’écran d’accueil, elle aura besoin d’une image de premier plan pour mettre en évidence les fonctionnalités de votre application. Si vous le souhaitez, vous pouvez fournir un [contenu dynamique supérieur](#Dynamic-Top-Shelf-Content) pour mettre en surbrillance le contenu de votre application.
- [Images Game Center](#Game-Center-Images) : Si votre application est un jeu et utilise Game Center, plusieurs images supplémentaires seront nécessaires.
- [Définition des images de projet Xamarin. tvOS](#Setting-Xamarin.tvOS-Project-Images) : décrit les étapes nécessaires à la définition de l’icône de lancement de l’image et de l’application pour votre application Xamarin. tvOS.

> [!IMPORTANT]
> Toutes les images sur la TV Apple sont à la résolution 1x ( `@1x` ) et vous ne devez utiliser _que_ des images de cette taille. L’inclusion de graphiques plus grands et de plus haute résolution prend le temps de télécharger et d’utiliser davantage de mémoire et de stockage, mais ils doivent être mis à l’échelle dynamiquement au moment de l’exécution et ont une incidence négative sur les performances de dessin.

<a name="Launch-Image"></a>

## <a name="launch-image"></a>Image de lancement

L’image de lancement est la première chose qui s’affiche lorsque votre application Xamarin. tvOS est initialement démarrée sur la TV Apple et, par conséquent, chaque application tvOS doit fournir une image de lancement. 

L’image de lancement s’affiche rapidement et donne l’impression que votre application est rapide et réactive. Le téléviseur Apple remplace l’image de lancement par le premier écran de votre application.

Les images de lancement ne sont pas une opportunité pour les publicités ou les expressions artistiques, elles existent uniquement pour apporter l’impression que votre application lance rapidement et est prête à l’emploi.

|Taille de l’image de lancement|Notes|
|---|---|
|1920x1080px|Fichiers. png non superposés uniquement|

Apple effectue les suggestions suivantes pour la conception de l’image de lancement de votre application :

- **Presque identique au premier écran** : votre écran de lancement doit être aussi proche que possible du premier écran de votre application. L’inclusion de différents graphiques ou éléments peut entraîner un « flash » ennuyeux lorsque le premier écran s’affiche.
- **Évitez d’utiliser** des images de lancement de texte statiques et, par conséquent, elles ne seront pas localisées avant d’être affichées.
- **Lancement de minimiser** : étant donné que les utilisateurs d’Apple TV changent fréquemment d’applications, vous ne devez pas attirer l’attention sur le processus de lancement de l’application.
- **Aucune publicité ou personnalisation** : votre image de lancement ne doit pas être utilisée en tant qu’écran à propos de ou inclure une personnalisation, sauf si elle est statique dans le premier écran de votre application. Les publicités sont strictement interdites.

<a name="Setting-the-Launch-Image"></a>

### <a name="setting-the-launch-image"></a>Définition de l’image de lancement

Pour définir l’image de lancement de votre projet tvOS, procédez comme suit :

1. Dans le **Explorateur de solutions**, double-cliquez dessus pour l' `Assets.xcassets` ouvrir et le modifier : 

    [![Fichier Assets. xcassets](icons-images-images/asset01.png)](icons-images-images/asset01.png#lightbox)
2. Dans l' **éditeur de ressources**, cliquez sur l' `LaunchImages` élément multimédia : 

    [![Ressource LaunchImages](icons-images-images/asset02.png)](icons-images-images/asset02.png#lightbox)
3. Cliquez sur l’entrée **1x Apple TV** , sélectionnez l’image de lancement ou faites éventuellement glisser une nouvelle image à partir du système de fichiers : 

    [![Sélectionner une image de lancement](icons-images-images/asset03.png)](icons-images-images/asset03.png#lightbox)
4. Enregistrez vos modifications.

<a name="Layered-Images"></a>

## <a name="layered-images"></a>Images superposées

Nouveauté du téléviseur Apple, les images superposées fonctionnent avec l’effet de parallaxe pour produire un effet 3D qui permet de maintenir l’utilisateur sur la canapé mentalement connecté au contenu à l’écran dans toute la pièce.

Les images en couche contiennent de deux (2) à cinq (5) couches distinctes qui sont combinées pour former une image complète. À l’exception de la couche d’arrière-plan, chaque couche utilise son ordre de plan et la transparence pour créer une illusion de profondeur. Lorsque l’utilisateur interagit avec une image en couche, les couches triées plus élevées sont mises à l’échelle et se chevauchent pour créer cet effet.

[![Images superposées diagramme Z-ordonné](icons-images-images/layered01.png)](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Les images en couches sont requises pour les icônes de votre application et sont facultatives pour d’autres éléments pouvant être mis en [focus](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (tels que l’image de l’étagère supérieure). Toutefois, Apple suggère d’utiliser des images en couche pour toutes les images pouvant être ciblées dans votre application.

Apple effectue les suggestions suivantes pour la conception de vos images en couche :

- **Rendre la couche d’arrière-plan opaque** -votre couche d’arrière-plan (couche 1) **doit** être opaque ou vous obtenez une erreur lorsque vous essayez d’utiliser l’image en couche sur Apple TV. Toutes les autres couches peuvent contenir plusieurs niveaux de transparence pour améliorer l’effet 3D.
- **Isoler les éléments de premier plan, de milieu et d’arrière-plan** : Placez les éléments les plus importants (tels que les caractères de jeu) au premier plan, à l’aide du milieu des éléments secondaires ou des ombres. Enfin, incluez un arrière-plan neutre pour fournir une étape pour vos couches supérieures.
- **Conserver le texte au premier plan** -à moins que vous ne souhaitiez que votre texte soit masqué par des niveaux supérieurs, en général il doit se trouver sur la couche la plus haute.
- **Utiliser la structuration en couches simples** : l’effet de parallaxe a été conçu de manière subtile, ce qui permet de réduire au minimum vos couches afin d’éviter les effets transférerezs, non réalistes.
- **Inclure une zone sécurisée** : étant donné que les couches supérieures peuvent être rognées pendant un effet de parallaxe, vous devez créer une bordure de zone sécurisée dans chaque couche. Si vous faites en sorte que votre contenu soit trop près du bord des couches, il peut devenir rogné. Les couches supérieures subissent davantage de mise à l’échelle et de rognage que les couches inférieures. Consultez la section [dimensionnement des couches d’images](#Sizing-Image-Layers) ci-dessous.
- Aperçu les images à couches **fréquentes** doivent souvent être prévisualisées pour s’assurer que l’effet 3D souhaité se produit et qu’aucun contenu sur les couches individuelles n’est rogné. Vous devez afficher un aperçu de vos images en couche sur du matériel Apple TV réel pour vous assurer qu’elles s’affichent comme prévu.

Chaque fois que cela est possible, vous devez toujours utiliser les `UIKit` contrôles intégrés pour afficher vos images superposées, car elles obtiennent automatiquement l’effet de parallaxe lorsqu’elles entrent en focus.

<a name="Sizing-Image-Layers"></a>

### <a name="sizing-image-layers"></a>Dimensionnement des couches d’images

Il est important de se souvenir d’inclure une bordure de _zone sécurisée_ dans chaque couche qui composera votre image en couche. Étant donné que les couches individuelles peuvent être mises à l’échelle et rognées pendant l’effet de parallaxe, le contenu des couches peut être rogné s’il est trop proche de l’arête de la couche :

[![bordure de 35 pixels](icons-images-images/layered02.png)](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images"></a>

### <a name="creating-layered-images"></a>Création d’images en couches

tvOS fonctionne avec les images en couches dans les formats suivants :

- **Fichiers de voiture** : il s’agit d’un format de catalogue de ressources propriétaire créé par Apple. Vous ne créez pas de fichiers de voiture directement, ils sont créés au moment de la compilation à partir de tous les fichiers LSR et inclus dans votre offre groupée d’applications.
- **Images LSR** -il s’agit d’un format d’image propriétaire créé par Apple. Utilisez le [plug-in Adobe Photoshop de l’exportateur parallaxe](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) ou le générateur d’aperçu de [parallaxe](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) pour créer et afficher un aperçu des images en couche au format LSR.
- **Assets. xcassets** : de deux (2) à cinq (5) `.png` images formatées standard incluses dans un catalogue de composants qui seront compilées dans une image en couche de voitures ou de LSR au moment de la compilation.
- **Fichiers LCR** : il s’agit d’un format de fichier propriétaire créé par Apple. Les fichiers LCR sont destinés à être utilisés en tant que contenu supplémentaire téléchargé à partir de l’un de vos serveurs de contenu. Le fichier LCR ne doit jamais être inclus dans votre offre groupée d’applications. Les fichiers LCR sont générés à partir de fichiers LSR ou Photoshop à l’aide de l' `layerutil` outil en ligne de commande inclus dans Xcode.

<a name="The-Parallax-Previewer"></a>

### <a name="the-parallax-previewer"></a>Générateur d’aperçu de parallaxe

Apple a créé le générateur d’aperçu de [parallaxe](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) pour prévisualiser et créer des images en couches requises pour les icônes d’application et les éléments de focus facultatifs. Le générateur d’aperçu affiche chaque couche qui forme l’image en couches terminée :

[![Générateur d’aperçu de parallaxe](icons-images-images/layered03.png)](icons-images-images/layered03.png#lightbox)

En prévisualisant une image en couches, vous pouvez utiliser la souris pour faire pivoter l’image et afficher un aperçu de l’effet parallaxe. Utilisez les **+** boutons (plus) et **-** (moins) pour ajouter et supprimer des couches.

Lors de la création d’une nouvelle image en couche, elle peut être exportée au format LSR et incluse dans le bundle de votre application.

Pour plus d’informations sur la création et l’affichage de l’aperçu des images en couche, consultez la section [création d’illustrations de parallaxe](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) dans le Guide de programmation d' [applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons"></a>

## <a name="app-icons"></a>Icônes d’application

Votre application Xamarin. tvOS nécessite non seulement une icône d’application pour l’écran d’accueil Apple TV, mais également une icône pour l’App Store. L’icône de l’application est la première modification à apporter à votre utilisateur potentiel et doit communiquer d’un coup d’œil à l’objectif de votre application.

[![Icône d’application](icons-images-images/icon01.png)](icons-images-images/icon01.png#lightbox)

Chaque application doit fournir une petite et une grande version de son icône d’application. La petite icône sera utilisée sur l’écran d’accueil Apple TV lorsque l’application est installée. La version volumineuse est utilisée par l’App Store. L’icône d’application de grande taille doit imiter l’apparence de la petite version de l’icône.

|Petite icône||Grande icône||
|---|---|---|---|
|Taille réelle|400x240px|Taille|1280x768px|
|Taille de zone sécurisée|370x222px|||
|Taille inactive|300x180px|||
|Taille focalisée|370x222px|||

> [!IMPORTANT]
> Les icônes de votre application doivent être fournies en tant qu' **images en couches**. Pour plus d’informations, consultez la section [image en couche](#Layered-Images) ci-dessus.

Apple fournit les suggestions suivantes pour créer les icônes de votre application :

- **Fournir un point de focalisation unique** : concevez votre icône avec un point de focalisation unique placé directement au centre de l’icône.
- **Utilisez un arrière-plan simple** : laissez votre icône en arrière-plan simple afin que les couches supérieures ressortent. Envisagez d’utiliser une couleur simple ou un dégradé subtil.
- **Limiter la quantité de texte** : étant donné que le nom de l’application apparaît sous l’icône lorsqu’elle est sélectionnée par l’utilisateur, vous devez inclure du texte uniquement lorsqu’il est essentiel à la conception de l’icône.
- **N’utilisez pas les captures d’écran** : les captures d’écran sont trop complexes pour une icône et ne permettent pas à l’utilisateur de voir l’objectif de votre application en un clin d’œil.
- **Conserver les icônes carrées** : tvOS applique automatiquement un masque qui arrondit légèrement les angles de vos icônes. N’incluez pas cet arrondi vous-même.
- **Séparer vos couches avec précaution** : le texte doit se trouver sur la couche la plus haute, sur les éléments secondaires au milieu et sur un arrière-plan neutre qui permet à vos couches supérieures de briller.
- **Utilisez les dégradés et les ombres avec précaution** : les dégradés et les ombres peuvent entrer en conflit avec l’effet parallaxe, afin de pouvoir les utiliser avec précaution. Les styles de dégradé de haut en bas et les plus simples fonctionnent mieux. Normalement, les ombres fonctionnent mieux comme des teintes nettes.
- **Faire varier la transparence** de la couche : utilisez des niveaux de transparence différents sur les niveaux supérieurs de l’icône de votre application pour augmenter l’effet 3D. La couche d’arrière-plan doit être opaque ou une erreur se produit.

<a name="Setting-the-App-Icons"></a>

### <a name="setting-the-app-icons"></a>Définition des icônes d’application

Pour définir les icônes d’application requises pour votre projet tvOS, procédez comme suit :

1. Dans le **Explorateur de solutions**, double-cliquez dessus pour l' `Assets.xcassets` ouvrir et le modifier : 

    [![Ressources. xcassets fileg](icons-images-images/asset01.png)](icons-images-images/asset01.png#lightbox)
2. Dans l' **éditeur de ressources**, développez l' `App Icon & Top Shelf Image` élément multimédia : 

    [![Développer la ressource d’image de rayon supérieure](icons-images-images/asset04.png)](icons-images-images/asset04.png#lightbox)
3. Ensuite, développez l' `App Icon - Small` élément multimédia : 

    [![Développer l’icône d’application-petite ressource](icons-images-images/asset05.png)](icons-images-images/asset05.png#lightbox)
4. Développez ensuite l' `Back` élément multimédia, puis cliquez sur l' `Contents` entrée suivante : 

    [![Puis développez la ressource back.](icons-images-images/asset06.png)](icons-images-images/asset06.png#lightbox)
5. Cliquez sur l' **entrée 1x Apple TV** et sélectionnez un fichier image.
6. Répétez les étapes ci-dessus pour les `Front` `Middle` ressources et.
7. Répétez ensuite les mêmes étapes pour définir la `App Icon - Large` ressource.
8. Enregistrez vos modifications.

<a name="Top-Shelf-Image"></a>

## <a name="top-shelf-image"></a>Image d’étagère supérieure

Si l’utilisateur a placé votre application Xamarin. tvOS sur la ligne supérieure de l’écran d’accueil Apple TV, une grande image de haut rayon s’affiche lorsque votre application est sélectionnée par l’utilisateur. Cette image doit mettre en surbrillance les fonctionnalités de votre application ou fournir des liens directs vers son contenu.

[![Exemple d’image de étagère supérieure](icons-images-images/topshelf01.png)](icons-images-images/topshelf01.png#lightbox)

L’image de l’étagère supérieure peut être fournie sous la forme d’un `.png` fichier statique ou `.lsr` d’un fichier unique (consultez [création d’images superposées](#Creating-Layered-Images)) ou elle peut être créée dynamiquement au moment de l’exécution en tant qu’une seule ligne d’éléments pouvant être mis sur le focus (consultez [contenu de haut](#Dynamic-Top-Shelf-Content) de page dynamique ci-dessous).

|Taille de l’image de l’étagère supérieure|Notes|
|---|---|
|1920x720px|Fichier static. png ou Layered. LSR|

Apple fournit les suggestions suivantes pour créer vos images de haut rayon :

- **Utiliser une image statique riche** : Si votre application ne fournit pas de contenu dynamique, son image de premier plan ne peut pas être focalisée. Utilisez cette image pour mettre en évidence les fonctionnalités de l’application ou de la vôtre.
- **Lien vers le contenu de l’application** : les dispositions dynamiques de l’étagère supérieure fournissent un lien rapide vers le contenu que votre utilisateur trouve le plus important dans votre application. Utilisez cette zone pour fournir un lien rapide permettant de démarrer votre application et d’accéder immédiatement au contenu donné.
- Présentez **le contenu le plus récent : le contenu du** haut de gamme riche peut être utilisé pour créer un utilisateur dans votre application. Utilisez-le comme zone pour présenter le contenu le plus élevé ou le plus récent.
- **Contenu personnalisé** : les utilisateurs placent leurs applications les plus fréquemment utilisées ou préférées dans la ligne supérieure de l’écran d’accueil. Utilisez l’étagère supérieure pour afficher le contenu qui s’intéresse le plus.
- **Annonces non autorisées** : il est strictement interdit d’afficher les annonces dans le rack supérieur. Vous pouvez afficher le contenu le plus récent, mais aucune information de tarification ne doit être affichée.

### <a name="setting-the-top-shelf-image"></a>Définition de l’image de l’étagère supérieure

Pour définir l’image de l’étagère supérieure requise pour votre projet tvOS, procédez comme suit :

1. Dans le **Explorateur de solutions**, double-cliquez dessus pour l' `Assets.xcassets` ouvrir et le modifier : 

    [![Fichier Assets. xcassets](icons-images-images/asset01.png)](icons-images-images/asset01.png#lightbox)
2. Dans l' **éditeur de ressources**, développez l' `App Icon & Top Shelf Image` élément multimédia : 

    [![Développer la ressource d’image de rayon supérieure](icons-images-images/asset04.png)](icons-images-images/asset04.png#lightbox)
3. Cliquez sur l' `Top Shelf Image` élément multimédia : 

    [![La ressource d’image de rayon supérieure](icons-images-images/asset07.png)](icons-images-images/asset07.png#lightbox)
4. Cliquez sur l' **entrée 1x Apple TV** et sélectionnez un fichier image.
5. Enregistrez vos modifications.

<a name="Dynamic-Top-Shelf-Content"></a>

### <a name="dynamic-top-shelf-content"></a>Contenu dynamique du haut

Au lieu d’afficher une image de rayon supérieure statique, l’étagère supérieure peut contenir une ligne dynamique d’éléments pouvant faire l' [objet du focus](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) ou un ensemble dynamique de bannières de défilement. Ces deux styles dynamiques vous permettent de mettre en surbrillance le contenu fourni par votre application ou d’accéder à ses fonctionnalités les plus utilisées.

<a name="Sectioned-Content-Row"></a>

#### <a name="sectioned-content-row"></a>Ligne de contenu avec section

Ce type de contenu de rayon supérieur dynamique présente une seule ligne de défilement, les éléments pouvant être activés, éventuellement répartis en sections. Elle est généralement utilisée pour mettre en surbrillance le contenu de l’application nouveau, favori ou récemment consulté.

Le contenu est présenté sous la forme d’une liste déroulante unique et horizontale de contenu avec une étiquette apparaissant sous la partie de contenu actuelle sélectionnée (qui a actuellement le focus). Si l’utilisateur sélectionne une partie de contenu donnée, votre application est lancée et doit être prise directement dans ce contenu.

Les tailles de contenu suivantes sont requises :

||Affiche (2:3)|Carré (1:1)|HDTV (16:9)|
|---|---|---|---|
|Taille réelle|404x608px|608x608px|908x512px|
|Taille de zone sécurisée|380x570px|570x570px|852x479px|
|Taille inactive|333x500px|500x500px|782x440px|
|Taille focalisée|380x570px|570x570px|852x479px|

Apple fournit les suggestions suivantes pour la ligne de contenu avec section :

- **Complétez la ligne** : vous devez fournir un contenu suffisant pour couvrir toute la largeur de l’écran.
- **Mise à l’échelle d’images mixtes** : la ligne de contenu avec section a été conçue pour contenir une combinaison de tailles d’image (à partir de la liste fournie ci-dessus). Toutefois, si vous combinez des tailles d’image, sachez que la mise à l’échelle supplémentaire sera appliquée pour normaliser l’affichage du contenu.

<a name="Scrolling-Inset-Banners"></a>

#### <a name="scrolling-inset-banners"></a>Défilement des bannières d’incrustation

Si vous le souhaitez, votre application Xamarin. tvOS peut présenter son contenu dans le rack supérieur en tant que collection automatique de bannières et de bannières qui remplissent presque l’écran. Ce style est généralement utilisé pour présenter du contenu riche et nouveau, comme les nouveaux diaporamas TV.

En plus du défilement automatique, l’utilisateur peut prendre le contrôle des bannières et faire défiler dans les deux sens à l’aide de la Siri distante. Si vous effectuez un petit mouvement circulaire sur le Siri à distance lorsqu’une bannière est activée, l’effet de parallaxe est activé pour cette bannière.

**Image de bannière (très grande largeur)**

|   |   |
|---|---|
|Taille réelle|1940x624px|
|Taille de zone sécurisée|1740x620px|
|Taille inactive|1740x560px|
|Taille focalisée|1740x620px|

Le défilement des bannières d’incrustation peut être fourni en tant que `.png` fichier statique ou en couches `.lsr` .

Apple fournit les suggestions suivantes pour les bannières d’incrustation de défilement :

- **Montant du contenu** : vous devez fournir au moins trois (3) bannières pour que le défilement soit naturel. Vous ne devez pas inclure plus de huit (8) bannières ou cela rend difficile la navigation pour l’utilisateur final.
- **Texte de contenu** : Si votre bannière requiert du texte dans, elle doit être incluse dans l’image de bannière. Si vous utilisez des images en couches, votre texte doit être dans la couche la plus haut.

Pour plus d’informations sur l’ajout d’une extension d’étagère supérieure à votre application, consultez le Guide de référence de l' [infrastructure TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) pour fournir un contenu dynamique supérieur.

<a name="Game-Center-Images"></a>

## <a name="game-center-images"></a>Images Game Center

Si votre application Xamarin. tvOS est un jeu et que vous avez inclus Game Center support, plusieurs ressources d’image supplémentaires seront nécessaires :

- **Icônes de réalisation** : une image opaque est nécessaire pour chaque réalisation qui sera automatiquement rognée dans un cercle. Les réalisations sont des éléments qui ne peuvent pas être activés.
- **Illustration du tableau de bord** : vous pouvez fournir une image facultative qui apparaîtra en haut du tableau de bord de votre application dans Game Center. Ces images ne peuvent pas être focalisées.
- **Illustration classement** : vous devez fournir entre une (1) à trois (3) images de proportions de 16:9 pour chaque classement pris en charge par votre application. Il peut s’agir `.png` de fichiers statiques ou en couches `.lsr` . L’illustration classement peut être active.

||Icônes de réussite|Illustration du tableau de bord|Graphique classement|
|---|---|---|---|
|Taille visible|200x200px|923x150px|n/a|
|Taille réelle|320x320px|n/a|659x371px|
|Taille de zone sécurisée|n/a|n/a|618x348px|
|Taille inactive|n/a|n/a|548x309px|
|Taille focalisée|n/a|n/a|618x348px|

Pour plus d’informations sur l’utilisation de Game Center, consultez le [Guide de programmation](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)d’Apple Game Center.

<a name="Working-with-Images"></a>

## <a name="working-with-images"></a>Utilisation d’images

Étant donné que tvOS 9 est un sous-ensemble d’iOS 9, les mêmes techniques que celles utilisées pour inclure et afficher des images dans une application Xamarin. iOS, fonctionnent également pour une application Xamarin. tvOS. Pour plus d’informations, consultez notre documentation sur [l’affichage d’une image](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

<a name="Setting-Xamarin.tvOS-Project-Images"></a>

## <a name="setting-xamarintvos-project-images"></a>Définition des images de projet Xamarin. tvOS

Comme indiqué ci-dessus, toutes les applications tvOS nécessitent une [image de lancement](#Launch-Image)et une [icône d’application](#App-Icons). Cette section décrit la sélection de l’icône de lancement de l’image et de l’application pour votre projet d’application Xamarin. tvOS une fois qu’elles ont été définies dans un catalogue de composants.

Effectuez les actions suivantes :

1. Dans la **Explorateur de solutions**, double-cliquez sur le `Info.plist` pour l’ouvrir et le modifier : 

    [![Fichier info. plist](icons-images-images/info01.png)](icons-images-images/info01.png#lightbox)
2. Dans l' **éditeur info. plist**, sélectionnez le catalogue des ressources (configuré ci-dessus dans la section [définition des icônes d’application](#Setting-the-App-Icons) ) pour les **icônes d’application**: 

    [![Éditeur info. plist](icons-images-images/info02.png)](icons-images-images/info02.png#lightbox)
3. Ensuite, sélectionnez le catalogue des ressources (configuré ci-dessus dans la section [définition de l’image de lancement](#Setting-the-Launch-Image) ) pour les **images de lancement**.
4. Enregistrez vos modifications.

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Cet article a traité tous les types d’images et tailles utilisés dans une application Xamarin. tvOS. Tout d’abord, les images de lancement, les images superposées, les icônes d’application, les images de haut rayon et les images de Game Center. Ensuite, il a abordé l’utilisation des images dans votre application Xamarin. tvOS.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
