---
title: Autres ressources
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 3f1e2ef06fb439f4b3ef290b1a7f80856b126a8d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755197"
---
# <a name="alternate-resources"></a>Autres ressources

Les ressources de remplacement sont les ressources qui ciblent un appareil spécifique ou une configuration au moment de l’exécution, comme la langue actuelle, la taille d’écran spécifique ou la densité de pixels. Si Android peut correspondre à une ressource qui est plus spécifique pour un périphérique ou une configuration spécifique que la ressource par défaut, cette ressource sera utilisée à la place. S’il ne trouve pas une autre ressource qui correspond à la configuration actuelle, les ressources par défaut sont chargées. La manière dont Android décide quelles ressources seront utilisées par une application sera traitée plus en détail ci-dessous, dans la section emplacement des ressources

Les ressources de remplacement sont organisées sous la forme d’un sous-répertoire dans le dossier resources en fonction du type de ressource, comme les ressources par défaut. Le nom du sous-répertoire de ressources de remplacement se présente sous la forme suivante :-_Qualificateur_ ResourceType

*Qualifier* est un nom qui identifie une configuration d’appareil spécifique.
Un nom peut contenir plusieurs qualificateurs, chacun étant séparé par un tiret. Par exemple, la capture d’écran ci-dessous montre un projet simple qui comporte d’autres ressources pour différentes configurations telles que les paramètres régionaux, la densité d’écran, la taille d’écran et l’orientation :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Ressources de remplacement](alternate-resources-images/alternate-resources-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Ressources de remplacement](alternate-resources-images/alternate-resources-xs.png)

-----

Les règles suivantes s’appliquent lors de l’ajout de qualificateurs à un type de ressource :

1. Il peut y avoir plusieurs qualificateurs, chacun étant séparé par un tiret.

2. Les qualificateurs n’ont peut-être pas été spécifiés une seule fois.

3. Les qualificateurs doivent être dans l’ordre dans lequel ils apparaissent dans le tableau ci-dessous.

Les qualificateurs possibles sont répertoriés ci-dessous pour référence :

