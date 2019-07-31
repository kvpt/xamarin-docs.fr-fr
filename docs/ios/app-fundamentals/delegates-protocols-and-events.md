---
title: Événements, protocoles et délégués dans Xamarin. iOS
description: Ce document décrit comment utiliser les événements, les protocoles et les délégués dans Xamarin. iOS. Ces concepts fondamentaux sont omniprésents dans le développement Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: d42263733c7fa793713738be4b389eaa4850f38b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649358"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Événements, protocoles et délégués dans Xamarin. iOS

Xamarin. iOS utilise des contrôles pour exposer des événements pour la plupart des interactions utilisateur.
Les applications Xamarin. iOS consomment ces événements à peu près de la même façon que les applications .NET traditionnelles. Par exemple, la classe Xamarin. iOS UIButton a un événement appelé TouchUpInside et utilise cet événement comme si cette classe et cet événement se trouvaient dans une application .NET.

Outre cette approche .NET, Xamarin. iOS expose un autre modèle qui peut être utilisé pour une interaction plus complexe et une liaison de données. Cette méthodologie utilise ce que Apple appelle les délégués et les protocoles. Les délégués sont du concept similaire aux délégués C#dans, mais au lieu de définir et d’appeler une méthode unique, un délégué en Objective-C est une classe entière conforme à un protocole. Un protocole est semblable à une interface dans C#, sauf que ses méthodes peuvent être facultatives. Par exemple, pour remplir un UITableView avec des données, vous devez créer une classe déléguée qui implémente les méthodes définies dans le protocole UITableViewDataSource que le UITableView appellera pour se remplir.

Dans cet article, vous allez découvrir toutes ces rubriques, en vous donnant une base solide pour gérer les scénarios de rappel dans Xamarin. iOS, notamment:

- **Événements** : utilisation d’événements .net avec des contrôles uikit.
- **Protocoles** : découvrir les protocoles et leur utilisation, et créer un exemple qui fournit des données pour une annotation de type carte.
- **Délégués** : en savoir plus sur les délégués objective-C en étendant l’exemple de carte pour gérer l’interaction de l’utilisateur qui comprend une annotation, puis en apprenant la différence entre les délégués forts et faibles et le moment où ils doivent être utilisés.

Pour illustrer les protocoles et les délégués, nous allons créer une application cartographique simple qui ajoute une annotation à une carte, comme illustré ici:

[![](delegates-protocols-and-events-images/01-map-sml.png "Exemple d’application Map simple qui ajoute une annotation à un mappage")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "un exemple d’annotation ajouté à une carte")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Avant de traiter cette application, commençons par examiner les événements .NET sous UIKit.

## <a name="net-events-with-uikit"></a>Événements .NET avec UIKit

Xamarin. iOS expose les événements .NET sur les contrôles UIKit. Par exemple, UIButton a un événement TouchUpInside, que vous gérez comme vous le feriez normalement dans .NET, comme illustré dans le code suivant qui C# utilise une expression lambda:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

Vous pouvez également implémenter cela avec C# une méthode anonyme de style 2,0 telle que celle-ci:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

Le code précédent est associé à la `ViewDidLoad` méthode de UIViewController. La `aButton` variable fait référence à un bouton, que vous pouvez ajouter dans le concepteur iOS ou avec du code. L’illustration suivante montre un bouton qui a été ajouté dans le concepteur iOS:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "Un bouton ajouté dans le concepteur iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin. iOS prend également en charge le style d’action cible qui consiste à connecter votre code à une interaction qui se produit avec un contrôle. Pour créer une action cible pour le bouton **Hello** , double-cliquez dessus dans le concepteur iOS. Le fichier code-behind de UIViewController s’affiche et le développeur est invité à sélectionner un emplacement pour insérer la méthode de connexion:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "Fichier code-behind UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Une fois l’emplacement sélectionné, une nouvelle méthode est créée et câblée au contrôle. Dans l’exemple suivant, un message est écrit dans la console lorsque vous cliquez sur le bouton:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "Un message est écrit sur la console lorsque l’utilisateur clique sur le bouton.")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Pour plus d’informations sur le modèle d’action cible iOS, consultez la section cible-action des [principales compétences des applications pour iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) dans la bibliothèque des développeurs iOS d’Apple.

