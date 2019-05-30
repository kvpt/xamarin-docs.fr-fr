---
title: Contrôles personnalisés dans le Concepteur de Xamarin pour iOS
description: Le Concepteur de Xamarin pour iOS prend en charge le rendu des contrôles personnalisés créés dans votre projet ou référencés à partir de sources externes telles que le Store du composant Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 267c465fbc10468e70e39831e4f47a4a87f36d00
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66353984"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Contrôles personnalisés dans le Concepteur de Xamarin pour iOS

_Le Concepteur de Xamarin pour iOS prend en charge le rendu des contrôles personnalisés créés dans votre projet ou référencés à partir de sources externes telles que le Store du composant Xamarin._

Le Concepteur de Xamarin pour iOS est un outil puissant permettant de visualiser l’interface utilisateur d’une application et fournit WYSIWYG édition prise en charge pour la plupart des vues d’iOS et les contrôleurs d’affichage. Votre application peut également contenir des contrôles personnalisés qui étendent ceux intégrés dans iOS. Si ces contrôles personnalisés sont écrits avec quelques recommandations à l’esprit, elles peuvent également être rendues par le concepteur, en fournissant une expérience d’édition encore plus riche iOS. Ce document examine ces instructions.

## <a name="requirements"></a>Configuration requise

Un contrôle qui répond à toutes les exigences suivantes s’affichera sur l’aire de conception :

1.  Il est une sous-classe directe ou indirecte de [UIView](xref:UIKit.UIView) ou [UIViewController](xref:UIKit.UIViewController). Autres [NSObject](xref:Foundation.NSObject) sous-classes apparaît sous la forme d’une icône sur l’aire de conception.
2.  Il a un [RegisterAttribute](xref:Foundation.RegisterAttribute) à exposer à Objective-C.
3.  Il a [le constructeur de IntPtr requis](~/ios/internals/api-design/index.md).
4.  Il implémente soit le [IComponent](xref:System.ComponentModel.IComponent) interface ou a un [DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute) définie sur True.

Les contrôles définis dans le code qui remplissent les conditions ci-dessus seront affiche dans le concepteur lorsque leur projet conteneur est compilé pour le simulateur. Par défaut, tous les contrôles personnalisés seront affiche dans le **des composants personnalisés** section de la **boîte à outils**. Toutefois, le [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute) peut être appliqué à la classe du contrôle personnalisé pour spécifier une autre section.

Le concepteur ne prend pas en charge le chargement des bibliothèques Objective-C de fournisseurs tiers.

## <a name="custom-properties"></a>Propriétés personnalisées

Une propriété déclarée par un contrôle personnalisé s’affiche dans le panneau de la propriété si les conditions suivantes sont remplies :

1.  La propriété a un accesseur Get public et un accesseur Set.
1.  La propriété a une [ExportAttribute](xref:Foundation.ExportAttribute) ainsi qu’un [BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) définie sur True.
1.  Le type de propriété est un type numérique, le type d’énumération, la chaîne, la bool, [SizeF](xref:System.Drawing.SizeF), [UIColor](xref:UIKit.UIColor), ou [UIImage](xref:UIKit.UIImage). Cette liste de types pris en charge peut être développée à l’avenir.


La propriété peut également être décorée avec un [attribut DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute) pour spécifier l’étiquette qui s’affiche pour lui dans le panneau de la propriété.

## <a name="initialization"></a>Initialisation

Pour `UIViewController` sous-classes, vous devez utiliser le [ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad) méthode pour le code qui dépend des vues que vous avez créé dans le concepteur.

Pour `UIView` et d’autres `NSObject` sous-classes, le [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib) méthode est l’emplacement recommandé pour effectuer l’initialisation de votre contrôle personnalisé après qu’il est chargé à partir du fichier de disposition. Il s’agit, car toutes les propriétés personnalisées définies dans le panneau de la propriété ne seront pas définies lorsque le constructeur du contrôle est exécuté, mais ils seront définis avant `AwakeFromNib` est appelée :


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

Si le contrôle est également conçu pour être créées directement à partir de code, vous souhaiterez créer une méthode qui a le code d’initialisation commun, comme suit :

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

## <a name="property-initialization-and-awakefromnib"></a>AwakeFromNib et l’initialisation des propriétés

