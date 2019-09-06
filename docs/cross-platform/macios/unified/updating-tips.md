---
title: Conseils pour la mise à jour du code vers l’API unifiée
description: Ce document traite des erreurs courantes et divers conseils utiles lors de la mise à jour d’une application pour utiliser la API unifiée de Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: conceptdev
ms.author: crdun
ms.openlocfilehash: 0d1f09f8318f1292f6c9b65627d9b659f8fc466b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280788"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Conseils pour la mise à jour du code vers l’API unifiée

Lors de la mise à jour des solutions Xamarin plus anciennes vers le API unifiée, les erreurs suivantes peuvent se produire.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException n’a pas pu trouver l’erreur de Storyboard

Il existe un [bogue](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) dans la version actuelle de Visual Studio pour Mac qui peut se produire après l’utilisation de l’outil de migration automatisé pour convertir votre projet en API unifiées. Après la mise à jour, si vous recevez un message d’erreur au format suivant :

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Vous pouvez effectuer les opérations suivantes pour résoudre ce problème, recherchez le fichier cible de build suivant :

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

Dans ce fichier, vous devez rechercher la déclaration cible suivante :

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Et y ajouter `DependsOnTargets="_CollectBundleResources"` l’attribut. Comme ceci :

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Enregistrez le fichier, redémarrez Visual Studio pour Mac et effectuez une nouvelle & régénération de votre projet. Un correctif pour résoudre ce problème devrait bientôt être publié par Xamarin.

## <a name="useful-tips"></a>Conseils utiles

Après l’utilisation de l’outil de migration, vous pouvez toujours recevoir des erreurs de compilateur nécessitant une intervention manuelle.
Voici quelques éléments que vous devrez peut-être corriger manuellement :

- La `enum`Comparaison de peut nécessiter un `(int)` cast.

- `NSDictionary.IntValue`retourne maintenant un `nint`, `Int32Value` qui peut être utilisé à la place.

- `nfloat`les `nint` types et ne peuvent `const`pas être marqués ; `static readonly nint` est une alternative raisonnable.

- Les éléments qui étaient utilisés directement dans l' `MonoTouch.` espace de noms se trouvent désormais `ObjCRuntime.` dans l’espace de noms `MonoTouch.Constants.Version` (par `ObjCRuntime.Constants.Version`exemple : est maintenant).

- Le code qui sérialise `nint` les objets peut s’arrêter lors de la tentative de sérialisation et `nfloat` de types. Veillez à vérifier que votre code de sérialisation fonctionne comme prévu après la migration.

- Parfois, l’outil automatisé ignore `#if #else` le code dans les directives de compilateur conditionnel. Dans ce cas, vous devez effectuer les corrections manuellement (voir les erreurs courantes ci-dessous).

- Les méthodes exportées manuellement à l’aide `[Export]` de ne peuvent pas être corrigées automatiquement par l’outil de migration, par exemple dans ce code snippert vous devez mettre à jour manuellement le type de retour en : `nfloat`

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- Le API unifiée ne fournit pas de conversion implicite entre NSDate et .NET DateTime, car il ne s’agit pas d’une conversion sans perte. Pour éviter les erreurs liées `DateTimeKind.Unspecified` à la conversion `DateTime` de .net en locale ou UTC avant `NSDate`d’effectuer un cast en.

- Les méthodes de catégorie objective-C sont maintenant générées comme méthodes d’extension dans l’API unifiée. Par exemple, le code utilisé `UIView.DrawString` précédemment fait désormais référence `NSString.DrawString` à la API unifiée.