Pour plus d’informations sur l’utilisation du concepteur iOS avec Xamarin. iOS, consultez la documentation de [Présentation du concepteur iOS](~/ios/user-interface/designer/index.md) .

## <a name="events"></a>Events

Si vous souhaitez intercepter des événements à partir de Uicontrolextensions, vous disposez d’une gamme d’options C# : de l’utilisation des fonctions lambda et des fonctions déléguées à l’utilisation des API objective-C de bas niveau.

La section suivante montre comment capturer l’événement d’événement sur un bouton, en fonction de la quantité de contrôle dont vous avez besoin.

## <a name="c-style"></a>C#Style

Utilisation de la syntaxe du délégué:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Si vous préférez des expressions lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Si vous souhaitez que plusieurs boutons utilisent le même gestionnaire pour partager le même code:

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

## <a name="monitoring-more-than-one-kind-of-event"></a>Analyse de plusieurs types d’événements

Les C# événements pour les indicateurs UIControlEvent ont un mappage un-à-un avec des indicateurs individuels. Si vous souhaitez que la même partie du code gère deux événements ou plus, utilisez la `UIControl.AddTarget` méthode:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Utilisation de la syntaxe lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Si vous devez utiliser des fonctionnalités de bas niveau de Objective-C, comme le raccordement à une instance d’objet particulière et l’appel d’un sélecteur particulier:

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Notez que si vous implémentez la méthode d’instance dans une classe de base héritée, il doit s’agir d’une méthode publique.

## <a name="protocols"></a>Protocoles

Un protocole est une fonctionnalité du langage Objective-C qui fournit une liste de déclarations de méthode. Il a un objectif similaire à une interface dans C#, la principale différence est qu’un protocole peut avoir des méthodes facultatives. Les méthodes facultatives ne sont pas appelées si la classe qui adopte un protocole ne les implémente pas. En outre, une classe unique en Objective-C peut implémenter plusieurs protocoles, C# tout comme une classe peut implémenter plusieurs interfaces.

Apple utilise des protocoles dans iOS pour définir les contrats des classes à adopter, tout en faisant abstraction de la classe d’implémentation de l’appelant, qui C# fonctionne comme une interface. Les protocoles sont utilisés à la fois dans les scénarios non délégués (comme `MKAnnotation` avec l’exemple suivant) et avec les délégués (comme indiqué plus loin dans ce document, dans la section délégués).

### <a name="protocols-with-xamarinios"></a>Protocoles avec Xamarin. iOS

Jetons un coup d’œil à un exemple utilisant un protocole objective-C à partir de Xamarin. iOS. Pour cet exemple, nous allons utiliser le `MKAnnotation` protocole, qui fait partie de l' `MapKit` infrastructure. `MKAnnotation`est un protocole qui permet à tout objet qui l’adopte de fournir des informations sur une annotation qui peut être ajoutée à un mappage. Par exemple, un objet qui `MKAnnotation` implémente fournit l’emplacement de l’annotation et le titre qui lui est associé.

De cette façon, le `MKAnnotation` protocole est utilisé pour fournir des données pertinentes qui accompagnent une annotation. La vue réelle de l’annotation est créée à partir des données de l’objet qui adopte le `MKAnnotation` protocole. Par exemple, le texte de la légende qui apparaît lorsque l’utilisateur appuie sur l’annotation (comme indiqué dans la capture d’écran ci-dessous `Title` ) provient de la propriété de la classe qui implémente le protocole:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Exemple de texte pour la légende lorsque l’utilisateur appuie sur l’annotation")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Comme décrit dans la section suivante, [immersion](#protocols-deep-dive)dans les protocoles, Xamarin. iOS lie les protocoles aux classes abstraites. Pour le `MKAnnotation` protocole, la classe C# liée est nommée `MKAnnotation` pour imiter le nom du protocole et il s’agit d’une sous- `NSObject`classe de, la classe de base racine pour cocoatouch. Le protocole requiert l’implémentation d’une méthode getter et setter pour la coordonnée. Toutefois, un titre et un sous-titre sont facultatifs. Par conséquent, dans `MKAnnotation` la classe, `Coordinate` la propriétéest abstraite, ce qui implique qu’elle `Title` soit `Subtitle` implémentée etque les propriétés et sont marquées comme virtuelles, ce qui les rend facultatives, comme indiqué ci-dessous:

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