Être vigilant sur quand et où initialiser des propriétés concevables dans un composant personnalisé à ne remplacent pas les valeurs qui ont été définis dans le concepteur iOS. Par exemple, prenez le code suivant :

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

Le `CustomView` composant expose un `Counter` propriété qui peut être définie par le développeur à l’intérieur du concepteur iOS. Toutefois, quelle que soit la valeur est définie dans le concepteur, la valeur de la `Counter` propriété ne sera toujours de zéro (0). En voici les raisons :

-  Une instance de la `CustomControl` est agrandie à partir du fichier de la table de montage séquentiel.
-  Toutes les propriétés modifiées dans le concepteur iOS sont définies (telles que la définition de la valeur de la `Counter` à deux (2), par exemple).
-  Le `AwakeFromNib` méthode est exécutée et un appel est effectué pour le composant `Initialize` (méthode).
-  À l’intérieur de `Initialize` la valeur de la `Counter` propriété est réinitialisée sur zéro (0).


Pour corriger cette situation, soit initialiser le `Counter` propriété ailleurs (par exemple, comme dans le constructeur du composant) ou vous ne substituez pas la `AwakeFromNib` méthode et appel `Initialize` si le composant ne requiert aucune initialisation supplémentaire en dehors de ce que est en cours de traitement par ses constructeurs.

## <a name="design-mode"></a>Mode Création

Sur l’aire de conception, un contrôle personnalisé doit respecter quelques restrictions :

-  Ressources de groupe d’application ne sont pas disponibles en mode Création. Les images sont disponibles lors du chargement via [UIImage méthodes](xref:UIKit.UIImage) .
-  Les opérations asynchrones, telles que les demandes web, ne doivent pas être effectuées en mode Création. L’aire de conception ne prend pas en charge l’animation ou les autres mises à jour asynchrones à l’interface utilisateur du contrôle.


Un contrôle personnalisé peut implémenter [IComponent](xref:System.ComponentModel.IComponent) et utiliser le [DesignMode](xref:System.ComponentModel.ISite.DesignMode) propriété pour vérifier s’il est sur l’aire de conception. Dans cet exemple, l’étiquette affiche « Mode de conception » sur l’aire de conception et le « Runtime » lors de l’exécution :

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

Vous devez toujours vérifier le `Site` propriété pour `null` avant d’essayer d’accéder à tous ses membres. Si `Site` est `null`, il est prudent de considérer le contrôle n’est pas en cours d’exécution dans le concepteur.
En mode Création, `Site` définira après l’exécution par le constructeur du contrôle et avant `AwakeFromNib` est appelée.

## <a name="debugging"></a>Débogage

Un contrôle qui répond aux exigences ci-dessus sera affiché dans la boîte à outils et rendu sur la surface.
Si un contrôle n’est pas affiché, recherchez les bogues dans le contrôle ou une de ses dépendances.

L’aire de conception peut souvent intercepter les exceptions levées par des contrôles individuels tout en continuant à restituer les autres contrôles. Le contrôle défaillant est remplacé par un espace réservé rouge, et vous pouvez afficher la trace de l’exception en cliquant sur l’icône d’exclamation :

 ![](ios-designable-controls-overview-images/exception-box.png "Un contrôle défectueux en tant qu’espace réservé rouge et les détails d’exception")

Si les symboles de débogage sont disponibles pour le contrôle, la trace auront des noms de fichier et les numéros de ligne. Double-cliquez sur une ligne dans la trace de pile passera à cette ligne dans le code source.

Si le concepteur ne peut pas isoler le contrôle défectueux, un message d’avertissement s’affiche en haut de l’aire de conception :

 ![](ios-designable-controls-overview-images/info-bar.png "Un message d’avertissement en haut de l’aire de conception")

Affichage complet reprendra lorsque le contrôle défectueux est fixe ou supprimé dans l’aire de conception.

## <a name="summary"></a>Récapitulatif

Cet article a présenté la création et l’application de contrôles personnalisés dans le concepteur iOS. Tout d’abord, il décrit les exigences que les contrôles doivent satisfaire pour être restitués sur l’aire de conception et exposent des propriétés personnalisées dans le volet de la propriété. Ensuite, il examiné le code-behind - l’initialisation du contrôle et la propriété DesignMode. Enfin, il décrit ce qui se passe lorsque les exceptions sont levées et comment résoudre ce problème.