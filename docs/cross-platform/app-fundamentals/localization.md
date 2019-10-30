---
title: Localisation de l’interface utilisateur de l’application
description: Ce document décrit les concepts interplateformes d’internationalisation et de localisation et examine leur impact sur la conception de l’application.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 418c0da2b8fa0e495ae7e6a605c7fde43b6515cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016794"
---
# <a name="localization"></a>Localisation

Ce guide présente les concepts qui sous-tendent l' *internationalisation* et la *localisation* , ainsi que des liens vers des instructions sur la façon de créer des applications mobiles Xamarin à l’aide de ces concepts.

Si vous souhaitez passer directement aux détails techniques de la localisation des applications Xamarin, commencez par l’un de ces Articles de procédure spécifiques à la plateforme :

- Localisation multiplateforme [**Xamarin. Forms**](~/xamarin-forms/app-fundamentals/localization/index.md) à l’aide de fichiers resx.
- Localisation de plateforme native [**Xamarin. iOS**](~/ios/app-fundamentals/localization/index.md) .
- Localisation de la plateforme native [**Xamarin. Android**](~/android/app-fundamentals/localization.md) .

## <a name="i18n-and-l10n"></a>i18n et l10n

L' *internationalisation* est le processus qui permet à votre code d’afficher des langages différents et d’adapter son affichage à différents paramètres régionaux (tels que la mise en forme des nombres et des dates). On parle également de *globalisation*.

La *localisation* est l’étape qui suit : création de ressources (telles que des chaînes et des images) pour chaque langue et regroupement avec l’application internationaliser.

L’internationalisation est souvent abrégée en i18n – raccourci pour 18 lettres entre « i » et « n ». La localisation est également raccourcie à l10n, soit 10 lettres entre « L » et « n ».

## <a name="overview"></a>Vue d'ensemble

Ce document présente les concepts associés à l’internationalisation et à la localisation, et comment ils s’appliquent au développement d’applications mobiles en général.
Lors de la conception et de la création d’une application, les éléments que vous pouvez avoir précédemment codés en dur, mais qui doivent être paramétrés pour la localisation, sont les suivants :

- Disposition de l’écran et texte,
- Icônes, graphismes et couleurs,
- Fichiers vidéo et audio,
- Le texte et la mise en forme du texte dynamiques (tels que les nombres, les devises et les dates);
- Modifications de disposition pour les langues de droite à gauche (RTL) et
- Tri des données.

Quelles que soient les plateformes mobiles ciblées par votre application, ces conseils vous aideront à créer une application localisée de haute qualité.

## <a name="design-considerations"></a>Considérations de design

L’architecture d’une application afin qu’il soit possible de localiser son contenu est appelée « internationalisation ». L’internationalisation est bien plus qu’une simple possibilité de charger des chaînes de langage différentes au moment de l’exécution : une application bien conçue doit permettre la modification de toutes les ressources en fonction de la langue et des paramètres régionaux (y compris les images, les sons et les vidéos) et peut s’adapter mise en forme et disposition pour faire face à des chaînes de taille différente.

Cette section décrit quelques considérations relatives à la conception à prendre en compte lors de la création d’une application internationalisée.

### <a name="layouts-and-string-length"></a>Dispositions et longueur de chaîne

Les chaînes chinoises et japonaises peuvent être très courtes : parfois, un ou deux caractères peuvent être suffisamment significatifs pour une étiquette de champ d’entrée.

Les chaînes allemandes (par exemple) peuvent être très longues ; parfois, un mot relativement bref en anglais devient très long dans d’autres langages, soit en cours de découpage, soit en repassant de manière inattendue votre disposition.

Comparez les longueurs de chaîne de quelques éléments sur l’écran d’accueil iOS en anglais, allemand et japonais :

