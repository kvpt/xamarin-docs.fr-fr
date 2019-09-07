---
title: Présentation de la boîte de dialogue MonoTouch. Xamarin. iOS
description: Ce document décrit Hypertouch. Dialog (MT. D), une infrastructure pour le développement d’IU rapide et déclaratif avec Xamarin. iOS. Il explique comment utiliser les API monotactiles pour créer une interface dans le code ou JSON et utiliser des fonctionnalités telles que l’extraction, la recherche, le chargement d’images d’arrière-plan et bien plus encore.
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: conceptdev
ms.author: crdun
ms.openlocfilehash: cbe1b374b97f64e0c28b2f89ca9f6d510511b74d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768845"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>Présentation de la boîte de dialogue MonoTouch. Xamarin. iOS

Dialogue monotactile, appelé MT. D pour résumer, est une boîte à outils de développement d’interface utilisateur rapide qui permet aux développeurs de créer des écrans d’application et de naviguer à l’aide d’informations, plutôt que de caractère fastidieux de créer des contrôleurs d’affichage, des tables, etc. En tant que tel, il offre une simplification significative du développement de l’interface utilisateur et de la réduction du code. Par exemple, examinez la capture d’écran suivante :

 [![](images/image1.png "Par exemple, considérez cette capture d’écran")](images/image1.png#lightbox)

Le code suivant a été utilisé pour définir la totalité de l’écran :

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

Lorsque vous travaillez avec des tables dans iOS, il y a souvent un peu de code répétitive.
Par exemple, chaque fois qu’une table est nécessaire, une source de données est nécessaire pour remplir cette table. Dans une application qui possède deux écrans basés sur des tables qui sont connectés via un contrôleur de navigation, chaque écran partage un grand nombre du même code.

MT. D simplifie cela en encapsulant tout ce code dans une API générique pour la création de table. Il fournit ensuite une abstraction au-dessus de cette API qui permet une syntaxe de liaison d’objet déclarative qui la rend encore plus facile. Par conséquent, deux API sont disponibles dans MT. E

- **API d’éléments de bas niveau** : l' *API Elements* est basée sur la création d’une arborescence d’éléments qui représentent des écrans et leurs composants. L’API Elements offre aux développeurs le plus de flexibilité et de contrôle dans la création d’interfaces utilisateur. En outre, l’API Elements a une prise en charge avancée de la définition déclarative via JSON, ce qui permet une déclaration extrêmement rapide, ainsi qu’une génération dynamique d’interface utilisateur à partir d’un serveur. 
- **API de réflexion de haut niveau** : également connu sous le *liaison* *API* , dans les classes sont annotées avec des indications d’interface utilisateur, puis le serveur cible maître. D automatiquement crée des écrans basés sur les objets et fournit une liaison entre ce qui est affichée (et éventuellement modifiée) sur l’écran, et l’objet sous-jacent sauvegarde. L’exemple ci-dessus illustre l’utilisation de l’API de réflexion. Cette API ne fournit pas le contrôle affiné que fait l’API Elements, mais elle réduit encore davantage la complexité en générant automatiquement la hiérarchie d’éléments en fonction des attributs de classe. 

MT. D est compressé avec un grand nombre d’éléments d’interface utilisateur intégrés pour la création d’écran, mais il reconnaît également la nécessité d’éléments personnalisés et de dispositions d’écran avancées. Par conséquent, l’extensibilité est une première classe intégrée à l’API. Les développeurs peuvent étendre les éléments existants ou en créer de nouveaux, puis s’intégrer en toute transparence.

En outre, MT. D possède un certain nombre de fonctionnalités d’expérience utilisateur iOS communes intégrées, telles que la prise en charge de l’extraction vers l’actualisation, le chargement d’images asynchrones et la prise en charge de la recherche.

Cet article présente en détail l’utilisation de la solution MT. D, y compris :

- **Mt. Composants D** : il est axé sur la compréhension des classes qui composent Mt. D pour permettre une mise à niveau rapide. 
- **Référence des éléments** : liste complète des éléments intégrés de Mt. E. 
- **Utilisation avancée** : inclut des fonctionnalités avancées telles que l’extraction vers l’actualisation, la recherche, le chargement d’images d’arrière-plan, l’utilisation de LINQ pour créer des hiérarchies d’éléments et la création d’éléments, de cellules et de contrôleurs personnalisés pour une utilisation avec Mt. E. 

## <a name="setting-up-mtd"></a>Configuration de MT. E

MT. D est distribué avec Xamarin. iOS. Pour l’utiliser, cliquez avec le bouton droit sur le nœud **références** d’un projet Xamarin. iOS dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à l’assembly **MonoTouch. boîte de dialogue-1** . Ajoutez `using MonoTouch.Dialog` ensuite des instructions dans votre code source, si nécessaire.

## <a name="understanding-the-pieces-of-mtd"></a>Comprendre les éléments de MT. E

Même en cas d’utilisation de l’API de réflexion, MT. D crée une hiérarchie d’éléments sous le capot, comme s’il avait été créé directement via l’API Elements. En outre, la prise en charge de JSON mentionnée dans la section précédente crée également des éléments. Pour cette raison, il est important d’avoir une compréhension de base des composants de MT. E.

MT. D génère des écrans à l’aide des quatre parties suivantes :

- **DialogViewController**
- **RootElement**
- **Section**
- **Élément**

### <a name="dialogviewcontroller"></a>DialogViewController

Un *DialogViewController*, ou *DVC* pour Short, hérite de `UITableViewController` et, par conséquent, représente un écran avec une table. Menés par peut faire l’objet d’un push sur un contrôleur de navigation comme un UITableViewController normal.

### <a name="rootelement"></a>RootElement

Un *RootElement* est le conteneur de niveau supérieur pour les éléments qui passent dans un DVC. Elle contient des sections qui peuvent ensuite contenir des éléments. Les RootElements ne sont pas restitués ; au lieu de cela, il s’agit simplement de conteneurs pour ce qui est réellement rendu. Un RootElement est attribué à un DVC, puis le DVC affiche ses enfants.

### <a name="section"></a>Section

Une section est un groupe de cellules dans un tableau. Comme avec une section de table normale, il peut éventuellement avoir un en-tête et un pied de page qui peuvent être du texte, ou même des vues personnalisées, comme dans la capture d’écran suivante :

 [![](images/image2.png "Comme avec une section de table normale, il peut éventuellement avoir un en-tête et un pied de page qui peuvent être du texte ou même des vues personnalisées, comme dans cette capture d’écran.")](images/image2.png#lightbox)

### <a name="element"></a>Élément

Un élément représente une cellule réelle dans la table. MT. D est compressé avec une grande variété d’éléments qui représentent des types de données différents ou des entrées différentes. Par exemple, les captures d’écran suivantes illustrent quelques-uns des éléments disponibles :

 [![](images/image3.png "Par exemple, ces captures d’écran illustrent quelques-uns des éléments disponibles")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>En savoir plus sur les sections et RootElements

Nous allons maintenant aborder les RootElements et les sections de manière plus détaillée.

### <a name="rootelements"></a>RootElements

Au moins un RootElement est requis pour démarrer le processus MonoTouch.

Si un RootElement est initialisé avec une valeur de section/élément, cette valeur est utilisée pour rechercher un élément enfant qui fournira un résumé de la configuration, qui est rendue à droite de l’affichage. Par exemple, la capture d’écran ci-dessous montre un tableau sur la gauche avec une cellule contenant le titre de l’écran de détails à droite, « dessert », ainsi que la valeur du désert sélectionné.

 [![](images/image4.png "Cette capture d’écran montre une table sur la gauche avec une cellule qui contient le titre de l’écran de détails sur la droite, Dessert, ainsi que la valeur de la desert sélectionné")](images/image4.png#lightbox) [![](images/image5.png "cela capture d’écran ci-dessous illustre une table sur la gauche, avec une cellule qui contient le titre de l’écran de détails sur la droite, Dessert, ainsi que la valeur de la desert sélectionné")](images/image5.png#lightbox)

Les éléments racine peuvent également être utilisés dans les sections pour déclencher le chargement d’une nouvelle page de configuration imbriquée, comme indiqué ci-dessus. En cas d’utilisation dans ce mode, la légende fournie est utilisée tout en étant rendue à l’intérieur d’une section et est également utilisée comme titre de la sous-page. Exemple :

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner") {
        new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
            new Section () {
                new RadioElement ("Ice Cream", "dessert"),
                new RadioElement ("Milkshake", "dessert"),
                new RadioElement ("Chocolate Cake", "dessert")
            }
        }
    }
};
```

Dans l’exemple ci-dessus, quand l’utilisateur appuie sur « dessert », le dialogue monotactile crée une nouvelle page et y accède avec la racine « dessert » et un groupe radio avec trois valeurs.

Dans cet exemple, le groupe de radios sélectionne « Chocolate gâteau » dans la section « dessert », car nous avons transmis la valeur « 2 » au groupe de radiogroupe. Cela signifie sélectionner le troisième élément de la liste (index zéro).

L’appel de la méthode Add ou C# l’utilisation de la syntaxe 4 initializer ajoute des sections.
Les méthodes d’insertion sont fournies pour insérer des sections à l’aide d’une animation.

Si vous créez le RootElement avec une instance de groupe (au lieu d’un groupe de radiogroupe), la valeur de résumé du RootElement affiché dans une section sera le nombre cumulé de tous les BooleanElements et CheckboxElements qui ont la même clé que la valeur Group. Key.

### <a name="sections"></a>Sections

Les sections sont utilisées pour regrouper des éléments dans l’écran et sont les seuls enfants directs valides de RootElement. Les sections peuvent contenir l’un des éléments standard, y compris les nouveaux RootElements.

RootElements Embedded dans une section est utilisé pour accéder à un nouveau niveau plus profond.

Les sections peuvent avoir des en-têtes et des pieds de page en tant que chaînes, ou en tant que UIViews.
En général, vous utilisez simplement les chaînes, mais pour créer des interfaces utilisateur personnalisées, vous pouvez utiliser n’importe quel UIView comme en-tête ou pied de page. Vous pouvez utiliser une chaîne pour les créer comme suit :

```csharp
var section = new Section ("Header", "Footer");
```

Pour utiliser des vues, il vous suffit de transmettre les vues au constructeur :

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header);
```

### <a name="getting-notified"></a>Recevoir des notifications

#### <a name="handling-nsaction"></a>Gestion de NSAction

MT. D surface un `NSAction` en tant que délégué pour gérer les rappels.
Par exemple, imaginons que vous souhaitez gérer un événement tactile pour une cellule de table créée par MT. E. Lors de la création d’un élément avec MT. D, fournissez simplement une fonction de rappel, comme indiqué ci-dessous :

```csharp
new Section () {
    new StringElement ("Demo Callback", delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Récupération de la valeur de l’élément

Combiné avec la `Element.Value` propriété, le rappel peut récupérer la valeur définie dans d’autres éléments. Considérons par exemple le code suivant :

```csharp
var element = new EntryElement (task.Name, "Enter task description", task.Description);
                
var taskElement = new RootElement (task.Name) {
    new Section () { element },
    new Section () { new DateElement ("Due Date", task.DueDate) },
    new Section ("Demo Retrieving Element Value") {
        new StringElement ("Output Task Description", delegate { Console.WriteLine (element.Value); })
    }
};
```

Ce code crée une interface utilisateur comme indiqué ci-dessous. Pour une procédure pas à pas complète de cet exemple, consultez le didacticiel [procédure pas à pas d’API d’éléments](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) .

 [![](images/image6.png "Combiné à la propriété Element. Value, le rappel peut récupérer la valeur définie dans d’autres éléments")](images/image6.png#lightbox)

Quand l’utilisateur appuie sur la cellule de table inférieure, le code de la fonction anonyme s’exécute, en écrivant la `element` valeur de l’instance dans le bloc de sortie de l' **application** dans Visual Studio pour Mac.

## <a name="built-in-elements"></a>Éléments intégrés

MT. D est fourni avec un certain nombre d’éléments de cellule de table intégrés appelés éléments.
Ces éléments sont utilisés pour afficher divers types différents dans les cellules de table, tels que les chaînes, les valeurs float, les dates et même les images, pour n’en nommer que quelques-uns. Chaque élément s’occupe de l’affichage du type de données de manière appropriée. Par exemple, un élément booléen affiche un commutateur pour basculer sa valeur. De même, un élément float affiche un curseur pour modifier la valeur float.

Il existe encore des éléments plus complexes pour prendre en charge des types de données plus riches, tels que des images et du html. Par exemple, un élément HTML, qui ouvre un UIWebView pour charger une page Web lorsque cette option est sélectionnée, affiche une légende dans la cellule du tableau.

### <a name="working-with-element-values"></a>Utilisation des valeurs d’élément

Les éléments utilisés pour capturer l’entrée d’utilisateur exposent à tout moment une propriété publique `Value` qui contient la valeur actuelle de l’élément. Elle est automatiquement mise à jour lorsque l’utilisateur utilise l’application.

Il s’agit du comportement de tous les éléments qui font partie de la boîte de dialogue MonoTouch. Toutefois, il n’est pas obligatoire pour les éléments créés par l’utilisateur.

### <a name="string-element"></a>Élément String

Un `StringElement` affiche une légende à gauche d’une cellule de tableau et la valeur de chaîne sur le côté droit de la cellule.

 [![](images/image7.png "Un StringElement affiche une légende sur le côté gauche d’une cellule de tableau et la valeur de chaîne sur le côté droit de la cellule.")](images/image7.png#lightbox)

Pour utiliser un `StringElement` comme bouton, fournissez un délégué.

```csharp
new StringElement ("Click me", () => { 
    new UIAlertView("Tapped", "String Element Tapped", null, "ok", null).Show();
});
```

 [![](images/image8.png "Pour utiliser un StringElement en tant que bouton, fournissez un délégué")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Élément de chaîne stylisé

Un `StyledStringElement` permet de présenter des chaînes à l’aide de styles de cellules de tableau intégrés ou d’une mise en forme personnalisée.

 [![](images/image9.png "Un StyledStringElement permet de présenter des chaînes à l’aide de styles de cellule de table intégrés ou d’une mise en forme personnalisée")](images/image9.png#lightbox)

La `StyledStringElement` classe dérive de `StringElement`, mais permet aux développeurs de personnaliser quelques propriétés telles que la police, la couleur de texte, la couleur de cellule d’arrière-plan, le mode de saut de ligne, le nombre de lignes à afficher et si un accessoire doit être affiché.

### <a name="multiline-element"></a>Élément Multiline

 [![](images/image10.png "Élément Multiline")](images/image10.png#lightbox)

### <a name="entry-element"></a>Élément Entry

Comme `EntryElement`son nom l’indique, est utilisé pour récupérer l’entrée d’utilisateur. Il prend en charge les chaînes normales ou les mots de passe, où les caractères sont masqués.

 [![](images/image11.png "EntryElement est utilisé pour récupérer l’entrée d’utilisateur")](images/image11.png#lightbox)

Elle est initialisée avec trois valeurs :

- Légende de l’entrée qui sera affichée à l’utilisateur.
- Texte d’espace réservé (il s’agit du texte grisé qui fournit une indication à l’utilisateur). 
- Valeur du texte.

L’espace réservé et la valeur peuvent être null. Toutefois, la légende est requise.

À tout moment, l’accès à sa propriété Value peut récupérer la valeur de `EntryElement`.

En outre, `KeyboardType` la propriété peut être définie au moment de la création en lui attribuant le style de type de clavier souhaité pour l’entrée de données. Cela peut être utilisé pour configurer le clavier à l’aide des `UIKeyboardType` valeurs de comme indiqué ci-dessous :

- Numérique
- Phone
- URL
- Messagerie

### <a name="boolean-element"></a>Élément booléen

 [![](images/image12.png "Élément booléen")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>CheckBox, élément

 [![](images/image13.png "CheckBox, élément")](images/image13.png#lightbox)

### <a name="radio-element"></a>Élément radio

Un `RadioElement` requiert qu' `RadioGroup` un soit spécifié dans le `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0));
```

 [![](images/image14.png "Un radioélément nécessite la spécification d’un groupe de radiogroupe dans le RootElement")](images/image14.png#lightbox)

 `RootElements`sont également utilisés pour coordonner les éléments radio. Les `RadioElement` membres peuvent s’étendre sur plusieurs sections (par exemple, pour implémenter un nom similaire au sélecteur de sonnerie de sonnerie et séparer les tonalités personnalisées des sonneries système). La vue Résumé affiche l’élément radio actuellement sélectionné. Pour ce faire, créez le `RootElement` avec le constructeur de groupe, comme suit :

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0));
```

