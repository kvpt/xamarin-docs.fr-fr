---
title: Localisation de l’Interface utilisateur application
description: Ce document décrit les concepts d’inter-plateformes d’internationalisation et localisation et examine leur impact sur la conception de l’application.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: a1218d836aad827390d9f5e70de189a869b7c6b8
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831001"
---
# <a name="localization"></a>Localisation

Ce guide présente les concepts qui sous-tendent *internationalisation* et *localisation* et des liens vers des instructions sur la façon de générer des applications mobiles Xamarin à l’aide de ces concepts.

Si vous souhaitez passer directement aux détails techniques de localisation d’applications Xamarin, commencez avec l’un de ces articles de savoir-faire spécifique à la plateforme :

- [**Xamarin.Forms** ](~/xamarin-forms/app-fundamentals/localization/index.md) localisation inter-plateformes à l’aide de fichiers RESX.
- [**Xamarin.iOS** ](~/ios/app-fundamentals/localization/index.md) localisation de la plateforme native.
- [**Xamarin.Android** ](~/android/app-fundamentals/localization.md) localisation de la plateforme native.

## <a name="i18n-and-l10n"></a>I18N et L10n

*Internationalisation* consiste à rendre votre code capable d’afficher différentes langues et l’adaptation de son affichage pour différents paramètres régionaux (par exemple, le nombre et date mise en forme). Cela est également appelé *globalisation*.

*Localisation* est l’étape qui suit : création des ressources (telles que des chaînes et images) pour chaque langue et regroupant avec l’application internationalize.

Il est souvent abrégée en i18n – abréviation de 18 lettres entre « i » et « n » de l’internationalisation. La localisation est réduite de la même façon pour L10n – pour 10 lettres entre « L » et « n ».

## <a name="overview"></a>Présentation

Ce document présente les concepts associés à l’internationalisation et localisation et comment elles s’appliquent au développement d’applications mobiles en général.
Lorsque conception et la création d’une application, les choses que vous deviez précédemment codée en dur, mais qui doivent paramétrables pour la localisation incluent :

- Dispositions d’écran et du texte,
- Icônes, les graphiques et les couleurs,
- Fichiers vidéo et audio,
- Texte dynamique et la mise en forme texte (par exemple, des nombres, devises et dates),
- Modifications de mise en page langues de droite-à-gauche (RTL), et
- Tri des données.

Quels que soient les plateformes mobiles, votre application cible les conseils suivants vous aideront à créer une application localisée de haute qualité.


## <a name="design-considerations"></a>Considérations de design

Architecture d’une application afin qu’il soit possible de localiser son contenu est appelée l’internationalisation. Internationalisation correctement est plus que simplement permettant de langue différente des chaînes à être chargé lors de l’exécution : une application bien conçue doit autoriser pour toutes les ressources modifiables selon la langue et paramètres régionaux (y compris les images, des sons et des vidéos) et peut s’adapter mise en forme et la disposition pour répondre à différentes chaînes de taille.

Cette section présente certaines considérations de conception à prendre en compte lors de la création d’une application internationalisée.

### <a name="layouts-and-string-length"></a>Dispositions et longueur de chaîne

Les chaînes chinois et le japonais peuvent être très courtes : parfois, un ou deux caractères peuvent être suffisamment explicites pour une étiquette de champ d’entrée.

(Chaînes allemands par exemple) peuvent être très longues ; Parfois, un mot relativement court en anglais devient très long dans d’autres langages – devenir soit détouré ou else inattendu redistribution votre disposition.

Comparer les longueurs de chaîne pour les quelques éléments sur l’écran d’accueil iOS en anglais, allemand et japonais :

