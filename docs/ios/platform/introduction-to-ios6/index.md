---
title: Introduction à iOS 6
description: Ce document contient des liens vers des guides qui décrivent les fonctionnalités introduites dans iOS 6. Les vues de collection, PassKit, l’infrastructure sociale et les modifications apportées à StoreKit sont toutes abordées.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: dc8f2c3a11dd283db46fd0a2530fcca435da75f9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752053"
---
# <a name="introduction-to-ios-6"></a>Introduction à iOS 6

_iOS 6 comprend une variété de nouvelles technologies pour le développement d’applications, que Xamarin. iOS 6 C# apporte aux développeurs._

[![](images/ios6-large.jpg "Logo iOS 6")](images/ios6-large.jpg#lightbox)

Avec iOS 6 et Xamarin. iOS 6, les développeurs disposent désormais d’une multitude de fonctionnalités pour créer des applications iOS, y compris celles qui ciblent iPhone 5.
Ce document répertorie quelques-unes des nouvelles fonctionnalités les plus intéressantes disponibles, ainsi que des liens vers des articles pour chaque rubrique. En outre, il s’intéresse à quelques modifications qui seront importantes à mesure que les développeurs passeront à iOS 6 et à la nouvelle résolution de iPhone 5.

## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Présentation des vues de collection](~/ios/user-interface/controls/uicollectionview.md)

Les vues de collection permettent d’afficher le contenu à l’aide de dispositions arbitraires. Ils permettent de créer facilement des dispositions de type grille prêtes à l’emploi, tout en prenant également en charge les dispositions personnalisées. Pour plus d’informations, consultez le guide [présentation des vues](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md).

## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Présentation de PassKit](~/ios/platform/passkit.md)

L’infrastructure PassKit permet aux applications d’interagir avec les passes numériques qui sont gérées dans l’application du plan d’application. Pour plus d’informations, consultez le [Guide d’introduction au kit Pass](~/ios/platform/passkit.md).

## <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Présentation de EventKit](~/ios/platform/eventkit.md)

L’infrastructure EventKit permet d’accéder aux données calendriers, événements de calendrier et rappels stockées dans la base de données de calendrier. L’accès aux calendriers et aux événements de calendrier est disponible depuis iOS 4, mais iOS 6 expose désormais l’accès aux données de rappels. Pour plus d’informations, consultez le guide [I](~/ios/platform/eventkit.md) [Introduction to EventKit](~/ios/platform/eventkit.md) .

## <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Présentation de l’infrastructure sociale](~/ios/platform/social-framework.md)

L’infrastructure sociale fournit une API unifiée pour l’interaction avec les réseaux sociaux, y compris Twitter et Facebook, ainsi que SinaWeibo pour les utilisateurs en Chine. Pour plus d’informations, consultez la présentation du guide [de l’infrastructure sociale](~/ios/platform/social-framework.md) .

## <a name="changes-to-storekitchanges-to-storekitmd"></a>[Modifications apportées à StoreKit](changes-to-storekit.md)

Apple a introduit deux nouvelles fonctionnalités dans le kit de boutique : achat et téléchargement d’iTunes ou du contenu App Store à partir de votre application, et Hébergement de vos fichiers de contenu pour les achats dans l’application !. Pour plus d’informations, consultez le guide des [modifications apportées au kit de stockage](changes-to-storekit.md) .

## <a name="other-changes"></a>Autres modifications

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload et ViewDidUnload déconseillé

Les `ViewWillUnload` méthodes `ViewDidUnload` et de`UIViewController` ne sont plus appelées dans iOS 6. Dans les versions précédentes d’iOS, ces méthodes utilisaient peut-être des applications pour enregistrer l’état avant un déchargement de la vue, et nettoyer le code respectivement.

Par exemple, Visual Studio pour Mac créerait une méthode appelée `ReleaseDesignerOutlets`, comme indiqué ci-dessous, qui serait ensuite `ViewDidUnload`appelée à partir de :

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

Pour le code de nettoyage, les applications iOS `DidReceiveMemoryWarning`6 doivent utiliser. Toutefois, le code qui `Dispose` appelle doit être utilisé avec modération et uniquement pour les objets gourmands en mémoire, comme indiqué ci-dessous :

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Là encore, `Dispose` l’appel de comme ci-dessus doit rarement être nécessaire. En général, la plupart des applications doivent effectuer la suppression de gestionnaires d’événements.

Dans le cas de l’enregistrement de l’État, les applications `ViewWillDisappear` peuvent `ViewDidDisappear` effectuer cette `ViewWillUnload`exécution dans et au lieu de.

### <a name="iphone-5-resolution"></a>Résolution iPhone 5

les appareils iPhone 5 ont une résolution 640x1136. Les applications qui ciblaient des versions précédentes d’iOS apparaîtront letterboxed lorsqu’elles seront exécutées sur un iPhone 5, comme indiqué ci-dessous :

 [![](images/01-letterboxed.png "Les applications qui ciblaient des versions précédentes d’iOS apparaîtront letterboxed lorsqu’elles seront exécutées sur un iPhone 5")](images/01-letterboxed.png#lightbox)

Pour que l’application s’affiche en plein écran sur iPhone 5, ajoutez simplement une image nommée `Default-568h@2x.png` qui a une résolution de 640x1136. La capture d’écran suivante montre l’application en cours d’exécution une fois que cette image a été incluse :

 [![](images/02-fullscreen.png "Cette capture d’écran montre l’application en cours d’exécution après l’inclusion de cette image")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Sous-classe UINavigationBar

Dans iOS 6 `UINavigationBar` peut être sous-classé. Cela permet un contrôle supplémentaire de l’apparence du `UINavigationBar`. Par exemple, les applications peuvent être sous-classées pour ajouter des sous-affichages, animer ces vues `UINavigationBar`et modifier les limites de.

Le code ci-dessous montre un exemple d’une sous `UINavigationBar` -classe qui `UIImageView`ajoute un :

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

Pour ajouter une sous-classe `UINavigationBar` à un `UINavigationController`, utilisez le `UINavigationController` constructeur qui prend le type de `UINavigationBar` et `UIToolbar`, comme indiqué ci-dessous :

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

L’utilisation `UINavigationBar` de cette sous-classe entraîne l’affichage de la vue image, comme illustré dans la capture d’écran suivante :

 [![](images/03-navbar.png "L’utilisation de cette sous-classe UINavigationBar entraîne l’affichage de la vue image comme indiqué dans cette capture d’écran.")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientation de l’interface

Avant les applications `ShouldAutorotateToInterfaceOrientation`iOS 6, retourne la valeur true pour toutes les orientations prises en charge par le contrôleur particulier. Par exemple, le code suivant est utilisé pour prendre en charge uniquement le mode portrait :

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

Dans iOS 6 `ShouldAutorotateToInterfaceOrientation` est déconseillé.
Au lieu de cela, `GetSupportedInterfaceOrientations` les applications peuvent se substituer au contrôleur d’affichage racine comme indiqué ci-dessous :

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

Sur iPad, la valeur par défaut est quatre orientations si `GetSupportedInterfaceOrientation` n’est pas implémenté. Sur iPhone et iPod Touch, la valeur par défaut est toutes les `PortraitUpsideDown`orientations, à l’exception de.