[![](localization-images/language-compare-sml.png "German vs Japanese string length")](localization-images/language-compare.png#lightbox)

Notez que les **paramètres** en anglais (8 caractères) requièrent 13 caractères pour la traduction en allemand, mais seulement 2 caractères en japonais.

Les dispositions où l’étiquette d’affichage et le champ d’entrée sont côte à côte sont difficiles à utiliser lorsque la longueur de l’étiquette peut varier considérablement. Souvent, une disposition dans laquelle l’étiquette est affichée au-dessus d’un champ est plus facile à localiser, car la largeur complète de l’écran est disponible à la fois pour l’étiquette et l’entrée.

En règle générale, si vous générez des dispositions fixes (en particulier des éléments côte à côte), vous devez disposer d’au moins 50% de largeur plus grande que vos chaînes en anglais pour les étiquettes et le texte. Cela ne résoudra pas tous les problèmes, mais fournira un tampon qui fonctionnera dans de nombreux cas.

### <a name="input-validation"></a>Validation des entrées

Méfiez-vous des hypothèses lors de l’écriture de règles de validation. Il peut sembler possible d’exiger une entrée de champ de texte pour « exiger » au moins trois caractères en anglais, car une seule lettre a rarement une signification. En chinois et japonais, toutefois, un caractère unique peut être une entrée valide et un message de validation « au moins 3 caractères est requis » n’a pas de sens pour ces langues.

D’autres tâches apparemment simples telles que la validation d’une adresse e-mail ou d’une URL de site Web deviennent plus compliquées avec les caractères qui ne sont pas limités au sous-ensemble ASCII.

Écrivez vos règles de validation avec l’internationalisation à l’esprit : choisissez les règles les moins restrictives ou écrivez la logique de manière à ce qu’elle fonctionne différemment pour chaque langue.

### <a name="images-and-color"></a>Images et couleurs

Toutes les images ne doivent pas être modifiées en fonction du choix de la langue d’un utilisateur. De nombreuses icônes ou photos sont adaptées à tous les utilisateurs, quel que soit le langage qu’ils parlent.
Certaines ressources ont un sens de localiser, par exemple :

- Images illustrant des personnes ou des emplacements spécifiques : votre application peut sembler plus pertinente pour les utilisateurs si elle affiche des personnes/emplacements locaux.
- Icônes : certains iconographie peuvent être spécifiques à une culture et vous pouvez rendre votre application plus facile à utiliser en localisant l’image pour refléter la compréhension locale.
- Couleurs : certaines cultures comprennent les couleurs différemment : le rouge peut signifier un avertissement dans une région, mais bonne chance dans une autre. Vérifiez avec les orateurs natifs lors de la conception de votre application pour déterminer si vous devez créer un mécanisme pour localiser les couleurs.

### <a name="videos-and-sound"></a>Vidéos et sons

Les vidéos et le son présentent des défis particuliers lors de la localisation d’une application. en effet, bien qu’il soit relativement facile d’utiliser des chaînes traduites, l’enregistrement de plusieurs pistes VoiceOver ou clips vidéo peut être coûteux et difficile.

Plusieurs copies de fichiers vidéo et audio peuvent également augmenter considérablement la taille de votre application (surtout si vous êtes en local dans un grand nombre de langues ou si vous avez beaucoup de fichiers multimédias). Vous pouvez envisager de télécharger uniquement les ressources de langue requises une fois que l’utilisateur a installé votre application, mais cela peut également entraîner une mauvaise expérience utilisateur sur les réseaux lents.

Il existe souvent plusieurs façons de résoudre les problèmes de localisation : l’élément le plus important consiste à les considérer comme devant et à s’assurer que votre application est conçue pour s’en occuper.

### <a name="dates-times-numbers-and-currency"></a>Dates, heures, nombres et devises

Si vous utilisez des fonctions de mise en forme .NET, n’oubliez pas de spécifier la culture afin que les séparateurs décimaux soient correctement analysés (et éviter la levée des exceptions de conversion). Par exemple, 1,99 et 1, 99 sont des représentations décimales valides en fonction de vos paramètres régionaux.

Lorsque les données proviennent d’une source connue (c’est-à-dire de votre propre code ou d’un service Web que vous contrôlez), vous pouvez coder en dur un identificateur de culture qui correspond à la mise en forme, par exemple InvariantCulture, qui fonctionnera pour la mise en forme de la langue anglaise standard.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Si les données sont entrées par l’utilisateur de l’application, analysez-la à l’aide d’une instance CultureInfo qui reflète leurs paramètres régionaux :

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Pour plus d’informations, consultez les articles MSDN [analyse des chaînes numériques](https://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) et [analyse des chaînes de date et d’heure](https://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) .

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Langues de droite à gauche (RTL)

Certains langages, tels que l’arabe, l’hébreu et l’ourdou (par exemple), sont lus de droite à gauche.
Les applications qui prennent en charge ces langages doivent utiliser des conceptions d’écran qui s’adaptent aux lecteurs de droite à gauche, par exemple :

- Le texte doit être aligné à droite.
- Les étiquettes doivent apparaître à droite des champs d’entrée.
- Le positionnement du bouton par défaut est généralement inversé.
- La navigation hiérarchique et l’animation (ainsi que d’autres métaphores et animations de navigation) qui utilisent la direction du contexte doivent également être inversées.

IOS et Android prennent en charge les mises en page de droite à gauche et le rendu des polices, avec des fonctionnalités intégrées qui permettent d’effectuer les réglages ci-dessus. Xamarin. Forms ne prend actuellement pas en charge automatiquement le rendu RTL.

### <a name="sorting"></a>Tri

Les différentes langues définissent l’ordre de tri de leurs alphabets différemment, même lorsqu’ils utilisent le même jeu de caractères.

Consultez les [Détails de la comparaison de chaînes](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) dans [meilleures pratiques pour l’utilisation de chaînes dans le .NET Framework](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) pour obtenir un exemple où Language (CultureInfo) affecte l’ordre de tri.

Il est peu probable que les fonctionnalités de base de données intégrées sur les plateformes mobiles prennent en charge l’ordre de tri spécifique à la langue. vous devrez peut-être mettre en œuvre du code supplémentaire dans votre logique métier.

### <a name="text-search"></a>Recherche de texte

Veillez à écrire et tester votre algorithme de recherche avec plusieurs langues à l’esprit. Les éléments à prendre en compte sont les suivants :

- Saisie semi-automatique : Si vous avez créé une fonction de saisie semi-automatique, assurez-vous que les suggestions de sources sont pertinentes pour la langue de l’utilisateur.
- Requête de correspondance aux données : les requêtes entrées dans une langue spécifique sont exécutées sur le contenu écrit dans cette langue ou sur tout le contenu de votre application ?
- Recherche de radical : Si votre recherche est conçue pour rechercher des mots similaires, des racines de mots et d’autres optimisations de recherche, les optimisations sont-elles générées pour toutes les langues prises en charge ?
- Tri : Assurez-vous que les résultats sont triés correctement (voir ci-dessus).

### <a name="data-from-external-sources"></a>Données provenant de sources externes

De nombreuses applications téléchargent des données à partir de sources externes, des flux Twitter et RSS aux cours météorologiques, d’actualité ou d’actions. Quand vous l’affichez à un utilisateur, vous devez prendre en compte la possibilité d’afficher un écran d’informations non pertinentes ou illisibles.

Il existe peu de stratégies que vous pouvez utiliser pour essayer de vérifier que votre application affiche les données pertinentes pour l’utilisateur :

- Différentes sources : votre application peut télécharger les données à partir d’une source différente selon la langue ou les paramètres régionaux de l’utilisateur. Les informations relatives aux paramètres régionaux, à la météo et aux actions peuvent être plus logiques que celles téléchargées à partir d’un flux d’Amérique du Nord.
- Affichage localisé : Si vous affichez un flux Twitter ou photo, vous devez afficher les métadonnées (telles que le temps nécessaire) dans sa propre langue, même si le contenu lui-même reste dans la langue d’origine.
- Traduction : vous pouvez créer une option de traduction dans votre application pour effectuer une traduction automatique des données entrantes. Cela peut être automatique ou à la discrétion de l’utilisateur. Veillez simplement à avertir l’utilisateur si cela se produit, car les traductions des machines ne sont jamais parfaites !

Cela peut également affecter des liens externes vers des pistes audio ou des vidéos : lors de la conception de votre application, veillez à planifier le contenu traduit ou à vous assurer que les utilisateurs sont correctement informés par l’interface utilisateur lorsque le contenu ne sera pas présenté dans leur sous.

### <a name="dont-over-translate"></a>Ne pas dépasser la traduction

Certaines chaînes de votre application n’ont peut-être pas besoin d’être traduites, ou au moins une attention particulière doit être prise en considération par le traducteur. Les exemples peuvent inclure :

- URL : Si vous répertoriez une URL, il se peut qu’elle ne doive pas être ajustée par langue. Par exemple, facebook.com ne nécessite pas de traduction. il détecte automatiquement la langue sur le site principal. D’autres sites ont un contenu spécifique aux paramètres régionaux et vous pouvez proposer une autre URL, telle que yahoo.com ou yahoo.fr ou yahoo.it.
- Numéros de téléphone, en particulier ceux dont les indicatifs sont différents pour les appelants qui parlent une langue particulière.
- Coordonnées : les adresses et d’autres informations peuvent varier selon la langue ou les paramètres régionaux.
- Marques & noms de produits : certaines chaînes n’ont pas besoin d’être traduites, car elles sont toujours écrites dans la même langue.

Enfin, veillez à inclure des instructions détaillées pour le traducteur si certaines chaînes nécessitent un traitement spécial.

### <a name="formatted-text"></a>Texte mis en forme

Ce n’est généralement pas un problème avec les applications mobiles, car les chaînes ne sont généralement pas formatées de manière riche. Toutefois, si du texte enrichi (par exemple, une mise en forme en gras ou en italique) est requis dans votre application, assurez-vous que le traducteur sait comment entrer la mise en forme, que vos fichiers de chaînes le stockent correctement et qu’il est correctement mis en forme avant d’être affiché à l’utilisateur (IE). ne laissez pas accidentellement les codes de mise en forme sont eux-mêmes présentés à l’utilisateur).

## <a name="translation-tips"></a>Conseils de traduction

La traduction des chaînes utilisées par une application est considérée comme faisant partie du processus de localisation. En général, cette tâche est externalisée sur un service de traduction et effectuée par un personnel multilingue qui peut ne pas connaître votre application ou votre entreprise.

Les conseils suivants vous aideront à créer des chaînes plus faciles à traduire correctement et à améliorer la qualité de vos applications localisées.

### <a name="localize-complete-strings-not-words"></a>Localiser des chaînes complètes, et non des mots

Les développeurs adoptent parfois l’approche consistant à spécifier des mots uniques ou des extraits de phrase pour qu’ils puissent les réutiliser dans toute l’application. Par exemple, pour le texte « vous avez 5 messages ». ils peuvent spécifier les chaînes suivantes pour la traduction

**Incorrect**:

```csharp
"You have"
"no"
"message"
"messages"
```

puis essayez de créer l’expression correcte à la volée dans le code à l’aide de la concaténation de chaînes :

**Incorrect**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Cela est déconseillé** , car il ne fonctionnera pas nécessairement pour tous les langages et sera difficile pour le traducteur de comprendre le contexte de chaque segment. Cela entraîne également la réutilisation de chaînes traduites, ce qui peut entraîner des problèmes ultérieurement s’ils sont utilisés dans des contextes différents (puis sont mis à jour).

### <a name="allow-for-parameter-re-ordering"></a>Autoriser le réordonnancement des paramètres

Certains langages de programmation requièrent une syntaxe supplémentaire pour spécifier l’ordre des paramètres dans une chaîne. Toutefois, .NET prend déjà en charge le concept d’espaces réservés numérotés, donc

**Bonne**:

```csharp
"a {0} b {1} cde {3}"
```

peut être traduite comme suit (où la position et l’ordre des espaces réservés sont modifiés)

```csharp
"{2} {3} f g h {0}"
```

et les jetons sont classés comme étant le traducteur prévu. Veillez à inclure une explication de ce que contient chaque espace réservé lors de l’envoi de la chaîne à un traducteur.

### <a name="use-multiple-strings-for-cardinality"></a>Utiliser plusieurs chaînes pour la cardinalité

Évitez les chaînes comme les `"You have {0} message/s."` utiliser des chaînes spécifiques pour chaque État afin de fournir une meilleure expérience utilisateur :

**Bonne**:

```csharp
"You have no messages."
"You have 1 message."
"You have 2 messages."
"You have {0} messages."
```

Vous devrez écrire du code dans votre application pour évaluer le nombre affiché et choisir la chaîne appropriée. Certaines plateformes (y compris iOS et Android) disposent de fonctionnalités intégrées pour choisir automatiquement la chaîne la mieux adaptée en fonction des préférences de la langue/des paramètres régionaux actuels.

### <a name="allowing-for-gender"></a>Autorisation pour le sexe

Les langues latines utilisent parfois des mots différents selon le sexe de l’objet. Si votre application connaît le sexe, vous devez autoriser les chaînes traduites à refléter ceci.

Il y a également le cas le plus évident, même en anglais, où les chaînes font référence à une personne ou un utilisateur spécifique de votre application. Par exemple, certains sites affichent des messages comme `"Bob commented on his post"`. vous avez donc besoin de chaînes pour un sexe mâle, femelle et non binaire ou inconnu :

**Bonne**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Ne pas réutiliser les chaînes

Ou plus précisément, ne réutilisez pas les chaînes juste parce qu’elles sont similaires lorsque la chaîne elle-même a un objectif ou une signification différente.

Par exemple : Imaginez que vous disposez d’un commutateur on/off dans votre application et que le contrôle du commutateur a besoin que le texte pour « on » et « off » soit localisé. Vous affichez également la valeur de ce paramètre ailleurs dans l’application dans une étiquette de texte. Vous devez utiliser des chaînes différentes pour l’affichage du commutateur par rapport à l’état du commutateur (même s’il s’agit de la même chaîne dans votre langue par défaut), par exemple :

- « On » : affiché sur le commutateur lui-même
- « OFF » : affiché sur le commutateur lui-même
- « On » – affiché dans une étiquette
- « OFF » – affiché dans une étiquette

Cela offre une flexibilité maximale pour le traducteur :

- Pour des raisons de conception, peut-être que le commutateur lui-même utilise les minuscules « on » et « OFF », mais que l’étiquette d’affichage utilise les majuscules « on » et « OFF ».
- Dans certains langages, la valeur du commutateur peut être abrégée pour s’ajuster au contrôle de l’interface utilisateur, tandis que le mot complet (traduit) peut apparaître dans l’étiquette.
- Sinon, pour certains langages, le rendu de votre commutateur peut utiliser « I » et « O » pour la familiarité culturelle, mais vous pouvez toujours souhaiter que l’étiquette Lise « on » ou « OFF ».

### <a name="translation-services"></a>Services de traduction

#### <a name="machine-translation"></a>Traduction automatique

Pour créer des fonctionnalités de traduction dans votre application, envisagez le [API de traduction de texte Translator Text Azure](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

À des fins de test, vous pouvez utiliser l’un des nombreux outils de traduction en ligne pour inclure du texte localisé dans votre application pendant le développement :

- [Traducteur Bing](https://www.bing.com/translator/)
- [Google translate](https://translate.google.com/)

De nombreuses autres sont disponibles. La qualité de la traduction automatique n’est généralement pas considérée comme suffisante pour libérer une application sans avoir été vérifiée et testée par des traducteurs professionnels ou des orateurs natifs.

#### <a name="professional-translation"></a>Traduction professionnelle

Il existe également des services de traduction professionnels qui prennent vos chaînes et les distribuent à leurs propres traducteurs, ce qui vous offre des traductions terminées.

L’un des meilleurs services connus est [Lionbridge](https://www.lionbridge.com/). La plupart des services professionnels prennent en charge tous les types de fichiers courants, y compris les chaînes, XML, RESX et POT/PO.

## <a name="summary"></a>Récapitulatif

Cet article a présenté certains des concepts avec lesquels vous devez être familiarisé avant l’internationalisation de votre application, puis la localisation de vos ressources, ainsi que la modification des préférences linguistiques pour chaque plateforme.

Ces concepts peuvent être appliqués aux différentes techniques d’internationalisation propres à la plateforme et à la plateforme, qui sont possibles avec Xamarin.

Continuez à lire les détails techniques de la plateforme qui vous intéresse :

- Localisation multiplateforme [Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md) à l’aide de fichiers resx.
- Localisation de plateforme native [Xamarin. iOS](~/ios/app-fundamentals/localization/index.md) .
- Localisation de la plateforme native [Xamarin. Android](~/android/app-fundamentals/localization.md) .

## <a name="related-links"></a>Liens associés

- [Présentation de la localisation d’Apple](https://developer.apple.com/internationalization/)
- [Liste de vérification de la localisation d’Android](https://developer.android.com/distribute/tools/localization-checklist.html)
- [Meilleures pratiques pour le développement d’applications mondialisables (MSDN)](https://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
