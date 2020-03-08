---
title: Conception de l’API Xamarin. iOS
description: Principes directeurs utilisés pour l’architecture des API Xamarin. iOS et leurs relations avec Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a2435b30b7d5b468fca6c55d295c87b9a0d20652
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915181"
---
# <a name="xamarinios-api-design"></a>Conception de l’API Xamarin. iOS

En plus des bibliothèques de classes de base principales qui font partie de mono, [Xamarin. iOS](~/ios/index.yml) est fourni avec des liaisons pour différentes API iOS afin de permettre aux développeurs de créer des applications iOS natives avec mono.

Au cœur de Xamarin. iOS, il existe un moteur d’interopérabilité qui fait C# un pont entre le monde et le monde objective-c, ainsi que des liaisons pour les API basées sur C iOS telles que coregraphics et [OpenGL es](#opengles).

Le runtime de bas niveau pour communiquer avec le code Objective-C se trouve dans [MonoTouch. ObjCRuntime](#objcruntime). En plus de cela, des liaisons pour [Foundation](#foundation), corefoundation et [UIKit](#uikit) sont fournies.

## <a name="design-principles"></a>Principes de conception

Voici quelques-uns de nos principes de conception pour les liaisons Xamarin. iOS (elles s’appliquent également à Xamarin. Mac, les liaisons mono pour objective-C sur macOS) :

- Suivre les [instructions de conception](https://docs.microsoft.com/dotnet/standard/design-guidelines) de l’infrastructure
- Permet aux développeurs de sous-classer des classes objective-C :

  - Dériver d’une classe existante
  - Appeler le constructeur de base pour chaîner
  - Les méthodes de substitution doivent être effectuées C#avec le système de remplacement de
  - Le sous-classement doit C# fonctionner avec les constructions standard

- N’exposez pas les développeurs aux sélecteurs objective-C
- Fournir un mécanisme pour appeler des bibliothèques objective-C arbitraires
- Rendez les tâches courantes objective-c simples et difficiles à effectuer
- Exposer les propriétés objective- C# C en tant que propriétés
- Exposer une API fortement typée :

  - Augmenter la sécurité des types
  - Réduire les erreurs d’exécution
  - Obtenir l’IDE IntelliSense sur les types de retour
  - Permet la documentation contextuelle de l’IDE

- Encourager l’exploration dans l’IDE des API :

  - Par exemple, au lieu d’exposer un tableau faiblement typé comme suit :

    ```objc
    NSArray *getViews
    ```

    Exposez un type fort, comme suit :

    ```csharp
    NSView [] Views { get; set; }
    ```

    Cela permet de Visual Studio pour Mac la possibilité d’effectuer une saisie semi-automatique lors de la navigation dans l’API, de rendre toutes les opérations de `System.Array` disponibles sur la valeur retournée et permet à la valeur de retour de participer à LINQ.

- Types C# natifs :

  - [`NSString` devient `string`](~/ios/internals/api-design/nsstring.md)
  - Transformez les paramètres `int` et `uint` qui doivent avoir été C# des énumérations C# et des énumérations avec des attributs `[Flags]`
  - Au lieu d’objets `NSArray` neutres sur le type, exposez les tableaux en tant que tableaux fortement typés.
  - Pour les événements et les notifications, donnez aux utilisateurs le choix entre :

    - Une version fortement typée par défaut
    - Version faiblement typée pour les cas d’usage avancés

- Prendre en charge le modèle de délégué objective-C :

  - C#système d’événements
  - Exposer C# des délégués (lambdas, méthodes anonymes et `System.Delegate`) à des API objective-C en tant que blocs

### <a name="assemblies"></a>Assemblys

Xamarin. iOS comprend un certain nombre d’assemblys qui constituent le *Profil Xamarin. iOS*. La page [assemblys](~/cross-platform/internals/available-assemblies.md) contient plus d’informations.

### <a name="major-namespaces"></a>Principaux espaces de noms

#### <a name="objcruntime"></a>ObjCRuntime

L’espace de noms [ObjCRuntime](xref:ObjCRuntime) permet aux développeurs de relier C# les mondes entre et objective-C.
Il s’agit d’une nouvelle liaison, conçue spécifiquement pour iOS, basée sur l’expérience de cacao # et GTK #.

#### <a name="foundation"></a>Foundation

L’espace de noms [Foundation](xref:Foundation) fournit les types de données de base conçus pour interagir avec l’infrastructure objective-c Foundation qui fait partie de l’IOS et est la base de la programmation orientée objet en objective-c.

Xamarin. iOS reflète dans C# la hiérarchie des classes de Objective-C. Par exemple, la classe de base objective-C NSObject est C# utilisable par le biais de [Foundation. NSObject](xref:Foundation.NSObject).

Bien que cet espace de noms fournit des liaisons pour les types objective-C Foundation sous-jacents, dans quelques cas, nous avons mappé les types sous-jacents aux types .NET. Par exemple :

- Au lieu de gérer chaîne NSString et [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), le runtime les expose sous forme C#de [chaîne](xref:System.String)s et de [tableau](xref:System.Array)fortement typé dans l’ensemble de l’API.

- Diverses API d’assistance sont exposées ici pour permettre aux développeurs de lier des API objective-C tierces, d’autres API iOS ou API qui ne sont pas actuellement liées par Xamarin. iOS.

Pour plus d’informations sur les API de liaison, consultez la section [Générateur de liaisons Xamarin. iOS](~/cross-platform/macios/binding/binding-types-reference.md) .

##### <a name="nsobject"></a>NSObject

Le type [NSObject](xref:Foundation.NSObject) est la base de toutes les liaisons objective-C. Les types Xamarin. iOS reflètent deux classes de types à partir des API CocoaTouch iOS : les types C (généralement appelés types CoreFoundation) et les types objective-C (tous dérivés de la classe NSObject).

Pour chaque type qui reflète un type non managé, il est possible d’obtenir l’objet natif via la propriété [handle](xref:Foundation.NSObject.Handle) .

Bien que mono fournisse garbage collection pour tous vos objets, le `Foundation.NSObject` implémente l’interface [System. IDisposable](xref:System.IDisposable) . Cela signifie que vous pouvez libérer explicitement les ressources d’un NSObject donné sans avoir à attendre que le garbage collector démarre. C’est important lorsque vous utilisez un NSObjects lourd, par exemple, UIImages qui peut contenir des pointeurs vers des blocs de données volumineux.

Si votre type doit effectuer une finalisation déterministe, remplacez la [méthode NSObject. Dispose (bool).](xref:Foundation.NSObject.Dispose(System.Boolean)) le paramètre à supprimer est « bool disposing » et, si la valeur est true, cela signifie que votre méthode dispose est appelée parce que l’utilisateur a explicitement appelé Dispose () sur l’objet. Si la valeur est false, cela signifie que votre méthode Dispose (bool disposing) est appelée à partir du finaliseur sur le thread finaliseur.

##### <a name="categories"></a>Catégories

À compter de Xamarin. iOS 8,10, il est possible de créer des catégories objective-C à partir de C#.

Pour ce faire, utilisez l’attribut `Category`, en spécifiant le type à étendre en tant qu’argument à l’attribut. L’exemple suivant permet d’étendre chaîne NSString.

```csharp
[Category (typeof (NSString))]
```

Chaque méthode de catégorie utilise le mécanisme normal pour exporter des méthodes vers objective-C à l’aide de l’attribut `Export` :

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Toutes les méthodes d’extension managées doivent être statiques, mais il est possible de créer des méthodes d’instance objective-C à C#l’aide de la syntaxe standard pour les méthodes d’extension dans :

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

et le premier argument de la méthode d’extension sera l’instance sur laquelle la méthode a été appelée.

Exemple complet :

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

Cet exemple ajoute une méthode d’instance toUpper native à la classe chaîne NSString, qui peut être appelée à partir de Objective-C.

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

Un scénario où cela est utile est l’ajout d’une méthode à un ensemble complet de classes dans votre code base, par exemple, cela fait que toutes les instances `UIViewController` indiquent qu’elles peuvent pivoter :

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

##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute est un attribut personnalisé utilisé pour indiquer à mTouch – l’outil de déploiement Xamarin. iOS : pour conserver un type, ou un membre d’un type, pendant la phase de traitement de l’application afin de réduire sa taille.

Chaque membre qui n’est pas lié de manière statique par l’application est soumis à suppression. Par conséquent, cet attribut est utilisé pour marquer les membres qui ne sont pas statiquement référencés, mais qui sont toujours requis par votre application.

Par exemple, si vous instanciez dynamiquement des types, vous souhaiterez éventuellement conserver le constructeur par défaut de vos types. Si vous utilisez la sérialisation XML, vous souhaiterez éventuellement conserver les propriétés de vos types.

Vous pouvez appliquer cet attribut à chaque membre d’un type ou au type lui-même. Si vous souhaitez conserver le type entier, vous pouvez utiliser la syntaxe [Preserve (AllMembers = true)] sur le type.

#### <a name="uikit"></a>UIKit

L’espace de noms [UIKit](xref:UIKit) contient un mappage un-à-un avec tous les composants d’interface utilisateur qui composent des C# cocoatouch sous la forme de classes. L’API a été modifiée pour suivre les conventions utilisées dans le C# langage.

C#les délégués sont fournis pour les opérations courantes. Pour plus d’informations, consultez la section [délégués](#delegates) .

#### <a name="opengles"></a>OpenGLES

Pour OpenGL, nous distribuons une [version modifiée](xref:OpenTK) de l’API [OpenTK](https://opentk.net/) , une liaison orientée objet à OpenGL qui a été modifiée pour utiliser des structures et des types de données coregraphics, ainsi que pour exposer uniquement les fonctionnalités disponibles sur iOS.

La fonctionnalité OpenGL 1,1 est disponible via le [type ES11.GL](xref:OpenTK.Graphics.ES11.GL).

La fonctionnalité OpenGL 2,0 est disponible via le [type ES20.GL](xref:OpenTK.Graphics.ES20.GL).

La fonctionnalité OpenGL 3,0 est disponible via le [type ES30.GL](xref:OpenTK.Graphics.ES30.GL).

### <a name="binding-design"></a>Conception de liaison

Xamarin. iOS n’est pas simplement une liaison à la plateforme objective-C sous-jacente. Il étend le système de type .NET et le système de distribution C# pour mieux mélanger et objective-C.

Tout comme P/Invoke est un outil utile pour appeler des bibliothèques natives sur Windows et Linux, ou lorsque la prise en charge de IJW peut être utilisée pour COM Interop sur Windows, Xamarin. iOS C# étend le runtime pour prendre en charge la liaison d’objets aux objets objective-C.

La discussion dans les sections suivantes n’est pas nécessaire pour les utilisateurs qui créent des applications Xamarin. iOS, mais elle aide les développeurs à comprendre comment effectuer les opérations et les assiste lors de la création d’applications plus compliquées.

#### <a name="types"></a>Types

Là où cela s’avère C# logique, les types sont exposés au lieu des types de fondation de C# bas niveau, à l’univers.  Cela signifie que [l’API utilise le C# type « String » à la place de chaîne NSString](~/ios/internals/api-design/nsstring.md) et qu’il C# utilise des tableaux fortement typés au lieu d’exposer NSArray.

En général, dans la conception Xamarin. iOS et Xamarin. Mac, l’objet `NSArray` sous-jacent n’est pas exposé. Au lieu de cela, le Runtime convertit automatiquement les `NSArray`s en tableaux fortement typés d’une classe `NSObject`. Ainsi, Xamarin. iOS n’expose pas une méthode faiblement typée telle que GetViews pour retourner un NSArray :

```csharp
NSArray GetViews ();
```

Au lieu de cela, la liaison expose une valeur de retour fortement typée, comme suit :

```csharp
UIView [] GetViews ();
```

Il existe quelques méthodes exposées dans `NSArray`, dans les cas où vous voudrez peut-être utiliser directement un `NSArray`, mais leur utilisation est déconseillée dans la liaison d’API.

En outre, dans le **API classique** au lieu d’exposer `CGRect`, `CGPoint` et `CGSize` à partir de l’API coregraphics, nous avons remplacé ceux-ci par les implémentations `System.Drawing` `RectangleF`, `PointF` et `SizeF`, car ils aideront les développeurs à conserver le code OpenGL existant qui utilise OpenTK. Lors de l’utilisation de la nouvelle **API unifiée**64 bits, l’API coregraphics doit être utilisée.

#### <a name="inheritance"></a>Héritage

La conception de l’API Xamarin. iOS permet aux développeurs d’étendre les types objective-C natifs de la même C# façon qu’ils étendent un type, en utilisant le mot clé « override » sur une classe dérivée, ainsi que le chaînage jusqu’à l' C# implémentation de base à l’aide du mot clé « base ».

Cette conception permet aux développeurs d’éviter le traitement des sélecteurs objective-C dans le cadre de leur processus de développement, car le système objective-C entier est déjà encapsulé dans les bibliothèques Xamarin. iOS.

#### <a name="types-and-interface-builder"></a>Types et Interface Builder

Quand vous créez des classes .NET qui sont des instances de types créées par Interface Builder, vous devez fournir un constructeur qui accepte un seul paramètre `IntPtr`.
Cela est nécessaire pour lier l’instance de l’objet managé à l’objet non managé.
Le code se compose d’une seule ligne, comme suit :

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>Délégués

Objective-C C# et les significations différentes pour le mot délégué dans chaque langage.

Dans le monde objective-C, et dans la documentation que vous trouverez en ligne sur CocoaTouch, un délégué est généralement une instance d’une classe qui répond à un ensemble de méthodes. Cela est très similaire à une C# interface, à la différence que les méthodes ne sont pas toujours obligatoires.

Ces délégués jouent un rôle important dans UIKit et d’autres API CocoaTouch. Ils sont utilisés pour accomplir diverses tâches :

- Pour fournir des notifications à votre code (semblable à la C# remise d’événements dans ou GTK +).
- Pour implémenter des modèles pour les contrôles de visualisation de données.
- Pour piloter le comportement d’un contrôle.

Le modèle de programmation a été conçu pour réduire la création de classes dérivées afin de modifier le comportement d’un contrôle. Cette solution est similaire à celle des autres kits d’outils GUI effectués au fil des années : les signaux de GTK, les emplacements QT, les événements WinForms, les événements WPF/Silverlight, etc. Pour éviter d’avoir des centaines d’interfaces (une pour chaque action) ou de demander aux développeurs d’implémenter un trop grand nombre de méthodes dont ils n’ont pas besoin, Objective-C prend en charge les définitions de méthode facultatives. Cela est différent des C# interfaces qui requièrent que toutes les méthodes soient implémentées.

Dans les classes objective-C, vous verrez que les classes qui utilisent ce modèle de programmation exposent une propriété, généralement appelée `delegate`, qui est nécessaire pour implémenter les parties obligatoires de l’interface et zéro, ou plus, des parties facultatives.

Dans Xamarin. iOS, trois mécanismes mutuellement exclusifs pour la liaison à ces délégués sont proposés :

1. [Via les événements](#via-events).
2. [Fortement typé via une propriété `Delegate`](#strongly-typed-via-a-delegate-property)
3. [Faiblement typé via une propriété `WeakDelegate`](#loosely-typed-via-the-weakdelegate-property)

Par exemple, considérez la classe UIWebView. Cette valeur est distribuée à une instance UIWebViewDelegate, qui est affectée à la propriété du délégué.

##### <a name="via-events"></a>Événements via

Pour de nombreux types, Xamarin. iOS créera automatiquement un délégué approprié qui transférera les appels `UIWebViewDelegate` C# vers les événements. Pour `UIWebView` :

- La méthode webViewDidStartLoad est mappée à l’événement [UIWebView. LoadStarted](xref:UIKit.UIWebView.LoadStarted) .
- La méthode webViewDidFinishLoad est mappée à l’événement [UIWebView. LoadFinished](xref:UIKit.UIWebView.LoadFinished) .
- La méthode WebView : didFailLoadWithError est mappée à l’événement [UIWebView. LoadError](xref:UIKit.UIWebView.LoadError) .

Par exemple, ce programme simple enregistre les heures de début et de fin lors du chargement d’une vue Web :

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Propriétés via

Les événements sont utiles quand il peut y avoir plusieurs abonnés à l’événement. En outre, les événements sont limités aux cas où il n’y a aucune valeur de retour à partir du code.

Dans les cas où le code est supposé retourner une valeur, nous avons opté à la place pour les propriétés. Cela signifie qu’une seule méthode peut être définie à un moment donné dans un objet.

Par exemple, vous pouvez utiliser ce mécanisme pour faire disparaître le clavier à l’écran sur le gestionnaire d’un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

Dans ce cas, la propriété `ShouldReturn` de `UITextField`prend comme argument un délégué qui retourne une valeur bool et détermine si le TextField doit effectuer une opération avec le bouton de retour enfoncé. Dans notre méthode, nous renvoyons *true* à l’appelant, mais nous supprimons également le clavier de l’écran (cela se produit lorsque le textfield appelle `ResignFirstResponder`).

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortement typé via une propriété de délégué

Si vous préférez ne pas utiliser d’événements, vous pouvez fournir votre propre sous-classe [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) et l’assigner à la propriété [UIWebView. Delegate](xref:UIKit.UIWebView.Delegate) . Une fois UIWebView. Delegate affecté, le mécanisme de dispatch d’événement UIWebView ne fonctionnera plus, et les méthodes UIWebViewDelegate seront appelées lorsque les événements correspondants se produiront.

Par exemple, ce type simple enregistre le temps nécessaire pour charger une vue Web :

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

L’exemple ci-dessus est utilisé dans le code comme suit :

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

L’exemple ci-dessus crée un UIWebViewer et lui demande d’envoyer des messages à une instance de notificateur, une classe que nous avons créée pour répondre aux messages.

Ce modèle est également utilisé pour contrôler le comportement de certains contrôles, par exemple dans le cas UIWebView, la propriété [UIWebView. ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) permet à l’instance `UIWebView` de contrôler si l' `UIWebView` chargera ou non une page.

Le modèle est également utilisé pour fournir les données à la demande pour quelques contrôles. Par exemple, le contrôle [UITableView](xref:UIKit.UITableView) est un contrôle de rendu de table puissant, et l’aspect et le contenu sont contrôlés par une instance d’un [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Faiblement typé à l’aide de la propriété WeakDelegate

En plus de la propriété fortement typée, il existe également un délégué typé faible qui permet au développeur de lier les éléments différemment si vous le souhaitez.
Partout où une propriété `Delegate` fortement typée est exposée dans la liaison de Xamarin. iOS, une propriété `WeakDelegate` correspondante est également exposée.

Lorsque vous utilisez l' `WeakDelegate`, vous êtes chargé de décorer correctement votre classe à l’aide de l’attribut [Export](xref:Foundation.ExportAttribute) pour spécifier le sélecteur. Par exemple :

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

Notez qu’une fois la propriété `WeakDelegate` affectée, la propriété `Delegate` ne sera pas utilisée. En outre, si vous implémentez la méthode dans une classe de base héritée que vous souhaitez [Exporter], vous devez en faire une méthode publique.

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>Mappage du modèle de délégué objective-C à C\#

Lorsque vous voyez des exemples objective-C qui ressemblent à ceci :

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

Cela indique au langage de créer et de construire une instance de la classe « SomethingDelegate » et d’assigner la valeur à la propriété de délégué sur la variable foo. Ce mécanisme est pris en charge par Xamarin. C# iOS et la syntaxe est la suivante :

```csharp
foo.Delegate = new SomethingDelegate ();
```

Dans Xamarin. iOS, nous avons fourni des classes fortement typées qui mappent aux classes déléguées objective-C. Pour les utiliser, vous allez sous-classer et remplacer les méthodes définies par l’implémentation de Xamarin. iOS. Pour plus d’informations sur leur fonctionnement, consultez la section « modèles » ci-dessous.

### <a name="mapping-delegates-to-c"></a>Mappage de délégués à des\# C

UIKit en général utilise des délégués objective-C sous deux formes.

Le premier formulaire fournit une interface au modèle d’un composant. Par exemple, il s’agit d’un mécanisme permettant de fournir des données à la demande pour une vue, telle que la fonctionnalité de stockage de données pour une vue liste.  Dans ce cas, vous devez toujours créer une instance de la classe appropriée et assigner la variable.

Dans l’exemple suivant, nous fournissons le `UIPickerView` avec une implémentation pour un modèle qui utilise des chaînes :

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

Le deuxième formulaire est de fournir une notification pour les événements. Dans ce cas, bien que nous exposons toujours l’API sous la forme décrite ci-dessus C# , nous fournissons également des événements, qui doivent être plus simples à utiliser pour les opérations rapides et intégrés C#aux délégués anonymes et aux expressions lambda dans.

Par exemple, vous pouvez vous abonner à des événements de `UIAccelerometer` :

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Les deux options sont disponibles là où elles ont un sens, mais en tant que programmeur, vous devez choisir l’une ou l’autre. Si vous créez votre propre instance d’un répondeur/délégué fortement typé et l’attribuez, C# les événements ne seront pas fonctionnels. Si vous utilisez les C# événements, les méthodes de votre classe de répondeur/délégué ne seront jamais appelées.

L’exemple précédent qui utilisait `UIWebView` peut être écrit C# à l’aide de 3,0 lambdas de la façon suivante :

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>Réponse aux événements

Dans le code Objective-C, parfois, les gestionnaires d’événements pour plusieurs contrôles et fournisseurs d’informations pour plusieurs contrôles sont hébergés dans la même classe. Cela est possible parce que les classes répondent aux messages, et tant que les classes répondent aux messages, il est possible de lier des objets ensemble.

Comme indiqué précédemment, Xamarin. iOS prend en charge C# le modèle de programmation basé sur les événements et le modèle de délégué objective-C, où vous pouvez créer une nouvelle classe qui implémente le délégué et substitue les méthodes souhaitées.

Il est également possible de prendre en charge le modèle objective-C, où les répondeurs pour plusieurs opérations différentes sont hébergés dans la même instance d’une classe. Pour cela, vous devez utiliser les fonctionnalités de bas niveau de la liaison Xamarin. iOS.

Par exemple, si vous souhaitez que votre classe réponde à la fois au message `UITextFieldDelegate.textFieldShouldClear`: et au `UIWebViewDelegate.webViewDidStartLoad`: dans la même instance d’une classe, vous devez utiliser la déclaration d’attribut [Export] :

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

Les C# noms des méthodes ne sont pas importants ; toutes les choses importantes sont les chaînes passées à l’attribut [Export].

Quand vous utilisez ce style de programmation, assurez C# -vous que les paramètres correspondent aux types réels que le moteur d’exécution passera.

#### <a name="models"></a>Modèles

Dans les fonctionnalités de stockage UIKit ou dans les répondeurs qui sont implémentés à l’aide de classes d’assistance, celles-ci sont généralement référencées dans le code Objective-C en tant que délégués, et elles sont implémentées en tant que protocoles.

Les protocoles objective-C sont des interfaces similaires, mais ils prennent en charge les méthodes facultatives, autrement dit, toutes les méthodes ne doivent pas être implémentées pour que le protocole fonctionne.

Il existe deux façons d’implémenter un modèle. Vous pouvez l’implémenter manuellement ou utiliser les définitions fortement typées existantes.

Le mécanisme manuel est nécessaire lorsque vous essayez d’implémenter une classe qui n’a pas été liée par Xamarin. iOS. C’est très facile :

- Marquer votre classe pour l’inscription avec le Runtime
- Appliquez l’attribut [Export] avec le nom du sélecteur réel sur chaque méthode que vous souhaitez substituer
- Instanciez la classe et transmettez-la.

Par exemple, le code suivant implémente une seule des méthodes facultatives dans la définition de protocole UIApplicationDelegate :

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Le nom du sélecteur objective-C (« applicationDidFinishLaunching : ») est déclaré avec l’attribut Export et la classe est inscrite avec l’attribut `[Register]`.

Xamarin. iOS fournit des déclarations fortement typées, prêtes à l’emploi, qui ne nécessitent pas de liaison manuelle. Pour prendre en charge ce modèle de programmation, le runtime Xamarin. iOS prend en charge l’attribut [Model] sur une déclaration de classe. Cela indique au runtime qu’il ne doit pas associer toutes les méthodes de la classe, sauf si les méthodes sont implémentées explicitement.

Cela signifie que dans UIKit, les classes qui représentent un protocole avec des méthodes facultatives sont écrites comme suit :

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

Lorsque vous souhaitez implémenter un modèle qui implémente uniquement certaines des méthodes, il vous suffit de remplacer les méthodes qui vous intéressent et d’ignorer les autres méthodes. Le runtime raccordera uniquement les méthodes remplacées, et non les méthodes d’origine au monde objective-C.

L’équivalent de l’exemple manuel précédent est le suivant :

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

Les avantages sont qu’il n’est pas nécessaire de plonger dans les fichiers d’en-tête objective-C pour rechercher le sélecteur, les types des arguments ou C#le mappage à, et que vous obtenez IntelliSense à partir de Visual Studio pour Mac, ainsi que les types forts

#### <a name="xib-outlets-and-c"></a>XIB et C\#

> [!IMPORTANT]
> Cette section explique l’intégration de l’IDE aux prises en cas d’utilisation de fichiers XIB. Lorsque vous utilisez l’Xamarin Designer pour iOS, il est remplacé par l’entrée d’un nom sous **identité > nom** dans la section Propriétés de votre IDE, comme indiqué ci-dessous :
>
> [![](images/designeroutlet.png "Entering an item Name in the iOS Designer")](images/designeroutlet.png#lightbox)
>
>Pour plus d’informations sur le concepteur iOS, consultez la présentation du document du [Concepteur iOS](~/ios/user-interface/designer/introduction.md#how-it-works) .

Il s’agit d’une description de bas niveau de la façon dont C# les sorties s’intègrent à et est fournie pour les utilisateurs expérimentés de Xamarin. iOS. Lorsque vous utilisez Visual Studio pour Mac le mappage est effectué automatiquement en arrière-plan à l’aide du code généré sur le vol pour vous.

Lorsque vous concevez votre interface utilisateur avec Interface Builder, vous concevez uniquement l’apparence de l’application et vous établissez des connexions par défaut. Si vous souhaitez extraire par programme des informations, modifier le comportement d’un contrôle au moment de l’exécution ou modifier le contrôle au moment de l’exécution, il est nécessaire de lier certains contrôles à votre code managé.

Cette opération s’effectue en quelques étapes :

1. Ajoutez la **déclaration de sortie** au **propriétaire de votre fichier**.
1. Connectez votre contrôle au **propriétaire du fichier**.
1. Stockez l’interface utilisateur plus les connexions dans votre fichier XIB/de plume.
1. Chargez le fichier de plume au moment de l’exécution.
1. Accédez à la variable de sortie.

Les étapes (1) à (3) sont couvertes dans la documentation d’Apple pour la création d’interfaces avec Interface Builder.

Lors de l’utilisation de Xamarin. iOS, votre application doit créer une classe qui dérive de UIViewController. Elle est implémentée comme suit :

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Pour charger votre ViewController à partir d’un fichier de plume, procédez comme suit :

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Cela charge l’interface utilisateur à partir du bec. À présent, pour accéder aux prises, il est nécessaire d’indiquer au runtime que nous souhaitons y accéder. Pour ce faire, la sous-classe `UIViewController` doit déclarer les propriétés et les annoter avec l’attribut [Connect]. Comme ceci :

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

L’implémentation de la propriété est celle qui récupère et stocke réellement la valeur pour le type natif réel.

Vous n’avez pas à vous soucier de cela lorsque vous utilisez Visual Studio pour Mac et InterfaceBuilder. Visual Studio pour Mac met automatiquement en miroir toutes les prises déclarées avec le code dans une classe partielle qui est compilée dans le cadre de votre projet.

#### <a name="selectors"></a>Sélecteurs

Les sélecteurs sont un concept fondamental de la programmation objective-C. Les API vous obligent souvent à passer un sélecteur ou à vous attendre à ce que votre code réponde à un sélecteur.

La création de nouveaux sélecteurs dans C# est très simple : il vous suffit de créer une nouvelle instance de la classe `ObjCRuntime.Selector` et d’utiliser le résultat à n’importe quel endroit de l’API qui l’exige. Par exemple :

```csharp
var selector_add = new Selector ("add:plus:");
```

Pour qu' C# une méthode réponde à un appel de sélecteur, elle doit hériter du type C# `NSObject` et la méthode doit être décorée avec le nom du sélecteur à l’aide de l’attribut `[Export]`. Par exemple :

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Notez que les noms de sélecteur **doivent** correspondre exactement, y compris tous les signes deux-points intermédiaires et de fin («  : »), s’ils sont présents.

#### <a name="nsobject-constructors"></a>Constructeurs NSObject

La plupart des classes dans Xamarin. iOS qui dérivent de `NSObject` exposent des constructeurs spécifiques aux fonctionnalités de l’objet, mais ils exposent également différents constructeurs qui ne sont pas immédiatement évidents.

Les constructeurs sont utilisés comme suit :

```csharp
public Foo (IntPtr handle)
```

Ce constructeur est utilisé pour instancier votre classe lorsque le runtime doit mapper votre classe à une classe non managée. Cela se produit lorsque vous chargez un fichier XIB/de plume.  À ce stade, le runtime objective-C aura créé un objet dans le monde non managé, et ce constructeur sera appelé pour initialiser le côté managé.

En règle générale, il vous suffit d’appeler le constructeur de base avec le paramètre handle et, dans le corps, d’effectuer toute initialisation nécessaire.

```csharp
public Foo ()
```

Il s’agit du constructeur par défaut pour une classe, et dans les classes fournies par Xamarin. iOS, cette méthode initialise la classe Foundation. NSObject et toutes les classes comprises entre et, à la fin, la chaîne à la méthode objective-C `init` sur la classe.

```csharp
public Foo (NSObjectFlag x)
```

Ce constructeur est utilisé pour initialiser l’instance, mais empêche le code d’appeler la méthode objective-C "init" à la fin. Vous l’utilisez généralement lorsque vous êtes déjà inscrit pour l’initialisation (lorsque vous utilisez `[Export]` sur votre constructeur) ou lorsque vous avez déjà effectué l’initialisation par une autre moyenne.

```csharp
public Foo (NSCoder coder)
```

Ce constructeur est fourni pour les cas où l’objet est initialisé à partir d’une instance de NSCoding.

#### <a name="exceptions"></a>Exceptions

La conception de l’API Xamarin. iOS ne déclenche pas d’exceptions objective-C comme C# des exceptions. La conception s’impose qu’aucun garbage collection ne soit envoyé au monde objective-C en premier lieu et que toutes les exceptions qui doivent être produites sont produites par la liaison elle-même avant que des données non valides ne soient passées au monde objective-C.

#### <a name="notifications"></a>Notifications

Dans iOS et OS X, les développeurs peuvent s’abonner à des notifications diffusées par la plateforme sous-jacente. Pour ce faire, utilisez la méthode `NSNotificationCenter.DefaultCenter.AddObserver`. La méthode `AddObserver` accepte deux paramètres ; l’une est la notification à laquelle vous souhaitez vous abonner. l’autre est la méthode à appeler lorsque la notification est déclenchée.

Dans Xamarin. iOS et Xamarin. Mac, les clés pour les différentes notifications sont hébergées sur la classe qui déclenche les notifications. Par exemple, les notifications déclenchées par le `UIMenuController` sont hébergées en tant que propriétés de `static NSString` dans les classes `UIMenuController` qui se terminent par le nom « notification ».

### <a name="memory-management"></a>Gestion de la mémoire

Xamarin. iOS dispose d’un garbage collector qui prend en charge la libération des ressources lorsque celles-ci ne sont plus utilisées. Outre le garbage collector, tous les objets qui dérivent de `NSObject` implémentent l’interface `System.IDisposable`.

#### <a name="nsobject-and-idisposable"></a>NSObject et IDisposable

L’exposition de l’interface `IDisposable` est un moyen pratique d’aider les développeurs à libérer des objets qui peuvent encapsuler de grands blocs de mémoire (par exemple, un `UIImage` peut se présenter comme un pointeur inoffensif, mais peut pointer vers une image de 2 mégaoctets) et d’autres ressources importantes et limitées (comme un tampon de décodage vidéo).

NSObject implémente l’interface IDisposable et également le [modèle de suppression .net](https://msdn.microsoft.com/library/fs2xkftw.aspx). Cela permet aux développeurs dont la sous-classe NSObject est de remplacer le comportement de suppression et de libérer leurs propres ressources à la demande. Par exemple, considérez ce contrôleur d’affichage qui continue autour d’une série d’images :

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

Lorsqu’un objet managé est supprimé, il n’est plus utile. Vous pouvez toujours avoir une référence aux objets, mais l’objet est pour toutes les intentions et les objectifs non valides à ce stade. Certaines API .NET garantissent cela en levant un ObjectDisposedException si vous tentez d’accéder à des méthodes sur un objet supprimé, par exemple :

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Même si vous pouvez toujours accéder à la variable « image », il s’agit véritablement d’une référence non valide et ne pointe plus vers l’objet objective-C qui contenait l’image.

Toutefois, la suppression d’un C# objet dans ne signifie pas que l’objet sera nécessairement détruit. Il vous suffit de libérer la référence qui C# avait l’objet. Il est possible que l’environnement de cacao ait gardé une référence pour sa propre utilisation. Par exemple, si vous affectez une image à la propriété image d’un UIImageView, puis que vous supprimez l’image, le UIImageView sous-jacent a pris sa propre référence et conserve une référence à cet objet jusqu’à ce qu’il ait terminé de l’utiliser.

#### <a name="when-to-call-dispose"></a>Quand appeler dispose

Vous devez appeler dispose lorsque vous avez besoin de mono pour vous débarrasser de votre objet. Un cas d’utilisation possible est lorsque mono n’a aucune connaissance que votre NSObject contient effectivement une référence à une ressource importante, telle qu’une mémoire, ou un pool d’informations. Dans ce cas, vous devez appeler dispose pour libérer immédiatement la référence à la mémoire, au lieu d’attendre que mono exécute un cycle de garbage collection.

En interne, lorsque mono crée [des références chaîne NSString C# à partir de chaînes](~/ios/internals/api-design/nsstring.md), il les supprime immédiatement pour réduire la quantité de travail que le garbage collector a à faire. Moins il y a d’objets à gérer, plus le GC s’exécutera rapidement.

#### <a name="when-to-keep-references-to-objects"></a>Quand conserver les références aux objets

L’un des effets secondaires de la gestion automatique de la mémoire est que le GC se débarrasser des objets inutilisés tant qu’il n’y a aucune référence à ces objets. Cela peut parfois avoir des effets secondaires étonnants, par exemple, si vous créez une variable locale pour stocker votre contrôleur d’affichage de niveau supérieur, ou votre fenêtre de niveau supérieur, puis que vous revenez à votre arrière-plan.

Si vous ne conservez pas de référence dans vos variables statiques ou d’instance à vos objets, mono appelle la méthode Dispose () sur ceux-ci, et ils libèrent la référence à l’objet. Comme il peut s’agir de la seule référence en suspens, le runtime objective-C détruit l’objet pour vous.

## <a name="related-links"></a>Liens connexes

- [Champs de liaison](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
