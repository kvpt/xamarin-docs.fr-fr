---
title: Événements, les protocoles et les délégués dans Xamarin.iOS
description: Ce document décrit comment utiliser des événements, protocoles et délègue dans Xamarin.iOS. Ces concepts fondamentaux sont omniprésentes dans le développement Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: a82d96cf3f290dd28d163dd6f147a9dc28dfaa81
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827600"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Événements, les protocoles et les délégués dans Xamarin.iOS

Xamarin.iOS utilise des contrôles pour exposer des événements pour la plupart des interactions de l’utilisateur.
Applications Xamarin.iOS consomment ces événements de la même façon, comme des applications .NET conventionnelles. Par exemple, la classe Xamarin.iOS UIButton comporte un événement appelé TouchUpInside et utilise cet événement comme si cette classe et les événements ont été dans une application .NET.

Outre cette approche .NET, Xamarin.iOS expose un autre modèle qui peut être utilisé pour la liaison de données et une interaction plus complexe. Cette méthodologie utilise ce que Apple appelle les délégués et protocoles. Les délégués sont un concept similaire aux délégués dans C#, mais au lieu de définir et d’appeler une méthode unique, un délégué en Objective-C est une classe entière qui est conforme à un protocole. Un protocole est similaire à une interface dans C#, sauf que ses méthodes peuvent être facultatifs. Par exemple, pour remplir un UITableView avec des données, vous créeriez une classe déléguée qui implémente les méthodes définies dans le protocole de UITableViewDataSource le UITableView appellerait à se remplir lui-même.

Dans cet article, vous allez en savoir plus sur tous ces aspects, ce qui vous donne une base solide pour la gestion des scénarios de rappel dans Xamarin.iOS, notamment :

- **Événements** – les événements à l’aide de .NET avec les contrôles UIKit.
- **Protocoles** : apprendre ce que les protocoles sont et comment elles sont utilisées, et création d’un exemple qui fournit des données pour une annotation de la carte.
- **Délégués** – apprentissage sur les délégués Objective-C par extension de l’exemple de mappage pour gérer l’interaction utilisateur qui inclut une annotation, puis la différence entre les délégués forts et faibles et comment utiliser chacune de ces d’apprentissage.

Pour illustrer les protocoles et les délégués, nous allons créer une application de mappage simple qui ajoute une annotation à un mappage, comme illustré ici :

[![](delegates-protocols-and-events-images/01-map-sml.png "Un exemple d’une application de mappage simple qui ajoute une annotation à un mappage")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "une annotation exemple ajoutée à une carte")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Avant d’entreprendre cette application, nous allons commencer en examinant les événements .NET sous le UIKit.

## <a name="net-events-with-uikit"></a>Événements .NET avec UIKit

Xamarin.iOS expose des événements .NET sur les contrôles UIKit. Par exemple, UIButton a un événement TouchUpInside, qui vous gérer comme vous le feriez normalement dans .NET, comme indiqué dans le code suivant utilise un C# expression lambda :

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

Vous pouvez aussi implémenter ceci avec un C# 2.0-style méthode anonyme comme celle-ci :

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

Le code précédent est relié dans le `ViewDidLoad` procédé de la UIViewController. Le `aButton` variable fait référence à un bouton, vous pouvez ajouter dans le concepteur iOS ou de code. La figure suivante montre un bouton qui a été ajouté dans le concepteur iOS :

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "Un bouton ajoutés dans le concepteur iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS prend également en charge le style d’action de la cible de la connexion de votre code à une interaction qui se produit avec un contrôle. Pour créer une action de cible pour le **Hello** bouton, double-cliquez dessus dans le concepteur iOS. Fichier de code-behind de la UIViewController s’affichera et le développeur est invité à sélectionner un emplacement où insérer la méthode de connexion :

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "Le fichier code-behind UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Après avoir sélectionné un emplacement, une nouvelle méthode est créée et câblé à distance au contrôle. Dans l’exemple suivant, un message est écrit dans la console lorsque le bouton est activé :

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "Un message est écrit dans la console lorsque le bouton est activé")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Pour plus d’informations sur le modèle d’action de la cible iOS, consultez la section de l’Action de la cible de [compétences en Application pour iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) sur iOS d’Apple Developer Library.

