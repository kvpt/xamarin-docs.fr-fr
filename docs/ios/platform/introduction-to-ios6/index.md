---
title: Introduction à iOS 6
description: Ce document contient des liens vers des guides qui décrivent les fonctionnalités introduites dans iOS 6. Vues de collection, PassKit, l’infrastructure des réseaux sociaux, et les modifications apportées à StoreKit sont tous abordées.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f0cfeaa049355c2b524ead748696eafd884a1c54
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865702"
---
# <a name="introduction-to-ios-6"></a>Introduction à iOS 6

_iOS 6 inclut une variété de nouvelles technologies de développement d’applications, ce qui entraîne une Xamarin.iOS 6 C# les développeurs._

[![](images/ios6-large.jpg "Le logo iOS 6")](images/ios6-large.jpg#lightbox)

IOS 6 et 6 de Xamarin.iOS, les développeurs peuvent désormais une multitude de fonctionnalité à leur disposition pour créer des applications iOS, y compris ceux qui cible iPhone 5.
Ce document répertorie quelques-unes des plus remarquables fonctions qui sont disponibles et des liens vers des articles pour chaque rubrique. En outre, il touche sur quelques modifications qui seront importantes lorsque les développeurs déplacent à iOS 6 et la nouvelle résolution d’iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introduction aux vues de Collection](~/ios/user-interface/controls/uicollectionview.md)

Vues de collection autoriser le contenu à afficher à l’aide de dispositions arbitraires. Ils permettent de créer facilement des dispositions de type grille dès le départ, lors de la prise en charge des dispositions personnalisées. Pour plus d’informations, consultez, le [Introduction aux vues de Collection](~/ios/user-interface/controls/uicollectionview.md) [ ](~/ios/user-interface/controls/uicollectionview.md)guide.


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Présentation de PassKit](~/ios/platform/passkit.md)

Le framework PassKit permet aux applications d’interagir avec les passes numériques qui sont gérés dans l’application de livret. Pour plus d’informations, consultez, le [Introduction au guide du Kit de passer](~/ios/platform/passkit.md).


## <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Introduction à EventKit](~/ios/platform/eventkit.md)

Le framework EventKit fournit un moyen d’accéder aux calendriers, événements de calendrier et les données des rappels qui stocke de base de données de calendrier. Accès aux calendriers et calendrier événements est disponible depuis iOS 4, mais iOS 6 expose désormais accès aux données de rappels. Pour plus d’informations, consultez le [je](~/ios/platform/eventkit.md) [Introduction aux EventKit](~/ios/platform/eventkit.md) guide.


## <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introduction à l’infrastructure des réseaux sociaux](~/ios/platform/social-framework.md)

L’infrastructure des réseaux sociaux fournit une API unifiée pour interagir avec les réseaux sociaux, y compris Twitter et Facebook, mais aussi SinaWeibo pour les utilisateurs en Chine. Pour plus d’informations, consultez, le [Introduction à l’infrastructure des réseaux sociaux](~/ios/platform/social-framework.md) guide.


## <a name="changes-to-storekitchanges-to-storekitmd"></a>[Modifications apportées à StoreKit](changes-to-storekit.md)

Apple a introduit deux nouvelles fonctionnalités dans le Kit de Store : achat et téléchargement iTunes ou contenu de l’App Store à partir de votre application et qui héberge vos fichiers de contenu pour les achats dans l’application !. Pour plus d’informations, consultez, le [change au Store Kit](changes-to-storekit.md) guide.


## <a name="other-changes"></a>Autres modifications


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload et ViewDidUnload déconseillée

Le `ViewWillUnload` et `ViewDidUnload` méthodes de `UIViewController` sont appelés n’est plus dans iOS 6. Dans les versions précédentes d’e/s, ces méthodes peuvent avoir été utilisées par les applications pour l’enregistrement de l’état avant décharge d’une vue et le code de nettoyage respectivement.

Par exemple, Visual Studio pour Mac créerait une méthode appelée `ReleaseDesignerOutlets`, comme indiqué ci-dessous, qui serait ensuite appelé à partir de `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

Toutefois, dans iOS 6, il n’est plus nécessaire d’appeler `ReleaseDesignerOutlets`.   
   
   
   
Pour le code de nettoyage, utilisez des applications iOS 6 `DidReceiveMemoryWarning`. Toutefois, le code qui appelle `Dispose` doit être utilisée avec parcimonie et uniquement pour la mémoire intensif objets comme indiqué ci-dessous :

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Là encore, l’appel `Dispose` comme ci-dessus doit rarement nécessaire. En général que le meilleur parti d’applications doivent faire est de supprimer des gestionnaires d’événements.

Dans le cas d’enregistrement de l’état, les applications peuvent effectuer cela dans `ViewWillDisappear` et `ViewDidDisappear` au lieu de `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 résolution

iPhone 5 appareils ont une résolution de 640 x 1136. Les applications ciblant les versions précédentes d’e/s apparaîtra letterboxed lorsque vous travaillez sur un iPhone 5, comme indiqué ci-dessous :

 [![](images/01-letterboxed.png "Les applications ciblant les versions précédentes d’e/s s’afficheront letterboxed lors de l’exécuter sur un iPhone 5")](images/01-letterboxed.png#lightbox)

Dans l’ordre d’affichage de l’application plein écran sur iPhone 5, ajoutez simplement une image nommée `Default-568h@2x.png` ayant une résolution de 640 x 1136. La capture d’écran suivante montre l’application en cours d’exécution une fois que cette image a été incluse :

 [![](images/02-fullscreen.png "Cette capture d’écran montre l’application en cours d’exécution une fois que cette image a été incluse")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Sous-classement UINavigationBar

Dans iOS 6 `UINavigationBar` peuvent être sous-classés. Cela permet un contrôle supplémentaire de l’apparence de la `UINavigationBar`. Par exemple, les applications peuvent sous-classe pour ajouter des sous-vues, d’animer ces vues et de modifier les limites de la `UINavigationBar`.

Le code ci-dessous montre un exemple d’un sous-classé `UINavigationBar` qui ajoute un `UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

Pour ajouter un sous-classé `UINavigationBar` à un `UINavigationController`, utilisez le `UINavigationController` constructeur qui prend le type de la `UINavigationBar` et `UIToolbar`, comme illustré ci-dessous :

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

L’utilisation de cette `UINavigationBar` sous-classe entraîne l’affichage de l’image affichée comme indiqué dans la capture d’écran suivante :

 [![](images/03-navbar.png "L’utilisation de cette UINavigationBar sous-classe la vue de l’image affichée comme indiqué dans cette capture d’écran")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientation de l’interface

Avant d’iOS 6 applications peut remplacer `ShouldAutorotateToInterfaceOrientation`, retournant true pour les orientations contrôleur spécifique pris en charge. Par exemple, le code suivant serait être utilisé pour prendre en charge de portrait uniquement :

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

Dans iOS 6 `ShouldAutorotateToInterfaceOrientation` est déconseillée.
À la place les applications peuvent substituer `GetSupportedInterfaceOrientations` sur le contrôleur d’affichage racine comme indiqué ci-dessous :

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

Sur iPad, par défaut est toutes les quatre orientations si `GetSupportedInterfaceOrientation` n’est pas implémentée. Sur iPhone et iPod Touch, la valeur par défaut est toutes les orientations sauf `PortraitUpsideDown`.
