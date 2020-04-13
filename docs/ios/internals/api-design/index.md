---
title: Conception API Xamarin.iOS
description: Principes directeurs qui ont été utilisés pour l’architecte des API Xamarin.iOS et comment ceux-ci se rapportent à Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a2435b30b7d5b468fca6c55d295c87b9a0d20652
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304799"
---
# <a name="xamarinios-api-design"></a>Conception API Xamarin.iOS

En plus des bibliothèques de base de base de base qui font partie de Mono, [Xamarin.iOS](~/ios/index.yml) expédie avec des fixations pour diverses API iOS pour permettre aux développeurs de créer des applications iOS indigènes avec Mono.

Au cœur de Xamarin.iOS, il existe un moteur interop qui relie le monde C avec le monde Objectif-C, ainsi que des fixations pour les API iOS C-basé comme CoreGraphics et [OpenGL ES](#opengles).

Le temps d’exécution de bas niveau pour communiquer avec le code Objectif-C est dans [MonoTouch.ObjCRuntime](#objcruntime). En plus de cela, des fixations pour [la Fondation](#foundation), CoreFoundation, et [UIKit](#uikit) sont fournis.

## <a name="design-principles"></a>Principes de conception

Ce sont quelques-uns de nos principes de conception pour les liaisons Xamarin.iOS (ils s’appliquent également à Xamarin.Mac, les fixations Mono pour Objectif-C sur macOS):

- Suivez les [lignes directrices](https://docs.microsoft.com/dotnet/standard/design-guidelines) sur la conception des cadres
- Permettre aux développeurs de sous-classer les classes Objective-C :

  - Dérive d’une classe existante
  - Appelez le constructeur de base pour enchaîner
  - Des méthodes primordiales doivent être faites avec le système de remplacement de C
  - La sous-classification devrait fonctionner avec les constructions standard de C

- N’exposez pas les développeurs aux sélecteurs Objectif-C
- Fournir un mécanisme pour appeler les bibliothèques objectives-C arbitraires
- Rendre possible les tâches objectives-C courantes faciles et difficiles Objective-C
- Exposer les propriétés Objectif-C en tant que propriétés C
- Exposez une API fortement typée :

  - Augmenter la sécurité de type
  - Minimiser les erreurs de temps d’exécution
  - Obtenez IDE IntelliSense sur les types de retour
  - Permet la documentation popup IDE

- Encourager l’exploration en IDE des API :

  - Par exemple, au lieu d’exposer un tableau faiblement typé comme ceci :

    ```objc
    NSArray *getViews
    ```

    Exposez un type fort, comme ceci :

    ```csharp
    NSView [] Views { get; set; }
    ```

    Cela donne Visual Studio pour Mac la possibilité de faire l’auto-achèvement tout en parcourant l’API, rend toutes les `System.Array` opérations disponibles sur la valeur retournée, et permet à la valeur de retour de participer à LINQ.

- Types natifs CMD :

  - [`NSString`Devient`string`](~/ios/internals/api-design/nsstring.md)
  - Tourner `int` `uint` et les paramètres qui auraient dû être enumérations en cmumérations et en énumérations C AVEC `[Flags]` des attributs
  - Au lieu d’objets neutres, `NSArray` exposez les tableaux comme des tableaux fortement typés.
  - Pour les événements et les notifications, donner aux utilisateurs le choix entre :

    - Une version fortement typée par défaut
    - Une version faiblement typée pour les cas d’utilisation avancée

- Soutenir le modèle de délégué Objectif-C :

  - Système d’événements CMD
  - Exposer les délégués CMD (lambdas, méthodes anonymes et `System.Delegate`) aux API Objectif-C comme blocs

### <a name="assemblies"></a>Assemblys

Xamarin.iOS comprend un certain nombre d’assemblages qui constituent le *profil Xamarin.iOS*. La page [Assemblées](~/cross-platform/internals/available-assemblies.md) contient plus d’informations.

### <a name="major-namespaces"></a>Principaux espaces de noms

#### <a name="objcruntime"></a>ObjCRuntime ObjCRuntime

L’espace de nom [ObjCRuntime](xref:ObjCRuntime) permet aux développeurs de faire le pont entre les mondes entre C et Objectif-C.
Il s’agit d’une nouvelle reliure, conçue spécifiquement pour l’iOS, basée sur l’expérience de Cocoa et Gtk.

#### <a name="foundation"></a>Foundation

[L’espace](xref:Foundation) de noms de la Fondation fournit les types de données de base conçus pour interopérer avec le cadre de la Fondation Objectif-C qui fait partie de l’iOS et c’est la base de la programmation orientée objet dans Objectif-C.

Xamarin.iOS reflète dans C ' la hiérarchie des classes de l’Objectif-C. Par exemple, la classe de base Objective-C NSObject est utilisable à partir de C par l’intermédiaire de [Foundation.NSObject](xref:Foundation.NSObject).

Bien que cet espace de nom fournit des liaisons pour les types sous-jacents de fondation Objective-C, dans quelques cas, nous avons cartographié les types sous-jacents aux types .NET. Par exemple :

- Au lieu de traiter avec NSString et [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), le runtime expose ceux-ci comme C ['corde](xref:System.String)s et fortement tapé [tableau](xref:System.Array)s tout au long de l’API.

- Diverses API d’aide sont exposées ici pour permettre aux développeurs de lier des API Objectif-C tiers, d’autres API iOS ou API qui ne sont pas actuellement liés par Xamarin.iOS.

Pour plus de détails sur les API de liaison, consultez la section [générateur de liaison Xamarin.iOS.](~/cross-platform/macios/binding/binding-types-reference.md)

##### <a name="nsobject"></a>NSObject (NSObject)

Le type [NSObject](xref:Foundation.NSObject) est à la base de toutes les liaisons Objectif-C. Xamarin.iOS types miroir de deux classes de types de l’IOS CocoaTouch API: les types C (généralement appelés types CoreFoundation) et les types Objectif-C (ceux-ci proviennent tous de la classe NSObject).

Pour chaque type qui reflète un type non géré, il est possible d’obtenir l’objet indigène par la propriété [Handle.](xref:Foundation.NSObject.Handle)

Alors que Mono fournira la collecte des `Foundation.NSObject` ordures pour tous vos objets, les impléments de [l’interface System.IDisposable.](xref:System.IDisposable) Cela signifie que vous pouvez libérer explicitement les ressources de n’importe quel NSObject donné sans avoir à attendre que le collecteur d’ordures pour donner un coup de pied dans. Ceci est important lorsque vous utilisez des NSObjects lourds, par exemple, UIImages qui pourraient contenir des indications à de grands blocs de données.

Si votre type doit effectuer une finalisation déterministe, remplacez la [méthode NSObject.Dispose (bool)](xref:Foundation.NSObject.Dispose(System.Boolean)) Le paramètre à éliminer est «bool disposing», et si défini pour vrai, cela signifie que votre méthode De disposer est appelé parce que l’utilisateur explicitement appelé Dispose () sur l’objet. Si la valeur est fausse, cela signifie que votre méthode de débarrasse est appelée à partir du finalisateur sur le fil final.

##### <a name="categories"></a>Catégories

En commençant par Xamarin.iOS 8.10, il est possible de créer des catégories Objectif-C à partir de C.

Ceci est fait `Category` en utilisant l’attribut, spécifiant le type à étendre comme argument à l’attribut. L’exemple suivant étendra par exemple NSString.

```csharp
[Category (typeof (NSString))]
```

Chaque méthode de catégorie utilise le mécanisme normal d’exportation des méthodes vers Objectif-C en utilisant l’attribut `Export` :

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Toutes les méthodes d’extension gérées doivent être statiques, mais il est possible de créer des méthodes d’instance Objectif-C en utilisant la syntaxe standard pour les méthodes d’extension dans C :

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

et le premier argument de la méthode d’extension sera le cas sur lequel la méthode a été invoquée.

Exemple complet :

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

Cet exemple ajoutera une méthode d’instance native toUpper à la classe NSString, qui peut être invoquée à partir d’Objectif-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Un scénario où cela est utile est d’ajouter une méthode à un ensemble `UIViewController` entier de classes dans votre base de code, par exemple, ce serait faire toutes les instances rapportent qu’ils peuvent tourner:

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

##### <a name="preserveattribute"></a>PréserverAttribute

PreserveAttribute est un attribut personnalisé qui est utilisé pour dire mtouch - l’outil de déploiement Xamarin.iOS - pour préserver un type, ou un membre d’un type, pendant la phase lorsque l’application est traitée pour réduire sa taille.

Chaque membre qui n’est pas lié de manière statique par l’application est soumis à suppression. Par conséquent, cet attribut est utilisé pour marquer les membres qui ne sont pas statiquement référencés, mais qui sont encore nécessaires par votre application.

Par exemple, si vous instanciez dynamiquement des types, vous souhaiterez éventuellement conserver le constructeur par défaut de vos types. Si vous utilisez la sérialisation XML, vous souhaiterez éventuellement conserver les propriétés de vos types.

Vous pouvez appliquer cet attribut à chaque membre d’un type ou au type lui-même. Si vous voulez préserver l’ensemble du type, vous pouvez utiliser la syntaxe [Préserver (Tous les membres - vrai)] sur le type.

#### <a name="uikit"></a>UIKit (en)

L’espace de nom [UIKit](xref:UIKit) contient une cartographie en tête-à-tête de tous les composants de l’interface utilisateur qui composent CocoaTouch sous la forme de classes C. L’API a été modifiée pour suivre les conventions utilisées dans la langue C.

Les délégués C sont prévus pour des opérations communes. Consultez la section [des délégués](#delegates) pour plus d’informations.

#### <a name="opengles"></a>OpenGLES (OpenGLES)

Pour OpenGLES, nous distribuons une [version modifiée](xref:OpenTK) de [l’API OpenTK,](https://opentk.net/) une liaison orientée objet vers OpenGL qui a été modifiée pour utiliser les types et les structures de données CoreGraphics, ainsi que d’exposer uniquement les fonctionnalités disponibles sur iOS.

La fonctionnalité OpenGLES 1.1 est disponible sur le [type ES11.GL](xref:OpenTK.Graphics.ES11.GL).

La fonctionnalité OpenGLES 2.0 est disponible sur le [type ES20.GL](xref:OpenTK.Graphics.ES20.GL).

La fonctionnalité OpenGLES 3.0 est disponible sur le [type ES30.GL](xref:OpenTK.Graphics.ES30.GL).

### <a name="binding-design"></a>Conception de liaison

Xamarin.iOS n’est pas seulement une liaison avec la plate-forme objective-C sous-jacente. Il étend le système de type .NET et le système d’expédition pour mieux mélanger C et Objectif-C.

Tout comme P/Invoke est un outil utile pour invoquer les bibliothèques natives sur Windows et Linux, ou comme le support IJW peut être utilisé pour COM interop sur Windows, Xamarin.iOS étend le temps d’exécution pour prendre en charge la liaison des objets C aux objets Objectif-C.

La discussion dans les prochaines sections n’est pas nécessaire pour les utilisateurs qui créent des applications Xamarin.iOS, mais aidera les développeurs à comprendre comment les choses sont faites et les aidera lors de la création d’applications plus compliquées.

#### <a name="types"></a>Types

Là où cela avait un sens, les types de CMD sont exposés au lieu de types de Fondation de bas niveau, à l’univers C.  Cela signifie que [l’API utilise le type « corde » C au lieu de NSString](~/ios/internals/api-design/nsstring.md) et qu’il utilise des tableaux Cmd fortement tapés au lieu d’exposer NSArray.

En général, dans la conception Xamarin.iOS et Xamarin.Mac, l’objet sous-jacent `NSArray` n’est pas exposé. Au lieu de cela, `NSArray`le temps d’exécution `NSObject` convertit automatiquement s à des tableaux fortement tapés d’une certaine classe. Ainsi, Xamarin.iOS n’expose pas une méthode faiblement typée comme GetViews pour retourner un NSArray:

```csharp
NSArray GetViews ();
```

Au lieu de cela, la liaison expose une valeur de rendement fortement tapée, comme ceci:

```csharp
UIView [] GetViews ();
```

Il ya quelques méthodes `NSArray`exposées dans , pour les cas `NSArray` de coin où vous pourriez vouloir utiliser un directement, mais leur utilisation est déconseillée dans la liaison API.

En outre, dans **l’API** `CGPoint` classique `CGSize` au lieu d’exposer `CGRect`, et de `System.Drawing` l’API CoreGraphics, nous avons remplacé ceux avec les implémentations `RectangleF`, `PointF` et `SizeF` comme ils aideraient les développeurs à préserver le code OpenGL existant qui utilise OpenTK. Lors de l’utilisation de la nouvelle **API unifiée**64 bits , l’API CoreGraphics doit être utilisé.

#### <a name="inheritance"></a>Héritage

La conception de l’API Xamarin.iOS permet aux développeurs d’étendre les types objectifs-C natifs de la même manière qu’ils étendraient un type C, en utilisant le mot clé « dérogation » sur une classe dérivée, ainsi que l’enchaînement jusqu’à l’implémentation de base à l’aide du mot clé « base » C.

Cette conception permet aux développeurs d’éviter de traiter avec les sélecteurs Objectif-C dans le cadre de leur processus de développement, parce que l’ensemble du système Objectif-C est déjà enveloppé à l’intérieur des bibliothèques Xamarin.iOS.

#### <a name="types-and-interface-builder"></a>Types et Interface Builder

Lorsque vous créez des classes .NET qui sont des instances de types créés `IntPtr` par Interface Builder, vous devez fournir un constructeur qui prend un seul paramètre.
Ceci est nécessaire pour lier l’instance d’objet géré avec l’objet non géré.
Le code se compose d’une seule ligne, comme celle-ci:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>Délégués

L’objectif-C et le Cmd ont des significations différentes pour le mot délégué dans chaque langue.

Dans le monde de l’Objectif-C, et dans la documentation que vous trouverez en ligne sur CocoaTouch, un délégué est généralement un exemple d’une classe qui répondra à un ensemble de méthodes. Ceci est très similaire à une interface C, avec la différence étant que les méthodes ne sont pas toujours obligatoires.

Ces délégués jouent un rôle important dans l’UIKit et d’autres API CocoaTouch. Ils sont utilisés pour accomplir diverses tâches :

- Fournir des notifications à votre code (semblable à la livraison d’événements dans C ou GtkMD).
- Implémenter des modèles de contrôle de visualisation des données.
- Pour conduire le comportement d’un contrôle.

Le modèle de programmation a été conçu pour minimiser la création de classes dérivées pour modifier le comportement d’un contrôle. Cette solution est similaire dans l’esprit à ce que d’autres boîtes à outils GUI ont fait au fil des ans: signaux De Gtk, fentes Qt, événements Winforms, événements WPF / Silverlight et ainsi de suite. Pour éviter d’avoir des centaines d’interfaces (une pour chaque action) ou d’exiger des développeurs qu’ils implémentent trop de méthodes dont ils n’ont pas besoin, Objectif-C prend en charge les définitions facultatives de la méthode. C’est différent des interfaces C qui nécessitent la mise en œuvre de toutes les méthodes.

Dans les classes Objectif-C, vous verrez que les classes qui `delegate`utilisent ce modèle de programmation exposent une propriété, généralement appelée , qui est nécessaire pour mettre en œuvre les parties obligatoires de l’interface et zéro, ou plus, des pièces facultatives.

Dans Xamarin.iOS trois mécanismes mutuellement exclusifs pour lier à ces délégués sont offerts:

1. [Par l’intermédiaire d’événements](#via-events).
2. [Fortement tapé `Delegate` via une propriété](#strongly-typed-via-a-delegate-property)
3. [Loosely tapé `WeakDelegate` via une propriété](#loosely-typed-via-the-weakdelegate-property)

Par exemple, considérez la classe UIWebView. Celui-ci envoie à une instance UIWebViewDelegate qui est assignée à la propriété déléguée.

##### <a name="via-events"></a>Par l’intermédiaire d’événements

Pour de nombreux types, Xamarin.iOS créera automatiquement `UIWebViewDelegate` un délégué approprié qui transmettra les appels sur les événements C. Pour `UIWebView` :

- La méthode webViewDidStartLoad est cartographiée pour l’événement [UIWebView.LoadStarted.](xref:UIKit.UIWebView.LoadStarted)
- La méthode webViewDidFinishLoad est cartographiée pour l’événement [UIWebView.LoadFinished.](xref:UIKit.UIWebView.LoadFinished)
- La méthode webView:didFailLoadWithError est cartographiée pour l’événement [UIWebView.LoadError.](xref:UIKit.UIWebView.LoadError)

Par exemple, ce programme simple enregistre les heures de début et de fin lors du chargement d’une vue web :

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Via Propriétés

Les événements sont utiles lorsqu’il peut y avoir plus d’un abonné à l’événement. En outre, les événements sont limités aux cas où il n’y a pas de valeur de retour du code.

Pour les cas où le code est censé retourner une valeur, nous avons opté à la place pour les propriétés. Cela signifie qu’une seule méthode peut être définie à un moment donné dans un objet.

Par exemple, vous pouvez utiliser ce mécanisme pour rejeter le `UITextField`clavier sur l’écran sur le gestionnaire pour un :

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

La `UITextField`propriété `ShouldReturn` de la présente affaire prend comme argument un délégué qui retourne une valeur bool et détermine si le TextField devrait faire quelque chose avec le bouton Retour étant pressé. Dans notre méthode, nous revenons *vrai* à l’appelant, mais nous supprimons `ResignFirstResponder`également le clavier de l’écran (ce qui se produit lorsque le champ de texte appelle ).

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortement tapé via une propriété déléguée

Si vous préférez ne pas utiliser les événements, vous pouvez fournir votre propre sous-classe [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) et l’attribuer à la propriété [UIWebView.Delegate.](xref:UIKit.UIWebView.Delegate) Une fois que UIWebView.Delegate a été affecté, le mécanisme de répartition de l’événement UIWebView ne fonctionnera plus, et les méthodes UIWebViewDelegate seront invoquées lorsque les événements correspondants se produiront.

Par exemple, ce type simple enregistre le temps qu’il faut pour charger une vue web :

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

Ce qui précède est utilisé dans un code comme celui-ci:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Ce qui précède créera un UIWebViewer et il lui demandera d’envoyer des messages à une instance de Notificateur, une classe que nous avons créée pour répondre aux messages.

Ce modèle est également utilisé pour contrôler le comportement de certains contrôles, par exemple dans l’affaire `UIWebView` UIWebView, la propriété [UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) permet à l’instance de contrôler si la charge d’une `UIWebView` page sera chargée ou non.

Le modèle est également utilisé pour fournir les données sur demande pour quelques contrôles. Par exemple, le contrôle [UITableView](xref:UIKit.UITableView) est un puissant contrôle de rendu de table, et l’apparence et le contenu sont conduits par une instance [d’un UITableViewDataSource](xref:UIKit.UITableViewDataSource)

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Loosely Tapd via la propriété WeakDelegate

En plus de la propriété fortement tapée, il y a également un délégué de type faible qui permet au développeur de lier les choses différemment si désiré.
Partout où `Delegate` une propriété fortement dactylographiée est `WeakDelegate` exposée dans la liaison de Xamarin.iOS, une propriété correspondante est également exposée.

Lors de `WeakDelegate`l’utilisation de la , vous êtes responsable de décorer correctement votre classe en utilisant [l’attribut Export](xref:Foundation.ExportAttribute) pour spécifier le sélecteur. Par exemple :

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

Notez qu’une fois que la `WeakDelegate` propriété a été attribuée, la `Delegate` propriété ne sera pas utilisée. De plus, si vous implémentez la méthode dans une classe de base héritée que vous souhaitez [Exporter], vous devez en faire une méthode publique.

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>Cartographie du modèle de délégué Objectif-C à C\#

Lorsque vous voyez des échantillons d’Objectif-C qui ressemblent à ceci :

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

Cela instruit la langue de créer et de construire un exemple de la classe "SomethingDelegate" et d’attribuer la valeur à la propriété déléguée sur la variable foo. Ce mécanisme est pris en charge par Xamarin.iOS et C ' la syntaxe est:

```csharp
foo.Delegate = new SomethingDelegate ();
```

Dans Xamarin.iOS, nous avons fourni des classes fortement typées qui cartographient les classes de délégués Objectif-C. Pour les utiliser, vous sous-classez et passerez outre les méthodes définies par la mise en œuvre de Xamarin.iOS. Pour plus d’informations sur leur fonctionnement, voir la section "Modèles" ci-dessous.

### <a name="mapping-delegates-to-c"></a>Cartographier les délégués à C\#

UIKit utilise en général les délégués Objectif-C sous deux formes.

Le premier formulaire fournit une interface au modèle d’un composant. Par exemple, en tant que mécanisme de fournir des données sur la demande pour une vue, comme l’installation de stockage de données pour une vue de liste.  Dans ces cas, vous devez toujours créer un exemple de la classe appropriée et attribuer la variable.

Dans l’exemple suivant, `UIPickerView` nous fournissons la mise en œuvre d’un modèle qui utilise des chaînes :

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

Le deuxième formulaire consiste à fournir une notification pour les événements. Dans ces cas, bien que nous exposions toujours l’API sous la forme décrite ci-dessus, nous fournissons également des événements C, qui devraient être plus simples à utiliser pour des opérations rapides et intégrés avec des délégués anonymes et des expressions lambda dans C.

Par exemple, vous `UIAccelerometer` pouvez vous abonner à des événements :

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Les deux options sont disponibles là où elles ont un sens, mais en tant que programmeur, vous devez choisir l’une ou l’autre. Si vous créez votre propre instance d’un répondant/délégué fortement tapé et l’attribuez, les événements CMD ne seront pas fonctionnels. Si vous utilisez les événements C, les méthodes de votre classe d’intervenants/délégués ne seront jamais appelées.

L’exemple précédent `UIWebView` qui a été utilisé peut être écrit à l’aide de lambdas C 3.0 comme ceci:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>Réponse aux événements

Dans le code Objectif-C, parfois les gestionnaires d’événements pour plusieurs contrôles et les fournisseurs d’informations pour les contrôles multiples, seront hébergés dans la même classe. Cela est possible parce que les classes répondent aux messages, et tant que les classes répondent aux messages, il est possible de lier des objets ensemble.

Comme indiqué précédemment, Xamarin.iOS prend en charge à la fois le modèle de programmation basé sur l’événement C et le modèle de délégué Objectif-C, où vous pouvez créer une nouvelle classe qui met en œuvre le délégué et l’emporte sur les méthodes souhaitées.

Il est également possible d’appuyer le modèle d’Objectif-C où les intervenants pour plusieurs opérations différentes sont tous hébergés dans le même cas d’une classe. Pour ce faire cependant, vous devrez utiliser des fonctionnalités de bas niveau de la liaison Xamarin.iOS.

Par exemple, si vous vouliez que `UITextFieldDelegate.textFieldShouldClear`votre classe `UIWebViewDelegate.webViewDidStartLoad`réponde à la fois au message : dans le même cas d’une classe, vous devrez utiliser la déclaration d’attribut [Export] :

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

Les noms C pour les méthodes ne sont pas importants; tout ce qui compte, ce sont les ficelles transmises à l’attribut [Export].

Lors de l’utilisation de ce style de programmation, assurez-vous que les paramètres C ' correspondent aux types réels que le moteur de temps d’exécution passera.

#### <a name="models"></a>Modèles

Dans les installations de stockage UIKit, ou dans les intervenants qui sont mis en œuvre à l’aide de classes d’aide, ceux-ci sont généralement mentionnés dans le code Objectif-C comme délégués, et ils sont mis en œuvre comme protocoles.

Les protocoles Objectif-C sont comme les interfaces, mais ils prennent en charge les méthodes facultatives, c’est-à-dire que toutes les méthodes ne doivent pas être mises en œuvre pour que le protocole fonctionne.

Il y a deux façons de mettre en œuvre un modèle. Vous pouvez soit l’implémenter manuellement, soit utiliser les définitions existantes fortement typées.

Le mécanisme manuel est nécessaire lorsque vous essayez d’implémenter une classe qui n’a pas été liée par Xamarin.iOS. Il est très facile à faire:

- Signalez votre classe pour l’inscription à l’heure de course
- Appliquer l’attribut [Export] avec le nom du sélecteur réel sur chaque méthode que vous souhaitez remplacer
- Instantané de la classe, et passez-le.

Par exemple, l’implémentation suivante n’est qu’une des méthodes facultatives dans la définition du protocole UIApplicationDelegate :

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Le nom du sélecteur Objectif-C ("applicationDidFinishLaunching:") est déclaré `[Register]` avec l’attribut Export et la classe est enregistrée avec l’attribut.

Xamarin.iOS fournit des déclarations fortement dactylographiées, prêtes à l’emploi, qui ne nécessitent pas de liaison manuelle. Pour soutenir ce modèle de programmation, l’exécution Xamarin.iOS prend en charge l’attribut [modèle] sur une déclaration de classe. Cela informe le temps d’exécution qu’il ne devrait pas filer toutes les méthodes dans la classe, sauf si les méthodes sont explicitement mises en œuvre.

Cela signifie que dans UIKit, les classes qui représentent un protocole avec des méthodes facultatives sont écrites comme ceci:

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

Lorsque vous voulez mettre en œuvre un modèle qui ne met en œuvre que certaines des méthodes, tout ce que vous avez à faire est de remplacer les méthodes qui vous intéressent, et d’ignorer les autres méthodes. Le temps d’exécution ne fera que brancher les méthodes écrasées, pas les méthodes originales au monde Objectif-C.

L’équivalent de l’échantillon manuel précédent est :

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

Les avantages sont qu’il n’est pas nécessaire de creuser dans les fichiers d’en-tête Objectif-C pour trouver le sélecteur, les types d’arguments, ou la cartographie à C, et que vous obtenez intellisense de Visual Studio pour Mac, avec des types forts

#### <a name="xib-outlets-and-c"></a>Points de vente XIB et C\#

> [!IMPORTANT]
> Cette section explique l’intégration IDE avec les points de vente lors de l’utilisation de fichiers XIB. Lors de l’utilisation du concepteur Xamarin pour iOS, tout cela est remplacé par l’entrée d’un nom sous **Identity > Name** dans la section Propriétés de votre IDE, comme indiqué ci-dessous:
>
> [![](images/designeroutlet.png "Entering an item Name in the iOS Designer")](images/designeroutlet.png#lightbox)
>
>Pour plus d’informations sur le concepteur iOS, veuillez consulter le document [Introduction to the iOS Designer.](~/ios/user-interface/designer/introduction.md#how-it-works)

Il s’agit d’une description de faible niveau de la façon dont les points de vente s’intègrent à C et est fourni pour les utilisateurs avancés de Xamarin.iOS. Lors de l’utilisation de Visual Studio pour Mac, la cartographie se fait automatiquement dans les coulisses à l’aide de code généré sur le vol pour vous.

Lorsque vous concevez votre interface utilisateur avec Interface Builder, vous ne concevez que l’apparence de l’application et établirez certaines connexions par défaut. Si vous souhaitez obtenir des informations programmatiques, modifier le comportement d’un contrôle au moment de l’exécution ou modifier le contrôle au moment de l’exécution, il est nécessaire de lier certains des contrôles à votre code géré.

Cela se fait en quelques étapes :

1. Ajoutez la déclaration de **sortie** au **propriétaire de**votre fichier .
1. Connectez votre contrôle au **propriétaire du fichier**.
1. Stockez l’interface utilisateur ainsi que les connexions dans votre fichier XIB/NIB.
1. Chargez le fichier NIB au moment de l’exécution.
1. Accédez à la variable de prise.

Les étapes (1) à travers (3) sont couvertes dans la documentation d’Apple pour les interfaces de construction avec Interface Builder.

Lors de l’utilisation de Xamarin.iOS, votre application devra créer une classe qui dérive de UIViewController. Il est mis en œuvre comme ceci:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Ensuite, pour charger votre ViewController à partir d’un fichier NIB, vous faites ceci:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Cela charge l’interface utilisateur de la NIB. Maintenant, pour accéder aux points de vente, il est nécessaire d’informer le temps d’exécution que nous voulons y accéder. Pour ce faire, la `UIViewController` sous-classe doit déclarer les propriétés et les annoter avec l’attribut [Connect]. Comme ceci :

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

La mise en œuvre de la propriété est celle qui récupère réellement et stocke la valeur pour le type natif réel.

Vous n’avez pas besoin de vous inquiéter à ce sujet lors de l’utilisation de Visual Studio pour Mac et InterfaceBuilder. Visual Studio pour Mac reflète automatiquement tous les points de vente déclarés avec du code dans une classe partielle qui est compilée dans le cadre de votre projet.

#### <a name="selectors"></a>Sélecteurs

Un concept de base de la programmation Objective-C est les sélecteurs. Vous rencontrerez souvent des API qui vous obligent à passer un sélecteur, ou s’attend à ce que votre code réponde à un sélecteur.

Créer de nouveaux sélecteurs dans C est très `ObjCRuntime.Selector` facile - vous venez de créer une nouvelle instance de la classe et d’utiliser le résultat dans n’importe quel endroit dans l’API qui l’exige. Par exemple :

```csharp
var selector_add = new Selector ("add:plus:");
```

Pour qu’une méthode CMD réponde à un `NSObject` appel de sélecteur, elle doit hériter du type et la méthode Cmd doit être décorée avec le nom du sélecteur à l’aide de l’attribut. `[Export]` Par exemple :

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Notez que les noms des sélecteurs **doivent** correspondre exactement, y compris tous les colons intermédiaires et de fuite (":"), si présent.

#### <a name="nsobject-constructors"></a>Constructeurs NSObject

La plupart des classes de Xamarin.iOS qui dérivent de `NSObject` exposera des constructeurs spécifiques à la fonctionnalité de l’objet, mais ils exposeront également divers constructeurs qui ne sont pas immédiatement évidents.

Les constructeurs sont utilisés comme suit :

```csharp
public Foo (IntPtr handle)
```

Ce constructeur est utilisé pour instantanéifier votre classe lorsque le temps d’exécution a besoin de cartographier votre classe à une classe non gérée. Cela se produit lorsque vous chargez un fichier XIB/NIB.  À ce stade, le temps d’exécution Objectif-C aura créé un objet dans le monde non géré, et ce constructeur sera appelé à initialiser le côté géré.

Typiquement, tout ce que vous devez faire est d’appeler le constructeur de base avec le paramètre de poignée, et dans le corps, faire toute initialisation qui est nécessaire.

```csharp
public Foo ()
```

C’est le constructeur par défaut pour une classe, et dans Xamarin.iOS a fourni des classes, cela initialise la classe Foundation.NSObject et toutes les classes entre les deux, et à la fin, enchaîne cette méthode à la méthode Objective-C `init` sur la classe.

```csharp
public Foo (NSObjectFlag x)
```

Ce constructeur est utilisé pour initialiser l’instance, mais empêcher le code d’appeler la méthode Objective-C "init" à la fin. Vous l’utilisez généralement lorsque vous vous êtes `[Export]` déjà inscrit à l’initialisation (lorsque vous utilisez sur votre constructeur) ou lorsque vous avez déjà fait votre initialisation par un autre moyen.

```csharp
public Foo (NSCoder coder)
```

Ce constructeur est prévu pour les cas où l’objet est parasécé à partir d’une instance de NSCoding.

#### <a name="exceptions"></a>Exceptions

La conception de l’API Xamarin.iOS ne soulève pas d’exceptions Objective-C comme exceptions C. La conception fait respecter qu’aucune ordure ne soit envoyée au monde de l’Objectif-C en premier lieu et que toutes les exceptions qui doivent être produites sont produites par la liaison elle-même avant que des données non valides ne soient jamais transmises au monde de l’Objectif-C.

#### <a name="notifications"></a>Notifications

Dans iOS et OS X, les développeurs peuvent s’abonner aux notifications qui sont diffusées par la plate-forme sous-jacente. Cela se fait `NSNotificationCenter.DefaultCenter.AddObserver` en utilisant la méthode. La `AddObserver` méthode prend deux paramètres; l’un est la notification à laquelle vous souhaitez vous abonner; l’autre est la méthode à invoquer lorsque la notification est soulevée.

Dans Xamarin.iOS et Xamarin.Mac, les clés des différentes notifications sont hébergées sur la classe qui déclenche les notifications. Par exemple, les notifications `UIMenuController` soulevées `static NSString` par les `UIMenuController` sont hébergées comme propriétés dans les classes qui se terminent par le nom "Notification".

### <a name="memory-management"></a>Gestion de la mémoire

Xamarin.iOS a un éboueur qui s’occupera de libérer des ressources pour vous quand ils ne sont plus utilisés. En plus du collecteur d’ordures, tous les objets qui dérivent de `NSObject` l’implément de l’interface. `System.IDisposable`

#### <a name="nsobject-and-idisposable"></a>NSObject et IDisposable

Exposer l’interface `IDisposable` est un moyen pratique d’aider les développeurs à libérer des objets `UIImage` qui pourraient encapsuler de grands blocs de mémoire (par exemple, un pourrait ressembler à juste un pointeur innocent, mais pourrait être pointant vers une image de 2 mégaoctets) et d’autres ressources importantes et limitées (comme un tampon de décodage vidéo).

NSObject implémente l’interface IDisposable et aussi le [modèle .NET Dispose](https://msdn.microsoft.com/library/fs2xkftw.aspx). Cela permet aux développeurs qui sous-classent NSObject de passer outre au comportement disposer et de libérer leurs propres ressources à la demande. Par exemple, considérez ce contrôleur de vue qui garde autour d’un tas d’images :

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

Lorsqu’un objet géré est éliminé, il n’est plus utile. Vous pouvez encore avoir une référence aux objets, mais l’objet est à toutes fins utiles invalides à ce stade. Certains API .NET s’en assurent en jetant une impression objectistosé si vous essayez d’accéder à toutes les méthodes sur un objet éliminé, par exemple:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Même si vous pouvez toujours accéder à la variable "image", il s’agit vraiment d’une référence invalide et ne pointe plus vers l’objet Objectif-C qui détenait l’image.

Mais l’élimination d’un objet dans le C ne signifie pas que l’objet sera nécessairement détruit. Tout ce que vous faites est de libérer la référence que C a eu à l’objet. Il est possible que l’environnement cocoaique ait gardé une référence pour son propre usage. Par exemple, si vous définissez une propriété Image d’UIImageView sur une image, puis que vous disposez de l’image, l’UIImageView sous-jacent avait pris sa propre référence et conservera une référence à cet objet jusqu’à ce qu’il soit fini de l’utiliser.

#### <a name="when-to-call-dispose"></a>Quand appeler Dispose

Vous devriez appeler Dispose lorsque vous avez besoin de Mono pour vous débarrasser de votre objet. Un cas d’utilisation possible est lorsque Mono n’a aucune connaissance que votre NSObject est en fait tenir une référence à une ressource importante comme la mémoire, ou un pool d’informations. Dans ces cas, vous devriez appeler Dispose pour libérer immédiatement la référence à la mémoire, au lieu d’attendre que Mono effectue un cycle de collecte des ordures.

En interne, lorsque Mono crée [des références NSString à partir de chaînes C ,](~/ios/internals/api-design/nsstring.md)il les disposera immédiatement pour réduire la quantité de travail que le éboueur doit faire. Moins il y a d’objets à traiter, plus vite le GC s’exécutera rapidement.

#### <a name="when-to-keep-references-to-objects"></a>Quand conserver les références aux objets

Un effet secondaire que la gestion automatique de la mémoire a est que le GC se débarrassera des objets inutilisés tant qu’il n’y a aucune référence à eux. Cela peut parfois avoir des effets secondaires surprenants, par exemple, si vous créez une variable locale pour tenir votre contrôleur de vue de haut niveau, ou votre fenêtre de niveau supérieur, puis ayant ceux qui disparaissent derrière votre dos.

Si vous ne conservez pas une référence dans vos variables statiques ou d’instance à vos objets, Mono appellera avec plaisir la méthode Dispose() sur eux, et ils publieront la référence à l’objet. Comme cela pourrait être la seule référence en suspens, l’exécution Objective-C détruira l’objet pour vous.

## <a name="related-links"></a>Liens connexes

- [Champs de liaison](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