Pour plus d’informations sur l’utilisation du concepteur iOS avec Xamarin.iOS, consultez le [vue d’ensemble du concepteur iOS](~/ios/user-interface/designer/index.md) documentation.

## <a name="events"></a>Événements

Si vous souhaitez intercepter des événements de contrôle, vous disposez d’un éventail d’options : à partir d’à l’aide de la C# les expressions lambda et des fonctions de délégué à l’aide des API Objective-C bas niveau.

La section suivante montre comment vous serez capturer l’événement TouchDown sur un bouton, selon le degré de contrôle vous avez besoin.

## <a name="c-style"></a>C#Style

À l’aide de la syntaxe de délégué :

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Si vous aimez les expressions lambda à la place :

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Si vous souhaitez avoir plusieurs boutons utilisent le même gestionnaire à partager le même code :

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

## <a name="monitoring-more-than-one-kind-of-event"></a>Surveillance de plusieurs types d’événement

Le C# événements pour les indicateurs de UIControlEvent ont un mappage aux indicateurs individuels. Lorsque vous souhaitez avoir la même partie du code gérer deux ou plusieurs événements, utilisez la `UIControl.AddTarget` méthode :

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

À l’aide de la syntaxe d’expression lambda :

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Si vous avez besoin utiliser les fonctionnalités de bas niveau de Objective-C, tels que le raccordement d’une instance d’objet particulier et l’appel d’un sélecteur :

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Veuillez noter que si vous implémentez la méthode d’instance dans une classe de base héritée, il doit être une méthode publique.

## <a name="protocols"></a>Protocoles

Un protocole est une fonctionnalité de langage d’Objective-C qui fournit la liste des déclarations de méthode. Il possède un objectif similaire à une interface dans C#, la principale différence est qu’un protocole peut avoir des méthodes facultatives. Méthodes facultatives ne sont pas appelés si la classe qui adopte un protocole n’implémente pas les. En outre, une classe unique dans Objective-C peut implémenter plusieurs protocoles, tout comme un C# classe peut implémenter plusieurs interfaces.

Apple utilise des protocoles dans iOS pour définir les contrats pour les classes à adopter, tout en faisant disparaître la classe d’implémentation de l’appelant, ce qui fonctionne comme un C# interface. Les protocoles sont utilisés à la fois dans les scénarios non délégué (comme avec la `MKAnnotation` exemple montre l’illustration suivante) et avec les délégués (comme présenté plus loin dans ce document, dans la section de délégués).

### <a name="protocols-with-xamarinios"></a>Protocoles avec Xamarin.ios

Jetons un œil à un exemple d’utilisation d’un protocole Objective-C à partir de Xamarin.iOS. Pour cet exemple, nous allons utiliser le `MKAnnotation` protocole, ce qui fait partie de la `MapKit` framework. `MKAnnotation` est un protocole qui permet à n’importe quel objet qui adopte pour fournir des informations sur une annotation peut être ajoutée à une carte. Par exemple, un objet qui implémente `MKAnnotation` fournit l’emplacement de l’annotation et le titre associé.

De cette façon, le `MKAnnotation` protocole est utilisé pour fournir des données pertinentes qui accompagne une annotation. L’affichage réel pour l’annotation lui-même est construit à partir des données dans l’objet adopte la `MKAnnotation` protocole. Par exemple, le texte de la légende qui apparaît lorsque l’utilisateur appuie sur l’annotation (comme indiqué dans la capture d’écran ci-dessous) est fourni à partir de la `Title` propriété dans la classe qui implémente le protocole :

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Exemple de texte de la légende lorsque l’utilisateur appuie sur l’annotation")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Comme décrit dans la section suivante, [protocoles immersion](#protocols-deep-dive), Xamarin.iOS lie les protocoles à des classes abstraites. Pour le `MKAnnotation` protocole, la limite C# classe est nommée `MKAnnotation` pour imiter le nom du protocole et il est une sous-classe de `NSObject`, la classe de base racine pour CocoaTouch. Le protocole nécessite des méthodes getter et setter pour être implémentée pour la coordonnée ; Toutefois, un titre et sous-titre sont facultatifs. Par conséquent, dans le `MKAnnotation` (classe), le `Coordinate` propriété est *abstraite*, qu’elle doit être implémentée et le `Title` et `Subtitle` propriétés sont marquées *virtuels* , ce qui les rend facultatif, comme indiqué ci-dessous :

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

