---
title: Liaison de données et codage de clé-valeur dans Xamarin. Mac
description: Cet article aborde l’utilisation du codage clé-valeur et de l’observation clé-valeur pour permettre la liaison de données aux éléments d’interface utilisateur dans les Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 81a1f63078a5f7a2a70f731d1790f85f4283d22f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304995"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Liaison de données et codage de clé-valeur dans Xamarin. Mac

_Cet article aborde l’utilisation du codage clé-valeur et de l’observation clé-valeur pour permettre la liaison de données aux éléments d’interface utilisateur dans les Interface Builder de Xcode._

## <a name="overview"></a>Vue d’ensemble

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes techniques de codage et de liaison de données que les développeurs qui travaillent en *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser les _Interface Builder_ de Xcode pour la liaison de données avec des éléments d’interface utilisateur au lieu d’écrire du code.

En utilisant le codage clé-valeur et les techniques de liaison de données dans votre application Xamarin. Mac, vous pouvez réduire la quantité de code que vous devez écrire et maintenir pour remplir et utiliser des éléments d’interface utilisateur. Vous avez également l’avantage de dissocier vos données de stockage (modèle de_données_) de votre interface utilisateur frontale (_Model-View-Controller_), ce qui vous permet de gérer plus facilement la conception d’applications plus souple.