Le nom du groupe dans `RadioGroup` est utilisé pour afficher la valeur sélectionnée dans la page conteneur (le cas échéant) et la valeur, qui est égale à zéro dans ce cas, est l’index du premier élément sélectionné.

### <a name="badge-element"></a>Badge, élément

 [![](images/image15.png "Badge, élément")](images/image15.png#lightbox)

### <a name="float-element"></a>Élément float

 [![](images/image16.png "Élément float")](images/image16.png#lightbox)

### <a name="activity-element"></a>Élément Activity

 [![](images/image17.png "Élément Activity")](images/image17.png#lightbox)

### <a name="date-element"></a>Élément Date

 ![](images/image18.png "Élément Date")

Lorsque la cellule correspondant à l’DateElement est sélectionnée, un sélecteur de dates est présenté comme indiqué ci-dessous :

 [![](images/image19.png "Lorsque la cellule correspondant à l’DateElement est sélectionnée, un sélecteur de dates est présenté comme indiqué")](images/image19.png#lightbox)

### <a name="time-element"></a>Élément Time

 [![](images/image20.png "Élément Time")](images/image20.png#lightbox)

Lorsque la cellule correspondant à l’TimeElement est sélectionnée, un sélecteur d’heure est présenté comme indiqué ci-dessous :

 [![](images/image21.png "Lorsque la cellule correspondant à l’TimeElement est sélectionnée, un sélecteur d’heure est présenté comme indiqué")](images/image21.png#lightbox)

### <a name="datetime-element"></a>Élément DateTime

 [![](images/image22.png "Élément DateTime")](images/image22.png#lightbox)

Lorsque la cellule correspondant à l’DateTimeElement est sélectionnée, un sélecteur de date/heure est présenté comme indiqué ci-dessous :

 [![](images/image23.png "Lorsque la cellule correspondant à l’DateTimeElement est sélectionnée, un sélecteur de date/heure est présenté comme indiqué")](images/image23.png#lightbox)

### <a name="html-element"></a>Élément HTML

 [![](images/image24.png "Élément HTML")](images/image24.png#lightbox)

Affiche la valeur de sa `Caption` propriété dans la cellule de tableau. `HTMLElement` Whe sélectionné, le `Url` assigné à l’élément est chargé dans un `UIWebView` contrôle comme indiqué ci-dessous :

 [![](images/image25.png "Whe sélectionné, l’URL assignée à l’élément est chargée dans un contrôle UIWebView comme indiqué ci-dessous")](images/image25.png#lightbox)

### <a name="message-element"></a>Message, élément

 [![](images/image26.png "Message, élément")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Charger plus d’éléments

Utilisez cet élément pour permettre aux utilisateurs de charger plus d’éléments dans votre liste. Vous pouvez personnaliser les sous-titres normal et de chargement, ainsi que la police et la couleur du texte.
L' `UIActivity` indicateur démarre l’animation et la légende de chargement s’affiche lorsqu’un utilisateur appuie sur la cellule, puis le `NSAction` passé dans le constructeur est exécuté. Une fois votre code `NSAction` terminé, l' `UIActivity` indicateur cesse de s’animer et la légende normale s’affiche à nouveau.

### <a name="uiview-element"></a>Élément UIView

En outre, n’importe `UIView` quel personnalisé peut être affiché `UIViewElement`à l’aide de.

### <a name="owner-drawn-element"></a>Élément owner-drawn

Cet élément doit être sous-classé, car il s’agit d’une classe abstraite. Vous devez substituer la `Height(RectangleF bounds)` méthode dans laquelle vous devez retourner la hauteur de l’élément, ainsi que `Draw(RectangleF bounds, CGContext context, UIView view)` dans lequel vous devez effectuer tous vos dessins personnalisés dans les limites spécifiées, à l’aide des paramètres Context et View. Cet élément fait le plus gros du sous-classement d' `UIView`un et le place dans la cellule pour être retourné, ce qui vous permet de ne pas avoir à implémenter deux remplacements simples. Vous pouvez voir un meilleur exemple d’implémentation dans l’exemple d’application `DemoOwnerDrawnElement.cs` dans le fichier.

Voici un exemple très simple d’implémentation de la classe :

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
{
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text { get; set; }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
}
```

### <a name="json-element"></a>Élément JSON

Est une sous-classe de `RootElement` qui étend un `RootElement` pour pouvoir charger le contenu d’un enfant imbriqué à partir d’une URL locale ou distante. `JsonElement`

Le `JsonElement` est un `RootElement` qui peut être instancié sous deux formes. Une version crée un `RootElement` qui chargera le contenu à la demande. Celles-ci sont créées à `JsonElement` l’aide des constructeurs qui prennent un argument supplémentaire à la fin, l’URL à partir de laquelle charger le contenu :

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

L’autre formulaire crée les données à partir d’un fichier local ou `System.Json.JsonObject` d’un existant que vous avez déjà analysé :

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Pour plus d’informations sur l’utilisation de JSON avec MT. D, consultez le didacticiel relatif [à la procédure pas à pas d’élément JSON](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) .

## <a name="other-features"></a>Autres fonctionnalités

### <a name="pull-to-refresh-support"></a>Prise en charge de l’extraction pour l’actualisation

 *Pull-to-* L' *actualisation* est un effet visuel identifié à l’origine dans l’application *Tweetie2* , qui est devenu un effet populaire parmi de nombreuses applications.

Pour ajouter la prise en charge de l’extraction automatique à vos boîtes de dialogue, vous devez effectuer deux opérations : raccorder un gestionnaire d’événements pour être averti lorsque l’utilisateur extrait les données et notifier le `DialogViewController` moment où les données ont été chargées pour revenir à son état par défaut.

Le raccordement d’une notification est simple ; Connectez-vous simplement `RefreshRequested` à l’événement `DialogViewController`sur le, comme suit :

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Ensuite, sur votre `OnUserRequestedRefresh`méthode, vous pouvez effectuer la mise en file d’attente d’un chargement de données, demander des données à partir du réseau ou faire tourner un thread pour calculer les données. Une fois les données chargées, vous devez notifier `DialogViewController` la présence des nouvelles données et rétablir l’État par défaut de la vue, en appelant : `ReloadComplete`

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Prise en charge de la recherche

Pour prendre en charge la recherche `EnableSearch` , définissez la `DialogViewController`propriété sur votre. Vous pouvez également définir la `SearchPlaceholder` propriété à utiliser comme texte de filigrane dans la barre de recherche.

La recherche va modifier le contenu de la vue au fur et à mesure que l’utilisateur tape. Il recherche les champs visibles et les affiche à l’utilisateur. Le `DialogViewController` expose trois méthodes pour lancer, terminer ou déclencher par programmation une nouvelle opération de filtre sur les résultats. Ces méthodes sont répertoriées ci-dessous :

- `StartSearch`
- `FinishSearch`
- `PerformFilter`

Le système étant extensible, vous pouvez modifier ce comportement si vous le souhaitez.

### <a name="background-image-loading"></a>Chargement de l’image d’arrière-plan

La boîte de dialogue unitouch. incorpore le chargeur d’image de l’application [TweetStation](https://github.com/migueldeicaza/TweetStation) . Ce chargeur d’image peut être utilisé pour charger des images en arrière-plan, prend en charge la mise en cache et peut notifier votre code lorsque l’image a été chargée.

Il limite également le nombre de connexions réseau sortantes.

Le chargeur d’image est implémenté dans la `ImageLoader` classe. il vous suffit d’appeler la `DefaultRequestImage` méthode, vous devez fournir l’URI de l’image que vous souhaitez charger, ainsi qu’une instance de l' `IImageUpdated` interface qui sera appelée lorsque la haute disponibilité de l’image ont été chargés.

Par exemple, le code suivant charge une image à partir d’une `BadgeElement`URL dans un :

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
    new Section() {
        new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
    }
};
```

La classe ImageLoader expose une méthode de vidage que vous pouvez appeler lorsque vous souhaitez libérer toutes les images qui sont actuellement mises en cache en mémoire. Le code actuel possède un cache pour les images 50. Si vous souhaitez utiliser une autre taille de cache (par exemple, si vous prévoyez que les images sont trop volumineuses pour que 50 images soient trop nombreuses), vous pouvez simplement créer des instances de ImageLoader et transmettre le nombre d’images que vous souhaitez conserver dans le cache.

## <a name="using-linq-to-create-element-hierarchy"></a>Utilisation de LINQ pour créer une hiérarchie d’éléments

Grâce à l’utilisation astucieuse de LINQ C#et de la syntaxe d’initialisation de LINQ, LINQ peut être utilisé pour créer une hiérarchie d’éléments. Par exemple, le code suivant crée un écran à partir de certains tableaux de chaînes et gère la sélection des cellules via une fonction anonyme qui `StringElement`est transmise à chaque :

```csharp
var rootElement = new RootElement ("LINQ root element") {
    from x in new string [] { "one", "two", "three" }
    select new Section (x) {
        from y in "Hello:World".Split (':')
        select (Element) new StringElement (y, delegate { Debug.WriteLine("cell tapped"); })
    }
};
```

Cela peut être facilement combiné avec un magasin de données XML ou des données d’une base de données pour créer des applications complexes presque entièrement à partir de données.

## <a name="extending-mtd"></a>Extension de MT. E

### <a name="creating-custom-elements"></a>Création d’éléments personnalisés

Vous pouvez créer votre propre élément en héritant d’un élément existant ou en dérivant de l’élément de classe racine.

Pour créer votre propre élément, vous pouvez substituer les méthodes suivantes :

```csharp
// To release any heavy resources that you might have
void Dispose (bool disposing);

// To retrieve the UITableViewCell for your element
// you would need to prepare the cell to be reused, in the
// same way that UITableView expects reusable cells to work
UITableViewCell GetCell (UITableView tv);

// To retrieve a "summary" that can be used with
// a root element to render a summary one level up.  
string Summary ();

// To detect when the user has tapped on the cell
void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path);

// If you support search, to probe if the cell matches the user input
bool Matches (string text);
```

Si votre élément peut avoir une taille variable, vous devez implémenter l' `IElementSizing` interface, qui contient une méthode :

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Si vous envisagez d’implémenter votre `GetCell` méthode en appelant `base.GetCell(tv)` et en personnalisant la cellule retournée, vous devez également `CellKey` substituer la propriété pour retourner une clé qui sera unique à votre élément, comme suit :

```csharp
static NSString MyKey = new NSString ("MyKey");
protected override NSString CellKey {
    get {
        return MyKey;
    }
}
```

Cela fonctionne pour la plupart des éléments, mais pas `StringElement` pour `StyledStringElement` le et, car ceux-ci utilisent leur propre ensemble de clés pour différents scénarios de rendu. Vous devez répliquer le code dans ces classes.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (menés par)

La réflexion et l’API des éléments utilisent le même `DialogViewController`. Parfois, vous souhaiterez personnaliser l’apparence de la vue ou utiliser certaines fonctionnalités du `UITableViewController` qui vont au-delà de la création de base des interfaces utilisateur.

Est simplement une sous-classe `UITableViewController` du et vous pouvez la personnaliser de la même façon `UITableViewController`que vous le feriez. `DialogViewController`

Par exemple, si vous souhaitez modifier le style de liste pour `Grouped` qu’il soit ou `Plain`, vous pouvez définir cette valeur en modifiant la propriété lors de la création du contrôleur, comme suit :

```csharp
var myController = new DialogViewController (root, true) {
    Style = UITableViewStyle.Grouped;
}
```

Pour les `DialogViewController`personnalisations plus avancées du, telles que la définition de son arrière-plan, vous devez la sous-classer et remplacer les méthodes appropriées, comme illustré dans l’exemple ci-dessous :

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

Un autre point de personnalisation est l’une des méthodes virtuelles suivantes dans le `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Cette méthode doit retourner une sous-classe `DialogViewController.Source` de pour les cas où vos cellules sont de taille égale ou une sous- `DialogViewController.SizingSource` classe de si vos cellules sont inégales.

Vous pouvez utiliser ce remplacement pour capturer toutes les `UITableViewSource` méthodes. Par exemple, [TweetStation](https://github.com/migueldeicaza/TweetStation) utilise cette fonction pour suivre le moment où l’utilisateur a fait défiler vers le haut et mettre à jour en conséquence le nombre de tweets non lus.

## <a name="validation"></a>Validation

Les éléments ne fournissent pas de validation proprement dit, car les modèles qui conviennent pour les pages Web et les applications de bureau ne sont pas directement mappés au modèle d’interaction iPhone.

Si vous souhaitez effectuer la validation des données, vous devez le faire lorsque l’utilisateur déclenche une action avec les données entrées. Par exemple`StringElement` , un bouton **terminé** ou suivant dans la barre d’outils supérieure, ou un bouton utilisé comme bouton pour passer à l’étape suivante.

C’est là que vous effectuez une validation des entrées de base, et peut-être une validation plus compliquée comme la vérification de la validité d’une combinaison utilisateur/mot de passe avec un serveur.

La façon dont vous informez l’utilisateur d’une erreur est spécifique à l’application. Vous pouvez `UIAlertView` afficher un ou un indicateur.

## <a name="summary"></a>Récapitulatif

Cet article a traité de nombreuses informations sur les dialogues monotactiles. Il a abordé les principes de base du fonctionnement de l’MT. D fonctionne et traite des différents composants qui composent le MT. E. Elle a également montré la vaste gamme d’éléments et de personnalisations de table prise en charge par MT. D et a abordé la manière dont MT. D peut être étendu avec des éléments personnalisés. En outre, il a expliqué la prise en charge de JSON dans MT. D, qui permet de créer dynamiquement des éléments à partir de JSON.

## <a name="related-links"></a>Liens associés

- [Procédure pas à pas : création d’une application à l’aide de l’API Elements](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procédure pas à pas : création d’une application à l’aide de l’API Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procédure pas à pas : Utilisation d’un élément JSON pour créer une interface utilisateur](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Balisage JSON de la boîte de dialogue.](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Boîte de dialogue MonoTouch sur GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Référence de la classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de la classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