- Le code qui utilise les `VideoSettings` classes AVFoundation avec doit changer `WeakVideoSettings` pour utiliser la propriété. Cela nécessite un `Dictionary`, qui est disponible en tant que propriété sur les classes de paramètres, par exemple :

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- Le constructeur `.ctor(IntPtr)` NSObject a été modifié de public en protected ([pour empêcher une utilisation incorrecte](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

- `NSAction`a été [remplacé](~/cross-platform/macios/unified/overview.md#NSAction) par le .NET `Action`standard. Certains délégués simples (paramètre unique) ont également été remplacés par `Action<T>`.

Enfin, reportez-vous aux [différences de API unifiée Classic v](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) pour rechercher les modifications apportées aux API dans votre code. La recherche de [cette page](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) permet de trouver des API classiques et leurs mises à jour.

> [!NOTE]
> L' `MonoTouch.Dialog` espace de noms reste le même après la migration. Si votre code utilise des **boîtes de dialogue monotactiles** , vous devez continuer à utiliser cet espace de `Dialog`noms-ne *pas* changer `MonoTouch.Dialog` en !

## <a name="common-compiler-errors"></a>Erreurs courantes du compilateur

D’autres exemples d’erreurs courantes sont répertoriés ci-dessous, ainsi que la solution :

**Erreur CS0012 : Le type « MonoTouch. UIKit. UIView » est défini dans un assembly qui n’est pas référencé.**

Correctif : Cela signifie généralement que le projet fait référence à un composant ou à un package NuGet qui n’a pas été généré avec le API unifiée. Vous devez supprimer et rajouter tous les composants et les packages NuGet. Si cela ne corrige pas l’erreur, il se peut que la bibliothèque externe ne prenne pas encore en charge le API unifiée.

**Erreur MT0034 : Impossible d’inclure à la fois’unitouch. dll’et’Xamarin. iOS. dll’dans le même projet Xamarin. iOS-'Xamarin. iOS. dll’est référencé explicitement, tandis que’unitouch. dll’est référencé par’Xamarin. mobile, version = 0.6.3.0, culture = neutral, PublicKeyToken = null'.**

Correctif : Supprimez le composant à l’origine de cette erreur et rajoutez-le au projet.

**Erreur CS0234 : Le type ou le nom d’espace de noms’Foundation’n’existe pas dans l’espace de noms’MonoTouch'. Manque-t-il une référence d’assembly ?**

Correctif : L’outil de migration automatisée de Visual Studio pour Mac doit `MonoTouch.Foundation` mettre à `Foundation`jour toutes les références à, mais dans certains cas, ceux-ci doivent être mis à jour manuellement. Des erreurs similaires peuvent apparaître pour les autres espaces de noms précédemment `MonoTouch`contenus dans, `UIKit`tels que.

**Erreur CS0266 : Impossible de convertir implicitement le type’double’en’System. float'**

Correctif : modifiez le type et effectuez `nfloat`un cast en. Cette erreur peut également se produire pour les autres types avec des équivalents 64 bits (tels `nint`que,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Erreur CS0266 : Impossible de convertir implicitement le type’CoreGraphics. CGRect’en’System. Drawing. RectangleF'. Une conversion explicite existe (un cast est-il manquant ?)**

Correctif : Remplacez les instances `RectangleF` `CGRect` de`SizeF`par , et`PointF` par. `CGPoint` `CGSize` L’espace `using System.Drawing;` de noms doit être `using CoreGraphics;` remplacé par (s’il n’est pas déjà présent).

**erreur CS1502 : La méthode surchargée correspondant le mieux à’CoreGraphics. CGContext. SetLineDash (System. nfloat, System. nfloat []) 'contient des arguments non valides**

Correctif : Modifiez le type de `nfloat[]` tableau en et `Math.PI`effectuez un cast explicite.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Erreur CS0115 : 'WordsTableSource. RowsInSection (UIKit. UITableView, int) 'est marqué comme remplacement, mais aucune méthode appropriée n’a été trouvée pour la substitution**

Correctif : Remplacez la valeur de retour et les types `nint`de paramètres par. Cela se produit généralement dans les substitutions de méthode, telles `UITableViewSource`que celles `RowsInSection`sur `NumberOfSections`, notamment `TitleForHeader`, `GetViewForHeader` `GetHeightForRow`,,,, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Erreur CS0508 : `WordsTableSource.NumberOfSections(UIKit.UITableView)`: le type de retour doit être’System. nint’pour correspondre au membre substitué`UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Correctif : Lorsque le type de retour est modifié `nint`en, effectuez un cast de `nint`la valeur de retour en.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Erreur CS1061 : Le type’CoreGraphics. CGPath’ne contient pas de définition pour’AddElipseInRect'**

Correctif : Corriger l’orthographe en `AddEllipseInRect`. Les autres modifications de nom sont les suivantes :

- Remplacez « Color. Black » par `NSColor.Black`.
- Remplacez MapKit « AddAnnotation » par `AddAnnotations`.
- Remplacez AVFoundation « DataUsingEncoding » par `Encode`.
- Remplacez AVFoundation « AVMetadataObject. TypeQRCode » par `AVMetadataObjectType.QRCode`.
- Remplacez AVFoundation « VideoSettings » par `WeakVideoSettings`.
- Remplacez PopViewControllerAnimated par `PopViewController`.
- Remplacez CoreGraphics « CGBitmapContext. SetRGBFillColor » par `SetFillColor`.

**Erreur CS0546 : substitution impossible, car’MapKit. MKAnnotation. Coordinate’n’a pas d’accesseur Set substituable (CS0546)**

Quand vous créez une annotation personnalisée en sous-classant MKAnnotation, le champ de coordonnées n’a pas d’accesseur Set, mais uniquement un accesseur Get.

[Correctif](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

- Ajouter un champ pour effectuer le suivi de la coordonnée
- retourne ce champ dans l’accesseur Get de la propriété de coordonnée
- Substituez la méthode SetCoordinate et définissez votre champ
- Appelez SetCoordinate dans le constructeur avec le paramètre de coordonnée passé

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

- [Mise à jour des applications](~/cross-platform/macios/unified/updating-apps.md)
- [Mise à jour des applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Mise à jour des applications Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Mise à jour des applications Xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Mise à jour des liaisons](~/cross-platform/macios/unified/update-binding.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