Toute classe peut fournir des données d’annotation en dérivant simplement `MKAnnotation`de, tant que la `Coordinate` propriété est implémentée au moins. Par exemple, voici un exemple de classe qui prend la coordonnée dans le constructeur et retourne une chaîne pour le titre:

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

Par le biais du protocole auquel il est lié, toute classe qui sous `MKAnnotation` -classe peut fournir les données pertinentes qui seront utilisées par la carte lorsqu’elle crée la vue de l’annotation. Pour ajouter une annotation à une carte, appelez simplement la `AddAnnotation` méthode d’une `MKMapView` instance, comme indiqué dans le code suivant:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

Ici, la variable de mappage est une instance `MKMapView`d’un, qui est la classe qui représente le mappage lui-même. Le `MKMapView` utilise les `Coordinate` données dérivées de l' `SampleMapAnnotation` instance pour positionner la vue d’annotation sur la carte.

Le `MKAnnotation` protocole fournit un ensemble connu de fonctionnalités pour tous les objets qui l’implémentent, sans le consommateur (le mappage dans ce cas), à savoir sur les détails de l’implémentation. Cela simplifie l’ajout d’un large éventail d’annotations possibles à une carte.

### <a name="protocols-deep-dive"></a>Exploration approfondie des protocoles

Étant C# donné que les interfaces ne prennent pas en charge les méthodes facultatives, Xamarin. iOS mappe les protocoles aux classes abstraites. Par conséquent, l’adoption d’un protocole en Objective-C s’effectue dans Xamarin. iOS en dérivant de la classe abstraite liée au protocole et en implémentant les méthodes requises. Ces méthodes seront exposées en tant que méthodes abstraites dans la classe. Les méthodes facultatives du protocole sont liées aux méthodes virtuelles de C# la classe.

Par exemple, voici une partie du `UITableViewDataSource` protocole telle qu’elle est liée dans Xamarin. iOS:

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

Notez que la classe est abstraite. Xamarin. iOS rend la classe abstraite pour prendre en charge les méthodes facultatives/requises dans les protocoles.
Toutefois, contrairement aux protocoles objective-C, C# (ou interfaces C# ), les classes ne prennent pas en charge l’héritage multiple. Cela affecte la conception du C# code qui utilise des protocoles, et en général, les classes imbriquées. Pour plus d’informations sur ce problème, vous trouverez plus loin dans ce document, dans la section délégués.

 `GetCell(…)`est une méthode abstraite, liée au *Sélecteur*objective-C `tableView:cellForRowAtIndexPath:`,, qui est une méthode `UITableViewDataSource` requise du protocole. Selector est le terme objective-C pour le nom de la méthode. Pour appliquer la méthode en fonction des besoins, Xamarin. iOS la déclare comme abstraite. L’autre méthode, `NumberOfSections(…)`, est liée à `numberOfSectionsInTableview:`. Cette méthode étant facultative dans le protocole, Xamarin. iOS la déclare comme étant virtuelle, ce qui la rend facultative à remplacer dans C#.

Xamarin. iOS gère toutes les liaisons iOS pour vous. Toutefois, si vous devez lier manuellement un protocole à Objective-C, vous pouvez le `ExportAttribute`faire en décorant une classe avec. Il s’agit de la même méthode que celle utilisée par Xamarin. iOS.

Pour plus d’informations sur la façon de lier des types objective-C dans Xamarin. iOS, consultez l’article [Binding objective-c types](~/ios/platform/binding-objective-c/index.md).

Toutefois, nous n’avons pas encore utilisé les protocoles. Ils sont également utilisés dans iOS comme base pour les délégués objective-C, qui est le sujet de la section suivante.

## <a name="delegates"></a>Délégués

