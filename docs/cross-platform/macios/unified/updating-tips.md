---
title: Conseils pour la mise à jour du code vers l’API unifiée
description: Ce document aborde les erreurs courantes et divers conseils utiles lors de la mise à jour d’une application pour utiliser l’API unifiée de Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: 62ef02d276e9c98e07f5e0d1b9ddec1b0874a99a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829628"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Conseils pour la mise à jour du code vers l’API unifiée

Lors de la mise à jour des solutions Xamarin plus anciennes vers l’API unifiée, les erreurs suivantes peuvent se produire.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException n’a pas trouvé le storyboard erreur

Il existe un [bogue](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) dans la version actuelle de Visual Studio pour Mac qui peut se produire après l’utilisation de l’outil de migration automatisé pour convertir votre projet à l’API unifiée. Après la mise à jour, si vous obtenez un message d’erreur sous la forme :

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Vous pouvez effectuer les opérations suivantes pour résoudre ce problème, recherchez le fichier cible de build suivant :

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

Dans ce fichier, vous devez trouver la déclaration suivante de la cible :

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Et ajoutez le `DependsOnTargets="_CollectBundleResources"` attribut. Comme ceci :

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Enregistrez le fichier, redémarrez Visual Studio pour Mac et effectuer un nettoyage & reconstruire de votre projet. Un correctif pour résoudre ce problème doit être libérée par Xamarin sous peu.

## <a name="useful-tips"></a>Conseils utiles

Après avoir utilisé l’outil de migration, vous pouvez toujours obtenir des erreurs du compilateur intervention manuelle.
Certaines choses peuvent être corrigées manuellement sont les suivantes :

* Comparaison `enum`s peut nécessiter un `(int)` cast.

* `NSDictionary.IntValue` renvoie désormais un `nint`, il existe un `Int32Value` qui peut être utilisée à la place.

* `nfloat` et `nint` types ne peuvent pas être marqués `const`;   `static readonly nint` constitue une alternative raisonnable.

* Ce qui permet d’être directement dans le `MonoTouch.` espace de noms sont désormais généralement dans le `ObjCRuntime.` espace de noms (par exemple : `MonoTouch.Constants.Version` est désormais `ObjCRuntime.Constants.Version`).

* Code qui sérialise les objets peuvent être supprimés lors de la tentative de sérialisation `nint` et `nfloat` types. Veillez à vérifier que votre code de sérialisation fonctionne comme prévu après la migration.

* Parfois, l’outil automatisé absences dans le code à l’intérieur `#if #else` directives de compilation conditionnelle. Dans ce cas, vous devrez apporter les corrections manuellement (voir les erreurs courantes ci-dessous).

* Les méthodes exportées manuellement à l’aide de `[Export]` ne peut pas être corrigée automatiquement par l’outil de migration, par exemple dans cette snippert de code que vous devez mettre à jour manuellement le type de retour pour `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

* L’API unifiée ne fournit pas une conversion implicite entre NSDate et DateTime de .NET, car il n’est pas une conversion sans perte. Pour éviter les erreurs liées aux `DateTimeKind.Unspecified` convertir .NET `DateTime` locaux ou l’heure UTC avant d’effectuer un cast vers `NSDate`.

* Les méthodes objective-C catégorie sont maintenant générés en tant que méthodes d’extension dans l’API unifiée. Par exemple, le code qui utilisait précédemment `UIView.DrawString` serait maintenant référencer `NSString.DrawString` dans l’API unifiée.

* Code à l’aide des classes AVFoundation avec `VideoSettings` de modifier et pour utiliser le `WeakVideoSettings` propriété. Cela nécessite un `Dictionary`, qui est disponible en tant que propriété sur les classes de paramètres, par exemple :

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

* Le NSObject `.ctor(IntPtr)` constructeur a été remplacé par le public à protégé ([pour empêcher toute utilisation incorrecte](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

* `NSAction` a été [remplacé](~/cross-platform/macios/unified/overview.md#NSAction) avec .NET standard `Action`. Des délégués simple (paramètre unique) ont également été remplacés par `Action<T>`.

Enfin, reportez-vous à la [différences d’API unifiée de classique v](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) pour rechercher les modifications apportées aux API dans votre code. Recherche [cette page](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) aidera à trouver les API classiques et qu’ils ont été mis à jour.

> [!NOTE]
> Le `MonoTouch.Dialog` espace de noms reste le même après la migration. Si votre code utilise **MonoTouch.Dialog** vous devez continuer à utiliser cet espace de noms ; *pas* modifier `MonoTouch.Dialog` à `Dialog`!

## <a name="common-compiler-errors"></a>Erreurs de compilateur communes

Autres exemples d’erreurs courantes sont répertoriées ci-dessous, ainsi que la solution :

**Erreur CS0012 : Le type 'MonoTouch.UIKit.UIView' est défini dans un assembly qui n’est pas référencé.**

Correctif : Cela signifie généralement que le projet fait référence à un composant ou un package NuGet qui n’a pas été créée avec l’API unifiée. Vous devez supprimer et rajouter tous les composants et NuGet packages. Si cela ne résout pas l’erreur, la bibliothèque externe ne peut pas en charge l’API unifiée.

**Erreur MT0034 : Ne peut pas inclure 'monotouch.dll' et 'Xamarin.iOS.dll' dans le même projet Xamarin.iOS - 'Xamarin.iOS.dll' est référencé explicitement, alors que « monotouch.dll » est référencé par ' Xamarin.Mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null'.**

Correctif : Supprimer le composant qui provoque cette erreur, ajoutez de nouveau au projet.

**Erreur CS0234 : Le nom du type ou espace de noms 'Base' n’existe pas dans l’espace de noms « MonoTouch ». Une référence d’assembly est-elle manquante ?**

Correctif : L’outil de migration automatisé dans Visual Studio pour Mac *doit* tout mettre à jour `MonoTouch.Foundation` fait référence à `Foundation`, mais dans certains cas, elles devront être mis à jour manuellement. Des erreurs similaires peuvent apparaître pour les autres espaces de noms contenu auparavant dans `MonoTouch`, tel que `UIKit`.

**Erreur CS0266 : Ne peut pas convertir implicitement le type 'double' à 'System.float'**

Correctif : modifier le type et casté en `nfloat`. Cette erreur peut également se produire pour les autres types avec des équivalents 64 bits (tels que `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Erreur CS0266 : Impossible de convertir implicitement le type « CoreGraphics.CGRect' à « System.Drawing.RectangleF ». Une conversion explicite existe (un cast est-il manquant ?)**