[![Exemple d’application en cours d’exécution](databinding-images/intro01.png "Exemple d’application en cours d’exécution")](databinding-images/intro01-large.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation du codage clé-valeur et de la liaison de données dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les attributs `Register` et `Export` utilisés pour relier vos C# classes aux objets objective-c et aux éléments d’interface utilisateur.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>Qu’est-ce que le codage clé-valeur ?

Le codage de clé-valeur (KVC) est un mécanisme permettant d’accéder indirectement aux propriétés d’un objet, à l’aide de clés (chaînes spécialement mises en forme) pour identifier les propriétés au lieu d’y accéder via des variables d’instance ou des méthodes d’accesseur (`get/set`). En implémentant des accesseurs conformes de codage de clé-valeur dans votre application Xamarin. Mac, vous accédez à d’autres fonctionnalités macOS (anciennement appelées OS X), telles que l’observation de la valeur clé (KVO), la liaison de données, les données de base, les liaisons de cacao et la scriptabilité.

En utilisant le codage clé-valeur et les techniques de liaison de données dans votre application Xamarin. Mac, vous pouvez réduire la quantité de code que vous devez écrire et maintenir pour remplir et utiliser des éléments d’interface utilisateur. Vous avez également l’avantage de dissocier vos données de stockage (modèle de_données_) de votre interface utilisateur frontale (_Model-View-Controller_), ce qui vous permet de gérer plus facilement la conception d’applications plus souple.

Par exemple, examinons la définition de classe suivante d’un objet conforme KVC :

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

Tout d’abord, l’attribut `[Register("PersonModel")]` inscrit la classe et l’expose à Objective-C. Ensuite, la classe doit hériter de `NSObject` (ou d’une sous-classe qui hérite de `NSObject`), cela ajoute plusieurs méthodes de base qui permettent à la classe d’être conforme KVC. Ensuite, l’attribut `[Export("Name")]` expose la propriété `Name` et définit la valeur de clé qui sera utilisée ultérieurement pour accéder à la propriété via les techniques KVC et KVO.

Enfin, pour être en mesure de modifier la valeur de la propriété, l’accesseur doit encapsuler les modifications apportées à sa valeur dans `WillChangeValue` et `DidChangeValue` appels de méthode (en spécifiant la même clé que l’attribut `Export`).  Par exemple :

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Cette étape est _très_ importante pour la liaison de données dans le Interface Builder de Xcode (comme nous le verrons plus loin dans cet article).

Pour plus d’informations, consultez [le Guide de programmation de codage clé-valeur](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)d’Apple.

### <a name="keys-and-key-paths"></a>Clés et chemins d’accès aux clés

Une _clé_ est une chaîne qui identifie une propriété spécifique d’un objet. En général, une clé correspond au nom d’une méthode d’accesseur dans un objet conforme au codage de clé-valeur. Les clés doivent utiliser l’encodage ASCII, elles commencent généralement par une lettre minuscule et ne peuvent pas contenir d’espaces blancs. Ainsi, étant donné l’exemple ci-dessus, `Name` serait une valeur de clé de `Name` propriété de la classe `PersonModel`. La clé et le nom de la propriété qu’elles exposent n’ont pas besoin d’être identiques. Toutefois, dans la plupart des cas, ils sont.

Un _chemin d’accès de clé_ est une chaîne de clés séparées par des points, utilisée pour spécifier une hiérarchie de propriétés d’objet à parcourir. La propriété de la première clé de la séquence est relative au récepteur, et chaque clé suivante est évaluée par rapport à la valeur de la propriété précédente. De la même façon que vous utilisez la notation par points pour parcourir un objet et ses propriétés C# dans une classe.

Par exemple, si vous avez développé la classe `PersonModel` et ajouté `Child` propriété :

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

Le chemin d’accès de la clé vers le nom de l’enfant est `self.Child.Name` ou simplement `Child.Name` (en fonction de la façon dont la valeur de clé a été utilisée).

### <a name="getting-values-using-key-value-coding"></a>Obtention de valeurs à l’aide du codage clé-valeur

La méthode `ValueForKey` retourne la valeur de la clé spécifiée (sous forme de `NSString`), par rapport à l’instance de la classe KVC recevant la demande. Par exemple, si `Person` est une instance de la classe `PersonModel` définie ci-dessus :

```csharp
// Read value
var name = Person.ValueForKey (new NSString("Name"));
```

Cela retourne la valeur de la propriété `Name` pour cette instance de `PersonModel`.

### <a name="setting-values-using-key-value-coding"></a>Définition de valeurs à l’aide du codage clé-valeur

De même, le `SetValueForKey` définir la valeur de la clé spécifiée (en tant que `NSString`), par rapport à l’instance de la classe KVC recevant la demande. À nouveau, à l’aide d’une instance de la classe `PersonModel`, comme indiqué ci-dessous :

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Affectez à la propriété `Name` la valeur `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Observation des modifications de valeur

En observant la valeur Key-value (KVO), vous pouvez attacher un observateur à une clé spécifique d’une classe conforme à KVC et recevoir une notification chaque fois que la valeur de cette clé est modifiée (à l’aide de techniques KVC ou C# d’un accès direct à la propriété donnée dans le code). Par exemple :

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

À présent, chaque fois que la propriété `Name` de l’instance `Person` de la classe `PersonModel` est modifiée, la nouvelle valeur est écrite dans la console.

Pour plus d’informations, consultez la [présentation d’Apple pour le Guide de programmation de la valeur Key-value](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Liaison de données

Les sections suivantes montrent comment utiliser une classe de codage clé-valeur et une classe conforme d’observation de clé-valeur pour lier des données aux éléments d’interface utilisateur dans le Interface Builder de Xcode, au lieu C# de lire et d’écrire des valeurs à l’aide du code. De cette façon, vous séparez votre _modèle de données_ des vues utilisées pour les afficher, ce qui rend l’application Xamarin. Mac plus flexible et plus facile à gérer. Vous réduisez également beaucoup la quantité de code qui doit être écrite.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Définition de votre modèle de données

Avant de pouvoir lier des données à un élément d’interface utilisateur dans Interface Builder, vous devez disposer d’une classe conforme KVC/KVO définie dans votre application Xamarin. Mac pour agir en tant que _modèle de données_ pour la liaison. Le modèle de données fournit toutes les données qui seront affichées dans l’interface utilisateur et reçoit toutes les modifications apportées aux données que l’utilisateur effectue dans l’interface utilisateur lors de l’exécution de l’application.

Par exemple, si vous écrivez une application qui gérait un groupe d’employés, vous pouvez utiliser la classe suivante pour définir le modèle de données :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

La plupart des fonctionnalités de cette classe étaient traitées dans la section [qu’est-ce que le codage de clé-valeur](#What_is_Key-Value_Coding) ci-dessus. Toutefois, observons quelques éléments spécifiques et quelques ajouts qui ont été effectués pour permettre à cette classe d’agir comme un modèle de données pour les **contrôleurs de tableau** et les **contrôleurs d’arborescence** (que nous utiliserons ultérieurement pour les vues de l' **arborescence**des liaisons de données, les **vues en mode plan** et les **vues de collection**).

Tout d’abord, étant donné qu’un employé peut être responsable, nous avons utilisé une `NSArray` (plus particulièrement `NSMutableArray` pour que les valeurs puissent être modifiées) afin d’autoriser les employés qu’ils ont gérés à être attachés :

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Deux points à noter :

1. Nous avons utilisé un `NSMutableArray` au lieu d' C# un tableau ou d’une collection standard, car il s’agit d’une exigence de liaison de données aux contrôles AppKit tels que les **vues de table**, les **vues de plan** et les **Collections**.
2. Nous avons exposé le tableau des employés en les convertissant à un `NSArray` à des fins de liaison de C# données et nous avons modifié leur nom mis en forme, `People`, en l’équivalent de la liaison de données, `personModelArray` au format **{class_name} tableau** (Notez que le premier caractère a été fait en minuscules).

Ensuite, nous devons ajouter des méthodes publiques de nom spécialement pour prendre en charge les contrôleurs de **groupe** et les **contrôleurs d’arborescence**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Celles-ci permettent aux contrôleurs de demander et de modifier les données qu’ils affichent. À l’instar de la `NSArray` exposée ci-dessus, elles ont une convention d’affectation C# de noms très spécifique (qui diffère des conventions de nommage standard) :

- `addObject:` : ajoute un objet au tableau.
- `insertObject:in{class_name}ArrayAtIndex:`-où `{class_name}` est le nom de votre classe. Cette méthode insère un objet dans le tableau à un index donné.
- `removeObjectFrom{class_name}ArrayAtIndex:`-où `{class_name}` est le nom de votre classe. Cette méthode supprime l’objet dans le tableau au niveau d’un index donné.
- `set{class_name}Array:`-où `{class_name}` est le nom de votre classe. Cette méthode vous permet de remplacer le port existant par un nouveau.

Dans ces méthodes, nous avons encapsulé les modifications apportées au tableau dans `WillChangeValue` et `DidChangeValue` messages pour la conformité KVO.

Enfin, étant donné que la propriété `Icon` repose sur la valeur de la propriété `isManager`, les modifications apportées à la propriété `isManager` peuvent ne pas être reflétées dans le `Icon` pour les éléments d’interface utilisateur liés aux données (pendant KVO) :

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
```

Pour corriger cela, nous utilisons le code suivant :

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

Notez qu’en plus de sa propre clé, l’accesseur `isManager` envoie également les messages `WillChangeValue` et `DidChangeValue` pour la clé de `Icon`, de sorte qu’il voit également la modification.

Nous utiliserons le modèle de données `PersonModel` dans le reste de cet article.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Liaison de données simple

Avec notre modèle de données défini, nous allons examiner un exemple simple de liaison de données dans le Interface Builder de Xcode. Par exemple, nous allons ajouter un formulaire à notre application Xamarin. Mac qui peut être utilisé pour modifier la `PersonModel` que nous avons définie ci-dessus. Nous allons ajouter quelques champs de texte et une case à cocher pour afficher et modifier les propriétés de notre modèle.

Tout d’abord, nous allons ajouter un nouveau **contrôleur d’affichage** à notre fichier **main. Storyboard** dans Interface Builder et nommer sa classe `SimpleViewController`:

[![Ajout d’un nouveau contrôleur d’affichage](databinding-images/simple01.png "Ajout d’un nouveau contrôleur d’affichage")](databinding-images/simple01-large.png#lightbox)

Ensuite, revenez à Visual Studio pour Mac, modifiez le fichier **SimpleViewController.cs** (qui a été automatiquement ajouté à notre projet) et exposez une instance du `PersonModel` que nous allons lier les données à notre formulaire. Ajoutez le code suivant :

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

Ensuite, lorsque la vue est chargée, nous allons créer une instance de notre `PersonModel` et la remplir avec ce code :

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

Nous devons maintenant créer notre formulaire, double-cliquer sur le fichier **main. Storyboard** pour l’ouvrir et le modifier dans Interface Builder. Mettez en forme le formulaire de façon à ce qu’il ressemble à ce qui suit :

[![Modification de la table de montage séquentiel dans Xcode](databinding-images/simple02.png "Modification de la table de montage séquentiel dans Xcode")](databinding-images/simple02-large.png#lightbox)

Pour lier le formulaire au `PersonModel` que nous avons exposé via la clé `Person`, procédez comme suit :

1. Sélectionnez le champ de texte nom de l' **employé** et basculez vers l' **inspecteur de liaisons**.
2. Cochez la case **lier à** et sélectionnez **contrôleur d’affichage simple** dans la liste déroulante. Entrez ensuite `self.Person.Name` pour le **chemin d’accès**de la clé :

    [![Saisie du chemin d’accès de la clé](databinding-images/simple03.png "Saisie du chemin d’accès de la clé")](databinding-images/simple03-large.png#lightbox)
3. Sélectionnez le champ texte de l' **occupation** et cochez la case **lier à** et sélectionnez **contrôleur d’affichage simple** dans la liste déroulante. Entrez ensuite `self.Person.Occupation` pour le **chemin d’accès**de la clé :

    [![Saisie du chemin d’accès de la clé](databinding-images/simple04.png "Saisie du chemin d’accès de la clé")](databinding-images/simple04-large.png#lightbox)
4. Activez la case à cocher l' **employé est un responsable** et activez la case à cocher **lier à** et sélectionnez **contrôleur d’affichage simple** dans la liste déroulante. Entrez ensuite `self.Person.isManager` pour le **chemin d’accès**de la clé :

    [![Saisie du chemin d’accès de la clé](databinding-images/simple05.png "Saisie du chemin d’accès de la clé")](databinding-images/simple05-large.png#lightbox)
5. Sélectionnez le champ **de texte nombre d’employés gérés** et cochez la case **lier à** et sélectionnez **contrôleur d’affichage simple** dans la liste déroulante. Entrez ensuite `self.Person.NumberOfEmployees` pour le **chemin d’accès**de la clé :

    [![Saisie du chemin d’accès de la clé](databinding-images/simple06.png "Saisie du chemin d’accès de la clé")](databinding-images/simple06-large.png#lightbox)
6. Si l’employé n’est pas un responsable, nous souhaitons masquer le champ nombre d’employés et de texte de l’étiquette gérée.
7. Sélectionnez l’étiquette **gérée nombre d’employés** , développez le Turndown **masqué** , puis activez la case à cocher **lier à** et sélectionnez **contrôleur d’affichage simple** dans la liste déroulante. Entrez ensuite `self.Person.isManager` pour le **chemin d’accès**de la clé :

    [![Saisie du chemin d’accès de la clé](databinding-images/simple07.png "Saisie du chemin d’accès de la clé")](databinding-images/simple07-large.png#lightbox)
8. Sélectionnez `NSNegateBoolean` dans la liste déroulante **transformateur de valeur** :

    ![Sélection de la transformation de clé NSNegateBoolean](databinding-images/simple08.png "Sélection de la transformation de clé NSNegateBoolean")
9. Cela indique à la liaison de données que l’étiquette sera masquée si la valeur de la propriété `isManager` est `false`.
10. Répétez les étapes 7 et 8 pour le champ **de texte nombre d’employés gérés** .
11. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Si vous exécutez l’application, les valeurs de la propriété `Person` remplissent automatiquement notre formulaire :

[![Présentation d’un formulaire rempli automatiquement](databinding-images/simple09.png "Présentation d’un formulaire rempli automatiquement")](databinding-images/simple09-large.png#lightbox)

Toutes les modifications apportées par les utilisateurs au formulaire sont réécrites dans la propriété `Person` dans le contrôleur d’affichage. Par exemple, le fait de désélectionner **un employé est un responsable** met à jour l’instance de `Person` de notre `PersonModel` et le champ de texte et l’étiquette **gérée du nombre d’employés** sont masqués automatiquement (via la liaison de données) :

[![Masquage du nombre d’employés pour les non-responsables](databinding-images/simple10.png "Masquage du nombre d’employés pour les non-responsables")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Liaison de données d’affichage de table

Maintenant que nous avons les principes fondamentaux de la liaison de données, examinons une tâche de liaison de données plus complexe à l’aide d’un _contrôleur de tableau_ et d’une liaison de données à une vue de table. Pour plus d’informations sur l’utilisation des vues de table, consultez notre documentation sur les [vues de tableau](~/mac/user-interface/table-view.md) .

Tout d’abord, nous allons ajouter un nouveau **contrôleur d’affichage** à notre fichier **main. Storyboard** dans Interface Builder et nommer sa classe `TableViewController`:

[![Ajout d’un nouveau contrôleur d’affichage](databinding-images/table01.png "Ajout d’un nouveau contrôleur d’affichage")](databinding-images/table01-large.png#lightbox)

Ensuite, nous allons modifier le fichier **TableViewController.cs** (qui a été automatiquement ajouté à notre projet) et exposer un tableau (`NSArray`) de `PersonModel` classes sur lesquelles nous allons lier les données de notre formulaire. Ajoutez le code suivant :

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Comme nous l’avons fait sur la classe `PersonModel` ci-dessus dans la section [définition de votre modèle de données](#Defining_your_Data_Model) , nous avons exposé quatre méthodes publiques spécialement nommées afin que le contrôleur de tableau et lisent et écrivent des données à partir de notre collection de `PersonModels`.

Ensuite, lorsque la vue est chargée, nous devons remplir notre tableau avec ce code :

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Nous devons maintenant créer notre vue de table, double-cliquer sur le fichier **main. Storyboard** pour l’ouvrir et le modifier dans Interface Builder. Mettez en page le tableau pour qu’il ressemble à ce qui suit :

[![Disposition d’une nouvelle vue de table](databinding-images/table02.png "Disposition d’une nouvelle vue de table")](databinding-images/table02-large.png#lightbox)

Nous devons ajouter un **contrôleur de baie** pour fournir des données liées à notre table, procédez comme suit :

1. Faites glisser un **contrôleur de tableau** de l’inspecteur de **bibliothèque** vers l’éditeur d' **interface**:

    ![Sélection d’un contrôleur de groupe à partir de la bibliothèque](databinding-images/table03.png "Sélection d’un contrôleur de groupe à partir de la bibliothèque")
2. Sélectionnez **contrôleur de groupe** dans la **hiérarchie d’interface** et basculez vers l’inspecteur d' **attribut**:

    [![Sélection de l’inspecteur d’attributs](databinding-images/table04.png "Sélection de l’inspecteur d’attributs")](databinding-images/table04-large.png#lightbox)
3. Entrez `PersonModel` pour le **nom**de la classe, cliquez sur le bouton **plus** et ajoutez trois clés. Nommez-les `Name`, `Occupation` et `isManager`:

    ![Ajout des chemins de clé requis](databinding-images/table05.png "Ajout des chemins de clé requis")
4. Cela indique au contrôleur de groupe ce qu’il gère un tableau de, ainsi que les propriétés qu’il doit exposer (via des clés).
5. Basculez vers **l’inspecteur de liaisons** et sous le tableau de **contenu** , sélectionnez **lier à** et contrôleur d' **affichage de table**. Entrez un **chemin d’accès de clé de modèle** `self.personModelArray`:

    ![Entrée d’un chemin d’accès de clé](databinding-images/table06.png "Entrée d’un chemin d’accès de clé")
6. Cela lie le contrôleur de tableau au tableau de `PersonModels` que nous avons exposé sur notre contrôleur d’affichage.

Nous devons maintenant lier notre vue de table au contrôleur de baie, procédez comme suit :

1. Sélectionnez la vue de table et l' **inspecteur de liaison**:

    [![Sélection de l’inspecteur de liaison](databinding-images/table07.png "Sélection de l’inspecteur de liaison")](databinding-images/table07-large.png#lightbox)
2. Sous le **contenu** de la table Turndown, sélectionnez **lier à** et **contrôleur de tableau**. Entrez `arrangedObjects` pour le champ **clé du contrôleur** :

    ![Définition de la clé de contrôleur](databinding-images/table08.png "Définition de la clé de contrôleur")
3. Sélectionnez la **cellule d’affichage de table** sous la colonne **Employee** . Dans l' **inspecteur de liaisons** sous la **valeur** Turndown, sélectionnez **lier à** et **vue de cellule de table**. Entrez `objectValue.Name` pour le **chemin d’accès**de la clé de modèle :

    [![Définition du chemin d’accès de la clé de modèle](databinding-images/table09.png "Définition du chemin d’accès de la clé de modèle")](databinding-images/table09-large.png#lightbox)
4. `objectValue` est le `PersonModel` actuel dans le tableau géré par le contrôleur de tableau.
5. Sélectionnez la **cellule d’affichage de table** sous la colonne **occupation** . Dans l' **inspecteur de liaisons** sous la **valeur** Turndown, sélectionnez **lier à** et **vue de cellule de table**. Entrez `objectValue.Occupation` pour le **chemin d’accès**de la clé de modèle :

    [![Définition du chemin d’accès de la clé de modèle](databinding-images/table10.png "Définition du chemin d’accès de la clé de modèle")](databinding-images/table10-large.png#lightbox)
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Si nous exécutons l’application, la table est remplie avec le tableau de `PersonModels`:

[![Exécution de l’application](databinding-images/table11.png "Exécution de l'application")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>Liaison de données vue Structure

la liaison de données par rapport à un mode plan est très similaire à la liaison à une vue de table. La principale différence réside dans le fait que nous allons utiliser un **contrôleur d’arborescence** au lieu d’un **contrôleur de tableau** pour fournir les données liées à la vue en mode plan. Pour plus d’informations sur l’utilisation des vues en mode plan, consultez notre documentation sur les [modes plan](~/mac/user-interface/outline-view.md) .

Tout d’abord, nous allons ajouter un nouveau **contrôleur d’affichage** à notre fichier **main. Storyboard** dans Interface Builder et nommer sa classe `OutlineViewController`:

[![Ajout d’un nouveau contrôleur d’affichage](databinding-images/outline01.png "Ajout d’un nouveau contrôleur d’affichage")](databinding-images/outline01-large.png#lightbox)

Ensuite, nous allons modifier le fichier **OutlineViewController.cs** (qui a été automatiquement ajouté à notre projet) et exposer un tableau (`NSArray`) de `PersonModel` classes sur lesquelles nous allons lier les données de notre formulaire. Ajoutez le code suivant :

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Comme nous l’avons fait sur la classe `PersonModel` ci-dessus dans la section [définition de votre modèle de données](#Defining_your_Data_Model) , nous avons exposé quatre méthodes publiques spécialement nommées afin que le contrôleur d’arborescence et lisent et écrivent des données à partir de notre collection de `PersonModels`.

Ensuite, lorsque la vue est chargée, nous devons remplir notre tableau avec ce code :

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

Nous devons maintenant créer notre mode plan, double-cliquer sur le fichier **main. Storyboard** pour l’ouvrir et le modifier dans Interface Builder. Mettez en page le tableau pour qu’il ressemble à ce qui suit :

[![Création du mode plan](databinding-images/outline02.png "Création du mode plan")](databinding-images/outline02-large.png#lightbox)

Nous devons ajouter un **contrôleur d’arborescence** pour fournir des données liées à notre plan, procédez comme suit :

1. Faites glisser un **contrôleur d’arborescence** de l’inspecteur de **bibliothèque** vers l’éditeur d' **interface**:

    ![Sélection d’un contrôleur d’arborescence dans la bibliothèque](databinding-images/outline03.png "Sélection d’un contrôleur d’arborescence dans la bibliothèque")
2. Sélectionnez **contrôleur d’arborescence** dans la **hiérarchie d’interface** et basculez vers l’inspecteur d' **attribut**:

    [![Sélection de l’inspecteur d’attribut](databinding-images/outline04.png "Sélection de l’inspecteur d’attribut")](databinding-images/outline04-large.png#lightbox)
3. Entrez `PersonModel` pour le **nom**de la classe, cliquez sur le bouton **plus** et ajoutez trois clés. Nommez-les `Name`, `Occupation` et `isManager`:

    ![Ajout des chemins de clé requis](databinding-images/outline05.png "Ajout des chemins de clé requis")
4. Cela indique au contrôleur d’arborescence ce qu’il gère un tableau et les propriétés qu’il doit exposer (via des clés).
5. Dans la section **contrôleur d’arborescence** , entrez `personModelArray` pour **enfants**, entrez `NumberOfEmployees` sous le **nombre** et entrez `isEmployee` sous **feuille**:

    ![Définition des chemins d’accès de clé du contrôleur d’arborescence](databinding-images/outline05.png "Définition des chemins d’accès de clé du contrôleur d’arborescence")
6. Cela indique au contrôleur d’arborescence où trouver les nœuds enfants, le nombre de nœuds enfants et si le nœud actuel possède des nœuds enfants.
7. Basculez vers **l’inspecteur de liaisons** et, sous le tableau de **contenu** , sélectionnez **lier au** **propriétaire du fichier**. Entrez un **chemin d’accès de clé de modèle** `self.personModelArray`:

    ![Modification du chemin d’accès de la clé](databinding-images/outline06.png "Modification du chemin d’accès de la clé")
8. Cela lie le contrôleur d’arborescence au tableau de `PersonModels` que nous avons exposé sur notre contrôleur d’affichage.

Nous devons maintenant lier notre mode plan au contrôleur d’arborescence, procédez comme suit :

1. Sélectionnez le mode plan et dans l' **inspecteur de liaison** , sélectionnez :

    [![Sélection de l’inspecteur de liaison](databinding-images/outline07.png "Sélection de l’inspecteur de liaison")](databinding-images/outline07-large.png#lightbox)
2. Sous le **mode plan contenu** Turndown, sélectionnez **lier à** et **contrôleur d’arborescence**. Entrez `arrangedObjects` pour le champ **clé du contrôleur** :

    ![Définition de la clé de contrôleur](databinding-images/outline08.png "Définition de la clé de contrôleur")
3. Sélectionnez la **cellule d’affichage de table** sous la colonne **Employee** . Dans l' **inspecteur de liaisons** sous la **valeur** Turndown, sélectionnez **lier à** et **vue de cellule de table**. Entrez `objectValue.Name` pour le **chemin d’accès**de la clé de modèle :

    [![Saisie du chemin d’accès de la clé de modèle](databinding-images/outline09.png "Saisie du chemin d’accès de la clé de modèle")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` est le `PersonModel` actuel dans le tableau géré par le contrôleur d’arborescence.
5. Sélectionnez la **cellule d’affichage de table** sous la colonne **occupation** . Dans l' **inspecteur de liaisons** sous la **valeur** Turndown, sélectionnez **lier à** et **vue de cellule de table**. Entrez `objectValue.Occupation` pour le **chemin d’accès**de la clé de modèle :

    [![Saisie du chemin d’accès de la clé de modèle](databinding-images/outline10.png "Saisie du chemin d’accès de la clé de modèle")](databinding-images/outline10-large.png#lightbox)
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Si nous exécutons l’application, le plan sera rempli avec le tableau de `PersonModels`:

[![Exécution de l’application](databinding-images/outline11.png "Exécution de l'application")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Liaison de données d’affichage de collection

La liaison de données avec une vue de collection est très similaire à la liaison avec une vue de table, car un contrôleur de tableau est utilisé pour fournir des données pour la collection. Étant donné que la vue de collection n’a pas de format d’affichage prédéfini, un travail supplémentaire est nécessaire pour fournir des commentaires sur l’interaction de l’utilisateur et effectuer le suivi de la sélection de l’utilisateur.

> [!IMPORTANT]
> En raison d’un problème dans Xcode 7 et macOS 10,11 (et versions ultérieures), les vues de collection ne peuvent pas être utilisées dans des fichiers de Storyboard (. Storyboard). Par conséquent, vous devrez continuer à utiliser les fichiers. XIB pour définir vos vues de collection pour vos applications Xamarin. Mac. Pour plus d’informations, consultez notre documentation sur les [vues de collection](~/mac/user-interface/collection-view.md) .

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`:

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1. **Collection View Item** -  That manages a single instance of an item in the collection.
2. **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>Débogage des blocages natifs

Si vous faites une erreur dans vos liaisons de données, vous risquez de provoquer un _incident natif_ dans du code non managé et de provoquer l’échec complet de votre application Xamarin. Mac avec une erreur `SIGABRT` :

[![Exemple de boîte de dialogue de blocage Native](databinding-images/debug01.png "Exemple de boîte de dialogue de blocage Native")](databinding-images/debug01-large.png#lightbox)

Il existe généralement quatre causes principales pour les pannes natives pendant la liaison de données :

1. Votre modèle de données n’hérite pas de `NSObject` ou d’une sous-classe de `NSObject`.
2. Vous n’avez pas exposé votre propriété à Objective-C à l’aide de l’attribut `[Export("key-name")]`.
3. Vous n’avez pas encapsulé les modifications apportées à la valeur de l’accesseur dans `WillChangeValue` et `DidChangeValue` appels de méthode (en spécifiant la même clé que l’attribut `Export`).
4. Vous avez une clé incorrecte ou mal typée dans l' **inspecteur de liaison** de Interface Builder.

### <a name="decoding-a-crash"></a>Décodage d’un incident

Provoquant un incident natif dans notre liaison de données, nous pouvons vous montrer comment les localiser et les corriger. Dans Interface Builder, nous allons modifier la liaison de la première étiquette dans l’exemple d’affichage de collection de `Name` à `Title`:

[![Modification de la clé de liaison](databinding-images/debug02.png "Modification de la clé de liaison")](databinding-images/debug02-large.png#lightbox)

Nous allons enregistrer la modification, revenir à Visual Studio pour Mac à synchroniser avec Xcode et exécuter notre application. Lorsque la vue de collection s’affiche, l’application se bloque momentanément avec une erreur de `SIGABRT` (comme indiqué dans la sortie de l' **application** dans Visual Studio pour Mac) puisque le `PersonModel` n’expose pas de propriété avec la clé `Title`:

[![Exemple d’erreur de liaison](databinding-images/debug03.png "Exemple d’erreur de liaison")](databinding-images/debug03-large.png#lightbox)

Si nous faisons défiler vers le haut de l’erreur dans la sortie de l' **application** , nous pouvons voir la clé pour résoudre le problème :

[![Détection du problème dans le journal des erreurs](databinding-images/debug04.png "Détection du problème dans le journal des erreurs")](databinding-images/debug04-large.png#lightbox)

Cette ligne indique que la clé `Title` n’existe pas sur l’objet auquel nous créons la liaison. Si nous revenons la liaison à `Name` dans Interface Builder, enregistrer, synchroniser, régénérer et exécuter, l’application s’exécutera comme prévu sans problème.

## <a name="summary"></a>Résumé

Cet article a décrit en détail l’utilisation de la liaison de données et du codage clé-valeur dans une application Xamarin. Mac. Tout d’abord, il s’est penché sur l’exposition d’une C# classe à Objective-C à l’aide du codage clé-valeur (KVC) et de l’observation clé-valeur (KVO). Ensuite, il a montré comment utiliser une classe conforme à KVO et lier les données aux éléments d’interface utilisateur du Interface Builder de Xcode. Enfin, elle affichait une liaison de données complexe à l’aide des **contrôleurs de tableau** et des **contrôleurs d’arborescence**.

## <a name="related-links"></a>Liens connexes

- [MacDatabinding Storyboard (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-storyboard)
- [MacDatabinding XIB (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-xibs)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Contrôles standard](~/mac/user-interface/standard-controls.md)
- [Vues de table](~/mac/user-interface/table-view.md)
- [Modes plan](~/mac/user-interface/outline-view.md)
- [Vues de collection](~/mac/user-interface/collection-view.md)
- [Guide de programmation de codage de clé-valeur](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Présentation du Guide de programmation de l’observation de la valeur clé](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Présentation des liaisons de cacao rubriques relatives à la programmation](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introduction à la référence des liaisons de cacao](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