- **MCC et MNC** Le code de [pays mobile](https://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) et éventuellement le [Code de réseau mobile](https://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). &ndash; La carte SIM fournit le MCC, tandis que le réseau auquel l’appareil est connecté fournit le MNC. Bien qu’il soit possible de cibler des paramètres régionaux à l’aide de l’indicatif de pays mobile, l’approche recommandée consiste à utiliser le qualificateur de langue spécifié ci-dessous. Par exemple, pour cibler des ressources en Allemagne, le qualificateur est `mcc262`. Pour cibler des ressources pour T-Mobile aux États-Unis, le qualificateur `mcc310-mnc026`est.
  Pour obtenir la liste complète des codes de pays mobiles et des codes <http://mcc-mnc.com/>de réseau mobile, consultez.

- **Langue** Code de [langue ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1) à deux lettres et éventuellement suivi du [Code de région ISO-3166-alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)à deux lettres. &ndash; 
  Si les deux qualificateurs sont fournis, ils sont séparés par un `-r`. Par exemple, pour cibler des paramètres régionaux francophones, le qualificateur de `fr` est utilisé. Pour cibler les paramètres régionaux français-canadien, `fr-rCA` le sera utilisé. Pour obtenir la liste complète des codes de langue et des codes de région, consultez [codes pour la représentation des noms des langues et des noms de](http://www.loc.gov/standards/iso639-2/php/English_list.php) [pays et des éléments de code](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Plus petite largeur** &ndash; Spécifie la largeur d’écran la plus petite sur laquelle l’application est censée s’exécuter. Abordée plus en détail dans [création de ressources pour différents écrans](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible au niveau de l’API 13 (Android 3,2) et versions ultérieures. Par exemple, le qualificateur `sw320dp` est utilisé pour cibler des appareils dont la hauteur et la largeur sont au moins 320dp.

- **Largeur disponible** Largeur minimale de l’écran au format w*N*DP, où N est la largeur en pixels indépendants de la densité. &ndash;
  Cette valeur peut changer à mesure que l’utilisateur fait pivoter l’appareil. Abordée plus en détail dans [création de ressources pour différents écrans](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible au niveau de l’API 13 (Android 3,2) et versions ultérieures. Exemple : le qualificateur w720dp est utilisé pour cibler des appareils dont la largeur est au moins égale à 720dp.

- **Hauteur disponible** Hauteur minimale de l’écran au format h*N*DP, où N est la hauteur en DP. &ndash; Cette valeur peut changer à mesure que l’utilisateur fait pivoter l’appareil. Abordée plus en détail dans [création de ressources pour différents écrans](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible au niveau de l’API 13 (Android 3,2) et versions ultérieures. Par exemple, le qualificateur h720dp est utilisé pour cibler des appareils qui ont une hauteur minimale de 720dp

- **Taille** de l’écran &ndash; Ce qualificateur est une généralisation de la taille de l’écran à laquelle ces ressources sont destinées. Il est abordé plus en détail dans la section [création de ressources pour différents écrans](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Les valeurs possibles sont `small`, `normal`, `large` et `xlarge`. Ajouté au niveau de l’API 9 (Android 2.3/Android 2.3.1/Android 2.3.2)

- **Apparence** de l’écran &ndash; Cela est basé sur les proportions, et non sur l’orientation de l’écran. Un écran long est plus grand. Ajouté au niveau de l’API 4 (Android 1,6). Les valeurs possibles sont long et notlong.

- **Orientation** de l’écran &ndash; Orientation de l’écran portrait ou paysage. Cela peut changer pendant la durée de vie d’une application.
  Les valeurs possibles sont `port` et `land`.

- **Mode d’ancrage** &ndash; Pour les appareils d’une station d’accueil automobile ou d’un dock de bureau. Ajouté au niveau de l’API 8 (Android 2.2. x). Les valeurs possibles sont `car` et `desk`.

- **Mode nuit** &ndash; Indique si l’application s’exécute la nuit ou la journée. Cela peut changer pendant la durée de vie d’une application et est destiné à offrir aux développeurs la possibilité d’utiliser des versions plus sombres d’une interface la nuit. Ajouté au niveau de l’API 8 (Android 2.2. x). Les valeurs possibles sont `night` et `notnight`.

- **Densité de pixels de l’écran (dpi)** &ndash; Nombre de pixels dans une zone donnée sur l’écran physique. Généralement exprimé en points par pouce (dpi). Les valeurs possibles sont les suivantes :

  - `ldpi`&ndash; Écrans à faible densité.

  - `mdpi`&ndash; Écrans de densité moyenne

  - `hdpi`&ndash; Écrans à haute densité

  - `xhdpi`&ndash; Écrans à haute densité supplémentaires

  - `nodpi`&ndash; Ressources qui ne doivent pas être mises à l’échelle

  - `tvdpi`&ndash; Introduit dans le niveau d’API 13 (Android 3,2) pour les écrans entre MDPI et HDPI.

- **Type d’écran tactile** &ndash; Spécifie le type d’écran tactile qu’un appareil peut avoir. Les valeurs possibles `notouch` sont (pas d’écran tactile `stylus` ), (un écran tactile résistant adapté à un stylet) `finger` et (un écran tactile).

- **Disponibilité du clavier** &ndash; Spécifie le type de clavier disponible. Cela peut changer pendant la durée de vie d' &ndash; une application, par exemple lorsqu’un utilisateur ouvre un clavier matériel. Les valeurs possibles sont les suivantes :

  - `keysexposed`&ndash; Le clavier est disponible sur l’appareil. Si aucun clavier logiciel n’est activé, cette option est utilisée uniquement lorsque le clavier matériel est ouvert.

  - `keyshidden`&ndash; L’appareil possède un clavier matériel, mais il est masqué et aucun clavier logiciel n’est activé.

  - `keyssoft`&ndash; un clavier logiciel est activé sur l’appareil.

- **Méthode d’entrée de texte principal** &ndash; Utilisez pour spécifier les types de clés matérielles disponibles pour l’entrée. Les valeurs possibles sont les suivantes :

  - `nokeys`&ndash; Il n’existe aucune clé matérielle pour l’entrée.

  - `qwerty`&ndash; Un clavier QWERTY est disponible.

  - `12key`&ndash; Il y a un clavier matériel à 12 clés

- **Disponibilité** de la clé de navigation &ndash; Pour les cas où la navigation à 5 ou d-pad est disponible. Cela peut changer pendant la durée de vie de votre application. Les valeurs possibles sont les suivantes :

  - `navexposed`&ndash; les touches de navigation sont disponibles pour l’utilisateur.

  - `navhidden`&ndash; les touches de navigation ne sont pas disponibles.

- **Méthode de navigation non tactile principale** &ndash; Type de navigation disponible sur l’appareil. Les valeurs possibles sont les suivantes :

  - `nonav`&ndash; la seule fonctionnalité de navigation disponible est l’écran tactile

  - `dpad`&ndash; une d-pad (pavé directionnel) est disponible pour la navigation

  - `trackball`&ndash; l’appareil a un trackball pour la navigation

  - `wheel`&ndash; scénario rare où une ou plusieurs roues directionnelles sont disponibles

- **Version de la plateforme (niveau d’API)** Niveau d’API pris en charge par l’appareil au format v*n*, où N est le niveau d’API ciblé. &ndash; Par exemple, v11 ciblera un appareil d’API de niveau 11 (Android 3,0).

Pour obtenir des informations plus complètes sur les qualificateurs de ressources, consultez [fourniture de ressources](https://developer.android.com/guide/topics/resources/providing-resources.html) sur le site Web des développeurs Android.

## <a name="how-android-determines-what-resources-to-use"></a>Comment Android détermine les ressources à utiliser

Il est très possible et probable qu’une application Android contienne de nombreuses ressources. Il est important de comprendre comment Android sélectionne les ressources d’une application lorsqu’elle s’exécute sur un appareil.

Android détermine la base des ressources en effectuant une itération au sein du test de règles suivant :

- **Éliminer les qualificateurs contradictoires** &ndash; par exemple, si l’orientation de l’appareil est portrait, tous les répertoires de ressources du paysage seront rejetés.

- **Ignorer les qualificateurs non pris en charge** &ndash; Tous les qualificateurs ne sont pas disponibles pour tous les niveaux d’API. Si un répertoire de ressources contient un qualificateur qui n’est pas pris en charge par l’appareil, ce répertoire de ressources sera ignoré.

- **Identifier le qualificateur de priorité la plus élevée suivante** &ndash; en faisant référence au tableau ci-dessus, sélectionnez le qualificateur de priorité la plus élevée suivante (de haut en bas).

- **Conserver les répertoires de ressources pour le qualificateur** &ndash; si des répertoires de ressources correspondent au qualificateur du tableau ci-dessus, sélectionnez le qualificateur de priorité la plus élevée (de haut en bas).

Ces règles sont également illustrées dans l’organigramme suivant :

[![Organigramme des ressources](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Lorsque le système recherche des ressources spécifiques à la densité et ne peut pas les trouver, il tente de localiser d’autres ressources spécifiques en densité et de les mettre à l’échelle. Android n’utilise pas nécessairement les ressources par défaut.
Par exemple, lors de la recherche d’une ressource à faible densité et qu’elle n’est pas disponible, Android peut sélectionner une version à haute densité de la ressource sur les ressources par défaut ou à moyenne densité. Cela est dû au fait que la ressource à haute densité peut être réduite par un facteur de 0,5, ce qui entraîne moins de problèmes de visibilité que la mise à l’échelle d’une ressource de densité moyenne qui nécessiterait un facteur de 0,75.

Par exemple, considérez une application qui contient les répertoires de ressources dessinables suivants :

```
drawable
drawable-en
drawable-fr-rCA
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

Et maintenant l’application est exécutée sur un appareil avec la configuration suivante :

- **Paramètres régionaux** &ndash; en-GB
- **Orientation** &ndash; port
- **Densité** de l’écran &ndash; HDPI
- **Type d’écran tactile** &ndash; NoTouch
- **Méthode d’entrée principale** &ndash; 12key

Pour commencer, les ressources françaises sont éliminées, car elles sont en conflit avec les `en-GB`paramètres régionaux de, ce qui nous laisse :

```
drawable
drawable-en
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

Ensuite, le premier qualificateur est sélectionné dans le tableau qualificateurs ci-dessus : MCC et MNC. Il n’existe aucun répertoire de ressources contenant ce qualificateur, de sorte que le code MCC/MNC est ignoré.

Le qualificateur suivant est sélectionné, ce qui correspond à la langue. Des ressources correspondent au code de langue. Tous les répertoires de ressources qui ne correspondent pas au `en` code de langue de sont rejetés, de sorte que la liste des ressources est désormais :

```
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
```

Le qualificateur suivant qui est présent est pour l’orientation de l’écran, donc tous les répertoires de ressources qui ne correspondent `port` pas à l’orientation de l’écran de sont éliminés :

```
drawable-en-port
drawable-en-port-ldpi
```

Ensuite, il s’agit du qualificateur de la `ldpi`densité de l’écran,, qui entraîne l’exclusion d’un autre répertoire de ressources :

```
drawable-en-port-ldpi
```

À la suite de ce processus, Android utilise les ressources qui peuvent être dessinées dans le `drawable-en-port-ldpi` répertoire de ressources de l’appareil.

> [!NOTE]
> Les qualificateurs de taille d’écran fournissent une exception à ce processus de sélection. Android peut sélectionner des ressources conçues pour un écran plus petit que celui fourni par l’appareil actuel. Par exemple, un périphérique à écran large peut utiliser les ressources fournies pour un écran de taille normale. Toutefois, l’inverse n’est pas vrai : le même appareil à écran volumineux n’utilise pas les ressources fournies pour un écran XLarge. Si Android ne trouve pas un jeu de ressources qui correspond à une taille d’écran donnée, l’application se bloque.
