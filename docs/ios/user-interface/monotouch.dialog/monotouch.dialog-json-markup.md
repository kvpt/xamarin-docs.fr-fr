---
title: MonoTouch.Dialog Json Markup
description: Ce document décrit la syntaxe JSON qui peut être utilisée pour créer une interface utilisateur de Xamarin.iOS à l’aide de MonoTouch.Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: bc6842871a2f59c9851e90adbc6609707a7ecd1f
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039658"
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json Markup

Cette page décrit le balisage Json accepté par de MonoTouch.Dialog [JsonElement](xref:MonoTouch.Dialog.JsonElement)

Commençons par un exemple. Voici un fichier Json complet qui peut être passé dans JsonElement.

```json
{     
    "title": "Json Sample",
    "sections": [ 
        {
            "header": "Booleans",
            "footer": "Slider or image-based",
            "id": "first-section",
            "elements": [
                { 
                    "type": "boolean",
                    "caption": "Demo of a Boolean",
                    "value": true
                }, {
                    "type": "boolean",
                    "caption": "Boolean using images",
                    "value": false,
                    "on": "favorite.png",
                    "off": "~/favorited.png"
                }, {
                    "type": "root",
                    "title": "Tap for nested controller",
                    "sections": [
                        {
                            "header": "Nested view!",
                            "elements": [
                                {
                                    "type": "boolean",
                                    "caption": "Just a boolean",
                                    "id": "the-boolean",
                                    "value": false
                                }, {
                                    "type": "string",
                                    "caption": "Welcome to the nested controller"
                                }
                            ]
                        }
                    ]
                }
            ]
        }, {
            "header": "Entries",
            "elements" : [
                {
                    "type": "entry",
                    "caption": "Username",
                    "value": "",
                    "placeholder": "Your account username"
                }
            ]
        }
    ]
}
```

Le balisage ci-dessus génère l’interface utilisateur suivant :

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "L’interface utilisateur créé par le balisage donné")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Chaque élément dans l’arborescence peut contenir la propriété `"id"`. Il est possible lors de l’exécution pour référencer des sections individuelles ou des éléments à l’aide de l’indexeur JsonElement. Comme ceci :

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Syntaxe d’élément racine

L’élément racine contient les valeurs suivantes :

-  `title`
-  `sections` (facultatif)


L’élément racine peut apparaître que dans une section en tant qu’élément pour créer un contrôleur imbriqué. Dans ce cas, la propriété supplémentaire `"type"` doit être définie sur `"root"`

 <a name="url" />


### <a name="url"></a>url

Si le `"url"` propriété est définie, si l’utilisateur appuie sur ce RootElement, le code demande un fichier à partir de l’url spécifiée et rendra le contenu affiche les nouvelles informations. Vous pouvez l’utiliser pour créer étendre l’interface utilisateur à partir du serveur en fonction de ce que l’utilisateur appuie sur.

 <a name="group" />


### <a name="group"></a>groupe

Si la valeur, cette valeur définit le nom de groupe pour l’élément racine. Les noms de groupe sont utilisés pour choisir un résumé s’affiche en tant que la valeur de l’élément racine à partir d’un des éléments imbriqués dans l’élément. Il s’agit de la valeur d’une case à cocher ou de la valeur d’un bouton radio.

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Identifie l’élément de case d’option est sélectionnée dans les éléments imbriqués

 <a name="title" />


### <a name="title"></a>titre

S’il est présent, il sera le titre utilisé pour le RootElement

 <a name="type" />


### <a name="type"></a>type

Doit être définie sur `"root"` lorsque cela s’affiche dans une section (cela est utilisé pour imbriquer des contrôleurs).

 <a name="sections" />


### <a name="sections"></a>sections

Il s’agit d’un tableau Json avec les sections individuelles

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>Syntaxe de la section

La section contient :

-  `header` (facultatif)
-  `footer` (facultatif)
-  Tableau `elements`


 <a name="header" />


### <a name="header"></a>en-tête

Le cas échéant, le texte d’en-tête s’affiche en tant que légende de la section.

 <a name="footer" />


### <a name="footer"></a>Pied de page

Le cas échéant, le pied de page s’affiche en bas de la section.

 <a name="elements" />


### <a name="elements"></a>éléments

Il s’agit d’un tableau d’éléments. Chaque élément doit contenir au moins une clé, le `"type"` clé qui est utilisé pour identifier le type d’élément à créer.
Certains éléments partagent certaines propriétés communes comme `"caption"` et `"value"`. Il s’agit de la liste des éléments pris en charge :

-  `string` éléments (à la fois avec et sans style)
-  `entry` lignes (standard ou mot de passe)
-  `boolean` valeurs (à l’aide des commutateurs ou des images)


Éléments de chaîne qui peuvent être utilisés en tant que boutons en fournissant une méthode à appeler lorsque l’utilisateur appuie sur la cellule ou l’accessoire,

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Rendu des éléments

