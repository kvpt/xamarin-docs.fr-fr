---
title: Guide de référence des types de liaison
description: Ce guide de référence décrit différents attributs et concepts que vous devez comprendre lors de la création de liaisons C# à des bibliothèques objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 764c6303956199982da779d87b0a29977575e767
ms.sourcegitcommit: 4f0223cf13e14d35c52fa72a026b1c7696bf8929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278349"
---
# <a name="binding-types-reference-guide"></a>Guide de référence des types de liaison

Ce document décrit la liste des attributs que vous pouvez utiliser pour annoter vos fichiers de contrat d’API pour piloter la liaison et le code généré

Les contrats d’API Xamarin. iOS et Xamarin. Mac sont écrits en C# principalement comme des définitions d’interface qui définissent la façon dont le code Objective-C est exposé à C#. Le processus implique une combinaison de déclarations d’interface et de définitions de type de base que le contrat d’API peut nécessiter. Pour obtenir une présentation des types de liaison, consultez notre guide d’accompagnement [Binding objective-C Libraries](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Définitions de type

Syntaxe :

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Chaque interface de votre définition de contrat qui a l' [`[BaseType]`](#BaseTypeAttribute) attribut déclare le type de base pour l’objet généré. Dans la déclaration ci-dessus, un `MyType` type C# de classe sera généré et lié à un type objective-C appelé `MyType` .

Si vous spécifiez des types après le TypeName (dans l’exemple ci-dessus `Protocol1` et `Protocol2` ) à l’aide de la syntaxe d’héritage de l’interface, le contenu de ces interfaces est Inline comme s’ils avaient fait partie du contrat pour `MyType` .
La façon dont les surfaces Xamarin. iOS qu’un type adopte un protocole consiste à incorporer toutes les méthodes et propriétés déclarées dans le protocole dans le type lui-même.

L’exemple suivant montre comment la déclaration objective-C pour `UITextField` est définie dans un contrat Xamarin. iOS :

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Serait écrit comme s’il s’agissait d’un contrat d’API C# :

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Vous pouvez contrôler de nombreux autres aspects de la génération de code en appliquant d’autres attributs à l’interface, ainsi que la configuration de l' [`[BaseType]`](#BaseTypeAttribute) attribut.

### <a name="generating-events"></a>Génération d’événements

L’une des fonctionnalités de la conception d’API Xamarin. iOS et Xamarin. Mac est que nous mettons en correspondance les classes de délégué objective-C en tant qu’événements et rappels C#. Les utilisateurs peuvent choisir, par instance, s’ils souhaitent adopter le modèle de programmation objective-C, en affectant à des propriétés telles qu' `Delegate` une instance d’une classe qui implémente les différentes méthodes que le runtime objective-c appellera, ou en choisissant les événements et les propriétés de style C#.

Voyons un exemple d’utilisation du modèle objective-C :

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

Dans l’exemple ci-dessus, vous pouvez voir que nous avons choisi de remplacer deux méthodes, une notification indiquant qu’un événement de défilement a eu lieu et la seconde qui est un rappel qui doit retourner une valeur booléenne indiquant `scrollView` si elle doit faire défiler vers le haut ou pas.

Le modèle C# permet à l’utilisateur de votre bibliothèque d’écouter les notifications à l’aide de la syntaxe d’événement C# ou de la syntaxe de propriété pour raccorder les rappels qui sont supposés retourner des valeurs.

C’est ainsi que le code C# pour la même fonctionnalité ressemble à l’utilisation d’expressions lambda :

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

Dans la mesure où les événements ne retournent pas de valeurs (ils ont un type de retour void), vous pouvez connecter plusieurs copies. `ShouldScrollToTop`N’est pas un événement, mais plutôt une propriété avec le type `UIScrollViewCondition` qui a cette signature :

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Elle retourne une `bool` valeur. dans ce cas, la syntaxe lambda nous permet de retourner simplement la valeur de la `MakeDecision` fonction.