Correctif : Modifier les instances à `RectangleF` à `CGRect`, `SizeF` à `CGSize`, et `PointF` à `CGPoint`. L’espace de noms `using System.Drawing;` doit être remplacé par `using CoreGraphics;` (si elle n’est pas déjà présent).

**erreur CS1502 : La meilleure méthode surchargée pour ' CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])' a des arguments non valides**

Correctif : Modifiez le type de tableau `nfloat[]` et effectuez un cast explicitement `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Erreur CS0115 : 'WordsTableSource.RowsInSection (UIKit.UITableView, int)' est marqué comme un remplacement, mais aucune méthode adéquate trouvée pour remplacer**

Correctif : Modifier les types valeur et paramètre de retour à `nint`. Cela se produit souvent dans les substitutions de méthodes telles que celles sur `UITableViewSource`, y compris `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Erreur CS0508 : `WordsTableSource.NumberOfSections(UIKit.UITableView)`: type de retour doit être 'System.nint' pour correspondre au membre substitué `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Correctif : Quand le type de retour est passé à `nint`, effectuer un cast de la valeur de retour pour `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Erreur CS1061 : Le type 'CoreGraphics.CGPath' ne contient pas d’une définition pour 'AddElipseInRect'**

Correctif : Corrigez l’orthographe pour `AddEllipseInRect`. Autres modifications de nom :

* Remplacez « Color.Black » `NSColor.Black`.
* Remplacez MapKit 'AddAnnotation' `AddAnnotations`.
* Remplacez AVFoundation 'DataUsingEncoding' `Encode`.
* Remplacez AVFoundation 'AVMetadataObject.TypeQRCode' `AVMetadataObjectType.QRCode`.
* Remplacez AVFoundation 'VideoSettings' `WeakVideoSettings`.
* Modifier PopViewControllerAnimated à `PopViewController`.
* Remplacez CoreGraphics 'CGBitmapContext.SetRGBFillColor' `SetFillColor`.

**Erreur CS0546 : substitution impossible, car 'MapKit.MKAnnotation.Coordinate' n’a pas d’accesseur set substituable (CS0546)**

Lors de la création d’une annotation personnalisée en sous-classant MKAnnotation le champ de coordonnées n’a pas d’accesseur Set, uniquement un accesseur Get.

[Corriger](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Ajouter un champ pour effectuer le suivi de la coordonnée
* retourner ce champ dans l’accesseur Get de la propriété de coordonnée
* Substituez la méthode SetCoordinate et définissez votre champ
* Appelez SetCoordinate dans votre constructeur avec le paramètre de coordonnées passé dans

Celui-ci doit se présenter comme suit :

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>Liens associés

- [La mise à jour des applications](~/cross-platform/macios/unified/updating-apps.md)
- [La mise à jour des applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [La mise à jour des applications Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [La mise à jour des applications Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [La mise à jour de liaisons](~/cross-platform/macios/unified/update-binding.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Différences d’API unifiée de vs classiques](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
