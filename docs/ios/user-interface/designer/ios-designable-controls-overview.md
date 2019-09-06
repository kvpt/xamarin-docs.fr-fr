---
title: Contrôles personnalisés dans le Xamarin Designer pour iOS
description: Le Xamarin Designer pour iOS prend en charge le rendu des contrôles personnalisés créés dans votre projet ou référencés à partir de sources externes telles que le magasin de composants Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: e934059f5428780ea19917068503b58961ac5673
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284179"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Contrôles personnalisés dans le Xamarin Designer pour iOS

_Le Xamarin Designer pour iOS prend en charge le rendu des contrôles personnalisés créés dans votre projet ou référencés à partir de sources externes telles que le magasin de composants Xamarin._

Le Xamarin Designer pour iOS est un outil puissant pour la visualisation de l’interface utilisateur d’une application et fournit la prise en charge de l’édition WYSIWYG pour la plupart des vues iOS et des contrôleurs d’affichage. Votre application peut également contenir des contrôles personnalisés qui étendent ceux intégrés à iOS. Si ces contrôles personnalisés sont écrits avec quelques instructions à l’esprit, ils peuvent également être rendus par le concepteur iOS, ce qui offre une expérience d’édition encore plus riche. Ce document examine ces instructions.

## <a name="requirements"></a>Configuration requise

Un contrôle qui remplit toutes les conditions suivantes sera restitué sur l’aire de conception :

1. Il s’agit d’une sous-classe directe ou indirecte de [UIView](xref:UIKit.UIView) ou [UIViewController](xref:UIKit.UIViewController). D’autres sous-classes [NSObject](xref:Foundation.NSObject) s’affichent sous la forme d’une icône sur l’aire de conception.
2. Il a un [RegisterAttribute](xref:Foundation.RegisterAttribute) pour l’exposer à Objective-C.
3. Il a [le constructeur IntPtr requis](~/ios/internals/api-design/index.md).
4. Il implémente l’interface [IComponent](xref:System.ComponentModel.IComponent) ou a un [DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute) défini sur true.

Les contrôles définis dans le code qui répondent aux exigences ci-dessus s’affichent dans le concepteur lorsque leur projet conteneur est compilé pour le simulateur. Par défaut, tous les contrôles personnalisés s’affichent dans la section **composants personnalisés** de la **boîte à outils**. Toutefois, [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute) peut être appliqué à la classe du contrôle personnalisé pour spécifier une autre section.

Le concepteur ne prend pas en charge le chargement de bibliothèques objective-C tierces.

## <a name="custom-properties"></a>Propriétés personnalisées

Une propriété déclarée par un contrôle personnalisé s’affiche dans le volet des propriétés si les conditions suivantes sont remplies :

1. La propriété possède un accesseur get et un accesseur set publics.
1. La propriété a un [ExportAttribute](xref:Foundation.ExportAttribute) , ainsi qu’un [BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) défini sur true.
1. Le type de propriété est un type numérique, un type énumération, une chaîne, un bool, [SizeF](xref:System.Drawing.SizeF), [UIColor](xref:UIKit.UIColor)ou [UIImage](xref:UIKit.UIImage). Cette liste de types pris en charge peut être développée à l’avenir.


La propriété peut également être décorée avec un [DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute) pour spécifier l’étiquette qui s’affiche dans le volet des propriétés.

## <a name="initialization"></a>Initialisation

Pour `UIViewController` les sous-classes, vous devez utiliser la méthode [ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad) pour le code qui dépend des vues que vous avez créées dans le concepteur.

Pour `UIView` et d' `NSObject` autres sous-classes, la méthode [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib) est l’emplacement recommandé pour effectuer l’initialisation de votre contrôle personnalisé après son chargement à partir du fichier de disposition. En effet, toutes les propriétés personnalisées définies dans le panneau des propriétés ne sont pas définies lors de l’exécution du constructeur du contrôle, mais elles `AwakeFromNib` sont définies avant que ne soit appelé :