N’importe quelle classe peut fournir des données d’annotation en dérivant simplement `MKAnnotation`, tant qu’au moins le `Coordinate` propriété est implémentée. Par exemple, Voici un exemple de classe qui accepte les coordonnées dans le constructeur et retourne une chaîne pour le titre :

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

Via le protocole, il est lié à, toute classe qui sous-classe `MKAnnotation` peut fournir des données pertinentes qui seront utilisées par la carte lorsqu’il crée la vue de l’annotation. Pour ajouter une annotation à un mappage, appelez simplement la `AddAnnotation` méthode d’un `MKMapView` de l’instance, comme indiqué dans le code suivant :

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

La variable de la carte ici est une instance d’un `MKMapView`, qui est la classe qui représente la carte elle-même. Le `MKMapView` utilisera le `Coordinate` données dérivées de la `SampleMapAnnotation` instance pour positionner la vue de l’annotation sur la carte.

Le `MKAnnotation` protocole fournit un ensemble connu de fonctionnalités sur tous les objets qui l’implémentent, sans le consommateur (la carte dans ce cas) avoir besoin de connaître les détails d’implémentation. Cela simplifie l’ajout d’une variété d’annotations possible à une carte.

### <a name="protocols-deep-dive"></a>Présentation approfondie des protocoles

Dans la mesure où C# interfaces ne prennent pas en charge les méthodes facultatives, Xamarin.iOS mappe les protocoles à des classes abstraites. Par conséquent, adoption d’un protocole en Objective-C est effectuée Xamarin.iOS en dérivant de la classe abstraite qui est liée au protocole et en implémentant les méthodes requises. Ces méthodes seront exposées en tant que méthodes abstraites dans la classe. Les méthodes facultatives du protocole seront liés à des méthodes virtuelles de la C# classe.

Par exemple, voici une partie de la `UITableViewDataSource` protocole en tant que dépendant dans Xamarin.iOS :

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

