---
title: MonoTouch.Dialog Json Markup
description: Ce document décrit la syntaxe JSON qui peut être utilisée pour générer une interface utilisateur Xamarin. iOS à l’aide de la boîte de dialogue.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 023a85451ca83df6c15e8b3bbc3169f2884a0a46
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936563"
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json Markup

Cette page décrit le balisage JSON accepté par Hypertouch. [JsonElement](xref:MonoTouch.Dialog.JsonElement) de la boîte de dialogue

Commençons par un exemple. Voici un fichier JSON complet qui peut être passé dans JsonElement.

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

Le balisage ci-dessus produit l’interface utilisateur suivante :

 [![Interface utilisateur créée par le balisage donné](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png)](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Chaque élément de l’arborescence peut contenir la propriété `"id"` . Il est possible lors de l’exécution de référencer des sections ou des éléments individuels à l’aide de l’indexeur JsonElement. Comme ceci :

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax"></a>

## <a name="root-element-syntax"></a>Syntaxe d’élément racine

L’élément racine contient les valeurs suivantes :

- `title`
- `sections` (facultatif)

L’élément racine peut apparaître à l’intérieur d’une section en tant qu’élément pour créer un contrôleur imbriqué. Dans ce cas, la propriété supplémentaire `"type"` doit avoir la valeur`"root"`

 <a name="url"></a>

### <a name="url"></a>url

Si la `"url"` propriété est définie, si l’utilisateur appuie sur ce RootElement, le code demandera un fichier à partir de l’URL spécifiée et fera en sorte que le contenu soit affiché dans les nouvelles informations. Vous pouvez l’utiliser pour créer étendre l’interface utilisateur à partir du serveur en fonction de ce que l’utilisateur appuie.

 <a name="group"></a>

### <a name="group"></a>group

Si cette valeur est définie, le GroupName de l’élément racine est défini. Les noms de groupes sont utilisés pour choisir un résumé affiché en tant que valeur de l’élément racine à partir de l’un des éléments imbriqués dans l’élément. Il s’agit de la valeur d’une case à cocher ou de la valeur d’une case d’option.

 <a name="radioselected"></a>

### <a name="radioselected"></a>radiosélectionné

Identifie l’élément radio sélectionné dans les éléments imbriqués

 <a name="title"></a>

### <a name="title"></a>title

S’il est présent, il s’agit du titre utilisé pour le RootElement

 <a name="type"></a>

### <a name="type"></a>type

Doit avoir la valeur `"root"` lorsque ce apparaît dans une section (utilisé pour imbriquer des contrôleurs).

 <a name="sections"></a>

### <a name="sections"></a>sections

Il s’agit d’un tableau JSON avec des sections individuelles

 <a name="Section_Syntax"></a>

## <a name="section-syntax"></a>Syntaxe de la section

La section contient les éléments suivants :

- `header` (facultatif)
- `footer` (facultatif)
- `elements` tableau

 <a name="header"></a>

### <a name="header"></a>en-tête

S’il est présent, le texte d’en-tête est affiché sous la forme d’une légende de la section.

 <a name="footer"></a>

### <a name="footer"></a>pied de page

S’il est présent, le pied de page est affiché en bas de la section.

 <a name="elements"></a>

### <a name="elements"></a>elements

Il s’agit d’un tableau d’éléments. Chaque élément doit contenir au moins une clé, la `"type"` clé utilisée pour identifier le type d’élément à créer.
Certains éléments partagent des propriétés communes telles que `"caption"` et `"value"` . Voici la liste des éléments pris en charge :

- `string`éléments (avec et sans style)
- `entry`lignes (régulière ou mot de passe)
- `boolean`valeurs (à l’aide de commutateurs ou d’images)

Les éléments de chaîne peuvent être utilisés en tant que boutons en fournissant une méthode à appeler lorsque l’utilisateur appuie sur la cellule ou sur l’accessoire.

 <a name="Rendering_Elements"></a>

## <a name="rendering-elements"></a>Éléments de rendu

Les éléments de rendu sont basés sur les StringElement et StyledStringElement C#, et ils peuvent restituer des informations de différentes façons et il est possible de les afficher de différentes façons. Les éléments les plus simples peuvent être créés comme suit :

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Cette opération affiche une chaîne simple avec toutes les valeurs par défaut : police, arrière-plan, couleur de texte et décorations. Il est possible de raccorder des actions à ces éléments et de faire en sorte qu’ils se comportent comme des boutons en définissant la `"ontap"` propriété ou les `"onaccessorytap"` Propriétés :

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

Le code ci-dessus appellera la méthode « ShowPhotos » dans la classe « Acme. photolibrary ». La `"onaccessorytap"` méthode est similaire, mais elle est appelée uniquement si l’utilisateur appuie sur l’accessoire au lieu de cliquer sur la cellule. Pour ce faire, vous devez également définir l’accessoire :

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Les éléments de rendu peuvent afficher deux chaînes à la fois, l’une est la légende et l’autre la valeur. La façon dont ces chaînes sont rendues dépend du style, vous pouvez définir cela à l’aide de la `"style"` propriété. La valeur par défaut affiche la légende à gauche et la valeur à droite. Pour plus d’informations, consultez la section relative au style. Les couleurs sont encodées à l’aide du symbole « # » suivi de nombres hexadécimaux qui représentent les valeurs des valeurs alpha, rouge, vert, bleu et peut-être. Le contenu peut être encodé sous forme abrégée (3 ou 4 chiffres hexadécimaux) qui représente des valeurs RVB ou RVBA. Ou la forme longue (6 ou 8 chiffres) qui représentent des valeurs RVB ou RVBA. La version abrégée est un raccourci pour écrire deux fois le même chiffre hexadécimal. La constante « #1bc » est donc intepreted comme rouge = 0x11, Green = 0xBB et Blue = 0xcc. Si la valeur alpha n’est pas présente, la couleur est opaque. Exemples :

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory"></a>

### <a name="accessory"></a>équipée

Détermine le type d’accessoire à afficher dans votre élément de rendu, les valeurs possibles sont :

- `checkmark`
- `detail-disclosure`
- `disclosure-indicator`

Si la valeur n’est pas présente, aucun accessoire n’est affiché

 <a name="background"></a>

### <a name="background"></a>background

La propriété Background définit la couleur d’arrière-plan de la cellule. La valeur est soit une URL vers une image (dans ce cas, le téléchargeur d’images Async sera appelé et l’arrière-plan sera mis à jour une fois l’image téléchargée), soit une couleur spécifiée à l’aide de la syntaxe de couleur.

 <a name="caption"></a>

### <a name="caption"></a>caption

Chaîne principale à afficher sur l’élément de rendu. La police et la couleur peuvent être personnalisées en définissant les `"textcolor"` `"font"` Propriétés et. Le style de rendu est déterminé par la `"style"` propriété.

 <a name="color_and_detailcolor"></a>

### <a name="color-and-detailcolor"></a>Color et detailcolor

Couleur à utiliser pour le texte principal ou le texte détaillé.

 <a name="detailfont_and_font"></a>

### <a name="detailfont-and-font"></a>DetailFont et police

Police à utiliser pour la légende ou le texte de détail. Le format d’une spécification de police est le nom de la police, suivi éventuellement d’un tiret et de la taille du point.
Les spécifications de police valides sont les suivantes :

- Helvetica
- « Helvetica-14 »

 <a name="linebreak"></a>

### <a name="linebreak"></a>linebreak

Détermine la façon dont les lignes sont réparties. Les valeurs possibles sont les suivantes :

- `character-wrap`
- `clip`
- `head-truncation`
- `middle-truncation`
- `tail-truncation`
- `word-wrap`

`character-wrap`Et `word-wrap` peuvent être utilisés avec la `"lines"` propriété définie sur zéro pour transformer l’élément de rendu en élément à plusieurs lignes.

 <a name="ontap_and_onaccessorytap"></a>

### <a name="ontap-and-onaccessorytap"></a>ONTAP et onaccessorytap

Ces propriétés doivent pointer vers un nom de méthode statique dans votre application qui prend un objet comme paramètre. Lorsque vous créez votre hiérarchie à l’aide des méthodes JsonDialog. FromFile ou JsonDialog. FromJson, vous pouvez passer une valeur d’objet facultative. Cette valeur d’objet est ensuite transmise à vos méthodes. Vous pouvez l’utiliser pour passer du contexte à votre méthode statique. Par exemple :

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

 <a name="lines"></a>

### <a name="lines"></a>lignes

Si la valeur est égale à zéro, l’élément est automatiquement redimensionné en fonction du contenu des chaînes contenues. Pour que cela fonctionne, vous devez également affecter à la propriété la valeur `"linebreak"` `"character-wrap"` ou `"word-wrap"` .

 <a name="style"></a>

### <a name="style"></a>style

Le style détermine le type de style de cellule qui sera utilisé pour afficher le contenu et il correspond aux valeurs d’énumération UITableViewCellStyle.
Les valeurs possibles sont les suivantes :

- `"default"`
- `"value1"`
- `"value2"`
- `"subtitle"`: texte avec un sous-titre.

 <a name="subtitle"></a>

### <a name="subtitle"></a>subtitle

Valeur à utiliser pour le sous-titre. Il s’agit d’un raccourci permettant de définir le style sur `"subtitle"` et de définir la `"value"` propriété sur une chaîne.
Cela se fait avec une seule entrée.

 <a name="textcolor"></a>

### <a name="textcolor"></a>TextColor

Couleur à utiliser pour le texte.

 <a name="value"></a>

### <a name="value"></a>value

Valeur secondaire à afficher sur l’élément de rendu. La disposition de ce est affectée par le `"style"` paramètre. La police et la couleur peuvent être personnalisées en définissant les paramètres `"detailfont"` et `"detailcolor"` .

 <a name="Boolean_Elements"></a>

## <a name="boolean-elements"></a>Éléments booléens

Les éléments booléens doivent définir le type avec la valeur `"bool"` , peut contenir un `"caption"` à afficher et `"value"` a la valeur true ou false. Si les `"on"` `"off"` Propriétés et sont définies, elles sont supposées être des images. Les images sont résolues par rapport au répertoire de travail actuel de l’application. Si vous souhaitez référencer des fichiers relatifs à un bundle, vous pouvez utiliser le `"~"` comme raccourci pour représenter le répertoire de l’ensemble d’applications. Par exemple `"~/favorite.png"` , est le favorite.png contenu dans le fichier de bundle. Par exemple :

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

 <a name="type"></a>

### <a name="type"></a>type

Le type peut être défini sur `"boolean"` ou `"checkbox"` . Si la valeur est booléenne, elle utilise un UISlider ou des images (si et sont tous les deux `"on"` `"off"` définis). Si la valeur est, elle utilise une case à cocher. La `"group"` propriété peut être utilisée pour baliser un élément booléen comme appartenant à un groupe particulier. Cela est utile si la racine contenante a également une `"group"` propriété, car la racine résume les résultats avec le nombre de tous les booléens (ou cases à cocher) qui appartiennent au même groupe.

 <a name="Entry_Elements"></a>

## <a name="entry-elements"></a>Éléments d’entrée

Vous utilisez des éléments d’entrée pour permettre à l’utilisateur d’entrer des données. Le type des éléments d’entrée est `"entry"` ou `"password"` . La `"caption"` propriété est définie sur le texte à afficher à droite, et `"value"` est défini sur la valeur initiale pour affecter à l’entrée. `"placeholder"`Est utilisé pour afficher une indication à l’utilisateur pour les entrées vides (il est affiché en grisé). Voici quelques exemples :

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

 <a name="autocorrect"></a>

### <a name="autocorrect"></a>correction automatique

Détermine le style de correction automatique à utiliser pour l’entrée. Les valeurs possibles sont true ou false (ou les chaînes `"yes"` et `"no"` ).

 <a name="capitalization"></a>

### <a name="capitalization"></a>mise en majuscules

Style de mise en majuscules à utiliser pour l’entrée. Les valeurs possibles sont les suivantes :

- `all`
- `none`
- `sentences`
- `words`

 <a name="caption"></a>

### <a name="caption"></a>caption

Légende à utiliser pour l’entrée

 <a name="keyboard"></a>

### <a name="keyboard"></a>clavier

Type de clavier à utiliser pour l’entrée de données. Les valeurs possibles sont les suivantes :

- `ascii`
- `decimal`
- `default`
- `email`
- `name`
- `numbers`
- `numbers-and-punctuation`
- `twitter`
- `url`

 <a name="placeholder"></a>

### <a name="placeholder"></a>espace réservé

Texte d’indication qui s’affiche lorsque l’entrée a une valeur vide.

 <a name="return-key"></a>

### <a name="return-key"></a>clé de retour

Étiquette utilisée pour la clé de retour. Les valeurs possibles sont les suivantes :

- `default`
- `done`
- `emergencycall`
- `go`
- `google`
- `join`
- `next`
- `route`
- `search`
- `send`
- `yahoo`

 <a name="value"></a>

### <a name="value"></a>value

Valeur initiale de l’entrée.

 <a name="Radio_Elements"></a>

## <a name="radio-elements"></a>Éléments radio

Les éléments radio ont le type `"radio"` . L’élément sélectionné est choisi par la `radioselected` propriété sur son élément racine conteneur.
En outre, si une valeur est définie pour la `"group"` propriété, cette case d’option appartient à ce groupe.

 <a name="Date_and_Time_Elements"></a>

## <a name="date-and-time-elements"></a>Éléments de date et d’heure

Les types d’éléments `"datetime"` , `"date"` et `"time"` sont utilisés pour restituer des dates avec des heures, des dates ou des heures. Ces éléments prennent comme paramètres une légende et une valeur. La valeur peut être écrite dans n’importe quel format pris en charge par la fonction .NET DateTime. Parse. Exemple :

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

 <a name="Html/Web_Element"></a>

## <a name="htmlweb-element"></a>Élément HTML/Web

Vous pouvez créer une cellule qui, lorsqu’il est taraudé, incorporera un UIWebView qui restitue le contenu d’une URL spécifiée, locale ou distante, à l’aide du `"html"` type. Les deux seules propriétés de cet élément sont `"caption"` et `"url"` :

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