Le générateur de liaison prend en charge la génération d’événements et de propriétés qui lient une classe comme `UIScrollView` avec son `UIScrollViewDelegate` (appelez bien la classe de modèle). pour ce faire, annotez votre [`[BaseType]`](#BaseTypeAttribute) définition avec les `Events` `Delegates` paramètres et (décrits ci-dessous). En plus d’annoter [`[BaseType]`](#BaseTypeAttribute) avec ces paramètres, il est nécessaire d’informer le générateur de quelques autres composants.

Pour les événements qui acceptent plusieurs paramètres (en Objective-C, la Convention est que le premier paramètre dans une classe déléguée est l’instance de l’objet expéditeur) vous devez fournir le nom que vous souhaitez pour la `EventArgs` classe générée. Cette opération s’effectue avec l' [`[EventArgs]`](#EventArgsAttribute) attribut sur la déclaration de méthode dans votre classe de modèle. Par exemple :

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La déclaration ci-dessus génère une `UIImagePickerImagePickedEventArgs` classe qui dérive de `EventArgs` et compresse les deux paramètres, `UIImage` et `NSDictionary` . Le générateur produit ce qui suit :

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Il expose ensuite les éléments suivants dans la `UIImagePickerController` classe :

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Les méthodes de modèle qui retournent une valeur sont liées différemment. Celles-ci requièrent un nom pour le délégué C# généré (la signature de la méthode) et également une valeur par défaut à retourner si l’utilisateur ne fournit pas lui-même une implémentation. Par exemple, la `ShouldScrollToTop` définition est la suivante :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

La version ci-dessus crée un `UIScrollViewCondition` délégué avec la signature qui a été présentée ci-dessus, et si l’utilisateur ne fournit pas d’implémentation, la valeur de retour est true.

En plus de l' [`[DefaultValue]`](#DefaultValueAttribute) attribut, vous pouvez également utiliser l' [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) attribut qui indique au générateur de retourner la valeur du paramètre spécifié dans l’appel ou le [`[NoDefaultValue]`](#NoDefaultValueAttribute) paramètre qui indique au générateur qu’il n’y a pas de valeur par défaut.

<a name="BaseTypeAttribute"></a>

### <a name="basetypeattribute"></a>BaseTypeAttribute

Syntaxe :

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

Vous utilisez la `Name` propriété pour contrôler le nom auquel ce type sera lié dans le monde objective-C. Il est généralement utilisé pour attribuer au type C# un nom qui est conforme aux règles de conception de .NET Framework, mais qui correspond à un nom en Objective-C qui ne respecte pas cette Convention.

Par exemple, dans le cas suivant, nous allons mapper le type objective-C `NSURLConnection` à `NSUrlConnection` , comme les instructions de conception .NET Framework utilisent « URL » au lieu de « URL » :

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

Le nom spécifié est utilisé comme valeur pour l’attribut généré `[Register]` dans la liaison. Si `Name` n’est pas spécifié, le nom abrégé du type est utilisé comme valeur de l' `[Register]` attribut dans la sortie générée.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. Events et BaseType. delegates

Ces propriétés sont utilisées pour piloter la génération d’événements de style C# dans les classes générées. Elles sont utilisées pour lier une classe donnée à sa classe déléguée objective-C. Vous rencontrerez de nombreux cas où une classe utilise une classe déléguée pour envoyer des notifications et des événements. Par exemple `BarcodeScanner` , a une classe auxiliaire `BardodeScannerDelegate` . La `BarcodeScanner` classe a généralement une `Delegate` propriété à laquelle vous assignez une instance `BarcodeScannerDelegate` , alors que cela fonctionne, vous souhaiterez peut-être exposer à vos utilisateurs une interface d’événement de style C#, et dans ce cas, vous utiliseriez les `Events` `Delegates` Propriétés et de l' [`[BaseType]`](#BaseTypeAttribute) attribut.

Ces propriétés sont toujours définies ensemble et doivent avoir le même nombre d’éléments et rester synchronisées. Le `Delegates` tableau contient une chaîne pour chaque délégué faiblement typé que vous souhaitez encapsuler, et le `Events` tableau contient un type pour chaque type que vous souhaitez associer à ce dernier.

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```

#### <a name="basetypekeeprefuntil"></a>BaseType. KeepRefUntil

Si vous appliquez cet attribut lors de la création de nouvelles instances de cette classe, l’instance de cet objet est conservée jusqu’à ce que la méthode référencée par l' `KeepRefUntil` ait été appelée. Cela est utile pour améliorer l’utilisation de vos API, lorsque vous ne souhaitez pas que votre utilisateur conserve une référence à un objet pour utiliser votre code. La valeur de cette propriété est le nom d’une méthode dans la `Delegate` classe. vous devez donc l’utiliser en association avec les `Events` `Delegates` Propriétés et également.

L’exemple suivant montre comment il est utilisé par `UIActionSheet` dans Xamarin. iOS :

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```

<a name="DesignatedDefaultCtorAttribute"></a>

### <a name="designateddefaultctorattribute"></a>DesignatedDefaultCtorAttribute

Quand cet attribut est appliqué à la définition de l’interface, il génère un `[DesignatedInitializer]` attribut sur le constructeur par défaut (généré), qui est mappé au `init` sélecteur.

<a name="DisableDefaultCtorAttribute"></a>

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Quand cet attribut est appliqué à la définition de l’interface, il empêche le générateur de générer le constructeur par défaut.

Utilisez cet attribut lorsque vous avez besoin que l’objet soit initialisé avec l’un des autres constructeurs de la classe.

<a name="PrivateDefaultCtorAttribute"></a>

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Quand cet attribut est appliqué à la définition de l’interface, il marque le constructeur par défaut comme privé. Cela signifie que vous pouvez toujours instancier l’objet de cette classe en interne à partir de votre fichier d’extension, mais il ne sera simplement pas accessible aux utilisateurs de votre classe.

<a name="CategoryAttribute"></a>

### <a name="categoryattribute"></a>CategoryAttribute

Utilisez cet attribut sur une définition de type pour lier des catégories objective-C et exposer celles-ci en tant que méthodes d’extension C# pour mettre en miroir la manière dont objective-C expose les fonctionnalités.

Les catégories sont un mécanisme objective-C utilisé pour étendre l’ensemble de méthodes et de propriétés disponibles dans une classe.   Dans la pratique, ils sont utilisés pour étendre les fonctionnalités d’une classe de base (par exemple `NSObject` ) lorsqu’un Framework spécifique est lié (par exemple `UIKit` ), en rendant leurs méthodes disponibles, mais uniquement si la nouvelle infrastructure est liée.   Dans d’autres cas, ils sont utilisés pour organiser les fonctionnalités dans une classe par fonctionnalité.   Elles sont similaires dans l’esprit des méthodes d’extension C#.

Voici à quoi ressemble une catégorie dans objective-C :

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

L’exemple ci-dessus se trouve dans une bibliothèque qui étendrait `UIView` les instances de avec la méthode `makeBackgroundRed` .

Pour les lier, vous pouvez utiliser l' [`[Category]`](#CategoryAttribute) attribut sur une définition d’interface.   Lors de l’utilisation de l' [`[Category]`](#CategoryAttribute) attribut, la signification de l' [`[BaseType]`](#BaseTypeAttribute) attribut change d’être utilisée pour spécifier la classe de base à étendre, en tant que type à étendre.

L’exemple suivant montre comment les `UIView` extensions sont liées et converties en méthodes d’extension C# :

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

L’opération ci-dessus crée une `MyUIViewExtension` classe qui contient la `MakeBackgroundRed` méthode d’extension.   Cela signifie que vous pouvez maintenant appeler `MakeBackgroundRed` sur n’importe quelle sous `UIView` -classe, ce qui vous donne les mêmes fonctionnalités que celles que vous obtiendriez sur objective-C.

Dans certains cas, vous trouverez des membres **statiques** à l’intérieur de catégories comme dans l’exemple suivant :

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Cela entraînera une définition d’interface C# de catégorie **incorrecte** :

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Cela est incorrect, car pour utiliser l' `BoolMethod` extension, vous avez besoin d’une instance de `FooObject` , mais vous liez une extension **statique** objc. il s’agit d’un effet secondaire en raison du fait que les méthodes d’extension C# sont implémentées.

La seule façon d’utiliser les définitions ci-dessus est d’utiliser le code insupportable suivant :

```csharp
(null as FooObject).BoolMethod (range);
```

Pour éviter cela, nous vous recommandons d’incorporer la `BoolMethod` définition dans la `FooObject` définition d’interface elle-même, ce qui vous permet d’appeler cette extension comme prévu `FooObject.BoolMethod (range)` .

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Nous allons émettre un avertissement (BI1117) chaque fois que nous trouvons un [`[Static]`](#StaticAttribute) membre à l’intérieur d’une [`[Category]`](#CategoryAttribute) définition. Si vous souhaitez vraiment avoir des [`[Static]`](#StaticAttribute) membres à l’intérieur de vos [`[Category]`](#CategoryAttribute) définitions, vous pouvez Silencer l’avertissement en utilisant `[Category (allowStaticMembers: true)]` ou en décorant votre définition de membre ou [`[Category]`](#CategoryAttribute) d’interface avec [`[Internal]`](#InternalAttribute) .

<a name="StaticAttribute_Class"></a>

### <a name="staticattribute"></a>StaticAttribute

Quand cet attribut est appliqué à une classe, il génère simplement une classe statique, qui ne dérive pas de `NSObject` , donc l' [`[BaseType]`](#BaseTypeAttribute) attribut est ignoré. Les classes statiques sont utilisées pour héberger les variables publiques C que vous souhaitez exposer.

Par exemple :

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Génère une classe C# avec l’API suivante :

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Définitions de protocole/modèle

Les modèles sont généralement utilisés par l’implémentation de protocole.
Elles diffèrent dans le fait que le runtime s’inscrit uniquement avec Objective-C, les méthodes qui ont été remplacées.
Dans le cas contraire, la méthode ne sera pas inscrite.

Cela signifie généralement que lorsque vous sous-classez une classe qui a été marquée avec le `ModelAttribute` , vous ne devez pas appeler la méthode de base.   L’appel de cette méthode lève l’exception suivante : Foundation.You_Should_Not_Call_base_In_This_Method. Vous êtes censé implémenter l’intégralité du comportement sur votre sous-classe pour toutes les méthodes que vous substituez.

<a name="AbstractAttribute"></a>

### <a name="abstractattribute"></a>AbstractAttribute

Par défaut, les membres qui font partie d’un protocole ne sont pas obligatoires. Cela permet aux utilisateurs de créer une sous-classe de l' `Model` objet en dérivant simplement de la classe en C# et en substituant uniquement les méthodes qui les intéressent. Parfois, le contrat objective-C requiert que l’utilisateur fournisse une implémentation pour cette méthode (celles-ci sont marquées avec la `@required` directive en objective-c). Dans ce cas, vous devez marquer ces méthodes avec l' `[Abstract]` attribut.

L' `[Abstract]` attribut peut être appliqué à des méthodes ou à des propriétés et oblige le générateur à marquer le membre généré comme abstrait et la classe comme une classe abstraite.

Les éléments suivants proviennent de Xamarin. iOS :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute"></a>

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Spécifie la valeur par défaut qui doit être retournée par une méthode de modèle si l’utilisateur ne fournit pas de méthode pour cette méthode particulière dans l’objet de modèle

Syntaxe :

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Par exemple, dans la classe déléguée imaginaire suivante pour une `Camera` classe, nous fournissons un `ShouldUploadToServer` qui serait exposé en tant que propriété sur la `Camera` classe. Si l’utilisateur de la `Camera` classe ne définit pas explicitement une valeur lambda qui peut répondre à true ou false, la valeur par défaut retournée dans ce cas est false, la valeur que nous avons spécifiée dans l' `DefaultValue` attribut :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Si l’utilisateur définit un gestionnaire dans la classe imaginaire, cette valeur est ignorée :

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

Voir aussi : [`[NoDefaultValue]`](#NoDefaultValueAttribute) , [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) .

<a name="DefaultValueFromArgumentAttribute"></a>

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Syntaxe :

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Cet attribut, lorsqu’il est fourni sur une méthode qui retourne une valeur sur une classe de modèle, indique au générateur de retourner la valeur du paramètre spécifié si l’utilisateur n’a pas fourni sa propre méthode ou lambda.

Exemple :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

Dans le cas ci-dessus, si l’utilisateur de la `NSAnimation` classe a choisi d’utiliser l’un des événements/propriétés C# et n’a pas défini `NSAnimation.ComputeAnimationCurve` sur une méthode ou une expression lambda, la valeur de retour est la valeur passée dans le paramètre Progress.

Voir aussi : [`[NoDefaultValue]`](#NoDefaultValueAttribute) , [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

Il est parfois judicieux de ne pas exposer une propriété d’événement ou de délégué à partir d’une classe de modèle dans la classe d’hôte, de sorte que l’ajout de cet attribut indique au générateur d’éviter la génération de toute méthode décorée avec lui.

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

Cet attribut est utilisé dans les méthodes de modèle qui retournent des valeurs pour définir le nom de la signature de délégué à utiliser.

Exemple :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Avec la définition ci-dessus, le générateur produira la déclaration publique suivante :

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Cet attribut est utilisé pour permettre au générateur de modifier le nom de la propriété générée dans la classe hôte. Parfois, il est utile lorsque le nom de la méthode de la classe FooDelegate est logique pour la classe déléguée, mais qu’il semblerait étrange dans la classe hôte en tant que propriété.

Cela est également très utile (et nécessaire) lorsque vous avez au moins deux méthodes de surcharge qui sont logiques de les garder nommées comme dans la classe FooDelegate, mais que vous souhaitez les exposer dans la classe hôte avec un meilleur nom donné.

Exemple :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Avec la définition ci-dessus, le générateur produira la déclaration publique suivante dans la classe hôte :

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute"></a>

### <a name="eventargsattribute"></a>EventArgsAttribute

Pour les événements qui acceptent plusieurs paramètres (en Objective-C, la Convention est que le premier paramètre dans une classe déléguée est l’instance de l’objet expéditeur) vous devez fournir le nom que vous souhaitez pour la classe EventArgs générée. Cette opération s’effectue avec l' `[EventArgs]` attribut sur la déclaration de méthode dans votre `Model` classe.

Par exemple :

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La déclaration ci-dessus génère une `UIImagePickerImagePickedEventArgs` classe qui dérive de EventArgs et compresse les deux paramètres, `UIImage` et `NSDictionary` . Le générateur produit ce qui suit :

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Il expose ensuite les éléments suivants dans la `UIImagePickerController` classe :

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>EventNameAttribute

Cet attribut est utilisé pour permettre au générateur de modifier le nom d’un événement ou d’une propriété générés dans la classe. Parfois, il est utile lorsque le nom de la méthode de classe de modèle est logique pour la classe de modèle, mais qu’il semblerait étrange dans la classe d’origine comme un événement ou une propriété.

Par exemple, le `UIWebView` utilise le bit suivant de `UIWebViewDelegate` :

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

L’exemple ci-dessus expose `LoadingFinished` en tant que méthode dans le `UIWebViewDelegate` , mais `LoadFinished` en tant qu’événement pour se raccorder à dans un `UIWebView` :

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute"></a>

### <a name="modelattribute"></a>ModelAttribute

Lorsque vous appliquez l' `[Model]` attribut à une définition de type dans votre API de contrat, le runtime génère du code spécial qui affiche uniquement les appels aux méthodes de la classe si l’utilisateur a remplacé une méthode dans la classe. Cet attribut est généralement appliqué à toutes les API qui encapsulent une classe déléguée objective-C.

<a name="NoDefaultValueAttribute"></a>

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Spécifie que la méthode sur le modèle ne fournit pas de valeur de retour par défaut.

Cela fonctionne avec le runtime objective-C en répondant `false` à la demande d’exécution objective-c pour déterminer si le sélecteur spécifié est implémenté dans cette classe.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Voir aussi : [`[DefaultValue]`](#DefaultValueAttribute) , [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute"></a>

## <a name="protocols"></a>Protocoles

Le concept de protocole objective-C n’existe pas vraiment en C#. Les protocoles sont similaires aux interfaces C#, mais ils diffèrent dans le cas où toutes les méthodes et propriétés déclarées dans un protocole doivent être implémentées par la classe qui l’adopte. Certaines méthodes et propriétés sont facultatives.

Certains protocoles sont généralement utilisés comme classes de modèle, ils doivent être liés à l’aide de l' [`[Model]`](#ModelAttribute) attribut.

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}
```

À compter de Xamarin. iOS 7,0, une nouvelle fonctionnalité de liaison de protocole améliorée a été incorporée.  Toute définition contenant l' `[Protocol]` attribut génère en fait trois classes de prise en charge qui améliorent considérablement la façon dont vous consommez les protocoles :

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

L' **implémentation de classe** fournit une classe abstraite complète qui vous permet de substituer des méthodes individuelles de et d’obtenir une sécurité de type complète. Mais en raison du fait que C# ne prend pas en charge l’héritage multiple, il existe des scénarios dans lesquels vous pouvez avoir besoin d’une classe de base différente, mais vous souhaitez toujours implémenter une interface.

C’est là qu’intervient la **définition d’interface** générée.  Il s’agit d’une interface qui contient toutes les méthodes requises du protocole.  Cela permet aux développeurs qui souhaitent implémenter votre protocole d’implémenter simplement l’interface.  Le runtime inscrit automatiquement le type comme adoptant le protocole.

Notez que l’interface répertorie uniquement les méthodes requises et expose les méthodes facultatives.   Cela signifie que les classes qui adoptent le protocole obtiennent une vérification de type complète pour les méthodes requises, mais doivent recourir à un typage faible (manuellement à l’aide des attributs d’exportation et de la signature) pour les méthodes de protocole facultatives.

Pour faciliter l’utilisation d’une API qui utilise des protocoles, l’outil de liaison produira également une classe de méthode extensions qui expose toutes les méthodes facultatives.   Cela signifie que tant que vous consommez une API, vous pouvez traiter les protocoles comme ayant toutes les méthodes.

Si vous souhaitez utiliser les définitions de protocole dans votre API, vous devrez écrire le squelette des interfaces vides dans la définition de votre API.   Si vous souhaitez utiliser MyProtocol dans une API, vous devez effectuer cette opération :

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

L’expression ci-dessus est nécessaire car, au moment de la liaison, le n' `IMyProtocol` existe pas, c’est pourquoi vous devez fournir une interface vide.

### <a name="adopting-protocol-generated-interfaces"></a>Adoption d’interfaces générées par le protocole

Chaque fois que vous implémentez l’une des interfaces générées pour les protocoles, comme suit :

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L’implémentation pour les méthodes d’interface requises est exportée avec le nom approprié. elle est donc équivalente à ce qui suit :

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Cela fonctionne pour tous les membres de protocole requis, mais il existe un cas particulier avec les sélecteurs facultatifs à connaître.

Les membres de protocole facultatifs sont traités de la même façon lors de l’utilisation de la classe de base :

```
public class UrlSessionDelegate : NSUrlSessionDownloadDelegate {
    public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
```

Toutefois, lors de l’utilisation de l’interface de protocole, il est nécessaire d’ajouter [Export]. L’IDE l’ajoute via la saisie semi-automatique lorsque vous l’ajoutez à partir de l’opération de remplacement. 

```
public class UrlSessionDelegate : NSObject, INSUrlSessionDownloadDelegate {
    [Export ("URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite:")]
    public void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
```

Il existe une légère différence de comportement entre les deux lors de l’exécution :

- Les utilisateurs de la classe de base (NSUrlSessionDownloadDelegate dans l’exemple) fournissent tous les sélecteurs requis et facultatifs, et retournent des valeurs par défaut raisonnables.
- Les utilisateurs de l’interface (INSUrlSessionDownloadDelegate, par exemple) répondent uniquement aux sélecteurs exacts fournis.

Certaines classes rares peuvent se comporter différemment. Dans la plupart des cas, cependant, il est possible d’utiliser l’un ou l’autre.

### <a name="protocol-inlining"></a>Incorporation de protocole

Lorsque vous liez des types objective-C existants qui ont été déclarés comme adoptant un protocole, vous souhaiterez incorporer le protocole directement. Pour ce faire, il vous suffit de déclarer votre protocole en tant qu’interface sans [`[BaseType]`](#BaseTypeAttribute) attribut et de répertorier le protocole dans la liste des interfaces de base pour votre interface.

Exemple :

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```

## <a name="member-definitions"></a>Définitions de membres

Les attributs de cette section sont appliqués aux membres individuels d’un type : les propriétés et les déclarations de méthode.

### <a name="alignattribute"></a>AlignAttribute

Utilisé pour spécifier la valeur d’alignement pour les types de retour de propriété. Certaines propriétés prennent des pointeurs vers des adresses qui doivent être alignées à certaines limites (dans Xamarin. iOS, par exemple, avec certaines `GLKBaseEffect` propriétés qui doivent être alignées sur 16 octets). Vous pouvez utiliser cette propriété pour décorer l’accesseur get et utiliser la valeur d’alignement. Elle est généralement utilisée avec les `OpenTK.Vector4` types et lorsqu’elle est `OpenTK.Matrix4` intégrée à des API objective-C.

Exemple :

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>AppearanceAttribute

L' `[Appearance]` attribut est limité à iOS 5, où le gestionnaire d’apparences a été introduit.

L' `[Appearance]` attribut peut être appliqué à toute méthode ou propriété qui participe au `UIAppearance` Framework. Quand cet attribut est appliqué à une méthode ou à une propriété dans une classe, il indique au générateur de liaison de créer une classe d’apparence fortement typée utilisée pour appliquer un style à toutes les instances de cette classe ou aux instances qui correspondent à certains critères.

Exemple :

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

L’exemple ci-dessus génère le code suivant dans UIToolbar :

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin. iOS 5,4)

Utilisez `[AutoReleaseAttribute]` sur les méthodes et les propriétés pour encapsuler l’appel de méthode dans la méthode d’un `NSAutoReleasePool` .

En Objective-C, il existe des méthodes qui retournent des valeurs qui sont ajoutées à la valeur par défaut `NSAutoReleasePool` . Par défaut, ils accèdent à votre thread `NSAutoReleasePool` , mais étant donné que Xamarin. iOS conserve également une référence à vos objets tant que l’objet managé vit, vous ne souhaitez peut-être pas conserver une référence supplémentaire dans, `NSAutoReleasePool` qui n’est vidée que lorsque votre thread renvoie le contrôle au thread suivant ou que vous revenez à la boucle principale.

Cet attribut est appliqué par exemple sur des propriétés lourdes (par exemple `UIImage.FromFile` ) qui retourne des objets qui ont été ajoutés à la valeur par défaut `NSAutoReleasePool` . Sans cet attribut, les images sont conservées tant que votre thread n’a pas retourné le contrôle à la boucle principale. UF votre thread était une sorte de téléchargeur d’arrière-plan toujours actif et en attente de travail, les images ne seront jamais libérées.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`Est utilisé pour appliquer la création d’un type managé même si l’objet non managé retourné ne correspond pas au type décrit dans la définition de liaison.

Cela est utile lorsque le type décrit dans un en-tête ne correspond pas au type retourné de la méthode native, par exemple, prenons la définition objective-C suivante à partir de `NSURLSession` :

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Il indique clairement qu’il renverra une `NSURLSessionDownloadTask` instance, mais il **retourne** encore un `NSURLSessionTask` , qui est une superclasse et donc non convertible en `NSURLSessionDownloadTask` . Étant donné que nous sommes dans un contexte de type sécurisé, un se `InvalidCastException` produira.

Pour se conformer à la description de l’en-tête et éviter le `InvalidCastException` , `[ForcedTypeAttribute]` est utilisé.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]`Accepte également une valeur booléenne nommée `Owns` qui est `false` par défaut `[ForcedType (owns: true)]` . Le paramètre détenu est utilisé pour suivre la [stratégie de propriété](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) pour les objets **Core Foundation** .

`[ForcedTypeAttribute]`Est uniquement valide sur les paramètres, les propriétés et la valeur de retour.

<a name="BindAsAttribute"></a>

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]`Autorise la liaison `NSNumber` `NSValue` et `NSString` (enums) en types C# plus précis. L’attribut peut être utilisé pour créer une API .NET plus précise et plus précise sur l’API native.

Vous pouvez décorer des méthodes (sur une valeur de retour), des paramètres et des propriétés avec `BindAs` . La seule restriction est que votre membre **ne doit pas** être à l’intérieur d’une `[Protocol]` [`[Model]`](#ModelAttribute) interface ou.

Par exemple :

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Sortie :

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

En interne, nous allons effectuer les `bool?`  <->  `NSNumber` `CGRect`  <->  `NSValue` conversions et.

Les types d’encapsulation actuellement pris en charge sont les suivants :

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

Les types de données C# suivants sont pris en charge pour être encapsulés à partir de/dans `NSValue` :

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint/PointF
* CGRect/RectangleF
* CGSize/SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

Les types de données C# suivants sont pris en charge pour être encapsulés à partir de/dans `NSNumber` :

* bool
* byte
* double
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* Énumérations

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute) fonctionne dans conjuntion avec des [enums associés à une constante chaîne NSString](#enum-attributes) , ce qui vous permet de créer une meilleure API .net, par exemple :

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Sortie :

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Nous allons gérer la `enum`  <->  `NSString` conversion uniquement si le type enum fourni [`[BindAs]`](#BindAsAttribute) est [stocké par une constante chaîne NSString](#enum-attributes).

#### <a name="arrays"></a>Tableaux

[`[BindAs]`](#BindAsAttribute) prend également en charge les tableaux de tous les types pris en charge, vous pouvez avoir la définition d’API suivante comme exemple :

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Sortie :

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

Le `rects` paramètre sera encapsulé dans un `NSArray` qui contient un `NSValue` pour chaque `CGRect` et dans le retour, vous obtiendrez un tableau de `CAScroll?` qui a été créé à l’aide des valeurs du retourné `NSArray` contenant `NSStrings` .

<a name="BindAttribute"></a>

### <a name="bindattribute"></a>BindAttribute

L' `[Bind]` attribut a deux utilisations de l’un lorsqu’il est appliqué à une déclaration de méthode ou de propriété, et un autre en cas d’application à la méthode Getter ou Setter individuelle dans une propriété.

Lorsqu’il est utilisé pour une méthode ou une propriété, l’effet de l' `[Bind]` attribut consiste à générer une méthode qui appelle le sélecteur spécifié. Mais la méthode générée résultante n’est pas décorée avec l' [`[Export]`](#ExportAttribute) attribut, ce qui signifie qu’elle ne peut pas participer au remplacement de la méthode. En général, il est utilisé en association avec l' `[Target]` attribut pour implémenter des méthodes d’extension objective-C.

Par exemple :

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Lorsqu’il est utilisé dans un accesseur Get ou Setter, l' `[Bind]` attribut est utilisé pour modifier les valeurs par défaut déduites par le générateur de code lors de la génération des noms de sélecteur objective-C de l’accesseur get et setter pour une propriété. Par défaut, quand vous marquez une propriété avec le nom `fooBar` , le générateur génère une `fooBar` exportation pour l’accesseur get et `setFooBar:` pour la méthode setter. Dans certains cas, Objective-C ne respecte pas cette Convention. en général, il change le nom de l’accesseur get `isFooBar` .
Vous pouvez utiliser cet attribut pour informer le générateur de ce.

Par exemple :

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute"></a>

### <a name="asyncattribute"></a>AsyncAttribute

Disponible uniquement sur Xamarin. iOS 6,3 et versions ultérieures.

Cet attribut peut être appliqué aux méthodes qui prennent un gestionnaire d’achèvement comme dernier argument.

Vous pouvez utiliser l' `[Async]` attribut sur les méthodes dont le dernier argument est un rappel.  Lorsque vous appliquez ce à une méthode, le générateur de liaisons génère une version de cette méthode avec le suffixe `Async` .  Si le rappel n’accepte aucun paramètre, la valeur de retour est `Task` , si le rappel prend un paramètre, le résultat est `Task<T>` .

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

L’exemple suivant génère cette méthode Async :

```csharp
Task LoadFileAsync (string file);
```

Si le rappel prend plusieurs paramètres, vous devez définir `ResultType` ou `ResultTypeName` pour spécifier le nom souhaité du type généré qui contiendra toutes les propriétés.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

La commande suivante génère cette méthode Async, où `FileLoading` contient des propriétés permettant d’accéder à la fois à `files` et à `byteCount` :

```csharp
Task<FileLoading> LoadFile (string file);
```

Si le dernier paramètre du rappel est un `NSError` , la `Async` méthode générée vérifie si la valeur n’est pas null et, si tel est le cas, la méthode Async générée définit l’exception de la tâche.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

L’exemple ci-dessus génère la méthode Async suivante :

```csharp
Task<string> UploadAsync (string file);
```

Et en cas d’erreur, la tâche résultante aura l’exception définie sur un `NSErrorException` qui encapsule le résultant `NSError` .

#### <a name="asyncattributeresulttype"></a>AsyncAttribute. ResultType

Utilisez cette propriété pour spécifier la valeur de l’objet qui retourne `Task` .   Ce paramètre prend un type existant, il doit donc être défini dans l’une de vos définitions d’API principales.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Utilisez cette propriété pour spécifier la valeur de l’objet qui retourne `Task` .   Ce paramètre prend le nom du type souhaité, le générateur produira une série de propriétés, une pour chaque paramètre que le rappel prend.

#### <a name="asyncattributemethodname"></a>AsyncAttribute. MethodName

Utilisez cette propriété pour personnaliser le nom des méthodes Async générées.   La valeur par défaut consiste à utiliser le nom de la méthode et à ajouter le texte « Async ». vous pouvez l’utiliser pour modifier ce paramètre par défaut.

<a name="DesignatedInitializerAttribute"></a>

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

Quand cet attribut est appliqué à un constructeur, il génère le même `[DesignatedInitializer]` dans l’assembly de plateforme final. Cela permet à l’IDE d’indiquer quel constructeur doit être utilisé dans les sous-classes.

Cela doit correspondre à l’utilisation objective-C/Clang de `__attribute__((objc_designated_initializer))` .

<a name="DisableZeroCopyAttribute"></a>

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Cet attribut est appliqué aux paramètres de chaîne ou aux propriétés de chaîne et indique au générateur de code de ne pas utiliser le marshaling de chaînes de copie zéro pour ce paramètre et de créer à la place une nouvelle instance chaîne NSString à partir de la chaîne C#.
Cet attribut est requis uniquement sur les chaînes si vous indiquez au générateur d’utiliser le marshaling de chaînes de copie zéro à l’aide de l' `--zero-copy` option de ligne de commande ou de la définition de l’attribut de niveau assembly `ZeroCopyStringsAttribute` .

Cela est nécessaire dans les cas où la propriété est déclarée en Objective-C pour être une `retain` `assign` propriété ou au lieu d’une `copy` propriété. Cela se produit généralement dans les bibliothèques tierces qui ont été incorrectement « optimisées » par les développeurs. En général, `retain` les `assign` `NSString` Propriétés ou sont incorrectes, car `NSMutableString` ou les classes dérivées de l’utilisateur de `NSString` peuvent modifier le contenu des chaînes sans avoir connaissance du code de bibliothèque, ce qui rompt légèrement l’application. En général, cela se produit en raison d’une optimisation prématurée.

L’exemple suivant montre deux propriétés de ce type en Objective-C :

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute"></a>

### <a name="disposeattribute"></a>DisposeAttribute

Quand vous appliquez `[DisposeAttribute]` à une classe, vous fournissez un extrait de code qui sera ajouté à l' `Dispose()` implémentation de la méthode de la classe.

Étant donné que la `Dispose` méthode est générée automatiquement par les `bmac-native` `btouch-native` Outils et, vous devez utiliser l' `[Dispose]` attribut pour injecter du code dans l’implémentation de la `Dispose` méthode générée.

Par exemple :

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute"></a>

### <a name="exportattribute"></a>ExportAttribute

L' `[Export]` attribut est utilisé pour signaler une méthode ou une propriété à exposer au runtime objective-C. Cet attribut est partagé entre l’outil de liaison et les runtimes Xamarin. iOS et Xamarin. Mac réels. Pour les méthodes, le paramètre est passé textuellement au code généré. pour les propriétés, les exportations getter et setter sont générées en fonction de la déclaration de base ( [`[BindAttribute]`](#BindAttribute) pour plus d’informations sur la façon de modifier le comportement de l’outil de liaison, consultez la section).

Syntaxe :

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

Le [Sélecteur](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) représente le nom de la méthode ou de la propriété objective-C sous-jacente qui est liée.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute. ArgumentSemantic

<a name="FieldAttribute"></a>

### <a name="fieldattribute"></a>FieldAttribute

Cet attribut est utilisé pour exposer une variable globale C en tant que champ chargé à la demande et exposé au code C#. Cela est généralement nécessaire pour récupérer les valeurs des constantes définies en C ou objective-C et qui peuvent être des jetons utilisés dans certaines API ou dont les valeurs sont opaques et doivent être utilisées telles quelles par le code utilisateur.

Syntaxe :

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`Est le symbole C avec lequel établir la liaison. Par défaut, ce sera chargé à partir d’une bibliothèque dont le nom est déduit de l’espace de noms où le type est défini. S’il ne s’agit pas de la bibliothèque dans laquelle le symbole est recherché, vous devez passer le `libraryName` paramètre. Si vous liez une bibliothèque statique, utilisez `__Internal` comme `libraryName` paramètre.

Les propriétés générées sont toujours statiques.

Les propriétés marquées avec l’attribut Field peuvent être de l’un des types suivants :

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Énumérations

Les méthodes setter ne sont pas prises en charge pour les [énumérations soutenues par des constantes chaîne NSString](#enum-attributes), mais elles peuvent être liées manuellement si nécessaire.

Exemple :

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute"></a>

### <a name="internalattribute"></a>InternalAttribute

L' `[Internal]` attribut peut être appliqué à des méthodes ou des propriétés, et il a pour effet de marquer le code généré avec le `internal` mot clé C#, ce qui rend le code accessible uniquement au code de l’assembly généré. Cela est généralement utilisé pour masquer les API qui sont trop faibles ou fournir une API publique non optimale que vous souhaitez améliorer sur ou pour les API qui ne sont pas prises en charge par le générateur et qui nécessitent un codage manuel.

Quand vous concevez la liaison, vous masquez généralement la méthode ou la propriété à l’aide de cet attribut et fournissez un nom différent pour la méthode ou la propriété, puis, dans votre fichier de support complémentaire C#, vous ajoutez un wrapper fortement typé qui expose la fonctionnalité sous-jacente.

Par exemple :

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Ensuite, dans votre fichier de prise en charge, vous pourriez avoir du code similaire à celui-ci :

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute"></a>

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Cet attribut marque le champ de stockage pour une propriété à annoter avec l' `[ThreadStatic]` attribut .net. Cela est utile si le champ est une variable statique de thread.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin. iOS 6.0.6)

Cet attribut rend une méthode prenant en charge les exceptions natives (objective-C).
Au lieu d’appeler `objc_msgSend` directement, l’appel passera par un trampoline personnalisé qui intercepte les exceptions ObjectiveC et les marshale dans des exceptions managées.

Actuellement, seules quelques `objc_msgSend` signatures sont prises en charge (vous découvrirez si une signature n’est pas prise en charge lorsque la liaison native d’une application qui utilise la liaison échoue avec un symbole monotouch_ *_objc_msgSend* manquant), mais d’autres peuvent être ajoutées à la demande.

### <a name="newattribute"></a>NewAttribute

Cet attribut est appliqué aux méthodes et propriétés pour que le générateur génère le `new` mot clé devant la déclaration.

Il est utilisé pour éviter les avertissements du compilateur lorsque la même méthode ou le même nom de propriété est introduit dans une sous-classe qui existait déjà dans une classe de base.

<a name="NotificationAttribute"></a>

### <a name="notificationattribute"></a>NotificationAttribute

Vous pouvez appliquer cet attribut à des champs pour que le générateur génère une classe de notifications d’assistance fortement typée.

Cet attribut peut être utilisé sans arguments pour les notifications qui ne contiennent pas de charge utile, ou vous pouvez spécifier un `System.Type` qui fait référence à une autre interface dans la définition de l’API, généralement avec le nom se terminant par « EventArgs ». Le générateur transformera l’interface en une classe qui sous-classe `EventArgs` et inclura toutes les propriétés qui y sont listées. L' [`[Export]`](#ExportAttribute) attribut doit être utilisé dans la `EventArgs` classe pour répertorier le nom de la clé utilisée pour rechercher le dictionnaire objective-C afin d’extraire la valeur.

Par exemple :

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Le code ci-dessus génère une classe imbriquée `MyClass.Notifications` avec les méthodes suivantes :

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Les utilisateurs de votre code peuvent alors facilement s’abonner aux notifications publiées dans le [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) à l’aide d’un code tel que celui-ci :

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Ou pour définir un objet spécifique à observer. Si vous transmettez `null` à `objectToObserve` cette méthode, se comportera comme son autre homologue.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

La valeur retournée par `ObserveDidStart` peut être utilisée pour arrêter facilement la réception de notifications, comme suit :

```csharp
token.Dispose ();
```

Vous pouvez appeler [NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) et passer le jeton. Si votre notification contient des paramètres, vous devez spécifier une interface d’assistance `EventArgs` , comme suit :

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

La version ci-dessus génère une `MyScreenChangedEventArgs` classe avec les `ScreenX` `ScreenY` Propriétés et qui récupèrent les données du dictionnaire [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) à l’aide des clés `ScreenXKey` et `ScreenYKey` appliquent les conversions appropriées. L' `[ProbePresence]` attribut est utilisé pour que le générateur sonde si la clé est définie dans le `UserInfo` , au lieu d’essayer d’extraire la valeur. Cela est utilisé dans les cas où la présence de la clé est la valeur (généralement pour les valeurs booléennes).

Cela vous permet d’écrire du code tel que celui-ci :

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

Dans certains cas, aucune constante n’est associée à la valeur transmise sur le dictionnaire.  Apple utilise parfois des constantes de symboles publiques et utilise parfois des constantes de chaîne.  Par défaut, l' [`[Export]`](#ExportAttribute) attribut dans votre `EventArgs` classe fournie utilise le nom spécifié comme symbole public à rechercher au moment de l’exécution.  Si ce n’est pas le cas, et qu’il est supposé être recherché comme une constante de chaîne, transmettez la `ArgumentSemantic.Assign` valeur à l’attribut d’exportation.

**Nouveautés de Xamarin. iOS 8,4**

Parfois, les notifications commencent la vie sans aucun argument, de sorte que l’utilisation de [`[Notification]`](#NotificationAttribute) sans arguments est acceptable.  Mais parfois, les paramètres de la notification sont introduits.  Pour prendre en charge ce scénario, l’attribut peut être appliqué plusieurs fois.

Si vous développez une liaison et que vous souhaitez éviter de rompre le code utilisateur existant, vous devez transformer une notification existante de :

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

Dans une version qui répertorie deux fois l’attribut de notification, comme suit :

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute"></a>

### <a name="nullallowedattribute"></a>NullAllowedAttribute

Quand cette propriété est appliquée à une propriété, elle signale la propriété comme autorisant la valeur `null` à lui être assignée. Cela n’est valide que pour les types référence.

Lorsqu’il est appliqué à un paramètre dans une signature de méthode, il indique que le paramètre spécifié peut être null et qu’aucun contrôle ne doit être effectué pour passer des `null` valeurs.

Si le type référence n’a pas cet attribut, l’outil de liaison génère une vérification de la valeur assignée avant de la passer à Objective-C et génère une vérification qui lève une exception `ArgumentNullException` si la valeur assignée est `null` .

Par exemple :

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute"></a>

### <a name="overrideattribute"></a>OverrideAttribute

Utilisez cet attribut pour indiquer au générateur de liaison que la liaison pour cette méthode particulière doit être marquée avec un `override` mot clé.

### <a name="presnippetattribute"></a>PreSnippetAttribute

Vous pouvez utiliser cet attribut pour injecter du code à insérer après la validation des paramètres d’entrée, mais avant que le code n’appelle objective-C.

Exemple :

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Vous pouvez utiliser cet attribut pour injecter du code à insérer avant la validation de l’un des paramètres dans la méthode générée.

Exemple :

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Demande au générateur de liaison d’appeler la propriété spécifiée à partir de cette classe pour en extraire une valeur.

Cette propriété est généralement utilisée pour actualiser le cache qui pointe vers les objets de référence qui maintiennent le graphique d’objet référencé. En général, il s’affiche dans le code qui a des opérations telles que Add/Remove. Cette méthode est utilisée afin qu’après l’ajout ou la suppression d’éléments, le cache interne soit mis à jour pour garantir que nous conservons les références managées aux objets en cours d’utilisation. Cela est possible car l’outil de liaison génère un champ de stockage pour tous les objets de référence dans une liaison donnée.

Exemple :

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

Dans ce cas, la `Dependencies` propriété est appelée après l’ajout ou la suppression des dépendances de l' `NSOperation` objet, en veillant à ce que nous ayons un graphique qui représente les objets chargés réels, en empêchant les fuites de mémoire ainsi que l’altération de la mémoire.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

Vous pouvez utiliser cet attribut pour injecter du code source C# à insérer après que le code a appelé la méthode objective-C sous-jacente.

Exemple :

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Cet attribut est appliqué aux valeurs de retour pour les marquer comme des objets proxy. Certaines API objective-C retournent des objets proxy qui ne peuvent pas être différenciés des liaisons utilisateur. L’effet de cet attribut est de marquer l’objet comme étant un `DirectBinding` objet. Pour un scénario dans Xamarin. Mac, vous pouvez voir la [discussion sur ce bogue](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Indique au générateur de conserver une référence managée au paramètre ou de supprimer une référence interne au paramètre. Cela permet de conserver les objets référencés.

Syntaxe :

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si la valeur de `doAdd` est true, le paramètre est ajouté à `__mt_{0}_var List<NSObject>;` . Où `{0}` est remplacé par le donné `listName` . Vous devez déclarer ce champ de stockage dans votre classe partielle complémentaire pour l’API.

Pour obtenir un exemple, consultez [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) et [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin. iOS 6,0)

Cela peut être appliqué aux types de retour pour indiquer que le générateur doit appeler `Release` sur l’objet avant de le retourner. Cela n’est nécessaire que si une méthode vous donne un objet conservé (par opposition à un objet republié, ce qui est le scénario le plus courant)

Exemple :

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

En outre, cet attribut est propagé au code généré, afin que le runtime Xamarin. iOS sache qu’il doit conserver l’objet lors du retour à Objective-C à partir de cette fonction.

### <a name="sealedattribute"></a>SealedAttribute

Fait en sorte que le générateur signale la méthode générée comme sealed. Si cet attribut n’est pas spécifié, la valeur par défaut consiste à générer une méthode virtuelle (une méthode virtuelle, une méthode abstraite ou une substitution en fonction de l’utilisation d’autres attributs).

<a name="StaticAttribute"></a>

### <a name="staticattribute"></a>StaticAttribute

Lorsque l' `[Static]` attribut est appliqué à une méthode ou une propriété, cela génère une méthode ou une propriété statique. Si cet attribut n’est pas spécifié, le générateur produit une méthode ou une propriété d’instance.

### <a name="transientattribute"></a>TransientAttribute

Utilisez cet attribut pour marquer les propriétés dont les valeurs sont temporaires, c’est-à-dire les objets qui sont créés temporairement par iOS, mais qui ne sont pas durables. Quand cet attribut est appliqué à une propriété, le générateur ne crée pas de champ de stockage pour cette propriété, ce qui signifie que la classe managée ne conserve pas de référence à l’objet.

<a name="WrapAttribute"></a>

### <a name="wrapattribute"></a>WrapAttribute

Dans la conception des liaisons Xamarin. iOS/Xamarin. Mac, l' `[Wrap]` attribut est utilisé pour encapsuler un objet faiblement typé avec un objet fortement typé. Cela se produit principalement avec les objets délégués objective-C qui sont généralement déclarés comme étant de type `id` ou `NSObject` . La Convention utilisée par Xamarin. iOS et Xamarin. Mac consiste à exposer ces délégués ou sources de données comme étant de type `NSObject` et nommés à l’aide de la Convention « faible » + le nom exposé. Une `id delegate` propriété de Objective-C sera exposée en tant que `NSObject WeakDelegate { get; set; }` propriété dans le fichier de contrat d’API.

Mais en général, la valeur assignée à ce délégué est d’un type fort. nous avons donc vu le type fort et appliqué l' `[Wrap]` attribut, ce qui signifie que les utilisateurs peuvent choisir d’utiliser des types faibles s’ils ont besoin d’un contrôle précis ou s’ils doivent recourir à des astuces de bas niveau, ou utiliser la propriété fortement typée pour la plupart de leur travail.

Exemple :

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

C’est ainsi que l’utilisateur utilise la version faiblement typée du délégué :

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

Et c’est ainsi que l’utilisateur utilise la version fortement typée, Notez que l’utilisateur tire parti du système de type de C# et utilise le mot clé override pour déclarer son intention et qu’il n’a pas à décorer manuellement la méthode avec `[Export]` , puisque nous avons effectué cette opération dans la liaison pour l’utilisateur :

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Une autre utilisation de l' `[Wrap]` attribut consiste à prendre en charge la version fortement typée des méthodes.  Par exemple :

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Quand l' `[Wrap]` attribut est appliqué à une méthode à l’intérieur d’un type décoré avec un `[Category]` attribut, vous devez inclure `This` comme premier argument, car une méthode d’extension est générée. Par exemple :

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

Les membres générés par `[Wrap]` ne sont pas par `virtual` défaut, si vous avez besoin d’un `virtual` membre, vous pouvez définir `true` le `isVirtual` paramètre facultatif.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]` peut également être utilisé directement dans les accesseurs get et les méthodes setter de propriété.
Cela permet d’avoir un contrôle total sur ces éléments et d’ajuster le code en fonction des besoins.
Par exemple, considérez la définition d’API suivante qui utilise des énumérations intelligentes :

```csharp
// Smart enum.
enum PersonRelationship {
        [Field (null)]
        None,

        [Field ("FMFather", "__Internal")]
        Father,

        [Field ("FMMother", "__Internal")]
        Mother
}
```

Définition de l’interface :

```csharp
// Property definition.

[Export ("presenceType")]
NSString _PresenceType { get; set; }

PersonRelationship PresenceType {
    [Wrap ("PersonRelationshipExtensions.GetValue (_PresenceType)")]
    get;
    [Wrap ("_PresenceType = value.GetConstant ()")]
    set;
}
```

## <a name="parameter-attributes"></a>Attributs de paramètres

Cette section décrit les attributs que vous pouvez appliquer aux paramètres dans une définition de méthode, ainsi que le `[NullAttribute]` qui s’applique à une propriété dans son ensemble.

<a name="BlockCallback"></a>

### <a name="blockcallback"></a>BlockCallback

Cet attribut est appliqué aux types de paramètres dans les déclarations de délégué C# pour notifier au Binder que le paramètre en question est conforme à la Convention d’appel de bloc objective-C et le marshale de cette façon.

Cela est généralement utilisé pour les rappels qui sont définis comme ceci dans objective-C :

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Voir aussi : [CCallback](#CCallback).

<a name="CCallback"></a>

### <a name="ccallback"></a>CCallback

Cet attribut est appliqué aux types de paramètres dans les déclarations de délégué C# pour notifier au Binder que le paramètre en question est conforme à la Convention d’appel du pointeur de fonction ABI C et doit le marshaler de cette façon.

Cela est généralement utilisé pour les rappels qui sont définis comme ceci dans objective-C :

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Voir aussi : [BlockCallback](#BlockCallback).

### <a name="params"></a>Paramètres

Vous pouvez utiliser l' `[Params]` attribut sur le dernier paramètre de tableau d’une définition de méthode pour que le générateur injecte un « params » dans la définition.   Cela permet à la liaison d’autoriser facilement les paramètres facultatifs.

Par exemple, la définition suivante :

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Autorise l’écriture du code suivant :

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Cela présente l’avantage de ne pas obliger les utilisateurs à créer un tableau exclusivement pour passer des éléments.

<a name="plainstring"></a>

### <a name="plainstring"></a>PlainString

Vous pouvez utiliser l' `[PlainString]` attribut devant des paramètres de chaîne pour indiquer au générateur de liaison de transmettre la chaîne en tant que chaîne C, au lieu de passer le paramètre en tant que `NSString` .

La plupart des API objective-C consomment des `NSString` paramètres, mais quelques API exposent une `char *` API pour passer des chaînes au lieu de la `NSString` variation.
Utilisez `[PlainString]` dans ces cas.

Par exemple, les déclarations objective-C suivantes :

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Doit être lié comme suit :

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

Indique au générateur de conserver une référence au paramètre spécifié. Le générateur fournira le magasin de stockage pour ce champ, ou vous pouvez spécifier un nom (le `WrapName` ) dans lequel stocker la valeur. Cela permet de conserver une référence à un objet managé passé comme paramètre à Objective-C et lorsque vous savez que objective-C conserve cette copie de l’objet. Par exemple, une API telle que `SetDisplay (SomeObject)` utilise cet attribut, car il est probable que le SetDisplay ne puisse afficher qu’un seul objet à la fois. Si vous devez effectuer le suivi de plusieurs objets (par exemple, pour une API de type pile), vous utiliseriez l' `[RetainList]` attribut.

Syntaxe :

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

### <a name="retainlistattribute"></a>RetainListAttribute

Indique au générateur de conserver une référence managée au paramètre ou de supprimer une référence interne au paramètre. Cela permet de conserver les objets référencés.

Syntaxe :

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si la valeur de `doAdd` est true, le paramètre est ajouté à `__mt_{0}_var List<NSObject>` . Où `{0}` est remplacé par le donné `listName` . Vous devez déclarer ce champ de stockage dans votre classe partielle complémentaire pour l’API.

Pour obtenir un exemple, consultez [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) et [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>TransientAttribute

Cet attribut est appliqué aux paramètres et n’est utilisé que lors de la transition entre objective-C et C#.  Pendant ces transitions, les différents paramètres objective-C `NSObject` sont encapsulés dans une représentation managée de l’objet.

Le runtime prend une référence à l’objet natif et conserve la référence jusqu’à ce que la dernière référence managée à l’objet ait disparu, et le GC a la possibilité de s’exécuter.

Dans certains cas, il est important que le runtime C# ne conserve pas de référence à l’objet natif.  Cela se produit parfois lorsque le code natif sous-jacent a attaché un comportement spécial au cycle de vie du paramètre.  Par exemple : le destructeur du paramètre effectue une action de nettoyage ou dispose d’une ressource précieuse.

Cet attribut indique au runtime que vous souhaitez que l’objet soit supprimé si possible lors du retour à Objective-C à partir de votre méthode remplacée.

La règle est simple : si le runtime devait créer une nouvelle représentation managée à partir de l’objet natif, à la fin de la fonction, le nombre de conservations pour l’objet natif sera supprimé et la propriété descripteur de l’objet managé sera effacée.   Cela signifie que si vous avez conservé une référence à l’objet managé, cette référence devient inutile (l’appel de méthodes sur celle-ci lèvera une exception).

Si l’objet passé n’a pas été créé, ou s’il existe déjà une représentation managée en suspens de l’objet, la suppression forcée n’a pas lieu. 

## <a name="property-attributes"></a>Attributs de la propriété

<a name="NotImplementedAttribute"></a>

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Cet attribut est utilisé pour prendre en charge un idiome objective-C dans lequel une propriété avec un accesseur Get est introduite dans une classe de base, et une sous-classe mutable introduit une méthode setter.

Dans la mesure où C# ne prend pas en charge ce modèle, la classe de base doit posséder à la fois l’accesseur Set et l’accesseur Get, et une sous-classe peut utiliser [OverrideAttribute](#OverrideAttribute).

Cet attribut est utilisé uniquement dans les accesseurs set de propriété et est utilisé pour prendre en charge l’idiome mutable en Objective-C.

Exemple :

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes"></a>

## <a name="enum-attributes"></a>Attributs enum

`NSString`Le mappage de constantes à des valeurs enum est un moyen simple de créer une meilleure API .net. Elle effectue les actions suivantes :

* permet à la saisie semi-automatique du code d’être plus utile, en n’apparaissant **que** les valeurs correctes pour l’API.
* Ajoute la sécurité de type, vous ne pouvez pas utiliser une autre `NSString` constante dans un contexte incorrect ; et
* permet de masquer certaines constantes, en faisant en sorte que la saisie semi-automatique du code affiche la liste des API plus courtes sans perdre de fonctionnalités.

Exemple :

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}
```

À partir de la définition de liaison ci-dessus, le générateur crée le `enum` lui-même et crée également un `*Extensions` type statique qui comprend des méthodes de conversion bidirectionnelles entre les valeurs enum et les `NSString` constantes. Cela signifie que les constantes restent disponibles pour les développeurs même si elles ne font pas partie de l’API.

Exemples :

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

Vous pouvez décorer **une** valeur enum avec cet attribut. La constante est alors retournée si la valeur enum n’est pas connue.

Dans l’exemple ci-dessus :

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Si aucune valeur enum n’est décorée, une est `NotSupportedException` levée.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Les codes d’erreur sont liés en tant que valeurs d’énumération. Il y a généralement un domaine d’erreur pour eux et il n’est pas toujours facile de trouver celui qui s’applique (ou s’il y en a un).

Vous pouvez utiliser cet attribut pour associer le domaine d’erreur à l’énumération elle-même.

Exemple :

```csharp
[Native]
[ErrorDomain ("AVKitErrorDomain")]
public enum AVKitError : nint {
    None = 0,
    Unknown = -1000,
    PictureInPictureStartFailed = -1001
}
```

Vous pouvez ensuite appeler la méthode `GetDomain` d’extension pour récupérer la constante de domaine de n’importe quelle erreur.

### <a name="fieldattribute"></a>FieldAttribute

Il s’agit du même attribut que celui `[Field]` utilisé pour les constantes dans le type. Il peut également être utilisé dans des enums pour mapper une valeur avec une constante spécifique.

Une `null` valeur peut être utilisée pour spécifier la valeur enum qui doit être retournée si une `null` `NSString` constante est spécifiée.

Dans l’exemple ci-dessus :

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Si aucune `null` valeur n’est présente, une est `ArgumentNullException` levée.

## <a name="global-attributes"></a>Attributs globaux

Les attributs globaux sont appliqués à l’aide du `[assembly:]` modificateur d’attribut tel que [`[LinkWithAttribute]`](#LinkWithAttribute) ou peuvent être utilisés n’importe où, comme les [`[Lion]`](#SinceAndLionAttributes) [`[Since]`](#SinceAndLionAttributes) attributs et.

<a name="LinkWithAttribute"></a>

### <a name="linkwithattribute"></a>LinkWithAttribute

Il s’agit d’un attribut de niveau assembly qui permet aux développeurs de spécifier les indicateurs de liaison requis pour réutiliser une bibliothèque liée sans forcer l’utilisateur de la bibliothèque à configurer manuellement le gcc_flags et les arguments mTouch supplémentaires passés à une bibliothèque.

Syntaxe :

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

Cet attribut est appliqué au niveau de l’assembly, par exemple, c’est ce que les [liaisons CorePlot](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) utilisent :

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Lorsque vous utilisez l' `[LinkWith]` attribut, le spécifié `libraryName` est incorporé à l’assembly résultant, ce qui permet aux utilisateurs de fournir une dll unique qui contient à la fois les dépendances non managées et les indicateurs de ligne de commande nécessaires pour utiliser correctement la bibliothèque à partir de Xamarin. iOS.

Il est également possible de ne pas fournir un `libraryName` , auquel cas l' `LinkWith` attribut peut être utilisé pour spécifier uniquement des indicateurs de l’éditeur de liens supplémentaires :

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Constructeurs LinkWithAttribute

Ces constructeurs vous permettent de spécifier la bibliothèque à lier à votre assembly résultant, les cibles prises en charge que la bibliothèque prend en charge et tous les indicateurs de bibliothèque facultatifs qui sont nécessaires pour établir une liaison avec la bibliothèque.

Notez que l' `LinkTarget` argument est déduit par Xamarin. iOS et qu’il n’a pas besoin d’être défini.

Exemples :

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

La `ForceLoad` propriété est utilisée pour décider si l' `-force_load` indicateur de lien est utilisé pour la liaison de la bibliothèque native. Pour le moment, cette valeur doit toujours être true.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute. frameworks

Si la bibliothèque en cours de liaison a une exigence matérielle sur des frameworks (autres que `Foundation` et `UIKit` ), vous devez affecter `Frameworks` à la propriété une chaîne contenant une liste délimitée par des espaces des infrastructures de plateforme requises. Par exemple, si vous liez une bibliothèque qui requiert `CoreGraphics` et `CoreText` , vous affectez à la `Frameworks` propriété la valeur `"CoreGraphics CoreText"` .

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Affectez à cette propriété la valeur true si l’exécutable résultant doit être compilé à l’aide d’un compilateur C++ au lieu de la valeur par défaut, qui est un compilateur C. Utilisez cette si la bibliothèque que vous liez a été écrite en C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute. NomBibliothèque

Nom de la bibliothèque non managée à regrouper. Il s’agit d’un fichier avec l’extension « . a » et il peut contenir du code objet pour plusieurs plateformes (par exemple, ARM et x86 pour le simulateur).

Les versions antérieures de Xamarin. iOS ont vérifié la `LinkTarget` propriété pour déterminer la plateforme prise en charge par votre bibliothèque, mais elle est maintenant détectée automatiquement et la `LinkTarget` propriété est ignorée.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

La `LinkerFlags` chaîne permet aux auteurs de lier de spécifier tout indicateur de l’éditeur de liens supplémentaire nécessaire pour lier la bibliothèque native à l’application.

Par exemple, si la bibliothèque Native requiert libxml2 et zlib, vous affectez la valeur `LinkerFlags` à la chaîne `"-lxml2 -lz"` .

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute. LinkTarget

Les versions antérieures de Xamarin. iOS ont vérifié la `LinkTarget` propriété pour déterminer la plateforme prise en charge par votre bibliothèque, mais elle est maintenant détectée automatiquement et la `LinkTarget` propriété est ignorée.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Affectez à cette propriété la valeur true si la bibliothèque que vous liez requiert la bibliothèque de gestion des exceptions GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute. a

La `SmartLink` propriété doit avoir la valeur true pour permettre à Xamarin. IOS de déterminer si `ForceLoad` est requis ou non.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

La `WeakFrameworks` propriété fonctionne de la même façon que la `Frameworks` propriété, sauf qu’au moment de l’édition de liens, le `-weak_framework` spécificateur est passé à GCC pour chaque Framework listé.

`WeakFrameworks` permet aux bibliothèques et aux applications d’établir une liaison faible avec les infrastructures de plateforme afin qu’elles puissent éventuellement les utiliser si elles sont disponibles, mais ne prennent pas de dépendance matérielle, ce qui est utile si votre bibliothèque est destinée à ajouter des fonctionnalités supplémentaires sur les versions plus récentes d’iOS. Pour plus d’informations sur la liaison faible, consultez la documentation d’Apple sur la [liaison faible](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Les bons candidats pour une liaison faible seraient `Frameworks` comme les comptes,,, `CoreBluetooth` et, `CoreImage` `GLKit` `NewsstandKit` `Twitter` car ils sont uniquement disponibles dans iOS 5.

<a name="SinceAndLionAttributes"></a>

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) et LionAttribute (macOS)

Vous utilisez l' `[Since]` attribut pour marquer les API comme ayant été introduites à un moment donné. L’attribut ne doit être utilisé que pour signaler des types et des méthodes susceptibles de provoquer un problème au moment de l’exécution si la classe, la méthode ou la propriété sous-jacente n’est pas disponible.

Syntaxe :

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

En général, il ne doit pas être appliqué aux énumérations, contraintes ou nouvelles structures, car celles-ci ne provoquent pas d’erreur d’exécution si elles sont exécutées sur un appareil avec une version antérieure du système d’exploitation.

Exemple appliqué à un type :

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Exemple appliqué à un nouveau membre :

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

L' `[Lion]` attribut est appliqué de la même façon, mais pour les types introduits avec Lion. La raison de l’utilisation de `[Lion]` par rapport au numéro de version plus spécifique utilisé dans IOS est que IOS est fréquemment modifié, tandis que les versions majeures de OS X se produisent rarement et qu’il est plus facile de mémoriser le système d’exploitation par leur nom de version.

### <a name="adviceattribute"></a>AdviceAttribute

Utilisez cet attribut pour fournir aux développeurs une indication sur les autres API qui peuvent être plus pratiques à utiliser.   Par exemple, si vous fournissez une version fortement typée d’une API, vous pouvez utiliser cet attribut sur l’attribut faiblement typé pour orienter le développeur vers la meilleure API.

Les informations de cet attribut sont présentées dans la documentation et les outils peuvent être développés pour fournir des suggestions à l’utilisateur sur la façon d’améliorer

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

Il s’agit d’une sous-classe spécialisée de l' `[Advice]` attribut qui peut être utilisée pour indiquer au développeur que la substitution d’une méthode **requiert** un appel à la méthode de base (substituée).

Cela correspond à `clang`[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Disponible uniquement dans Xamarin. iOS 5,4 et versions ultérieures.

Cet attribut indique au générateur que la liaison pour cette bibliothèque spécifique (si elle est appliquée avec `[assembly:]` ) ou le type doit utiliser le marshaling rapide de chaîne de copie zéro. Cet attribut équivaut à passer l’option `--zero-copy` de ligne de commande au générateur.

Lors de l’utilisation de zéro-Copy pour les chaînes, le générateur utilise efficacement la même chaîne C# que la chaîne que objective-C consomme sans impliquer la création d’un nouvel `NSString` objet et en évitant de copier les données des chaînes C# dans la chaîne objective-c. Le seul inconvénient de l’utilisation de chaînes de copie zéro est que vous devez vous assurer que toute propriété de type chaîne que vous encapsulez est marquée comme `retain` ou `copy` dont l' `[DisableZeroCopy]` attribut est défini. Cela est nécessaire, car le handle pour les chaînes de copie zéro est alloué sur la pile et n’est pas valide lors du retour de la fonction.

Exemple :

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

Vous pouvez également appliquer l’attribut au niveau de l’assembly et s’appliquera à tous les types de l’assembly :

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Dictionnaires fortement typés

Avec Xamarin. iOS 8,0, nous avons introduit la prise en charge pour créer facilement des classes fortement typées qui encapsulent `NSDictionaries` .

Bien qu’il soit toujours possible d’utiliser le type de données [DictionaryContainer](xref:Foundation.DictionaryContainer) avec une API manuelle, il est maintenant beaucoup plus simple de le faire.  Pour plus d’informations, consultez la page relative aux [types forts](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary"></a>

### <a name="strongdictionary"></a>StrongDictionary

Quand cet attribut est appliqué à une interface, le générateur produit une classe portant le même nom que l’interface qui dérive de [DictionaryContainer](xref:Foundation.DictionaryContainer) et convertit chaque propriété définie dans l’interface en accesseur Get fortement typé et setter pour le dictionnaire.

Cela génère automatiquement une classe qui peut être instanciée à partir d’un existant `NSDictionary` ou qui a été créé.

Cet attribut accepte un paramètre, le nom de la classe contenant les clés utilisées pour accéder aux éléments sur le dictionnaire.   Par défaut, chaque propriété de l’interface avec l’attribut recherche un membre dans le type spécifié pour un nom avec le suffixe « Key ».

Par exemple :

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

Dans le cas ci-dessus, la `MyOption` classe produira une propriété de type chaîne pour `Name` qui utilisera `MyOptionKeys.NameKey` comme clé dans le dictionnaire pour récupérer une chaîne.   Et utilisera `MyOptionKeys.AgeKey` comme clé dans le dictionnaire pour récupérer un `NSNumber` qui contient un int.

Si vous souhaitez utiliser une clé différente, vous pouvez utiliser l’attribut Export sur la propriété, par exemple :

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>Types de dictionnaire forts

Les types de données suivants sont pris en charge dans la `StrongDictionary` définition :

|Type d’interface C#|`NSDictionary` Type de stockage|
|---|---|
|`bool`|`Boolean` stocké dans un `NSNumber`|
|Valeurs d’énumération|entier stocké dans un `NSNumber`|
|`int`|entier 32 bits stocké dans un `NSNumber`|
|`uint`|entier non signé 32 bits stocké dans un `NSNumber`|
|`nint`|`NSInteger` stocké dans un `NSNumber`|
|`nuint`|`NSUInteger` stocké dans un `NSNumber`|
|`long`|entier 64 bits stocké dans un `NSNumber`|
|`float`|entier 32 bits stocké en tant que `NSNumber`|
|`double`|entier 64 bits stocké en tant que `NSNumber`|
|`NSObject` et sous-classes|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C# `Array` de `NSObject`|`NSArray`|
|C# `Array` d’énumérations|`NSArray` contenant des `NSNumber` valeurs|