Les éléments de rendu sont basés sur le C# StringElement et StyledStringElement et ils peuvent rendre les informations de différentes manières, et il est possible d’effectuer le rendu de différentes manières. Les éléments plus simples peuvent être créés comme suit :

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Cette commande affiche une chaîne simple avec toutes les valeurs par défaut : police, arrière-plan, couleur de texte et décorations. Il est possible de raccorder des actions à ces éléments et les rendre se comportent comme des boutons en définissant le `"ontap"` propriété ou le `"onaccessorytap"` propriétés :

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

Appelle la méthode « ShowPhotos » dans la classe « Acme.PhotoLibrary » ci-dessus. Le `"onaccessorytap"` est similaire, mais il sera uniquement appelé si l’utilisateur appuie sur l’accessoire au lieu d’appuyant sur la cellule. Pour ce faire, vous devez également définir l’accessoire :

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Rendu des éléments peut afficher deux chaînes en une seule fois, une est la légende et un autre est la valeur. Comment ces chaînes sont rendus varient selon le style, vous pouvez définir cette option à l’aide de la `"style"` propriété. La valeur par défaut affiche la légende à gauche et la valeur située à droite. Sur le style pour plus d’informations, consultez la section. Couleurs sont encodés à l’aide du symbole « # » suivi de nombres hexadécimaux qui représentent les valeurs pour les valeurs rouges, verts, bleu et alpha peut-être. Le contenu peut être encodé sous la forme abrégée (3 ou 4 des chiffres hexadécimaux) qui représente les valeurs RVB ou RVBA. Ou la forme longue (6 ou 8 chiffres) qui représentent les valeurs RVB ou RVBA. La version courte est un raccourci pour écrire le même chiffre hexadécimal à deux reprises. Par conséquent, la constante « #1bc » est interprétés en rouge = 0 x 11, vert = 0xbb et bleu = 0xcc. Si la valeur alpha n’est pas présente, la couleur est opaque. Voici quelques exemples :

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>ACCESSOIRE

Détermine le type d’accessoire à afficher dans votre élément de rendu, les valeurs possibles sont :

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Si la valeur n’est pas présente, aucun accessoire n’est indiqué.

 <a name="background" />


### <a name="background"></a>arrière-plan

La propriété background définit la couleur d’arrière-plan de la cellule. La valeur est soit une URL vers une image (dans ce cas, le Téléchargeur d’image asynchrone est appelé et l’arrière-plan sera mis à jour une fois que l’image soit téléchargée) ou il peut être une couleur spécifiée à l’aide de la syntaxe de couleur.

 <a name="caption" />


### <a name="caption"></a>caption

La chaîne principale à afficher sur l’élément de rendu. La police et la couleur peuvent être personnalisés en définissant le `"textcolor"` et `"font"` propriétés. Le style de rendu est déterminé par le `"style"` propriété.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>couleur et detailcolor

La couleur à utiliser pour le texte principal ou le texte détaillé.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>DetailFont et la police

La police à utiliser pour la légende ou le texte de détail. Le format d’une spécification de la police est le nom de police suivi éventuellement d’un tiret et la taille en points.
Spécifications de police valides sont les suivantes :

-  « Helvetica »
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>LineBreak

Détermine la façon dont les lignes sont divisés. Les valeurs possibles sont :

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


Les deux `character-wrap` et `word-wrap` peut être utilisé conjointement avec le `"lines"` propriété est définie sur zéro pour activer l’élément de rendu dans un élément de plusieurs lignes.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ONTAP et onaccessorytap

Ces propriétés doivent pointer vers un nom de méthode statique dans votre application qui prend un objet en tant que paramètre. Lorsque vous créez votre hiérarchie à l’aide des méthodes JsonDialog.FromFile ou JsonDialog.FromJson, vous pouvez passer une valeur d’objet facultatif. Valeur de cet objet est ensuite passée à vos méthodes. Vous pouvez utiliser cela pour transmettre un contexte à votre méthode statique. Exemple :

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines" />


### <a name="lines"></a>lignes

Si la valeur est zéro, cela rendra la taille automatique à un élément en fonction du contenu des chaînes contenues. Pour ce faire, vous devez également définir le `"linebreak"` propriété `"character-wrap"` ou `"word-wrap"`.

 <a name="style" />


### <a name="style"></a>style

Le style détermine le type de style de cellule qui sera utilisé pour restituer le contenu et qu’ils correspondent aux valeurs d’énumération de UITableViewCellStyle.
Les valeurs possibles sont :

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` : texte avec un sous-titre.


 <a name="subtitle" />


### <a name="subtitle"></a>sous-titre

La valeur à utiliser pour le sous-titre. Il s’agit d’un raccourci pour définir le style `"subtitle"` et définir le `"value"` en une chaîne.
Cela fait à la fois avec une seule entrée.

 <a name="textcolor" />


### <a name="textcolor"></a>TextColor

La couleur à utiliser pour le texte.

 <a name="value" />


### <a name="value"></a>par défaut

La valeur secondaire à afficher sur l’élément de rendu. La disposition de ce qui est affectée par la `"style"` paramètre. La police et la couleur peuvent être personnalisés en définissant le `"detailfont"` et `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Éléments Boolean contenus