iOS utilise des délégués objective-C pour implémenter le modèle de délégation, dans lequel un objet passe à un autre. L’objet qui fait le travail est le délégué du premier objet. Un objet indique à son délégué de travailler en lui envoyant des messages après certains événements. L’envoi d’un message de ce type en Objective-C est fonctionnellement équivalent à C#l’appel d’une méthode dans. Un délégué implémente des méthodes en réponse à ces appels, et fournit donc des fonctionnalités à l’application.

Les délégués vous permettent d’étendre le comportement des classes sans avoir à créer des sous-classes. Les applications dans iOS utilisent souvent des délégués quand une classe effectue un rappel à un autre après une action importante. Par exemple, la `MKMapView` classe rappelle son délégué lorsque l’utilisateur appuie sur une annotation sur une carte, donnant à l’auteur de la classe déléguée l’opportunité de répondre dans l’application. Vous pouvez utiliser un exemple de ce type d’utilisation de délégué plus loin dans cet article, en utilisant par exemple un délégué avec Xamarin. iOS.

À ce stade, vous vous demandez peut-être comment une classe détermine les méthodes à appeler sur son délégué. Il s’agit d’un autre emplacement où vous utilisez des protocoles. En règle générale, les méthodes disponibles pour un délégué proviennent des protocoles qu’elles adoptent.

### <a name="how-protocols-are-used-with-delegates"></a>Utilisation des protocoles avec les délégués

Nous avons vu plus haut comment les protocoles sont utilisés pour prendre en charge l’ajout d’annotations à une carte.
Les protocoles sont également utilisés pour fournir un ensemble connu de méthodes pour que les classes soient appelées après l’exécution de certains événements, par exemple, une fois que l’utilisateur a appuyé sur une annotation sur une carte ou qu’il a sélectionné une cellule dans une table. Les classes qui implémentent ces méthodes sont appelées délégués des classes qui les appellent.

Les classes qui prennent en charge la délégation le font en exposant une propriété de délégué, à laquelle une classe qui implémente le délégué est assignée. Les méthodes que vous implémentez pour le délégué dépendent du Protocole adopté par le délégué particulier. Pour la `UITableView` méthode, vous implémentez `UITableViewDelegate` le protocole, pour `UIAccelerometer` la méthode, vous implémentez `UIAccelerometerDelegate`, et ainsi de suite, pour toutes les autres classes d’IOS pour lesquelles vous souhaitez exposer un délégué.

La `MKMapView` classe que nous avons vue dans notre exemple précédent possède également une propriété appelée Delegate, qu’elle appellera après l’apparition de plusieurs événements. Le délégué de `MKMapView` est de type `MKMapViewDelegate`.
Vous utiliserez cela peu de temps dans un exemple pour répondre à l’annotation une fois qu’elle est sélectionnée, mais commençons par aborder la différence entre les délégués forts et faibles.

### <a name="strong-delegates-vs-weak-delegates"></a>Délégués forts et Délégués faibles

Les délégués que nous avons examinés jusqu’à présent sont des délégués forts, ce qui signifie qu’ils sont fortement typés. Les liaisons Xamarin. iOS sont fournies avec une classe fortement typée pour chaque protocole délégué dans iOS. Toutefois, iOS a également le concept de délégué faible. Au lieu de sous-classer une classe liée au protocole objective-C pour un délégué particulier, iOS vous permet également de lier les méthodes de protocole vous-même dans une classe de votre choix qui dérive de NSObject, en décorant vos méthodes avec ExportAttribute, puis fournir les sélecteurs appropriés.
Lorsque vous adoptez cette approche, vous assignez une instance de votre classe à la propriété WeakDelegate au lieu de la propriété de délégué. Un délégué faible vous offre la possibilité de mettre votre classe déléguée dans une hiérarchie d’héritage différente. Examinons un exemple Xamarin. iOS qui utilise à la fois des délégués forts et faibles.

### <a name="example-using-a-delegate-with-xamarinios"></a>Exemple utilisant un délégué avec Xamarin. iOS