[![](localization-images/language-compare-sml.png "Longueur de la chaîne japonais et allemand")](localization-images/language-compare.png#lightbox)

Notez que **paramètres** en anglais (8 caractères) requiert 13 caractères pour la traduction allemande, mais seulement 2 caractères en japonais.

Dispositions où l’étiquette de l’affichage et d’un champ d’entrée sont côte à côte sont difficiles à utiliser lorsque la longueur d’étiquette peut varier considérablement. Une mise en page dans laquelle l’étiquette est affichée au-dessus d’un champ est souvent plus facile à localiser parce que la largeur totale de l’écran est disponible pour l’étiquette et l’entrée.

En règle générale, si vous générez des dispositions fixes (en particulier les éléments côte à côte) autoriser au moins 50 % de la largeur de plus que vos chaînes en anglais nécessitent pour les étiquettes et le texte. Cela ne résoudra pas chaque problème mais que vous fournira une mémoire tampon qui fonctionnera dans de nombreux cas.

### <a name="input-validation"></a>Validation des entrées

Méfiez-vous des hypothèses lors de l’écriture des règles de validation. Il peut sembler valide pour exiger un champ de texte d’entrée au moins trois caractères en anglais, « exiger » dans la mesure où une seule lettre a très rarement toute signification. Dans le chinois et le japonais cependant un caractère unique peut être une entrée valide, et une validation de message « au moins 3 caractères est requis » n’est pas pertinent pour ces langues.

Autres tâches apparemment simples telles que la validation d’une adresse de messagerie ou URL du site Web deviennent plus compliquée avec les caractères ne sont pas limitées au sous-ensemble d’ASCII.

Écrire vos règles de validation avec internationalisation esprit – choisissez les règles moins restrictifs, ou écrire la logique pour qu’elle fonctionne différemment pour chaque langue.

### <a name="images-and-color"></a>Images et couleur

Pas à chaque image doit changer en fonction des choix de la langue d’un utilisateur. Plusieurs icônes ou les photos seront adaptées à tous les utilisateurs, peu importe quelle langue parlée.
Sens de certaines ressources à localiser, telles que :

- Images représentant des personnes ou des emplacements spécifiques – votre application se sentiront peut-être plus pertinente pour les utilisateurs si elle présente des personnes/emplacements locaux.
- Icônes – certains iconographie peut être spécifiques à la culture et vous pouvez rendre votre application plus facile à utiliser en localisant l’imagerie afin de refléter la présentation locale.
- Couleurs – certaines cultures comprennent les couleurs différemment : rouge peut vouloir dire avertissement dans une région, mais bonne chance dans un autre. Vérifiez auprès de leur langue maternelle lors de la conception de votre application pour déterminer si vous êtes nécessaire de créer un mécanisme permettant de localiser des couleurs.


### <a name="videos-and-sound"></a>Vidéos et audio

Vidéos et des clips vidéo ou audio défis particuliers présentes lors de la localisation d’une application, car il est très facile d’ajouter des chaînes traduites, l’enregistrement voiceover plusieurs effectue le suivi peuvent être coûteux et difficile.

Plusieurs copies de fichiers vidéo et audio peuvent augmenter considérablement la taille de votre application (surtout si vous localisez dans un grand nombre de langues ou que vous avez un grand nombre de fichiers multimédias). Vous pouvez envisager de télécharger uniquement les ressources linguistiques nécessaires une fois que l’utilisateur a installé votre application, mais cela pourrait également entraîner une expérience utilisateur médiocre sur des réseaux lents.

Il existe souvent plusieurs façons de résoudre les problèmes de localisation : la chose la plus importante consiste à prendre en compte initiaux et vérifiez que votre application est conçue pour prendre en charge les.


### <a name="dates-times-numbers-and-currency"></a>Dates, heures, nombres et devise

Si vous utilisez des fonctions de mise en forme .NET, pensez à spécifier la culture afin que les séparateurs décimaux sont analysés correctement (et éviter les exceptions de conversion levées). Par exemple 1,99 et 1,99 sont des représentations décimales valides en fonction de vos paramètres régionaux.

Lorsque les données provient d’une source connue (ie. à partir de votre propre code ou un service web que vous contrôlez) vous pouvez coder en dur un identificateur de culture qui correspond à la mise en forme telles que InvariantCulture qui fonctionne pour la mise en forme de langue anglais standard.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Si les données en entrée par l’utilisateur de l’application, l’analyser à l’aide d’une instance CultureInfo qui reflète les paramètres régionaux :

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Consultez le [l’analyse de chaînes numériques](https://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) et [l’analyse de chaînes de Date et heure](https://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) articles MSDN pour plus d’informations.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Langues de droite à gauche (DÀG)

Certains langages, tels que l’arabe et hébreu Ourdou (par exemple), sont lues de droite à gauche.
Les applications qui prennent en charge ces langues doivent utiliser des conceptions d’écran qui s’adaptent pour les lecteurs de droite à gauche, par exemple :

- Texte doit être aligné à droite.
- Étiquettes doivent apparaître à droite des champs d’entrée.
- Emplacement du bouton par défaut est généralement inversé.
- Navigation hiérarchique balayant et animation (et autres métaphores basées sur la navigation et animations) qui utilisent la direction pour le contexte doit également être annulée.

IOS et Android prennent en charge les dispositions de droite à gauche et le rendu des polices, avec des fonctionnalités intégrées qui aident à apporter les modifications ci-dessus. Xamarin.Forms ne prend pas actuellement automatiquement en charge le rendu de droite à gauche.

### <a name="sorting"></a>Tri

Différentes langues définissent l’ordre de tri de leurs alphabets différemment, même lorsqu’ils utilisent le même jeu de caractères.

Consultez le [détail de la comparaison de chaînes](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) dans [meilleures pratiques pour l’utilisation de chaînes dans le .NET Framework](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) pour obtenir un exemple où langue (CultureInfo) affecte l’ordre de tri.

Il est peu probable que les fonctionnalités de base de données intégrée sur les plateformes mobiles prendront en charge de tri spécifiques au langage classement afin de vous être amené à implémenter le code supplémentaire dans votre logique métier.

### <a name="text-search"></a>Recherche de texte

Vérifiez que vous écrivez et testez votre algorithme de recherche avec plusieurs langues à l’esprit. Éléments à prendre en considération sont les suivantes :

- Saisie semi-automatique – si vous avez créé une fonction de saisie semi-automatique vous assurer de que sources de suggestions relatives à la langue de l’utilisateur.
- Requête correspondant aux données – recherche requêtes entrés dans spécifiques à un langage être exécuté sur tout contenu écrit dans ce langage, ou sur tout le contenu dans votre application ?
- Recherche de radical – si votre recherche a été conçue pour rechercher des mots similaires, les racines de word et les autres optimisations de recherche, est ces optimisations intégrées pour toutes les langues que vous prenez en charge ?
- Tri – Assurez-vous que les résultats sont triés correctement (voir ci-dessus).


### <a name="data-from-external-sources"></a>Données provenant de sources externes

De nombreuses applications téléchargement des données provenant de sources externes, à partir de Twitter et les flux RSS à la météo, actualités ou des actions. Lors de l’affichage cela à un utilisateur, vous devez envisager la possibilité que vous afficherez un écran des informations inutiles ou illisibles leur.

Il existe quelques stratégies que vous pouvez utiliser pour essayer et vérifiez que votre application affiche les données pertinentes pour l’utilisateur :

- Sources différentes : votre application peut télécharger les données d’une source différente en fonction des paramètres régionaux ou linguistiques de l’utilisateur. Les prix des actualités, météo et le stock de paramètres régionaux peuvent être plus judicieux que quelque chose téléchargé à partir d’un flux en Amérique du Nord.
- Affichage localisé – si vous affichez une photo du flux, ou Twitter vous devez afficher les métadonnées (par exemple, le temps nécessaire) dans la langue de son propre, même si le contenu proprement dit reste dans la langue d’origine.
- Traduction –, vous pourriez créer une option de traduction à votre application pour effectuer une traduction de données entrantes. Cela peut être automatique ou à la discrétion de l’utilisateur – Veillez simplement à informer l’utilisateur si cela a lieu, étant donné que les traductions de la machine ne sont jamais parfaites !

Cela peut également affecter des liens externes aux pistes audio ou de vidéos – lors de la conception de votre application n’oubliez pas de planifier à l’avance pour l’alimentation des textes traduits ou veiller à ce que les utilisateurs sont correctement informés par l’interface utilisateur lorsque le contenu n’est pas présenté dans leur langage.


### <a name="dont-over-translate"></a>Ne se traduisent excessif

Certaines chaînes dans votre application peuvent ne pas nécessitent pas de traduction ou au moins une attention particulière par le traducteur. Exemples peuvent inclure :

- URL – si vous répertoriez une URL, il peut ou n'ayez pas besoin d’être ajustée par langage. Par exemple, il détecte automatiquement la langue sur le site principal de traduction ne nécessite pas facebook.com. Autres sites ont contenu spécifique de paramètres régionaux et vous souhaiteriez offrir une URL différente, telles que yahoo.com quelques yahoo.fr ou yahoo.it.
- Numéros de téléphone : en particulier ceux avec des numéros pour les appelants qui parlent une certaine langue ou de codes de pays différents.
- Les détails du contact – adresses et autres informations peuvent varier par paramètres régionaux ou linguistiques.
- Des marques & noms de produits – certaines chaînes n’avez pas besoin traduire, car ils sont toujours écrits dans la même langue.

Enfin, veillez à inclure des instructions détaillées pour le traducteur si certaines chaînes nécessitent un traitement spécial.


### <a name="formatted-text"></a>Texte mis en forme

N’est généralement pas un problème avec les applications mobiles, car les chaînes généralement ne sont pas mise en forme enrichie. Cependant, si le texte enrichi (par exemple, la mise en forme gras ou italique) est requis dans votre application vous assurer le traducteur sait comment à la mise en forme des entrées, vos fichiers de chaînes stockent correctement, il est mis en forme correctement avant d’être affichées à l’utilisateur (c.-à-d. ne laissez pas accidentellement les codes de mise en forme eux-mêmes être présentées à l’utilisateur).



## <a name="translation-tips"></a>Conseils de traduction

Traduire les chaînes utilisées par une application est considérée comme partie du processus de localisation. En général, cette tâche est sous-traité à un service de traduction et effectuée par le personnel multilingue qui peuvent ne pas savoir votre application ou votre entreprise.

Les conseils suivants vous aideront à produire des chaînes qui sont plus faciles à traduire avec précision et par conséquent, améliorer la qualité de vos applications localisées.



### <a name="localize-complete-strings-not-words"></a>Localiser des chaînes complètes, pas les mots

Parfois, les développeurs prennent l’approche de la tentative de spécifier des mots uniques ou la phrase « extraits de code » afin qu’ils peuvent réutiliser tout au long de l’application. Par exemple, pour le texte « vous avez 5 messages. » ils peuvent spécifier les chaînes suivantes pour la traduction

**Mauvais**:

```csharp
"You have"
"no"
"message"
"messages"
```

et essayez de créer la corriger l’expression à la volée dans le code à l’aide de la concaténation de chaînes :

**Mauvais**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Cette méthode est déconseillée** , car il ne fonctionnera pas nécessairement pour tous les langages et sera difficile pour le traducteur de comprendre le contexte de chaque segment court. Il permet d’accéder à la réutilisation des chaînes traduites, ce qui peuvent entraîner des problèmes plus tard s’ils sont utilisés dans des contextes différents (et puis mis à jour).


### <a name="allow-for-parameter-re-ordering"></a>Autoriser pour le paramètre réorganisation

Certains langages de programmation nécessitent une syntaxe supplémentaire pour spécifier l’ordre des paramètres dans une chaîne, mais .NET prend déjà en charge le concept des espaces réservés numérotés, par conséquent

**Bonne**:

```csharp
"a {0} b {1} cde {3}"
```

peut être traduit ce qui suit (où est modifié la position et l’ordre des espaces réservés)

```csharp
"{2} {3} f g h {0}"
```

et les jetons sont classés en tant que le traducteur prévu. Veillez à inclure une explication de ce que contient chaque espace réservé lors de l’envoi de la chaîne à un traducteur.


### <a name="use-multiple-strings-for-cardinality"></a>Utiliser plusieurs chaînes de cardinalité

Éviter les chaînes comme `"You have {0} message/s."` chaînes spécifiques pour chaque état permet d’offrir une meilleure expérience utilisateur :

**Bonne**:

```csharp
"You have no messages."
"You have 1 messages."
"You have 2 messages."
"You have {0} messages."
```

Vous devrez écrire du code dans votre application pour évaluer le nombre affiché et choisissez la chaîne appropriée. Certaines plateformes (y compris iOS et Android) ont des fonctionnalités intégrées pour choisir automatiquement la chaîne au pluriel meilleures basée sur les préférences pour la langue/région actuelle.


### <a name="allowing-for-gender"></a>Ce qui permet de sexe

Langues latines parfois utilisent des mots différents selon le sexe de l’objet. Si votre application connaît sexe, vous devez autoriser les chaînes traduites en conséquence.

Il est également le cas le plus évident même en anglais, où les chaînes font référence à une personne spécifique ou d’un utilisateur de votre application. Par exemple, certains sites affichent les messages comme `"Bob commented on his post"` ; vous devez donc des chaînes pour un sexe masculin, féminin et non binaires ou inconnu :

**Bonne**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Ne réutilisez pas les chaînes

Ou, plus précisément, ne réutilisez pas chaînes juste, car elles sont similaires lorsque la chaîne elle-même a un objectif différent ou la signification.

Par exemple : imaginez que vous avez un commutateur activé/désactivé dans votre application et le commutateur contrôle a besoin le texte pour 'on' et 'off' à localiser. Vous également affichez la valeur de ce paramètre d’ailleurs dans l’application d’une étiquette de texte. Vous devez utiliser des chaînes différentes pour l’affichage du commutateur par rapport à l’état du commutateur (même si elles sont la même chaîne dans votre langue par défaut) – par exemple :

- « Activé » – affiché sur le commutateur lui-même
- « Désactivé » – affiché sur le commutateur lui-même
- « Activé » – affiché dans une étiquette
- « Désactivé » – affiché dans une étiquette

Cela fournit une flexibilité maximale pour le traducteur :

- Pour des raisons de conception, par exemple le commutateur lui-même utilise en minuscules « on » et « désactivé », mais l’étiquette de l’affichage utilise la majuscule « On » et « Désactivé ».
- Certains langages peut-être la valeur du commutateur sera abrégé pour tenir dans le contrôle d’interface utilisateur, tandis que le mot complet (traduit) peut apparaître dans l’étiquette.
- Vous pouvez également, pour certaines langues le rendu de votre commutateur peut être utiliser « I » et « O » pour plus d’informations culturelles, mais vous pouvez toujours l’étiquette à lire « On » ou « Off ».

### <a name="translation-services"></a>Services de traduction

#### <a name="machine-translation"></a>Traduction automatique

Pour générer des fonctionnalités de traduction dans votre application, envisagez le [Azure l’API Translator Text](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

Aux fins de test vous pouvez utiliser un des nombreux outils de traduction en ligne pour inclure du texte localisé dans votre application pendant le développement :

- [Bing Translator](https://www.bing.com/translator/)
- [Traduire Google](http://translate.google.com/)

Il existe de nombreux autres disponibles. La qualité de traduction automatique n’est pas généralement considérées comme suffisamment efficace pour publier une application sans tout d’abord examinée et testée par des traducteurs professionnels ou leur langue maternelle.

#### <a name="professional-translation"></a>Traduction professionnelle

Il existe également des services de traduction professionnelle qui prend les chaînes et les distribuer à leurs propres traducteurs, en vous proposant des traductions terminées pour des frais.

Un des services plus connus est [LionBridge](http://www.lionbridge.com/). Services professionnels prend en charge tous les types de fichiers courants, y compris les chaînes, XML, RESX et POT/bon de commande.


## <a name="summary"></a>Récapitulatif

Cet article a présenté certains des concepts que vous devez être familiarisé avec avant qui consiste à internationaliser votre application et de la localisation puis vos ressources et également vu comment modifier les préférences de langue pour chaque plateforme.

Ces concepts sont applicables pour les diverses techniques d’internationalisation spécifiques à la plateforme et inter-plateformes sont possibles avec Xamarin.

Poursuivez la lecture des détails techniques pour la plateforme qui que vous intéresse :

- [Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md) localisation inter-plateformes à l’aide de fichiers RESX.
- [Xamarin.iOS](~/ios/app-fundamentals/localization/index.md) localisation de la plateforme native.
- [Xamarin.Android](~/android/app-fundamentals/localization.md) localisation de la plateforme native.



## <a name="related-links"></a>Liens associés

- [Vue d’ensemble de la localisation d’Apple](https://developer.apple.com/internationalization/)
- [Liste de vérification de la localisation d’Android](https://developer.android.com/distribute/tools/localization-checklist.html)
- [Meilleures pratiques pour développer des Applications mondialisables (MSDN)](https://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