Notez que la classe est abstraite. Xamarin.iOS rend la classe abstraite pour prendre en charge les méthodes requis/facultatif dans les protocoles.
Toutefois, contrairement aux protocoles Objective-C, (ou C# interfaces), C# classes ne prennent pas en charge l’héritage multiple. Cela affecte la conception de C# code qui utilise des protocoles et entraîne généralement des classes imbriquées. Plus sur ce problème est couvert plus loin dans ce document, dans la section de délégués.

 `GetCell(…)` est une méthode abstraite, liée à la Objective-C *sélecteur*, `tableView:cellForRowAtIndexPath:`, qui est une méthode requise de la `UITableViewDataSource` protocole. Sélecteur est le terme Objective-C pour le nom de la méthode. Pour appliquer la méthode en fonction des besoins, Xamarin.iOS déclare comme abstraite. L’autre méthode, `NumberOfSections(…)`, est lié à `numberOfSectionsInTableview:`. Cette méthode est facultative dans le protocole, Xamarin.iOS déclare comme virtual, rendant facultatif pour remplacer dans C#.

Xamarin.iOS s’occupe de tous les liaison iOS pour vous. Toutefois, si vous avez besoin de lier un protocole à partir d’Objective-C manuellement, vous pouvez le faire en décorant une classe avec le `ExportAttribute`. Il s’agit de la même méthode utilisée par Xamarin.iOS lui-même.

Pour plus d’informations sur la façon de lier les types de Objective-C dans Xamarin.iOS, consultez l’article [Objective-C, les Types de liaison](~/ios/platform/binding-objective-c/index.md).

Nous ne sommes pas via avec des protocoles encore, cependant. Elles sont également utilisées dans iOS comme base pour les délégués Objective-C, qui est le sujet de la section suivante.

## <a name="delegates"></a>Délégués

iOS utilise des délégués d’Objective-C pour implémenter le modèle de délégation, dans lequel un objet transmet travail vers un autre. L’objet qui effectue le travail est le délégué du premier objet. Un objet indique son délégué pour effectuer le travail en envoyant des que messages d’après que certains événements se produisent. Envoyer un message comme celui-ci en Objective-C est fonctionnellement équivalent à l’appel d’une méthode dans C#. Un délégué implémente les méthodes en réponse à ces appels et fournit ainsi une fonctionnalité à l’application.

Les délégués permettent d’étendre le comportement des classes sans avoir à créer des sous-classes. Dans iOS, les applications utilisent souvent des délégués quand une classe rappelle à l’autre après qu’une action importante se produit. Par exemple, le `MKMapView` classe les appels vers son délégué lorsque l’utilisateur appuie sur une annotation sur une carte, ce qui donne à l’auteur de la classe déléguée l’opportunité de répondre au sein de l’application. Un exemple de ce type d’utilisation de délégué plus loin dans cet article, à l’aide de l’exemple, vous pouvez utiliser un délégué avec Xamarin.iOS.

À ce stade, vous vous demandez peut-être comment une classe détermine quelles méthodes à appeler sur son délégué. Il s’agit d’un autre emplacement dans lequel vous utilisez des protocoles. En règle générale, les méthodes disponibles pour un délégué proviennent des protocoles qu’ils adoptent.

### <a name="how-protocols-are-used-with-delegates"></a>Utilisation des protocoles avec des délégués

Nous avons vu précédemment comment les protocoles sont utilisés pour prendre en charge d’ajout d’annotations à un mappage.
Protocoles sont également utilisés pour fournir un ensemble connu de méthodes pour les classes d’appeler une fois que certains événements se produisent, par exemple comme une fois l’utilisateur appuie sur une annotation sur une carte ou sélectionne une cellule dans une table. Les classes qui implémentent ces méthodes sont appelées les délégués des classes qui les appellent.

Les classes qui prennent en charge la délégation faire en exposant une propriété de délégué, à laquelle une classe qui implémente le délégué est affectée. Les méthodes que vous implémentez pour le délégué varient en fonction du protocole qui adopte le délégué particulier. Pour le `UITableView` (méthode), vous implémentez le `UITableViewDelegate` protocole, pour le `UIAccelerometer` (méthode), vous devrez implémenter `UIAccelerometerDelegate`, et ainsi de suite pour toutes les autres classes tout au long d’iOS pour lequel vous souhaitez exposent un délégué.

Le `MKMapView` nous l’avons vu dans notre exemple précédent comporte également une propriété appelée délégué, qui il appellera une fois que certains événements se produisent. Le délégué pour `MKMapView` est de type `MKMapViewDelegate`.
Vous allez l’utiliser dans un exemple pour répondre à l’annotation qu’elle est sélectionnée, mais tout d’abord nous allons aborder la différence entre les délégués forts et faibles.

### <a name="strong-delegates-vs-weak-delegates"></a>Les délégués fort vs. Délégués faibles

Les délégués, que nous avons parlé jusqu'à présent sont délégués forts, ce qui signifie qu’ils sont fortement typés. Les liaisons Xamarin.iOS livré avec une classe fortement typée pour chaque protocole de délégué dans iOS. Toutefois, iOS a également le concept d’un délégué faible. Au lieu de sous-classement d’une classe liée au protocole Objective-C pour un délégué particulier, iOS vous permet également de choisir de lier les méthodes de protocole dans n’importe quelle classe que qui dérive de NSObject, en décorant vos méthodes avec ExportAttribute, puis en fournissant les sélecteurs appropriés.
Lorsque vous adoptez cette approche, vous affectez une instance de votre classe à la propriété WeakDelegate et non à la propriété de délégué. Un délégué faible vous offre la flexibilité nécessaire pour prendre votre classe de délégué vers le bas d’une hiérarchie d’héritage différents. Examinons un exemple de Xamarin.iOS qui utilise des délégués forts et faibles.

### <a name="example-using-a-delegate-with-xamarinios"></a>Exemple à l’aide d’un délégué avec Xamarin.iOS

Pour exécuter du code en réponse à l’utilisateur en appuyant sur l’annotation dans notre exemple, nous pouvons sous-classe `MKMapViewDelegate` et assignez une instance à la `MKMapView`de `Delegate` propriété. Le `MKMapViewDelegate` protocole contient uniquement les méthodes facultatives.
Par conséquent, toutes les méthodes sont virtuelles qui sont liés à ce protocole dans le Xamarin.iOS `MKMapViewDelegate` classe. Lorsque l’utilisateur sélectionne une annotation, le `MKMapView` instance enverra le `mapView:didSelectAnnotationView:` message à son délégué. Pour gérer cela dans Xamarin.iOS, nous devons remplacer le `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` méthode dans la sous-classe MKMapViewDelegate comme suit :

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

La classe SampleMapDelegate illustrée ci-dessus est implémentée comme une classe imbriquée dans le contrôleur qui contienne le `MKMapView` instance. En Objective-C, vous verrez souvent le contrôleur adopter plusieurs protocoles directement au sein de la classe. Toutefois, étant donné que les protocoles liés aux classes dans Xamarin.iOS, les classes qui implémentent des délégués fortement typés sont généralement inclus en tant que classes imbriquées.

Avec l’implémentation de classe de délégué en place, il vous suffit instancier une instance du délégué dans le contrôleur et l’affecter à la `MKMapView`de `Delegate` propriété comme indiqué ici :

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

Pour utiliser un délégué faible pour accomplir la même chose, vous devez lier la méthode dans toute classe qui dérive de `NSObject` et assignez-la à la `WeakDelegate` propriété de la `MKMapView`. Dans la mesure où le `UIViewController` dérivent `NSObject` (par exemple, chaque classe Objective-C dans CocoaTouch), nous pouvons simplement implémenter une méthode liée à `mapView:didSelectAnnotationView:` directement dans le contrôleur et affecter le contrôleur de `MKMapView`de `WeakDelegate`, en évitant d’avoir la classe imbriquée supplémentaire. Le code ci-dessous illustre cette approche :

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

Lorsque vous exécutez ce code, l’application se comporte exactement comme elle le faisait lors de l’exécution de la version fortement typée de délégué. L’avantage de ce code est que le délégué faible ne nécessite pas la création de la classe supplémentaire qui a été créée lorsque nous avons utilisé le délégué fortement typé. Toutefois, cela se fait au détriment de la sécurité de type. Si vous deviez commettez une erreur dans le sélecteur qui a été passé à la `ExportAttribute`, vous ne trouverez pas jusqu'à l’exécution.

### <a name="events-and-delegates"></a>Événements et délégués

Les délégués sont utilisés pour les rappels dans iOS de la même façon à la manière dont .NET utilise des événements. Pour rendre iOS API et la manière qu’ils utilisent des délégués d’Objective-C sembler plus que .NET, Xamarin.iOS expose des événements de .NET dans de nombreux endroits où les délégués sont utilisés dans iOS.

Par exemple, l’implémentation précédente où le `MKMapViewDelegate` a répondu à une annotation sélectionnée pourrait également être implémentée dans Xamarin.iOS à l’aide d’un événement .NET. Dans ce cas, l’événement serait définie dans `MKMapView` et appelé `DidSelectAnnotationView`. Il fallait un `EventArgs` sous-classe du type `MKMapViewAnnotationEventsArgs`. Le `View` propriété du `MKMapViewAnnotationEventsArgs` vous donnera une référence à la vue de l’annotation, à partir de ce qui peut procéder à la même implémentation vous aviez précédemment, comme illustré ici :

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

## <a name="summary"></a>Récapitulatif

Cet article a abordé comment utiliser des événements, les protocoles et les délégués dans Xamarin.iOS. Nous avons vu comment Xamarin.iOS expose des événements de style .NET normaux pour les contrôles.
Ensuite, nous avons appris sur les protocoles Objective-C, y compris comment ils sont différents des C# interfaces et Xamarin.iOS leur utilisation. Enfin, nous avons examiné les délégués Objective-C à partir d’une perspective de Xamarin.iOS. Nous avons vu comment Xamarin.iOS prend en charge les deux fortement et faiblement typés délégués et comment lier les événements .NET pour déléguer des méthodes.

## <a name="related-links"></a>Liens associés

- [Protocoles, délégués et événements (exemple)](https://developer.xamarin.com/samples/monotouch/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Types de liaison Objective-C](~/ios/platform/binding-objective-c/index.md)
- [Le langage de programmation Objective-C](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Conception des Interfaces utilisateur dans Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Compétences en Application pour iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