```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

Si le contrôle est également conçu pour être créé directement à partir du code, vous souhaiterez peut-être créer une méthode qui a un code d’initialisation commun, comme suit :

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>Initialisation de propriété et AwakeFromNib

Vous devez prendre soin de déterminer quand et où initialiser les propriétés concevables dans un composant personnalisé en tant que pour ne pas remplacer les valeurs qui ont été définies dans le concepteur iOS. Par exemple, prenez le code suivant :

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

Le `CustomView` composant expose une `Counter` propriété qui peut être définie par le développeur à l’intérieur du concepteur iOS. Toutefois, quelle que soit la valeur définie dans le concepteur, la valeur de la `Counter` propriété sera toujours égale à zéro (0). En voici les raisons :

- Une instance du `CustomControl` est incluse dans le fichier de table de montage séquentiel.
- Toutes les propriétés modifiées dans le concepteur IOS sont définies (comme la définition de la valeur `Counter` de sur deux (2), par exemple).
- La `AwakeFromNib` méthode est exécutée et un appel à la méthode du `Initialize` composant est effectué.
- L' `Initialize` intérieur de la valeur `Counter` de la propriété est remis à zéro (0).


Pour résoudre ce problème, initialisez la `Counter` propriété ailleurs (par exemple, dans le constructeur du composant) ou ne substituez pas la `AwakeFromNib` méthode et appelez `Initialize` si le composant ne requiert aucune initialisation supplémentaire en dehors de ce est actuellement géré par ses constructeurs.

## <a name="design-mode"></a>Mode création

Sur l’aire de conception, un contrôle personnalisé doit respecter quelques restrictions :

- Les ressources de bundle d’applications ne sont pas disponibles en mode création. Les images sont disponibles lorsqu’elles sont chargées via les [méthodes UIImage](xref:UIKit.UIImage) .
- Les opérations asynchrones, telles que les requêtes Web, ne doivent pas être effectuées en mode création. L’aire de conception ne prend pas en charge l’animation ou les autres mises à jour asynchrones de l’interface utilisateur du contrôle.


Un contrôle personnalisé peut implémenter [IComponent](xref:System.ComponentModel.IComponent) et utiliser la propriété [DesignMode](xref:System.ComponentModel.ISite.DesignMode) pour vérifier s’il se trouve sur l’aire de conception. Dans cet exemple, l’étiquette affiche « mode Design » sur l’aire de conception et « Runtime » au moment de l’exécution :

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null && Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

Vous devez toujours vérifier la `Site` propriété pour `null` avant d’essayer d’accéder à l’un de ses membres. Si `Site` est`null`, il est possible de supposer que le contrôle n’est pas en cours d’exécution dans le concepteur.
En mode création, `Site` est défini après l’exécution du constructeur du contrôle et avant `AwakeFromNib` l’appel de.

## <a name="debugging"></a>Débogage

Un contrôle qui répond aux exigences ci-dessus est affiché dans la boîte à outils et rendu sur l’aire de conception.
Si un contrôle n’est pas rendu, vérifiez les bogues dans le contrôle ou l’une de ses dépendances.

L’aire de conception peut souvent intercepter les exceptions levées par des contrôles, tout en continuant d’afficher d’autres contrôles. Le contrôle défectueux est remplacé par un espace réservé rouge et vous pouvez afficher le suivi des exceptions en cliquant sur l’icône d’exclamation :

 ![](ios-designable-controls-overview-images/exception-box.png "Un contrôle défectueux en tant qu’espace réservé rouge et les détails de l’exception")

Si des symboles de débogage sont disponibles pour le contrôle, la trace aura des noms de fichiers et des numéros de ligne.
Double-cliquez sur une ligne dans la trace de la pile pour accéder à cette ligne dans le code source.

Si le concepteur ne peut pas isoler le contrôle défectueux, un message d’avertissement s’affiche en haut de l’aire de conception :

 ![](ios-designable-controls-overview-images/info-bar.png "Un message d’avertissement en haut de l’aire de conception")

Le rendu complet reprendra lorsque le contrôle défectueux sera résolu ou supprimé de l’aire de conception.

## <a name="summary"></a>Récapitulatif

Cet article a introduit la création et l’application de contrôles personnalisés dans le concepteur iOS. Tout d’abord, il a décrit les exigences que les contrôles doivent respecter pour être rendues sur l’aire de conception et exposer des propriétés personnalisées dans le volet de propriétés. Il a ensuite examiné le code derrière l’initialisation du contrôle et la propriété DesignMode. Enfin, il a décrit ce qui se passe lorsque des exceptions sont levées et comment résoudre ce problème.