Pour exécuter du code en réponse à l’utilisateur en appuyant sur l’annotation dans notre exemple, `MKMapViewDelegate` nous pouvons la sous-classe `MKMapView`et `Delegate` assigner une instance à la propriété de. Le `MKMapViewDelegate` protocole contient uniquement des méthodes facultatives.
Par conséquent, toutes les méthodes sont virtuelles qui sont liées à ce protocole dans la classe `MKMapViewDelegate` Xamarin. iOS. Lorsque l’utilisateur sélectionne une annotation, l' `MKMapView` instance envoie le `mapView:didSelectAnnotationView:` message à son délégué. Pour gérer cela dans Xamarin. iOS, nous devons remplacer la `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` méthode dans la sous-classe MKMapViewDelegate comme suit:

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

La classe SampleMapDelegate présentée ci-dessus est implémentée en tant que classe imbriquée dans le `MKMapView` contrôleur qui contient l’instance. En Objective-C, vous verrez souvent que le contrôleur adopte plusieurs protocoles directement dans la classe. Toutefois, étant donné que les protocoles sont liés à des classes dans Xamarin. iOS, les classes qui implémentent des délégués fortement typés sont généralement incluses comme des classes imbriquées.

Avec l’implémentation de la classe déléguée en place, il vous suffit d’instancier une instance du délégué dans le contrôleur et de `MKMapView`l' `Delegate` assigner à la propriété de, comme illustré ici:

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

Pour utiliser un délégué faible pour accomplir la même chose, vous devez lier la méthode vous-même dans toute classe qui dérive `NSObject` de et l’assigner à la `MKMapView` `WeakDelegate` propriété de. Étant donné `UIViewController` que la classe dérive `NSObject` finalement de (comme chaque classe objective-C dans cocoatouch), nous pouvons tout simplement `mapView:didSelectAnnotationView:` implémenter une méthode liée directement dans le contrôleur et `MKMapView`assigner le contrôleur à `WeakDelegate`, évitant ainsi la nécessité de la classe imbriquée supplémentaire. Le code ci-dessous illustre cette approche:

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

Lors de l’exécution de ce code, l’application se comporte exactement comme elle le faisait lors de l’exécution de la version déléguée fortement typée. L’avantage de ce code est que le délégué faible ne requiert pas la création de la classe supplémentaire créée lorsque nous avons utilisé le délégué fortement typé. Toutefois, cela se fait au détriment de la sécurité de type. Si vous deviez faire une erreur dans le sélecteur qui a été passé à `ExportAttribute`l', vous ne le verrez pas jusqu’au moment de l’exécution.

### <a name="events-and-delegates"></a>Événements et délégués

Les délégués sont utilisés pour les rappels dans iOS de la même façon que .NET utilise des événements. Pour faire en sorte que les API iOS et la façon dont elles utilisent les délégués objective-C semblent plus similaires à .NET, Xamarin. iOS expose les événements .NET dans de nombreux endroits où les délégués sont utilisés dans iOS.

Par exemple, l’implémentation antérieure dans laquelle `MKMapViewDelegate` le a répondu à une annotation sélectionnée peut également être implémentée dans Xamarin. IOS à l’aide d’un événement .net. Dans ce cas, l’événement est défini dans `MKMapView` et appelé. `DidSelectAnnotationView` Elle aurait une `EventArgs` sous-classe de type `MKMapViewAnnotationEventsArgs`. La `View` propriété de `MKMapViewAnnotationEventsArgs` vous donne une référence à la vue d’annotation, à partir de laquelle vous pouvez effectuer la même implémentation que vous aviez précédemment, comme illustré ici:

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

Cet article a décrit comment utiliser des événements, des protocoles et des délégués dans Xamarin. iOS. Nous avons vu comment Xamarin. iOS expose les événements de style .NET normaux pour les contrôles.
Nous avons ensuite appris les protocoles objective-C, notamment la façon dont C# ils sont différents des interfaces et comment Xamarin. iOS les utilise. Enfin, nous avons examiné les délégués objective-C à partir d’une perspective Xamarin. iOS. Nous avons vu comment Xamarin. iOS prend en charge les délégués fortement et faiblement typés, et comment lier des événements .NET pour déléguer des méthodes.

## <a name="related-links"></a>Liens associés

- [Protocoles, délégués et événements (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/protocols-delegates-events)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Liaison d’un type objective-C](~/ios/platform/binding-objective-c/index.md)
- [Langage de programmation objective-C](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Conception d’interfaces utilisateur dans Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Principales compétences en matière d’applications pour iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