Éléments Boolean contenus doivent définir le type sur `"bool"`, peut contenir un `"caption"` pour afficher et le `"value"` est définie sur true ou false. Si le `"on"` et `"off"` propriétés sont définies, elles sont supposées pour être des images. Les images sont résolues par rapport au répertoire de travail actuel dans l’application. Si vous souhaitez référencer relatifs à un groupe de fichiers, vous pouvez utiliser le `"~"` sous forme de raccourci pour représenter le répertoire du bundle d’application. Par exemple `"~/favorite.png"` sera favorite.png qui est contenue dans le fichier d’offre groupée. Exemple :

```json
{ 
    "type": "boolean",
    "caption": "Demo of a Boolean",
    "value": true
},

{
    "type": "boolean",
    "caption": "Boolean using images",
    "value": false,
    "on": "favorite.png",
    "off": "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>type

Type peut être défini avec la valeur `"boolean"` ou `"checkbox"`. Si la valeur de type boolean il utilisera un UISlider ou des images (si les deux `"on"` et `"off"` sont définis). Si la valeur checkbox, il utilisera une case à cocher. Le `"group"` propriété peut être utilisée pour marquer un élément booléen comme appartenant à un groupe particulier. Cela est utile si la conteneur racine possède également un `"group"` la racine de la propriété résume les résultats avec un nombre de toutes les valeurs booléennes (ou les cases à cocher) qui appartiennent au même groupe.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Éléments d’entrée

Vous utilisez des éléments d’entrée pour autoriser l’utilisateur à entrer des données. Le type d’éléments d’entrée est `"entry"` ou `"password"`. Le `"caption"` propriété est définie sur le texte à afficher sur la droite et le `"value"` est défini sur la valeur initiale pour définir l’entrée. Le `"placeholder"` est utilisé pour afficher un indicateur à l’utilisateur pour les entrées vides (il apparaît grisé). Voici quelques exemples :

```json
{
    "type": "entry",
    "caption": "Username",
    "value": "",
    "placeholder": "Your account username"
}, {
    "type": "password",
    "caption": "Password",
    "value": "",
    "placeholder": "You password"
}, {
    "type": "entry",
    "caption": "Zip Code",
    "value": "01010",
    "placeholder": "your zip code",
    "keyboard": "numbers"
}, {
    "type": "entry",
    "return-key": "route",
    "caption": "Entry with 'route'",
    "placeholder": "captialization all + no corrections",
    "capitalization": "all",
    "autocorrect": "no"
}
```

 <a name="autocorrect" />


### <a name="autocorrect"></a>autocorrect

Détermine le style de la correction automatique à utiliser pour l’entrée. Les valeurs possibles sont true ou false (ou les chaînes `"yes"` et `"no"`).

 <a name="capitalization" />


### <a name="capitalization"></a>mise en majuscules

Le style de mise en majuscules à utiliser pour l’entrée. Les valeurs possibles sont :

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>caption

La légende à utiliser pour l’entrée

 <a name="keyboard" />


### <a name="keyboard"></a>clavier

Le type de clavier à utiliser pour la saisie de données. Les valeurs possibles sont :

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>espace réservé

Le texte d’indication qui s’affiche lorsque l’entrée a une valeur vide.

 <a name="return-key" />


### <a name="return-key"></a>touche de retour

L’étiquette utilisée pour la touche Retour. Les valeurs possibles sont :

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>par défaut

La valeur initiale pour l’entrée

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>Éléments de case d’option

Type des éléments de case d’option est `"radio"`. L’élément sélectionné est récupéré par le `radioselected` propriété sur son élément conteneur de la racine.
En outre, si une valeur est définie pour le `"group"` propriété, cette case d’option appartient à ce groupe.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Date et heure éléments

Les types d’élément `"datetime"`, `"date"` et `"time"` sont utilisés pour restituer les dates avec des délais, dates ou heures. Ces éléments prennent comme paramètres une légende et une valeur. La valeur peut être écrite dans n’importe quel format pris en charge par la fonction .NET DateTime.Parse. Exemple :

```json
"header": "Dates and Times",
"elements": [
    {
        "type": "datetime",
        "caption": "Date and Time",
        "value": "Sat, 01 Nov 2008 19:35:00 GMT"
    }, {
        "type": "date",
        "caption": "Date",
        "value": "10/10"
    }, {
        "type": "time",
        "caption": "Time",
        "value": "11:23"
    }                       
]
```

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>Élément HTML/Web

Vous pouvez créer une cellule que lorsque j’appuie incorpore un UIWebView qui restitue le contenu d’une URL spécifiée, local ou distant à l’aide du `"html"` type. Les deux seules propriétés pour cet élément sont `"caption"` et `"url"`:

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
